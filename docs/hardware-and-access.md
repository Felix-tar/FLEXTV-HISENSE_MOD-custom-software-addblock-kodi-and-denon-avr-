# Reference hardware and authorized access

## Confirm the reference platform

| Item | Reference |
| --- | --- |
| TV model | Hisense 55U8QF |
| System | VIDAA U4 `V0000.01.00C.P0219` with Linux `4.9.44` |
| Userland | ARM EABI5 with the soft-float calling convention and `/lib/ld-linux.so.3` |
| Display and media | DirectFB/GOP graphics, Mali GLES 2, MStar VDEC and the hardware video path |
| Optional AVR | Denon AVC-X3800H with the TV's HDMI 1 path and HDMI-CEC/ARC in the reference setup |

Ask the operator to read the model and firmware from the TV settings and confirm
the regional hardware revision. A matching model name alone gives an incomplete
compatibility check. Use the [reference platform inventory](../projects/hisense-vidaa-root-mod/docs/06-reference-platform.md)
and [TV architecture](../projects/hisense-vidaa-root-mod/docs/01-hisense-architecture.md)
to compare the application and media environment. Keep unique device identifiers
in the operator's private notes.

## UART and the adapter

UART is an asynchronous serial interface. The transmit signal (TXD), receive
signal (RXD) and ground reference (GND) have different roles. A USB serial
converter exposes a serial interface on a computer and translates it to the
specified signal levels on the cable. A service connector is a device-side
interface with its own electrical mapping and documented service procedure.
The shape of a jack only identifies the physical connector. TTL UART, RS-232
serial and analog audio use different signal interfaces.

The supplied product listing pictured a DSD TECH USB-to-3.5 mm lead described
as 3.3 V TTL with a length of about 1.8 m. DSD TECH catalogues the SH-U35A in
this adapter category. The vendor drawing gives the adapter-side signal
assignments. Confirm the ordered model and hardware revision with the seller.
[DSD TECH adapter specifications](https://www.deshide.com/product-details_SH-U35A.html)

The reference investigation encountered a signal-mapping difference between the
adapter listing and the device connection. The adapter drawing is evidence
about the cable end. The TV service specification is the source for the device
end. Confirm compatibility from both sets of documentation before purchasing
for a particular device or making a connection.

| Before ordering or connecting | Confirm with the vendor or technician |
| --- | --- |
| Correct device | Exact model, board or hardware revision, and named service connector |
| Signal standard and level | Documented UART logic level at both ends and the adapter's input/output tolerance |
| Contact mapping | Signal roles, ground and physical connector contact numbering for each end |
| Host support | Identified USB bridge chipset and a supported driver for the host OS |
| Service method | Manufacturer-supported authorization, cabling procedure, and recovery option for this firmware |

Follow the device documentation for power state, connection and any measurements.
Have an authorized technician resolve unclear or incompatible wiring. Keep the
product listing and the device service document separate in the handoff to the
coding agent.

## Provide access and context

The TV-side guides start with a working, operator-authorized development session.
Use the service or development procedure supplied for the particular device.
The operator determines the approved service method and sets up access before
giving the coding agent a target. Where the build guide uses SSH, give the agent
the already configured target and confirm its host key through a trusted source.
Keep the access configuration, passwords and private keys outside the repository.

The coding agent can start with local source review, build and tests even while
device access is pending. Share observations such as the firmware label, the
interfaces required by the planned features, and masked error messages.
A working authorized session can confirm the CPU architecture and kernel using
ordinary read-only system queries such as `uname -m` and `uname -r`. Compare the
results with the reference and document differences before any deployment.

Continue with [the integrated build and validation sequence](reproduction.md).
