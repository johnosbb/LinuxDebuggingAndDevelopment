# Kernel Crash Analysis


A kernel crash takes the following form:

```sh
[77001.092637] Unable to handle kernel NULL pointer dereference at virtual address 00000070
[77001.106711] pgd = 5f4ca819
[77001.110498] [00000070] *pgd=00000000
[77001.112933] Internal error: Oops: 5 [#1] PREEMPT SMP ARM
[77001.118065] Modules linked in:
[77001.121094] CPU: 0 PID: 369 Comm: watchdog Not tainted 5.13.0 #1
[77001.127121] Hardware name: STM32 (Device Tree Support)
[77001.132254] PC is at stm32_iwdg_start+0x40/0x180
[77001.136868] LR is at watchdog_start+0x98/0x154
[77001.141307] pc : [<c07c45cc>]    lr : [<c07c39d4>]    psr: 400f0013
[77001.147591] sp : c2315c98  ip : c2315cb8  fp : c2315cb4
[77001.152809] r10: 00000000  r9 : c2314000  r8 : 35dcd172
[77001.158034] r7 : 00004608  r6 : c3854c00  r5 : c3859940  r4 : 00000000
[77001.164577] r3 : 00000020  r2 : 00020000  r1 : 10624dd3  r0 : c3859940
[77001.171110] Flags: nZcv  IRQs on  FIQs on  Mode SVC_32  ISA ARM  Segment none
[77001.178259] Control: 10c5387d  Table: c3f2c06a  DAC: 00000051
[77001.183993] Register r0 information: slab kmalloc-192 start c3859900 pointer offset 64 size 192
[77001.192732] Register r1 information: non-paged memory
[77001.197752] Register r2 information: non-paged memory
[77001.202803] Register r3 information: non-paged memory
[77001.207855] Register r4 information: NULL pointer
[77001.212558] Register r5 information: slab kmalloc-192 start c3859900 pointer offset 64 size 192
[77001.221302] Register r6 information: slab kmalloc-1k start c3854c00 pointer offset 0 size 1024
[77001.229924] Register r7 information: non-paged memory
[77001.234945] Register r8 information: non-paged memory
[77001.239997] Register r9 information: non-slab/vmalloc memory
[77001.245662] Register r10 information: NULL pointer
[77001.250447] Register r11 information: non-slab/vmalloc memory
[77001.256204] Register r12 information: non-slab/vmalloc memory
[77001.261952] Process watchdog (pid: 369, stack limit = 0xb760e51f)
[77001.268053] Stack: (0xc2315c98 to 0xc2316000)
[77001.272399] 5c80:                                                       c3859940 c3859940
[77001.280626] 5ca0: c3854c00 00004608 c2315cec c2315cb8 c07c39d4 c07c4598 c2315cec 060148c4
[77001.288814] 5cc0: c015cd40 00000001 00000000 c3854c00 c3859940 c26af0d0 c21083c0 c3854fc4
[77001.297002] 5ce0: c2315d14 c2315cf0 c07c3b14 c07c3948 c3854ed8 c26af0d0 c21083c0 c0b6523c
[77001.305188] 5d00: c21083c8 c2314000 c2315d44 c2315d18 c030ae20 c07c3a9c 00000000 060148c4
[77001.313387] 5d20: c2315d44 c21083c0 c26af0d0 00000000 c030ad50 c21083c8 c2315d6c c2315d48
[77001.321566] 5d40: c02ff7fc c030ad5c 00020001 c10138c0 00000000 c2315e58 c21083c0 c2314000
[77001.329754] 5d60: c2315d7c c2315d70 c0301720 c02ff6c4 c2315e54 c2315d80 c03181c8 c03016f0
[77001.337943] 5d80: 00000002 c154cd70 00000000 000043ad c0a39f08 c0a39db0 c2314000 00000041
[77001.346136] 5da0: 00000000 c0d25a9c c104da84 c0f6b538 c2315dcc c2315dc0 00000000 00000000
[77001.354320] 5dc0: 00000002 c26af0d0 c0a393a8 c0a39ef8 c2314000 c10d2eb6 c1008d50 060148c4
[77001.362505] 5de0: c0f6b538 00000000 00000000 00000001 c104da84 c0f6b538 c2315e14 c2315e08
[77001.370696] 5e00: c0a39f08 c0a39db0 c2315e44 c2315e18 c0a393a8 c0a39ef8 c3eedb18 c2108a90
[77001.378885] 5e20: c2315e54 060148c4 600f0013 c2315f58 c2315e58 c2315f20 00000001 c0100244
[77001.387069] 5e40: c2314000 00000142 c2315f0c c2315e58 c0319920 c0317688 c243d6d0 c2ffecc0
[77001.395256] 5e60: f61d9899 00000009 c246c015 060148c4 00000000 c32dd9c0 c26af0d0 00000101
[77001.403440] 5e80: 00000002 00000080 0000002e 00000000 00000000 00000000 c2315e9c c015c3f8
[77001.411630] 5ea0: 00000003 00000008 c2315efc c2315eb8 c032e280 c0a44bbc c1009460 c0d3ac60
[77001.419820] 5ec0: c0cfd958 00020001 c2108a80 c246c000 00000000 00000000 ffffff9c 00000000
[77001.428008] 5ee0: ffff41ed c0100244 c2314000 060148c4 c2315f58 c246c000 00000003 ffffff9c
[77001.436195] 5f00: c2315f54 c2315f10 c03019fc c031989c 00000051 c0100244 c2314000 000000ae
[77001.444381] 5f20: 00020001 c2310000 00000002 00000100 00000001 060148c4 ffffff9c bebcff47
[77001.452568] 5f40: bebcfe24 00000142 c2315fa4 c2315f58 c0301e9c c0301958 00020001 00000000
[77001.460755] 5f60: 00000000 00000000 00000000 00000000 00020001 00000000 00000000 00000000
[77001.468938] 5f80: 00000000 00000000 c0237878 060148c4 00000000 b6fcd1a0 00000000 c2315fa8
[77001.477128] 5fa0: c0100060 c0301dfc 00000000 b6fcd1a0 ffffff9c bebcff47 00020001 00000000
[77001.485318] 5fc0: 00000000 b6fcd1a0 bebcfe24 00000142 004f18a4 b6f6de60 bebcfe40 00000000
[77001.493499] 5fe0: 004f2c44 bebcfc28 0041e2c8 b6edd018 600f0010 ffffff9c 00000000 00000000
[77001.501676] Backtrace: 
[77001.504068] [<c07c458c>] (stm32_iwdg_start) from [<c07c39d4>] (watchdog_start+0x98/0x154)
[77001.512315]  r7:00004608 r6:c3854c00 r5:c3859940 r4:c3859940
[77001.517941] [<c07c393c>] (watchdog_start) from [<c07c3b14>] (watchdog_open+0x84/0x104)
[77001.525898]  r8:c3854fc4 r7:c21083c0 r6:c26af0d0 r5:c3859940 r4:c3854c00
[77001.532583] [<c07c3a90>] (watchdog_open) from [<c030ae20>] (chrdev_open+0xd0/0x220)
[77001.540270]  r9:c2314000 r8:c21083c8 r7:c0b6523c r6:c21083c0 r5:c26af0d0 r4:c3854ed8
[77001.548011] [<c030ad50>] (chrdev_open) from [<c02ff7fc>] (do_dentry_open+0x144/0x3e8)
[77001.555861]  r8:c21083c8 r7:c030ad50 r6:00000000 r5:c26af0d0 r4:c21083c0
[77001.562545] [<c02ff6b8>] (do_dentry_open) from [<c0301720>] (vfs_open+0x3c/0x40)
[77001.569970]  r9:c2314000 r8:c21083c0 r7:c2315e58 r6:00000000 r5:c10138c0 r4:00020001
[77001.577715] [<c03016e4>] (vfs_open) from [<c03181c8>] (path_openat+0xb4c/0xe5c)
[77001.585029] [<c031767c>] (path_openat) from [<c0319920>] (do_filp_open+0x90/0x140)
[77001.592619]  r10:00000142 r9:c2314000 r8:c0100244 r7:00000001 r6:c2315f20 r5:c2315e58
[77001.600452]  r4:c2315f58
[77001.602930] [<c0319890>] (do_filp_open) from [<c03019fc>] (do_sys_openat2+0xb0/0x168)
[77001.610823]  r7:ffffff9c r6:00000003 r5:c246c000 r4:c2315f58
[77001.616456] [<c030194c>] (do_sys_openat2) from [<c0301e9c>] (sys_openat+0xac/0xd4)
[77001.624061]  r7:00000142 r6:bebcfe24 r5:bebcff47 r4:ffffff9c
[77001.629702] [<c0301df0>] (sys_openat) from [<c0100060>] (ret_fast_syscall+0x0/0x2c)
[77001.637387] Exception stack(0xc2315fa8 to 0xc2315ff0)
[77001.642420] 5fa0:                   00000000 b6fcd1a0 ffffff9c bebcff47 00020001 00000000
[77001.650638] 5fc0: 00000000 b6fcd1a0 bebcfe24 00000142 004f18a4 b6f6de60 bebcfe40 00000000
[77001.658823] 5fe0: 004f2c44 bebcfc28 0041e2c8 b6edd018
[77001.663847]  r5:b6fcd1a0 r4:00000000
[77001.667410] Code: e5952030 e3041dd3 e3411062 e595301c (e5940070) 
```

