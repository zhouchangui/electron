# BrowserWindow

> Create and control browser windows.

Process: [Main](../glossary.md#main-process)

```javascript
// In the main process.
const { BrowserWindow } = require('electron')

// Or use `remote` from the renderer process.
// const { BrowserWindow } = require('electron').remote

const browserWindow = new BrowserWindow({ width: 800, height: 600 })

// Load a remote URL
browserWindow.loadURL('https://github.com')

// Or load a local HTML file
browserWindow.loadURL(`file://${__dirname}/app/index.html`)
```

## Showing window gracefully

When loading a page in the window directly, users may see the page load
incrementally, which is not a good experience for a native app. To make the
window display without visual flash, there are two solutions for different
situations.

### Using `ready-to-show` event

While loading the page, the `ready-to-show` event will be emitted when the
renderer process has rendered the page for the first time if the window has not
been shown yet. Showing the window after this event will have no visual flash:

```javascript
const { BrowserWindow } = require('electron')
let browserWindow = new BrowserWindow({ show: false })
browserWindow.once('ready-to-show', () => {
  browserWindow.show()
})
```

This event is usually emitted after the `did-finish-load` event, but for
pages with many remote resources, it may be emitted before the `did-finish-load`
event.

Please note that using this event implies that the renderer will be considered
"visible" and paint even though `show` is false.  This event will never fire if
you use `paintWhenInitiallyHidden: false`

### Setting `backgroundColor`

For a complex app, the `ready-to-show` event could be emitted too late, making
the app feel slow. In this case, it is recommended to show the window
immediately, and use a `backgroundColor` close to your app's background:

```javascript
const { BrowserWindow } = require('electron')

let browserWindow = new BrowserWindow({ backgroundColor: '#2e2c29' })
browserWindow.loadURL('https://github.com')
```

Note that even for apps that use `ready-to-show` event, it is still recommended
to set `backgroundColor` to make app feel more native.

## Page visibility

The [Page Visibility API][page-visibility-api] works as follows:

* On all platforms, the visibility state tracks whether the window is
  hidden/minimized or not.
* Additionally, on macOS, the visibility state also tracks the window
  occlusion state. If the window is occluded (i.e. fully covered) by another
  window, the visibility state will be `hidden`. On other platforms, the
  visibility state will be `hidden` only when the window is minimized or
  explicitly hidden with `browserWindow.hide()`.
* If a `BrowserWindow` is created with `show: false`, the initial visibility
  state will be `visible` despite the window actually being hidden.
* If `backgroundThrottling` is disabled, the visibility state will remain
  `visible` even if the window is minimized, occluded, or hidden.

It is recommended that you pause expensive operations when the visibility
state is `hidden` in order to minimize power consumption.

## `beforeunload` event

For instances of `BrowserWindow`, the `close` event is emitted before the
`beforeunload` and `unload` event of the DOM.

Usually you would want to use the `beforeunload` handler to decide whether the
window should be closed, which will also be called when the window is reloaded.
In Electron, returning any value other than `undefined` would cancel the close.
For example:

```javascript
window.onbeforeunload = (e) => {
  console.log('I do not want to be closed')

  // Unlike usual browsers that a message box will be prompted to users,
  // returning a non-void value will silently cancel the close. It is
  // recommended to use the dialog API to let the user confirm closing the
  // application.
  e.returnValue = false // equivalent to `return false` but not recommended
}
```
_**Note**: There is a subtle difference between the behaviors of `window.onbeforeunload = handler` and `window.addEventListener('beforeunload', handler)`. It is recommended to always set the `event.returnValue` explicitly, instead of only returning a value, as the former works more consistently within Electron._

## Class: BrowserWindow extends `TopLevelWindow`

> Create and control browser windows.

Process: [Main](../glossary.md#main-process)

### `new BrowserWindow([options])`

* `options` Object (optional)
  * `paintWhenInitiallyHidden` Boolean (optional) - Whether the renderer should be active when `show` is `false` and it has just been created.  In order for `document.visibilityState` to work correctly on first load with `show: false` you should set this to `false`.  Setting this to `false` will cause the `ready-to-show` event to not fire.  Default is `true`.
  * `webPreferences` Object (optional) - Settings of web page's features.
    * `devTools` Boolean (optional) - Whether to enable DevTools. If it is set to `false`, can not use `BrowserWindow.webContents.openDevTools()` to open DevTools. Default is `true`.
    * `nodeIntegration` Boolean (optional) - Whether node integration is enabled.
      Default is `false`.
    * `nodeIntegrationInWorker` Boolean (optional) - Whether node integration is
      enabled in web workers. Default is `false`. More about this can be found
      in [Multithreading](../tutorial/multithreading.md).
    * `nodeIntegrationInSubFrames` Boolean (optional) - Experimental option for
      enabling Node.js support in sub-frames such as iframes and child windows. All your preloads will load for
      every iframe, you can use `process.isMainFrame` to determine if you are
      in the main frame or not.
    * `preload` String (optional) - Specifies a script that will be loaded before other
      scripts run in the page. This script will always have access to node APIs
      no matter whether node integration is turned on or off. The value should
      be the absolute file path to the script.
      When node integration is turned off, the preload script can reintroduce
      Node global symbols back to the global scope. See example
      [here](process.md#event-loaded).
    * `sandbox` Boolean (optional) - If set, this will sandbox the renderer
      associated with the window, making it compatible with the Chromium
      OS-level sandbox and disabling the Node.js engine. This is not the same as
      the `nodeIntegration` option and the APIs available to the preload script
      are more limited. Read more about the option [here](sandbox-option.md).
    * `enableRemoteModule` Boolean (optional) - Whether to enable the [`remote`](remote.md) module.
      Default is `true`.
    * `session` [Session](session.md#class-session) (optional) - Sets the session used by the
      page. Instead of passing the Session object directly, you can also choose to
      use the `partition` option instead, which accepts a partition string. When
      both `session` and `partition` are provided, `session` will be preferred.
      Default is the default session.
    * `partition` String (optional) - Sets the session used by the page according to the
      session's partition string. If `partition` starts with `persist:`, the page
      will use a persistent session available to all pages in the app with the
      same `partition`. If there is no `persist:` prefix, the page will use an
      in-memory session. By assigning the same `partition`, multiple pages can share
      the same session. Default is the default session.
    * `affinity` String (optional) - When specified, web pages with the same
      `affinity` will run in the same renderer process. Note that due to reusing
      the renderer process, certain `webPreferences` options will also be shared
      between the web pages even when you specified different values for them,
      including but not limited to `preload`, `sandbox` and `nodeIntegration`.
      So it is suggested to use exact same `webPreferences` for web pages with
      the same `affinity`. _Deprecated_
    * `zoomFactor` Number (optional) - The default zoom factor of the page, `3.0` represents
      `300%`. Default is `1.0`.
    * `javascript` Boolean (optional) - Enables JavaScript support. Default is `true`.
    * `webSecurity` Boolean (optional) - When `false`, it will disable the
      same-origin policy (usually using testing websites by people), and set
      `allowRunningInsecureContent` to `true` if this options has not been set
      by user. Default is `true`.
    * `allowRunningInsecureContent` Boolean (optional) - Allow an https page to run
      JavaScript, CSS or plugins from http URLs. Default is `false`.
    * `images` Boolean (optional) - Enables image support. Default is `true`.
    * `textAreasAreResizable` Boolean (optional) - Make TextArea elements resizable. Default
      is `true`.
    * `webgl` Boolean (optional) - Enables WebGL support. Default is `true`.
    * `plugins` Boolean (optional) - Whether plugins should be enabled. Default is `false`.
    * `experimentalFeatures` Boolean (optional) - Enables Chromium's experimental features.
      Default is `false`.
    * `scrollBounce` Boolean (optional) - Enables scroll bounce (rubber banding) effect on
      macOS. Default is `false`.
    * `enableBlinkFeatures` String (optional) - A list of feature strings separated by `,`, like
      `CSSVariables,KeyboardEventKey` to enable. The full list of supported feature
      strings can be found in the [RuntimeEnabledFeatures.json5][runtime-enabled-features]
      file.
    * `disableBlinkFeatures` String (optional) - A list of feature strings separated by `,`,
      like `CSSVariables,KeyboardEventKey` to disable. The full list of supported
      feature strings can be found in the
      [RuntimeEnabledFeatures.json5][runtime-enabled-features] file.
    * `defaultFontFamily` Object (optional) - Sets the default font for the font-family.
      * `standard` String (optional) - Defaults to `Times New Roman`.
      * `serif` String (optional) - Defaults to `Times New Roman`.
      * `sansSerif` String (optional) - Defaults to `Arial`.
      * `monospace` String (optional) - Defaults to `Courier New`.
      * `cursive` String (optional) - Defaults to `Script`.
      * `fantasy` String (optional) - Defaults to `Impact`.
    * `defaultFontSize` Integer (optional) - Defaults to `16`.
    * `defaultMonospaceFontSize` Integer (optional) - Defaults to `13`.
    * `minimumFontSize` Integer (optional) - Defaults to `0`.
    * `defaultEncoding` String (optional) - Defaults to `ISO-8859-1`.
    * `backgroundThrottling` Boolean (optional) - Whether to throttle animations and timers
      when the page becomes background. This also affects the
      [Page Visibility API](#page-visibility). Defaults to `true`.
    * `offscreen` Boolean (optional) - Whether to enable offscreen rendering for the browser
      window. Defaults to `false`. See the
      [offscreen rendering tutorial](../tutorial/offscreen-rendering.md) for
      more details.
    * `contextIsolation` Boolean (optional) - Whether to run Electron APIs and
      the specified `preload` script in a separate JavaScript context. Defaults
      to `false`. The context that the `preload` script runs in will still
      have full access to the `document` and `window` globals but it will use
      its own set of JavaScript builtins (`Array`, `Object`, `JSON`, etc.)
      and will be isolated from any changes made to the global environment
      by the loaded page. The Electron API will only be available in the
      `preload` script and not the loaded page. This option should be used when
      loading potentially untrusted remote content to ensure the loaded content
      cannot tamper with the `preload` script and any Electron APIs being used.
      This option uses the same technique used by [Chrome Content Scripts][chrome-content-scripts].
      You can access this context in the dev tools by selecting the
      'Electron Isolated Context' entry in the combo box at the top of the
      Console tab.
    * `nativeWindowOpen` Boolean (optional) - Whether to use native
      `window.open()`. Defaults to `false`. Child windows will always have node
      integration disabled unless `nodeIntegrationInSubFrames` is true. **Note:** This option is currently
      experimental.
    * `webviewTag` Boolean (optional) - Whether to enable the [`<webview>` tag](webview-tag.md).
      Defaults to `false`. **Note:** The
      `preload` script configured for the `<webview>` will have node integration
      enabled when it is executed so you should ensure remote/untrusted content
      is not able to create a `<webview>` tag with a possibly malicious `preload`
      script. You can use the `will-attach-webview` event on [webContents](web-contents.md)
      to strip away the `preload` script and to validate or alter the
      `<webview>`'s initial settings.
    * `additionalArguments` String[] (optional) - A list of strings that will be appended
      to `process.argv` in the renderer process of this app.  Useful for passing small
      bits of data down to renderer process preload scripts.
    * `safeDialogs` Boolean (optional) - Whether to enable browser style
      consecutive dialog protection. Default is `false`.
    * `safeDialogsMessage` String (optional) - The message to display when
      consecutive dialog protection is triggered. If not defined the default
      message would be used, note that currently the default message is in
      English and not localized.
    * `disableDialogs` Boolean (optional) - Whether to disable dialogs
      completely. Overrides `safeDialogs`. Default is `false`.
    * `navigateOnDragDrop` Boolean (optional) - Whether dragging and dropping a
      file or link onto the page causes a navigation. Default is `false`.
    * `autoplayPolicy` String (optional) - Autoplay policy to apply to
      content in the window, can be `no-user-gesture-required`,
      `user-gesture-required`, `document-user-activation-required`. Defaults to
      `no-user-gesture-required`.
    * `disableHtmlFullscreenWindowResize` Boolean (optional) - Whether to
      prevent the window from resizing when entering HTML Fullscreen. Default
      is `false`.
    * `accessibleTitle` String (optional) - An alternative title string provided only
      to accessibility tools such as screen readers. This string is not directly
      visible to users.
    * `spellcheck` Boolean (optional) - Whether to enable the builtin spellchecker.
      Default is `true`.

Creates a new `BrowserWindow`, the `options` will also be passed to its base
class `TopLevelWindow`.

### Instance Events

Objects created with `new BrowserWindow` emit the following events:

#### Event: 'page-title-updated'

Returns:

* `event` Event
* `title` String
* `explicitSet` Boolean

Emitted when the document changed its title, calling `event.preventDefault()`
will prevent the native window's title from changing.
`explicitSet` is false when title is synthesized from file URL.

#### Event: 'unresponsive'

Emitted when the web page becomes unresponsive.

#### Event: 'responsive'

Emitted when the unresponsive web page becomes responsive again.

#### Event: 'ready-to-show'

Emitted when the web page has been rendered (while not being shown) and window
can be displayed without a visual flash.

Please note that using this event implies that the renderer will be considered
"visible" and paint even though `show` is false. This event will never fire if
you use `paintWhenInitiallyHidden: false`.

#### Event: 'enter-html-full-screen'

Emitted when the window enters a full-screen state triggered by HTML API.

#### Event: 'leave-html-full-screen'

Emitted when the window leaves a full-screen state triggered by HTML API.

#### Event: 'scroll-touch-edge' _macOS_

Emitted when scroll wheel event phase filed upon reaching the edge of element.

### Static Methods

The `BrowserWindow` class has the following static methods:

#### `BrowserWindow.getAllWindows()`

Returns `BrowserWindow[]` - An array of all opened browser windows.

#### `BrowserWindow.getFocusedWindow()`

Returns `BrowserWindow | null` - The window that is focused in this application, otherwise returns `null`.

#### `BrowserWindow.fromWebContents(webContents)`

* `webContents` [WebContents](web-contents.md)

Returns `BrowserWindow | null` - The window that owns the given `webContents`
or `null` if the contents are not owned by a window.

#### `BrowserWindow.fromBrowserView(browserView)`

* `browserView` [BrowserView](browser-view.md)

Returns `BrowserWindow | null` - The window that owns the given `browserView`. If the given view is not attached to any window, returns `null`.

#### `BrowserWindow.fromId(id)`

* `id` Integer

Returns `BrowserWindow` - The window with the given `id`.

#### `BrowserWindow.addExtension(path)` _Deprecated_

* `path` String

Adds Chrome extension located at `path`, and returns extension's name.

The method will also not return if the extension's manifest is missing or incomplete.

**Note:** This API cannot be called before the `ready` event of the `app` module
is emitted.

**Note:** This method is deprecated. Instead, use
[`ses.loadExtension(path)`](session.md#sesloadextensionpath).

#### `BrowserWindow.removeExtension(name)` _Deprecated_

* `name` String

Remove a Chrome extension by name.

**Note:** This API cannot be called before the `ready` event of the `app` module
is emitted.

**Note:** This method is deprecated. Instead, use
[`ses.removeExtension(extension_id)`](session.md#sesremoveextensionextensionid).

#### `BrowserWindow.getExtensions()` _Deprecated_

Returns `Record<String, ExtensionInfo>` - The keys are the extension names and each value is
an Object containing `name` and `version` properties.

**Note:** This API cannot be called before the `ready` event of the `app` module
is emitted.

**Note:** This method is deprecated. Instead, use
[`ses.getAllExtensions()`](session.md#sesgetallextensions).

#### `BrowserWindow.addDevToolsExtension(path)` _Deprecated_

* `path` String

Adds DevTools extension located at `path`, and returns extension's name.

The extension will be remembered so you only need to call this API once, this
API is not for programming use. If you try to add an extension that has already
been loaded, this method will not return and instead log a warning to the
console.

The method will also not return if the extension's manifest is missing or incomplete.

**Note:** This API cannot be called before the `ready` event of the `app` module
is emitted.

**Note:** This method is deprecated. Instead, use
[`ses.loadExtension(path)`](session.md#sesloadextensionpath).

#### `BrowserWindow.removeDevToolsExtension(name)` _Deprecated_

* `name` String

Remove a DevTools extension by name.

**Note:** This API cannot be called before the `ready` event of the `app` module
is emitted.

**Note:** This method is deprecated. Instead, use
[`ses.removeExtension(extension_id)`](session.md#sesremoveextensionextensionid).

#### `BrowserWindow.getDevToolsExtensions()` _Deprecated_

Returns `Record<string, ExtensionInfo>` - The keys are the extension names and each value is
an Object containing `name` and `version` properties.

To check if a DevTools extension is installed you can run the following:

```javascript
const { BrowserWindow } = require('electron')

let installed = BrowserWindow.getDevToolsExtensions().hasOwnProperty('devtron')
console.log(installed)
```

**Note:** This API cannot be called before the `ready` event of the `app` module
is emitted.

**Note:** This method is deprecated. Instead, use
[`ses.getAllExtensions()`](session.md#sesgetallextensions).

### Instance Properties

Objects created with `new BrowserWindow` have the following properties:

```javascript
const { BrowserWindow } = require('electron')
// In this example `win` is our instance
let browserWindow = new BrowserWindow({ width: 800, height: 600 })
browserWindow.loadURL('https://github.com')
```

#### `browserWindow.webContents` _Readonly_

A `WebContents` object this window owns. All web page related events and
operations will be done via it.

See the [`webContents` documentation](web-contents.md) for its methods and
events.

### Instance Methods

Objects created with `new BrowserWindow` have the following instance methods:

**Note:** Some methods are only available on specific operating systems and are
labeled as such.

#### `browserWindow.focusOnWebView()`

#### `browserWindow.blurWebView()`

#### `browserWindow.capturePage([rect])`

* `rect` [Rectangle](structures/rectangle.md) (optional) - The bounds to capture

Returns `Promise<NativeImage>` - Resolves with a [NativeImage](native-image.md)

Captures a snapshot of the page within `rect`. Omitting `rect` will capture the whole visible page.

#### `browserWindow.loadURL(url[, options])`

* `url` String
* `options` Object (optional)
  * `httpReferrer` (String | [Referrer](structures/referrer.md)) (optional) - An HTTP Referrer URL.
  * `userAgent` String (optional) - A user agent originating the request.
  * `extraHeaders` String (optional) - Extra headers separated by "\n"
  * `postData` ([UploadRawData[]](structures/upload-raw-data.md) | [UploadFile[]](structures/upload-file.md) | [UploadBlob[]](structures/upload-blob.md)) (optional)
  * `baseURLForDataURL` String (optional) - Base URL (with trailing path separator) for files to be loaded by the data URL. This is needed only if the specified `url` is a data URL and needs to load other files.

Returns `Promise<void>` - the promise will resolve when the page has finished loading
(see [`did-finish-load`](web-contents.md#event-did-finish-load)), and rejects
if the page fails to load (see [`did-fail-load`](web-contents.md#event-did-fail-load)).

Same as [`webContents.loadURL(url[, options])`](web-contents.md#contentsloadurlurl-options).

The `url` can be a remote address (e.g. `http://`) or a path to a local
HTML file using the `file://` protocol.

To ensure that file URLs are properly formatted, it is recommended to use
Node's [`url.format`](https://nodejs.org/api/url.html#url_url_format_urlobject)
method:

```javascript
let url = require('url').format({
  protocol: 'file',
  slashes: true,
  pathname: require('path').join(__dirname, 'index.html')
})

browserWindow.loadURL(url)
```

You can load a URL using a `POST` request with URL-encoded data by doing
the following:

```javascript
browserWindow.loadURL('http://localhost:8000/post', {
  postData: [{
    type: 'rawData',
    bytes: Buffer.from('hello=world')
  }],
  extraHeaders: 'Content-Type: application/x-www-form-urlencoded'
})
```

#### `browserWindow.loadFile(filePath[, options])`

* `filePath` String
* `options` Object (optional)
  * `query` Record<String, String> (optional) - Passed to `url.format()`.
  * `search` String (optional) - Passed to `url.format()`.
  * `hash` String (optional) - Passed to `url.format()`.

Returns `Promise<void>` - the promise will resolve when the page has finished loading
(see [`did-finish-load`](web-contents.md#event-did-finish-load)), and rejects
if the page fails to load (see [`did-fail-load`](web-contents.md#event-did-fail-load)).

Same as `webContents.loadFile`, `filePath` should be a path to an HTML
file relative to the root of your application.  See the `webContents` docs
for more information.

#### `browserWindow.reload()`

Same as `webContents.reload`.

[page-visibility-api]: https://developer.mozilla.org/en-US/docs/Web/API/Page_Visibility_API
[runtime-enabled-features]: https://cs.chromium.org/chromium/src/third_party/blink/renderer/platform/runtime_enabled_features.json5?l=70
