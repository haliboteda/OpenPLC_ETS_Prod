# OpenPLC ETS product

The ETS product file for using the OpenPLC board as a KNX TP device, its source,
and how to use the board's KNX module.

| File | What it is |
|---|---|
| `OpenPLC_TP_minimal.knxprod` | The signed product, imported into ETS |
| `OpenPLC_TP_minimal.xml` | Its source; edit this, then regenerate the `.knxprod` |

## Status

**A trial product, not the final one.** It has two group objects and has not yet been
imported into ETS or downloaded to a board. The KNX TP link of the board's `OpenPLC_KNX`
library has not yet been validated on a real bus. The final object layout and the
examples that go with it are still being decided.

## Using the KNX module

The board is a KNX TP device (mask `0x07B0`). ETS reaches the bus through a KNX IP
interface or gateway on the network.

1. **Sketch.** In the Arduino IDE choose *Tools → KNX Role → KNX TP Device (TP bus only,
   MASK 0x07B0)*, then upload the sketch to the board.
2. **Import.** In ETS: *Catalogs → Import*, choose `OpenPLC_TP_minimal.knxprod`.
3. **Add the device** from the catalog to a TP line in your project.
4. **Individual address.** Put the board in programming mode and let ETS program the
   address. How the board enters programming mode is not settled yet. The KNX
   programming button is the board's BOOT0 button, and the board has no programming LED.
5. **Download** the application from ETS.
6. **Group addresses.** Link a group address to each object:

| Object | Name | Type | Direction |
|---|---|---|---|
| 1 | Switch | DPT 1.001, 1 bit | written by the bus |
| 2 | Status | DPT 1.001, 1 bit | sent by the board, readable |

## Regenerating the product

The product is generated with [OpenKNXproducer](https://github.com/OpenKNX/OpenKNXproducer)
v4.3.12. It signs with the DLLs of the locally installed ETS, so it only runs on a
machine with ETS installed. With ETS 5.7, the source must use the namespace
`http://knx.org/xml/project/20`.

```
OpenKNXproducer-x64.exe knxprod -o OpenPLC_TP_minimal.knxprod OpenPLC_TP_minimal.xml
```

Keep the last four digits of every ID in the source as `0000`; signing replaces them
with a hash. Commit the source and the regenerated `.knxprod` together.
