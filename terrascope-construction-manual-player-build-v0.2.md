# TerraScope Construction Manual — Player Build (v0.2)

For a player who wants to get every part on the BOM and assemble one.

**Sources — both trees read 2026-10-02:**

- **The live Biotexturas TerraScope tree** — https://biotexturas.org/www/?page_id=55 and all 26 pages under its menu (Hardware, Wetware and Software branches), reviewed page by page per Juan's directive (2026-10-02).
- **Keymer Lab's published Home Scope build documentation** (keymerlab.nl, page_id=3041 and its sub-pages, read 2026-10-01) — still the most complete source for the BOM tables and assembly detail.

Per Juan's naming rule this machine is the TerraScope; the source pages still call it HomeScope (v1.0), so both names appear here where you'll need to find things on the site.

**Honest note on links, checked 2026-10-02:**

- The live site is at **https://biotexturas.org/www/?page_id=55** (Juan's word, verified serving). homescope.biotexturas.org still hands a Hostnet placeholder to direct requests, so every site link below goes to the biotexturas.org address.
- **All downloadable files under homescope.biotexturas.org/docs/ are dead right now (404):** the menu's "get codes" tar (`Ardu_XYZ_codes.tar`) and the per-page `.ino` links (`Arduino_Z.ino` tested) both fail. Until the hosting lands, get the code from the Keymer Lab Supporting Files page (verified working 2026-10-01).

---

## 1. What you're building

A homemade Raspberry Pi camera microscope that records video and image stacks (time-lapse) while robotically scanning a 70 × 70 mm² area.

- **Frame:** OpenBeam beams as documented below; the Aluminum Frame page also allows **V-slot beams** with small bracket changes. Order to the OpenBeam tables unless you deliberately go V-slot.
- **XY stage (OpenStage):** two 180° micro servos driving a 3D-printed PlotterBot gear mechanism. Servo angle range maps to 70 mm of linear travel; at boot both servos go to 90° = center of the viewing area. The optics and illumination stay stationary; the stage moves the sample underneath them.
- **Focus (Z):** one Nema 17 stepper (200 steps) turning a lead screw between two smooth rods, with a flexible shaft coupler.
- **Two Arduino controllers, by design:** Arduino XY (servos, joystick, LCD) and Arduino Z (stepper, buttons) are separate so stage code and focus code can evolve independently. They tell the Raspberry Pi they are busy over pin 12. The published build uses two UNOs; the PCB route on the live site uses two Arduino Nanos (section 2.7).
- **Raspberry Pi 3B + RaspiCAM:** runs the camera, the GUI apps, and sends movement wishes to the Arduinos through logic-level converters.
- **Illumination:** one white LED under the sample, dimmed by a potentiometer fed from the Pi's only PWM-capable pin (0–3.3 V; software control possible later). For fluorescent microscopy the site notes a UV LED must be added, with the coated focal lens protecting the sensor.
- **Optics:** same illumination and optics as the Open FIESTA microscope — C-mount-DIN optical system, focal lens, CNScopes objectives (see section 2.5 for the exact specs the live site now publishes).

---

## 2. Bill of Materials

### 2.1 Frame — OpenBeam beams

| Length | Qty | Where it goes |
|---|---|---|
| 300 mm | 10 | 5 in the top frame, 5 in the bottom frame |
| 270 mm | 4 | vertical corner beams joining top and bottom frames |
| 210 mm | 5 | the stage |
| 150 mm | 6 | rod-clamp beams (top + bottom) + the donor piece you cut down for the 125 mm optical-clamp beam |
| 135 mm | 1 | appears in the materials table; no role described in the text — verify before cutting |

*Discrepancy flagged:* the overview page says the frames are held by "four 300 mm beams," but the Materials table totals (4× 270 mm) and the frame page (both trees: "four 270 mm beams at the corners") agree on **270 mm**. Order to the table.

### 2.2 Brackets and clamps (OpenBeam)

| Part | Top frame | Bottom frame | Stage | Total |
|---|---|---|---|---|
| Nema-17 bracket | — | 1 (holds the motor) | 1 (no motor — lead-screw interface, rotated 90°) | 2 |
| T-bracket | 8 | 12 | 2 | 22 |
| L-bracket | 8 | 6 | 8 | 22 |
| Shaft clamps | 4 (two pairs, upper rails) | 4 (two pairs, lower rails) | — | 8 |

