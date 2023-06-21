# PX4FMU和PX4IO最底层启动过程分析（下）
PX4FMU的系统启动函数为`nash_main(int argc,char *argv[])`  
PX4IO的系统启动函数为`nash_start(int argc,char *argv[])`

## PX4FMU启动函数nash_main(int argc,char *argv[])
首先分析一下`nash_main(int argc,char *argv[])`  
PX4FMU中有`#define CONFIG_USER_ENTRYPOINT nsh_main`
```
int nsh_main(int argc, char *argv[])
{
  int exitval = 0;
  int ret;

  /* Call all C++ static constructors */

#if defined(CONFIG_HAVE_CXX) && defined(CONFIG_HAVE_CXXINITIALIZE)
  up_cxxinitialize();
#endif

  /* Make sure that we are using our symbol take */

#if defined(CONFIG_LIBC_EXECFUNCS) && defined(CONFIG_EXECFUNCS_SYMTAB)
  exec_setsymtab(CONFIG_EXECFUNCS_SYMTAB, 0);
#endif

  /* Register the BINFS file system */

#if defined(CONFIG_FS_BINFS) && (CONFIG_BUILTIN)
  ret = builtin_initialize();
  if (ret < 0)
    {
     fprintf(stderr, "ERROR: builtin_initialize failed: %d\n", ret);
     exitval = 1;
   }
#endif

  /* Initialize the NSH library */

  nsh_initialize();

  /* If the Telnet console is selected as a front-end, then start the
   * Telnet daemon.
   */

#ifdef CONFIG_NSH_TELNET
  ret = nsh_telnetstart();
  if (ret < 0)
    {
     /* The daemon is NOT running.  Report the the error then fail...
      * either with the serial console up or just exiting.
      */

     fprintf(stderr, "ERROR: Failed to start TELNET daemon: %d\n", ret);
     exitval = 1;
   }
#endif

  /* If the serial console front end is selected, then run it on this thread */

#ifdef CONFIG_NSH_CONSOLE
  ret = nsh_consolemain(0, NULL);

  /* nsh_consolemain() should not return.  So if we get here, something
   * is wrong.
   */

  fprintf(stderr, "ERROR: nsh_consolemain() returned: %d\n", ret);
  exitval = 1;
#endif

  return exitval;
}
```
其中包含
```
#ifdef CONFIG_NSH_CONSOLE
  ret = nsh_consolemain(0, NULL);
```
进入`nsh_consolemain(int argc, char *argv[])`函数
```
int nsh_consolemain(int argc, char *argv[])
{
  FAR struct console_stdio_s *pstate = nsh_newconsole();
  int ret;

  DEBUGASSERT(pstate);

  /* Execute the start-up script */

#ifdef CONFIG_NSH_ROMFSETC
  (void)nsh_initscript(&pstate->cn_vtbl);
#endif

  /* Initialize any USB tracing options that were requested */

#ifdef CONFIG_NSH_USBDEV_TRACE
  usbtrace_enable(TRACE_BITSET);
#endif

  /* Execute the session */

  ret = nsh_session(pstate);

  /* Exit upon return */

  nsh_exit(&pstate->cn_vtbl, ret);
  return ret;
}
```
其中包含
```
/* Execute the start-up script */

#ifdef CONFIG_NSH_ROMFSETC
  (void)nsh_initscript(&pstate->cn_vtbl);
#endif
```
执行启动脚本也就是rcS，接下来根据本身版本分别看ardupilot和PX4原生码
```
/* Execute the session */

  ret = nsh_session(pstate);
```
执行用户程序  
跟踪pstate
```
FAR struct console_stdio_s *pstate = nsh_newconsole();
```
进入`console_stdio_s *nsh_newconsole(void)`
```
FAR struct console_stdio_s *nsh_newconsole(void)
{
  struct console_stdio_s *pstate = (struct console_stdio_s *)zalloc(sizeof(struct console_stdio_s));
  if (pstate)
    {
      /* Initialize the call table */

#ifndef CONFIG_NSH_DISABLEBG
      pstate->cn_vtbl.clone      = nsh_consoleclone;
      pstate->cn_vtbl.release    = nsh_consolerelease;
#endif
      pstate->cn_vtbl.write      = nsh_consolewrite;
      pstate->cn_vtbl.output     = nsh_consoleoutput;
      pstate->cn_vtbl.linebuffer = nsh_consolelinebuffer;
      pstate->cn_vtbl.redirect   = nsh_consoleredirect;
      pstate->cn_vtbl.undirect   = nsh_consoleundirect;
      pstate->cn_vtbl.exit       = nsh_consoleexit;

      /* (Re-) open the console input device */

#ifdef CONFIG_NSH_CONDEV
      pstate->cn_confd           = open(CONFIG_NSH_CONDEV, O_RDWR);
      if (pstate->cn_confd < 0)
        {
          free(pstate);
          return NULL;
        }

      /* Create a standard C stream on the console device */

      pstate->cn_constream = fdopen(pstate->cn_confd, "r+");
      if (!pstate->cn_constream)
        {
          close(pstate->cn_confd);
          free(pstate);
          return NULL;
        }
#endif

      /* Initialize the output stream */

      pstate->cn_outfd           = OUTFD(pstate);
      pstate->cn_outstream       = OUTSTREAM(pstate);
    }
  return pstate;
}
```
应该是用户在console输入新的nsh命令吧

