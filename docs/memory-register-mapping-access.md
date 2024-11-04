# Memory, Register, Mapping, and Access
## Overview
The Arm Cortex-M4 architecture defines a standard memory space for unified code and data access. This memory space is addressed in units of single bytes but is most typically accessed in 32-bit (4 byte) units. It may also be accessed, depending on the implementation, in 8-bit (1 byte) or 16-bit (2 byte) widths. The total range of the memory space is 32 bits wide (4GB addressable total), from addresses 0x0000 0000 to 0xFFFF FFFF.

However, it is important to note that the architectural definition does not require the entire 4GB memory range to be populated with addressable memory instances.

## Standard Memory Regions
Several standard memory regions are defined for the Arm Cortex-M4 (CPU0) and RISC-V (CPU1) architectures; many of these are optional for the system integrator. At a minimum, the MAX78000 must contain some code and data memory for application software, stack, and variable space for CPU0.

### Code Space
The code space area of memory is designed to contain the primary memory used for code execution by the device. This memory area is defined from byte address range 0x0000 0000 to 0x1FFF FFFF (0.5GB maximum). The Cortex-M4 core and Arm debugger use two different standard core bus masters to access this memory area. The I-Code AHB bus master is used for instruction decode fetching from code memory, while the D-Code AHB bus master is used for data fetches from code memory. This is arranged so that data fetches avoid interfering with instruction execution. Additionally, the RV32 uses the D-BUS to access code memory in this area and the I-Bus to access data fetches from the code memory.

The MAX78000 code memory mapping is illustrated in Figure 3-1 and Figure 3-2. The code space memory area contains the main internal flash memory, which holds most of the software executed on the device. The internal flash memory is mapped into both code and data space from 0x1000 0000 to 0x1007 FFFF. The main program flash memory is 512KB and consists of 64 logical pages of 8,192 Bytes per page.

This program memory area must also contain the default system vector table and the initial settings for all system exception handlers and interrupt handlers for the CM4 core. The reset vector for the device is 0x0000 0000 and contains the device ROM code that transfers execution to user code at address 0x1000 0000.

The code space memory on the MAX78000 also contains the mapping for the flash information block, from 0x1080 0000 to 0x1080 3FFF. However, this mapping is only present during production test; it is disabled once the information block has been loaded with valid data and the info block lockout option has been set. This memory is accessible for data reads only and cannot be used for code execution. See Information Block Flash Memory for additional details.

### Internal Cache Memory
The MAX78000 includes a dedicated unified internal cache controller with 16,384 bytes of internal cache memory (ICC0) for the CM4 core. Optionally, sysram3 can be used as a unified internal cache controller (ICC1) for the RV32.

The unified internal cache memory is used to cache data and instructions fetched through the I-Code bus for the CM4 or the IBUS for the RV32 from the internal flash memory. See section Unified Internal Cache Controller for detailed instructions on enabling the unified internal cache controllers.

### Information Block Flash Memory
The information block is a separate area of the internal flash memory and is 16,384 Bytes. The information block is used to store trim settings (option configuration and analog trim) and other nonvolatile device-specific information. The information block also contains the device's unique serial number (USN). The USN is a 104-bit field. USN bits 0 thru 7 contain the die revision.

Figure 3-5: Unique Serial Number Format
<a name="unique-serial-number-format"></a>