### 2.3 Motion and mechanics

- Smooth rods (linear rails): **2**
- Lead screw + nut: **1**
- Flexible shaft coupler: **1** (Nema 17 ↔ lead screw)
- Linear platform bearings: **2** (stage rides the rods)
- Nema 17 stepper, 200 steps: **1** (Z/focus)
- Micro servos 9 g: **2** (X and Y)
- Z end-stop switches (range switches): **2** — optional to boot, required for real protection (section 5; the published code reads them on pins D6/D7)
- Custom-made: lead-screw-to-Nema-bracket adaptor
- Custom-made: laser-cut acrylic stage board

### 2.4 3D-printed parts

- PlotterBot pieces: **6** — print the **OLD version of the PlotterBot with 2 servos** (not the 3-servo version)
- Cambridge OpenLabTools optical clamp: **1**
- Cambridge OpenLabTools RaspiCAM holder: **1**
- Optical tube: **1** (printed alternative to the Edmund metal tubes — the Optics page points at a 3D-printed version by Comunicaciones Especulativas with downloadable .stl files)

### 2.5 Optics

- **Objectives (CNScopes or cheap Chinese-market objectives):** the build uses **4× and 10×**; the live Optics page states 4×, 10×, 20× and even 40× work with the system.
- **Focal lens:** Comar Optics **50VB25** — bi-convex, 50 mm focal length, 25 mm diameter, AR coating passing 450–900 nm (blocks deep UV, protecting the sensor under blue/UV illumination). The page notes a cheaper uncoated variant (50VQ25) and other suppliers.
- **Optical tube, two options:**
  - *Metal, Edmund Optics (expensive):* five parts — DIN/C-mount adapter (03-627), C-mount 40 mm tube (54-631), lens mount (56-353), C-mount 10 mm tube (54-629), adjustable-length tube (58-756).
  - *3D-printed (cheap, recommended by the site):* the Comunicaciones Especulativas version, .stl files linked from the Optics page.

### 2.6 Electronics

- Raspberry Pi 3B: **1**
- RaspiCAM: **1**
- Arduino UNO: **2** (breadboard route; the PCB route uses 2× Arduino Nano instead — see 2.7)
- Easy Driver (stepper driver): **1**
- Logic-level converters: **3**
- Joystick module: **1**
- i2c 16×2 LCD: **1**
- Breadboards: listed on the source page as "3× + 3×"
- Cobbler Pi PIN cable: **1**
- Push buttons: **2**
- Resistors (LED current limiting + button pull resistors)
- White LED: **1**
- Potentiometer: **1** (light intensity)
- Nuts & bolts for OpenBeam: "lots" — the source page does not specify sizes

### 2.7 The PCB route (documented on the live site)

The **get a PCB** page offers three custom boards that replace the breadboard wiring:

- **Z control board** — Arduino Nano (Arduino Z), a state LED (off = listening, on = executing), one logic-level converter; socket clusters for the EasyDriver (GND / Step / Dir), a **Range** header for the two Z end-stop switches, the push-button cluster, the RBPi link (state signal + 3.3 V), and the LV rail.
- **Motor control board** — EasyDriver, two power plugs for a 12 V line, two logic-level converters; motor-phase socket (check your motor's color code) + Z-control pass-through.
- **XY control board** — Arduino Nano (Arduino XY), two logic-level shifters, state LED; servo headers (2×3 pins), the RBPi socket (2 bits per movement + state + joystick click), and the user cluster (LCD i2c SCL/SDA + joystick X/Y/click).
- **Mounting:** laser-cut **universal PCB holder** and **RBPi holder** — files linked on the page (pdf / dxf / svg). Each board carries Sketch / PCB / Gerber links.
- **BOM delta:** 2× Arduino Nano instead of 2× UNO, no breadboards or cobbler in this route; everything else (Easy Driver, converters, LCD, joystick, buttons) is unchanged.

*Marked:* the PCB route is the newer path. This manual's steps and pin maps come from the documented UNO/breadboard build and the published code, which the boards implement one-to-one.

---

## 3. Fabricate before you assemble

1. 3D print: 6× PlotterBot pieces (old 2-servo version), optical clamp, RaspiCAM holder, optical tube.
2. Laser-cut: acrylic stage board.
3. Make: the lead-screw-to-Nema-bracket adaptor.
4. Cut: the 125 mm beam from a 150 mm piece (it carries the optical clamp).
5. *PCB route only:* laser-cut the universal PCB holder and the RBPi holder from the get-a-PCB files.

*Gap:* no dimensions are published for the two custom parts — source them from the OpenLabTools/PlotterBot reference builds or from an existing TerraScope.

---

## 4. Assembly order (from the frame documentation)

**Guiding principle, the site's own words (Assemble a frame page): all vertical axes need to have freedom of motion in 2D at every level.** That is what the loose-bolt discipline and the local XY adjustments below are for.

1. **Stage:** five 210 mm beams — four L-brackets form the rectangle, two T-brackets join, four side L-brackets carry the linear platform bearings. Bolt the Nema-17 bracket (no motor, rotated 90°) as the lead-screw interface. Add the acrylic board, the PlotterBot mechanism with both servos, and the dish holder.
2. **Top frame:** five 300 mm beams + two 150 mm beams holding two pairs of shaft clamps for the smooth rods. Cut and fit the 125 mm beam for the optical clamp. Eight T-brackets inside, eight L-brackets on the corners.
3. **Bottom frame:** five 300 mm beams + an extra pair of 150 mm beams carrying the lower shaft clamps and the Nema-17 bracket **with** the motor. Twelve T-brackets, six L-brackets.
4. **Join:** stand top on bottom with the four 270 mm vertical beams (six L-brackets for the union). Keep every alignment-critical attachment **loose** at this stage.
5. **Z axis:** drop the smooth rods through the top clamps → stage bearings → bottom clamps. Lead screw down the center, coupled to the Nema 17 with the flexible coupler.
6. **Align, then tighten:** the left/right rod-clamp pairs, the optical system, the center lead screw, and the Nema bracket all slide locally in XY by design. Use that play to align top, bottom, and stage, then tighten. The documentation calls this the critical design feature for easy assembly.
7. **Optics head:** optical clamp on the 125 mm beam → optical tube + focal lens + objective; RaspiCAM into its holder.
8. **Illumination:** white LED under the sample position; potentiometer for intensity, powered from the Pi's PWM pin.
9. **Electronics:** both Arduinos, Easy Driver, LCD, joystick, buttons, and the three logic-level converters on the breadboards (or the three PCBs, section 2.7), per the pin maps below.

---

## 5. Electronics pin maps

### Arduino XY

| Function | Pin |
|---|---|
| Pi message, X up / X down | D2 / D3 |
| Pi message, Y up / Y down | D4 / D5 |
| Joystick X / Y | A0 / A1 (center ≈ 512; threshold ±64) |
| Servo X / Servo Y | D9 / D10 (PWM) |
| Busy signal to Pi | D12 |
| LCD over i2c | A4 = SDA, A5 = SCL (address 0x27; Wire + LiquidCrystal_I2C) |

Servo angles track 0–180° and are displayed live on the LCD ("position: x,y"). Boot message in the current published code: **"HomeScope / by Biotexturas"** (the v1.0 docs print "OpenStage" — same slot, different text).

### Arduino Z

| Function | Pin |
|---|---|
| Push buttons up / down | D2 / D3 (external pull-ups; a press reads LOW) |
| Pi message, Z up / Z down | D4 / D5 |
| Z end-stop, top / bottom | D6 / D7 (code enables internal pull-ups; the stage moves only while both read HIGH; serial prints "Stage out of range" otherwise) |
| Easy Driver: direction / step | **D8 / D9** |
| Busy signal to Pi | D12 |

One step per actuation cycle (DISTANCE = 1) — finest possible focus resolution.

*Flagged conflict on the live site:* the Arduino overview prose says the stepper is driven over "pins 7 and 8," but the Z code page itself defines `driver_dir = 8` and `driver_stp = 9`. This manual follows the **code** (D8/D9) — same as v1.0.

### Raspberry Pi side

- Six GPIO outputs carry the movement protocol, two bits per axis (WiringPi pins 1, 2, 3, 4, 24, 29), through the logic-level converters: (0,0) stay-put, (1,0) move up, (0,1) move down.
- LED on the Pi's PWM-capable pin via the potentiometer (Juan's TerraScope-as-a-Network diagram labels it pin 23, WiringPi numbering).
- The Pi reads each Arduino's busy state on its side of pin 12 (present in the wiring; the current code ignores it — reserved for autofocus and tracking).
- Pi numbering on these pages is **WiringPi** notation, used by the `gpio` utility.
- Both Arduinos are two-state automata (listening / executing), reflected on pin 12 (LOW = listening, HIGH = executing) — straight from the Arduino page.