The key pieces of information here are the following:

[77001.092637] Unable to handle kernel NULL pointer dereference at virtual address 00000070. This tells us we crashed because we attempted to dereference a null pointer.

The PC register contains the location of the instruction being executed at the time of the crash, and we can use this to find the function in which the crash occured.

```sh
addr2line -e ./vmlinux -a c07c45cc
```
This identifies the function:

```sh
/home/snuc/debugging-labs/buildroot/output/build/linux-5.13/drivers/watchdog/stm32_iwdg.c:98
```
We can then use gdb to dissassemble the code

```sh
gdb-multiarch vmlinux
```
We then disassemble that function in gdb my passing the function address:

```gdb
disassemble 0xc07c45cc
```
This produces the required disassembly:

```gdb
Dump of assembler code for function stm32_iwdg_start:
   0xc07c458c <+0>:	mov	r12, sp
   0xc07c4590 <+4>:	push	{r4, r5, r6, r7, r11, r12, lr, pc}
   0xc07c4594 <+8>:	sub	r11, r12, #4
   0xc07c4598 <+12>:	push	{lr}		; (str lr, [sp, #-4]!)
   0xc07c459c <+16>:	bl	0xc0111b78 <__gnu_mcount_nc>
   0xc07c45a0 <+20>:	movw	r2, #16256	; 0x3f80
   0xc07c45a4 <+24>:	movt	r2, #49422	; 0xc10e
   0xc07c45a8 <+28>:	ldr	r4, [r0, #76]	; 0x4c
   0xc07c45ac <+32>:	mov	r5, r0
   0xc07c45b0 <+36>:	ldrh	r3, [r2, #42]	; 0x2a
   0xc07c45b4 <+40>:	tst	r3, #4
   0xc07c45b8 <+44>:	bne	0xc07c46ec <stm32_iwdg_start+352>
   0xc07c45bc <+48>:	ldr	r2, [r5, #48]	; 0x30
   0xc07c45c0 <+52>:	movw	r1, #19923	; 0x4dd3
   0xc07c45c4 <+56>:	movt	r1, #4194	; 0x1062
   0xc07c45c8 <+60>:	ldr	r3, [r5, #28]
   0xc07c45cc <+64>:	ldr	r0, [r4, #112]	; 0x70
   0xc07c45d0 <+68>:	umull	r1, r2, r1, r2
   0xc07c45d4 <+72>:	ldr	r1, [r5, #36]	; 0x24
   0xc07c45d8 <+76>:	cmp	r3, r1
   0xc07c45dc <+80>:	lsr	r2, r2, #6
   0xc07c45e0 <+84>:	movcc	r3, r1
   0xc07c45e4 <+88>:	cmp	r3, r2
   0xc07c45e8 <+92>:	movcs	r3, r2
   0xc07c45ec <+96>:	mul	r3, r0, r3
   0xc07c45f0 <+100>:	add	r2, r3, #4080	; 0xff0
   0xc07c45f4 <+104>:	add	r2, r2, #15
   0xc07c45f8 <+108>:	lsr	r2, r2, #12
   0xc07c45fc <+112>:	subs	r2, r2, #1
   0xc07c4600 <+116>:	bne	0xc07c469c <stm32_iwdg_start+272>
   0xc07c4604 <+120>:	ldr	r1, [r4, #100]	; 0x64
   0xc07c4608 <+124>:	sub	r3, r3, #1
   0xc07c460c <+128>:	movw	r0, #21845	; 0x5555
   0xc07c4610 <+132>:	str	r0, [r1]
   0xc07c4614 <+136>:	str	r2, [r1, #4]
   0xc07c4618 <+140>:	str	r3, [r1, #8]
   0xc07c461c <+144>:	movw	r3, #52428	; 0xcccc
   0xc07c4620 <+148>:	str	r3, [r1]
   0xc07c4624 <+152>:	bl	0xc01de4c4 <ktime_get>
   0xc07c4628 <+156>:	mov	r3, #57600	; 0xe100
   0xc07c462c <+160>:	movt	r3, #1525	; 0x5f5
   0xc07c4630 <+164>:	mov	r2, #0
   0xc07c4634 <+168>:	adds	r6, r0, r3
   0xc07c4638 <+172>:	movw	r0, #42812	; 0xa73c
   0xc07c463c <+176>:	movt	r0, #49368	; 0xc0d8
   0xc07c4640 <+180>:	adc	r7, r1, #0
   0xc07c4644 <+184>:	mov	r1, #114	; 0x72
   0xc07c4648 <+188>:	bl	0xc015cb7c <__might_sleep>
   0xc07c464c <+192>:	ldr	r3, [r4, #100]	; 0x64
   0xc07c4650 <+196>:	ldr	r2, [r3, #12]
   0xc07c4654 <+200>:	tst	r2, #3

```

