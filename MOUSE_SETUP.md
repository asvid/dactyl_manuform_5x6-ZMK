# Mouse Support Configuration for Dactyl Manuform 5x6

This configuration adds mouse support with dual joysticks to your Dactyl Manuform 5x6 keyboard:

- **Right board**: Joystick controls mouse cursor movement (X/Y axes)
- **Left board**: Joystick controls scroll wheel functionality (horizontal and vertical scrolling)

## Hardware Requirements

### Joystick Connections
Each joystick requires 5 pins total:
- VCC (3.3V)
- GND
- X-axis analog output
- Y-axis analog output  
- Switch/button (optional, can be wired to a regular switch pin)

### Pin Assignments
Both boards use the same ADC pin assignments:
- **X-axis**: P0.02 (ADC channel 0)
- **Y-axis**: P0.29 (ADC channel 5)

**Important**: Make sure your Nice!Nano or compatible board supports ADC on these pins. These pins are confirmed to work with SuperMini NRF52840 boards.

## Configuration Details

### West.yml Changes
- Added the `zmk-analog-input-driver` module from badjeff's repository
- This provides the analog input driver needed for joystick support

### Config File Changes
Enabled in `dactyl_manuform_5x6.conf`:
```
CONFIG_ZMK_POINTING=y                    # Enable mouse support
CONFIG_ZMK_POINTING_SMOOTH_SCROLLING=y   # Enable smooth scrolling
CONFIG_ADC=y                             # Enable ADC subsystem
CONFIG_ADC_ASYNC=y                       # Enable async ADC operations
CONFIG_ANALOG_INPUT=y                    # Enable analog input driver
CONFIG_INPUT=y                           # Enable input subsystem
```

### Right Board (Mouse Cursor)
- Joystick movements translate directly to mouse cursor movements
- Configured with:
  - Mid-point: 1650mV (adjust based on your joystick's center voltage)
  - Deadzone: 50mV (prevents drift when joystick is centered)
  - Scale divisor: 10 (adjust for sensitivity)
  - Y-axis inverted for natural movement

### Left Board (Scrolling)
- Joystick movements are mapped to scroll wheel events
- Uses the built-in `zip_xy_to_scroll_mapper` processor
- Configured with:
  - Same voltage settings as right board
  - Scale divisor: 20 (slower scrolling than cursor movement)
  - Y-axis inverted for natural scrolling

### Keymap Changes
- Added mouse click buttons to the raise layer:
  - Left click, middle click, right click on the right side
  - Access via the raise layer (hold the raise key)

## Power Consumption Warning

**Important**: The analog input driver uses polling mode which has relatively high power consumption. This configuration is **not recommended for wireless builds** if battery life is a concern. For wireless usage, consider:

1. Using lower sampling rates (reduce `sampling-hz`)
2. Implementing sleep/wake functionality for the joysticks
3. Using the joysticks only when needed

## Calibration

You may need to adjust these values based on your specific joysticks:

- `mv-mid`: The center/rest voltage of your joystick (typically 1.65V for 3.3V supply)
- `mv-min-max`: Range from center point (how far the voltage swings)
- `mv-deadzone`: Dead zone to prevent drift (start with 50mV and adjust)
- `scale-divisor`: Higher values = slower movement/scrolling

## Testing

1. Build and flash the firmware to both halves
2. Test mouse cursor movement with the right joystick
3. Test scrolling with the left joystick  
4. Test mouse click buttons in the raise layer
5. Adjust sensitivity and deadzone values as needed

## Troubleshooting

- If the mouse moves in wrong directions, try adding/removing `invert;` from the axis configuration
- If movement is too fast/slow, adjust the `scale-divisor` values
- If there's cursor drift, increase the `mv-deadzone` value
- If the joystick doesn't respond, verify ADC pin assignments and voltage levels