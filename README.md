# APRSOTA
Detailing some hardware setups proven to work for APRSOTA

# APRS OTA Hardware & Setup Guide (OTAgo)

**OTAgo** (`[https://otago.dl7pj.workers.dev/](https://otago.dl7pj.workers.dev/)`) is a web app developed by DL7PJ for playing **APRS OTA** (`aprsota.org`). Because APRS OTA requires packets to travel over RF (for hosting activations as well as receiving two-way bot replies and QSL confirmations), you need an RF link between OTAgo and APRS-IS.

Choose one of the three hardware setups below depending on your equipment, local iGate coverage, and whether you are operating at home or in the field.

| Setup | Hardware Needed | Local RX/TX iGate Required? | Best For |
| --- | --- | --- | --- |
| **Option 1: Dual UV-K1** | 2x Quansheng UV-K1 + PC | No (you host your own) | Operating at home with no local two-way iGate |
| **Option 2: Bluetooth HT** | 1x BT KISS TNC HT + Phone/PC | Yes (two-way RX/TX) | Lightweight portable field operation |
| **Option 3: Worldwide Remote** | Option 1 at home + Phone | No (uses your home iGate) | Operating worldwide from your phone via your home RF station |

---

### Option 1: Fully Self-Contained Dual UV-K1 Setup (Local iGate + Serial KISS TNC)

If you do not have a local two-way (**RX/TX**) iGate nearby, you can host everything yourself at home. This setup uses **two Quansheng UV-K1** handhelds connected to a single PC: one acts as your **Serial KISS TNC** (controlled by OTAgo), and the other acts as your local **RF-to-APRS-IS iGate** (controlled by a custom Python script). Your OTAgo client transmits over RF from Radio 1 to Radio 2, which gates the traffic into APRS-IS.

#### Hardware & Wiring

* **2x Quansheng UV-K1** running firmware with the **KISS TNC** overlay app installed.
* **2x Data-capable USB-C cables** plugged directly into your PC.
* **Remove the batteries** from both radios. The USB-C connection powers the radios directly, and running without batteries prevents battery degradation from continuous charging and heat.

#### Radio Configuration (Both Units)

1. **Frequency:** Set both radios to the exact same simplex frequency (any valid amateur frequency works as long as both match).
2. **TX Power:** Set transmit power to the **lowest possible setting** on both radios. Because the antennas sit right next to each other and your computer, higher power will cause RF interference that can crash or glitch your PC's USB ports.
3. **Launch KISS TNC:** Open the **KISS TNC** overlay app (`F + 7`) on both radios so they expose their USB serial KISS interfaces to the computer.

#### Software Setup

1. **iGate (Radio 1):** Run the custom Python iGate script (`[Download Link]`) pointed at the first UV-K1's COM/serial port to forward RF packets between your local frequency and APRS-IS.
2. **OTAgo Client (Radio 2):**
* Open `[https://otago.dl7pj.workers.dev/](https://otago.dl7pj.workers.dev/)` in **Chrome, Edge, or Firefox (151+)** on your PC.
* Under **Radio · Serial (USB)**, connect to the second UV-K1's COM/serial port.



---

### Option 2: Single Bluetooth KISS TNC Handheld (Portable Field Setup)

If you are within range of a local two-way (**RX/TX**) iGate, this is the cleanest portable setup. You only need a single handheld with a built-in Bluetooth KISS TNC (such as a **TIDRADIO TD-H9**, **BTECH UV-PRO**, or **Vero VR-N76**) paired directly to your phone or laptop, with no extra cables, external modems, or second radio required.

#### Requirement: Local RX/TX iGate

* This setup **only works if you are within RF range of an RX/TX iGate** (one that both receives RF packets into APRS-IS and gates APRS-IS messages back out onto RF).
* If your local iGate is **RX-only**, you will not receive replies or QSL confirmations back from the OTA bot over RF, and the session will not work.

#### Hardware & Radio Setup

1. **Radio:** A handheld with built-in Bluetooth KISS TNC support:
* **BTECH UV-PRO / Vero VR-N76** (classic **Bluetooth SPP**).
* **TIDRADIO TD-H9** (**Bluetooth Low Energy / BLE**).


2. **Frequency & Squelch:** Tune the radio to your local APRS frequency (**144.390 MHz** in North America). Set squelch low (or open) and turn off battery/power-saving sleep modes so the TNC does not miss incoming packets.
3. **Enable KISS TNC:** Turn on Bluetooth and enable the KISS TNC mode in your radio's settings.

