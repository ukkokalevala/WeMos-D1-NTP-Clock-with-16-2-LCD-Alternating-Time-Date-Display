A WiFi-connected digital clock built on the WeMos D1 Mini (ESP8266) that fetches accurate time from the internet via NTP (Network Time Protocol) and displays it on a standard HD44780 16×2 character LCD. To protect the LCD from image persistence (burn-in), the display alternates every 5 seconds between a Time page and a Date page, so no single static pattern stays on screen for long periods.

Features
Internet-synced time — no RTC module needed; time is pulled from pool.ntp.org on boot and refreshed every 60 seconds.

South African Standard Time (UTC+2) — automatically applied via the NTP offset.

Alternating display — toggles between Time and Date every 5 seconds to prevent LCD image retention.

Non-blocking toggle logic — uses millis() instead of delay(), so NTP updates keep running smoothly in the background.

Startup feedback — the LCD shows "Connecting..." during WiFi setup and "Syncing NTP..." while the first time sync completes.

Parallel HD44780 in 4-bit mode — only 4 data lines + RS + EN needed, freeing up GPIO pins.

Hardware
Component	Details
Microcontroller	WeMos D1 Mini (ESP8266)
Display	HD44780 16×2 character LCD (parallel)
Contrast	10kΩ potentiometer on V0
Backlight	220Ω resistor on anode (Pin 15)
Wiring
LCD Pin	Function	WeMos D1 Pin	GPIO
1	VSS	GND	—
2	VDD	5V	—
3	V0	10k pot wiper	—
4	RS	D4	2
5	RW	GND	—
6	EN	D3	0
11	D4	D2	4
12	D5	D1	5
13	D6	D6	12
14	D7	D5	15
15	A (backlight +)	5V via 220Ω	—
16	K (backlight −)	GND	—
Software
Libraries required:

LiquidCrystal (built-in with ESP8266 core)

NTPClient (by Fabrice Weinberg)

WiFiUdp (built-in)

ESP8266WiFi (built-in with ESP8266 board package)

How it works:

On boot, the LCD initialises and shows a connection message.

The ESP8266 connects to the configured WiFi network.

The NTP client syncs with pool.ntp.org and applies the +2h South African offset.

In the main loop, a millis() timer flips a boolean every 5 seconds, switching between:

Time page: Time: on line 1, HH:MM:SS on line 2

Date page: Date: on line 1, YYYY-MM-DD on line 2

The NTP client silently refreshes time every 60 seconds in the background.

Configuration
Edit these two lines at the top of the sketch with your WiFi credentials:

const char* ssid     = "YOUR_WIFI_SSID";
const char* password = "YOUR_WIFI_PASSWORD";
To change timezone, adjust the offset in seconds (in the NTPClient constructor and setTimeOffset):

Timezone	Offset (seconds)
UTC	0
SAST (UTC+2)	7200
EST (UTC−5)	−18000
CET (UTC+1)	3600
To change how often the display alternates, edit:

const long interval = 5000;   // milliseconds
Why Alternate the Display?
Character LCDs using the HD44780 controller are far more resistant to burn-in than OLEDs, but prolonged display of identical static patterns (like a colon separator or fixed digits) can still cause image persistence over months of continuous use. By swapping between two different layouts, the pixel pattern refreshes regularly, which keeps the display healthy long-term.

