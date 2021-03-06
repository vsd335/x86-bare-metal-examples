# PIC

TODO add some examples.

Programmable interrupt controller:

- <http://wiki.osdev.org/PIC>
- <http://www.jamesmolloy.co.uk/tutorial_html/5.-IRQs%20and%20the%20PIT.html>

How it works:

    Hardware -> IRQ -> PIC -> Interrupt handler

Each hardware has an IRQ, e.g. 0 for the PIT.

When an IRQ activated (e.g. PIT sends a signal), the PIC decides:

-   whether or not it will call an interrupt handler. For example, without and EOI, further interrupts will not be generated.

-   which interrupt handler it will call. This can be modified by programming the PIT.x

    For example, Molloy shifts protected mode IRQs from interrupt 0 to 32, so that they won't conflict with the CPU defined exceptions in that area. <https://github.com/cirosantilli/jamesmolloy-kernel-development-tutorials/blob/d15a2dfb721008e2a3df132c8cda37c0e62ad826/5_irq/descriptor_tables.c#L72>

The default IRQ assignment is shown at: <https://en.wikipedia.org/wiki/Interrupt_request_%28PC_architecture%29#x86_IRQs>

Like other external circuits, the PIC is itself also programmed by `in` and `out` instructions.

## EOI

End of interrupt.

We must tell the PIC that we are at the end.

Otherwise new interrupts with equal or lower precedence don't fire again.

<https://en.wikipedia.org/wiki/End_of_interrupt>

## Plug and play

TODO: how does plug and play configure IRQs?

## APIC

APIC vs PIC:

- allows for multithreading
- 24 IRQs instead of 15. The new top 8 are for PCI and deal better with conflicts.
- has a millisecond timer built-in. Different from the HPET.

## Linux

### Shared IRQs

It is possible to use a single IRQ for multiple hardware:

<http://unix.stackexchange.com/questions/47306/how-does-the-linux-kernel-handle-shared-irqs>

### /proc/interrupts

PIC information can be found under:

    cat /proc/interrupts

Sample output:

               CPU0       CPU1       CPU2       CPU3
      0:         19          0          0          0   IO-APIC-edge      timer
      1:       1032       9717        745        811   IO-APIC-edge      i8042
      8:          0          1          0          0   IO-APIC-edge      rtc0
      9:        752       2027        603        895   IO-APIC-fasteoi   acpi
     12:     139040     766443     108662     100894   IO-APIC-edge      i8042
     16:        139        802       5766      10382   IO-APIC  16-fasteoi   ehci_hcd:usb3, mmc0
     17:     216285     493484      25621      65079   IO-APIC  17-fasteoi   rtl_pci
     23:         27        218         79        318   IO-APIC  23-fasteoi   ehci_hcd:usb4
     25:          0          0          0          0   PCI-MSI-edge      xhci_hcd
     26:      41510     101990      49473     124665   PCI-MSI-edge      0000:00:1f.2
     27:         10          2       4065          0   PCI-MSI-edge      eth0
     28:         22          1          1          0   PCI-MSI-edge      mei_me
     29:      65843     218345      46842      43270   PCI-MSI-edge      i915
     30:         76        175         17          1   PCI-MSI-edge      snd_hda_intel
     31:         12         16          5          7   PCI-MSI-edge      nouveau
    NMI:         71         67         71         65   Non-maskable interrupts
    LOC:    1265066     702342    1396277     840420   Local timer interrupts
    SPU:          0          0          0          0   Spurious interrupts
    PMI:         71         67         71         65   Performance monitoring interrupts
    IWI:          0          0          0          0   IRQ work interrupts
    RTR:          0          0          0          0   APIC ICR read retries
    RES:     180487     200186     197582     204727   Rescheduling interrupts
    CAL:       2570        885       1391       1252   Function call interrupts
    TLB:      60853      64789      54844      73507   TLB shootdowns
    TRM:          0          0          0          0   Thermal event interrupts
    THR:          0          0          0          0   Threshold APIC interrupts
    MCE:          0          0          0          0   Machine check exceptions
    MCP:         29         29         29         29   Machine check polls
    HYP:          0          0          0          0   Hypervisor callback interrupts
    ERR:          0
    MIS:          0

- timer: PIT
- i8042: keyboard
- fasteoi vs edge: <http://stackoverflow.com/questions/7005331/difference-between-io-apic-fasteoi-and-io-apic-edge>
- PCI-MSI-edge: <http://stackoverflow.com/questions/10894702/diff-between-io-apic-level-and-pci-msi-x>
- EHCI http://www.intel.com/content/www/us/en/io/universal-serial-bus/ehci-specification.html
