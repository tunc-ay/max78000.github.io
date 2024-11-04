# System, Power, Clocks, Reset
Different peripherals and subsystems use several clocks. These clocks are highly configurable by software, allowing developers to select the combination of application performance and power savings required for the target systems. Support for selectable core operating voltage is provided, enabling optimal timing access to the internal memories.

## Oscillator Sources
### 100MHz Internal Primary Oscillator (IPO)
The MAX78000 includes a 100MHz internal high-speed oscillator, referred to in this document as the internal primary oscillator (IPO). The IPO is the highest frequency oscillator and draws the most power.

The IPO can optionally be powered down in LPM by setting the GCR_PM.ipo_pd field to 1.

The IPO can be selected as the SYS_OSC. Use the IPO as the SYS_OSC by performing the following steps:

1. Enable the IPO by setting GCR_CLKCTRL.ipo_en to 1.
2. Wait until the GCR_CLKCTRL.ipo_rdy field reads 1, indicating the IPO is operating.
3. Set GCR_CLKCTRL.sysclk_sel to 4.
4. Wait until the GCR_CLKCTRL.sysclk_rdy field reads 1. The IPO is now operating as the SYS_OSC.

### 60MHz Internal Secondary Oscillator (ISO)
The ISO is a low-power internal secondary oscillator that is the power-on reset default SYS_OSC. The ISO is automatically selected as SYS_OSC after a system reset or POR.

The following steps show how to enable the ISO and select it as the SYS_OSC.

1. Enable the ISO by setting GCR_CLKCTRL.iso_en to 1.
2. Wait until the GCR_CLKCTRL.iso_rdy field reads 1, indicating the ISO is operating.
3. Set GCR_CLKCTRL.sysclk_sel to 0.
4. Wait until the GCR_CLKCTRL.sysclk_rdy field reads 1. The ISO is now operating as the SYS_OSC.

### 8kHz-30kHz Internal Nano-Ring Oscillator (INRO)
The INRO is an ultra-low-power internal oscillator that can be selected as the SYS_OSC. The INRO is always enabled and cannot be disabled by software.

The frequency of this oscillator is configurable to 8kHz, 16kHz, or 30kHz. Use the TRIMSIR_INRO.lpclksel field to select the desired frequency. On a POR or system reset, the frequency defaults to 30kHz.

The following steps show how to set the INRO as the SYS_OSC.

1. Verify the GCR_CLKCTRL.inro_rdy field reads 1.
2. Set GCR_CLKCTRL.sysclk_sel to 3.
3. Wait until the GCR_CLKCTRL.sysclk_rdy field reads 1. The INRO is now operating as the SYS_OSC.

### 7.3728MHz Internal Baud Rate Oscillator (IBRO)
The IBRO is a very low-power internal oscillator that can be selected as SYS_OSC. The INRO can optionally be used as a dedicated baud rate clock for the UARTs. The INRO is useful if the selected SYS_OSC does not accurately generate a desired UART baud rate.

The following steps show how to enable the IBRO and select it as the SYS_OSC.

1. Wait until the GCR_CLKCTRL.ibro_rdy field reads 1, indicating the IBRO is operating.
2. Set GCR_CLKCTRL.sysclk_sel to 5.
3. Wait until the GCR_CLKCTRL.sysclk_rdy field reads 1. The IBRO is now operating as the SYS_OSC.

### 32.768kHz External Real-Time Clock Oscillator (ERTCO)
The ERTCO is an extremely low-power internal oscillator that can be selected as the SYS_OSC. The ERTCO can optionally use a 32.768kHz input clock or an 8kHz independent nano-ring oscillator instead of an external crystal. The internal 32.768kHz clock is available as an output on GPIO P3.1 as alternate function 1 (SQWOUT).

This oscillator is the default clock for the real-time clock (RTC). If the RTC is enabled, the ERTCO is enabled automatically, independent of the selection of the SYS_OSC. The ERTCO is disabled on a POR or system reset.

The following steps show how to enable the ERTCO and select it as the SYS_OSC.