---
## PX4IO启动函数nash_start(int argc,char *argv[])
接着分析一下`nash_start(int argc,char *argv[])`  
PX4IO中有`#define CONFIG_USER_ENTRYPOINT user_start`
```
int user_start(int argc, char *argv[])
{
	/* configure the first 8 PWM outputs (i.e. all of them) */
	up_pwm_servo_init(0xff);

	/* run C++ ctors before we go any further */
	up_cxxinitialize();

	/* reset all to zero */
	memset(&system_state, 0, sizeof(system_state));

	/* configure the high-resolution time/callout interface */
	hrt_init();

	/* calculate our fw CRC so FMU can decide if we need to update */
	calculate_fw_crc();

	/*
	 * Poll at 1ms intervals for received bytes that have not triggered
	 * a DMA event.
	 */
#ifdef CONFIG_ARCH_DMA
	hrt_call_every(&serial_dma_call, 1000, 1000, (hrt_callout)stm32_serial_dma_poll, NULL);
#endif

	/* print some startup info */
	lowsyslog("\nPX4IO: starting\n");

	/* default all the LEDs to off while we start */
	LED_AMBER(false);
	LED_BLUE(false);
	LED_SAFETY(false);
#ifdef GPIO_LED4
	LED_RING(false);
#endif

	/* turn on servo power (if supported) */
#ifdef POWER_SERVO
	POWER_SERVO(true);
#endif

	/* turn off S.Bus out (if supported) */
#ifdef ENABLE_SBUS_OUT
	ENABLE_SBUS_OUT(false);
#endif

	/* start the safety switch handler */
	safety_init();

	/* initialise the control inputs */
	controls_init();

	/* set up the ADC */
	adc_init();

	/* start the FMU interface */
	interface_init();

	/* add a performance counter for mixing */
	perf_counter_t mixer_perf = perf_alloc(PC_ELAPSED, "mix");

	/* add a performance counter for controls */
	perf_counter_t controls_perf = perf_alloc(PC_ELAPSED, "controls");

	/* and one for measuring the loop rate */
	perf_counter_t loop_perf = perf_alloc(PC_INTERVAL, "loop");

	struct mallinfo minfo = mallinfo();
	lowsyslog("MEM: free %u, largest %u\n", minfo.mxordblk, minfo.fordblks);

	/* initialize PWM limit lib */
	pwm_limit_init(&pwm_limit);

	/*
	 *    P O L I C E    L I G H T S
	 *
	 * Not enough memory, lock down.
	 *
	 * We might need to allocate mixers later, and this will
	 * ensure that a developer doing a change will notice
	 * that he just burned the remaining RAM with static
	 * allocations. We don't want him to be able to
	 * get past that point. This needs to be clearly
	 * documented in the dev guide.
	 *
	 */
	if (minfo.mxordblk < 600) {

		lowsyslog("ERR: not enough MEM");
		bool phase = false;

		while (true) {

			if (phase) {
				LED_AMBER(true);
				LED_BLUE(false);

			} else {
				LED_AMBER(false);
				LED_BLUE(true);
			}

			up_udelay(250000);

			phase = !phase;
		}
	}

	/* Start the failsafe led init */
	failsafe_led_init();

	/*
	 * Run everything in a tight loop.
	 */

	uint64_t last_debug_time = 0;
	uint64_t last_heartbeat_time = 0;

	for (;;) {

		/* track the rate at which the loop is running */
		perf_count(loop_perf);

		/* kick the mixer */
		perf_begin(mixer_perf);
		mixer_tick();
		perf_end(mixer_perf);

		/* kick the control inputs */
		perf_begin(controls_perf);
		controls_tick();
		perf_end(controls_perf);

		if ((hrt_absolute_time() - last_heartbeat_time) > 250 * 1000) {
			last_heartbeat_time = hrt_absolute_time();
			heartbeat_blink();
		}

		ring_blink();

		check_reboot();

		/* check for debug activity (default: none) */
		show_debug_messages();

		/* post debug state at ~1Hz - this is via an auxiliary serial port
		 * DEFAULTS TO OFF!
		 */
		if (hrt_absolute_time() - last_debug_time > (1000 * 1000)) {

			isr_debug(1, "d:%u s=0x%x a=0x%x f=0x%x m=%u",
				  (unsigned)r_page_setup[PX4IO_P_SETUP_SET_DEBUG],
				  (unsigned)r_status_flags,
				  (unsigned)r_setup_arming,
				  (unsigned)r_setup_features,
				  (unsigned)mallinfo().mxordblk);
			last_debug_time = hrt_absolute_time();
		}
	}
}
```
**user_start 负责px4io 基础环境的初始化,包括PWM,串口,ADC 等资源的初始化,最后运行一个死循环,用于处理遥控器输入,与PX4FMU 通讯的内容**  
**controls_tick 负责处理遥控器的输入内容,包括SBUS 的处理sbus_input、 SPKT/DSM 的处理dsm_port_input、 PPM 的处理ppm_input**