---

## 6. Software setup

1. **Arduinos:** from `XYZ.tar` (Keymer Lab Supporting Files page — the live site's own "get codes" tar and `.ino` links are 404 as of 2026-10-02), upload `Arduino_XY.ino` to one controller and `Arduino_Z.ino` to the other (`tar -xvf`, then the Arduino IDE).
2. **Raspberry Pi:** extract `open_stage.tar` and `open_scope.tar`; in each expanded directory (`Stage_code`, `Scope_code`) run `make` (dependencies assumed installed).
3. Run **OpenStage** (stage control + image/video capture) and **OpenScope** (focus + illumination) from the desktop. Shell control also works: `gpio` (WiringPi), `raspivid`, `raspistill`, and `v4l2` (the Linux shell page lists all four).
4. **The app suite named on the live site** (Linux apps page) — the direction the software is documented to grow:
   - **HomeScope_core** — focus, illumination and image/video capture; the hub the other two connect to.
   - **HomeScope_stage** — CNC scanning with G-code capabilities; takes commands from core and oracle to track patterns of activity in the culture.
   - **HomeScope_oracle** — reads time-lapse data with machine vision and learning, and issues game commands through the **Oracle field** (the optical-flow field at a given scale, extracted by a Python/OpenCV collection).
   - These three are what the site describes today; the published downloadable tars remain the v1.0 `open_stage` / `open_scope` pair above.

---

## 7. First light — test sequence

1. Power up: LCD prints its boot message ("OpenStage" in the v1.0 docs, "HomeScope / by Biotexturas" in the current code) and both servos center to (90,90).
2. Move XY with the joystick — deflection beyond the threshold actuates, LCD tracks the angles.
3. Focus with the two push buttons — one step per press. If the end-stop switches are fitted, the stage refuses to move past either limit and the serial monitor reports "Stage out of range".
4. Set illumination with the potentiometer.
5. `raspistill` a test frame; check focus at 4×, then 10×.
6. Drive the same axes from the GUI buttons — the Pi speaks the identical two-bit protocol.
7. Run a short time-lapse; the site's own examples scan the stage while a Paenibacillus swarm moves underneath (its Microbial Swarm page shows a 10-minute-interval time-lapse at 4×).

---

## 8. Gotchas from the source documentation

- **Frame rule (site's own line):** every vertical axis needs freedom of motion in 2D at every level until the final alignment pass.
- **Two Arduinos is the design**, not redundancy — the split keeps stage and focus development independent.
- Print the **old 2-servo PlotterBot** parts, not the 3-servo revision.
- The viewing area is a **small square inside** the 70 × 70 mm scan range; its size depends on objective magnification.
- Buttons need their **external pull-ups** — press = LOW.
- The Z end-stops use the code's internal pull-ups, so the machine boots and moves fine with no switches fitted; fit them before trusting long unattended focus runs.
- Do not skip the **logic-level converters** between Pi and Arduinos (5 V vs 3.3 V).
- The coated focal lens **protects the RaspiCAM** from UV/blue LED damage — it is also what makes fluorescence microscopy possible later.
- Leave alignment-critical bolts loose until the final alignment pass.
- **Two names, one machine:** the pages alternate HomeScope and TerraScope; per Juan's rule, TerraScope.

---

## 9. Reference links

### Live site — Biotexturas TerraScope tree (all verified 2026-10-02)

- TerraScope landing: https://biotexturas.org/www/?page_id=55
- Hardware overview: https://biotexturas.org/www/?page_id=83
- Robotics/Computing: https://biotexturas.org/www/?page_id=97
- Imaging: https://biotexturas.org/www/?page_id=320
- Optics: https://biotexturas.org/www/?page_id=132
- Aluminum Frame: https://biotexturas.org/www/?page_id=124
- Build it!: https://biotexturas.org/www/?page_id=154
- Assemble a frame: https://biotexturas.org/www/?page_id=419
- get a PCB: https://biotexturas.org/www/?page_id=219
- 3D print an optical tube: https://biotexturas.org/www/?page_id=226 (page is currently empty)
- Wetware: https://biotexturas.org/www/?page_id=117
- Microbial Swarm: https://biotexturas.org/www/?page_id=281
- Grow chamber: https://biotexturas.org/www/?page_id=404
- Grow it!: https://biotexturas.org/www/?page_id=169
- Software: https://biotexturas.org/www/?page_id=115
- Arduino (protocol + two-state machine): https://biotexturas.org/www/?page_id=286
- Arduino Z (code): https://biotexturas.org/www/?page_id=350
- Arduino XY (code): https://biotexturas.org/www/?page_id=353
- Linux shell: https://biotexturas.org/www/?page_id=469
- Linux apps (C + GTK): https://biotexturas.org/www/?page_id=284
- core: https://biotexturas.org/www/?page_id=356
- stage: https://biotexturas.org/www/?page_id=359
- oracle: https://biotexturas.org/www/?page_id=363
- Machine Vision: https://biotexturas.org/www/?page_id=275
- Oracle field: https://biotexturas.org/www/?page_id=376
- Hack it!: https://biotexturas.org/www/?page_id=159

### Keymer Lab build docs (verified 2026-10-01)

- Home Scope overview: https://keymerlab.nl/www/?page_id=3041
- Materials (BOM): https://keymerlab.nl/www/?page_id=4259
- Illumination system: https://keymerlab.nl/www/?page_id=3031
- Aluminium frame: https://keymerlab.nl/www/?page_id=3880
- Electronic system: https://keymerlab.nl/www/?page_id=4322
- Arduino XY: https://keymerlab.nl/www/?page_id=4325
- Arduino Z: https://keymerlab.nl/www/?page_id=4327
- Controlling the TerraScope: https://keymerlab.nl/www/?page_id=4329
- Supporting files (code tars): https://keymerlab.nl/www/?page_id=4337
- OpenFIESTA step-by-step guide (PDF): https://keymerlab.nl/www/wp-content/uploads/2017/09/OpenFiesta_microscope_hackaton.pdf
- OpenFIESTA installation manual (PDF): https://keymerlab.nl/www/wp-content/uploads/2017/09/InstallationManual_OpenFIESTAscope.pdf

### External

- Cambridge OpenLabTools microscope: http://openlabtools.eng.cam.ac.uk/Instruments/Microscope/
- PlotterBot tiny-CNC: http://plotterbot.com/robots/tiny-cnc/

---

## 10. Known gaps in this draft (updated 2026-10-02)

1. **homescope.biotexturas.org still serves the Hostnet placeholder** to direct requests (re-checked 2026-10-02); the live tree sits at biotexturas.org/www/?page_id=55 per Juan, and that is where all links above point.
2. **All homescope /docs/ downloads 404** (Ardu_XYZ_codes.tar and Arduino_Z.ino both tested 2026-10-02) — use the Keymer Lab Supporting Files page until the hosting lands.
3. The **"3D print an optical tube" page is empty** (title only, no content) — the Optics page carries the actual tube information.
4. **Site prose/code conflict** on the Z step pins (overview prose 7/8, code 8/9) — this manual follows the code.
5. Nut and bolt **sizes unspecified** on the source pages.
6. **No dimensions** published for the two custom parts (lead-screw adaptor, acrylic stage board).
7. **No cost figures** anywhere on the source pages.
8. The Keymer Lab "pdf → English" page is **empty** — this PDF is the working substitute.
9. This documents the **published v1.0 design plus what the live tree adds** (PCB route, optics specs, end-stops, app suite). Juan's own current bench revisions fold in on his word (question b in the delivery note).