1. Enable the ERTCO by setting GCR_CLKCTRL.ertco_en to 1.
2. Wait until the GCR_CLKCTRL.ertco_rdy field reads 1, indicating the ERTCO is operating.
3. Set GCR_CLKCTRL.sysclk_sel to 6.
4. Wait until the GCR_CLKCTRL.sysclk_rdy field reads 1. The ERTCO is now operating as the SYS_OSC.

## System Oscillator (SYS_OSC)
The MAX78000 supports multiple clock sources as the SYS_OSC. The selected SYS_OSC is the clock source for most internal blocks. Each oscillator, description, and nominal frequency are shown in [Table 4-1](#available-system-oscillators). An external clock source, EXT_CLK, is supported on P0.3, alternate function 1. Each of the oscillators/clocks is described in detail in section Oscillator Sources.

*Table 4-1: Available System Oscillators*
<a name="available-system-oscillators"></a>

<table border="1" cellpadding="5" cellspacing="0">
    <tr style="background-color: #e0e0e0; font-weight: bold; text-align: center">
        <th>Oscillator/Clock</th>
        <th>Description</th>
        <th>Nominal Frequency</th>
    </tr>
    <tr>
        <td>IPO</td>
        <td>Internal Primary Oscillator</td>
        <td>100MHz</td>
    </tr>
    <tr>
        <td>ISO</td>
        <td>Internal Secondary Oscillator</td>
        <td>60MHz</td>
    </tr>
    <tr>
        <td>INRO</td>
        <td>Internal Nano-Ring Oscillator</td>
        <td>Configurable 8kHz, 16kHz, or 30kHz</td>
    </tr>
    <tr>
        <td>IBRO</td>
        <td>Internal Baud Rate Oscillator</td>
        <td>7.3728MHz</td>
    </tr>
    <tr>
        <td>ERTCO</td>
        <td>External Real-Time Clock Oscillator</td>
        <td>32.768kHz</td>
    </tr>
    <tr>
        <td>EXT_CLK</td>
        <td>External Clock</td>
        <td>Up to 80MHz</td>
    </tr>
</table>

### System Oscillator Selection
Set the system oscillator using the GCR_CLKCTRL.sysclk_sel field. Before selecting an oscillator as the system oscillator, the oscillator source must first be enabled and ready. See each oscillator source’s detailed description for the required steps to enable the oscillator and select it as the system oscillator.

When the GCR_CLKCTRL.sysclk_sel is modified, hardware clears the GCR_CLKCTRL.sysclk_rdy field, and there is a delay until the switchover is complete. When the switchover to the selected SYS_OSC is complete, the GCR_CLKCTRL.sysclk_rdy field is set to 1 by hardware. The application software must verify that the switchover is complete before continuing operation.

### System Clock (SYS_CLK)
The selected SYS_OSC is the input to the system oscillator divider to generate the system clock (SYS_CLK). The system clock divider divides the selected SYS_OSC by the GCR_CLKCTRL.sysclk_div field, as shown in [Equation 4-1](#equation-4-1-system-clock-scaling).

*Equation 4-1: System Clock Scaling*
<a name="equation-4-1-system-clock-scaling"></a>

SYS_CLK = SYS_OSC / 2<sup>sysclk_div</sup>

GCR_CLKCTRL.sysclk_div is selectable from 0 to 7, resulting in divisors of 1, 2, 4, 8, 16, 32, 64 or 128.

SYS_CLK drives the Arm core, the RV32 core, and all AHB masters in the system. SYS_CLK generates the following internal clocks as shown below:

- AHB Clock
    - HLCK = SYS_CLK
- APB Clock
    - PCLK = SYS_CLK / 2

The RTC uses the ERTCO for its clock source. Optionally, the RTC can run using an internal dedicated 8kHz nano-ring oscillator. See the Real-Time Clock (RTC) chapter for details on using this 8kHz nano-ring oscillator for the RTC.

All oscillators are reset to their POR reset default state during:
- Power-On Reset
- System Reset

Oscillator settings are *not* reset during:
- Soft Reset
- Peripheral Reset

*Table 4-2* shows each oscillator’s enabled state for each type of reset source in the MAX78000.
*Note: A Watchdog Timer Reset performs a System Reset.*

*Table 4-2: Reset Sources and Effect on Oscillator and System Clock*
<a name="reset-sources-effect-oscillator-system-clock"></a>

<table border="1" cellpadding="5" cellspacing="0">
    <tr style="background-color: #e0e0e0; font-weight: bold; text-align: center">
        <th></th>
        <th colspan="4" align="center">Reset Source</th>
    </tr>
    <tr>
        <th style="background-color: #e0e0e0; font-weight: bold; text-align: center">Oscillator</th>
        <th>POR</th>
        <th>System</th>
        <th>Soft</th>
        <th>Peripheral</th>
    </tr>
    <tr>
        <td style="background-color: #e0e0e0; font-weight: bold; text-align: center">IPO</td>
        <td>Disabled</td>
        <td>Disabled</td>
        <td>Retains State</td>
        <td>Retains State</td>
    </tr>
    <tr>
        <td style="background-color: #e0e0e0; font-weight: bold; text-align: center">ISO</td>
        <td>Enabled</td>
        <td>Enabled</td>
        <td>Retains State</td>
        <td>Retains State</td>
    </tr>
    <tr>
        <td style="background-color: #e0e0e0; font-weight: bold; text-align: center">IBRO</td>
        <td>Enabled</td>
        <td>Enabled</td>
        <td>Enabled</td>
        <td>Enabled</td>
    </tr>
    <tr>
        <td style="background-color: #e0e0e0; font-weight: bold; text-align: center">INRO</td>
        <td>Enabled</td>
        <td>Enabled</td>
        <td>Enabled</td>
        <td>Enabled</td>
    </tr>
    <tr>
        <td style="background-color: #e0e0e0; font-weight: bold; text-align: center">ERTCO</td>
        <td>Disabled</td>
        <td>Disabled</td>
        <td>Retains State</td>
        <td>Retains State</td>
    </tr>
    <tr>
        <td style="background-color: #e0e0e0; font-weight: bold; text-align: center">System Clock<br>(SYS_OSC) Source</td>
        <td>ISO</td>
        <td>ISO</td>
        <td>Retains State</td>
        <td>Retains State</td>
    </tr>
</table>

Figure 4-1: MAX78000 Clock Block Diagram shows a high-level diagram of the MAX78000 clock tree.
*Figure 4-1: MAX78000 Clock Block Diagram*

## Operating Modes
The MAX78000 includes multiple operating modes and the ability to fine-tune power options to optimize performance and power. The system supports the following operating modes:

- ACTIVE
- SLEEP
- Low-Power Mode (LPM)
- Micro Power Mode (UPM)
- STANDBY
- BACKUP
- Power Down Mode (PDM)

### ACTIVE Mode
In this mode, both the CM4 and the RV32 cores can execute software, and all digital and analog peripherals are available on demand. Dynamic clocking disables peripheral not in use, providing the optimal mix of high performance and low power consumption. The CM4 has access to all System RAM by default. The RV32 has access to sysram2 and sysram3 and can be optionally configured to have exclusive access to these RAMs. Additionally, sysram3 can be configured as a unified internal cache controller for the RV32 allowing simultaneous data access and code execution for the CM4 and RV32 from the internal flash memory.

Each of the peripherals can be individually enabled during active mode or powered down. The CNN and each of the four CNNx16_n Processor Arrays and their associated memories can be powered down or set to active mode.

### Low-Power Modes
#### SLEEP
This mode consumes less power but wakes faster because the clocks can optionally be enabled.
The device status is as follows:

- The CM4 (CPU0) is sleeping
- The RV32 (CPU1) is sleeping
- The CNN is optionally available for use
- Each of the four CNNx16_n quadrants is individually configurable for power down
- Standard DMA is available for use
- All peripherals are on unless explicitly disabled before entering SLEEP

##### Entering SLEEP
Entering SLEEP requires both the CM4 and RV32 to cooperate to enter SLEEP. Synchronization is necessary for deterministic entry into SLEEP. Two methods are described below, allowing either core to request entry into SLEEP. Both methods use the semaphore peripheral interrupt to communicate between the cores.

If the RV32 is driving entry to SLEEP, the RV32 notifies the CM4 of a request to enter SLEEP using Multiprocessor Communications. The CM4 receives the notification and then sends confirmation through the semaphore peripheral to the RV32. The CM4 should then enter SLEEP by setting the SCR.sleepdeep field to 0 and performing a WFI or WFE instruction. The RV32 should then enter SLEEP by performing a WFI instruction or by setting GCR_PM.mode to 1, followed by two NOP instructions.

Alternatively, the CM4 can initiate the request to enter SLEEP by sending the request to the RV32 using Multiprocessor Communications. The RV32 confirms the request through Multiprocessor Communications and performs a WFI instruction followed by two NOP instructions. The CM4 should then enter SLEEP by setting SCR.sleepdeep to 0 and performing a WFI or WFE instruction or by setting GCR_PM.mode to 1.

*Figure 4-2: SLEEP Mode Clock Control*

#### LPM
This mode is suitable for running the RV32 processor to collect and move data from enabled peripherals. The device status is a follows:

- The CM4, sysram0, and sysram1 are in state retention
- The CNN quadrants and memory are active and configurable.
- The RV32 can access the SPI, UARTS, Timers, I2C, 1-Wire, Timers, Pulse Train Engine, I2S, CRC, AES, TRNG, Comparators, as well as sysram2 and sysram3. Sysram3 can be configured to operate as the RV32 unified instruction cache
- The transition from LPM to ACTIVE is faster than the transition from BACKUP to ACTIVE because system initialization is not required
- The DMA is in state retention mode
- PWRSEQ_GP0 and PWRSEQ_GP1 registers retain state
- Choose the system PCLK or ISO as the clock source for the RV32 and all peripherals 
    - PWRSEQ_LPCN.lpmclksel defaults to use ISO during LPM. Setting this field to 1 uses the PCLK
- The following oscillators are powered down by default, but can be configured by software to remain active:
    - ISO
    - IPO
    - ERTCO
    - INRO
- The following oscillator is enabled:
    - IBRO

##### Entering LPM
Entry into LPM should be managed between the two cores using Multiprocessor Communications to ensure both cores are in a known state when entering LPM.

When the CM4 puts itself into deep sleep, the device automatically enters LPM, and hardware sets the GCR_PM.mode to LPM. To place the CM4 in LPM mode in software, perform the following instructions.

SCR.sleepdeep = 1;  // deep sleep mode enabled
WFI (or WFE);   // Enter deep sleep mode

If the RV32 requests the CM4 to enter LPM mode through Multiprocessor Communications and the CM4 enters SLEEP instead, by setting SCR.sleepdeep to 0 and performing a WFI or WFE instruction, the RV32 can put the device into LPM by directly setting the GCR_PM.mode field to LPM (8).

*Note: The device immediately enters LPM when the GCR_PM.mode field is set to LPM. If the CM4 is not in a known state, issues may occur when exiting LPM.*

*Figure 4-3: LPM Clock and State Retention Diagram*

#### UPM
This mode is used for extremely low power consumption while using a minimal set of peripherals to provide wake-up capability. The device status during UPM is:

- Both CM4 and RV32 are state retained.
- System state and all system RAM are retained
- CNN quadrants are optionally powered off
- CNN memory provides selectable retention
- The GPIO pins retain their state
- All non-UPM peripherals are state retained
- The following oscillators are powered down:
    - IPO
    - ISO
- The following oscillators are enabled:
    - IBRO
    - ERTCO, firmware configurable
    - INRO, firmware configurable
- The following UPM peripherals are available for use to wake the device:
    - LPUART0
    - LPTMR0
    - LPTMR1
    - LPWDT0
    - LPCOMP0-LPCOMP3
    - GPIO

##### Entering UPM
Entering UPM mode requires both the CM4 and RV32 to cooperate to enter UPM mode. Synchronization is necessary for deterministic entry into UPM. Two methods are described below, allowing either core to request entry into UPM and ensuring deterministic entry. Both methods use the Semaphore peripheral interrupt to communicate between the cores.

If the RV32 is driving entry to UPM, the RV32 notifies the CM4 of a request to enter UPM using Multiprocessor Communications. The CM4 receives the notification and then sends a confirmation through the semaphore peripheral to the RV32. The CM4 should then enter SLEEP by setting SCR.sleepdeep to 0 and performing a WFI or WFE instruction. The RV32 sets the GCR_PM.mode to UPM, followed by two NOP instructions, and the device immediately enters UPM.

Alternatively, the CM4 can initiate the request to enter UPM by sending the request to the RV32 using Multiprocessor Communications. The RV32 confirms the request through Multiprocessor Communications and performs a WFI instruction, followed by two NOP instructions. The CM4 then sets the GCR_PM.mode to UPM, and the device immediately enters UPM.

*Figure 4-4: UPM Clock and State Retention Block Diagram*

#### STANDBY
This mode is used to maintain the system operation while keeping time with the RTC. The device status is as follows:

- Both CM4 and RV32 are state retained.
- System state and all system RAM is retained
- CNN quadrants are powered off
- CNN memory provides selectable retention (optional state retention)
- GPIO pins retain their state
- All peripherals retain state
- The following oscillators are powered down:
    - IPO
    - ISO
    - IBRO
- The following oscillators are enabled:
    - ERTCO, firmware configurable
    - INRO

##### Entering STANDBY
Entering STANDBY requires both the CM4 and RV32 to enter STANDBY mode. Synchronization is necessary for deterministic entry into STANDBY. Two methods are described below, allowing either core to request entry into STANDBY and ensuring deterministic entry. Both methods use the semaphore peripheral interrupt to communicate between the cores.

If the RV32 is driving entry to STANDBY, the RV32 notifies the CM4 of a request to enter STANDBY using Multiprocessor Communications. The CM4 receives the notification and then sends a confirmation through the semaphore peripheral to the RV32. The CM4 should then enter SLEEP by setting SCR.sleepdeep to 0 and performing a WFI or WFE instruction. The RV32 sets the GCR_PM.mode to STANDBY, followed by two NOP instructions, and the device immediately enters into STANDBY.

Alternatively, the CM4 can initiate the request to enter STANDBY by sending the request to the RV32 using Multiprocessor Communications. The RV32 confirms the request through Multiprocessor Communications and performs a WFI instruction followed by two NOP instructions. The CM4 then sets the GCR_PM.mode to STANDBY, and the device immediately enters STANDBY.

*Figure 4-5: STANDBY Mode Clock and State Retention Block Diagram*

#### BACKUP
This mode is used to maintain the System RAM. The device status is as follows:

- CM4 and RV32 are powered off.
- Sysram0, sysram1, sysram2, and sysram3 can be independently configured for state retention, as shown in Table 4-3.
- User-configurable CNN memory retention
- All peripherals are powered off
- The following oscillators are powered down:
    - IPO
    - ISO
    - IBRO
    - INRO
- The following oscillators are enabled:
    - ERTCO (The RTC peripheral can be turned off, but not the oscillator)

*Table 4-3 System RAM Retention in BACKUP Mode*

##### Entering BACKUP
Entering BACKUP mode does not require synchronization between the RV32 and CM4 cores. However, it is recommended that Multiprocessor Communications are used to ensure both cores are aware of entry into BACKUP and complete any memory transactions before entry.

Either core can set GCR_PM.mode to BACKUP, and the device immediately enters BACKUP.

*Figure 4-6: BACKUP Mode Clock and State Retention Block Diagram*

#### PDM
This mode is used during product level distribution and storage. The device status is as follows:

- The CM4 and RV32 are powered off
- All peripherals and all RAMs are powered down
- All oscillators are powered down
- There is no data retention in this mode, but values in the flash are preserved
- V<sub>REGI</sub> POR voltage monitor is operational.
- Exit from PDM is possible through an external reset (RSTN) or a wake-up event using either P3.0 or P3.1 if configured.

##### Entering PDM
Entering PDM does not require synchronization between the RV32 and CM4 cores. However, it is recommended that Multiprocessor Communications is used to ensure both cores are aware of entry into PDM and complete any flash memory transactions.

Either core can set GCR_PM.mode to PDM, and the device immediately enters PDM.

*Figure 4-7: PDM Clock and State Retention Block Diagram*

## Wake-Up Sources for Each Operating Mode
In all operating modes other than ACTIVE, wake-up sources are required to re-enter ACTIVE operation. [Table 4-4](#table4-4-wakeup-sources-for-each-operating-mode-in-78000) shows available wake-up sources for each operating mode of the MAX78000.

*Note: Each wake-up source must be enabled individually except for External Reset, which is hardware controlled.*

*Table 4-4: Wake-Up Sources for Each Operating Mode in the MAX78000*
<a name="table4-4-wakeup-sources-for-each-operating-mode-in-78000"></a>

<table>
    <tr>
        <th>Operating Mode</th>
        <th>Any Peripheral Interrupts</th>
        <th>External Reset</th>
        <th>RV32</th>
        <th>CNN</th>
        <th>CNN FIFO</th>
        <th>SPI1</th>
        <th>SPI0</th>
        <th>Any Peripheral Interrupts</th>
        <th>I2C</th>
        <th>I2C2</th>
        <th>I2C1</th>
        <th>I2C0</th>
        <th>LPUART0 (UART3)</th>
        <th>UART2</th>
        <th>UART1</th>
        <th>UART0</th>
        <th>LPUART0 (TMR5)</th>
        <th>LPUART0 (TMR4)</th>
        <th>TMR3</th>
        <th>TMR2</th>
        <th>TMR1</th>
        <th>TMR0</th>
        <th>LPWDT0 (WDT1)</th>
        <th>WDT0</th>
        <th>LPCOMP3</th>
        <th>LPCOMP2</th>
        <th>LPCOMP1</th>
        <th>COMP0</th>
        <th>RTC</th>
        <th>WUT</th>
        <th>GPIO3</th>
        <th>GPIO2</th>
        <th>GPIO1</th>
        <th>GPIO0</th>
    </tr>
    <tr>
        <td>SLEEP</td>
        <td>x</td><td>x</td><td>x</td><td>x</td><td></td><td></td><td></td><td></td>
        <td></td><td></td><td></td><td>x</td><td></td><td></td><td></td><td></td>
        <td></td><td></td><td></td><td></td><td></td><td></td><td></td><td></td>
        <td></td><td></td><td></td><td>x</td><td></td><td></td><td>x</td>
    </tr>
    <tr>
        <td>LPM</td>
        <td></td><td></td><td></td><td></td><td></td><td></td><td></td><td></td>
        <td></td><td></td><td></td><td></td><td></td><td></td><td></td><td></td>
        <td></td><td></td><td></td><td></td><td></td><td></td><td></td><td></td>
        <td></td><td></td><td></td><td></td><td></td>
    </tr>
    <tr>
        <td>UPM</td>
        <td></td><td></td><td></td><td></td><td></td><td></td><td></td><td></td>
        <td></td><td></td><td></td><td></td><td></td><td></td><td></td>
    </tr>
    <tr>
        <td>STANDBY</td>
        <td></td><td></td><td></td><td></td><td></td><td></td><td></td><td></td>
    </tr>
    <tr>
        <td>BACKUP</td>
        <td></td><td></td><td></td><td></td><td></td><td></td><td></td><td></td>
    </tr>
    <tr>
        <td>PDM</td>
        <td></td><td></td>
    </tr>
</table>

## Device Resets
Four device resets are available:

- Peripheral Reset
- Soft Reset
- System Reset
- Power-On Reset

On completion of any of the four reset cycles, all peripherals are reset. On completion of any reset cycle, HCLK and PCLK are operational, the CPU core receives clocks and power, and the device is in ACTIVE. Program execution begins at the reset vector address.

The contents of the always-on domain (AoD) are reset only on power-cycling VCOREA, VCOREB, VDDA, VDDIOH, or VREGI.

The on-chip peripherals can also be reset to their POR default state using the two reset registers, GCR_RST0 and GCR_RST1.

[Table 4-5]() shows the effects of each reset type on each of the operating modes.

*Table 4-5: Reset and Low-Power Mode Effects*

### Peripheral Reset
Peripheral reset resets all peripherals. The CPU retains its state. The GPIO, watchdog timers, AoD, RAM retention, and general control registers (GCR), including the clock configuration, are unaffected.

To start a peripheral reset, set GCR_RST0.periph to 1. The reset is completed immediately upon setting GCR_RST0.periph to 1.

### Soft Reset
A soft reset is the same as a peripheral reset except that it also resets the GPIO to its POR state.

To perform a soft reset, set GCR_RST0.soft to 1. The reset occurs immediately upon setting GCR_RST0.soft to 1.

### System Reset
A system reset is the same as a soft reset, except it also resets all GCR, resetting the clocks to their POR default state. The CPU state is reset, as well as the watchdog timers. The AoD and RAM are unaffected.

A watchdog timer reset event initiates a system reset. To start a system reset, set GCR_RST0.sys to 1.

### Power-On Reset
A POR resets everything in the device to its default state. A POR results from V<sub>COREA</sub>, V<sub>COREB</sub>, V<sub>DDA</sub>, or V<sub>REGI</sub> falling below their reset voltage level. Refer to the MAX78000 data sheet for details of the reset voltage levels.

## Unified Internal Cache Controllers
The MAX78000 includes two unified internal cache controllers. ICC0 is the cache controller used for the CM4. ICC1, if enabled, is dedicated to the RV32 core. ICC1 uses sysram3 as the cache memory. If ICC1 is enabled, sysram3 is not accessible as SRAM (address range 0x2001 C000 to 0x2001 FFFF).

Both caches, ICC0 and ICC1, include a line buffer, tag RAM, and a 16KB 2-way set associative RAM when enabled.

### Enabling the Internal Cache Controllers
Enabling ICC1 for use as the cache controller for the RV32 requires using *sysram3* as the cache memory.
*Note: The contents of sysram3 are lost when ICC1 is enabled, and sysram3 is not accessible for data reads or writes as part of the memory map.*
*Note: Before enabling ICC1 as a cache controller, sysram3 should be zeroized.*

Perform the following steps to enable each ICC:

1. Set the ICCn_CTRL.en to 0, ensuring the cache is invalidated when enabled.
2. Set ICCn_CTRL.en to 1.
3. Read ICCn_CTRL.rdy until it returns 1.
4. Zeroize the ICC instance by setting GCR_MEMZ.icc0 or GCR_MEMZ.icc1 to 1.

### Disabling the ICC
Disable an ICC instance by setting ICCn_CTRL.en to 0.

To use sysram3 as data RAM, first, disable the ICC1 instance as described above. When ICC1 is disabled, sysram3 is accessible as data RAM by both the CM4 and RV32 controllers unless sysram3 is configured for exclusive access by the RV32 core only.

### Invalidating the ICC Cache and Tag RAM
Invalidate the contents of a specific ICC instance by setting the ICCn_INVALIDATE register to 1. Once invalidated, the system flushes the cache. Read the ICCn_CTRL.rdy field until it returns 1 to determine when the flush is completed.

### Flushing the ICC
Flush ICC0 using the system configuration register (GCR_SYSCTRL). Set GCR_SYSCTRL.icc0_flush to 1 to immediately flush the contents of the 16KB cache and tag RAM.

Flush ICC1 using the RV32 Control Register (FCR_URVCTRL). Set FCR_URVCTRL.icc1_flush to 1 to immediately flush the contents of the 16KB cache and tag RAM.

### Internal Cache Control Registers (ICC)