#### Connecting to OTAgo (Phone or PC)

1. **Browser:** Open `[https://otago.dl7pj.workers.dev/](https://otago.dl7pj.workers.dev/)` in **Chrome, Edge, or Firefox (151+)** on Android or desktop (note: **BLE for the TD-H9** requires **Chrome or Edge**). Close any other APRS apps (like APRSdroid or ODmaster) so they do not hold the Bluetooth connection.
2. **Pair & Link:**
* **For UV-PRO / VR-N76 (Bluetooth SPP):** Pair the radio in your device's Bluetooth settings first, then open OTAgo's radio menu (**Radio · Bluetooth (SPP)**) and select your radio.
* **For TD-H9 (BLE):** In Chrome or Edge, enable `chrome://flags/#enable-experimental-web-platform-features` and `chrome://flags/#enable-web-bluetooth-new-permissions-backend`, restart the browser, and pair the radio inside OTAgo under **Radio · BLE (TD-H9)**.


3. **Path:** Set your APRS path under **Radio · Path** (typically `WIDE1-1,WIDE2-1` on the ground, or `WIDE1-1` from a summit or high elevation) so your packets reach the local RX/TX iGate.

---

### Option 3: Worldwide Remote Self-Contained Setup (Home Station Gateway + Phone)

This builds directly on **Option 1** so you can operate APRS OTA from anywhere in the world using just your phone over cellular data or Wi-Fi, while your home PC and dual UV-K1 setup handle the RF link back home.

#### 1. Home PC Setup (Gateway Host)

1. **Hardware & iGate:** Set up the two Quansheng UV-K1 radios (batteries removed, lowest TX power, matching simplex frequency, KISS TNC overlay open) and start your local Python iGate script on Radio 1 just like in **Option 1**.
2. **Connect Radio 2 in OTAgo:** Open `[https://otago.dl7pj.workers.dev/](https://otago.dl7pj.workers.dev/)` in **Chrome, Edge, or Firefox (151+)** on your home PC, sign in with your callsign and APRS-IS passcode, and connect Radio 2 under **Radio · Serial (USB)**.
3. **Enable Home Station Gateway:**
* Open **Radio · Home Station Gateway**.
* Under **Radio Path**, select **Serial (USB)**.
* Toggle the gateway button on (from **Gateway off**) and confirm **Status** shows `connected` and **Ready** shows your radio is connected.
* Keep the browser tab open on your home PC (the app keeps the screen awake while the gateway is active).



#### 2. Phone Setup (Remote Field Client)

1. Open `[https://otago.dl7pj.workers.dev/](https://otago.dl7pj.workers.dev/)` on your phone over cellular data or Wi-Fi.
2. Enter the **same callsign** as your home gateway station (using a distinct SSID if needed so it does not collide with another active APRS-IS session).
3. Operate normally from your phone: when you send OTA commands or GPS position beacons from your phone, OTAgo relays them over the internet to your home PC, which transmits them over RF through Radio 2 to your local iGate (Radio 1).

---

### Configuring OTAgo & Getting On the Air

Once your hardware is connected, finish configuring OTAgo and start operating:

1. **Station & APRS-IS Settings:**
* Enter your **callsign** and **APRS-IS passcode** (use the built-in passcode generator link if needed).
* Choose an **SSID** that is not currently logged in to APRS-IS on another device.
* Leave the beacon comment blank (defaults to `aprsota.org` / `OTAgo`) or add a short note. OTAgo automatically appends a timestamp so consecutive beacons are not dropped as duplicates.


2. **Hosting an Activation (`HOST`):**
* Send `HOST` (with an optional short description) to the **OTA** bot via RF.
* Send a **GPS position beacon** from the same callsign and SSID within **5 minutes** (it must arrive over RF) to put your op on the air.
* Confirm incoming chasers with `QSL <callsign>`, check waiting stations with `CHASERS`, extend your window if needed with `STAY`, and close out with `DONE` (or `QRT`).


3. **Chasing an Activation (`CHASE`):**
* Check the **On Air** list (or query `CQ` from the OTA bot) to see active stations.
* Send `CHASE <callsign>` to the **OTA** bot and transmit a **position report** within 5 minutes so your contact and distance/grid can be logged.
