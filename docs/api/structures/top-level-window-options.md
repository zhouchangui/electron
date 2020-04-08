# TopLevelWindowOptions Object

* `width` Integer (optional) - Window's width in pixels. Default is `800`.
* `height` Integer (optional) - Window's height in pixels. Default is `600`.
* `x` Integer (optional) - (**required** if y is used) Window's left offset from
  screen. Default is to center the window.
* `y` Integer (optional) - (**required** if x is used) Window's top offset from
  screen. Default is to center the window.
* `useContentSize` Boolean (optional) - The `width` and `height` would be used
  as web page's size, which means the actual window's size will include window
  frame's size and be slightly larger. Default is `false`.
* `center` Boolean (optional) - Show window in the center of the screen.
* `minWidth` Integer (optional) - Window's minimum width. Default is `0`.
* `minHeight` Integer (optional) - Window's minimum height. Default is `0`.
* `maxWidth` Integer (optional) - Window's maximum width. Default is no limit.
* `maxHeight` Integer (optional) - Window's maximum height. Default is no limit.
* `resizable` Boolean (optional) - Whether window is resizable. Default is
  `true`.
* `movable` Boolean (optional) - Whether window is movable. This is not
  implemented on Linux. Default is `true`.
* `minimizable` Boolean (optional) - Whether window is minimizable. This is not
  implemented on Linux. Default is `true`.
* `maximizable` Boolean (optional) - Whether window is maximizable. This is not
  implemented on Linux. Default is `true`.
* `closable` Boolean (optional) - Whether window is closable. This is not
  implemented on Linux. Default is `true`.
* `focusable` Boolean (optional) - Whether the window can be focused. Default is
  `true`. On Windows setting `focusable: false` also implies setting
  `skipTaskbar: true`. On Linux setting `focusable: false` makes the window
  stop interacting with wm, so the window will always stay on top in all
  workspaces.
* `alwaysOnTop` Boolean (optional) - Whether the window should always stay on
  top of other windows. Default is `false`.
* `fullscreen` Boolean (optional) - Whether the window should show in
  fullscreen. When explicitly set to `false` the fullscreen button will be
  hidden or disabled on macOS. Default is `false`.
* `fullscreenable` Boolean (optional) - Whether the window can be put into
  fullscreen mode. On macOS, also whether the maximize/zoom button should toggle
  full screen mode or maximize window. Default is `true`.
* `simpleFullscreen` Boolean (optional) - Use pre-Lion fullscreen on macOS.
  Default is `false`.
* `skipTaskbar` Boolean (optional) - Whether to show the window in taskbar.
  Default is `false`.
* `kiosk` Boolean (optional) - Whether the window is in kiosk mode. Default is
  `false`.
* `title` String (optional) - Default window title. Default is `"Electron"`. If
  the HTML tag `<title>` is defined in the HTML file loaded by `loadURL()`, this
  property will be ignored.
* `icon` ([NativeImage](../native-image.md) | String) (optional) - The window
  icon. On Windows it is recommended to use `ICO` icons to get best visual
  effects, you can also leave it undefined so the executable's icon will be
  used.
* `show` Boolean (optional) - Whether window should be shown when created.
  Default is `true`.
* `frame` Boolean (optional) - Specify `false` to create a
  [Frameless Window](../frameless-window.md). Default is `true`.
* `parent` BrowserWindow (optional) - Specify parent window. Default is `null`.
* `modal` Boolean (optional) - Whether this is a modal window. This only works
  when the window is a child window. Default is `false`.
* `acceptFirstMouse` Boolean (optional) - Whether the web view accepts a single
  mouse-down event that simultaneously activates the window. Default is
  `false`.
* `disableAutoHideCursor` Boolean (optional) - Whether to hide cursor when
  typing. Default is `false`.
* `autoHideMenuBar` Boolean (optional) - Auto hide the menu bar unless the `Alt`
  key is pressed. Default is `false`.
