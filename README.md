# Toronto Scavenger Hunt

An iOS app that turns downtown Toronto into a photo scavenger hunt. Players get ten riddle-style clues, each pointing to a real item at a real Toronto business or landmark, and prove they found it by snapping a photo in the app. The more they find, the better the reward.

Built with SwiftUI as a hands-on exercise in state management, custom view modifiers, and bridging UIKit into SwiftUI.

## Features

- **Ten hand-written clues** tied to real Toronto locations — Tim Hortons, St. Lawrence Market, Ripley's Aquarium, the CN Tower gift shop, Casa Loma, Courage My Love in Kensington Market, Balzac's in the Distillery District, and more.
- **Photo proof** — the camera opens right from the clue screen, and capturing a photo marks the item found.
- **Live progress** — a running `x/10 Found` counter in the navigation bar, with found cards animating into a green highlighted state.
- **Tiered rewards** — the payoff scales with how much of the city you cover:

  | Items found | Reward |
  |---|---|
  | 10 | Entry into the $5,000 grand prize draw |
  | 7–9 | 20% discount code (`HUNT20`) |
  | 5–6 | 10% discount code (`HUNT10`) |
  | 0–4 | No reward yet |

- **Results screen** with the earned code and a photo grid of everything collected, plus a **Play Again** reset.

## Screens

| Screen | What it does |
|---|---|
| `HomeView` | Landing screen with the hero graphic and a Start / Continue Hunt button |
| `ItemListView` | Two-column grid of all ten items, progress counter, and Submit button |
| `ItemDetailView` | Clue, location details, photo preview, and the camera button |
| `ResultsView` | Reward tier, discount code, photo summary grid, and Play Again |

## Architecture

The app uses a single observable state object injected through the SwiftUI environment, so every screen reads from and writes to the same source of truth.

```
iOSApp2/
├── iOSApp2/
│   ├── iOSApp2App.swift        // App entry point; creates and injects HuntManager
│   └── Assets.xcassets
├── Models/
│   ├── ScavengerItem.swift     // Item model + the 10 Toronto locations + array helpers
│   └── RewardTier.swift        // Reward tiers, messages, and discount codes
├── Managers/
│   └── HuntManager.swift       // @Observable hunt state: items, progress, reset
├── Views/
│   ├── HomeView.swift
│   ├── ItemListView.swift
│   ├── ItemDetailView.swift
│   ├── ItemCardView.swift
│   ├── ResultsView.swift
│   └── ImagePicker.swift       // UIViewControllerRepresentable wrapper for the camera
└── Modifiers/
    └── HuntCardModifier.swift  // Custom ViewModifier for found/unfound card styling
```

**Key pieces**

- `HuntManager` is marked `@Observable` and passed down with `.environment()`. It owns the item list and exposes `markFound(id:photo:)`, `submitHunt()`, and `resetHunt()`.
- Reward logic lives in an `Array where Element == ScavengerItem` extension, so `foundCount` and `rewardTier` are derived rather than stored — there is no way for progress and rewards to fall out of sync.
- `ImagePicker` wraps `UIImagePickerController` via `UIViewControllerRepresentable`. It uses the camera on a real device and falls back to the photo library on the simulator, where no camera exists.
- `HuntCardModifier` is a custom `ViewModifier` exposed through a `.huntCardStyle(isFound:)` extension, keeping the card styling and its spring animation in one place.

## Requirements

- Xcode 26 or later
- iOS 26.4+
- Swift 5
- A physical device to use the camera (the simulator falls back to the photo library)

## Getting started

```bash
git clone https://github.com/zubiatahseen1/iOSApp2.git
cd iOSApp2
open iOSApp2.xcodeproj
```

Select a simulator or connected device and press **Run** (⌘R).

## Known limitations

Hunt progress lives in memory only — closing the app clears found items and photos.

## Roadmap

- Persist progress with SwiftData so a hunt survives app restarts
- Hunt timer (`HuntManager.timeElapsed` is already in place, waiting to be wired up)
- MapKit view showing all ten locations
- Share results to social media

## Author

Zubia Tahseen — [@zubiatahseen1](https://github.com/zubiatahseen1)
