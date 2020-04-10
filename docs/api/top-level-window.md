# TopLevelWindow

> Create and control native windows.

Process: [Main](../glossary.md#main-process)

## Frameless window

To create a window without chrome, or a transparent window in arbitrary shape,
you can use the [Frameless Window](frameless-window.md) API.

## Parent and child windows

By using `parent` option, you can create child windows:

```javascript
const { TopLevelWindow } = require('electron')

let top = new TopLevelWindow()
let child = new TopLevelWindow({ parent: top })
child.show()
top.show()
```

The `child` window will always show on top of the `top` window.

## Modal windows

A modal window is a child window that disables parent window, to create a modal
window, you have to set both `parent` and `modal` options:

```javascript
let child = new TopLevelWindow({ parent: top, modal: true, show: false })
child.show()
```

## Platform notices

* On macOS modal windows will be displayed as sheets attached to the parent window.
* On macOS the child windows will keep the relative position to parent window
  when parent window moves, while on Windows and Linux child windows will not
  move.
* On Linux the type of modal windows will be changed to `dialog`.
* On Linux many desktop environments do not support hiding a modal window.

## Class: TopLevelWindow

> Create and control native windows.

Process: [Main](../glossary.md#main-process)

`TopLevelWindow` is an [EventEmitter][event-emitter].

### `new TopLevelWindow([options])` _Experimental_

* `options` [TopLevelWindowOptions](structures/top-level-window-options.md) (optional)

Creates a new `TopLevelWindow` with native properties as set by the `options`.

When setting minimum or maximum window size with `minWidth`/`maxWidth`/
`minHeight`/`maxHeight`, it only constrains the users. It won't prevent you from
passing a size that does not follow size constraints to `setBounds`/`setSize` or
to the constructor of `BrowserWindow`.

The possible values and behaviors of the `type` option are platform dependent.
Possible values are:

* On Linux, possible types are `desktop`, `dock`, `toolbar`, `splash`,
  `notification`.
* On macOS, possible types are `desktop`, `textured`.
  * The `textured` type adds metal gradient appearance
    (`NSTexturedBackgroundWindowMask`).
  * The `desktop` type places the window at the desktop background window level
    (`kCGDesktopWindowLevel - 1`). Note that desktop window will not receive
    focus, keyboard or mouse events, but you can use `globalShortcut` to receive
    input sparingly.
* On Windows, possible type is `toolbar`.

### Instance Events

Objects created with `new TopLevelWindow` emit the following events:

**Note:** Some events are only available on specific operating systems and are
labeled as such.

#### Event: 'close'

Returns:

* `event` Event

Emitted when the window is going to be closed. Calling `event.preventDefault()`
will cancel the close.

#### Event: 'closed'

Emitted when the window is closed. After you have received this event you should
remove the reference to the window and avoid using it any more.

#### Event: 'session-end' _Windows_

Emitted when window session is going to end due to force shutdown or machine restart
or session log off.

#### Event: 'blur'

Emitted when the window loses focus.

#### Event: 'focus'

Emitted when the window gains focus.

#### Event: 'show'

Emitted when the window is shown.

#### Event: 'hide'

Emitted when the window is hidden.

#### Event: 'maximize'

Emitted when window is maximized.

#### Event: 'unmaximize'

Emitted when the window exits from a maximized state.

#### Event: 'minimize'

Emitted when the window is minimized.

#### Event: 'restore'

Emitted when the window is restored from a minimized state.

#### Event: 'will-resize' _macOS_ _Windows_

Returns:

* `event` Event
* `newBounds` [Rectangle](structures/rectangle.md) - Size the window is being resized to.

Emitted before the window is resized. Calling `event.preventDefault()` will prevent the window from being resized.

Note that this is only emitted when the window is being resized manually. Resizing the window with `setBounds`/`setSize` will not emit this event.

#### Event: 'resize'

Emitted after the window has been resized.

#### Event: 'will-move' _macOS_ _Windows_

Returns:

* `event` Event
* `newBounds` [Rectangle](structures/rectangle.md) - Location the window is being moved to.

Emitted before the window is moved. On Windows, calling `event.preventDefault()` will prevent the window from being moved.

Note that this is only emitted when the window is being resized manually. Resizing the window with `setBounds`/`setSize` will not emit this event.

#### Event: 'move'

Emitted when the window is being moved to a new position.

__Note__: On macOS this event is an alias of `moved`.

#### Event: 'moved' _macOS_

Emitted once when the window is moved to a new position.

#### Event: 'enter-full-screen'

Emitted when the window enters a full-screen state.

#### Event: 'leave-full-screen'

Emitted when the window leaves a full-screen state.

#### Event: 'always-on-top-changed'

Returns:

* `event` Event
* `isAlwaysOnTop` Boolean

Emitted when the window is set or unset to show always on top of other windows.

#### Event: 'app-command' _Windows_ _Linux_

Returns:

* `event` Event
* `command` String

Emitted when an [App Command](https://msdn.microsoft.com/en-us/library/windows/desktop/ms646275(v=vs.85).aspx)
is invoked. These are typically related to keyboard media keys or browser
commands, as well as the "Back" button built into some mice on Windows.

Commands are lowercased, underscores are replaced with hyphens, and the
`APPCOMMAND_` prefix is stripped off.
e.g. `APPCOMMAND_BROWSER_BACKWARD` is emitted as `browser-backward`.

```javascript
const { BrowserWindow } = require('electron')
let win = new BrowserWindow()
win.on('app-command', (e, cmd) => {
  // Navigate the window back when the user hits their mouse back button
  if (cmd === 'browser-backward' && win.webContents.canGoBack()) {
    win.webContents.goBack()
  }
})
```

The following app commands are explicitly supported on Linux:

* `browser-backward`
* `browser-forward`

#### Event: 'scroll-touch-begin' _macOS_

Emitted when scroll wheel event phase has begun.

#### Event: 'scroll-touch-end' _macOS_

Emitted when scroll wheel event phase has ended.

#### Event: 'swipe' _macOS_

Returns:

* `event` Event
* `direction` String

Emitted on 3-finger swipe. Possible directions are `up`, `right`, `down`, `left`.

#### Event: 'rotate-gesture' _macOS_

Returns:

* `event` Event
* `rotation` Float

Emitted on trackpad rotation gesture. Continually emitted until rotation gesture is
ended. The `rotation` value on each emission is the angle in degrees rotated since
the last emission. The last emitted event upon a rotation gesture will always be of
value `0`. Counter-clockwise rotation values are positive, while clockwise ones are
negative.

#### Event: 'sheet-begin' _macOS_

Emitted when the window opens a sheet.

#### Event: 'sheet-end' _macOS_

Emitted when the window has closed a sheet.

#### Event: 'new-window-for-tab' _macOS_

Emitted when the native new tab button is clicked.

### Static Methods

The `TopLevelWindow` class has the following static methods:

#### `TopLevelWindow.getAllWindows()`

Returns `TopLevelWindow[]` - An array of all opened browser windows.

#### `TopLevelWindow.getFocusedWindow()`

Returns `TopLevelWindow | null` - The window that is focused in this
application, otherwise returns `null`.

#### `TopLevelWindow.fromId(id)`

* `id` Integer

Returns `BrowserWindow` - The window with the given `id`.

### Instance Properties

Objects created with `new BrowserWindow` have the following properties:

```javascript
const { TopLevelWindow } = require('electron')
// In this example `win` is our instance
let win = new TopLevelWindow({ width: 800, height: 600 })
```

#### `win.id` _Readonly_

A `Integer` property representing the unique ID of the window. Each ID is unique among all `BrowserWindow` instances of the entire Electron application.

#### `win.autoHideMenuBar`

A `Boolean` property that determines whether the window menu bar should hide itself automatically. Once set, the menu bar will only show when users press the single `Alt` key.

If the menu bar is already visible, setting this property to `true` won't
hide it immediately.

#### `win.simpleFullScreen`

A `Boolean` property that determines whether the window is in simple (pre-Lion) fullscreen mode.

#### `win.visibleOnAllWorkspaces`

A `Boolean` property that determines whether the window is visible on all workspaces.

**Note:** Always returns false on Windows.

#### `win.shadow`

A `Boolean` property that determines whether the window has a shadow.

#### `win.menuBarVisible` _Windows_ _Linux_

A `Boolean` property that determines whether the menu bar should be visible.

**Note:** If the menu bar is auto-hide, users can still bring up the menu bar by pressing the single `Alt` key.

####  `win.kiosk`

A `Boolean` property that determines whether the window is in kiosk mode.

#### `win.documentEdited` _macOS_

A `Boolean` property that specifies whether the window’s document has been edited.

The icon in title bar will become gray when set to `true`.

#### `win.representedFilename` _macOS_

A `String` property that determines the pathname of the file the window represents,
and the icon of the file will show in window's title bar.

#### `win.title`

A `String` property that determines the title of the native window.

**Note:** The title of the web page can be different from the title of the native window.

#### `win.minimizable`

A `Boolean` property that determines whether the window can be manually minimized by user.

On Linux the setter is a no-op, although the getter returns `true`.

#### `win.maximizable`

A `Boolean` property that determines whether the window can be manually maximized by user.

On Linux the setter is a no-op, although the getter returns `true`.

#### `win.fullScreenable`

A `Boolean` property that determines whether the maximize/zoom window button toggles fullscreen mode or
maximizes the window.

#### `win.resizable`

A `Boolean` property that determines whether the window can be manually resized by user.

#### `win.closable`

A `Boolean` property that determines whether the window can be manually closed by user.

On Linux the setter is a no-op, although the getter returns `true`.

#### `win.movable`

A `Boolean` property that determines Whether the window can be moved by user.

On Linux the setter is a no-op, although the getter returns `true`.

#### `win.excludedFromShownWindowsMenu` _macOS_

A `Boolean` property that determines whether the window is excluded from the application’s Windows menu. `false` by default.

```js
const win = new TopLevelWindow({ height: 600, width: 600 })

const template = [
  {
    role: 'windowmenu'
  }
]

win.excludedFromShownWindowsMenu = true

const menu = Menu.buildFromTemplate(template)
Menu.setApplicationMenu(menu)
```

#### `win.accessibleTitle`

A `String` property that defines an alternative title provided only to
accessibility tools such as screen readers. This string is not directly
visible to users.

### Instance Methods

Objects created with `new BrowserWindow` have the following instance methods:

**Note:** Some methods are only available on specific operating systems and are
labeled as such.

#### `win.destroy()`

Force closing the window, the `unload` and `beforeunload` event won't be emitted
for the web page, and `close` event will also not be emitted
for this window, but it guarantees the `closed` event will be emitted.

#### `win.close()`

Try to close the window. This has the same effect as a user manually clicking
the close button of the window. The web page may cancel the close though. See
the [close event](#event-close).

#### `win.focus()`

Focuses on the window.

#### `win.blur()`

Removes focus from the window.

#### `win.isFocused()`

Returns `Boolean` - Whether the window is focused.

#### `win.isDestroyed()`

Returns `Boolean` - Whether the window is destroyed.

#### `win.show()`

Shows and gives focus to the window.

#### `win.showInactive()`

Shows the window but doesn't focus on it.

#### `win.hide()`

Hides the window.

#### `win.isVisible()`

Returns `Boolean` - Whether the window is visible to the user.

#### `win.isModal()`

Returns `Boolean` - Whether current window is a modal window.

#### `win.maximize()`

Maximizes the window. This will also show (but not focus) the window if it
isn't being displayed already.

#### `win.unmaximize()`

Unmaximizes the window.

#### `win.isMaximized()`

Returns `Boolean` - Whether the window is maximized.

#### `win.minimize()`

Minimizes the window. On some platforms the minimized window will be shown in
the Dock.

#### `win.restore()`

Restores the window from minimized state to its previous state.

#### `win.isMinimized()`

Returns `Boolean` - Whether the window is minimized.

#### `win.setFullScreen(flag)`

* `flag` Boolean

Sets whether the window should be in fullscreen mode.

#### `win.isFullScreen()`

Returns `Boolean` - Whether the window is in fullscreen mode.

#### `win.setSimpleFullScreen(flag)` _macOS_

* `flag` Boolean

Enters or leaves simple fullscreen mode.

Simple fullscreen mode emulates the native fullscreen behavior found in versions of macOS prior to Lion (10.7).

#### `win.isSimpleFullScreen()` _macOS_

Returns `Boolean` - Whether the window is in simple (pre-Lion) fullscreen mode.

#### `win.isNormal()`

Returns `Boolean` - Whether the window is in normal state (not maximized, not minimized, not in fullscreen mode).

#### `win.setAspectRatio(aspectRatio[, extraSize])` _macOS_ _Linux_

* `aspectRatio` Float - The aspect ratio to maintain for some portion of the
content view.
 * `extraSize` [Size](structures/size.md) (optional) _macOS_ - The extra size not to be included while
maintaining the aspect ratio.

This will make a window maintain an aspect ratio. The extra size allows a
developer to have space, specified in pixels, not included within the aspect
ratio calculations. This API already takes into account the difference between a
window's size and its content size.

Consider a normal window with an HD video player and associated controls.
Perhaps there are 15 pixels of controls on the left edge, 25 pixels of controls
on the right edge and 50 pixels of controls below the player. In order to
maintain a 16:9 aspect ratio (standard aspect ratio for HD @1920x1080) within
the player itself we would call this function with arguments of 16/9 and
{ width: 40, height: 50 }. The second argument doesn't care where the extra width and height
are within the content view--only that they exist. Sum any extra width and
height areas you have within the overall content view.

#### `win.setBackgroundColor(backgroundColor)`

* `backgroundColor` String - Window's background color as a hexadecimal value,
  like `#66CD00` or `#FFF` or `#80FFFFFF` (alpha is supported if `transparent`
  is `true`). Default is `#FFF` (white).

Sets the background color of the window. See [Setting
`backgroundColor`](#setting-backgroundcolor).

#### `win.previewFile(path[, displayName])` _macOS_

* `path` String - The absolute path to the file to preview with QuickLook. This
  is important as Quick Look uses the file name and file extension on the path
  to determine the content type of the file to open.
* `displayName` String (optional) - The name of the file to display on the
  Quick Look modal view. This is purely visual and does not affect the content
  type of the file. Defaults to `path`.

Uses [Quick Look][quick-look] to preview a file at a given path.

#### `win.closeFilePreview()` _macOS_

Closes the currently open [Quick Look][quick-look] panel.

#### `win.setBounds(bounds[, animate])`

* `bounds` Partial<[Rectangle](structures/rectangle.md)>
* `animate` Boolean (optional) _macOS_

Resizes and moves the window to the supplied bounds. Any properties that are not supplied will default to their current values.

```javascript
const { TopLevelWindow } = require('electron')
const win = new BrowserWindow()

// set all bounds properties
win.setBounds({ x: 440, y: 225, width: 800, height: 600 })

// set a single bounds property
win.setBounds({ width: 100 })

// { x: 440, y: 225, width: 100, height: 600 }
console.log(win.getBounds())
```

#### `win.getBounds()`

Returns [`Rectangle`](structures/rectangle.md) - The `bounds` of the window as `Object`.

#### `win.getBackgroundColor()`

Returns `String` - Gets the background color of the window. See [Setting
`backgroundColor`](#setting-backgroundcolor).

#### `win.setContentBounds(bounds[, animate])`

* `bounds` [Rectangle](structures/rectangle.md)
* `animate` Boolean (optional) _macOS_

Resizes and moves the window's client area (e.g. the web page) to
the supplied bounds.

#### `win.getContentBounds()`

Returns [`Rectangle`](structures/rectangle.md) - The `bounds` of the window's client area as `Object`.

#### `win.getNormalBounds()`

Returns [`Rectangle`](structures/rectangle.md) - Contains the window bounds of the normal state

**Note:** whatever the current state of the window : maximized, minimized or in fullscreen, this function always returns the position and size of the window in normal state. In normal state, getBounds and getNormalBounds returns the same [`Rectangle`](structures/rectangle.md).

#### `win.setEnabled(enable)`

* `enable` Boolean

Disable or enable the window.

#### `win.isEnabled()`

Returns Boolean - whether the window is enabled.

#### `win.setSize(width, height[, animate])`

* `width` Integer
* `height` Integer
* `animate` Boolean (optional) _macOS_

Resizes the window to `width` and `height`. If `width` or `height` are below any set minimum size constraints the window will snap to its minimum size.

#### `win.getSize()`

Returns `Integer[]` - Contains the window's width and height.

#### `win.setContentSize(width, height[, animate])`

* `width` Integer
* `height` Integer
* `animate` Boolean (optional) _macOS_

Resizes the window's client area (e.g. the web page) to `width` and `height`.

#### `win.getContentSize()`

Returns `Integer[]` - Contains the window's client area's width and height.

#### `win.setMinimumSize(width, height)`

* `width` Integer
* `height` Integer

Sets the minimum size of window to `width` and `height`.

#### `win.getMinimumSize()`

Returns `Integer[]` - Contains the window's minimum width and height.

#### `win.setMaximumSize(width, height)`

* `width` Integer
* `height` Integer

Sets the maximum size of window to `width` and `height`.

#### `win.getMaximumSize()`

Returns `Integer[]` - Contains the window's maximum width and height.

#### `win.setResizable(resizable)`

* `resizable` Boolean

Sets whether the window can be manually resized by the user.

#### `win.isResizable()`

Returns `Boolean` - Whether the window can be manually resized by the user.

#### `win.setMovable(movable)` _macOS_ _Windows_

* `movable` Boolean

Sets whether the window can be moved by user. On Linux does nothing.

#### `win.isMovable()` _macOS_ _Windows_

Returns `Boolean` - Whether the window can be moved by user.

On Linux always returns `true`.

#### `win.setMinimizable(minimizable)` _macOS_ _Windows_

* `minimizable` Boolean

Sets whether the window can be manually minimized by user. On Linux does nothing.

#### `win.isMinimizable()` _macOS_ _Windows_

Returns `Boolean` - Whether the window can be manually minimized by the user.

On Linux always returns `true`.

#### `win.setMaximizable(maximizable)` _macOS_ _Windows_

* `maximizable` Boolean

Sets whether the window can be manually maximized by user. On Linux does nothing.

#### `win.isMaximizable()` _macOS_ _Windows_

Returns `Boolean` - Whether the window can be manually maximized by user.

On Linux always returns `true`.

#### `win.setFullScreenable(fullscreenable)`

* `fullscreenable` Boolean

Sets whether the maximize/zoom window button toggles fullscreen mode or maximizes the window.

#### `win.isFullScreenable()`

Returns `Boolean` - Whether the maximize/zoom window button toggles fullscreen mode or maximizes the window.

#### `win.setClosable(closable)` _macOS_ _Windows_

* `closable` Boolean

Sets whether the window can be manually closed by user. On Linux does nothing.

#### `win.isClosable()` _macOS_ _Windows_

Returns `Boolean` - Whether the window can be manually closed by user.

On Linux always returns `true`.

#### `win.setAlwaysOnTop(flag[, level][, relativeLevel])`

* `flag` Boolean
* `level` String (optional) _macOS_ _Windows_ - Values include `normal`,
  `floating`, `torn-off-menu`, `modal-panel`, `main-menu`, `status`,
  `pop-up-menu`, `screen-saver`, and ~~`dock`~~ (Deprecated). The default is
  `floating` when `flag` is true. The `level` is reset to `normal` when the
  flag is false. Note that from `floating` to `status` included, the window is
  placed below the Dock on macOS and below the taskbar on Windows. From
  `pop-up-menu` to a higher it is shown above the Dock on macOS and above the
  taskbar on Windows. See the [macOS docs][window-levels] for more details.
* `relativeLevel` Integer (optional) _macOS_ - The number of layers higher to set
  this window relative to the given `level`. The default is `0`. Note that Apple
  discourages setting levels higher than 1 above `screen-saver`.

Sets whether the window should show always on top of other windows. After
setting this, the window is still a normal window, not a toolbox window which
can not be focused on.

#### `win.isAlwaysOnTop()`

Returns `Boolean` - Whether the window is always on top of other windows.

#### `win.moveAbove(mediaSourceId)`

* `mediaSourceId` String - Window id in the format of DesktopCapturerSource's id. For example "window:1869:0".

Moves window above the source window in the sense of z-order. If the
`mediaSourceId` is not of type window or if the window does not exist then
this method throws an error.

#### `win.moveTop()`

Moves window to top(z-order) regardless of focus

#### `win.center()`

Moves window to the center of the screen.

#### `win.setPosition(x, y[, animate])`

* `x` Integer
* `y` Integer
* `animate` Boolean (optional) _macOS_

Moves window to `x` and `y`.

#### `win.getPosition()`

Returns `Integer[]` - Contains the window's current position.

#### `win.setTitle(title)`

* `title` String

Changes the title of native window to `title`.

#### `win.getTitle()`

Returns `String` - The title of the native window.

**Note:** The title of the web page can be different from the title of the native
window.

#### `win.setSheetOffset(offsetY[, offsetX])` _macOS_

* `offsetY` Float
* `offsetX` Float (optional)

Changes the attachment point for sheets on macOS. By default, sheets are
attached just below the window frame, but you may want to display them beneath
a HTML-rendered toolbar. For example:

```javascript
const { TopLevelWindow } = require('electron')
let win = new TopLevelWindow()

let toolbarRect = document.getElementById('toolbar').getBoundingClientRect()
win.setSheetOffset(toolbarRect.height)
```

#### `win.flashFrame(flag)`

* `flag` Boolean

Starts or stops flashing the window to attract user's attention.

#### `win.setSkipTaskbar(skip)`

* `skip` Boolean

Makes the window not show in the taskbar.

#### `win.setKiosk(flag)`

* `flag` Boolean

Enters or leaves kiosk mode.

#### `win.isKiosk()`

Returns `Boolean` - Whether the window is in kiosk mode.

#### `win.getMediaSourceId()`

Returns `String` - Window id in the format of DesktopCapturerSource's id. For example "window:1234:0".

More precisely the format is `window:id:other_id` where `id` is `HWND` on
Windows, `CGWindowID` (`uint64_t`) on macOS and `Window` (`unsigned long`) on
Linux. `other_id` is used to identify web contents (tabs) so within the same
top level window.

#### `win.getNativeWindowHandle()`

Returns `Buffer` - The platform-specific handle of the window.

The native type of the handle is `HWND` on Windows, `NSView*` on macOS, and
`Window` (`unsigned long`) on Linux.

#### `win.hookWindowMessage(message, callback)` _Windows_

* `message` Integer
* `callback` Function

Hooks a windows message. The `callback` is called when
the message is received in the WndProc.

#### `win.isWindowMessageHooked(message)` _Windows_

* `message` Integer

Returns `Boolean` - `true` or `false` depending on whether the message is hooked.

#### `win.unhookWindowMessage(message)` _Windows_

* `message` Integer

Unhook the window message.

#### `win.unhookAllWindowMessages()` _Windows_

Unhooks all of the window messages.

#### `win.setRepresentedFilename(filename)` _macOS_

* `filename` String

Sets the pathname of the file the window represents, and the icon of the file
will show in window's title bar.

#### `win.getRepresentedFilename()` _macOS_

Returns `String` - The pathname of the file the window represents.

#### `win.setDocumentEdited(edited)` _macOS_

* `edited` Boolean

Specifies whether the window’s document has been edited, and the icon in title
bar will become gray when set to `true`.

#### `win.isDocumentEdited()` _macOS_

Returns `Boolean` - Whether the window's document has been edited.

#### `win.setMenu(menu)` _Linux_ _Windows_

* `menu` Menu | null

Sets the `menu` as the window's menu bar.

#### `win.removeMenu()` _Linux_ _Windows_

Remove the window's menu bar.

#### `win.setProgressBar(progress[, options])`

* `progress` Double
* `options` Object (optional)
  * `mode` String _Windows_ - Mode for the progress bar. Can be `none`, `normal`, `indeterminate`, `error` or `paused`.

Sets progress value in progress bar. Valid range is [0, 1.0].

Remove progress bar when progress < 0;
Change to indeterminate mode when progress > 1.

On Linux platform, only supports Unity desktop environment, you need to specify
the `*.desktop` file name to `desktopName` field in `package.json`. By default,
it will assume `{app.name}.desktop`.

On Windows, a mode can be passed. Accepted values are `none`, `normal`,
`indeterminate`, `error`, and `paused`. If you call `setProgressBar` without a
mode set (but with a value within the valid range), `normal` will be assumed.

#### `win.setOverlayIcon(overlay, description)` _Windows_

* `overlay` [NativeImage](native-image.md) | null - the icon to display on the bottom
right corner of the taskbar icon. If this parameter is `null`, the overlay is
cleared
* `description` String - a description that will be provided to Accessibility
screen readers

Sets a 16 x 16 pixel overlay onto the current taskbar icon, usually used to
convey some sort of application status or to passively notify the user.

#### `win.setHasShadow(hasShadow)`

* `hasShadow` Boolean

Sets whether the window should have a shadow.

#### `win.hasShadow()`

Returns `Boolean` - Whether the window has a shadow.

#### `win.setOpacity(opacity)` _Windows_ _macOS_

* `opacity` Number - between 0.0 (fully transparent) and 1.0 (fully opaque)

Sets the opacity of the window. On Linux, does nothing. Out of bound number
values are clamped to the [0, 1] range.

#### `win.getOpacity()`

Returns `Number` - between 0.0 (fully transparent) and 1.0 (fully opaque). On
Linux, always returns 1.

#### `win.setShape(rects)` _Windows_ _Linux_ _Experimental_

* `rects` [Rectangle[]](structures/rectangle.md) - Sets a shape on the window.
  Passing an empty list reverts the window to being rectangular.

Setting a window shape determines the area within the window where the system
permits drawing and user interaction. Outside of the given region, no pixels
will be drawn and no mouse events will be registered. Mouse events outside of
the region will not be received by that window, but will fall through to
whatever is behind the window.

#### `win.setThumbarButtons(buttons)` _Windows_

* `buttons` [ThumbarButton[]](structures/thumbar-button.md)

Returns `Boolean` - Whether the buttons were added successfully

Add a thumbnail toolbar with a specified set of buttons to the thumbnail image
of a window in a taskbar button layout. Returns a `Boolean` object indicates
whether the thumbnail has been added successfully.

The number of buttons in thumbnail toolbar should be no greater than 7 due to
the limited room. Once you setup the thumbnail toolbar, the toolbar cannot be
removed due to the platform's limitation. But you can call the API with an empty
array to clean the buttons.

The `buttons` is an array of `Button` objects:

* `Button` Object
  * `icon` [NativeImage](native-image.md) - The icon showing in thumbnail
    toolbar.
  * `click` Function
  * `tooltip` String (optional) - The text of the button's tooltip.
  * `flags` String[] (optional) - Control specific states and behaviors of the
    button. By default, it is `['enabled']`.

The `flags` is an array that can include following `String`s:

* `enabled` - The button is active and available to the user.
* `disabled` - The button is disabled. It is present, but has a visual state
  indicating it will not respond to user action.
* `dismissonclick` - When the button is clicked, the thumbnail window closes
  immediately.
* `nobackground` - Do not draw a button border, use only the image.
* `hidden` - The button is not shown to the user.
* `noninteractive` - The button is enabled but not interactive; no pressed
  button state is drawn. This value is intended for instances where the button
  is used in a notification.

#### `win.setThumbnailClip(region)` _Windows_

* `region` [Rectangle](structures/rectangle.md) - Region of the window

Sets the region of the window to show as the thumbnail image displayed when
hovering over the window in the taskbar. You can reset the thumbnail to be
the entire window by specifying an empty region:
`{ x: 0, y: 0, width: 0, height: 0 }`.

#### `win.setThumbnailToolTip(toolTip)` _Windows_

* `toolTip` String

Sets the toolTip that is displayed when hovering over the window thumbnail
in the taskbar.

#### `win.setAppDetails(options)` _Windows_

* `options` Object
  * `appId` String (optional) - Window's [App User Model ID](https://msdn.microsoft.com/en-us/library/windows/desktop/dd391569(v=vs.85).aspx).
    It has to be set, otherwise the other options will have no effect.
  * `appIconPath` String (optional) - Window's [Relaunch Icon](https://msdn.microsoft.com/en-us/library/windows/desktop/dd391573(v=vs.85).aspx).
  * `appIconIndex` Integer (optional) - Index of the icon in `appIconPath`.
    Ignored when `appIconPath` is not set. Default is `0`.
  * `relaunchCommand` String (optional) - Window's [Relaunch Command](https://msdn.microsoft.com/en-us/library/windows/desktop/dd391571(v=vs.85).aspx).
  * `relaunchDisplayName` String (optional) - Window's [Relaunch Display Name](https://msdn.microsoft.com/en-us/library/windows/desktop/dd391572(v=vs.85).aspx).

Sets the properties for the window's taskbar button.

**Note:** `relaunchCommand` and `relaunchDisplayName` must always be set
together. If one of those properties is not set, then neither will be used.

#### `win.showDefinitionForSelection()` _macOS_

Same as `webContents.showDefinitionForSelection()`.

#### `win.setIcon(icon)` _Windows_ _Linux_

* `icon` [NativeImage](native-image.md) | String

Changes window icon.

#### `win.setWindowButtonVisibility(visible)` _macOS_

* `visible` Boolean

Sets whether the window traffic light buttons should be visible.

This cannot be called when `titleBarStyle` is set to `customButtonsOnHover`.

#### `win.setAutoHideMenuBar(hide)`

* `hide` Boolean

Sets whether the window menu bar should hide itself automatically. Once set the
menu bar will only show when users press the single `Alt` key.

If the menu bar is already visible, calling `setAutoHideMenuBar(true)` won't hide it immediately.

#### `win.isMenuBarAutoHide()`

Returns `Boolean` - Whether menu bar automatically hides itself.

#### `win.setMenuBarVisibility(visible)` _Windows_ _Linux_

* `visible` Boolean

Sets whether the menu bar should be visible. If the menu bar is auto-hide, users can still bring up the menu bar by pressing the single `Alt` key.

#### `win.isMenuBarVisible()`

Returns `Boolean` - Whether the menu bar is visible.

#### `win.setVisibleOnAllWorkspaces(visible)`

* `visible` Boolean

Sets whether the window should be visible on all workspaces.

**Note:** This API does nothing on Windows.

#### `win.isVisibleOnAllWorkspaces()`

Returns `Boolean` - Whether the window is visible on all workspaces.

**Note:** This API always returns false on Windows.

#### `win.setIgnoreMouseEvents(ignore[, options])`

* `ignore` Boolean
* `options` Object (optional)
  * `forward` Boolean (optional) _macOS_ _Windows_ - If true, forwards mouse move
    messages to Chromium, enabling mouse related events such as `mouseleave`.
    Only used when `ignore` is true. If `ignore` is false, forwarding is always
    disabled regardless of this value.

Makes the window ignore all mouse events.

All mouse events happened in this window will be passed to the window below
this window, but if this window has focus, it will still receive keyboard
events.

#### `win.setContentProtection(enable)` _macOS_ _Windows_

* `enable` Boolean

Prevents the window contents from being captured by other apps.

On macOS it sets the NSWindow's sharingType to NSWindowSharingNone.
On Windows it calls SetWindowDisplayAffinity with `WDA_MONITOR`.

#### `win.setFocusable(focusable)` _macOS_ _Windows_

* `focusable` Boolean

Changes whether the window can be focused.

On macOS it does not remove the focus from the window.

#### `win.setParentWindow(parent)`

* `parent` BrowserWindow | null

Sets `parent` as current window's parent window, passing `null` will turn
current window into a top-level window.

#### `win.getParentWindow()`

Returns `TopLevelWindow` - The parent window.

#### `win.getChildWindows()`

Returns `TopLevelWindow[]` - All child windows.

#### `win.setAutoHideCursor(autoHide)` _macOS_

* `autoHide` Boolean

Controls whether to hide cursor when typing.

#### `win.selectPreviousTab()` _macOS_

Selects the previous tab when native tabs are enabled and there are other
tabs in the window.

#### `win.selectNextTab()` _macOS_

Selects the next tab when native tabs are enabled and there are other
tabs in the window.

#### `win.mergeAllWindows()` _macOS_

Merges all windows into one window with multiple tabs when native tabs
are enabled and there is more than one open window.

#### `win.moveTabToNewWindow()` _macOS_

Moves the current tab into a new window if native tabs are enabled and
there is more than one tab in the current window.

#### `win.toggleTabBar()` _macOS_

Toggles the visibility of the tab bar if native tabs are enabled and
there is only one tab in the current window.

#### `win.addTabbedWindow(topLevelWindow)` _macOS_

* `topLevelWindow` TopLevelWindow

Adds a window as a tab on this window, after the tab for the window instance.

#### `win.setVibrancy(type)` _macOS_

* `type` String | null - Can be `appearance-based`, `light`, `dark`, `titlebar`,
  `selection`, `menu`, `popover`, `sidebar`, `medium-light`, `ultra-dark`, `header`, `sheet`, `window`, `hud`, `fullscreen-ui`, `tooltip`, `content`, `under-window`, or `under-page`. See
  the [macOS documentation][vibrancy-docs] for more details.

Adds a vibrancy effect to the browser window. Passing `null` or an empty string
will remove the vibrancy effect on the window.

Note that `appearance-based`, `light`, `dark`, `medium-light`, and `ultra-dark` have been
deprecated and will be removed in an upcoming version of macOS.

#### `win.setTrafficLightPosition(position)` _macOS_

* `position` [Point](structures/point.md)

Set a custom position for the traffic light buttons. Can only be used with `titleBarStyle` set to `hidden`.

#### `win.getTrafficLightPosition()` _macOS_

Returns `Point` - The current position for the traffic light buttons. Can only be used with `titleBarStyle`
set to `hidden`.

#### `win.setTouchBar(touchBar)` _macOS_

* `touchBar` TouchBar | null

Sets the touchBar layout for the current window. Specifying `null` or
`undefined` clears the touch bar. This method only has an effect if the
machine has a touch bar and is running on macOS 10.12.1+.

**Note:** The TouchBar API is currently experimental and may change or be
removed in future Electron releases.

#### `win.setBrowserView(browserView)` _Experimental_

* `browserView` [BrowserView](browser-view.md) | null - Attach `browserView` to `win`.
If there are other `BrowserView`s attached, they will be removed from
this window.

#### `win.getBrowserView()` _Experimental_

Returns `BrowserView | null` - The `BrowserView` attached to `win`. Returns `null`
if one is not attached. Throws an error if multiple `BrowserView`s are attached.

#### `win.addBrowserView(browserView)` _Experimental_

* `browserView` [BrowserView](browser-view.md)

Replacement API for setBrowserView supporting work with multi browser views.

#### `win.removeBrowserView(browserView)` _Experimental_

* `browserView` [BrowserView](browser-view.md)

#### `win.getBrowserViews()` _Experimental_

Returns `BrowserView[]` - an array of all BrowserViews that have been attached
with `addBrowserView` or `setBrowserView`.

**Note:** The BrowserView API is currently experimental and may change or be
removed in future Electron releases.

[quick-look]: https://en.wikipedia.org/wiki/Quick_Look
[vibrancy-docs]: https://developer.apple.com/documentation/appkit/nsvisualeffectview?preferredLanguage=objc
[window-levels]: https://developer.apple.com/documentation/appkit/nswindow/level
[chrome-content-scripts]: https://developer.chrome.com/extensions/content_scripts#execution-environment
[event-emitter]: https://nodejs.org/api/events.html#events_class_eventemitter
