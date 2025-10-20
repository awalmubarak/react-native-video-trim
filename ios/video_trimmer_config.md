# Video Trimmer Configuration for iOS

This document describes the video trimmer color configuration implementation in the iOS native module.

## Configuration Properties

### Trimmer Color (`trimmerColor`)
- **Type**: `UIColor`
- **Default**: `UIColor(red: 0.945, green: 0.824, blue: 0.278, alpha: 1.0)` (equivalent to `#f1d247`)
- **Description**: Controls the color of the trimmer bar border (top, bottom, left, and right edges)
- **Usage**: Passed from React Native via `processColor()` and converted using `RCTConvert.uiColor()`

### Handle Icon Color (`handleIconColor`)
- **Type**: `UIColor`
- **Default**: `UIColor.black`
- **Description**: Controls the color of the chevron icons on the left and right handle grabbers
- **Usage**: Passed from React Native via `processColor()` and converted using `RCTConvert.uiColor()`

## Implementation Details

### Default Color Initialization

The default colors are set in three locations to ensure consistency:

1. **VideoTrimmerViewController.swift** - Property declarations (lines 52-54)
   ```swift
   private var trimmerColor: UIColor = UIColor(red: 0.945, green: 0.824, blue: 0.278, alpha: 1.0)
   private var handleIconColor: UIColor = UIColor.black
   ```

2. **VideoTrimmerViewController.swift** - Configure method fallback (lines 489-494)
   ```swift
   if let trimmerColorValue = config["trimmerColor"] as? Double {
       trimmerColor = RCTConvert.uiColor(trimmerColorValue) ?? UIColor(red: 0.945, green: 0.824, blue: 0.278, alpha: 1.0)
   }
   if let handleIconColorValue = config["handleIconColor"] as? Double {
       handleIconColor = RCTConvert.uiColor(handleIconColorValue) ?? UIColor.black
   }
   ```

3. **VideoTrimmerThumb.swift** - Initial setup (lines 168-179)
   ```swift
   private func updateColor() {
       let color = UIColor(red: 0.945, green: 0.824, blue: 0.278, alpha: 1.0)
       leadingView.backgroundColor = color
       trailingView.backgroundColor = color
       topView.backgroundColor = color
       bottomView.backgroundColor = color
       
       leadingChevronImageView.tintColor = .black
       trailingChevronView.tintColor = .black
   }
   ```

### Color Application Flow

1. User calls `showEditor()` with configuration from JavaScript
2. `VideoTrimmerViewController` is instantiated
3. `configure(config:)` method is called, extracting color values from config
4. When asset loads, `setupVideoTrimmer()` is called
5. `applyTrimmerColors()` is invoked, which calls:
   - `trimmer.thumbView.updateTrimmerColor(trimmerColor)`
   - `trimmer.thumbView.updateHandleIconColor(handleIconColor)`

### Public API Methods

**VideoTrimmerThumb.swift** provides public methods for updating colors:

```swift
func updateTrimmerColor(_ color: UIColor)
func updateHandleIconColor(_ color: UIColor)
```

These methods allow dynamic color updates after the trimmer view has been initialized.

## Consistency with Other Platforms

The default color `#f1d247` matches:
- TypeScript configuration in `src/index.tsx` (line 68)
- Android resource color in `android/src/main/res/values/colors.xml` (line 17)

This ensures consistent default appearance across all platforms.

## Usage Example

From JavaScript/TypeScript:

```typescript
import { showEditor } from 'react-native-video-trim';

showEditor('file://path/to/video.mp4', {
  trimmerColor: '#007AFF',      // iOS blue
  handleIconColor: '#FFFFFF',    // White chevrons
  // ... other config options
}, (event) => {
  // Handle events
});
```

The colors are processed through React Native's `processColor()` utility which converts color strings to numeric values that can be decoded by `RCTConvert.uiColor()` on the native side.