<table border="1" cellpadding="5" cellspacing="0">
   <tr>
       <td colspan="2"></td>
       <td style="background-color: #e0e0e0; font-weight: bold; text-align: center" colspan="32">Bit Position</td>
   </tr>
   <tr>
       <td colspan="2"></td>
       <td>31</td>
       <td>30</td>
       <td>29</td>
       <td>28</td>
       <td>27</td>
       <td>26</td>
       <td>25</td>
       <td>24</td>
       <td>23</td>
       <td>22</td>
       <td>21</td>
       <td>20</td>
       <td>19</td>
       <td>18</td>
       <td>17</td>
       <td>16</td>
       <td>15</td>
       <td>14</td>
       <td>13</td>
       <td>12</td>
       <td>11</td>
       <td>10</td>
       <td>9</td>
       <td>8</td>
       <td>7</td>
       <td>6</td>
       <td>5</td>
       <td>4</td>
       <td>3</td>
       <td>2</td>
       <td>1</td>
       <td>0</td>
   </tr>
   <tr>
       <td colspan="1"></td>
       <td><strong>0x10800000</strong></td>
       <td align="center" colspan="17">USN bits 16-0</td>
       <td>x</td>
       <td>x</td>
       <td>x</td>
       <td>x</td>
       <td>x</td>
       <td>x</td>
       <td>x</td>
       <td>x</td>
       <td>x</td>
       <td>x</td>
       <td>x</td>
       <td>x</td>
       <td>x</td>
       <td>x</td>
       <td>x</td>
   </tr>
   <tr>
       <td colspan="1"></td>
       <td><strong>0x10800004</strong></td>
       <td colspan="1">x</td>
       <td align="center" colspan="31">USN bits 47-17</td>
   </tr>
   <tr>
       <td colspan="1"></td>
       <td><strong>0x10800000</strong></td>
       <td align="center" colspan="17">USN bits 64-48</td>
       <td>x</td>
       <td>x</td>
       <td>x</td>
       <td>x</td>
       <td>x</td>
       <td>x</td>
       <td>x</td>
       <td>x</td>
       <td>x</td>
       <td>x</td>
       <td>x</td>
       <td>x</td>
       <td>x</td>
       <td>x</td>
       <td>x</td>
   </tr>
   <tr>
       <td colspan="1"></td>
       <td><strong>0x1080000C</strong></td>
       <td colspan="1">x</td>
       <td align="center" colspan="31">USN bits 95-65</td>
    </tr>
    <tr>
       <td colspan="1"></td>
       <td><strong>0x10800010</strong></td>
       <td>x</td>
       <td>x</td>
       <td>x</td>
       <td>x</td>
       <td>x</td>
       <td>x</td>
       <td>x</td>
       <td>x</td>
       <td>x</td>
       <td colspan="8">USN bits 103-96</td>
       <td>x</td>
       <td>x</td>
       <td>x</td>
       <td>x</td>
       <td>x</td>
       <td>x</td>
       <td>x</td>
       <td>x</td>
       <td>x</td>
       <td>x</td>
       <td>x</td>
       <td>x</td>
       <td>x</td>
       <td>x</td>
       <td>x</td>
   </tr>
</table>

Reading the USN requires unlocking the information block. Unlocking the information block does not enable write access to the block but allows the contents of the USN to be read from the block. Unlock the information block using the following steps:

