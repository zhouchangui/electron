# WebPreferences Object

* `devTools` Boolean (optional) - Whether to enable DevTools. If it is set to
  `false`, can not use `BrowserWindow.webContents.openDevTools()` to open
  DevTools. Default is `true`.
* `nodeIntegration` Boolean (optional) - Whether node integration is enabled.
  Default is `false`.
* `nodeIntegrationInWorker` Boolean (optional) - Whether node integration is
  enabled in web workers. Default is `false`. More about this can be found
  in [Multithreading](../../tutorial/multithreading.md).
* `nodeIntegrationInSubFrames` Boolean (optional) - Experimental option for
  enabling Node.js support in sub-frames such as iframes and child windows. All
  your preloads will load for every iframe, you can use `process.isMainFrame` to
  determine if you are in the main frame or not.
* `preload` String (optional) - Specifies a script that will be loaded before
  other scripts run in the page. This script will always have access to node
  APIs no matter whether node integration is turned on or off. The value should
  be the absolute file path to the script. When node integration is turned off,
  the preload script can reintroduce Node global symbols back to the global
  scope. See example [here](../process.md#event-loaded).
* `sandbox` Boolean (optional) - If set, this will sandbox the renderer
  associated with the window, making it compatible with the Chromium
  OS-level sandbox and disabling the Node.js engine. This is not the same as
  the `nodeIntegration` option and the APIs available to the preload script
  are more limited. Read more about the option [here](../sandbox-option.md).
* `enableRemoteModule` Boolean (optional) - Whether to enable the
  [`remote`](../remote.md) module. Default is `true`.
* `session` [Session](../session.md#class-session) (optional) - Sets the session
  used by the page. Instead of passing the Session object directly, you can also
  choose to use the `partition` option instead, which accepts a partition
  string. When both `session` and `partition` are provided, `session` will be
  preferred. Default is the default session.
* `partition` String (optional) - Sets the session used by the page according to
  the session's partition string. If `partition` starts with `persist:`, the
  page will use a persistent session available to all pages in the app with the
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
* `zoomFactor` Number (optional) - The default zoom factor of the page, `3.0`
  represents `300%`. Default is `1.0`.
* `javascript` Boolean (optional) - Enables JavaScript support. Default is
  `true`.
* `webSecurity` Boolean (optional) - When `false`, it will disable the
  same-origin policy (usually using testing websites by people), and set
  `allowRunningInsecureContent` to `true` if this options has not been set
  by user. Default is `true`.
* `allowRunningInsecureContent` Boolean (optional) - Allow an https page to run
  JavaScript, CSS or plugins from http URLs. Default is `false`.
* `images` Boolean (optional) - Enables image support. Default is `true`.
* `textAreasAreResizable` Boolean (optional) - Make TextArea elements resizable.
  Default is `true`.
* `webgl` Boolean (optional) - Enables WebGL support. Default is `true`.
* `plugins` Boolean (optional) - Whether plugins should be enabled. Default is
  `false`.
* `experimentalFeatures` Boolean (optional) - Enables Chromium's experimental
  features. Default is `false`.
* `scrollBounce` Boolean (optional) - Enables scroll bounce (rubber banding)
  effect on macOS. Default is `false`.
* `enableBlinkFeatures` String (optional) - A list of feature strings separated
  by `,`, like `CSSVariables,KeyboardEventKey` to enable. The full list of
  supported feature strings can be found in the
  [RuntimeEnabledFeatures.json5][runtime-enabled-features] file.
* `disableBlinkFeatures` String (optional) - A list of feature strings separated
  by `,`, like `CSSVariables,KeyboardEventKey` to disable. The full list of
  supported feature strings can be found in the
  [RuntimeEnabledFeatures.json5][runtime-enabled-features] file.
* `defaultFontFamily` Object (optional) - Sets the default font for the
  font-family.
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
* `backgroundThrottling` Boolean (optional) - Whether to throttle animations and
  timers when the page becomes background. This also affects the [Page
  Visibility API](#page-visibility). Defaults to `true`.
* `offscreen` Boolean (optional) - Whether to enable offscreen rendering for the
  browser window. Defaults to `false`. See the [offscreen rendering
  tutorial](../../tutorial/offscreen-rendering.md) for more details.
* `contextIsolation` Boolean (optional) - Whether to run Electron APIs and the
  specified `preload` script in a separate JavaScript context. Defaults to
  `false`. The context that the `preload` script runs in will still have full
  access to the `document` and `window` globals but it will use its own set of
  JavaScript builtins (`Array`, `Object`, `JSON`, etc.) and will be isolated
  from any changes made to the global environment by the loaded page. The
  Electron API will only be available in the `preload` script and not the loaded
  page. This option should be used when loading potentially untrusted remote
  content to ensure the loaded content cannot tamper with the `preload` script
  and any Electron APIs being used. This option uses the same technique used by
  [Chrome Content Scripts][chrome-content-scripts]. You can access this context
  in the dev tools by selecting the 'Electron Isolated Context' entry in the
  combo box at the top of the Console tab.
* `nativeWindowOpen` Boolean (optional) - Whether to use native `window.open()`.
  Defaults to `false`. Child windows will always have node integration disabled
  unless `nodeIntegrationInSubFrames` is true. **Note:** This option is
  currently experimental.
* `webviewTag` Boolean (optional) - Whether to enable the [`<webview>`
  tag](webview-tag.md). Defaults to `false`. **Note:** The `preload` script
  configured for the `<webview>` will have node integration enabled when it is
  executed so you should ensure remote/untrusted content is not able to create a
  `<webview>` tag with a possibly malicious `preload` script. You can use the
  `will-attach-webview` event on [webContents](../web-contents.md) to strip away
  the `preload` script and to validate or alter the `<webview>`'s initial
  settings.
* `additionalArguments` String[] (optional) - A list of strings that will be
  appended to `process.argv` in the renderer process of this app.  Useful for
  passing small bits of data down to renderer process preload scripts.
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
