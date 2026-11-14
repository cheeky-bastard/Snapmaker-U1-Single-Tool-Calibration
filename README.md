# Snapmaker U1 Single Tool Calibration

**Project version: 0.2.0-dev2 | Updated: 2026-11-14**

**Button-start update:** CFG `0.1.1` starts T1/T2/T3 directly from their buttons. Separate backup acknowledgement is optional and disabled by default. Native Python payloads are unchanged. The new CFG has offline coverage; the 2026-10-17 live evidence below used CFG `0.1.0`.

Independent T1/T2/T3 calibration and a guided clean-and-validate workflow for the Snapmaker U1.

This project extends the native offset-saving logic and reuses the stock heating, wiping, docking, and probing routines. Recalibrating one secondary tool preserves the other three tools' saved positions and applied offsets. T0 remains the reference: its button displays a warning to use stock **Multi-Toolhead Calibration** for all four tools.

## Acknowledgements and license

Thank you to [Paxx](https://github.com/paxx12-snapmaker-u1) for [Snapmaker U1 Extended Firmware](https://github.com/paxx12-snapmaker-u1/SnapmakerU1-Extended-Firmware) and its documented overlay and documentation conventions, which informed this project's organization. The calibration implementation builds on [Snapmaker's U1 Klipper](https://github.com/Snapmaker/u1-klipper) and [Klipper](https://github.com/Klipper3d/klipper); their existing copyright and license notices are preserved.

**Modified work, dated 2026-10-12:** this project changes native calibration persistence and offset handling, and adds the custom macros described below. It is distributed under **GPL-3.0** with source and the full [license](LICENSE). See [NOTICE](NOTICE.md) for the modification record and attribution. It is independently maintained by this project; affiliation with or endorsement by Paxx, Snapmaker, or Klipper is not implied.

## Tested status

| Source profile | Status | Exact source manifest |
| --- | --- | --- |
| `stock-1.4.1.6` | Live validated on one U1 running `1.4.1.6_20260608141446` | [Original manifest](tests/native/manifest.json) |
| `paxx-2.0-pr760` | Planned live acceptance passed, including all secondary tools, restarts, stock full, validation and both web UI warnings | [PR760 manifest](profiles/paxx-2.0-pr760/manifest.json) |

Profiles match all three native source hashes, rather than a release label. PR760 is a changing development PR; this candidate targets the captured source set only. The original 1.4 native payload remains version `0.1.0`; the current shared macro CFG is `0.1.1`. A separate PR760 usermod candidate passed a full firmware-image build and five checks against the unpacked image. After the operator flashed it, 20 boot checks, 16 independent T2 save checks and nine settled restart checks passed. Post-flash clean/validate passed 16 checks with all six tool pairs within 0.05 mm. Planned printer testing is complete. See [Firmware compatibility](docs/firmware-compatibility.md).

## Community testing

Repository restored under `cheeky-bastard` on 2026-11-14. The former `cheekyb52` repositories were removed. Historical source banners, copyright notices, saved test records and payload hashes are preserved; old URLs in those records identify their original provenance. Current project and build links use the new account. The stable `cheeky_b52` mod namespace is retained for compatibility. No printer configuration or calibration data is included.


The project is being restored under [cheeky-bastard](https://github.com/cheeky-bastard).
The same reviewed calibration/native/CFG payloads and pinned firmware bases are retained:

| Firmware | Source base | Downloads |
| --- | --- | --- |
| 2.0 | `2c80e3dc495fdf724bc53c5c35c875010ee1668f` / stock 2.0.0.205 | [2.0 test builds](https://github.com/cheeky-bastard/SnapmakerU1-Extended-Firmware/releases) |
| 2.1 | `36fbd40c63ad83818ef3a7b2f8facef48814dd58` / stock 2.1.0.157 | [Separate 2.1 test builds](https://github.com/cheeky-bastard/Fork-SnapmakerU1-Extended-Firmware_2.1/releases) |

Restored firmware CI builds are pending. Earlier automated and live results remain historical evidence for their exact source/image sets. The new images require their own printer acceptance.

Both firmware lines include the independent calibration native patch and the separately guarded mixed-nozzle print-start workaround. The 2.1 workaround preserves its newer automatic filament-replenishment behavior. Keep actual nozzle sizes correct in printer and slicer. Installing only the CFG does not install the Python changes.

Read [Installation](docs/installation.md), [Work instructions](docs/work-instructions.md), [Firmware compatibility](docs/firmware-compatibility.md), and [Rollback](docs/rollback.md). Use [this project's issues](https://github.com/cheeky-bastard/Snapmaker-U1-Single-Tool-Calibration/issues) for test reports; keep credentials, addresses and saved calibration tables private. Upstream submission remains deferred.

## Features

| Button | Behavior |
| --- | --- |
| `CALIBRATE_TOOL_1`, `CALIBRATE_TOOL_2`, `CALIBRATE_TOOL_3` | Clean the reference and selected tools, measure their relative correction, and save only the selected tool |
| `CALIBRATE_TOOL_0` | Display an informational web UI prompt directing the operator to stock full calibration |
| `CLEAN_AND_VALIDATE_OFFSETS` | Clean all four tools, probe the ring, and compare all six tool pairs against configurable XYZ tolerances; save nothing |
| `OFFSET_CONTINUE` | Advance after manual nozzle cleaning or PEI plate removal |
| `OFFSET_ABORT` | Exit a waiting custom cycle; does not undo an earlier save |

**Macro buttons and Mainsail groups:** The names above are Klipper macros that appear as clickable buttons in the web UI. For Mainsail, we recommend putting all seven buttons in a dedicated **Tool Calibration** macro group so the calibration, cleaning, **OFFSET_CONTINUE**, and **OFFSET_ABORT** controls stay together.

In Mainsail, open **Interface Settings → Macros**, switch **Management** to **Expert**, then select **Add Group** and add the macros listed above. Enable the group's **Standby** visibility so the buttons are available while the printer is idle.

Independent saves persist a shared reference in the existing calibration JSON. Stock full calibration retires it and restores ordinary centering. A native capability check prevents the macros from silently using an older save implementation.

The custom workflow uses the web console for manual confirmations. T0's warning uses the common Fluidd/Mainsail prompt protocol. The stock touchscreen workflow remains available separately.

## Documentation

- [Installation](docs/installation.md) — verified backups, source matching, staging, and deployment checks
- [Firmware compatibility](docs/firmware-compatibility.md) — PR760 port, source differences, and firmware-update recovery
- [Macro work instructions](docs/work-instructions.md) — buttons, temperatures, tolerances, and completion
- [Modified files](docs/changes.md) — every printer file changed and its purpose
- [Implementation](docs/implementation.md) — shared-reference and persistence contracts
- [Validation](docs/validation.md) — tested behavior and remaining limits
- [Rollback](docs/rollback.md) — matched code/data recovery
- [Codex handoff](docs/codex-handoff.md) — instructions for implementing on another owner's printer
- [Development](docs/development.md) — portable tests and overlay layout

## Validation

96 offline checks pass: 32 native regressions per source profile, 25 shared macro checks and seven profile/staging checks. The original 1.4 live acceptance remains separate from the completed PR760 checks below, recorded on 2026-10-16–17.

| Case | Result |
| --- | --- |
| Installation/startup | 16 checks passed |
| Independent T1/T2/T3 calibration | 16 checks each; only the selected tool changed |
| Normal restart persistence | Nine checks each after T1, T2, T3, stock full and the subsequent independent T2 save |
| Stock full calibration | Ten pre-Finish and eleven post-exit checks; operator confirmed normal completion |
| Independent T2 after stock full | 16 save checks; new stock frame pinned and other three tools preserved |
| T0 warning in Mainsail and Fluidd | 13 checks each; operator confirmed OK dismissal and no touchscreen anomaly |
| Abort at a manual-cleaning wait | 12 checks passed; saved and applied calibration unchanged |
| Default clean/validate | 16 checks; all six pairs within 0.05 mm XYZ limits; no calibration changes |
| Deliberately tight validation | 17 checks; all six pairs correctly flagged at 0.000001 mm XYZ; no calibration changes |

Default PR760 validation's largest absolute pair residuals were 0.0023 mm X, 0.0028 mm Y and 0.0035 mm Z. Both validation runs preserved saved JSON bytes, all four applied offsets and the shared reference, with correct active origin, idle controllers and zero heater targets at completion.

A separate optional PR760 usermod candidate passed a complete firmware-image build, five unpacked-payload checks and 17 build-guard fixture checks. The operator flashed the image and it passed 20 boot checks, preserving the current calibration and all offsets. A post-flash independent T2 cycle passed 16 checks; its normal restart passed nine settled-state checks. The early snapshot preceded native automatic tool activation and is retained in private evidence; fresh snapshots confirmed the correct origin without a firmware change. Post-flash clean/validate passed 16 checks; all six pairs were within 0.05 mm, with maximum absolute XYZ residuals of 0.0095, 0.0040 and 0.0072 mm. Saved calibration, all offsets and reference remained unchanged. Planned printer testing is complete. These results establish save/reference/workflow behavior on one printer and exact source set; measured printed alignment and broad firmware compatibility remain unverified. See [Results and limits](docs/validation.md).

Speed tuning is deferred. The validated settings remain ten calibration ring points, six validation ring points, and three XY repeat samples.

On 2026-10-17, the owner also reported having printed using these macros and that the results looked fine. This is owner-reported visual experience; a measured alignment artifact remains untested.

## Repository layout

```text
overlays/mods/tooloffsets/
  01-native/patches/home/lava/klipper/   Native three-file patch
  02-macros/root/usr/local/share/firmware-config/extended/klipper/
                                      Macro template
docs/                                 Operator and developer guides
profiles/paxx-2.0-pr760/                Separate PR760 patch and source manifest
tests/                                Synthetic fixtures and sanitized acceptance results
tools/                                Offline staging and privacy checks
```

The numbered overlays and documentation organization follow the conventions described in [Paxx's development guide](https://github.com/paxx12-snapmaker-u1/SnapmakerU1-Extended-Firmware/blob/develop/docs/development.md). This is an independent project prepared for the owner's GitHub account. Paxx's repository is a reference only; nothing has been submitted there.

## Privacy and license

This package contains calibration-project material only. Device addresses, credentials, personal paths, actual calibration tables, captured images, raw logs, and local backups are excluded. The automated audit and package inventory are documented in [Development](docs/development.md).

GPL-3.0; see [LICENSE](LICENSE) and [NOTICE](NOTICE.md) for upstream source attribution.