We can also disassemble with source code interleaved

```gdb
(gdb) disassemble /m 0xc07c45cc
```

This produces the following:

```gdb
Dump of assembler code for function stm32_iwdg_start:
88	{
   0xc07c458c <+0>:	mov	r12, sp
   0xc07c4590 <+4>:	push	{r4, r5, r6, r7, r11, r12, lr, pc}
   0xc07c4594 <+8>:	sub	r11, r12, #4
   0xc07c4598 <+12>:	push	{lr}		; (str lr, [sp, #-4]!)
   0xc07c459c <+16>:	bl	0xc0111b78 <__gnu_mcount_nc>
   0xc07c45ac <+32>:	mov	r5, r0

89		struct stm32_iwdg *wdt = watchdog_get_drvdata(wdd);

90		u32 tout, presc, iwdg_rlr, iwdg_pr, iwdg_sr;

91		int ret;

92	
93		dev_dbg(wdd->parent, "%s\n", __func__);
   0xc07c45a0 <+20>:	movw	r2, #16256	; 0x3f80
   0xc07c45a4 <+24>:	movt	r2, #49422	; 0xc10e
   0xc07c45b0 <+36>:	ldrh	r3, [r2, #42]	; 0x2a
   0xc07c45b4 <+40>:	tst	r3, #4
   0xc07c45b8 <+44>:	bne	0xc07c46ec <stm32_iwdg_start+352>
   0xc07c46ec <+352>:	ldr	r1, [r0, #4]
   0xc07c46f0 <+356>:	add	r0, r2, #24
   0xc07c46f4 <+360>:	ldr	r3, [pc, #12]	; 0xc07c4708 <stm32_iwdg_start+380>
   0xc07c46f8 <+364>:	movw	r2, #19168	; 0x4ae0
   0xc07c46fc <+368>:	movt	r2, #49370	; 0xc0da
   0xc07c4700 <+372>:	bl	0xc052416c <__dynamic_dev_dbg>
   0xc07c4704 <+376>:	b	0xc07c45bc <stm32_iwdg_start+48>
   0xc07c4708 <+380>:	adcsgt	r5, r6, r4, lsl #6

94	
95		tout = clamp_t(unsigned int, wdd->timeout,
   0xc07c45bc <+48>:	ldr	r2, [r5, #48]	; 0x30
   0xc07c45c0 <+52>:	movw	r1, #19923	; 0x4dd3
   0xc07c45c4 <+56>:	movt	r1, #4194	; 0x1062
   0xc07c45c8 <+60>:	ldr	r3, [r5, #28]
   0xc07c45d0 <+68>:	umull	r1, r2, r1, r2
   0xc07c45d4 <+72>:	ldr	r1, [r5, #36]	; 0x24
   0xc07c45d8 <+76>:	cmp	r3, r1
   0xc07c45dc <+80>:	lsr	r2, r2, #6
   0xc07c45e0 <+84>:	movcc	r3, r1
   0xc07c45e4 <+88>:	cmp	r3, r2
   0xc07c45e8 <+92>:	movcs	r3, r2

96			       wdd->min_timeout, wdd->max_hw_heartbeat_ms / 1000);
97	
98		presc = DIV_ROUND_UP(tout * wdt->rate, RLR_MAX + 1);
   0xc07c45cc <+64>:	ldr	r0, [r4, #112]	; 0x70
   0xc07c45ec <+96>:	mul	r3, r0, r3
   0xc07c45f8 <+108>:	lsr	r2, r2, #12

```
