# Flash Controller (FLC)
The MAX78000 flash controller manages read, write, and erase accesses to the internal flash and provides the following features:

* Up to 512KB total internal flash memory
* 64 pages
* 8,192 bytes per page
* 2,048 words by 128 bits per page
* 128-bit data reads and writes
* Page erase and mass erase support
* Write protection

## Instances
The device includes one instance of the FLC. The 512KB of internal flash memory is programmable through the serial wire debug interface (in-system) or directly with software (in-application).

The flash is organized as an array of 2,048 words by 128 bits, or 8,192 bytes per page. [*Table 7-1*](#internal-flash-memory-organization) shows the page start address and page end address of the internal flash memory.

*Table 7-1: MAX78000 Internal Flash Memory Organization*
<a name="internal-flash-memory-organization"></a>

<table border="1" cellpadding="5" cellspacing="0">
  <thead>
    <tr style="background-color: #e0e0e0; font-weight: bold; text-align: center">
      <th>Instance</th>
      <th>Page Number</th>
      <th>Size (per page)</th>
      <th>Start Address</th>
      <th>End Address</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>FLC0</td>
      <td>1</td>
      <td>8,192 Bytes</td>
      <td>0x1000 0000</td>
      <td>0x1000 1FFF</td>
    </tr>
    <tr>
      <td></td>
      <td>2</td>
      <td>8,192 Bytes</td>
      <td>0x1000 2000</td>
      <td>0x1000 3FFF</td>
    </tr>
    <tr>
      <td></td>
      <td>3</td>
      <td>8,192 Bytes</td>
      <td>0x1000 4000</td>
      <td>0x1000 5FFF</td>
    </tr>
    <tr>
      <td></td>
      <td>4</td>
      <td>8,192 Bytes</td>
      <td>0x1000 6000</td>
      <td>0x1000 7FFF</td>
    </tr>
    <tr>
      <td></td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <td></td>
      <td>63</td>
      <td>8,192 Bytes</td>
      <td>0x1007 C000</td>
      <td>0x1007 DFFF</td>
    </tr>
    <tr>
      <td></td>
      <td>64</td>
      <td>8,192 Bytes</td>
      <td>0x1007 E000</td>
      <td>0x1007 FFFF</td>
    </tr>
  </tbody>
</table>

## Usage
The flash controller manages write and erase operations for internal flash memory and provides a lock mechanism to prevent unintentional writes to the internal flash. In-application and in-system programming, page erase, and mass erase operations are supported.

### Clock Configuration
The FLC requires a 1MHz internal clock. See Oscillator Sources for details. Use the FLC clock divisor to generate f<sub>FLCn_CLK</sub> = 1MHz, as shown in [Equation 7-1](#flc-clock-frequency). If using the IPO as the system clock, the [FLC_CLKDIV](#flash-controller-address-pointer).*clkdiv* should be set to 100 (0x64).

*Equation 7-1: FLC Clock Frequency*
<a name="flc-clock-frequency"></a>

$$
f_{\text{FLCn\_CLK}} = \frac{f_{\text{SYS\_CLK}}}{\text{FLCn\_CLKDIV} \cdot \text{clkdiv}} = 1 \, \text{MHz}
$$

### Lock Protection
A locking mechanism prevents accidental memory writes and erases. All write and erase operations require the [FLC_CTRL](#flash-controller-clock-divisor-register).*unlock* field to be set to 2 before starting the operation. Writing any other value to the [FLC_CTRL](#flash-controller-clock-divisor-register).*unlock* field results in:

1. The flash instance remaining locked, or
2. The flash instance is locked from the unlocked state.

*Note: If a write, page erase, or mass erase operation is started, and the unlock code was not set to 2, the flash controller hardware sets the access fail flag, [FLC_INTR](#flash-controller-interrupt-register).af, to indicate an access violation occurred.*

### Flash Write Width
The FLC supports write widths of 128-bits only. The target address bits [FLC_ADDR](#flash-controller-address-pointer)[3:0] are ignored, resulting in 128-bit address alignment.

*Table 7-2: Valid Addresses Flash Writes*
<a name="valid-addresses-flash-writes"></a>

<table border="1" cellpadding="5" cellspacing="0">
   <tr>
       <td colspan="1"></td>
       <td style="background-color: #e0e0e0; font-weight: bold; text-align: center" colspan="32">FLC_ADDR[31:0]</td>
   </tr>
   <tr>
       <td><strong>Bit Number</strong></td>
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
       <td><strong>128-bit Write</strong></td>
       <td>1</td>
       <td>0</td>
       <td>0</td>
       <td>0</td>
       <td>0</td>
       <td>0</td>
       <td>0</td>
       <td>0</td>
       <td>0</td>
       <td>0</td>
       <td>0</td>
       <td>0</td>
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
       <td>x</td>
       <td>0</td>
       <td>0</td>
       <td>0</td>
       <td>0</td>
   </tr>
</table>

### Flash Write
Writes to a flash address are only successful if the target address is already in its erased state. Perform the following steps to write to a flash memory address:

1. If desired, enable the flash controller interrupts by setting the [FLC_INTR](#flash-controller-interrupt-register).*afie* and [FLC_INTR](#flash-controller-interrupt-register).*doneie* bits.
2. Read the [FLC_CTRL](#flash-controller-clock-register).*pend* bit until it returns 0.
3. Configure the [FLC_CLKDIV](#flash-controller-clock-divisor-register).*clkdiv* field to achieve a 1MHz frequency based on the selected SYS_CLK frequency.
4. Set the [FLC_ADDR](#flash-controller-address-pointer) register to a valid target address. See [Table 7-2](#valid-addresses-flash-writes) for details.
5. Set [FLC_DATA3](#flash-controller-data-register3), [FLC_DATA2](#flash-controller-data-register2), [FLC_DATA1](#flash-controller-data-register1), and [FLC_DATA0](#flash-controller-data-register0) to the data to write.
    
    a. **FLC_DATA3** is the most significant word, and **FLC_DATA0** is the least significant word.
      
    i. Each word of the data to write follows the little-endian format where the least significant byte of the word is stored at the lowest-numbered byte, and the most significant byte is stored at the highest-numbered byte.

6. Set the [FLC_CTRL](#flash-controller-clock-register).*unlock* field to 2 to unlock the flash.
7. Set the [FLC_CTRL](#flash-controller-clock-register).*wr* field to 1.
    
    a. The hardware automatically clears this field when the write operation is complete.

8. The [FLC_INTR](#flash-controller-interrupt-register).*done* field is set to 1 by hardware when the write completes.
   
    a. An interrupt is generated if the [FLC_INTR](#flash-controller-interrupt-register).*doneie* field is set to 1.

9. If an error occurred, the [FLC_INTR](#flash-controller-interrupt-register).*af* field is set to 1 by hardware. An interrupt is generated if the FLC_INTR.*afie* field is set to 1.
10. Set the [FLC_CTRL](#flash-controller-clock-register).*unlock* field to any value other than 2 to re-lock the flash.

*Note: Code execution can occur within the same flash instance as targeted programming.*

### Page Erase
*CAUTION: Care must be taken not to erase the page from which the application software is currently executing.*

Perform the following to erase a page of a flash memory instance:

1. If desired, enable flash controller interrupts by setting the FLC_INTR.*afie* and FLC_INTR.*doneie* bits.
2. Read the [FLC_CTRL](#flash-controller-clock-register).*pend* bit until it returns 0.
3. Configure [FLC_CLKDIV](#flash-controller-clock-divisor-register).*clkdiv* to match the SYS_CLK frequency.
4. Set the [FLC_ADDR](#flash-controller-address-pointer) register to an address within the target page to be erased. [FLC_ADDR](#flash-controller-address-pointer)[12:0] is ignored by the FLC to ensure the address is page-aligned.
5. Set [FLC_CTRL](#flash-controller-clock-register).*unlock* to 2 to unlock the flash instance.
6. Set [FLC_CTRL](#flash-controller-clock-register).*erase_code* to 0x55 for page erase.
7. Set [FLC_CTRL](#flash-controller-clock-register).*pge* to 1 to start the page erase operation.
8. The [FLC_CTRL](#flash-controller-clock-register).*pend* bit is set by the flash controller while the page erase is in progress, and the [FLC_CTRL](#flash-controller-clock-register).*pge* and [FLC_CTRL](#flash-controller-clock-register).*pend* are cleared by the flash controller when the page erase is complete.
9. [FLC_INTR](#flash-controller-interrupt-register).*done* is set by hardware when the page erase completes, and if an error occurred, the [FLC_INTR](#flash-controller-interrupt-register).*af* flag is set. These bits generate a flash interrupt if the interrupt enable bits are set.
10. Set [FLC_CTRL](#flash-controller-clock-register).*unlock* to any value other than 2 to re-lock the flash instance.

### Mass Erase
*CAUTION: Care must be taken not to erase the flash from which application software is currently executing.*

Mass erase clears the internal flash memory on an instance basis. Perform the following steps to mass erase a single flash memory instance:

1. Read the [FLC_CTRL](#flash-controller-clock-register).*pend* bit until it returns 0.
2. Configure [FLC_CLKDIV](#flash-controller-clock-divisor-register).*clkdiv* to match the SYS_CLK frequency.
3. Set [FLC_CTRL](#flash-controller-clock-register).*unlock* to 2 to unlock the internal flash.
4. Set [FLC_CTRL](#flash-controller-clock-register).*erase_code* to 0xAA for mass erase.
5. Set [FLC_CTRL](#flash-controller-clock-register).*me* to 1 to start the mass erase operation.
6. The [FLC_CTRL](#flash-controller-clock-register).*pend* bit is set by the flash controller while the mass erase is in progress, and the [FLC_CTRL](#flash-controller-clock-register).*me* and [FLC_CTRL](#flash-controller-clock-register).*pend* are cleared by the flash controller when the mass erase is complete.
7. [FLC_INTR](#flash-controller-interrupt-register).*done* is set by the flash controller when the mass erase completes, and if an error occurred, the [FLC_INTR](#flash-controller-interrupt-register).*af* flag is set. These bits generate a flash interrupt if the interrupt enable bits are set.
8. Set [FLC_CTRL](#flash-controller-clock-register).*unlock* to any value other than 2 to re-lock the flash instance.

## Registers
See Table 3-3 for the base address of this peripheral/module. See Table 1-1 for an explanation of the read and write access of each field. Unless specified otherwise, all fields are reset on a system reset, soft reset, POR, and peripheral-specific resets.

*Note: The FLC registers are reset only on a POR. System reset, soft reset, and peripheral reset do not affect the FLC register values.*

*Table 7-3: Flash Controller Register Summary*
<a name="flash-controller-register-summary"></a>
<table>
  <thead>
    <tr>
      <th>Offset</th>
      <th>Register Name</th>
      <th>Access</th>
      <th>Description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>[0x0000]</td>
      <td><a href="#flash-controller-address-pointer">FLC_ADDR</td>
      <td>R/W</td>
      <td>Flash Controller Address Pointer Register</td>
    </tr>
    <tr>
      <td>[0x0004]</td>
      <td><a href="#flash-controller-clock-divisor-register">FLC_CLKDIV</td>
      <td>R/W</td>
      <td>Flash Controller Clock Divisor Register</td>
    </tr>
    <tr>
      <td>[0x0008]</td>
      <td><a href="#flash-controller-clock-register">FLC_CTRL</td>
      <td>R/W</td>
      <td>Flash Controller Control Register</td>
    </tr>
    <tr>
      <td>[0x0024]</td>
      <td><a href="#flash-controller-interrupt-register">FLC_INTR</td>
      <td>R/W</td>
      <td>Flash Controller Interrupt Register</td>
    </tr>
    <tr>
      <td>[0x0030]</td>
      <td><a href="#flash-controller-data-register0">FLC_DATA0</td>
      <td>R/W</td>
      <td>Flash Controller Data Register 0</td>
    </tr>
    <tr>
      <td>[0x0034]</td>
      <td><a href="#flash-controller-data-register1">FLC_DATA1</td>
      <td>R/W</td>
      <td>Flash Controller Data Register 1</td>
    </tr>
    <tr>
      <td>[0x0038]</td>
      <td><a href="#flash-controller-data-register2">FLC_DATA2</td>
      <td>R/W</td>
      <td>Flash Controller Data Register 2</td>
    </tr>
    <tr>
      <td>[0x003C]</td>
      <td><a href="#flash-controller-data-register3">FLC_DATA3</td>
      <td>R/W</td>
      <td>Flash Controller Data Register 3</td>
    </tr>
    <tr>
      <td>[0x0040]</td>
      <td><a href="#flash-controller-access-control-register">FLC_ACTRL</td>
      <td>R/W</td>
      <td>Flash Controller Access Control Register</td>
    </tr>
    <tr>
      <td>[0x0080]</td>
      <td><a href="#flash-write-lock0-register">FLC_WELR0</td>
      <td>R/W</td>
      <td>Flash Write/Erase Lock 0 Register</td>
    </tr>
    <tr>
      <td>[0x0088]</td>
      <td><a href="#flash-write-lock1-register">FLC_WELR1</td>
      <td>R/W</td>
      <td>Flash Write/Erase Lock 1 Register</td>
    </tr>
    <tr>
      <td>[0x0090]</td>
      <td><a href="#flash-read-lock0-register">FLC_RLR0</td>
      <td>R/W</td>
      <td>Flash Read Lock 0 Register</td>
    </tr>
    <tr>
      <td>[0x0098]</td>
      <td><a href="#flash-read-lock1-register">FLC_RLR1</td>
      <td>R/W</td>
      <td>Flash Read Lock 1 Register</td>
    </tr>
  </tbody>
</table>

### Register Details

*Table 7-4: Flash Controller Address Pointer Register*
<a name="flash-controller-address-pointer"></a>

<table border="1" cellpadding="5" cellspacing="0">
   <tr style="background-color: #e0e0e0; font-weight: bold; text-align: center">
       <td colspan="3">Flash Controller Address Pointer</td>
       <td colspan="1">FLC_ADDR</td>
       <td>[0x0000]</td>
   </tr>
   <tr>
       <th>Bits</th>
       <th>Name</th>
       <th>Access</th>
       <th>Reset</th>
       <th>Description</th>
   </tr>
   <tr>
       <td>31:0</td>
       <td>addr</td>
       <td>R/W</td>
       <td>0x1000 0000</td>
       <td><strong>Flash Address</strong><br>This field contains the target address for a write operation. A valid internal flash memory address is required for all write operations.</td>
   </tr>
</table>

*Table 7-5: Flash Controller Clock Divisor Register*
<a name="flash-controller-clock-divisor-register"></a>

<table border="1" cellpadding="5" cellspacing="0">
   <tr style="background-color: #e0e0e0; font-weight: bold; text-align: center">
       <td colspan="3">Flash Controller Clock Divisor</td>
       <td colspan="1">FLC_CLKDIV</td>
       <td>[0x0004]</td>
   </tr>
   <tr>
       <th>Bits</th>
       <th>Name</th>
       <th>Access</th>
       <th>Reset</th>
       <th>Description</th>
   </tr>
   <tr>
       <td>31:8</td>
       <td>-</td>
       <td>RO</td>
       <td>-</td>
       <td><strong>Reserved</strong></td>
   </tr>
      <tr>
       <td>7:0</td>
       <td>clkdiv</td>
       <td>R/W</td>
       <td>0x64</td>
       <td><strong>Flash Controller Clock Divisor</strong><br>The APB clock is divided by the value in this field to generate the FLCn peripheral clock, f<sub>FLC_CLK</sub>. The FLC peripheral clock must equal 1MHz. The default on POR, system reset, and watchdog reset is 100, resulting in f<sub>FLC_CLK</sub> = 1MHz when IPO is the system oscillator. The FLC peripheral clock is only used during erase and program functions and not during read functions. See Clock Configuration for additional details.</td>
   </tr>
</table>

*Table 7-6: Flash Controller Control Register*
<a name="flash-controller-clock-register"></a>

<table border="1" cellpadding="5" cellspacing="0">
   <tr style="background-color: #e0e0e0; font-weight: bold; text-align: center">
       <td colspan="3">Flash Controller Control</td>
       <td colspan="1">FLC_CTRL</td>
       <td>[0x0008]</td>
   </tr>
   <tr>
       <th>Bits</th>
       <th>Name</th>
       <th>Access</th>
       <th>Reset</th>
       <th>Description</th>
   </tr>
   <tr>
       <td>31:28</td>
       <td>unlock</td>
       <td>R/W</td>
       <td>0</td>
       <td><strong>Flash Unlock</strong><br>Write the unlock code, 2, before any flash write or erase operation to unlock the flash. Writing any other value to this field locks the internal flash.<br> <div style="margin-left: 20px">
       <p>2: Flash unlock code</p>
       </div>
       </td>
   </tr>
   <tr>
       <td>27:26</td>
       <td>-</td>
       <td>RO</td>
       <td>-</td>
       <td><strong>Reserved</strong></td>
   </tr>
   <tr>
    <td>25</td>
    <td>lve</td>
    <td>R/W</td>
    <td>0</td>
    <td>
        <strong>Low Voltage Enable</strong><br>Set this field to 1 to enable low voltage operation for the flash memory.
        <div style="margin-left: 20px">
            <p>0: Low voltage operation disabled (Default).</p>
            <p>1: Low voltage operation enabled.</p>
        </div>
    </td>
   </tr>
   <tr>
    <td>24</td>
    <td>pend</td>
    <td>RO</td>
    <td>0</td>
    <td>
        <strong>Flash Busy Flag</strong><br>When this field is set, writes to all flash registers, except the FLC_INTR register, are ignored by the flash controller. This bit is cleared by hardware once the flash becomes accessible.
        <p><em>Note:If the flash controller is busy (FLC_CTRL.pend = 1), reads, writes, and erase operations are not allowed and result in an access failure (FLC_INTR.af = 1).</em></p>
        <div style="margin-left: 20px">
            <p>0: Low voltage operation disabled (Default).</p>
            <p>1: Low voltage operation enabled.</p>
        </div>
    </td>
   </tr>
      <tr>
    <td>23:16</td>
    <td>-</td>
    <td>RO</td>
    <td>0</td>
    <td>
        <strong>Reserved</strong><br>
    </td>
   </tr>
    <tr>
    <td>15:8</td>
    <td>erase_code</td>
    <td>R/W</td>
    <td>0</td>
    <td>
        <strong>Erase Code</strong><br>Before an erase operation, this field must be set to 0x55 for a page erase or 0xAA for a mass erase. The flash must be unlocked before setting the erase code. This field is automatically cleared after the erase operation is complete.
        <div style="margin-left: 20px">
            <p>0x00: Erase disabled.</p>
            <p>0x55: Page erase code.</p>
            <p>0xAA: Mass erase code.</p>
        </div>
    </td>
   </tr>
    <tr>
    <td>7:3</td>
    <td>-</td>
    <td>RO</td>
    <td>0</td>
    <td>
        <strong>Reserved</strong>
    </td>
   </tr>
    <tr>
    <td>2</td>
    <td>pge</td>
    <td>R/W1</td>
    <td>0</td>
    <td>
        <strong>Page Erase</strong><br>Write a 1 to this field to initiate a page erase at the address in FLC_ADDR.addr. The flash must be unlocked before attempting a page erase. See FLC_CTRL.unlock for details.
        <p>The flash controller hardware clears this bit when a page erase operation is complete.</p>
        <div style="margin-left: 20px">
            <p>0: Normal operation.</p>
            <p>1: Write a 1 to initiate a page erase. If this field reads 1, a page erase operation is in progress.</p>
        </div>
    </td>
   </tr>
    <tr>
    <td>1</td>
    <td>me</td>
    <td>R/W1</td>
    <td>0</td>
    <td>
        <strong>Mass Erase</strong><br>Write a 1 to this field to initiate a mass erase of the internal flash memory. The flash must be unlocked before attempting a mass erase. See FLC_CTRL.unlock for details. The flash controller hardware clears this bit when the mass erase operation completes.
        <div style="margin-left: 20px">
            <p>0: Normal operation.</p>
            <p>1: Initiate mass erase.</p>
        </div>
    </td>
   </tr>
    <tr>
    <td>0</td>
    <td>wr</td>
    <td>R/W1O</td>
    <td>0</td>
    <td>
        <strong>Write</strong><br>If this field reads 0, no write operation is pending for the flash. To initiate a write operation, set this bit to 1, and the flash controller writes to the address set in the FLC_ADDR register.
        <div style="margin-left: 20px">
            <p>0: Normal operation.</p>
            <p>1: Write 1 to initiate a write operation. If this field reads 1, a write operation is in progress.</p>
        </div>
        <em>Note: This field is protected and cannot be set to 0 by application software.</em>
    </td>
   </tr>
</table>

*Table 7-7: Flash Controller Interrupt Register*
<a name="flash-controller-interrupt-register"></a>

<table border="1" cellpadding="5" cellspacing="0">
   <tr style="background-color: #e0e0e0; font-weight: bold; text-align: center">
       <td colspan="3">Flash Controller Interrupt</td>
       <td colspan="1">FLC_INTR</td>
       <td>[0x0024]</td>
   </tr>
   <tr>
       <th>Bits</th>
       <th>Name</th>
       <th>Access</th>
       <th>Reset</th>
       <th>Description</th>
   </tr>
   <tr>
       <td>31:10</td>
       <td>-</td>
       <td>RO</td>
       <td>0</td>
       <td><strong>Reserved</strong></td>
   </tr>
    <tr>
       <td>9</td>
       <td>afie</td>
       <td>R/W</td>
       <td>0</td>
       <td><strong>Flash Access Fail Interrupt Enable</strong><br>Set this bit to 1 to enable interrupts on flash access failures.
       <p>0: Disabled</p>
       <p>1: Enabled</p>
       </td>
   </tr>
       <tr>
       <td>8</td>
       <td>doneie</td>
       <td>R/W</td>
       <td>0</td>
       <td><strong>Flash Operation Complete Interrupt Enable</strong><br>Set this bit to 1 to enable interrupts on flash operations complete.
       <p>0: Disabled</p>
       <p>1: Enabled</p>
       </td>
   </tr>
      <tr>
       <td>7:2</td>
       <td>-</td>
       <td>RO</td>
       <td>0</td>
       <td><strong>Reserved</strong></td>
   </tr>
  <tr>
       <td>1</td>
       <td>af</td>
       <td>R/W0C</td>
       <td>0</td>
       <td><strong>Flash Access Fail Interrupt Flag</strong><br>This bit is set when an attempt is made to write or erase the flash while the flash is busy or locked. Only hardware can set this bit to 1. Writing a 1 to this bit has no effect. This bit is cleared by writing a 0.
       <p>0: No access failure has occurred.</p>
       <p>1: Access failure occurred.</p>
       </td>
   </tr>
   <tr>
       <td>0</td>
       <td>done</td>
       <td>R/W0C</td>
       <td>0</td>
       <td><strong>Flash Operation Complete Interrupt Flag</strong><br>This flag is automatically set by hardware after a flash write or erase operation completes.
       <p>0: Operation not complete or not in process.</p>
       <p>1: Flash operation complete.</p>
       </td>
   </tr>
</table>

*Table7-8: Flash Controller Data 0 Register*
<a name="flash-controller-data0-register"></a>

<table border="1" cellpadding="5" cellspacing="0">
   <tr style="background-color: #e0e0e0; font-weight: bold; text-align: center">
       <td colspan="3">Flash Controller Data 0</td>
       <td colspan="1">FLC_DATA0</td>
       <td>[0x0030]</td>
   </tr>
   <tr>
       <th>Bits</th>
       <th>Name</th>
       <th>Access</th>
       <th>Reset</th>
       <th>Description</th>
   </tr>
   <tr>
       <td>31:0</td>
       <td>data</td>
       <td>R/W</td>
       <td>0</td>
       <td><strong>Flash Data 0</strong><br>Flash data for bits 31:0.</td>
</table>

*Table 7-9: Flash Controller Data Register 1*
<a name="flash-controller-data-register1"></a>

<table border="1" cellpadding="5" cellspacing="0">
   <tr style="background-color: #e0e0e0; font-weight: bold; text-align: center">
       <td colspan="3">Flash Controller Data 1</td>
       <td colspan="1">FLC_DATA1</td>
       <td>[0x0030]</td>
   </tr>
   <tr>
       <th>Bits</th>
       <th>Name</th>
       <th>Access</th>
       <th>Reset</th>
       <th>Description</th>
   </tr>
   <tr>
       <td>31:0</td>
       <td>data</td>
       <td>R/W</td>
       <td>0</td>
       <td><strong>Flash Data 1</strong><br>Flash data for bits 63:32.</td>
</table>

*Table 7-10: Flash Controller Data Register 2*
<a name="flash-controller-data-register2"></a>

<table border="1" cellpadding="5" cellspacing="0">
   <tr style="background-color: #e0e0e0; font-weight: bold; text-align: center">
       <td colspan="3">Flash Controller Data 2</td>
       <td colspan="1">FLC_DATA2</td>
       <td>[0x0030]</td>
   </tr>
   <tr>
       <th>Bits</th>
       <th>Name</th>
       <th>Access</th>
       <th>Reset</th>
       <th>Description</th>
   </tr>
   <tr>
       <td>31:0</td>
       <td>data</td>
       <td>R/W</td>
       <td>0</td>
       <td><strong>Flash Data 2</strong><br>Flash data for bits 95:64.</td>
</table>

*Table 7-11: Flash Controller Data Register 3*
<a name="flash-controller-data-register3"></a>

<table border="1" cellpadding="5" cellspacing="0">
   <tr style="background-color: #e0e0e0; font-weight: bold; text-align: center">
       <td colspan="3">Flash Controller Data 3</td>
       <td colspan="1">FLC_DATA3</td>
       <td>[0x0030]</td>
   </tr>
   <tr>
       <th>Bits</th>
       <th>Name</th>
       <th>Access</th>
       <th>Reset</th>
       <th>Description</th>
   </tr>
   <tr>
       <td>31:0</td>
       <td>data</td>
       <td>R/W</td>
       <td>0</td>
       <td><strong>Flash Data 3</strong><br>Flash data for bits 127:96.</td>
</table>

*Table 7-12: Flash Controller Access Control Register*
<a name="flash-controller-access-control-register"></a>

<table border="1" cellpadding="5" cellspacing="0">
   <tr style="background-color: #e0e0e0; font-weight: bold; text-align: center">
       <td colspan="3">Flash Controller Access Control</td>
       <td colspan="1">FLC_ACTRL</td>
       <td>[0x0040]</td>
   </tr>
   <tr>
       <th>Bits</th>
       <th>Name</th>
       <th>Access</th>
       <th>Reset</th>
       <th>Description</th>
   </tr>
   <tr>
       <td>31:0</td>
       <td>actrl</td>
       <td>R/W</td>
       <td>0</td>
       <td><strong>Access Control</strong><br>When this register is written with the access control sequence, the information block can be accessed. See Information Block Flash Memory for details.</td>
</table>

*Table 7-13: Flash Write/Lock 0 Register*
<a name="flash-write-lock0-register"></a>

<table border="1" cellpadding="5" cellspacing="0">
   <tr style="background-color: #e0e0e0; font-weight: bold; text-align: center">
       <td colspan="3">Flash Write/Lock 0</td>
       <td colspan="1">FLC_WELR0</td>
       <td>[0x0080]</td>
   </tr>
   <tr>
       <th>Bits</th>
       <th>Name</th>
       <th>Access</th>
       <th>Reset</th>
       <th>Description</th>
   </tr>
   <tr>
       <td>31:0</td>
       <td>welr0</td>
       <td>R/W1C</td>
       <td>0xFFFF FFFF</td>
       <td><strong>Flash Write/Lock Bit</strong><br>Each bit in this register maps to a page of the internal flash. FLC_WELR0[0] maps to page 0 of the flash, and FLC_WELR0[31] maps to page 31. Each flash page is 8,192 bytes. Write a 1 to a bit position in this register, and the corresponding page of flash is immediately locked. The page protection can only be unlocked by an external reset or a POR.
       <p>0: The corresponding page of flash is write protected.</p>
       <p>1: The corresponding page of flash is not write protected.</p>
       </td>
</table>

*Table 7-14: Flash Write/Lock 1 Register*
<a name="flash-write-lock1-register"></a>

<table border="1" cellpadding="5" cellspacing="0">
   <tr style="background-color: #e0e0e0; font-weight: bold; text-align: center">
       <td colspan="3">Flash Write/Lock 1</td>
       <td colspan="1">FLC_WELR1</td>
       <td>[0x0088]</td>
   </tr>
   <tr>
       <th>Bits</th>
       <th>Name</th>
       <th>Access</th>
       <th>Reset</th>
       <th>Description</th>
   </tr>
   <tr>
       <td>31:0</td>
       <td>welr1</td>
       <td>R/W1C</td>
       <td>0xFFFF FFFF</td>
       <td><strong>Flash Write/Lock Bit</strong><br>Each bit in this register maps to a page of the internal flash. FLC_WELR1[0] maps to page 32 of the flash, and FLC_WELR1[31] maps to page 63 of flash. Each flash page is 8,192 bytes. Write a 1 to a bit position in this register, and the corresponding page of flash is immediately locked. The page protection can only be unlocked by an external reset or a POR.
       <p>0: The corresponding flash page is write protected.</p>
       <p>1: The corresponding flash page is not write protected.</p>
       </td>
</table>

*Table 7-15: Flash Read Lock 0 Register*
<a name="flash-read-lock0-register"></a>

<table border="1" cellpadding="5" cellspacing="0">
   <tr style="background-color: #e0e0e0; font-weight: bold; text-align: center">
       <td colspan="3">Flash Read Lock 0</td>
       <td colspan="1">FLC_RLR0</td>
       <td>[0x0090]</td>
   </tr>
   <tr>
       <th>Bits</th>
       <th>Name</th>
       <th>Access</th>
       <th>Reset</th>
       <th>Description</th>
   </tr>
   <tr>
       <td>31:0</td>
       <td>rlr0</td>
       <td>R/W1C</td>
       <td>0xFFFF FFFF</td>
       <td><strong>Read Lock Bit</strong><br>Each bit in this register maps to a page of the internal flash. FLC_RLR0[0] maps to page 0 of the flash, and FLC_RLR0[31] maps to page 31 of flash. Each flash page is 8,192 bytes. Write a 1 to a bit position in this register, and the corresponding page of flash is immediately read protected. The page’s read protection can only be unlocked by an external reset or a POR.
       <p>0: The corresponding flash page is read protected.</p>
       <p>1: The corresponding flash page is not read protected.</p>
       </td>
</table>

*Table 7-16: Flash Read Lock 1 Register*
<a name="flash-read-lock1-register"></a>

<table border="1" cellpadding="5" cellspacing="0">
   <tr style="background-color: #e0e0e0; font-weight: bold; text-align: center">
       <td colspan="3">Flash Read Lock 1</td>
       <td colspan="1">FLC_RLR1</td>
       <td>[0x0098]</td>
   </tr>
   <tr>
       <th>Bits</th>
       <th>Name</th>
       <th>Access</th>
       <th>Reset</th>
       <th>Description</th>
   </tr>
   <tr>
       <td>31:0</td>
       <td>rlr1</td>
       <td>R/W1C</td>
       <td>0xFFFF FFFF</td>
       <td><strong>Read Lock Bit</strong><br>Each bit in this register maps to a page of the internal flash. FLC_RLR1[0] maps to page 32 of the flash, and FLC_RLR1[31] maps to page 63 of flash. Each flash page is 8,192 bytes. Write a 1 to a bit position in this register, and the corresponding page of flash is immediately read protected. The page’s read protection can only be unlocked by an external reset or a POR.
       <p>0: The corresponding flash page is read protected.</p>
       <p>1: The corresponding flash page is not read protected.</p>
       </td>
</table>