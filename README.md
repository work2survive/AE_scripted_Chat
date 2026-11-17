# AE Scripted Chat Generator (`Scripted_Chat_Generator.jsx`)

An Adobe After Effects ScriptUI panel (`.jsx`) that generates **fully animated, multi-message chat conversations**—similar to [FakeDetail](https://fakedetail.com/), but with procedural vector shapes, real-time expressions, automatic timestamps, independent real vs. fake delays, and automatic vertical overflow scrolling in After Effects.

Built on top of the single-bubble engine from **[AE_message_bubble](https://github.com/work2survive/AE_message_bubble)**.

---

## ✨ Key Features

1. **Top Conversation Pull-Down (`/conversation/*.json`)**
   - Automatically scans the [`conversation/`](./conversation) subfolder for `.json` conversation scripts, as well as any `.json` footage items imported directly into your After Effects Project panel.
   - Includes quick toolbar buttons to **Rescan (`↻`)**, **Open the `conversation/` folder (`📂`)**, **Import the selected `.JSON` into the AE Project (`📥`)**, and **Save UI Header adjustments back to `.JSON` (`💾`)**.

2. **Header + Messages `.JSON` Architecture (After Effects & Web UI Ready)**
   - Every conversation `.json` has a **`"header"`** block storing all shared conversation settings (platform, theme, typography, timestamps, default delays, and chat screen appearance) and a **`"messages"`** array storing the dialog items.
   - Directly importable as native `.json` footage in After Effects and structured for a future Web UI conversation builder.
   - Extensible for future **RCS rich dialog types** (`"text"`, `"image"`, `"video"`, `"carousel"`, `"buttons"`).

3. **Automatic Timestamps & Real vs. Fake Delays**
   - **Initial Timestamp & Format (`24h` / `12h`)**: Set the starting clock time in the header or UI (e.g., `"14:20"` or `"11:45"`), and toggle between **`24h`** (`14:32`) and **`12h`** (`2:32 PM`). All subsequent sent and received bubble timestamps are calculated automatically.
   - **Real Delay (AE Timeline Seconds)**: Controls the actual time in seconds between bubbles appearing in the After Effects timeline (`realDelayBetweenBubbles`, e.g., `1.0`s). Any individual bubble can override this via `"realDelay"` (for example, a received bubble `"Hey, are you at home?"` with `"realDelay": 2.0` will wait 2 seconds after the previous bubble, while the rest use the 1 second header default).
   - **Fake Delay (Displayed Clock Time)**: Controls the clock increment between bubble timestamps (`fakeDelayBetweenBubbles`, e.g., `"+00:01"`). Any individual bubble can specify `"timestamp"`:
     - **With a `+` symbol (e.g., `"+00:05"` or `"+14:32"`)**: Adds that `HH:MM` duration to the previous bubble's timestamp.
     - **Without a `+` symbol (e.g., `"14:32"`)**: Directly uses that exact clock time (`14:32` in 24h or `2:32 PM` in 12h), and subsequent relative bubbles continue from that time.

4. **Automatic Chat Scrolling on Vertical Overflow**
   - Bubbles are stacked top-to-bottom inside the chat screen's inner area (`screenHeight - 2 * screenInnerPadding`).
   - Once a newly appearing bubble pushes the conversation height past the vertical inner bounds of the chat screen, `Chat_Scroll_Controller` smoothly scrolls the entire chat upward with a cubic ease-out curve.

5. **Dedicated `Screen` Tab**
   - Configure **Chat Screen Visibility** (`Show Chat Screen`), **Opacity** (`0–100%`), **Screen Size** (`Screen Width` & `Screen Height`), **Inner Padding**, **Corner Roundness**, **Background Color**, **Auto-Scroll on Overflow**, and **Top/Bottom Overflow Clipping**—both before generation in the UI and live afterward via `Chat_Master_Controller` Effect Controls.

6. **Powered by `AE_message_bubble`**
   - Preserves single-path Bézier bubble + tail geometry (`iOS`, `WhatsApp`, `Android RCS`, `Instagram`, `Messenger`, `Telegram`, `Discord`), smart consecutive tail grouping, Unicode emoji font range assignment, animated RCS circled checkmarks (`○✓` → `○✓ ○✓`) and standard checkmarks (`✓` → `✓✓`), and iOS 18 per-character text animations (`Nod`, `Shake`, `Blink`).

---

## 📁 Repository Structure

```text
AE_scripted_Chat/
├── Scripted_Chat_Generator.jsx          # Main After Effects ScriptUI Panel (.JSX)
├── conversation/                        # Scripted Conversation .JSON files
│   ├── conversation_schema.json         # JSON Schema (Draft 2020-12) for conversations & RCS items
│   ├── rcs_home_invitation.json         # Android RCS 24h demo ("Hey, are you at home?" w/ realDelay: 2.0)
│   ├── ios_lunch_plans.json             # iOS Message 12h demo
│   └── whatsapp_project_update.json     # WhatsApp Dark Mode 24h demo
├── message bubble presets/
│   └── presets.json                     # Platform color, radius, font, and screenBg presets
├── LICENSE
└── README.md
```

---

## 📋 Conversation `.JSON` Structure

Below is an example conversation file stored in [`conversation/rcs_home_invitation.json`](./conversation/rcs_home_invitation.json):

```json
{
  "$schema": "./conversation_schema.json",
  "version": "1.0",
  "header": {
    "title": "RCS Home Check-in (Demo)",
    "platform": "Android RCS",
    "theme": "light",
    "initialTimestamp": "14:20",
    "timeFormat": "24h",
    "showTimestamps": true,
    "realDelayBetweenBubbles": 1.0,
    "fakeDelayBetweenBubbles": "+00:01",
    "typography": {
      "fontFamily": "Roboto (Google / Android)",
      "emojiFont": "Auto (Preset Default)",
      "textWrap": "pretty",
      "bubbleWidth": 0,
      "maxBubbleWidth": 320,
      "textSize": 20,
      "leadingPercent": 100,
      "bubbleGap": 12
    },
    "appearance": {
      "showTail": true,
      "smartGroupTails": false,
      "statusIconStyle": "rcs",
      "colors": {
        "sentBubble": "#0B57D0",
        "sentText": "#FFFFFF",
        "sentTime": "#C4D7F8",
        "receivedBubble": "#F1F3F4",
        "receivedText": "#1F1F1F",
        "receivedTime": "#70757A"
      }
    },
    "screen": {
      "visible": true,
      "opacity": 100,
      "width": 460,
      "height": 820,
      "innerPadding": 24,
      "backgroundColor": "#FFFFFF",
      "cornerRadius": 36,
      "autoScroll": true,
      "clipOverflow": true,
      "scrollDurationFrames": 8
    }
  },
  "messages": [
    {
      "id": "msg_01",
      "type": "sent",
      "dialogType": "text",
      "text": "Just wrapped up the edit at the studio! Heading back now 🎬✨",
      "realDelay": 0.2
    },
    {
      "id": "msg_02",
      "type": "received",
      "dialogType": "text",
      "text": "Hey, are you at home?",
      "realDelay": 2.0,
      "timestamp": "+00:05"
    },
    {
      "id": "msg_03",
      "type": "sent",
      "dialogType": "text",
      "text": "Just unlocked the front door! Why, what's up? 🔑🏠"
    },
    {
      "id": "msg_04",
      "type": "received",
      "dialogType": "text",
      "text": "Look outside on the porch, I dropped off iced lattes and croissants ☕🥐",
      "timestamp": "14:32"
    }
  ]
}
```

### Timestamp & Delay Rules Summary

| Field | Location | Example | Behavior |
| :--- | :--- | :--- | :--- |
| `initialTimestamp` | `header` | `"14:20"` | Starting clock time of the conversation (`#1` bubble uses this unless overridden). |
| `timeFormat` | `header` | `"24h"` or `"12h"` | Formats all calculated timestamps as `14:32` (`24h`) or `2:32 PM` (`12h`). |
| `realDelayBetweenBubbles` | `header` | `1.0` | Default **real timeline delay** (in seconds) between bubbles appearing in After Effects. |
| `fakeDelayBetweenBubbles` | `header` | `"+00:01"` | Default **fake clock delay** (`+HH:MM`) added to the previous bubble's clock time when a message omits `"timestamp"`. |
| `realDelay` | `messages[i]` | `2.0` | Overrides the **real timeline delay** (in seconds) before this specific bubble appears. Subsequent bubbles revert to `header.realDelayBetweenBubbles`. |
| `timestamp` (with `+`) | `messages[i]` | `"+00:05"` or `"+14:32"` | **Relative fake delay**: adds `HH:MM` to the previous bubble's clock timestamp. |
| `timestamp` (without `+`) | `messages[i]` | `"14:32"` | **Direct timestamp**: sets this bubble's clock timestamp directly to `14:32` (or `2:32 PM` in 12h mode). Subsequent relative bubbles continue from `14:32`. |

## ⚠️ Note on Color Emojis in After Effects

> **Important:** Adobe After Effects **does not have native support for color emojis** (COLR/CPAL, SBIX, or CBDT/CBLC color font tables) in standard text layers yet.
> - While the script automatically detects Unicode emoji ranges and assigns your selected Emoji font (`Noto Color Emoji`, `Apple Color Emoji`, `Segoe UI Emoji`, etc.) so glyphs don't appear as missing boxes (`□`), After Effects renders them as **monochrome/outline glyphs** tinted with the bubble's text fill color.
> - Full-color emoji rendering is supported in the companion Web UI / simulator preview, or by compositing PNG/SVG emoji assets in After Effects.

---

## 🚀 Installation & Usage in After Effects

1. Copy [`Scripted_Chat_Generator.jsx`](./Scripted_Chat_Generator.jsx), the [`conversation/`](./conversation) folder, and the [`message bubble presets/`](./message%20bubble%20presets) folder into your After Effects `Scripts/ScriptUI Panels/` directory (or run via **File > Scripts > Run Script File...**).
2. In After Effects, enable **Edit > Preferences > Scripting & Expressions > Allow Scripts to Write Files and Access Network**.
3. Select a conversation from the **top pull-down** (`Conversation (.JSON in /conversation/)`).
4. Review or tweak settings across the 5 tabs (**`Chat & Timing`**, **`Appearance`**, **`Screen`**, **`Animation`**, **`Help`**) and click **Generate Scripted Chat**.
5. After generation, select the **`Chat_Master_Controller`** null layer in your composition to adjust screen visibility, opacity, width, height, inner padding, corner roundness, background color, and scroll offset non-destructively in real time.
