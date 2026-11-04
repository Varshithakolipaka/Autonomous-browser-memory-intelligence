# Autonomous Browser Memory Intelligence (ABMI)

**Context-Aware Browser Resource Management Using Reinforcement Learning**

ABMI is a Chrome Manifest V3 extension prototype that monitors open browser tabs, browsing activity, and system-level RAM/CPU signals. It estimates tab importance and resource pressure, then supports user-controlled tab management through **KEEP, WARN, SUSPEND, CLOSE, and RESTORE** actions.

The goal is to make tab management more context-aware than a fixed inactivity timer, while keeping the user in control of destructive actions.

## Key Features

- Periodic browser monitoring using Chrome extension APIs.
- Collection of tab metadata, activity signals, browsing-history statistics, and system-level memory/CPU information.
- Feature engineering for activity, history importance, tab importance, and resource pressure.
- A 27-state tabular Q-learning design with an epsilon-greedy action-selection mechanism.
- Warning interface that lets the user decide what happens to a candidate tab.
- Safety checks for active, pinned, and audible tabs.
- Logical suspension: save a tab's URL and session metadata, remove the live tab, and restore it only when the user requests it.
- Local persistence using `chrome.storage.local`.

## Project Modules

The implementation is organized around three main project modules:

1. **Data Collection** — obtains tab information, browsing history, and system resource readings.
2. **RL State Representation** — converts collected information into activity/importance/resource-pressure features and a browser-level state.
3. **Action Manager** — applies safety checks, presents warnings, executes the user's selected action, and supports restoration.

Supporting JavaScript files separate feature engineering, Q-learning, warning UI, and background monitoring.

## Technology Stack

- JavaScript
- HTML and CSS
- Chrome Extensions API — Manifest V3
- Chrome APIs: `chrome.tabs`, `chrome.history`, `chrome.storage`, `chrome.alarms`, `chrome.system.memory`, and `chrome.system.cpu`
- Tabular Q-learning

## Repository Structure

```text
ABMI/
├── manifest.json
├── background.js
├── feature_engineering.js
├── rlstate.js
├── qlearning.js
├── action_manager.js
├── popup.html
├── popup.js
├── warning.html
├── warning.js

```

Keep the filenames consistent with the actual files in your repository. The `screenshots/`, `LICENSE`, and `.gitignore` entries are recommended repository additions.

## Installation and Usage

1. Download or clone this repository.
2. Open Google Chrome and navigate to `chrome://extensions`.
3. Enable **Developer mode**.
4. Select **Load unpacked**.
5. Choose the project folder containing `manifest.json`.
6. Open the ABMI extension from the Chrome toolbar.
7. Keep the extension enabled while testing tab monitoring and the warning workflow.

After changing extension code, return to `chrome://extensions` and reload ABMI.

## How the Actions Work

| Action | Behaviour |
|---|---|
| **KEEP** | Leaves the tab open. |
| **WARN** | Displays a warning and lets the user decide. |
| **SUSPEND** | Saves the tab URL and relevant metadata, then removes the live tab. |
| **CLOSE** | Removes the selected tab after the applicable safety checks. |
| **RESTORE** | Recreates a logically suspended tab from its saved information. |

**Important:** ABMI's logical SUSPEND operation is different from Chrome's native tab discard. A logically suspended tab is removed from the tab strip and can return through ABMI's RESTORE workflow.

## Reinforcement Learning Design

The Q-learning implementation defines:

- **27 states**
- **4 Q-table action columns:** KEEP, WARN, SUSPEND, CLOSE
- Learning rate (`alpha`): **0.20**
- Discount factor (`gamma`): **0.80**
- Exploration rate (`epsilon`): **0.10**

The policy's automatic recommendation path is limited to KEEP, WARN, and SUSPEND; CLOSE remains a user-confirmed action. Active, pinned, and audible tabs are protected by the safety logic.

The Q-learning code includes a Q-value update using the reward and next state. The implementation also contains simulated-reward logic and an observed-reward path based on measured system-level resource impact. When presenting results, distinguish simulated reward/testing from rewards obtained during actual user interactions.

## Resource Measurement and Limitations

- Chrome's stable extension APIs do **not** reliably expose exact RAM or CPU consumption for each individual tab.
- ABMI therefore records system-level RAM/CPU readings; these values must not be described as per-tab measurements.
- System-level readings can be affected by other browser tabs and applications running on the computer.
- A change in system RAM/CPU after an action is an observed system-level change, not proof that the selected tab alone caused the change.
- Warning eligibility and timing depend on the monitoring logic and configured conditions.
- Results should be evaluated across repeated trials and compared with an inactivity-only baseline before making performance claims.

## Privacy

The project is designed to store its browsing and learning data locally using `chrome.storage.local`. The supplied extension manifest does not declare network permissions. Review any future code changes before making privacy claims about later versions.

## Testing Suggestions

Test the extension with a controlled set of tabs and record:

1. Whether tab and system information is collected.
2. Whether active, pinned, and audible tabs are protected.
3. Whether warning eligibility behaves as expected.
4. Whether KEEP, SUSPEND, and CLOSE perform the intended operation.
5. Whether RESTORE recreates a suspended tab.
6. Whether system-level RAM/CPU readings are recorded before and after actions.
7. Whether the Q-table is persisted and updated through the implemented feedback path.

Add genuine screenshots, test cases, and measured results to the repository. Do not publish illustrative values as experimental results.

## Future Enhancements

- More robust evaluation against inactivity-only tab management.
- Larger-scale testing across different devices and browsing workloads.
- Improved resource attribution where supported by reliable platform APIs.
- Better personalization of tab-importance features and reward design.
- More detailed evaluation of Q-learning convergence and user experience.

## Team

Developed as a student mini-project by a team from the Department of Artificial Intelligence and Data Science, Chaitanya Bharathi Institute of Technology, Hyderabad.




