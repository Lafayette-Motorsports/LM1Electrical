# Safety loop LTspice circuit

Open **safety_loop.asc** in LTspice. Keep every `.asy` and `.lib` file in this folder alongside it. No symbol installation or external component library is required for these models. Click Run and probe `TO_SAFETY_LOOP`, `AMS_RESET_MINUS`, or `COIL_RETURN`.

The relay symbol is a box with COM and COIL START on the left; NC, NO, and COIL END on the right. Both instances use the same five-port `RELAY_SPDT` subcircuit, in that exact order.

## Falstad configuration

- Each relay: 0.2 H, 720 ohm, pickup 12 mA, dropout 10 mA, closed contacts 0.05 ohm, open contacts 1 megohm.
- The 5 ms mechanical setting is represented by a first-order response to coil-current magnitude. This preserves the setting but approximates Falstad's armature dynamics; exact switching timestamps are not guaranteed.
- 12 V IMD latch output and separate 10 V OKHS source.
- Parallel relay coils feed a low-side NMOS; flyback diode has its cathode at +12 V and anode at the coil-return/drain node.
- K1 COM feeds the NC red-LED branch and NO safety-loop branch. Each branch retains its 1 kohm resistor.
- K2 NO parallels the reset button. K2 NC (`NC2`) is intentionally unused.
- **User-approved addition:** `R_GATE = 47 kohm` from the MOSFET gate to ground. No other pull-down or series gate resistor was added.

## Simulation controls and model choices

The clickable Falstad switches are represented by controlled SPST subcircuits. Their PWL control sources follow the newer LTspice reference's demonstration sequence: reset at 0.5–0.6 s, OKHS fault at 2–3 s, second reset at 3.5–3.6 s. Right-click `V_RESET` or `V_OKHS` to edit the sequence; a constant 1 closes a contact and 0 opens it. The full PWL values are hidden on the drawing for readability and remain in the component settings.

The MOSFET uses generic NMOS parameters Vto=1.5 V, Kp=0.02 A/V², Lambda=0; its simulated 0.19513 V drain voltage reproduces the supplied Falstad steady coil current closely. Diode and red-LED models come from the supplied newer LTspice reference. The Falstad text names default semiconductor models without their parameter definitions, so exact cross-simulator semiconductor equivalence is not claimed. Manual switches retain the reference model's 0.01 ohm on / 1 Gohm off approximation. Falstad's serialized snapshot currents and switch positions are not forced as initial conditions; the test starts unlatched.

## Files

- `safety_loop.asc`: editable visual schematic.
- `RELAY_SPDT.asy`, `relay.lib`: requested relay box and five-port model.
- `SW_CTRL.asy`, `LED_IMD.asy`, `SL_*.asy`: local circuit symbols.
- `safety_models.lib`: switch, NMOS, diode, and LED models.
- `schematic_preview.png`: preview rendered directly from the schematic and symbol geometry.
- `validation.txt`: final LTspice batch-simulation results.

The supplied reference files were read for reference; the new design is saved separately. The final schematic was netlisted and simulated successfully in installed LTspice 26.0.2. Validation confirms the modeled reset/fault/re-reset sequence; it does not establish hardware safety certification.