PX4IO 底层中断处理的内容以下图
<div align=center><img src="https://jokerbin.oss-cn-beijing.aliyuncs.com/202209041300374.jpg"/></div>


（1）紫色为PX4IO 的底层串口IO 操做,流程为当PX4IO 收到PX4FMU 的串口数据后会运行serial_interrupt, serial_interrupt 负责收发DMA 的操做,若是收到一个完整的包,则调用rx_dma_callback 进行处理, rx_dma_callback 首先调用rx_handle_packet 解析包中的内容,判断为写寄存器仍是读寄存器,处理完成后由rx_dma_callback 发送回包给PX4FMU
```
static int
serial_interrupt(int irq, void *context)
{
	static bool abort_on_idle = false;

	uint32_t sr = rSR;	/* get UART status register */
	(void)rDR;		/* required to clear any of the interrupt status that brought us here */

	if (sr & (USART_SR_ORE |	/* overrun error - packet was too big for DMA or DMA was too slow */
		  USART_SR_NE |		/* noise error - we have lost a byte due to noise */
		  USART_SR_FE)) {		/* framing error - start/stop bit lost or line break */

		perf_count(pc_errors);

		if (sr & USART_SR_ORE) {
			perf_count(pc_ore);
		}

		if (sr & USART_SR_NE) {
			perf_count(pc_ne);
		}

		if (sr & USART_SR_FE) {
			perf_count(pc_fe);
		}

		/* send a line break - this will abort transmission/reception on the other end */
		rCR1 |= USART_CR1_SBK;

		/* when the line goes idle, abort rather than look at the packet */
		abort_on_idle = true;
	}

	if (sr & USART_SR_IDLE) {

		/*
		 * If we saw an error, don't bother looking at the packet - it should have
		 * been aborted by the sender and will definitely be bad. Get the DMA reconfigured
		 * ready for their retry.
		 */
		if (abort_on_idle) {

			abort_on_idle = false;
			dma_reset();
			return 0;
		}

		/*
		 * The sender has stopped sending - this is probably the end of a packet.
		 * Check the received length against the length in the header to see if
		 * we have something that looks like a packet.
		 */
		unsigned length = sizeof(dma_packet) - stm32_dmaresidual(rx_dma);

		if ((length < 1) || (length < PKT_SIZE(dma_packet))) {

			/* it was too short - possibly truncated */
			perf_count(pc_badidle);
			dma_reset();
			return 0;
		}

		/*
		 * Looks like we received a packet. Stop the DMA and go process the
		 * packet.
		 */
		perf_count(pc_idle);
		stm32_dmastop(rx_dma);
		rx_dma_callback(rx_dma, DMA_STATUS_TCIF, NULL);
	}

	return 0;
}
```
```
static void
rx_dma_callback(DMA_HANDLE handle, uint8_t status, void *arg)
{
	/*
	 * We are here because DMA completed, or UART reception stopped and
	 * we think we have a packet in the buffer.
	 */
	perf_begin(pc_txns);

	/* disable UART DMA */
	rCR3 &= ~(USART_CR3_DMAT | USART_CR3_DMAR);

	/* handle the received packet */
	rx_handle_packet();

	/* re-set DMA for reception first, so we are ready to receive before we start sending */
	dma_reset();

	/* send the reply to the just-processed request */
	dma_packet.crc = 0;
	dma_packet.crc = crc_packet(&dma_packet);
	stm32_dmasetup(
		tx_dma,
		(uint32_t)&rDR,
		(uint32_t)&dma_packet,
		PKT_SIZE(dma_packet),
		DMA_CCR_DIR		|
		DMA_CCR_MINC		|
		DMA_CCR_PSIZE_8BITS	|
		DMA_CCR_MSIZE_8BITS);
	stm32_dmastart(tx_dma, NULL, NULL, false);
	rCR3 |= USART_CR3_DMAT;

	perf_end(pc_txns);
}
```

