# OpenPLC ETS product

The ETS product file for using the OpenPLC board as a KNX TP device, its source,
and how to use the board's KNX module.

| File | What it is |
|---|---|
| `OpenPLC_TP.knxprod` | The signed product, imported into ETS |
| `OpenPLC_TP.xml` | Its source; edit this, then regenerate the `.knxprod` |

## Status

It imports into ETS 5.7 but has not yet been downloaded to a board. The KNX TP link of
the board's `OpenPLC_KNX` library has not yet been validated on a real bus. The
examples that use it are still being written.

## Using the KNX module

The board is a KNX TP device (mask `0x07B0`). ETS reaches the bus through a KNX IP
interface or gateway on the network.

1. **Sketch.** In the Arduino IDE choose *Tools → KNX Role → KNX TP Device (TP bus only,
   MASK 0x07B0)*, then upload the sketch to the board.
2. **Import.** In ETS: *Catalogs → Import*, choose `OpenPLC_TP.knxprod`.
3. **Add the device** from the catalog to a TP line in your project. ETS lists it
   under the manufacturer *KNX Association* (the shared test manufacturer ID
   `M-00FA`); search for `OpenPLC TP` or the order number `OPENPLC-TP`.
4. **Individual address.** While the sketch runs, press the BOOT0 button once: the
   system LED stays on and the USB serial port prints `KNX: programming mode on`.
   Let ETS program the address; ETS or a second press ends programming mode.
   **Do not hold the button while powering up or resetting the board**: that enters
   the bootloader's upload mode, and holding it 10 s restores factory state.
5. **Download** the application from ETS.
6. **Group addresses.** Link a group address to each object:

All examples share this one product, each using some of its objects, so switching
examples only needs the new sketch uploaded; the ETS configuration stays on the board.

| Objects | Name | Type | Flags |
|---|---|---|---|
| 1, 3, 5, 7, 9, 11 | Relay 1-6 switch | DPT 1.001, 1 bit | communicate, write |
| 2, 4, 6, 8, 10, 12 | Relay 1-6 status | DPT 1.001, 1 bit | communicate, read, transmit |
| 13-20 | DI 1-8 | DPT 1.001, 1 bit | communicate, read, transmit |

Object numbers never change once published; new objects are added after the last one,
so existing ETS projects stay valid.

## Regenerating the product

The product is generated with [OpenKNXproducer](https://github.com/OpenKNX/OpenKNXproducer)
v4.3.12. It signs with the DLLs of the locally installed ETS, so it only runs on a
machine with ETS installed. With ETS 5.7, the source must use the namespace
`http://knx.org/xml/project/20`.

```
OpenKNXproducer-x64.exe knxprod -o OpenPLC_TP.knxprod OpenPLC_TP.xml
```

Keep the last four digits of every ID in the source as `0000`; signing replaces them
with a hash. Inside an ID, write the order number with every character other than a
letter or digit as `.` plus its hex code (`OPENPLC-TP` becomes `OPENPLC.2DTP`), or ETS
imports the product but cannot list it. Commit the source and the regenerated `.knxprod` together.