* `enableLargerThanScreen` Boolean (optional) - Enable the window to be resized
  larger than screen. Only relevant for macOS, as other OSes allow
  larger-than-screen windows by default. Default is `false`.
* `backgroundColor` String (optional) - Window's background color as a
  hexadecimal value, like `#66CD00` or `#FFF` or `#80FFFFFF` (alpha in #AARRGGBB
  format is supported if `transparent` is set to `true`). Default is `#FFF`
  (white).
* `hasShadow` Boolean (optional) - Whether window should have a shadow. Default
  is `true`.
* `opacity` Number (optional) - Set the initial opacity of the window, between
  0.0 (fully transparent) and 1.0 (fully opaque). This is only implemented on
  Windows and macOS.
* `darkTheme` Boolean (optional) - Forces using dark theme for the window, only
  works on some GTK desktop environments. Default is
  [`nativeTheme.shouldUseDarkColors`](../native-theme.md).
* `transparent` Boolean (optional) - Makes the window
  [transparent](../frameless-window.md#transparent-window). Default is `false`.
  On Windows, does not work unless the window is frameless.
* `type` String (optional) - The type of window, default is normal window. See
  more about this in [ToplevelWindow][../top-level-window.md].
* `titleBarStyle` String (optional) - The style of window title bar.
  Default is `default`. Possible values are:
  * `default` - Results in the standard gray opaque Mac title
    bar.
  * `hidden` - Results in a hidden title bar and a full size content window, yet
    the title bar still has the standard window controls ("traffic lights") in
    the top left.
  * `hiddenInset` - Results in a hidden title bar with an alternative look
    where the traffic light buttons are slightly more inset from the window
    edge.
  * `customButtonsOnHover` Boolean (optional) - Draw custom close, and minimize
    buttons on macOS frameless windows. These buttons will not display unless
    hovered over in the top left of the window. These custom buttons prevent
    issues with mouse events that occur with the standard window toolbar
    buttons. **Note:** This option is currently experimental.
* `trafficLightPosition` [Point](point.md) (optional) - Set a custom position
  for the traffic light buttons. Can only be used with `titleBarStyle` set to
  `hidden`.
* `fullscreenWindowTitle` Boolean (optional) - Shows the title in the title bar
  in full screen mode on macOS for all `titleBarStyle` options. Default is
  `false`.
* `thickFrame` Boolean (optional) - Use `WS_THICKFRAME` style for frameless
  windows on Windows, which adds standard window frame. Setting it to `false`
  will remove window shadow and window animations. Default is `true`.
* `vibrancy` String (optional) - Add a type of vibrancy effect to the window,
  only on macOS. Can be `appearance-based`, `light`, `dark`, `titlebar`,
  `selection`, `menu`, `popover`, `sidebar`, `medium-light`, `ultra-dark`,
  `header`, `sheet`, `window`, `hud`, `fullscreen-ui`, `tooltip`, `content`,
  `under-window`, or `under-page`.  Please note that using `frame: false` in
  combination with a vibrancy value requires that you use a non-default
  `titleBarStyle` as well. Also note that `appearance-based`, `light`, `dark`,
  `medium-light`, and `ultra-dark` have been deprecated and will be removed in
  an upcoming version of macOS.
* `zoomToPageWidth` Boolean (optional) - Controls the behavior on macOS when
  option-clicking the green stoplight button on the toolbar or by clicking the
  Window > Zoom menu item. If `true`, the window will grow to the preferred
  width of the web page when zoomed, `false` will cause it to zoom to the width
  of the screen. This will also affect the behavior when calling `maximize()`
  directly. Default is `false`.
* `tabbingIdentifier` String (optional) - Tab group name, allows opening the
  window as a native tab on macOS 10.12+. Windows with the same tabbing
  identifier will be grouped together. This also adds a native new tab button to
  your window's tab bar and allows your `app` and window to receive the
  `new-window-for-tab` event.