```
static void
rx_handle_packet(void)
{
	/* check packet CRC */
	uint8_t crc = dma_packet.crc;
	dma_packet.crc = 0;

	if (crc != crc_packet(&dma_packet)) {
		perf_count(pc_crcerr);

		/* send a CRC error reply */
		dma_packet.count_code = PKT_CODE_CORRUPT;
		dma_packet.page = 0xff;
		dma_packet.offset = 0xff;

		return;
	}

	if (PKT_CODE(dma_packet) == PKT_CODE_WRITE) {

		/* it's a blind write - pass it on */
		if (registers_set(dma_packet.page, dma_packet.offset, &dma_packet.regs[0], PKT_COUNT(dma_packet))) {
			perf_count(pc_regerr);
			dma_packet.count_code = PKT_CODE_ERROR;

		} else {
			dma_packet.count_code = PKT_CODE_SUCCESS;
		}

		return;
	}

	if (PKT_CODE(dma_packet) == PKT_CODE_READ) {

		/* it's a read - get register pointer for reply */
		unsigned count;
		uint16_t *registers;

		if (registers_get(dma_packet.page, dma_packet.offset, &registers, &count) < 0) {
			perf_count(pc_regerr);
			dma_packet.count_code = PKT_CODE_ERROR;

		} else {
			/* constrain reply to requested size */
			if (count > PKT_MAX_REGS) {
				count = PKT_MAX_REGS;
			}

			if (count > PKT_COUNT(dma_packet)) {
				count = PKT_COUNT(dma_packet);
			}

			/* copy reply registers into DMA buffer */
			memcpy((void *)&dma_packet.regs[0], registers, count * 2);
			dma_packet.count_code = count | PKT_CODE_SUCCESS;
		}

		return;
	}

	/* send a bad-packet error reply */
	dma_packet.count_code = PKT_CODE_CORRUPT;
	dma_packet.page = 0xff;
	dma_packet.offset = 0xfe;
}
```

（2） 蓝色为包操做,只提供registers_set 写操做和registers_get 读操做  
（3）IOPacket 为协议包,包括如下几部分


|    定义    |            描述            |
| :--------: | :------------------------: |
| count_code | 标记包的读写,错误,长度等信息 |
|    crc     |        为包的效验码         |
|    page    |          为数据页          |
|   offset   |        为数据偏移量         |
|    regs    |         为数据内容          |


<div align=center><img src="https://jokerbin.oss-cn-beijing.aliyuncs.com/202209041300044.jpg" width="600"/></div>

<div align=center><img src="https://jokerbin.oss-cn-beijing.aliyuncs.com/202209041301966.png" width="600"/></div>                                                                                                                                                                     