1. Write 0x3A7F 5CA3 to [FLC_ACTRL](flash-controller.md#flash-controller-access-control-register).
2. Write 0xA1E3 4F20 to [FLC_ACTRL](flash-controller.md#flash-controller-access-control-register).
3. Write 0x9608 B2C1 to [FLC_ACTRL](flash-controller.md#flash-controller-access-control-register).
4. The information block is now read-only accessible.

To re-lock the information block to prevent access, write any 32-bit word to [FLC_ACTRL](flash-controller.md#flash-controller-access-control-register).

### SRAM Space
The SRAM area of memory is intended to contain the primary SRAM data memory of the device and is defined from byte address range 0x2000 0000 to 0x3FFF FFFF (0.5GB maximum). This memory can be used for general-purpose variable and data storage, code execution, the CM4 stack, and the RV32 stack.
The MAX78000 CM4's data memory mapping is illustrated in Figure 3-1. The MAX78000 RV32's data memory mapping is illustrated in Figure 3-4.

The system SRAM configuration is defined in Table 3-1. Additionally, the CNN memory is covered in the CNN chapter in the section Memory Configuration.

The SRAM area contains the main system RAM. The size of the internal general-purpose data SRAM is 128KB. The SRAM is divided into four blocks and consists of the contiguous address range from 0x2000 0000 to 0x2001 FFFF. The SRAM area on the MAX78000 can be used for data storage and code execution by the CM4. The RV32 is limited to sysram2 and sysram3 for code and data storage.

Note: After a POR, the CM4 has access to all four SRAM regions. sysram2 and sysram3 can be configured to restrict access from the CM4 to prevent unintended modifications of these SRAM instances by the CM4. Set the FCR_URVCTRL.memsel field to 1 to set the RV32 core as the exclusive master for sysram2 and sysram3.

Code stored in the SRAM is accessed directly for execution (using the system bus) and is not cached. The SRAM is also where the CM4 and RV32 stack must be located, as it is the only general-purpose SRAM on the device capable of this function.

*Table 3-1: System SRAM Configuration*
<a name="system-sram-configuration"></a>

<table>
    <tr style="background-color: #e0e0e0; font-weight: bold; text-align: center">
        <th>System RAM Block #</th>
        <th>Size</th>
        <th>Start Address</th>
        <th>End Address</th>
        <th>CM4 Accessible</th>
        <th>RV32 Accessible</th>
    </tr>
    <tr>
        <td>sysram0</td>
        <td>32KB</td>
        <td>0x2000 0000</td>
        <td>0x2000 7FFF</td>
        <td>✓</td>
        <td>No</td>
    </tr>
    <tr>
        <td>sysram1</td>
        <td>32KB</td>
        <td>0x2000 8000</td>
        <td>0x2000 FFFF</td>
        <td>✓</td>
        <td>No</td>
    </tr>
    <tr>
        <td>sysram2</td>
        <td>48KB</td>
        <td>0x2001 0000</td>
        <td>0x2001 BFFF</td>
        <td>Configurable</td>
        <td>✓</td>
    </tr>
    <tr>
        <td>sysram3</td>
        <td>16KB</td>
        <td>0x2001 C000</td>
        <td>0x2001 FFFF</td>
        <td>Configurable</td>
        <td>✓ (Optional ICC1)</td>
    </tr>
</table>

The MAX78000 specific AHB Bus Masters can access the SRAM to use as general storage or working space.

The entirety of the SRAM space on the MAX78000 is contained within the dedicated Arm Cortex-M4 SRAM bit-banding region from 0x2000 0000 to 0x200F FFFF (1MB maximum for bit-banding). This means that the CPU can access the entire SRAM either using standard byte/word/doubleword access or using bit-banding operations. The bit-banding mechanism allows any single bit of any given SRAM byte address location to be set, cleared, or read individually by reading from or writing to a corresponding doubleword (32-bit wide) location in the bit-banding alias area.

The alias area for the SRAM bit-banding is located beginning at 0x2200 0000 and is a total of 32MB maximum, which allows the entire 128KB bit banding area to be accessed. Each 32-bit (4 byte aligned) address location in the bit-banding alias area translates into a single bit access (read or write) in the bit-banding primary area. Reading from the location performs a single bit read while writing either a 1 or 0 to the location performs a single bit set or clear.

*Note: The Arm Cortex-M4 core translates the access in the bit-banding alias area into the appropriate read cycle (for a single bit read) or a read-modify-write cycle (for a single bit set or clear) of the bit-banding primary area. Bit-banding is a core function (i.e., not a function of the SRAM interface layer or the AHB bus layer) and thus is only applicable to accesses generated by the core. Reads and writes to the bit-banding alias area by other (non-Arm-core) bus masters does not trigger a bit-banding operation and instead results in an AHB bus error.*

### Peripheral Space
The peripheral space area of memory is intended to map control registers, internal buffers, and other features needed for the software control of non-core peripherals. It is defined from byte address range 0x4000 0000 to 0x5FFF FFFF (0.5GB maximum). On the MAX78000, all device-specific module registers are mapped to this memory area and any local memory buffers or FIFOs that are required by modules.

As with the SRAM region, there is a dedicated 1MB area at the bottom of this memory region (from 0x4000 0000 to 0x400F FFFF) used for bit-banding operations by the Arm core. Four-byte-aligned read/write operations in the peripheral bit-banding alias area (32MB in length, from 0x4200 0000 to 0x43FF FFFF) are translated by the core into read/mask/shift or read/modify/write operation sequences to the appropriate byte location in the bit-banding area.

*Note: The bit-banding operation within peripheral memory space is, like bit-banding function in SRAM space, a core remapping function. As such, it is only applicable to operations performed directly by the Arm core. If another memory bus master accesses the peripheral bit-banding alias region, the bit-banding remapping operation does not occur. In this case, the bit-banding alias region appears to be a non-implemented memory area (causing an AHB bus error).*

On the MAX78000, access to the region containing most peripheral registers (0x4000 0000 to 0x400F FFFF) goes from the AHB bus through an AHB-to-APB bridge enabling the peripheral modules to operate on the lower power APB bus matrix. This also ensures that peripherals with slower response times do not tie up bandwidth on the AHB bus, which must necessarily have a faster response time since it handles main application instruction and data fetching.

### AES Key and Working Space Memory
The AES key memory and working space for AES operations (including input and output parameters) are in a dedicated register file memory tied to the AES engine block. This AES memory is mapped into AHB space for rapid software access.

### System Area (Private Peripheral Bus)
The system area (private peripheral bus) memory space contains register areas for functions that are only accessible by the Arm core itself (and the Arm debugger, in certain instances). It is defined from byte address range 0xE000 0000 to 0xE00F FFFF. This APB bus is restricted and can only be accessed by the Arm core and core-internal functions. It cannot be accessed by other modules which implement AHB memory masters, such as the DMA interface.

In addition to being restricted to the core, application software can only access this area when running in privileged execution mode (instead of the standard user thread execution mode). This helps ensure that critical system settings controlled in this area are not altered inadvertently or by errant code that should not access this area.

Core functions controlled by registers mapped to this area include the SysTick timer, debug and tracing functions, the nested vector interrupt controller (NVIC), and the flash breakpoint controller.

## AHB Interfaces
The following sections detail memory accessibility on the AHB and the organization of AHB master and slave instances.

### Arm Core AHB Interfaces
#### I-Code
The Arm core uses the I-Code AHB master for instruction fetching from memory instances located in code space from byte addresses 0x0000 0000 to 0x1FFF FFFF. This bus master is used to fetch instructions from the internal flash memory.

Instructions fetched by this bus master are returned by the cache, which in turn triggers a cache line fill cycle to fetch instructions from the internal flash memory when a cache miss occurs.

#### D-Code
The Arm core uses the D-Code AHB master for data fetches from memory instances in code space from byte addresses 0x0000 0000 to 0x1FFF FFFF. This bus master has access to the internal flash memory and the information block.

#### System
The Arm core uses the system AHB master for all instruction fetches, and data read and write operations involving the SRAM data cache. The APB mapped peripherals (through the AHB-to-APB bridge) and AHB mapped peripheral and memory areas are also accessed using this bus master.

### AHB Slaves
#### Standard DMA
The standard DMA AHB slave has access to all non-core memory areas accessible by the system bus. The standard DMA does not have access to the internal flash memory or Information blocks.

#### CNN and CNN TX FIFO
The CNN and CNN TX FIFO AHB slaves have access to all non-core memory areas accessible by the system bus. They do not have access to the internal flash memory or information blocks.

#### SPIO 
The SPI0 AHB slave has access to all non-core memory areas accessible by the system bus. SPI0 does not have access to the internal flash memory or information blocks.

### AHB Slave Base Address Map
[Table 3-2](#ahb-slave-base-address-map) contains the base address for each of the AHB slave peripherals. The base address for a given peripheral is the start of the register map for the peripheral. For a given peripheral, the address for a register within the peripheral is defined as the peripheral's AHB base address plus the register's offset.

*Table 3-2: AHB Slave Base Address Map*
<a name="ahb-slave-base-address-map"></a>

<table border="1" cellpadding="5" cellspacing="0">
    <tr style="background-color: #e0e0e0; font-weight: bold; text-align: center">
        <th>AHB Slave Register Name</th>
        <th>Register Prefix</th>
        <th>AHB Base Address</th>
        <th>AHB End Address</th>
    </tr>
    <tr>
        <td>SPI0</td>
        <td>SPI0_</td>
        <td>0x400B E000</td>
        <td>0x400B E3FF</td>
    </tr>
    <tr>
        <td>CNN TX FIFO</td>
        <td>CNN_FIFO_</td>
        <td>0x400C 0400</td>
        <td>0x400C 0400</td>
    </tr>
</table>

## Peripheral Register Map
### APB Peripheral Base Address Map
[Table 3-3](#apb-peripheral-base-address-map) contains the base address for each of the APB mapped peripherals. The base address for a given peripheral is the start of the register map for the peripheral. For a given peripheral, the address for a register within the peripheral is defined as the APB peripheral base address plus the registers offset.

*Table 3-3: APB Peripheral Base Address Map*
<a name="apb-peripheral-base-address-map"></a>

<table border="1" cellpadding="5" cellspacing="0">
    <tr style="background-color: #e0e0e0; font-weight: bold; text-align: center">
        <th>Peripheral Register Name</th>
        <th>Register Prefix</th>
        <th>APB Base Address</th>
        <th>APB End Address</th>
    </tr>
    <tr><td>Global Control</td><td>GCR_</td><td>0x4000 0000</td><td>0x4000 03FF</td></tr>
    <tr><td>System Interface</td><td>SIR_</td><td>0x4000 0400</td><td></td></tr>
    <tr><td>Watchdog Timer 0</td><td>WDT0_</td><td>0x4000 3000</td><td>0x4000 33FF</td></tr>
    <tr><td>Dynamic Voltage Scaling Controller</td><td>DVS_</td><td>0x4000 3C00</td><td>0x4000 3C3F</td></tr>
    <tr><td>Single Input Multiple Output</td><td>SIMO_</td><td>0x4000 4400</td><td>0x4000 47FF</td></tr>
    <tr><td>Trim System Initialization</td><td>TRIMSIR_</td><td>0x4000 5400</td><td>0x4000 57FF</td></tr>
    <tr><td>General Control Function</td><td>GCFR_</td><td>0x4000 5800</td><td>0x4000 5BFF</td></tr>
    <tr><td>Real time Clock</td><td>RTC_</td><td>0x4000 6000</td><td>0x4000 63FF</td></tr>
    <tr><td>Wakeup Timer</td><td>WUT_</td><td>0x4000 6400</td><td>0x4000 67FF</td></tr>
    <tr><td>Power Sequencer</td><td>PWRSEQ_</td><td>0x4000 6800</td><td>0x4000 6BFF</td></tr>
    <tr><td>Miscellaneous Control</td><td>MCR_</td><td>0x4000 6C00</td><td>0x4000 6FFF</td></tr>
    <tr><td>AES</td><td>AES_</td><td>0x4000 7400</td><td>0x4000 77FF</td></tr>
    <tr><td>AES Key</td><td>AESKEY_</td><td>0x4000 7800</td><td>0x4000 7BFF</td></tr>
    <tr><td>GPIO Port 0</td><td>GPIO0_</td><td>0x4000 8000</td><td>0x4000 8FFF</td></tr>
    <tr><td>GPIO Port 1</td><td>GPIO1_</td><td>0x4000 9000</td><td>0x4000 9FFF</td></tr>
    <tr><td>Parallel Camera Interface</td><td>PCIF_</td><td>0x4000 E000</td><td>0x4000 EFFF</td></tr>
    <tr><td>CRC</td><td>CRC_</td><td>0x4000 F000</td><td>0x4000 FFFF</td></tr>
    <tr><td>Timer 0</td><td>TMR0_</td><td>0x4001 0000</td><td>0x4001 0FFF</td></tr>
    <tr><td>Timer 1</td><td>TMR1_</td><td>0x4001 1000</td><td>0x4001 1FFF</td></tr>
    <tr><td>Timer 2</td><td>TMR2_</td><td>0x4001 2000</td><td>0x4001 2FFF</td></tr>
    <tr><td>Timer 3</td><td>TMR3_</td><td>0x4001 3000</td><td>0x4001 3FFF</td></tr>
    <tr><td>I2C 0</td><td>I2C0_</td><td>0x4001 D000</td><td>0x4001 DFFF</td></tr>
    <tr><td>I2C 1</td><td>I2C1_</td><td>0x4001 E000</td><td>0x4001 EFFF</td></tr>
    <tr><td>I2C 2</td><td>I2C2_</td><td>0x4001 F000</td><td>0x4001 FFFF</td></tr>
    <tr><td>Standard DMA</td><td>DMA_</td><td>0x4002 8000</td><td>0x4002 8FFF</td></tr>
    <tr><td>Flash Controller 0</td><td>FLC0_</td><td>0x4002 9000</td><td>0x4002 93FF</td></tr>
    <tr><td>Instruction-Cache Controller 0 (CM4)</td><td>ICC0_</td><td>0x4002 A000</td><td>0x4002 A7FF</td></tr>
    <tr><td>Instruction Cache Controller 1 (RV32)</td><td>ICC1_</td><td>0x4002 A800</td><td>0x4002 AFFF</td></tr>
    <tr><td>ADC</td><td>ADC_</td><td>0x4003 4000</td><td>0x4003 4FFF</td></tr>
    <tr><td>Pulse Train Engine</td><td>PT_</td><td>0x4003 C000</td><td>0x4003 C09F</td></tr>
    <tr><td>1-Wire Master</td><td>OWM0_</td><td>0x4003 D000</td><td>0x4003 DFFF</td></tr>
    <tr><td>Semaphore</td><td>SEMA_</td><td>0x4003 E000</td><td>0x4003 EFFF</td></tr>
    <tr><td>UART 0</td><td>UART0_</td><td>0x4004 2000</td><td>0x4004 2FFF</td></tr>
    <tr><td>UART 1</td><td>UART1_</td><td>0x4004 3000</td><td>0x4004 3FFF</td></tr>
    <tr><td>UART 2</td><td>UART2_</td><td>0x4004 4000</td><td>0x4004 4FFF</td></tr>
    <tr><td>SPI1</td><td>SPI1_</td><td>0x4004 6000</td><td>0x4004 7FFF</td></tr>
    <tr><td>TRNG</td><td>TRNG_</td><td>0x4004 D000</td><td>0x4004 DFFF</td></tr>
    <tr><td>I2S</td><td>I2S_</td><td>0x4006 0000</td><td>0x4006 0FFF</td></tr>
    <tr><td>Low Power General Control</td><td>LPGCR_</td><td>0x4008 0000</td><td>0x4008 03FF</td></tr>
    <tr><td>GPIO Port 2</td><td>GPIO2_</td><td>0x4008 0400</td><td>0x4008 05FF</td></tr>
    <tr><td>Low Power Watchdog Timer 0 (WDT1)</td><td>WDT1_</td><td>0x4008 0800</td><td>0x4008 0BFF</td></tr>
    <tr><td>Low Power Timer 4</td><td>TMR4_</td><td>0x4008 0C00</td><td>0x4008 0FFF</td></tr>
    <tr><td>Function Control</td><td>FCR_</td><td>0x4000 0800</td><td>0x4000 0BFF</td></tr>
</table>


## Error Correction Coding (ECC) Module
This device features an Error Correction Coding (ECC) module that helps ensure data integrity by detecting and correcting bit corruption of the system RAM0 (sysram0) memory array. More specifically, the ECC module is a single error-correcting, double error detecting (SEC-DED). It corrects any single bit flip, detects two bit errors, and features a transparent zero wait state operation for reads.

The ECC works by creating check bits for all data written to sysram0. These check bits are then stored along with the data. During a read, both the data and check bits are used to determine if one or more bits have become corrupt. If a single bit has been corrupted, this can be corrected. If two bits have been corrupted, it is detected but not corrected.

If only one bit is determined to be corrupt, reads contain the "corrected" value. Reading memory does not correct the error value stored at the read memory location. It is up to the software to determine the appropriate time and method to write the correct data to memory. It is strongly recommended that the software correct the memory as soon as possible to minimize the chance of a second bit from becoming corrupt, resulting in data loss. Since ECC error checking occurs only during a read operation, it is recommended that the application periodically reads critical memory so that errors can be identified and corrected.

### SRAM
A check bit RAM is used to store sysram0's check bits, enabling ECC SEC-DED for sysram0. The check bit RAM is not mapped to the user memory space and is unavailable for application usage.

### Limitations
Any read from non-initialized memory can trigger an ECC error since the random check bits most likely do not match the random data bits contained in the memory. Writing sysram0 to all zeroes before enabling ECC functionality can prevent this at the expense of the time required. To zeroize sysram0, write GCR_MEMZ.ram0 to 1.