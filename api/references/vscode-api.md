---
# DO NOT TOUCH — Managed by doc writer
ContentId: 8CEBCDF8-4F0A-4C81-A904-3DEA43480EA6
DateApproved: 9/5/2018

VSCodeCommitHash: c6e592b2b5770e40a98cb9c2715a8ef89aec3d74
VSCodeVersion: 1.30.0

# Summarize the whole topic in less than 300 characters for SEO purpose
MetaDescription: Visual Studio Code extensions (plug-in) API Reference.
---

# VS Code API

**VS Code API** is a set of JavaScript APIs that you can invoke in your Visual Studio Code extension. This page lists all VS Code APIs available to extension authors.

## API Patterns

These are some of the common patterns we use in the VS Code API.

### Promises

The VS Code API represents asynchronous operations with [promises](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise). From extensions, __any__ type of promise can be returned, like ES6, WinJS, A+, etc.

Being independent of a specific promise library is expressed in the API by the `Thenable`-type. `Thenable` represents the common denominator which is the [then](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise/then) method.

In most cases the use of promises is optional and when VS Code calls into an extension, it can handle the _result type_ as well as a `Thenable` of the _result type_. When the use of a promise is optional, the API indicates this by returning `or`-types.

```typescript
provideNumber(): number | Thenable<number>
```

### Cancellation Tokens

Often operations are started on volatile state which changes before operations can finish. For instance, computing IntelliSense starts and the user continues to type making the result of that operation obsolete.

APIs that are exposed to such behavior will get passed a `CancellationToken` on which you can check for cancellation (`isCancellationRequested`) or get notified when cancellation occurs (`onCancellationRequested`). The cancellation token is usually the last parameter of a function call and optional.

### Disposables

The VS Code API uses the [dispose pattern](https://en.wikipedia.org/wiki/Dispose_pattern) for resources that are obtained from VS Code. This applies to event listening, commands, interacting with the UI, and various language contributions.

For instance, the `setStatusBarMessage(value: string)` function returns a `Disposable` which upon calling `dispose` removes the message again.

### Events

Events in the VS Code API are exposed as functions which you call with a listener-function to subscribe. Calls to subscribe return a `Disposable` which removes the event listener upon dispose.

```javascript
var listener = function(event) {
    console.log("It happened", event);
};

// start listening
var subscription = fsWatcher.onDidDelete(listener);

// do more stuff

subscription.dispose(); // stop listening
```

Names of events follow the `on[Will|Did]VerbNoun?` pattern. The name signals if the event is going to happen *(onWill)* or already happened *(onDid)*, what happened *(verb)*, and the context *(noun)* unless obvious from the context.

An example from the VS Code API is `window.onDidChangeActiveTextEditor` which is an event fired when the active text editor *(noun)* has been (*onDid*) changed (*verb*).

### Strict null

The VS Code API uses the `undefined` and `null` TypeScript types where appropriate to support [strict null checking](https://github.com/Microsoft/TypeScript/pull/7140).

## API Listing

This listing is compiled from the [`vscode.d.ts`](https://github.com/Microsoft/vscode/blob/master/src/vs/vscode.d.ts) file from the VS Code repository.

## commands



<div class="comment"><p>Namespace for dealing with commands. In short, a command is a function with a
unique identifier. The function is sometimes also called <em>command handler</em>.</p>
<p>Commands can be added to the editor using the <a href="#commands.registerCommand">registerCommand</a>
and <a href="#commands.registerTextEditorCommand">registerTextEditorCommand</a> functions. Commands
can be executed <a href="#commands.executeCommand">manually</a> or from a UI gesture. Those are:</p>
<ul>
<li>palette - Use the <code>commands</code>-section in <code>package.json</code> to make a command show in
the <a href="https://code.visualstudio.com/docs/getstarted/userinterface#_command-palette">command palette</a>.</li>
<li>keybinding - Use the <code>keybindings</code>-section in <code>package.json</code> to enable
<a href="https://code.visualstudio.com/docs/getstarted/keybindings#_customizing-shortcuts">keybindings</a>
for your extension.</li>
</ul>
<p>Commands from other extensions and from the editor itself are accessible to an extension. However,
when invoking an editor command not all argument types are supported.</p>
<p>This is a sample that registers a command handler and adds an entry for that command to the palette. First
register a command handler with the identifier <code>extension.sayHello</code>.</p>

<pre><code class="lang-javascript">commands.registerCommand(&#39;extension.sayHello&#39;, () =&gt; {
    window.showInformationMessage(&#39;Hello World!&#39;);
});
</code></pre>
<p>Second, bind the command identifier to a title under which it will show in the palette (<code>package.json</code>).</p>

<pre><code class="lang-json">{
    &quot;contributes&quot;: {
        &quot;commands&quot;: [{
            &quot;command&quot;: &quot;extension.sayHello&quot;,
            &quot;title&quot;: &quot;Hello World&quot;
        }]
    }
}
</code></pre>
</div>

#### Functions



<a name="commands.executeCommand"></a><span class="ts" id=2043 data-target="#details-2043" data-toggle="collapse"><span class="ident">executeCommand</span><span>&lt;</span>T<span>&gt;</span><span>(</span><span class="ident">command</span><span>: </span><a class="type-intrinsic">string</a>, <span>...</span><span class="ident">rest</span><span>: </span><a class="type-intrinsic">any</a>[]<span>)</span><span>: </span><a class="type-ref" href="#Thenable">Thenable</a>&lt;<a class="type-intrinsic">T</a> &#124; <a class="type-intrinsic">undefined</a>&gt;</span>
<div class="details collapse" id="details-2043">
<div class="comment"><p>Executes the command denoted by the given command identifier.</p>
<ul>
<li><em>Note 1:</em> When executing an editor command not all types are allowed to
be passed as arguments. Allowed are the primitive types <code>string</code>, <code>boolean</code>,
<code>number</code>, <code>undefined</code>, and <code>null</code>, as well as <a href="#Position"><code>Position</code></a>, <a href="#Range"><code>Range</code></a>, <a href="#Uri"><code>Uri</code></a> and <a href="#Location"><code>Location</code></a>.</li>
<li><em>Note 2:</em> There are no restrictions when executing commands that have been contributed
by extensions.</li>
</ul>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="command"></a><span class="ts" id=2045 data-target="#details-2045" data-toggle="collapse"><span class="ident">command</span><span>: </span><a class="type-intrinsic">string</a></span></td><td><div class="comment"><p>Identifier of the command to execute.</p>
</div></td></tr>
<tr><td><a name="rest"></a><span class="ts" id=2046 data-target="#details-2046" data-toggle="collapse"><span>...</span><span class="ident">rest</span><span>: </span><a class="type-intrinsic">any</a>[]</span></td><td><div class="comment"><p>Parameters passed to the command function.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#Thenable">Thenable</a>&lt;<a class="type-intrinsic">T</a> &#124; <a class="type-intrinsic">undefined</a>&gt;</span></td><td><div class="comment"><p>A thenable that resolves to the returned value of the given command. <code>undefined</code> when
the command handler function doesn&#39;t return anything.</p>
</div></td></tr>
</table>
</div>
</div>



<a name="commands.getCommands"></a><span class="ts" id=2048 data-target="#details-2048" data-toggle="collapse"><span class="ident">getCommands</span><span>(</span><span class="ident">filterInternal</span><span>?</span><span>: </span><a class="type-intrinsic">boolean</a><span>)</span><span>: </span><a class="type-ref" href="#Thenable">Thenable</a>&lt;<a class="type-intrinsic">string</a>[]&gt;</span>
<div class="details collapse" id="details-2048">
<div class="comment"><p>Retrieve the list of all available commands. Commands starting an underscore are
treated as internal commands.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="filterInternal"></a><span class="ts" id=2049 data-target="#details-2049" data-toggle="collapse"><span class="ident">filterInternal</span><span>?</span><span>: </span><a class="type-intrinsic">boolean</a></span></td><td><div class="comment"><p>Set <code>true</code> to not see internal commands (starting with an underscore)</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#Thenable">Thenable</a>&lt;<a class="type-intrinsic">string</a>[]&gt;</span></td><td><div class="comment"><p>Thenable that resolves to a list of command ids.</p>
</div></td></tr>
</table>
</div>
</div>



<a name="commands.registerCommand"></a><span class="ts" id=2025 data-target="#details-2025" data-toggle="collapse"><span class="ident">registerCommand</span><span>(</span><span class="ident">command</span><span>: </span><a class="type-intrinsic">string</a>, <span class="ident">callback</span><span>: </span>(args: <a class="type-intrinsic">any</a>[]) =&gt; <a class="type-intrinsic">any</a>, <span class="ident">thisArg</span><span>?</span><span>: </span><a class="type-intrinsic">any</a><span>)</span><span>: </span><a class="type-ref" href="#Disposable">Disposable</a></span>
<div class="details collapse" id="details-2025">
<div class="comment"><p>Registers a command that can be invoked via a keyboard shortcut,
a menu item, an action, or directly.</p>
<p>Registering a command with an existing command identifier twice
will cause an error.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="command"></a><span class="ts" id=2026 data-target="#details-2026" data-toggle="collapse"><span class="ident">command</span><span>: </span><a class="type-intrinsic">string</a></span></td><td><div class="comment"><p>A unique identifier for the command.</p>
</div></td></tr>
<tr><td><a name="callback"></a><span class="ts" id=2027 data-target="#details-2027" data-toggle="collapse"><span class="ident">callback</span><span>: </span>(args: <a class="type-intrinsic">any</a>[]) =&gt; <a class="type-intrinsic">any</a></span></td><td><div class="comment"><p>A command handler function.</p>
</div></td></tr>
<tr><td><a name="thisArg"></a><span class="ts" id=2031 data-target="#details-2031" data-toggle="collapse"><span class="ident">thisArg</span><span>?</span><span>: </span><a class="type-intrinsic">any</a></span></td><td><div class="comment"><p>The <code>this</code> context used when invoking the handler function.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#Disposable">Disposable</a></span></td><td><div class="comment"><p>Disposable which unregisters this command on disposal.</p>
</div></td></tr>
</table>
</div>
</div>



<a name="commands.registerTextEditorCommand"></a><span class="ts" id=2033 data-target="#details-2033" data-toggle="collapse"><span class="ident">registerTextEditorCommand</span><span>(</span><span class="ident">command</span><span>: </span><a class="type-intrinsic">string</a>, <span class="ident">callback</span><span>: </span>(textEditor: <a class="type-ref" href="#TextEditor">TextEditor</a>, edit: <a class="type-ref" href="#TextEditorEdit">TextEditorEdit</a>, args: <a class="type-intrinsic">any</a>[]) =&gt; <a class="type-intrinsic">void</a>, <span class="ident">thisArg</span><span>?</span><span>: </span><a class="type-intrinsic">any</a><span>)</span><span>: </span><a class="type-ref" href="#Disposable">Disposable</a></span>
<div class="details collapse" id="details-2033">
<div class="comment"><p>Registers a text editor command that can be invoked via a keyboard shortcut,
a menu item, an action, or directly.</p>
<p>Text editor commands are different from ordinary <a href="#commands.registerCommand">commands</a> as
they only execute when there is an active editor when the command is called. Also, the
command handler of an editor command has access to the active editor and to an
<a href="#TextEditorEdit">edit</a>-builder.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="command"></a><span class="ts" id=2034 data-target="#details-2034" data-toggle="collapse"><span class="ident">command</span><span>: </span><a class="type-intrinsic">string</a></span></td><td><div class="comment"><p>A unique identifier for the command.</p>
</div></td></tr>
<tr><td><a name="callback"></a><span class="ts" id=2035 data-target="#details-2035" data-toggle="collapse"><span class="ident">callback</span><span>: </span>(textEditor: <a class="type-ref" href="#TextEditor">TextEditor</a>, edit: <a class="type-ref" href="#TextEditorEdit">TextEditorEdit</a>, args: <a class="type-intrinsic">any</a>[]) =&gt; <a class="type-intrinsic">void</a></span></td><td><div class="comment"><p>A command handler function with access to an <a href="#TextEditor">editor</a> and an <a href="#TextEditorEdit">edit</a>.</p>
</div></td></tr>
<tr><td><a name="thisArg"></a><span class="ts" id=2041 data-target="#details-2041" data-toggle="collapse"><span class="ident">thisArg</span><span>?</span><span>: </span><a class="type-intrinsic">any</a></span></td><td><div class="comment"><p>The <code>this</code> context used when invoking the handler function.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#Disposable">Disposable</a></span></td><td><div class="comment"><p>Disposable which unregisters this command on disposal.</p>
</div></td></tr>
</table>
</div>
</div>

## debug



<div class="comment"><p>Namespace for debug functionality.</p>
</div>

#### Variables



<a name="debug.activeDebugConsole"></a><span class="ts" id=2437 data-target="#details-2437" data-toggle="collapse"><span class="ident">activeDebugConsole</span><span>: </span><a class="type-ref" href="#DebugConsole">DebugConsole</a></span>
<div class="details collapse" id="details-2437">
<div class="comment"><p>The currently active <a href="#DebugConsole">debug console</a>.
If no debug session is active, output sent to the debug console is not shown.</p>
</div>
</div>



<a name="debug.activeDebugSession"></a><span class="ts" id=2436 data-target="#details-2436" data-toggle="collapse"><span class="ident">activeDebugSession</span><span>: </span><a class="type-ref" href="#DebugSession">DebugSession</a> &#124; <a class="type-intrinsic">undefined</a></span>
<div class="details collapse" id="details-2436">
<div class="comment"><p>The currently active <a href="#DebugSession">debug session</a> or <code>undefined</code>. The active debug session is the one
represented by the debug action floating window or the one currently shown in the drop down menu of the debug action floating window.
If no debug session is active, the value is <code>undefined</code>.</p>
</div>
</div>



<a name="debug.breakpoints"></a><span class="ts" id=2438 data-target="#details-2438" data-toggle="collapse"><span class="ident">breakpoints</span><span>: </span><a class="type-ref" href="#Breakpoint">Breakpoint</a>[]</span>
<div class="details collapse" id="details-2438">
<div class="comment"><p>List of breakpoints.</p>
</div>
</div>

#### Events



<a name="debug.onDidChangeActiveDebugSession"></a><span class="ts" id=2439 data-target="#details-2439" data-toggle="collapse"><span class="ident">onDidChangeActiveDebugSession</span><span>: </span><a class="type-ref" href="#Event">Event</a>&lt;<a class="type-ref" href="#DebugSession">DebugSession</a> &#124; <a class="type-intrinsic">undefined</a>&gt;</span>
<div class="details collapse" id="details-2439">
<div class="comment"><p>An <a href="#Event">event</a> which fires when the <a href="#debug.activeDebugSession">active debug session</a>
has changed. <em>Note</em> that the event also fires when the active debug session changes
to <code>undefined</code>.</p>
</div>
</div>



<a name="debug.onDidChangeBreakpoints"></a><span class="ts" id=2443 data-target="#details-2443" data-toggle="collapse"><span class="ident">onDidChangeBreakpoints</span><span>: </span><a class="type-ref" href="#Event">Event</a>&lt;<a class="type-ref" href="#BreakpointsChangeEvent">BreakpointsChangeEvent</a>&gt;</span>
<div class="details collapse" id="details-2443">
<div class="comment"><p>An <a href="#Event">event</a> that is emitted when the set of breakpoints is added, removed, or changed.</p>
</div>
</div>



<a name="debug.onDidReceiveDebugSessionCustomEvent"></a><span class="ts" id=2441 data-target="#details-2441" data-toggle="collapse"><span class="ident">onDidReceiveDebugSessionCustomEvent</span><span>: </span><a class="type-ref" href="#Event">Event</a>&lt;<a class="type-ref" href="#DebugSessionCustomEvent">DebugSessionCustomEvent</a>&gt;</span>
<div class="details collapse" id="details-2441">
<div class="comment"><p>An <a href="#Event">event</a> which fires when a custom DAP event is received from the <a href="#DebugSession">debug session</a>.</p>
</div>
</div>



<a name="debug.onDidStartDebugSession"></a><span class="ts" id=2440 data-target="#details-2440" data-toggle="collapse"><span class="ident">onDidStartDebugSession</span><span>: </span><a class="type-ref" href="#Event">Event</a>&lt;<a class="type-ref" href="#DebugSession">DebugSession</a>&gt;</span>
<div class="details collapse" id="details-2440">
<div class="comment"><p>An <a href="#Event">event</a> which fires when a new <a href="#DebugSession">debug session</a> has been started.</p>
</div>
</div>



<a name="debug.onDidTerminateDebugSession"></a><span class="ts" id=2442 data-target="#details-2442" data-toggle="collapse"><span class="ident">onDidTerminateDebugSession</span><span>: </span><a class="type-ref" href="#Event">Event</a>&lt;<a class="type-ref" href="#DebugSession">DebugSession</a>&gt;</span>
<div class="details collapse" id="details-2442">
<div class="comment"><p>An <a href="#Event">event</a> which fires when a <a href="#DebugSession">debug session</a> has terminated.</p>
</div>
</div>

#### Functions



<a name="debug.addBreakpoints"></a><span class="ts" id=2461 data-target="#details-2461" data-toggle="collapse"><span class="ident">addBreakpoints</span><span>(</span><span class="ident">breakpoints</span><span>: </span><a class="type-ref" href="#Breakpoint">Breakpoint</a>[]<span>)</span><span>: </span><a class="type-intrinsic">void</a></span>
<div class="details collapse" id="details-2461">
<div class="comment"><p>Add breakpoints.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="breakpoints"></a><span class="ts" id=2462 data-target="#details-2462" data-toggle="collapse"><span class="ident">breakpoints</span><span>: </span><a class="type-ref" href="#Breakpoint">Breakpoint</a>[]</span></td><td><div class="comment"><p>The breakpoints to add.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-intrinsic">void</a></span></td><td><div class="comment"></div></td></tr>
</table>
</div>
</div>



<a name="debug.registerDebugAdapterDescriptorFactory"></a><span class="ts" id=2449 data-target="#details-2449" data-toggle="collapse"><span class="ident">registerDebugAdapterDescriptorFactory</span><span>(</span><span class="ident">debugType</span><span>: </span><a class="type-intrinsic">string</a>, <span class="ident">factory</span><span>: </span><a class="type-ref" href="#DebugAdapterDescriptorFactory">DebugAdapterDescriptorFactory</a><span>)</span><span>: </span><a class="type-ref" href="#Disposable">Disposable</a></span>
<div class="details collapse" id="details-2449">
<div class="comment"><p>Register a <a href="#DebugAdapterDescriptorFactory">debug adapter descriptor factory</a> for a specific debug type.
An extension is only allowed to register a DebugAdapterDescriptorFactory for the debug type(s) defined by the extension. Otherwise an error is thrown.
Registering more than one DebugAdapterDescriptorFactory for a debug type results in an error.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="debugType"></a><span class="ts" id=2450 data-target="#details-2450" data-toggle="collapse"><span class="ident">debugType</span><span>: </span><a class="type-intrinsic">string</a></span></td><td><div class="comment"><p>The debug type for which the factory is registered.</p>
</div></td></tr>
<tr><td><a name="factory"></a><span class="ts" id=2451 data-target="#details-2451" data-toggle="collapse"><span class="ident">factory</span><span>: </span><a class="type-ref" href="#DebugAdapterDescriptorFactory">DebugAdapterDescriptorFactory</a></span></td><td><div class="comment"><p>The <a href="#DebugAdapterDescriptorFactory">debug adapter descriptor factory</a> to register.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#Disposable">Disposable</a></span></td><td><div class="comment"><p>A <a href="#Disposable">disposable</a> that unregisters this factory when being disposed.</p>
</div></td></tr>
</table>
</div>
</div>



<a name="debug.registerDebugAdapterTrackerFactory"></a><span class="ts" id=2453 data-target="#details-2453" data-toggle="collapse"><span class="ident">registerDebugAdapterTrackerFactory</span><span>(</span><span class="ident">debugType</span><span>: </span><a class="type-intrinsic">string</a>, <span class="ident">factory</span><span>: </span><a class="type-ref" href="#DebugAdapterTrackerFactory">DebugAdapterTrackerFactory</a><span>)</span><span>: </span><a class="type-ref" href="#Disposable">Disposable</a></span>
<div class="details collapse" id="details-2453">
<div class="comment"><p>Register a debug adapter tracker factory for the given debug type.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="debugType"></a><span class="ts" id=2454 data-target="#details-2454" data-toggle="collapse"><span class="ident">debugType</span><span>: </span><a class="type-intrinsic">string</a></span></td><td><div class="comment"><p>The debug type for which the factory is registered or &#39;*&#39; for matching all debug types.</p>
</div></td></tr>
<tr><td><a name="factory"></a><span class="ts" id=2455 data-target="#details-2455" data-toggle="collapse"><span class="ident">factory</span><span>: </span><a class="type-ref" href="#DebugAdapterTrackerFactory">DebugAdapterTrackerFactory</a></span></td><td><div class="comment"><p>The <a href="#DebugAdapterTrackerFactory">debug adapter tracker factory</a> to register.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#Disposable">Disposable</a></span></td><td><div class="comment"><p>A <a href="#Disposable">disposable</a> that unregisters this factory when being disposed.</p>
</div></td></tr>
</table>
</div>
</div>



<a name="debug.registerDebugConfigurationProvider"></a><span class="ts" id=2445 data-target="#details-2445" data-toggle="collapse"><span class="ident">registerDebugConfigurationProvider</span><span>(</span><span class="ident">debugType</span><span>: </span><a class="type-intrinsic">string</a>, <span class="ident">provider</span><span>: </span><a class="type-ref" href="#DebugConfigurationProvider">DebugConfigurationProvider</a><span>)</span><span>: </span><a class="type-ref" href="#Disposable">Disposable</a></span>
<div class="details collapse" id="details-2445">
<div class="comment"><p>Register a <a href="#DebugConfigurationProvider">debug configuration provider</a> for a specific debug type.
More than one provider can be registered for the same type.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="debugType"></a><span class="ts" id=2446 data-target="#details-2446" data-toggle="collapse"><span class="ident">debugType</span><span>: </span><a class="type-intrinsic">string</a></span></td><td><div class="comment"></div></td></tr>
<tr><td><a name="provider"></a><span class="ts" id=2447 data-target="#details-2447" data-toggle="collapse"><span class="ident">provider</span><span>: </span><a class="type-ref" href="#DebugConfigurationProvider">DebugConfigurationProvider</a></span></td><td><div class="comment"><p>The <a href="#DebugConfigurationProvider">debug configuration provider</a> to register.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#Disposable">Disposable</a></span></td><td><div class="comment"><p>A <a href="#Disposable">disposable</a> that unregisters this provider when being disposed.</p>
</div></td></tr>
</table>
</div>
</div>



<a name="debug.removeBreakpoints"></a><span class="ts" id=2464 data-target="#details-2464" data-toggle="collapse"><span class="ident">removeBreakpoints</span><span>(</span><span class="ident">breakpoints</span><span>: </span><a class="type-ref" href="#Breakpoint">Breakpoint</a>[]<span>)</span><span>: </span><a class="type-intrinsic">void</a></span>
<div class="details collapse" id="details-2464">
<div class="comment"><p>Remove breakpoints.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="breakpoints"></a><span class="ts" id=2465 data-target="#details-2465" data-toggle="collapse"><span class="ident">breakpoints</span><span>: </span><a class="type-ref" href="#Breakpoint">Breakpoint</a>[]</span></td><td><div class="comment"><p>The breakpoints to remove.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-intrinsic">void</a></span></td><td><div class="comment"></div></td></tr>
</table>
</div>
</div>



<a name="debug.startDebugging"></a><span class="ts" id=2457 data-target="#details-2457" data-toggle="collapse"><span class="ident">startDebugging</span><span>(</span><span class="ident">folder</span><span>: </span><a class="type-ref" href="#WorkspaceFolder">WorkspaceFolder</a> &#124; <a class="type-intrinsic">undefined</a>, <span class="ident">nameOrConfiguration</span><span>: </span><a class="type-intrinsic">string</a> &#124; <a class="type-ref" href="#DebugConfiguration">DebugConfiguration</a><span>)</span><span>: </span><a class="type-ref" href="#Thenable">Thenable</a>&lt;<a class="type-intrinsic">boolean</a>&gt;</span>
<div class="details collapse" id="details-2457">
<div class="comment"><p>Start debugging by using either a named launch or named compound configuration,
or by directly passing a <a href="#DebugConfiguration">DebugConfiguration</a>.
The named configurations are looked up in &#39;.vscode/launch.json&#39; found in the given folder.
Before debugging starts, all unsaved files are saved and the launch configurations are brought up-to-date.
Folder specific variables used in the configuration (e.g. &#39;${workspaceFolder}&#39;) are resolved against the given folder.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="folder"></a><span class="ts" id=2458 data-target="#details-2458" data-toggle="collapse"><span class="ident">folder</span><span>: </span><a class="type-ref" href="#WorkspaceFolder">WorkspaceFolder</a> &#124; <a class="type-intrinsic">undefined</a></span></td><td><div class="comment"><p>The <a href="#WorkspaceFolder">workspace folder</a> for looking up named configurations and resolving variables or <code>undefined</code> for a non-folder setup.</p>
</div></td></tr>
<tr><td><a name="nameOrConfiguration"></a><span class="ts" id=2459 data-target="#details-2459" data-toggle="collapse"><span class="ident">nameOrConfiguration</span><span>: </span><a class="type-intrinsic">string</a> &#124; <a class="type-ref" href="#DebugConfiguration">DebugConfiguration</a></span></td><td><div class="comment"><p>Either the name of a debug or compound configuration or a <a href="#DebugConfiguration">DebugConfiguration</a> object.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#Thenable">Thenable</a>&lt;<a class="type-intrinsic">boolean</a>&gt;</span></td><td><div class="comment"><p>A thenable that resolves when debugging could be successfully started.</p>
</div></td></tr>
</table>
</div>
</div>

## env



<div class="comment"><p>Namespace describing the environment the editor runs in.</p>
</div>

#### Variables



<a name="env.appName"></a><span class="ts" id=2017 data-target="#details-2017" data-toggle="collapse"><span class="ident">appName</span><span>: </span><a class="type-intrinsic">string</a></span>
<div class="details collapse" id="details-2017">
<div class="comment"><p>The application name of the editor, like &#39;VS Code&#39;.</p>
<ul>
<li><em>readonly</em></li>
</ul>
</div>
</div>



<a name="env.appRoot"></a><span class="ts" id=2018 data-target="#details-2018" data-toggle="collapse"><span class="ident">appRoot</span><span>: </span><a class="type-intrinsic">string</a></span>
<div class="details collapse" id="details-2018">
<div class="comment"><p>The application root folder from which the editor is running.</p>
<ul>
<li><em>readonly</em></li>
</ul>
</div>
</div>



<a name="env.clipboard"></a><span class="ts" id=2020 data-target="#details-2020" data-toggle="collapse"><span class="ident">clipboard</span><span>: </span><a class="type-ref" href="#Clipboard">Clipboard</a></span>
<div class="details collapse" id="details-2020">
<div class="comment"><p>The system clipboard.</p>
</div>
</div>



<a name="env.language"></a><span class="ts" id=2019 data-target="#details-2019" data-toggle="collapse"><span class="ident">language</span><span>: </span><a class="type-intrinsic">string</a></span>
<div class="details collapse" id="details-2019">
<div class="comment"><p>Represents the preferred user-language, like <code>de-CH</code>, <code>fr</code>, or <code>en-US</code>.</p>
<ul>
<li><em>readonly</em></li>
</ul>
</div>
</div>



<a name="env.machineId"></a><span class="ts" id=2021 data-target="#details-2021" data-toggle="collapse"><span class="ident">machineId</span><span>: </span><a class="type-intrinsic">string</a></span>
<div class="details collapse" id="details-2021">
<div class="comment"><p>A unique identifier for the computer.</p>
<ul>
<li><em>readonly</em></li>
</ul>
</div>
</div>



<a name="env.sessionId"></a><span class="ts" id=2022 data-target="#details-2022" data-toggle="collapse"><span class="ident">sessionId</span><span>: </span><a class="type-intrinsic">string</a></span>
<div class="details collapse" id="details-2022">
<div class="comment"><p>A unique identifier for the current session.
Changes each time the editor is started.</p>
<ul>
<li><em>readonly</em></li>
</ul>
</div>
</div>

## extensions



<div class="comment"><p>Namespace for dealing with installed extensions. Extensions are represented
by an <a href="#Extension">extension</a>-interface which enables reflection on them.</p>
<p>Extension writers can provide APIs to other extensions by returning their API public
surface from the <code>activate</code>-call.</p>

<pre><code class="lang-javascript">export function activate(context: vscode.ExtensionContext) {
    let api = {
        sum(a, b) {
            return a + b;
        },
        mul(a, b) {
            return a * b;
        }
    };
    // &#39;export&#39; public api-surface
    return api;
}
</code></pre>
<p>When depending on the API of another extension add an <code>extensionDependency</code>-entry
to <code>package.json</code>, and use the <a href="#extensions.getExtension">getExtension</a>-function
and the <a href="#Extension.exports">exports</a>-property, like below:</p>

<pre><code class="lang-javascript">let mathExt = extensions.getExtension(&#39;genius.math&#39;);
let importedApi = mathExt.exports;

console.log(importedApi.mul(42, 1));
</code></pre>
</div>

#### Variables



<a name="extensions.all"></a><span class="ts" id=2473 data-target="#details-2473" data-toggle="collapse"><span class="ident">all</span><span>: </span><a class="type-ref" href="#Extension">Extension</a>&lt;<a class="type-intrinsic">any</a>&gt;[]</span>
<div class="details collapse" id="details-2473">
<div class="comment"><p>All extensions currently known to the system.</p>
</div>
</div>

#### Functions



<a name="extensions.getExtension"></a><span class="ts" id=2468 data-target="#details-2468" data-toggle="collapse"><span class="ident">getExtension</span><span>(</span><span class="ident">extensionId</span><span>: </span><a class="type-intrinsic">string</a><span>)</span><span>: </span><a class="type-ref" href="#Extension">Extension</a>&lt;<a class="type-intrinsic">any</a>&gt; &#124; <a class="type-intrinsic">undefined</a></span>
<div class="details collapse" id="details-2468">
<div class="comment"><p>Get an extension by its full identifier in the form of: <code>publisher.name</code>.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="extensionId"></a><span class="ts" id=2469 data-target="#details-2469" data-toggle="collapse"><span class="ident">extensionId</span><span>: </span><a class="type-intrinsic">string</a></span></td><td><div class="comment"><p>An extension identifier.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#Extension">Extension</a>&lt;<a class="type-intrinsic">any</a>&gt; &#124; <a class="type-intrinsic">undefined</a></span></td><td><div class="comment"><p>An extension or <code>undefined</code>.</p>
</div></td></tr>
</table>
</div>
</div>



<a name="extensions.getExtension"></a><span class="ts" id=2470 data-target="#details-2470" data-toggle="collapse"><span class="ident">getExtension</span><span>&lt;</span>T<span>&gt;</span><span>(</span><span class="ident">extensionId</span><span>: </span><a class="type-intrinsic">string</a><span>)</span><span>: </span><a class="type-ref" href="#Extension">Extension</a>&lt;<a class="type-intrinsic">T</a>&gt; &#124; <a class="type-intrinsic">undefined</a></span>
<div class="details collapse" id="details-2470">
<div class="comment"><p>Get an extension its full identifier in the form of: <code>publisher.name</code>.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="extensionId"></a><span class="ts" id=2472 data-target="#details-2472" data-toggle="collapse"><span class="ident">extensionId</span><span>: </span><a class="type-intrinsic">string</a></span></td><td><div class="comment"><p>An extension identifier.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#Extension">Extension</a>&lt;<a class="type-intrinsic">T</a>&gt; &#124; <a class="type-intrinsic">undefined</a></span></td><td><div class="comment"><p>An extension or <code>undefined</code>.</p>
</div></td></tr>
</table>
</div>
</div>

## languages



<div class="comment"><p>Namespace for participating in language-specific editor <a href="https://code.visualstudio.com/docs/editor/editingevolved">features</a>,
like IntelliSense, code actions, diagnostics etc.</p>
<p>Many programming languages exist and there is huge variety in syntaxes, semantics, and paradigms. Despite that, features
like automatic word-completion, code navigation, or code checking have become popular across different tools for different
programming languages.</p>
<p>The editor provides an API that makes it simple to provide such common features by having all UI and actions already in place and
by allowing you to participate by providing data only. For instance, to contribute a hover all you have to do is provide a function
that can be called with a <a href="#TextDocument">TextDocument</a> and a <a href="#Position">Position</a> returning hover info. The rest, like tracking the
mouse, positioning the hover, keeping the hover stable etc. is taken care of by the editor.</p>

<pre><code class="lang-javascript">languages.registerHoverProvider(&#39;javascript&#39;, {
    provideHover(document, position, token) {
        return new Hover(&#39;I am a hover!&#39;);
    }
});
</code></pre>
<p>Registration is done using a <a href="#DocumentSelector">document selector</a> which is either a language id, like <code>javascript</code> or
a more complex <a href="#DocumentFilter">filter</a> like <code>{ language: &#39;typescript&#39;, scheme: &#39;file&#39; }</code>. Matching a document against such
a selector will result in a <a href="#languages.match">score</a> that is used to determine if and how a provider shall be used. When
scores are equal the provider that came last wins. For features that allow full arity, like <a href="#languages.registerHoverProvider">hover</a>,
the score is only checked to be <code>&gt;0</code>, for other features, like <a href="#languages.registerCompletionItemProvider">IntelliSense</a> the
score is used for determining the order in which providers are asked to participate.</p>
</div>

#### Events



<a name="languages.onDidChangeDiagnostics"></a><span class="ts" id=2326 data-target="#details-2326" data-toggle="collapse"><span class="ident">onDidChangeDiagnostics</span><span>: </span><a class="type-ref" href="#Event">Event</a>&lt;<a class="type-ref" href="#DiagnosticChangeEvent">DiagnosticChangeEvent</a>&gt;</span>
<div class="details collapse" id="details-2326">
<div class="comment"><p>An <a href="#Event">event</a> which fires when the global set of diagnostics changes. This is
newly added and removed diagnostics.</p>
</div>
</div>

#### Functions



<a name="languages.createDiagnosticCollection"></a><span class="ts" id=2332 data-target="#details-2332" data-toggle="collapse"><span class="ident">createDiagnosticCollection</span><span>(</span><span class="ident">name</span><span>?</span><span>: </span><a class="type-intrinsic">string</a><span>)</span><span>: </span><a class="type-ref" href="#DiagnosticCollection">DiagnosticCollection</a></span>
<div class="details collapse" id="details-2332">
<div class="comment"><p>Create a diagnostics collection.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="name"></a><span class="ts" id=2333 data-target="#details-2333" data-toggle="collapse"><span class="ident">name</span><span>?</span><span>: </span><a class="type-intrinsic">string</a></span></td><td><div class="comment"><p>The <a href="#DiagnosticCollection.name">name</a> of the collection.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#DiagnosticCollection">DiagnosticCollection</a></span></td><td><div class="comment"><p>A new diagnostic collection.</p>
</div></td></tr>
</table>
</div>
</div>



<a name="languages.getDiagnostics"></a><span class="ts" id=2328 data-target="#details-2328" data-toggle="collapse"><span class="ident">getDiagnostics</span><span>(</span><span class="ident">resource</span><span>: </span><a class="type-ref" href="#Uri">Uri</a><span>)</span><span>: </span><a class="type-ref" href="#Diagnostic">Diagnostic</a>[]</span>
<div class="details collapse" id="details-2328">
<div class="comment"><p>Get all diagnostics for a given resource. <em>Note</em> that this includes diagnostics from
all extensions but <em>not yet</em> from the task framework.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="resource"></a><span class="ts" id=2329 data-target="#details-2329" data-toggle="collapse"><span class="ident">resource</span><span>: </span><a class="type-ref" href="#Uri">Uri</a></span></td><td><div class="comment"><p>A resource</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#Diagnostic">Diagnostic</a>[]</span></td><td><div class="comment"><p>An array of <a href="#Diagnostic">diagnostics</a> objects or an empty array.</p>
</div></td></tr>
</table>
</div>
</div>



<a name="languages.getDiagnostics"></a><span class="ts" id=2330 data-target="#details-2330" data-toggle="collapse"><span class="ident">getDiagnostics</span><span>(</span><span>)</span><span>: </span>[<a class="type-ref" href="#Uri">Uri</a>, <a class="type-ref" href="#Diagnostic">Diagnostic</a>[]][]</span>
<div class="details collapse" id="details-2330">
<div class="comment"><p>Get all diagnostics. <em>Note</em> that this includes diagnostics from
all extensions but <em>not yet</em> from the task framework.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts">[<a class="type-ref" href="#Uri">Uri</a>, <a class="type-ref" href="#Diagnostic">Diagnostic</a>[]][]</span></td><td><div class="comment"><p>An array of uri-diagnostics tuples or an empty array.</p>
</div></td></tr>
</table>
</div>
</div>



<a name="languages.getLanguages"></a><span class="ts" id=2317 data-target="#details-2317" data-toggle="collapse"><span class="ident">getLanguages</span><span>(</span><span>)</span><span>: </span><a class="type-ref" href="#Thenable">Thenable</a>&lt;<a class="type-intrinsic">string</a>[]&gt;</span>
<div class="details collapse" id="details-2317">
<div class="comment"><p>Return the identifiers of all known languages.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#Thenable">Thenable</a>&lt;<a class="type-intrinsic">string</a>[]&gt;</span></td><td><div class="comment"><p>Promise resolving to an array of identifier strings.</p>
</div></td></tr>
</table>
</div>
</div>



<a name="languages.match"></a><span class="ts" id=2323 data-target="#details-2323" data-toggle="collapse"><span class="ident">match</span><span>(</span><span class="ident">selector</span><span>: </span><a class="type-ref" href="#DocumentSelector">DocumentSelector</a>, <span class="ident">document</span><span>: </span><a class="type-ref" href="#TextDocument">TextDocument</a><span>)</span><span>: </span><a class="type-intrinsic">number</a></span>
<div class="details collapse" id="details-2323">
<div class="comment"><p>Compute the match between a document <a href="#DocumentSelector">selector</a> and a document. Values
greater than zero mean the selector matches the document.</p>
<p>A match is computed according to these rules:</p>
<ol>
<li>When <a href="#DocumentSelector"><code>DocumentSelector</code></a> is an array, compute the match for each contained <code>DocumentFilter</code> or language identifier and take the maximum value.</li>
<li>A string will be desugared to become the <code>language</code>-part of a <a href="#DocumentFilter"><code>DocumentFilter</code></a>, so <code>&quot;fooLang&quot;</code> is like <code>{ language: &quot;fooLang&quot; }</code>.</li>
<li>A <a href="#DocumentFilter"><code>DocumentFilter</code></a> will be matched against the document by comparing its parts with the document. The following rules apply:<ol>
<li>When the <code>DocumentFilter</code> is empty (<code>{}</code>) the result is <code>0</code></li>
<li>When <code>scheme</code>, <code>language</code>, or <code>pattern</code> are defined but one doesn’t match, the result is <code>0</code></li>
<li>Matching against <code>*</code> gives a score of <code>5</code>, matching via equality or via a glob-pattern gives a score of <code>10</code></li>
<li>The result is the maximum value of each match</li>
</ol>
</li>
</ol>
<p>Samples:</p>

<pre><code class="lang-js">// default document from disk (file-scheme)
doc.uri; //&#39;file:///my/file.js&#39;
doc.languageId; // &#39;javascript&#39;
match(&#39;javascript&#39;, doc); // 10;
match({language: &#39;javascript&#39;}, doc); // 10;
match({language: &#39;javascript&#39;, scheme: &#39;file&#39;}, doc); // 10;
match(&#39;*&#39;, doc); // 5
match(&#39;fooLang&#39;, doc); // 0
match([&#39;fooLang&#39;, &#39;*&#39;], doc); // 5

// virtual document, e.g. from git-index
doc.uri; // &#39;git:/my/file.js&#39;
doc.languageId; // &#39;javascript&#39;
match(&#39;javascript&#39;, doc); // 10;
match({language: &#39;javascript&#39;, scheme: &#39;git&#39;}, doc); // 10;
match(&#39;*&#39;, doc); // 5
</code></pre>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="selector"></a><span class="ts" id=2324 data-target="#details-2324" data-toggle="collapse"><span class="ident">selector</span><span>: </span><a class="type-ref" href="#DocumentSelector">DocumentSelector</a></span></td><td><div class="comment"><p>A document selector.</p>
</div></td></tr>
<tr><td><a name="document"></a><span class="ts" id=2325 data-target="#details-2325" data-toggle="collapse"><span class="ident">document</span><span>: </span><a class="type-ref" href="#TextDocument">TextDocument</a></span></td><td><div class="comment"><p>A text document.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-intrinsic">number</a></span></td><td><div class="comment"><p>A number <code>&gt;0</code> when the selector matches and <code>0</code> when the selector does not match.</p>
</div></td></tr>
</table>
</div>
</div>



<a name="languages.registerCodeActionsProvider"></a><span class="ts" id=2340 data-target="#details-2340" data-toggle="collapse"><span class="ident">registerCodeActionsProvider</span><span>(</span><span class="ident">selector</span><span>: </span><a class="type-ref" href="#DocumentSelector">DocumentSelector</a>, <span class="ident">provider</span><span>: </span><a class="type-ref" href="#CodeActionProvider">CodeActionProvider</a>, <span class="ident">metadata</span><span>?</span><span>: </span><a class="type-ref" href="#CodeActionProviderMetadata">CodeActionProviderMetadata</a><span>)</span><span>: </span><a class="type-ref" href="#Disposable">Disposable</a></span>
<div class="details collapse" id="details-2340">
<div class="comment"><p>Register a code action provider.</p>
<p>Multiple providers can be registered for a language. In that case providers are asked in
parallel and the results are merged. A failing provider (rejected promise or exception) will
not cause a failure of the whole operation.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="selector"></a><span class="ts" id=2341 data-target="#details-2341" data-toggle="collapse"><span class="ident">selector</span><span>: </span><a class="type-ref" href="#DocumentSelector">DocumentSelector</a></span></td><td><div class="comment"><p>A selector that defines the documents this provider is applicable to.</p>
</div></td></tr>
<tr><td><a name="provider"></a><span class="ts" id=2342 data-target="#details-2342" data-toggle="collapse"><span class="ident">provider</span><span>: </span><a class="type-ref" href="#CodeActionProvider">CodeActionProvider</a></span></td><td><div class="comment"><p>A code action provider.</p>
</div></td></tr>
<tr><td><a name="metadata"></a><span class="ts" id=2343 data-target="#details-2343" data-toggle="collapse"><span class="ident">metadata</span><span>?</span><span>: </span><a class="type-ref" href="#CodeActionProviderMetadata">CodeActionProviderMetadata</a></span></td><td><div class="comment"><p>Metadata about the kind of code actions the provider providers.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#Disposable">Disposable</a></span></td><td><div class="comment"><p>A <a href="#Disposable">disposable</a> that unregisters this provider when being disposed.</p>
</div></td></tr>
</table>
</div>
</div>



<a name="languages.registerCodeLensProvider"></a><span class="ts" id=2345 data-target="#details-2345" data-toggle="collapse"><span class="ident">registerCodeLensProvider</span><span>(</span><span class="ident">selector</span><span>: </span><a class="type-ref" href="#DocumentSelector">DocumentSelector</a>, <span class="ident">provider</span><span>: </span><a class="type-ref" href="#CodeLensProvider">CodeLensProvider</a><span>)</span><span>: </span><a class="type-ref" href="#Disposable">Disposable</a></span>
<div class="details collapse" id="details-2345">
<div class="comment"><p>Register a code lens provider.</p>
<p>Multiple providers can be registered for a language. In that case providers are asked in
parallel and the results are merged. A failing provider (rejected promise or exception) will
not cause a failure of the whole operation.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="selector"></a><span class="ts" id=2346 data-target="#details-2346" data-toggle="collapse"><span class="ident">selector</span><span>: </span><a class="type-ref" href="#DocumentSelector">DocumentSelector</a></span></td><td><div class="comment"><p>A selector that defines the documents this provider is applicable to.</p>
</div></td></tr>
<tr><td><a name="provider"></a><span class="ts" id=2347 data-target="#details-2347" data-toggle="collapse"><span class="ident">provider</span><span>: </span><a class="type-ref" href="#CodeLensProvider">CodeLensProvider</a></span></td><td><div class="comment"><p>A code lens provider.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#Disposable">Disposable</a></span></td><td><div class="comment"><p>A <a href="#Disposable">disposable</a> that unregisters this provider when being disposed.</p>
</div></td></tr>
</table>
</div>
</div>



<a name="languages.registerColorProvider"></a><span class="ts" id=2416 data-target="#details-2416" data-toggle="collapse"><span class="ident">registerColorProvider</span><span>(</span><span class="ident">selector</span><span>: </span><a class="type-ref" href="#DocumentSelector">DocumentSelector</a>, <span class="ident">provider</span><span>: </span><a class="type-ref" href="#DocumentColorProvider">DocumentColorProvider</a><span>)</span><span>: </span><a class="type-ref" href="#Disposable">Disposable</a></span>
<div class="details collapse" id="details-2416">
<div class="comment"><p>Register a color provider.</p>
<p>Multiple providers can be registered for a language. In that case providers are asked in
parallel and the results are merged. A failing provider (rejected promise or exception) will
not cause a failure of the whole operation.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="selector"></a><span class="ts" id=2417 data-target="#details-2417" data-toggle="collapse"><span class="ident">selector</span><span>: </span><a class="type-ref" href="#DocumentSelector">DocumentSelector</a></span></td><td><div class="comment"><p>A selector that defines the documents this provider is applicable to.</p>
</div></td></tr>
<tr><td><a name="provider"></a><span class="ts" id=2418 data-target="#details-2418" data-toggle="collapse"><span class="ident">provider</span><span>: </span><a class="type-ref" href="#DocumentColorProvider">DocumentColorProvider</a></span></td><td><div class="comment"><p>A color provider.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#Disposable">Disposable</a></span></td><td><div class="comment"><p>A <a href="#Disposable">disposable</a> that unregisters this provider when being disposed.</p>
</div></td></tr>
</table>
</div>
</div>



<a name="languages.registerCompletionItemProvider"></a><span class="ts" id=2335 data-target="#details-2335" data-toggle="collapse"><span class="ident">registerCompletionItemProvider</span><span>(</span><span class="ident">selector</span><span>: </span><a class="type-ref" href="#DocumentSelector">DocumentSelector</a>, <span class="ident">provider</span><span>: </span><a class="type-ref" href="#CompletionItemProvider">CompletionItemProvider</a>, <span>...</span><span class="ident">triggerCharacters</span><span>: </span><a class="type-intrinsic">string</a>[]<span>)</span><span>: </span><a class="type-ref" href="#Disposable">Disposable</a></span>
<div class="details collapse" id="details-2335">
<div class="comment"><p>Register a completion provider.</p>
<p>Multiple providers can be registered for a language. In that case providers are sorted
by their <a href="#languages.match">score</a> and groups of equal score are sequentially asked for
completion items. The process stops when one or many providers of a group return a
result. A failing provider (rejected promise or exception) will not fail the whole
operation.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="selector"></a><span class="ts" id=2336 data-target="#details-2336" data-toggle="collapse"><span class="ident">selector</span><span>: </span><a class="type-ref" href="#DocumentSelector">DocumentSelector</a></span></td><td><div class="comment"><p>A selector that defines the documents this provider is applicable to.</p>
</div></td></tr>
<tr><td><a name="provider"></a><span class="ts" id=2337 data-target="#details-2337" data-toggle="collapse"><span class="ident">provider</span><span>: </span><a class="type-ref" href="#CompletionItemProvider">CompletionItemProvider</a></span></td><td><div class="comment"><p>A completion provider.</p>
</div></td></tr>
<tr><td><a name="triggerCharacters"></a><span class="ts" id=2338 data-target="#details-2338" data-toggle="collapse"><span>...</span><span class="ident">triggerCharacters</span><span>: </span><a class="type-intrinsic">string</a>[]</span></td><td><div class="comment"><p>Trigger completion when the user types one of the characters, like <code>.</code> or <code>:</code>.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#Disposable">Disposable</a></span></td><td><div class="comment"><p>A <a href="#Disposable">disposable</a> that unregisters this provider when being disposed.</p>
</div></td></tr>
</table>
</div>
</div>



<a name="languages.registerDeclarationProvider"></a><span class="ts" id=2361 data-target="#details-2361" data-toggle="collapse"><span class="ident">registerDeclarationProvider</span><span>(</span><span class="ident">selector</span><span>: </span><a class="type-ref" href="#DocumentSelector">DocumentSelector</a>, <span class="ident">provider</span><span>: </span><a class="type-ref" href="#DeclarationProvider">DeclarationProvider</a><span>)</span><span>: </span><a class="type-ref" href="#Disposable">Disposable</a></span>
<div class="details collapse" id="details-2361">
<div class="comment"><p>Register a declaration provider.</p>
<p>Multiple providers can be registered for a language. In that case providers are asked in
parallel and the results are merged. A failing provider (rejected promise or exception) will
not cause a failure of the whole operation.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="selector"></a><span class="ts" id=2362 data-target="#details-2362" data-toggle="collapse"><span class="ident">selector</span><span>: </span><a class="type-ref" href="#DocumentSelector">DocumentSelector</a></span></td><td><div class="comment"><p>A selector that defines the documents this provider is applicable to.</p>
</div></td></tr>
<tr><td><a name="provider"></a><span class="ts" id=2363 data-target="#details-2363" data-toggle="collapse"><span class="ident">provider</span><span>: </span><a class="type-ref" href="#DeclarationProvider">DeclarationProvider</a></span></td><td><div class="comment"><p>A declaration provider.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#Disposable">Disposable</a></span></td><td><div class="comment"><p>A <a href="#Disposable">disposable</a> that unregisters this provider when being disposed.</p>
</div></td></tr>
</table>
</div>
</div>



<a name="languages.registerDefinitionProvider"></a><span class="ts" id=2349 data-target="#details-2349" data-toggle="collapse"><span class="ident">registerDefinitionProvider</span><span>(</span><span class="ident">selector</span><span>: </span><a class="type-ref" href="#DocumentSelector">DocumentSelector</a>, <span class="ident">provider</span><span>: </span><a class="type-ref" href="#DefinitionProvider">DefinitionProvider</a><span>)</span><span>: </span><a class="type-ref" href="#Disposable">Disposable</a></span>
<div class="details collapse" id="details-2349">
<div class="comment"><p>Register a definition provider.</p>
<p>Multiple providers can be registered for a language. In that case providers are asked in
parallel and the results are merged. A failing provider (rejected promise or exception) will
not cause a failure of the whole operation.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="selector"></a><span class="ts" id=2350 data-target="#details-2350" data-toggle="collapse"><span class="ident">selector</span><span>: </span><a class="type-ref" href="#DocumentSelector">DocumentSelector</a></span></td><td><div class="comment"><p>A selector that defines the documents this provider is applicable to.</p>
</div></td></tr>
<tr><td><a name="provider"></a><span class="ts" id=2351 data-target="#details-2351" data-toggle="collapse"><span class="ident">provider</span><span>: </span><a class="type-ref" href="#DefinitionProvider">DefinitionProvider</a></span></td><td><div class="comment"><p>A definition provider.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#Disposable">Disposable</a></span></td><td><div class="comment"><p>A <a href="#Disposable">disposable</a> that unregisters this provider when being disposed.</p>
</div></td></tr>
</table>
</div>
</div>



<a name="languages.registerDocumentFormattingEditProvider"></a><span class="ts" id=2389 data-target="#details-2389" data-toggle="collapse"><span class="ident">registerDocumentFormattingEditProvider</span><span>(</span><span class="ident">selector</span><span>: </span><a class="type-ref" href="#DocumentSelector">DocumentSelector</a>, <span class="ident">provider</span><span>: </span><a class="type-ref" href="#DocumentFormattingEditProvider">DocumentFormattingEditProvider</a><span>)</span><span>: </span><a class="type-ref" href="#Disposable">Disposable</a></span>
<div class="details collapse" id="details-2389">
<div class="comment"><p>Register a formatting provider for a document.</p>
<p>Multiple providers can be registered for a language. In that case providers are sorted
by their <a href="#languages.match">score</a> and the best-matching provider is used. Failure
of the selected provider will cause a failure of the whole operation.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="selector"></a><span class="ts" id=2390 data-target="#details-2390" data-toggle="collapse"><span class="ident">selector</span><span>: </span><a class="type-ref" href="#DocumentSelector">DocumentSelector</a></span></td><td><div class="comment"><p>A selector that defines the documents this provider is applicable to.</p>
</div></td></tr>
<tr><td><a name="provider"></a><span class="ts" id=2391 data-target="#details-2391" data-toggle="collapse"><span class="ident">provider</span><span>: </span><a class="type-ref" href="#DocumentFormattingEditProvider">DocumentFormattingEditProvider</a></span></td><td><div class="comment"><p>A document formatting edit provider.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#Disposable">Disposable</a></span></td><td><div class="comment"><p>A <a href="#Disposable">disposable</a> that unregisters this provider when being disposed.</p>
</div></td></tr>
</table>
</div>
</div>



<a name="languages.registerDocumentHighlightProvider"></a><span class="ts" id=2369 data-target="#details-2369" data-toggle="collapse"><span class="ident">registerDocumentHighlightProvider</span><span>(</span><span class="ident">selector</span><span>: </span><a class="type-ref" href="#DocumentSelector">DocumentSelector</a>, <span class="ident">provider</span><span>: </span><a class="type-ref" href="#DocumentHighlightProvider">DocumentHighlightProvider</a><span>)</span><span>: </span><a class="type-ref" href="#Disposable">Disposable</a></span>
<div class="details collapse" id="details-2369">
<div class="comment"><p>Register a document highlight provider.</p>
<p>Multiple providers can be registered for a language. In that case providers are sorted
by their <a href="#languages.match">score</a> and groups sequentially asked for document highlights.
The process stops when a provider returns a <code>non-falsy</code> or <code>non-failure</code> result.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="selector"></a><span class="ts" id=2370 data-target="#details-2370" data-toggle="collapse"><span class="ident">selector</span><span>: </span><a class="type-ref" href="#DocumentSelector">DocumentSelector</a></span></td><td><div class="comment"><p>A selector that defines the documents this provider is applicable to.</p>
</div></td></tr>
<tr><td><a name="provider"></a><span class="ts" id=2371 data-target="#details-2371" data-toggle="collapse"><span class="ident">provider</span><span>: </span><a class="type-ref" href="#DocumentHighlightProvider">DocumentHighlightProvider</a></span></td><td><div class="comment"><p>A document highlight provider.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#Disposable">Disposable</a></span></td><td><div class="comment"><p>A <a href="#Disposable">disposable</a> that unregisters this provider when being disposed.</p>
</div></td></tr>
</table>
</div>
</div>



<a name="languages.registerDocumentLinkProvider"></a><span class="ts" id=2412 data-target="#details-2412" data-toggle="collapse"><span class="ident">registerDocumentLinkProvider</span><span>(</span><span class="ident">selector</span><span>: </span><a class="type-ref" href="#DocumentSelector">DocumentSelector</a>, <span class="ident">provider</span><span>: </span><a class="type-ref" href="#DocumentLinkProvider">DocumentLinkProvider</a><span>)</span><span>: </span><a class="type-ref" href="#Disposable">Disposable</a></span>
<div class="details collapse" id="details-2412">
<div class="comment"><p>Register a document link provider.</p>
<p>Multiple providers can be registered for a language. In that case providers are asked in
parallel and the results are merged. A failing provider (rejected promise or exception) will
not cause a failure of the whole operation.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="selector"></a><span class="ts" id=2413 data-target="#details-2413" data-toggle="collapse"><span class="ident">selector</span><span>: </span><a class="type-ref" href="#DocumentSelector">DocumentSelector</a></span></td><td><div class="comment"><p>A selector that defines the documents this provider is applicable to.</p>
</div></td></tr>
<tr><td><a name="provider"></a><span class="ts" id=2414 data-target="#details-2414" data-toggle="collapse"><span class="ident">provider</span><span>: </span><a class="type-ref" href="#DocumentLinkProvider">DocumentLinkProvider</a></span></td><td><div class="comment"><p>A document link provider.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#Disposable">Disposable</a></span></td><td><div class="comment"><p>A <a href="#Disposable">disposable</a> that unregisters this provider when being disposed.</p>
</div></td></tr>
</table>
</div>
</div>



<a name="languages.registerDocumentRangeFormattingEditProvider"></a><span class="ts" id=2393 data-target="#details-2393" data-toggle="collapse"><span class="ident">registerDocumentRangeFormattingEditProvider</span><span>(</span><span class="ident">selector</span><span>: </span><a class="type-ref" href="#DocumentSelector">DocumentSelector</a>, <span class="ident">provider</span><span>: </span><a class="type-ref" href="#DocumentRangeFormattingEditProvider">DocumentRangeFormattingEditProvider</a><span>)</span><span>: </span><a class="type-ref" href="#Disposable">Disposable</a></span>
<div class="details collapse" id="details-2393">
<div class="comment"><p>Register a formatting provider for a document range.</p>
<p><em>Note:</em> A document range provider is also a <a href="#DocumentFormattingEditProvider">document formatter</a>
which means there is no need to <a href="#languages.registerDocumentFormattingEditProvider">register</a> a document
formatter when also registering a range provider.</p>
<p>Multiple providers can be registered for a language. In that case providers are sorted
by their <a href="#languages.match">score</a> and the best-matching provider is used. Failure
of the selected provider will cause a failure of the whole operation.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="selector"></a><span class="ts" id=2394 data-target="#details-2394" data-toggle="collapse"><span class="ident">selector</span><span>: </span><a class="type-ref" href="#DocumentSelector">DocumentSelector</a></span></td><td><div class="comment"><p>A selector that defines the documents this provider is applicable to.</p>
</div></td></tr>
<tr><td><a name="provider"></a><span class="ts" id=2395 data-target="#details-2395" data-toggle="collapse"><span class="ident">provider</span><span>: </span><a class="type-ref" href="#DocumentRangeFormattingEditProvider">DocumentRangeFormattingEditProvider</a></span></td><td><div class="comment"><p>A document range formatting edit provider.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#Disposable">Disposable</a></span></td><td><div class="comment"><p>A <a href="#Disposable">disposable</a> that unregisters this provider when being disposed.</p>
</div></td></tr>
</table>
</div>
</div>



<a name="languages.registerDocumentSymbolProvider"></a><span class="ts" id=2373 data-target="#details-2373" data-toggle="collapse"><span class="ident">registerDocumentSymbolProvider</span><span>(</span><span class="ident">selector</span><span>: </span><a class="type-ref" href="#DocumentSelector">DocumentSelector</a>, <span class="ident">provider</span><span>: </span><a class="type-ref" href="#DocumentSymbolProvider">DocumentSymbolProvider</a>, <span class="ident">metaData</span><span>?</span><span>: </span><a class="type-ref" href="#DocumentSymbolProviderMetadata">DocumentSymbolProviderMetadata</a><span>)</span><span>: </span><a class="type-ref" href="#Disposable">Disposable</a></span>
<div class="details collapse" id="details-2373">
<div class="comment"><p>Register a document symbol provider.</p>
<p>Multiple providers can be registered for a language. In that case providers are asked in
parallel and the results are merged. A failing provider (rejected promise or exception) will
not cause a failure of the whole operation.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="selector"></a><span class="ts" id=2374 data-target="#details-2374" data-toggle="collapse"><span class="ident">selector</span><span>: </span><a class="type-ref" href="#DocumentSelector">DocumentSelector</a></span></td><td><div class="comment"><p>A selector that defines the documents this provider is applicable to.</p>
</div></td></tr>
<tr><td><a name="provider"></a><span class="ts" id=2375 data-target="#details-2375" data-toggle="collapse"><span class="ident">provider</span><span>: </span><a class="type-ref" href="#DocumentSymbolProvider">DocumentSymbolProvider</a></span></td><td><div class="comment"><p>A document symbol provider.</p>
</div></td></tr>
<tr><td><a name="metaData"></a><span class="ts" id=2376 data-target="#details-2376" data-toggle="collapse"><span class="ident">metaData</span><span>?</span><span>: </span><a class="type-ref" href="#DocumentSymbolProviderMetadata">DocumentSymbolProviderMetadata</a></span></td><td><div class="comment"><p>metadata about the provider</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#Disposable">Disposable</a></span></td><td><div class="comment"><p>A <a href="#Disposable">disposable</a> that unregisters this provider when being disposed.</p>
</div></td></tr>
</table>
</div>
</div>



<a name="languages.registerFoldingRangeProvider"></a><span class="ts" id=2420 data-target="#details-2420" data-toggle="collapse"><span class="ident">registerFoldingRangeProvider</span><span>(</span><span class="ident">selector</span><span>: </span><a class="type-ref" href="#DocumentSelector">DocumentSelector</a>, <span class="ident">provider</span><span>: </span><a class="type-ref" href="#FoldingRangeProvider">FoldingRangeProvider</a><span>)</span><span>: </span><a class="type-ref" href="#Disposable">Disposable</a></span>
<div class="details collapse" id="details-2420">
<div class="comment"><p>Register a folding range provider.</p>
<p>Multiple providers can be registered for a language. In that case providers are asked in
parallel and the results are merged.
If multiple folding ranges start at the same position, only the range of the first registered provider is used.
If a folding range overlaps with an other range that has a smaller position, it is also ignored.</p>
<p>A failing provider (rejected promise or exception) will
not cause a failure of the whole operation.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="selector"></a><span class="ts" id=2421 data-target="#details-2421" data-toggle="collapse"><span class="ident">selector</span><span>: </span><a class="type-ref" href="#DocumentSelector">DocumentSelector</a></span></td><td><div class="comment"><p>A selector that defines the documents this provider is applicable to.</p>
</div></td></tr>
<tr><td><a name="provider"></a><span class="ts" id=2422 data-target="#details-2422" data-toggle="collapse"><span class="ident">provider</span><span>: </span><a class="type-ref" href="#FoldingRangeProvider">FoldingRangeProvider</a></span></td><td><div class="comment"><p>A folding range provider.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#Disposable">Disposable</a></span></td><td><div class="comment"><p>A <a href="#Disposable">disposable</a> that unregisters this provider when being disposed.</p>
</div></td></tr>
</table>
</div>
</div>



<a name="languages.registerHoverProvider"></a><span class="ts" id=2365 data-target="#details-2365" data-toggle="collapse"><span class="ident">registerHoverProvider</span><span>(</span><span class="ident">selector</span><span>: </span><a class="type-ref" href="#DocumentSelector">DocumentSelector</a>, <span class="ident">provider</span><span>: </span><a class="type-ref" href="#HoverProvider">HoverProvider</a><span>)</span><span>: </span><a class="type-ref" href="#Disposable">Disposable</a></span>
<div class="details collapse" id="details-2365">
<div class="comment"><p>Register a hover provider.</p>
<p>Multiple providers can be registered for a language. In that case providers are asked in
parallel and the results are merged. A failing provider (rejected promise or exception) will
not cause a failure of the whole operation.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="selector"></a><span class="ts" id=2366 data-target="#details-2366" data-toggle="collapse"><span class="ident">selector</span><span>: </span><a class="type-ref" href="#DocumentSelector">DocumentSelector</a></span></td><td><div class="comment"><p>A selector that defines the documents this provider is applicable to.</p>
</div></td></tr>
<tr><td><a name="provider"></a><span class="ts" id=2367 data-target="#details-2367" data-toggle="collapse"><span class="ident">provider</span><span>: </span><a class="type-ref" href="#HoverProvider">HoverProvider</a></span></td><td><div class="comment"><p>A hover provider.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#Disposable">Disposable</a></span></td><td><div class="comment"><p>A <a href="#Disposable">disposable</a> that unregisters this provider when being disposed.</p>
</div></td></tr>
</table>
</div>
</div>



<a name="languages.registerImplementationProvider"></a><span class="ts" id=2353 data-target="#details-2353" data-toggle="collapse"><span class="ident">registerImplementationProvider</span><span>(</span><span class="ident">selector</span><span>: </span><a class="type-ref" href="#DocumentSelector">DocumentSelector</a>, <span class="ident">provider</span><span>: </span><a class="type-ref" href="#ImplementationProvider">ImplementationProvider</a><span>)</span><span>: </span><a class="type-ref" href="#Disposable">Disposable</a></span>
<div class="details collapse" id="details-2353">
<div class="comment"><p>Register an implementation provider.</p>
<p>Multiple providers can be registered for a language. In that case providers are asked in
parallel and the results are merged. A failing provider (rejected promise or exception) will
not cause a failure of the whole operation.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="selector"></a><span class="ts" id=2354 data-target="#details-2354" data-toggle="collapse"><span class="ident">selector</span><span>: </span><a class="type-ref" href="#DocumentSelector">DocumentSelector</a></span></td><td><div class="comment"><p>A selector that defines the documents this provider is applicable to.</p>
</div></td></tr>
<tr><td><a name="provider"></a><span class="ts" id=2355 data-target="#details-2355" data-toggle="collapse"><span class="ident">provider</span><span>: </span><a class="type-ref" href="#ImplementationProvider">ImplementationProvider</a></span></td><td><div class="comment"><p>An implementation provider.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#Disposable">Disposable</a></span></td><td><div class="comment"><p>A <a href="#Disposable">disposable</a> that unregisters this provider when being disposed.</p>
</div></td></tr>
</table>
</div>
</div>



<a name="languages.registerOnTypeFormattingEditProvider"></a><span class="ts" id=2397 data-target="#details-2397" data-toggle="collapse"><span class="ident">registerOnTypeFormattingEditProvider</span><span>(</span><span class="ident">selector</span><span>: </span><a class="type-ref" href="#DocumentSelector">DocumentSelector</a>, <span class="ident">provider</span><span>: </span><a class="type-ref" href="#OnTypeFormattingEditProvider">OnTypeFormattingEditProvider</a>, <span class="ident">firstTriggerCharacter</span><span>: </span><a class="type-intrinsic">string</a>, <span>...</span><span class="ident">moreTriggerCharacter</span><span>: </span><a class="type-intrinsic">string</a>[]<span>)</span><span>: </span><a class="type-ref" href="#Disposable">Disposable</a></span>
<div class="details collapse" id="details-2397">
<div class="comment"><p>Register a formatting provider that works on type. The provider is active when the user enables the setting <code>editor.formatOnType</code>.</p>
<p>Multiple providers can be registered for a language. In that case providers are sorted
by their <a href="#languages.match">score</a> and the best-matching provider is used. Failure
of the selected provider will cause a failure of the whole operation.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="selector"></a><span class="ts" id=2398 data-target="#details-2398" data-toggle="collapse"><span class="ident">selector</span><span>: </span><a class="type-ref" href="#DocumentSelector">DocumentSelector</a></span></td><td><div class="comment"><p>A selector that defines the documents this provider is applicable to.</p>
</div></td></tr>
<tr><td><a name="provider"></a><span class="ts" id=2399 data-target="#details-2399" data-toggle="collapse"><span class="ident">provider</span><span>: </span><a class="type-ref" href="#OnTypeFormattingEditProvider">OnTypeFormattingEditProvider</a></span></td><td><div class="comment"><p>An on type formatting edit provider.</p>
</div></td></tr>
<tr><td><a name="firstTriggerCharacter"></a><span class="ts" id=2400 data-target="#details-2400" data-toggle="collapse"><span class="ident">firstTriggerCharacter</span><span>: </span><a class="type-intrinsic">string</a></span></td><td><div class="comment"><p>A character on which formatting should be triggered, like <code>}</code>.</p>
</div></td></tr>
<tr><td><a name="moreTriggerCharacter"></a><span class="ts" id=2401 data-target="#details-2401" data-toggle="collapse"><span>...</span><span class="ident">moreTriggerCharacter</span><span>: </span><a class="type-intrinsic">string</a>[]</span></td><td><div class="comment"><p>More trigger characters.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#Disposable">Disposable</a></span></td><td><div class="comment"><p>A <a href="#Disposable">disposable</a> that unregisters this provider when being disposed.</p>
</div></td></tr>
</table>
</div>
</div>



<a name="languages.registerReferenceProvider"></a><span class="ts" id=2381 data-target="#details-2381" data-toggle="collapse"><span class="ident">registerReferenceProvider</span><span>(</span><span class="ident">selector</span><span>: </span><a class="type-ref" href="#DocumentSelector">DocumentSelector</a>, <span class="ident">provider</span><span>: </span><a class="type-ref" href="#ReferenceProvider">ReferenceProvider</a><span>)</span><span>: </span><a class="type-ref" href="#Disposable">Disposable</a></span>
<div class="details collapse" id="details-2381">
<div class="comment"><p>Register a reference provider.</p>
<p>Multiple providers can be registered for a language. In that case providers are asked in
parallel and the results are merged. A failing provider (rejected promise or exception) will
not cause a failure of the whole operation.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="selector"></a><span class="ts" id=2382 data-target="#details-2382" data-toggle="collapse"><span class="ident">selector</span><span>: </span><a class="type-ref" href="#DocumentSelector">DocumentSelector</a></span></td><td><div class="comment"><p>A selector that defines the documents this provider is applicable to.</p>
</div></td></tr>
<tr><td><a name="provider"></a><span class="ts" id=2383 data-target="#details-2383" data-toggle="collapse"><span class="ident">provider</span><span>: </span><a class="type-ref" href="#ReferenceProvider">ReferenceProvider</a></span></td><td><div class="comment"><p>A reference provider.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#Disposable">Disposable</a></span></td><td><div class="comment"><p>A <a href="#Disposable">disposable</a> that unregisters this provider when being disposed.</p>
</div></td></tr>
</table>
</div>
</div>



<a name="languages.registerRenameProvider"></a><span class="ts" id=2385 data-target="#details-2385" data-toggle="collapse"><span class="ident">registerRenameProvider</span><span>(</span><span class="ident">selector</span><span>: </span><a class="type-ref" href="#DocumentSelector">DocumentSelector</a>, <span class="ident">provider</span><span>: </span><a class="type-ref" href="#RenameProvider">RenameProvider</a><span>)</span><span>: </span><a class="type-ref" href="#Disposable">Disposable</a></span>
<div class="details collapse" id="details-2385">
<div class="comment"><p>Register a rename provider.</p>
<p>Multiple providers can be registered for a language. In that case providers are sorted
by their <a href="#languages.match">score</a> and the best-matching provider is used. Failure
of the selected provider will cause a failure of the whole operation.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="selector"></a><span class="ts" id=2386 data-target="#details-2386" data-toggle="collapse"><span class="ident">selector</span><span>: </span><a class="type-ref" href="#DocumentSelector">DocumentSelector</a></span></td><td><div class="comment"><p>A selector that defines the documents this provider is applicable to.</p>
</div></td></tr>
<tr><td><a name="provider"></a><span class="ts" id=2387 data-target="#details-2387" data-toggle="collapse"><span class="ident">provider</span><span>: </span><a class="type-ref" href="#RenameProvider">RenameProvider</a></span></td><td><div class="comment"><p>A rename provider.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#Disposable">Disposable</a></span></td><td><div class="comment"><p>A <a href="#Disposable">disposable</a> that unregisters this provider when being disposed.</p>
</div></td></tr>
</table>
</div>
</div>



<a name="languages.registerSignatureHelpProvider"></a><span class="ts" id=2403 data-target="#details-2403" data-toggle="collapse"><span class="ident">registerSignatureHelpProvider</span><span>(</span><span class="ident">selector</span><span>: </span><a class="type-ref" href="#DocumentSelector">DocumentSelector</a>, <span class="ident">provider</span><span>: </span><a class="type-ref" href="#SignatureHelpProvider">SignatureHelpProvider</a>, <span>...</span><span class="ident">triggerCharacters</span><span>: </span><a class="type-intrinsic">string</a>[]<span>)</span><span>: </span><a class="type-ref" href="#Disposable">Disposable</a></span>
<div class="details collapse" id="details-2403">
<div class="comment"><p>Register a signature help provider.</p>
<p>Multiple providers can be registered for a language. In that case providers are sorted
by their <a href="#languages.match">score</a> and called sequentially until a provider returns a
valid result.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="selector"></a><span class="ts" id=2404 data-target="#details-2404" data-toggle="collapse"><span class="ident">selector</span><span>: </span><a class="type-ref" href="#DocumentSelector">DocumentSelector</a></span></td><td><div class="comment"><p>A selector that defines the documents this provider is applicable to.</p>
</div></td></tr>
<tr><td><a name="provider"></a><span class="ts" id=2405 data-target="#details-2405" data-toggle="collapse"><span class="ident">provider</span><span>: </span><a class="type-ref" href="#SignatureHelpProvider">SignatureHelpProvider</a></span></td><td><div class="comment"><p>A signature help provider.</p>
</div></td></tr>
<tr><td><a name="triggerCharacters"></a><span class="ts" id=2406 data-target="#details-2406" data-toggle="collapse"><span>...</span><span class="ident">triggerCharacters</span><span>: </span><a class="type-intrinsic">string</a>[]</span></td><td><div class="comment"><p>Trigger signature help when the user types one of the characters, like <code>,</code> or <code>(</code>.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#Disposable">Disposable</a></span></td><td><div class="comment"><p>A <a href="#Disposable">disposable</a> that unregisters this provider when being disposed.</p>
</div></td></tr>
</table>
</div>
</div>



<a name="languages.registerSignatureHelpProvider"></a><span class="ts" id=2407 data-target="#details-2407" data-toggle="collapse"><span class="ident">registerSignatureHelpProvider</span><span>(</span><span class="ident">selector</span><span>: </span><a class="type-ref" href="#DocumentSelector">DocumentSelector</a>, <span class="ident">provider</span><span>: </span><a class="type-ref" href="#SignatureHelpProvider">SignatureHelpProvider</a>, <span class="ident">metadata</span><span>: </span><a class="type-ref" href="#SignatureHelpProviderMetadata">SignatureHelpProviderMetadata</a><span>)</span><span>: </span><a class="type-ref" href="#Disposable">Disposable</a></span>
<div class="details collapse" id="details-2407">
<div class="comment"></div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="selector"></a><span class="ts" id=2408 data-target="#details-2408" data-toggle="collapse"><span class="ident">selector</span><span>: </span><a class="type-ref" href="#DocumentSelector">DocumentSelector</a></span></td><td><div class="comment"></div></td></tr>
<tr><td><a name="provider"></a><span class="ts" id=2409 data-target="#details-2409" data-toggle="collapse"><span class="ident">provider</span><span>: </span><a class="type-ref" href="#SignatureHelpProvider">SignatureHelpProvider</a></span></td><td><div class="comment"></div></td></tr>
<tr><td><a name="metadata"></a><span class="ts" id=2410 data-target="#details-2410" data-toggle="collapse"><span class="ident">metadata</span><span>: </span><a class="type-ref" href="#SignatureHelpProviderMetadata">SignatureHelpProviderMetadata</a></span></td><td><div class="comment"></div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#Disposable">Disposable</a></span></td><td><div class="comment"></div></td></tr>
</table>
</div>
</div>



<a name="languages.registerTypeDefinitionProvider"></a><span class="ts" id=2357 data-target="#details-2357" data-toggle="collapse"><span class="ident">registerTypeDefinitionProvider</span><span>(</span><span class="ident">selector</span><span>: </span><a class="type-ref" href="#DocumentSelector">DocumentSelector</a>, <span class="ident">provider</span><span>: </span><a class="type-ref" href="#TypeDefinitionProvider">TypeDefinitionProvider</a><span>)</span><span>: </span><a class="type-ref" href="#Disposable">Disposable</a></span>
<div class="details collapse" id="details-2357">
<div class="comment"><p>Register a type definition provider.</p>
<p>Multiple providers can be registered for a language. In that case providers are asked in
parallel and the results are merged. A failing provider (rejected promise or exception) will
not cause a failure of the whole operation.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="selector"></a><span class="ts" id=2358 data-target="#details-2358" data-toggle="collapse"><span class="ident">selector</span><span>: </span><a class="type-ref" href="#DocumentSelector">DocumentSelector</a></span></td><td><div class="comment"><p>A selector that defines the documents this provider is applicable to.</p>
</div></td></tr>
<tr><td><a name="provider"></a><span class="ts" id=2359 data-target="#details-2359" data-toggle="collapse"><span class="ident">provider</span><span>: </span><a class="type-ref" href="#TypeDefinitionProvider">TypeDefinitionProvider</a></span></td><td><div class="comment"><p>A type definition provider.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#Disposable">Disposable</a></span></td><td><div class="comment"><p>A <a href="#Disposable">disposable</a> that unregisters this provider when being disposed.</p>
</div></td></tr>
</table>
</div>
</div>



<a name="languages.registerWorkspaceSymbolProvider"></a><span class="ts" id=2378 data-target="#details-2378" data-toggle="collapse"><span class="ident">registerWorkspaceSymbolProvider</span><span>(</span><span class="ident">provider</span><span>: </span><a class="type-ref" href="#WorkspaceSymbolProvider">WorkspaceSymbolProvider</a><span>)</span><span>: </span><a class="type-ref" href="#Disposable">Disposable</a></span>
<div class="details collapse" id="details-2378">
<div class="comment"><p>Register a workspace symbol provider.</p>
<p>Multiple providers can be registered. In that case providers are asked in parallel and
the results are merged. A failing provider (rejected promise or exception) will not cause
a failure of the whole operation.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="provider"></a><span class="ts" id=2379 data-target="#details-2379" data-toggle="collapse"><span class="ident">provider</span><span>: </span><a class="type-ref" href="#WorkspaceSymbolProvider">WorkspaceSymbolProvider</a></span></td><td><div class="comment"><p>A workspace symbol provider.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#Disposable">Disposable</a></span></td><td><div class="comment"><p>A <a href="#Disposable">disposable</a> that unregisters this provider when being disposed.</p>
</div></td></tr>
</table>
</div>
</div>



<a name="languages.setLanguageConfiguration"></a><span class="ts" id=2424 data-target="#details-2424" data-toggle="collapse"><span class="ident">setLanguageConfiguration</span><span>(</span><span class="ident">language</span><span>: </span><a class="type-intrinsic">string</a>, <span class="ident">configuration</span><span>: </span><a class="type-ref" href="#LanguageConfiguration">LanguageConfiguration</a><span>)</span><span>: </span><a class="type-ref" href="#Disposable">Disposable</a></span>
<div class="details collapse" id="details-2424">
<div class="comment"><p>Set a <a href="#LanguageConfiguration">language configuration</a> for a language.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="language"></a><span class="ts" id=2425 data-target="#details-2425" data-toggle="collapse"><span class="ident">language</span><span>: </span><a class="type-intrinsic">string</a></span></td><td><div class="comment"><p>A language identifier like <code>typescript</code>.</p>
</div></td></tr>
<tr><td><a name="configuration"></a><span class="ts" id=2426 data-target="#details-2426" data-toggle="collapse"><span class="ident">configuration</span><span>: </span><a class="type-ref" href="#LanguageConfiguration">LanguageConfiguration</a></span></td><td><div class="comment"><p>Language configuration.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#Disposable">Disposable</a></span></td><td><div class="comment"><p>A <a href="#Disposable">disposable</a> that unsets this configuration.</p>
</div></td></tr>
</table>
</div>
</div>



<a name="languages.setTextDocumentLanguage"></a><span class="ts" id=2319 data-target="#details-2319" data-toggle="collapse"><span class="ident">setTextDocumentLanguage</span><span>(</span><span class="ident">document</span><span>: </span><a class="type-ref" href="#TextDocument">TextDocument</a>, <span class="ident">languageId</span><span>: </span><a class="type-intrinsic">string</a><span>)</span><span>: </span><a class="type-ref" href="#Thenable">Thenable</a>&lt;<a class="type-ref" href="#TextDocument">TextDocument</a>&gt;</span>
<div class="details collapse" id="details-2319">
<div class="comment"><p>Set (and change) the <a href="#TextDocument.languageId">language</a> that is associated
with the given document.</p>
<p><em>Note</em> that calling this function will trigger the <a href="#workspace.onDidCloseTextDocument"><code>onDidCloseTextDocument</code></a> event
followed by the <a href="#workspace.onDidOpenTextDocument"><code>onDidOpenTextDocument</code></a> event.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="document"></a><span class="ts" id=2320 data-target="#details-2320" data-toggle="collapse"><span class="ident">document</span><span>: </span><a class="type-ref" href="#TextDocument">TextDocument</a></span></td><td><div class="comment"><p>The document which language is to be changed</p>
</div></td></tr>
<tr><td><a name="languageId"></a><span class="ts" id=2321 data-target="#details-2321" data-toggle="collapse"><span class="ident">languageId</span><span>: </span><a class="type-intrinsic">string</a></span></td><td><div class="comment"><p>The new language identifier.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#Thenable">Thenable</a>&lt;<a class="type-ref" href="#TextDocument">TextDocument</a>&gt;</span></td><td><div class="comment"><p>A thenable that resolves with the updated document.</p>
</div></td></tr>
</table>
</div>
</div>

## scm



<div class="comment"></div>

#### Variables



<a name="scm.inputBox"></a><span class="ts" id=2428 data-target="#details-2428" data-toggle="collapse"><span class="ident">inputBox</span><span>: </span><a class="type-ref" href="#SourceControlInputBox">SourceControlInputBox</a></span>
<div class="details collapse" id="details-2428">
<div class="comment"><p><del>The <a href="#SourceControlInputBox">input box</a> for the last source control
created by the extension.</del></p>
<ul>
<li><em>deprecated</em> - Use SourceControl.inputBox instead</li>
</ul>
</div>
</div>

#### Functions



<a name="scm.createSourceControl"></a><span class="ts" id=2430 data-target="#details-2430" data-toggle="collapse"><span class="ident">createSourceControl</span><span>(</span><span class="ident">id</span><span>: </span><a class="type-intrinsic">string</a>, <span class="ident">label</span><span>: </span><a class="type-intrinsic">string</a>, <span class="ident">rootUri</span><span>?</span><span>: </span><a class="type-ref" href="#Uri">Uri</a><span>)</span><span>: </span><a class="type-ref" href="#SourceControl">SourceControl</a></span>
<div class="details collapse" id="details-2430">
<div class="comment"><p>Creates a new <a href="#SourceControl">source control</a> instance.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="id"></a><span class="ts" id=2431 data-target="#details-2431" data-toggle="collapse"><span class="ident">id</span><span>: </span><a class="type-intrinsic">string</a></span></td><td><div class="comment"><p>An <code>id</code> for the source control. Something short, eg: <code>git</code>.</p>
</div></td></tr>
<tr><td><a name="label"></a><span class="ts" id=2432 data-target="#details-2432" data-toggle="collapse"><span class="ident">label</span><span>: </span><a class="type-intrinsic">string</a></span></td><td><div class="comment"><p>A human-readable string for the source control. Eg: <code>Git</code>.</p>
</div></td></tr>
<tr><td><a name="rootUri"></a><span class="ts" id=2433 data-target="#details-2433" data-toggle="collapse"><span class="ident">rootUri</span><span>?</span><span>: </span><a class="type-ref" href="#Uri">Uri</a></span></td><td><div class="comment"><p>An optional Uri of the root of the source control. Eg: <code>Uri.parse(workspaceRoot)</code>.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#SourceControl">SourceControl</a></span></td><td><div class="comment"><p>An instance of <a href="#SourceControl">source control</a>.</p>
</div></td></tr>
</table>
</div>
</div>

## tasks



<div class="comment"><p>Namespace for tasks functionality.</p>
</div>

#### Variables



<a name="tasks.taskExecutions"></a><span class="ts" id=2011 data-target="#details-2011" data-toggle="collapse"><span class="ident">taskExecutions</span><span>: </span><a class="type-ref" href="#ReadonlyArray">ReadonlyArray</a>&lt;<a class="type-ref" href="#TaskExecution">TaskExecution</a>&gt;</span>
<div class="details collapse" id="details-2011">
<div class="comment"><p>The currently active task executions or an empty array.</p>
<ul>
<li><em>readonly</em></li>
</ul>
</div>
</div>

#### Events



<a name="tasks.onDidEndTask"></a><span class="ts" id=2013 data-target="#details-2013" data-toggle="collapse"><span class="ident">onDidEndTask</span><span>: </span><a class="type-ref" href="#Event">Event</a>&lt;<a class="type-ref" href="#TaskEndEvent">TaskEndEvent</a>&gt;</span>
<div class="details collapse" id="details-2013">
<div class="comment"><p>Fires when a task ends.</p>
</div>
</div>



<a name="tasks.onDidEndTaskProcess"></a><span class="ts" id=2015 data-target="#details-2015" data-toggle="collapse"><span class="ident">onDidEndTaskProcess</span><span>: </span><a class="type-ref" href="#Event">Event</a>&lt;<a class="type-ref" href="#TaskProcessEndEvent">TaskProcessEndEvent</a>&gt;</span>
<div class="details collapse" id="details-2015">
<div class="comment"><p>Fires when the underlying process has ended.
This event will not fire for tasks that don&#39;t
execute an underlying process.</p>
</div>
</div>



<a name="tasks.onDidStartTask"></a><span class="ts" id=2012 data-target="#details-2012" data-toggle="collapse"><span class="ident">onDidStartTask</span><span>: </span><a class="type-ref" href="#Event">Event</a>&lt;<a class="type-ref" href="#TaskStartEvent">TaskStartEvent</a>&gt;</span>
<div class="details collapse" id="details-2012">
<div class="comment"><p>Fires when a task starts.</p>
</div>
</div>



<a name="tasks.onDidStartTaskProcess"></a><span class="ts" id=2014 data-target="#details-2014" data-toggle="collapse"><span class="ident">onDidStartTaskProcess</span><span>: </span><a class="type-ref" href="#Event">Event</a>&lt;<a class="type-ref" href="#TaskProcessStartEvent">TaskProcessStartEvent</a>&gt;</span>
<div class="details collapse" id="details-2014">
<div class="comment"><p>Fires when the underlying process has been started.
This event will not fire for tasks that don&#39;t
execute an underlying process.</p>
</div>
</div>

#### Functions



<a name="tasks.executeTask"></a><span class="ts" id=2009 data-target="#details-2009" data-toggle="collapse"><span class="ident">executeTask</span><span>(</span><span class="ident">task</span><span>: </span><a class="type-ref" href="#Task">Task</a><span>)</span><span>: </span><a class="type-ref" href="#Thenable">Thenable</a>&lt;<a class="type-ref" href="#TaskExecution">TaskExecution</a>&gt;</span>
<div class="details collapse" id="details-2009">
<div class="comment"><p>Executes a task that is managed by VS Code. The returned
task execution can be used to terminate the task.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="task"></a><span class="ts" id=2010 data-target="#details-2010" data-toggle="collapse"><span class="ident">task</span><span>: </span><a class="type-ref" href="#Task">Task</a></span></td><td><div class="comment"><p>the task to execute</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#Thenable">Thenable</a>&lt;<a class="type-ref" href="#TaskExecution">TaskExecution</a>&gt;</span></td><td><div class="comment"></div></td></tr>
</table>
</div>
</div>



<a name="tasks.fetchTasks"></a><span class="ts" id=2006 data-target="#details-2006" data-toggle="collapse"><span class="ident">fetchTasks</span><span>(</span><span class="ident">filter</span><span>?</span><span>: </span><a class="type-ref" href="#TaskFilter">TaskFilter</a><span>)</span><span>: </span><a class="type-ref" href="#Thenable">Thenable</a>&lt;<a class="type-ref" href="#Task">Task</a>[]&gt;</span>
<div class="details collapse" id="details-2006">
<div class="comment"><p>Fetches all tasks available in the systems. This includes tasks
from <code>tasks.json</code> files as well as tasks from task providers
contributed through extensions.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="filter"></a><span class="ts" id=2007 data-target="#details-2007" data-toggle="collapse"><span class="ident">filter</span><span>?</span><span>: </span><a class="type-ref" href="#TaskFilter">TaskFilter</a></span></td><td><div class="comment"><p>a filter to filter the return tasks.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#Thenable">Thenable</a>&lt;<a class="type-ref" href="#Task">Task</a>[]&gt;</span></td><td><div class="comment"></div></td></tr>
</table>
</div>
</div>



<a name="tasks.registerTaskProvider"></a><span class="ts" id=2002 data-target="#details-2002" data-toggle="collapse"><span class="ident">registerTaskProvider</span><span>(</span><span class="ident">type</span><span>: </span><a class="type-intrinsic">string</a>, <span class="ident">provider</span><span>: </span><a class="type-ref" href="#TaskProvider">TaskProvider</a><span>)</span><span>: </span><a class="type-ref" href="#Disposable">Disposable</a></span>
<div class="details collapse" id="details-2002">
<div class="comment"><p>Register a task provider.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="type"></a><span class="ts" id=2003 data-target="#details-2003" data-toggle="collapse"><span class="ident">type</span><span>: </span><a class="type-intrinsic">string</a></span></td><td><div class="comment"><p>The task kind type this provider is registered for.</p>
</div></td></tr>
<tr><td><a name="provider"></a><span class="ts" id=2004 data-target="#details-2004" data-toggle="collapse"><span class="ident">provider</span><span>: </span><a class="type-ref" href="#TaskProvider">TaskProvider</a></span></td><td><div class="comment"><p>A task provider.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#Disposable">Disposable</a></span></td><td><div class="comment"><p>A <a href="#Disposable">disposable</a> that unregisters this provider when being disposed.</p>
</div></td></tr>
</table>
</div>
</div>

## window



<div class="comment"><p>Namespace for dealing with the current window of the editor. That is visible
and active editors, as well as, UI elements to show messages, selections, and
asking for user input.</p>
</div>

#### Variables



<a name="window.activeTerminal"></a><span class="ts" id=2060 data-target="#details-2060" data-toggle="collapse"><span class="ident">activeTerminal</span><span>: </span><a class="type-ref" href="#Terminal">Terminal</a> &#124; <a class="type-intrinsic">undefined</a></span>
<div class="details collapse" id="details-2060">
<div class="comment"><p>The currently active terminal or <code>undefined</code>. The active terminal is the one that
currently has focus or most recently had focus.</p>
</div>
</div>



<a name="window.activeTextEditor"></a><span class="ts" id=2051 data-target="#details-2051" data-toggle="collapse"><span class="ident">activeTextEditor</span><span>: </span><a class="type-ref" href="#TextEditor">TextEditor</a> &#124; <a class="type-intrinsic">undefined</a></span>
<div class="details collapse" id="details-2051">
<div class="comment"><p>The currently active editor or <code>undefined</code>. The active editor is the one
that currently has focus or, when none has focus, the one that has changed
input most recently.</p>
</div>
</div>



<a name="window.state"></a><span class="ts" id=2064 data-target="#details-2064" data-toggle="collapse"><span class="ident">state</span><span>: </span><a class="type-ref" href="#WindowState">WindowState</a></span>
<div class="details collapse" id="details-2064">
<div class="comment"><p>Represents the current window&#39;s state.</p>
<ul>
<li><em>readonly</em></li>
</ul>
</div>
</div>



<a name="window.terminals"></a><span class="ts" id=2059 data-target="#details-2059" data-toggle="collapse"><span class="ident">terminals</span><span>: </span><a class="type-ref" href="#ReadonlyArray">ReadonlyArray</a>&lt;<a class="type-ref" href="#Terminal">Terminal</a>&gt;</span>
<div class="details collapse" id="details-2059">
<div class="comment"><p>The currently opened terminals or an empty array.</p>
</div>
</div>



<a name="window.visibleTextEditors"></a><span class="ts" id=2052 data-target="#details-2052" data-toggle="collapse"><span class="ident">visibleTextEditors</span><span>: </span><a class="type-ref" href="#TextEditor">TextEditor</a>[]</span>
<div class="details collapse" id="details-2052">
<div class="comment"><p>The currently visible editors or an empty array.</p>
</div>
</div>

#### Events



<a name="window.onDidChangeActiveTerminal"></a><span class="ts" id=2061 data-target="#details-2061" data-toggle="collapse"><span class="ident">onDidChangeActiveTerminal</span><span>: </span><a class="type-ref" href="#Event">Event</a>&lt;<a class="type-ref" href="#Terminal">Terminal</a> &#124; <a class="type-intrinsic">undefined</a>&gt;</span>
<div class="details collapse" id="details-2061">
<div class="comment"><p>An <a href="#Event">event</a> which fires when the <a href="#window.activeTerminal">active terminal</a>
has changed. <em>Note</em> that the event also fires when the active terminal changes
to <code>undefined</code>.</p>
</div>
</div>



<a name="window.onDidChangeActiveTextEditor"></a><span class="ts" id=2053 data-target="#details-2053" data-toggle="collapse"><span class="ident">onDidChangeActiveTextEditor</span><span>: </span><a class="type-ref" href="#Event">Event</a>&lt;<a class="type-ref" href="#TextEditor">TextEditor</a> &#124; <a class="type-intrinsic">undefined</a>&gt;</span>
<div class="details collapse" id="details-2053">
<div class="comment"><p>An <a href="#Event">event</a> which fires when the <a href="#window.activeTextEditor">active editor</a>
has changed. <em>Note</em> that the event also fires when the active editor changes
to <code>undefined</code>.</p>
</div>
</div>



<a name="window.onDidChangeTextEditorOptions"></a><span class="ts" id=2057 data-target="#details-2057" data-toggle="collapse"><span class="ident">onDidChangeTextEditorOptions</span><span>: </span><a class="type-ref" href="#Event">Event</a>&lt;<a class="type-ref" href="#TextEditorOptionsChangeEvent">TextEditorOptionsChangeEvent</a>&gt;</span>
<div class="details collapse" id="details-2057">
<div class="comment"><p>An <a href="#Event">event</a> which fires when the options of an editor have changed.</p>
</div>
</div>



<a name="window.onDidChangeTextEditorSelection"></a><span class="ts" id=2055 data-target="#details-2055" data-toggle="collapse"><span class="ident">onDidChangeTextEditorSelection</span><span>: </span><a class="type-ref" href="#Event">Event</a>&lt;<a class="type-ref" href="#TextEditorSelectionChangeEvent">TextEditorSelectionChangeEvent</a>&gt;</span>
<div class="details collapse" id="details-2055">
<div class="comment"><p>An <a href="#Event">event</a> which fires when the selection in an editor has changed.</p>
</div>
</div>



<a name="window.onDidChangeTextEditorViewColumn"></a><span class="ts" id=2058 data-target="#details-2058" data-toggle="collapse"><span class="ident">onDidChangeTextEditorViewColumn</span><span>: </span><a class="type-ref" href="#Event">Event</a>&lt;<a class="type-ref" href="#TextEditorViewColumnChangeEvent">TextEditorViewColumnChangeEvent</a>&gt;</span>
<div class="details collapse" id="details-2058">
<div class="comment"><p>An <a href="#Event">event</a> which fires when the view column of an editor has changed.</p>
</div>
</div>



<a name="window.onDidChangeTextEditorVisibleRanges"></a><span class="ts" id=2056 data-target="#details-2056" data-toggle="collapse"><span class="ident">onDidChangeTextEditorVisibleRanges</span><span>: </span><a class="type-ref" href="#Event">Event</a>&lt;<a class="type-ref" href="#TextEditorVisibleRangesChangeEvent">TextEditorVisibleRangesChangeEvent</a>&gt;</span>
<div class="details collapse" id="details-2056">
<div class="comment"><p>An <a href="#Event">event</a> which fires when the visible ranges of an editor has changed.</p>
</div>
</div>



<a name="window.onDidChangeVisibleTextEditors"></a><span class="ts" id=2054 data-target="#details-2054" data-toggle="collapse"><span class="ident">onDidChangeVisibleTextEditors</span><span>: </span><a class="type-ref" href="#Event">Event</a>&lt;<a class="type-ref" href="#TextEditor">TextEditor</a>[]&gt;</span>
<div class="details collapse" id="details-2054">
<div class="comment"><p>An <a href="#Event">event</a> which fires when the array of <a href="#window.visibleTextEditors">visible editors</a>
has changed.</p>
</div>
</div>



<a name="window.onDidChangeWindowState"></a><span class="ts" id=2065 data-target="#details-2065" data-toggle="collapse"><span class="ident">onDidChangeWindowState</span><span>: </span><a class="type-ref" href="#Event">Event</a>&lt;<a class="type-ref" href="#WindowState">WindowState</a>&gt;</span>
<div class="details collapse" id="details-2065">
<div class="comment"><p>An <a href="#Event">event</a> which fires when the focus state of the current window
changes. The value of the event represents whether the window is focused.</p>
</div>
</div>



<a name="window.onDidCloseTerminal"></a><span class="ts" id=2063 data-target="#details-2063" data-toggle="collapse"><span class="ident">onDidCloseTerminal</span><span>: </span><a class="type-ref" href="#Event">Event</a>&lt;<a class="type-ref" href="#Terminal">Terminal</a>&gt;</span>
<div class="details collapse" id="details-2063">
<div class="comment"><p>An <a href="#Event">event</a> which fires when a terminal is disposed.</p>
</div>
</div>



<a name="window.onDidOpenTerminal"></a><span class="ts" id=2062 data-target="#details-2062" data-toggle="collapse"><span class="ident">onDidOpenTerminal</span><span>: </span><a class="type-ref" href="#Event">Event</a>&lt;<a class="type-ref" href="#Terminal">Terminal</a>&gt;</span>
<div class="details collapse" id="details-2062">
<div class="comment"><p>An <a href="#Event">event</a> which fires when a terminal has been created, either through the
<a href="#window.createTerminal">createTerminal</a> API or commands.</p>
</div>
</div>

#### Functions



<a name="window.createInputBox"></a><span class="ts" id=2171 data-target="#details-2171" data-toggle="collapse"><span class="ident">createInputBox</span><span>(</span><span>)</span><span>: </span><a class="type-ref" href="#InputBox">InputBox</a></span>
<div class="details collapse" id="details-2171">
<div class="comment"><p>Creates a <a href="#InputBox">InputBox</a> to let the user enter some text input.</p>
<p>Note that in many cases the more convenient <a href="#window.showInputBox">window.showInputBox</a>
is easier to use. <a href="#window.createInputBox">window.createInputBox</a> should be used
when <a href="#window.showInputBox">window.showInputBox</a> does not offer the required flexibility.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#InputBox">InputBox</a></span></td><td><div class="comment"><p>A new <a href="#InputBox">InputBox</a>.</p>
</div></td></tr>
</table>
</div>
</div>



<a name="window.createOutputChannel"></a><span class="ts" id=2173 data-target="#details-2173" data-toggle="collapse"><span class="ident">createOutputChannel</span><span>(</span><span class="ident">name</span><span>: </span><a class="type-intrinsic">string</a><span>)</span><span>: </span><a class="type-ref" href="#OutputChannel">OutputChannel</a></span>
<div class="details collapse" id="details-2173">
<div class="comment"><p>Creates a new <a href="#OutputChannel">output channel</a> with the given name.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="name"></a><span class="ts" id=2174 data-target="#details-2174" data-toggle="collapse"><span class="ident">name</span><span>: </span><a class="type-intrinsic">string</a></span></td><td><div class="comment"><p>Human-readable string which will be used to represent the channel in the UI.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#OutputChannel">OutputChannel</a></span></td><td><div class="comment"></div></td></tr>
</table>
</div>
</div>



<a name="window.createQuickPick"></a><span class="ts" id=2168 data-target="#details-2168" data-toggle="collapse"><span class="ident">createQuickPick</span><span>&lt;</span>T extends <a class="type-ref" href="#QuickPickItem">QuickPickItem</a><span>&gt;</span><span>(</span><span>)</span><span>: </span><a class="type-ref" href="#QuickPick">QuickPick</a>&lt;<a class="type-intrinsic">T</a>&gt;</span>
<div class="details collapse" id="details-2168">
<div class="comment"><p>Creates a <a href="#QuickPick">QuickPick</a> to let the user pick an item from a list
of items of type T.</p>
<p>Note that in many cases the more convenient <a href="#window.showQuickPick">window.showQuickPick</a>
is easier to use. <a href="#window.createQuickPick">window.createQuickPick</a> should be used
when <a href="#window.showQuickPick">window.showQuickPick</a> does not offer the required flexibility.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#QuickPick">QuickPick</a>&lt;<a class="type-intrinsic">T</a>&gt;</span></td><td><div class="comment"><p>A new <a href="#QuickPick">QuickPick</a>.</p>
</div></td></tr>
</table>
</div>
</div>



<a name="window.createStatusBarItem"></a><span class="ts" id=2213 data-target="#details-2213" data-toggle="collapse"><span class="ident">createStatusBarItem</span><span>(</span><span class="ident">alignment</span><span>?</span><span>: </span><a class="type-ref" href="#StatusBarAlignment">StatusBarAlignment</a>, <span class="ident">priority</span><span>?</span><span>: </span><a class="type-intrinsic">number</a><span>)</span><span>: </span><a class="type-ref" href="#StatusBarItem">StatusBarItem</a></span>
<div class="details collapse" id="details-2213">
<div class="comment"><p>Creates a status bar <a href="#StatusBarItem">item</a>.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="alignment"></a><span class="ts" id=2214 data-target="#details-2214" data-toggle="collapse"><span class="ident">alignment</span><span>?</span><span>: </span><a class="type-ref" href="#StatusBarAlignment">StatusBarAlignment</a></span></td><td><div class="comment"><p>The alignment of the item.</p>
</div></td></tr>
<tr><td><a name="priority"></a><span class="ts" id=2215 data-target="#details-2215" data-toggle="collapse"><span class="ident">priority</span><span>?</span><span>: </span><a class="type-intrinsic">number</a></span></td><td><div class="comment"><p>The priority of the item. Higher values mean the item should be shown more to the left.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#StatusBarItem">StatusBarItem</a></span></td><td><div class="comment"><p>A new status bar item.</p>
</div></td></tr>
</table>
</div>
</div>



<a name="window.createTerminal"></a><span class="ts" id=2217 data-target="#details-2217" data-toggle="collapse"><span class="ident">createTerminal</span><span>(</span><span class="ident">name</span><span>?</span><span>: </span><a class="type-intrinsic">string</a>, <span class="ident">shellPath</span><span>?</span><span>: </span><a class="type-intrinsic">string</a>, <span class="ident">shellArgs</span><span>?</span><span>: </span><a class="type-intrinsic">string</a>[]<span>)</span><span>: </span><a class="type-ref" href="#Terminal">Terminal</a></span>
<div class="details collapse" id="details-2217">
<div class="comment"><p>Creates a <a href="#Terminal">Terminal</a>. The cwd of the terminal will be the workspace directory
if it exists, regardless of whether an explicit customStartPath setting exists.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="name"></a><span class="ts" id=2218 data-target="#details-2218" data-toggle="collapse"><span class="ident">name</span><span>?</span><span>: </span><a class="type-intrinsic">string</a></span></td><td><div class="comment"><p>Optional human-readable string which will be used to represent the terminal in the UI.</p>
</div></td></tr>
<tr><td><a name="shellPath"></a><span class="ts" id=2219 data-target="#details-2219" data-toggle="collapse"><span class="ident">shellPath</span><span>?</span><span>: </span><a class="type-intrinsic">string</a></span></td><td><div class="comment"><p>Optional path to a custom shell executable to be used in the terminal.</p>
</div></td></tr>
<tr><td><a name="shellArgs"></a><span class="ts" id=2220 data-target="#details-2220" data-toggle="collapse"><span class="ident">shellArgs</span><span>?</span><span>: </span><a class="type-intrinsic">string</a>[]</span></td><td><div class="comment"><p>Optional args for the custom shell executable, this does not work on Windows (see #8429)</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#Terminal">Terminal</a></span></td><td><div class="comment"><p>A new Terminal.</p>
</div></td></tr>
</table>
</div>
</div>



<a name="window.createTerminal"></a><span class="ts" id=2221 data-target="#details-2221" data-toggle="collapse"><span class="ident">createTerminal</span><span>(</span><span class="ident">options</span><span>: </span><a class="type-ref" href="#TerminalOptions">TerminalOptions</a><span>)</span><span>: </span><a class="type-ref" href="#Terminal">Terminal</a></span>
<div class="details collapse" id="details-2221">
<div class="comment"><p>Creates a <a href="#Terminal">Terminal</a>. The cwd of the terminal will be the workspace directory
if it exists, regardless of whether an explicit customStartPath setting exists.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="options"></a><span class="ts" id=2222 data-target="#details-2222" data-toggle="collapse"><span class="ident">options</span><span>: </span><a class="type-ref" href="#TerminalOptions">TerminalOptions</a></span></td><td><div class="comment"><p>A TerminalOptions object describing the characteristics of the new terminal.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#Terminal">Terminal</a></span></td><td><div class="comment"><p>A new Terminal.</p>
</div></td></tr>
</table>
</div>
</div>



<a name="window.createTextEditorDecorationType"></a><span class="ts" id=2078 data-target="#details-2078" data-toggle="collapse"><span class="ident">createTextEditorDecorationType</span><span>(</span><span class="ident">options</span><span>: </span><a class="type-ref" href="#DecorationRenderOptions">DecorationRenderOptions</a><span>)</span><span>: </span><a class="type-ref" href="#TextEditorDecorationType">TextEditorDecorationType</a></span>
<div class="details collapse" id="details-2078">
<div class="comment"><p>Create a TextEditorDecorationType that can be used to add decorations to text editors.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="options"></a><span class="ts" id=2079 data-target="#details-2079" data-toggle="collapse"><span class="ident">options</span><span>: </span><a class="type-ref" href="#DecorationRenderOptions">DecorationRenderOptions</a></span></td><td><div class="comment"><p>Rendering options for the decoration type.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#TextEditorDecorationType">TextEditorDecorationType</a></span></td><td><div class="comment"><p>A new decoration type instance.</p>
</div></td></tr>
</table>
</div>
</div>



<a name="window.createTreeView"></a><span class="ts" id=2229 data-target="#details-2229" data-toggle="collapse"><span class="ident">createTreeView</span><span>&lt;</span>T<span>&gt;</span><span>(</span><span class="ident">viewId</span><span>: </span><a class="type-intrinsic">string</a>, <span class="ident">options</span><span>: </span><a class="type-ref" href="#TreeViewOptions">TreeViewOptions</a>&lt;<a class="type-intrinsic">T</a>&gt;<span>)</span><span>: </span><a class="type-ref" href="#TreeView">TreeView</a>&lt;<a class="type-intrinsic">T</a>&gt;</span>
<div class="details collapse" id="details-2229">
<div class="comment"><p>Create a <a href="#TreeView">TreeView</a> for the view contributed using the extension point <code>views</code>.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="viewId"></a><span class="ts" id=2231 data-target="#details-2231" data-toggle="collapse"><span class="ident">viewId</span><span>: </span><a class="type-intrinsic">string</a></span></td><td><div class="comment"><p>Id of the view contributed using the extension point <code>views</code>.</p>
</div></td></tr>
<tr><td><a name="options"></a><span class="ts" id=2232 data-target="#details-2232" data-toggle="collapse"><span class="ident">options</span><span>: </span><a class="type-ref" href="#TreeViewOptions">TreeViewOptions</a>&lt;<a class="type-intrinsic">T</a>&gt;</span></td><td><div class="comment"><p>Options for creating the <a href="#TreeView">TreeView</a></p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#TreeView">TreeView</a>&lt;<a class="type-intrinsic">T</a>&gt;</span></td><td><div class="comment"><p>a <a href="#TreeView">TreeView</a>.</p>
</div></td></tr>
</table>
</div>
</div>



<a name="window.createWebviewPanel"></a><span class="ts" id=2176 data-target="#details-2176" data-toggle="collapse"><span class="ident">createWebviewPanel</span><span>(</span><span class="ident">viewType</span><span>: </span><a class="type-intrinsic">string</a>, <span class="ident">title</span><span>: </span><a class="type-intrinsic">string</a>, <span class="ident">showOptions</span><span>: </span><a class="type-ref" href="#ViewColumn">ViewColumn</a> &#124; {preserveFocus: <a class="type-intrinsic">boolean</a>, viewColumn: <a class="type-ref" href="#ViewColumn">ViewColumn</a>}, <span class="ident">options</span><span>?</span><span>: </span><a class="type-ref" href="#WebviewPanelOptions">WebviewPanelOptions</a> &#38; <a class="type-ref" href="#WebviewOptions">WebviewOptions</a><span>)</span><span>: </span><a class="type-ref" href="#WebviewPanel">WebviewPanel</a></span>
<div class="details collapse" id="details-2176">
<div class="comment"><p>Create and show a new webview panel.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="viewType"></a><span class="ts" id=2177 data-target="#details-2177" data-toggle="collapse"><span class="ident">viewType</span><span>: </span><a class="type-intrinsic">string</a></span></td><td><div class="comment"><p>Identifies the type of the webview panel.</p>
</div></td></tr>
<tr><td><a name="title"></a><span class="ts" id=2178 data-target="#details-2178" data-toggle="collapse"><span class="ident">title</span><span>: </span><a class="type-intrinsic">string</a></span></td><td><div class="comment"><p>Title of the panel.</p>
</div></td></tr>
<tr><td><a name="showOptions"></a><span class="ts" id=2179 data-target="#details-2179" data-toggle="collapse"><span class="ident">showOptions</span><span>: </span><a class="type-ref" href="#ViewColumn">ViewColumn</a> &#124; {preserveFocus: <a class="type-intrinsic">boolean</a>, viewColumn: <a class="type-ref" href="#ViewColumn">ViewColumn</a>}</span></td><td><div class="comment"><p>Where to show the webview in the editor. If preserveFocus is set, the new webview will not take focus.</p>
</div></td></tr>
<tr><td><a name="options"></a><span class="ts" id=2183 data-target="#details-2183" data-toggle="collapse"><span class="ident">options</span><span>?</span><span>: </span><a class="type-ref" href="#WebviewPanelOptions">WebviewPanelOptions</a> &#38; <a class="type-ref" href="#WebviewOptions">WebviewOptions</a></span></td><td><div class="comment"><p>Settings for the new panel.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#WebviewPanel">WebviewPanel</a></span></td><td><div class="comment"><p>New webview panel.</p>
</div></td></tr>
</table>
</div>
</div>



<a name="window.registerTreeDataProvider"></a><span class="ts" id=2224 data-target="#details-2224" data-toggle="collapse"><span class="ident">registerTreeDataProvider</span><span>&lt;</span>T<span>&gt;</span><span>(</span><span class="ident">viewId</span><span>: </span><a class="type-intrinsic">string</a>, <span class="ident">treeDataProvider</span><span>: </span><a class="type-ref" href="#TreeDataProvider">TreeDataProvider</a>&lt;<a class="type-intrinsic">T</a>&gt;<span>)</span><span>: </span><a class="type-ref" href="#Disposable">Disposable</a></span>
<div class="details collapse" id="details-2224">
<div class="comment"><p>Register a <a href="#TreeDataProvider">TreeDataProvider</a> for the view contributed using the extension point <code>views</code>.
This will allow you to contribute data to the <a href="#TreeView">TreeView</a> and update if the data changes.</p>
<p><strong>Note:</strong> To get access to the <a href="#TreeView">TreeView</a> and perform operations on it, use <a href="#window.createTreeView">createTreeView</a>.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="viewId"></a><span class="ts" id=2226 data-target="#details-2226" data-toggle="collapse"><span class="ident">viewId</span><span>: </span><a class="type-intrinsic">string</a></span></td><td><div class="comment"><p>Id of the view contributed using the extension point <code>views</code>.</p>
</div></td></tr>
<tr><td><a name="treeDataProvider"></a><span class="ts" id=2227 data-target="#details-2227" data-toggle="collapse"><span class="ident">treeDataProvider</span><span>: </span><a class="type-ref" href="#TreeDataProvider">TreeDataProvider</a>&lt;<a class="type-intrinsic">T</a>&gt;</span></td><td><div class="comment"><p>A <a href="#TreeDataProvider">TreeDataProvider</a> that provides tree data for the view</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#Disposable">Disposable</a></span></td><td><div class="comment"></div></td></tr>
</table>
</div>
</div>



<a name="window.registerUriHandler"></a><span class="ts" id=2234 data-target="#details-2234" data-toggle="collapse"><span class="ident">registerUriHandler</span><span>(</span><span class="ident">handler</span><span>: </span><a class="type-ref" href="#UriHandler">UriHandler</a><span>)</span><span>: </span><a class="type-ref" href="#Disposable">Disposable</a></span>
<div class="details collapse" id="details-2234">
<div class="comment"><p>Registers a <a href="#UriHandler">uri handler</a> capable of handling system-wide <a href="#Uri">uris</a>.
In case there are multiple windows open, the topmost window will handle the uri.
A uri handler is scoped to the extension it is contributed from; it will only
be able to handle uris which are directed to the extension itself. A uri must respect
the following rules:</p>
<ul>
<li>The uri-scheme must be the product name;</li>
<li>The uri-authority must be the extension id (eg. <code>my.extension</code>);</li>
<li>The uri-path, -query and -fragment parts are arbitrary.</li>
</ul>
<p>For example, if the <code>my.extension</code> extension registers a uri handler, it will only
be allowed to handle uris with the prefix <code>product-name://my.extension</code>.</p>
<p>An extension can only register a single uri handler in its entire activation lifetime.</p>
<ul>
<li><em>Note:</em> There is an activation event <code>onUri</code> that fires when a uri directed for
the current extension is about to be handled.</li>
</ul>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="handler"></a><span class="ts" id=2235 data-target="#details-2235" data-toggle="collapse"><span class="ident">handler</span><span>: </span><a class="type-ref" href="#UriHandler">UriHandler</a></span></td><td><div class="comment"><p>The uri handler to register for this extension.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#Disposable">Disposable</a></span></td><td><div class="comment"></div></td></tr>
</table>
</div>
</div>



<a name="window.registerWebviewPanelSerializer"></a><span class="ts" id=2237 data-target="#details-2237" data-toggle="collapse"><span class="ident">registerWebviewPanelSerializer</span><span>(</span><span class="ident">viewType</span><span>: </span><a class="type-intrinsic">string</a>, <span class="ident">serializer</span><span>: </span><a class="type-ref" href="#WebviewPanelSerializer">WebviewPanelSerializer</a><span>)</span><span>: </span><a class="type-ref" href="#Disposable">Disposable</a></span>
<div class="details collapse" id="details-2237">
<div class="comment"><p>Registers a webview panel serializer.</p>
<p>Extensions that support reviving should have an <code>&quot;onWebviewPanel:viewType&quot;</code> activation event and
make sure that <a href="#registerWebviewPanelSerializer">registerWebviewPanelSerializer</a> is called during activation.</p>
<p>Only a single serializer may be registered at a time for a given <code>viewType</code>.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="viewType"></a><span class="ts" id=2238 data-target="#details-2238" data-toggle="collapse"><span class="ident">viewType</span><span>: </span><a class="type-intrinsic">string</a></span></td><td><div class="comment"><p>Type of the webview panel that can be serialized.</p>
</div></td></tr>
<tr><td><a name="serializer"></a><span class="ts" id=2239 data-target="#details-2239" data-toggle="collapse"><span class="ident">serializer</span><span>: </span><a class="type-ref" href="#WebviewPanelSerializer">WebviewPanelSerializer</a></span></td><td><div class="comment"><p>Webview serializer.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#Disposable">Disposable</a></span></td><td><div class="comment"></div></td></tr>
</table>
</div>
</div>



<a name="window.setStatusBarMessage"></a><span class="ts" id=2185 data-target="#details-2185" data-toggle="collapse"><span class="ident">setStatusBarMessage</span><span>(</span><span class="ident">text</span><span>: </span><a class="type-intrinsic">string</a>, <span class="ident">hideAfterTimeout</span><span>: </span><a class="type-intrinsic">number</a><span>)</span><span>: </span><a class="type-ref" href="#Disposable">Disposable</a></span>
<div class="details collapse" id="details-2185">
<div class="comment"><p>Set a message to the status bar. This is a short hand for the more powerful
status bar <a href="#window.createStatusBarItem">items</a>.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="text"></a><span class="ts" id=2186 data-target="#details-2186" data-toggle="collapse"><span class="ident">text</span><span>: </span><a class="type-intrinsic">string</a></span></td><td><div class="comment"><p>The message to show, supports icon substitution as in status bar <a href="#StatusBarItem.text">items</a>.</p>
</div></td></tr>
<tr><td><a name="hideAfterTimeout"></a><span class="ts" id=2187 data-target="#details-2187" data-toggle="collapse"><span class="ident">hideAfterTimeout</span><span>: </span><a class="type-intrinsic">number</a></span></td><td><div class="comment"><p>Timeout in milliseconds after which the message will be disposed.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#Disposable">Disposable</a></span></td><td><div class="comment"><p>A disposable which hides the status bar message.</p>
</div></td></tr>
</table>
</div>
</div>



<a name="window.setStatusBarMessage"></a><span class="ts" id=2188 data-target="#details-2188" data-toggle="collapse"><span class="ident">setStatusBarMessage</span><span>(</span><span class="ident">text</span><span>: </span><a class="type-intrinsic">string</a>, <span class="ident">hideWhenDone</span><span>: </span><a class="type-ref" href="#Thenable">Thenable</a>&lt;<a class="type-intrinsic">any</a>&gt;<span>)</span><span>: </span><a class="type-ref" href="#Disposable">Disposable</a></span>
<div class="details collapse" id="details-2188">
<div class="comment"><p>Set a message to the status bar. This is a short hand for the more powerful
status bar <a href="#window.createStatusBarItem">items</a>.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="text"></a><span class="ts" id=2189 data-target="#details-2189" data-toggle="collapse"><span class="ident">text</span><span>: </span><a class="type-intrinsic">string</a></span></td><td><div class="comment"><p>The message to show, supports icon substitution as in status bar <a href="#StatusBarItem.text">items</a>.</p>
</div></td></tr>
<tr><td><a name="hideWhenDone"></a><span class="ts" id=2190 data-target="#details-2190" data-toggle="collapse"><span class="ident">hideWhenDone</span><span>: </span><a class="type-ref" href="#Thenable">Thenable</a>&lt;<a class="type-intrinsic">any</a>&gt;</span></td><td><div class="comment"><p>Thenable on which completion (resolve or reject) the message will be disposed.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#Disposable">Disposable</a></span></td><td><div class="comment"><p>A disposable which hides the status bar message.</p>
</div></td></tr>
</table>
</div>
</div>



<a name="window.setStatusBarMessage"></a><span class="ts" id=2191 data-target="#details-2191" data-toggle="collapse"><span class="ident">setStatusBarMessage</span><span>(</span><span class="ident">text</span><span>: </span><a class="type-intrinsic">string</a><span>)</span><span>: </span><a class="type-ref" href="#Disposable">Disposable</a></span>
<div class="details collapse" id="details-2191">
<div class="comment"><p>Set a message to the status bar. This is a short hand for the more powerful
status bar <a href="#window.createStatusBarItem">items</a>.</p>
<p><em>Note</em> that status bar messages stack and that they must be disposed when no
longer used.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="text"></a><span class="ts" id=2192 data-target="#details-2192" data-toggle="collapse"><span class="ident">text</span><span>: </span><a class="type-intrinsic">string</a></span></td><td><div class="comment"><p>The message to show, supports icon substitution as in status bar <a href="#StatusBarItem.text">items</a>.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#Disposable">Disposable</a></span></td><td><div class="comment"><p>A disposable which hides the status bar message.</p>
</div></td></tr>
</table>
</div>
</div>



<a name="window.showErrorMessage"></a><span class="ts" id=2115 data-target="#details-2115" data-toggle="collapse"><span class="ident">showErrorMessage</span><span>(</span><span class="ident">message</span><span>: </span><a class="type-intrinsic">string</a>, <span>...</span><span class="ident">items</span><span>: </span><a class="type-intrinsic">string</a>[]<span>)</span><span>: </span><a class="type-ref" href="#Thenable">Thenable</a>&lt;<a class="type-intrinsic">string</a> &#124; <a class="type-intrinsic">undefined</a>&gt;</span>
<div class="details collapse" id="details-2115">
<div class="comment"><p>Show an error message.</p>
<ul>
<li><em>see</em> - <a href="#window.showInformationMessage">showInformationMessage</a></li>
</ul>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="message"></a><span class="ts" id=2116 data-target="#details-2116" data-toggle="collapse"><span class="ident">message</span><span>: </span><a class="type-intrinsic">string</a></span></td><td><div class="comment"><p>The message to show.</p>
</div></td></tr>
<tr><td><a name="items"></a><span class="ts" id=2117 data-target="#details-2117" data-toggle="collapse"><span>...</span><span class="ident">items</span><span>: </span><a class="type-intrinsic">string</a>[]</span></td><td><div class="comment"><p>A set of items that will be rendered as actions in the message.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#Thenable">Thenable</a>&lt;<a class="type-intrinsic">string</a> &#124; <a class="type-intrinsic">undefined</a>&gt;</span></td><td><div class="comment"><p>A thenable that resolves to the selected item or <code>undefined</code> when being dismissed.</p>
</div></td></tr>
</table>
</div>
</div>



<a name="window.showErrorMessage"></a><span class="ts" id=2118 data-target="#details-2118" data-toggle="collapse"><span class="ident">showErrorMessage</span><span>(</span><span class="ident">message</span><span>: </span><a class="type-intrinsic">string</a>, <span class="ident">options</span><span>: </span><a class="type-ref" href="#MessageOptions">MessageOptions</a>, <span>...</span><span class="ident">items</span><span>: </span><a class="type-intrinsic">string</a>[]<span>)</span><span>: </span><a class="type-ref" href="#Thenable">Thenable</a>&lt;<a class="type-intrinsic">string</a> &#124; <a class="type-intrinsic">undefined</a>&gt;</span>
<div class="details collapse" id="details-2118">
<div class="comment"><p>Show an error message.</p>
<ul>
<li><em>see</em> - <a href="#window.showInformationMessage">showInformationMessage</a></li>
</ul>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="message"></a><span class="ts" id=2119 data-target="#details-2119" data-toggle="collapse"><span class="ident">message</span><span>: </span><a class="type-intrinsic">string</a></span></td><td><div class="comment"><p>The message to show.</p>
</div></td></tr>
<tr><td><a name="options"></a><span class="ts" id=2120 data-target="#details-2120" data-toggle="collapse"><span class="ident">options</span><span>: </span><a class="type-ref" href="#MessageOptions">MessageOptions</a></span></td><td><div class="comment"><p>Configures the behaviour of the message.</p>
</div></td></tr>
<tr><td><a name="items"></a><span class="ts" id=2121 data-target="#details-2121" data-toggle="collapse"><span>...</span><span class="ident">items</span><span>: </span><a class="type-intrinsic">string</a>[]</span></td><td><div class="comment"><p>A set of items that will be rendered as actions in the message.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#Thenable">Thenable</a>&lt;<a class="type-intrinsic">string</a> &#124; <a class="type-intrinsic">undefined</a>&gt;</span></td><td><div class="comment"><p>A thenable that resolves to the selected item or <code>undefined</code> when being dismissed.</p>
</div></td></tr>
</table>
</div>
</div>



<a name="window.showErrorMessage"></a><span class="ts" id=2122 data-target="#details-2122" data-toggle="collapse"><span class="ident">showErrorMessage</span><span>&lt;</span>T extends <a class="type-ref" href="#MessageItem">MessageItem</a><span>&gt;</span><span>(</span><span class="ident">message</span><span>: </span><a class="type-intrinsic">string</a>, <span>...</span><span class="ident">items</span><span>: </span><a class="type-intrinsic">T</a>[]<span>)</span><span>: </span><a class="type-ref" href="#Thenable">Thenable</a>&lt;<a class="type-intrinsic">T</a> &#124; <a class="type-intrinsic">undefined</a>&gt;</span>
<div class="details collapse" id="details-2122">
<div class="comment"><p>Show an error message.</p>
<ul>
<li><em>see</em> - <a href="#window.showInformationMessage">showInformationMessage</a></li>
</ul>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="message"></a><span class="ts" id=2124 data-target="#details-2124" data-toggle="collapse"><span class="ident">message</span><span>: </span><a class="type-intrinsic">string</a></span></td><td><div class="comment"><p>The message to show.</p>
</div></td></tr>
<tr><td><a name="items"></a><span class="ts" id=2125 data-target="#details-2125" data-toggle="collapse"><span>...</span><span class="ident">items</span><span>: </span><a class="type-intrinsic">T</a>[]</span></td><td><div class="comment"><p>A set of items that will be rendered as actions in the message.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#Thenable">Thenable</a>&lt;<a class="type-intrinsic">T</a> &#124; <a class="type-intrinsic">undefined</a>&gt;</span></td><td><div class="comment"><p>A thenable that resolves to the selected item or <code>undefined</code> when being dismissed.</p>
</div></td></tr>
</table>
</div>
</div>



<a name="window.showErrorMessage"></a><span class="ts" id=2126 data-target="#details-2126" data-toggle="collapse"><span class="ident">showErrorMessage</span><span>&lt;</span>T extends <a class="type-ref" href="#MessageItem">MessageItem</a><span>&gt;</span><span>(</span><span class="ident">message</span><span>: </span><a class="type-intrinsic">string</a>, <span class="ident">options</span><span>: </span><a class="type-ref" href="#MessageOptions">MessageOptions</a>, <span>...</span><span class="ident">items</span><span>: </span><a class="type-intrinsic">T</a>[]<span>)</span><span>: </span><a class="type-ref" href="#Thenable">Thenable</a>&lt;<a class="type-intrinsic">T</a> &#124; <a class="type-intrinsic">undefined</a>&gt;</span>
<div class="details collapse" id="details-2126">
<div class="comment"><p>Show an error message.</p>
<ul>
<li><em>see</em> - <a href="#window.showInformationMessage">showInformationMessage</a></li>
</ul>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="message"></a><span class="ts" id=2128 data-target="#details-2128" data-toggle="collapse"><span class="ident">message</span><span>: </span><a class="type-intrinsic">string</a></span></td><td><div class="comment"><p>The message to show.</p>
</div></td></tr>
<tr><td><a name="options"></a><span class="ts" id=2129 data-target="#details-2129" data-toggle="collapse"><span class="ident">options</span><span>: </span><a class="type-ref" href="#MessageOptions">MessageOptions</a></span></td><td><div class="comment"><p>Configures the behaviour of the message.</p>
</div></td></tr>
<tr><td><a name="items"></a><span class="ts" id=2130 data-target="#details-2130" data-toggle="collapse"><span>...</span><span class="ident">items</span><span>: </span><a class="type-intrinsic">T</a>[]</span></td><td><div class="comment"><p>A set of items that will be rendered as actions in the message.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#Thenable">Thenable</a>&lt;<a class="type-intrinsic">T</a> &#124; <a class="type-intrinsic">undefined</a>&gt;</span></td><td><div class="comment"><p>A thenable that resolves to the selected item or <code>undefined</code> when being dismissed.</p>
</div></td></tr>
</table>
</div>
</div>



<a name="window.showInformationMessage"></a><span class="ts" id=2081 data-target="#details-2081" data-toggle="collapse"><span class="ident">showInformationMessage</span><span>(</span><span class="ident">message</span><span>: </span><a class="type-intrinsic">string</a>, <span>...</span><span class="ident">items</span><span>: </span><a class="type-intrinsic">string</a>[]<span>)</span><span>: </span><a class="type-ref" href="#Thenable">Thenable</a>&lt;<a class="type-intrinsic">string</a> &#124; <a class="type-intrinsic">undefined</a>&gt;</span>
<div class="details collapse" id="details-2081">
<div class="comment"><p>Show an information message to users. Optionally provide an array of items which will be presented as
clickable buttons.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="message"></a><span class="ts" id=2082 data-target="#details-2082" data-toggle="collapse"><span class="ident">message</span><span>: </span><a class="type-intrinsic">string</a></span></td><td><div class="comment"><p>The message to show.</p>
</div></td></tr>
<tr><td><a name="items"></a><span class="ts" id=2083 data-target="#details-2083" data-toggle="collapse"><span>...</span><span class="ident">items</span><span>: </span><a class="type-intrinsic">string</a>[]</span></td><td><div class="comment"><p>A set of items that will be rendered as actions in the message.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#Thenable">Thenable</a>&lt;<a class="type-intrinsic">string</a> &#124; <a class="type-intrinsic">undefined</a>&gt;</span></td><td><div class="comment"><p>A thenable that resolves to the selected item or <code>undefined</code> when being dismissed.</p>
</div></td></tr>
</table>
</div>
</div>



<a name="window.showInformationMessage"></a><span class="ts" id=2084 data-target="#details-2084" data-toggle="collapse"><span class="ident">showInformationMessage</span><span>(</span><span class="ident">message</span><span>: </span><a class="type-intrinsic">string</a>, <span class="ident">options</span><span>: </span><a class="type-ref" href="#MessageOptions">MessageOptions</a>, <span>...</span><span class="ident">items</span><span>: </span><a class="type-intrinsic">string</a>[]<span>)</span><span>: </span><a class="type-ref" href="#Thenable">Thenable</a>&lt;<a class="type-intrinsic">string</a> &#124; <a class="type-intrinsic">undefined</a>&gt;</span>
<div class="details collapse" id="details-2084">
<div class="comment"><p>Show an information message to users. Optionally provide an array of items which will be presented as
clickable buttons.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="message"></a><span class="ts" id=2085 data-target="#details-2085" data-toggle="collapse"><span class="ident">message</span><span>: </span><a class="type-intrinsic">string</a></span></td><td><div class="comment"><p>The message to show.</p>
</div></td></tr>
<tr><td><a name="options"></a><span class="ts" id=2086 data-target="#details-2086" data-toggle="collapse"><span class="ident">options</span><span>: </span><a class="type-ref" href="#MessageOptions">MessageOptions</a></span></td><td><div class="comment"><p>Configures the behaviour of the message.</p>
</div></td></tr>
<tr><td><a name="items"></a><span class="ts" id=2087 data-target="#details-2087" data-toggle="collapse"><span>...</span><span class="ident">items</span><span>: </span><a class="type-intrinsic">string</a>[]</span></td><td><div class="comment"><p>A set of items that will be rendered as actions in the message.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#Thenable">Thenable</a>&lt;<a class="type-intrinsic">string</a> &#124; <a class="type-intrinsic">undefined</a>&gt;</span></td><td><div class="comment"><p>A thenable that resolves to the selected item or <code>undefined</code> when being dismissed.</p>
</div></td></tr>
</table>
</div>
</div>



<a name="window.showInformationMessage"></a><span class="ts" id=2088 data-target="#details-2088" data-toggle="collapse"><span class="ident">showInformationMessage</span><span>&lt;</span>T extends <a class="type-ref" href="#MessageItem">MessageItem</a><span>&gt;</span><span>(</span><span class="ident">message</span><span>: </span><a class="type-intrinsic">string</a>, <span>...</span><span class="ident">items</span><span>: </span><a class="type-intrinsic">T</a>[]<span>)</span><span>: </span><a class="type-ref" href="#Thenable">Thenable</a>&lt;<a class="type-intrinsic">T</a> &#124; <a class="type-intrinsic">undefined</a>&gt;</span>
<div class="details collapse" id="details-2088">
<div class="comment"><p>Show an information message.</p>
<ul>
<li><em>see</em> - <a href="#window.showInformationMessage">showInformationMessage</a></li>
</ul>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="message"></a><span class="ts" id=2090 data-target="#details-2090" data-toggle="collapse"><span class="ident">message</span><span>: </span><a class="type-intrinsic">string</a></span></td><td><div class="comment"><p>The message to show.</p>
</div></td></tr>
<tr><td><a name="items"></a><span class="ts" id=2091 data-target="#details-2091" data-toggle="collapse"><span>...</span><span class="ident">items</span><span>: </span><a class="type-intrinsic">T</a>[]</span></td><td><div class="comment"><p>A set of items that will be rendered as actions in the message.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#Thenable">Thenable</a>&lt;<a class="type-intrinsic">T</a> &#124; <a class="type-intrinsic">undefined</a>&gt;</span></td><td><div class="comment"><p>A thenable that resolves to the selected item or <code>undefined</code> when being dismissed.</p>
</div></td></tr>
</table>
</div>
</div>



<a name="window.showInformationMessage"></a><span class="ts" id=2092 data-target="#details-2092" data-toggle="collapse"><span class="ident">showInformationMessage</span><span>&lt;</span>T extends <a class="type-ref" href="#MessageItem">MessageItem</a><span>&gt;</span><span>(</span><span class="ident">message</span><span>: </span><a class="type-intrinsic">string</a>, <span class="ident">options</span><span>: </span><a class="type-ref" href="#MessageOptions">MessageOptions</a>, <span>...</span><span class="ident">items</span><span>: </span><a class="type-intrinsic">T</a>[]<span>)</span><span>: </span><a class="type-ref" href="#Thenable">Thenable</a>&lt;<a class="type-intrinsic">T</a> &#124; <a class="type-intrinsic">undefined</a>&gt;</span>
<div class="details collapse" id="details-2092">
<div class="comment"><p>Show an information message.</p>
<ul>
<li><em>see</em> - <a href="#window.showInformationMessage">showInformationMessage</a></li>
</ul>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="message"></a><span class="ts" id=2094 data-target="#details-2094" data-toggle="collapse"><span class="ident">message</span><span>: </span><a class="type-intrinsic">string</a></span></td><td><div class="comment"><p>The message to show.</p>
</div></td></tr>
<tr><td><a name="options"></a><span class="ts" id=2095 data-target="#details-2095" data-toggle="collapse"><span class="ident">options</span><span>: </span><a class="type-ref" href="#MessageOptions">MessageOptions</a></span></td><td><div class="comment"><p>Configures the behaviour of the message.</p>
</div></td></tr>
<tr><td><a name="items"></a><span class="ts" id=2096 data-target="#details-2096" data-toggle="collapse"><span>...</span><span class="ident">items</span><span>: </span><a class="type-intrinsic">T</a>[]</span></td><td><div class="comment"><p>A set of items that will be rendered as actions in the message.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#Thenable">Thenable</a>&lt;<a class="type-intrinsic">T</a> &#124; <a class="type-intrinsic">undefined</a>&gt;</span></td><td><div class="comment"><p>A thenable that resolves to the selected item or <code>undefined</code> when being dismissed.</p>
</div></td></tr>
</table>
</div>
</div>



<a name="window.showInputBox"></a><span class="ts" id=2164 data-target="#details-2164" data-toggle="collapse"><span class="ident">showInputBox</span><span>(</span><span class="ident">options</span><span>?</span><span>: </span><a class="type-ref" href="#InputBoxOptions">InputBoxOptions</a>, <span class="ident">token</span><span>?</span><span>: </span><a class="type-ref" href="#CancellationToken">CancellationToken</a><span>)</span><span>: </span><a class="type-ref" href="#Thenable">Thenable</a>&lt;<a class="type-intrinsic">string</a> &#124; <a class="type-intrinsic">undefined</a>&gt;</span>
<div class="details collapse" id="details-2164">
<div class="comment"><p>Opens an input box to ask the user for input.</p>
<p>The returned value will be <code>undefined</code> if the input box was canceled (e.g. pressing ESC). Otherwise the
returned value will be the string typed by the user or an empty string if the user did not type
anything but dismissed the input box with OK.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="options"></a><span class="ts" id=2165 data-target="#details-2165" data-toggle="collapse"><span class="ident">options</span><span>?</span><span>: </span><a class="type-ref" href="#InputBoxOptions">InputBoxOptions</a></span></td><td><div class="comment"><p>Configures the behavior of the input box.</p>
</div></td></tr>
<tr><td><a name="token"></a><span class="ts" id=2166 data-target="#details-2166" data-toggle="collapse"><span class="ident">token</span><span>?</span><span>: </span><a class="type-ref" href="#CancellationToken">CancellationToken</a></span></td><td><div class="comment"><p>A token that can be used to signal cancellation.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#Thenable">Thenable</a>&lt;<a class="type-intrinsic">string</a> &#124; <a class="type-intrinsic">undefined</a>&gt;</span></td><td><div class="comment"><p>A promise that resolves to a string the user provided or to <code>undefined</code> in case of dismissal.</p>
</div></td></tr>
</table>
</div>
</div>



<a name="window.showOpenDialog"></a><span class="ts" id=2158 data-target="#details-2158" data-toggle="collapse"><span class="ident">showOpenDialog</span><span>(</span><span class="ident">options</span><span>: </span><a class="type-ref" href="#OpenDialogOptions">OpenDialogOptions</a><span>)</span><span>: </span><a class="type-ref" href="#Thenable">Thenable</a>&lt;<a class="type-ref" href="#Uri">Uri</a>[] &#124; <a class="type-intrinsic">undefined</a>&gt;</span>
<div class="details collapse" id="details-2158">
<div class="comment"><p>Shows a file open dialog to the user which allows to select a file
for opening-purposes.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="options"></a><span class="ts" id=2159 data-target="#details-2159" data-toggle="collapse"><span class="ident">options</span><span>: </span><a class="type-ref" href="#OpenDialogOptions">OpenDialogOptions</a></span></td><td><div class="comment"><p>Options that control the dialog.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#Thenable">Thenable</a>&lt;<a class="type-ref" href="#Uri">Uri</a>[] &#124; <a class="type-intrinsic">undefined</a>&gt;</span></td><td><div class="comment"><p>A promise that resolves to the selected resources or <code>undefined</code>.</p>
</div></td></tr>
</table>
</div>
</div>



<a name="window.showQuickPick"></a><span class="ts" id=2132 data-target="#details-2132" data-toggle="collapse"><span class="ident">showQuickPick</span><span>(</span><span class="ident">items</span><span>: </span><a class="type-intrinsic">string</a>[] &#124; <a class="type-ref" href="#Thenable">Thenable</a>&lt;<a class="type-intrinsic">string</a>[]&gt;, <span class="ident">options</span><span>: </span><a class="type-ref" href="#QuickPickOptions">QuickPickOptions</a> &#38; {canPickMany: <a class="type-intrinsic">true</a>}, <span class="ident">token</span><span>?</span><span>: </span><a class="type-ref" href="#CancellationToken">CancellationToken</a><span>)</span><span>: </span><a class="type-ref" href="#Thenable">Thenable</a>&lt;<a class="type-intrinsic">string</a>[] &#124; <a class="type-intrinsic">undefined</a>&gt;</span>
<div class="details collapse" id="details-2132">
<div class="comment"><p>Shows a selection list allowing multiple selections.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="items"></a><span class="ts" id=2133 data-target="#details-2133" data-toggle="collapse"><span class="ident">items</span><span>: </span><a class="type-intrinsic">string</a>[] &#124; <a class="type-ref" href="#Thenable">Thenable</a>&lt;<a class="type-intrinsic">string</a>[]&gt;</span></td><td><div class="comment"><p>An array of strings, or a promise that resolves to an array of strings.</p>
</div></td></tr>
<tr><td><a name="options"></a><span class="ts" id=2134 data-target="#details-2134" data-toggle="collapse"><span class="ident">options</span><span>: </span><a class="type-ref" href="#QuickPickOptions">QuickPickOptions</a> &#38; {canPickMany: <a class="type-intrinsic">true</a>}</span></td><td><div class="comment"><p>Configures the behavior of the selection list.</p>
</div></td></tr>
<tr><td><a name="token"></a><span class="ts" id=2137 data-target="#details-2137" data-toggle="collapse"><span class="ident">token</span><span>?</span><span>: </span><a class="type-ref" href="#CancellationToken">CancellationToken</a></span></td><td><div class="comment"><p>A token that can be used to signal cancellation.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#Thenable">Thenable</a>&lt;<a class="type-intrinsic">string</a>[] &#124; <a class="type-intrinsic">undefined</a>&gt;</span></td><td><div class="comment"><p>A promise that resolves to the selected items or <code>undefined</code>.</p>
</div></td></tr>
</table>
</div>
</div>



<a name="window.showQuickPick"></a><span class="ts" id=2138 data-target="#details-2138" data-toggle="collapse"><span class="ident">showQuickPick</span><span>(</span><span class="ident">items</span><span>: </span><a class="type-intrinsic">string</a>[] &#124; <a class="type-ref" href="#Thenable">Thenable</a>&lt;<a class="type-intrinsic">string</a>[]&gt;, <span class="ident">options</span><span>?</span><span>: </span><a class="type-ref" href="#QuickPickOptions">QuickPickOptions</a>, <span class="ident">token</span><span>?</span><span>: </span><a class="type-ref" href="#CancellationToken">CancellationToken</a><span>)</span><span>: </span><a class="type-ref" href="#Thenable">Thenable</a>&lt;<a class="type-intrinsic">string</a> &#124; <a class="type-intrinsic">undefined</a>&gt;</span>
<div class="details collapse" id="details-2138">
<div class="comment"><p>Shows a selection list.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="items"></a><span class="ts" id=2139 data-target="#details-2139" data-toggle="collapse"><span class="ident">items</span><span>: </span><a class="type-intrinsic">string</a>[] &#124; <a class="type-ref" href="#Thenable">Thenable</a>&lt;<a class="type-intrinsic">string</a>[]&gt;</span></td><td><div class="comment"><p>An array of strings, or a promise that resolves to an array of strings.</p>
</div></td></tr>
<tr><td><a name="options"></a><span class="ts" id=2140 data-target="#details-2140" data-toggle="collapse"><span class="ident">options</span><span>?</span><span>: </span><a class="type-ref" href="#QuickPickOptions">QuickPickOptions</a></span></td><td><div class="comment"><p>Configures the behavior of the selection list.</p>
</div></td></tr>
<tr><td><a name="token"></a><span class="ts" id=2141 data-target="#details-2141" data-toggle="collapse"><span class="ident">token</span><span>?</span><span>: </span><a class="type-ref" href="#CancellationToken">CancellationToken</a></span></td><td><div class="comment"><p>A token that can be used to signal cancellation.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#Thenable">Thenable</a>&lt;<a class="type-intrinsic">string</a> &#124; <a class="type-intrinsic">undefined</a>&gt;</span></td><td><div class="comment"><p>A promise that resolves to the selection or <code>undefined</code>.</p>
</div></td></tr>
</table>
</div>
</div>



<a name="window.showQuickPick"></a><span class="ts" id=2142 data-target="#details-2142" data-toggle="collapse"><span class="ident">showQuickPick</span><span>&lt;</span>T extends <a class="type-ref" href="#QuickPickItem">QuickPickItem</a><span>&gt;</span><span>(</span><span class="ident">items</span><span>: </span><a class="type-intrinsic">T</a>[] &#124; <a class="type-ref" href="#Thenable">Thenable</a>&lt;<a class="type-intrinsic">T</a>[]&gt;, <span class="ident">options</span><span>: </span><a class="type-ref" href="#QuickPickOptions">QuickPickOptions</a> &#38; {canPickMany: <a class="type-intrinsic">true</a>}, <span class="ident">token</span><span>?</span><span>: </span><a class="type-ref" href="#CancellationToken">CancellationToken</a><span>)</span><span>: </span><a class="type-ref" href="#Thenable">Thenable</a>&lt;<a class="type-intrinsic">T</a>[] &#124; <a class="type-intrinsic">undefined</a>&gt;</span>
<div class="details collapse" id="details-2142">
<div class="comment"><p>Shows a selection list allowing multiple selections.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="items"></a><span class="ts" id=2144 data-target="#details-2144" data-toggle="collapse"><span class="ident">items</span><span>: </span><a class="type-intrinsic">T</a>[] &#124; <a class="type-ref" href="#Thenable">Thenable</a>&lt;<a class="type-intrinsic">T</a>[]&gt;</span></td><td><div class="comment"><p>An array of items, or a promise that resolves to an array of items.</p>
</div></td></tr>
<tr><td><a name="options"></a><span class="ts" id=2145 data-target="#details-2145" data-toggle="collapse"><span class="ident">options</span><span>: </span><a class="type-ref" href="#QuickPickOptions">QuickPickOptions</a> &#38; {canPickMany: <a class="type-intrinsic">true</a>}</span></td><td><div class="comment"><p>Configures the behavior of the selection list.</p>
</div></td></tr>
<tr><td><a name="token"></a><span class="ts" id=2148 data-target="#details-2148" data-toggle="collapse"><span class="ident">token</span><span>?</span><span>: </span><a class="type-ref" href="#CancellationToken">CancellationToken</a></span></td><td><div class="comment"><p>A token that can be used to signal cancellation.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#Thenable">Thenable</a>&lt;<a class="type-intrinsic">T</a>[] &#124; <a class="type-intrinsic">undefined</a>&gt;</span></td><td><div class="comment"><p>A promise that resolves to the selected items or <code>undefined</code>.</p>
</div></td></tr>
</table>
</div>
</div>



<a name="window.showQuickPick"></a><span class="ts" id=2149 data-target="#details-2149" data-toggle="collapse"><span class="ident">showQuickPick</span><span>&lt;</span>T extends <a class="type-ref" href="#QuickPickItem">QuickPickItem</a><span>&gt;</span><span>(</span><span class="ident">items</span><span>: </span><a class="type-intrinsic">T</a>[] &#124; <a class="type-ref" href="#Thenable">Thenable</a>&lt;<a class="type-intrinsic">T</a>[]&gt;, <span class="ident">options</span><span>?</span><span>: </span><a class="type-ref" href="#QuickPickOptions">QuickPickOptions</a>, <span class="ident">token</span><span>?</span><span>: </span><a class="type-ref" href="#CancellationToken">CancellationToken</a><span>)</span><span>: </span><a class="type-ref" href="#Thenable">Thenable</a>&lt;<a class="type-intrinsic">T</a> &#124; <a class="type-intrinsic">undefined</a>&gt;</span>
<div class="details collapse" id="details-2149">
<div class="comment"><p>Shows a selection list.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="items"></a><span class="ts" id=2151 data-target="#details-2151" data-toggle="collapse"><span class="ident">items</span><span>: </span><a class="type-intrinsic">T</a>[] &#124; <a class="type-ref" href="#Thenable">Thenable</a>&lt;<a class="type-intrinsic">T</a>[]&gt;</span></td><td><div class="comment"><p>An array of items, or a promise that resolves to an array of items.</p>
</div></td></tr>
<tr><td><a name="options"></a><span class="ts" id=2152 data-target="#details-2152" data-toggle="collapse"><span class="ident">options</span><span>?</span><span>: </span><a class="type-ref" href="#QuickPickOptions">QuickPickOptions</a></span></td><td><div class="comment"><p>Configures the behavior of the selection list.</p>
</div></td></tr>
<tr><td><a name="token"></a><span class="ts" id=2153 data-target="#details-2153" data-toggle="collapse"><span class="ident">token</span><span>?</span><span>: </span><a class="type-ref" href="#CancellationToken">CancellationToken</a></span></td><td><div class="comment"><p>A token that can be used to signal cancellation.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#Thenable">Thenable</a>&lt;<a class="type-intrinsic">T</a> &#124; <a class="type-intrinsic">undefined</a>&gt;</span></td><td><div class="comment"><p>A promise that resolves to the selected item or <code>undefined</code>.</p>
</div></td></tr>
</table>
</div>
</div>



<a name="window.showSaveDialog"></a><span class="ts" id=2161 data-target="#details-2161" data-toggle="collapse"><span class="ident">showSaveDialog</span><span>(</span><span class="ident">options</span><span>: </span><a class="type-ref" href="#SaveDialogOptions">SaveDialogOptions</a><span>)</span><span>: </span><a class="type-ref" href="#Thenable">Thenable</a>&lt;<a class="type-ref" href="#Uri">Uri</a> &#124; <a class="type-intrinsic">undefined</a>&gt;</span>
<div class="details collapse" id="details-2161">
<div class="comment"><p>Shows a file save dialog to the user which allows to select a file
for saving-purposes.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="options"></a><span class="ts" id=2162 data-target="#details-2162" data-toggle="collapse"><span class="ident">options</span><span>: </span><a class="type-ref" href="#SaveDialogOptions">SaveDialogOptions</a></span></td><td><div class="comment"><p>Options that control the dialog.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#Thenable">Thenable</a>&lt;<a class="type-ref" href="#Uri">Uri</a> &#124; <a class="type-intrinsic">undefined</a>&gt;</span></td><td><div class="comment"><p>A promise that resolves to the selected resource or <code>undefined</code>.</p>
</div></td></tr>
</table>
</div>
</div>



<a name="window.showTextDocument"></a><span class="ts" id=2067 data-target="#details-2067" data-toggle="collapse"><span class="ident">showTextDocument</span><span>(</span><span class="ident">document</span><span>: </span><a class="type-ref" href="#TextDocument">TextDocument</a>, <span class="ident">column</span><span>?</span><span>: </span><a class="type-ref" href="#ViewColumn">ViewColumn</a>, <span class="ident">preserveFocus</span><span>?</span><span>: </span><a class="type-intrinsic">boolean</a><span>)</span><span>: </span><a class="type-ref" href="#Thenable">Thenable</a>&lt;<a class="type-ref" href="#TextEditor">TextEditor</a>&gt;</span>
<div class="details collapse" id="details-2067">
<div class="comment"><p>Show the given document in a text editor. A <a href="#ViewColumn">column</a> can be provided
to control where the editor is being shown. Might change the <a href="#window.activeTextEditor">active editor</a>.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="document"></a><span class="ts" id=2068 data-target="#details-2068" data-toggle="collapse"><span class="ident">document</span><span>: </span><a class="type-ref" href="#TextDocument">TextDocument</a></span></td><td><div class="comment"><p>A text document to be shown.</p>
</div></td></tr>
<tr><td><a name="column"></a><span class="ts" id=2069 data-target="#details-2069" data-toggle="collapse"><span class="ident">column</span><span>?</span><span>: </span><a class="type-ref" href="#ViewColumn">ViewColumn</a></span></td><td><div class="comment"><p>A view column in which the <a href="#TextEditor">editor</a> should be shown. The default is the <a href="#ViewColumn.Active">active</a>, other values
are adjusted to be <code>Min(column, columnCount + 1)</code>, the <a href="#ViewColumn.Active">active</a>-column is not adjusted. Use <a href="#ViewColumn.Beside"><code>ViewColumn.Beside</code></a>
to open the editor to the side of the currently active one.</p>
</div></td></tr>
<tr><td><a name="preserveFocus"></a><span class="ts" id=2070 data-target="#details-2070" data-toggle="collapse"><span class="ident">preserveFocus</span><span>?</span><span>: </span><a class="type-intrinsic">boolean</a></span></td><td><div class="comment"><p>When <code>true</code> the editor will not take focus.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#Thenable">Thenable</a>&lt;<a class="type-ref" href="#TextEditor">TextEditor</a>&gt;</span></td><td><div class="comment"><p>A promise that resolves to an <a href="#TextEditor">editor</a>.</p>
</div></td></tr>
</table>
</div>
</div>



<a name="window.showTextDocument"></a><span class="ts" id=2071 data-target="#details-2071" data-toggle="collapse"><span class="ident">showTextDocument</span><span>(</span><span class="ident">document</span><span>: </span><a class="type-ref" href="#TextDocument">TextDocument</a>, <span class="ident">options</span><span>?</span><span>: </span><a class="type-ref" href="#TextDocumentShowOptions">TextDocumentShowOptions</a><span>)</span><span>: </span><a class="type-ref" href="#Thenable">Thenable</a>&lt;<a class="type-ref" href="#TextEditor">TextEditor</a>&gt;</span>
<div class="details collapse" id="details-2071">
<div class="comment"><p>Show the given document in a text editor. <a href="#TextDocumentShowOptions">Options</a> can be provided
to control options of the editor is being shown. Might change the <a href="#window.activeTextEditor">active editor</a>.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="document"></a><span class="ts" id=2072 data-target="#details-2072" data-toggle="collapse"><span class="ident">document</span><span>: </span><a class="type-ref" href="#TextDocument">TextDocument</a></span></td><td><div class="comment"><p>A text document to be shown.</p>
</div></td></tr>
<tr><td><a name="options"></a><span class="ts" id=2073 data-target="#details-2073" data-toggle="collapse"><span class="ident">options</span><span>?</span><span>: </span><a class="type-ref" href="#TextDocumentShowOptions">TextDocumentShowOptions</a></span></td><td><div class="comment"><p>(#TextDocumentShowOptions) to configure the behavior of showing the <a href="#TextEditor">editor</a>.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#Thenable">Thenable</a>&lt;<a class="type-ref" href="#TextEditor">TextEditor</a>&gt;</span></td><td><div class="comment"><p>A promise that resolves to an <a href="#TextEditor">editor</a>.</p>
</div></td></tr>
</table>
</div>
</div>



<a name="window.showTextDocument"></a><span class="ts" id=2074 data-target="#details-2074" data-toggle="collapse"><span class="ident">showTextDocument</span><span>(</span><span class="ident">uri</span><span>: </span><a class="type-ref" href="#Uri">Uri</a>, <span class="ident">options</span><span>?</span><span>: </span><a class="type-ref" href="#TextDocumentShowOptions">TextDocumentShowOptions</a><span>)</span><span>: </span><a class="type-ref" href="#Thenable">Thenable</a>&lt;<a class="type-ref" href="#TextEditor">TextEditor</a>&gt;</span>
<div class="details collapse" id="details-2074">
<div class="comment"><p>A short-hand for <code>openTextDocument(uri).then(document =&gt; showTextDocument(document, options))</code>.</p>
<ul>
<li><em>see</em> - <a href="#openTextDocument">openTextDocument</a></li>
</ul>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="uri"></a><span class="ts" id=2075 data-target="#details-2075" data-toggle="collapse"><span class="ident">uri</span><span>: </span><a class="type-ref" href="#Uri">Uri</a></span></td><td><div class="comment"><p>A resource identifier.</p>
</div></td></tr>
<tr><td><a name="options"></a><span class="ts" id=2076 data-target="#details-2076" data-toggle="collapse"><span class="ident">options</span><span>?</span><span>: </span><a class="type-ref" href="#TextDocumentShowOptions">TextDocumentShowOptions</a></span></td><td><div class="comment"><p>(#TextDocumentShowOptions) to configure the behavior of showing the <a href="#TextEditor">editor</a>.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#Thenable">Thenable</a>&lt;<a class="type-ref" href="#TextEditor">TextEditor</a>&gt;</span></td><td><div class="comment"><p>A promise that resolves to an <a href="#TextEditor">editor</a>.</p>
</div></td></tr>
</table>
</div>
</div>



<a name="window.showWarningMessage"></a><span class="ts" id=2098 data-target="#details-2098" data-toggle="collapse"><span class="ident">showWarningMessage</span><span>(</span><span class="ident">message</span><span>: </span><a class="type-intrinsic">string</a>, <span>...</span><span class="ident">items</span><span>: </span><a class="type-intrinsic">string</a>[]<span>)</span><span>: </span><a class="type-ref" href="#Thenable">Thenable</a>&lt;<a class="type-intrinsic">string</a> &#124; <a class="type-intrinsic">undefined</a>&gt;</span>
<div class="details collapse" id="details-2098">
<div class="comment"><p>Show a warning message.</p>
<ul>
<li><em>see</em> - <a href="#window.showInformationMessage">showInformationMessage</a></li>
</ul>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="message"></a><span class="ts" id=2099 data-target="#details-2099" data-toggle="collapse"><span class="ident">message</span><span>: </span><a class="type-intrinsic">string</a></span></td><td><div class="comment"><p>The message to show.</p>
</div></td></tr>
<tr><td><a name="items"></a><span class="ts" id=2100 data-target="#details-2100" data-toggle="collapse"><span>...</span><span class="ident">items</span><span>: </span><a class="type-intrinsic">string</a>[]</span></td><td><div class="comment"><p>A set of items that will be rendered as actions in the message.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#Thenable">Thenable</a>&lt;<a class="type-intrinsic">string</a> &#124; <a class="type-intrinsic">undefined</a>&gt;</span></td><td><div class="comment"><p>A thenable that resolves to the selected item or <code>undefined</code> when being dismissed.</p>
</div></td></tr>
</table>
</div>
</div>



<a name="window.showWarningMessage"></a><span class="ts" id=2101 data-target="#details-2101" data-toggle="collapse"><span class="ident">showWarningMessage</span><span>(</span><span class="ident">message</span><span>: </span><a class="type-intrinsic">string</a>, <span class="ident">options</span><span>: </span><a class="type-ref" href="#MessageOptions">MessageOptions</a>, <span>...</span><span class="ident">items</span><span>: </span><a class="type-intrinsic">string</a>[]<span>)</span><span>: </span><a class="type-ref" href="#Thenable">Thenable</a>&lt;<a class="type-intrinsic">string</a> &#124; <a class="type-intrinsic">undefined</a>&gt;</span>
<div class="details collapse" id="details-2101">
<div class="comment"><p>Show a warning message.</p>
<ul>
<li><em>see</em> - <a href="#window.showInformationMessage">showInformationMessage</a></li>
</ul>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="message"></a><span class="ts" id=2102 data-target="#details-2102" data-toggle="collapse"><span class="ident">message</span><span>: </span><a class="type-intrinsic">string</a></span></td><td><div class="comment"><p>The message to show.</p>
</div></td></tr>
<tr><td><a name="options"></a><span class="ts" id=2103 data-target="#details-2103" data-toggle="collapse"><span class="ident">options</span><span>: </span><a class="type-ref" href="#MessageOptions">MessageOptions</a></span></td><td><div class="comment"><p>Configures the behaviour of the message.</p>
</div></td></tr>
<tr><td><a name="items"></a><span class="ts" id=2104 data-target="#details-2104" data-toggle="collapse"><span>...</span><span class="ident">items</span><span>: </span><a class="type-intrinsic">string</a>[]</span></td><td><div class="comment"><p>A set of items that will be rendered as actions in the message.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#Thenable">Thenable</a>&lt;<a class="type-intrinsic">string</a> &#124; <a class="type-intrinsic">undefined</a>&gt;</span></td><td><div class="comment"><p>A thenable that resolves to the selected item or <code>undefined</code> when being dismissed.</p>
</div></td></tr>
</table>
</div>
</div>



<a name="window.showWarningMessage"></a><span class="ts" id=2105 data-target="#details-2105" data-toggle="collapse"><span class="ident">showWarningMessage</span><span>&lt;</span>T extends <a class="type-ref" href="#MessageItem">MessageItem</a><span>&gt;</span><span>(</span><span class="ident">message</span><span>: </span><a class="type-intrinsic">string</a>, <span>...</span><span class="ident">items</span><span>: </span><a class="type-intrinsic">T</a>[]<span>)</span><span>: </span><a class="type-ref" href="#Thenable">Thenable</a>&lt;<a class="type-intrinsic">T</a> &#124; <a class="type-intrinsic">undefined</a>&gt;</span>
<div class="details collapse" id="details-2105">
<div class="comment"><p>Show a warning message.</p>
<ul>
<li><em>see</em> - <a href="#window.showInformationMessage">showInformationMessage</a></li>
</ul>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="message"></a><span class="ts" id=2107 data-target="#details-2107" data-toggle="collapse"><span class="ident">message</span><span>: </span><a class="type-intrinsic">string</a></span></td><td><div class="comment"><p>The message to show.</p>
</div></td></tr>
<tr><td><a name="items"></a><span class="ts" id=2108 data-target="#details-2108" data-toggle="collapse"><span>...</span><span class="ident">items</span><span>: </span><a class="type-intrinsic">T</a>[]</span></td><td><div class="comment"><p>A set of items that will be rendered as actions in the message.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#Thenable">Thenable</a>&lt;<a class="type-intrinsic">T</a> &#124; <a class="type-intrinsic">undefined</a>&gt;</span></td><td><div class="comment"><p>A thenable that resolves to the selected item or <code>undefined</code> when being dismissed.</p>
</div></td></tr>
</table>
</div>
</div>



<a name="window.showWarningMessage"></a><span class="ts" id=2109 data-target="#details-2109" data-toggle="collapse"><span class="ident">showWarningMessage</span><span>&lt;</span>T extends <a class="type-ref" href="#MessageItem">MessageItem</a><span>&gt;</span><span>(</span><span class="ident">message</span><span>: </span><a class="type-intrinsic">string</a>, <span class="ident">options</span><span>: </span><a class="type-ref" href="#MessageOptions">MessageOptions</a>, <span>...</span><span class="ident">items</span><span>: </span><a class="type-intrinsic">T</a>[]<span>)</span><span>: </span><a class="type-ref" href="#Thenable">Thenable</a>&lt;<a class="type-intrinsic">T</a> &#124; <a class="type-intrinsic">undefined</a>&gt;</span>
<div class="details collapse" id="details-2109">
<div class="comment"><p>Show a warning message.</p>
<ul>
<li><em>see</em> - <a href="#window.showInformationMessage">showInformationMessage</a></li>
</ul>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="message"></a><span class="ts" id=2111 data-target="#details-2111" data-toggle="collapse"><span class="ident">message</span><span>: </span><a class="type-intrinsic">string</a></span></td><td><div class="comment"><p>The message to show.</p>
</div></td></tr>
<tr><td><a name="options"></a><span class="ts" id=2112 data-target="#details-2112" data-toggle="collapse"><span class="ident">options</span><span>: </span><a class="type-ref" href="#MessageOptions">MessageOptions</a></span></td><td><div class="comment"><p>Configures the behaviour of the message.</p>
</div></td></tr>
<tr><td><a name="items"></a><span class="ts" id=2113 data-target="#details-2113" data-toggle="collapse"><span>...</span><span class="ident">items</span><span>: </span><a class="type-intrinsic">T</a>[]</span></td><td><div class="comment"><p>A set of items that will be rendered as actions in the message.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#Thenable">Thenable</a>&lt;<a class="type-intrinsic">T</a> &#124; <a class="type-intrinsic">undefined</a>&gt;</span></td><td><div class="comment"><p>A thenable that resolves to the selected item or <code>undefined</code> when being dismissed.</p>
</div></td></tr>
</table>
</div>
</div>



<a name="window.showWorkspaceFolderPick"></a><span class="ts" id=2155 data-target="#details-2155" data-toggle="collapse"><span class="ident">showWorkspaceFolderPick</span><span>(</span><span class="ident">options</span><span>?</span><span>: </span><a class="type-ref" href="#WorkspaceFolderPickOptions">WorkspaceFolderPickOptions</a><span>)</span><span>: </span><a class="type-ref" href="#Thenable">Thenable</a>&lt;<a class="type-ref" href="#WorkspaceFolder">WorkspaceFolder</a> &#124; <a class="type-intrinsic">undefined</a>&gt;</span>
<div class="details collapse" id="details-2155">
<div class="comment"><p>Shows a selection list of <a href="#workspace.workspaceFolders">workspace folders</a> to pick from.
Returns <code>undefined</code> if no folder is open.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="options"></a><span class="ts" id=2156 data-target="#details-2156" data-toggle="collapse"><span class="ident">options</span><span>?</span><span>: </span><a class="type-ref" href="#WorkspaceFolderPickOptions">WorkspaceFolderPickOptions</a></span></td><td><div class="comment"><p>Configures the behavior of the workspace folder list.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#Thenable">Thenable</a>&lt;<a class="type-ref" href="#WorkspaceFolder">WorkspaceFolder</a> &#124; <a class="type-intrinsic">undefined</a>&gt;</span></td><td><div class="comment"><p>A promise that resolves to the workspace folder or <code>undefined</code>.</p>
</div></td></tr>
</table>
</div>
</div>



<a name="window.withProgress"></a><span class="ts" id=2201 data-target="#details-2201" data-toggle="collapse"><span class="ident">withProgress</span><span>&lt;</span>R<span>&gt;</span><span>(</span><span class="ident">options</span><span>: </span><a class="type-ref" href="#ProgressOptions">ProgressOptions</a>, <span class="ident">task</span><span>: </span>(progress: <a class="type-ref" href="#Progress">Progress</a>&lt;{increment: <a class="type-intrinsic">number</a>, message: <a class="type-intrinsic">string</a>}&gt;, token: <a class="type-ref" href="#CancellationToken">CancellationToken</a>) =&gt; <a class="type-ref" href="#Thenable">Thenable</a>&lt;<a class="type-intrinsic">R</a>&gt;<span>)</span><span>: </span><a class="type-ref" href="#Thenable">Thenable</a>&lt;<a class="type-intrinsic">R</a>&gt;</span>
<div class="details collapse" id="details-2201">
<div class="comment"><p>Show progress in the editor. Progress is shown while running the given callback
and while the promise it returned isn&#39;t resolved nor rejected. The location at which
progress should show (and other details) is defined via the passed <a href="#ProgressOptions"><code>ProgressOptions</code></a>.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="options"></a><span class="ts" id=2203 data-target="#details-2203" data-toggle="collapse"><span class="ident">options</span><span>: </span><a class="type-ref" href="#ProgressOptions">ProgressOptions</a></span></td><td><div class="comment"></div></td></tr>
<tr><td><a name="task"></a><span class="ts" id=2204 data-target="#details-2204" data-toggle="collapse"><span class="ident">task</span><span>: </span>(progress: <a class="type-ref" href="#Progress">Progress</a>&lt;{increment: <a class="type-intrinsic">number</a>, message: <a class="type-intrinsic">string</a>}&gt;, token: <a class="type-ref" href="#CancellationToken">CancellationToken</a>) =&gt; <a class="type-ref" href="#Thenable">Thenable</a>&lt;<a class="type-intrinsic">R</a>&gt;</span></td><td><div class="comment"><p>A callback returning a promise. Progress state can be reported with
the provided <a href="#Progress">progress</a>-object.</p>
<p>To report discrete progress, use <code>increment</code> to indicate how much work has been completed. Each call with
a <code>increment</code> value will be summed up and reflected as overall progress until 100% is reached (a value of
e.g. <code>10</code> accounts for <code>10%</code> of work done).
Note that currently only <code>ProgressLocation.Notification</code> is capable of showing discrete progress.</p>
<p>To monitor if the operation has been cancelled by the user, use the provided <a href="#CancellationToken"><code>CancellationToken</code></a>.
Note that currently only <code>ProgressLocation.Notification</code> is supporting to show a cancel button to cancel the
long running operation.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#Thenable">Thenable</a>&lt;<a class="type-intrinsic">R</a>&gt;</span></td><td><div class="comment"><p>The thenable the task-callback returned.</p>
</div></td></tr>
</table>
</div>
</div>



<a name="window.withScmProgress"></a><span class="ts" id=2194 data-target="#details-2194" data-toggle="collapse"><span class="ident">withScmProgress</span><span>&lt;</span>R<span>&gt;</span><span>(</span><span class="ident">task</span><span>: </span>(progress: <a class="type-ref" href="#Progress">Progress</a>&lt;<a class="type-intrinsic">number</a>&gt;) =&gt; <a class="type-ref" href="#Thenable">Thenable</a>&lt;<a class="type-intrinsic">R</a>&gt;<span>)</span><span>: </span><a class="type-ref" href="#Thenable">Thenable</a>&lt;<a class="type-intrinsic">R</a>&gt;</span>
<div class="details collapse" id="details-2194">
<div class="comment"><p><del>Show progress in the Source Control viewlet while running the given callback and while
its returned promise isn&#39;t resolve or rejected.</del></p>
<ul>
<li><em>deprecated</em> - Use <code>withProgress</code> instead.</li>
</ul>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="task"></a><span class="ts" id=2196 data-target="#details-2196" data-toggle="collapse"><span class="ident">task</span><span>: </span>(progress: <a class="type-ref" href="#Progress">Progress</a>&lt;<a class="type-intrinsic">number</a>&gt;) =&gt; <a class="type-ref" href="#Thenable">Thenable</a>&lt;<a class="type-intrinsic">R</a>&gt;</span></td><td><div class="comment"><p>A callback returning a promise. Progress increments can be reported with
the provided <a href="#Progress">progress</a>-object.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#Thenable">Thenable</a>&lt;<a class="type-intrinsic">R</a>&gt;</span></td><td><div class="comment"><p>The thenable the task did return.</p>
</div></td></tr>
</table>
</div>
</div>

## workspace



<div class="comment"><p>Namespace for dealing with the current workspace. A workspace is the representation
of the folder that has been opened. There is no workspace when just a file but not a
folder has been opened.</p>
<p>The workspace offers support for <a href="#workspace.createFileSystemWatcher">listening</a> to fs
events and for <a href="#workspace.findFiles">finding</a> files. Both perform well and run <em>outside</em>
the editor-process so that they should be always used instead of nodejs-equivalents.</p>
</div>

#### Variables



<a name="workspace.name"></a><span class="ts" id=2243 data-target="#details-2243" data-toggle="collapse"><span class="ident">name</span><span>: </span><a class="type-intrinsic">string</a> &#124; <a class="type-intrinsic">undefined</a></span>
<div class="details collapse" id="details-2243">
<div class="comment"><p>The name of the workspace. <code>undefined</code> when no folder
has been opened.</p>
<ul>
<li><em>readonly</em></li>
</ul>
</div>
</div>



<a name="workspace.rootPath"></a><span class="ts" id=2241 data-target="#details-2241" data-toggle="collapse"><span class="ident">rootPath</span><span>: </span><a class="type-intrinsic">string</a> &#124; <a class="type-intrinsic">undefined</a></span>
<div class="details collapse" id="details-2241">
<div class="comment"><p><del>The folder that is open in the editor. <code>undefined</code> when no folder
has been opened.</del></p>
<ul>
<li><em>deprecated</em> - Use <a href="#workspace.workspaceFolders"><code>workspaceFolders</code></a> instead.</li>
</ul>
<ul>
<li><em>readonly</em></li>
</ul>
</div>
</div>



<a name="workspace.textDocuments"></a><span class="ts" id=2278 data-target="#details-2278" data-toggle="collapse"><span class="ident">textDocuments</span><span>: </span><a class="type-ref" href="#TextDocument">TextDocument</a>[]</span>
<div class="details collapse" id="details-2278">
<div class="comment"><p>All text documents currently known to the system.</p>
<ul>
<li><em>readonly</em></li>
</ul>
</div>
</div>



<a name="workspace.workspaceFolders"></a><span class="ts" id=2242 data-target="#details-2242" data-toggle="collapse"><span class="ident">workspaceFolders</span><span>: </span><a class="type-ref" href="#WorkspaceFolder">WorkspaceFolder</a>[] &#124; <a class="type-intrinsic">undefined</a></span>
<div class="details collapse" id="details-2242">
<div class="comment"><p>List of workspace folders or <code>undefined</code> when no folder is open.
<em>Note</em> that the first entry corresponds to the value of <code>rootPath</code>.</p>
<ul>
<li><em>readonly</em></li>
</ul>
</div>
</div>

#### Events



<a name="workspace.onDidChangeConfiguration"></a><span class="ts" id=2302 data-target="#details-2302" data-toggle="collapse"><span class="ident">onDidChangeConfiguration</span><span>: </span><a class="type-ref" href="#Event">Event</a>&lt;<a class="type-ref" href="#ConfigurationChangeEvent">ConfigurationChangeEvent</a>&gt;</span>
<div class="details collapse" id="details-2302">
<div class="comment"><p>An event that is emitted when the <a href="#WorkspaceConfiguration">configuration</a> changed.</p>
</div>
</div>



<a name="workspace.onDidChangeTextDocument"></a><span class="ts" id=2295 data-target="#details-2295" data-toggle="collapse"><span class="ident">onDidChangeTextDocument</span><span>: </span><a class="type-ref" href="#Event">Event</a>&lt;<a class="type-ref" href="#TextDocumentChangeEvent">TextDocumentChangeEvent</a>&gt;</span>
<div class="details collapse" id="details-2295">
<div class="comment"><p>An event that is emitted when a <a href="#TextDocument">text document</a> is changed. This usually happens
when the <a href="#TextDocument.getText">contents</a> changes but also when other things like the
<a href="#TextDocument.isDirty">dirty</a>-state changes.</p>
</div>
</div>



<a name="workspace.onDidChangeWorkspaceFolders"></a><span class="ts" id=2244 data-target="#details-2244" data-toggle="collapse"><span class="ident">onDidChangeWorkspaceFolders</span><span>: </span><a class="type-ref" href="#Event">Event</a>&lt;<a class="type-ref" href="#WorkspaceFoldersChangeEvent">WorkspaceFoldersChangeEvent</a>&gt;</span>
<div class="details collapse" id="details-2244">
<div class="comment"><p>An event that is emitted when a workspace folder is added or removed.</p>
</div>
</div>



<a name="workspace.onDidCloseTextDocument"></a><span class="ts" id=2294 data-target="#details-2294" data-toggle="collapse"><span class="ident">onDidCloseTextDocument</span><span>: </span><a class="type-ref" href="#Event">Event</a>&lt;<a class="type-ref" href="#TextDocument">TextDocument</a>&gt;</span>
<div class="details collapse" id="details-2294">
<div class="comment"><p>An event that is emitted when a <a href="#TextDocument">text document</a> is disposed or when the language id
of a text document <a href="#languages.setTextDocumentLanguage">has been changed</a>.</p>
<p>To add an event listener when a visible text document is closed, use the <a href="#TextEditor">TextEditor</a> events in the
<a href="#window">window</a> namespace. Note that this event is not emitted when a <a href="#TextEditor">TextEditor</a> is closed
but the document remains open in another <a href="#window.visibleTextEditors">visible text editor</a>.</p>
</div>
</div>



<a name="workspace.onDidOpenTextDocument"></a><span class="ts" id=2293 data-target="#details-2293" data-toggle="collapse"><span class="ident">onDidOpenTextDocument</span><span>: </span><a class="type-ref" href="#Event">Event</a>&lt;<a class="type-ref" href="#TextDocument">TextDocument</a>&gt;</span>
<div class="details collapse" id="details-2293">
<div class="comment"><p>An event that is emitted when a <a href="#TextDocument">text document</a> is opened or when the language id
of a text document <a href="#languages.setTextDocumentLanguage">has been changed</a>.</p>
<p>To add an event listener when a visible text document is opened, use the <a href="#TextEditor">TextEditor</a> events in the
<a href="#window">window</a> namespace. Note that:</p>
<ul>
<li>The event is emitted before the <a href="#TextDocument">document</a> is updated in the
<a href="#window.activeTextEditor">active text editor</a></li>
<li>When a <a href="#TextDocument">text document</a> is already open (e.g.: open in another <a href="#window.visibleTextEditors">visible text editor</a>) this event is not emitted</li>
</ul>
</div>
</div>



<a name="workspace.onDidSaveTextDocument"></a><span class="ts" id=2297 data-target="#details-2297" data-toggle="collapse"><span class="ident">onDidSaveTextDocument</span><span>: </span><a class="type-ref" href="#Event">Event</a>&lt;<a class="type-ref" href="#TextDocument">TextDocument</a>&gt;</span>
<div class="details collapse" id="details-2297">
<div class="comment"><p>An event that is emitted when a <a href="#TextDocument">text document</a> is saved to disk.</p>
</div>
</div>



<a name="workspace.onWillSaveTextDocument"></a><span class="ts" id=2296 data-target="#details-2296" data-toggle="collapse"><span class="ident">onWillSaveTextDocument</span><span>: </span><a class="type-ref" href="#Event">Event</a>&lt;<a class="type-ref" href="#TextDocumentWillSaveEvent">TextDocumentWillSaveEvent</a>&gt;</span>
<div class="details collapse" id="details-2296">
<div class="comment"><p>An event that is emitted when a <a href="#TextDocument">text document</a> will be saved to disk.</p>
<p><em>Note 1:</em> Subscribers can delay saving by registering asynchronous work. For the sake of data integrity the editor
might save without firing this event. For instance when shutting down with dirty files.</p>
<p><em>Note 2:</em> Subscribers are called sequentially and they can <a href="#TextDocumentWillSaveEvent.waitUntil">delay</a> saving
by registering asynchronous work. Protection against misbehaving listeners is implemented as such:</p>
<ul>
<li>there is an overall time budget that all listeners share and if that is exhausted no further listener is called</li>
<li>listeners that take a long time or produce errors frequently will not be called anymore</li>
</ul>
<p>The current thresholds are 1.5 seconds as overall time budget and a listener can misbehave 3 times before being ignored.</p>
</div>
</div>

#### Functions



<a name="workspace.applyEdit"></a><span class="ts" id=2276 data-target="#details-2276" data-toggle="collapse"><span class="ident">applyEdit</span><span>(</span><span class="ident">edit</span><span>: </span><a class="type-ref" href="#WorkspaceEdit">WorkspaceEdit</a><span>)</span><span>: </span><a class="type-ref" href="#Thenable">Thenable</a>&lt;<a class="type-intrinsic">boolean</a>&gt;</span>
<div class="details collapse" id="details-2276">
<div class="comment"><p>Make changes to one or many resources or create, delete, and rename resources as defined by the given
<a href="#WorkspaceEdit">workspace edit</a>.</p>
<p>All changes of a workspace edit are applied in the same order in which they have been added. If
multiple textual inserts are made at the same position, these strings appear in the resulting text
in the order the &#39;inserts&#39; were made. Invalid sequences like &#39;delete file a&#39; -&gt; &#39;insert text in file a&#39;
cause failure of the operation.</p>
<p>When applying a workspace edit that consists only of text edits an &#39;all-or-nothing&#39;-strategy is used.
A workspace edit with resource creations or deletions aborts the operation, e.g. consective edits will
not be attempted, when a single edit fails.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="edit"></a><span class="ts" id=2277 data-target="#details-2277" data-toggle="collapse"><span class="ident">edit</span><span>: </span><a class="type-ref" href="#WorkspaceEdit">WorkspaceEdit</a></span></td><td><div class="comment"><p>A workspace edit.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#Thenable">Thenable</a>&lt;<a class="type-intrinsic">boolean</a>&gt;</span></td><td><div class="comment"><p>A thenable that resolves when the edit could be applied.</p>
</div></td></tr>
</table>
</div>
</div>



<a name="workspace.asRelativePath"></a><span class="ts" id=2249 data-target="#details-2249" data-toggle="collapse"><span class="ident">asRelativePath</span><span>(</span><span class="ident">pathOrUri</span><span>: </span><a class="type-intrinsic">string</a> &#124; <a class="type-ref" href="#Uri">Uri</a>, <span class="ident">includeWorkspaceFolder</span><span>?</span><span>: </span><a class="type-intrinsic">boolean</a><span>)</span><span>: </span><a class="type-intrinsic">string</a></span>
<div class="details collapse" id="details-2249">
<div class="comment"><p>Returns a path that is relative to the workspace folder or folders.</p>
<p>When there are no <a href="#workspace.workspaceFolders">workspace folders</a> or when the path
is not contained in them, the input is returned.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="pathOrUri"></a><span class="ts" id=2250 data-target="#details-2250" data-toggle="collapse"><span class="ident">pathOrUri</span><span>: </span><a class="type-intrinsic">string</a> &#124; <a class="type-ref" href="#Uri">Uri</a></span></td><td><div class="comment"><p>A path or uri. When a uri is given its <a href="#Uri.fsPath">fsPath</a> is used.</p>
</div></td></tr>
<tr><td><a name="includeWorkspaceFolder"></a><span class="ts" id=2251 data-target="#details-2251" data-toggle="collapse"><span class="ident">includeWorkspaceFolder</span><span>?</span><span>: </span><a class="type-intrinsic">boolean</a></span></td><td><div class="comment"><p>When <code>true</code> and when the given path is contained inside a
workspace folder the name of the workspace is prepended. Defaults to <code>true</code> when there are
multiple workspace folders and <code>false</code> otherwise.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-intrinsic">string</a></span></td><td><div class="comment"><p>A path relative to the root or the input.</p>
</div></td></tr>
</table>
</div>
</div>



<a name="workspace.createFileSystemWatcher"></a><span class="ts" id=2261 data-target="#details-2261" data-toggle="collapse"><span class="ident">createFileSystemWatcher</span><span>(</span><span class="ident">globPattern</span><span>: </span><a class="type-ref" href="#GlobPattern">GlobPattern</a>, <span class="ident">ignoreCreateEvents</span><span>?</span><span>: </span><a class="type-intrinsic">boolean</a>, <span class="ident">ignoreChangeEvents</span><span>?</span><span>: </span><a class="type-intrinsic">boolean</a>, <span class="ident">ignoreDeleteEvents</span><span>?</span><span>: </span><a class="type-intrinsic">boolean</a><span>)</span><span>: </span><a class="type-ref" href="#FileSystemWatcher">FileSystemWatcher</a></span>
<div class="details collapse" id="details-2261">
<div class="comment"><p>Creates a file system watcher.</p>
<p>A glob pattern that filters the file events on their absolute path must be provided. Optionally,
flags to ignore certain kinds of events can be provided. To stop listening to events the watcher must be disposed.</p>
<p><em>Note</em> that only files within the current <a href="#workspace.workspaceFolders">workspace folders</a> can be watched.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="globPattern"></a><span class="ts" id=2262 data-target="#details-2262" data-toggle="collapse"><span class="ident">globPattern</span><span>: </span><a class="type-ref" href="#GlobPattern">GlobPattern</a></span></td><td><div class="comment"><p>A <a href="#GlobPattern">glob pattern</a> that is applied to the absolute paths of created, changed,
and deleted files. Use a <a href="#RelativePattern">relative pattern</a> to limit events to a certain <a href="#WorkspaceFolder">workspace folder</a>.</p>
</div></td></tr>
<tr><td><a name="ignoreCreateEvents"></a><span class="ts" id=2263 data-target="#details-2263" data-toggle="collapse"><span class="ident">ignoreCreateEvents</span><span>?</span><span>: </span><a class="type-intrinsic">boolean</a></span></td><td><div class="comment"><p>Ignore when files have been created.</p>
</div></td></tr>
<tr><td><a name="ignoreChangeEvents"></a><span class="ts" id=2264 data-target="#details-2264" data-toggle="collapse"><span class="ident">ignoreChangeEvents</span><span>?</span><span>: </span><a class="type-intrinsic">boolean</a></span></td><td><div class="comment"><p>Ignore when files have been changed.</p>
</div></td></tr>
<tr><td><a name="ignoreDeleteEvents"></a><span class="ts" id=2265 data-target="#details-2265" data-toggle="collapse"><span class="ident">ignoreDeleteEvents</span><span>?</span><span>: </span><a class="type-intrinsic">boolean</a></span></td><td><div class="comment"><p>Ignore when files have been deleted.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#FileSystemWatcher">FileSystemWatcher</a></span></td><td><div class="comment"><p>A new file system watcher instance.</p>
</div></td></tr>
</table>
</div>
</div>



<a name="workspace.findFiles"></a><span class="ts" id=2267 data-target="#details-2267" data-toggle="collapse"><span class="ident">findFiles</span><span>(</span><span class="ident">include</span><span>: </span><a class="type-ref" href="#GlobPattern">GlobPattern</a>, <span class="ident">exclude</span><span>?</span><span>: </span><a class="type-ref" href="#GlobPattern">GlobPattern</a> &#124; <a class="type-intrinsic">null</a>, <span class="ident">maxResults</span><span>?</span><span>: </span><a class="type-intrinsic">number</a>, <span class="ident">token</span><span>?</span><span>: </span><a class="type-ref" href="#CancellationToken">CancellationToken</a><span>)</span><span>: </span><a class="type-ref" href="#Thenable">Thenable</a>&lt;<a class="type-ref" href="#Uri">Uri</a>[]&gt;</span>
<div class="details collapse" id="details-2267">
<div class="comment"><p>Find files across all <a href="#workspace.workspaceFolders">workspace folders</a> in the workspace.</p>
<ul>
<li><em>sample</em> - <code>findFiles(&#39;**/*.js&#39;, &#39;**/node_modules/**&#39;, 10)</code></li>
</ul>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="include"></a><span class="ts" id=2268 data-target="#details-2268" data-toggle="collapse"><span class="ident">include</span><span>: </span><a class="type-ref" href="#GlobPattern">GlobPattern</a></span></td><td><div class="comment"><p>A <a href="#GlobPattern">glob pattern</a> that defines the files to search for. The glob pattern
will be matched against the file paths of resulting matches relative to their workspace. Use a <a href="#RelativePattern">relative pattern</a>
to restrict the search results to a <a href="#WorkspaceFolder">workspace folder</a>.</p>
</div></td></tr>
<tr><td><a name="exclude"></a><span class="ts" id=2269 data-target="#details-2269" data-toggle="collapse"><span class="ident">exclude</span><span>?</span><span>: </span><a class="type-ref" href="#GlobPattern">GlobPattern</a> &#124; <a class="type-intrinsic">null</a></span></td><td><div class="comment"><p>A <a href="#GlobPattern">glob pattern</a> that defines files and folders to exclude. The glob pattern
will be matched against the file paths of resulting matches relative to their workspace. When <code>undefined</code> only default excludes will
apply, when <code>null</code> no excludes will apply.</p>
</div></td></tr>
<tr><td><a name="maxResults"></a><span class="ts" id=2270 data-target="#details-2270" data-toggle="collapse"><span class="ident">maxResults</span><span>?</span><span>: </span><a class="type-intrinsic">number</a></span></td><td><div class="comment"><p>An upper-bound for the result.</p>
</div></td></tr>
<tr><td><a name="token"></a><span class="ts" id=2271 data-target="#details-2271" data-toggle="collapse"><span class="ident">token</span><span>?</span><span>: </span><a class="type-ref" href="#CancellationToken">CancellationToken</a></span></td><td><div class="comment"><p>A token that can be used to signal cancellation to the underlying search engine.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#Thenable">Thenable</a>&lt;<a class="type-ref" href="#Uri">Uri</a>[]&gt;</span></td><td><div class="comment"><p>A thenable that resolves to an array of resource identifiers. Will return no results if no
<a href="#workspace.workspaceFolders">workspace folders</a> are opened.</p>
</div></td></tr>
</table>
</div>
</div>



<a name="workspace.getConfiguration"></a><span class="ts" id=2299 data-target="#details-2299" data-toggle="collapse"><span class="ident">getConfiguration</span><span>(</span><span class="ident">section</span><span>?</span><span>: </span><a class="type-intrinsic">string</a>, <span class="ident">resource</span><span>?</span><span>: </span><a class="type-ref" href="#Uri">Uri</a> &#124; <a class="type-intrinsic">null</a><span>)</span><span>: </span><a class="type-ref" href="#WorkspaceConfiguration">WorkspaceConfiguration</a></span>
<div class="details collapse" id="details-2299">
<div class="comment"><p>Get a workspace configuration object.</p>
<p>When a section-identifier is provided only that part of the configuration
is returned. Dots in the section-identifier are interpreted as child-access,
like <code>{ myExt: { setting: { doIt: true }}}</code> and <code>getConfiguration(&#39;myExt.setting&#39;).get(&#39;doIt&#39;) === true</code>.</p>
<p>When a resource is provided, configuration scoped to that resource is returned.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="section"></a><span class="ts" id=2300 data-target="#details-2300" data-toggle="collapse"><span class="ident">section</span><span>?</span><span>: </span><a class="type-intrinsic">string</a></span></td><td><div class="comment"><p>A dot-separated identifier.</p>
</div></td></tr>
<tr><td><a name="resource"></a><span class="ts" id=2301 data-target="#details-2301" data-toggle="collapse"><span class="ident">resource</span><span>?</span><span>: </span><a class="type-ref" href="#Uri">Uri</a> &#124; <a class="type-intrinsic">null</a></span></td><td><div class="comment"><p>A resource for which the configuration is asked for</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#WorkspaceConfiguration">WorkspaceConfiguration</a></span></td><td><div class="comment"><p>The full configuration or a subset.</p>
</div></td></tr>
</table>
</div>
</div>



<a name="workspace.getWorkspaceFolder"></a><span class="ts" id=2246 data-target="#details-2246" data-toggle="collapse"><span class="ident">getWorkspaceFolder</span><span>(</span><span class="ident">uri</span><span>: </span><a class="type-ref" href="#Uri">Uri</a><span>)</span><span>: </span><a class="type-ref" href="#WorkspaceFolder">WorkspaceFolder</a> &#124; <a class="type-intrinsic">undefined</a></span>
<div class="details collapse" id="details-2246">
<div class="comment"><p>Returns the <a href="#WorkspaceFolder">workspace folder</a> that contains a given uri.</p>
<ul>
<li>returns <code>undefined</code> when the given uri doesn&#39;t match any workspace folder</li>
<li>returns the <em>input</em> when the given uri is a workspace folder itself</li>
</ul>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="uri"></a><span class="ts" id=2247 data-target="#details-2247" data-toggle="collapse"><span class="ident">uri</span><span>: </span><a class="type-ref" href="#Uri">Uri</a></span></td><td><div class="comment"><p>An uri.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#WorkspaceFolder">WorkspaceFolder</a> &#124; <a class="type-intrinsic">undefined</a></span></td><td><div class="comment"><p>A workspace folder or <code>undefined</code></p>
</div></td></tr>
</table>
</div>
</div>



<a name="workspace.openTextDocument"></a><span class="ts" id=2280 data-target="#details-2280" data-toggle="collapse"><span class="ident">openTextDocument</span><span>(</span><span class="ident">uri</span><span>: </span><a class="type-ref" href="#Uri">Uri</a><span>)</span><span>: </span><a class="type-ref" href="#Thenable">Thenable</a>&lt;<a class="type-ref" href="#TextDocument">TextDocument</a>&gt;</span>
<div class="details collapse" id="details-2280">
<div class="comment"><p>Opens a document. Will return early if this document is already open. Otherwise
the document is loaded and the <a href="#workspace.onDidOpenTextDocument">didOpen</a>-event fires.</p>
<p>The document is denoted by an <a href="#Uri">uri</a>. Depending on the <a href="#Uri.scheme">scheme</a> the
following rules apply:</p>
<ul>
<li><code>file</code>-scheme: Open a file on disk, will be rejected if the file does not exist or cannot be loaded.</li>
<li><code>untitled</code>-scheme: A new file that should be saved on disk, e.g. <code>untitled:c:\frodo\new.js</code>. The language
will be derived from the file name.</li>
<li>For all other schemes the registered text document content <a href="#TextDocumentContentProvider">providers</a> are consulted.</li>
</ul>
<p><em>Note</em> that the lifecycle of the returned document is owned by the editor and not by the extension. That means an
<a href="#workspace.onDidCloseTextDocument"><code>onDidClose</code></a>-event can occur at any time after opening it.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="uri"></a><span class="ts" id=2281 data-target="#details-2281" data-toggle="collapse"><span class="ident">uri</span><span>: </span><a class="type-ref" href="#Uri">Uri</a></span></td><td><div class="comment"><p>Identifies the resource to open.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#Thenable">Thenable</a>&lt;<a class="type-ref" href="#TextDocument">TextDocument</a>&gt;</span></td><td><div class="comment"><p>A promise that resolves to a <a href="#TextDocument">document</a>.</p>
</div></td></tr>
</table>
</div>
</div>



<a name="workspace.openTextDocument"></a><span class="ts" id=2282 data-target="#details-2282" data-toggle="collapse"><span class="ident">openTextDocument</span><span>(</span><span class="ident">fileName</span><span>: </span><a class="type-intrinsic">string</a><span>)</span><span>: </span><a class="type-ref" href="#Thenable">Thenable</a>&lt;<a class="type-ref" href="#TextDocument">TextDocument</a>&gt;</span>
<div class="details collapse" id="details-2282">
<div class="comment"><p>A short-hand for <code>openTextDocument(Uri.file(fileName))</code>.</p>
<ul>
<li><em>see</em> - <a href="#openTextDocument">openTextDocument</a></li>
</ul>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="fileName"></a><span class="ts" id=2283 data-target="#details-2283" data-toggle="collapse"><span class="ident">fileName</span><span>: </span><a class="type-intrinsic">string</a></span></td><td><div class="comment"><p>A name of a file on disk.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#Thenable">Thenable</a>&lt;<a class="type-ref" href="#TextDocument">TextDocument</a>&gt;</span></td><td><div class="comment"><p>A promise that resolves to a <a href="#TextDocument">document</a>.</p>
</div></td></tr>
</table>
</div>
</div>



<a name="workspace.openTextDocument"></a><span class="ts" id=2284 data-target="#details-2284" data-toggle="collapse"><span class="ident">openTextDocument</span><span>(</span><span class="ident">options</span><span>?</span><span>: </span>{content: <a class="type-intrinsic">string</a>, language: <a class="type-intrinsic">string</a>}<span>)</span><span>: </span><a class="type-ref" href="#Thenable">Thenable</a>&lt;<a class="type-ref" href="#TextDocument">TextDocument</a>&gt;</span>
<div class="details collapse" id="details-2284">
<div class="comment"><p>Opens an untitled text document. The editor will prompt the user for a file
path when the document is to be saved. The <code>options</code> parameter allows to
specify the <em>language</em> and/or the <em>content</em> of the document.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="options"></a><span class="ts" id=2285 data-target="#details-2285" data-toggle="collapse"><span class="ident">options</span><span>?</span><span>: </span>{content: <a class="type-intrinsic">string</a>, language: <a class="type-intrinsic">string</a>}</span></td><td><div class="comment"><p>Options to control how the document will be created.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#Thenable">Thenable</a>&lt;<a class="type-ref" href="#TextDocument">TextDocument</a>&gt;</span></td><td><div class="comment"><p>A promise that resolves to a <a href="#TextDocument">document</a>.</p>
</div></td></tr>
</table>
</div>
</div>



<a name="workspace.registerFileSystemProvider"></a><span class="ts" id=2308 data-target="#details-2308" data-toggle="collapse"><span class="ident">registerFileSystemProvider</span><span>(</span><span class="ident">scheme</span><span>: </span><a class="type-intrinsic">string</a>, <span class="ident">provider</span><span>: </span><a class="type-ref" href="#FileSystemProvider">FileSystemProvider</a>, <span class="ident">options</span><span>?</span><span>: </span>{isCaseSensitive: <a class="type-intrinsic">boolean</a>, isReadonly: <a class="type-intrinsic">boolean</a>}<span>)</span><span>: </span><a class="type-ref" href="#Disposable">Disposable</a></span>
<div class="details collapse" id="details-2308">
<div class="comment"><p>Register a filesystem provider for a given scheme, e.g. <code>ftp</code>.</p>
<p>There can only be one provider per scheme and an error is being thrown when a scheme
has been claimed by another provider or when it is reserved.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="scheme"></a><span class="ts" id=2309 data-target="#details-2309" data-toggle="collapse"><span class="ident">scheme</span><span>: </span><a class="type-intrinsic">string</a></span></td><td><div class="comment"><p>The uri-<a href="#Uri.scheme">scheme</a> the provider registers for.</p>
</div></td></tr>
<tr><td><a name="provider"></a><span class="ts" id=2310 data-target="#details-2310" data-toggle="collapse"><span class="ident">provider</span><span>: </span><a class="type-ref" href="#FileSystemProvider">FileSystemProvider</a></span></td><td><div class="comment"><p>The filesystem provider.</p>
</div></td></tr>
<tr><td><a name="options"></a><span class="ts" id=2311 data-target="#details-2311" data-toggle="collapse"><span class="ident">options</span><span>?</span><span>: </span>{isCaseSensitive: <a class="type-intrinsic">boolean</a>, isReadonly: <a class="type-intrinsic">boolean</a>}</span></td><td><div class="comment"><p>Immutable metadata about the provider.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#Disposable">Disposable</a></span></td><td><div class="comment"><p>A <a href="#Disposable">disposable</a> that unregisters this provider when being disposed.</p>
</div></td></tr>
</table>
</div>
</div>



<a name="workspace.registerTaskProvider"></a><span class="ts" id=2304 data-target="#details-2304" data-toggle="collapse"><span class="ident">registerTaskProvider</span><span>(</span><span class="ident">type</span><span>: </span><a class="type-intrinsic">string</a>, <span class="ident">provider</span><span>: </span><a class="type-ref" href="#TaskProvider">TaskProvider</a><span>)</span><span>: </span><a class="type-ref" href="#Disposable">Disposable</a></span>
<div class="details collapse" id="details-2304">
<div class="comment"><p><del>Register a task provider.</del></p>
<ul>
<li><em>deprecated</em> - Use the corresponding function on the <code>tasks</code> namespace instead</li>
</ul>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="type"></a><span class="ts" id=2305 data-target="#details-2305" data-toggle="collapse"><span class="ident">type</span><span>: </span><a class="type-intrinsic">string</a></span></td><td><div class="comment"><p>The task kind type this provider is registered for.</p>
</div></td></tr>
<tr><td><a name="provider"></a><span class="ts" id=2306 data-target="#details-2306" data-toggle="collapse"><span class="ident">provider</span><span>: </span><a class="type-ref" href="#TaskProvider">TaskProvider</a></span></td><td><div class="comment"><p>A task provider.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#Disposable">Disposable</a></span></td><td><div class="comment"><p>A <a href="#Disposable">disposable</a> that unregisters this provider when being disposed.</p>
</div></td></tr>
</table>
</div>
</div>



<a name="workspace.registerTextDocumentContentProvider"></a><span class="ts" id=2290 data-target="#details-2290" data-toggle="collapse"><span class="ident">registerTextDocumentContentProvider</span><span>(</span><span class="ident">scheme</span><span>: </span><a class="type-intrinsic">string</a>, <span class="ident">provider</span><span>: </span><a class="type-ref" href="#TextDocumentContentProvider">TextDocumentContentProvider</a><span>)</span><span>: </span><a class="type-ref" href="#Disposable">Disposable</a></span>
<div class="details collapse" id="details-2290">
<div class="comment"><p>Register a text document content provider.</p>
<p>Only one provider can be registered per scheme.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="scheme"></a><span class="ts" id=2291 data-target="#details-2291" data-toggle="collapse"><span class="ident">scheme</span><span>: </span><a class="type-intrinsic">string</a></span></td><td><div class="comment"><p>The uri-scheme to register for.</p>
</div></td></tr>
<tr><td><a name="provider"></a><span class="ts" id=2292 data-target="#details-2292" data-toggle="collapse"><span class="ident">provider</span><span>: </span><a class="type-ref" href="#TextDocumentContentProvider">TextDocumentContentProvider</a></span></td><td><div class="comment"><p>A content provider.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#Disposable">Disposable</a></span></td><td><div class="comment"><p>A <a href="#Disposable">disposable</a> that unregisters this provider when being disposed.</p>
</div></td></tr>
</table>
</div>
</div>



<a name="workspace.saveAll"></a><span class="ts" id=2273 data-target="#details-2273" data-toggle="collapse"><span class="ident">saveAll</span><span>(</span><span class="ident">includeUntitled</span><span>?</span><span>: </span><a class="type-intrinsic">boolean</a><span>)</span><span>: </span><a class="type-ref" href="#Thenable">Thenable</a>&lt;<a class="type-intrinsic">boolean</a>&gt;</span>
<div class="details collapse" id="details-2273">
<div class="comment"><p>Save all dirty files.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="includeUntitled"></a><span class="ts" id=2274 data-target="#details-2274" data-toggle="collapse"><span class="ident">includeUntitled</span><span>?</span><span>: </span><a class="type-intrinsic">boolean</a></span></td><td><div class="comment"><p>Also save files that have been created during this session.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#Thenable">Thenable</a>&lt;<a class="type-intrinsic">boolean</a>&gt;</span></td><td><div class="comment"><p>A thenable that resolves when the files have been saved.</p>
</div></td></tr>
</table>
</div>
</div>



<a name="workspace.updateWorkspaceFolders"></a><span class="ts" id=2253 data-target="#details-2253" data-toggle="collapse"><span class="ident">updateWorkspaceFolders</span><span>(</span><span class="ident">start</span><span>: </span><a class="type-intrinsic">number</a>, <span class="ident">deleteCount</span><span>: </span><a class="type-intrinsic">number</a> &#124; <a class="type-intrinsic">undefined</a> &#124; <a class="type-intrinsic">null</a>, <span>...</span><span class="ident">workspaceFoldersToAdd</span><span>: </span>{name: <a class="type-intrinsic">string</a>, uri: <a class="type-ref" href="#Uri">Uri</a>}[]<span>)</span><span>: </span><a class="type-intrinsic">boolean</a></span>
<div class="details collapse" id="details-2253">
<div class="comment"><p>This method replaces <code>deleteCount</code> <a href="#workspace.workspaceFolders">workspace folders</a> starting at index <code>start</code>
by an optional set of <code>workspaceFoldersToAdd</code> on the <code>vscode.workspace.workspaceFolders</code> array. This &quot;splice&quot;
behavior can be used to add, remove and change workspace folders in a single operation.</p>
<p>If the first workspace folder is added, removed or changed, the currently executing extensions (including the
one that called this method) will be terminated and restarted so that the (deprecated) <code>rootPath</code> property is
updated to point to the first workspace folder.</p>
<p>Use the <a href="#onDidChangeWorkspaceFolders"><code>onDidChangeWorkspaceFolders()</code></a> event to get notified when the
workspace folders have been updated.</p>
<p><strong>Example:</strong> adding a new workspace folder at the end of workspace folders</p>

<pre><code class="lang-typescript">workspace.updateWorkspaceFolders(workspace.workspaceFolders ? workspace.workspaceFolders.length : 0, null, { uri: ...});
</code></pre>
<p><strong>Example:</strong> removing the first workspace folder</p>

<pre><code class="lang-typescript">workspace.updateWorkspaceFolders(0, 1);
</code></pre>
<p><strong>Example:</strong> replacing an existing workspace folder with a new one</p>

<pre><code class="lang-typescript">workspace.updateWorkspaceFolders(0, 1, { uri: ...});
</code></pre>
<p>It is valid to remove an existing workspace folder and add it again with a different name
to rename that folder.</p>
<p><strong>Note:</strong> it is not valid to call <a href="#updateWorkspaceFolders">updateWorkspaceFolders()</a> multiple times
without waiting for the <a href="#onDidChangeWorkspaceFolders"><code>onDidChangeWorkspaceFolders()</code></a> to fire.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="start"></a><span class="ts" id=2254 data-target="#details-2254" data-toggle="collapse"><span class="ident">start</span><span>: </span><a class="type-intrinsic">number</a></span></td><td><div class="comment"><p>the zero-based location in the list of currently opened <a href="#WorkspaceFolder">workspace folders</a>
from which to start deleting workspace folders.</p>
</div></td></tr>
<tr><td><a name="deleteCount"></a><span class="ts" id=2255 data-target="#details-2255" data-toggle="collapse"><span class="ident">deleteCount</span><span>: </span><a class="type-intrinsic">number</a> &#124; <a class="type-intrinsic">undefined</a> &#124; <a class="type-intrinsic">null</a></span></td><td><div class="comment"><p>the optional number of workspace folders to remove.</p>
</div></td></tr>
<tr><td><a name="workspaceFoldersToAdd"></a><span class="ts" id=2256 data-target="#details-2256" data-toggle="collapse"><span>...</span><span class="ident">workspaceFoldersToAdd</span><span>: </span>{name: <a class="type-intrinsic">string</a>, uri: <a class="type-ref" href="#Uri">Uri</a>}[]</span></td><td><div class="comment"><p>the optional variable set of workspace folders to add in place of the deleted ones.
Each workspace is identified with a mandatory URI and an optional name.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-intrinsic">boolean</a></span></td><td><div class="comment"><p>true if the operation was successfully started and false otherwise if arguments were used that would result
in invalid workspace folder state (e.g. 2 folders with the same URI).</p>
</div></td></tr>
</table>
</div>
</div>

### <a name="Breakpoint"></a><span class="code-item" id=1947>Breakpoint</span>



<div class="comment"><p>The base class of all breakpoint types.</p>
</div>

#### Constructors



<a name="Breakpoint.new Breakpoint"></a><span class="ts" id=1954 data-target="#details-1954" data-toggle="collapse"><span class="ident">new Breakpoint</span><span>(</span><span class="ident">enabled</span><span>?</span><span>: </span><a class="type-intrinsic">boolean</a>, <span class="ident">condition</span><span>?</span><span>: </span><a class="type-intrinsic">string</a>, <span class="ident">hitCondition</span><span>?</span><span>: </span><a class="type-intrinsic">string</a>, <span class="ident">logMessage</span><span>?</span><span>: </span><a class="type-intrinsic">string</a><span>)</span><span>: </span><a class="type-ref" href="#Breakpoint">Breakpoint</a></span>
<div class="details collapse" id="details-1954">
<div class="comment"></div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="enabled"></a><span class="ts" id=1955 data-target="#details-1955" data-toggle="collapse"><span class="ident">enabled</span><span>?</span><span>: </span><a class="type-intrinsic">boolean</a></span></td><td><div class="comment"></div></td></tr>
<tr><td><a name="condition"></a><span class="ts" id=1956 data-target="#details-1956" data-toggle="collapse"><span class="ident">condition</span><span>?</span><span>: </span><a class="type-intrinsic">string</a></span></td><td><div class="comment"></div></td></tr>
<tr><td><a name="hitCondition"></a><span class="ts" id=1957 data-target="#details-1957" data-toggle="collapse"><span class="ident">hitCondition</span><span>?</span><span>: </span><a class="type-intrinsic">string</a></span></td><td><div class="comment"></div></td></tr>
<tr><td><a name="logMessage"></a><span class="ts" id=1958 data-target="#details-1958" data-toggle="collapse"><span class="ident">logMessage</span><span>?</span><span>: </span><a class="type-intrinsic">string</a></span></td><td><div class="comment"></div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#Breakpoint">Breakpoint</a></span></td><td><div class="comment"></div></td></tr>
</table>
</div>
</div>

#### Properties



<a name="Breakpoint.condition"></a><span class="ts" id=1950 data-target="#details-1950" data-toggle="collapse"><span class="ident">condition</span><span>?</span><span>: </span><a class="type-intrinsic">string</a></span>
<div class="details collapse" id="details-1950">
<div class="comment"><p>An optional expression for conditional breakpoints.</p>
</div>
</div>



<a name="Breakpoint.enabled"></a><span class="ts" id=1949 data-target="#details-1949" data-toggle="collapse"><span class="ident">enabled</span><span>: </span><a class="type-intrinsic">boolean</a></span>
<div class="details collapse" id="details-1949">
<div class="comment"><p>Is breakpoint enabled.</p>
</div>
</div>



<a name="Breakpoint.hitCondition"></a><span class="ts" id=1951 data-target="#details-1951" data-toggle="collapse"><span class="ident">hitCondition</span><span>?</span><span>: </span><a class="type-intrinsic">string</a></span>
<div class="details collapse" id="details-1951">
<div class="comment"><p>An optional expression that controls how many hits of the breakpoint are ignored.</p>
</div>
</div>



<a name="Breakpoint.id"></a><span class="ts" id=1948 data-target="#details-1948" data-toggle="collapse"><span class="ident">id</span><span>: </span><a class="type-intrinsic">string</a></span>
<div class="details collapse" id="details-1948">
<div class="comment"><p>The unique ID of the breakpoint.</p>
</div>
</div>



<a name="Breakpoint.logMessage"></a><span class="ts" id=1952 data-target="#details-1952" data-toggle="collapse"><span class="ident">logMessage</span><span>?</span><span>: </span><a class="type-intrinsic">string</a></span>
<div class="details collapse" id="details-1952">
<div class="comment"><p>An optional message that gets logged when this breakpoint is hit. Embedded expressions within {} are interpolated by the debug adapter.</p>
</div>
</div>

### <a name="BreakpointsChangeEvent"></a><span class="code-item" id=1943>BreakpointsChangeEvent</span>



<div class="comment"><p>An event describing the changes to the set of <a href="#Breakpoint">breakpoints</a>.</p>
</div>

#### Properties



<a name="BreakpointsChangeEvent.added"></a><span class="ts" id=1944 data-target="#details-1944" data-toggle="collapse"><span class="ident">added</span><span>: </span><a class="type-ref" href="#Breakpoint">Breakpoint</a>[]</span>
<div class="details collapse" id="details-1944">
<div class="comment"><p>Added breakpoints.</p>
</div>
</div>



<a name="BreakpointsChangeEvent.changed"></a><span class="ts" id=1946 data-target="#details-1946" data-toggle="collapse"><span class="ident">changed</span><span>: </span><a class="type-ref" href="#Breakpoint">Breakpoint</a>[]</span>
<div class="details collapse" id="details-1946">
<div class="comment"><p>Changed breakpoints.</p>
</div>
</div>



<a name="BreakpointsChangeEvent.removed"></a><span class="ts" id=1945 data-target="#details-1945" data-toggle="collapse"><span class="ident">removed</span><span>: </span><a class="type-ref" href="#Breakpoint">Breakpoint</a>[]</span>
<div class="details collapse" id="details-1945">
<div class="comment"><p>Removed breakpoints.</p>
</div>
</div>

### <a name="CancellationToken"></a><span class="code-item" id=425>CancellationToken</span>



<div class="comment"><p>A cancellation token is passed to an asynchronous or long running
operation to request cancellation, like cancelling a request
for completion items because the user continued to type.</p>
<p>To get an instance of a <code>CancellationToken</code> use a
<a href="#CancellationTokenSource">CancellationTokenSource</a>.</p>
</div>

#### Properties



<a name="CancellationToken.isCancellationRequested"></a><span class="ts" id=426 data-target="#details-426" data-toggle="collapse"><span class="ident">isCancellationRequested</span><span>: </span><a class="type-intrinsic">boolean</a></span>
<div class="details collapse" id="details-426">
<div class="comment"><p>Is <code>true</code> when the token has been cancelled, <code>false</code> otherwise.</p>
</div>
</div>



<a name="CancellationToken.onCancellationRequested"></a><span class="ts" id=427 data-target="#details-427" data-toggle="collapse"><span class="ident">onCancellationRequested</span><span>: </span><a class="type-ref" href="#Event">Event</a>&lt;<a class="type-intrinsic">any</a>&gt;</span>
<div class="details collapse" id="details-427">
<div class="comment"><p>An <a href="#Event">event</a> which fires upon cancellation.</p>
</div>
</div>

### <a name="CancellationTokenSource"></a><span class="code-item" id=428>CancellationTokenSource</span>



<div class="comment"><p>A cancellation source creates and controls a <a href="#CancellationToken">cancellation token</a>.</p>
</div>

#### Properties



<a name="CancellationTokenSource.token"></a><span class="ts" id=429 data-target="#details-429" data-toggle="collapse"><span class="ident">token</span><span>: </span><a class="type-ref" href="#CancellationToken">CancellationToken</a></span>
<div class="details collapse" id="details-429">
<div class="comment"><p>The cancellation token of this source.</p>
</div>
</div>

#### Methods



<a name="CancellationTokenSource.cancel"></a><span class="ts" id=431 data-target="#details-431" data-toggle="collapse"><span class="ident">cancel</span><span>(</span><span>)</span><span>: </span><a class="type-intrinsic">void</a></span>
<div class="details collapse" id="details-431">
<div class="comment"><p>Signal cancellation on the token.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-intrinsic">void</a></span></td><td><div class="comment"></div></td></tr>
</table>
</div>
</div>



<a name="CancellationTokenSource.dispose"></a><span class="ts" id=433 data-target="#details-433" data-toggle="collapse"><span class="ident">dispose</span><span>(</span><span>)</span><span>: </span><a class="type-intrinsic">void</a></span>
<div class="details collapse" id="details-433">
<div class="comment"><p>Dispose object and free resources.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-intrinsic">void</a></span></td><td><div class="comment"></div></td></tr>
</table>
</div>
</div>

### <a name="CharacterPair"></a><span class="code-item" id=1999>CharacterPair</span>



<div class="comment"><p>A tuple of two characters, like a pair of
opening and closing brackets.</p>
</div>



<a name="CharacterPair"></a><span class="ts" id=1999 data-target="#details-1999" data-toggle="collapse"><span class="ident">CharacterPair</span><span>: </span>[<a class="type-intrinsic">string</a>, <a class="type-intrinsic">string</a>]</span>

### <a name="Clipboard"></a><span class="code-item" id=1600>Clipboard</span>



<div class="comment"><p>The clipboard provides read and write access to the system&#39;s clipboard.</p>
</div>

#### Methods



<a name="Clipboard.readText"></a><span class="ts" id=1602 data-target="#details-1602" data-toggle="collapse"><span class="ident">readText</span><span>(</span><span>)</span><span>: </span><a class="type-ref" href="#Thenable">Thenable</a>&lt;<a class="type-intrinsic">string</a>&gt;</span>
<div class="details collapse" id="details-1602">
<div class="comment"><p>Read the current clipboard contents as text.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#Thenable">Thenable</a>&lt;<a class="type-intrinsic">string</a>&gt;</span></td><td><div class="comment"><p>A thenable that resolves to a string.</p>
</div></td></tr>
</table>
</div>
</div>



<a name="Clipboard.writeText"></a><span class="ts" id=1604 data-target="#details-1604" data-toggle="collapse"><span class="ident">writeText</span><span>(</span><span class="ident">value</span><span>: </span><a class="type-intrinsic">string</a><span>)</span><span>: </span><a class="type-ref" href="#Thenable">Thenable</a>&lt;<a class="type-intrinsic">void</a>&gt;</span>
<div class="details collapse" id="details-1604">
<div class="comment"><p>Writes text into the clipboard.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="value"></a><span class="ts" id=1605 data-target="#details-1605" data-toggle="collapse"><span class="ident">value</span><span>: </span><a class="type-intrinsic">string</a></span></td><td><div class="comment"></div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#Thenable">Thenable</a>&lt;<a class="type-intrinsic">void</a>&gt;</span></td><td><div class="comment"><p>A thenable that resolves when writing happened.</p>
</div></td></tr>
</table>
</div>
</div>

### <a name="CodeAction"></a><span class="code-item" id=572>CodeAction</span>



<div class="comment"><p>A code action represents a change that can be performed in code, e.g. to fix a problem or
to refactor code.</p>
<p>A CodeAction must set either <a href="#CodeAction.edit"><code>edit</code></a> and/or a <a href="#CodeAction.command"><code>command</code></a>. If both are supplied, the <code>edit</code> is applied first, then the command is executed.</p>
</div>

#### Constructors



<a name="CodeAction.new CodeAction"></a><span class="ts" id=579 data-target="#details-579" data-toggle="collapse"><span class="ident">new CodeAction</span><span>(</span><span class="ident">title</span><span>: </span><a class="type-intrinsic">string</a>, <span class="ident">kind</span><span>?</span><span>: </span><a class="type-ref" href="#CodeActionKind">CodeActionKind</a><span>)</span><span>: </span><a class="type-ref" href="#CodeAction">CodeAction</a></span>
<div class="details collapse" id="details-579">
<div class="comment"><p>Creates a new code action.</p>
<p>A code action must have at least a <a href="#CodeAction.title">title</a> and <a href="#CodeAction.edit">edits</a>
and/or a <a href="#CodeAction.command">command</a>.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="title"></a><span class="ts" id=580 data-target="#details-580" data-toggle="collapse"><span class="ident">title</span><span>: </span><a class="type-intrinsic">string</a></span></td><td><div class="comment"><p>The title of the code action.</p>
</div></td></tr>
<tr><td><a name="kind"></a><span class="ts" id=581 data-target="#details-581" data-toggle="collapse"><span class="ident">kind</span><span>?</span><span>: </span><a class="type-ref" href="#CodeActionKind">CodeActionKind</a></span></td><td><div class="comment"><p>The kind of the code action.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#CodeAction">CodeAction</a></span></td><td><div class="comment"></div></td></tr>
</table>
</div>
</div>

#### Properties



<a name="CodeAction.command"></a><span class="ts" id=576 data-target="#details-576" data-toggle="collapse"><span class="ident">command</span><span>?</span><span>: </span><a class="type-ref" href="#Command">Command</a></span>
<div class="details collapse" id="details-576">
<div class="comment"><p>A <a href="#Command">command</a> this code action executes.</p>
</div>
</div>



<a name="CodeAction.diagnostics"></a><span class="ts" id=575 data-target="#details-575" data-toggle="collapse"><span class="ident">diagnostics</span><span>?</span><span>: </span><a class="type-ref" href="#Diagnostic">Diagnostic</a>[]</span>
<div class="details collapse" id="details-575">
<div class="comment"><p><a href="#Diagnostic">Diagnostics</a> that this code action resolves.</p>
</div>
</div>



<a name="CodeAction.edit"></a><span class="ts" id=574 data-target="#details-574" data-toggle="collapse"><span class="ident">edit</span><span>?</span><span>: </span><a class="type-ref" href="#WorkspaceEdit">WorkspaceEdit</a></span>
<div class="details collapse" id="details-574">
<div class="comment"><p>A <a href="#WorkspaceEdit">workspace edit</a> this code action performs.</p>
</div>
</div>



<a name="CodeAction.kind"></a><span class="ts" id=577 data-target="#details-577" data-toggle="collapse"><span class="ident">kind</span><span>?</span><span>: </span><a class="type-ref" href="#CodeActionKind">CodeActionKind</a></span>
<div class="details collapse" id="details-577">
<div class="comment"><p><a href="#CodeActionKind">Kind</a> of the code action.</p>
<p>Used to filter code actions.</p>
</div>
</div>



<a name="CodeAction.title"></a><span class="ts" id=573 data-target="#details-573" data-toggle="collapse"><span class="ident">title</span><span>: </span><a class="type-intrinsic">string</a></span>
<div class="details collapse" id="details-573">
<div class="comment"><p>A short, human-readable, title for this code action.</p>
</div>
</div>

### <a name="CodeActionContext"></a><span class="code-item" id=569>CodeActionContext</span>



<div class="comment"><p>Contains additional diagnostic information about the context in which
a <a href="#CodeActionProvider.provideCodeActions">code action</a> is run.</p>
</div>

#### Events



<a name="CodeActionContext.only"></a><span class="ts" id=571 data-target="#details-571" data-toggle="collapse"><span class="ident">only</span><span>?</span><span>: </span><a class="type-ref" href="#CodeActionKind">CodeActionKind</a></span>
<div class="details collapse" id="details-571">
<div class="comment"><p>Requested kind of actions to return.</p>
<p>Actions not of this kind are filtered out before being shown by the lightbulb.</p>
</div>
</div>

#### Properties



<a name="CodeActionContext.diagnostics"></a><span class="ts" id=570 data-target="#details-570" data-toggle="collapse"><span class="ident">diagnostics</span><span>: </span><a class="type-ref" href="#Diagnostic">Diagnostic</a>[]</span>
<div class="details collapse" id="details-570">
<div class="comment"><p>An array of diagnostics.</p>
</div>
</div>

### <a name="CodeActionKind"></a><span class="code-item" id=550>CodeActionKind</span>



<div class="comment"><p>Kind of a code action.</p>
<p>Kinds are a hierarchical list of identifiers separated by <code>.</code>, e.g. <code>&quot;refactor.extract.function&quot;</code>.</p>
<p>Code action kinds are used by VS Code for UI elements such as the refactoring context menu. Users
can also trigger code actions with a specific kind with the <code>editor.action.codeAction</code> command.</p>
</div>

#### Static



<a name="CodeActionKind.Empty"></a><span class="ts" id=551 data-target="#details-551" data-toggle="collapse"><span class="ident">Empty</span><span>: </span><a class="type-ref" href="#CodeActionKind">CodeActionKind</a></span>
<div class="details collapse" id="details-551">
<div class="comment"><p>Empty kind.</p>
</div>
</div>



<a name="CodeActionKind.QuickFix"></a><span class="ts" id=552 data-target="#details-552" data-toggle="collapse"><span class="ident">QuickFix</span><span>: </span><a class="type-ref" href="#CodeActionKind">CodeActionKind</a></span>
<div class="details collapse" id="details-552">
<div class="comment"><p>Base kind for quickfix actions: <code>quickfix</code>.</p>
<p>Quick fix actions address a problem in the code and are shown in the normal code action context menu.</p>
</div>
</div>



<a name="CodeActionKind.Refactor"></a><span class="ts" id=553 data-target="#details-553" data-toggle="collapse"><span class="ident">Refactor</span><span>: </span><a class="type-ref" href="#CodeActionKind">CodeActionKind</a></span>
<div class="details collapse" id="details-553">
<div class="comment"><p>Base kind for refactoring actions: <code>refactor</code></p>
<p>Refactoring actions are shown in the refactoring context menu.</p>
</div>
</div>



<a name="CodeActionKind.RefactorExtract"></a><span class="ts" id=554 data-target="#details-554" data-toggle="collapse"><span class="ident">RefactorExtract</span><span>: </span><a class="type-ref" href="#CodeActionKind">CodeActionKind</a></span>
<div class="details collapse" id="details-554">
<div class="comment"><p>Base kind for refactoring extraction actions: <code>refactor.extract</code></p>
<p>Example extract actions:</p>
<ul>
<li>Extract method</li>
<li>Extract function</li>
<li>Extract variable</li>
<li>Extract interface from class</li>
<li>...</li>
</ul>
</div>
</div>



<a name="CodeActionKind.RefactorInline"></a><span class="ts" id=555 data-target="#details-555" data-toggle="collapse"><span class="ident">RefactorInline</span><span>: </span><a class="type-ref" href="#CodeActionKind">CodeActionKind</a></span>
<div class="details collapse" id="details-555">
<div class="comment"><p>Base kind for refactoring inline actions: <code>refactor.inline</code></p>
<p>Example inline actions:</p>
<ul>
<li>Inline function</li>
<li>Inline variable</li>
<li>Inline constant</li>
<li>...</li>
</ul>
</div>
</div>



<a name="CodeActionKind.RefactorRewrite"></a><span class="ts" id=556 data-target="#details-556" data-toggle="collapse"><span class="ident">RefactorRewrite</span><span>: </span><a class="type-ref" href="#CodeActionKind">CodeActionKind</a></span>
<div class="details collapse" id="details-556">
<div class="comment"><p>Base kind for refactoring rewrite actions: <code>refactor.rewrite</code></p>
<p>Example rewrite actions:</p>
<ul>
<li>Convert JavaScript function to class</li>
<li>Add or remove parameter</li>
<li>Encapsulate field</li>
<li>Make method static</li>
<li>Move method to base class</li>
<li>...</li>
</ul>
</div>
</div>



<a name="CodeActionKind.Source"></a><span class="ts" id=557 data-target="#details-557" data-toggle="collapse"><span class="ident">Source</span><span>: </span><a class="type-ref" href="#CodeActionKind">CodeActionKind</a></span>
<div class="details collapse" id="details-557">
<div class="comment"><p>Base kind for source actions: <code>source</code></p>
<p>Source code actions apply to the entire file and can be run on save
using <code>editor.codeActionsOnSave</code>. They also are shown in <code>source</code> context menu.</p>
</div>
</div>



<a name="CodeActionKind.SourceOrganizeImports"></a><span class="ts" id=558 data-target="#details-558" data-toggle="collapse"><span class="ident">SourceOrganizeImports</span><span>: </span><a class="type-ref" href="#CodeActionKind">CodeActionKind</a></span>
<div class="details collapse" id="details-558">
<div class="comment"><p>Base kind for an organize imports source action: <code>source.organizeImports</code>.</p>
</div>
</div>

#### Constructors



<a name="CodeActionKind.new CodeActionKind"></a><span class="ts" id=560 data-target="#details-560" data-toggle="collapse"><span class="ident">new CodeActionKind</span><span>(</span><span class="ident">value</span><span>: </span><a class="type-intrinsic">string</a><span>)</span><span>: </span><a class="type-ref" href="#CodeActionKind">CodeActionKind</a></span>
<div class="details collapse" id="details-560">
<div class="comment"></div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="value"></a><span class="ts" id=561 data-target="#details-561" data-toggle="collapse"><span class="ident">value</span><span>: </span><a class="type-intrinsic">string</a></span></td><td><div class="comment"></div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#CodeActionKind">CodeActionKind</a></span></td><td><div class="comment"></div></td></tr>
</table>
</div>
</div>

#### Properties



<a name="CodeActionKind.value"></a><span class="ts" id=562 data-target="#details-562" data-toggle="collapse"><span class="ident">value</span><span>?</span><span>: </span><a class="type-intrinsic">string</a></span>
<div class="details collapse" id="details-562">
<div class="comment"><p>String value of the kind, e.g. <code>&quot;refactor.extract.function&quot;</code>.</p>
</div>
</div>

#### Methods



<a name="CodeActionKind.append"></a><span class="ts" id=564 data-target="#details-564" data-toggle="collapse"><span class="ident">append</span><span>(</span><span class="ident">parts</span><span>: </span><a class="type-intrinsic">string</a><span>)</span><span>: </span><a class="type-ref" href="#CodeActionKind">CodeActionKind</a></span>
<div class="details collapse" id="details-564">
<div class="comment"><p>Create a new kind by appending a more specific selector to the current kind.</p>
<p>Does not modify the current kind.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="parts"></a><span class="ts" id=565 data-target="#details-565" data-toggle="collapse"><span class="ident">parts</span><span>: </span><a class="type-intrinsic">string</a></span></td><td><div class="comment"></div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#CodeActionKind">CodeActionKind</a></span></td><td><div class="comment"></div></td></tr>
</table>
</div>
</div>



<a name="CodeActionKind.contains"></a><span class="ts" id=567 data-target="#details-567" data-toggle="collapse"><span class="ident">contains</span><span>(</span><span class="ident">other</span><span>: </span><a class="type-ref" href="#CodeActionKind">CodeActionKind</a><span>)</span><span>: </span><a class="type-intrinsic">boolean</a></span>
<div class="details collapse" id="details-567">
<div class="comment"><p>Does this kind contain <code>other</code>?</p>
<p>The kind <code>&quot;refactor&quot;</code> for example contains <code>&quot;refactor.extract&quot;</code> and <code>`&quot;refactor.extract.function&quot;</code>, but not <code>&quot;unicorn.refactor.extract&quot;</code> or <code>&quot;refactory.extract&quot;</code></p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="other"></a><span class="ts" id=568 data-target="#details-568" data-toggle="collapse"><span class="ident">other</span><span>: </span><a class="type-ref" href="#CodeActionKind">CodeActionKind</a></span></td><td><div class="comment"><p>Kind to check.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-intrinsic">boolean</a></span></td><td><div class="comment"></div></td></tr>
</table>
</div>
</div>

### <a name="CodeActionProvider"></a><span class="code-item" id=582>CodeActionProvider</span>



<div class="comment"><p>The code action interface defines the contract between extensions and
the <a href="https://code.visualstudio.com/docs/editor/editingevolved#_code-action">light bulb</a> feature.</p>
<p>A code action can be any command that is <a href="#commands.getCommands">known</a> to the system.</p>
</div>

#### Methods



<a name="CodeActionProvider.provideCodeActions"></a><span class="ts" id=584 data-target="#details-584" data-toggle="collapse"><span class="ident">provideCodeActions</span><span>(</span><span class="ident">document</span><span>: </span><a class="type-ref" href="#TextDocument">TextDocument</a>, <span class="ident">range</span><span>: </span><a class="type-ref" href="#Range">Range</a> &#124; <a class="type-ref" href="#Selection">Selection</a>, <span class="ident">context</span><span>: </span><a class="type-ref" href="#CodeActionContext">CodeActionContext</a>, <span class="ident">token</span><span>: </span><a class="type-ref" href="#CancellationToken">CancellationToken</a><span>)</span><span>: </span><a class="type-ref" href="#ProviderResult">ProviderResult</a>&lt;<a class="type-ref" href="#Command">Command</a> &#124; <a class="type-ref" href="#CodeAction">CodeAction</a>[]&gt;</span>
<div class="details collapse" id="details-584">
<div class="comment"><p>Provide commands for the given document and range.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="document"></a><span class="ts" id=585 data-target="#details-585" data-toggle="collapse"><span class="ident">document</span><span>: </span><a class="type-ref" href="#TextDocument">TextDocument</a></span></td><td><div class="comment"><p>The document in which the command was invoked.</p>
</div></td></tr>
<tr><td><a name="range"></a><span class="ts" id=586 data-target="#details-586" data-toggle="collapse"><span class="ident">range</span><span>: </span><a class="type-ref" href="#Range">Range</a> &#124; <a class="type-ref" href="#Selection">Selection</a></span></td><td><div class="comment"><p>The selector or range for which the command was invoked. This will always be a selection if
there is a currently active editor.</p>
</div></td></tr>
<tr><td><a name="context"></a><span class="ts" id=587 data-target="#details-587" data-toggle="collapse"><span class="ident">context</span><span>: </span><a class="type-ref" href="#CodeActionContext">CodeActionContext</a></span></td><td><div class="comment"><p>Context carrying additional information.</p>
</div></td></tr>
<tr><td><a name="token"></a><span class="ts" id=588 data-target="#details-588" data-toggle="collapse"><span class="ident">token</span><span>: </span><a class="type-ref" href="#CancellationToken">CancellationToken</a></span></td><td><div class="comment"><p>A cancellation token.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#ProviderResult">ProviderResult</a>&lt;<a class="type-ref" href="#Command">Command</a> &#124; <a class="type-ref" href="#CodeAction">CodeAction</a>[]&gt;</span></td><td><div class="comment"><p>An array of commands, quick fixes, or refactorings or a thenable of such. The lack of a result can be
signaled by returning <code>undefined</code>, <code>null</code>, or an empty array.</p>
</div></td></tr>
</table>
</div>
</div>

### <a name="CodeActionProviderMetadata"></a><span class="code-item" id=589>CodeActionProviderMetadata</span>



<div class="comment"><p>Metadata about the type of code actions that a <a href="#CodeActionProvider">CodeActionProvider</a> providers</p>
</div>

#### Properties



<a name="CodeActionProviderMetadata.providedCodeActionKinds"></a><span class="ts" id=590 data-target="#details-590" data-toggle="collapse"><span class="ident">providedCodeActionKinds</span><span>?</span><span>: </span><a class="type-ref" href="#ReadonlyArray">ReadonlyArray</a>&lt;<a class="type-ref" href="#CodeActionKind">CodeActionKind</a>&gt;</span>
<div class="details collapse" id="details-590">
<div class="comment"><p><a href="#CodeActionKind">CodeActionKinds</a> that this provider may return.</p>
<p>The list of kinds may be generic, such as <code>CodeActionKind.Refactor</code>, or the provider
may list our every specific kind they provide, such as <code>CodeActionKind.Refactor.Extract.append(&#39;function</code>)`</p>
</div>
</div>

### <a name="CodeLens"></a><span class="code-item" id=591>CodeLens</span>



<div class="comment"><p>A code lens represents a <a href="#Command">command</a> that should be shown along with
source text, like the number of references, a way to run tests, etc.</p>
<p>A code lens is <em>unresolved</em> when no command is associated to it. For performance
reasons the creation of a code lens and resolving should be done to two stages.</p>
<ul>
<li><em>see</em> - <a href="#CodeLensProvider.provideCodeLenses">CodeLensProvider.provideCodeLenses</a></li>
</ul>
<ul>
<li><em>see</em> - <a href="#CodeLensProvider.resolveCodeLens">CodeLensProvider.resolveCodeLens</a></li>
</ul>
</div>

#### Constructors



<a name="CodeLens.new CodeLens"></a><span class="ts" id=596 data-target="#details-596" data-toggle="collapse"><span class="ident">new CodeLens</span><span>(</span><span class="ident">range</span><span>: </span><a class="type-ref" href="#Range">Range</a>, <span class="ident">command</span><span>?</span><span>: </span><a class="type-ref" href="#Command">Command</a><span>)</span><span>: </span><a class="type-ref" href="#CodeLens">CodeLens</a></span>
<div class="details collapse" id="details-596">
<div class="comment"><p>Creates a new code lens object.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="range"></a><span class="ts" id=597 data-target="#details-597" data-toggle="collapse"><span class="ident">range</span><span>: </span><a class="type-ref" href="#Range">Range</a></span></td><td><div class="comment"><p>The range to which this code lens applies.</p>
</div></td></tr>
<tr><td><a name="command"></a><span class="ts" id=598 data-target="#details-598" data-toggle="collapse"><span class="ident">command</span><span>?</span><span>: </span><a class="type-ref" href="#Command">Command</a></span></td><td><div class="comment"><p>The command associated to this code lens.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#CodeLens">CodeLens</a></span></td><td><div class="comment"></div></td></tr>
</table>
</div>
</div>

#### Properties



<a name="CodeLens.command"></a><span class="ts" id=593 data-target="#details-593" data-toggle="collapse"><span class="ident">command</span><span>?</span><span>: </span><a class="type-ref" href="#Command">Command</a></span>
<div class="details collapse" id="details-593">
<div class="comment"><p>The command this code lens represents.</p>
</div>
</div>



<a name="CodeLens.isResolved"></a><span class="ts" id=594 data-target="#details-594" data-toggle="collapse"><span class="ident">isResolved</span><span>: </span><a class="type-intrinsic">boolean</a></span>
<div class="details collapse" id="details-594">
<div class="comment"><p><code>true</code> when there is a command associated.</p>
</div>
</div>



<a name="CodeLens.range"></a><span class="ts" id=592 data-target="#details-592" data-toggle="collapse"><span class="ident">range</span><span>: </span><a class="type-ref" href="#Range">Range</a></span>
<div class="details collapse" id="details-592">
<div class="comment"><p>The range in which this code lens is valid. Should only span a single line.</p>
</div>
</div>

### <a name="CodeLensProvider"></a><span class="code-item" id=599>CodeLensProvider</span>



<div class="comment"><p>A code lens provider adds <a href="#Command">commands</a> to source text. The commands will be shown
as dedicated horizontal lines in between the source text.</p>
</div>

#### Events



<a name="CodeLensProvider.onDidChangeCodeLenses"></a><span class="ts" id=600 data-target="#details-600" data-toggle="collapse"><span class="ident">onDidChangeCodeLenses</span><span>?</span><span>: </span><a class="type-ref" href="#Event">Event</a>&lt;<a class="type-intrinsic">void</a>&gt;</span>
<div class="details collapse" id="details-600">
<div class="comment"><p>An optional event to signal that the code lenses from this provider have changed.</p>
</div>
</div>

#### Methods



<a name="CodeLensProvider.provideCodeLenses"></a><span class="ts" id=602 data-target="#details-602" data-toggle="collapse"><span class="ident">provideCodeLenses</span><span>(</span><span class="ident">document</span><span>: </span><a class="type-ref" href="#TextDocument">TextDocument</a>, <span class="ident">token</span><span>: </span><a class="type-ref" href="#CancellationToken">CancellationToken</a><span>)</span><span>: </span><a class="type-ref" href="#ProviderResult">ProviderResult</a>&lt;<a class="type-ref" href="#CodeLens">CodeLens</a>[]&gt;</span>
<div class="details collapse" id="details-602">
<div class="comment"><p>Compute a list of <a href="#CodeLens">lenses</a>. This call should return as fast as possible and if
computing the commands is expensive implementors should only return code lens objects with the
range set and implement <a href="#CodeLensProvider.resolveCodeLens">resolve</a>.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="document"></a><span class="ts" id=603 data-target="#details-603" data-toggle="collapse"><span class="ident">document</span><span>: </span><a class="type-ref" href="#TextDocument">TextDocument</a></span></td><td><div class="comment"><p>The document in which the command was invoked.</p>
</div></td></tr>
<tr><td><a name="token"></a><span class="ts" id=604 data-target="#details-604" data-toggle="collapse"><span class="ident">token</span><span>: </span><a class="type-ref" href="#CancellationToken">CancellationToken</a></span></td><td><div class="comment"><p>A cancellation token.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#ProviderResult">ProviderResult</a>&lt;<a class="type-ref" href="#CodeLens">CodeLens</a>[]&gt;</span></td><td><div class="comment"><p>An array of code lenses or a thenable that resolves to such. The lack of a result can be
signaled by returning <code>undefined</code>, <code>null</code>, or an empty array.</p>
</div></td></tr>
</table>
</div>
</div>



<a name="CodeLensProvider.resolveCodeLens"></a><span class="ts" id=606 data-target="#details-606" data-toggle="collapse"><span class="ident">resolveCodeLens</span><span>(</span><span class="ident">codeLens</span><span>: </span><a class="type-ref" href="#CodeLens">CodeLens</a>, <span class="ident">token</span><span>: </span><a class="type-ref" href="#CancellationToken">CancellationToken</a><span>)</span><span>: </span><a class="type-ref" href="#ProviderResult">ProviderResult</a>&lt;<a class="type-ref" href="#CodeLens">CodeLens</a>&gt;</span>
<div class="details collapse" id="details-606">
<div class="comment"><p>This function will be called for each visible code lens, usually when scrolling and after
calls to <a href="#CodeLensProvider.provideCodeLenses">compute</a>-lenses.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="codeLens"></a><span class="ts" id=607 data-target="#details-607" data-toggle="collapse"><span class="ident">codeLens</span><span>: </span><a class="type-ref" href="#CodeLens">CodeLens</a></span></td><td><div class="comment"><p>code lens that must be resolved.</p>
</div></td></tr>
<tr><td><a name="token"></a><span class="ts" id=608 data-target="#details-608" data-toggle="collapse"><span class="ident">token</span><span>: </span><a class="type-ref" href="#CancellationToken">CancellationToken</a></span></td><td><div class="comment"><p>A cancellation token.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#ProviderResult">ProviderResult</a>&lt;<a class="type-ref" href="#CodeLens">CodeLens</a>&gt;</span></td><td><div class="comment"><p>The given, resolved code lens or thenable that resolves to such.</p>
</div></td></tr>
</table>
</div>
</div>

### <a name="Color"></a><span class="code-item" id=1023>Color</span>



<div class="comment"><p>Represents a color in RGBA space.</p>
</div>

#### Constructors



<a name="Color.new Color"></a><span class="ts" id=1029 data-target="#details-1029" data-toggle="collapse"><span class="ident">new Color</span><span>(</span><span class="ident">red</span><span>: </span><a class="type-intrinsic">number</a>, <span class="ident">green</span><span>: </span><a class="type-intrinsic">number</a>, <span class="ident">blue</span><span>: </span><a class="type-intrinsic">number</a>, <span class="ident">alpha</span><span>: </span><a class="type-intrinsic">number</a><span>)</span><span>: </span><a class="type-ref" href="#Color">Color</a></span>
<div class="details collapse" id="details-1029">
<div class="comment"><p>Creates a new color instance.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="red"></a><span class="ts" id=1030 data-target="#details-1030" data-toggle="collapse"><span class="ident">red</span><span>: </span><a class="type-intrinsic">number</a></span></td><td><div class="comment"><p>The red component.</p>
</div></td></tr>
<tr><td><a name="green"></a><span class="ts" id=1031 data-target="#details-1031" data-toggle="collapse"><span class="ident">green</span><span>: </span><a class="type-intrinsic">number</a></span></td><td><div class="comment"><p>The green component.</p>
</div></td></tr>
<tr><td><a name="blue"></a><span class="ts" id=1032 data-target="#details-1032" data-toggle="collapse"><span class="ident">blue</span><span>: </span><a class="type-intrinsic">number</a></span></td><td><div class="comment"><p>The blue component.</p>
</div></td></tr>
<tr><td><a name="alpha"></a><span class="ts" id=1033 data-target="#details-1033" data-toggle="collapse"><span class="ident">alpha</span><span>: </span><a class="type-intrinsic">number</a></span></td><td><div class="comment"><p>The alpha component.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#Color">Color</a></span></td><td><div class="comment"></div></td></tr>
</table>
</div>
</div>

#### Properties



<a name="Color.alpha"></a><span class="ts" id=1027 data-target="#details-1027" data-toggle="collapse"><span class="ident">alpha</span><span>: </span><a class="type-intrinsic">number</a></span>
<div class="details collapse" id="details-1027">
<div class="comment"><p>The alpha component of this color in the range [0-1].</p>
</div>
</div>



<a name="Color.blue"></a><span class="ts" id=1026 data-target="#details-1026" data-toggle="collapse"><span class="ident">blue</span><span>: </span><a class="type-intrinsic">number</a></span>
<div class="details collapse" id="details-1026">
<div class="comment"><p>The blue component of this color in the range [0-1].</p>
</div>
</div>



<a name="Color.green"></a><span class="ts" id=1025 data-target="#details-1025" data-toggle="collapse"><span class="ident">green</span><span>: </span><a class="type-intrinsic">number</a></span>
<div class="details collapse" id="details-1025">
<div class="comment"><p>The green component of this color in the range [0-1].</p>
</div>
</div>



<a name="Color.red"></a><span class="ts" id=1024 data-target="#details-1024" data-toggle="collapse"><span class="ident">red</span><span>: </span><a class="type-intrinsic">number</a></span>
<div class="details collapse" id="details-1024">
<div class="comment"><p>The red component of this color in the range [0-1].</p>
</div>
</div>

### <a name="ColorInformation"></a><span class="code-item" id=1034>ColorInformation</span>



<div class="comment"><p>Represents a color range from a document.</p>
</div>

#### Constructors



<a name="ColorInformation.new ColorInformation"></a><span class="ts" id=1038 data-target="#details-1038" data-toggle="collapse"><span class="ident">new ColorInformation</span><span>(</span><span class="ident">range</span><span>: </span><a class="type-ref" href="#Range">Range</a>, <span class="ident">color</span><span>: </span><a class="type-ref" href="#Color">Color</a><span>)</span><span>: </span><a class="type-ref" href="#ColorInformation">ColorInformation</a></span>
<div class="details collapse" id="details-1038">
<div class="comment"><p>Creates a new color range.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="range"></a><span class="ts" id=1039 data-target="#details-1039" data-toggle="collapse"><span class="ident">range</span><span>: </span><a class="type-ref" href="#Range">Range</a></span></td><td><div class="comment"><p>The range the color appears in. Must not be empty.</p>
</div></td></tr>
<tr><td><a name="color"></a><span class="ts" id=1040 data-target="#details-1040" data-toggle="collapse"><span class="ident">color</span><span>: </span><a class="type-ref" href="#Color">Color</a></span></td><td><div class="comment"><p>The value of the color.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#ColorInformation">ColorInformation</a></span></td><td><div class="comment"></div></td></tr>
</table>
</div>
</div>

#### Properties



<a name="ColorInformation.color"></a><span class="ts" id=1036 data-target="#details-1036" data-toggle="collapse"><span class="ident">color</span><span>: </span><a class="type-ref" href="#Color">Color</a></span>
<div class="details collapse" id="details-1036">
<div class="comment"><p>The actual color value for this color range.</p>
</div>
</div>



<a name="ColorInformation.range"></a><span class="ts" id=1035 data-target="#details-1035" data-toggle="collapse"><span class="ident">range</span><span>: </span><a class="type-ref" href="#Range">Range</a></span>
<div class="details collapse" id="details-1035">
<div class="comment"><p>The range in the document where this color appears.</p>
</div>
</div>

### <a name="ColorPresentation"></a><span class="code-item" id=1041>ColorPresentation</span>



<div class="comment"><p>A color presentation object describes how a <a href="#Color"><code>color</code></a> should be represented as text and what
edits are required to refer to it from source code.</p>
<p>For some languages one color can have multiple presentations, e.g. css can represent the color red with
the constant <code>Red</code>, the hex-value <code>#ff0000</code>, or in rgba and hsla forms. In csharp other representations
apply, e.g <code>System.Drawing.Color.Red</code>.</p>
</div>

#### Constructors



<a name="ColorPresentation.new ColorPresentation"></a><span class="ts" id=1046 data-target="#details-1046" data-toggle="collapse"><span class="ident">new ColorPresentation</span><span>(</span><span class="ident">label</span><span>: </span><a class="type-intrinsic">string</a><span>)</span><span>: </span><a class="type-ref" href="#ColorPresentation">ColorPresentation</a></span>
<div class="details collapse" id="details-1046">
<div class="comment"><p>Creates a new color presentation.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="label"></a><span class="ts" id=1047 data-target="#details-1047" data-toggle="collapse"><span class="ident">label</span><span>: </span><a class="type-intrinsic">string</a></span></td><td><div class="comment"><p>The label of this color presentation.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#ColorPresentation">ColorPresentation</a></span></td><td><div class="comment"></div></td></tr>
</table>
</div>
</div>

#### Properties



<a name="ColorPresentation.additionalTextEdits"></a><span class="ts" id=1044 data-target="#details-1044" data-toggle="collapse"><span class="ident">additionalTextEdits</span><span>?</span><span>: </span><a class="type-ref" href="#TextEdit">TextEdit</a>[]</span>
<div class="details collapse" id="details-1044">
<div class="comment"><p>An optional array of additional <a href="#TextEdit">text edits</a> that are applied when
selecting this color presentation. Edits must not overlap with the main <a href="#ColorPresentation.textEdit">edit</a> nor with themselves.</p>
</div>
</div>



<a name="ColorPresentation.label"></a><span class="ts" id=1042 data-target="#details-1042" data-toggle="collapse"><span class="ident">label</span><span>: </span><a class="type-intrinsic">string</a></span>
<div class="details collapse" id="details-1042">
<div class="comment"><p>The label of this color presentation. It will be shown on the color
picker header. By default this is also the text that is inserted when selecting
this color presentation.</p>
</div>
</div>



<a name="ColorPresentation.textEdit"></a><span class="ts" id=1043 data-target="#details-1043" data-toggle="collapse"><span class="ident">textEdit</span><span>?</span><span>: </span><a class="type-ref" href="#TextEdit">TextEdit</a></span>
<div class="details collapse" id="details-1043">
<div class="comment"><p>An <a href="#TextEdit">edit</a> which is applied to a document when selecting
this presentation for the color.  When <code>falsy</code> the <a href="#ColorPresentation.label">label</a>
is used.</p>
</div>
</div>

### <a name="Command"></a><span class="code-item" id=26>Command</span>



<div class="comment"><p>Represents a reference to a command. Provides a title which
will be used to represent a command in the UI and, optionally,
an array of arguments which will be passed to the command handler
function when invoked.</p>
</div>

#### Properties



<a name="Command.arguments"></a><span class="ts" id=30 data-target="#details-30" data-toggle="collapse"><span class="ident">arguments</span><span>?</span><span>: </span><a class="type-intrinsic">any</a>[]</span>
<div class="details collapse" id="details-30">
<div class="comment"><p>Arguments that the command handler should be
invoked with.</p>
</div>
</div>



<a name="Command.command"></a><span class="ts" id=28 data-target="#details-28" data-toggle="collapse"><span class="ident">command</span><span>: </span><a class="type-intrinsic">string</a></span>
<div class="details collapse" id="details-28">
<div class="comment"><p>The identifier of the actual command handler.</p>
<ul>
<li><em>see</em> - <a href="#commands.registerCommand">commands.registerCommand</a>.</li>
</ul>
</div>
</div>



<a name="Command.title"></a><span class="ts" id=27 data-target="#details-27" data-toggle="collapse"><span class="ident">title</span><span>: </span><a class="type-intrinsic">string</a></span>
<div class="details collapse" id="details-27">
<div class="comment"><p>Title of the command, like <code>save</code>.</p>
</div>
</div>



<a name="Command.tooltip"></a><span class="ts" id=29 data-target="#details-29" data-toggle="collapse"><span class="ident">tooltip</span><span>?</span><span>: </span><a class="type-intrinsic">string</a></span>
<div class="details collapse" id="details-29">
<div class="comment"><p>A tooltip for the command, when represented in the UI.</p>
</div>
</div>

### <a name="CommentRule"></a><span class="code-item" id=1081>CommentRule</span>



<div class="comment"><p>Describes how comments for a language work.</p>
</div>

#### Properties



<a name="CommentRule.blockComment"></a><span class="ts" id=1083 data-target="#details-1083" data-toggle="collapse"><span class="ident">blockComment</span><span>?</span><span>: </span><a class="type-ref" href="#CharacterPair">CharacterPair</a></span>
<div class="details collapse" id="details-1083">
<div class="comment"><p>The block comment character pair, like <code>/* block comment *&amp;#47;</code></p>
</div>
</div>



<a name="CommentRule.lineComment"></a><span class="ts" id=1082 data-target="#details-1082" data-toggle="collapse"><span class="ident">lineComment</span><span>?</span><span>: </span><a class="type-intrinsic">string</a></span>
<div class="details collapse" id="details-1082">
<div class="comment"><p>The line comment token, like <code>// this is a comment</code></p>
</div>
</div>

### <a name="CompletionContext"></a><span class="code-item" id=993>CompletionContext</span>



<div class="comment"><p>Contains additional information about the context in which
<a href="#CompletionItemProvider.provideCompletionItems">completion provider</a> is triggered.</p>
</div>

#### Properties



<a name="CompletionContext.triggerCharacter"></a><span class="ts" id=995 data-target="#details-995" data-toggle="collapse"><span class="ident">triggerCharacter</span><span>?</span><span>: </span><a class="type-intrinsic">string</a></span>
<div class="details collapse" id="details-995">
<div class="comment"><p>Character that triggered the completion item provider.</p>
<p><code>undefined</code> if provider was not triggered by a character.</p>
<p>The trigger character is already in the document when the completion provider is triggered.</p>
</div>
</div>



<a name="CompletionContext.triggerKind"></a><span class="ts" id=994 data-target="#details-994" data-toggle="collapse"><span class="ident">triggerKind</span><span>: </span><a class="type-ref" href="#CompletionTriggerKind">CompletionTriggerKind</a></span>
<div class="details collapse" id="details-994">
<div class="comment"><p>How the completion was triggered.</p>
</div>
</div>

### <a name="CompletionItem"></a><span class="code-item" id=963>CompletionItem</span>



<div class="comment"><p>A completion item represents a text snippet that is proposed to complete text that is being typed.</p>
<p>It is sufficient to create a completion item from just a <a href="#CompletionItem.label">label</a>. In that
case the completion item will replace the <a href="#TextDocument.getWordRangeAtPosition">word</a>
until the cursor with the given label or <a href="#CompletionItem.insertText">insertText</a>. Otherwise the
given <a href="#CompletionItem.textEdit">edit</a> is used.</p>
<p>When selecting a completion item in the editor its defined or synthesized text edit will be applied
to <em>all</em> cursors/selections whereas <a href="#CompletionItem.additionalTextEdits">additionalTextEdits</a> will be
applied as provided.</p>
<ul>
<li><em>see</em> - <a href="#CompletionItemProvider.provideCompletionItems">CompletionItemProvider.provideCompletionItems</a></li>
</ul>
<ul>
<li><em>see</em> - <a href="#CompletionItemProvider.resolveCompletionItem">CompletionItemProvider.resolveCompletionItem</a></li>
</ul>
</div>

#### Constructors



<a name="CompletionItem.new CompletionItem"></a><span class="ts" id=979 data-target="#details-979" data-toggle="collapse"><span class="ident">new CompletionItem</span><span>(</span><span class="ident">label</span><span>: </span><a class="type-intrinsic">string</a>, <span class="ident">kind</span><span>?</span><span>: </span><a class="type-ref" href="#CompletionItemKind">CompletionItemKind</a><span>)</span><span>: </span><a class="type-ref" href="#CompletionItem">CompletionItem</a></span>
<div class="details collapse" id="details-979">
<div class="comment"><p>Creates a new completion item.</p>
<p>Completion items must have at least a <a href="#CompletionItem.label">label</a> which then
will be used as insert text as well as for sorting and filtering.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="label"></a><span class="ts" id=980 data-target="#details-980" data-toggle="collapse"><span class="ident">label</span><span>: </span><a class="type-intrinsic">string</a></span></td><td><div class="comment"><p>The label of the completion.</p>
</div></td></tr>
<tr><td><a name="kind"></a><span class="ts" id=981 data-target="#details-981" data-toggle="collapse"><span class="ident">kind</span><span>?</span><span>: </span><a class="type-ref" href="#CompletionItemKind">CompletionItemKind</a></span></td><td><div class="comment"><p>The <a href="#CompletionItemKind">kind</a> of the completion.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#CompletionItem">CompletionItem</a></span></td><td><div class="comment"></div></td></tr>
</table>
</div>
</div>

#### Properties



<a name="CompletionItem.additionalTextEdits"></a><span class="ts" id=976 data-target="#details-976" data-toggle="collapse"><span class="ident">additionalTextEdits</span><span>?</span><span>: </span><a class="type-ref" href="#TextEdit">TextEdit</a>[]</span>
<div class="details collapse" id="details-976">
<div class="comment"><p>An optional array of additional <a href="#TextEdit">text edits</a> that are applied when
selecting this completion. Edits must not overlap with the main <a href="#CompletionItem.textEdit">edit</a>
nor with themselves.</p>
</div>
</div>



<a name="CompletionItem.command"></a><span class="ts" id=977 data-target="#details-977" data-toggle="collapse"><span class="ident">command</span><span>?</span><span>: </span><a class="type-ref" href="#Command">Command</a></span>
<div class="details collapse" id="details-977">
<div class="comment"><p>An optional <a href="#Command">command</a> that is executed <em>after</em> inserting this completion. <em>Note</em> that
additional modifications to the current document should be described with the
<a href="#CompletionItem.additionalTextEdits">additionalTextEdits</a>-property.</p>
</div>
</div>



<a name="CompletionItem.commitCharacters"></a><span class="ts" id=973 data-target="#details-973" data-toggle="collapse"><span class="ident">commitCharacters</span><span>?</span><span>: </span><a class="type-intrinsic">string</a>[]</span>
<div class="details collapse" id="details-973">
<div class="comment"><p>An optional set of characters that when pressed while this completion is active will accept it first and
then type that character. <em>Note</em> that all commit characters should have <code>length=1</code> and that superfluous
characters will be ignored.</p>
</div>
</div>



<a name="CompletionItem.detail"></a><span class="ts" id=966 data-target="#details-966" data-toggle="collapse"><span class="ident">detail</span><span>?</span><span>: </span><a class="type-intrinsic">string</a></span>
<div class="details collapse" id="details-966">
<div class="comment"><p>A human-readable string with additional information
about this item, like type or symbol information.</p>
</div>
</div>



<a name="CompletionItem.documentation"></a><span class="ts" id=967 data-target="#details-967" data-toggle="collapse"><span class="ident">documentation</span><span>?</span><span>: </span><a class="type-intrinsic">string</a> &#124; <a class="type-ref" href="#MarkdownString">MarkdownString</a></span>
<div class="details collapse" id="details-967">
<div class="comment"><p>A human-readable string that represents a doc-comment.</p>
</div>
</div>



<a name="CompletionItem.filterText"></a><span class="ts" id=969 data-target="#details-969" data-toggle="collapse"><span class="ident">filterText</span><span>?</span><span>: </span><a class="type-intrinsic">string</a></span>
<div class="details collapse" id="details-969">
<div class="comment"><p>A string that should be used when filtering a set of
completion items. When <code>falsy</code> the <a href="#CompletionItem.label">label</a>
is used.</p>
</div>
</div>



<a name="CompletionItem.insertText"></a><span class="ts" id=971 data-target="#details-971" data-toggle="collapse"><span class="ident">insertText</span><span>?</span><span>: </span><a class="type-intrinsic">string</a> &#124; <a class="type-ref" href="#SnippetString">SnippetString</a></span>
<div class="details collapse" id="details-971">
<div class="comment"><p>A string or snippet that should be inserted in a document when selecting
this completion. When <code>falsy</code> the <a href="#CompletionItem.label">label</a>
is used.</p>
</div>
</div>



<a name="CompletionItem.keepWhitespace"></a><span class="ts" id=974 data-target="#details-974" data-toggle="collapse"><span class="ident">keepWhitespace</span><span>?</span><span>: </span><a class="type-intrinsic">boolean</a></span>
<div class="details collapse" id="details-974">
<div class="comment"><p>Keep whitespace of the <a href="#CompletionItem.insertText">insertText</a> as is. By default, the editor adjusts leading
whitespace of new lines so that they match the indentation of the line for which the item is accepeted - setting
this to <code>true</code> will prevent that.</p>
</div>
</div>



<a name="CompletionItem.kind"></a><span class="ts" id=965 data-target="#details-965" data-toggle="collapse"><span class="ident">kind</span><span>?</span><span>: </span><a class="type-ref" href="#CompletionItemKind">CompletionItemKind</a></span>
<div class="details collapse" id="details-965">
<div class="comment"><p>The kind of this completion item. Based on the kind
an icon is chosen by the editor.</p>
</div>
</div>



<a name="CompletionItem.label"></a><span class="ts" id=964 data-target="#details-964" data-toggle="collapse"><span class="ident">label</span><span>: </span><a class="type-intrinsic">string</a></span>
<div class="details collapse" id="details-964">
<div class="comment"><p>The label of this completion item. By default
this is also the text that is inserted when selecting
this completion.</p>
</div>
</div>



<a name="CompletionItem.preselect"></a><span class="ts" id=970 data-target="#details-970" data-toggle="collapse"><span class="ident">preselect</span><span>?</span><span>: </span><a class="type-intrinsic">boolean</a></span>
<div class="details collapse" id="details-970">
<div class="comment"><p>Select this item when showing. <em>Note</em> that only one completion item can be selected and
that the editor decides which item that is. The rule is that the <em>first</em> item of those
that match best is selected.</p>
</div>
</div>



<a name="CompletionItem.range"></a><span class="ts" id=972 data-target="#details-972" data-toggle="collapse"><span class="ident">range</span><span>?</span><span>: </span><a class="type-ref" href="#Range">Range</a></span>
<div class="details collapse" id="details-972">
<div class="comment"><p>A range of text that should be replaced by this completion item.</p>
<p>Defaults to a range from the start of the <a href="#TextDocument.getWordRangeAtPosition">current word</a> to the
current position.</p>
<p><em>Note:</em> The range must be a <a href="#Range.isSingleLine">single line</a> and it must
<a href="#Range.contains">contain</a> the position at which completion has been <a href="#CompletionItemProvider.provideCompletionItems">requested</a>.</p>
</div>
</div>



<a name="CompletionItem.sortText"></a><span class="ts" id=968 data-target="#details-968" data-toggle="collapse"><span class="ident">sortText</span><span>?</span><span>: </span><a class="type-intrinsic">string</a></span>
<div class="details collapse" id="details-968">
<div class="comment"><p>A string that should be used when comparing this item
with other items. When <code>falsy</code> the <a href="#CompletionItem.label">label</a>
is used.</p>
</div>
</div>



<a name="CompletionItem.textEdit"></a><span class="ts" id=975 data-target="#details-975" data-toggle="collapse"><span class="ident">textEdit</span><span>?</span><span>: </span><a class="type-ref" href="#TextEdit">TextEdit</a></span>
<div class="details collapse" id="details-975">
<div class="comment"><ul>
<li><em>deprecated</em> - Use <code>CompletionItem.insertText</code> and <code>CompletionItem.range</code> instead.</li>
</ul>
<p><del>An <a href="#TextEdit">edit</a> which is applied to a document when selecting
this completion. When an edit is provided the value of
<a href="#CompletionItem.insertText">insertText</a> is ignored.</del></p>
<p><del>The <a href="#Range">range</a> of the edit must be single-line and on the same
line completions were <a href="#CompletionItemProvider.provideCompletionItems">requested</a> at.</del></p>
</div>
</div>

### <a name="CompletionItemKind"></a><span class="code-item" id=937>CompletionItemKind</span>



<div class="comment"><p>Completion item kinds.</p>
</div>

#### Enumeration members



<a name="CompletionItemKind.Class"></a><span class="ts" id=944 data-target="#details-944" data-toggle="collapse"><span class="ident">Class</span></span>
<div class="details collapse" id="details-944">
<em>6</em>
</div>



<a name="CompletionItemKind.Color"></a><span class="ts" id=953 data-target="#details-953" data-toggle="collapse"><span class="ident">Color</span></span>
<div class="details collapse" id="details-953">
<em>15</em>
</div>



<a name="CompletionItemKind.Constant"></a><span class="ts" id=958 data-target="#details-958" data-toggle="collapse"><span class="ident">Constant</span></span>
<div class="details collapse" id="details-958">
<em>20</em>
</div>



<a name="CompletionItemKind.Constructor"></a><span class="ts" id=941 data-target="#details-941" data-toggle="collapse"><span class="ident">Constructor</span></span>
<div class="details collapse" id="details-941">
<em>3</em>
</div>



<a name="CompletionItemKind.Enum"></a><span class="ts" id=950 data-target="#details-950" data-toggle="collapse"><span class="ident">Enum</span></span>
<div class="details collapse" id="details-950">
<em>12</em>
</div>



<a name="CompletionItemKind.EnumMember"></a><span class="ts" id=957 data-target="#details-957" data-toggle="collapse"><span class="ident">EnumMember</span></span>
<div class="details collapse" id="details-957">
<em>19</em>
</div>



<a name="CompletionItemKind.Event"></a><span class="ts" id=960 data-target="#details-960" data-toggle="collapse"><span class="ident">Event</span></span>
<div class="details collapse" id="details-960">
<em>22</em>
</div>



<a name="CompletionItemKind.Field"></a><span class="ts" id=942 data-target="#details-942" data-toggle="collapse"><span class="ident">Field</span></span>
<div class="details collapse" id="details-942">
<em>4</em>
</div>



<a name="CompletionItemKind.File"></a><span class="ts" id=955 data-target="#details-955" data-toggle="collapse"><span class="ident">File</span></span>
<div class="details collapse" id="details-955">
<em>16</em>
</div>



<a name="CompletionItemKind.Folder"></a><span class="ts" id=956 data-target="#details-956" data-toggle="collapse"><span class="ident">Folder</span></span>
<div class="details collapse" id="details-956">
<em>18</em>
</div>



<a name="CompletionItemKind.Function"></a><span class="ts" id=940 data-target="#details-940" data-toggle="collapse"><span class="ident">Function</span></span>
<div class="details collapse" id="details-940">
<em>2</em>
</div>



<a name="CompletionItemKind.Interface"></a><span class="ts" id=945 data-target="#details-945" data-toggle="collapse"><span class="ident">Interface</span></span>
<div class="details collapse" id="details-945">
<em>7</em>
</div>



<a name="CompletionItemKind.Keyword"></a><span class="ts" id=951 data-target="#details-951" data-toggle="collapse"><span class="ident">Keyword</span></span>
<div class="details collapse" id="details-951">
<em>13</em>
</div>



<a name="CompletionItemKind.Method"></a><span class="ts" id=939 data-target="#details-939" data-toggle="collapse"><span class="ident">Method</span></span>
<div class="details collapse" id="details-939">
<em>1</em>
</div>



<a name="CompletionItemKind.Module"></a><span class="ts" id=946 data-target="#details-946" data-toggle="collapse"><span class="ident">Module</span></span>
<div class="details collapse" id="details-946">
<em>8</em>
</div>



<a name="CompletionItemKind.Operator"></a><span class="ts" id=961 data-target="#details-961" data-toggle="collapse"><span class="ident">Operator</span></span>
<div class="details collapse" id="details-961">
<em>23</em>
</div>



<a name="CompletionItemKind.Property"></a><span class="ts" id=947 data-target="#details-947" data-toggle="collapse"><span class="ident">Property</span></span>
<div class="details collapse" id="details-947">
<em>9</em>
</div>



<a name="CompletionItemKind.Reference"></a><span class="ts" id=954 data-target="#details-954" data-toggle="collapse"><span class="ident">Reference</span></span>
<div class="details collapse" id="details-954">
<em>17</em>
</div>



<a name="CompletionItemKind.Snippet"></a><span class="ts" id=952 data-target="#details-952" data-toggle="collapse"><span class="ident">Snippet</span></span>
<div class="details collapse" id="details-952">
<em>14</em>
</div>



<a name="CompletionItemKind.Struct"></a><span class="ts" id=959 data-target="#details-959" data-toggle="collapse"><span class="ident">Struct</span></span>
<div class="details collapse" id="details-959">
<em>21</em>
</div>



<a name="CompletionItemKind.Text"></a><span class="ts" id=938 data-target="#details-938" data-toggle="collapse"><span class="ident">Text</span></span>
<div class="details collapse" id="details-938">
<em>0</em>
</div>



<a name="CompletionItemKind.TypeParameter"></a><span class="ts" id=962 data-target="#details-962" data-toggle="collapse"><span class="ident">TypeParameter</span></span>
<div class="details collapse" id="details-962">
<em>24</em>
</div>



<a name="CompletionItemKind.Unit"></a><span class="ts" id=948 data-target="#details-948" data-toggle="collapse"><span class="ident">Unit</span></span>
<div class="details collapse" id="details-948">
<em>10</em>
</div>



<a name="CompletionItemKind.Value"></a><span class="ts" id=949 data-target="#details-949" data-toggle="collapse"><span class="ident">Value</span></span>
<div class="details collapse" id="details-949">
<em>11</em>
</div>



<a name="CompletionItemKind.Variable"></a><span class="ts" id=943 data-target="#details-943" data-toggle="collapse"><span class="ident">Variable</span></span>
<div class="details collapse" id="details-943">
<em>5</em>
</div>

### <a name="CompletionItemProvider"></a><span class="code-item" id=996>CompletionItemProvider</span>



<div class="comment"><p>The completion item provider interface defines the contract between extensions and
<a href="https://code.visualstudio.com/docs/editor/intellisense">IntelliSense</a>.</p>
<p>Providers can delay the computation of the <a href="#CompletionItem.detail"><code>detail</code></a>
and <a href="#CompletionItem.documentation"><code>documentation</code></a> properties by implementing the
<a href="#CompletionItemProvider.resolveCompletionItem"><code>resolveCompletionItem</code></a>-function. However, properties that
are needed for the initial sorting and filtering, like <code>sortText</code>, <code>filterText</code>, <code>insertText</code>, and <code>range</code>, must
not be changed during resolve.</p>
<p>Providers are asked for completions either explicitly by a user gesture or -depending on the configuration-
implicitly when typing words or trigger characters.</p>
</div>

#### Methods



<a name="CompletionItemProvider.provideCompletionItems"></a><span class="ts" id=998 data-target="#details-998" data-toggle="collapse"><span class="ident">provideCompletionItems</span><span>(</span><span class="ident">document</span><span>: </span><a class="type-ref" href="#TextDocument">TextDocument</a>, <span class="ident">position</span><span>: </span><a class="type-ref" href="#Position">Position</a>, <span class="ident">token</span><span>: </span><a class="type-ref" href="#CancellationToken">CancellationToken</a>, <span class="ident">context</span><span>: </span><a class="type-ref" href="#CompletionContext">CompletionContext</a><span>)</span><span>: </span><a class="type-ref" href="#ProviderResult">ProviderResult</a>&lt;<a class="type-ref" href="#CompletionItem">CompletionItem</a>[] &#124; <a class="type-ref" href="#CompletionList">CompletionList</a>&gt;</span>
<div class="details collapse" id="details-998">
<div class="comment"><p>Provide completion items for the given position and document.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="document"></a><span class="ts" id=999 data-target="#details-999" data-toggle="collapse"><span class="ident">document</span><span>: </span><a class="type-ref" href="#TextDocument">TextDocument</a></span></td><td><div class="comment"><p>The document in which the command was invoked.</p>
</div></td></tr>
<tr><td><a name="position"></a><span class="ts" id=1000 data-target="#details-1000" data-toggle="collapse"><span class="ident">position</span><span>: </span><a class="type-ref" href="#Position">Position</a></span></td><td><div class="comment"><p>The position at which the command was invoked.</p>
</div></td></tr>
<tr><td><a name="token"></a><span class="ts" id=1001 data-target="#details-1001" data-toggle="collapse"><span class="ident">token</span><span>: </span><a class="type-ref" href="#CancellationToken">CancellationToken</a></span></td><td><div class="comment"><p>A cancellation token.</p>
</div></td></tr>
<tr><td><a name="context"></a><span class="ts" id=1002 data-target="#details-1002" data-toggle="collapse"><span class="ident">context</span><span>: </span><a class="type-ref" href="#CompletionContext">CompletionContext</a></span></td><td><div class="comment"><p>How the completion was triggered.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#ProviderResult">ProviderResult</a>&lt;<a class="type-ref" href="#CompletionItem">CompletionItem</a>[] &#124; <a class="type-ref" href="#CompletionList">CompletionList</a>&gt;</span></td><td><div class="comment"><p>An array of completions, a <a href="#CompletionList">completion list</a>, or a thenable that resolves to either.
The lack of a result can be signaled by returning <code>undefined</code>, <code>null</code>, or an empty array.</p>
</div></td></tr>
</table>
</div>
</div>



<a name="CompletionItemProvider.resolveCompletionItem"></a><span class="ts" id=1004 data-target="#details-1004" data-toggle="collapse"><span class="ident">resolveCompletionItem</span><span>(</span><span class="ident">item</span><span>: </span><a class="type-ref" href="#CompletionItem">CompletionItem</a>, <span class="ident">token</span><span>: </span><a class="type-ref" href="#CancellationToken">CancellationToken</a><span>)</span><span>: </span><a class="type-ref" href="#ProviderResult">ProviderResult</a>&lt;<a class="type-ref" href="#CompletionItem">CompletionItem</a>&gt;</span>
<div class="details collapse" id="details-1004">
<div class="comment"><p>Given a completion item fill in more data, like <a href="#CompletionItem.documentation">doc-comment</a>
or <a href="#CompletionItem.detail">details</a>.</p>
<p>The editor will only resolve a completion item once.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="item"></a><span class="ts" id=1005 data-target="#details-1005" data-toggle="collapse"><span class="ident">item</span><span>: </span><a class="type-ref" href="#CompletionItem">CompletionItem</a></span></td><td><div class="comment"><p>A completion item currently active in the UI.</p>
</div></td></tr>
<tr><td><a name="token"></a><span class="ts" id=1006 data-target="#details-1006" data-toggle="collapse"><span class="ident">token</span><span>: </span><a class="type-ref" href="#CancellationToken">CancellationToken</a></span></td><td><div class="comment"><p>A cancellation token.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#ProviderResult">ProviderResult</a>&lt;<a class="type-ref" href="#CompletionItem">CompletionItem</a>&gt;</span></td><td><div class="comment"><p>The resolved completion item or a thenable that resolves to of such. It is OK to return the given
<code>item</code>. When no result is returned, the given <code>item</code> will be used.</p>
</div></td></tr>
</table>
</div>
</div>

### <a name="CompletionList"></a><span class="code-item" id=982>CompletionList</span>



<div class="comment"><p>Represents a collection of <a href="#CompletionItem">completion items</a> to be presented
in the editor.</p>
</div>

#### Constructors



<a name="CompletionList.new CompletionList"></a><span class="ts" id=986 data-target="#details-986" data-toggle="collapse"><span class="ident">new CompletionList</span><span>(</span><span class="ident">items</span><span>?</span><span>: </span><a class="type-ref" href="#CompletionItem">CompletionItem</a>[], <span class="ident">isIncomplete</span><span>?</span><span>: </span><a class="type-intrinsic">boolean</a><span>)</span><span>: </span><a class="type-ref" href="#CompletionList">CompletionList</a></span>
<div class="details collapse" id="details-986">
<div class="comment"><p>Creates a new completion list.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="items"></a><span class="ts" id=987 data-target="#details-987" data-toggle="collapse"><span class="ident">items</span><span>?</span><span>: </span><a class="type-ref" href="#CompletionItem">CompletionItem</a>[]</span></td><td><div class="comment"><p>The completion items.</p>
</div></td></tr>
<tr><td><a name="isIncomplete"></a><span class="ts" id=988 data-target="#details-988" data-toggle="collapse"><span class="ident">isIncomplete</span><span>?</span><span>: </span><a class="type-intrinsic">boolean</a></span></td><td><div class="comment"><p>The list is not complete.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#CompletionList">CompletionList</a></span></td><td><div class="comment"></div></td></tr>
</table>
</div>
</div>

#### Properties



<a name="CompletionList.isIncomplete"></a><span class="ts" id=983 data-target="#details-983" data-toggle="collapse"><span class="ident">isIncomplete</span><span>?</span><span>: </span><a class="type-intrinsic">boolean</a></span>
<div class="details collapse" id="details-983">
<div class="comment"><p>This list is not complete. Further typing should result in recomputing
this list.</p>
</div>
</div>



<a name="CompletionList.items"></a><span class="ts" id=984 data-target="#details-984" data-toggle="collapse"><span class="ident">items</span><span>: </span><a class="type-ref" href="#CompletionItem">CompletionItem</a>[]</span>
<div class="details collapse" id="details-984">
<div class="comment"><p>The completion items.</p>
</div>
</div>

### <a name="CompletionTriggerKind"></a><span class="code-item" id=989>CompletionTriggerKind</span>



<div class="comment"><p>How a <a href="#CompletionItemProvider">completion provider</a> was triggered</p>
</div>

#### Enumeration members



<a name="CompletionTriggerKind.Invoke"></a><span class="ts" id=990 data-target="#details-990" data-toggle="collapse"><span class="ident">Invoke</span></span>
<div class="details collapse" id="details-990">
<em>0</em>
</div>



<a name="CompletionTriggerKind.TriggerCharacter"></a><span class="ts" id=991 data-target="#details-991" data-toggle="collapse"><span class="ident">TriggerCharacter</span></span>
<div class="details collapse" id="details-991">
<em>1</em>
</div>



<a name="CompletionTriggerKind.TriggerForIncompleteCompletions"></a><span class="ts" id=992 data-target="#details-992" data-toggle="collapse"><span class="ident">TriggerForIncompleteCompletions</span></span>
<div class="details collapse" id="details-992">
<em>2</em>
</div>

### <a name="ConfigurationChangeEvent"></a><span class="code-item" id=1808>ConfigurationChangeEvent</span>



<div class="comment"><p>An event describing the change in Configuration</p>
</div>

#### Methods



<a name="ConfigurationChangeEvent.affectsConfiguration"></a><span class="ts" id=1810 data-target="#details-1810" data-toggle="collapse"><span class="ident">affectsConfiguration</span><span>(</span><span class="ident">section</span><span>: </span><a class="type-intrinsic">string</a>, <span class="ident">resource</span><span>?</span><span>: </span><a class="type-ref" href="#Uri">Uri</a><span>)</span><span>: </span><a class="type-intrinsic">boolean</a></span>
<div class="details collapse" id="details-1810">
<div class="comment"><p>Returns <code>true</code> if the given section for the given resource (if provided) is affected.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="section"></a><span class="ts" id=1811 data-target="#details-1811" data-toggle="collapse"><span class="ident">section</span><span>: </span><a class="type-intrinsic">string</a></span></td><td><div class="comment"><p>Configuration name, supports <em>dotted</em> names.</p>
</div></td></tr>
<tr><td><a name="resource"></a><span class="ts" id=1812 data-target="#details-1812" data-toggle="collapse"><span class="ident">resource</span><span>?</span><span>: </span><a class="type-ref" href="#Uri">Uri</a></span></td><td><div class="comment"><p>A resource Uri.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-intrinsic">boolean</a></span></td><td><div class="comment"><p><code>true</code> if the given section for the given resource (if provided) is affected.</p>
</div></td></tr>
</table>
</div>
</div>

### <a name="ConfigurationTarget"></a><span class="code-item" id=1124>ConfigurationTarget</span>



<div class="comment"><p>The configuration target</p>
</div>

#### Enumeration members



<a name="ConfigurationTarget.Global"></a><span class="ts" id=1125 data-target="#details-1125" data-toggle="collapse"><span class="ident">Global</span></span>
<div class="details collapse" id="details-1125">
<em>1</em>
</div>



<a name="ConfigurationTarget.Workspace"></a><span class="ts" id=1126 data-target="#details-1126" data-toggle="collapse"><span class="ident">Workspace</span></span>
<div class="details collapse" id="details-1126">
<em>2</em>
</div>



<a name="ConfigurationTarget.WorkspaceFolder"></a><span class="ts" id=1127 data-target="#details-1127" data-toggle="collapse"><span class="ident">WorkspaceFolder</span></span>
<div class="details collapse" id="details-1127">
<em>3</em>
</div>

### <a name="DebugAdapterDescriptor"></a><span class="code-item" id=2434>DebugAdapterDescriptor</span>



<div class="comment"></div>



<a name="DebugAdapterDescriptor"></a><span class="ts" id=2434 data-target="#details-2434" data-toggle="collapse"><span class="ident">DebugAdapterDescriptor</span><span>: </span><a class="type-ref" href="#DebugAdapterExecutable">DebugAdapterExecutable</a> &#124; <a class="type-ref" href="#DebugAdapterServer">DebugAdapterServer</a></span>

### <a name="DebugAdapterDescriptorFactory"></a><span class="code-item" id=1909>DebugAdapterDescriptorFactory</span>



<div class="comment"></div>

#### Methods



<a name="DebugAdapterDescriptorFactory.createDebugAdapterDescriptor"></a><span class="ts" id=1911 data-target="#details-1911" data-toggle="collapse"><span class="ident">createDebugAdapterDescriptor</span><span>(</span><span class="ident">session</span><span>: </span><a class="type-ref" href="#DebugSession">DebugSession</a>, <span class="ident">executable</span><span>: </span><a class="type-ref" href="#DebugAdapterExecutable">DebugAdapterExecutable</a> &#124; <a class="type-intrinsic">undefined</a><span>)</span><span>: </span><a class="type-ref" href="#ProviderResult">ProviderResult</a>&lt;<a class="type-ref" href="#DebugAdapterDescriptor">DebugAdapterDescriptor</a>&gt;</span>
<div class="details collapse" id="details-1911">
<div class="comment"><p>&#39;createDebugAdapterDescriptor&#39; is called at the start of a debug session to provide details about the debug adapter to use.
These details must be returned as objects of type <a href="#DebugAdapterDescriptor">DebugAdapterDescriptor</a>.
Currently two types of debug adapters are supported:</p>
<ul>
<li>a debug adapter executable is specified as a command path and arguments (see <a href="#DebugAdapterExecutable">DebugAdapterExecutable</a>),</li>
<li>a debug adapter server reachable via a communication port (see <a href="#DebugAdapterServer">DebugAdapterServer</a>).
If the method is not implemented the default behavior is this:
createDebugAdapter(session: DebugSession, executable: DebugAdapterExecutable) {
   if (typeof session.configuration.debugServer === &#39;number&#39;) {
<pre><code>  return new DebugAdapterServer(session.configuration.debugServer);
</code></pre>   }
   return executable;
}</li>
</ul>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="session"></a><span class="ts" id=1912 data-target="#details-1912" data-toggle="collapse"><span class="ident">session</span><span>: </span><a class="type-ref" href="#DebugSession">DebugSession</a></span></td><td><div class="comment"><p>The <a href="#DebugSession">debug session</a> for which the debug adapter will be used.</p>
</div></td></tr>
<tr><td><a name="executable"></a><span class="ts" id=1913 data-target="#details-1913" data-toggle="collapse"><span class="ident">executable</span><span>: </span><a class="type-ref" href="#DebugAdapterExecutable">DebugAdapterExecutable</a> &#124; <a class="type-intrinsic">undefined</a></span></td><td><div class="comment"><p>The debug adapter&#39;s executable information as specified in the package.json (or undefined if no such information exists).</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#ProviderResult">ProviderResult</a>&lt;<a class="type-ref" href="#DebugAdapterDescriptor">DebugAdapterDescriptor</a>&gt;</span></td><td><div class="comment"><p>a <a href="#DebugAdapterDescriptor">debug adapter descriptor</a> or undefined.</p>
</div></td></tr>
</table>
</div>
</div>

### <a name="DebugAdapterExecutable"></a><span class="code-item" id=1887>DebugAdapterExecutable</span>



<div class="comment"><p>Represents a debug adapter executable and optional arguments and runtime options passed to it.</p>
</div>

#### Constructors



<a name="DebugAdapterExecutable.new DebugAdapterExecutable"></a><span class="ts" id=1889 data-target="#details-1889" data-toggle="collapse"><span class="ident">new DebugAdapterExecutable</span><span>(</span><span class="ident">command</span><span>: </span><a class="type-intrinsic">string</a>, <span class="ident">args</span><span>?</span><span>: </span><a class="type-intrinsic">string</a>[], <span class="ident">options</span><span>?</span><span>: </span><a class="type-ref" href="#DebugAdapterExecutableOptions">DebugAdapterExecutableOptions</a><span>)</span><span>: </span><a class="type-ref" href="#DebugAdapterExecutable">DebugAdapterExecutable</a></span>
<div class="details collapse" id="details-1889">
<div class="comment"><p>Creates a description for a debug adapter based on an executable program.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="command"></a><span class="ts" id=1890 data-target="#details-1890" data-toggle="collapse"><span class="ident">command</span><span>: </span><a class="type-intrinsic">string</a></span></td><td><div class="comment"><p>The command or executable path that implements the debug adapter.</p>
</div></td></tr>
<tr><td><a name="args"></a><span class="ts" id=1891 data-target="#details-1891" data-toggle="collapse"><span class="ident">args</span><span>?</span><span>: </span><a class="type-intrinsic">string</a>[]</span></td><td><div class="comment"><p>Optional arguments to be passed to the command or executable.</p>
</div></td></tr>
<tr><td><a name="options"></a><span class="ts" id=1892 data-target="#details-1892" data-toggle="collapse"><span class="ident">options</span><span>?</span><span>: </span><a class="type-ref" href="#DebugAdapterExecutableOptions">DebugAdapterExecutableOptions</a></span></td><td><div class="comment"><p>Optional options to be used when starting the command or executable.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#DebugAdapterExecutable">DebugAdapterExecutable</a></span></td><td><div class="comment"></div></td></tr>
</table>
</div>
</div>

#### Properties



<a name="DebugAdapterExecutable.args"></a><span class="ts" id=1894 data-target="#details-1894" data-toggle="collapse"><span class="ident">args</span><span>: </span><a class="type-intrinsic">string</a>[]</span>
<div class="details collapse" id="details-1894">
<div class="comment"><p>The arguments passed to the debug adapter executable. Defaults to an empty array.</p>
</div>
</div>



<a name="DebugAdapterExecutable.command"></a><span class="ts" id=1893 data-target="#details-1893" data-toggle="collapse"><span class="ident">command</span><span>: </span><a class="type-intrinsic">string</a></span>
<div class="details collapse" id="details-1893">
<div class="comment"><p>The command or path of the debug adapter executable.
A command must be either an absolute path of an executable or the name of an command to be looked up via the PATH environment variable.
The special value &#39;node&#39; will be mapped to VS Code&#39;s built-in Node.js runtime.</p>
</div>
</div>



<a name="DebugAdapterExecutable.options"></a><span class="ts" id=1895 data-target="#details-1895" data-toggle="collapse"><span class="ident">options</span><span>?</span><span>: </span><a class="type-ref" href="#DebugAdapterExecutableOptions">DebugAdapterExecutableOptions</a></span>
<div class="details collapse" id="details-1895">
<div class="comment"><p>Optional options to be used when the debug adapter is started.
Defaults to undefined.</p>
</div>
</div>

### <a name="DebugAdapterExecutableOptions"></a><span class="code-item" id=1896>DebugAdapterExecutableOptions</span>



<div class="comment"><p>Options for a debug adapter executable.</p>
</div>

#### Properties



<a name="DebugAdapterExecutableOptions.cwd"></a><span class="ts" id=1901 data-target="#details-1901" data-toggle="collapse"><span class="ident">cwd</span><span>?</span><span>: </span><a class="type-intrinsic">string</a></span>
<div class="details collapse" id="details-1901">
<div class="comment"><p>The current working directory for the executed debug adapter.</p>
</div>
</div>



<a name="DebugAdapterExecutableOptions.env"></a><span class="ts" id=1897 data-target="#details-1897" data-toggle="collapse"><span class="ident">env</span><span>?</span><span>: </span></span>
<div class="details collapse" id="details-1897">
<div class="comment"><p>The additional environment of the executed program or shell. If omitted
the parent process&#39; environment is used. If provided it is merged with
the parent process&#39; environment.</p>
</div>
</div>

### <a name="DebugAdapterServer"></a><span class="code-item" id=1902>DebugAdapterServer</span>



<div class="comment"><p>Represents a debug adapter running as a socket based server.</p>
</div>

#### Constructors



<a name="DebugAdapterServer.new DebugAdapterServer"></a><span class="ts" id=1906 data-target="#details-1906" data-toggle="collapse"><span class="ident">new DebugAdapterServer</span><span>(</span><span class="ident">port</span><span>: </span><a class="type-intrinsic">number</a>, <span class="ident">host</span><span>?</span><span>: </span><a class="type-intrinsic">string</a><span>)</span><span>: </span><a class="type-ref" href="#DebugAdapterServer">DebugAdapterServer</a></span>
<div class="details collapse" id="details-1906">
<div class="comment"><p>Create a description for a debug adapter running as a socket based server.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="port"></a><span class="ts" id=1907 data-target="#details-1907" data-toggle="collapse"><span class="ident">port</span><span>: </span><a class="type-intrinsic">number</a></span></td><td><div class="comment"></div></td></tr>
<tr><td><a name="host"></a><span class="ts" id=1908 data-target="#details-1908" data-toggle="collapse"><span class="ident">host</span><span>?</span><span>: </span><a class="type-intrinsic">string</a></span></td><td><div class="comment"></div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#DebugAdapterServer">DebugAdapterServer</a></span></td><td><div class="comment"></div></td></tr>
</table>
</div>
</div>

#### Properties



<a name="DebugAdapterServer.host"></a><span class="ts" id=1904 data-target="#details-1904" data-toggle="collapse"><span class="ident">host</span><span>?</span><span>: </span><a class="type-intrinsic">string</a></span>
<div class="details collapse" id="details-1904">
<div class="comment"><p>The host.</p>
</div>
</div>



<a name="DebugAdapterServer.port"></a><span class="ts" id=1903 data-target="#details-1903" data-toggle="collapse"><span class="ident">port</span><span>: </span><a class="type-intrinsic">number</a></span>
<div class="details collapse" id="details-1903">
<div class="comment"><p>The port.</p>
</div>
</div>

### <a name="DebugAdapterTracker"></a><span class="code-item" id=1914>DebugAdapterTracker</span>



<div class="comment"><p>A Debug Adapter Tracker is a means to track the communication between VS Code and a Debug Adapter.</p>
</div>

#### Events



<a name="DebugAdapterTracker.onDidSendMessage"></a><span class="ts" id=1921 data-target="#details-1921" data-toggle="collapse"><span class="ident">onDidSendMessage</span><span>(</span><span class="ident">message</span><span>: </span><a class="type-intrinsic">any</a><span>)</span><span>: </span><a class="type-intrinsic">void</a></span>
<div class="details collapse" id="details-1921">
<div class="comment"><p>The debug adapter has sent a Debug Adapter Protocol message to VS Code.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="message"></a><span class="ts" id=1922 data-target="#details-1922" data-toggle="collapse"><span class="ident">message</span><span>: </span><a class="type-intrinsic">any</a></span></td><td><div class="comment"></div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-intrinsic">void</a></span></td><td><div class="comment"></div></td></tr>
</table>
</div>
</div>



<a name="DebugAdapterTracker.onWillReceiveMessage"></a><span class="ts" id=1918 data-target="#details-1918" data-toggle="collapse"><span class="ident">onWillReceiveMessage</span><span>(</span><span class="ident">message</span><span>: </span><a class="type-intrinsic">any</a><span>)</span><span>: </span><a class="type-intrinsic">void</a></span>
<div class="details collapse" id="details-1918">
<div class="comment"><p>The debug adapter is about to receive a Debug Adapter Protocol message from VS Code.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="message"></a><span class="ts" id=1919 data-target="#details-1919" data-toggle="collapse"><span class="ident">message</span><span>: </span><a class="type-intrinsic">any</a></span></td><td><div class="comment"></div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-intrinsic">void</a></span></td><td><div class="comment"></div></td></tr>
</table>
</div>
</div>



<a name="DebugAdapterTracker.onWillStartSession"></a><span class="ts" id=1916 data-target="#details-1916" data-toggle="collapse"><span class="ident">onWillStartSession</span><span>(</span><span>)</span><span>: </span><a class="type-intrinsic">void</a></span>
<div class="details collapse" id="details-1916">
<div class="comment"><p>A session with the debug adapter is about to be started.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-intrinsic">void</a></span></td><td><div class="comment"></div></td></tr>
</table>
</div>
</div>



<a name="DebugAdapterTracker.onWillStopSession"></a><span class="ts" id=1924 data-target="#details-1924" data-toggle="collapse"><span class="ident">onWillStopSession</span><span>(</span><span>)</span><span>: </span><a class="type-intrinsic">void</a></span>
<div class="details collapse" id="details-1924">
<div class="comment"><p>The debug adapter session is about to be stopped.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-intrinsic">void</a></span></td><td><div class="comment"></div></td></tr>
</table>
</div>
</div>

#### Methods



<a name="DebugAdapterTracker.onError"></a><span class="ts" id=1926 data-target="#details-1926" data-toggle="collapse"><span class="ident">onError</span><span>(</span><span class="ident">error</span><span>: </span><a class="type-ref" href="#Error">Error</a><span>)</span><span>: </span><a class="type-intrinsic">void</a></span>
<div class="details collapse" id="details-1926">
<div class="comment"><p>An error with the debug adapter has occured.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="error"></a><span class="ts" id=1927 data-target="#details-1927" data-toggle="collapse"><span class="ident">error</span><span>: </span><a class="type-ref" href="#Error">Error</a></span></td><td><div class="comment"></div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-intrinsic">void</a></span></td><td><div class="comment"></div></td></tr>
</table>
</div>
</div>



<a name="DebugAdapterTracker.onExit"></a><span class="ts" id=1929 data-target="#details-1929" data-toggle="collapse"><span class="ident">onExit</span><span>(</span><span class="ident">code</span><span>: </span><a class="type-intrinsic">number</a> &#124; <a class="type-intrinsic">undefined</a>, <span class="ident">signal</span><span>: </span><a class="type-intrinsic">string</a> &#124; <a class="type-intrinsic">undefined</a><span>)</span><span>: </span><a class="type-intrinsic">void</a></span>
<div class="details collapse" id="details-1929">
<div class="comment"><p>The debug adapter has exited with the given exit code or signal.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="code"></a><span class="ts" id=1930 data-target="#details-1930" data-toggle="collapse"><span class="ident">code</span><span>: </span><a class="type-intrinsic">number</a> &#124; <a class="type-intrinsic">undefined</a></span></td><td><div class="comment"></div></td></tr>
<tr><td><a name="signal"></a><span class="ts" id=1931 data-target="#details-1931" data-toggle="collapse"><span class="ident">signal</span><span>: </span><a class="type-intrinsic">string</a> &#124; <a class="type-intrinsic">undefined</a></span></td><td><div class="comment"></div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-intrinsic">void</a></span></td><td><div class="comment"></div></td></tr>
</table>
</div>
</div>

### <a name="DebugAdapterTrackerFactory"></a><span class="code-item" id=1932>DebugAdapterTrackerFactory</span>



<div class="comment"></div>

#### Methods



<a name="DebugAdapterTrackerFactory.createDebugAdapterTracker"></a><span class="ts" id=1934 data-target="#details-1934" data-toggle="collapse"><span class="ident">createDebugAdapterTracker</span><span>(</span><span class="ident">session</span><span>: </span><a class="type-ref" href="#DebugSession">DebugSession</a><span>)</span><span>: </span><a class="type-ref" href="#ProviderResult">ProviderResult</a>&lt;<a class="type-ref" href="#DebugAdapterTracker">DebugAdapterTracker</a>&gt;</span>
<div class="details collapse" id="details-1934">
<div class="comment"><p>The method &#39;createDebugAdapterTracker&#39; is called at the start of a debug session in order
to return a &quot;tracker&quot; object that provides read-access to the communication between VS Code and a debug adapter.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="session"></a><span class="ts" id=1935 data-target="#details-1935" data-toggle="collapse"><span class="ident">session</span><span>: </span><a class="type-ref" href="#DebugSession">DebugSession</a></span></td><td><div class="comment"><p>The <a href="#DebugSession">debug session</a> for which the debug adapter tracker will be used.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#ProviderResult">ProviderResult</a>&lt;<a class="type-ref" href="#DebugAdapterTracker">DebugAdapterTracker</a>&gt;</span></td><td><div class="comment"><p>A <a href="#DebugAdapterTracker">debug adapter tracker</a> or undefined.</p>
</div></td></tr>
</table>
</div>
</div>

### <a name="DebugConfiguration"></a><span class="code-item" id=1857>DebugConfiguration</span>



<div class="comment"><p>Configuration for a debug session.</p>
</div>

#### Properties



<a name="DebugConfiguration.name"></a><span class="ts" id=1859 data-target="#details-1859" data-toggle="collapse"><span class="ident">name</span><span>: </span><a class="type-intrinsic">string</a></span>
<div class="details collapse" id="details-1859">
<div class="comment"><p>The name of the debug session.</p>
</div>
</div>



<a name="DebugConfiguration.request"></a><span class="ts" id=1860 data-target="#details-1860" data-toggle="collapse"><span class="ident">request</span><span>: </span><a class="type-intrinsic">string</a></span>
<div class="details collapse" id="details-1860">
<div class="comment"><p>The request type of the debug session.</p>
</div>
</div>



<a name="DebugConfiguration.type"></a><span class="ts" id=1858 data-target="#details-1858" data-toggle="collapse"><span class="ident">type</span><span>: </span><a class="type-intrinsic">string</a></span>
<div class="details collapse" id="details-1858">
<div class="comment"><p>The type of the debug session.</p>
</div>
</div>

### <a name="DebugConfigurationProvider"></a><span class="code-item" id=1877>DebugConfigurationProvider</span>



<div class="comment"><p>A debug configuration provider allows to add the initial debug configurations to a newly created launch.json
and to resolve a launch configuration before it is used to start a new debug session.
A debug configuration provider is registered via #debug.registerDebugConfigurationProvider.</p>
</div>

#### Methods



<a name="DebugConfigurationProvider.provideDebugConfigurations"></a><span class="ts" id=1879 data-target="#details-1879" data-toggle="collapse"><span class="ident">provideDebugConfigurations</span><span>(</span><span class="ident">folder</span><span>: </span><a class="type-ref" href="#WorkspaceFolder">WorkspaceFolder</a> &#124; <a class="type-intrinsic">undefined</a>, <span class="ident">token</span><span>?</span><span>: </span><a class="type-ref" href="#CancellationToken">CancellationToken</a><span>)</span><span>: </span><a class="type-ref" href="#ProviderResult">ProviderResult</a>&lt;<a class="type-ref" href="#DebugConfiguration">DebugConfiguration</a>[]&gt;</span>
<div class="details collapse" id="details-1879">
<div class="comment"><p>Provides initial <a href="#DebugConfiguration">debug configuration</a>. If more than one debug configuration provider is
registered for the same type, debug configurations are concatenated in arbitrary order.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="folder"></a><span class="ts" id=1880 data-target="#details-1880" data-toggle="collapse"><span class="ident">folder</span><span>: </span><a class="type-ref" href="#WorkspaceFolder">WorkspaceFolder</a> &#124; <a class="type-intrinsic">undefined</a></span></td><td><div class="comment"><p>The workspace folder for which the configurations are used or <code>undefined</code> for a folderless setup.</p>
</div></td></tr>
<tr><td><a name="token"></a><span class="ts" id=1881 data-target="#details-1881" data-toggle="collapse"><span class="ident">token</span><span>?</span><span>: </span><a class="type-ref" href="#CancellationToken">CancellationToken</a></span></td><td><div class="comment"><p>A cancellation token.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#ProviderResult">ProviderResult</a>&lt;<a class="type-ref" href="#DebugConfiguration">DebugConfiguration</a>[]&gt;</span></td><td><div class="comment"><p>An array of <a href="#DebugConfiguration">debug configurations</a>.</p>
</div></td></tr>
</table>
</div>
</div>



<a name="DebugConfigurationProvider.resolveDebugConfiguration"></a><span class="ts" id=1883 data-target="#details-1883" data-toggle="collapse"><span class="ident">resolveDebugConfiguration</span><span>(</span><span class="ident">folder</span><span>: </span><a class="type-ref" href="#WorkspaceFolder">WorkspaceFolder</a> &#124; <a class="type-intrinsic">undefined</a>, <span class="ident">debugConfiguration</span><span>: </span><a class="type-ref" href="#DebugConfiguration">DebugConfiguration</a>, <span class="ident">token</span><span>?</span><span>: </span><a class="type-ref" href="#CancellationToken">CancellationToken</a><span>)</span><span>: </span><a class="type-ref" href="#ProviderResult">ProviderResult</a>&lt;<a class="type-ref" href="#DebugConfiguration">DebugConfiguration</a>&gt;</span>
<div class="details collapse" id="details-1883">
<div class="comment"><p>Resolves a <a href="#DebugConfiguration">debug configuration</a> by filling in missing values or by adding/changing/removing attributes.
If more than one debug configuration provider is registered for the same type, the resolveDebugConfiguration calls are chained
in arbitrary order and the initial debug configuration is piped through the chain.
Returning the value &#39;undefined&#39; prevents the debug session from starting.
Returning the value &#39;null&#39; prevents the debug session from starting and opens the underlying debug configuration instead.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="folder"></a><span class="ts" id=1884 data-target="#details-1884" data-toggle="collapse"><span class="ident">folder</span><span>: </span><a class="type-ref" href="#WorkspaceFolder">WorkspaceFolder</a> &#124; <a class="type-intrinsic">undefined</a></span></td><td><div class="comment"><p>The workspace folder from which the configuration originates from or <code>undefined</code> for a folderless setup.</p>
</div></td></tr>
<tr><td><a name="debugConfiguration"></a><span class="ts" id=1885 data-target="#details-1885" data-toggle="collapse"><span class="ident">debugConfiguration</span><span>: </span><a class="type-ref" href="#DebugConfiguration">DebugConfiguration</a></span></td><td><div class="comment"><p>The <a href="#DebugConfiguration">debug configuration</a> to resolve.</p>
</div></td></tr>
<tr><td><a name="token"></a><span class="ts" id=1886 data-target="#details-1886" data-toggle="collapse"><span class="ident">token</span><span>?</span><span>: </span><a class="type-ref" href="#CancellationToken">CancellationToken</a></span></td><td><div class="comment"><p>A cancellation token.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#ProviderResult">ProviderResult</a>&lt;<a class="type-ref" href="#DebugConfiguration">DebugConfiguration</a>&gt;</span></td><td><div class="comment"><p>The resolved debug configuration or undefined or null.</p>
</div></td></tr>
</table>
</div>
</div>

### <a name="DebugConsole"></a><span class="code-item" id=1936>DebugConsole</span>



<div class="comment"><p>Represents the debug console.</p>
</div>

#### Methods



<a name="DebugConsole.append"></a><span class="ts" id=1938 data-target="#details-1938" data-toggle="collapse"><span class="ident">append</span><span>(</span><span class="ident">value</span><span>: </span><a class="type-intrinsic">string</a><span>)</span><span>: </span><a class="type-intrinsic">void</a></span>
<div class="details collapse" id="details-1938">
<div class="comment"><p>Append the given value to the debug console.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="value"></a><span class="ts" id=1939 data-target="#details-1939" data-toggle="collapse"><span class="ident">value</span><span>: </span><a class="type-intrinsic">string</a></span></td><td><div class="comment"><p>A string, falsy values will not be printed.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-intrinsic">void</a></span></td><td><div class="comment"></div></td></tr>
</table>
</div>
</div>



<a name="DebugConsole.appendLine"></a><span class="ts" id=1941 data-target="#details-1941" data-toggle="collapse"><span class="ident">appendLine</span><span>(</span><span class="ident">value</span><span>: </span><a class="type-intrinsic">string</a><span>)</span><span>: </span><a class="type-intrinsic">void</a></span>
<div class="details collapse" id="details-1941">
<div class="comment"><p>Append the given value and a line feed character
to the debug console.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="value"></a><span class="ts" id=1942 data-target="#details-1942" data-toggle="collapse"><span class="ident">value</span><span>: </span><a class="type-intrinsic">string</a></span></td><td><div class="comment"><p>A string, falsy values will be printed.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-intrinsic">void</a></span></td><td><div class="comment"></div></td></tr>
</table>
</div>
</div>

### <a name="DebugSession"></a><span class="code-item" id=1863>DebugSession</span>



<div class="comment"><p>A debug session.</p>
</div>

#### Properties



<a name="DebugSession.configuration"></a><span class="ts" id=1868 data-target="#details-1868" data-toggle="collapse"><span class="ident">configuration</span><span>: </span><a class="type-ref" href="#DebugConfiguration">DebugConfiguration</a></span>
<div class="details collapse" id="details-1868">
<div class="comment"><p>The &quot;resolved&quot; <a href="#DebugConfiguration">debug configuration</a> of this session.
&quot;Resolved&quot; means that</p>
<ul>
<li>all variables have been substituted and</li>
<li>platform specific attribute sections have been &quot;flattened&quot; for the matching platform and removed for non-matching platforms.</li>
</ul>
</div>
</div>



<a name="DebugSession.id"></a><span class="ts" id=1864 data-target="#details-1864" data-toggle="collapse"><span class="ident">id</span><span>: </span><a class="type-intrinsic">string</a></span>
<div class="details collapse" id="details-1864">
<div class="comment"><p>The unique ID of this debug session.</p>
</div>
</div>



<a name="DebugSession.name"></a><span class="ts" id=1866 data-target="#details-1866" data-toggle="collapse"><span class="ident">name</span><span>: </span><a class="type-intrinsic">string</a></span>
<div class="details collapse" id="details-1866">
<div class="comment"><p>The debug session&#39;s name from the <a href="#DebugConfiguration">debug configuration</a>.</p>
</div>
</div>



<a name="DebugSession.type"></a><span class="ts" id=1865 data-target="#details-1865" data-toggle="collapse"><span class="ident">type</span><span>: </span><a class="type-intrinsic">string</a></span>
<div class="details collapse" id="details-1865">
<div class="comment"><p>The debug session&#39;s type from the <a href="#DebugConfiguration">debug configuration</a>.</p>
</div>
</div>



<a name="DebugSession.workspaceFolder"></a><span class="ts" id=1867 data-target="#details-1867" data-toggle="collapse"><span class="ident">workspaceFolder</span><span>: </span><a class="type-ref" href="#WorkspaceFolder">WorkspaceFolder</a> &#124; <a class="type-intrinsic">undefined</a></span>
<div class="details collapse" id="details-1867">
<div class="comment"><p>The workspace folder of this session or <code>undefined</code> for a folderless setup.</p>
</div>
</div>

#### Methods



<a name="DebugSession.customRequest"></a><span class="ts" id=1870 data-target="#details-1870" data-toggle="collapse"><span class="ident">customRequest</span><span>(</span><span class="ident">command</span><span>: </span><a class="type-intrinsic">string</a>, <span class="ident">args</span><span>?</span><span>: </span><a class="type-intrinsic">any</a><span>)</span><span>: </span><a class="type-ref" href="#Thenable">Thenable</a>&lt;<a class="type-intrinsic">any</a>&gt;</span>
<div class="details collapse" id="details-1870">
<div class="comment"><p>Send a custom request to the debug adapter.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="command"></a><span class="ts" id=1871 data-target="#details-1871" data-toggle="collapse"><span class="ident">command</span><span>: </span><a class="type-intrinsic">string</a></span></td><td><div class="comment"></div></td></tr>
<tr><td><a name="args"></a><span class="ts" id=1872 data-target="#details-1872" data-toggle="collapse"><span class="ident">args</span><span>?</span><span>: </span><a class="type-intrinsic">any</a></span></td><td><div class="comment"></div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#Thenable">Thenable</a>&lt;<a class="type-intrinsic">any</a>&gt;</span></td><td><div class="comment"></div></td></tr>
</table>
</div>
</div>

### <a name="DebugSessionCustomEvent"></a><span class="code-item" id=1873>DebugSessionCustomEvent</span>



<div class="comment"><p>A custom Debug Adapter Protocol event received from a <a href="#DebugSession">debug session</a>.</p>
</div>

#### Properties



<a name="DebugSessionCustomEvent.body"></a><span class="ts" id=1876 data-target="#details-1876" data-toggle="collapse"><span class="ident">body</span><span>?</span><span>: </span><a class="type-intrinsic">any</a></span>
<div class="details collapse" id="details-1876">
<div class="comment"><p>Event specific information.</p>
</div>
</div>



<a name="DebugSessionCustomEvent.event"></a><span class="ts" id=1875 data-target="#details-1875" data-toggle="collapse"><span class="ident">event</span><span>: </span><a class="type-intrinsic">string</a></span>
<div class="details collapse" id="details-1875">
<div class="comment"><p>Type of event.</p>
</div>
</div>



<a name="DebugSessionCustomEvent.session"></a><span class="ts" id=1874 data-target="#details-1874" data-toggle="collapse"><span class="ident">session</span><span>: </span><a class="type-ref" href="#DebugSession">DebugSession</a></span>
<div class="details collapse" id="details-1874">
<div class="comment"><p>The <a href="#DebugSession">debug session</a> for which the custom event was received.</p>
</div>
</div>

### <a name="Declaration"></a><span class="code-item" id=1994>Declaration</span>



<div class="comment"><p>The declaration of a symbol representation as one or many <a href="#Location">locations</a>
or [location links][#LocationLink].</p>
</div>



<a name="Declaration"></a><span class="ts" id=1994 data-target="#details-1994" data-toggle="collapse"><span class="ident">Declaration</span><span>: </span><a class="type-ref" href="#Location">Location</a> &#124; <a class="type-ref" href="#Location">Location</a>[] &#124; <a class="type-ref" href="#LocationLink">LocationLink</a>[]</span>

### <a name="DeclarationProvider"></a><span class="code-item" id=627>DeclarationProvider</span>



<div class="comment"><p>The declaration provider interface defines the contract between extensions and
the go to declaration feature.</p>
</div>

#### Methods



<a name="DeclarationProvider.provideDeclaration"></a><span class="ts" id=629 data-target="#details-629" data-toggle="collapse"><span class="ident">provideDeclaration</span><span>(</span><span class="ident">document</span><span>: </span><a class="type-ref" href="#TextDocument">TextDocument</a>, <span class="ident">position</span><span>: </span><a class="type-ref" href="#Position">Position</a>, <span class="ident">token</span><span>: </span><a class="type-ref" href="#CancellationToken">CancellationToken</a><span>)</span><span>: </span><a class="type-ref" href="#ProviderResult">ProviderResult</a>&lt;<a class="type-ref" href="#Declaration">Declaration</a>&gt;</span>
<div class="details collapse" id="details-629">
<div class="comment"><p>Provide the declaration of the symbol at the given position and document.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="document"></a><span class="ts" id=630 data-target="#details-630" data-toggle="collapse"><span class="ident">document</span><span>: </span><a class="type-ref" href="#TextDocument">TextDocument</a></span></td><td><div class="comment"><p>The document in which the command was invoked.</p>
</div></td></tr>
<tr><td><a name="position"></a><span class="ts" id=631 data-target="#details-631" data-toggle="collapse"><span class="ident">position</span><span>: </span><a class="type-ref" href="#Position">Position</a></span></td><td><div class="comment"><p>The position at which the command was invoked.</p>
</div></td></tr>
<tr><td><a name="token"></a><span class="ts" id=632 data-target="#details-632" data-toggle="collapse"><span class="ident">token</span><span>: </span><a class="type-ref" href="#CancellationToken">CancellationToken</a></span></td><td><div class="comment"><p>A cancellation token.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#ProviderResult">ProviderResult</a>&lt;<a class="type-ref" href="#Declaration">Declaration</a>&gt;</span></td><td><div class="comment"><p>A declaration or a thenable that resolves to such. The lack of a result can be
signaled by returning <code>undefined</code> or <code>null</code>.</p>
</div></td></tr>
</table>
</div>
</div>

### <a name="DecorationInstanceRenderOptions"></a><span class="code-item" id=330>DecorationInstanceRenderOptions</span>



<div class="comment"></div>

#### Properties



<a name="DecorationInstanceRenderOptions.after"></a><span class="ts" id=334 data-target="#details-334" data-toggle="collapse"><span class="ident">after</span><span>?</span><span>: </span><a class="type-ref" href="#ThemableDecorationAttachmentRenderOptions">ThemableDecorationAttachmentRenderOptions</a></span>
<div class="details collapse" id="details-334">
<div class="comment"><p>Defines the rendering options of the attachment that is inserted after the decorated text.</p>
</div>
</div>



<a name="DecorationInstanceRenderOptions.before"></a><span class="ts" id=333 data-target="#details-333" data-toggle="collapse"><span class="ident">before</span><span>?</span><span>: </span><a class="type-ref" href="#ThemableDecorationAttachmentRenderOptions">ThemableDecorationAttachmentRenderOptions</a></span>
<div class="details collapse" id="details-333">
<div class="comment"><p>Defines the rendering options of the attachment that is inserted before the decorated text.</p>
</div>
</div>



<a name="DecorationInstanceRenderOptions.dark"></a><span class="ts" id=332 data-target="#details-332" data-toggle="collapse"><span class="ident">dark</span><span>?</span><span>: </span><a class="type-ref" href="#ThemableDecorationInstanceRenderOptions">ThemableDecorationInstanceRenderOptions</a></span>
<div class="details collapse" id="details-332">
<div class="comment"><p>Overwrite options for dark themes.</p>
</div>
</div>



<a name="DecorationInstanceRenderOptions.light"></a><span class="ts" id=331 data-target="#details-331" data-toggle="collapse"><span class="ident">light</span><span>?</span><span>: </span><a class="type-ref" href="#ThemableDecorationInstanceRenderOptions">ThemableDecorationInstanceRenderOptions</a></span>
<div class="details collapse" id="details-331">
<div class="comment"><p>Overwrite options for light themes.</p>
</div>
</div>

### <a name="DecorationOptions"></a><span class="code-item" id=323>DecorationOptions</span>



<div class="comment"><p>Represents options for a specific decoration in a <a href="#TextEditorDecorationType">decoration set</a>.</p>
</div>

#### Properties



<a name="DecorationOptions.hoverMessage"></a><span class="ts" id=325 data-target="#details-325" data-toggle="collapse"><span class="ident">hoverMessage</span><span>?</span><span>: </span><a class="type-ref" href="#MarkedString">MarkedString</a> &#124; <a class="type-ref" href="#MarkedString">MarkedString</a>[]</span>
<div class="details collapse" id="details-325">
<div class="comment"><p>A message that should be rendered when hovering over the decoration.</p>
</div>
</div>



<a name="DecorationOptions.range"></a><span class="ts" id=324 data-target="#details-324" data-toggle="collapse"><span class="ident">range</span><span>: </span><a class="type-ref" href="#Range">Range</a></span>
<div class="details collapse" id="details-324">
<div class="comment"><p>Range to which this decoration is applied. The range must not be empty.</p>
</div>
</div>



<a name="DecorationOptions.renderOptions"></a><span class="ts" id=326 data-target="#details-326" data-toggle="collapse"><span class="ident">renderOptions</span><span>?</span><span>: </span><a class="type-ref" href="#DecorationInstanceRenderOptions">DecorationInstanceRenderOptions</a></span>
<div class="details collapse" id="details-326">
<div class="comment"><p>Render options applied to the current decoration. For performance reasons, keep the
number of decoration specific options small, and use decoration types wherever possible.</p>
</div>
</div>

### <a name="DecorationRangeBehavior"></a><span class="code-item" id=237>DecorationRangeBehavior</span>



<div class="comment"><p>Describes the behavior of decorations when typing/editing at their edges.</p>
</div>

#### Enumeration members



<a name="DecorationRangeBehavior.ClosedClosed"></a><span class="ts" id=239 data-target="#details-239" data-toggle="collapse"><span class="ident">ClosedClosed</span></span>
<div class="details collapse" id="details-239">
<em>1</em>
</div>



<a name="DecorationRangeBehavior.ClosedOpen"></a><span class="ts" id=241 data-target="#details-241" data-toggle="collapse"><span class="ident">ClosedOpen</span></span>
<div class="details collapse" id="details-241">
<em>3</em>
</div>



<a name="DecorationRangeBehavior.OpenClosed"></a><span class="ts" id=240 data-target="#details-240" data-toggle="collapse"><span class="ident">OpenClosed</span></span>
<div class="details collapse" id="details-240">
<em>2</em>
</div>



<a name="DecorationRangeBehavior.OpenOpen"></a><span class="ts" id=238 data-target="#details-238" data-toggle="collapse"><span class="ident">OpenOpen</span></span>
<div class="details collapse" id="details-238">
<em>0</em>
</div>

### <a name="DecorationRenderOptions"></a><span class="code-item" id=294>DecorationRenderOptions</span>



<div class="comment"><p>Represents rendering styles for a <a href="#TextEditorDecorationType">text editor decoration</a>.</p>
</div>

#### Properties



<a name="DecorationRenderOptions.after"></a><span class="ts" id=322 data-target="#details-322" data-toggle="collapse"><span class="ident">after</span><span>?</span><span>: </span><a class="type-ref" href="#ThemableDecorationAttachmentRenderOptions">ThemableDecorationAttachmentRenderOptions</a></span>
<div class="details collapse" id="details-322">
<div class="comment"><p>Defines the rendering options of the attachment that is inserted after the decorated text.</p>
</div>
</div>



<a name="DecorationRenderOptions.backgroundColor"></a><span class="ts" id=300 data-target="#details-300" data-toggle="collapse"><span class="ident">backgroundColor</span><span>?</span><span>: </span><a class="type-intrinsic">string</a> &#124; <a class="type-ref" href="#ThemeColor">ThemeColor</a></span>
<div class="details collapse" id="details-300">
<div class="comment"><p>Background color of the decoration. Use rgba() and define transparent background colors to play well with other decorations.
Alternatively a color from the color registry can be <a href="#ThemeColor">referenced</a>.</p>
</div>
</div>



<a name="DecorationRenderOptions.before"></a><span class="ts" id=321 data-target="#details-321" data-toggle="collapse"><span class="ident">before</span><span>?</span><span>: </span><a class="type-ref" href="#ThemableDecorationAttachmentRenderOptions">ThemableDecorationAttachmentRenderOptions</a></span>
<div class="details collapse" id="details-321">
<div class="comment"><p>Defines the rendering options of the attachment that is inserted before the decorated text.</p>
</div>
</div>



<a name="DecorationRenderOptions.border"></a><span class="ts" id=305 data-target="#details-305" data-toggle="collapse"><span class="ident">border</span><span>?</span><span>: </span><a class="type-intrinsic">string</a></span>
<div class="details collapse" id="details-305">
<div class="comment"><p>CSS styling property that will be applied to text enclosed by a decoration.</p>
</div>
</div>



<a name="DecorationRenderOptions.borderColor"></a><span class="ts" id=306 data-target="#details-306" data-toggle="collapse"><span class="ident">borderColor</span><span>?</span><span>: </span><a class="type-intrinsic">string</a> &#124; <a class="type-ref" href="#ThemeColor">ThemeColor</a></span>
<div class="details collapse" id="details-306">
<div class="comment"><p>CSS styling property that will be applied to text enclosed by a decoration.
Better use &#39;border&#39; for setting one or more of the individual border properties.</p>
</div>
</div>



<a name="DecorationRenderOptions.borderRadius"></a><span class="ts" id=307 data-target="#details-307" data-toggle="collapse"><span class="ident">borderRadius</span><span>?</span><span>: </span><a class="type-intrinsic">string</a></span>
<div class="details collapse" id="details-307">
<div class="comment"><p>CSS styling property that will be applied to text enclosed by a decoration.
Better use &#39;border&#39; for setting one or more of the individual border properties.</p>
</div>
</div>



<a name="DecorationRenderOptions.borderSpacing"></a><span class="ts" id=308 data-target="#details-308" data-toggle="collapse"><span class="ident">borderSpacing</span><span>?</span><span>: </span><a class="type-intrinsic">string</a></span>
<div class="details collapse" id="details-308">
<div class="comment"><p>CSS styling property that will be applied to text enclosed by a decoration.
Better use &#39;border&#39; for setting one or more of the individual border properties.</p>
</div>
</div>



<a name="DecorationRenderOptions.borderStyle"></a><span class="ts" id=309 data-target="#details-309" data-toggle="collapse"><span class="ident">borderStyle</span><span>?</span><span>: </span><a class="type-intrinsic">string</a></span>
<div class="details collapse" id="details-309">
<div class="comment"><p>CSS styling property that will be applied to text enclosed by a decoration.
Better use &#39;border&#39; for setting one or more of the individual border properties.</p>
</div>
</div>



<a name="DecorationRenderOptions.borderWidth"></a><span class="ts" id=310 data-target="#details-310" data-toggle="collapse"><span class="ident">borderWidth</span><span>?</span><span>: </span><a class="type-intrinsic">string</a></span>
<div class="details collapse" id="details-310">
<div class="comment"><p>CSS styling property that will be applied to text enclosed by a decoration.
Better use &#39;border&#39; for setting one or more of the individual border properties.</p>
</div>
</div>



<a name="DecorationRenderOptions.color"></a><span class="ts" id=315 data-target="#details-315" data-toggle="collapse"><span class="ident">color</span><span>?</span><span>: </span><a class="type-intrinsic">string</a> &#124; <a class="type-ref" href="#ThemeColor">ThemeColor</a></span>
<div class="details collapse" id="details-315">
<div class="comment"><p>CSS styling property that will be applied to text enclosed by a decoration.</p>
</div>
</div>



<a name="DecorationRenderOptions.cursor"></a><span class="ts" id=314 data-target="#details-314" data-toggle="collapse"><span class="ident">cursor</span><span>?</span><span>: </span><a class="type-intrinsic">string</a></span>
<div class="details collapse" id="details-314">
<div class="comment"><p>CSS styling property that will be applied to text enclosed by a decoration.</p>
</div>
</div>



<a name="DecorationRenderOptions.dark"></a><span class="ts" id=299 data-target="#details-299" data-toggle="collapse"><span class="ident">dark</span><span>?</span><span>: </span><a class="type-ref" href="#ThemableDecorationRenderOptions">ThemableDecorationRenderOptions</a></span>
<div class="details collapse" id="details-299">
<div class="comment"><p>Overwrite options for dark themes.</p>
</div>
</div>



<a name="DecorationRenderOptions.fontStyle"></a><span class="ts" id=311 data-target="#details-311" data-toggle="collapse"><span class="ident">fontStyle</span><span>?</span><span>: </span><a class="type-intrinsic">string</a></span>
<div class="details collapse" id="details-311">
<div class="comment"><p>CSS styling property that will be applied to text enclosed by a decoration.</p>
</div>
</div>



<a name="DecorationRenderOptions.fontWeight"></a><span class="ts" id=312 data-target="#details-312" data-toggle="collapse"><span class="ident">fontWeight</span><span>?</span><span>: </span><a class="type-intrinsic">string</a></span>
<div class="details collapse" id="details-312">
<div class="comment"><p>CSS styling property that will be applied to text enclosed by a decoration.</p>
</div>
</div>



<a name="DecorationRenderOptions.gutterIconPath"></a><span class="ts" id=318 data-target="#details-318" data-toggle="collapse"><span class="ident">gutterIconPath</span><span>?</span><span>: </span><a class="type-intrinsic">string</a> &#124; <a class="type-ref" href="#Uri">Uri</a></span>
<div class="details collapse" id="details-318">
<div class="comment"><p>An <strong>absolute path</strong> or an URI to an image to be rendered in the gutter.</p>
</div>
</div>



<a name="DecorationRenderOptions.gutterIconSize"></a><span class="ts" id=319 data-target="#details-319" data-toggle="collapse"><span class="ident">gutterIconSize</span><span>?</span><span>: </span><a class="type-intrinsic">string</a></span>
<div class="details collapse" id="details-319">
<div class="comment"><p>Specifies the size of the gutter icon.
Available values are &#39;auto&#39;, &#39;contain&#39;, &#39;cover&#39; and any percentage value.
For further information: <a href="https://msdn.microsoft.com/en-us/library/jj127316(v=vs.85).aspx">https://msdn.microsoft.com/en-us/library/jj127316(v=vs.85).aspx</a></p>
</div>
</div>



<a name="DecorationRenderOptions.isWholeLine"></a><span class="ts" id=295 data-target="#details-295" data-toggle="collapse"><span class="ident">isWholeLine</span><span>?</span><span>: </span><a class="type-intrinsic">boolean</a></span>
<div class="details collapse" id="details-295">
<div class="comment"><p>Should the decoration be rendered also on the whitespace after the line text.
Defaults to <code>false</code>.</p>
</div>
</div>



<a name="DecorationRenderOptions.letterSpacing"></a><span class="ts" id=317 data-target="#details-317" data-toggle="collapse"><span class="ident">letterSpacing</span><span>?</span><span>: </span><a class="type-intrinsic">string</a></span>
<div class="details collapse" id="details-317">
<div class="comment"><p>CSS styling property that will be applied to text enclosed by a decoration.</p>
</div>
</div>



<a name="DecorationRenderOptions.light"></a><span class="ts" id=298 data-target="#details-298" data-toggle="collapse"><span class="ident">light</span><span>?</span><span>: </span><a class="type-ref" href="#ThemableDecorationRenderOptions">ThemableDecorationRenderOptions</a></span>
<div class="details collapse" id="details-298">
<div class="comment"><p>Overwrite options for light themes.</p>
</div>
</div>



<a name="DecorationRenderOptions.opacity"></a><span class="ts" id=316 data-target="#details-316" data-toggle="collapse"><span class="ident">opacity</span><span>?</span><span>: </span><a class="type-intrinsic">string</a></span>
<div class="details collapse" id="details-316">
<div class="comment"><p>CSS styling property that will be applied to text enclosed by a decoration.</p>
</div>
</div>



<a name="DecorationRenderOptions.outline"></a><span class="ts" id=301 data-target="#details-301" data-toggle="collapse"><span class="ident">outline</span><span>?</span><span>: </span><a class="type-intrinsic">string</a></span>
<div class="details collapse" id="details-301">
<div class="comment"><p>CSS styling property that will be applied to text enclosed by a decoration.</p>
</div>
</div>



<a name="DecorationRenderOptions.outlineColor"></a><span class="ts" id=302 data-target="#details-302" data-toggle="collapse"><span class="ident">outlineColor</span><span>?</span><span>: </span><a class="type-intrinsic">string</a> &#124; <a class="type-ref" href="#ThemeColor">ThemeColor</a></span>
<div class="details collapse" id="details-302">
<div class="comment"><p>CSS styling property that will be applied to text enclosed by a decoration.
Better use &#39;outline&#39; for setting one or more of the individual outline properties.</p>
</div>
</div>



<a name="DecorationRenderOptions.outlineStyle"></a><span class="ts" id=303 data-target="#details-303" data-toggle="collapse"><span class="ident">outlineStyle</span><span>?</span><span>: </span><a class="type-intrinsic">string</a></span>
<div class="details collapse" id="details-303">
<div class="comment"><p>CSS styling property that will be applied to text enclosed by a decoration.
Better use &#39;outline&#39; for setting one or more of the individual outline properties.</p>
</div>
</div>



<a name="DecorationRenderOptions.outlineWidth"></a><span class="ts" id=304 data-target="#details-304" data-toggle="collapse"><span class="ident">outlineWidth</span><span>?</span><span>: </span><a class="type-intrinsic">string</a></span>
<div class="details collapse" id="details-304">
<div class="comment"><p>CSS styling property that will be applied to text enclosed by a decoration.
Better use &#39;outline&#39; for setting one or more of the individual outline properties.</p>
</div>
</div>



<a name="DecorationRenderOptions.overviewRulerColor"></a><span class="ts" id=320 data-target="#details-320" data-toggle="collapse"><span class="ident">overviewRulerColor</span><span>?</span><span>: </span><a class="type-intrinsic">string</a> &#124; <a class="type-ref" href="#ThemeColor">ThemeColor</a></span>
<div class="details collapse" id="details-320">
<div class="comment"><p>The color of the decoration in the overview ruler. Use rgba() and define transparent colors to play well with other decorations.</p>
</div>
</div>



<a name="DecorationRenderOptions.overviewRulerLane"></a><span class="ts" id=297 data-target="#details-297" data-toggle="collapse"><span class="ident">overviewRulerLane</span><span>?</span><span>: </span><a class="type-ref" href="#OverviewRulerLane">OverviewRulerLane</a></span>
<div class="details collapse" id="details-297">
<div class="comment"><p>The position in the overview ruler where the decoration should be rendered.</p>
</div>
</div>



<a name="DecorationRenderOptions.rangeBehavior"></a><span class="ts" id=296 data-target="#details-296" data-toggle="collapse"><span class="ident">rangeBehavior</span><span>?</span><span>: </span><a class="type-ref" href="#DecorationRangeBehavior">DecorationRangeBehavior</a></span>
<div class="details collapse" id="details-296">
<div class="comment"><p>Customize the growing behavior of the decoration when edits occur at the edges of the decoration&#39;s range.
Defaults to <code>DecorationRangeBehavior.OpenOpen</code>.</p>
</div>
</div>



<a name="DecorationRenderOptions.textDecoration"></a><span class="ts" id=313 data-target="#details-313" data-toggle="collapse"><span class="ident">textDecoration</span><span>?</span><span>: </span><a class="type-intrinsic">string</a></span>
<div class="details collapse" id="details-313">
<div class="comment"><p>CSS styling property that will be applied to text enclosed by a decoration.</p>
</div>
</div>

### <a name="Definition"></a><span class="code-item" id=1993>Definition</span>



<div class="comment"><p>The definition of a symbol represented as one or many <a href="#Location">locations</a>.
For most programming languages there is only one location at which a symbol is
defined.</p>
</div>



<a name="Definition"></a><span class="ts" id=1993 data-target="#details-1993" data-toggle="collapse"><span class="ident">Definition</span><span>: </span><a class="type-ref" href="#Location">Location</a> &#124; <a class="type-ref" href="#Location">Location</a>[]</span>

### <a name="DefinitionLink"></a><span class="code-item" id=1992>DefinitionLink</span>



<div class="comment"><p>Information about where a symbol is defined.</p>
<p>Provides additional metadata over normal <a href="#Location">location</a> definitions, including the range of
the defining symbol</p>
</div>



<a name="DefinitionLink"></a><span class="ts" id=1992 data-target="#details-1992" data-toggle="collapse"><span class="ident">DefinitionLink</span><span>: </span><a class="type-ref" href="#LocationLink">LocationLink</a></span>

### <a name="DefinitionProvider"></a><span class="code-item" id=609>DefinitionProvider</span>



<div class="comment"><p>The definition provider interface defines the contract between extensions and
the <a href="https://code.visualstudio.com/docs/editor/editingevolved#_go-to-definition">go to definition</a>
and peek definition features.</p>
</div>

#### Methods



<a name="DefinitionProvider.provideDefinition"></a><span class="ts" id=611 data-target="#details-611" data-toggle="collapse"><span class="ident">provideDefinition</span><span>(</span><span class="ident">document</span><span>: </span><a class="type-ref" href="#TextDocument">TextDocument</a>, <span class="ident">position</span><span>: </span><a class="type-ref" href="#Position">Position</a>, <span class="ident">token</span><span>: </span><a class="type-ref" href="#CancellationToken">CancellationToken</a><span>)</span><span>: </span><a class="type-ref" href="#ProviderResult">ProviderResult</a>&lt;<a class="type-ref" href="#Definition">Definition</a> &#124; <a class="type-ref" href="#DefinitionLink">DefinitionLink</a>[]&gt;</span>
<div class="details collapse" id="details-611">
<div class="comment"><p>Provide the definition of the symbol at the given position and document.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="document"></a><span class="ts" id=612 data-target="#details-612" data-toggle="collapse"><span class="ident">document</span><span>: </span><a class="type-ref" href="#TextDocument">TextDocument</a></span></td><td><div class="comment"><p>The document in which the command was invoked.</p>
</div></td></tr>
<tr><td><a name="position"></a><span class="ts" id=613 data-target="#details-613" data-toggle="collapse"><span class="ident">position</span><span>: </span><a class="type-ref" href="#Position">Position</a></span></td><td><div class="comment"><p>The position at which the command was invoked.</p>
</div></td></tr>
<tr><td><a name="token"></a><span class="ts" id=614 data-target="#details-614" data-toggle="collapse"><span class="ident">token</span><span>: </span><a class="type-ref" href="#CancellationToken">CancellationToken</a></span></td><td><div class="comment"><p>A cancellation token.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#ProviderResult">ProviderResult</a>&lt;<a class="type-ref" href="#Definition">Definition</a> &#124; <a class="type-ref" href="#DefinitionLink">DefinitionLink</a>[]&gt;</span></td><td><div class="comment"><p>A definition or a thenable that resolves to such. The lack of a result can be
signaled by returning <code>undefined</code> or <code>null</code>.</p>
</div></td></tr>
</table>
</div>
</div>

### <a name="Diagnostic"></a><span class="code-item" id=1185>Diagnostic</span>



<div class="comment"><p>Represents a diagnostic, such as a compiler error or warning. Diagnostic objects
are only valid in the scope of a file.</p>
</div>

#### Constructors



<a name="Diagnostic.new Diagnostic"></a><span class="ts" id=1194 data-target="#details-1194" data-toggle="collapse"><span class="ident">new Diagnostic</span><span>(</span><span class="ident">range</span><span>: </span><a class="type-ref" href="#Range">Range</a>, <span class="ident">message</span><span>: </span><a class="type-intrinsic">string</a>, <span class="ident">severity</span><span>?</span><span>: </span><a class="type-ref" href="#DiagnosticSeverity">DiagnosticSeverity</a><span>)</span><span>: </span><a class="type-ref" href="#Diagnostic">Diagnostic</a></span>
<div class="details collapse" id="details-1194">
<div class="comment"><p>Creates a new diagnostic object.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="range"></a><span class="ts" id=1195 data-target="#details-1195" data-toggle="collapse"><span class="ident">range</span><span>: </span><a class="type-ref" href="#Range">Range</a></span></td><td><div class="comment"><p>The range to which this diagnostic applies.</p>
</div></td></tr>
<tr><td><a name="message"></a><span class="ts" id=1196 data-target="#details-1196" data-toggle="collapse"><span class="ident">message</span><span>: </span><a class="type-intrinsic">string</a></span></td><td><div class="comment"><p>The human-readable message.</p>
</div></td></tr>
<tr><td><a name="severity"></a><span class="ts" id=1197 data-target="#details-1197" data-toggle="collapse"><span class="ident">severity</span><span>?</span><span>: </span><a class="type-ref" href="#DiagnosticSeverity">DiagnosticSeverity</a></span></td><td><div class="comment"><p>The severity, default is <a href="#DiagnosticSeverity.Error">error</a>.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#Diagnostic">Diagnostic</a></span></td><td><div class="comment"></div></td></tr>
</table>
</div>
</div>

#### Properties



<a name="Diagnostic.code"></a><span class="ts" id=1190 data-target="#details-1190" data-toggle="collapse"><span class="ident">code</span><span>?</span><span>: </span><a class="type-intrinsic">string</a> &#124; <a class="type-intrinsic">number</a></span>
<div class="details collapse" id="details-1190">
<div class="comment"><p>A code or identifier for this diagnostic.
Should be used for later processing, e.g. when providing <a href="#CodeActionContext">code actions</a>.</p>
</div>
</div>



<a name="Diagnostic.message"></a><span class="ts" id=1187 data-target="#details-1187" data-toggle="collapse"><span class="ident">message</span><span>: </span><a class="type-intrinsic">string</a></span>
<div class="details collapse" id="details-1187">
<div class="comment"><p>The human-readable message.</p>
</div>
</div>



<a name="Diagnostic.range"></a><span class="ts" id=1186 data-target="#details-1186" data-toggle="collapse"><span class="ident">range</span><span>: </span><a class="type-ref" href="#Range">Range</a></span>
<div class="details collapse" id="details-1186">
<div class="comment"><p>The range to which this diagnostic applies.</p>
</div>
</div>



<a name="Diagnostic.relatedInformation"></a><span class="ts" id=1191 data-target="#details-1191" data-toggle="collapse"><span class="ident">relatedInformation</span><span>?</span><span>: </span><a class="type-ref" href="#DiagnosticRelatedInformation">DiagnosticRelatedInformation</a>[]</span>
<div class="details collapse" id="details-1191">
<div class="comment"><p>An array of related diagnostic information, e.g. when symbol-names within
a scope collide all definitions can be marked via this property.</p>
</div>
</div>



<a name="Diagnostic.severity"></a><span class="ts" id=1188 data-target="#details-1188" data-toggle="collapse"><span class="ident">severity</span><span>: </span><a class="type-ref" href="#DiagnosticSeverity">DiagnosticSeverity</a></span>
<div class="details collapse" id="details-1188">
<div class="comment"><p>The severity, default is <a href="#DiagnosticSeverity.Error">error</a>.</p>
</div>
</div>



<a name="Diagnostic.source"></a><span class="ts" id=1189 data-target="#details-1189" data-toggle="collapse"><span class="ident">source</span><span>?</span><span>: </span><a class="type-intrinsic">string</a></span>
<div class="details collapse" id="details-1189">
<div class="comment"><p>A human-readable string describing the source of this
diagnostic, e.g. &#39;typescript&#39; or &#39;super lint&#39;.</p>
</div>
</div>



<a name="Diagnostic.tags"></a><span class="ts" id=1192 data-target="#details-1192" data-toggle="collapse"><span class="ident">tags</span><span>?</span><span>: </span><a class="type-ref" href="#DiagnosticTag">DiagnosticTag</a>[]</span>
<div class="details collapse" id="details-1192">
<div class="comment"><p>Additional metadata about the diagnostic.</p>
</div>
</div>

### <a name="DiagnosticChangeEvent"></a><span class="code-item" id=1169>DiagnosticChangeEvent</span>



<div class="comment"><p>The event that is fired when diagnostics change.</p>
</div>

#### Properties



<a name="DiagnosticChangeEvent.uris"></a><span class="ts" id=1170 data-target="#details-1170" data-toggle="collapse"><span class="ident">uris</span><span>: </span><a class="type-ref" href="#Uri">Uri</a>[]</span>
<div class="details collapse" id="details-1170">
<div class="comment"><p>An array of resources for which diagnostics have changed.</p>
</div>
</div>

### <a name="DiagnosticCollection"></a><span class="code-item" id=1198>DiagnosticCollection</span>



<div class="comment"><p>A diagnostics collection is a container that manages a set of
<a href="#Diagnostic">diagnostics</a>. Diagnostics are always scopes to a
diagnostics collection and a resource.</p>
<p>To get an instance of a <code>DiagnosticCollection</code> use
<a href="#languages.createDiagnosticCollection">createDiagnosticCollection</a>.</p>
</div>

#### Properties



<a name="DiagnosticCollection.name"></a><span class="ts" id=1199 data-target="#details-1199" data-toggle="collapse"><span class="ident">name</span><span>: </span><a class="type-intrinsic">string</a></span>
<div class="details collapse" id="details-1199">
<div class="comment"><p>The name of this diagnostic collection, for instance <code>typescript</code>. Every diagnostic
from this collection will be associated with this name. Also, the task framework uses this
name when defining <a href="https://code.visualstudio.com/docs/editor/tasks#_defining-a-problem-matcher">problem matchers</a>.</p>
</div>
</div>

#### Methods



<a name="DiagnosticCollection.clear"></a><span class="ts" id=1210 data-target="#details-1210" data-toggle="collapse"><span class="ident">clear</span><span>(</span><span>)</span><span>: </span><a class="type-intrinsic">void</a></span>
<div class="details collapse" id="details-1210">
<div class="comment"><p>Remove all diagnostics from this collection. The same
as calling <code>#set(undefined)</code>;</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-intrinsic">void</a></span></td><td><div class="comment"></div></td></tr>
</table>
</div>
</div>



<a name="DiagnosticCollection.delete"></a><span class="ts" id=1207 data-target="#details-1207" data-toggle="collapse"><span class="ident">delete</span><span>(</span><span class="ident">uri</span><span>: </span><a class="type-ref" href="#Uri">Uri</a><span>)</span><span>: </span><a class="type-intrinsic">void</a></span>
<div class="details collapse" id="details-1207">
<div class="comment"><p>Remove all diagnostics from this collection that belong
to the provided <code>uri</code>. The same as <code>#set(uri, undefined)</code>.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="uri"></a><span class="ts" id=1208 data-target="#details-1208" data-toggle="collapse"><span class="ident">uri</span><span>: </span><a class="type-ref" href="#Uri">Uri</a></span></td><td><div class="comment"><p>A resource identifier.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-intrinsic">void</a></span></td><td><div class="comment"></div></td></tr>
</table>
</div>
</div>



<a name="DiagnosticCollection.dispose"></a><span class="ts" id=1227 data-target="#details-1227" data-toggle="collapse"><span class="ident">dispose</span><span>(</span><span>)</span><span>: </span><a class="type-intrinsic">void</a></span>
<div class="details collapse" id="details-1227">
<div class="comment"><p>Dispose and free associated resources. Calls
<a href="#DiagnosticCollection.clear">clear</a>.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-intrinsic">void</a></span></td><td><div class="comment"></div></td></tr>
</table>
</div>
</div>



<a name="DiagnosticCollection.forEach"></a><span class="ts" id=1212 data-target="#details-1212" data-toggle="collapse"><span class="ident">forEach</span><span>(</span><span class="ident">callback</span><span>: </span>(uri: <a class="type-ref" href="#Uri">Uri</a>, diagnostics: <a class="type-ref" href="#Diagnostic">Diagnostic</a>[], collection: <a class="type-ref" href="#DiagnosticCollection">DiagnosticCollection</a>) =&gt; <a class="type-intrinsic">any</a>, <span class="ident">thisArg</span><span>?</span><span>: </span><a class="type-intrinsic">any</a><span>)</span><span>: </span><a class="type-intrinsic">void</a></span>
<div class="details collapse" id="details-1212">
<div class="comment"><p>Iterate over each entry in this collection.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="callback"></a><span class="ts" id=1213 data-target="#details-1213" data-toggle="collapse"><span class="ident">callback</span><span>: </span>(uri: <a class="type-ref" href="#Uri">Uri</a>, diagnostics: <a class="type-ref" href="#Diagnostic">Diagnostic</a>[], collection: <a class="type-ref" href="#DiagnosticCollection">DiagnosticCollection</a>) =&gt; <a class="type-intrinsic">any</a></span></td><td><div class="comment"><p>Function to execute for each entry.</p>
</div></td></tr>
<tr><td><a name="thisArg"></a><span class="ts" id=1219 data-target="#details-1219" data-toggle="collapse"><span class="ident">thisArg</span><span>?</span><span>: </span><a class="type-intrinsic">any</a></span></td><td><div class="comment"><p>The <code>this</code> context used when invoking the handler function.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-intrinsic">void</a></span></td><td><div class="comment"></div></td></tr>
</table>
</div>
</div>



<a name="DiagnosticCollection.get"></a><span class="ts" id=1221 data-target="#details-1221" data-toggle="collapse"><span class="ident">get</span><span>(</span><span class="ident">uri</span><span>: </span><a class="type-ref" href="#Uri">Uri</a><span>)</span><span>: </span><a class="type-ref" href="#Diagnostic">Diagnostic</a>[] &#124; <a class="type-intrinsic">undefined</a></span>
<div class="details collapse" id="details-1221">
<div class="comment"><p>Get the diagnostics for a given resource. <em>Note</em> that you cannot
modify the diagnostics-array returned from this call.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="uri"></a><span class="ts" id=1222 data-target="#details-1222" data-toggle="collapse"><span class="ident">uri</span><span>: </span><a class="type-ref" href="#Uri">Uri</a></span></td><td><div class="comment"><p>A resource identifier.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#Diagnostic">Diagnostic</a>[] &#124; <a class="type-intrinsic">undefined</a></span></td><td><div class="comment"><p>An immutable array of <a href="#Diagnostic">diagnostics</a> or <code>undefined</code>.</p>
</div></td></tr>
</table>
</div>
</div>



<a name="DiagnosticCollection.has"></a><span class="ts" id=1224 data-target="#details-1224" data-toggle="collapse"><span class="ident">has</span><span>(</span><span class="ident">uri</span><span>: </span><a class="type-ref" href="#Uri">Uri</a><span>)</span><span>: </span><a class="type-intrinsic">boolean</a></span>
<div class="details collapse" id="details-1224">
<div class="comment"><p>Check if this collection contains diagnostics for a
given resource.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="uri"></a><span class="ts" id=1225 data-target="#details-1225" data-toggle="collapse"><span class="ident">uri</span><span>: </span><a class="type-ref" href="#Uri">Uri</a></span></td><td><div class="comment"><p>A resource identifier.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-intrinsic">boolean</a></span></td><td><div class="comment"><p><code>true</code> if this collection has diagnostic for the given resource.</p>
</div></td></tr>
</table>
</div>
</div>



<a name="DiagnosticCollection.set"></a><span class="ts" id=1201 data-target="#details-1201" data-toggle="collapse"><span class="ident">set</span><span>(</span><span class="ident">uri</span><span>: </span><a class="type-ref" href="#Uri">Uri</a>, <span class="ident">diagnostics</span><span>: </span><a class="type-ref" href="#Diagnostic">Diagnostic</a>[] &#124; <a class="type-intrinsic">undefined</a><span>)</span><span>: </span><a class="type-intrinsic">void</a></span>
<div class="details collapse" id="details-1201">
<div class="comment"><p>Assign diagnostics for given resource. Will replace
existing diagnostics for that resource.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="uri"></a><span class="ts" id=1202 data-target="#details-1202" data-toggle="collapse"><span class="ident">uri</span><span>: </span><a class="type-ref" href="#Uri">Uri</a></span></td><td><div class="comment"><p>A resource identifier.</p>
</div></td></tr>
<tr><td><a name="diagnostics"></a><span class="ts" id=1203 data-target="#details-1203" data-toggle="collapse"><span class="ident">diagnostics</span><span>: </span><a class="type-ref" href="#Diagnostic">Diagnostic</a>[] &#124; <a class="type-intrinsic">undefined</a></span></td><td><div class="comment"><p>Array of diagnostics or <code>undefined</code></p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-intrinsic">void</a></span></td><td><div class="comment"></div></td></tr>
</table>
</div>
</div>



<a name="DiagnosticCollection.set"></a><span class="ts" id=1204 data-target="#details-1204" data-toggle="collapse"><span class="ident">set</span><span>(</span><span class="ident">entries</span><span>: </span>[<a class="type-ref" href="#Uri">Uri</a>, <a class="type-ref" href="#Diagnostic">Diagnostic</a>[] &#124; <a class="type-intrinsic">undefined</a>][]<span>)</span><span>: </span><a class="type-intrinsic">void</a></span>
<div class="details collapse" id="details-1204">
<div class="comment"><p>Replace all entries in this collection.</p>
<p>Diagnostics of multiple tuples of the same uri will be merged, e.g
<code>[[file1, [d1]], [file1, [d2]]]</code> is equivalent to <code>[[file1, [d1, d2]]]</code>.
If a diagnostics item is <code>undefined</code> as in <code>[file1, undefined]</code>
all previous but not subsequent diagnostics are removed.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="entries"></a><span class="ts" id=1205 data-target="#details-1205" data-toggle="collapse"><span class="ident">entries</span><span>: </span>[<a class="type-ref" href="#Uri">Uri</a>, <a class="type-ref" href="#Diagnostic">Diagnostic</a>[] &#124; <a class="type-intrinsic">undefined</a>][]</span></td><td><div class="comment"><p>An array of tuples, like <code>[[file1, [d1, d2]], [file2, [d3, d4, d5]]]</code>, or <code>undefined</code>.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-intrinsic">void</a></span></td><td><div class="comment"></div></td></tr>
</table>
</div>
</div>

### <a name="DiagnosticRelatedInformation"></a><span class="code-item" id=1176>DiagnosticRelatedInformation</span>



<div class="comment"><p>Represents a related message and source code location for a diagnostic. This should be
used to point to code locations that cause or related to a diagnostics, e.g when duplicating
a symbol in a scope.</p>
</div>

#### Constructors



<a name="DiagnosticRelatedInformation.new DiagnosticRelatedInformation"></a><span class="ts" id=1180 data-target="#details-1180" data-toggle="collapse"><span class="ident">new DiagnosticRelatedInformation</span><span>(</span><span class="ident">location</span><span>: </span><a class="type-ref" href="#Location">Location</a>, <span class="ident">message</span><span>: </span><a class="type-intrinsic">string</a><span>)</span><span>: </span><a class="type-ref" href="#DiagnosticRelatedInformation">DiagnosticRelatedInformation</a></span>
<div class="details collapse" id="details-1180">
<div class="comment"><p>Creates a new related diagnostic information object.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="location"></a><span class="ts" id=1181 data-target="#details-1181" data-toggle="collapse"><span class="ident">location</span><span>: </span><a class="type-ref" href="#Location">Location</a></span></td><td><div class="comment"><p>The location.</p>
</div></td></tr>
<tr><td><a name="message"></a><span class="ts" id=1182 data-target="#details-1182" data-toggle="collapse"><span class="ident">message</span><span>: </span><a class="type-intrinsic">string</a></span></td><td><div class="comment"><p>The message.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#DiagnosticRelatedInformation">DiagnosticRelatedInformation</a></span></td><td><div class="comment"></div></td></tr>
</table>
</div>
</div>

#### Properties



<a name="DiagnosticRelatedInformation.location"></a><span class="ts" id=1177 data-target="#details-1177" data-toggle="collapse"><span class="ident">location</span><span>: </span><a class="type-ref" href="#Location">Location</a></span>
<div class="details collapse" id="details-1177">
<div class="comment"><p>The location of this related diagnostic information.</p>
</div>
</div>



<a name="DiagnosticRelatedInformation.message"></a><span class="ts" id=1178 data-target="#details-1178" data-toggle="collapse"><span class="ident">message</span><span>: </span><a class="type-intrinsic">string</a></span>
<div class="details collapse" id="details-1178">
<div class="comment"><p>The message of this related diagnostic information.</p>
</div>
</div>

### <a name="DiagnosticSeverity"></a><span class="code-item" id=1171>DiagnosticSeverity</span>



<div class="comment"><p>Represents the severity of diagnostics.</p>
</div>

#### Enumeration members



<a name="DiagnosticSeverity.Error"></a><span class="ts" id=1172 data-target="#details-1172" data-toggle="collapse"><span class="ident">Error</span></span>
<div class="details collapse" id="details-1172">
<em>0</em>
</div>



<a name="DiagnosticSeverity.Hint"></a><span class="ts" id=1175 data-target="#details-1175" data-toggle="collapse"><span class="ident">Hint</span></span>
<div class="details collapse" id="details-1175">
<em>3</em>
</div>



<a name="DiagnosticSeverity.Information"></a><span class="ts" id=1174 data-target="#details-1174" data-toggle="collapse"><span class="ident">Information</span></span>
<div class="details collapse" id="details-1174">
<em>2</em>
</div>



<a name="DiagnosticSeverity.Warning"></a><span class="ts" id=1173 data-target="#details-1173" data-toggle="collapse"><span class="ident">Warning</span></span>
<div class="details collapse" id="details-1173">
<em>1</em>
</div>

### <a name="DiagnosticTag"></a><span class="code-item" id=1183>DiagnosticTag</span>



<div class="comment"><p>Additional metadata about the type of a diagnostic.</p>
</div>

#### Enumeration members



<a name="DiagnosticTag.Unnecessary"></a><span class="ts" id=1184 data-target="#details-1184" data-toggle="collapse"><span class="ident">Unnecessary</span></span>
<div class="details collapse" id="details-1184">
<em>1</em>
</div>

### <a name="Disposable"></a><span class="code-item" id=434>Disposable</span>



<div class="comment"><p>Represents a type which can release resources, such
as event listening or a timer.</p>
</div>

#### Static



<a name="Disposable.from"></a><span class="ts" id=436 data-target="#details-436" data-toggle="collapse"><span class="ident">from</span><span>(</span><span>...</span><span class="ident">disposableLikes</span><span>: </span>{dispose: () =&gt; <a class="type-intrinsic">any</a>}[]<span>)</span><span>: </span><a class="type-ref" href="#Disposable">Disposable</a></span>
<div class="details collapse" id="details-436">
<div class="comment"><p>Combine many disposable-likes into one. Use this method
when having objects with a dispose function which are not
instances of Disposable.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="disposableLikes"></a><span class="ts" id=437 data-target="#details-437" data-toggle="collapse"><span>...</span><span class="ident">disposableLikes</span><span>: </span>{dispose: () =&gt; <a class="type-intrinsic">any</a>}[]</span></td><td><div class="comment"><p>Objects that have at least a <code>dispose</code>-function member.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#Disposable">Disposable</a></span></td><td><div class="comment"><p>Returns a new disposable which, upon dispose, will
dispose all provided disposables.</p>
</div></td></tr>
</table>
</div>
</div>

#### Constructors



<a name="Disposable.new Disposable"></a><span class="ts" id=443 data-target="#details-443" data-toggle="collapse"><span class="ident">new Disposable</span><span>(</span><span class="ident">callOnDispose</span><span>: </span><a class="type-ref" href="#Function">Function</a><span>)</span><span>: </span><a class="type-ref" href="#Disposable">Disposable</a></span>
<div class="details collapse" id="details-443">
<div class="comment"><p>Creates a new Disposable calling the provided function
on dispose.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="callOnDispose"></a><span class="ts" id=444 data-target="#details-444" data-toggle="collapse"><span class="ident">callOnDispose</span><span>: </span><a class="type-ref" href="#Function">Function</a></span></td><td><div class="comment"><p>Function that disposes something.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#Disposable">Disposable</a></span></td><td><div class="comment"></div></td></tr>
</table>
</div>
</div>

#### Methods



<a name="Disposable.dispose"></a><span class="ts" id=446 data-target="#details-446" data-toggle="collapse"><span class="ident">dispose</span><span>(</span><span>)</span><span>: </span><a class="type-intrinsic">any</a></span>
<div class="details collapse" id="details-446">
<div class="comment"><p>Dispose this object.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-intrinsic">any</a></span></td><td><div class="comment"></div></td></tr>
</table>
</div>
</div>

### <a name="DocumentColorProvider"></a><span class="code-item" id=1048>DocumentColorProvider</span>



<div class="comment"><p>The document color provider defines the contract between extensions and feature of
picking and modifying colors in the editor.</p>
</div>

#### Methods



<a name="DocumentColorProvider.provideColorPresentations"></a><span class="ts" id=1054 data-target="#details-1054" data-toggle="collapse"><span class="ident">provideColorPresentations</span><span>(</span><span class="ident">color</span><span>: </span><a class="type-ref" href="#Color">Color</a>, <span class="ident">context</span><span>: </span>{document: <a class="type-ref" href="#TextDocument">TextDocument</a>, range: <a class="type-ref" href="#Range">Range</a>}, <span class="ident">token</span><span>: </span><a class="type-ref" href="#CancellationToken">CancellationToken</a><span>)</span><span>: </span><a class="type-ref" href="#ProviderResult">ProviderResult</a>&lt;<a class="type-ref" href="#ColorPresentation">ColorPresentation</a>[]&gt;</span>
<div class="details collapse" id="details-1054">
<div class="comment"><p>Provide <a href="#ColorPresentation">representations</a> for a color.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="color"></a><span class="ts" id=1055 data-target="#details-1055" data-toggle="collapse"><span class="ident">color</span><span>: </span><a class="type-ref" href="#Color">Color</a></span></td><td><div class="comment"><p>The color to show and insert.</p>
</div></td></tr>
<tr><td><a name="context"></a><span class="ts" id=1056 data-target="#details-1056" data-toggle="collapse"><span class="ident">context</span><span>: </span>{document: <a class="type-ref" href="#TextDocument">TextDocument</a>, range: <a class="type-ref" href="#Range">Range</a>}</span></td><td><div class="comment"><p>A context object with additional information</p>
</div></td></tr>
<tr><td><a name="token"></a><span class="ts" id=1060 data-target="#details-1060" data-toggle="collapse"><span class="ident">token</span><span>: </span><a class="type-ref" href="#CancellationToken">CancellationToken</a></span></td><td><div class="comment"><p>A cancellation token.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#ProviderResult">ProviderResult</a>&lt;<a class="type-ref" href="#ColorPresentation">ColorPresentation</a>[]&gt;</span></td><td><div class="comment"><p>An array of color presentations or a thenable that resolves to such. The lack of a result
can be signaled by returning <code>undefined</code>, <code>null</code>, or an empty array.</p>
</div></td></tr>
</table>
</div>
</div>



<a name="DocumentColorProvider.provideDocumentColors"></a><span class="ts" id=1050 data-target="#details-1050" data-toggle="collapse"><span class="ident">provideDocumentColors</span><span>(</span><span class="ident">document</span><span>: </span><a class="type-ref" href="#TextDocument">TextDocument</a>, <span class="ident">token</span><span>: </span><a class="type-ref" href="#CancellationToken">CancellationToken</a><span>)</span><span>: </span><a class="type-ref" href="#ProviderResult">ProviderResult</a>&lt;<a class="type-ref" href="#ColorInformation">ColorInformation</a>[]&gt;</span>
<div class="details collapse" id="details-1050">
<div class="comment"><p>Provide colors for the given document.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="document"></a><span class="ts" id=1051 data-target="#details-1051" data-toggle="collapse"><span class="ident">document</span><span>: </span><a class="type-ref" href="#TextDocument">TextDocument</a></span></td><td><div class="comment"><p>The document in which the command was invoked.</p>
</div></td></tr>
<tr><td><a name="token"></a><span class="ts" id=1052 data-target="#details-1052" data-toggle="collapse"><span class="ident">token</span><span>: </span><a class="type-ref" href="#CancellationToken">CancellationToken</a></span></td><td><div class="comment"><p>A cancellation token.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#ProviderResult">ProviderResult</a>&lt;<a class="type-ref" href="#ColorInformation">ColorInformation</a>[]&gt;</span></td><td><div class="comment"><p>An array of <a href="#ColorInformation">color information</a> or a thenable that resolves to such. The lack of a result
can be signaled by returning <code>undefined</code>, <code>null</code>, or an empty array.</p>
</div></td></tr>
</table>
</div>
</div>

### <a name="DocumentFilter"></a><span class="code-item" id=546>DocumentFilter</span>



<div class="comment"><p>A document filter denotes a document by different properties like
the <a href="#TextDocument.languageId">language</a>, the <a href="#Uri.scheme">scheme</a> of
its resource, or a glob-pattern that is applied to the <a href="#TextDocument.fileName">path</a>.</p>
<ul>
<li><em>sample</em> - A language filter that applies to typescript files on disk: <code>{ language: &#39;typescript&#39;, scheme: &#39;file&#39; }</code></li>
</ul>
<ul>
<li><em>sample</em> - A language filter that applies to all package.json paths: <code>{ language: &#39;json&#39;, scheme: &#39;untitled&#39;, pattern: &#39;**/package.json&#39; }</code></li>
</ul>
</div>

#### Properties



<a name="DocumentFilter.language"></a><span class="ts" id=547 data-target="#details-547" data-toggle="collapse"><span class="ident">language</span><span>?</span><span>: </span><a class="type-intrinsic">string</a></span>
<div class="details collapse" id="details-547">
<div class="comment"><p>A language id, like <code>typescript</code>.</p>
</div>
</div>



<a name="DocumentFilter.pattern"></a><span class="ts" id=549 data-target="#details-549" data-toggle="collapse"><span class="ident">pattern</span><span>?</span><span>: </span><a class="type-ref" href="#GlobPattern">GlobPattern</a></span>
<div class="details collapse" id="details-549">
<div class="comment"><p>A <a href="#GlobPattern">glob pattern</a> that is matched on the absolute path of the document. Use a <a href="#RelativePattern">relative pattern</a>
to filter documents to a <a href="#WorkspaceFolder">workspace folder</a>.</p>
</div>
</div>



<a name="DocumentFilter.scheme"></a><span class="ts" id=548 data-target="#details-548" data-toggle="collapse"><span class="ident">scheme</span><span>?</span><span>: </span><a class="type-intrinsic">string</a></span>
<div class="details collapse" id="details-548">
<div class="comment"><p>A Uri <a href="#Uri.scheme">scheme</a>, like <code>file</code> or <code>untitled</code>.</p>
</div>
</div>

### <a name="DocumentFormattingEditProvider"></a><span class="code-item" id=879>DocumentFormattingEditProvider</span>



<div class="comment"><p>The document formatting provider interface defines the contract between extensions and
the formatting-feature.</p>
</div>

#### Methods



<a name="DocumentFormattingEditProvider.provideDocumentFormattingEdits"></a><span class="ts" id=881 data-target="#details-881" data-toggle="collapse"><span class="ident">provideDocumentFormattingEdits</span><span>(</span><span class="ident">document</span><span>: </span><a class="type-ref" href="#TextDocument">TextDocument</a>, <span class="ident">options</span><span>: </span><a class="type-ref" href="#FormattingOptions">FormattingOptions</a>, <span class="ident">token</span><span>: </span><a class="type-ref" href="#CancellationToken">CancellationToken</a><span>)</span><span>: </span><a class="type-ref" href="#ProviderResult">ProviderResult</a>&lt;<a class="type-ref" href="#TextEdit">TextEdit</a>[]&gt;</span>
<div class="details collapse" id="details-881">
<div class="comment"><p>Provide formatting edits for a whole document.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="document"></a><span class="ts" id=882 data-target="#details-882" data-toggle="collapse"><span class="ident">document</span><span>: </span><a class="type-ref" href="#TextDocument">TextDocument</a></span></td><td><div class="comment"><p>The document in which the command was invoked.</p>
</div></td></tr>
<tr><td><a name="options"></a><span class="ts" id=883 data-target="#details-883" data-toggle="collapse"><span class="ident">options</span><span>: </span><a class="type-ref" href="#FormattingOptions">FormattingOptions</a></span></td><td><div class="comment"><p>Options controlling formatting.</p>
</div></td></tr>
<tr><td><a name="token"></a><span class="ts" id=884 data-target="#details-884" data-toggle="collapse"><span class="ident">token</span><span>: </span><a class="type-ref" href="#CancellationToken">CancellationToken</a></span></td><td><div class="comment"><p>A cancellation token.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#ProviderResult">ProviderResult</a>&lt;<a class="type-ref" href="#TextEdit">TextEdit</a>[]&gt;</span></td><td><div class="comment"><p>A set of text edits or a thenable that resolves to such. The lack of a result can be
signaled by returning <code>undefined</code>, <code>null</code>, or an empty array.</p>
</div></td></tr>
</table>
</div>
</div>

### <a name="DocumentHighlight"></a><span class="code-item" id=666>DocumentHighlight</span>



<div class="comment"><p>A document highlight is a range inside a text document which deserves
special attention. Usually a document highlight is visualized by changing
the background color of its range.</p>
</div>

#### Constructors



<a name="DocumentHighlight.new DocumentHighlight"></a><span class="ts" id=670 data-target="#details-670" data-toggle="collapse"><span class="ident">new DocumentHighlight</span><span>(</span><span class="ident">range</span><span>: </span><a class="type-ref" href="#Range">Range</a>, <span class="ident">kind</span><span>?</span><span>: </span><a class="type-ref" href="#DocumentHighlightKind">DocumentHighlightKind</a><span>)</span><span>: </span><a class="type-ref" href="#DocumentHighlight">DocumentHighlight</a></span>
<div class="details collapse" id="details-670">
<div class="comment"><p>Creates a new document highlight object.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="range"></a><span class="ts" id=671 data-target="#details-671" data-toggle="collapse"><span class="ident">range</span><span>: </span><a class="type-ref" href="#Range">Range</a></span></td><td><div class="comment"><p>The range the highlight applies to.</p>
</div></td></tr>
<tr><td><a name="kind"></a><span class="ts" id=672 data-target="#details-672" data-toggle="collapse"><span class="ident">kind</span><span>?</span><span>: </span><a class="type-ref" href="#DocumentHighlightKind">DocumentHighlightKind</a></span></td><td><div class="comment"><p>The highlight kind, default is <a href="#DocumentHighlightKind.Text">text</a>.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#DocumentHighlight">DocumentHighlight</a></span></td><td><div class="comment"></div></td></tr>
</table>
</div>
</div>

#### Properties



<a name="DocumentHighlight.kind"></a><span class="ts" id=668 data-target="#details-668" data-toggle="collapse"><span class="ident">kind</span><span>?</span><span>: </span><a class="type-ref" href="#DocumentHighlightKind">DocumentHighlightKind</a></span>
<div class="details collapse" id="details-668">
<div class="comment"><p>The highlight kind, default is <a href="#DocumentHighlightKind.Text">text</a>.</p>
</div>
</div>



<a name="DocumentHighlight.range"></a><span class="ts" id=667 data-target="#details-667" data-toggle="collapse"><span class="ident">range</span><span>: </span><a class="type-ref" href="#Range">Range</a></span>
<div class="details collapse" id="details-667">
<div class="comment"><p>The range this highlight applies to.</p>
</div>
</div>

### <a name="DocumentHighlightKind"></a><span class="code-item" id=662>DocumentHighlightKind</span>



<div class="comment"><p>A document highlight kind.</p>
</div>

#### Enumeration members



<a name="DocumentHighlightKind.Read"></a><span class="ts" id=664 data-target="#details-664" data-toggle="collapse"><span class="ident">Read</span></span>
<div class="details collapse" id="details-664">
<em>1</em>
</div>



<a name="DocumentHighlightKind.Text"></a><span class="ts" id=663 data-target="#details-663" data-toggle="collapse"><span class="ident">Text</span></span>
<div class="details collapse" id="details-663">
<em>0</em>
</div>



<a name="DocumentHighlightKind.Write"></a><span class="ts" id=665 data-target="#details-665" data-toggle="collapse"><span class="ident">Write</span></span>
<div class="details collapse" id="details-665">
<em>2</em>
</div>

### <a name="DocumentHighlightProvider"></a><span class="code-item" id=673>DocumentHighlightProvider</span>



<div class="comment"><p>The document highlight provider interface defines the contract between extensions and
the word-highlight-feature.</p>
</div>

#### Methods



<a name="DocumentHighlightProvider.provideDocumentHighlights"></a><span class="ts" id=675 data-target="#details-675" data-toggle="collapse"><span class="ident">provideDocumentHighlights</span><span>(</span><span class="ident">document</span><span>: </span><a class="type-ref" href="#TextDocument">TextDocument</a>, <span class="ident">position</span><span>: </span><a class="type-ref" href="#Position">Position</a>, <span class="ident">token</span><span>: </span><a class="type-ref" href="#CancellationToken">CancellationToken</a><span>)</span><span>: </span><a class="type-ref" href="#ProviderResult">ProviderResult</a>&lt;<a class="type-ref" href="#DocumentHighlight">DocumentHighlight</a>[]&gt;</span>
<div class="details collapse" id="details-675">
<div class="comment"><p>Provide a set of document highlights, like all occurrences of a variable or
all exit-points of a function.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="document"></a><span class="ts" id=676 data-target="#details-676" data-toggle="collapse"><span class="ident">document</span><span>: </span><a class="type-ref" href="#TextDocument">TextDocument</a></span></td><td><div class="comment"><p>The document in which the command was invoked.</p>
</div></td></tr>
<tr><td><a name="position"></a><span class="ts" id=677 data-target="#details-677" data-toggle="collapse"><span class="ident">position</span><span>: </span><a class="type-ref" href="#Position">Position</a></span></td><td><div class="comment"><p>The position at which the command was invoked.</p>
</div></td></tr>
<tr><td><a name="token"></a><span class="ts" id=678 data-target="#details-678" data-toggle="collapse"><span class="ident">token</span><span>: </span><a class="type-ref" href="#CancellationToken">CancellationToken</a></span></td><td><div class="comment"><p>A cancellation token.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#ProviderResult">ProviderResult</a>&lt;<a class="type-ref" href="#DocumentHighlight">DocumentHighlight</a>[]&gt;</span></td><td><div class="comment"><p>An array of document highlights or a thenable that resolves to such. The lack of a result can be
signaled by returning <code>undefined</code>, <code>null</code>, or an empty array.</p>
</div></td></tr>
</table>
</div>
</div>

### <a name="DocumentLink"></a><span class="code-item" id=1007>DocumentLink</span>



<div class="comment"><p>A document link is a range in a text document that links to an internal or external resource, like another
text document or a web site.</p>
</div>

#### Constructors



<a name="DocumentLink.new DocumentLink"></a><span class="ts" id=1011 data-target="#details-1011" data-toggle="collapse"><span class="ident">new DocumentLink</span><span>(</span><span class="ident">range</span><span>: </span><a class="type-ref" href="#Range">Range</a>, <span class="ident">target</span><span>?</span><span>: </span><a class="type-ref" href="#Uri">Uri</a><span>)</span><span>: </span><a class="type-ref" href="#DocumentLink">DocumentLink</a></span>
<div class="details collapse" id="details-1011">
<div class="comment"><p>Creates a new document link.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="range"></a><span class="ts" id=1012 data-target="#details-1012" data-toggle="collapse"><span class="ident">range</span><span>: </span><a class="type-ref" href="#Range">Range</a></span></td><td><div class="comment"><p>The range the document link applies to. Must not be empty.</p>
</div></td></tr>
<tr><td><a name="target"></a><span class="ts" id=1013 data-target="#details-1013" data-toggle="collapse"><span class="ident">target</span><span>?</span><span>: </span><a class="type-ref" href="#Uri">Uri</a></span></td><td><div class="comment"><p>The uri the document link points to.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#DocumentLink">DocumentLink</a></span></td><td><div class="comment"></div></td></tr>
</table>
</div>
</div>

#### Properties



<a name="DocumentLink.range"></a><span class="ts" id=1008 data-target="#details-1008" data-toggle="collapse"><span class="ident">range</span><span>: </span><a class="type-ref" href="#Range">Range</a></span>
<div class="details collapse" id="details-1008">
<div class="comment"><p>The range this link applies to.</p>
</div>
</div>



<a name="DocumentLink.target"></a><span class="ts" id=1009 data-target="#details-1009" data-toggle="collapse"><span class="ident">target</span><span>?</span><span>: </span><a class="type-ref" href="#Uri">Uri</a></span>
<div class="details collapse" id="details-1009">
<div class="comment"><p>The uri this link points to.</p>
</div>
</div>

### <a name="DocumentLinkProvider"></a><span class="code-item" id=1014>DocumentLinkProvider</span>



<div class="comment"><p>The document link provider defines the contract between extensions and feature of showing
links in the editor.</p>
</div>

#### Methods



<a name="DocumentLinkProvider.provideDocumentLinks"></a><span class="ts" id=1016 data-target="#details-1016" data-toggle="collapse"><span class="ident">provideDocumentLinks</span><span>(</span><span class="ident">document</span><span>: </span><a class="type-ref" href="#TextDocument">TextDocument</a>, <span class="ident">token</span><span>: </span><a class="type-ref" href="#CancellationToken">CancellationToken</a><span>)</span><span>: </span><a class="type-ref" href="#ProviderResult">ProviderResult</a>&lt;<a class="type-ref" href="#DocumentLink">DocumentLink</a>[]&gt;</span>
<div class="details collapse" id="details-1016">
<div class="comment"><p>Provide links for the given document. Note that the editor ships with a default provider that detects
<code>http(s)</code> and <code>file</code> links.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="document"></a><span class="ts" id=1017 data-target="#details-1017" data-toggle="collapse"><span class="ident">document</span><span>: </span><a class="type-ref" href="#TextDocument">TextDocument</a></span></td><td><div class="comment"><p>The document in which the command was invoked.</p>
</div></td></tr>
<tr><td><a name="token"></a><span class="ts" id=1018 data-target="#details-1018" data-toggle="collapse"><span class="ident">token</span><span>: </span><a class="type-ref" href="#CancellationToken">CancellationToken</a></span></td><td><div class="comment"><p>A cancellation token.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#ProviderResult">ProviderResult</a>&lt;<a class="type-ref" href="#DocumentLink">DocumentLink</a>[]&gt;</span></td><td><div class="comment"><p>An array of <a href="#DocumentLink">document links</a> or a thenable that resolves to such. The lack of a result
can be signaled by returning <code>undefined</code>, <code>null</code>, or an empty array.</p>
</div></td></tr>
</table>
</div>
</div>



<a name="DocumentLinkProvider.resolveDocumentLink"></a><span class="ts" id=1020 data-target="#details-1020" data-toggle="collapse"><span class="ident">resolveDocumentLink</span><span>(</span><span class="ident">link</span><span>: </span><a class="type-ref" href="#DocumentLink">DocumentLink</a>, <span class="ident">token</span><span>: </span><a class="type-ref" href="#CancellationToken">CancellationToken</a><span>)</span><span>: </span><a class="type-ref" href="#ProviderResult">ProviderResult</a>&lt;<a class="type-ref" href="#DocumentLink">DocumentLink</a>&gt;</span>
<div class="details collapse" id="details-1020">
<div class="comment"><p>Given a link fill in its <a href="#DocumentLink.target">target</a>. This method is called when an incomplete
link is selected in the UI. Providers can implement this method and return incomplete links
(without target) from the <a href="#DocumentLinkProvider.provideDocumentLinks"><code>provideDocumentLinks</code></a> method which
often helps to improve performance.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="link"></a><span class="ts" id=1021 data-target="#details-1021" data-toggle="collapse"><span class="ident">link</span><span>: </span><a class="type-ref" href="#DocumentLink">DocumentLink</a></span></td><td><div class="comment"><p>The link that is to be resolved.</p>
</div></td></tr>
<tr><td><a name="token"></a><span class="ts" id=1022 data-target="#details-1022" data-toggle="collapse"><span class="ident">token</span><span>: </span><a class="type-ref" href="#CancellationToken">CancellationToken</a></span></td><td><div class="comment"><p>A cancellation token.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#ProviderResult">ProviderResult</a>&lt;<a class="type-ref" href="#DocumentLink">DocumentLink</a>&gt;</span></td><td><div class="comment"></div></td></tr>
</table>
</div>
</div>

### <a name="DocumentRangeFormattingEditProvider"></a><span class="code-item" id=885>DocumentRangeFormattingEditProvider</span>



<div class="comment"><p>The document formatting provider interface defines the contract between extensions and
the formatting-feature.</p>
</div>

#### Methods



<a name="DocumentRangeFormattingEditProvider.provideDocumentRangeFormattingEdits"></a><span class="ts" id=887 data-target="#details-887" data-toggle="collapse"><span class="ident">provideDocumentRangeFormattingEdits</span><span>(</span><span class="ident">document</span><span>: </span><a class="type-ref" href="#TextDocument">TextDocument</a>, <span class="ident">range</span><span>: </span><a class="type-ref" href="#Range">Range</a>, <span class="ident">options</span><span>: </span><a class="type-ref" href="#FormattingOptions">FormattingOptions</a>, <span class="ident">token</span><span>: </span><a class="type-ref" href="#CancellationToken">CancellationToken</a><span>)</span><span>: </span><a class="type-ref" href="#ProviderResult">ProviderResult</a>&lt;<a class="type-ref" href="#TextEdit">TextEdit</a>[]&gt;</span>
<div class="details collapse" id="details-887">
<div class="comment"><p>Provide formatting edits for a range in a document.</p>
<p>The given range is a hint and providers can decide to format a smaller
or larger range. Often this is done by adjusting the start and end
of the range to full syntax nodes.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="document"></a><span class="ts" id=888 data-target="#details-888" data-toggle="collapse"><span class="ident">document</span><span>: </span><a class="type-ref" href="#TextDocument">TextDocument</a></span></td><td><div class="comment"><p>The document in which the command was invoked.</p>
</div></td></tr>
<tr><td><a name="range"></a><span class="ts" id=889 data-target="#details-889" data-toggle="collapse"><span class="ident">range</span><span>: </span><a class="type-ref" href="#Range">Range</a></span></td><td><div class="comment"><p>The range which should be formatted.</p>
</div></td></tr>
<tr><td><a name="options"></a><span class="ts" id=890 data-target="#details-890" data-toggle="collapse"><span class="ident">options</span><span>: </span><a class="type-ref" href="#FormattingOptions">FormattingOptions</a></span></td><td><div class="comment"><p>Options controlling formatting.</p>
</div></td></tr>
<tr><td><a name="token"></a><span class="ts" id=891 data-target="#details-891" data-toggle="collapse"><span class="ident">token</span><span>: </span><a class="type-ref" href="#CancellationToken">CancellationToken</a></span></td><td><div class="comment"><p>A cancellation token.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#ProviderResult">ProviderResult</a>&lt;<a class="type-ref" href="#TextEdit">TextEdit</a>[]&gt;</span></td><td><div class="comment"><p>A set of text edits or a thenable that resolves to such. The lack of a result can be
signaled by returning <code>undefined</code>, <code>null</code>, or an empty array.</p>
</div></td></tr>
</table>
</div>
</div>

### <a name="DocumentSelector"></a><span class="code-item" id=1989>DocumentSelector</span>



<div class="comment"><p>A language selector is the combination of one or many language identifiers
and <a href="#DocumentFilter">language filters</a>.</p>
<p><em>Note</em> that a document selector that is just a language identifier selects <em>all</em>
documents, even those that are not saved on disk. Only use such selectors when
a feature works without further context, e.g without the need to resolve related
&#39;files&#39;.</p>
<ul>
<li><em>sample</em> - <code>let sel:DocumentSelector = { scheme: &#39;file&#39;, language: &#39;typescript&#39; }</code>;</li>
</ul>
</div>



<a name="DocumentSelector"></a><span class="ts" id=1989 data-target="#details-1989" data-toggle="collapse"><span class="ident">DocumentSelector</span><span>: </span><a class="type-ref" href="#DocumentFilter">DocumentFilter</a> &#124; <a class="type-intrinsic">string</a> &#124; <a class="type-ref" href="#Array">Array</a>&lt;<a class="type-ref" href="#DocumentFilter">DocumentFilter</a> &#124; <a class="type-intrinsic">string</a>&gt;</span>

### <a name="DocumentSymbol"></a><span class="code-item" id=723>DocumentSymbol</span>



<div class="comment"><p>Represents programming constructs like variables, classes, interfaces etc. that appear in a document. Document
symbols can be hierarchical and they have two ranges: one that encloses its definition and one that points to
its most interesting range, e.g. the range of an identifier.</p>
</div>

#### Constructors



<a name="DocumentSymbol.new DocumentSymbol"></a><span class="ts" id=731 data-target="#details-731" data-toggle="collapse"><span class="ident">new DocumentSymbol</span><span>(</span><span class="ident">name</span><span>: </span><a class="type-intrinsic">string</a>, <span class="ident">detail</span><span>: </span><a class="type-intrinsic">string</a>, <span class="ident">kind</span><span>: </span><a class="type-ref" href="#SymbolKind">SymbolKind</a>, <span class="ident">range</span><span>: </span><a class="type-ref" href="#Range">Range</a>, <span class="ident">selectionRange</span><span>: </span><a class="type-ref" href="#Range">Range</a><span>)</span><span>: </span><a class="type-ref" href="#DocumentSymbol">DocumentSymbol</a></span>
<div class="details collapse" id="details-731">
<div class="comment"><p>Creates a new document symbol.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="name"></a><span class="ts" id=732 data-target="#details-732" data-toggle="collapse"><span class="ident">name</span><span>: </span><a class="type-intrinsic">string</a></span></td><td><div class="comment"><p>The name of the symbol.</p>
</div></td></tr>
<tr><td><a name="detail"></a><span class="ts" id=733 data-target="#details-733" data-toggle="collapse"><span class="ident">detail</span><span>: </span><a class="type-intrinsic">string</a></span></td><td><div class="comment"><p>Details for the symbol.</p>
</div></td></tr>
<tr><td><a name="kind"></a><span class="ts" id=734 data-target="#details-734" data-toggle="collapse"><span class="ident">kind</span><span>: </span><a class="type-ref" href="#SymbolKind">SymbolKind</a></span></td><td><div class="comment"><p>The kind of the symbol.</p>
</div></td></tr>
<tr><td><a name="range"></a><span class="ts" id=735 data-target="#details-735" data-toggle="collapse"><span class="ident">range</span><span>: </span><a class="type-ref" href="#Range">Range</a></span></td><td><div class="comment"><p>The full range of the symbol.</p>
</div></td></tr>
<tr><td><a name="selectionRange"></a><span class="ts" id=736 data-target="#details-736" data-toggle="collapse"><span class="ident">selectionRange</span><span>: </span><a class="type-ref" href="#Range">Range</a></span></td><td><div class="comment"><p>The range that should be reveal.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#DocumentSymbol">DocumentSymbol</a></span></td><td><div class="comment"></div></td></tr>
</table>
</div>
</div>

#### Properties



<a name="DocumentSymbol.children"></a><span class="ts" id=729 data-target="#details-729" data-toggle="collapse"><span class="ident">children</span><span>: </span><a class="type-ref" href="#DocumentSymbol">DocumentSymbol</a>[]</span>
<div class="details collapse" id="details-729">
<div class="comment"><p>Children of this symbol, e.g. properties of a class.</p>
</div>
</div>



<a name="DocumentSymbol.detail"></a><span class="ts" id=725 data-target="#details-725" data-toggle="collapse"><span class="ident">detail</span><span>: </span><a class="type-intrinsic">string</a></span>
<div class="details collapse" id="details-725">
<div class="comment"><p>More detail for this symbol, e.g the signature of a function.</p>
</div>
</div>



<a name="DocumentSymbol.kind"></a><span class="ts" id=726 data-target="#details-726" data-toggle="collapse"><span class="ident">kind</span><span>: </span><a class="type-ref" href="#SymbolKind">SymbolKind</a></span>
<div class="details collapse" id="details-726">
<div class="comment"><p>The kind of this symbol.</p>
</div>
</div>



<a name="DocumentSymbol.name"></a><span class="ts" id=724 data-target="#details-724" data-toggle="collapse"><span class="ident">name</span><span>: </span><a class="type-intrinsic">string</a></span>
<div class="details collapse" id="details-724">
<div class="comment"><p>The name of this symbol.</p>
</div>
</div>



<a name="DocumentSymbol.range"></a><span class="ts" id=727 data-target="#details-727" data-toggle="collapse"><span class="ident">range</span><span>: </span><a class="type-ref" href="#Range">Range</a></span>
<div class="details collapse" id="details-727">
<div class="comment"><p>The range enclosing this symbol not including leading/trailing whitespace but everything else, e.g comments and code.</p>
</div>
</div>



<a name="DocumentSymbol.selectionRange"></a><span class="ts" id=728 data-target="#details-728" data-toggle="collapse"><span class="ident">selectionRange</span><span>: </span><a class="type-ref" href="#Range">Range</a></span>
<div class="details collapse" id="details-728">
<div class="comment"><p>The range that should be selected and reveal when this symbol is being picked, e.g the name of a function.
Must be contained by the <a href="#DocumentSymbol.range"><code>range</code></a>.</p>
</div>
</div>

### <a name="DocumentSymbolProvider"></a><span class="code-item" id=737>DocumentSymbolProvider</span>



<div class="comment"><p>The document symbol provider interface defines the contract between extensions and
the <a href="https://code.visualstudio.com/docs/editor/editingevolved#_go-to-symbol">go to symbol</a>-feature.</p>
</div>

#### Methods



<a name="DocumentSymbolProvider.provideDocumentSymbols"></a><span class="ts" id=739 data-target="#details-739" data-toggle="collapse"><span class="ident">provideDocumentSymbols</span><span>(</span><span class="ident">document</span><span>: </span><a class="type-ref" href="#TextDocument">TextDocument</a>, <span class="ident">token</span><span>: </span><a class="type-ref" href="#CancellationToken">CancellationToken</a><span>)</span><span>: </span><a class="type-ref" href="#ProviderResult">ProviderResult</a>&lt;<a class="type-ref" href="#SymbolInformation">SymbolInformation</a>[] &#124; <a class="type-ref" href="#DocumentSymbol">DocumentSymbol</a>[]&gt;</span>
<div class="details collapse" id="details-739">
<div class="comment"><p>Provide symbol information for the given document.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="document"></a><span class="ts" id=740 data-target="#details-740" data-toggle="collapse"><span class="ident">document</span><span>: </span><a class="type-ref" href="#TextDocument">TextDocument</a></span></td><td><div class="comment"><p>The document in which the command was invoked.</p>
</div></td></tr>
<tr><td><a name="token"></a><span class="ts" id=741 data-target="#details-741" data-toggle="collapse"><span class="ident">token</span><span>: </span><a class="type-ref" href="#CancellationToken">CancellationToken</a></span></td><td><div class="comment"><p>A cancellation token.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#ProviderResult">ProviderResult</a>&lt;<a class="type-ref" href="#SymbolInformation">SymbolInformation</a>[] &#124; <a class="type-ref" href="#DocumentSymbol">DocumentSymbol</a>[]&gt;</span></td><td><div class="comment"><p>An array of document highlights or a thenable that resolves to such. The lack of a result can be
signaled by returning <code>undefined</code>, <code>null</code>, or an empty array.</p>
</div></td></tr>
</table>
</div>
</div>

### <a name="DocumentSymbolProviderMetadata"></a><span class="code-item" id=742>DocumentSymbolProviderMetadata</span>



<div class="comment"><p>Metadata about a document symbol provider.</p>
</div>

#### Properties



<a name="DocumentSymbolProviderMetadata.label"></a><span class="ts" id=743 data-target="#details-743" data-toggle="collapse"><span class="ident">label</span><span>?</span><span>: </span><a class="type-intrinsic">string</a></span>
<div class="details collapse" id="details-743">
<div class="comment"><p>A human readable string that is shown when multiple outlines trees show for one document.</p>
</div>
</div>

### <a name="EndOfLine"></a><span class="code-item" id=373>EndOfLine</span>



<div class="comment"><p>Represents an end of line character sequence in a <a href="#TextDocument">document</a>.</p>
</div>

#### Enumeration members



<a name="EndOfLine.CRLF"></a><span class="ts" id=375 data-target="#details-375" data-toggle="collapse"><span class="ident">CRLF</span></span>
<div class="details collapse" id="details-375">
<em>2</em>
</div>



<a name="EndOfLine.LF"></a><span class="ts" id=374 data-target="#details-374" data-toggle="collapse"><span class="ident">LF</span></span>
<div class="details collapse" id="details-374">
<em>1</em>
</div>

### <a name="EnterAction"></a><span class="code-item" id=1094>EnterAction</span>



<div class="comment"><p>Describes what to do when pressing Enter.</p>
</div>

#### Properties



<a name="EnterAction.appendText"></a><span class="ts" id=1096 data-target="#details-1096" data-toggle="collapse"><span class="ident">appendText</span><span>?</span><span>: </span><a class="type-intrinsic">string</a></span>
<div class="details collapse" id="details-1096">
<div class="comment"><p>Describes text to be appended after the new line and after the indentation.</p>
</div>
</div>



<a name="EnterAction.indentAction"></a><span class="ts" id=1095 data-target="#details-1095" data-toggle="collapse"><span class="ident">indentAction</span><span>: </span><a class="type-ref" href="#IndentAction">IndentAction</a></span>
<div class="details collapse" id="details-1095">
<div class="comment"><p>Describe what to do with the indentation.</p>
</div>
</div>



<a name="EnterAction.removeText"></a><span class="ts" id=1097 data-target="#details-1097" data-toggle="collapse"><span class="ident">removeText</span><span>?</span><span>: </span><a class="type-intrinsic">number</a></span>
<div class="details collapse" id="details-1097">
<div class="comment"><p>Describes the number of characters to remove from the new line&#39;s indentation.</p>
</div>
</div>

### <a name="Event"></a><span class="code-item" id=447>Event&lt;T&gt;</span>



<div class="comment"><p>Represents a typed event.</p>
<p>A function that represents an event to which you subscribe by calling it with
a listener function as argument.</p>
<ul>
<li><em>sample</em> - <code>item.onDidChange(function(event) { console.log(&quot;Event happened: &quot; + event); });</code></li>
</ul>
</div>



<a name="__call"></a><span class="ts" id=449 data-target="#details-449" data-toggle="collapse"><span>(</span><span class="ident">listener</span><span>: </span>(e: <a class="type-intrinsic">T</a>) =&gt; <a class="type-intrinsic">any</a>, <span class="ident">thisArgs</span><span>?</span><span>: </span><a class="type-intrinsic">any</a>, <span class="ident">disposables</span><span>?</span><span>: </span><a class="type-ref" href="#Disposable">Disposable</a>[]<span>)</span><span>: </span><a class="type-ref" href="#Disposable">Disposable</a></span>
<div class="details collapse" id="details-449">
<div class="comment"><p>A function that represents an event to which you subscribe by calling it with
a listener function as argument.</p>
<p>A function that represents an event to which you subscribe by calling it with
a listener function as argument.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="listener"></a><span class="ts" id=450 data-target="#details-450" data-toggle="collapse"><span class="ident">listener</span><span>: </span>(e: <a class="type-intrinsic">T</a>) =&gt; <a class="type-intrinsic">any</a></span></td><td><div class="comment"><p>The listener function will be called when the event happens.</p>
</div></td></tr>
<tr><td><a name="thisArgs"></a><span class="ts" id=454 data-target="#details-454" data-toggle="collapse"><span class="ident">thisArgs</span><span>?</span><span>: </span><a class="type-intrinsic">any</a></span></td><td><div class="comment"><p>The <code>this</code>-argument which will be used when calling the event listener.</p>
</div></td></tr>
<tr><td><a name="disposables"></a><span class="ts" id=455 data-target="#details-455" data-toggle="collapse"><span class="ident">disposables</span><span>?</span><span>: </span><a class="type-ref" href="#Disposable">Disposable</a>[]</span></td><td><div class="comment"><p>An array to which a <a href="#Disposable">disposable</a> will be added.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#Disposable">Disposable</a></span></td><td><div class="comment"><p>A disposable which unsubscribes the event listener.</p>
</div></td></tr>
</table>
</div>
</div>

### <a name="EventEmitter"></a><span class="code-item" id=456>EventEmitter&lt;T&gt;</span>



<div class="comment"><p>An event emitter can be used to create and manage an <a href="#Event">event</a> for others
to subscribe to. One emitter always owns one event.</p>
<p>Use this class if you want to provide event from within your extension, for instance
inside a <a href="#TextDocumentContentProvider">TextDocumentContentProvider</a> or when providing
API to other extensions.</p>
</div>

#### Properties



<a name="EventEmitter.event"></a><span class="ts" id=458 data-target="#details-458" data-toggle="collapse"><span class="ident">event</span><span>: </span><a class="type-ref" href="#Event">Event</a>&lt;<a class="type-intrinsic">T</a>&gt;</span>
<div class="details collapse" id="details-458">
<div class="comment"><p>The event listeners can subscribe to.</p>
</div>
</div>

#### Methods



<a name="EventEmitter.dispose"></a><span class="ts" id=463 data-target="#details-463" data-toggle="collapse"><span class="ident">dispose</span><span>(</span><span>)</span><span>: </span><a class="type-intrinsic">void</a></span>
<div class="details collapse" id="details-463">
<div class="comment"><p>Dispose this object and free resources.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-intrinsic">void</a></span></td><td><div class="comment"></div></td></tr>
</table>
</div>
</div>



<a name="EventEmitter.fire"></a><span class="ts" id=460 data-target="#details-460" data-toggle="collapse"><span class="ident">fire</span><span>(</span><span class="ident">data</span><span>?</span><span>: </span><a class="type-intrinsic">T</a><span>)</span><span>: </span><a class="type-intrinsic">void</a></span>
<div class="details collapse" id="details-460">
<div class="comment"><p>Notify all subscribers of the <a href="#EventEmitter.event">event</a>. Failure
of one or more listener will not fail this function call.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="data"></a><span class="ts" id=461 data-target="#details-461" data-toggle="collapse"><span class="ident">data</span><span>?</span><span>: </span><a class="type-intrinsic">T</a></span></td><td><div class="comment"><p>The event object.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-intrinsic">void</a></span></td><td><div class="comment"></div></td></tr>
</table>
</div>
</div>

### <a name="Extension"></a><span class="code-item" id=1295>Extension&lt;T&gt;</span>



<div class="comment"><p>Represents an extension.</p>
<p>To get an instance of an <code>Extension</code> use <a href="#extensions.getExtension">getExtension</a>.</p>
</div>

#### Properties



<a name="Extension.exports"></a><span class="ts" id=1301 data-target="#details-1301" data-toggle="collapse"><span class="ident">exports</span><span>: </span><a class="type-intrinsic">T</a></span>
<div class="details collapse" id="details-1301">
<div class="comment"><p>The public API exported by this extension. It is an invalid action
to access this field before this extension has been activated.</p>
</div>
</div>



<a name="Extension.extensionPath"></a><span class="ts" id=1298 data-target="#details-1298" data-toggle="collapse"><span class="ident">extensionPath</span><span>: </span><a class="type-intrinsic">string</a></span>
<div class="details collapse" id="details-1298">
<div class="comment"><p>The absolute file path of the directory containing this extension.</p>
</div>
</div>



<a name="Extension.id"></a><span class="ts" id=1297 data-target="#details-1297" data-toggle="collapse"><span class="ident">id</span><span>: </span><a class="type-intrinsic">string</a></span>
<div class="details collapse" id="details-1297">
<div class="comment"><p>The canonical extension identifier in the form of: <code>publisher.name</code>.</p>
</div>
</div>



<a name="Extension.isActive"></a><span class="ts" id=1299 data-target="#details-1299" data-toggle="collapse"><span class="ident">isActive</span><span>: </span><a class="type-intrinsic">boolean</a></span>
<div class="details collapse" id="details-1299">
<div class="comment"><p><code>true</code> if the extension has been activated.</p>
</div>
</div>



<a name="Extension.packageJSON"></a><span class="ts" id=1300 data-target="#details-1300" data-toggle="collapse"><span class="ident">packageJSON</span><span>: </span><a class="type-intrinsic">any</a></span>
<div class="details collapse" id="details-1300">
<div class="comment"><p>The parsed contents of the extension&#39;s package.json.</p>
</div>
</div>

#### Methods



<a name="Extension.activate"></a><span class="ts" id=1303 data-target="#details-1303" data-toggle="collapse"><span class="ident">activate</span><span>(</span><span>)</span><span>: </span><a class="type-ref" href="#Thenable">Thenable</a>&lt;<a class="type-intrinsic">T</a>&gt;</span>
<div class="details collapse" id="details-1303">
<div class="comment"><p>Activates this extension and returns its public API.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#Thenable">Thenable</a>&lt;<a class="type-intrinsic">T</a>&gt;</span></td><td><div class="comment"><p>A promise that will resolve when this extension has been activated.</p>
</div></td></tr>
</table>
</div>
</div>

### <a name="ExtensionContext"></a><span class="code-item" id=1304>ExtensionContext</span>



<div class="comment"><p>An extension context is a collection of utilities private to an
extension.</p>
<p>An instance of an <code>ExtensionContext</code> is provided as the first
parameter to the <code>activate</code>-call of an extension.</p>
</div>

#### Properties



<a name="ExtensionContext.extensionPath"></a><span class="ts" id=1311 data-target="#details-1311" data-toggle="collapse"><span class="ident">extensionPath</span><span>: </span><a class="type-intrinsic">string</a></span>
<div class="details collapse" id="details-1311">
<div class="comment"><p>The absolute file path of the directory containing the extension.</p>
</div>
</div>



<a name="ExtensionContext.globalState"></a><span class="ts" id=1310 data-target="#details-1310" data-toggle="collapse"><span class="ident">globalState</span><span>: </span><a class="type-ref" href="#Memento">Memento</a></span>
<div class="details collapse" id="details-1310">
<div class="comment"><p>A memento object that stores state independent
of the current opened <a href="#workspace.workspaceFolders">workspace</a>.</p>
</div>
</div>



<a name="ExtensionContext.logPath"></a><span class="ts" id=1316 data-target="#details-1316" data-toggle="collapse"><span class="ident">logPath</span><span>: </span><a class="type-intrinsic">string</a></span>
<div class="details collapse" id="details-1316">
<div class="comment"><p>An absolute file path of a directory in which the extension can create log files.
The directory might not exist on disk and creation is up to the extension. However,
the parent directory is guaranteed to be existent.</p>
</div>
</div>



<a name="ExtensionContext.storagePath"></a><span class="ts" id=1315 data-target="#details-1315" data-toggle="collapse"><span class="ident">storagePath</span><span>: </span><a class="type-intrinsic">string</a> &#124; <a class="type-intrinsic">undefined</a></span>
<div class="details collapse" id="details-1315">
<div class="comment"><p>An absolute file path of a workspace specific directory in which the extension
can store private state. The directory might not exist on disk and creation is
up to the extension. However, the parent directory is guaranteed to be existent.</p>
<p>Use <a href="#ExtensionContext.workspaceState"><code>workspaceState</code></a> or
<a href="#ExtensionContext.globalState"><code>globalState</code></a> to store key value data.</p>
</div>
</div>



<a name="ExtensionContext.subscriptions"></a><span class="ts" id=1305 data-target="#details-1305" data-toggle="collapse"><span class="ident">subscriptions</span><span>: </span>{dispose}[]</span>
<div class="details collapse" id="details-1305">
<div class="comment"><p>An array to which disposables can be added. When this
extension is deactivated the disposables will be disposed.</p>
</div>
</div>



<a name="ExtensionContext.workspaceState"></a><span class="ts" id=1309 data-target="#details-1309" data-toggle="collapse"><span class="ident">workspaceState</span><span>: </span><a class="type-ref" href="#Memento">Memento</a></span>
<div class="details collapse" id="details-1309">
<div class="comment"><p>A memento object that stores state in the context
of the currently opened <a href="#workspace.workspaceFolders">workspace</a>.</p>
</div>
</div>

#### Methods



<a name="ExtensionContext.asAbsolutePath"></a><span class="ts" id=1313 data-target="#details-1313" data-toggle="collapse"><span class="ident">asAbsolutePath</span><span>(</span><span class="ident">relativePath</span><span>: </span><a class="type-intrinsic">string</a><span>)</span><span>: </span><a class="type-intrinsic">string</a></span>
<div class="details collapse" id="details-1313">
<div class="comment"><p>Get the absolute path of a resource contained in the extension.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="relativePath"></a><span class="ts" id=1314 data-target="#details-1314" data-toggle="collapse"><span class="ident">relativePath</span><span>: </span><a class="type-intrinsic">string</a></span></td><td><div class="comment"><p>A relative path to a resource contained in the extension.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-intrinsic">string</a></span></td><td><div class="comment"><p>The absolute path of the resource.</p>
</div></td></tr>
</table>
</div>
</div>

### <a name="FileChangeEvent"></a><span class="code-item" id=1507>FileChangeEvent</span>



<div class="comment"><p>The event filesystem providers must use to signal a file change.</p>
</div>

#### Properties



<a name="FileChangeEvent.type"></a><span class="ts" id=1508 data-target="#details-1508" data-toggle="collapse"><span class="ident">type</span><span>: </span><a class="type-ref" href="#FileChangeType">FileChangeType</a></span>
<div class="details collapse" id="details-1508">
<div class="comment"><p>The type of change.</p>
</div>
</div>



<a name="FileChangeEvent.uri"></a><span class="ts" id=1509 data-target="#details-1509" data-toggle="collapse"><span class="ident">uri</span><span>: </span><a class="type-ref" href="#Uri">Uri</a></span>
<div class="details collapse" id="details-1509">
<div class="comment"><p>The uri of the file that has changed.</p>
</div>
</div>

### <a name="FileChangeType"></a><span class="code-item" id=1503>FileChangeType</span>



<div class="comment"><p>Enumeration of file change types.</p>
</div>

#### Enumeration members



<a name="FileChangeType.Changed"></a><span class="ts" id=1504 data-target="#details-1504" data-toggle="collapse"><span class="ident">Changed</span></span>
<div class="details collapse" id="details-1504">
<em>1</em>
</div>



<a name="FileChangeType.Created"></a><span class="ts" id=1505 data-target="#details-1505" data-toggle="collapse"><span class="ident">Created</span></span>
<div class="details collapse" id="details-1505">
<em>2</em>
</div>



<a name="FileChangeType.Deleted"></a><span class="ts" id=1506 data-target="#details-1506" data-toggle="collapse"><span class="ident">Deleted</span></span>
<div class="details collapse" id="details-1506">
<em>3</em>
</div>

### <a name="FileStat"></a><span class="code-item" id=1472>FileStat</span>



<div class="comment"><p>The <code>FileStat</code>-type represents metadata about a file</p>
</div>

#### Properties



<a name="FileStat.ctime"></a><span class="ts" id=1474 data-target="#details-1474" data-toggle="collapse"><span class="ident">ctime</span><span>: </span><a class="type-intrinsic">number</a></span>
<div class="details collapse" id="details-1474">
<div class="comment"><p>The creation timestamp in milliseconds elapsed since January 1, 1970 00:00:00 UTC.</p>
</div>
</div>



<a name="FileStat.mtime"></a><span class="ts" id=1475 data-target="#details-1475" data-toggle="collapse"><span class="ident">mtime</span><span>: </span><a class="type-intrinsic">number</a></span>
<div class="details collapse" id="details-1475">
<div class="comment"><p>The modification timestamp in milliseconds elapsed since January 1, 1970 00:00:00 UTC.</p>
</div>
</div>



<a name="FileStat.size"></a><span class="ts" id=1476 data-target="#details-1476" data-toggle="collapse"><span class="ident">size</span><span>: </span><a class="type-intrinsic">number</a></span>
<div class="details collapse" id="details-1476">
<div class="comment"><p>The size in bytes.</p>
</div>
</div>



<a name="FileStat.type"></a><span class="ts" id=1473 data-target="#details-1473" data-toggle="collapse"><span class="ident">type</span><span>: </span><a class="type-ref" href="#FileType">FileType</a></span>
<div class="details collapse" id="details-1473">
<div class="comment"><p>The type of the file, e.g. is a regular file, a directory, or symbolic link
to a file.</p>
</div>
</div>

### <a name="FileSystemError"></a><span class="code-item" id=1477>FileSystemError</span>



<div class="comment"><p>A type that filesystem providers should use to signal errors.</p>
<p>This class has factory methods for common error-cases, like <code>EntryNotFound</code> when
a file or folder doesn&#39;t exist, use them like so: <code>throw vscode.FileSystemError.EntryNotFound(someUri);</code></p>
</div>

#### Static



<a name="FileSystemError.Error"></a><span class="ts" id=1502 data-target="#details-1502" data-toggle="collapse"><span class="ident">Error</span><span>: </span><a class="type-ref" href="#ErrorConstructor">ErrorConstructor</a></span>
<div class="details collapse" id="details-1502">
<div class="comment"></div>
</div>



<a name="FileSystemError.message"></a><span class="ts" id=1500 data-target="#details-1500" data-toggle="collapse"><span class="ident">message</span><span>: </span><a class="type-intrinsic">string</a></span>
<div class="details collapse" id="details-1500">
<div class="comment"></div>
</div>



<a name="FileSystemError.name"></a><span class="ts" id=1499 data-target="#details-1499" data-toggle="collapse"><span class="ident">name</span><span>: </span><a class="type-intrinsic">string</a></span>
<div class="details collapse" id="details-1499">
<div class="comment"></div>
</div>



<a name="FileSystemError.stack"></a><span class="ts" id=1501 data-target="#details-1501" data-toggle="collapse"><span class="ident">stack</span><span>?</span><span>: </span><a class="type-intrinsic">string</a></span>
<div class="details collapse" id="details-1501">
<div class="comment"></div>
</div>



<a name="FileSystemError.FileExists"></a><span class="ts" id=1482 data-target="#details-1482" data-toggle="collapse"><span class="ident">FileExists</span><span>(</span><span class="ident">messageOrUri</span><span>?</span><span>: </span><a class="type-intrinsic">string</a> &#124; <a class="type-ref" href="#Uri">Uri</a><span>)</span><span>: </span><a class="type-ref" href="#FileSystemError">FileSystemError</a></span>
<div class="details collapse" id="details-1482">
<div class="comment"><p>Create an error to signal that a file or folder already exists, e.g. when
creating but not overwriting a file.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="messageOrUri"></a><span class="ts" id=1483 data-target="#details-1483" data-toggle="collapse"><span class="ident">messageOrUri</span><span>?</span><span>: </span><a class="type-intrinsic">string</a> &#124; <a class="type-ref" href="#Uri">Uri</a></span></td><td><div class="comment"><p>Message or uri.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#FileSystemError">FileSystemError</a></span></td><td><div class="comment"></div></td></tr>
</table>
</div>
</div>



<a name="FileSystemError.FileIsADirectory"></a><span class="ts" id=1488 data-target="#details-1488" data-toggle="collapse"><span class="ident">FileIsADirectory</span><span>(</span><span class="ident">messageOrUri</span><span>?</span><span>: </span><a class="type-intrinsic">string</a> &#124; <a class="type-ref" href="#Uri">Uri</a><span>)</span><span>: </span><a class="type-ref" href="#FileSystemError">FileSystemError</a></span>
<div class="details collapse" id="details-1488">
<div class="comment"><p>Create an error to signal that a file is a folder.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="messageOrUri"></a><span class="ts" id=1489 data-target="#details-1489" data-toggle="collapse"><span class="ident">messageOrUri</span><span>?</span><span>: </span><a class="type-intrinsic">string</a> &#124; <a class="type-ref" href="#Uri">Uri</a></span></td><td><div class="comment"><p>Message or uri.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#FileSystemError">FileSystemError</a></span></td><td><div class="comment"></div></td></tr>
</table>
</div>
</div>



<a name="FileSystemError.FileNotADirectory"></a><span class="ts" id=1485 data-target="#details-1485" data-toggle="collapse"><span class="ident">FileNotADirectory</span><span>(</span><span class="ident">messageOrUri</span><span>?</span><span>: </span><a class="type-intrinsic">string</a> &#124; <a class="type-ref" href="#Uri">Uri</a><span>)</span><span>: </span><a class="type-ref" href="#FileSystemError">FileSystemError</a></span>
<div class="details collapse" id="details-1485">
<div class="comment"><p>Create an error to signal that a file is not a folder.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="messageOrUri"></a><span class="ts" id=1486 data-target="#details-1486" data-toggle="collapse"><span class="ident">messageOrUri</span><span>?</span><span>: </span><a class="type-intrinsic">string</a> &#124; <a class="type-ref" href="#Uri">Uri</a></span></td><td><div class="comment"><p>Message or uri.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#FileSystemError">FileSystemError</a></span></td><td><div class="comment"></div></td></tr>
</table>
</div>
</div>



<a name="FileSystemError.FileNotFound"></a><span class="ts" id=1479 data-target="#details-1479" data-toggle="collapse"><span class="ident">FileNotFound</span><span>(</span><span class="ident">messageOrUri</span><span>?</span><span>: </span><a class="type-intrinsic">string</a> &#124; <a class="type-ref" href="#Uri">Uri</a><span>)</span><span>: </span><a class="type-ref" href="#FileSystemError">FileSystemError</a></span>
<div class="details collapse" id="details-1479">
<div class="comment"><p>Create an error to signal that a file or folder wasn&#39;t found.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="messageOrUri"></a><span class="ts" id=1480 data-target="#details-1480" data-toggle="collapse"><span class="ident">messageOrUri</span><span>?</span><span>: </span><a class="type-intrinsic">string</a> &#124; <a class="type-ref" href="#Uri">Uri</a></span></td><td><div class="comment"><p>Message or uri.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#FileSystemError">FileSystemError</a></span></td><td><div class="comment"></div></td></tr>
</table>
</div>
</div>



<a name="FileSystemError.NoPermissions"></a><span class="ts" id=1491 data-target="#details-1491" data-toggle="collapse"><span class="ident">NoPermissions</span><span>(</span><span class="ident">messageOrUri</span><span>?</span><span>: </span><a class="type-intrinsic">string</a> &#124; <a class="type-ref" href="#Uri">Uri</a><span>)</span><span>: </span><a class="type-ref" href="#FileSystemError">FileSystemError</a></span>
<div class="details collapse" id="details-1491">
<div class="comment"><p>Create an error to signal that an operation lacks required permissions.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="messageOrUri"></a><span class="ts" id=1492 data-target="#details-1492" data-toggle="collapse"><span class="ident">messageOrUri</span><span>?</span><span>: </span><a class="type-intrinsic">string</a> &#124; <a class="type-ref" href="#Uri">Uri</a></span></td><td><div class="comment"><p>Message or uri.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#FileSystemError">FileSystemError</a></span></td><td><div class="comment"></div></td></tr>
</table>
</div>
</div>



<a name="FileSystemError.Unavailable"></a><span class="ts" id=1494 data-target="#details-1494" data-toggle="collapse"><span class="ident">Unavailable</span><span>(</span><span class="ident">messageOrUri</span><span>?</span><span>: </span><a class="type-intrinsic">string</a> &#124; <a class="type-ref" href="#Uri">Uri</a><span>)</span><span>: </span><a class="type-ref" href="#FileSystemError">FileSystemError</a></span>
<div class="details collapse" id="details-1494">
<div class="comment"><p>Create an error to signal that the file system is unavailable or too busy to
complete a request.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="messageOrUri"></a><span class="ts" id=1495 data-target="#details-1495" data-toggle="collapse"><span class="ident">messageOrUri</span><span>?</span><span>: </span><a class="type-intrinsic">string</a> &#124; <a class="type-ref" href="#Uri">Uri</a></span></td><td><div class="comment"><p>Message or uri.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#FileSystemError">FileSystemError</a></span></td><td><div class="comment"></div></td></tr>
</table>
</div>
</div>

#### Constructors



<a name="FileSystemError.new FileSystemError"></a><span class="ts" id=1497 data-target="#details-1497" data-toggle="collapse"><span class="ident">new FileSystemError</span><span>(</span><span class="ident">messageOrUri</span><span>?</span><span>: </span><a class="type-intrinsic">string</a> &#124; <a class="type-ref" href="#Uri">Uri</a><span>)</span><span>: </span><a class="type-ref" href="#FileSystemError">FileSystemError</a></span>
<div class="details collapse" id="details-1497">
<div class="comment"><p>Creates a new filesystem error.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="messageOrUri"></a><span class="ts" id=1498 data-target="#details-1498" data-toggle="collapse"><span class="ident">messageOrUri</span><span>?</span><span>: </span><a class="type-intrinsic">string</a> &#124; <a class="type-ref" href="#Uri">Uri</a></span></td><td><div class="comment"><p>Message or uri.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#FileSystemError">FileSystemError</a></span></td><td><div class="comment"></div></td></tr>
</table>
</div>
</div>

### <a name="FileSystemProvider"></a><span class="code-item" id=1510>FileSystemProvider</span>



<div class="comment"><p>The filesystem provider defines what the editor needs to read, write, discover,
and to manage files and folders. It allows extensions to serve files from remote places,
like ftp-servers, and to seamlessly integrate those into the editor.</p>
<ul>
<li><em>Note 1:</em> The filesystem provider API works with <a href="#Uri">uris</a> and assumes hierarchical
paths, e.g. <code>foo:/my/path</code> is a child of <code>foo:/my/</code> and a parent of <code>foo:/my/path/deeper</code>.</li>
<li><em>Note 2:</em> There is an activation event <code>onFileSystem:&lt;scheme&gt;</code> that fires when a file
or folder is being accessed.</li>
<li><em>Note 3:</em> The word &#39;file&#39; is often used to denote all <a href="#FileType">kinds</a> of files, e.g.
folders, symbolic links, and regular files.</li>
</ul>
</div>

#### Events



<a name="FileSystemProvider.onDidChangeFile"></a><span class="ts" id=1511 data-target="#details-1511" data-toggle="collapse"><span class="ident">onDidChangeFile</span><span>: </span><a class="type-ref" href="#Event">Event</a>&lt;<a class="type-ref" href="#FileChangeEvent">FileChangeEvent</a>[]&gt;</span>
<div class="details collapse" id="details-1511">
<div class="comment"><p>An event to signal that a resource has been created, changed, or deleted. This
event should fire for resources that are being <a href="#FileSystemProvider.watch">watched</a>
by clients of this provider.</p>
</div>
</div>

#### Methods



<a name="FileSystemProvider.copy"></a><span class="ts" id=1553 data-target="#details-1553" data-toggle="collapse"><span class="ident">copy</span><span>(</span><span class="ident">source</span><span>: </span><a class="type-ref" href="#Uri">Uri</a>, <span class="ident">destination</span><span>: </span><a class="type-ref" href="#Uri">Uri</a>, <span class="ident">options</span><span>: </span>{overwrite: <a class="type-intrinsic">boolean</a>}<span>)</span><span>: </span><a class="type-intrinsic">void</a> &#124; <a class="type-ref" href="#Thenable">Thenable</a>&lt;<a class="type-intrinsic">void</a>&gt;</span>
<div class="details collapse" id="details-1553">
<div class="comment"><p>Copy files or folders. Implementing this function is optional but it will speedup
the copy operation.</p>
<ul>
<li><em>throws</em> - <a href="#FileSystemError.FileNotFound"><code>FileNotFound</code></a> when <code>source</code> doesn&#39;t exist.</li>
</ul>
<ul>
<li><em>throws</em> - <a href="#FileSystemError.FileNotFound"><code>FileNotFound</code></a> when parent of <code>destination</code> doesn&#39;t exist, e.g. no mkdirp-logic required.</li>
</ul>
<ul>
<li><em>throws</em> - <a href="#FileSystemError.FileExists"><code>FileExists</code></a> when <code>destination</code> exists and when the <code>overwrite</code> option is not <code>true</code>.</li>
</ul>
<ul>
<li><em>throws</em> - <a href="#FileSystemError.NoPermissions"><code>NoPermissions</code></a> when permissions aren&#39;t sufficient.</li>
</ul>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="source"></a><span class="ts" id=1554 data-target="#details-1554" data-toggle="collapse"><span class="ident">source</span><span>: </span><a class="type-ref" href="#Uri">Uri</a></span></td><td><div class="comment"><p>The existing file.</p>
</div></td></tr>
<tr><td><a name="destination"></a><span class="ts" id=1555 data-target="#details-1555" data-toggle="collapse"><span class="ident">destination</span><span>: </span><a class="type-ref" href="#Uri">Uri</a></span></td><td><div class="comment"><p>The destination location.</p>
</div></td></tr>
<tr><td><a name="options"></a><span class="ts" id=1556 data-target="#details-1556" data-toggle="collapse"><span class="ident">options</span><span>: </span>{overwrite: <a class="type-intrinsic">boolean</a>}</span></td><td><div class="comment"><p>Defines if existing files should be overwriten.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-intrinsic">void</a> &#124; <a class="type-ref" href="#Thenable">Thenable</a>&lt;<a class="type-intrinsic">void</a>&gt;</span></td><td><div class="comment"></div></td></tr>
</table>
</div>
</div>



<a name="FileSystemProvider.createDirectory"></a><span class="ts" id=1526 data-target="#details-1526" data-toggle="collapse"><span class="ident">createDirectory</span><span>(</span><span class="ident">uri</span><span>: </span><a class="type-ref" href="#Uri">Uri</a><span>)</span><span>: </span><a class="type-intrinsic">void</a> &#124; <a class="type-ref" href="#Thenable">Thenable</a>&lt;<a class="type-intrinsic">void</a>&gt;</span>
<div class="details collapse" id="details-1526">
<div class="comment"><p>Create a new directory (Note, that new files are created via <code>write</code>-calls).</p>
<ul>
<li><em>throws</em> - <a href="#FileSystemError.FileNotFound"><code>FileNotFound</code></a> when the parent of <code>uri</code> doesn&#39;t exist, e.g. no mkdirp-logic required.</li>
</ul>
<ul>
<li><em>throws</em> - <a href="#FileSystemError.FileExists"><code>FileExists</code></a> when <code>uri</code> already exists.</li>
</ul>
<ul>
<li><em>throws</em> - <a href="#FileSystemError.NoPermissions"><code>NoPermissions</code></a> when permissions aren&#39;t sufficient.</li>
</ul>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="uri"></a><span class="ts" id=1527 data-target="#details-1527" data-toggle="collapse"><span class="ident">uri</span><span>: </span><a class="type-ref" href="#Uri">Uri</a></span></td><td><div class="comment"><p>The uri of the new folder.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-intrinsic">void</a> &#124; <a class="type-ref" href="#Thenable">Thenable</a>&lt;<a class="type-intrinsic">void</a>&gt;</span></td><td><div class="comment"></div></td></tr>
</table>
</div>
</div>



<a name="FileSystemProvider.delete"></a><span class="ts" id=1540 data-target="#details-1540" data-toggle="collapse"><span class="ident">delete</span><span>(</span><span class="ident">uri</span><span>: </span><a class="type-ref" href="#Uri">Uri</a>, <span class="ident">options</span><span>: </span>{recursive: <a class="type-intrinsic">boolean</a>}<span>)</span><span>: </span><a class="type-intrinsic">void</a> &#124; <a class="type-ref" href="#Thenable">Thenable</a>&lt;<a class="type-intrinsic">void</a>&gt;</span>
<div class="details collapse" id="details-1540">
<div class="comment"><p>Delete a file.</p>
<ul>
<li><em>throws</em> - <a href="#FileSystemError.FileNotFound"><code>FileNotFound</code></a> when <code>uri</code> doesn&#39;t exist.</li>
</ul>
<ul>
<li><em>throws</em> - <a href="#FileSystemError.NoPermissions"><code>NoPermissions</code></a> when permissions aren&#39;t sufficient.</li>
</ul>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="uri"></a><span class="ts" id=1541 data-target="#details-1541" data-toggle="collapse"><span class="ident">uri</span><span>: </span><a class="type-ref" href="#Uri">Uri</a></span></td><td><div class="comment"><p>The resource that is to be deleted.</p>
</div></td></tr>
<tr><td><a name="options"></a><span class="ts" id=1542 data-target="#details-1542" data-toggle="collapse"><span class="ident">options</span><span>: </span>{recursive: <a class="type-intrinsic">boolean</a>}</span></td><td><div class="comment"><p>Defines if deletion of folders is recursive.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-intrinsic">void</a> &#124; <a class="type-ref" href="#Thenable">Thenable</a>&lt;<a class="type-intrinsic">void</a>&gt;</span></td><td><div class="comment"></div></td></tr>
</table>
</div>
</div>



<a name="FileSystemProvider.readDirectory"></a><span class="ts" id=1523 data-target="#details-1523" data-toggle="collapse"><span class="ident">readDirectory</span><span>(</span><span class="ident">uri</span><span>: </span><a class="type-ref" href="#Uri">Uri</a><span>)</span><span>: </span>[<a class="type-intrinsic">string</a>, <a class="type-ref" href="#FileType">FileType</a>][] &#124; <a class="type-ref" href="#Thenable">Thenable</a>&lt;[<a class="type-intrinsic">string</a>, <a class="type-ref" href="#FileType">FileType</a>][]&gt;</span>
<div class="details collapse" id="details-1523">
<div class="comment"><p>Retrieve all entries of a <a href="#FileType.Directory">directory</a>.</p>
<ul>
<li><em>throws</em> - <a href="#FileSystemError.FileNotFound"><code>FileNotFound</code></a> when <code>uri</code> doesn&#39;t exist.</li>
</ul>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="uri"></a><span class="ts" id=1524 data-target="#details-1524" data-toggle="collapse"><span class="ident">uri</span><span>: </span><a class="type-ref" href="#Uri">Uri</a></span></td><td><div class="comment"><p>The uri of the folder.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts">[<a class="type-intrinsic">string</a>, <a class="type-ref" href="#FileType">FileType</a>][] &#124; <a class="type-ref" href="#Thenable">Thenable</a>&lt;[<a class="type-intrinsic">string</a>, <a class="type-ref" href="#FileType">FileType</a>][]&gt;</span></td><td><div class="comment"><p>An array of name/type-tuples or a thenable that resolves to such.</p>
</div></td></tr>
</table>
</div>
</div>



<a name="FileSystemProvider.readFile"></a><span class="ts" id=1529 data-target="#details-1529" data-toggle="collapse"><span class="ident">readFile</span><span>(</span><span class="ident">uri</span><span>: </span><a class="type-ref" href="#Uri">Uri</a><span>)</span><span>: </span><a class="type-ref" href="#Uint8Array">Uint8Array</a> &#124; <a class="type-ref" href="#Thenable">Thenable</a>&lt;<a class="type-ref" href="#Uint8Array">Uint8Array</a>&gt;</span>
<div class="details collapse" id="details-1529">
<div class="comment"><p>Read the entire contents of a file.</p>
<ul>
<li><em>throws</em> - <a href="#FileSystemError.FileNotFound"><code>FileNotFound</code></a> when <code>uri</code> doesn&#39;t exist.</li>
</ul>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="uri"></a><span class="ts" id=1530 data-target="#details-1530" data-toggle="collapse"><span class="ident">uri</span><span>: </span><a class="type-ref" href="#Uri">Uri</a></span></td><td><div class="comment"><p>The uri of the file.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#Uint8Array">Uint8Array</a> &#124; <a class="type-ref" href="#Thenable">Thenable</a>&lt;<a class="type-ref" href="#Uint8Array">Uint8Array</a>&gt;</span></td><td><div class="comment"><p>An array of bytes or a thenable that resolves to such.</p>
</div></td></tr>
</table>
</div>
</div>



<a name="FileSystemProvider.rename"></a><span class="ts" id=1546 data-target="#details-1546" data-toggle="collapse"><span class="ident">rename</span><span>(</span><span class="ident">oldUri</span><span>: </span><a class="type-ref" href="#Uri">Uri</a>, <span class="ident">newUri</span><span>: </span><a class="type-ref" href="#Uri">Uri</a>, <span class="ident">options</span><span>: </span>{overwrite: <a class="type-intrinsic">boolean</a>}<span>)</span><span>: </span><a class="type-intrinsic">void</a> &#124; <a class="type-ref" href="#Thenable">Thenable</a>&lt;<a class="type-intrinsic">void</a>&gt;</span>
<div class="details collapse" id="details-1546">
<div class="comment"><p>Rename a file or folder.</p>
<ul>
<li><em>throws</em> - <a href="#FileSystemError.FileNotFound"><code>FileNotFound</code></a> when <code>oldUri</code> doesn&#39;t exist.</li>
</ul>
<ul>
<li><em>throws</em> - <a href="#FileSystemError.FileNotFound"><code>FileNotFound</code></a> when parent of <code>newUri</code> doesn&#39;t exist, e.g. no mkdirp-logic required.</li>
</ul>
<ul>
<li><em>throws</em> - <a href="#FileSystemError.FileExists"><code>FileExists</code></a> when <code>newUri</code> exists and when the <code>overwrite</code> option is not <code>true</code>.</li>
</ul>
<ul>
<li><em>throws</em> - <a href="#FileSystemError.NoPermissions"><code>NoPermissions</code></a> when permissions aren&#39;t sufficient.</li>
</ul>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="oldUri"></a><span class="ts" id=1547 data-target="#details-1547" data-toggle="collapse"><span class="ident">oldUri</span><span>: </span><a class="type-ref" href="#Uri">Uri</a></span></td><td><div class="comment"><p>The existing file.</p>
</div></td></tr>
<tr><td><a name="newUri"></a><span class="ts" id=1548 data-target="#details-1548" data-toggle="collapse"><span class="ident">newUri</span><span>: </span><a class="type-ref" href="#Uri">Uri</a></span></td><td><div class="comment"><p>The new location.</p>
</div></td></tr>
<tr><td><a name="options"></a><span class="ts" id=1549 data-target="#details-1549" data-toggle="collapse"><span class="ident">options</span><span>: </span>{overwrite: <a class="type-intrinsic">boolean</a>}</span></td><td><div class="comment"><p>Defines if existing files should be overwritten.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-intrinsic">void</a> &#124; <a class="type-ref" href="#Thenable">Thenable</a>&lt;<a class="type-intrinsic">void</a>&gt;</span></td><td><div class="comment"></div></td></tr>
</table>
</div>
</div>



<a name="FileSystemProvider.stat"></a><span class="ts" id=1520 data-target="#details-1520" data-toggle="collapse"><span class="ident">stat</span><span>(</span><span class="ident">uri</span><span>: </span><a class="type-ref" href="#Uri">Uri</a><span>)</span><span>: </span><a class="type-ref" href="#FileStat">FileStat</a> &#124; <a class="type-ref" href="#Thenable">Thenable</a>&lt;<a class="type-ref" href="#FileStat">FileStat</a>&gt;</span>
<div class="details collapse" id="details-1520">
<div class="comment"><p>Retrieve metadata about a file.</p>
<p>Note that the metadata for symbolic links should be the metadata of the file they refer to.
Still, the <a href="#FileType.SymbolicLink">SymbolicLink</a>-type must be used in addition to the actual type, e.g.
<code>FileType.SymbolicLink | FileType.Directory</code>.</p>
<ul>
<li><em>throws</em> - <a href="#FileSystemError.FileNotFound"><code>FileNotFound</code></a> when <code>uri</code> doesn&#39;t exist.</li>
</ul>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="uri"></a><span class="ts" id=1521 data-target="#details-1521" data-toggle="collapse"><span class="ident">uri</span><span>: </span><a class="type-ref" href="#Uri">Uri</a></span></td><td><div class="comment"><p>The uri of the file to retrieve metadata about.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#FileStat">FileStat</a> &#124; <a class="type-ref" href="#Thenable">Thenable</a>&lt;<a class="type-ref" href="#FileStat">FileStat</a>&gt;</span></td><td><div class="comment"><p>The file metadata about the file.</p>
</div></td></tr>
</table>
</div>
</div>



<a name="FileSystemProvider.watch"></a><span class="ts" id=1513 data-target="#details-1513" data-toggle="collapse"><span class="ident">watch</span><span>(</span><span class="ident">uri</span><span>: </span><a class="type-ref" href="#Uri">Uri</a>, <span class="ident">options</span><span>: </span>{excludes: <a class="type-intrinsic">string</a>[], recursive: <a class="type-intrinsic">boolean</a>}<span>)</span><span>: </span><a class="type-ref" href="#Disposable">Disposable</a></span>
<div class="details collapse" id="details-1513">
<div class="comment"><p>Subscribe to events in the file or folder denoted by <code>uri</code>.</p>
<p>The editor will call this function for files and folders. In the latter case, the
options differ from defaults, e.g. what files/folders to exclude from watching
and if subfolders, sub-subfolder, etc. should be watched (<code>recursive</code>).</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="uri"></a><span class="ts" id=1514 data-target="#details-1514" data-toggle="collapse"><span class="ident">uri</span><span>: </span><a class="type-ref" href="#Uri">Uri</a></span></td><td><div class="comment"><p>The uri of the file to be watched.</p>
</div></td></tr>
<tr><td><a name="options"></a><span class="ts" id=1515 data-target="#details-1515" data-toggle="collapse"><span class="ident">options</span><span>: </span>{excludes: <a class="type-intrinsic">string</a>[], recursive: <a class="type-intrinsic">boolean</a>}</span></td><td><div class="comment"><p>Configures the watch.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#Disposable">Disposable</a></span></td><td><div class="comment"><p>A disposable that tells the provider to stop watching the <code>uri</code>.</p>
</div></td></tr>
</table>
</div>
</div>



<a name="FileSystemProvider.writeFile"></a><span class="ts" id=1532 data-target="#details-1532" data-toggle="collapse"><span class="ident">writeFile</span><span>(</span><span class="ident">uri</span><span>: </span><a class="type-ref" href="#Uri">Uri</a>, <span class="ident">content</span><span>: </span><a class="type-ref" href="#Uint8Array">Uint8Array</a>, <span class="ident">options</span><span>: </span>{create: <a class="type-intrinsic">boolean</a>, overwrite: <a class="type-intrinsic">boolean</a>}<span>)</span><span>: </span><a class="type-intrinsic">void</a> &#124; <a class="type-ref" href="#Thenable">Thenable</a>&lt;<a class="type-intrinsic">void</a>&gt;</span>
<div class="details collapse" id="details-1532">
<div class="comment"><p>Write data to a file, replacing its entire contents.</p>
<ul>
<li><em>throws</em> - <a href="#FileSystemError.FileNotFound"><code>FileNotFound</code></a> when <code>uri</code> doesn&#39;t exist and <code>create</code> is not set.</li>
</ul>
<ul>
<li><em>throws</em> - <a href="#FileSystemError.FileNotFound"><code>FileNotFound</code></a> when the parent of <code>uri</code> doesn&#39;t exist and <code>create</code> is set, e.g. no mkdirp-logic required.</li>
</ul>
<ul>
<li><em>throws</em> - <a href="#FileSystemError.FileExists"><code>FileExists</code></a> when <code>uri</code> already exists, <code>create</code> is set but <code>overwrite</code> is not set.</li>
</ul>
<ul>
<li><em>throws</em> - <a href="#FileSystemError.NoPermissions"><code>NoPermissions</code></a> when permissions aren&#39;t sufficient.</li>
</ul>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="uri"></a><span class="ts" id=1533 data-target="#details-1533" data-toggle="collapse"><span class="ident">uri</span><span>: </span><a class="type-ref" href="#Uri">Uri</a></span></td><td><div class="comment"><p>The uri of the file.</p>
</div></td></tr>
<tr><td><a name="content"></a><span class="ts" id=1534 data-target="#details-1534" data-toggle="collapse"><span class="ident">content</span><span>: </span><a class="type-ref" href="#Uint8Array">Uint8Array</a></span></td><td><div class="comment"><p>The new content of the file.</p>
</div></td></tr>
<tr><td><a name="options"></a><span class="ts" id=1535 data-target="#details-1535" data-toggle="collapse"><span class="ident">options</span><span>: </span>{create: <a class="type-intrinsic">boolean</a>, overwrite: <a class="type-intrinsic">boolean</a>}</span></td><td><div class="comment"><p>Defines if missing files should or must be created.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-intrinsic">void</a> &#124; <a class="type-ref" href="#Thenable">Thenable</a>&lt;<a class="type-intrinsic">void</a>&gt;</span></td><td><div class="comment"></div></td></tr>
</table>
</div>
</div>

### <a name="FileSystemWatcher"></a><span class="code-item" id=464>FileSystemWatcher</span>



<div class="comment"><p>A file system watcher notifies about changes to files and folders
on disk.</p>
<p>To get an instance of a <code>FileSystemWatcher</code> use
<a href="#workspace.createFileSystemWatcher">createFileSystemWatcher</a>.</p>
</div>

#### Events



<a name="FileSystemWatcher.onDidChange"></a><span class="ts" id=469 data-target="#details-469" data-toggle="collapse"><span class="ident">onDidChange</span><span>: </span><a class="type-ref" href="#Event">Event</a>&lt;<a class="type-ref" href="#Uri">Uri</a>&gt;</span>
<div class="details collapse" id="details-469">
<div class="comment"><p>An event which fires on file/folder change.</p>
</div>
</div>



<a name="FileSystemWatcher.onDidCreate"></a><span class="ts" id=468 data-target="#details-468" data-toggle="collapse"><span class="ident">onDidCreate</span><span>: </span><a class="type-ref" href="#Event">Event</a>&lt;<a class="type-ref" href="#Uri">Uri</a>&gt;</span>
<div class="details collapse" id="details-468">
<div class="comment"><p>An event which fires on file/folder creation.</p>
</div>
</div>



<a name="FileSystemWatcher.onDidDelete"></a><span class="ts" id=470 data-target="#details-470" data-toggle="collapse"><span class="ident">onDidDelete</span><span>: </span><a class="type-ref" href="#Event">Event</a>&lt;<a class="type-ref" href="#Uri">Uri</a>&gt;</span>
<div class="details collapse" id="details-470">
<div class="comment"><p>An event which fires on file/folder deletion.</p>
</div>
</div>

#### Static



<a name="FileSystemWatcher.from"></a><span class="ts" id=472 data-target="#details-472" data-toggle="collapse"><span class="ident">from</span><span>(</span><span>...</span><span class="ident">disposableLikes</span><span>: </span>{dispose: () =&gt; <a class="type-intrinsic">any</a>}[]<span>)</span><span>: </span><a class="type-ref" href="#Disposable">Disposable</a></span>
<div class="details collapse" id="details-472">
<div class="comment"><p>Combine many disposable-likes into one. Use this method
when having objects with a dispose function which are not
instances of Disposable.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="disposableLikes"></a><span class="ts" id=473 data-target="#details-473" data-toggle="collapse"><span>...</span><span class="ident">disposableLikes</span><span>: </span>{dispose: () =&gt; <a class="type-intrinsic">any</a>}[]</span></td><td><div class="comment"><p>Objects that have at least a <code>dispose</code>-function member.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#Disposable">Disposable</a></span></td><td><div class="comment"><p>Returns a new disposable which, upon dispose, will
dispose all provided disposables.</p>
</div></td></tr>
</table>
</div>
</div>

#### Constructors



<a name="FileSystemWatcher.new FileSystemWatcher"></a><span class="ts" id=479 data-target="#details-479" data-toggle="collapse"><span class="ident">new FileSystemWatcher</span><span>(</span><span class="ident">callOnDispose</span><span>: </span><a class="type-ref" href="#Function">Function</a><span>)</span><span>: </span><a class="type-ref" href="#FileSystemWatcher">FileSystemWatcher</a></span>
<div class="details collapse" id="details-479">
<div class="comment"><p>Creates a new Disposable calling the provided function
on dispose.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="callOnDispose"></a><span class="ts" id=480 data-target="#details-480" data-toggle="collapse"><span class="ident">callOnDispose</span><span>: </span><a class="type-ref" href="#Function">Function</a></span></td><td><div class="comment"><p>Function that disposes something.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#FileSystemWatcher">FileSystemWatcher</a></span></td><td><div class="comment"></div></td></tr>
</table>
</div>
</div>

#### Properties



<a name="FileSystemWatcher.ignoreChangeEvents"></a><span class="ts" id=466 data-target="#details-466" data-toggle="collapse"><span class="ident">ignoreChangeEvents</span><span>: </span><a class="type-intrinsic">boolean</a></span>
<div class="details collapse" id="details-466">
<div class="comment"><p>true if this file system watcher has been created such that
it ignores change file system events.</p>
</div>
</div>



<a name="FileSystemWatcher.ignoreCreateEvents"></a><span class="ts" id=465 data-target="#details-465" data-toggle="collapse"><span class="ident">ignoreCreateEvents</span><span>: </span><a class="type-intrinsic">boolean</a></span>
<div class="details collapse" id="details-465">
<div class="comment"><p>true if this file system watcher has been created such that
it ignores creation file system events.</p>
</div>
</div>



<a name="FileSystemWatcher.ignoreDeleteEvents"></a><span class="ts" id=467 data-target="#details-467" data-toggle="collapse"><span class="ident">ignoreDeleteEvents</span><span>: </span><a class="type-intrinsic">boolean</a></span>
<div class="details collapse" id="details-467">
<div class="comment"><p>true if this file system watcher has been created such that
it ignores delete file system events.</p>
</div>
</div>

#### Methods



<a name="FileSystemWatcher.dispose"></a><span class="ts" id=482 data-target="#details-482" data-toggle="collapse"><span class="ident">dispose</span><span>(</span><span>)</span><span>: </span><a class="type-intrinsic">any</a></span>
<div class="details collapse" id="details-482">
<div class="comment"><p>Dispose this object.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-intrinsic">any</a></span></td><td><div class="comment"></div></td></tr>
</table>
</div>
</div>

### <a name="FileType"></a><span class="code-item" id=1467>FileType</span>



<div class="comment"><p>Enumeration of file types. The types <code>File</code> and <code>Directory</code> can also be
a symbolic links, in that use <code>FileType.File | FileType.SymbolicLink</code> and
<code>FileType.Directory | FileType.SymbolicLink</code>.</p>
</div>

#### Enumeration members



<a name="FileType.Directory"></a><span class="ts" id=1470 data-target="#details-1470" data-toggle="collapse"><span class="ident">Directory</span></span>
<div class="details collapse" id="details-1470">
<em>2</em>
</div>



<a name="FileType.File"></a><span class="ts" id=1469 data-target="#details-1469" data-toggle="collapse"><span class="ident">File</span></span>
<div class="details collapse" id="details-1469">
<em>1</em>
</div>



<a name="FileType.SymbolicLink"></a><span class="ts" id=1471 data-target="#details-1471" data-toggle="collapse"><span class="ident">SymbolicLink</span></span>
<div class="details collapse" id="details-1471">
<em>64</em>
</div>



<a name="FileType.Unknown"></a><span class="ts" id=1468 data-target="#details-1468" data-toggle="collapse"><span class="ident">Unknown</span></span>
<div class="details collapse" id="details-1468">
<em>0</em>
</div>

### <a name="FoldingContext"></a><span class="code-item" id=1074>FoldingContext</span>



<div class="comment"><p>Folding context (for future use)</p>
</div>

### <a name="FoldingRange"></a><span class="code-item" id=1061>FoldingRange</span>



<div class="comment"><p>A line based folding range. To be valid, start and end line must a zero or larger and smaller than the number of lines in the document.
Invalid ranges will be ignored.</p>
</div>

#### Constructors



<a name="FoldingRange.new FoldingRange"></a><span class="ts" id=1066 data-target="#details-1066" data-toggle="collapse"><span class="ident">new FoldingRange</span><span>(</span><span class="ident">start</span><span>: </span><a class="type-intrinsic">number</a>, <span class="ident">end</span><span>: </span><a class="type-intrinsic">number</a>, <span class="ident">kind</span><span>?</span><span>: </span><a class="type-ref" href="#FoldingRangeKind">FoldingRangeKind</a><span>)</span><span>: </span><a class="type-ref" href="#FoldingRange">FoldingRange</a></span>
<div class="details collapse" id="details-1066">
<div class="comment"><p>Creates a new folding range.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="start"></a><span class="ts" id=1067 data-target="#details-1067" data-toggle="collapse"><span class="ident">start</span><span>: </span><a class="type-intrinsic">number</a></span></td><td><div class="comment"><p>The start line of the folded range.</p>
</div></td></tr>
<tr><td><a name="end"></a><span class="ts" id=1068 data-target="#details-1068" data-toggle="collapse"><span class="ident">end</span><span>: </span><a class="type-intrinsic">number</a></span></td><td><div class="comment"><p>The end line of the folded range.</p>
</div></td></tr>
<tr><td><a name="kind"></a><span class="ts" id=1069 data-target="#details-1069" data-toggle="collapse"><span class="ident">kind</span><span>?</span><span>: </span><a class="type-ref" href="#FoldingRangeKind">FoldingRangeKind</a></span></td><td><div class="comment"><p>The kind of the folding range.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#FoldingRange">FoldingRange</a></span></td><td><div class="comment"></div></td></tr>
</table>
</div>
</div>

#### Properties



<a name="FoldingRange.end"></a><span class="ts" id=1063 data-target="#details-1063" data-toggle="collapse"><span class="ident">end</span><span>: </span><a class="type-intrinsic">number</a></span>
<div class="details collapse" id="details-1063">
<div class="comment"><p>The zero-based end line of the range to fold. The folded area ends with the line&#39;s last character.
To be valid, the end must be zero or larger and smaller than the number of lines in the document.</p>
</div>
</div>



<a name="FoldingRange.kind"></a><span class="ts" id=1064 data-target="#details-1064" data-toggle="collapse"><span class="ident">kind</span><span>?</span><span>: </span><a class="type-ref" href="#FoldingRangeKind">FoldingRangeKind</a></span>
<div class="details collapse" id="details-1064">
<div class="comment"><p>Describes the <a href="#FoldingRangeKind">Kind</a> of the folding range such as <a href="#FoldingRangeKind.Comment">Comment</a> or
<a href="#FoldingRangeKind.Region">Region</a>. The kind is used to categorize folding ranges and used by commands
like &#39;Fold all comments&#39;. See
<a href="#FoldingRangeKind">FoldingRangeKind</a> for an enumeration of all kinds.
If not set, the range is originated from a syntax element.</p>
</div>
</div>



<a name="FoldingRange.start"></a><span class="ts" id=1062 data-target="#details-1062" data-toggle="collapse"><span class="ident">start</span><span>: </span><a class="type-intrinsic">number</a></span>
<div class="details collapse" id="details-1062">
<div class="comment"><p>The zero-based start line of the range to fold. The folded area starts after the line&#39;s last character.
To be valid, the end must be zero or larger and smaller than the number of lines in the document.</p>
</div>
</div>

### <a name="FoldingRangeKind"></a><span class="code-item" id=1070>FoldingRangeKind</span>



<div class="comment"><p>An enumeration of specific folding range kinds. The kind is an optional field of a <a href="#FoldingRange">FoldingRange</a>
and is used to distinguish specific folding ranges such as ranges originated from comments. The kind is used by commands like
<code>Fold all comments</code> or <code>Fold all regions</code>.
If the kind is not set on the range, the range originated from a syntax element other than comments, imports or region markers.</p>
</div>

#### Enumeration members



<a name="FoldingRangeKind.Comment"></a><span class="ts" id=1071 data-target="#details-1071" data-toggle="collapse"><span class="ident">Comment</span></span>
<div class="details collapse" id="details-1071">
<em>1</em>
</div>



<a name="FoldingRangeKind.Imports"></a><span class="ts" id=1072 data-target="#details-1072" data-toggle="collapse"><span class="ident">Imports</span></span>
<div class="details collapse" id="details-1072">
<em>2</em>
</div>



<a name="FoldingRangeKind.Region"></a><span class="ts" id=1073 data-target="#details-1073" data-toggle="collapse"><span class="ident">Region</span></span>
<div class="details collapse" id="details-1073">
<em>3</em>
</div>

### <a name="FoldingRangeProvider"></a><span class="code-item" id=1075>FoldingRangeProvider</span>



<div class="comment"><p>The folding range provider interface defines the contract between extensions and
<a href="https://code.visualstudio.com/docs/editor/codebasics#_folding">Folding</a> in the editor.</p>
</div>

#### Methods



<a name="FoldingRangeProvider.provideFoldingRanges"></a><span class="ts" id=1077 data-target="#details-1077" data-toggle="collapse"><span class="ident">provideFoldingRanges</span><span>(</span><span class="ident">document</span><span>: </span><a class="type-ref" href="#TextDocument">TextDocument</a>, <span class="ident">context</span><span>: </span><a class="type-ref" href="#FoldingContext">FoldingContext</a>, <span class="ident">token</span><span>: </span><a class="type-ref" href="#CancellationToken">CancellationToken</a><span>)</span><span>: </span><a class="type-ref" href="#ProviderResult">ProviderResult</a>&lt;<a class="type-ref" href="#FoldingRange">FoldingRange</a>[]&gt;</span>
<div class="details collapse" id="details-1077">
<div class="comment"><p>Returns a list of folding ranges or null and undefined if the provider
does not want to participate or was cancelled.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="document"></a><span class="ts" id=1078 data-target="#details-1078" data-toggle="collapse"><span class="ident">document</span><span>: </span><a class="type-ref" href="#TextDocument">TextDocument</a></span></td><td><div class="comment"><p>The document in which the command was invoked.</p>
</div></td></tr>
<tr><td><a name="context"></a><span class="ts" id=1079 data-target="#details-1079" data-toggle="collapse"><span class="ident">context</span><span>: </span><a class="type-ref" href="#FoldingContext">FoldingContext</a></span></td><td><div class="comment"><p>Additional context information (for future use)</p>
</div></td></tr>
<tr><td><a name="token"></a><span class="ts" id=1080 data-target="#details-1080" data-toggle="collapse"><span class="ident">token</span><span>: </span><a class="type-ref" href="#CancellationToken">CancellationToken</a></span></td><td><div class="comment"><p>A cancellation token.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#ProviderResult">ProviderResult</a>&lt;<a class="type-ref" href="#FoldingRange">FoldingRange</a>[]&gt;</span></td><td><div class="comment"></div></td></tr>
</table>
</div>
</div>

### <a name="FormattingOptions"></a><span class="code-item" id=874>FormattingOptions</span>



<div class="comment"><p>Value-object describing what options formatting should use.</p>
</div>

#### Properties



<a name="FormattingOptions.insertSpaces"></a><span class="ts" id=876 data-target="#details-876" data-toggle="collapse"><span class="ident">insertSpaces</span><span>: </span><a class="type-intrinsic">boolean</a></span>
<div class="details collapse" id="details-876">
<div class="comment"><p>Prefer spaces over tabs.</p>
</div>
</div>



<a name="FormattingOptions.tabSize"></a><span class="ts" id=875 data-target="#details-875" data-toggle="collapse"><span class="ident">tabSize</span><span>: </span><a class="type-intrinsic">number</a></span>
<div class="details collapse" id="details-875">
<div class="comment"><p>Size of a tab in spaces.</p>
</div>
</div>

### <a name="FunctionBreakpoint"></a><span class="code-item" id=1973>FunctionBreakpoint</span>



<div class="comment"><p>A breakpoint specified by a function name.</p>
</div>

#### Constructors



<a name="FunctionBreakpoint.new FunctionBreakpoint"></a><span class="ts" id=1976 data-target="#details-1976" data-toggle="collapse"><span class="ident">new FunctionBreakpoint</span><span>(</span><span class="ident">functionName</span><span>: </span><a class="type-intrinsic">string</a>, <span class="ident">enabled</span><span>?</span><span>: </span><a class="type-intrinsic">boolean</a>, <span class="ident">condition</span><span>?</span><span>: </span><a class="type-intrinsic">string</a>, <span class="ident">hitCondition</span><span>?</span><span>: </span><a class="type-intrinsic">string</a>, <span class="ident">logMessage</span><span>?</span><span>: </span><a class="type-intrinsic">string</a><span>)</span><span>: </span><a class="type-ref" href="#FunctionBreakpoint">FunctionBreakpoint</a></span>
<div class="details collapse" id="details-1976">
<div class="comment"><p>Create a new function breakpoint.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="functionName"></a><span class="ts" id=1977 data-target="#details-1977" data-toggle="collapse"><span class="ident">functionName</span><span>: </span><a class="type-intrinsic">string</a></span></td><td><div class="comment"></div></td></tr>
<tr><td><a name="enabled"></a><span class="ts" id=1978 data-target="#details-1978" data-toggle="collapse"><span class="ident">enabled</span><span>?</span><span>: </span><a class="type-intrinsic">boolean</a></span></td><td><div class="comment"></div></td></tr>
<tr><td><a name="condition"></a><span class="ts" id=1979 data-target="#details-1979" data-toggle="collapse"><span class="ident">condition</span><span>?</span><span>: </span><a class="type-intrinsic">string</a></span></td><td><div class="comment"></div></td></tr>
<tr><td><a name="hitCondition"></a><span class="ts" id=1980 data-target="#details-1980" data-toggle="collapse"><span class="ident">hitCondition</span><span>?</span><span>: </span><a class="type-intrinsic">string</a></span></td><td><div class="comment"></div></td></tr>
<tr><td><a name="logMessage"></a><span class="ts" id=1981 data-target="#details-1981" data-toggle="collapse"><span class="ident">logMessage</span><span>?</span><span>: </span><a class="type-intrinsic">string</a></span></td><td><div class="comment"></div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#FunctionBreakpoint">FunctionBreakpoint</a></span></td><td><div class="comment"></div></td></tr>
</table>
</div>
</div>

#### Properties



<a name="FunctionBreakpoint.condition"></a><span class="ts" id=1984 data-target="#details-1984" data-toggle="collapse"><span class="ident">condition</span><span>?</span><span>: </span><a class="type-intrinsic">string</a></span>
<div class="details collapse" id="details-1984">
<div class="comment"><p>An optional expression for conditional breakpoints.</p>
</div>
</div>



<a name="FunctionBreakpoint.enabled"></a><span class="ts" id=1983 data-target="#details-1983" data-toggle="collapse"><span class="ident">enabled</span><span>: </span><a class="type-intrinsic">boolean</a></span>
<div class="details collapse" id="details-1983">
<div class="comment"><p>Is breakpoint enabled.</p>
</div>
</div>



<a name="FunctionBreakpoint.functionName"></a><span class="ts" id=1974 data-target="#details-1974" data-toggle="collapse"><span class="ident">functionName</span><span>: </span><a class="type-intrinsic">string</a></span>
<div class="details collapse" id="details-1974">
<div class="comment"><p>The name of the function to which this breakpoint is attached.</p>
</div>
</div>



<a name="FunctionBreakpoint.hitCondition"></a><span class="ts" id=1985 data-target="#details-1985" data-toggle="collapse"><span class="ident">hitCondition</span><span>?</span><span>: </span><a class="type-intrinsic">string</a></span>
<div class="details collapse" id="details-1985">
<div class="comment"><p>An optional expression that controls how many hits of the breakpoint are ignored.</p>
</div>
</div>



<a name="FunctionBreakpoint.id"></a><span class="ts" id=1982 data-target="#details-1982" data-toggle="collapse"><span class="ident">id</span><span>: </span><a class="type-intrinsic">string</a></span>
<div class="details collapse" id="details-1982">
<div class="comment"><p>The unique ID of the breakpoint.</p>
</div>
</div>



<a name="FunctionBreakpoint.logMessage"></a><span class="ts" id=1986 data-target="#details-1986" data-toggle="collapse"><span class="ident">logMessage</span><span>?</span><span>: </span><a class="type-intrinsic">string</a></span>
<div class="details collapse" id="details-1986">
<div class="comment"><p>An optional message that gets logged when this breakpoint is hit. Embedded expressions within {} are interpolated by the debug adapter.</p>
</div>
</div>

### <a name="GlobPattern"></a><span class="code-item" id=1988>GlobPattern</span>



<div class="comment"><p>A file glob pattern to match file paths against. This can either be a glob pattern string
(like <code>**/*.{ts,js}</code> or <code>*.{ts,js}</code>) or a <a href="#RelativePattern">relative pattern</a>.</p>
<p>Glob patterns can have the following syntax:</p>
<ul>
<li><code>*</code> to match one or more characters in a path segment</li>
<li><code>?</code> to match on one character in a path segment</li>
<li><code>**</code> to match any number of path segments, including none</li>
<li><code>{}</code> to group conditions (e.g. <code>**/*.{ts,js}</code> matches all TypeScript and JavaScript files)</li>
<li><code>[]</code> to declare a range of characters to match in a path segment (e.g., <code>example.[0-9]</code> to match on <code>example.0</code>, <code>example.1</code>, …)</li>
<li><code>[!...]</code> to negate a range of characters to match in a path segment (e.g., <code>example.[!0-9]</code> to match on <code>example.a</code>, <code>example.b</code>, but not <code>example.0</code>)</li>
</ul>
</div>



<a name="GlobPattern"></a><span class="ts" id=1988 data-target="#details-1988" data-toggle="collapse"><span class="ident">GlobPattern</span><span>: </span><a class="type-intrinsic">string</a> &#124; <a class="type-ref" href="#RelativePattern">RelativePattern</a></span>

### <a name="Hover"></a><span class="code-item" id=649>Hover</span>



<div class="comment"><p>A hover represents additional information for a symbol or word. Hovers are
rendered in a tooltip-like widget.</p>
</div>

#### Constructors



<a name="Hover.new Hover"></a><span class="ts" id=653 data-target="#details-653" data-toggle="collapse"><span class="ident">new Hover</span><span>(</span><span class="ident">contents</span><span>: </span><a class="type-ref" href="#MarkedString">MarkedString</a> &#124; <a class="type-ref" href="#MarkedString">MarkedString</a>[], <span class="ident">range</span><span>?</span><span>: </span><a class="type-ref" href="#Range">Range</a><span>)</span><span>: </span><a class="type-ref" href="#Hover">Hover</a></span>
<div class="details collapse" id="details-653">
<div class="comment"><p>Creates a new hover object.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="contents"></a><span class="ts" id=654 data-target="#details-654" data-toggle="collapse"><span class="ident">contents</span><span>: </span><a class="type-ref" href="#MarkedString">MarkedString</a> &#124; <a class="type-ref" href="#MarkedString">MarkedString</a>[]</span></td><td><div class="comment"><p>The contents of the hover.</p>
</div></td></tr>
<tr><td><a name="range"></a><span class="ts" id=655 data-target="#details-655" data-toggle="collapse"><span class="ident">range</span><span>?</span><span>: </span><a class="type-ref" href="#Range">Range</a></span></td><td><div class="comment"><p>The range to which the hover applies.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#Hover">Hover</a></span></td><td><div class="comment"></div></td></tr>
</table>
</div>
</div>

#### Properties



<a name="Hover.contents"></a><span class="ts" id=650 data-target="#details-650" data-toggle="collapse"><span class="ident">contents</span><span>: </span><a class="type-ref" href="#MarkedString">MarkedString</a>[]</span>
<div class="details collapse" id="details-650">
<div class="comment"><p>The contents of this hover.</p>
</div>
</div>



<a name="Hover.range"></a><span class="ts" id=651 data-target="#details-651" data-toggle="collapse"><span class="ident">range</span><span>?</span><span>: </span><a class="type-ref" href="#Range">Range</a></span>
<div class="details collapse" id="details-651">
<div class="comment"><p>The range to which this hover applies. When missing, the
editor will use the range at the current position or the
current position itself.</p>
</div>
</div>

### <a name="HoverProvider"></a><span class="code-item" id=656>HoverProvider</span>



<div class="comment"><p>The hover provider interface defines the contract between extensions and
the <a href="https://code.visualstudio.com/docs/editor/intellisense">hover</a>-feature.</p>
</div>

#### Methods



<a name="HoverProvider.provideHover"></a><span class="ts" id=658 data-target="#details-658" data-toggle="collapse"><span class="ident">provideHover</span><span>(</span><span class="ident">document</span><span>: </span><a class="type-ref" href="#TextDocument">TextDocument</a>, <span class="ident">position</span><span>: </span><a class="type-ref" href="#Position">Position</a>, <span class="ident">token</span><span>: </span><a class="type-ref" href="#CancellationToken">CancellationToken</a><span>)</span><span>: </span><a class="type-ref" href="#ProviderResult">ProviderResult</a>&lt;<a class="type-ref" href="#Hover">Hover</a>&gt;</span>
<div class="details collapse" id="details-658">
<div class="comment"><p>Provide a hover for the given position and document. Multiple hovers at the same
position will be merged by the editor. A hover can have a range which defaults
to the word range at the position when omitted.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="document"></a><span class="ts" id=659 data-target="#details-659" data-toggle="collapse"><span class="ident">document</span><span>: </span><a class="type-ref" href="#TextDocument">TextDocument</a></span></td><td><div class="comment"><p>The document in which the command was invoked.</p>
</div></td></tr>
<tr><td><a name="position"></a><span class="ts" id=660 data-target="#details-660" data-toggle="collapse"><span class="ident">position</span><span>: </span><a class="type-ref" href="#Position">Position</a></span></td><td><div class="comment"><p>The position at which the command was invoked.</p>
</div></td></tr>
<tr><td><a name="token"></a><span class="ts" id=661 data-target="#details-661" data-toggle="collapse"><span class="ident">token</span><span>: </span><a class="type-ref" href="#CancellationToken">CancellationToken</a></span></td><td><div class="comment"><p>A cancellation token.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#ProviderResult">ProviderResult</a>&lt;<a class="type-ref" href="#Hover">Hover</a>&gt;</span></td><td><div class="comment"><p>A hover or a thenable that resolves to such. The lack of a result can be
signaled by returning <code>undefined</code> or <code>null</code>.</p>
</div></td></tr>
</table>
</div>
</div>

### <a name="ImplementationProvider"></a><span class="code-item" id=615>ImplementationProvider</span>



<div class="comment"><p>The implementation provider interface defines the contract between extensions and
the go to implementation feature.</p>
</div>

#### Methods



<a name="ImplementationProvider.provideImplementation"></a><span class="ts" id=617 data-target="#details-617" data-toggle="collapse"><span class="ident">provideImplementation</span><span>(</span><span class="ident">document</span><span>: </span><a class="type-ref" href="#TextDocument">TextDocument</a>, <span class="ident">position</span><span>: </span><a class="type-ref" href="#Position">Position</a>, <span class="ident">token</span><span>: </span><a class="type-ref" href="#CancellationToken">CancellationToken</a><span>)</span><span>: </span><a class="type-ref" href="#ProviderResult">ProviderResult</a>&lt;<a class="type-ref" href="#Definition">Definition</a> &#124; <a class="type-ref" href="#DefinitionLink">DefinitionLink</a>[]&gt;</span>
<div class="details collapse" id="details-617">
<div class="comment"><p>Provide the implementations of the symbol at the given position and document.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="document"></a><span class="ts" id=618 data-target="#details-618" data-toggle="collapse"><span class="ident">document</span><span>: </span><a class="type-ref" href="#TextDocument">TextDocument</a></span></td><td><div class="comment"><p>The document in which the command was invoked.</p>
</div></td></tr>
<tr><td><a name="position"></a><span class="ts" id=619 data-target="#details-619" data-toggle="collapse"><span class="ident">position</span><span>: </span><a class="type-ref" href="#Position">Position</a></span></td><td><div class="comment"><p>The position at which the command was invoked.</p>
</div></td></tr>
<tr><td><a name="token"></a><span class="ts" id=620 data-target="#details-620" data-toggle="collapse"><span class="ident">token</span><span>: </span><a class="type-ref" href="#CancellationToken">CancellationToken</a></span></td><td><div class="comment"><p>A cancellation token.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#ProviderResult">ProviderResult</a>&lt;<a class="type-ref" href="#Definition">Definition</a> &#124; <a class="type-ref" href="#DefinitionLink">DefinitionLink</a>[]&gt;</span></td><td><div class="comment"><p>A definition or a thenable that resolves to such. The lack of a result can be
signaled by returning <code>undefined</code> or <code>null</code>.</p>
</div></td></tr>
</table>
</div>
</div>

### <a name="IndentAction"></a><span class="code-item" id=1089>IndentAction</span>



<div class="comment"><p>Describes what to do with the indentation when pressing Enter.</p>
</div>

#### Enumeration members



<a name="IndentAction.Indent"></a><span class="ts" id=1091 data-target="#details-1091" data-toggle="collapse"><span class="ident">Indent</span></span>
<div class="details collapse" id="details-1091">
<em>1</em>
</div>



<a name="IndentAction.IndentOutdent"></a><span class="ts" id=1092 data-target="#details-1092" data-toggle="collapse"><span class="ident">IndentOutdent</span></span>
<div class="details collapse" id="details-1092">
<em>2</em>
</div>



<a name="IndentAction.None"></a><span class="ts" id=1090 data-target="#details-1090" data-toggle="collapse"><span class="ident">None</span></span>
<div class="details collapse" id="details-1090">
<em>0</em>
</div>



<a name="IndentAction.Outdent"></a><span class="ts" id=1093 data-target="#details-1093" data-toggle="collapse"><span class="ident">Outdent</span></span>
<div class="details collapse" id="details-1093">
<em>3</em>
</div>

### <a name="IndentationRule"></a><span class="code-item" id=1084>IndentationRule</span>



<div class="comment"><p>Describes indentation rules for a language.</p>
</div>

#### Properties



<a name="IndentationRule.decreaseIndentPattern"></a><span class="ts" id=1085 data-target="#details-1085" data-toggle="collapse"><span class="ident">decreaseIndentPattern</span><span>: </span><a class="type-ref" href="#RegExp">RegExp</a></span>
<div class="details collapse" id="details-1085">
<div class="comment"><p>If a line matches this pattern, then all the lines after it should be unindented once (until another rule matches).</p>
</div>
</div>



<a name="IndentationRule.increaseIndentPattern"></a><span class="ts" id=1086 data-target="#details-1086" data-toggle="collapse"><span class="ident">increaseIndentPattern</span><span>: </span><a class="type-ref" href="#RegExp">RegExp</a></span>
<div class="details collapse" id="details-1086">
<div class="comment"><p>If a line matches this pattern, then all the lines after it should be indented once (until another rule matches).</p>
</div>
</div>



<a name="IndentationRule.indentNextLinePattern"></a><span class="ts" id=1087 data-target="#details-1087" data-toggle="collapse"><span class="ident">indentNextLinePattern</span><span>?</span><span>: </span><a class="type-ref" href="#RegExp">RegExp</a></span>
<div class="details collapse" id="details-1087">
<div class="comment"><p>If a line matches this pattern, then <strong>only the next line</strong> after it should be indented once.</p>
</div>
</div>



<a name="IndentationRule.unIndentedLinePattern"></a><span class="ts" id=1088 data-target="#details-1088" data-toggle="collapse"><span class="ident">unIndentedLinePattern</span><span>?</span><span>: </span><a class="type-ref" href="#RegExp">RegExp</a></span>
<div class="details collapse" id="details-1088">
<div class="comment"><p>If a line matches this pattern, then its indentation should not be changed and it should not be evaluated against the other rules.</p>
</div>
</div>

### <a name="InputBox"></a><span class="code-item" id=1748>InputBox</span>



<div class="comment"><p>A concrete <a href="#QuickInput">QuickInput</a> to let the user input a text value.</p>
<p>Note that in many cases the more convenient <a href="#window.showInputBox">window.showInputBox</a>
is easier to use. <a href="#window.createInputBox">window.createInputBox</a> should be used
when <a href="#window.showInputBox">window.showInputBox</a> does not offer the required flexibility.</p>
</div>

#### Events



<a name="InputBox.onDidAccept"></a><span class="ts" id=1753 data-target="#details-1753" data-toggle="collapse"><span class="ident">onDidAccept</span><span>: </span><a class="type-ref" href="#Event">Event</a>&lt;<a class="type-intrinsic">void</a>&gt;</span>
<div class="details collapse" id="details-1753">
<div class="comment"><p>An event signaling when the user indicated acceptance of the input value.</p>
</div>
</div>



<a name="InputBox.onDidChangeValue"></a><span class="ts" id=1752 data-target="#details-1752" data-toggle="collapse"><span class="ident">onDidChangeValue</span><span>: </span><a class="type-ref" href="#Event">Event</a>&lt;<a class="type-intrinsic">string</a>&gt;</span>
<div class="details collapse" id="details-1752">
<div class="comment"><p>An event signaling when the value has changed.</p>
</div>
</div>



<a name="InputBox.onDidHide"></a><span class="ts" id=1768 data-target="#details-1768" data-toggle="collapse"><span class="ident">onDidHide</span><span>: </span><a class="type-ref" href="#Event">Event</a>&lt;<a class="type-intrinsic">void</a>&gt;</span>
<div class="details collapse" id="details-1768">
<div class="comment"><p>An event signaling when this input UI is hidden.</p>
<p>There are several reasons why this UI might have to be hidden and
the extension will be notified through <a href="#QuickInput.onDidHide">QuickInput.onDidHide</a>.
(Examples include: an explict call to <a href="#QuickInput.hide">QuickInput.hide</a>,
the user pressing Esc, some other input UI opening, etc.)</p>
</div>
</div>



<a name="InputBox.onDidTriggerButton"></a><span class="ts" id=1755 data-target="#details-1755" data-toggle="collapse"><span class="ident">onDidTriggerButton</span><span>: </span><a class="type-ref" href="#Event">Event</a>&lt;<a class="type-ref" href="#QuickInputButton">QuickInputButton</a>&gt;</span>
<div class="details collapse" id="details-1755">
<div class="comment"><p>An event signaling when a button was triggered.</p>
</div>
</div>

#### Properties



<a name="InputBox.busy"></a><span class="ts" id=1762 data-target="#details-1762" data-toggle="collapse"><span class="ident">busy</span><span>: </span><a class="type-intrinsic">boolean</a></span>
<div class="details collapse" id="details-1762">
<div class="comment"><p>If the UI should show a progress indicator. Defaults to false.</p>
<p>Change this to true, e.g., while loading more data or validating
user input.</p>
</div>
</div>



<a name="InputBox.buttons"></a><span class="ts" id=1754 data-target="#details-1754" data-toggle="collapse"><span class="ident">buttons</span><span>: </span><a class="type-ref" href="#ReadonlyArray">ReadonlyArray</a>&lt;<a class="type-ref" href="#QuickInputButton">QuickInputButton</a>&gt;</span>
<div class="details collapse" id="details-1754">
<div class="comment"><p>Buttons for actions in the UI.</p>
</div>
</div>



<a name="InputBox.enabled"></a><span class="ts" id=1761 data-target="#details-1761" data-toggle="collapse"><span class="ident">enabled</span><span>: </span><a class="type-intrinsic">boolean</a></span>
<div class="details collapse" id="details-1761">
<div class="comment"><p>If the UI should allow for user input. Defaults to true.</p>
<p>Change this to false, e.g., while validating user input or
loading data for the next step in user input.</p>
</div>
</div>



<a name="InputBox.ignoreFocusOut"></a><span class="ts" id=1763 data-target="#details-1763" data-toggle="collapse"><span class="ident">ignoreFocusOut</span><span>: </span><a class="type-intrinsic">boolean</a></span>
<div class="details collapse" id="details-1763">
<div class="comment"><p>If the UI should stay open even when loosing UI focus. Defaults to false.</p>
</div>
</div>



<a name="InputBox.password"></a><span class="ts" id=1751 data-target="#details-1751" data-toggle="collapse"><span class="ident">password</span><span>: </span><a class="type-intrinsic">boolean</a></span>
<div class="details collapse" id="details-1751">
<div class="comment"><p>If the input value should be hidden. Defaults to false.</p>
</div>
</div>



<a name="InputBox.placeholder"></a><span class="ts" id=1750 data-target="#details-1750" data-toggle="collapse"><span class="ident">placeholder</span><span>: </span><a class="type-intrinsic">string</a> &#124; <a class="type-intrinsic">undefined</a></span>
<div class="details collapse" id="details-1750">
<div class="comment"><p>Optional placeholder in the filter text.</p>
</div>
</div>



<a name="InputBox.prompt"></a><span class="ts" id=1756 data-target="#details-1756" data-toggle="collapse"><span class="ident">prompt</span><span>: </span><a class="type-intrinsic">string</a> &#124; <a class="type-intrinsic">undefined</a></span>
<div class="details collapse" id="details-1756">
<div class="comment"><p>An optional prompt text providing some ask or explanation to the user.</p>
</div>
</div>



<a name="InputBox.step"></a><span class="ts" id=1759 data-target="#details-1759" data-toggle="collapse"><span class="ident">step</span><span>: </span><a class="type-intrinsic">number</a> &#124; <a class="type-intrinsic">undefined</a></span>
<div class="details collapse" id="details-1759">
<div class="comment"><p>An optional current step count.</p>
</div>
</div>



<a name="InputBox.title"></a><span class="ts" id=1758 data-target="#details-1758" data-toggle="collapse"><span class="ident">title</span><span>: </span><a class="type-intrinsic">string</a> &#124; <a class="type-intrinsic">undefined</a></span>
<div class="details collapse" id="details-1758">
<div class="comment"><p>An optional title.</p>
</div>
</div>



<a name="InputBox.totalSteps"></a><span class="ts" id=1760 data-target="#details-1760" data-toggle="collapse"><span class="ident">totalSteps</span><span>: </span><a class="type-intrinsic">number</a> &#124; <a class="type-intrinsic">undefined</a></span>
<div class="details collapse" id="details-1760">
<div class="comment"><p>An optional total step count.</p>
</div>
</div>



<a name="InputBox.validationMessage"></a><span class="ts" id=1757 data-target="#details-1757" data-toggle="collapse"><span class="ident">validationMessage</span><span>: </span><a class="type-intrinsic">string</a> &#124; <a class="type-intrinsic">undefined</a></span>
<div class="details collapse" id="details-1757">
<div class="comment"><p>An optional validation message indicating a problem with the current input value.</p>
</div>
</div>



<a name="InputBox.value"></a><span class="ts" id=1749 data-target="#details-1749" data-toggle="collapse"><span class="ident">value</span><span>: </span><a class="type-intrinsic">string</a></span>
<div class="details collapse" id="details-1749">
<div class="comment"><p>Current input value.</p>
</div>
</div>

#### Methods



<a name="InputBox.dispose"></a><span class="ts" id=1770 data-target="#details-1770" data-toggle="collapse"><span class="ident">dispose</span><span>(</span><span>)</span><span>: </span><a class="type-intrinsic">void</a></span>
<div class="details collapse" id="details-1770">
<div class="comment"><p>Dispose of this input UI and any associated resources. If it is still
visible, it is first hidden. After this call the input UI is no longer
functional and no additional methods or properties on it should be
accessed. Instead a new input UI should be created.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-intrinsic">void</a></span></td><td><div class="comment"></div></td></tr>
</table>
</div>
</div>



<a name="InputBox.hide"></a><span class="ts" id=1767 data-target="#details-1767" data-toggle="collapse"><span class="ident">hide</span><span>(</span><span>)</span><span>: </span><a class="type-intrinsic">void</a></span>
<div class="details collapse" id="details-1767">
<div class="comment"><p>Hides this input UI. This will also fire an <a href="#QuickInput.onDidHide">QuickInput.onDidHide</a>
event.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-intrinsic">void</a></span></td><td><div class="comment"></div></td></tr>
</table>
</div>
</div>



<a name="InputBox.show"></a><span class="ts" id=1765 data-target="#details-1765" data-toggle="collapse"><span class="ident">show</span><span>(</span><span>)</span><span>: </span><a class="type-intrinsic">void</a></span>
<div class="details collapse" id="details-1765">
<div class="comment"><p>Makes the input UI visible in its current configuration. Any other input
UI will first fire an <a href="#QuickInput.onDidHide">QuickInput.onDidHide</a> event.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-intrinsic">void</a></span></td><td><div class="comment"></div></td></tr>
</table>
</div>
</div>

### <a name="InputBoxOptions"></a><span class="code-item" id=529>InputBoxOptions</span>



<div class="comment"><p>Options to configure the behavior of the input box UI.</p>
</div>

#### Properties



<a name="InputBoxOptions.ignoreFocusOut"></a><span class="ts" id=535 data-target="#details-535" data-toggle="collapse"><span class="ident">ignoreFocusOut</span><span>?</span><span>: </span><a class="type-intrinsic">boolean</a></span>
<div class="details collapse" id="details-535">
<div class="comment"><p>Set to <code>true</code> to keep the input box open when focus moves to another part of the editor or to another window.</p>
</div>
</div>



<a name="InputBoxOptions.password"></a><span class="ts" id=534 data-target="#details-534" data-toggle="collapse"><span class="ident">password</span><span>?</span><span>: </span><a class="type-intrinsic">boolean</a></span>
<div class="details collapse" id="details-534">
<div class="comment"><p>Set to <code>true</code> to show a password prompt that will not show the typed value.</p>
</div>
</div>



<a name="InputBoxOptions.placeHolder"></a><span class="ts" id=533 data-target="#details-533" data-toggle="collapse"><span class="ident">placeHolder</span><span>?</span><span>: </span><a class="type-intrinsic">string</a></span>
<div class="details collapse" id="details-533">
<div class="comment"><p>An optional string to show as place holder in the input box to guide the user what to type.</p>
</div>
</div>



<a name="InputBoxOptions.prompt"></a><span class="ts" id=532 data-target="#details-532" data-toggle="collapse"><span class="ident">prompt</span><span>?</span><span>: </span><a class="type-intrinsic">string</a></span>
<div class="details collapse" id="details-532">
<div class="comment"><p>The text to display underneath the input box.</p>
</div>
</div>



<a name="InputBoxOptions.value"></a><span class="ts" id=530 data-target="#details-530" data-toggle="collapse"><span class="ident">value</span><span>?</span><span>: </span><a class="type-intrinsic">string</a></span>
<div class="details collapse" id="details-530">
<div class="comment"><p>The value to prefill in the input box.</p>
</div>
</div>



<a name="InputBoxOptions.valueSelection"></a><span class="ts" id=531 data-target="#details-531" data-toggle="collapse"><span class="ident">valueSelection</span><span>?</span><span>: </span>[<a class="type-intrinsic">number</a>, <a class="type-intrinsic">number</a>]</span>
<div class="details collapse" id="details-531">
<div class="comment"><p>Selection of the prefilled <a href="#InputBoxOptions.value"><code>value</code></a>. Defined as tuple of two number where the
first is the inclusive start index and the second the exclusive end index. When <code>undefined</code> the whole
word will be selected, when empty (start equals end) only the cursor will be set,
otherwise the defined range will be selected.</p>
</div>
</div>

#### Methods



<a name="InputBoxOptions.validateInput"></a><span class="ts" id=537 data-target="#details-537" data-toggle="collapse"><span class="ident">validateInput</span><span>(</span><span class="ident">value</span><span>: </span><a class="type-intrinsic">string</a><span>)</span><span>: </span><a class="type-intrinsic">string</a> &#124; <a class="type-intrinsic">undefined</a> &#124; <a class="type-intrinsic">null</a> &#124; <a class="type-ref" href="#Thenable">Thenable</a>&lt;<a class="type-intrinsic">string</a> &#124; <a class="type-intrinsic">undefined</a> &#124; <a class="type-intrinsic">null</a>&gt;</span>
<div class="details collapse" id="details-537">
<div class="comment"><p>An optional function that will be called to validate input and to give a hint
to the user.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="value"></a><span class="ts" id=538 data-target="#details-538" data-toggle="collapse"><span class="ident">value</span><span>: </span><a class="type-intrinsic">string</a></span></td><td><div class="comment"><p>The current value of the input box.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-intrinsic">string</a> &#124; <a class="type-intrinsic">undefined</a> &#124; <a class="type-intrinsic">null</a> &#124; <a class="type-ref" href="#Thenable">Thenable</a>&lt;<a class="type-intrinsic">string</a> &#124; <a class="type-intrinsic">undefined</a> &#124; <a class="type-intrinsic">null</a>&gt;</span></td><td><div class="comment"><p>A human readable string which is presented as diagnostic message.
Return <code>undefined</code>, <code>null</code>, or the empty string when &#39;value&#39; is valid.</p>
</div></td></tr>
</table>
</div>
</div>

### <a name="LanguageConfiguration"></a><span class="code-item" id=1102>LanguageConfiguration</span>



<div class="comment"><p>The language configuration interfaces defines the contract between extensions
and various editor features, like automatic bracket insertion, automatic indentation etc.</p>
</div>

#### Properties



<a name="LanguageConfiguration.__characterPairSupport"></a><span class="ts" id=1117 data-target="#details-1117" data-toggle="collapse"><span class="ident">__characterPairSupport</span><span>?</span><span>: </span>{autoClosingPairs: {close: <a class="type-intrinsic">string</a>, notIn: <a class="type-intrinsic">string</a>[], open: <a class="type-intrinsic">string</a>}[]}</span>
<div class="details collapse" id="details-1117">
<div class="comment"><p><strong>Deprecated</strong> Do not use.</p>
<ul>
<li><em>deprecated</em> - * Use the autoClosingPairs property in the language configuration file instead.</li>
</ul>
</div>
</div>



<a name="LanguageConfiguration.__electricCharacterSupport"></a><span class="ts" id=1108 data-target="#details-1108" data-toggle="collapse"><span class="ident">__electricCharacterSupport</span><span>?</span><span>: </span>{brackets: <a class="type-intrinsic">any</a>, docComment: {close: <a class="type-intrinsic">string</a>, lineStart: <a class="type-intrinsic">string</a>, open: <a class="type-intrinsic">string</a>, scope: <a class="type-intrinsic">string</a>}}</span>
<div class="details collapse" id="details-1108">
<div class="comment"><p><strong>Deprecated</strong> Do not use.</p>
<ul>
<li><em>deprecated</em> - Will be replaced by a better API soon.</li>
</ul>
</div>
</div>



<a name="LanguageConfiguration.brackets"></a><span class="ts" id=1104 data-target="#details-1104" data-toggle="collapse"><span class="ident">brackets</span><span>?</span><span>: </span><a class="type-ref" href="#CharacterPair">CharacterPair</a>[]</span>
<div class="details collapse" id="details-1104">
<div class="comment"><p>The language&#39;s brackets.
This configuration implicitly affects pressing Enter around these brackets.</p>
</div>
</div>



<a name="LanguageConfiguration.comments"></a><span class="ts" id=1103 data-target="#details-1103" data-toggle="collapse"><span class="ident">comments</span><span>?</span><span>: </span><a class="type-ref" href="#CommentRule">CommentRule</a></span>
<div class="details collapse" id="details-1103">
<div class="comment"><p>The language&#39;s comment settings.</p>
</div>
</div>



<a name="LanguageConfiguration.indentationRules"></a><span class="ts" id=1106 data-target="#details-1106" data-toggle="collapse"><span class="ident">indentationRules</span><span>?</span><span>: </span><a class="type-ref" href="#IndentationRule">IndentationRule</a></span>
<div class="details collapse" id="details-1106">
<div class="comment"><p>The language&#39;s indentation settings.</p>
</div>
</div>



<a name="LanguageConfiguration.onEnterRules"></a><span class="ts" id=1107 data-target="#details-1107" data-toggle="collapse"><span class="ident">onEnterRules</span><span>?</span><span>: </span><a class="type-ref" href="#OnEnterRule">OnEnterRule</a>[]</span>
<div class="details collapse" id="details-1107">
<div class="comment"><p>The language&#39;s rules to be evaluated when pressing Enter.</p>
</div>
</div>



<a name="LanguageConfiguration.wordPattern"></a><span class="ts" id=1105 data-target="#details-1105" data-toggle="collapse"><span class="ident">wordPattern</span><span>?</span><span>: </span><a class="type-ref" href="#RegExp">RegExp</a></span>
<div class="details collapse" id="details-1105">
<div class="comment"><p>The language&#39;s word definition.
If the language supports Unicode identifiers (e.g. JavaScript), it is preferable
to provide a word definition that uses exclusion of known separators.
e.g.: A regex that matches anything except known separators (and dot is allowed to occur in a floating point number):
  /(-?\d<em>.\d\w</em>)|([^`~!\@@#\%\^\&amp;*()-\=+[{]}\|\;\:\&#39;\&quot;\,.\&lt;>\/\?\s]+)/g</p>
</div>
</div>

### <a name="Location"></a><span class="code-item" id=1157>Location</span>



<div class="comment"><p>Represents a location inside a resource, such as a line
inside a text file.</p>
</div>

#### Constructors



<a name="Location.new Location"></a><span class="ts" id=1161 data-target="#details-1161" data-toggle="collapse"><span class="ident">new Location</span><span>(</span><span class="ident">uri</span><span>: </span><a class="type-ref" href="#Uri">Uri</a>, <span class="ident">rangeOrPosition</span><span>: </span><a class="type-ref" href="#Range">Range</a> &#124; <a class="type-ref" href="#Position">Position</a><span>)</span><span>: </span><a class="type-ref" href="#Location">Location</a></span>
<div class="details collapse" id="details-1161">
<div class="comment"><p>Creates a new location object.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="uri"></a><span class="ts" id=1162 data-target="#details-1162" data-toggle="collapse"><span class="ident">uri</span><span>: </span><a class="type-ref" href="#Uri">Uri</a></span></td><td><div class="comment"><p>The resource identifier.</p>
</div></td></tr>
<tr><td><a name="rangeOrPosition"></a><span class="ts" id=1163 data-target="#details-1163" data-toggle="collapse"><span class="ident">rangeOrPosition</span><span>: </span><a class="type-ref" href="#Range">Range</a> &#124; <a class="type-ref" href="#Position">Position</a></span></td><td><div class="comment"><p>The range or position. Positions will be converted to an empty range.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#Location">Location</a></span></td><td><div class="comment"></div></td></tr>
</table>
</div>
</div>

#### Properties



<a name="Location.range"></a><span class="ts" id=1159 data-target="#details-1159" data-toggle="collapse"><span class="ident">range</span><span>: </span><a class="type-ref" href="#Range">Range</a></span>
<div class="details collapse" id="details-1159">
<div class="comment"><p>The document range of this location.</p>
</div>
</div>



<a name="Location.uri"></a><span class="ts" id=1158 data-target="#details-1158" data-toggle="collapse"><span class="ident">uri</span><span>: </span><a class="type-ref" href="#Uri">Uri</a></span>
<div class="details collapse" id="details-1158">
<div class="comment"><p>The resource identifier of this location.</p>
</div>
</div>

### <a name="LocationLink"></a><span class="code-item" id=1164>LocationLink</span>



<div class="comment"><p>Represents the connection of two locations. Provides additional metadata over normal <a href="#Location">locations</a>,
including an origin range.</p>
</div>

#### Properties



<a name="LocationLink.originSelectionRange"></a><span class="ts" id=1165 data-target="#details-1165" data-toggle="collapse"><span class="ident">originSelectionRange</span><span>?</span><span>: </span><a class="type-ref" href="#Range">Range</a></span>
<div class="details collapse" id="details-1165">
<div class="comment"><p>Span of the origin of this link.</p>
<p>Used as the underlined span for mouse definition hover. Defaults to the word range at
the definition position.</p>
</div>
</div>



<a name="LocationLink.targetRange"></a><span class="ts" id=1167 data-target="#details-1167" data-toggle="collapse"><span class="ident">targetRange</span><span>: </span><a class="type-ref" href="#Range">Range</a></span>
<div class="details collapse" id="details-1167">
<div class="comment"><p>The full target range of this link.</p>
</div>
</div>



<a name="LocationLink.targetSelectionRange"></a><span class="ts" id=1168 data-target="#details-1168" data-toggle="collapse"><span class="ident">targetSelectionRange</span><span>?</span><span>: </span><a class="type-ref" href="#Range">Range</a></span>
<div class="details collapse" id="details-1168">
<div class="comment"><p>The span of this link.</p>
</div>
</div>



<a name="LocationLink.targetUri"></a><span class="ts" id=1166 data-target="#details-1166" data-toggle="collapse"><span class="ident">targetUri</span><span>: </span><a class="type-ref" href="#Uri">Uri</a></span>
<div class="details collapse" id="details-1166">
<div class="comment"><p>The target resource identifier of this link.</p>
</div>
</div>

### <a name="MarkdownString"></a><span class="code-item" id=633>MarkdownString</span>



<div class="comment"><p>The MarkdownString represents human readable text that supports formatting via the
markdown syntax. Standard markdown is supported, also tables, but no embedded html.</p>
</div>

#### Constructors



<a name="MarkdownString.new MarkdownString"></a><span class="ts" id=637 data-target="#details-637" data-toggle="collapse"><span class="ident">new MarkdownString</span><span>(</span><span class="ident">value</span><span>?</span><span>: </span><a class="type-intrinsic">string</a><span>)</span><span>: </span><a class="type-ref" href="#MarkdownString">MarkdownString</a></span>
<div class="details collapse" id="details-637">
<div class="comment"><p>Creates a new markdown string with the given value.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="value"></a><span class="ts" id=638 data-target="#details-638" data-toggle="collapse"><span class="ident">value</span><span>?</span><span>: </span><a class="type-intrinsic">string</a></span></td><td><div class="comment"><p>Optional, initial value.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#MarkdownString">MarkdownString</a></span></td><td><div class="comment"></div></td></tr>
</table>
</div>
</div>

#### Properties



<a name="MarkdownString.isTrusted"></a><span class="ts" id=635 data-target="#details-635" data-toggle="collapse"><span class="ident">isTrusted</span><span>?</span><span>: </span><a class="type-intrinsic">boolean</a></span>
<div class="details collapse" id="details-635">
<div class="comment"><p>Indicates that this markdown string is from a trusted source. Only <em>trusted</em>
markdown supports links that execute commands, e.g. <code>[Run it](command:myCommandId)</code>.</p>
</div>
</div>



<a name="MarkdownString.value"></a><span class="ts" id=634 data-target="#details-634" data-toggle="collapse"><span class="ident">value</span><span>: </span><a class="type-intrinsic">string</a></span>
<div class="details collapse" id="details-634">
<div class="comment"><p>The markdown string.</p>
</div>
</div>

#### Methods



<a name="MarkdownString.appendCodeblock"></a><span class="ts" id=646 data-target="#details-646" data-toggle="collapse"><span class="ident">appendCodeblock</span><span>(</span><span class="ident">value</span><span>: </span><a class="type-intrinsic">string</a>, <span class="ident">language</span><span>?</span><span>: </span><a class="type-intrinsic">string</a><span>)</span><span>: </span><a class="type-ref" href="#MarkdownString">MarkdownString</a></span>
<div class="details collapse" id="details-646">
<div class="comment"><p>Appends the given string as codeblock using the provided language.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="value"></a><span class="ts" id=647 data-target="#details-647" data-toggle="collapse"><span class="ident">value</span><span>: </span><a class="type-intrinsic">string</a></span></td><td><div class="comment"><p>A code snippet.</p>
</div></td></tr>
<tr><td><a name="language"></a><span class="ts" id=648 data-target="#details-648" data-toggle="collapse"><span class="ident">language</span><span>?</span><span>: </span><a class="type-intrinsic">string</a></span></td><td><div class="comment"><p>An optional <a href="#languages.getLanguages">language identifier</a>.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#MarkdownString">MarkdownString</a></span></td><td><div class="comment"></div></td></tr>
</table>
</div>
</div>



<a name="MarkdownString.appendMarkdown"></a><span class="ts" id=643 data-target="#details-643" data-toggle="collapse"><span class="ident">appendMarkdown</span><span>(</span><span class="ident">value</span><span>: </span><a class="type-intrinsic">string</a><span>)</span><span>: </span><a class="type-ref" href="#MarkdownString">MarkdownString</a></span>
<div class="details collapse" id="details-643">
<div class="comment"><p>Appends the given string &#39;as is&#39; to this markdown string.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="value"></a><span class="ts" id=644 data-target="#details-644" data-toggle="collapse"><span class="ident">value</span><span>: </span><a class="type-intrinsic">string</a></span></td><td><div class="comment"><p>Markdown string.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#MarkdownString">MarkdownString</a></span></td><td><div class="comment"></div></td></tr>
</table>
</div>
</div>



<a name="MarkdownString.appendText"></a><span class="ts" id=640 data-target="#details-640" data-toggle="collapse"><span class="ident">appendText</span><span>(</span><span class="ident">value</span><span>: </span><a class="type-intrinsic">string</a><span>)</span><span>: </span><a class="type-ref" href="#MarkdownString">MarkdownString</a></span>
<div class="details collapse" id="details-640">
<div class="comment"><p>Appends and escapes the given string to this markdown string.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="value"></a><span class="ts" id=641 data-target="#details-641" data-toggle="collapse"><span class="ident">value</span><span>: </span><a class="type-intrinsic">string</a></span></td><td><div class="comment"><p>Plain text.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#MarkdownString">MarkdownString</a></span></td><td><div class="comment"></div></td></tr>
</table>
</div>
</div>

### <a name="MarkedString"></a><span class="code-item" id=1995>MarkedString</span>



<div class="comment"><p><del>MarkedString can be used to render human readable text. It is either a markdown string
or a code-block that provides a language and a code snippet. Note that
markdown strings will be sanitized - that means html will be escaped.</del></p>
<ul>
<li><em>deprecated</em> - This type is deprecated, please use <a href="#MarkdownString"><code>MarkdownString</code></a> instead.</li>
</ul>
</div>



<a name="MarkedString"></a><span class="ts" id=1995 data-target="#details-1995" data-toggle="collapse"><span class="ident">MarkedString</span><span>: </span><a class="type-ref" href="#MarkdownString">MarkdownString</a> &#124; <a class="type-intrinsic">string</a> &#124; {language: <a class="type-intrinsic">string</a>, value: <a class="type-intrinsic">string</a>}</span>

### <a name="Memento"></a><span class="code-item" id=1317>Memento</span>



<div class="comment"><p>A memento represents a storage utility. It can store and retrieve
values.</p>
</div>

#### Methods



<a name="Memento.get"></a><span class="ts" id=1319 data-target="#details-1319" data-toggle="collapse"><span class="ident">get</span><span>&lt;</span>T<span>&gt;</span><span>(</span><span class="ident">key</span><span>: </span><a class="type-intrinsic">string</a><span>)</span><span>: </span><a class="type-intrinsic">T</a> &#124; <a class="type-intrinsic">undefined</a></span>
<div class="details collapse" id="details-1319">
<div class="comment"><p>Return a value.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="key"></a><span class="ts" id=1321 data-target="#details-1321" data-toggle="collapse"><span class="ident">key</span><span>: </span><a class="type-intrinsic">string</a></span></td><td><div class="comment"><p>A string.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-intrinsic">T</a> &#124; <a class="type-intrinsic">undefined</a></span></td><td><div class="comment"><p>The stored value or <code>undefined</code>.</p>
</div></td></tr>
</table>
</div>
</div>



<a name="Memento.get"></a><span class="ts" id=1322 data-target="#details-1322" data-toggle="collapse"><span class="ident">get</span><span>&lt;</span>T<span>&gt;</span><span>(</span><span class="ident">key</span><span>: </span><a class="type-intrinsic">string</a>, <span class="ident">defaultValue</span><span>: </span><a class="type-intrinsic">T</a><span>)</span><span>: </span><a class="type-intrinsic">T</a></span>
<div class="details collapse" id="details-1322">
<div class="comment"><p>Return a value.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="key"></a><span class="ts" id=1324 data-target="#details-1324" data-toggle="collapse"><span class="ident">key</span><span>: </span><a class="type-intrinsic">string</a></span></td><td><div class="comment"><p>A string.</p>
</div></td></tr>
<tr><td><a name="defaultValue"></a><span class="ts" id=1325 data-target="#details-1325" data-toggle="collapse"><span class="ident">defaultValue</span><span>: </span><a class="type-intrinsic">T</a></span></td><td><div class="comment"><p>A value that should be returned when there is no
value (<code>undefined</code>) with the given key.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-intrinsic">T</a></span></td><td><div class="comment"><p>The stored value or the defaultValue.</p>
</div></td></tr>
</table>
</div>
</div>



<a name="Memento.update"></a><span class="ts" id=1327 data-target="#details-1327" data-toggle="collapse"><span class="ident">update</span><span>(</span><span class="ident">key</span><span>: </span><a class="type-intrinsic">string</a>, <span class="ident">value</span><span>: </span><a class="type-intrinsic">any</a><span>)</span><span>: </span><a class="type-ref" href="#Thenable">Thenable</a>&lt;<a class="type-intrinsic">void</a>&gt;</span>
<div class="details collapse" id="details-1327">
<div class="comment"><p>Store a value. The value must be JSON-stringifyable.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="key"></a><span class="ts" id=1328 data-target="#details-1328" data-toggle="collapse"><span class="ident">key</span><span>: </span><a class="type-intrinsic">string</a></span></td><td><div class="comment"><p>A string.</p>
</div></td></tr>
<tr><td><a name="value"></a><span class="ts" id=1329 data-target="#details-1329" data-toggle="collapse"><span class="ident">value</span><span>: </span><a class="type-intrinsic">any</a></span></td><td><div class="comment"><p>A value. MUST not contain cyclic references.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#Thenable">Thenable</a>&lt;<a class="type-intrinsic">void</a>&gt;</span></td><td><div class="comment"></div></td></tr>
</table>
</div>
</div>

### <a name="MessageItem"></a><span class="code-item" id=524>MessageItem</span>



<div class="comment"><p>Represents an action that is shown with an information, warning, or
error message.</p>
<ul>
<li><em>see</em> - <a href="#window.showInformationMessage">showInformationMessage</a></li>
</ul>
<ul>
<li><em>see</em> - <a href="#window.showWarningMessage">showWarningMessage</a></li>
</ul>
<ul>
<li><em>see</em> - <a href="#window.showErrorMessage">showErrorMessage</a></li>
</ul>
</div>

#### Properties



<a name="MessageItem.isCloseAffordance"></a><span class="ts" id=526 data-target="#details-526" data-toggle="collapse"><span class="ident">isCloseAffordance</span><span>?</span><span>: </span><a class="type-intrinsic">boolean</a></span>
<div class="details collapse" id="details-526">
<div class="comment"><p>A hint for modal dialogs that the item should be triggered
when the user cancels the dialog (e.g. by pressing the ESC
key).</p>
<p>Note: this option is ignored for non-modal messages.</p>
</div>
</div>



<a name="MessageItem.title"></a><span class="ts" id=525 data-target="#details-525" data-toggle="collapse"><span class="ident">title</span><span>: </span><a class="type-intrinsic">string</a></span>
<div class="details collapse" id="details-525">
<div class="comment"><p>A short title like &#39;Retry&#39;, &#39;Open Log&#39; etc.</p>
</div>
</div>

### <a name="MessageOptions"></a><span class="code-item" id=527>MessageOptions</span>



<div class="comment"><p>Options to configure the behavior of the message.</p>
<ul>
<li><em>see</em> - <a href="#window.showInformationMessage">showInformationMessage</a></li>
</ul>
<ul>
<li><em>see</em> - <a href="#window.showWarningMessage">showWarningMessage</a></li>
</ul>
<ul>
<li><em>see</em> - <a href="#window.showErrorMessage">showErrorMessage</a></li>
</ul>
</div>

#### Properties



<a name="MessageOptions.modal"></a><span class="ts" id=528 data-target="#details-528" data-toggle="collapse"><span class="ident">modal</span><span>?</span><span>: </span><a class="type-intrinsic">boolean</a></span>
<div class="details collapse" id="details-528">
<div class="comment"><p>Indicates that this message should be modal.</p>
</div>
</div>

### <a name="OnEnterRule"></a><span class="code-item" id=1098>OnEnterRule</span>



<div class="comment"><p>Describes a rule to be evaluated when pressing Enter.</p>
</div>

#### Properties



<a name="OnEnterRule.action"></a><span class="ts" id=1101 data-target="#details-1101" data-toggle="collapse"><span class="ident">action</span><span>: </span><a class="type-ref" href="#EnterAction">EnterAction</a></span>
<div class="details collapse" id="details-1101">
<div class="comment"><p>The action to execute.</p>
</div>
</div>



<a name="OnEnterRule.afterText"></a><span class="ts" id=1100 data-target="#details-1100" data-toggle="collapse"><span class="ident">afterText</span><span>?</span><span>: </span><a class="type-ref" href="#RegExp">RegExp</a></span>
<div class="details collapse" id="details-1100">
<div class="comment"><p>This rule will only execute if the text after the cursor matches this regular expression.</p>
</div>
</div>



<a name="OnEnterRule.beforeText"></a><span class="ts" id=1099 data-target="#details-1099" data-toggle="collapse"><span class="ident">beforeText</span><span>: </span><a class="type-ref" href="#RegExp">RegExp</a></span>
<div class="details collapse" id="details-1099">
<div class="comment"><p>This rule will only execute if the text before the cursor matches this regular expression.</p>
</div>
</div>

### <a name="OnTypeFormattingEditProvider"></a><span class="code-item" id=892>OnTypeFormattingEditProvider</span>



<div class="comment"><p>The document formatting provider interface defines the contract between extensions and
the formatting-feature.</p>
</div>

#### Methods



<a name="OnTypeFormattingEditProvider.provideOnTypeFormattingEdits"></a><span class="ts" id=894 data-target="#details-894" data-toggle="collapse"><span class="ident">provideOnTypeFormattingEdits</span><span>(</span><span class="ident">document</span><span>: </span><a class="type-ref" href="#TextDocument">TextDocument</a>, <span class="ident">position</span><span>: </span><a class="type-ref" href="#Position">Position</a>, <span class="ident">ch</span><span>: </span><a class="type-intrinsic">string</a>, <span class="ident">options</span><span>: </span><a class="type-ref" href="#FormattingOptions">FormattingOptions</a>, <span class="ident">token</span><span>: </span><a class="type-ref" href="#CancellationToken">CancellationToken</a><span>)</span><span>: </span><a class="type-ref" href="#ProviderResult">ProviderResult</a>&lt;<a class="type-ref" href="#TextEdit">TextEdit</a>[]&gt;</span>
<div class="details collapse" id="details-894">
<div class="comment"><p>Provide formatting edits after a character has been typed.</p>
<p>The given position and character should hint to the provider
what range the position to expand to, like find the matching <code>{</code>
when <code>}</code> has been entered.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="document"></a><span class="ts" id=895 data-target="#details-895" data-toggle="collapse"><span class="ident">document</span><span>: </span><a class="type-ref" href="#TextDocument">TextDocument</a></span></td><td><div class="comment"><p>The document in which the command was invoked.</p>
</div></td></tr>
<tr><td><a name="position"></a><span class="ts" id=896 data-target="#details-896" data-toggle="collapse"><span class="ident">position</span><span>: </span><a class="type-ref" href="#Position">Position</a></span></td><td><div class="comment"><p>The position at which the command was invoked.</p>
</div></td></tr>
<tr><td><a name="ch"></a><span class="ts" id=897 data-target="#details-897" data-toggle="collapse"><span class="ident">ch</span><span>: </span><a class="type-intrinsic">string</a></span></td><td><div class="comment"><p>The character that has been typed.</p>
</div></td></tr>
<tr><td><a name="options"></a><span class="ts" id=898 data-target="#details-898" data-toggle="collapse"><span class="ident">options</span><span>: </span><a class="type-ref" href="#FormattingOptions">FormattingOptions</a></span></td><td><div class="comment"><p>Options controlling formatting.</p>
</div></td></tr>
<tr><td><a name="token"></a><span class="ts" id=899 data-target="#details-899" data-toggle="collapse"><span class="ident">token</span><span>: </span><a class="type-ref" href="#CancellationToken">CancellationToken</a></span></td><td><div class="comment"><p>A cancellation token.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#ProviderResult">ProviderResult</a>&lt;<a class="type-ref" href="#TextEdit">TextEdit</a>[]&gt;</span></td><td><div class="comment"><p>A set of text edits or a thenable that resolves to such. The lack of a result can be
signaled by returning <code>undefined</code>, <code>null</code>, or an empty array.</p>
</div></td></tr>
</table>
</div>
</div>

### <a name="OpenDialogOptions"></a><span class="code-item" id=507>OpenDialogOptions</span>



<div class="comment"><p>Options to configure the behaviour of a file open dialog.</p>
<ul>
<li>Note 1: A dialog can select files, folders, or both. This is not true for Windows
which enforces to open either files or folder, but <em>not both</em>.</li>
<li>Note 2: Explicitly setting <code>canSelectFiles</code> and <code>canSelectFolders</code> to <code>false</code> is futile
and the editor then silently adjusts the options to select files.</li>
</ul>
</div>

#### Properties



<a name="OpenDialogOptions.canSelectFiles"></a><span class="ts" id=510 data-target="#details-510" data-toggle="collapse"><span class="ident">canSelectFiles</span><span>?</span><span>: </span><a class="type-intrinsic">boolean</a></span>
<div class="details collapse" id="details-510">
<div class="comment"><p>Allow to select files, defaults to <code>true</code>.</p>
</div>
</div>



<a name="OpenDialogOptions.canSelectFolders"></a><span class="ts" id=511 data-target="#details-511" data-toggle="collapse"><span class="ident">canSelectFolders</span><span>?</span><span>: </span><a class="type-intrinsic">boolean</a></span>
<div class="details collapse" id="details-511">
<div class="comment"><p>Allow to select folders, defaults to <code>false</code>.</p>
</div>
</div>



<a name="OpenDialogOptions.canSelectMany"></a><span class="ts" id=512 data-target="#details-512" data-toggle="collapse"><span class="ident">canSelectMany</span><span>?</span><span>: </span><a class="type-intrinsic">boolean</a></span>
<div class="details collapse" id="details-512">
<div class="comment"><p>Allow to select many files or folders.</p>
</div>
</div>



<a name="OpenDialogOptions.defaultUri"></a><span class="ts" id=508 data-target="#details-508" data-toggle="collapse"><span class="ident">defaultUri</span><span>?</span><span>: </span><a class="type-ref" href="#Uri">Uri</a></span>
<div class="details collapse" id="details-508">
<div class="comment"><p>The resource the dialog shows when opened.</p>
</div>
</div>



<a name="OpenDialogOptions.filters"></a><span class="ts" id=513 data-target="#details-513" data-toggle="collapse"><span class="ident">filters</span><span>?</span><span>: </span></span>
<div class="details collapse" id="details-513">
<div class="comment"><p>A set of file filters that are used by the dialog. Each entry is a human readable label,
like &quot;TypeScript&quot;, and an array of extensions, e.g.</p>

<pre><code class="lang-ts">{
    &#39;Images&#39;: [&#39;png&#39;, &#39;jpg&#39;]
    &#39;TypeScript&#39;: [&#39;ts&#39;, &#39;tsx&#39;]
}
</code></pre>
</div>
</div>



<a name="OpenDialogOptions.openLabel"></a><span class="ts" id=509 data-target="#details-509" data-toggle="collapse"><span class="ident">openLabel</span><span>?</span><span>: </span><a class="type-intrinsic">string</a></span>
<div class="details collapse" id="details-509">
<div class="comment"><p>A human-readable string for the open button.</p>
</div>
</div>

### <a name="OutputChannel"></a><span class="code-item" id=1240>OutputChannel</span>



<div class="comment"><p>An output channel is a container for readonly textual information.</p>
<p>To get an instance of an <code>OutputChannel</code> use
<a href="#window.createOutputChannel">createOutputChannel</a>.</p>
</div>

#### Properties



<a name="OutputChannel.name"></a><span class="ts" id=1241 data-target="#details-1241" data-toggle="collapse"><span class="ident">name</span><span>: </span><a class="type-intrinsic">string</a></span>
<div class="details collapse" id="details-1241">
<div class="comment"><p>The human-readable name of this output channel.</p>
</div>
</div>

#### Methods



<a name="OutputChannel.append"></a><span class="ts" id=1243 data-target="#details-1243" data-toggle="collapse"><span class="ident">append</span><span>(</span><span class="ident">value</span><span>: </span><a class="type-intrinsic">string</a><span>)</span><span>: </span><a class="type-intrinsic">void</a></span>
<div class="details collapse" id="details-1243">
<div class="comment"><p>Append the given value to the channel.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="value"></a><span class="ts" id=1244 data-target="#details-1244" data-toggle="collapse"><span class="ident">value</span><span>: </span><a class="type-intrinsic">string</a></span></td><td><div class="comment"><p>A string, falsy values will not be printed.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-intrinsic">void</a></span></td><td><div class="comment"></div></td></tr>
</table>
</div>
</div>



<a name="OutputChannel.appendLine"></a><span class="ts" id=1246 data-target="#details-1246" data-toggle="collapse"><span class="ident">appendLine</span><span>(</span><span class="ident">value</span><span>: </span><a class="type-intrinsic">string</a><span>)</span><span>: </span><a class="type-intrinsic">void</a></span>
<div class="details collapse" id="details-1246">
<div class="comment"><p>Append the given value and a line feed character
to the channel.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="value"></a><span class="ts" id=1247 data-target="#details-1247" data-toggle="collapse"><span class="ident">value</span><span>: </span><a class="type-intrinsic">string</a></span></td><td><div class="comment"><p>A string, falsy values will be printed.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-intrinsic">void</a></span></td><td><div class="comment"></div></td></tr>
</table>
</div>
</div>



<a name="OutputChannel.clear"></a><span class="ts" id=1249 data-target="#details-1249" data-toggle="collapse"><span class="ident">clear</span><span>(</span><span>)</span><span>: </span><a class="type-intrinsic">void</a></span>
<div class="details collapse" id="details-1249">
<div class="comment"><p>Removes all output from the channel.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-intrinsic">void</a></span></td><td><div class="comment"></div></td></tr>
</table>
</div>
</div>



<a name="OutputChannel.dispose"></a><span class="ts" id=1259 data-target="#details-1259" data-toggle="collapse"><span class="ident">dispose</span><span>(</span><span>)</span><span>: </span><a class="type-intrinsic">void</a></span>
<div class="details collapse" id="details-1259">
<div class="comment"><p>Dispose and free associated resources.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-intrinsic">void</a></span></td><td><div class="comment"></div></td></tr>
</table>
</div>
</div>



<a name="OutputChannel.hide"></a><span class="ts" id=1257 data-target="#details-1257" data-toggle="collapse"><span class="ident">hide</span><span>(</span><span>)</span><span>: </span><a class="type-intrinsic">void</a></span>
<div class="details collapse" id="details-1257">
<div class="comment"><p>Hide this channel from the UI.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-intrinsic">void</a></span></td><td><div class="comment"></div></td></tr>
</table>
</div>
</div>



<a name="OutputChannel.show"></a><span class="ts" id=1251 data-target="#details-1251" data-toggle="collapse"><span class="ident">show</span><span>(</span><span class="ident">preserveFocus</span><span>?</span><span>: </span><a class="type-intrinsic">boolean</a><span>)</span><span>: </span><a class="type-intrinsic">void</a></span>
<div class="details collapse" id="details-1251">
<div class="comment"><p>Reveal this channel in the UI.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="preserveFocus"></a><span class="ts" id=1252 data-target="#details-1252" data-toggle="collapse"><span class="ident">preserveFocus</span><span>?</span><span>: </span><a class="type-intrinsic">boolean</a></span></td><td><div class="comment"><p>When <code>true</code> the channel will not take focus.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-intrinsic">void</a></span></td><td><div class="comment"></div></td></tr>
</table>
</div>
</div>



<a name="OutputChannel.show"></a><span class="ts" id=1253 data-target="#details-1253" data-toggle="collapse"><span class="ident">show</span><span>(</span><span class="ident">column</span><span>?</span><span>: </span><a class="type-ref" href="#ViewColumn">ViewColumn</a>, <span class="ident">preserveFocus</span><span>?</span><span>: </span><a class="type-intrinsic">boolean</a><span>)</span><span>: </span><a class="type-intrinsic">void</a></span>
<div class="details collapse" id="details-1253">
<div class="comment"><p><del>Reveal this channel in the UI.</del></p>
<ul>
<li><em>deprecated</em> - Use the overload with just one parameter (<code>show(preserveFocus?: boolean): void</code>).</li>
</ul>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="column"></a><span class="ts" id=1254 data-target="#details-1254" data-toggle="collapse"><span class="ident">column</span><span>?</span><span>: </span><a class="type-ref" href="#ViewColumn">ViewColumn</a></span></td><td><div class="comment"><p>This argument is <strong>deprecated</strong> and will be ignored.</p>
</div></td></tr>
<tr><td><a name="preserveFocus"></a><span class="ts" id=1255 data-target="#details-1255" data-toggle="collapse"><span class="ident">preserveFocus</span><span>?</span><span>: </span><a class="type-intrinsic">boolean</a></span></td><td><div class="comment"><p>When <code>true</code> the channel will not take focus.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-intrinsic">void</a></span></td><td><div class="comment"></div></td></tr>
</table>
</div>
</div>

### <a name="OverviewRulerLane"></a><span class="code-item" id=232>OverviewRulerLane</span>



<div class="comment"><p>Represents different positions for rendering a decoration in an <a href="#DecorationRenderOptions.overviewRulerLane">overview ruler</a>.
The overview ruler supports three lanes.</p>
</div>

#### Enumeration members



<a name="OverviewRulerLane.Center"></a><span class="ts" id=234 data-target="#details-234" data-toggle="collapse"><span class="ident">Center</span></span>
<div class="details collapse" id="details-234">
<em>2</em>
</div>



<a name="OverviewRulerLane.Full"></a><span class="ts" id=236 data-target="#details-236" data-toggle="collapse"><span class="ident">Full</span></span>
<div class="details collapse" id="details-236">
<em>7</em>
</div>



<a name="OverviewRulerLane.Left"></a><span class="ts" id=233 data-target="#details-233" data-toggle="collapse"><span class="ident">Left</span></span>
<div class="details collapse" id="details-233">
<em>1</em>
</div>



<a name="OverviewRulerLane.Right"></a><span class="ts" id=235 data-target="#details-235" data-toggle="collapse"><span class="ident">Right</span></span>
<div class="details collapse" id="details-235">
<em>4</em>
</div>

### <a name="ParameterInformation"></a><span class="code-item" id=900>ParameterInformation</span>



<div class="comment"><p>Represents a parameter of a callable-signature. A parameter can
have a label and a doc-comment.</p>
</div>

#### Constructors



<a name="ParameterInformation.new ParameterInformation"></a><span class="ts" id=904 data-target="#details-904" data-toggle="collapse"><span class="ident">new ParameterInformation</span><span>(</span><span class="ident">label</span><span>: </span><a class="type-intrinsic">string</a> &#124; [<a class="type-intrinsic">number</a>, <a class="type-intrinsic">number</a>], <span class="ident">documentation</span><span>?</span><span>: </span><a class="type-intrinsic">string</a> &#124; <a class="type-ref" href="#MarkdownString">MarkdownString</a><span>)</span><span>: </span><a class="type-ref" href="#ParameterInformation">ParameterInformation</a></span>
<div class="details collapse" id="details-904">
<div class="comment"><p>Creates a new parameter information object.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="label"></a><span class="ts" id=905 data-target="#details-905" data-toggle="collapse"><span class="ident">label</span><span>: </span><a class="type-intrinsic">string</a> &#124; [<a class="type-intrinsic">number</a>, <a class="type-intrinsic">number</a>]</span></td><td><div class="comment"><p>A label string or inclusive start and exclusive end offsets within its containing signature label.</p>
</div></td></tr>
<tr><td><a name="documentation"></a><span class="ts" id=906 data-target="#details-906" data-toggle="collapse"><span class="ident">documentation</span><span>?</span><span>: </span><a class="type-intrinsic">string</a> &#124; <a class="type-ref" href="#MarkdownString">MarkdownString</a></span></td><td><div class="comment"><p>A doc string.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#ParameterInformation">ParameterInformation</a></span></td><td><div class="comment"></div></td></tr>
</table>
</div>
</div>

#### Properties



<a name="ParameterInformation.documentation"></a><span class="ts" id=902 data-target="#details-902" data-toggle="collapse"><span class="ident">documentation</span><span>?</span><span>: </span><a class="type-intrinsic">string</a> &#124; <a class="type-ref" href="#MarkdownString">MarkdownString</a></span>
<div class="details collapse" id="details-902">
<div class="comment"><p>The human-readable doc-comment of this signature. Will be shown
in the UI but can be omitted.</p>
</div>
</div>



<a name="ParameterInformation.label"></a><span class="ts" id=901 data-target="#details-901" data-toggle="collapse"><span class="ident">label</span><span>: </span><a class="type-intrinsic">string</a> &#124; [<a class="type-intrinsic">number</a>, <a class="type-intrinsic">number</a>]</span>
<div class="details collapse" id="details-901">
<div class="comment"><p>The label of this signature.</p>
<p>Either a string or inclusive start and exclusive end offsets within its containing
<a href="#SignatureInformation.label">signature label</a>. <em>Note</em>: A label of type string must be
a substring of its containing signature information&#39;s <a href="#SignatureInformation.label">label</a>.</p>
</div>
</div>

### <a name="Position"></a><span class="code-item" id=74>Position</span>



<div class="comment"><p>Represents a line and character position, such as
the position of the cursor.</p>
<p>Position objects are <strong>immutable</strong>. Use the <a href="#Position.with">with</a> or
<a href="#Position.translate">translate</a> methods to derive new positions
from an existing position.</p>
</div>

#### Constructors



<a name="Position.new Position"></a><span class="ts" id=78 data-target="#details-78" data-toggle="collapse"><span class="ident">new Position</span><span>(</span><span class="ident">line</span><span>: </span><a class="type-intrinsic">number</a>, <span class="ident">character</span><span>: </span><a class="type-intrinsic">number</a><span>)</span><span>: </span><a class="type-ref" href="#Position">Position</a></span>
<div class="details collapse" id="details-78">
<div class="comment"></div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="line"></a><span class="ts" id=79 data-target="#details-79" data-toggle="collapse"><span class="ident">line</span><span>: </span><a class="type-intrinsic">number</a></span></td><td><div class="comment"><p>A zero-based line value.</p>
</div></td></tr>
<tr><td><a name="character"></a><span class="ts" id=80 data-target="#details-80" data-toggle="collapse"><span class="ident">character</span><span>: </span><a class="type-intrinsic">number</a></span></td><td><div class="comment"><p>A zero-based character value.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#Position">Position</a></span></td><td><div class="comment"></div></td></tr>
</table>
</div>
</div>

#### Properties



<a name="Position.character"></a><span class="ts" id=76 data-target="#details-76" data-toggle="collapse"><span class="ident">character</span><span>: </span><a class="type-intrinsic">number</a></span>
<div class="details collapse" id="details-76">
<div class="comment"><p>The zero-based character value.</p>
</div>
</div>



<a name="Position.line"></a><span class="ts" id=75 data-target="#details-75" data-toggle="collapse"><span class="ident">line</span><span>: </span><a class="type-intrinsic">number</a></span>
<div class="details collapse" id="details-75">
<div class="comment"><p>The zero-based line value.</p>
</div>
</div>

#### Methods



<a name="Position.compareTo"></a><span class="ts" id=97 data-target="#details-97" data-toggle="collapse"><span class="ident">compareTo</span><span>(</span><span class="ident">other</span><span>: </span><a class="type-ref" href="#Position">Position</a><span>)</span><span>: </span><a class="type-intrinsic">number</a></span>
<div class="details collapse" id="details-97">
<div class="comment"><p>Compare this to <code>other</code>.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="other"></a><span class="ts" id=98 data-target="#details-98" data-toggle="collapse"><span class="ident">other</span><span>: </span><a class="type-ref" href="#Position">Position</a></span></td><td><div class="comment"><p>A position.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-intrinsic">number</a></span></td><td><div class="comment"><p>A number smaller than zero if this position is before the given position,
a number greater than zero if this position is after the given position, or zero when
this and the given position are equal.</p>
</div></td></tr>
</table>
</div>
</div>



<a name="Position.isAfter"></a><span class="ts" id=88 data-target="#details-88" data-toggle="collapse"><span class="ident">isAfter</span><span>(</span><span class="ident">other</span><span>: </span><a class="type-ref" href="#Position">Position</a><span>)</span><span>: </span><a class="type-intrinsic">boolean</a></span>
<div class="details collapse" id="details-88">
<div class="comment"><p>Check if this position is after <code>other</code>.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="other"></a><span class="ts" id=89 data-target="#details-89" data-toggle="collapse"><span class="ident">other</span><span>: </span><a class="type-ref" href="#Position">Position</a></span></td><td><div class="comment"><p>A position.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-intrinsic">boolean</a></span></td><td><div class="comment"><p><code>true</code> if position is on a greater line
or on the same line on a greater character.</p>
</div></td></tr>
</table>
</div>
</div>



<a name="Position.isAfterOrEqual"></a><span class="ts" id=91 data-target="#details-91" data-toggle="collapse"><span class="ident">isAfterOrEqual</span><span>(</span><span class="ident">other</span><span>: </span><a class="type-ref" href="#Position">Position</a><span>)</span><span>: </span><a class="type-intrinsic">boolean</a></span>
<div class="details collapse" id="details-91">
<div class="comment"><p>Check if this position is after or equal to <code>other</code>.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="other"></a><span class="ts" id=92 data-target="#details-92" data-toggle="collapse"><span class="ident">other</span><span>: </span><a class="type-ref" href="#Position">Position</a></span></td><td><div class="comment"><p>A position.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-intrinsic">boolean</a></span></td><td><div class="comment"><p><code>true</code> if position is on a greater line
or on the same line on a greater or equal character.</p>
</div></td></tr>
</table>
</div>
</div>



<a name="Position.isBefore"></a><span class="ts" id=82 data-target="#details-82" data-toggle="collapse"><span class="ident">isBefore</span><span>(</span><span class="ident">other</span><span>: </span><a class="type-ref" href="#Position">Position</a><span>)</span><span>: </span><a class="type-intrinsic">boolean</a></span>
<div class="details collapse" id="details-82">
<div class="comment"><p>Check if this position is before <code>other</code>.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="other"></a><span class="ts" id=83 data-target="#details-83" data-toggle="collapse"><span class="ident">other</span><span>: </span><a class="type-ref" href="#Position">Position</a></span></td><td><div class="comment"><p>A position.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-intrinsic">boolean</a></span></td><td><div class="comment"><p><code>true</code> if position is on a smaller line
or on the same line on a smaller character.</p>
</div></td></tr>
</table>
</div>
</div>



<a name="Position.isBeforeOrEqual"></a><span class="ts" id=85 data-target="#details-85" data-toggle="collapse"><span class="ident">isBeforeOrEqual</span><span>(</span><span class="ident">other</span><span>: </span><a class="type-ref" href="#Position">Position</a><span>)</span><span>: </span><a class="type-intrinsic">boolean</a></span>
<div class="details collapse" id="details-85">
<div class="comment"><p>Check if this position is before or equal to <code>other</code>.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="other"></a><span class="ts" id=86 data-target="#details-86" data-toggle="collapse"><span class="ident">other</span><span>: </span><a class="type-ref" href="#Position">Position</a></span></td><td><div class="comment"><p>A position.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-intrinsic">boolean</a></span></td><td><div class="comment"><p><code>true</code> if position is on a smaller line
or on the same line on a smaller or equal character.</p>
</div></td></tr>
</table>
</div>
</div>



<a name="Position.isEqual"></a><span class="ts" id=94 data-target="#details-94" data-toggle="collapse"><span class="ident">isEqual</span><span>(</span><span class="ident">other</span><span>: </span><a class="type-ref" href="#Position">Position</a><span>)</span><span>: </span><a class="type-intrinsic">boolean</a></span>
<div class="details collapse" id="details-94">
<div class="comment"><p>Check if this position is equal to <code>other</code>.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="other"></a><span class="ts" id=95 data-target="#details-95" data-toggle="collapse"><span class="ident">other</span><span>: </span><a class="type-ref" href="#Position">Position</a></span></td><td><div class="comment"><p>A position.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-intrinsic">boolean</a></span></td><td><div class="comment"><p><code>true</code> if the line and character of the given position are equal to
the line and character of this position.</p>
</div></td></tr>
</table>
</div>
</div>



<a name="Position.translate"></a><span class="ts" id=100 data-target="#details-100" data-toggle="collapse"><span class="ident">translate</span><span>(</span><span class="ident">lineDelta</span><span>?</span><span>: </span><a class="type-intrinsic">number</a>, <span class="ident">characterDelta</span><span>?</span><span>: </span><a class="type-intrinsic">number</a><span>)</span><span>: </span><a class="type-ref" href="#Position">Position</a></span>
<div class="details collapse" id="details-100">
<div class="comment"><p>Create a new position relative to this position.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="lineDelta"></a><span class="ts" id=101 data-target="#details-101" data-toggle="collapse"><span class="ident">lineDelta</span><span>?</span><span>: </span><a class="type-intrinsic">number</a></span></td><td><div class="comment"><p>Delta value for the line value, default is <code>0</code>.</p>
</div></td></tr>
<tr><td><a name="characterDelta"></a><span class="ts" id=102 data-target="#details-102" data-toggle="collapse"><span class="ident">characterDelta</span><span>?</span><span>: </span><a class="type-intrinsic">number</a></span></td><td><div class="comment"><p>Delta value for the character value, default is <code>0</code>.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#Position">Position</a></span></td><td><div class="comment"><p>A position which line and character is the sum of the current line and
character and the corresponding deltas.</p>
</div></td></tr>
</table>
</div>
</div>



<a name="Position.translate"></a><span class="ts" id=103 data-target="#details-103" data-toggle="collapse"><span class="ident">translate</span><span>(</span><span class="ident">change</span><span>: </span>{characterDelta: <a class="type-intrinsic">number</a>, lineDelta: <a class="type-intrinsic">number</a>}<span>)</span><span>: </span><a class="type-ref" href="#Position">Position</a></span>
<div class="details collapse" id="details-103">
<div class="comment"><p>Derived a new position relative to this position.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="change"></a><span class="ts" id=104 data-target="#details-104" data-toggle="collapse"><span class="ident">change</span><span>: </span>{characterDelta: <a class="type-intrinsic">number</a>, lineDelta: <a class="type-intrinsic">number</a>}</span></td><td><div class="comment"><p>An object that describes a delta to this position.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#Position">Position</a></span></td><td><div class="comment"><p>A position that reflects the given delta. Will return <code>this</code> position if the change
is not changing anything.</p>
</div></td></tr>
</table>
</div>
</div>



<a name="Position.with"></a><span class="ts" id=109 data-target="#details-109" data-toggle="collapse"><span class="ident">with</span><span>(</span><span class="ident">line</span><span>?</span><span>: </span><a class="type-intrinsic">number</a>, <span class="ident">character</span><span>?</span><span>: </span><a class="type-intrinsic">number</a><span>)</span><span>: </span><a class="type-ref" href="#Position">Position</a></span>
<div class="details collapse" id="details-109">
<div class="comment"><p>Create a new position derived from this position.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="line"></a><span class="ts" id=110 data-target="#details-110" data-toggle="collapse"><span class="ident">line</span><span>?</span><span>: </span><a class="type-intrinsic">number</a></span></td><td><div class="comment"><p>Value that should be used as line value, default is the <a href="#Position.line">existing value</a></p>
</div></td></tr>
<tr><td><a name="character"></a><span class="ts" id=111 data-target="#details-111" data-toggle="collapse"><span class="ident">character</span><span>?</span><span>: </span><a class="type-intrinsic">number</a></span></td><td><div class="comment"><p>Value that should be used as character value, default is the <a href="#Position.character">existing value</a></p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#Position">Position</a></span></td><td><div class="comment"><p>A position where line and character are replaced by the given values.</p>
</div></td></tr>
</table>
</div>
</div>



<a name="Position.with"></a><span class="ts" id=112 data-target="#details-112" data-toggle="collapse"><span class="ident">with</span><span>(</span><span class="ident">change</span><span>: </span>{character: <a class="type-intrinsic">number</a>, line: <a class="type-intrinsic">number</a>}<span>)</span><span>: </span><a class="type-ref" href="#Position">Position</a></span>
<div class="details collapse" id="details-112">
<div class="comment"><p>Derived a new position from this position.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="change"></a><span class="ts" id=113 data-target="#details-113" data-toggle="collapse"><span class="ident">change</span><span>: </span>{character: <a class="type-intrinsic">number</a>, line: <a class="type-intrinsic">number</a>}</span></td><td><div class="comment"><p>An object that describes a change to this position.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#Position">Position</a></span></td><td><div class="comment"><p>A position that reflects the given change. Will return <code>this</code> position if the change
is not changing anything.</p>
</div></td></tr>
</table>
</div>
</div>

### <a name="ProcessExecution"></a><span class="code-item" id=1364>ProcessExecution</span>



<div class="comment"><p>The execution of a task happens as an external process
without shell interaction.</p>
</div>

#### Constructors



<a name="ProcessExecution.new ProcessExecution"></a><span class="ts" id=1366 data-target="#details-1366" data-toggle="collapse"><span class="ident">new ProcessExecution</span><span>(</span><span class="ident">process</span><span>: </span><a class="type-intrinsic">string</a>, <span class="ident">options</span><span>?</span><span>: </span><a class="type-ref" href="#ProcessExecutionOptions">ProcessExecutionOptions</a><span>)</span><span>: </span><a class="type-ref" href="#ProcessExecution">ProcessExecution</a></span>
<div class="details collapse" id="details-1366">
<div class="comment"><p>Creates a process execution.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="process"></a><span class="ts" id=1367 data-target="#details-1367" data-toggle="collapse"><span class="ident">process</span><span>: </span><a class="type-intrinsic">string</a></span></td><td><div class="comment"><p>The process to start.</p>
</div></td></tr>
<tr><td><a name="options"></a><span class="ts" id=1368 data-target="#details-1368" data-toggle="collapse"><span class="ident">options</span><span>?</span><span>: </span><a class="type-ref" href="#ProcessExecutionOptions">ProcessExecutionOptions</a></span></td><td><div class="comment"><p>Optional options for the started process.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#ProcessExecution">ProcessExecution</a></span></td><td><div class="comment"></div></td></tr>
</table>
</div>
</div>



<a name="ProcessExecution.new ProcessExecution"></a><span class="ts" id=1369 data-target="#details-1369" data-toggle="collapse"><span class="ident">new ProcessExecution</span><span>(</span><span class="ident">process</span><span>: </span><a class="type-intrinsic">string</a>, <span class="ident">args</span><span>: </span><a class="type-intrinsic">string</a>[], <span class="ident">options</span><span>?</span><span>: </span><a class="type-ref" href="#ProcessExecutionOptions">ProcessExecutionOptions</a><span>)</span><span>: </span><a class="type-ref" href="#ProcessExecution">ProcessExecution</a></span>
<div class="details collapse" id="details-1369">
<div class="comment"><p>Creates a process execution.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="process"></a><span class="ts" id=1370 data-target="#details-1370" data-toggle="collapse"><span class="ident">process</span><span>: </span><a class="type-intrinsic">string</a></span></td><td><div class="comment"><p>The process to start.</p>
</div></td></tr>
<tr><td><a name="args"></a><span class="ts" id=1371 data-target="#details-1371" data-toggle="collapse"><span class="ident">args</span><span>: </span><a class="type-intrinsic">string</a>[]</span></td><td><div class="comment"><p>Arguments to be passed to the process.</p>
</div></td></tr>
<tr><td><a name="options"></a><span class="ts" id=1372 data-target="#details-1372" data-toggle="collapse"><span class="ident">options</span><span>?</span><span>: </span><a class="type-ref" href="#ProcessExecutionOptions">ProcessExecutionOptions</a></span></td><td><div class="comment"><p>Optional options for the started process.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#ProcessExecution">ProcessExecution</a></span></td><td><div class="comment"></div></td></tr>
</table>
</div>
</div>

#### Properties



<a name="ProcessExecution.args"></a><span class="ts" id=1374 data-target="#details-1374" data-toggle="collapse"><span class="ident">args</span><span>: </span><a class="type-intrinsic">string</a>[]</span>
<div class="details collapse" id="details-1374">
<div class="comment"><p>The arguments passed to the process. Defaults to an empty array.</p>
</div>
</div>



<a name="ProcessExecution.options"></a><span class="ts" id=1375 data-target="#details-1375" data-toggle="collapse"><span class="ident">options</span><span>?</span><span>: </span><a class="type-ref" href="#ProcessExecutionOptions">ProcessExecutionOptions</a></span>
<div class="details collapse" id="details-1375">
<div class="comment"><p>The process options used when the process is executed.
Defaults to undefined.</p>
</div>
</div>



<a name="ProcessExecution.process"></a><span class="ts" id=1373 data-target="#details-1373" data-toggle="collapse"><span class="ident">process</span><span>: </span><a class="type-intrinsic">string</a></span>
<div class="details collapse" id="details-1373">
<div class="comment"><p>The process to be executed.</p>
</div>
</div>

### <a name="ProcessExecutionOptions"></a><span class="code-item" id=1358>ProcessExecutionOptions</span>



<div class="comment"><p>Options for a process execution</p>
</div>

#### Properties



<a name="ProcessExecutionOptions.cwd"></a><span class="ts" id=1359 data-target="#details-1359" data-toggle="collapse"><span class="ident">cwd</span><span>?</span><span>: </span><a class="type-intrinsic">string</a></span>
<div class="details collapse" id="details-1359">
<div class="comment"><p>The current working directory of the executed program or shell.
If omitted the tools current workspace root is used.</p>
</div>
</div>



<a name="ProcessExecutionOptions.env"></a><span class="ts" id=1360 data-target="#details-1360" data-toggle="collapse"><span class="ident">env</span><span>?</span><span>: </span></span>
<div class="details collapse" id="details-1360">
<div class="comment"><p>The additional environment of the executed program or shell. If omitted
the parent process&#39; environment is used. If provided it is merged with
the parent process&#39; environment.</p>
</div>
</div>

### <a name="Progress"></a><span class="code-item" id=1276>Progress&lt;T&gt;</span>



<div class="comment"><p>Defines a generalized way of reporting progress updates.</p>
</div>

#### Methods



<a name="Progress.report"></a><span class="ts" id=1279 data-target="#details-1279" data-toggle="collapse"><span class="ident">report</span><span>(</span><span class="ident">value</span><span>: </span><a class="type-intrinsic">T</a><span>)</span><span>: </span><a class="type-intrinsic">void</a></span>
<div class="details collapse" id="details-1279">
<div class="comment"><p>Report a progress update.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="value"></a><span class="ts" id=1280 data-target="#details-1280" data-toggle="collapse"><span class="ident">value</span><span>: </span><a class="type-intrinsic">T</a></span></td><td><div class="comment"><p>A progress item, like a message and/or an
report on how much work finished</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-intrinsic">void</a></span></td><td><div class="comment"></div></td></tr>
</table>
</div>
</div>

### <a name="ProgressLocation"></a><span class="code-item" id=1697>ProgressLocation</span>



<div class="comment"><p>A location in the editor at which progress information can be shown. It depends on the
location how progress is visually represented.</p>
</div>

#### Enumeration members



<a name="ProgressLocation.Notification"></a><span class="ts" id=1700 data-target="#details-1700" data-toggle="collapse"><span class="ident">Notification</span></span>
<div class="details collapse" id="details-1700">
<em>15</em>
</div>



<a name="ProgressLocation.SourceControl"></a><span class="ts" id=1698 data-target="#details-1698" data-toggle="collapse"><span class="ident">SourceControl</span></span>
<div class="details collapse" id="details-1698">
<em>1</em>
</div>



<a name="ProgressLocation.Window"></a><span class="ts" id=1699 data-target="#details-1699" data-toggle="collapse"><span class="ident">Window</span></span>
<div class="details collapse" id="details-1699">
<em>10</em>
</div>

### <a name="ProgressOptions"></a><span class="code-item" id=1701>ProgressOptions</span>



<div class="comment"><p>Value-object describing where and how progress should show.</p>
</div>

#### Properties



<a name="ProgressOptions.cancellable"></a><span class="ts" id=1704 data-target="#details-1704" data-toggle="collapse"><span class="ident">cancellable</span><span>?</span><span>: </span><a class="type-intrinsic">boolean</a></span>
<div class="details collapse" id="details-1704">
<div class="comment"><p>Controls if a cancel button should show to allow the user to
cancel the long running operation.  Note that currently only
<code>ProgressLocation.Notification</code> is supporting to show a cancel
button.</p>
</div>
</div>



<a name="ProgressOptions.location"></a><span class="ts" id=1702 data-target="#details-1702" data-toggle="collapse"><span class="ident">location</span><span>: </span><a class="type-ref" href="#ProgressLocation">ProgressLocation</a></span>
<div class="details collapse" id="details-1702">
<div class="comment"><p>The location at which progress should show.</p>
</div>
</div>



<a name="ProgressOptions.title"></a><span class="ts" id=1703 data-target="#details-1703" data-toggle="collapse"><span class="ident">title</span><span>?</span><span>: </span><a class="type-intrinsic">string</a></span>
<div class="details collapse" id="details-1703">
<div class="comment"><p>A human-readable string which will be used to describe the
operation.</p>
</div>
</div>

### <a name="ProviderResult"></a><span class="code-item" id=1990>ProviderResult&lt;T&gt;</span>



<div class="comment"><p>A provider result represents the values a provider, like the <a href="#HoverProvider"><code>HoverProvider</code></a>,
may return. For once this is the actual result type <code>T</code>, like <code>Hover</code>, or a thenable that resolves
to that type <code>T</code>. In addition, <code>null</code> and <code>undefined</code> can be returned - either directly or from a
thenable.</p>
<p>The snippets below are all valid implementations of the <a href="#HoverProvider"><code>HoverProvider</code></a>:</p>

<pre><code class="lang-ts">let a: HoverProvider = {
    provideHover(doc, pos, token): ProviderResult&lt;Hover&gt; {
        return new Hover(&#39;Hello World&#39;);
    }
}

let b: HoverProvider = {
    provideHover(doc, pos, token): ProviderResult&lt;Hover&gt; {
        return new Promise(resolve =&gt; {
            resolve(new Hover(&#39;Hello World&#39;));
         });
    }
}

let c: HoverProvider = {
    provideHover(doc, pos, token): ProviderResult&lt;Hover&gt; {
        return; // undefined
    }
}
</code></pre>
</div>



<a name="ProviderResult"></a><span class="ts" id=1990 data-target="#details-1990" data-toggle="collapse"><span class="ident">ProviderResult</span><span>: </span><a class="type-intrinsic">T</a> &#124; <a class="type-intrinsic">undefined</a> &#124; <a class="type-intrinsic">null</a> &#124; <a class="type-ref" href="#Thenable">Thenable</a>&lt;<a class="type-intrinsic">T</a> &#124; <a class="type-intrinsic">undefined</a> &#124; <a class="type-intrinsic">null</a>&gt;</span>

### <a name="QuickDiffProvider"></a><span class="code-item" id=1816>QuickDiffProvider</span>



<div class="comment"></div>

#### Methods



<a name="QuickDiffProvider.provideOriginalResource"></a><span class="ts" id=1818 data-target="#details-1818" data-toggle="collapse"><span class="ident">provideOriginalResource</span><span>(</span><span class="ident">uri</span><span>: </span><a class="type-ref" href="#Uri">Uri</a>, <span class="ident">token</span><span>: </span><a class="type-ref" href="#CancellationToken">CancellationToken</a><span>)</span><span>: </span><a class="type-ref" href="#ProviderResult">ProviderResult</a>&lt;<a class="type-ref" href="#Uri">Uri</a>&gt;</span>
<div class="details collapse" id="details-1818">
<div class="comment"><p>Provide a <a href="#Uri">uri</a> to the original resource of any given resource uri.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="uri"></a><span class="ts" id=1819 data-target="#details-1819" data-toggle="collapse"><span class="ident">uri</span><span>: </span><a class="type-ref" href="#Uri">Uri</a></span></td><td><div class="comment"><p>The uri of the resource open in a text editor.</p>
</div></td></tr>
<tr><td><a name="token"></a><span class="ts" id=1820 data-target="#details-1820" data-toggle="collapse"><span class="ident">token</span><span>: </span><a class="type-ref" href="#CancellationToken">CancellationToken</a></span></td><td><div class="comment"><p>A cancellation token.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#ProviderResult">ProviderResult</a>&lt;<a class="type-ref" href="#Uri">Uri</a>&gt;</span></td><td><div class="comment"><p>A thenable that resolves to uri of the matching original resource.</p>
</div></td></tr>
</table>
</div>
</div>

### <a name="QuickInput"></a><span class="code-item" id=1705>QuickInput</span>



<div class="comment"><p>A light-weight user input UI that is intially not visible. After
configuring it through its properties the extension can make it
visible by calling <a href="#QuickInput.show">QuickInput.show</a>.</p>
<p>There are several reasons why this UI might have to be hidden and
the extension will be notified through <a href="#QuickInput.onDidHide">QuickInput.onDidHide</a>.
(Examples include: an explict call to <a href="#QuickInput.hide">QuickInput.hide</a>,
the user pressing Esc, some other input UI opening, etc.)</p>
<p>A user pressing Enter or some other gesture implying acceptance
of the current state does not automatically hide this UI component.
It is up to the extension to decide whether to accept the user&#39;s input
and if the UI should indeed be hidden through a call to <a href="#QuickInput.hide">QuickInput.hide</a>.</p>
<p>When the extension no longer needs this input UI, it should
<a href="#QuickInput.dispose">QuickInput.dispose</a> it to allow for freeing up
any resources associated with it.</p>
<p>See <a href="#QuickPick">QuickPick</a> and <a href="#InputBox">InputBox</a> for concrete UIs.</p>
</div>

#### Events



<a name="QuickInput.onDidHide"></a><span class="ts" id=1716 data-target="#details-1716" data-toggle="collapse"><span class="ident">onDidHide</span><span>: </span><a class="type-ref" href="#Event">Event</a>&lt;<a class="type-intrinsic">void</a>&gt;</span>
<div class="details collapse" id="details-1716">
<div class="comment"><p>An event signaling when this input UI is hidden.</p>
<p>There are several reasons why this UI might have to be hidden and
the extension will be notified through <a href="#QuickInput.onDidHide">QuickInput.onDidHide</a>.
(Examples include: an explict call to <a href="#QuickInput.hide">QuickInput.hide</a>,
the user pressing Esc, some other input UI opening, etc.)</p>
</div>
</div>

#### Properties



<a name="QuickInput.busy"></a><span class="ts" id=1710 data-target="#details-1710" data-toggle="collapse"><span class="ident">busy</span><span>: </span><a class="type-intrinsic">boolean</a></span>
<div class="details collapse" id="details-1710">
<div class="comment"><p>If the UI should show a progress indicator. Defaults to false.</p>
<p>Change this to true, e.g., while loading more data or validating
user input.</p>
</div>
</div>



<a name="QuickInput.enabled"></a><span class="ts" id=1709 data-target="#details-1709" data-toggle="collapse"><span class="ident">enabled</span><span>: </span><a class="type-intrinsic">boolean</a></span>
<div class="details collapse" id="details-1709">
<div class="comment"><p>If the UI should allow for user input. Defaults to true.</p>
<p>Change this to false, e.g., while validating user input or
loading data for the next step in user input.</p>
</div>
</div>



<a name="QuickInput.ignoreFocusOut"></a><span class="ts" id=1711 data-target="#details-1711" data-toggle="collapse"><span class="ident">ignoreFocusOut</span><span>: </span><a class="type-intrinsic">boolean</a></span>
<div class="details collapse" id="details-1711">
<div class="comment"><p>If the UI should stay open even when loosing UI focus. Defaults to false.</p>
</div>
</div>



<a name="QuickInput.step"></a><span class="ts" id=1707 data-target="#details-1707" data-toggle="collapse"><span class="ident">step</span><span>: </span><a class="type-intrinsic">number</a> &#124; <a class="type-intrinsic">undefined</a></span>
<div class="details collapse" id="details-1707">
<div class="comment"><p>An optional current step count.</p>
</div>
</div>



<a name="QuickInput.title"></a><span class="ts" id=1706 data-target="#details-1706" data-toggle="collapse"><span class="ident">title</span><span>: </span><a class="type-intrinsic">string</a> &#124; <a class="type-intrinsic">undefined</a></span>
<div class="details collapse" id="details-1706">
<div class="comment"><p>An optional title.</p>
</div>
</div>



<a name="QuickInput.totalSteps"></a><span class="ts" id=1708 data-target="#details-1708" data-toggle="collapse"><span class="ident">totalSteps</span><span>: </span><a class="type-intrinsic">number</a> &#124; <a class="type-intrinsic">undefined</a></span>
<div class="details collapse" id="details-1708">
<div class="comment"><p>An optional total step count.</p>
</div>
</div>

#### Methods



<a name="QuickInput.dispose"></a><span class="ts" id=1718 data-target="#details-1718" data-toggle="collapse"><span class="ident">dispose</span><span>(</span><span>)</span><span>: </span><a class="type-intrinsic">void</a></span>
<div class="details collapse" id="details-1718">
<div class="comment"><p>Dispose of this input UI and any associated resources. If it is still
visible, it is first hidden. After this call the input UI is no longer
functional and no additional methods or properties on it should be
accessed. Instead a new input UI should be created.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-intrinsic">void</a></span></td><td><div class="comment"></div></td></tr>
</table>
</div>
</div>



<a name="QuickInput.hide"></a><span class="ts" id=1715 data-target="#details-1715" data-toggle="collapse"><span class="ident">hide</span><span>(</span><span>)</span><span>: </span><a class="type-intrinsic">void</a></span>
<div class="details collapse" id="details-1715">
<div class="comment"><p>Hides this input UI. This will also fire an <a href="#QuickInput.onDidHide">QuickInput.onDidHide</a>
event.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-intrinsic">void</a></span></td><td><div class="comment"></div></td></tr>
</table>
</div>
</div>



<a name="QuickInput.show"></a><span class="ts" id=1713 data-target="#details-1713" data-toggle="collapse"><span class="ident">show</span><span>(</span><span>)</span><span>: </span><a class="type-intrinsic">void</a></span>
<div class="details collapse" id="details-1713">
<div class="comment"><p>Makes the input UI visible in its current configuration. Any other input
UI will first fire an <a href="#QuickInput.onDidHide">QuickInput.onDidHide</a> event.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-intrinsic">void</a></span></td><td><div class="comment"></div></td></tr>
</table>
</div>
</div>

### <a name="QuickInputButton"></a><span class="code-item" id=1771>QuickInputButton</span>



<div class="comment"><p>Button for an action in a <a href="#QuickPick">QuickPick</a> or <a href="#InputBox">InputBox</a>.</p>
</div>

#### Properties



<a name="QuickInputButton.iconPath"></a><span class="ts" id=1772 data-target="#details-1772" data-toggle="collapse"><span class="ident">iconPath</span><span>: </span><a class="type-ref" href="#Uri">Uri</a> &#124; {dark: <a class="type-ref" href="#Uri">Uri</a>, light: <a class="type-ref" href="#Uri">Uri</a>} &#124; <a class="type-ref" href="#ThemeIcon">ThemeIcon</a></span>
<div class="details collapse" id="details-1772">
<div class="comment"><p>Icon for the button.</p>
</div>
</div>



<a name="QuickInputButton.tooltip"></a><span class="ts" id=1776 data-target="#details-1776" data-toggle="collapse"><span class="ident">tooltip</span><span>?</span><span>: </span><a class="type-intrinsic">string</a> &#124; <a class="type-intrinsic">undefined</a></span>
<div class="details collapse" id="details-1776">
<div class="comment"><p>An optional tooltip.</p>
</div>
</div>

### <a name="QuickInputButtons"></a><span class="code-item" id=1777>QuickInputButtons</span>



<div class="comment"><p>Predefined buttons for <a href="#QuickPick">QuickPick</a> and <a href="#InputBox">InputBox</a>.</p>
</div>

#### Static



<a name="QuickInputButtons.Back"></a><span class="ts" id=1778 data-target="#details-1778" data-toggle="collapse"><span class="ident">Back</span><span>: </span><a class="type-ref" href="#QuickInputButton">QuickInputButton</a></span>
<div class="details collapse" id="details-1778">
<div class="comment"><p>A back button for <a href="#QuickPick">QuickPick</a> and <a href="#InputBox">InputBox</a>.</p>
<p>When a navigation &#39;back&#39; button is needed this one should be used for consistency.
It comes with a predefined icon, tooltip and location.</p>
</div>
</div>

### <a name="QuickPick"></a><span class="code-item" id=1719>QuickPick&lt;T&gt;</span>



<div class="comment"><p>A concrete <a href="#QuickInput">QuickInput</a> to let the user pick an item from a
list of items of type T. The items can be filtered through a filter text field and
there is an option <a href="#QuickPick.canSelectMany">canSelectMany</a> to allow for
selecting multiple items.</p>
<p>Note that in many cases the more convenient <a href="#window.showQuickPick">window.showQuickPick</a>
is easier to use. <a href="#window.createQuickPick">window.createQuickPick</a> should be used
when <a href="#window.showQuickPick">window.showQuickPick</a> does not offer the required flexibility.</p>
</div>

#### Events



<a name="QuickPick.onDidAccept"></a><span class="ts" id=1724 data-target="#details-1724" data-toggle="collapse"><span class="ident">onDidAccept</span><span>: </span><a class="type-ref" href="#Event">Event</a>&lt;<a class="type-intrinsic">void</a>&gt;</span>
<div class="details collapse" id="details-1724">
<div class="comment"><p>An event signaling when the user indicated acceptance of the selected item(s).</p>
</div>
</div>



<a name="QuickPick.onDidChangeActive"></a><span class="ts" id=1732 data-target="#details-1732" data-toggle="collapse"><span class="ident">onDidChangeActive</span><span>: </span><a class="type-ref" href="#Event">Event</a>&lt;<a class="type-intrinsic">T</a>[]&gt;</span>
<div class="details collapse" id="details-1732">
<div class="comment"><p>An event signaling when the active items have changed.</p>
</div>
</div>



<a name="QuickPick.onDidChangeSelection"></a><span class="ts" id=1734 data-target="#details-1734" data-toggle="collapse"><span class="ident">onDidChangeSelection</span><span>: </span><a class="type-ref" href="#Event">Event</a>&lt;<a class="type-intrinsic">T</a>[]&gt;</span>
<div class="details collapse" id="details-1734">
<div class="comment"><p>An event signaling when the selected items have changed.</p>
</div>
</div>



<a name="QuickPick.onDidChangeValue"></a><span class="ts" id=1723 data-target="#details-1723" data-toggle="collapse"><span class="ident">onDidChangeValue</span><span>: </span><a class="type-ref" href="#Event">Event</a>&lt;<a class="type-intrinsic">string</a>&gt;</span>
<div class="details collapse" id="details-1723">
<div class="comment"><p>An event signaling when the value of the filter text has changed.</p>
</div>
</div>



<a name="QuickPick.onDidHide"></a><span class="ts" id=1745 data-target="#details-1745" data-toggle="collapse"><span class="ident">onDidHide</span><span>: </span><a class="type-ref" href="#Event">Event</a>&lt;<a class="type-intrinsic">void</a>&gt;</span>
<div class="details collapse" id="details-1745">
<div class="comment"><p>An event signaling when this input UI is hidden.</p>
<p>There are several reasons why this UI might have to be hidden and
the extension will be notified through <a href="#QuickInput.onDidHide">QuickInput.onDidHide</a>.
(Examples include: an explict call to <a href="#QuickInput.hide">QuickInput.hide</a>,
the user pressing Esc, some other input UI opening, etc.)</p>
</div>
</div>



<a name="QuickPick.onDidTriggerButton"></a><span class="ts" id=1726 data-target="#details-1726" data-toggle="collapse"><span class="ident">onDidTriggerButton</span><span>: </span><a class="type-ref" href="#Event">Event</a>&lt;<a class="type-ref" href="#QuickInputButton">QuickInputButton</a>&gt;</span>
<div class="details collapse" id="details-1726">
<div class="comment"><p>An event signaling when a button was triggered.</p>
</div>
</div>

#### Properties



<a name="QuickPick.activeItems"></a><span class="ts" id=1731 data-target="#details-1731" data-toggle="collapse"><span class="ident">activeItems</span><span>: </span><a class="type-ref" href="#ReadonlyArray">ReadonlyArray</a>&lt;<a class="type-intrinsic">T</a>&gt;</span>
<div class="details collapse" id="details-1731">
<div class="comment"><p>Active items. This can be read and updated by the extension.</p>
</div>
</div>



<a name="QuickPick.busy"></a><span class="ts" id=1739 data-target="#details-1739" data-toggle="collapse"><span class="ident">busy</span><span>: </span><a class="type-intrinsic">boolean</a></span>
<div class="details collapse" id="details-1739">
<div class="comment"><p>If the UI should show a progress indicator. Defaults to false.</p>
<p>Change this to true, e.g., while loading more data or validating
user input.</p>
</div>
</div>



<a name="QuickPick.buttons"></a><span class="ts" id=1725 data-target="#details-1725" data-toggle="collapse"><span class="ident">buttons</span><span>: </span><a class="type-ref" href="#ReadonlyArray">ReadonlyArray</a>&lt;<a class="type-ref" href="#QuickInputButton">QuickInputButton</a>&gt;</span>
<div class="details collapse" id="details-1725">
<div class="comment"><p>Buttons for actions in the UI.</p>
</div>
</div>



<a name="QuickPick.canSelectMany"></a><span class="ts" id=1728 data-target="#details-1728" data-toggle="collapse"><span class="ident">canSelectMany</span><span>: </span><a class="type-intrinsic">boolean</a></span>
<div class="details collapse" id="details-1728">
<div class="comment"><p>If multiple items can be selected at the same time. Defaults to false.</p>
</div>
</div>



<a name="QuickPick.enabled"></a><span class="ts" id=1738 data-target="#details-1738" data-toggle="collapse"><span class="ident">enabled</span><span>: </span><a class="type-intrinsic">boolean</a></span>
<div class="details collapse" id="details-1738">
<div class="comment"><p>If the UI should allow for user input. Defaults to true.</p>
<p>Change this to false, e.g., while validating user input or
loading data for the next step in user input.</p>
</div>
</div>



<a name="QuickPick.ignoreFocusOut"></a><span class="ts" id=1740 data-target="#details-1740" data-toggle="collapse"><span class="ident">ignoreFocusOut</span><span>: </span><a class="type-intrinsic">boolean</a></span>
<div class="details collapse" id="details-1740">
<div class="comment"><p>If the UI should stay open even when loosing UI focus. Defaults to false.</p>
</div>
</div>



<a name="QuickPick.items"></a><span class="ts" id=1727 data-target="#details-1727" data-toggle="collapse"><span class="ident">items</span><span>: </span><a class="type-ref" href="#ReadonlyArray">ReadonlyArray</a>&lt;<a class="type-intrinsic">T</a>&gt;</span>
<div class="details collapse" id="details-1727">
<div class="comment"><p>Items to pick from.</p>
</div>
</div>



<a name="QuickPick.matchOnDescription"></a><span class="ts" id=1729 data-target="#details-1729" data-toggle="collapse"><span class="ident">matchOnDescription</span><span>: </span><a class="type-intrinsic">boolean</a></span>
<div class="details collapse" id="details-1729">
<div class="comment"><p>If the filter text should also be matched against the description of the items. Defaults to false.</p>
</div>
</div>



<a name="QuickPick.matchOnDetail"></a><span class="ts" id=1730 data-target="#details-1730" data-toggle="collapse"><span class="ident">matchOnDetail</span><span>: </span><a class="type-intrinsic">boolean</a></span>
<div class="details collapse" id="details-1730">
<div class="comment"><p>If the filter text should also be matched against the detail of the items. Defaults to false.</p>
</div>
</div>



<a name="QuickPick.placeholder"></a><span class="ts" id=1722 data-target="#details-1722" data-toggle="collapse"><span class="ident">placeholder</span><span>: </span><a class="type-intrinsic">string</a> &#124; <a class="type-intrinsic">undefined</a></span>
<div class="details collapse" id="details-1722">
<div class="comment"><p>Optional placeholder in the filter text.</p>
</div>
</div>



<a name="QuickPick.selectedItems"></a><span class="ts" id=1733 data-target="#details-1733" data-toggle="collapse"><span class="ident">selectedItems</span><span>: </span><a class="type-ref" href="#ReadonlyArray">ReadonlyArray</a>&lt;<a class="type-intrinsic">T</a>&gt;</span>
<div class="details collapse" id="details-1733">
<div class="comment"><p>Selected items. This can be read and updated by the extension.</p>
</div>
</div>



<a name="QuickPick.step"></a><span class="ts" id=1736 data-target="#details-1736" data-toggle="collapse"><span class="ident">step</span><span>: </span><a class="type-intrinsic">number</a> &#124; <a class="type-intrinsic">undefined</a></span>
<div class="details collapse" id="details-1736">
<div class="comment"><p>An optional current step count.</p>
</div>
</div>



<a name="QuickPick.title"></a><span class="ts" id=1735 data-target="#details-1735" data-toggle="collapse"><span class="ident">title</span><span>: </span><a class="type-intrinsic">string</a> &#124; <a class="type-intrinsic">undefined</a></span>
<div class="details collapse" id="details-1735">
<div class="comment"><p>An optional title.</p>
</div>
</div>



<a name="QuickPick.totalSteps"></a><span class="ts" id=1737 data-target="#details-1737" data-toggle="collapse"><span class="ident">totalSteps</span><span>: </span><a class="type-intrinsic">number</a> &#124; <a class="type-intrinsic">undefined</a></span>
<div class="details collapse" id="details-1737">
<div class="comment"><p>An optional total step count.</p>
</div>
</div>



<a name="QuickPick.value"></a><span class="ts" id=1721 data-target="#details-1721" data-toggle="collapse"><span class="ident">value</span><span>: </span><a class="type-intrinsic">string</a></span>
<div class="details collapse" id="details-1721">
<div class="comment"><p>Current value of the filter text.</p>
</div>
</div>

#### Methods



<a name="QuickPick.dispose"></a><span class="ts" id=1747 data-target="#details-1747" data-toggle="collapse"><span class="ident">dispose</span><span>(</span><span>)</span><span>: </span><a class="type-intrinsic">void</a></span>
<div class="details collapse" id="details-1747">
<div class="comment"><p>Dispose of this input UI and any associated resources. If it is still
visible, it is first hidden. After this call the input UI is no longer
functional and no additional methods or properties on it should be
accessed. Instead a new input UI should be created.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-intrinsic">void</a></span></td><td><div class="comment"></div></td></tr>
</table>
</div>
</div>



<a name="QuickPick.hide"></a><span class="ts" id=1744 data-target="#details-1744" data-toggle="collapse"><span class="ident">hide</span><span>(</span><span>)</span><span>: </span><a class="type-intrinsic">void</a></span>
<div class="details collapse" id="details-1744">
<div class="comment"><p>Hides this input UI. This will also fire an <a href="#QuickInput.onDidHide">QuickInput.onDidHide</a>
event.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-intrinsic">void</a></span></td><td><div class="comment"></div></td></tr>
</table>
</div>
</div>



<a name="QuickPick.show"></a><span class="ts" id=1742 data-target="#details-1742" data-toggle="collapse"><span class="ident">show</span><span>(</span><span>)</span><span>: </span><a class="type-intrinsic">void</a></span>
<div class="details collapse" id="details-1742">
<div class="comment"><p>Makes the input UI visible in its current configuration. Any other input
UI will first fire an <a href="#QuickInput.onDidHide">QuickInput.onDidHide</a> event.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-intrinsic">void</a></span></td><td><div class="comment"></div></td></tr>
</table>
</div>
</div>

### <a name="QuickPickItem"></a><span class="code-item" id=489>QuickPickItem</span>



<div class="comment"><p>Represents an item that can be selected from
a list of items.</p>
</div>

#### Properties



<a name="QuickPickItem.alwaysShow"></a><span class="ts" id=494 data-target="#details-494" data-toggle="collapse"><span class="ident">alwaysShow</span><span>?</span><span>: </span><a class="type-intrinsic">boolean</a></span>
<div class="details collapse" id="details-494">
<div class="comment"><p>Always show this item.</p>
</div>
</div>



<a name="QuickPickItem.description"></a><span class="ts" id=491 data-target="#details-491" data-toggle="collapse"><span class="ident">description</span><span>?</span><span>: </span><a class="type-intrinsic">string</a></span>
<div class="details collapse" id="details-491">
<div class="comment"><p>A human readable string which is rendered less prominent.</p>
</div>
</div>



<a name="QuickPickItem.detail"></a><span class="ts" id=492 data-target="#details-492" data-toggle="collapse"><span class="ident">detail</span><span>?</span><span>: </span><a class="type-intrinsic">string</a></span>
<div class="details collapse" id="details-492">
<div class="comment"><p>A human readable string which is rendered less prominent.</p>
</div>
</div>



<a name="QuickPickItem.label"></a><span class="ts" id=490 data-target="#details-490" data-toggle="collapse"><span class="ident">label</span><span>: </span><a class="type-intrinsic">string</a></span>
<div class="details collapse" id="details-490">
<div class="comment"><p>A human readable string which is rendered prominent.</p>
</div>
</div>



<a name="QuickPickItem.picked"></a><span class="ts" id=493 data-target="#details-493" data-toggle="collapse"><span class="ident">picked</span><span>?</span><span>: </span><a class="type-intrinsic">boolean</a></span>
<div class="details collapse" id="details-493">
<div class="comment"><p>Optional flag indicating if this item is picked initially.
(Only honored when the picker allows multiple selections.)</p>
<ul>
<li><em>see</em> - <a href="#QuickPickOptions.canPickMany">QuickPickOptions.canPickMany</a></li>
</ul>
</div>
</div>

### <a name="QuickPickOptions"></a><span class="code-item" id=495>QuickPickOptions</span>



<div class="comment"><p>Options to configure the behavior of the quick pick UI.</p>
</div>

#### Events



<a name="QuickPickOptions.onDidSelectItem"></a><span class="ts" id=502 data-target="#details-502" data-toggle="collapse"><span class="ident">onDidSelectItem</span><span>(</span><span class="ident">item</span><span>: </span><a class="type-ref" href="#QuickPickItem">QuickPickItem</a> &#124; <a class="type-intrinsic">string</a><span>)</span><span>: </span><a class="type-intrinsic">any</a></span>
<div class="details collapse" id="details-502">
<div class="comment"><p>An optional function that is invoked whenever an item is selected.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="item"></a><span class="ts" id=503 data-target="#details-503" data-toggle="collapse"><span class="ident">item</span><span>: </span><a class="type-ref" href="#QuickPickItem">QuickPickItem</a> &#124; <a class="type-intrinsic">string</a></span></td><td><div class="comment"></div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-intrinsic">any</a></span></td><td><div class="comment"></div></td></tr>
</table>
</div>
</div>

#### Properties



<a name="QuickPickOptions.canPickMany"></a><span class="ts" id=500 data-target="#details-500" data-toggle="collapse"><span class="ident">canPickMany</span><span>?</span><span>: </span><a class="type-intrinsic">boolean</a></span>
<div class="details collapse" id="details-500">
<div class="comment"><p>An optional flag to make the picker accept multiple selections, if true the result is an array of picks.</p>
</div>
</div>



<a name="QuickPickOptions.ignoreFocusOut"></a><span class="ts" id=499 data-target="#details-499" data-toggle="collapse"><span class="ident">ignoreFocusOut</span><span>?</span><span>: </span><a class="type-intrinsic">boolean</a></span>
<div class="details collapse" id="details-499">
<div class="comment"><p>Set to <code>true</code> to keep the picker open when focus moves to another part of the editor or to another window.</p>
</div>
</div>



<a name="QuickPickOptions.matchOnDescription"></a><span class="ts" id=496 data-target="#details-496" data-toggle="collapse"><span class="ident">matchOnDescription</span><span>?</span><span>: </span><a class="type-intrinsic">boolean</a></span>
<div class="details collapse" id="details-496">
<div class="comment"><p>An optional flag to include the description when filtering the picks.</p>
</div>
</div>



<a name="QuickPickOptions.matchOnDetail"></a><span class="ts" id=497 data-target="#details-497" data-toggle="collapse"><span class="ident">matchOnDetail</span><span>?</span><span>: </span><a class="type-intrinsic">boolean</a></span>
<div class="details collapse" id="details-497">
<div class="comment"><p>An optional flag to include the detail when filtering the picks.</p>
</div>
</div>



<a name="QuickPickOptions.placeHolder"></a><span class="ts" id=498 data-target="#details-498" data-toggle="collapse"><span class="ident">placeHolder</span><span>?</span><span>: </span><a class="type-intrinsic">string</a></span>
<div class="details collapse" id="details-498">
<div class="comment"><p>An optional string to show as place holder in the input box to guide the user what to pick on.</p>
</div>
</div>

### <a name="Range"></a><span class="code-item" id=117>Range</span>



<div class="comment"><p>A range represents an ordered pair of two positions.
It is guaranteed that <a href="#Range.start">start</a>.isBeforeOrEqual(<a href="#Range.end">end</a>)</p>
<p>Range objects are <strong>immutable</strong>. Use the <a href="#Range.with">with</a>,
<a href="#Range.intersection">intersection</a>, or <a href="#Range.union">union</a> methods
to derive new ranges from an existing range.</p>
</div>

#### Constructors



<a name="Range.new Range"></a><span class="ts" id=121 data-target="#details-121" data-toggle="collapse"><span class="ident">new Range</span><span>(</span><span class="ident">start</span><span>: </span><a class="type-ref" href="#Position">Position</a>, <span class="ident">end</span><span>: </span><a class="type-ref" href="#Position">Position</a><span>)</span><span>: </span><a class="type-ref" href="#Range">Range</a></span>
<div class="details collapse" id="details-121">
<div class="comment"><p>Create a new range from two positions. If <code>start</code> is not
before or equal to <code>end</code>, the values will be swapped.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="start"></a><span class="ts" id=122 data-target="#details-122" data-toggle="collapse"><span class="ident">start</span><span>: </span><a class="type-ref" href="#Position">Position</a></span></td><td><div class="comment"><p>A position.</p>
</div></td></tr>
<tr><td><a name="end"></a><span class="ts" id=123 data-target="#details-123" data-toggle="collapse"><span class="ident">end</span><span>: </span><a class="type-ref" href="#Position">Position</a></span></td><td><div class="comment"><p>A position.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#Range">Range</a></span></td><td><div class="comment"></div></td></tr>
</table>
</div>
</div>



<a name="Range.new Range"></a><span class="ts" id=124 data-target="#details-124" data-toggle="collapse"><span class="ident">new Range</span><span>(</span><span class="ident">startLine</span><span>: </span><a class="type-intrinsic">number</a>, <span class="ident">startCharacter</span><span>: </span><a class="type-intrinsic">number</a>, <span class="ident">endLine</span><span>: </span><a class="type-intrinsic">number</a>, <span class="ident">endCharacter</span><span>: </span><a class="type-intrinsic">number</a><span>)</span><span>: </span><a class="type-ref" href="#Range">Range</a></span>
<div class="details collapse" id="details-124">
<div class="comment"><p>Create a new range from number coordinates. It is a shorter equivalent of
using <code>new Range(new Position(startLine, startCharacter), new Position(endLine, endCharacter))</code></p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="startLine"></a><span class="ts" id=125 data-target="#details-125" data-toggle="collapse"><span class="ident">startLine</span><span>: </span><a class="type-intrinsic">number</a></span></td><td><div class="comment"><p>A zero-based line value.</p>
</div></td></tr>
<tr><td><a name="startCharacter"></a><span class="ts" id=126 data-target="#details-126" data-toggle="collapse"><span class="ident">startCharacter</span><span>: </span><a class="type-intrinsic">number</a></span></td><td><div class="comment"><p>A zero-based character value.</p>
</div></td></tr>
<tr><td><a name="endLine"></a><span class="ts" id=127 data-target="#details-127" data-toggle="collapse"><span class="ident">endLine</span><span>: </span><a class="type-intrinsic">number</a></span></td><td><div class="comment"><p>A zero-based line value.</p>
</div></td></tr>
<tr><td><a name="endCharacter"></a><span class="ts" id=128 data-target="#details-128" data-toggle="collapse"><span class="ident">endCharacter</span><span>: </span><a class="type-intrinsic">number</a></span></td><td><div class="comment"><p>A zero-based character value.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#Range">Range</a></span></td><td><div class="comment"></div></td></tr>
</table>
</div>
</div>

#### Properties



<a name="Range.end"></a><span class="ts" id=119 data-target="#details-119" data-toggle="collapse"><span class="ident">end</span><span>: </span><a class="type-ref" href="#Position">Position</a></span>
<div class="details collapse" id="details-119">
<div class="comment"><p>The end position. It is after or equal to <a href="#Range.start">start</a>.</p>
</div>
</div>



<a name="Range.isEmpty"></a><span class="ts" id=129 data-target="#details-129" data-toggle="collapse"><span class="ident">isEmpty</span><span>: </span><a class="type-intrinsic">boolean</a></span>
<div class="details collapse" id="details-129">
<div class="comment"><p><code>true</code> if <code>start</code> and <code>end</code> are equal.</p>
</div>
</div>



<a name="Range.isSingleLine"></a><span class="ts" id=130 data-target="#details-130" data-toggle="collapse"><span class="ident">isSingleLine</span><span>: </span><a class="type-intrinsic">boolean</a></span>
<div class="details collapse" id="details-130">
<div class="comment"><p><code>true</code> if <code>start.line</code> and <code>end.line</code> are equal.</p>
</div>
</div>



<a name="Range.start"></a><span class="ts" id=118 data-target="#details-118" data-toggle="collapse"><span class="ident">start</span><span>: </span><a class="type-ref" href="#Position">Position</a></span>
<div class="details collapse" id="details-118">
<div class="comment"><p>The start position. It is before or equal to <a href="#Range.end">end</a>.</p>
</div>
</div>

#### Methods



<a name="Range.contains"></a><span class="ts" id=132 data-target="#details-132" data-toggle="collapse"><span class="ident">contains</span><span>(</span><span class="ident">positionOrRange</span><span>: </span><a class="type-ref" href="#Position">Position</a> &#124; <a class="type-ref" href="#Range">Range</a><span>)</span><span>: </span><a class="type-intrinsic">boolean</a></span>
<div class="details collapse" id="details-132">
<div class="comment"><p>Check if a position or a range is contained in this range.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="positionOrRange"></a><span class="ts" id=133 data-target="#details-133" data-toggle="collapse"><span class="ident">positionOrRange</span><span>: </span><a class="type-ref" href="#Position">Position</a> &#124; <a class="type-ref" href="#Range">Range</a></span></td><td><div class="comment"><p>A position or a range.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-intrinsic">boolean</a></span></td><td><div class="comment"><p><code>true</code> if the position or range is inside or equal
to this range.</p>
</div></td></tr>
</table>
</div>
</div>



<a name="Range.intersection"></a><span class="ts" id=138 data-target="#details-138" data-toggle="collapse"><span class="ident">intersection</span><span>(</span><span class="ident">range</span><span>: </span><a class="type-ref" href="#Range">Range</a><span>)</span><span>: </span><a class="type-ref" href="#Range">Range</a> &#124; <a class="type-intrinsic">undefined</a></span>
<div class="details collapse" id="details-138">
<div class="comment"><p>Intersect <code>range</code> with this range and returns a new range or <code>undefined</code>
if the ranges have no overlap.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="range"></a><span class="ts" id=139 data-target="#details-139" data-toggle="collapse"><span class="ident">range</span><span>: </span><a class="type-ref" href="#Range">Range</a></span></td><td><div class="comment"><p>A range.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#Range">Range</a> &#124; <a class="type-intrinsic">undefined</a></span></td><td><div class="comment"><p>A range of the greater start and smaller end positions. Will
return undefined when there is no overlap.</p>
</div></td></tr>
</table>
</div>
</div>



<a name="Range.isEqual"></a><span class="ts" id=135 data-target="#details-135" data-toggle="collapse"><span class="ident">isEqual</span><span>(</span><span class="ident">other</span><span>: </span><a class="type-ref" href="#Range">Range</a><span>)</span><span>: </span><a class="type-intrinsic">boolean</a></span>
<div class="details collapse" id="details-135">
<div class="comment"><p>Check if <code>other</code> equals this range.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="other"></a><span class="ts" id=136 data-target="#details-136" data-toggle="collapse"><span class="ident">other</span><span>: </span><a class="type-ref" href="#Range">Range</a></span></td><td><div class="comment"><p>A range.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-intrinsic">boolean</a></span></td><td><div class="comment"><p><code>true</code> when start and end are <a href="#Position.isEqual">equal</a> to
start and end of this range.</p>
</div></td></tr>
</table>
</div>
</div>



<a name="Range.union"></a><span class="ts" id=141 data-target="#details-141" data-toggle="collapse"><span class="ident">union</span><span>(</span><span class="ident">other</span><span>: </span><a class="type-ref" href="#Range">Range</a><span>)</span><span>: </span><a class="type-ref" href="#Range">Range</a></span>
<div class="details collapse" id="details-141">
<div class="comment"><p>Compute the union of <code>other</code> with this range.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="other"></a><span class="ts" id=142 data-target="#details-142" data-toggle="collapse"><span class="ident">other</span><span>: </span><a class="type-ref" href="#Range">Range</a></span></td><td><div class="comment"><p>A range.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#Range">Range</a></span></td><td><div class="comment"><p>A range of smaller start position and the greater end position.</p>
</div></td></tr>
</table>
</div>
</div>



<a name="Range.with"></a><span class="ts" id=144 data-target="#details-144" data-toggle="collapse"><span class="ident">with</span><span>(</span><span class="ident">start</span><span>?</span><span>: </span><a class="type-ref" href="#Position">Position</a>, <span class="ident">end</span><span>?</span><span>: </span><a class="type-ref" href="#Position">Position</a><span>)</span><span>: </span><a class="type-ref" href="#Range">Range</a></span>
<div class="details collapse" id="details-144">
<div class="comment"><p>Derived a new range from this range.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="start"></a><span class="ts" id=145 data-target="#details-145" data-toggle="collapse"><span class="ident">start</span><span>?</span><span>: </span><a class="type-ref" href="#Position">Position</a></span></td><td><div class="comment"><p>A position that should be used as start. The default value is the <a href="#Range.start">current start</a>.</p>
</div></td></tr>
<tr><td><a name="end"></a><span class="ts" id=146 data-target="#details-146" data-toggle="collapse"><span class="ident">end</span><span>?</span><span>: </span><a class="type-ref" href="#Position">Position</a></span></td><td><div class="comment"><p>A position that should be used as end. The default value is the <a href="#Range.end">current end</a>.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#Range">Range</a></span></td><td><div class="comment"><p>A range derived from this range with the given start and end position.
If start and end are not different <code>this</code> range will be returned.</p>
</div></td></tr>
</table>
</div>
</div>



<a name="Range.with"></a><span class="ts" id=147 data-target="#details-147" data-toggle="collapse"><span class="ident">with</span><span>(</span><span class="ident">change</span><span>: </span>{end: <a class="type-ref" href="#Position">Position</a>, start: <a class="type-ref" href="#Position">Position</a>}<span>)</span><span>: </span><a class="type-ref" href="#Range">Range</a></span>
<div class="details collapse" id="details-147">
<div class="comment"><p>Derived a new range from this range.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="change"></a><span class="ts" id=148 data-target="#details-148" data-toggle="collapse"><span class="ident">change</span><span>: </span>{end: <a class="type-ref" href="#Position">Position</a>, start: <a class="type-ref" href="#Position">Position</a>}</span></td><td><div class="comment"><p>An object that describes a change to this range.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#Range">Range</a></span></td><td><div class="comment"><p>A range that reflects the given change. Will return <code>this</code> range if the change
is not changing anything.</p>
</div></td></tr>
</table>
</div>
</div>

### <a name="ReferenceContext"></a><span class="code-item" id=753>ReferenceContext</span>



<div class="comment"><p>Value-object that contains additional information when
requesting references.</p>
</div>

#### Properties



<a name="ReferenceContext.includeDeclaration"></a><span class="ts" id=754 data-target="#details-754" data-toggle="collapse"><span class="ident">includeDeclaration</span><span>: </span><a class="type-intrinsic">boolean</a></span>
<div class="details collapse" id="details-754">
<div class="comment"><p>Include the declaration of the current symbol.</p>
</div>
</div>

### <a name="ReferenceProvider"></a><span class="code-item" id=755>ReferenceProvider</span>



<div class="comment"><p>The reference provider interface defines the contract between extensions and
the <a href="https://code.visualstudio.com/docs/editor/editingevolved#_peek">find references</a>-feature.</p>
</div>

#### Methods



<a name="ReferenceProvider.provideReferences"></a><span class="ts" id=757 data-target="#details-757" data-toggle="collapse"><span class="ident">provideReferences</span><span>(</span><span class="ident">document</span><span>: </span><a class="type-ref" href="#TextDocument">TextDocument</a>, <span class="ident">position</span><span>: </span><a class="type-ref" href="#Position">Position</a>, <span class="ident">context</span><span>: </span><a class="type-ref" href="#ReferenceContext">ReferenceContext</a>, <span class="ident">token</span><span>: </span><a class="type-ref" href="#CancellationToken">CancellationToken</a><span>)</span><span>: </span><a class="type-ref" href="#ProviderResult">ProviderResult</a>&lt;<a class="type-ref" href="#Location">Location</a>[]&gt;</span>
<div class="details collapse" id="details-757">
<div class="comment"><p>Provide a set of project-wide references for the given position and document.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="document"></a><span class="ts" id=758 data-target="#details-758" data-toggle="collapse"><span class="ident">document</span><span>: </span><a class="type-ref" href="#TextDocument">TextDocument</a></span></td><td><div class="comment"><p>The document in which the command was invoked.</p>
</div></td></tr>
<tr><td><a name="position"></a><span class="ts" id=759 data-target="#details-759" data-toggle="collapse"><span class="ident">position</span><span>: </span><a class="type-ref" href="#Position">Position</a></span></td><td><div class="comment"><p>The position at which the command was invoked.</p>
</div></td></tr>
<tr><td><a name="context"></a><span class="ts" id=760 data-target="#details-760" data-toggle="collapse"><span class="ident">context</span><span>: </span><a class="type-ref" href="#ReferenceContext">ReferenceContext</a></span></td><td><div class="comment"></div></td></tr>
<tr><td><a name="token"></a><span class="ts" id=761 data-target="#details-761" data-toggle="collapse"><span class="ident">token</span><span>: </span><a class="type-ref" href="#CancellationToken">CancellationToken</a></span></td><td><div class="comment"><p>A cancellation token.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#ProviderResult">ProviderResult</a>&lt;<a class="type-ref" href="#Location">Location</a>[]&gt;</span></td><td><div class="comment"><p>An array of locations or a thenable that resolves to such. The lack of a result can be
signaled by returning <code>undefined</code>, <code>null</code>, or an empty array.</p>
</div></td></tr>
</table>
</div>
</div>

### <a name="RelativePattern"></a><span class="code-item" id=539>RelativePattern</span>



<div class="comment"><p>A relative pattern is a helper to construct glob patterns that are matched
relatively to a base path. The base path can either be an absolute file path
or a <a href="#WorkspaceFolder">workspace folder</a>.</p>
</div>

#### Constructors



<a name="RelativePattern.new RelativePattern"></a><span class="ts" id=543 data-target="#details-543" data-toggle="collapse"><span class="ident">new RelativePattern</span><span>(</span><span class="ident">base</span><span>: </span><a class="type-ref" href="#WorkspaceFolder">WorkspaceFolder</a> &#124; <a class="type-intrinsic">string</a>, <span class="ident">pattern</span><span>: </span><a class="type-intrinsic">string</a><span>)</span><span>: </span><a class="type-ref" href="#RelativePattern">RelativePattern</a></span>
<div class="details collapse" id="details-543">
<div class="comment"><p>Creates a new relative pattern object with a base path and pattern to match. This pattern
will be matched on file paths relative to the base path.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="base"></a><span class="ts" id=544 data-target="#details-544" data-toggle="collapse"><span class="ident">base</span><span>: </span><a class="type-ref" href="#WorkspaceFolder">WorkspaceFolder</a> &#124; <a class="type-intrinsic">string</a></span></td><td><div class="comment"><p>A base file path to which this pattern will be matched against relatively.</p>
</div></td></tr>
<tr><td><a name="pattern"></a><span class="ts" id=545 data-target="#details-545" data-toggle="collapse"><span class="ident">pattern</span><span>: </span><a class="type-intrinsic">string</a></span></td><td><div class="comment"><p>A file glob pattern like <code>*.{ts,js}</code> that will be matched on file paths
relative to the base path.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#RelativePattern">RelativePattern</a></span></td><td><div class="comment"></div></td></tr>
</table>
</div>
</div>

#### Properties



<a name="RelativePattern.base"></a><span class="ts" id=540 data-target="#details-540" data-toggle="collapse"><span class="ident">base</span><span>: </span><a class="type-intrinsic">string</a></span>
<div class="details collapse" id="details-540">
<div class="comment"><p>A base file path to which this pattern will be matched against relatively.</p>
</div>
</div>



<a name="RelativePattern.pattern"></a><span class="ts" id=541 data-target="#details-541" data-toggle="collapse"><span class="ident">pattern</span><span>: </span><a class="type-intrinsic">string</a></span>
<div class="details collapse" id="details-541">
<div class="comment"><p>A file glob pattern like <code>*.{ts,js}</code> that will be matched on file paths
relative to the base path.</p>
<p>Example: Given a base of <code>/home/work/folder</code> and a file path of <code>/home/work/folder/index.js</code>,
the file glob pattern will match on <code>index.js</code>.</p>
</div>
</div>

### <a name="RenameProvider"></a><span class="code-item" id=859>RenameProvider</span>



<div class="comment"><p>The rename provider interface defines the contract between extensions and
the <a href="https://code.visualstudio.com/docs/editor/editingevolved#_rename-symbol">rename</a>-feature.</p>
</div>

#### Methods



<a name="RenameProvider.prepareRename"></a><span class="ts" id=867 data-target="#details-867" data-toggle="collapse"><span class="ident">prepareRename</span><span>(</span><span class="ident">document</span><span>: </span><a class="type-ref" href="#TextDocument">TextDocument</a>, <span class="ident">position</span><span>: </span><a class="type-ref" href="#Position">Position</a>, <span class="ident">token</span><span>: </span><a class="type-ref" href="#CancellationToken">CancellationToken</a><span>)</span><span>: </span><a class="type-ref" href="#ProviderResult">ProviderResult</a>&lt;<a class="type-ref" href="#Range">Range</a> &#124; {placeholder: <a class="type-intrinsic">string</a>, range: <a class="type-ref" href="#Range">Range</a>}&gt;</span>
<div class="details collapse" id="details-867">
<div class="comment"><p>Optional function for resolving and validating a position <em>before</em> running rename. The result can
be a range or a range and a placeholder text. The placeholder text should be the identifier of the symbol
which is being renamed - when omitted the text in the returned range is used.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="document"></a><span class="ts" id=868 data-target="#details-868" data-toggle="collapse"><span class="ident">document</span><span>: </span><a class="type-ref" href="#TextDocument">TextDocument</a></span></td><td><div class="comment"><p>The document in which rename will be invoked.</p>
</div></td></tr>
<tr><td><a name="position"></a><span class="ts" id=869 data-target="#details-869" data-toggle="collapse"><span class="ident">position</span><span>: </span><a class="type-ref" href="#Position">Position</a></span></td><td><div class="comment"><p>The position at which rename will be invoked.</p>
</div></td></tr>
<tr><td><a name="token"></a><span class="ts" id=870 data-target="#details-870" data-toggle="collapse"><span class="ident">token</span><span>: </span><a class="type-ref" href="#CancellationToken">CancellationToken</a></span></td><td><div class="comment"><p>A cancellation token.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#ProviderResult">ProviderResult</a>&lt;<a class="type-ref" href="#Range">Range</a> &#124; {placeholder: <a class="type-intrinsic">string</a>, range: <a class="type-ref" href="#Range">Range</a>}&gt;</span></td><td><div class="comment"><p>The range or range and placeholder text of the identifier that is to be renamed. The lack of a result can signaled by returning <code>undefined</code> or <code>null</code>.</p>
</div></td></tr>
</table>
</div>
</div>



<a name="RenameProvider.provideRenameEdits"></a><span class="ts" id=861 data-target="#details-861" data-toggle="collapse"><span class="ident">provideRenameEdits</span><span>(</span><span class="ident">document</span><span>: </span><a class="type-ref" href="#TextDocument">TextDocument</a>, <span class="ident">position</span><span>: </span><a class="type-ref" href="#Position">Position</a>, <span class="ident">newName</span><span>: </span><a class="type-intrinsic">string</a>, <span class="ident">token</span><span>: </span><a class="type-ref" href="#CancellationToken">CancellationToken</a><span>)</span><span>: </span><a class="type-ref" href="#ProviderResult">ProviderResult</a>&lt;<a class="type-ref" href="#WorkspaceEdit">WorkspaceEdit</a>&gt;</span>
<div class="details collapse" id="details-861">
<div class="comment"><p>Provide an edit that describes changes that have to be made to one
or many resources to rename a symbol to a different name.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="document"></a><span class="ts" id=862 data-target="#details-862" data-toggle="collapse"><span class="ident">document</span><span>: </span><a class="type-ref" href="#TextDocument">TextDocument</a></span></td><td><div class="comment"><p>The document in which the command was invoked.</p>
</div></td></tr>
<tr><td><a name="position"></a><span class="ts" id=863 data-target="#details-863" data-toggle="collapse"><span class="ident">position</span><span>: </span><a class="type-ref" href="#Position">Position</a></span></td><td><div class="comment"><p>The position at which the command was invoked.</p>
</div></td></tr>
<tr><td><a name="newName"></a><span class="ts" id=864 data-target="#details-864" data-toggle="collapse"><span class="ident">newName</span><span>: </span><a class="type-intrinsic">string</a></span></td><td><div class="comment"><p>The new name of the symbol. If the given name is not valid, the provider must return a rejected promise.</p>
</div></td></tr>
<tr><td><a name="token"></a><span class="ts" id=865 data-target="#details-865" data-toggle="collapse"><span class="ident">token</span><span>: </span><a class="type-ref" href="#CancellationToken">CancellationToken</a></span></td><td><div class="comment"><p>A cancellation token.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#ProviderResult">ProviderResult</a>&lt;<a class="type-ref" href="#WorkspaceEdit">WorkspaceEdit</a>&gt;</span></td><td><div class="comment"><p>A workspace edit or a thenable that resolves to such. The lack of a result can be
signaled by returning <code>undefined</code> or <code>null</code>.</p>
</div></td></tr>
</table>
</div>
</div>

### <a name="RunOptions"></a><span class="code-item" id=1415>RunOptions</span>



<div class="comment"><p>Run options for a task.</p>
</div>

#### Properties



<a name="RunOptions.reevaluateOnRerun"></a><span class="ts" id=1416 data-target="#details-1416" data-toggle="collapse"><span class="ident">reevaluateOnRerun</span><span>?</span><span>: </span><a class="type-intrinsic">boolean</a></span>
<div class="details collapse" id="details-1416">
<div class="comment"><p>Controls whether task variables are re-evaluated on rerun.</p>
</div>
</div>

### <a name="SaveDialogOptions"></a><span class="code-item" id=517>SaveDialogOptions</span>



<div class="comment"><p>Options to configure the behaviour of a file save dialog.</p>
</div>

#### Properties



<a name="SaveDialogOptions.defaultUri"></a><span class="ts" id=518 data-target="#details-518" data-toggle="collapse"><span class="ident">defaultUri</span><span>?</span><span>: </span><a class="type-ref" href="#Uri">Uri</a></span>
<div class="details collapse" id="details-518">
<div class="comment"><p>The resource the dialog shows when opened.</p>
</div>
</div>



<a name="SaveDialogOptions.filters"></a><span class="ts" id=520 data-target="#details-520" data-toggle="collapse"><span class="ident">filters</span><span>?</span><span>: </span></span>
<div class="details collapse" id="details-520">
<div class="comment"><p>A set of file filters that are used by the dialog. Each entry is a human readable label,
like &quot;TypeScript&quot;, and an array of extensions, e.g.</p>

<pre><code class="lang-ts">{
    &#39;Images&#39;: [&#39;png&#39;, &#39;jpg&#39;]
    &#39;TypeScript&#39;: [&#39;ts&#39;, &#39;tsx&#39;]
}
</code></pre>
</div>
</div>



<a name="SaveDialogOptions.saveLabel"></a><span class="ts" id=519 data-target="#details-519" data-toggle="collapse"><span class="ident">saveLabel</span><span>?</span><span>: </span><a class="type-intrinsic">string</a></span>
<div class="details collapse" id="details-519">
<div class="comment"><p>A human-readable string for the save button.</p>
</div>
</div>

### <a name="Selection"></a><span class="code-item" id=152>Selection</span>



<div class="comment"><p>Represents a text selection in an editor.</p>
</div>

#### Constructors



<a name="Selection.new Selection"></a><span class="ts" id=156 data-target="#details-156" data-toggle="collapse"><span class="ident">new Selection</span><span>(</span><span class="ident">anchor</span><span>: </span><a class="type-ref" href="#Position">Position</a>, <span class="ident">active</span><span>: </span><a class="type-ref" href="#Position">Position</a><span>)</span><span>: </span><a class="type-ref" href="#Selection">Selection</a></span>
<div class="details collapse" id="details-156">
<div class="comment"><p>Create a selection from two positions.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="anchor"></a><span class="ts" id=157 data-target="#details-157" data-toggle="collapse"><span class="ident">anchor</span><span>: </span><a class="type-ref" href="#Position">Position</a></span></td><td><div class="comment"><p>A position.</p>
</div></td></tr>
<tr><td><a name="active"></a><span class="ts" id=158 data-target="#details-158" data-toggle="collapse"><span class="ident">active</span><span>: </span><a class="type-ref" href="#Position">Position</a></span></td><td><div class="comment"><p>A position.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#Selection">Selection</a></span></td><td><div class="comment"></div></td></tr>
</table>
</div>
</div>



<a name="Selection.new Selection"></a><span class="ts" id=159 data-target="#details-159" data-toggle="collapse"><span class="ident">new Selection</span><span>(</span><span class="ident">anchorLine</span><span>: </span><a class="type-intrinsic">number</a>, <span class="ident">anchorCharacter</span><span>: </span><a class="type-intrinsic">number</a>, <span class="ident">activeLine</span><span>: </span><a class="type-intrinsic">number</a>, <span class="ident">activeCharacter</span><span>: </span><a class="type-intrinsic">number</a><span>)</span><span>: </span><a class="type-ref" href="#Selection">Selection</a></span>
<div class="details collapse" id="details-159">
<div class="comment"><p>Create a selection from four coordinates.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="anchorLine"></a><span class="ts" id=160 data-target="#details-160" data-toggle="collapse"><span class="ident">anchorLine</span><span>: </span><a class="type-intrinsic">number</a></span></td><td><div class="comment"><p>A zero-based line value.</p>
</div></td></tr>
<tr><td><a name="anchorCharacter"></a><span class="ts" id=161 data-target="#details-161" data-toggle="collapse"><span class="ident">anchorCharacter</span><span>: </span><a class="type-intrinsic">number</a></span></td><td><div class="comment"><p>A zero-based character value.</p>
</div></td></tr>
<tr><td><a name="activeLine"></a><span class="ts" id=162 data-target="#details-162" data-toggle="collapse"><span class="ident">activeLine</span><span>: </span><a class="type-intrinsic">number</a></span></td><td><div class="comment"><p>A zero-based line value.</p>
</div></td></tr>
<tr><td><a name="activeCharacter"></a><span class="ts" id=163 data-target="#details-163" data-toggle="collapse"><span class="ident">activeCharacter</span><span>: </span><a class="type-intrinsic">number</a></span></td><td><div class="comment"><p>A zero-based character value.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#Selection">Selection</a></span></td><td><div class="comment"></div></td></tr>
</table>
</div>
</div>

#### Properties



<a name="Selection.active"></a><span class="ts" id=154 data-target="#details-154" data-toggle="collapse"><span class="ident">active</span><span>: </span><a class="type-ref" href="#Position">Position</a></span>
<div class="details collapse" id="details-154">
<div class="comment"><p>The position of the cursor.
This position might be before or after <a href="#Selection.anchor">anchor</a>.</p>
</div>
</div>



<a name="Selection.anchor"></a><span class="ts" id=153 data-target="#details-153" data-toggle="collapse"><span class="ident">anchor</span><span>: </span><a class="type-ref" href="#Position">Position</a></span>
<div class="details collapse" id="details-153">
<div class="comment"><p>The position at which the selection starts.
This position might be before or after <a href="#Selection.active">active</a>.</p>
</div>
</div>



<a name="Selection.end"></a><span class="ts" id=166 data-target="#details-166" data-toggle="collapse"><span class="ident">end</span><span>: </span><a class="type-ref" href="#Position">Position</a></span>
<div class="details collapse" id="details-166">
<div class="comment"><p>The end position. It is after or equal to <a href="#Range.start">start</a>.</p>
</div>
</div>



<a name="Selection.isEmpty"></a><span class="ts" id=167 data-target="#details-167" data-toggle="collapse"><span class="ident">isEmpty</span><span>: </span><a class="type-intrinsic">boolean</a></span>
<div class="details collapse" id="details-167">
<div class="comment"><p><code>true</code> if <code>start</code> and <code>end</code> are equal.</p>
</div>
</div>



<a name="Selection.isReversed"></a><span class="ts" id=164 data-target="#details-164" data-toggle="collapse"><span class="ident">isReversed</span><span>: </span><a class="type-intrinsic">boolean</a></span>
<div class="details collapse" id="details-164">
<div class="comment"><p>A selection is reversed if <a href="#Selection.active">active</a>.isBefore(<a href="#Selection.anchor">anchor</a>).</p>
</div>
</div>



<a name="Selection.isSingleLine"></a><span class="ts" id=168 data-target="#details-168" data-toggle="collapse"><span class="ident">isSingleLine</span><span>: </span><a class="type-intrinsic">boolean</a></span>
<div class="details collapse" id="details-168">
<div class="comment"><p><code>true</code> if <code>start.line</code> and <code>end.line</code> are equal.</p>
</div>
</div>



<a name="Selection.start"></a><span class="ts" id=165 data-target="#details-165" data-toggle="collapse"><span class="ident">start</span><span>: </span><a class="type-ref" href="#Position">Position</a></span>
<div class="details collapse" id="details-165">
<div class="comment"><p>The start position. It is before or equal to <a href="#Range.end">end</a>.</p>
</div>
</div>

#### Methods



<a name="Selection.contains"></a><span class="ts" id=170 data-target="#details-170" data-toggle="collapse"><span class="ident">contains</span><span>(</span><span class="ident">positionOrRange</span><span>: </span><a class="type-ref" href="#Position">Position</a> &#124; <a class="type-ref" href="#Range">Range</a><span>)</span><span>: </span><a class="type-intrinsic">boolean</a></span>
<div class="details collapse" id="details-170">
<div class="comment"><p>Check if a position or a range is contained in this range.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="positionOrRange"></a><span class="ts" id=171 data-target="#details-171" data-toggle="collapse"><span class="ident">positionOrRange</span><span>: </span><a class="type-ref" href="#Position">Position</a> &#124; <a class="type-ref" href="#Range">Range</a></span></td><td><div class="comment"><p>A position or a range.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-intrinsic">boolean</a></span></td><td><div class="comment"><p><code>true</code> if the position or range is inside or equal
to this range.</p>
</div></td></tr>
</table>
</div>
</div>



<a name="Selection.intersection"></a><span class="ts" id=176 data-target="#details-176" data-toggle="collapse"><span class="ident">intersection</span><span>(</span><span class="ident">range</span><span>: </span><a class="type-ref" href="#Range">Range</a><span>)</span><span>: </span><a class="type-ref" href="#Range">Range</a> &#124; <a class="type-intrinsic">undefined</a></span>
<div class="details collapse" id="details-176">
<div class="comment"><p>Intersect <code>range</code> with this range and returns a new range or <code>undefined</code>
if the ranges have no overlap.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="range"></a><span class="ts" id=177 data-target="#details-177" data-toggle="collapse"><span class="ident">range</span><span>: </span><a class="type-ref" href="#Range">Range</a></span></td><td><div class="comment"><p>A range.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#Range">Range</a> &#124; <a class="type-intrinsic">undefined</a></span></td><td><div class="comment"><p>A range of the greater start and smaller end positions. Will
return undefined when there is no overlap.</p>
</div></td></tr>
</table>
</div>
</div>



<a name="Selection.isEqual"></a><span class="ts" id=173 data-target="#details-173" data-toggle="collapse"><span class="ident">isEqual</span><span>(</span><span class="ident">other</span><span>: </span><a class="type-ref" href="#Range">Range</a><span>)</span><span>: </span><a class="type-intrinsic">boolean</a></span>
<div class="details collapse" id="details-173">
<div class="comment"><p>Check if <code>other</code> equals this range.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="other"></a><span class="ts" id=174 data-target="#details-174" data-toggle="collapse"><span class="ident">other</span><span>: </span><a class="type-ref" href="#Range">Range</a></span></td><td><div class="comment"><p>A range.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-intrinsic">boolean</a></span></td><td><div class="comment"><p><code>true</code> when start and end are <a href="#Position.isEqual">equal</a> to
start and end of this range.</p>
</div></td></tr>
</table>
</div>
</div>



<a name="Selection.union"></a><span class="ts" id=179 data-target="#details-179" data-toggle="collapse"><span class="ident">union</span><span>(</span><span class="ident">other</span><span>: </span><a class="type-ref" href="#Range">Range</a><span>)</span><span>: </span><a class="type-ref" href="#Range">Range</a></span>
<div class="details collapse" id="details-179">
<div class="comment"><p>Compute the union of <code>other</code> with this range.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="other"></a><span class="ts" id=180 data-target="#details-180" data-toggle="collapse"><span class="ident">other</span><span>: </span><a class="type-ref" href="#Range">Range</a></span></td><td><div class="comment"><p>A range.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#Range">Range</a></span></td><td><div class="comment"><p>A range of smaller start position and the greater end position.</p>
</div></td></tr>
</table>
</div>
</div>



<a name="Selection.with"></a><span class="ts" id=182 data-target="#details-182" data-toggle="collapse"><span class="ident">with</span><span>(</span><span class="ident">start</span><span>?</span><span>: </span><a class="type-ref" href="#Position">Position</a>, <span class="ident">end</span><span>?</span><span>: </span><a class="type-ref" href="#Position">Position</a><span>)</span><span>: </span><a class="type-ref" href="#Range">Range</a></span>
<div class="details collapse" id="details-182">
<div class="comment"><p>Derived a new range from this range.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="start"></a><span class="ts" id=183 data-target="#details-183" data-toggle="collapse"><span class="ident">start</span><span>?</span><span>: </span><a class="type-ref" href="#Position">Position</a></span></td><td><div class="comment"><p>A position that should be used as start. The default value is the <a href="#Range.start">current start</a>.</p>
</div></td></tr>
<tr><td><a name="end"></a><span class="ts" id=184 data-target="#details-184" data-toggle="collapse"><span class="ident">end</span><span>?</span><span>: </span><a class="type-ref" href="#Position">Position</a></span></td><td><div class="comment"><p>A position that should be used as end. The default value is the <a href="#Range.end">current end</a>.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#Range">Range</a></span></td><td><div class="comment"><p>A range derived from this range with the given start and end position.
If start and end are not different <code>this</code> range will be returned.</p>
</div></td></tr>
</table>
</div>
</div>



<a name="Selection.with"></a><span class="ts" id=185 data-target="#details-185" data-toggle="collapse"><span class="ident">with</span><span>(</span><span class="ident">change</span><span>: </span>{end: <a class="type-ref" href="#Position">Position</a>, start: <a class="type-ref" href="#Position">Position</a>}<span>)</span><span>: </span><a class="type-ref" href="#Range">Range</a></span>
<div class="details collapse" id="details-185">
<div class="comment"><p>Derived a new range from this range.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="change"></a><span class="ts" id=186 data-target="#details-186" data-toggle="collapse"><span class="ident">change</span><span>: </span>{end: <a class="type-ref" href="#Position">Position</a>, start: <a class="type-ref" href="#Position">Position</a>}</span></td><td><div class="comment"><p>An object that describes a change to this range.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#Range">Range</a></span></td><td><div class="comment"><p>A range that reflects the given change. Will return <code>this</code> range if the change
is not changing anything.</p>
</div></td></tr>
</table>
</div>
</div>

### <a name="ShellExecution"></a><span class="code-item" id=1399>ShellExecution</span>



<div class="comment"></div>

#### Constructors



<a name="ShellExecution.new ShellExecution"></a><span class="ts" id=1401 data-target="#details-1401" data-toggle="collapse"><span class="ident">new ShellExecution</span><span>(</span><span class="ident">commandLine</span><span>: </span><a class="type-intrinsic">string</a>, <span class="ident">options</span><span>?</span><span>: </span><a class="type-ref" href="#ShellExecutionOptions">ShellExecutionOptions</a><span>)</span><span>: </span><a class="type-ref" href="#ShellExecution">ShellExecution</a></span>
<div class="details collapse" id="details-1401">
<div class="comment"><p>Creates a shell execution with a full command line.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="commandLine"></a><span class="ts" id=1402 data-target="#details-1402" data-toggle="collapse"><span class="ident">commandLine</span><span>: </span><a class="type-intrinsic">string</a></span></td><td><div class="comment"><p>The command line to execute.</p>
</div></td></tr>
<tr><td><a name="options"></a><span class="ts" id=1403 data-target="#details-1403" data-toggle="collapse"><span class="ident">options</span><span>?</span><span>: </span><a class="type-ref" href="#ShellExecutionOptions">ShellExecutionOptions</a></span></td><td><div class="comment"><p>Optional options for the started the shell.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#ShellExecution">ShellExecution</a></span></td><td><div class="comment"></div></td></tr>
</table>
</div>
</div>



<a name="ShellExecution.new ShellExecution"></a><span class="ts" id=1404 data-target="#details-1404" data-toggle="collapse"><span class="ident">new ShellExecution</span><span>(</span><span class="ident">command</span><span>: </span><a class="type-intrinsic">string</a> &#124; <a class="type-ref" href="#ShellQuotedString">ShellQuotedString</a>, <span class="ident">args</span><span>: </span><a class="type-intrinsic">string</a> &#124; <a class="type-ref" href="#ShellQuotedString">ShellQuotedString</a>[], <span class="ident">options</span><span>?</span><span>: </span><a class="type-ref" href="#ShellExecutionOptions">ShellExecutionOptions</a><span>)</span><span>: </span><a class="type-ref" href="#ShellExecution">ShellExecution</a></span>
<div class="details collapse" id="details-1404">
<div class="comment"><p>Creates a shell execution with a command and arguments. For the real execution VS Code will
construct a command line from the command and the arguments. This is subject to interpretation
especially when it comes to quoting. If full control over the command line is needed please
use the constructor that creates a <code>ShellExecution</code> with the full command line.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="command"></a><span class="ts" id=1405 data-target="#details-1405" data-toggle="collapse"><span class="ident">command</span><span>: </span><a class="type-intrinsic">string</a> &#124; <a class="type-ref" href="#ShellQuotedString">ShellQuotedString</a></span></td><td><div class="comment"><p>The command to execute.</p>
</div></td></tr>
<tr><td><a name="args"></a><span class="ts" id=1406 data-target="#details-1406" data-toggle="collapse"><span class="ident">args</span><span>: </span><a class="type-intrinsic">string</a> &#124; <a class="type-ref" href="#ShellQuotedString">ShellQuotedString</a>[]</span></td><td><div class="comment"><p>The command arguments.</p>
</div></td></tr>
<tr><td><a name="options"></a><span class="ts" id=1407 data-target="#details-1407" data-toggle="collapse"><span class="ident">options</span><span>?</span><span>: </span><a class="type-ref" href="#ShellExecutionOptions">ShellExecutionOptions</a></span></td><td><div class="comment"><p>Optional options for the started the shell.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#ShellExecution">ShellExecution</a></span></td><td><div class="comment"></div></td></tr>
</table>
</div>
</div>

#### Properties



<a name="ShellExecution.args"></a><span class="ts" id=1410 data-target="#details-1410" data-toggle="collapse"><span class="ident">args</span><span>: </span><a class="type-intrinsic">string</a> &#124; <a class="type-ref" href="#ShellQuotedString">ShellQuotedString</a>[]</span>
<div class="details collapse" id="details-1410">
<div class="comment"><p>The shell args. Is <code>undefined</code> if created with a full command line.</p>
</div>
</div>



<a name="ShellExecution.command"></a><span class="ts" id=1409 data-target="#details-1409" data-toggle="collapse"><span class="ident">command</span><span>: </span><a class="type-intrinsic">string</a> &#124; <a class="type-ref" href="#ShellQuotedString">ShellQuotedString</a></span>
<div class="details collapse" id="details-1409">
<div class="comment"><p>The shell command. Is <code>undefined</code> if created with a full command line.</p>
</div>
</div>



<a name="ShellExecution.commandLine"></a><span class="ts" id=1408 data-target="#details-1408" data-toggle="collapse"><span class="ident">commandLine</span><span>: </span><a class="type-intrinsic">string</a></span>
<div class="details collapse" id="details-1408">
<div class="comment"><p>The shell command line. Is <code>undefined</code> if created with a command and arguments.</p>
</div>
</div>



<a name="ShellExecution.options"></a><span class="ts" id=1411 data-target="#details-1411" data-toggle="collapse"><span class="ident">options</span><span>?</span><span>: </span><a class="type-ref" href="#ShellExecutionOptions">ShellExecutionOptions</a></span>
<div class="details collapse" id="details-1411">
<div class="comment"><p>The shell options used when the command line is executed in a shell.
Defaults to undefined.</p>
</div>
</div>

### <a name="ShellExecutionOptions"></a><span class="code-item" id=1383>ShellExecutionOptions</span>



<div class="comment"><p>Options for a shell execution</p>
</div>

#### Properties



<a name="ShellExecutionOptions.cwd"></a><span class="ts" id=1387 data-target="#details-1387" data-toggle="collapse"><span class="ident">cwd</span><span>?</span><span>: </span><a class="type-intrinsic">string</a></span>
<div class="details collapse" id="details-1387">
<div class="comment"><p>The current working directory of the executed shell.
If omitted the tools current workspace root is used.</p>
</div>
</div>



<a name="ShellExecutionOptions.env"></a><span class="ts" id=1388 data-target="#details-1388" data-toggle="collapse"><span class="ident">env</span><span>?</span><span>: </span></span>
<div class="details collapse" id="details-1388">
<div class="comment"><p>The additional environment of the executed shell. If omitted
the parent process&#39; environment is used. If provided it is merged with
the parent process&#39; environment.</p>
</div>
</div>



<a name="ShellExecutionOptions.executable"></a><span class="ts" id=1384 data-target="#details-1384" data-toggle="collapse"><span class="ident">executable</span><span>?</span><span>: </span><a class="type-intrinsic">string</a></span>
<div class="details collapse" id="details-1384">
<div class="comment"><p>The shell executable.</p>
</div>
</div>



<a name="ShellExecutionOptions.shellArgs"></a><span class="ts" id=1385 data-target="#details-1385" data-toggle="collapse"><span class="ident">shellArgs</span><span>?</span><span>: </span><a class="type-intrinsic">string</a>[]</span>
<div class="details collapse" id="details-1385">
<div class="comment"><p>The arguments to be passed to the shell executable used to run the task. Most shells
require special arguments to execute a command. For  example <code>bash</code> requires the <code>-c</code>
argument to execute a command, <code>PowerShell</code> requires <code>-Command</code> and <code>cmd</code> requires both
<code>/d</code> and <code>/c</code>.</p>
</div>
</div>



<a name="ShellExecutionOptions.shellQuoting"></a><span class="ts" id=1386 data-target="#details-1386" data-toggle="collapse"><span class="ident">shellQuoting</span><span>?</span><span>: </span><a class="type-ref" href="#ShellQuotingOptions">ShellQuotingOptions</a></span>
<div class="details collapse" id="details-1386">
<div class="comment"><p>The shell quotes supported by this shell.</p>
</div>
</div>

### <a name="ShellQuotedString"></a><span class="code-item" id=1396>ShellQuotedString</span>



<div class="comment"><p>A string that will be quoted depending on the used shell.</p>
</div>

#### Properties



<a name="ShellQuotedString.quoting"></a><span class="ts" id=1398 data-target="#details-1398" data-toggle="collapse"><span class="ident">quoting</span><span>: </span><a class="type-ref" href="#ShellQuoting">ShellQuoting</a></span>
<div class="details collapse" id="details-1398">
<div class="comment"><p>The quoting style to use.</p>
</div>
</div>



<a name="ShellQuotedString.value"></a><span class="ts" id=1397 data-target="#details-1397" data-toggle="collapse"><span class="ident">value</span><span>: </span><a class="type-intrinsic">string</a></span>
<div class="details collapse" id="details-1397">
<div class="comment"><p>The actual string value.</p>
</div>
</div>

### <a name="ShellQuoting"></a><span class="code-item" id=1392>ShellQuoting</span>



<div class="comment"><p>Defines how an argument should be quoted if it contains
spaces or unsupported characters.</p>
</div>

#### Enumeration members



<a name="ShellQuoting.Escape"></a><span class="ts" id=1393 data-target="#details-1393" data-toggle="collapse"><span class="ident">Escape</span></span>
<div class="details collapse" id="details-1393">
<em>1</em>
</div>



<a name="ShellQuoting.Strong"></a><span class="ts" id=1394 data-target="#details-1394" data-toggle="collapse"><span class="ident">Strong</span></span>
<div class="details collapse" id="details-1394">
<em>2</em>
</div>



<a name="ShellQuoting.Weak"></a><span class="ts" id=1395 data-target="#details-1395" data-toggle="collapse"><span class="ident">Weak</span></span>
<div class="details collapse" id="details-1395">
<em>3</em>
</div>

### <a name="ShellQuotingOptions"></a><span class="code-item" id=1376>ShellQuotingOptions</span>



<div class="comment"><p>The shell quoting options.</p>
</div>

#### Properties



<a name="ShellQuotingOptions.escape"></a><span class="ts" id=1377 data-target="#details-1377" data-toggle="collapse"><span class="ident">escape</span><span>?</span><span>: </span><a class="type-intrinsic">string</a> &#124; {charsToEscape: <a class="type-intrinsic">string</a>, escapeChar: <a class="type-intrinsic">string</a>}</span>
<div class="details collapse" id="details-1377">
<div class="comment"><p>The character used to do character escaping. If a string is provided only spaces
are escaped. If a <code>{ escapeChar, charsToEscape }</code> literal is provide all characters
in <code>charsToEscape</code> are escaped using the <code>escapeChar</code>.</p>
</div>
</div>



<a name="ShellQuotingOptions.strong"></a><span class="ts" id=1381 data-target="#details-1381" data-toggle="collapse"><span class="ident">strong</span><span>?</span><span>: </span><a class="type-intrinsic">string</a></span>
<div class="details collapse" id="details-1381">
<div class="comment"><p>The character used for strong quoting. The string&#39;s length must be 1.</p>
</div>
</div>



<a name="ShellQuotingOptions.weak"></a><span class="ts" id=1382 data-target="#details-1382" data-toggle="collapse"><span class="ident">weak</span><span>?</span><span>: </span><a class="type-intrinsic">string</a></span>
<div class="details collapse" id="details-1382">
<div class="comment"><p>The character used for weak quoting. The string&#39;s length must be 1.</p>
</div>
</div>

### <a name="SignatureHelp"></a><span class="code-item" id=915>SignatureHelp</span>



<div class="comment"><p>Signature help represents the signature of something
callable. There can be multiple signatures but only one
active and only one active parameter.</p>
</div>

#### Properties



<a name="SignatureHelp.activeParameter"></a><span class="ts" id=918 data-target="#details-918" data-toggle="collapse"><span class="ident">activeParameter</span><span>: </span><a class="type-intrinsic">number</a></span>
<div class="details collapse" id="details-918">
<div class="comment"><p>The active parameter of the active signature.</p>
</div>
</div>



<a name="SignatureHelp.activeSignature"></a><span class="ts" id=917 data-target="#details-917" data-toggle="collapse"><span class="ident">activeSignature</span><span>: </span><a class="type-intrinsic">number</a></span>
<div class="details collapse" id="details-917">
<div class="comment"><p>The active signature.</p>
</div>
</div>



<a name="SignatureHelp.signatures"></a><span class="ts" id=916 data-target="#details-916" data-toggle="collapse"><span class="ident">signatures</span><span>: </span><a class="type-ref" href="#SignatureInformation">SignatureInformation</a>[]</span>
<div class="details collapse" id="details-916">
<div class="comment"><p>One or more signatures.</p>
</div>
</div>

### <a name="SignatureHelpContext"></a><span class="code-item" id=923>SignatureHelpContext</span>



<div class="comment"><p>Additional information about the context in which a
<a href="#SignatureHelpProvider.provideSignatureHelp"><code>SignatureHelpProvider</code></a> was triggered.</p>
</div>

#### Properties



<a name="SignatureHelpContext.isRetrigger"></a><span class="ts" id=926 data-target="#details-926" data-toggle="collapse"><span class="ident">isRetrigger</span><span>: </span><a class="type-intrinsic">boolean</a></span>
<div class="details collapse" id="details-926">
<div class="comment"><p><code>true</code> if signature help was already showing when it was triggered.</p>
<p>Retriggers occur when the signature help is already active and can be caused by actions such as
typing a trigger character, a cursor move, or document content changes.</p>
</div>
</div>



<a name="SignatureHelpContext.triggerCharacter"></a><span class="ts" id=925 data-target="#details-925" data-toggle="collapse"><span class="ident">triggerCharacter</span><span>?</span><span>: </span><a class="type-intrinsic">string</a></span>
<div class="details collapse" id="details-925">
<div class="comment"><p>Character that caused signature help to be triggered.</p>
<p>This is <code>undefined</code> when signature help is not triggered by typing, such as when manually invoking
signature help or when moving the cursor.</p>
</div>
</div>



<a name="SignatureHelpContext.triggerKind"></a><span class="ts" id=924 data-target="#details-924" data-toggle="collapse"><span class="ident">triggerKind</span><span>: </span><a class="type-ref" href="#SignatureHelpTriggerKind">SignatureHelpTriggerKind</a></span>
<div class="details collapse" id="details-924">
<div class="comment"><p>Action that caused signature help to be triggered.</p>
</div>
</div>

### <a name="SignatureHelpProvider"></a><span class="code-item" id=927>SignatureHelpProvider</span>



<div class="comment"><p>The signature help provider interface defines the contract between extensions and
the <a href="https://code.visualstudio.com/docs/editor/intellisense">parameter hints</a>-feature.</p>
</div>

#### Methods



<a name="SignatureHelpProvider.provideSignatureHelp"></a><span class="ts" id=929 data-target="#details-929" data-toggle="collapse"><span class="ident">provideSignatureHelp</span><span>(</span><span class="ident">document</span><span>: </span><a class="type-ref" href="#TextDocument">TextDocument</a>, <span class="ident">position</span><span>: </span><a class="type-ref" href="#Position">Position</a>, <span class="ident">token</span><span>: </span><a class="type-ref" href="#CancellationToken">CancellationToken</a>, <span class="ident">context</span><span>: </span><a class="type-ref" href="#SignatureHelpContext">SignatureHelpContext</a><span>)</span><span>: </span><a class="type-ref" href="#ProviderResult">ProviderResult</a>&lt;<a class="type-ref" href="#SignatureHelp">SignatureHelp</a>&gt;</span>
<div class="details collapse" id="details-929">
<div class="comment"><p>Provide help for the signature at the given position and document.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="document"></a><span class="ts" id=930 data-target="#details-930" data-toggle="collapse"><span class="ident">document</span><span>: </span><a class="type-ref" href="#TextDocument">TextDocument</a></span></td><td><div class="comment"><p>The document in which the command was invoked.</p>
</div></td></tr>
<tr><td><a name="position"></a><span class="ts" id=931 data-target="#details-931" data-toggle="collapse"><span class="ident">position</span><span>: </span><a class="type-ref" href="#Position">Position</a></span></td><td><div class="comment"><p>The position at which the command was invoked.</p>
</div></td></tr>
<tr><td><a name="token"></a><span class="ts" id=932 data-target="#details-932" data-toggle="collapse"><span class="ident">token</span><span>: </span><a class="type-ref" href="#CancellationToken">CancellationToken</a></span></td><td><div class="comment"><p>A cancellation token.</p>
</div></td></tr>
<tr><td><a name="context"></a><span class="ts" id=933 data-target="#details-933" data-toggle="collapse"><span class="ident">context</span><span>: </span><a class="type-ref" href="#SignatureHelpContext">SignatureHelpContext</a></span></td><td><div class="comment"><p>Information about how signature help was triggered.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#ProviderResult">ProviderResult</a>&lt;<a class="type-ref" href="#SignatureHelp">SignatureHelp</a>&gt;</span></td><td><div class="comment"><p>Signature help or a thenable that resolves to such. The lack of a result can be
signaled by returning <code>undefined</code> or <code>null</code>.</p>
</div></td></tr>
</table>
</div>
</div>

### <a name="SignatureHelpProviderMetadata"></a><span class="code-item" id=934>SignatureHelpProviderMetadata</span>



<div class="comment"><p>Metadata about a registered <a href="#SignatureHelpProvider"><code>SignatureHelpProvider</code></a>.</p>
</div>

#### Properties



<a name="SignatureHelpProviderMetadata.retriggerCharacters"></a><span class="ts" id=936 data-target="#details-936" data-toggle="collapse"><span class="ident">retriggerCharacters</span><span>: </span><a class="type-ref" href="#ReadonlyArray">ReadonlyArray</a>&lt;<a class="type-intrinsic">string</a>&gt;</span>
<div class="details collapse" id="details-936">
<div class="comment"><p>List of characters that re-trigger signature help.</p>
<p>These trigger characters are only active when signature help is alread showing. All trigger characters
are also counted as re-trigger characters.</p>
</div>
</div>



<a name="SignatureHelpProviderMetadata.triggerCharacters"></a><span class="ts" id=935 data-target="#details-935" data-toggle="collapse"><span class="ident">triggerCharacters</span><span>: </span><a class="type-ref" href="#ReadonlyArray">ReadonlyArray</a>&lt;<a class="type-intrinsic">string</a>&gt;</span>
<div class="details collapse" id="details-935">
<div class="comment"><p>List of characters that trigger signature help.</p>
</div>
</div>

### <a name="SignatureHelpTriggerKind"></a><span class="code-item" id=919>SignatureHelpTriggerKind</span>



<div class="comment"><p>How a <a href="#SignatureHelpProvider"><code>SignatureHelpProvider</code></a> was triggered.</p>
</div>

#### Enumeration members



<a name="SignatureHelpTriggerKind.ContentChange"></a><span class="ts" id=922 data-target="#details-922" data-toggle="collapse"><span class="ident">ContentChange</span></span>
<div class="details collapse" id="details-922">
<em>3</em>
</div>



<a name="SignatureHelpTriggerKind.Invoke"></a><span class="ts" id=920 data-target="#details-920" data-toggle="collapse"><span class="ident">Invoke</span></span>
<div class="details collapse" id="details-920">
<em>1</em>
</div>



<a name="SignatureHelpTriggerKind.TriggerCharacter"></a><span class="ts" id=921 data-target="#details-921" data-toggle="collapse"><span class="ident">TriggerCharacter</span></span>
<div class="details collapse" id="details-921">
<em>2</em>
</div>

### <a name="SignatureInformation"></a><span class="code-item" id=907>SignatureInformation</span>



<div class="comment"><p>Represents the signature of something callable. A signature
can have a label, like a function-name, a doc-comment, and
a set of parameters.</p>
</div>

#### Constructors



<a name="SignatureInformation.new SignatureInformation"></a><span class="ts" id=912 data-target="#details-912" data-toggle="collapse"><span class="ident">new SignatureInformation</span><span>(</span><span class="ident">label</span><span>: </span><a class="type-intrinsic">string</a>, <span class="ident">documentation</span><span>?</span><span>: </span><a class="type-intrinsic">string</a> &#124; <a class="type-ref" href="#MarkdownString">MarkdownString</a><span>)</span><span>: </span><a class="type-ref" href="#SignatureInformation">SignatureInformation</a></span>
<div class="details collapse" id="details-912">
<div class="comment"><p>Creates a new signature information object.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="label"></a><span class="ts" id=913 data-target="#details-913" data-toggle="collapse"><span class="ident">label</span><span>: </span><a class="type-intrinsic">string</a></span></td><td><div class="comment"><p>A label string.</p>
</div></td></tr>
<tr><td><a name="documentation"></a><span class="ts" id=914 data-target="#details-914" data-toggle="collapse"><span class="ident">documentation</span><span>?</span><span>: </span><a class="type-intrinsic">string</a> &#124; <a class="type-ref" href="#MarkdownString">MarkdownString</a></span></td><td><div class="comment"><p>A doc string.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#SignatureInformation">SignatureInformation</a></span></td><td><div class="comment"></div></td></tr>
</table>
</div>
</div>

#### Properties



<a name="SignatureInformation.documentation"></a><span class="ts" id=909 data-target="#details-909" data-toggle="collapse"><span class="ident">documentation</span><span>?</span><span>: </span><a class="type-intrinsic">string</a> &#124; <a class="type-ref" href="#MarkdownString">MarkdownString</a></span>
<div class="details collapse" id="details-909">
<div class="comment"><p>The human-readable doc-comment of this signature. Will be shown
in the UI but can be omitted.</p>
</div>
</div>



<a name="SignatureInformation.label"></a><span class="ts" id=908 data-target="#details-908" data-toggle="collapse"><span class="ident">label</span><span>: </span><a class="type-intrinsic">string</a></span>
<div class="details collapse" id="details-908">
<div class="comment"><p>The label of this signature. Will be shown in
the UI.</p>
</div>
</div>



<a name="SignatureInformation.parameters"></a><span class="ts" id=910 data-target="#details-910" data-toggle="collapse"><span class="ident">parameters</span><span>: </span><a class="type-ref" href="#ParameterInformation">ParameterInformation</a>[]</span>
<div class="details collapse" id="details-910">
<div class="comment"><p>The parameters of this signature.</p>
</div>
</div>

### <a name="SnippetString"></a><span class="code-item" id=834>SnippetString</span>



<div class="comment"><p>A snippet string is a template which allows to insert text
and to control the editor cursor when insertion happens.</p>
<p>A snippet can define tab stops and placeholders with <code>$1</code>, <code>$2</code>
and <code>${3:foo}</code>. <code>$0</code> defines the final tab stop, it defaults to
the end of the snippet. Variables are defined with <code>$name</code> and
<code>${name:default value}</code>. The full snippet syntax is documented
<a href="http://code.visualstudio.com/docs/editor/userdefinedsnippets#_creating-your-own-snippets">here</a>.</p>
</div>

#### Constructors



<a name="SnippetString.new SnippetString"></a><span class="ts" id=837 data-target="#details-837" data-toggle="collapse"><span class="ident">new SnippetString</span><span>(</span><span class="ident">value</span><span>?</span><span>: </span><a class="type-intrinsic">string</a><span>)</span><span>: </span><a class="type-ref" href="#SnippetString">SnippetString</a></span>
<div class="details collapse" id="details-837">
<div class="comment"></div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="value"></a><span class="ts" id=838 data-target="#details-838" data-toggle="collapse"><span class="ident">value</span><span>?</span><span>: </span><a class="type-intrinsic">string</a></span></td><td><div class="comment"></div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#SnippetString">SnippetString</a></span></td><td><div class="comment"></div></td></tr>
</table>
</div>
</div>

#### Properties



<a name="SnippetString.value"></a><span class="ts" id=835 data-target="#details-835" data-toggle="collapse"><span class="ident">value</span><span>: </span><a class="type-intrinsic">string</a></span>
<div class="details collapse" id="details-835">
<div class="comment"><p>The snippet string.</p>
</div>
</div>

#### Methods



<a name="SnippetString.appendPlaceholder"></a><span class="ts" id=846 data-target="#details-846" data-toggle="collapse"><span class="ident">appendPlaceholder</span><span>(</span><span class="ident">value</span><span>: </span><a class="type-intrinsic">string</a> &#124; (snippet: <a class="type-ref" href="#SnippetString">SnippetString</a>) =&gt; <a class="type-intrinsic">any</a>, <span class="ident">number</span><span>?</span><span>: </span><a class="type-intrinsic">number</a><span>)</span><span>: </span><a class="type-ref" href="#SnippetString">SnippetString</a></span>
<div class="details collapse" id="details-846">
<div class="comment"><p>Builder-function that appends a placeholder (<code>${1:value}</code>) to
the <a href="#SnippetString.value"><code>value</code></a> of this snippet string.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="value"></a><span class="ts" id=847 data-target="#details-847" data-toggle="collapse"><span class="ident">value</span><span>: </span><a class="type-intrinsic">string</a> &#124; (snippet: <a class="type-ref" href="#SnippetString">SnippetString</a>) =&gt; <a class="type-intrinsic">any</a></span></td><td><div class="comment"><p>The value of this placeholder - either a string or a function
with which a nested snippet can be created.</p>
</div></td></tr>
<tr><td><a name="number"></a><span class="ts" id=851 data-target="#details-851" data-toggle="collapse"><span class="ident">number</span><span>?</span><span>: </span><a class="type-intrinsic">number</a></span></td><td><div class="comment"><p>The number of this tabstop, defaults to an auto-increment
value starting at 1.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#SnippetString">SnippetString</a></span></td><td><div class="comment"><p>This snippet string.</p>
</div></td></tr>
</table>
</div>
</div>



<a name="SnippetString.appendTabstop"></a><span class="ts" id=843 data-target="#details-843" data-toggle="collapse"><span class="ident">appendTabstop</span><span>(</span><span class="ident">number</span><span>?</span><span>: </span><a class="type-intrinsic">number</a><span>)</span><span>: </span><a class="type-ref" href="#SnippetString">SnippetString</a></span>
<div class="details collapse" id="details-843">
<div class="comment"><p>Builder-function that appends a tabstop (<code>$1</code>, <code>$2</code> etc) to
the <a href="#SnippetString.value"><code>value</code></a> of this snippet string.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="number"></a><span class="ts" id=844 data-target="#details-844" data-toggle="collapse"><span class="ident">number</span><span>?</span><span>: </span><a class="type-intrinsic">number</a></span></td><td><div class="comment"><p>The number of this tabstop, defaults to an auto-increment
value starting at 1.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#SnippetString">SnippetString</a></span></td><td><div class="comment"><p>This snippet string.</p>
</div></td></tr>
</table>
</div>
</div>



<a name="SnippetString.appendText"></a><span class="ts" id=840 data-target="#details-840" data-toggle="collapse"><span class="ident">appendText</span><span>(</span><span class="ident">string</span><span>: </span><a class="type-intrinsic">string</a><span>)</span><span>: </span><a class="type-ref" href="#SnippetString">SnippetString</a></span>
<div class="details collapse" id="details-840">
<div class="comment"><p>Builder-function that appends the given string to
the <a href="#SnippetString.value"><code>value</code></a> of this snippet string.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="string"></a><span class="ts" id=841 data-target="#details-841" data-toggle="collapse"><span class="ident">string</span><span>: </span><a class="type-intrinsic">string</a></span></td><td><div class="comment"><p>A value to append &#39;as given&#39;. The string will be escaped.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#SnippetString">SnippetString</a></span></td><td><div class="comment"><p>This snippet string.</p>
</div></td></tr>
</table>
</div>
</div>



<a name="SnippetString.appendVariable"></a><span class="ts" id=853 data-target="#details-853" data-toggle="collapse"><span class="ident">appendVariable</span><span>(</span><span class="ident">name</span><span>: </span><a class="type-intrinsic">string</a>, <span class="ident">defaultValue</span><span>: </span><a class="type-intrinsic">string</a> &#124; (snippet: <a class="type-ref" href="#SnippetString">SnippetString</a>) =&gt; <a class="type-intrinsic">any</a><span>)</span><span>: </span><a class="type-ref" href="#SnippetString">SnippetString</a></span>
<div class="details collapse" id="details-853">
<div class="comment"><p>Builder-function that appends a variable (<code>${VAR}</code>) to
the <a href="#SnippetString.value"><code>value</code></a> of this snippet string.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="name"></a><span class="ts" id=854 data-target="#details-854" data-toggle="collapse"><span class="ident">name</span><span>: </span><a class="type-intrinsic">string</a></span></td><td><div class="comment"><p>The name of the variable - excluding the <code>$</code>.</p>
</div></td></tr>
<tr><td><a name="defaultValue"></a><span class="ts" id=855 data-target="#details-855" data-toggle="collapse"><span class="ident">defaultValue</span><span>: </span><a class="type-intrinsic">string</a> &#124; (snippet: <a class="type-ref" href="#SnippetString">SnippetString</a>) =&gt; <a class="type-intrinsic">any</a></span></td><td><div class="comment"><p>The default value which is used when the variable name cannot
be resolved - either a string or a function with which a nested snippet can be created.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#SnippetString">SnippetString</a></span></td><td><div class="comment"><p>This snippet string.</p>
</div></td></tr>
</table>
</div>
</div>

### <a name="SourceBreakpoint"></a><span class="code-item" id=1959>SourceBreakpoint</span>



<div class="comment"><p>A breakpoint specified by a source location.</p>
</div>

#### Constructors



<a name="SourceBreakpoint.new SourceBreakpoint"></a><span class="ts" id=1962 data-target="#details-1962" data-toggle="collapse"><span class="ident">new SourceBreakpoint</span><span>(</span><span class="ident">location</span><span>: </span><a class="type-ref" href="#Location">Location</a>, <span class="ident">enabled</span><span>?</span><span>: </span><a class="type-intrinsic">boolean</a>, <span class="ident">condition</span><span>?</span><span>: </span><a class="type-intrinsic">string</a>, <span class="ident">hitCondition</span><span>?</span><span>: </span><a class="type-intrinsic">string</a>, <span class="ident">logMessage</span><span>?</span><span>: </span><a class="type-intrinsic">string</a><span>)</span><span>: </span><a class="type-ref" href="#SourceBreakpoint">SourceBreakpoint</a></span>
<div class="details collapse" id="details-1962">
<div class="comment"><p>Create a new breakpoint for a source location.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="location"></a><span class="ts" id=1963 data-target="#details-1963" data-toggle="collapse"><span class="ident">location</span><span>: </span><a class="type-ref" href="#Location">Location</a></span></td><td><div class="comment"></div></td></tr>
<tr><td><a name="enabled"></a><span class="ts" id=1964 data-target="#details-1964" data-toggle="collapse"><span class="ident">enabled</span><span>?</span><span>: </span><a class="type-intrinsic">boolean</a></span></td><td><div class="comment"></div></td></tr>
<tr><td><a name="condition"></a><span class="ts" id=1965 data-target="#details-1965" data-toggle="collapse"><span class="ident">condition</span><span>?</span><span>: </span><a class="type-intrinsic">string</a></span></td><td><div class="comment"></div></td></tr>
<tr><td><a name="hitCondition"></a><span class="ts" id=1966 data-target="#details-1966" data-toggle="collapse"><span class="ident">hitCondition</span><span>?</span><span>: </span><a class="type-intrinsic">string</a></span></td><td><div class="comment"></div></td></tr>
<tr><td><a name="logMessage"></a><span class="ts" id=1967 data-target="#details-1967" data-toggle="collapse"><span class="ident">logMessage</span><span>?</span><span>: </span><a class="type-intrinsic">string</a></span></td><td><div class="comment"></div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#SourceBreakpoint">SourceBreakpoint</a></span></td><td><div class="comment"></div></td></tr>
</table>
</div>
</div>

#### Properties



<a name="SourceBreakpoint.condition"></a><span class="ts" id=1970 data-target="#details-1970" data-toggle="collapse"><span class="ident">condition</span><span>?</span><span>: </span><a class="type-intrinsic">string</a></span>
<div class="details collapse" id="details-1970">
<div class="comment"><p>An optional expression for conditional breakpoints.</p>
</div>
</div>



<a name="SourceBreakpoint.enabled"></a><span class="ts" id=1969 data-target="#details-1969" data-toggle="collapse"><span class="ident">enabled</span><span>: </span><a class="type-intrinsic">boolean</a></span>
<div class="details collapse" id="details-1969">
<div class="comment"><p>Is breakpoint enabled.</p>
</div>
</div>



<a name="SourceBreakpoint.hitCondition"></a><span class="ts" id=1971 data-target="#details-1971" data-toggle="collapse"><span class="ident">hitCondition</span><span>?</span><span>: </span><a class="type-intrinsic">string</a></span>
<div class="details collapse" id="details-1971">
<div class="comment"><p>An optional expression that controls how many hits of the breakpoint are ignored.</p>
</div>
</div>



<a name="SourceBreakpoint.id"></a><span class="ts" id=1968 data-target="#details-1968" data-toggle="collapse"><span class="ident">id</span><span>: </span><a class="type-intrinsic">string</a></span>
<div class="details collapse" id="details-1968">
<div class="comment"><p>The unique ID of the breakpoint.</p>
</div>
</div>



<a name="SourceBreakpoint.location"></a><span class="ts" id=1960 data-target="#details-1960" data-toggle="collapse"><span class="ident">location</span><span>: </span><a class="type-ref" href="#Location">Location</a></span>
<div class="details collapse" id="details-1960">
<div class="comment"><p>The source and line position of this breakpoint.</p>
</div>
</div>



<a name="SourceBreakpoint.logMessage"></a><span class="ts" id=1972 data-target="#details-1972" data-toggle="collapse"><span class="ident">logMessage</span><span>?</span><span>: </span><a class="type-intrinsic">string</a></span>
<div class="details collapse" id="details-1972">
<div class="comment"><p>An optional message that gets logged when this breakpoint is hit. Embedded expressions within {} are interpolated by the debug adapter.</p>
</div>
</div>

### <a name="SourceControl"></a><span class="code-item" id=1841>SourceControl</span>



<div class="comment"><p>An source control is able to provide <a href="#SourceControlResourceState">resource states</a>
to the editor and interact with the editor in several source control related ways.</p>
</div>

#### Properties



<a name="SourceControl.acceptInputCommand"></a><span class="ts" id=1849 data-target="#details-1849" data-toggle="collapse"><span class="ident">acceptInputCommand</span><span>?</span><span>: </span><a class="type-ref" href="#Command">Command</a></span>
<div class="details collapse" id="details-1849">
<div class="comment"><p>Optional accept input command.</p>
<p>This command will be invoked when the user accepts the value
in the Source Control input.</p>
</div>
</div>



<a name="SourceControl.commitTemplate"></a><span class="ts" id=1848 data-target="#details-1848" data-toggle="collapse"><span class="ident">commitTemplate</span><span>?</span><span>: </span><a class="type-intrinsic">string</a></span>
<div class="details collapse" id="details-1848">
<div class="comment"><p>Optional commit template string.</p>
<p>The Source Control viewlet will populate the Source Control
input with this value when appropriate.</p>
</div>
</div>



<a name="SourceControl.count"></a><span class="ts" id=1846 data-target="#details-1846" data-toggle="collapse"><span class="ident">count</span><span>?</span><span>: </span><a class="type-intrinsic">number</a></span>
<div class="details collapse" id="details-1846">
<div class="comment"><p>The UI-visible count of <a href="#SourceControlResourceState">resource states</a> of
this source control.</p>
<p>Equals to the total number of <a href="#SourceControlResourceState">resource state</a>
of this source control, if undefined.</p>
</div>
</div>



<a name="SourceControl.id"></a><span class="ts" id=1842 data-target="#details-1842" data-toggle="collapse"><span class="ident">id</span><span>: </span><a class="type-intrinsic">string</a></span>
<div class="details collapse" id="details-1842">
<div class="comment"><p>The id of this source control.</p>
</div>
</div>



<a name="SourceControl.inputBox"></a><span class="ts" id=1845 data-target="#details-1845" data-toggle="collapse"><span class="ident">inputBox</span><span>: </span><a class="type-ref" href="#SourceControlInputBox">SourceControlInputBox</a></span>
<div class="details collapse" id="details-1845">
<div class="comment"><p>The <a href="#SourceControlInputBox">input box</a> for this source control.</p>
</div>
</div>



<a name="SourceControl.label"></a><span class="ts" id=1843 data-target="#details-1843" data-toggle="collapse"><span class="ident">label</span><span>: </span><a class="type-intrinsic">string</a></span>
<div class="details collapse" id="details-1843">
<div class="comment"><p>The human-readable label of this source control.</p>
</div>
</div>



<a name="SourceControl.quickDiffProvider"></a><span class="ts" id=1847 data-target="#details-1847" data-toggle="collapse"><span class="ident">quickDiffProvider</span><span>?</span><span>: </span><a class="type-ref" href="#QuickDiffProvider">QuickDiffProvider</a></span>
<div class="details collapse" id="details-1847">
<div class="comment"><p>An optional <a href="#QuickDiffProvider">quick diff provider</a>.</p>
</div>
</div>



<a name="SourceControl.rootUri"></a><span class="ts" id=1844 data-target="#details-1844" data-toggle="collapse"><span class="ident">rootUri</span><span>: </span><a class="type-ref" href="#Uri">Uri</a> &#124; <a class="type-intrinsic">undefined</a></span>
<div class="details collapse" id="details-1844">
<div class="comment"><p>The (optional) Uri of the root of this source control.</p>
</div>
</div>



<a name="SourceControl.statusBarCommands"></a><span class="ts" id=1850 data-target="#details-1850" data-toggle="collapse"><span class="ident">statusBarCommands</span><span>?</span><span>: </span><a class="type-ref" href="#Command">Command</a>[]</span>
<div class="details collapse" id="details-1850">
<div class="comment"><p>Optional status bar commands.</p>
<p>These commands will be displayed in the editor&#39;s status bar.</p>
</div>
</div>

#### Methods



<a name="SourceControl.createResourceGroup"></a><span class="ts" id=1852 data-target="#details-1852" data-toggle="collapse"><span class="ident">createResourceGroup</span><span>(</span><span class="ident">id</span><span>: </span><a class="type-intrinsic">string</a>, <span class="ident">label</span><span>: </span><a class="type-intrinsic">string</a><span>)</span><span>: </span><a class="type-ref" href="#SourceControlResourceGroup">SourceControlResourceGroup</a></span>
<div class="details collapse" id="details-1852">
<div class="comment"><p>Create a new <a href="#SourceControlResourceGroup">resource group</a>.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="id"></a><span class="ts" id=1853 data-target="#details-1853" data-toggle="collapse"><span class="ident">id</span><span>: </span><a class="type-intrinsic">string</a></span></td><td><div class="comment"></div></td></tr>
<tr><td><a name="label"></a><span class="ts" id=1854 data-target="#details-1854" data-toggle="collapse"><span class="ident">label</span><span>: </span><a class="type-intrinsic">string</a></span></td><td><div class="comment"></div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#SourceControlResourceGroup">SourceControlResourceGroup</a></span></td><td><div class="comment"></div></td></tr>
</table>
</div>
</div>



<a name="SourceControl.dispose"></a><span class="ts" id=1856 data-target="#details-1856" data-toggle="collapse"><span class="ident">dispose</span><span>(</span><span>)</span><span>: </span><a class="type-intrinsic">void</a></span>
<div class="details collapse" id="details-1856">
<div class="comment"><p>Dispose this source control.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-intrinsic">void</a></span></td><td><div class="comment"></div></td></tr>
</table>
</div>
</div>

### <a name="SourceControlInputBox"></a><span class="code-item" id=1813>SourceControlInputBox</span>



<div class="comment"><p>Represents the input box in the Source Control viewlet.</p>
</div>

#### Properties



<a name="SourceControlInputBox.placeholder"></a><span class="ts" id=1815 data-target="#details-1815" data-toggle="collapse"><span class="ident">placeholder</span><span>: </span><a class="type-intrinsic">string</a></span>
<div class="details collapse" id="details-1815">
<div class="comment"><p>A string to show as place holder in the input box to guide the user.</p>
</div>
</div>



<a name="SourceControlInputBox.value"></a><span class="ts" id=1814 data-target="#details-1814" data-toggle="collapse"><span class="ident">value</span><span>: </span><a class="type-intrinsic">string</a></span>
<div class="details collapse" id="details-1814">
<div class="comment"><p>Setter and getter for the contents of the input box.</p>
</div>
</div>

### <a name="SourceControlResourceDecorations"></a><span class="code-item" id=1823>SourceControlResourceDecorations</span>



<div class="comment"><p>The decorations for a <a href="#SourceControlResourceState">source control resource state</a>.
Can be independently specified for light and dark themes.</p>
</div>

#### Properties



<a name="SourceControlResourceDecorations.dark"></a><span class="ts" id=1828 data-target="#details-1828" data-toggle="collapse"><span class="ident">dark</span><span>?</span><span>: </span><a class="type-ref" href="#SourceControlResourceThemableDecorations">SourceControlResourceThemableDecorations</a></span>
<div class="details collapse" id="details-1828">
<div class="comment"><p>The dark theme decorations.</p>
</div>
</div>



<a name="SourceControlResourceDecorations.faded"></a><span class="ts" id=1825 data-target="#details-1825" data-toggle="collapse"><span class="ident">faded</span><span>?</span><span>: </span><a class="type-intrinsic">boolean</a></span>
<div class="details collapse" id="details-1825">
<div class="comment"><p>Whether the <a href="#SourceControlResourceState">source control resource state</a> should
be faded in the UI.</p>
</div>
</div>



<a name="SourceControlResourceDecorations.iconPath"></a><span class="ts" id=1829 data-target="#details-1829" data-toggle="collapse"><span class="ident">iconPath</span><span>?</span><span>: </span><a class="type-intrinsic">string</a> &#124; <a class="type-ref" href="#Uri">Uri</a></span>
<div class="details collapse" id="details-1829">
<div class="comment"><p>The icon path for a specific
<a href="#SourceControlResourceState">source control resource state</a>.</p>
</div>
</div>



<a name="SourceControlResourceDecorations.light"></a><span class="ts" id=1827 data-target="#details-1827" data-toggle="collapse"><span class="ident">light</span><span>?</span><span>: </span><a class="type-ref" href="#SourceControlResourceThemableDecorations">SourceControlResourceThemableDecorations</a></span>
<div class="details collapse" id="details-1827">
<div class="comment"><p>The light theme decorations.</p>
</div>
</div>



<a name="SourceControlResourceDecorations.strikeThrough"></a><span class="ts" id=1824 data-target="#details-1824" data-toggle="collapse"><span class="ident">strikeThrough</span><span>?</span><span>: </span><a class="type-intrinsic">boolean</a></span>
<div class="details collapse" id="details-1824">
<div class="comment"><p>Whether the <a href="#SourceControlResourceState">source control resource state</a> should
be striked-through in the UI.</p>
</div>
</div>



<a name="SourceControlResourceDecorations.tooltip"></a><span class="ts" id=1826 data-target="#details-1826" data-toggle="collapse"><span class="ident">tooltip</span><span>?</span><span>: </span><a class="type-intrinsic">string</a></span>
<div class="details collapse" id="details-1826">
<div class="comment"><p>The title for a specific
<a href="#SourceControlResourceState">source control resource state</a>.</p>
</div>
</div>

### <a name="SourceControlResourceGroup"></a><span class="code-item" id=1834>SourceControlResourceGroup</span>



<div class="comment"><p>A source control resource group is a collection of
<a href="#SourceControlResourceState">source control resource states</a>.</p>
</div>

#### Properties



<a name="SourceControlResourceGroup.hideWhenEmpty"></a><span class="ts" id=1837 data-target="#details-1837" data-toggle="collapse"><span class="ident">hideWhenEmpty</span><span>?</span><span>: </span><a class="type-intrinsic">boolean</a></span>
<div class="details collapse" id="details-1837">
<div class="comment"><p>Whether this source control resource group is hidden when it contains
no <a href="#SourceControlResourceState">source control resource states</a>.</p>
</div>
</div>



<a name="SourceControlResourceGroup.id"></a><span class="ts" id=1835 data-target="#details-1835" data-toggle="collapse"><span class="ident">id</span><span>: </span><a class="type-intrinsic">string</a></span>
<div class="details collapse" id="details-1835">
<div class="comment"><p>The id of this source control resource group.</p>
</div>
</div>



<a name="SourceControlResourceGroup.label"></a><span class="ts" id=1836 data-target="#details-1836" data-toggle="collapse"><span class="ident">label</span><span>: </span><a class="type-intrinsic">string</a></span>
<div class="details collapse" id="details-1836">
<div class="comment"><p>The label of this source control resource group.</p>
</div>
</div>



<a name="SourceControlResourceGroup.resourceStates"></a><span class="ts" id=1838 data-target="#details-1838" data-toggle="collapse"><span class="ident">resourceStates</span><span>: </span><a class="type-ref" href="#SourceControlResourceState">SourceControlResourceState</a>[]</span>
<div class="details collapse" id="details-1838">
<div class="comment"><p>This group&#39;s collection of
<a href="#SourceControlResourceState">source control resource states</a>.</p>
</div>
</div>

#### Methods



<a name="SourceControlResourceGroup.dispose"></a><span class="ts" id=1840 data-target="#details-1840" data-toggle="collapse"><span class="ident">dispose</span><span>(</span><span>)</span><span>: </span><a class="type-intrinsic">void</a></span>
<div class="details collapse" id="details-1840">
<div class="comment"><p>Dispose this source control resource group.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-intrinsic">void</a></span></td><td><div class="comment"></div></td></tr>
</table>
</div>
</div>

### <a name="SourceControlResourceState"></a><span class="code-item" id=1830>SourceControlResourceState</span>



<div class="comment"><p>An source control resource state represents the state of an underlying workspace
resource within a certain <a href="#SourceControlResourceGroup">source control group</a>.</p>
</div>

#### Properties



<a name="SourceControlResourceState.command"></a><span class="ts" id=1832 data-target="#details-1832" data-toggle="collapse"><span class="ident">command</span><span>?</span><span>: </span><a class="type-ref" href="#Command">Command</a></span>
<div class="details collapse" id="details-1832">
<div class="comment"><p>The <a href="#Command">command</a> which should be run when the resource
state is open in the Source Control viewlet.</p>
</div>
</div>



<a name="SourceControlResourceState.decorations"></a><span class="ts" id=1833 data-target="#details-1833" data-toggle="collapse"><span class="ident">decorations</span><span>?</span><span>: </span><a class="type-ref" href="#SourceControlResourceDecorations">SourceControlResourceDecorations</a></span>
<div class="details collapse" id="details-1833">
<div class="comment"><p>The <a href="#SourceControlResourceDecorations">decorations</a> for this source control
resource state.</p>
</div>
</div>



<a name="SourceControlResourceState.resourceUri"></a><span class="ts" id=1831 data-target="#details-1831" data-toggle="collapse"><span class="ident">resourceUri</span><span>: </span><a class="type-ref" href="#Uri">Uri</a></span>
<div class="details collapse" id="details-1831">
<div class="comment"><p>The <a href="#Uri">uri</a> of the underlying resource inside the workspace.</p>
</div>
</div>

### <a name="SourceControlResourceThemableDecorations"></a><span class="code-item" id=1821>SourceControlResourceThemableDecorations</span>



<div class="comment"><p>The theme-aware decorations for a
<a href="#SourceControlResourceState">source control resource state</a>.</p>
</div>

#### Properties



<a name="SourceControlResourceThemableDecorations.iconPath"></a><span class="ts" id=1822 data-target="#details-1822" data-toggle="collapse"><span class="ident">iconPath</span><span>?</span><span>: </span><a class="type-intrinsic">string</a> &#124; <a class="type-ref" href="#Uri">Uri</a></span>
<div class="details collapse" id="details-1822">
<div class="comment"><p>The icon path for a specific
<a href="#SourceControlResourceState">source control resource state</a>.</p>
</div>
</div>

### <a name="StatusBarAlignment"></a><span class="code-item" id=1260>StatusBarAlignment</span>



<div class="comment"><p>Represents the alignment of status bar items.</p>
</div>

#### Enumeration members



<a name="StatusBarAlignment.Left"></a><span class="ts" id=1261 data-target="#details-1261" data-toggle="collapse"><span class="ident">Left</span></span>
<div class="details collapse" id="details-1261">
<em>1</em>
</div>



<a name="StatusBarAlignment.Right"></a><span class="ts" id=1262 data-target="#details-1262" data-toggle="collapse"><span class="ident">Right</span></span>
<div class="details collapse" id="details-1262">
<em>2</em>
</div>

### <a name="StatusBarItem"></a><span class="code-item" id=1263>StatusBarItem</span>



<div class="comment"><p>A status bar item is a status bar contribution that can
show text and icons and run a command on click.</p>
</div>

#### Properties



<a name="StatusBarItem.alignment"></a><span class="ts" id=1264 data-target="#details-1264" data-toggle="collapse"><span class="ident">alignment</span><span>: </span><a class="type-ref" href="#StatusBarAlignment">StatusBarAlignment</a></span>
<div class="details collapse" id="details-1264">
<div class="comment"><p>The alignment of this item.</p>
</div>
</div>



<a name="StatusBarItem.color"></a><span class="ts" id=1268 data-target="#details-1268" data-toggle="collapse"><span class="ident">color</span><span>: </span><a class="type-intrinsic">string</a> &#124; <a class="type-ref" href="#ThemeColor">ThemeColor</a> &#124; <a class="type-intrinsic">undefined</a></span>
<div class="details collapse" id="details-1268">
<div class="comment"><p>The foreground color for this entry.</p>
</div>
</div>



<a name="StatusBarItem.command"></a><span class="ts" id=1269 data-target="#details-1269" data-toggle="collapse"><span class="ident">command</span><span>: </span><a class="type-intrinsic">string</a> &#124; <a class="type-intrinsic">undefined</a></span>
<div class="details collapse" id="details-1269">
<div class="comment"><p>The identifier of a command to run on click. The command must be
<a href="#commands.getCommands">known</a>.</p>
</div>
</div>



<a name="StatusBarItem.priority"></a><span class="ts" id=1265 data-target="#details-1265" data-toggle="collapse"><span class="ident">priority</span><span>: </span><a class="type-intrinsic">number</a></span>
<div class="details collapse" id="details-1265">
<div class="comment"><p>The priority of this item. Higher value means the item should
be shown more to the left.</p>
</div>
</div>



<a name="StatusBarItem.text"></a><span class="ts" id=1266 data-target="#details-1266" data-toggle="collapse"><span class="ident">text</span><span>: </span><a class="type-intrinsic">string</a></span>
<div class="details collapse" id="details-1266">
<div class="comment"><p>The text to show for the entry. You can embed icons in the text by leveraging the syntax:</p>
<p><code>My text $(icon-name) contains icons like $(icon-name) this one.</code></p>
<p>Where the icon-name is taken from the <a href="https://octicons.github.com">octicon</a> icon set, e.g.
<code>light-bulb</code>, <code>thumbsup</code>, <code>zap</code> etc.</p>
</div>
</div>



<a name="StatusBarItem.tooltip"></a><span class="ts" id=1267 data-target="#details-1267" data-toggle="collapse"><span class="ident">tooltip</span><span>: </span><a class="type-intrinsic">string</a> &#124; <a class="type-intrinsic">undefined</a></span>
<div class="details collapse" id="details-1267">
<div class="comment"><p>The tooltip text when you hover over this entry.</p>
</div>
</div>

#### Methods



<a name="StatusBarItem.dispose"></a><span class="ts" id=1275 data-target="#details-1275" data-toggle="collapse"><span class="ident">dispose</span><span>(</span><span>)</span><span>: </span><a class="type-intrinsic">void</a></span>
<div class="details collapse" id="details-1275">
<div class="comment"><p>Dispose and free associated resources. Call
<a href="#StatusBarItem.hide">hide</a>.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-intrinsic">void</a></span></td><td><div class="comment"></div></td></tr>
</table>
</div>
</div>



<a name="StatusBarItem.hide"></a><span class="ts" id=1273 data-target="#details-1273" data-toggle="collapse"><span class="ident">hide</span><span>(</span><span>)</span><span>: </span><a class="type-intrinsic">void</a></span>
<div class="details collapse" id="details-1273">
<div class="comment"><p>Hide the entry in the status bar.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-intrinsic">void</a></span></td><td><div class="comment"></div></td></tr>
</table>
</div>
</div>



<a name="StatusBarItem.show"></a><span class="ts" id=1271 data-target="#details-1271" data-toggle="collapse"><span class="ident">show</span><span>(</span><span>)</span><span>: </span><a class="type-intrinsic">void</a></span>
<div class="details collapse" id="details-1271">
<div class="comment"><p>Shows the entry in the status bar.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-intrinsic">void</a></span></td><td><div class="comment"></div></td></tr>
</table>
</div>
</div>

### <a name="SymbolInformation"></a><span class="code-item" id=706>SymbolInformation</span>



<div class="comment"><p>Represents information about programming constructs like variables, classes,
interfaces etc.</p>
</div>

#### Constructors



<a name="SymbolInformation.new SymbolInformation"></a><span class="ts" id=712 data-target="#details-712" data-toggle="collapse"><span class="ident">new SymbolInformation</span><span>(</span><span class="ident">name</span><span>: </span><a class="type-intrinsic">string</a>, <span class="ident">kind</span><span>: </span><a class="type-ref" href="#SymbolKind">SymbolKind</a>, <span class="ident">containerName</span><span>: </span><a class="type-intrinsic">string</a>, <span class="ident">location</span><span>: </span><a class="type-ref" href="#Location">Location</a><span>)</span><span>: </span><a class="type-ref" href="#SymbolInformation">SymbolInformation</a></span>
<div class="details collapse" id="details-712">
<div class="comment"><p>Creates a new symbol information object.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="name"></a><span class="ts" id=713 data-target="#details-713" data-toggle="collapse"><span class="ident">name</span><span>: </span><a class="type-intrinsic">string</a></span></td><td><div class="comment"><p>The name of the symbol.</p>
</div></td></tr>
<tr><td><a name="kind"></a><span class="ts" id=714 data-target="#details-714" data-toggle="collapse"><span class="ident">kind</span><span>: </span><a class="type-ref" href="#SymbolKind">SymbolKind</a></span></td><td><div class="comment"><p>The kind of the symbol.</p>
</div></td></tr>
<tr><td><a name="containerName"></a><span class="ts" id=715 data-target="#details-715" data-toggle="collapse"><span class="ident">containerName</span><span>: </span><a class="type-intrinsic">string</a></span></td><td><div class="comment"><p>The name of the symbol containing the symbol.</p>
</div></td></tr>
<tr><td><a name="location"></a><span class="ts" id=716 data-target="#details-716" data-toggle="collapse"><span class="ident">location</span><span>: </span><a class="type-ref" href="#Location">Location</a></span></td><td><div class="comment"><p>The location of the symbol.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#SymbolInformation">SymbolInformation</a></span></td><td><div class="comment"></div></td></tr>
</table>
</div>
</div>



<a name="SymbolInformation.new SymbolInformation"></a><span class="ts" id=717 data-target="#details-717" data-toggle="collapse"><span class="ident">new SymbolInformation</span><span>(</span><span class="ident">name</span><span>: </span><a class="type-intrinsic">string</a>, <span class="ident">kind</span><span>: </span><a class="type-ref" href="#SymbolKind">SymbolKind</a>, <span class="ident">range</span><span>: </span><a class="type-ref" href="#Range">Range</a>, <span class="ident">uri</span><span>?</span><span>: </span><a class="type-ref" href="#Uri">Uri</a>, <span class="ident">containerName</span><span>?</span><span>: </span><a class="type-intrinsic">string</a><span>)</span><span>: </span><a class="type-ref" href="#SymbolInformation">SymbolInformation</a></span>
<div class="details collapse" id="details-717">
<div class="comment"><p><del>Creates a new symbol information object.</del></p>
<ul>
<li><em>deprecated</em> - Please use the constructor taking a <a href="#Location">location</a> object.</li>
</ul>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="name"></a><span class="ts" id=718 data-target="#details-718" data-toggle="collapse"><span class="ident">name</span><span>: </span><a class="type-intrinsic">string</a></span></td><td><div class="comment"><p>The name of the symbol.</p>
</div></td></tr>
<tr><td><a name="kind"></a><span class="ts" id=719 data-target="#details-719" data-toggle="collapse"><span class="ident">kind</span><span>: </span><a class="type-ref" href="#SymbolKind">SymbolKind</a></span></td><td><div class="comment"><p>The kind of the symbol.</p>
</div></td></tr>
<tr><td><a name="range"></a><span class="ts" id=720 data-target="#details-720" data-toggle="collapse"><span class="ident">range</span><span>: </span><a class="type-ref" href="#Range">Range</a></span></td><td><div class="comment"><p>The range of the location of the symbol.</p>
</div></td></tr>
<tr><td><a name="uri"></a><span class="ts" id=721 data-target="#details-721" data-toggle="collapse"><span class="ident">uri</span><span>?</span><span>: </span><a class="type-ref" href="#Uri">Uri</a></span></td><td><div class="comment"><p>The resource of the location of symbol, defaults to the current document.</p>
</div></td></tr>
<tr><td><a name="containerName"></a><span class="ts" id=722 data-target="#details-722" data-toggle="collapse"><span class="ident">containerName</span><span>?</span><span>: </span><a class="type-intrinsic">string</a></span></td><td><div class="comment"><p>The name of the symbol containing the symbol.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#SymbolInformation">SymbolInformation</a></span></td><td><div class="comment"></div></td></tr>
</table>
</div>
</div>

#### Properties



<a name="SymbolInformation.containerName"></a><span class="ts" id=708 data-target="#details-708" data-toggle="collapse"><span class="ident">containerName</span><span>: </span><a class="type-intrinsic">string</a></span>
<div class="details collapse" id="details-708">
<div class="comment"><p>The name of the symbol containing this symbol.</p>
</div>
</div>



<a name="SymbolInformation.kind"></a><span class="ts" id=709 data-target="#details-709" data-toggle="collapse"><span class="ident">kind</span><span>: </span><a class="type-ref" href="#SymbolKind">SymbolKind</a></span>
<div class="details collapse" id="details-709">
<div class="comment"><p>The kind of this symbol.</p>
</div>
</div>



<a name="SymbolInformation.location"></a><span class="ts" id=710 data-target="#details-710" data-toggle="collapse"><span class="ident">location</span><span>: </span><a class="type-ref" href="#Location">Location</a></span>
<div class="details collapse" id="details-710">
<div class="comment"><p>The location of this symbol.</p>
</div>
</div>



<a name="SymbolInformation.name"></a><span class="ts" id=707 data-target="#details-707" data-toggle="collapse"><span class="ident">name</span><span>: </span><a class="type-intrinsic">string</a></span>
<div class="details collapse" id="details-707">
<div class="comment"><p>The name of this symbol.</p>
</div>
</div>

### <a name="SymbolKind"></a><span class="code-item" id=679>SymbolKind</span>



<div class="comment"><p>A symbol kind.</p>
</div>

#### Enumeration members



<a name="SymbolKind.Array"></a><span class="ts" id=697 data-target="#details-697" data-toggle="collapse"><span class="ident">Array</span></span>
<div class="details collapse" id="details-697">
<em>17</em>
</div>



<a name="SymbolKind.Boolean"></a><span class="ts" id=696 data-target="#details-696" data-toggle="collapse"><span class="ident">Boolean</span></span>
<div class="details collapse" id="details-696">
<em>16</em>
</div>



<a name="SymbolKind.Class"></a><span class="ts" id=684 data-target="#details-684" data-toggle="collapse"><span class="ident">Class</span></span>
<div class="details collapse" id="details-684">
<em>4</em>
</div>



<a name="SymbolKind.Constant"></a><span class="ts" id=693 data-target="#details-693" data-toggle="collapse"><span class="ident">Constant</span></span>
<div class="details collapse" id="details-693">
<em>13</em>
</div>



<a name="SymbolKind.Constructor"></a><span class="ts" id=688 data-target="#details-688" data-toggle="collapse"><span class="ident">Constructor</span></span>
<div class="details collapse" id="details-688">
<em>8</em>
</div>



<a name="SymbolKind.Enum"></a><span class="ts" id=689 data-target="#details-689" data-toggle="collapse"><span class="ident">Enum</span></span>
<div class="details collapse" id="details-689">
<em>9</em>
</div>



<a name="SymbolKind.EnumMember"></a><span class="ts" id=701 data-target="#details-701" data-toggle="collapse"><span class="ident">EnumMember</span></span>
<div class="details collapse" id="details-701">
<em>21</em>
</div>



<a name="SymbolKind.Event"></a><span class="ts" id=703 data-target="#details-703" data-toggle="collapse"><span class="ident">Event</span></span>
<div class="details collapse" id="details-703">
<em>23</em>
</div>



<a name="SymbolKind.Field"></a><span class="ts" id=687 data-target="#details-687" data-toggle="collapse"><span class="ident">Field</span></span>
<div class="details collapse" id="details-687">
<em>7</em>
</div>



<a name="SymbolKind.File"></a><span class="ts" id=680 data-target="#details-680" data-toggle="collapse"><span class="ident">File</span></span>
<div class="details collapse" id="details-680">
<em>0</em>
</div>



<a name="SymbolKind.Function"></a><span class="ts" id=691 data-target="#details-691" data-toggle="collapse"><span class="ident">Function</span></span>
<div class="details collapse" id="details-691">
<em>11</em>
</div>



<a name="SymbolKind.Interface"></a><span class="ts" id=690 data-target="#details-690" data-toggle="collapse"><span class="ident">Interface</span></span>
<div class="details collapse" id="details-690">
<em>10</em>
</div>



<a name="SymbolKind.Key"></a><span class="ts" id=699 data-target="#details-699" data-toggle="collapse"><span class="ident">Key</span></span>
<div class="details collapse" id="details-699">
<em>19</em>
</div>



<a name="SymbolKind.Method"></a><span class="ts" id=685 data-target="#details-685" data-toggle="collapse"><span class="ident">Method</span></span>
<div class="details collapse" id="details-685">
<em>5</em>
</div>



<a name="SymbolKind.Module"></a><span class="ts" id=681 data-target="#details-681" data-toggle="collapse"><span class="ident">Module</span></span>
<div class="details collapse" id="details-681">
<em>1</em>
</div>



<a name="SymbolKind.Namespace"></a><span class="ts" id=682 data-target="#details-682" data-toggle="collapse"><span class="ident">Namespace</span></span>
<div class="details collapse" id="details-682">
<em>2</em>
</div>



<a name="SymbolKind.Null"></a><span class="ts" id=700 data-target="#details-700" data-toggle="collapse"><span class="ident">Null</span></span>
<div class="details collapse" id="details-700">
<em>20</em>
</div>



<a name="SymbolKind.Number"></a><span class="ts" id=695 data-target="#details-695" data-toggle="collapse"><span class="ident">Number</span></span>
<div class="details collapse" id="details-695">
<em>15</em>
</div>



<a name="SymbolKind.Object"></a><span class="ts" id=698 data-target="#details-698" data-toggle="collapse"><span class="ident">Object</span></span>
<div class="details collapse" id="details-698">
<em>18</em>
</div>



<a name="SymbolKind.Operator"></a><span class="ts" id=704 data-target="#details-704" data-toggle="collapse"><span class="ident">Operator</span></span>
<div class="details collapse" id="details-704">
<em>24</em>
</div>



<a name="SymbolKind.Package"></a><span class="ts" id=683 data-target="#details-683" data-toggle="collapse"><span class="ident">Package</span></span>
<div class="details collapse" id="details-683">
<em>3</em>
</div>



<a name="SymbolKind.Property"></a><span class="ts" id=686 data-target="#details-686" data-toggle="collapse"><span class="ident">Property</span></span>
<div class="details collapse" id="details-686">
<em>6</em>
</div>



<a name="SymbolKind.String"></a><span class="ts" id=694 data-target="#details-694" data-toggle="collapse"><span class="ident">String</span></span>
<div class="details collapse" id="details-694">
<em>14</em>
</div>



<a name="SymbolKind.Struct"></a><span class="ts" id=702 data-target="#details-702" data-toggle="collapse"><span class="ident">Struct</span></span>
<div class="details collapse" id="details-702">
<em>22</em>
</div>



<a name="SymbolKind.TypeParameter"></a><span class="ts" id=705 data-target="#details-705" data-toggle="collapse"><span class="ident">TypeParameter</span></span>
<div class="details collapse" id="details-705">
<em>25</em>
</div>



<a name="SymbolKind.Variable"></a><span class="ts" id=692 data-target="#details-692" data-toggle="collapse"><span class="ident">Variable</span></span>
<div class="details collapse" id="details-692">
<em>12</em>
</div>

### <a name="Task"></a><span class="code-item" id=1417>Task</span>



<div class="comment"><p>A task to execute</p>
</div>

#### Constructors



<a name="Task.new Task"></a><span class="ts" id=1419 data-target="#details-1419" data-toggle="collapse"><span class="ident">new Task</span><span>(</span><span class="ident">taskDefinition</span><span>: </span><a class="type-ref" href="#TaskDefinition">TaskDefinition</a>, <span class="ident">scope</span><span>: </span><a class="type-ref" href="#WorkspaceFolder">WorkspaceFolder</a> &#124; <a class="type-ref" href="#Global">Global</a> &#124; <a class="type-ref" href="#Workspace">Workspace</a>, <span class="ident">name</span><span>: </span><a class="type-intrinsic">string</a>, <span class="ident">source</span><span>: </span><a class="type-intrinsic">string</a>, <span class="ident">execution</span><span>?</span><span>: </span><a class="type-ref" href="#ProcessExecution">ProcessExecution</a> &#124; <a class="type-ref" href="#ShellExecution">ShellExecution</a>, <span class="ident">problemMatchers</span><span>?</span><span>: </span><a class="type-intrinsic">string</a> &#124; <a class="type-intrinsic">string</a>[]<span>)</span><span>: </span><a class="type-ref" href="#Task">Task</a></span>
<div class="details collapse" id="details-1419">
<div class="comment"><p>Creates a new task.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="taskDefinition"></a><span class="ts" id=1420 data-target="#details-1420" data-toggle="collapse"><span class="ident">taskDefinition</span><span>: </span><a class="type-ref" href="#TaskDefinition">TaskDefinition</a></span></td><td><div class="comment"></div></td></tr>
<tr><td><a name="scope"></a><span class="ts" id=1421 data-target="#details-1421" data-toggle="collapse"><span class="ident">scope</span><span>: </span><a class="type-ref" href="#WorkspaceFolder">WorkspaceFolder</a> &#124; <a class="type-ref" href="#Global">Global</a> &#124; <a class="type-ref" href="#Workspace">Workspace</a></span></td><td><div class="comment"><p>Specifies the task&#39;s scope. It is either a global or a workspace task or a task for a specific workspace folder.</p>
</div></td></tr>
<tr><td><a name="name"></a><span class="ts" id=1422 data-target="#details-1422" data-toggle="collapse"><span class="ident">name</span><span>: </span><a class="type-intrinsic">string</a></span></td><td><div class="comment"><p>The task&#39;s name. Is presented in the user interface.</p>
</div></td></tr>
<tr><td><a name="source"></a><span class="ts" id=1423 data-target="#details-1423" data-toggle="collapse"><span class="ident">source</span><span>: </span><a class="type-intrinsic">string</a></span></td><td><div class="comment"><p>The task&#39;s source (e.g. &#39;gulp&#39;, &#39;npm&#39;, ...). Is presented in the user interface.</p>
</div></td></tr>
<tr><td><a name="execution"></a><span class="ts" id=1424 data-target="#details-1424" data-toggle="collapse"><span class="ident">execution</span><span>?</span><span>: </span><a class="type-ref" href="#ProcessExecution">ProcessExecution</a> &#124; <a class="type-ref" href="#ShellExecution">ShellExecution</a></span></td><td><div class="comment"><p>The process or shell execution.</p>
</div></td></tr>
<tr><td><a name="problemMatchers"></a><span class="ts" id=1425 data-target="#details-1425" data-toggle="collapse"><span class="ident">problemMatchers</span><span>?</span><span>: </span><a class="type-intrinsic">string</a> &#124; <a class="type-intrinsic">string</a>[]</span></td><td><div class="comment"><p>the names of problem matchers to use, like &#39;$tsc&#39;
 or &#39;$eslint&#39;. Problem matchers can be contributed by an extension using
 the <code>problemMatchers</code> extension point.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#Task">Task</a></span></td><td><div class="comment"></div></td></tr>
</table>
</div>
</div>



<a name="Task.new Task"></a><span class="ts" id=1426 data-target="#details-1426" data-toggle="collapse"><span class="ident">new Task</span><span>(</span><span class="ident">taskDefinition</span><span>: </span><a class="type-ref" href="#TaskDefinition">TaskDefinition</a>, <span class="ident">name</span><span>: </span><a class="type-intrinsic">string</a>, <span class="ident">source</span><span>: </span><a class="type-intrinsic">string</a>, <span class="ident">execution</span><span>?</span><span>: </span><a class="type-ref" href="#ProcessExecution">ProcessExecution</a> &#124; <a class="type-ref" href="#ShellExecution">ShellExecution</a>, <span class="ident">problemMatchers</span><span>?</span><span>: </span><a class="type-intrinsic">string</a> &#124; <a class="type-intrinsic">string</a>[]<span>)</span><span>: </span><a class="type-ref" href="#Task">Task</a></span>
<div class="details collapse" id="details-1426">
<div class="comment"><p><del>Creates a new task.</del></p>
<ul>
<li><em>deprecated</em> - Use the new constructors that allow specifying a scope for the task.</li>
</ul>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="taskDefinition"></a><span class="ts" id=1427 data-target="#details-1427" data-toggle="collapse"><span class="ident">taskDefinition</span><span>: </span><a class="type-ref" href="#TaskDefinition">TaskDefinition</a></span></td><td><div class="comment"></div></td></tr>
<tr><td><a name="name"></a><span class="ts" id=1428 data-target="#details-1428" data-toggle="collapse"><span class="ident">name</span><span>: </span><a class="type-intrinsic">string</a></span></td><td><div class="comment"><p>The task&#39;s name. Is presented in the user interface.</p>
</div></td></tr>
<tr><td><a name="source"></a><span class="ts" id=1429 data-target="#details-1429" data-toggle="collapse"><span class="ident">source</span><span>: </span><a class="type-intrinsic">string</a></span></td><td><div class="comment"><p>The task&#39;s source (e.g. &#39;gulp&#39;, &#39;npm&#39;, ...). Is presented in the user interface.</p>
</div></td></tr>
<tr><td><a name="execution"></a><span class="ts" id=1430 data-target="#details-1430" data-toggle="collapse"><span class="ident">execution</span><span>?</span><span>: </span><a class="type-ref" href="#ProcessExecution">ProcessExecution</a> &#124; <a class="type-ref" href="#ShellExecution">ShellExecution</a></span></td><td><div class="comment"><p>The process or shell execution.</p>
</div></td></tr>
<tr><td><a name="problemMatchers"></a><span class="ts" id=1431 data-target="#details-1431" data-toggle="collapse"><span class="ident">problemMatchers</span><span>?</span><span>: </span><a class="type-intrinsic">string</a> &#124; <a class="type-intrinsic">string</a>[]</span></td><td><div class="comment"><p>the names of problem matchers to use, like &#39;$tsc&#39;
 or &#39;$eslint&#39;. Problem matchers can be contributed by an extension using
 the <code>problemMatchers</code> extension point.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#Task">Task</a></span></td><td><div class="comment"></div></td></tr>
</table>
</div>
</div>

#### Properties



<a name="Task.definition"></a><span class="ts" id=1432 data-target="#details-1432" data-toggle="collapse"><span class="ident">definition</span><span>: </span><a class="type-ref" href="#TaskDefinition">TaskDefinition</a></span>
<div class="details collapse" id="details-1432">
<div class="comment"><p>The task&#39;s definition.</p>
</div>
</div>



<a name="Task.execution"></a><span class="ts" id=1435 data-target="#details-1435" data-toggle="collapse"><span class="ident">execution</span><span>?</span><span>: </span><a class="type-ref" href="#ProcessExecution">ProcessExecution</a> &#124; <a class="type-ref" href="#ShellExecution">ShellExecution</a></span>
<div class="details collapse" id="details-1435">
<div class="comment"><p>The task&#39;s execution engine</p>
</div>
</div>



<a name="Task.group"></a><span class="ts" id=1438 data-target="#details-1438" data-toggle="collapse"><span class="ident">group</span><span>?</span><span>: </span><a class="type-ref" href="#TaskGroup">TaskGroup</a></span>
<div class="details collapse" id="details-1438">
<div class="comment"><p>The task group this tasks belongs to. See TaskGroup
for a predefined set of available groups.
Defaults to undefined meaning that the task doesn&#39;t
belong to any special group.</p>
</div>
</div>



<a name="Task.isBackground"></a><span class="ts" id=1436 data-target="#details-1436" data-toggle="collapse"><span class="ident">isBackground</span><span>: </span><a class="type-intrinsic">boolean</a></span>
<div class="details collapse" id="details-1436">
<div class="comment"><p>Whether the task is a background task or not.</p>
</div>
</div>



<a name="Task.name"></a><span class="ts" id=1434 data-target="#details-1434" data-toggle="collapse"><span class="ident">name</span><span>: </span><a class="type-intrinsic">string</a></span>
<div class="details collapse" id="details-1434">
<div class="comment"><p>The task&#39;s name</p>
</div>
</div>



<a name="Task.presentationOptions"></a><span class="ts" id=1439 data-target="#details-1439" data-toggle="collapse"><span class="ident">presentationOptions</span><span>: </span><a class="type-ref" href="#TaskPresentationOptions">TaskPresentationOptions</a></span>
<div class="details collapse" id="details-1439">
<div class="comment"><p>The presentation options. Defaults to an empty literal.</p>
</div>
</div>



<a name="Task.problemMatchers"></a><span class="ts" id=1440 data-target="#details-1440" data-toggle="collapse"><span class="ident">problemMatchers</span><span>: </span><a class="type-intrinsic">string</a>[]</span>
<div class="details collapse" id="details-1440">
<div class="comment"><p>The problem matchers attached to the task. Defaults to an empty
array.</p>
</div>
</div>



<a name="Task.runOptions"></a><span class="ts" id=1441 data-target="#details-1441" data-toggle="collapse"><span class="ident">runOptions</span><span>: </span><a class="type-ref" href="#RunOptions">RunOptions</a></span>
<div class="details collapse" id="details-1441">
<div class="comment"><p>Run options for the task</p>
</div>
</div>



<a name="Task.scope"></a><span class="ts" id=1433 data-target="#details-1433" data-toggle="collapse"><span class="ident">scope</span><span>?</span><span>: </span><a class="type-ref" href="#Global">Global</a> &#124; <a class="type-ref" href="#Workspace">Workspace</a> &#124; <a class="type-ref" href="#WorkspaceFolder">WorkspaceFolder</a></span>
<div class="details collapse" id="details-1433">
<div class="comment"><p>The task&#39;s scope.</p>
</div>
</div>



<a name="Task.source"></a><span class="ts" id=1437 data-target="#details-1437" data-toggle="collapse"><span class="ident">source</span><span>: </span><a class="type-intrinsic">string</a></span>
<div class="details collapse" id="details-1437">
<div class="comment"><p>A human-readable string describing the source of this
shell task, e.g. &#39;gulp&#39; or &#39;npm&#39;.</p>
</div>
</div>

### <a name="TaskDefinition"></a><span class="code-item" id=1354>TaskDefinition</span>



<div class="comment"><p>A structure that defines a task kind in the system.
The value must be JSON-stringifyable.</p>
</div>

#### Properties



<a name="TaskDefinition.type"></a><span class="ts" id=1355 data-target="#details-1355" data-toggle="collapse"><span class="ident">type</span><span>: </span><a class="type-intrinsic">string</a></span>
<div class="details collapse" id="details-1355">
<div class="comment"><p>The task definition describing the task provided by an extension.
Usually a task provider defines more properties to identify
a task. They need to be defined in the package.json of the
extension under the &#39;taskDefinitions&#39; extension point. The npm
task definition for example looks like this</p>

<pre><code class="lang-typescript">interface NpmTaskDefinition extends TaskDefinition {
    script: string;
}
</code></pre>
<p>Note that type identifier starting with a &#39;$&#39; are reserved for internal
usages and shouldn&#39;t be used by extensions.</p>
</div>
</div>

### <a name="TaskEndEvent"></a><span class="code-item" id=1456>TaskEndEvent</span>



<div class="comment"><p>An event signaling the end of an executed task.</p>
<p>This interface is not intended to be implemented.</p>
</div>

#### Properties



<a name="TaskEndEvent.execution"></a><span class="ts" id=1457 data-target="#details-1457" data-toggle="collapse"><span class="ident">execution</span><span>: </span><a class="type-ref" href="#TaskExecution">TaskExecution</a></span>
<div class="details collapse" id="details-1457">
<div class="comment"><p>The task item representing the task that finished.</p>
</div>
</div>

### <a name="TaskExecution"></a><span class="code-item" id=1450>TaskExecution</span>



<div class="comment"><p>An object representing an executed Task. It can be used
to terminate a task.</p>
<p>This interface is not intended to be implemented.</p>
</div>

#### Properties



<a name="TaskExecution.task"></a><span class="ts" id=1451 data-target="#details-1451" data-toggle="collapse"><span class="ident">task</span><span>: </span><a class="type-ref" href="#Task">Task</a></span>
<div class="details collapse" id="details-1451">
<div class="comment"><p>The task that got started.</p>
</div>
</div>

#### Methods



<a name="TaskExecution.terminate"></a><span class="ts" id=1453 data-target="#details-1453" data-toggle="collapse"><span class="ident">terminate</span><span>(</span><span>)</span><span>: </span><a class="type-intrinsic">void</a></span>
<div class="details collapse" id="details-1453">
<div class="comment"><p>Terminates the task execution.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-intrinsic">void</a></span></td><td><div class="comment"></div></td></tr>
</table>
</div>
</div>

### <a name="TaskFilter"></a><span class="code-item" id=1464>TaskFilter</span>



<div class="comment"></div>

#### Properties



<a name="TaskFilter.type"></a><span class="ts" id=1466 data-target="#details-1466" data-toggle="collapse"><span class="ident">type</span><span>?</span><span>: </span><a class="type-intrinsic">string</a></span>
<div class="details collapse" id="details-1466">
<div class="comment"><p>The task type to return;</p>
</div>
</div>



<a name="TaskFilter.version"></a><span class="ts" id=1465 data-target="#details-1465" data-toggle="collapse"><span class="ident">version</span><span>?</span><span>: </span><a class="type-intrinsic">string</a></span>
<div class="details collapse" id="details-1465">
<div class="comment"><p>The task version as used in the tasks.json file.
The string support the package.json semver notation.</p>
</div>
</div>

### <a name="TaskGroup"></a><span class="code-item" id=1345>TaskGroup</span>



<div class="comment"><p>A grouping for tasks. The editor by default supports the
&#39;Clean&#39;, &#39;Build&#39;, &#39;RebuildAll&#39; and &#39;Test&#39; group.</p>
</div>

#### Static



<a name="TaskGroup.Build"></a><span class="ts" id=1347 data-target="#details-1347" data-toggle="collapse"><span class="ident">Build</span><span>: </span><a class="type-ref" href="#TaskGroup">TaskGroup</a></span>
<div class="details collapse" id="details-1347">
<div class="comment"><p>The build task group;</p>
</div>
</div>



<a name="TaskGroup.Clean"></a><span class="ts" id=1346 data-target="#details-1346" data-toggle="collapse"><span class="ident">Clean</span><span>: </span><a class="type-ref" href="#TaskGroup">TaskGroup</a></span>
<div class="details collapse" id="details-1346">
<div class="comment"><p>The clean task group;</p>
</div>
</div>



<a name="TaskGroup.Rebuild"></a><span class="ts" id=1348 data-target="#details-1348" data-toggle="collapse"><span class="ident">Rebuild</span><span>: </span><a class="type-ref" href="#TaskGroup">TaskGroup</a></span>
<div class="details collapse" id="details-1348">
<div class="comment"><p>The rebuild all task group;</p>
</div>
</div>



<a name="TaskGroup.Test"></a><span class="ts" id=1349 data-target="#details-1349" data-toggle="collapse"><span class="ident">Test</span><span>: </span><a class="type-ref" href="#TaskGroup">TaskGroup</a></span>
<div class="details collapse" id="details-1349">
<div class="comment"><p>The test all task group;</p>
</div>
</div>

#### Constructors



<a name="TaskGroup.new TaskGroup"></a><span class="ts" id=1351 data-target="#details-1351" data-toggle="collapse"><span class="ident">new TaskGroup</span><span>(</span><span class="ident">id</span><span>: </span><a class="type-intrinsic">string</a>, <span class="ident">label</span><span>: </span><a class="type-intrinsic">string</a><span>)</span><span>: </span><a class="type-ref" href="#TaskGroup">TaskGroup</a></span>
<div class="details collapse" id="details-1351">
<div class="comment"></div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="id"></a><span class="ts" id=1352 data-target="#details-1352" data-toggle="collapse"><span class="ident">id</span><span>: </span><a class="type-intrinsic">string</a></span></td><td><div class="comment"></div></td></tr>
<tr><td><a name="label"></a><span class="ts" id=1353 data-target="#details-1353" data-toggle="collapse"><span class="ident">label</span><span>: </span><a class="type-intrinsic">string</a></span></td><td><div class="comment"></div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#TaskGroup">TaskGroup</a></span></td><td><div class="comment"></div></td></tr>
</table>
</div>
</div>

### <a name="TaskPanelKind"></a><span class="code-item" id=1334>TaskPanelKind</span>



<div class="comment"><p>Controls how the task channel is used between tasks</p>
</div>

#### Enumeration members



<a name="TaskPanelKind.Dedicated"></a><span class="ts" id=1336 data-target="#details-1336" data-toggle="collapse"><span class="ident">Dedicated</span></span>
<div class="details collapse" id="details-1336">
<em>2</em>
</div>



<a name="TaskPanelKind.New"></a><span class="ts" id=1337 data-target="#details-1337" data-toggle="collapse"><span class="ident">New</span></span>
<div class="details collapse" id="details-1337">
<em>3</em>
</div>



<a name="TaskPanelKind.Shared"></a><span class="ts" id=1335 data-target="#details-1335" data-toggle="collapse"><span class="ident">Shared</span></span>
<div class="details collapse" id="details-1335">
<em>1</em>
</div>

### <a name="TaskPresentationOptions"></a><span class="code-item" id=1338>TaskPresentationOptions</span>



<div class="comment"><p>Controls how the task is presented in the UI.</p>
</div>

#### Properties



<a name="TaskPresentationOptions.clear"></a><span class="ts" id=1344 data-target="#details-1344" data-toggle="collapse"><span class="ident">clear</span><span>?</span><span>: </span><a class="type-intrinsic">boolean</a></span>
<div class="details collapse" id="details-1344">
<div class="comment"><p>Controls whether the terminal is cleared before executing the task.</p>
</div>
</div>



<a name="TaskPresentationOptions.echo"></a><span class="ts" id=1340 data-target="#details-1340" data-toggle="collapse"><span class="ident">echo</span><span>?</span><span>: </span><a class="type-intrinsic">boolean</a></span>
<div class="details collapse" id="details-1340">
<div class="comment"><p>Controls whether the command associated with the task is echoed
in the user interface.</p>
</div>
</div>



<a name="TaskPresentationOptions.focus"></a><span class="ts" id=1341 data-target="#details-1341" data-toggle="collapse"><span class="ident">focus</span><span>?</span><span>: </span><a class="type-intrinsic">boolean</a></span>
<div class="details collapse" id="details-1341">
<div class="comment"><p>Controls whether the panel showing the task output is taking focus.</p>
</div>
</div>



<a name="TaskPresentationOptions.panel"></a><span class="ts" id=1342 data-target="#details-1342" data-toggle="collapse"><span class="ident">panel</span><span>?</span><span>: </span><a class="type-ref" href="#TaskPanelKind">TaskPanelKind</a></span>
<div class="details collapse" id="details-1342">
<div class="comment"><p>Controls if the task panel is used for this task only (dedicated),
shared between tasks (shared) or if a new panel is created on
every task execution (new). Defaults to <code>TaskInstanceKind.Shared</code></p>
</div>
</div>



<a name="TaskPresentationOptions.reveal"></a><span class="ts" id=1339 data-target="#details-1339" data-toggle="collapse"><span class="ident">reveal</span><span>?</span><span>: </span><a class="type-ref" href="#TaskRevealKind">TaskRevealKind</a></span>
<div class="details collapse" id="details-1339">
<div class="comment"><p>Controls whether the task output is reveal in the user interface.
Defaults to <code>RevealKind.Always</code>.</p>
</div>
</div>



<a name="TaskPresentationOptions.showReuseMessage"></a><span class="ts" id=1343 data-target="#details-1343" data-toggle="collapse"><span class="ident">showReuseMessage</span><span>?</span><span>: </span><a class="type-intrinsic">boolean</a></span>
<div class="details collapse" id="details-1343">
<div class="comment"><p>Controls whether to show the &quot;Terminal will be reused by tasks, press any key to close it&quot; message.</p>
</div>
</div>

### <a name="TaskProcessEndEvent"></a><span class="code-item" id=1461>TaskProcessEndEvent</span>



<div class="comment"><p>An event signaling the end of a process execution
triggered through a task</p>
</div>

#### Properties



<a name="TaskProcessEndEvent.execution"></a><span class="ts" id=1462 data-target="#details-1462" data-toggle="collapse"><span class="ident">execution</span><span>: </span><a class="type-ref" href="#TaskExecution">TaskExecution</a></span>
<div class="details collapse" id="details-1462">
<div class="comment"><p>The task execution for which the process got started.</p>
</div>
</div>



<a name="TaskProcessEndEvent.exitCode"></a><span class="ts" id=1463 data-target="#details-1463" data-toggle="collapse"><span class="ident">exitCode</span><span>: </span><a class="type-intrinsic">number</a></span>
<div class="details collapse" id="details-1463">
<div class="comment"><p>The process&#39;s exit code.</p>
</div>
</div>

### <a name="TaskProcessStartEvent"></a><span class="code-item" id=1458>TaskProcessStartEvent</span>



<div class="comment"><p>An event signaling the start of a process execution
triggered through a task</p>
</div>

#### Properties



<a name="TaskProcessStartEvent.execution"></a><span class="ts" id=1459 data-target="#details-1459" data-toggle="collapse"><span class="ident">execution</span><span>: </span><a class="type-ref" href="#TaskExecution">TaskExecution</a></span>
<div class="details collapse" id="details-1459">
<div class="comment"><p>The task execution for which the process got started.</p>
</div>
</div>



<a name="TaskProcessStartEvent.processId"></a><span class="ts" id=1460 data-target="#details-1460" data-toggle="collapse"><span class="ident">processId</span><span>: </span><a class="type-intrinsic">number</a></span>
<div class="details collapse" id="details-1460">
<div class="comment"><p>The underlying process id.</p>
</div>
</div>

### <a name="TaskProvider"></a><span class="code-item" id=1442>TaskProvider</span>



<div class="comment"><p>A task provider allows to add tasks to the task service.
A task provider is registered via #tasks.registerTaskProvider.</p>
</div>

#### Methods



<a name="TaskProvider.provideTasks"></a><span class="ts" id=1444 data-target="#details-1444" data-toggle="collapse"><span class="ident">provideTasks</span><span>(</span><span class="ident">token</span><span>?</span><span>: </span><a class="type-ref" href="#CancellationToken">CancellationToken</a><span>)</span><span>: </span><a class="type-ref" href="#ProviderResult">ProviderResult</a>&lt;<a class="type-ref" href="#Task">Task</a>[]&gt;</span>
<div class="details collapse" id="details-1444">
<div class="comment"><p>Provides tasks.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="token"></a><span class="ts" id=1445 data-target="#details-1445" data-toggle="collapse"><span class="ident">token</span><span>?</span><span>: </span><a class="type-ref" href="#CancellationToken">CancellationToken</a></span></td><td><div class="comment"><p>A cancellation token.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#ProviderResult">ProviderResult</a>&lt;<a class="type-ref" href="#Task">Task</a>[]&gt;</span></td><td><div class="comment"><p>an array of tasks</p>
</div></td></tr>
</table>
</div>
</div>



<a name="TaskProvider.resolveTask"></a><span class="ts" id=1447 data-target="#details-1447" data-toggle="collapse"><span class="ident">resolveTask</span><span>(</span><span class="ident">task</span><span>: </span><a class="type-ref" href="#Task">Task</a>, <span class="ident">token</span><span>?</span><span>: </span><a class="type-ref" href="#CancellationToken">CancellationToken</a><span>)</span><span>: </span><a class="type-ref" href="#ProviderResult">ProviderResult</a>&lt;<a class="type-ref" href="#Task">Task</a>&gt;</span>
<div class="details collapse" id="details-1447">
<div class="comment"><p>Resolves a task that has no <a href="#Task.execution"><code>execution</code></a> set. Tasks are
often created from information found in the <code>tasks.json</code>-file. Such tasks miss
the information on how to execute them and a task provider must fill in
the missing information in the <code>resolveTask</code>-method. This method will not be
called for tasks returned from the above <code>provideTasks</code> method since those
tasks are always fully resolved. A valid default implementation for the
<code>resolveTask</code> method is to return <code>undefined</code>.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="task"></a><span class="ts" id=1448 data-target="#details-1448" data-toggle="collapse"><span class="ident">task</span><span>: </span><a class="type-ref" href="#Task">Task</a></span></td><td><div class="comment"><p>The task to resolve.</p>
</div></td></tr>
<tr><td><a name="token"></a><span class="ts" id=1449 data-target="#details-1449" data-toggle="collapse"><span class="ident">token</span><span>?</span><span>: </span><a class="type-ref" href="#CancellationToken">CancellationToken</a></span></td><td><div class="comment"><p>A cancellation token.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#ProviderResult">ProviderResult</a>&lt;<a class="type-ref" href="#Task">Task</a>&gt;</span></td><td><div class="comment"><p>The resolved task</p>
</div></td></tr>
</table>
</div>
</div>

### <a name="TaskRevealKind"></a><span class="code-item" id=1330>TaskRevealKind</span>



<div class="comment"><p>Controls the behaviour of the terminal&#39;s visibility.</p>
</div>

#### Enumeration members



<a name="TaskRevealKind.Always"></a><span class="ts" id=1331 data-target="#details-1331" data-toggle="collapse"><span class="ident">Always</span></span>
<div class="details collapse" id="details-1331">
<em>1</em>
</div>



<a name="TaskRevealKind.Never"></a><span class="ts" id=1333 data-target="#details-1333" data-toggle="collapse"><span class="ident">Never</span></span>
<div class="details collapse" id="details-1333">
<em>3</em>
</div>



<a name="TaskRevealKind.Silent"></a><span class="ts" id=1332 data-target="#details-1332" data-toggle="collapse"><span class="ident">Silent</span></span>
<div class="details collapse" id="details-1332">
<em>2</em>
</div>

### <a name="TaskScope"></a><span class="code-item" id=1412>TaskScope</span>



<div class="comment"><p>The scope of a task.</p>
</div>

#### Enumeration members



<a name="TaskScope.Global"></a><span class="ts" id=1413 data-target="#details-1413" data-toggle="collapse"><span class="ident">Global</span></span>
<div class="details collapse" id="details-1413">
<em>1</em>
</div>



<a name="TaskScope.Workspace"></a><span class="ts" id=1414 data-target="#details-1414" data-toggle="collapse"><span class="ident">Workspace</span></span>
<div class="details collapse" id="details-1414">
<em>2</em>
</div>

### <a name="TaskStartEvent"></a><span class="code-item" id=1454>TaskStartEvent</span>



<div class="comment"><p>An event signaling the start of a task execution.</p>
<p>This interface is not intended to be implemented.</p>
</div>

#### Properties



<a name="TaskStartEvent.execution"></a><span class="ts" id=1455 data-target="#details-1455" data-toggle="collapse"><span class="ident">execution</span><span>: </span><a class="type-ref" href="#TaskExecution">TaskExecution</a></span>
<div class="details collapse" id="details-1455">
<div class="comment"><p>The task item representing the task that got started.</p>
</div>
</div>

### <a name="Terminal"></a><span class="code-item" id=1281>Terminal</span>



<div class="comment"><p>An individual terminal instance within the integrated terminal.</p>
</div>

#### Properties



<a name="Terminal.name"></a><span class="ts" id=1282 data-target="#details-1282" data-toggle="collapse"><span class="ident">name</span><span>: </span><a class="type-intrinsic">string</a></span>
<div class="details collapse" id="details-1282">
<div class="comment"><p>The name of the terminal.</p>
</div>
</div>



<a name="Terminal.processId"></a><span class="ts" id=1283 data-target="#details-1283" data-toggle="collapse"><span class="ident">processId</span><span>: </span><a class="type-ref" href="#Thenable">Thenable</a>&lt;<a class="type-intrinsic">number</a>&gt;</span>
<div class="details collapse" id="details-1283">
<div class="comment"><p>The process ID of the shell process.</p>
</div>
</div>

#### Methods



<a name="Terminal.dispose"></a><span class="ts" id=1294 data-target="#details-1294" data-toggle="collapse"><span class="ident">dispose</span><span>(</span><span>)</span><span>: </span><a class="type-intrinsic">void</a></span>
<div class="details collapse" id="details-1294">
<div class="comment"><p>Dispose and free associated resources.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-intrinsic">void</a></span></td><td><div class="comment"></div></td></tr>
</table>
</div>
</div>



<a name="Terminal.hide"></a><span class="ts" id=1292 data-target="#details-1292" data-toggle="collapse"><span class="ident">hide</span><span>(</span><span>)</span><span>: </span><a class="type-intrinsic">void</a></span>
<div class="details collapse" id="details-1292">
<div class="comment"><p>Hide the terminal panel if this terminal is currently showing.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-intrinsic">void</a></span></td><td><div class="comment"></div></td></tr>
</table>
</div>
</div>



<a name="Terminal.sendText"></a><span class="ts" id=1285 data-target="#details-1285" data-toggle="collapse"><span class="ident">sendText</span><span>(</span><span class="ident">text</span><span>: </span><a class="type-intrinsic">string</a>, <span class="ident">addNewLine</span><span>?</span><span>: </span><a class="type-intrinsic">boolean</a><span>)</span><span>: </span><a class="type-intrinsic">void</a></span>
<div class="details collapse" id="details-1285">
<div class="comment"><p>Send text to the terminal. The text is written to the stdin of the underlying pty process
(shell) of the terminal.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="text"></a><span class="ts" id=1286 data-target="#details-1286" data-toggle="collapse"><span class="ident">text</span><span>: </span><a class="type-intrinsic">string</a></span></td><td><div class="comment"><p>The text to send.</p>
</div></td></tr>
<tr><td><a name="addNewLine"></a><span class="ts" id=1287 data-target="#details-1287" data-toggle="collapse"><span class="ident">addNewLine</span><span>?</span><span>: </span><a class="type-intrinsic">boolean</a></span></td><td><div class="comment"><p>Whether to add a new line to the text being sent, this is normally
required to run a command in the terminal. The character(s) added are \n or \r\n
depending on the platform. This defaults to <code>true</code>.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-intrinsic">void</a></span></td><td><div class="comment"></div></td></tr>
</table>
</div>
</div>



<a name="Terminal.show"></a><span class="ts" id=1289 data-target="#details-1289" data-toggle="collapse"><span class="ident">show</span><span>(</span><span class="ident">preserveFocus</span><span>?</span><span>: </span><a class="type-intrinsic">boolean</a><span>)</span><span>: </span><a class="type-intrinsic">void</a></span>
<div class="details collapse" id="details-1289">
<div class="comment"><p>Show the terminal panel and reveal this terminal in the UI.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="preserveFocus"></a><span class="ts" id=1290 data-target="#details-1290" data-toggle="collapse"><span class="ident">preserveFocus</span><span>?</span><span>: </span><a class="type-intrinsic">boolean</a></span></td><td><div class="comment"><p>When <code>true</code> the terminal will not take focus.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-intrinsic">void</a></span></td><td><div class="comment"></div></td></tr>
</table>
</div>
</div>

### <a name="TerminalOptions"></a><span class="code-item" id=1688>TerminalOptions</span>



<div class="comment"><p>Value-object describing what options a terminal should use.</p>
</div>

#### Properties



<a name="TerminalOptions.cwd"></a><span class="ts" id=1692 data-target="#details-1692" data-toggle="collapse"><span class="ident">cwd</span><span>?</span><span>: </span><a class="type-intrinsic">string</a></span>
<div class="details collapse" id="details-1692">
<div class="comment"><p>A path for the current working directory to be used for the terminal.</p>
</div>
</div>



<a name="TerminalOptions.env"></a><span class="ts" id=1693 data-target="#details-1693" data-toggle="collapse"><span class="ident">env</span><span>?</span><span>: </span></span>
<div class="details collapse" id="details-1693">
<div class="comment"><p>Object with environment variables that will be added to the VS Code process.</p>
</div>
</div>



<a name="TerminalOptions.name"></a><span class="ts" id=1689 data-target="#details-1689" data-toggle="collapse"><span class="ident">name</span><span>?</span><span>: </span><a class="type-intrinsic">string</a></span>
<div class="details collapse" id="details-1689">
<div class="comment"><p>A human-readable string which will be used to represent the terminal in the UI.</p>
</div>
</div>



<a name="TerminalOptions.shellArgs"></a><span class="ts" id=1691 data-target="#details-1691" data-toggle="collapse"><span class="ident">shellArgs</span><span>?</span><span>: </span><a class="type-intrinsic">string</a>[]</span>
<div class="details collapse" id="details-1691">
<div class="comment"><p>Args for the custom shell executable, this does not work on Windows (see #8429)</p>
</div>
</div>



<a name="TerminalOptions.shellPath"></a><span class="ts" id=1690 data-target="#details-1690" data-toggle="collapse"><span class="ident">shellPath</span><span>?</span><span>: </span><a class="type-intrinsic">string</a></span>
<div class="details collapse" id="details-1690">
<div class="comment"><p>A path to a custom shell executable to be used in the terminal.</p>
</div>
</div>

### <a name="TextDocument"></a><span class="code-item" id=38>TextDocument</span>



<div class="comment"><p>Represents a text document, such as a source file. Text documents have
<a href="#TextLine">lines</a> and knowledge about an underlying resource like a file.</p>
</div>

#### Properties



<a name="TextDocument.eol"></a><span class="ts" id=48 data-target="#details-48" data-toggle="collapse"><span class="ident">eol</span><span>: </span><a class="type-ref" href="#EndOfLine">EndOfLine</a></span>
<div class="details collapse" id="details-48">
<div class="comment"><p>The <a href="#EndOfLine">end of line</a> sequence that is predominately
used in this document.</p>
</div>
</div>



<a name="TextDocument.fileName"></a><span class="ts" id=40 data-target="#details-40" data-toggle="collapse"><span class="ident">fileName</span><span>: </span><a class="type-intrinsic">string</a></span>
<div class="details collapse" id="details-40">
<div class="comment"><p>The file system path of the associated resource. Shorthand
notation for <a href="#TextDocument.uri">TextDocument.uri.fsPath</a>. Independent of the uri scheme.</p>
</div>
</div>



<a name="TextDocument.isClosed"></a><span class="ts" id=45 data-target="#details-45" data-toggle="collapse"><span class="ident">isClosed</span><span>: </span><a class="type-intrinsic">boolean</a></span>
<div class="details collapse" id="details-45">
<div class="comment"><p><code>true</code> if the document have been closed. A closed document isn&#39;t synchronized anymore
and won&#39;t be re-used when the same resource is opened again.</p>
</div>
</div>



<a name="TextDocument.isDirty"></a><span class="ts" id=44 data-target="#details-44" data-toggle="collapse"><span class="ident">isDirty</span><span>: </span><a class="type-intrinsic">boolean</a></span>
<div class="details collapse" id="details-44">
<div class="comment"><p><code>true</code> if there are unpersisted changes.</p>
</div>
</div>



<a name="TextDocument.isUntitled"></a><span class="ts" id=41 data-target="#details-41" data-toggle="collapse"><span class="ident">isUntitled</span><span>: </span><a class="type-intrinsic">boolean</a></span>
<div class="details collapse" id="details-41">
<div class="comment"><p>Is this document representing an untitled file which has never been saved yet. <em>Note</em> that
this does not mean the document will be saved to disk, use <a href="#Uri.scheme"><code>uri.scheme</code></a>
to figure out where a document will be <a href="#FileSystemProvider">saved</a>, e.g. <code>file</code>, <code>ftp</code> etc.</p>
</div>
</div>



<a name="TextDocument.languageId"></a><span class="ts" id=42 data-target="#details-42" data-toggle="collapse"><span class="ident">languageId</span><span>: </span><a class="type-intrinsic">string</a></span>
<div class="details collapse" id="details-42">
<div class="comment"><p>The identifier of the language associated with this document.</p>
</div>
</div>



<a name="TextDocument.lineCount"></a><span class="ts" id=49 data-target="#details-49" data-toggle="collapse"><span class="ident">lineCount</span><span>: </span><a class="type-intrinsic">number</a></span>
<div class="details collapse" id="details-49">
<div class="comment"><p>The number of lines in this document.</p>
</div>
</div>



<a name="TextDocument.uri"></a><span class="ts" id=39 data-target="#details-39" data-toggle="collapse"><span class="ident">uri</span><span>: </span><a class="type-ref" href="#Uri">Uri</a></span>
<div class="details collapse" id="details-39">
<div class="comment"><p>The associated uri for this document.</p>
<p><em>Note</em> that most documents use the <code>file</code>-scheme, which means they are files on disk. However, <strong>not</strong> all documents are
saved on disk and therefore the <code>scheme</code> must be checked before trying to access the underlying file or siblings on disk.</p>
<ul>
<li><em>see</em> - <a href="#FileSystemProvider">FileSystemProvider</a></li>
</ul>
<ul>
<li><em>see</em> - <a href="#TextDocumentContentProvider">TextDocumentContentProvider</a></li>
</ul>
</div>
</div>



<a name="TextDocument.version"></a><span class="ts" id=43 data-target="#details-43" data-toggle="collapse"><span class="ident">version</span><span>: </span><a class="type-intrinsic">number</a></span>
<div class="details collapse" id="details-43">
<div class="comment"><p>The version number of this document (it will strictly increase after each
change, including undo/redo).</p>
</div>
</div>

#### Methods



<a name="TextDocument.getText"></a><span class="ts" id=62 data-target="#details-62" data-toggle="collapse"><span class="ident">getText</span><span>(</span><span class="ident">range</span><span>?</span><span>: </span><a class="type-ref" href="#Range">Range</a><span>)</span><span>: </span><a class="type-intrinsic">string</a></span>
<div class="details collapse" id="details-62">
<div class="comment"><p>Get the text of this document. A substring can be retrieved by providing
a range. The range will be <a href="#TextDocument.validateRange">adjusted</a>.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="range"></a><span class="ts" id=63 data-target="#details-63" data-toggle="collapse"><span class="ident">range</span><span>?</span><span>: </span><a class="type-ref" href="#Range">Range</a></span></td><td><div class="comment"><p>Include only the text included by the range.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-intrinsic">string</a></span></td><td><div class="comment"><p>The text inside the provided range or the entire text.</p>
</div></td></tr>
</table>
</div>
</div>



<a name="TextDocument.getWordRangeAtPosition"></a><span class="ts" id=65 data-target="#details-65" data-toggle="collapse"><span class="ident">getWordRangeAtPosition</span><span>(</span><span class="ident">position</span><span>: </span><a class="type-ref" href="#Position">Position</a>, <span class="ident">regex</span><span>?</span><span>: </span><a class="type-ref" href="#RegExp">RegExp</a><span>)</span><span>: </span><a class="type-ref" href="#Range">Range</a> &#124; <a class="type-intrinsic">undefined</a></span>
<div class="details collapse" id="details-65">
<div class="comment"><p>Get a word-range at the given position. By default words are defined by
common separators, like space, -, _, etc. In addition, per language custom
<a href="#LanguageConfiguration.wordPattern">word definitions</a> can be defined. It
is also possible to provide a custom regular expression.</p>
<ul>
<li><em>Note 1:</em> A custom regular expression must not match the empty string and
if it does, it will be ignored.</li>
<li><em>Note 2:</em> A custom regular expression will fail to match multiline strings
and in the name of speed regular expressions should not match words with
spaces. Use <a href="#TextLine.text"><code>TextLine.text</code></a> for more complex, non-wordy, scenarios.</li>
</ul>
<p>The position will be <a href="#TextDocument.validatePosition">adjusted</a>.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="position"></a><span class="ts" id=66 data-target="#details-66" data-toggle="collapse"><span class="ident">position</span><span>: </span><a class="type-ref" href="#Position">Position</a></span></td><td><div class="comment"><p>A position.</p>
</div></td></tr>
<tr><td><a name="regex"></a><span class="ts" id=67 data-target="#details-67" data-toggle="collapse"><span class="ident">regex</span><span>?</span><span>: </span><a class="type-ref" href="#RegExp">RegExp</a></span></td><td><div class="comment"><p>Optional regular expression that describes what a word is.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#Range">Range</a> &#124; <a class="type-intrinsic">undefined</a></span></td><td><div class="comment"><p>A range spanning a word, or <code>undefined</code>.</p>
</div></td></tr>
</table>
</div>
</div>



<a name="TextDocument.lineAt"></a><span class="ts" id=51 data-target="#details-51" data-toggle="collapse"><span class="ident">lineAt</span><span>(</span><span class="ident">line</span><span>: </span><a class="type-intrinsic">number</a><span>)</span><span>: </span><a class="type-ref" href="#TextLine">TextLine</a></span>
<div class="details collapse" id="details-51">
<div class="comment"><p>Returns a text line denoted by the line number. Note
that the returned object is <em>not</em> live and changes to the
document are not reflected.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="line"></a><span class="ts" id=52 data-target="#details-52" data-toggle="collapse"><span class="ident">line</span><span>: </span><a class="type-intrinsic">number</a></span></td><td><div class="comment"><p>A line number in [0, lineCount).</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#TextLine">TextLine</a></span></td><td><div class="comment"><p>A <a href="#TextLine">line</a>.</p>
</div></td></tr>
</table>
</div>
</div>



<a name="TextDocument.lineAt"></a><span class="ts" id=53 data-target="#details-53" data-toggle="collapse"><span class="ident">lineAt</span><span>(</span><span class="ident">position</span><span>: </span><a class="type-ref" href="#Position">Position</a><span>)</span><span>: </span><a class="type-ref" href="#TextLine">TextLine</a></span>
<div class="details collapse" id="details-53">
<div class="comment"><p>Returns a text line denoted by the position. Note
that the returned object is <em>not</em> live and changes to the
document are not reflected.</p>
<p>The position will be <a href="#TextDocument.validatePosition">adjusted</a>.</p>
<ul>
<li><em>see</em> - <a href="#TextDocument.lineAt">TextDocument.lineAt</a></li>
</ul>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="position"></a><span class="ts" id=54 data-target="#details-54" data-toggle="collapse"><span class="ident">position</span><span>: </span><a class="type-ref" href="#Position">Position</a></span></td><td><div class="comment"><p>A position.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#TextLine">TextLine</a></span></td><td><div class="comment"><p>A <a href="#TextLine">line</a>.</p>
</div></td></tr>
</table>
</div>
</div>



<a name="TextDocument.offsetAt"></a><span class="ts" id=56 data-target="#details-56" data-toggle="collapse"><span class="ident">offsetAt</span><span>(</span><span class="ident">position</span><span>: </span><a class="type-ref" href="#Position">Position</a><span>)</span><span>: </span><a class="type-intrinsic">number</a></span>
<div class="details collapse" id="details-56">
<div class="comment"><p>Converts the position to a zero-based offset.</p>
<p>The position will be <a href="#TextDocument.validatePosition">adjusted</a>.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="position"></a><span class="ts" id=57 data-target="#details-57" data-toggle="collapse"><span class="ident">position</span><span>: </span><a class="type-ref" href="#Position">Position</a></span></td><td><div class="comment"><p>A position.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-intrinsic">number</a></span></td><td><div class="comment"><p>A valid zero-based offset.</p>
</div></td></tr>
</table>
</div>
</div>



<a name="TextDocument.positionAt"></a><span class="ts" id=59 data-target="#details-59" data-toggle="collapse"><span class="ident">positionAt</span><span>(</span><span class="ident">offset</span><span>: </span><a class="type-intrinsic">number</a><span>)</span><span>: </span><a class="type-ref" href="#Position">Position</a></span>
<div class="details collapse" id="details-59">
<div class="comment"><p>Converts a zero-based offset to a position.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="offset"></a><span class="ts" id=60 data-target="#details-60" data-toggle="collapse"><span class="ident">offset</span><span>: </span><a class="type-intrinsic">number</a></span></td><td><div class="comment"><p>A zero-based offset.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#Position">Position</a></span></td><td><div class="comment"><p>A valid <a href="#Position">position</a>.</p>
</div></td></tr>
</table>
</div>
</div>



<a name="TextDocument.save"></a><span class="ts" id=47 data-target="#details-47" data-toggle="collapse"><span class="ident">save</span><span>(</span><span>)</span><span>: </span><a class="type-ref" href="#Thenable">Thenable</a>&lt;<a class="type-intrinsic">boolean</a>&gt;</span>
<div class="details collapse" id="details-47">
<div class="comment"><p>Save the underlying file.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#Thenable">Thenable</a>&lt;<a class="type-intrinsic">boolean</a>&gt;</span></td><td><div class="comment"><p>A promise that will resolve to true when the file
has been saved. If the file was not dirty or the save failed,
will return false.</p>
</div></td></tr>
</table>
</div>
</div>



<a name="TextDocument.validatePosition"></a><span class="ts" id=72 data-target="#details-72" data-toggle="collapse"><span class="ident">validatePosition</span><span>(</span><span class="ident">position</span><span>: </span><a class="type-ref" href="#Position">Position</a><span>)</span><span>: </span><a class="type-ref" href="#Position">Position</a></span>
<div class="details collapse" id="details-72">
<div class="comment"><p>Ensure a position is contained in the range of this document.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="position"></a><span class="ts" id=73 data-target="#details-73" data-toggle="collapse"><span class="ident">position</span><span>: </span><a class="type-ref" href="#Position">Position</a></span></td><td><div class="comment"><p>A position.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#Position">Position</a></span></td><td><div class="comment"><p>The given position or a new, adjusted position.</p>
</div></td></tr>
</table>
</div>
</div>



<a name="TextDocument.validateRange"></a><span class="ts" id=69 data-target="#details-69" data-toggle="collapse"><span class="ident">validateRange</span><span>(</span><span class="ident">range</span><span>: </span><a class="type-ref" href="#Range">Range</a><span>)</span><span>: </span><a class="type-ref" href="#Range">Range</a></span>
<div class="details collapse" id="details-69">
<div class="comment"><p>Ensure a range is completely contained in this document.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="range"></a><span class="ts" id=70 data-target="#details-70" data-toggle="collapse"><span class="ident">range</span><span>: </span><a class="type-ref" href="#Range">Range</a></span></td><td><div class="comment"><p>A range.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#Range">Range</a></span></td><td><div class="comment"><p>The given range or a new, adjusted range.</p>
</div></td></tr>
</table>
</div>
</div>

### <a name="TextDocumentChangeEvent"></a><span class="code-item" id=1786>TextDocumentChangeEvent</span>



<div class="comment"><p>An event describing a transactional <a href="#TextDocument">document</a> change.</p>
</div>

#### Properties



<a name="TextDocumentChangeEvent.contentChanges"></a><span class="ts" id=1788 data-target="#details-1788" data-toggle="collapse"><span class="ident">contentChanges</span><span>: </span><a class="type-ref" href="#TextDocumentContentChangeEvent">TextDocumentContentChangeEvent</a>[]</span>
<div class="details collapse" id="details-1788">
<div class="comment"><p>An array of content changes.</p>
</div>
</div>



<a name="TextDocumentChangeEvent.document"></a><span class="ts" id=1787 data-target="#details-1787" data-toggle="collapse"><span class="ident">document</span><span>: </span><a class="type-ref" href="#TextDocument">TextDocument</a></span>
<div class="details collapse" id="details-1787">
<div class="comment"><p>The affected document.</p>
</div>
</div>

### <a name="TextDocumentContentChangeEvent"></a><span class="code-item" id=1781>TextDocumentContentChangeEvent</span>



<div class="comment"><p>An event describing an individual change in the text of a <a href="#TextDocument">document</a>.</p>
</div>

#### Properties



<a name="TextDocumentContentChangeEvent.range"></a><span class="ts" id=1782 data-target="#details-1782" data-toggle="collapse"><span class="ident">range</span><span>: </span><a class="type-ref" href="#Range">Range</a></span>
<div class="details collapse" id="details-1782">
<div class="comment"><p>The range that got replaced.</p>
</div>
</div>



<a name="TextDocumentContentChangeEvent.rangeLength"></a><span class="ts" id=1784 data-target="#details-1784" data-toggle="collapse"><span class="ident">rangeLength</span><span>: </span><a class="type-intrinsic">number</a></span>
<div class="details collapse" id="details-1784">
<div class="comment"><p>The length of the range that got replaced.</p>
</div>
</div>



<a name="TextDocumentContentChangeEvent.rangeOffset"></a><span class="ts" id=1783 data-target="#details-1783" data-toggle="collapse"><span class="ident">rangeOffset</span><span>: </span><a class="type-intrinsic">number</a></span>
<div class="details collapse" id="details-1783">
<div class="comment"><p>The offset of the range that got replaced.</p>
</div>
</div>



<a name="TextDocumentContentChangeEvent.text"></a><span class="ts" id=1785 data-target="#details-1785" data-toggle="collapse"><span class="ident">text</span><span>: </span><a class="type-intrinsic">string</a></span>
<div class="details collapse" id="details-1785">
<div class="comment"><p>The new text for the range.</p>
</div>
</div>

### <a name="TextDocumentContentProvider"></a><span class="code-item" id=483>TextDocumentContentProvider</span>



<div class="comment"><p>A text document content provider allows to add readonly documents
to the editor, such as source from a dll or generated html from md.</p>
<p>Content providers are <a href="#workspace.registerTextDocumentContentProvider">registered</a>
for a <a href="#Uri.scheme">uri-scheme</a>. When a uri with that scheme is to
be <a href="#workspace.openTextDocument">loaded</a> the content provider is
asked.</p>
</div>

#### Events



<a name="TextDocumentContentProvider.onDidChange"></a><span class="ts" id=484 data-target="#details-484" data-toggle="collapse"><span class="ident">onDidChange</span><span>?</span><span>: </span><a class="type-ref" href="#Event">Event</a>&lt;<a class="type-ref" href="#Uri">Uri</a>&gt;</span>
<div class="details collapse" id="details-484">
<div class="comment"><p>An event to signal a resource has changed.</p>
</div>
</div>

#### Methods



<a name="TextDocumentContentProvider.provideTextDocumentContent"></a><span class="ts" id=486 data-target="#details-486" data-toggle="collapse"><span class="ident">provideTextDocumentContent</span><span>(</span><span class="ident">uri</span><span>: </span><a class="type-ref" href="#Uri">Uri</a>, <span class="ident">token</span><span>: </span><a class="type-ref" href="#CancellationToken">CancellationToken</a><span>)</span><span>: </span><a class="type-ref" href="#ProviderResult">ProviderResult</a>&lt;<a class="type-intrinsic">string</a>&gt;</span>
<div class="details collapse" id="details-486">
<div class="comment"><p>Provide textual content for a given uri.</p>
<p>The editor will use the returned string-content to create a readonly
<a href="#TextDocument">document</a>. Resources allocated should be released when
the corresponding document has been <a href="#workspace.onDidCloseTextDocument">closed</a>.</p>
<p><strong>Note</strong>: The contents of the created <a href="#TextDocument">document</a> might not be
identical to the provided text due to end-of-line-sequence normalization.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="uri"></a><span class="ts" id=487 data-target="#details-487" data-toggle="collapse"><span class="ident">uri</span><span>: </span><a class="type-ref" href="#Uri">Uri</a></span></td><td><div class="comment"><p>An uri which scheme matches the scheme this provider was <a href="#workspace.registerTextDocumentContentProvider">registered</a> for.</p>
</div></td></tr>
<tr><td><a name="token"></a><span class="ts" id=488 data-target="#details-488" data-toggle="collapse"><span class="ident">token</span><span>: </span><a class="type-ref" href="#CancellationToken">CancellationToken</a></span></td><td><div class="comment"><p>A cancellation token.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#ProviderResult">ProviderResult</a>&lt;<a class="type-intrinsic">string</a>&gt;</span></td><td><div class="comment"><p>A string or a thenable that resolves to such.</p>
</div></td></tr>
</table>
</div>
</div>

### <a name="TextDocumentSaveReason"></a><span class="code-item" id=1789>TextDocumentSaveReason</span>



<div class="comment"><p>Represents reasons why a text document is saved.</p>
</div>

#### Enumeration members



<a name="TextDocumentSaveReason.AfterDelay"></a><span class="ts" id=1791 data-target="#details-1791" data-toggle="collapse"><span class="ident">AfterDelay</span></span>
<div class="details collapse" id="details-1791">
<em>2</em>
</div>



<a name="TextDocumentSaveReason.FocusOut"></a><span class="ts" id=1792 data-target="#details-1792" data-toggle="collapse"><span class="ident">FocusOut</span></span>
<div class="details collapse" id="details-1792">
<em>3</em>
</div>



<a name="TextDocumentSaveReason.Manual"></a><span class="ts" id=1790 data-target="#details-1790" data-toggle="collapse"><span class="ident">Manual</span></span>
<div class="details collapse" id="details-1790">
<em>1</em>
</div>

### <a name="TextDocumentShowOptions"></a><span class="code-item" id=242>TextDocumentShowOptions</span>



<div class="comment"><p>Represents options to configure the behavior of showing a <a href="#TextDocument">document</a> in an <a href="#TextEditor">editor</a>.</p>
</div>

#### Properties



<a name="TextDocumentShowOptions.preserveFocus"></a><span class="ts" id=244 data-target="#details-244" data-toggle="collapse"><span class="ident">preserveFocus</span><span>?</span><span>: </span><a class="type-intrinsic">boolean</a></span>
<div class="details collapse" id="details-244">
<div class="comment"><p>An optional flag that when <code>true</code> will stop the <a href="#TextEditor">editor</a> from taking focus.</p>
</div>
</div>



<a name="TextDocumentShowOptions.preview"></a><span class="ts" id=245 data-target="#details-245" data-toggle="collapse"><span class="ident">preview</span><span>?</span><span>: </span><a class="type-intrinsic">boolean</a></span>
<div class="details collapse" id="details-245">
<div class="comment"><p>An optional flag that controls if an <a href="#TextEditor">editor</a>-tab will be replaced
with the next editor or if it will be kept.</p>
</div>
</div>



<a name="TextDocumentShowOptions.selection"></a><span class="ts" id=246 data-target="#details-246" data-toggle="collapse"><span class="ident">selection</span><span>?</span><span>: </span><a class="type-ref" href="#Range">Range</a></span>
<div class="details collapse" id="details-246">
<div class="comment"><p>An optional selection to apply for the document in the <a href="#TextEditor">editor</a>.</p>
</div>
</div>



<a name="TextDocumentShowOptions.viewColumn"></a><span class="ts" id=243 data-target="#details-243" data-toggle="collapse"><span class="ident">viewColumn</span><span>?</span><span>: </span><a class="type-ref" href="#ViewColumn">ViewColumn</a></span>
<div class="details collapse" id="details-243">
<div class="comment"><p>An optional view column in which the <a href="#TextEditor">editor</a> should be shown.
The default is the <a href="#ViewColumn.Active">active</a>, other values are adjusted to
be <code>Min(column, columnCount + 1)</code>, the <a href="#ViewColumn.Active">active</a>-column is
not adjusted. Use <a href="#ViewColumn.Beside"><code>ViewColumn.Beside</code></a> to open the
editor to the side of the currently active one.</p>
</div>
</div>

### <a name="TextDocumentWillSaveEvent"></a><span class="code-item" id=1793>TextDocumentWillSaveEvent</span>



<div class="comment"><p>An event that is fired when a <a href="#TextDocument">document</a> will be saved.</p>
<p>To make modifications to the document before it is being saved, call the
<a href="#TextDocumentWillSaveEvent.waitUntil"><code>waitUntil</code></a>-function with a thenable
that resolves to an array of <a href="#TextEdit">text edits</a>.</p>
</div>

#### Properties



<a name="TextDocumentWillSaveEvent.document"></a><span class="ts" id=1794 data-target="#details-1794" data-toggle="collapse"><span class="ident">document</span><span>: </span><a class="type-ref" href="#TextDocument">TextDocument</a></span>
<div class="details collapse" id="details-1794">
<div class="comment"><p>The document that will be saved.</p>
</div>
</div>



<a name="TextDocumentWillSaveEvent.reason"></a><span class="ts" id=1795 data-target="#details-1795" data-toggle="collapse"><span class="ident">reason</span><span>: </span><a class="type-ref" href="#TextDocumentSaveReason">TextDocumentSaveReason</a></span>
<div class="details collapse" id="details-1795">
<div class="comment"><p>The reason why save was triggered.</p>
</div>
</div>

#### Methods



<a name="TextDocumentWillSaveEvent.waitUntil"></a><span class="ts" id=1797 data-target="#details-1797" data-toggle="collapse"><span class="ident">waitUntil</span><span>(</span><span class="ident">thenable</span><span>: </span><a class="type-ref" href="#Thenable">Thenable</a>&lt;<a class="type-ref" href="#TextEdit">TextEdit</a>[]&gt;<span>)</span><span>: </span><a class="type-intrinsic">void</a></span>
<div class="details collapse" id="details-1797">
<div class="comment"><p>Allows to pause the event loop and to apply <a href="#TextEdit">pre-save-edits</a>.
Edits of subsequent calls to this function will be applied in order. The
edits will be <em>ignored</em> if concurrent modifications of the document happened.</p>
<p><em>Note:</em> This function can only be called during event dispatch and not
in an asynchronous manner:</p>

<pre><code class="lang-ts">workspace.onWillSaveTextDocument(event =&gt; {
    // async, will *throw* an error
    setTimeout(() =&gt; event.waitUntil(promise));

    // sync, OK
    event.waitUntil(promise);
})
</code></pre>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="thenable"></a><span class="ts" id=1798 data-target="#details-1798" data-toggle="collapse"><span class="ident">thenable</span><span>: </span><a class="type-ref" href="#Thenable">Thenable</a>&lt;<a class="type-ref" href="#TextEdit">TextEdit</a>[]&gt;</span></td><td><div class="comment"><p>A thenable that resolves to <a href="#TextEdit">pre-save-edits</a>.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-intrinsic">void</a></span></td><td><div class="comment"></div></td></tr>
</table>
</div>
</div>



<a name="TextDocumentWillSaveEvent.waitUntil"></a><span class="ts" id=1799 data-target="#details-1799" data-toggle="collapse"><span class="ident">waitUntil</span><span>(</span><span class="ident">thenable</span><span>: </span><a class="type-ref" href="#Thenable">Thenable</a>&lt;<a class="type-intrinsic">any</a>&gt;<span>)</span><span>: </span><a class="type-intrinsic">void</a></span>
<div class="details collapse" id="details-1799">
<div class="comment"><p>Allows to pause the event loop until the provided thenable resolved.</p>
<p><em>Note:</em> This function can only be called during event dispatch.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="thenable"></a><span class="ts" id=1800 data-target="#details-1800" data-toggle="collapse"><span class="ident">thenable</span><span>: </span><a class="type-ref" href="#Thenable">Thenable</a>&lt;<a class="type-intrinsic">any</a>&gt;</span></td><td><div class="comment"><p>A thenable that delays saving.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-intrinsic">void</a></span></td><td><div class="comment"></div></td></tr>
</table>
</div>
</div>

### <a name="TextEdit"></a><span class="code-item" id=762>TextEdit</span>



<div class="comment"><p>A text edit represents edits that should be applied
to a document.</p>
</div>

#### Static



<a name="TextEdit.delete"></a><span class="ts" id=772 data-target="#details-772" data-toggle="collapse"><span class="ident">delete</span><span>(</span><span class="ident">range</span><span>: </span><a class="type-ref" href="#Range">Range</a><span>)</span><span>: </span><a class="type-ref" href="#TextEdit">TextEdit</a></span>
<div class="details collapse" id="details-772">
<div class="comment"><p>Utility to create a delete edit.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="range"></a><span class="ts" id=773 data-target="#details-773" data-toggle="collapse"><span class="ident">range</span><span>: </span><a class="type-ref" href="#Range">Range</a></span></td><td><div class="comment"><p>A range.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#TextEdit">TextEdit</a></span></td><td><div class="comment"><p>A new text edit object.</p>
</div></td></tr>
</table>
</div>
</div>



<a name="TextEdit.insert"></a><span class="ts" id=768 data-target="#details-768" data-toggle="collapse"><span class="ident">insert</span><span>(</span><span class="ident">position</span><span>: </span><a class="type-ref" href="#Position">Position</a>, <span class="ident">newText</span><span>: </span><a class="type-intrinsic">string</a><span>)</span><span>: </span><a class="type-ref" href="#TextEdit">TextEdit</a></span>
<div class="details collapse" id="details-768">
<div class="comment"><p>Utility to create an insert edit.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="position"></a><span class="ts" id=769 data-target="#details-769" data-toggle="collapse"><span class="ident">position</span><span>: </span><a class="type-ref" href="#Position">Position</a></span></td><td><div class="comment"><p>A position, will become an empty range.</p>
</div></td></tr>
<tr><td><a name="newText"></a><span class="ts" id=770 data-target="#details-770" data-toggle="collapse"><span class="ident">newText</span><span>: </span><a class="type-intrinsic">string</a></span></td><td><div class="comment"><p>A string.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#TextEdit">TextEdit</a></span></td><td><div class="comment"><p>A new text edit object.</p>
</div></td></tr>
</table>
</div>
</div>



<a name="TextEdit.replace"></a><span class="ts" id=764 data-target="#details-764" data-toggle="collapse"><span class="ident">replace</span><span>(</span><span class="ident">range</span><span>: </span><a class="type-ref" href="#Range">Range</a>, <span class="ident">newText</span><span>: </span><a class="type-intrinsic">string</a><span>)</span><span>: </span><a class="type-ref" href="#TextEdit">TextEdit</a></span>
<div class="details collapse" id="details-764">
<div class="comment"><p>Utility to create a replace edit.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="range"></a><span class="ts" id=765 data-target="#details-765" data-toggle="collapse"><span class="ident">range</span><span>: </span><a class="type-ref" href="#Range">Range</a></span></td><td><div class="comment"><p>A range.</p>
</div></td></tr>
<tr><td><a name="newText"></a><span class="ts" id=766 data-target="#details-766" data-toggle="collapse"><span class="ident">newText</span><span>: </span><a class="type-intrinsic">string</a></span></td><td><div class="comment"><p>A string.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#TextEdit">TextEdit</a></span></td><td><div class="comment"><p>A new text edit object.</p>
</div></td></tr>
</table>
</div>
</div>



<a name="TextEdit.setEndOfLine"></a><span class="ts" id=775 data-target="#details-775" data-toggle="collapse"><span class="ident">setEndOfLine</span><span>(</span><span class="ident">eol</span><span>: </span><a class="type-ref" href="#EndOfLine">EndOfLine</a><span>)</span><span>: </span><a class="type-ref" href="#TextEdit">TextEdit</a></span>
<div class="details collapse" id="details-775">
<div class="comment"><p>Utility to create an eol-edit.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="eol"></a><span class="ts" id=776 data-target="#details-776" data-toggle="collapse"><span class="ident">eol</span><span>: </span><a class="type-ref" href="#EndOfLine">EndOfLine</a></span></td><td><div class="comment"><p>An eol-sequence</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#TextEdit">TextEdit</a></span></td><td><div class="comment"><p>A new text edit object.</p>
</div></td></tr>
</table>
</div>
</div>

#### Constructors



<a name="TextEdit.new TextEdit"></a><span class="ts" id=781 data-target="#details-781" data-toggle="collapse"><span class="ident">new TextEdit</span><span>(</span><span class="ident">range</span><span>: </span><a class="type-ref" href="#Range">Range</a>, <span class="ident">newText</span><span>: </span><a class="type-intrinsic">string</a><span>)</span><span>: </span><a class="type-ref" href="#TextEdit">TextEdit</a></span>
<div class="details collapse" id="details-781">
<div class="comment"><p>Create a new TextEdit.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="range"></a><span class="ts" id=782 data-target="#details-782" data-toggle="collapse"><span class="ident">range</span><span>: </span><a class="type-ref" href="#Range">Range</a></span></td><td><div class="comment"><p>A range.</p>
</div></td></tr>
<tr><td><a name="newText"></a><span class="ts" id=783 data-target="#details-783" data-toggle="collapse"><span class="ident">newText</span><span>: </span><a class="type-intrinsic">string</a></span></td><td><div class="comment"><p>A string.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#TextEdit">TextEdit</a></span></td><td><div class="comment"></div></td></tr>
</table>
</div>
</div>

#### Properties



<a name="TextEdit.newEol"></a><span class="ts" id=779 data-target="#details-779" data-toggle="collapse"><span class="ident">newEol</span><span>: </span><a class="type-ref" href="#EndOfLine">EndOfLine</a></span>
<div class="details collapse" id="details-779">
<div class="comment"><p>The eol-sequence used in the document.</p>
<p><em>Note</em> that the eol-sequence will be applied to the
whole document.</p>
</div>
</div>



<a name="TextEdit.newText"></a><span class="ts" id=778 data-target="#details-778" data-toggle="collapse"><span class="ident">newText</span><span>: </span><a class="type-intrinsic">string</a></span>
<div class="details collapse" id="details-778">
<div class="comment"><p>The string this edit will insert.</p>
</div>
</div>



<a name="TextEdit.range"></a><span class="ts" id=777 data-target="#details-777" data-toggle="collapse"><span class="ident">range</span><span>: </span><a class="type-ref" href="#Range">Range</a></span>
<div class="details collapse" id="details-777">
<div class="comment"><p>The range this edit applies to.</p>
</div>
</div>

### <a name="TextEditor"></a><span class="code-item" id=335>TextEditor</span>



<div class="comment"><p>Represents an editor that is attached to a <a href="#TextDocument">document</a>.</p>
</div>

#### Properties



<a name="TextEditor.document"></a><span class="ts" id=336 data-target="#details-336" data-toggle="collapse"><span class="ident">document</span><span>: </span><a class="type-ref" href="#TextDocument">TextDocument</a></span>
<div class="details collapse" id="details-336">
<div class="comment"><p>The document associated with this text editor. The document will be the same for the entire lifetime of this text editor.</p>
</div>
</div>



<a name="TextEditor.options"></a><span class="ts" id=340 data-target="#details-340" data-toggle="collapse"><span class="ident">options</span><span>: </span><a class="type-ref" href="#TextEditorOptions">TextEditorOptions</a></span>
<div class="details collapse" id="details-340">
<div class="comment"><p>Text editor options.</p>
</div>
</div>



<a name="TextEditor.selection"></a><span class="ts" id=337 data-target="#details-337" data-toggle="collapse"><span class="ident">selection</span><span>: </span><a class="type-ref" href="#Selection">Selection</a></span>
<div class="details collapse" id="details-337">
<div class="comment"><p>The primary selection on this text editor. Shorthand for <code>TextEditor.selections[0]</code>.</p>
</div>
</div>



<a name="TextEditor.selections"></a><span class="ts" id=338 data-target="#details-338" data-toggle="collapse"><span class="ident">selections</span><span>: </span><a class="type-ref" href="#Selection">Selection</a>[]</span>
<div class="details collapse" id="details-338">
<div class="comment"><p>The selections in this text editor. The primary selection is always at index 0.</p>
</div>
</div>



<a name="TextEditor.viewColumn"></a><span class="ts" id=341 data-target="#details-341" data-toggle="collapse"><span class="ident">viewColumn</span><span>?</span><span>: </span><a class="type-ref" href="#ViewColumn">ViewColumn</a></span>
<div class="details collapse" id="details-341">
<div class="comment"><p>The column in which this editor shows. Will be <code>undefined</code> in case this
isn&#39;t one of the main editors, e.g an embedded editor, or when the editor
column is larger than three.</p>
</div>
</div>



<a name="TextEditor.visibleRanges"></a><span class="ts" id=339 data-target="#details-339" data-toggle="collapse"><span class="ident">visibleRanges</span><span>: </span><a class="type-ref" href="#Range">Range</a>[]</span>
<div class="details collapse" id="details-339">
<div class="comment"><p>The current visible ranges in the editor (vertically).
This accounts only for vertical scrolling, and not for horizontal scrolling.</p>
</div>
</div>

#### Methods



<a name="TextEditor.edit"></a><span class="ts" id=343 data-target="#details-343" data-toggle="collapse"><span class="ident">edit</span><span>(</span><span class="ident">callback</span><span>: </span>(editBuilder: <a class="type-ref" href="#TextEditorEdit">TextEditorEdit</a>) =&gt; <a class="type-intrinsic">void</a>, <span class="ident">options</span><span>?</span><span>: </span>{undoStopAfter: <a class="type-intrinsic">boolean</a>, undoStopBefore: <a class="type-intrinsic">boolean</a>}<span>)</span><span>: </span><a class="type-ref" href="#Thenable">Thenable</a>&lt;<a class="type-intrinsic">boolean</a>&gt;</span>
<div class="details collapse" id="details-343">
<div class="comment"><p>Perform an edit on the document associated with this text editor.</p>
<p>The given callback-function is invoked with an <a href="#TextEditorEdit">edit-builder</a> which must
be used to make edits. Note that the edit-builder is only valid while the
callback executes.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="callback"></a><span class="ts" id=344 data-target="#details-344" data-toggle="collapse"><span class="ident">callback</span><span>: </span>(editBuilder: <a class="type-ref" href="#TextEditorEdit">TextEditorEdit</a>) =&gt; <a class="type-intrinsic">void</a></span></td><td><div class="comment"><p>A function which can create edits using an <a href="#TextEditorEdit">edit-builder</a>.</p>
</div></td></tr>
<tr><td><a name="options"></a><span class="ts" id=348 data-target="#details-348" data-toggle="collapse"><span class="ident">options</span><span>?</span><span>: </span>{undoStopAfter: <a class="type-intrinsic">boolean</a>, undoStopBefore: <a class="type-intrinsic">boolean</a>}</span></td><td><div class="comment"><p>The undo/redo behavior around this edit. By default, undo stops will be created before and after this edit.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#Thenable">Thenable</a>&lt;<a class="type-intrinsic">boolean</a>&gt;</span></td><td><div class="comment"><p>A promise that resolves with a value indicating if the edits could be applied.</p>
</div></td></tr>
</table>
</div>
</div>



<a name="TextEditor.hide"></a><span class="ts" id=372 data-target="#details-372" data-toggle="collapse"><span class="ident">hide</span><span>(</span><span>)</span><span>: </span><a class="type-intrinsic">void</a></span>
<div class="details collapse" id="details-372">
<div class="comment"><p><del>Hide the text editor.</del></p>
<ul>
<li><em>deprecated</em> - Use the command <code>workbench.action.closeActiveEditor</code> instead.
This method shows unexpected behavior and will be removed in the next major update.</li>
</ul>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-intrinsic">void</a></span></td><td><div class="comment"></div></td></tr>
</table>
</div>
</div>



<a name="TextEditor.insertSnippet"></a><span class="ts" id=353 data-target="#details-353" data-toggle="collapse"><span class="ident">insertSnippet</span><span>(</span><span class="ident">snippet</span><span>: </span><a class="type-ref" href="#SnippetString">SnippetString</a>, <span class="ident">location</span><span>?</span><span>: </span><a class="type-ref" href="#Position">Position</a> &#124; <a class="type-ref" href="#Range">Range</a> &#124; <a class="type-ref" href="#Position">Position</a>[] &#124; <a class="type-ref" href="#Range">Range</a>[], <span class="ident">options</span><span>?</span><span>: </span>{undoStopAfter: <a class="type-intrinsic">boolean</a>, undoStopBefore: <a class="type-intrinsic">boolean</a>}<span>)</span><span>: </span><a class="type-ref" href="#Thenable">Thenable</a>&lt;<a class="type-intrinsic">boolean</a>&gt;</span>
<div class="details collapse" id="details-353">
<div class="comment"><p>Insert a <a href="#SnippetString">snippet</a> and put the editor into snippet mode. &quot;Snippet mode&quot;
means the editor adds placeholders and additional cursors so that the user can complete
or accept the snippet.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="snippet"></a><span class="ts" id=354 data-target="#details-354" data-toggle="collapse"><span class="ident">snippet</span><span>: </span><a class="type-ref" href="#SnippetString">SnippetString</a></span></td><td><div class="comment"><p>The snippet to insert in this edit.</p>
</div></td></tr>
<tr><td><a name="location"></a><span class="ts" id=355 data-target="#details-355" data-toggle="collapse"><span class="ident">location</span><span>?</span><span>: </span><a class="type-ref" href="#Position">Position</a> &#124; <a class="type-ref" href="#Range">Range</a> &#124; <a class="type-ref" href="#Position">Position</a>[] &#124; <a class="type-ref" href="#Range">Range</a>[]</span></td><td><div class="comment"><p>Position or range at which to insert the snippet, defaults to the current editor selection or selections.</p>
</div></td></tr>
<tr><td><a name="options"></a><span class="ts" id=356 data-target="#details-356" data-toggle="collapse"><span class="ident">options</span><span>?</span><span>: </span>{undoStopAfter: <a class="type-intrinsic">boolean</a>, undoStopBefore: <a class="type-intrinsic">boolean</a>}</span></td><td><div class="comment"><p>The undo/redo behavior around this edit. By default, undo stops will be created before and after this edit.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#Thenable">Thenable</a>&lt;<a class="type-intrinsic">boolean</a>&gt;</span></td><td><div class="comment"><p>A promise that resolves with a value indicating if the snippet could be inserted. Note that the promise does not signal
that the snippet is completely filled-in or accepted.</p>
</div></td></tr>
</table>
</div>
</div>



<a name="TextEditor.revealRange"></a><span class="ts" id=365 data-target="#details-365" data-toggle="collapse"><span class="ident">revealRange</span><span>(</span><span class="ident">range</span><span>: </span><a class="type-ref" href="#Range">Range</a>, <span class="ident">revealType</span><span>?</span><span>: </span><a class="type-ref" href="#TextEditorRevealType">TextEditorRevealType</a><span>)</span><span>: </span><a class="type-intrinsic">void</a></span>
<div class="details collapse" id="details-365">
<div class="comment"><p>Scroll as indicated by <code>revealType</code> in order to reveal the given range.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="range"></a><span class="ts" id=366 data-target="#details-366" data-toggle="collapse"><span class="ident">range</span><span>: </span><a class="type-ref" href="#Range">Range</a></span></td><td><div class="comment"><p>A range.</p>
</div></td></tr>
<tr><td><a name="revealType"></a><span class="ts" id=367 data-target="#details-367" data-toggle="collapse"><span class="ident">revealType</span><span>?</span><span>: </span><a class="type-ref" href="#TextEditorRevealType">TextEditorRevealType</a></span></td><td><div class="comment"><p>The scrolling strategy for revealing <code>range</code>.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-intrinsic">void</a></span></td><td><div class="comment"></div></td></tr>
</table>
</div>
</div>



<a name="TextEditor.setDecorations"></a><span class="ts" id=361 data-target="#details-361" data-toggle="collapse"><span class="ident">setDecorations</span><span>(</span><span class="ident">decorationType</span><span>: </span><a class="type-ref" href="#TextEditorDecorationType">TextEditorDecorationType</a>, <span class="ident">rangesOrOptions</span><span>: </span><a class="type-ref" href="#Range">Range</a>[] &#124; <a class="type-ref" href="#DecorationOptions">DecorationOptions</a>[]<span>)</span><span>: </span><a class="type-intrinsic">void</a></span>
<div class="details collapse" id="details-361">
<div class="comment"><p>Adds a set of decorations to the text editor. If a set of decorations already exists with
the given <a href="#TextEditorDecorationType">decoration type</a>, they will be replaced.</p>
<ul>
<li><em>see</em> - <a href="#window.createTextEditorDecorationType">createTextEditorDecorationType</a>.</li>
</ul>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="decorationType"></a><span class="ts" id=362 data-target="#details-362" data-toggle="collapse"><span class="ident">decorationType</span><span>: </span><a class="type-ref" href="#TextEditorDecorationType">TextEditorDecorationType</a></span></td><td><div class="comment"><p>A decoration type.</p>
</div></td></tr>
<tr><td><a name="rangesOrOptions"></a><span class="ts" id=363 data-target="#details-363" data-toggle="collapse"><span class="ident">rangesOrOptions</span><span>: </span><a class="type-ref" href="#Range">Range</a>[] &#124; <a class="type-ref" href="#DecorationOptions">DecorationOptions</a>[]</span></td><td><div class="comment"><p>Either <a href="#Range">ranges</a> or more detailed <a href="#DecorationOptions">options</a>.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-intrinsic">void</a></span></td><td><div class="comment"></div></td></tr>
</table>
</div>
</div>



<a name="TextEditor.show"></a><span class="ts" id=369 data-target="#details-369" data-toggle="collapse"><span class="ident">show</span><span>(</span><span class="ident">column</span><span>?</span><span>: </span><a class="type-ref" href="#ViewColumn">ViewColumn</a><span>)</span><span>: </span><a class="type-intrinsic">void</a></span>
<div class="details collapse" id="details-369">
<div class="comment"><p><del>Show the text editor.</del></p>
<ul>
<li><em>deprecated</em> - Use <a href="#window.showTextDocument">window.showTextDocument</a> instead.</li>
</ul>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="column"></a><span class="ts" id=370 data-target="#details-370" data-toggle="collapse"><span class="ident">column</span><span>?</span><span>: </span><a class="type-ref" href="#ViewColumn">ViewColumn</a></span></td><td><div class="comment"><p>The <a href="#ViewColumn">column</a> in which to show this editor.
This method shows unexpected behavior and will be removed in the next major update.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-intrinsic">void</a></span></td><td><div class="comment"></div></td></tr>
</table>
</div>
</div>

### <a name="TextEditorCursorStyle"></a><span class="code-item" id=207>TextEditorCursorStyle</span>



<div class="comment"><p>Rendering style of the cursor.</p>
</div>

#### Enumeration members



<a name="TextEditorCursorStyle.Block"></a><span class="ts" id=209 data-target="#details-209" data-toggle="collapse"><span class="ident">Block</span></span>
<div class="details collapse" id="details-209">
<em>2</em>
</div>



<a name="TextEditorCursorStyle.BlockOutline"></a><span class="ts" id=212 data-target="#details-212" data-toggle="collapse"><span class="ident">BlockOutline</span></span>
<div class="details collapse" id="details-212">
<em>5</em>
</div>



<a name="TextEditorCursorStyle.Line"></a><span class="ts" id=208 data-target="#details-208" data-toggle="collapse"><span class="ident">Line</span></span>
<div class="details collapse" id="details-208">
<em>1</em>
</div>



<a name="TextEditorCursorStyle.LineThin"></a><span class="ts" id=211 data-target="#details-211" data-toggle="collapse"><span class="ident">LineThin</span></span>
<div class="details collapse" id="details-211">
<em>4</em>
</div>



<a name="TextEditorCursorStyle.Underline"></a><span class="ts" id=210 data-target="#details-210" data-toggle="collapse"><span class="ident">Underline</span></span>
<div class="details collapse" id="details-210">
<em>3</em>
</div>



<a name="TextEditorCursorStyle.UnderlineThin"></a><span class="ts" id=213 data-target="#details-213" data-toggle="collapse"><span class="ident">UnderlineThin</span></span>
<div class="details collapse" id="details-213">
<em>6</em>
</div>

### <a name="TextEditorDecorationType"></a><span class="code-item" id=223>TextEditorDecorationType</span>



<div class="comment"><p>Represents a handle to a set of decorations
sharing the same <a href="#DecorationRenderOptions">styling options</a> in a <a href="#TextEditor">text editor</a>.</p>
<p>To get an instance of a <code>TextEditorDecorationType</code> use
<a href="#window.createTextEditorDecorationType">createTextEditorDecorationType</a>.</p>
</div>

#### Properties



<a name="TextEditorDecorationType.key"></a><span class="ts" id=224 data-target="#details-224" data-toggle="collapse"><span class="ident">key</span><span>: </span><a class="type-intrinsic">string</a></span>
<div class="details collapse" id="details-224">
<div class="comment"><p>Internal representation of the handle.</p>
</div>
</div>

#### Methods



<a name="TextEditorDecorationType.dispose"></a><span class="ts" id=226 data-target="#details-226" data-toggle="collapse"><span class="ident">dispose</span><span>(</span><span>)</span><span>: </span><a class="type-intrinsic">void</a></span>
<div class="details collapse" id="details-226">
<div class="comment"><p>Remove this decoration type and all decorations on all text editors using it.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-intrinsic">void</a></span></td><td><div class="comment"></div></td></tr>
</table>
</div>
</div>

### <a name="TextEditorEdit"></a><span class="code-item" id=376>TextEditorEdit</span>



<div class="comment"><p>A complex edit that will be applied in one transaction on a TextEditor.
This holds a description of the edits and if the edits are valid (i.e. no overlapping regions, document was not changed in the meantime, etc.)
they can be applied on a <a href="#TextDocument">document</a> associated with a <a href="#TextEditor">text editor</a>.</p>
</div>

#### Methods



<a name="TextEditorEdit.delete"></a><span class="ts" id=386 data-target="#details-386" data-toggle="collapse"><span class="ident">delete</span><span>(</span><span class="ident">location</span><span>: </span><a class="type-ref" href="#Range">Range</a> &#124; <a class="type-ref" href="#Selection">Selection</a><span>)</span><span>: </span><a class="type-intrinsic">void</a></span>
<div class="details collapse" id="details-386">
<div class="comment"><p>Delete a certain text region.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="location"></a><span class="ts" id=387 data-target="#details-387" data-toggle="collapse"><span class="ident">location</span><span>: </span><a class="type-ref" href="#Range">Range</a> &#124; <a class="type-ref" href="#Selection">Selection</a></span></td><td><div class="comment"><p>The range this operation should remove.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-intrinsic">void</a></span></td><td><div class="comment"></div></td></tr>
</table>
</div>
</div>



<a name="TextEditorEdit.insert"></a><span class="ts" id=382 data-target="#details-382" data-toggle="collapse"><span class="ident">insert</span><span>(</span><span class="ident">location</span><span>: </span><a class="type-ref" href="#Position">Position</a>, <span class="ident">value</span><span>: </span><a class="type-intrinsic">string</a><span>)</span><span>: </span><a class="type-intrinsic">void</a></span>
<div class="details collapse" id="details-382">
<div class="comment"><p>Insert text at a location.
You can use \r\n or \n in <code>value</code> and they will be normalized to the current <a href="#TextDocument">document</a>.
Although the equivalent text edit can be made with <a href="#TextEditorEdit.replace">replace</a>, <code>insert</code> will produce a different resulting selection (it will get moved).</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="location"></a><span class="ts" id=383 data-target="#details-383" data-toggle="collapse"><span class="ident">location</span><span>: </span><a class="type-ref" href="#Position">Position</a></span></td><td><div class="comment"><p>The position where the new text should be inserted.</p>
</div></td></tr>
<tr><td><a name="value"></a><span class="ts" id=384 data-target="#details-384" data-toggle="collapse"><span class="ident">value</span><span>: </span><a class="type-intrinsic">string</a></span></td><td><div class="comment"><p>The new text this operation should insert.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-intrinsic">void</a></span></td><td><div class="comment"></div></td></tr>
</table>
</div>
</div>



<a name="TextEditorEdit.replace"></a><span class="ts" id=378 data-target="#details-378" data-toggle="collapse"><span class="ident">replace</span><span>(</span><span class="ident">location</span><span>: </span><a class="type-ref" href="#Position">Position</a> &#124; <a class="type-ref" href="#Range">Range</a> &#124; <a class="type-ref" href="#Selection">Selection</a>, <span class="ident">value</span><span>: </span><a class="type-intrinsic">string</a><span>)</span><span>: </span><a class="type-intrinsic">void</a></span>
<div class="details collapse" id="details-378">
<div class="comment"><p>Replace a certain text region with a new value.
You can use \r\n or \n in <code>value</code> and they will be normalized to the current <a href="#TextDocument">document</a>.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="location"></a><span class="ts" id=379 data-target="#details-379" data-toggle="collapse"><span class="ident">location</span><span>: </span><a class="type-ref" href="#Position">Position</a> &#124; <a class="type-ref" href="#Range">Range</a> &#124; <a class="type-ref" href="#Selection">Selection</a></span></td><td><div class="comment"><p>The range this operation should remove.</p>
</div></td></tr>
<tr><td><a name="value"></a><span class="ts" id=380 data-target="#details-380" data-toggle="collapse"><span class="ident">value</span><span>: </span><a class="type-intrinsic">string</a></span></td><td><div class="comment"><p>The new text this operation should insert after removing <code>location</code>.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-intrinsic">void</a></span></td><td><div class="comment"></div></td></tr>
</table>
</div>
</div>



<a name="TextEditorEdit.setEndOfLine"></a><span class="ts" id=389 data-target="#details-389" data-toggle="collapse"><span class="ident">setEndOfLine</span><span>(</span><span class="ident">endOfLine</span><span>: </span><a class="type-ref" href="#EndOfLine">EndOfLine</a><span>)</span><span>: </span><a class="type-intrinsic">void</a></span>
<div class="details collapse" id="details-389">
<div class="comment"><p>Set the end of line sequence.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="endOfLine"></a><span class="ts" id=390 data-target="#details-390" data-toggle="collapse"><span class="ident">endOfLine</span><span>: </span><a class="type-ref" href="#EndOfLine">EndOfLine</a></span></td><td><div class="comment"><p>The new end of line for the <a href="#TextDocument">document</a>.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-intrinsic">void</a></span></td><td><div class="comment"></div></td></tr>
</table>
</div>
</div>

### <a name="TextEditorLineNumbersStyle"></a><span class="code-item" id=214>TextEditorLineNumbersStyle</span>



<div class="comment"><p>Rendering style of the line numbers.</p>
</div>

#### Enumeration members



<a name="TextEditorLineNumbersStyle.Off"></a><span class="ts" id=215 data-target="#details-215" data-toggle="collapse"><span class="ident">Off</span></span>
<div class="details collapse" id="details-215">
<em>0</em>
</div>



<a name="TextEditorLineNumbersStyle.On"></a><span class="ts" id=216 data-target="#details-216" data-toggle="collapse"><span class="ident">On</span></span>
<div class="details collapse" id="details-216">
<em>1</em>
</div>



<a name="TextEditorLineNumbersStyle.Relative"></a><span class="ts" id=217 data-target="#details-217" data-toggle="collapse"><span class="ident">Relative</span></span>
<div class="details collapse" id="details-217">
<em>2</em>
</div>

### <a name="TextEditorOptions"></a><span class="code-item" id=218>TextEditorOptions</span>



<div class="comment"><p>Represents a <a href="#TextEditor">text editor</a>&#39;s <a href="#TextEditor.options">options</a>.</p>
</div>

#### Properties



<a name="TextEditorOptions.cursorStyle"></a><span class="ts" id=221 data-target="#details-221" data-toggle="collapse"><span class="ident">cursorStyle</span><span>?</span><span>: </span><a class="type-ref" href="#TextEditorCursorStyle">TextEditorCursorStyle</a></span>
<div class="details collapse" id="details-221">
<div class="comment"><p>The rendering style of the cursor in this editor.
When getting a text editor&#39;s options, this property will always be present.
When setting a text editor&#39;s options, this property is optional.</p>
</div>
</div>



<a name="TextEditorOptions.insertSpaces"></a><span class="ts" id=220 data-target="#details-220" data-toggle="collapse"><span class="ident">insertSpaces</span><span>?</span><span>: </span><a class="type-intrinsic">boolean</a> &#124; <a class="type-intrinsic">string</a></span>
<div class="details collapse" id="details-220">
<div class="comment"><p>When pressing Tab insert <a href="#TextEditorOptions.tabSize">n</a> spaces.
When getting a text editor&#39;s options, this property will always be a boolean (resolved).
When setting a text editor&#39;s options, this property is optional and it can be a boolean or <code>&quot;auto&quot;</code>.</p>
</div>
</div>



<a name="TextEditorOptions.lineNumbers"></a><span class="ts" id=222 data-target="#details-222" data-toggle="collapse"><span class="ident">lineNumbers</span><span>?</span><span>: </span><a class="type-ref" href="#TextEditorLineNumbersStyle">TextEditorLineNumbersStyle</a></span>
<div class="details collapse" id="details-222">
<div class="comment"><p>Render relative line numbers w.r.t. the current line number.
When getting a text editor&#39;s options, this property will always be present.
When setting a text editor&#39;s options, this property is optional.</p>
</div>
</div>



<a name="TextEditorOptions.tabSize"></a><span class="ts" id=219 data-target="#details-219" data-toggle="collapse"><span class="ident">tabSize</span><span>?</span><span>: </span><a class="type-intrinsic">number</a> &#124; <a class="type-intrinsic">string</a></span>
<div class="details collapse" id="details-219">
<div class="comment"><p>The size in spaces a tab takes. This is used for two purposes:</p>
<ul>
<li>the rendering width of a tab character;</li>
<li>the number of spaces to insert when <a href="#TextEditorOptions.insertSpaces">insertSpaces</a> is true.</li>
</ul>
<p>When getting a text editor&#39;s options, this property will always be a number (resolved).
When setting a text editor&#39;s options, this property is optional and it can be a number or <code>&quot;auto&quot;</code>.</p>
</div>
</div>

### <a name="TextEditorOptionsChangeEvent"></a><span class="code-item" id=201>TextEditorOptionsChangeEvent</span>



<div class="comment"><p>Represents an event describing the change in a <a href="#TextEditor.options">text editor&#39;s options</a>.</p>
</div>

#### Properties



<a name="TextEditorOptionsChangeEvent.options"></a><span class="ts" id=203 data-target="#details-203" data-toggle="collapse"><span class="ident">options</span><span>: </span><a class="type-ref" href="#TextEditorOptions">TextEditorOptions</a></span>
<div class="details collapse" id="details-203">
<div class="comment"><p>The new value for the <a href="#TextEditor.options">text editor&#39;s options</a>.</p>
</div>
</div>



<a name="TextEditorOptionsChangeEvent.textEditor"></a><span class="ts" id=202 data-target="#details-202" data-toggle="collapse"><span class="ident">textEditor</span><span>: </span><a class="type-ref" href="#TextEditor">TextEditor</a></span>
<div class="details collapse" id="details-202">
<div class="comment"><p>The <a href="#TextEditor">text editor</a> for which the options have changed.</p>
</div>
</div>

### <a name="TextEditorRevealType"></a><span class="code-item" id=227>TextEditorRevealType</span>



<div class="comment"><p>Represents different <a href="#TextEditor.revealRange">reveal</a> strategies in a text editor.</p>
</div>

#### Enumeration members



<a name="TextEditorRevealType.AtTop"></a><span class="ts" id=231 data-target="#details-231" data-toggle="collapse"><span class="ident">AtTop</span></span>
<div class="details collapse" id="details-231">
<em>3</em>
</div>



<a name="TextEditorRevealType.Default"></a><span class="ts" id=228 data-target="#details-228" data-toggle="collapse"><span class="ident">Default</span></span>
<div class="details collapse" id="details-228">
<em>0</em>
</div>



<a name="TextEditorRevealType.InCenter"></a><span class="ts" id=229 data-target="#details-229" data-toggle="collapse"><span class="ident">InCenter</span></span>
<div class="details collapse" id="details-229">
<em>1</em>
</div>



<a name="TextEditorRevealType.InCenterIfOutsideViewport"></a><span class="ts" id=230 data-target="#details-230" data-toggle="collapse"><span class="ident">InCenterIfOutsideViewport</span></span>
<div class="details collapse" id="details-230">
<em>2</em>
</div>

### <a name="TextEditorSelectionChangeEvent"></a><span class="code-item" id=194>TextEditorSelectionChangeEvent</span>



<div class="comment"><p>Represents an event describing the change in a <a href="#TextEditor.selections">text editor&#39;s selections</a>.</p>
</div>

#### Properties



<a name="TextEditorSelectionChangeEvent.kind"></a><span class="ts" id=197 data-target="#details-197" data-toggle="collapse"><span class="ident">kind</span><span>?</span><span>: </span><a class="type-ref" href="#TextEditorSelectionChangeKind">TextEditorSelectionChangeKind</a></span>
<div class="details collapse" id="details-197">
<div class="comment"><p>The <a href="#TextEditorSelectionChangeKind">change kind</a> which has triggered this
event. Can be <code>undefined</code>.</p>
</div>
</div>



<a name="TextEditorSelectionChangeEvent.selections"></a><span class="ts" id=196 data-target="#details-196" data-toggle="collapse"><span class="ident">selections</span><span>: </span><a class="type-ref" href="#Selection">Selection</a>[]</span>
<div class="details collapse" id="details-196">
<div class="comment"><p>The new value for the <a href="#TextEditor.selections">text editor&#39;s selections</a>.</p>
</div>
</div>



<a name="TextEditorSelectionChangeEvent.textEditor"></a><span class="ts" id=195 data-target="#details-195" data-toggle="collapse"><span class="ident">textEditor</span><span>: </span><a class="type-ref" href="#TextEditor">TextEditor</a></span>
<div class="details collapse" id="details-195">
<div class="comment"><p>The <a href="#TextEditor">text editor</a> for which the selections have changed.</p>
</div>
</div>

### <a name="TextEditorSelectionChangeKind"></a><span class="code-item" id=190>TextEditorSelectionChangeKind</span>



<div class="comment"><p>Represents sources that can cause <a href="#window.onDidChangeTextEditorSelection">selection change events</a>.</p>
</div>

#### Enumeration members



<a name="TextEditorSelectionChangeKind.Command"></a><span class="ts" id=193 data-target="#details-193" data-toggle="collapse"><span class="ident">Command</span></span>
<div class="details collapse" id="details-193">
<em>3</em>
</div>



<a name="TextEditorSelectionChangeKind.Keyboard"></a><span class="ts" id=191 data-target="#details-191" data-toggle="collapse"><span class="ident">Keyboard</span></span>
<div class="details collapse" id="details-191">
<em>1</em>
</div>



<a name="TextEditorSelectionChangeKind.Mouse"></a><span class="ts" id=192 data-target="#details-192" data-toggle="collapse"><span class="ident">Mouse</span></span>
<div class="details collapse" id="details-192">
<em>2</em>
</div>

### <a name="TextEditorViewColumnChangeEvent"></a><span class="code-item" id=204>TextEditorViewColumnChangeEvent</span>



<div class="comment"><p>Represents an event describing the change of a <a href="#TextEditor.viewColumn">text editor&#39;s view column</a>.</p>
</div>

#### Properties



<a name="TextEditorViewColumnChangeEvent.textEditor"></a><span class="ts" id=205 data-target="#details-205" data-toggle="collapse"><span class="ident">textEditor</span><span>: </span><a class="type-ref" href="#TextEditor">TextEditor</a></span>
<div class="details collapse" id="details-205">
<div class="comment"><p>The <a href="#TextEditor">text editor</a> for which the view column has changed.</p>
</div>
</div>



<a name="TextEditorViewColumnChangeEvent.viewColumn"></a><span class="ts" id=206 data-target="#details-206" data-toggle="collapse"><span class="ident">viewColumn</span><span>: </span><a class="type-ref" href="#ViewColumn">ViewColumn</a></span>
<div class="details collapse" id="details-206">
<div class="comment"><p>The new value for the <a href="#TextEditor.viewColumn">text editor&#39;s view column</a>.</p>
</div>
</div>

### <a name="TextEditorVisibleRangesChangeEvent"></a><span class="code-item" id=198>TextEditorVisibleRangesChangeEvent</span>



<div class="comment"><p>Represents an event describing the change in a <a href="#TextEditor.visibleRanges">text editor&#39;s visible ranges</a>.</p>
</div>

#### Properties



<a name="TextEditorVisibleRangesChangeEvent.textEditor"></a><span class="ts" id=199 data-target="#details-199" data-toggle="collapse"><span class="ident">textEditor</span><span>: </span><a class="type-ref" href="#TextEditor">TextEditor</a></span>
<div class="details collapse" id="details-199">
<div class="comment"><p>The <a href="#TextEditor">text editor</a> for which the visible ranges have changed.</p>
</div>
</div>



<a name="TextEditorVisibleRangesChangeEvent.visibleRanges"></a><span class="ts" id=200 data-target="#details-200" data-toggle="collapse"><span class="ident">visibleRanges</span><span>: </span><a class="type-ref" href="#Range">Range</a>[]</span>
<div class="details collapse" id="details-200">
<div class="comment"><p>The new value for the <a href="#TextEditor.visibleRanges">text editor&#39;s visible ranges</a>.</p>
</div>
</div>

### <a name="TextLine"></a><span class="code-item" id=31>TextLine</span>



<div class="comment"><p>Represents a line of text, such as a line of source code.</p>
<p>TextLine objects are <strong>immutable</strong>. When a <a href="#TextDocument">document</a> changes,
previously retrieved lines will not represent the latest state.</p>
</div>

#### Properties



<a name="TextLine.firstNonWhitespaceCharacterIndex"></a><span class="ts" id=36 data-target="#details-36" data-toggle="collapse"><span class="ident">firstNonWhitespaceCharacterIndex</span><span>: </span><a class="type-intrinsic">number</a></span>
<div class="details collapse" id="details-36">
<div class="comment"><p>The offset of the first character which is not a whitespace character as defined
by <code>/\s/</code>. <strong>Note</strong> that if a line is all whitespaces the length of the line is returned.</p>
</div>
</div>



<a name="TextLine.isEmptyOrWhitespace"></a><span class="ts" id=37 data-target="#details-37" data-toggle="collapse"><span class="ident">isEmptyOrWhitespace</span><span>: </span><a class="type-intrinsic">boolean</a></span>
<div class="details collapse" id="details-37">
<div class="comment"><p>Whether this line is whitespace only, shorthand
for <a href="#TextLine.firstNonWhitespaceCharacterIndex">TextLine.firstNonWhitespaceCharacterIndex</a> === <a href="#TextLine.text">TextLine.text.length</a>.</p>
</div>
</div>



<a name="TextLine.lineNumber"></a><span class="ts" id=32 data-target="#details-32" data-toggle="collapse"><span class="ident">lineNumber</span><span>: </span><a class="type-intrinsic">number</a></span>
<div class="details collapse" id="details-32">
<div class="comment"><p>The zero-based line number.</p>
</div>
</div>



<a name="TextLine.range"></a><span class="ts" id=34 data-target="#details-34" data-toggle="collapse"><span class="ident">range</span><span>: </span><a class="type-ref" href="#Range">Range</a></span>
<div class="details collapse" id="details-34">
<div class="comment"><p>The range this line covers without the line separator characters.</p>
</div>
</div>



<a name="TextLine.rangeIncludingLineBreak"></a><span class="ts" id=35 data-target="#details-35" data-toggle="collapse"><span class="ident">rangeIncludingLineBreak</span><span>: </span><a class="type-ref" href="#Range">Range</a></span>
<div class="details collapse" id="details-35">
<div class="comment"><p>The range this line covers with the line separator characters.</p>
</div>
</div>



<a name="TextLine.text"></a><span class="ts" id=33 data-target="#details-33" data-toggle="collapse"><span class="ident">text</span><span>: </span><a class="type-intrinsic">string</a></span>
<div class="details collapse" id="details-33">
<div class="comment"><p>The text of this line without the line separator characters.</p>
</div>
</div>

### <a name="ThemableDecorationAttachmentRenderOptions"></a><span class="code-item" id=281>ThemableDecorationAttachmentRenderOptions</span>



<div class="comment"></div>

#### Properties



<a name="ThemableDecorationAttachmentRenderOptions.backgroundColor"></a><span class="ts" id=290 data-target="#details-290" data-toggle="collapse"><span class="ident">backgroundColor</span><span>?</span><span>: </span><a class="type-intrinsic">string</a> &#124; <a class="type-ref" href="#ThemeColor">ThemeColor</a></span>
<div class="details collapse" id="details-290">
<div class="comment"><p>CSS styling property that will be applied to the decoration attachment.</p>
</div>
</div>



<a name="ThemableDecorationAttachmentRenderOptions.border"></a><span class="ts" id=284 data-target="#details-284" data-toggle="collapse"><span class="ident">border</span><span>?</span><span>: </span><a class="type-intrinsic">string</a></span>
<div class="details collapse" id="details-284">
<div class="comment"><p>CSS styling property that will be applied to the decoration attachment.</p>
</div>
</div>



<a name="ThemableDecorationAttachmentRenderOptions.borderColor"></a><span class="ts" id=285 data-target="#details-285" data-toggle="collapse"><span class="ident">borderColor</span><span>?</span><span>: </span><a class="type-intrinsic">string</a> &#124; <a class="type-ref" href="#ThemeColor">ThemeColor</a></span>
<div class="details collapse" id="details-285">
<div class="comment"><p>CSS styling property that will be applied to text enclosed by a decoration.</p>
</div>
</div>



<a name="ThemableDecorationAttachmentRenderOptions.color"></a><span class="ts" id=289 data-target="#details-289" data-toggle="collapse"><span class="ident">color</span><span>?</span><span>: </span><a class="type-intrinsic">string</a> &#124; <a class="type-ref" href="#ThemeColor">ThemeColor</a></span>
<div class="details collapse" id="details-289">
<div class="comment"><p>CSS styling property that will be applied to the decoration attachment.</p>
</div>
</div>



<a name="ThemableDecorationAttachmentRenderOptions.contentIconPath"></a><span class="ts" id=283 data-target="#details-283" data-toggle="collapse"><span class="ident">contentIconPath</span><span>?</span><span>: </span><a class="type-intrinsic">string</a> &#124; <a class="type-ref" href="#Uri">Uri</a></span>
<div class="details collapse" id="details-283">
<div class="comment"><p>An <strong>absolute path</strong> or an URI to an image to be rendered in the attachment. Either an icon
or a text can be shown, but not both.</p>
</div>
</div>



<a name="ThemableDecorationAttachmentRenderOptions.contentText"></a><span class="ts" id=282 data-target="#details-282" data-toggle="collapse"><span class="ident">contentText</span><span>?</span><span>: </span><a class="type-intrinsic">string</a></span>
<div class="details collapse" id="details-282">
<div class="comment"><p>Defines a text content that is shown in the attachment. Either an icon or a text can be shown, but not both.</p>
</div>
</div>



<a name="ThemableDecorationAttachmentRenderOptions.fontStyle"></a><span class="ts" id=286 data-target="#details-286" data-toggle="collapse"><span class="ident">fontStyle</span><span>?</span><span>: </span><a class="type-intrinsic">string</a></span>
<div class="details collapse" id="details-286">
<div class="comment"><p>CSS styling property that will be applied to the decoration attachment.</p>
</div>
</div>



<a name="ThemableDecorationAttachmentRenderOptions.fontWeight"></a><span class="ts" id=287 data-target="#details-287" data-toggle="collapse"><span class="ident">fontWeight</span><span>?</span><span>: </span><a class="type-intrinsic">string</a></span>
<div class="details collapse" id="details-287">
<div class="comment"><p>CSS styling property that will be applied to the decoration attachment.</p>
</div>
</div>



<a name="ThemableDecorationAttachmentRenderOptions.height"></a><span class="ts" id=293 data-target="#details-293" data-toggle="collapse"><span class="ident">height</span><span>?</span><span>: </span><a class="type-intrinsic">string</a></span>
<div class="details collapse" id="details-293">
<div class="comment"><p>CSS styling property that will be applied to the decoration attachment.</p>
</div>
</div>



<a name="ThemableDecorationAttachmentRenderOptions.margin"></a><span class="ts" id=291 data-target="#details-291" data-toggle="collapse"><span class="ident">margin</span><span>?</span><span>: </span><a class="type-intrinsic">string</a></span>
<div class="details collapse" id="details-291">
<div class="comment"><p>CSS styling property that will be applied to the decoration attachment.</p>
</div>
</div>



<a name="ThemableDecorationAttachmentRenderOptions.textDecoration"></a><span class="ts" id=288 data-target="#details-288" data-toggle="collapse"><span class="ident">textDecoration</span><span>?</span><span>: </span><a class="type-intrinsic">string</a></span>
<div class="details collapse" id="details-288">
<div class="comment"><p>CSS styling property that will be applied to the decoration attachment.</p>
</div>
</div>



<a name="ThemableDecorationAttachmentRenderOptions.width"></a><span class="ts" id=292 data-target="#details-292" data-toggle="collapse"><span class="ident">width</span><span>?</span><span>: </span><a class="type-intrinsic">string</a></span>
<div class="details collapse" id="details-292">
<div class="comment"><p>CSS styling property that will be applied to the decoration attachment.</p>
</div>
</div>

### <a name="ThemableDecorationInstanceRenderOptions"></a><span class="code-item" id=327>ThemableDecorationInstanceRenderOptions</span>



<div class="comment"></div>

#### Properties



<a name="ThemableDecorationInstanceRenderOptions.after"></a><span class="ts" id=329 data-target="#details-329" data-toggle="collapse"><span class="ident">after</span><span>?</span><span>: </span><a class="type-ref" href="#ThemableDecorationAttachmentRenderOptions">ThemableDecorationAttachmentRenderOptions</a></span>
<div class="details collapse" id="details-329">
<div class="comment"><p>Defines the rendering options of the attachment that is inserted after the decorated text.</p>
</div>
</div>



<a name="ThemableDecorationInstanceRenderOptions.before"></a><span class="ts" id=328 data-target="#details-328" data-toggle="collapse"><span class="ident">before</span><span>?</span><span>: </span><a class="type-ref" href="#ThemableDecorationAttachmentRenderOptions">ThemableDecorationAttachmentRenderOptions</a></span>
<div class="details collapse" id="details-328">
<div class="comment"><p>Defines the rendering options of the attachment that is inserted before the decorated text.</p>
</div>
</div>

### <a name="ThemableDecorationRenderOptions"></a><span class="code-item" id=257>ThemableDecorationRenderOptions</span>



<div class="comment"><p>Represents theme specific rendering styles for a <a href="#TextEditorDecorationType">text editor decoration</a>.</p>
</div>

#### Properties



<a name="ThemableDecorationRenderOptions.after"></a><span class="ts" id=280 data-target="#details-280" data-toggle="collapse"><span class="ident">after</span><span>?</span><span>: </span><a class="type-ref" href="#ThemableDecorationAttachmentRenderOptions">ThemableDecorationAttachmentRenderOptions</a></span>
<div class="details collapse" id="details-280">
<div class="comment"><p>Defines the rendering options of the attachment that is inserted after the decorated text.</p>
</div>
</div>



<a name="ThemableDecorationRenderOptions.backgroundColor"></a><span class="ts" id=258 data-target="#details-258" data-toggle="collapse"><span class="ident">backgroundColor</span><span>?</span><span>: </span><a class="type-intrinsic">string</a> &#124; <a class="type-ref" href="#ThemeColor">ThemeColor</a></span>
<div class="details collapse" id="details-258">
<div class="comment"><p>Background color of the decoration. Use rgba() and define transparent background colors to play well with other decorations.
Alternatively a color from the color registry can be <a href="#ThemeColor">referenced</a>.</p>
</div>
</div>



<a name="ThemableDecorationRenderOptions.before"></a><span class="ts" id=279 data-target="#details-279" data-toggle="collapse"><span class="ident">before</span><span>?</span><span>: </span><a class="type-ref" href="#ThemableDecorationAttachmentRenderOptions">ThemableDecorationAttachmentRenderOptions</a></span>
<div class="details collapse" id="details-279">
<div class="comment"><p>Defines the rendering options of the attachment that is inserted before the decorated text.</p>
</div>
</div>



<a name="ThemableDecorationRenderOptions.border"></a><span class="ts" id=263 data-target="#details-263" data-toggle="collapse"><span class="ident">border</span><span>?</span><span>: </span><a class="type-intrinsic">string</a></span>
<div class="details collapse" id="details-263">
<div class="comment"><p>CSS styling property that will be applied to text enclosed by a decoration.</p>
</div>
</div>



<a name="ThemableDecorationRenderOptions.borderColor"></a><span class="ts" id=264 data-target="#details-264" data-toggle="collapse"><span class="ident">borderColor</span><span>?</span><span>: </span><a class="type-intrinsic">string</a> &#124; <a class="type-ref" href="#ThemeColor">ThemeColor</a></span>
<div class="details collapse" id="details-264">
<div class="comment"><p>CSS styling property that will be applied to text enclosed by a decoration.
Better use &#39;border&#39; for setting one or more of the individual border properties.</p>
</div>
</div>



<a name="ThemableDecorationRenderOptions.borderRadius"></a><span class="ts" id=265 data-target="#details-265" data-toggle="collapse"><span class="ident">borderRadius</span><span>?</span><span>: </span><a class="type-intrinsic">string</a></span>
<div class="details collapse" id="details-265">
<div class="comment"><p>CSS styling property that will be applied to text enclosed by a decoration.
Better use &#39;border&#39; for setting one or more of the individual border properties.</p>
</div>
</div>



<a name="ThemableDecorationRenderOptions.borderSpacing"></a><span class="ts" id=266 data-target="#details-266" data-toggle="collapse"><span class="ident">borderSpacing</span><span>?</span><span>: </span><a class="type-intrinsic">string</a></span>
<div class="details collapse" id="details-266">
<div class="comment"><p>CSS styling property that will be applied to text enclosed by a decoration.
Better use &#39;border&#39; for setting one or more of the individual border properties.</p>
</div>
</div>



<a name="ThemableDecorationRenderOptions.borderStyle"></a><span class="ts" id=267 data-target="#details-267" data-toggle="collapse"><span class="ident">borderStyle</span><span>?</span><span>: </span><a class="type-intrinsic">string</a></span>
<div class="details collapse" id="details-267">
<div class="comment"><p>CSS styling property that will be applied to text enclosed by a decoration.
Better use &#39;border&#39; for setting one or more of the individual border properties.</p>
</div>
</div>



<a name="ThemableDecorationRenderOptions.borderWidth"></a><span class="ts" id=268 data-target="#details-268" data-toggle="collapse"><span class="ident">borderWidth</span><span>?</span><span>: </span><a class="type-intrinsic">string</a></span>
<div class="details collapse" id="details-268">
<div class="comment"><p>CSS styling property that will be applied to text enclosed by a decoration.
Better use &#39;border&#39; for setting one or more of the individual border properties.</p>
</div>
</div>



<a name="ThemableDecorationRenderOptions.color"></a><span class="ts" id=273 data-target="#details-273" data-toggle="collapse"><span class="ident">color</span><span>?</span><span>: </span><a class="type-intrinsic">string</a> &#124; <a class="type-ref" href="#ThemeColor">ThemeColor</a></span>
<div class="details collapse" id="details-273">
<div class="comment"><p>CSS styling property that will be applied to text enclosed by a decoration.</p>
</div>
</div>



<a name="ThemableDecorationRenderOptions.cursor"></a><span class="ts" id=272 data-target="#details-272" data-toggle="collapse"><span class="ident">cursor</span><span>?</span><span>: </span><a class="type-intrinsic">string</a></span>
<div class="details collapse" id="details-272">
<div class="comment"><p>CSS styling property that will be applied to text enclosed by a decoration.</p>
</div>
</div>



<a name="ThemableDecorationRenderOptions.fontStyle"></a><span class="ts" id=269 data-target="#details-269" data-toggle="collapse"><span class="ident">fontStyle</span><span>?</span><span>: </span><a class="type-intrinsic">string</a></span>
<div class="details collapse" id="details-269">
<div class="comment"><p>CSS styling property that will be applied to text enclosed by a decoration.</p>
</div>
</div>



<a name="ThemableDecorationRenderOptions.fontWeight"></a><span class="ts" id=270 data-target="#details-270" data-toggle="collapse"><span class="ident">fontWeight</span><span>?</span><span>: </span><a class="type-intrinsic">string</a></span>
<div class="details collapse" id="details-270">
<div class="comment"><p>CSS styling property that will be applied to text enclosed by a decoration.</p>
</div>
</div>



<a name="ThemableDecorationRenderOptions.gutterIconPath"></a><span class="ts" id=276 data-target="#details-276" data-toggle="collapse"><span class="ident">gutterIconPath</span><span>?</span><span>: </span><a class="type-intrinsic">string</a> &#124; <a class="type-ref" href="#Uri">Uri</a></span>
<div class="details collapse" id="details-276">
<div class="comment"><p>An <strong>absolute path</strong> or an URI to an image to be rendered in the gutter.</p>
</div>
</div>



<a name="ThemableDecorationRenderOptions.gutterIconSize"></a><span class="ts" id=277 data-target="#details-277" data-toggle="collapse"><span class="ident">gutterIconSize</span><span>?</span><span>: </span><a class="type-intrinsic">string</a></span>
<div class="details collapse" id="details-277">
<div class="comment"><p>Specifies the size of the gutter icon.
Available values are &#39;auto&#39;, &#39;contain&#39;, &#39;cover&#39; and any percentage value.
For further information: <a href="https://msdn.microsoft.com/en-us/library/jj127316(v=vs.85).aspx">https://msdn.microsoft.com/en-us/library/jj127316(v=vs.85).aspx</a></p>
</div>
</div>



<a name="ThemableDecorationRenderOptions.letterSpacing"></a><span class="ts" id=275 data-target="#details-275" data-toggle="collapse"><span class="ident">letterSpacing</span><span>?</span><span>: </span><a class="type-intrinsic">string</a></span>
<div class="details collapse" id="details-275">
<div class="comment"><p>CSS styling property that will be applied to text enclosed by a decoration.</p>
</div>
</div>



<a name="ThemableDecorationRenderOptions.opacity"></a><span class="ts" id=274 data-target="#details-274" data-toggle="collapse"><span class="ident">opacity</span><span>?</span><span>: </span><a class="type-intrinsic">string</a></span>
<div class="details collapse" id="details-274">
<div class="comment"><p>CSS styling property that will be applied to text enclosed by a decoration.</p>
</div>
</div>



<a name="ThemableDecorationRenderOptions.outline"></a><span class="ts" id=259 data-target="#details-259" data-toggle="collapse"><span class="ident">outline</span><span>?</span><span>: </span><a class="type-intrinsic">string</a></span>
<div class="details collapse" id="details-259">
<div class="comment"><p>CSS styling property that will be applied to text enclosed by a decoration.</p>
</div>
</div>



<a name="ThemableDecorationRenderOptions.outlineColor"></a><span class="ts" id=260 data-target="#details-260" data-toggle="collapse"><span class="ident">outlineColor</span><span>?</span><span>: </span><a class="type-intrinsic">string</a> &#124; <a class="type-ref" href="#ThemeColor">ThemeColor</a></span>
<div class="details collapse" id="details-260">
<div class="comment"><p>CSS styling property that will be applied to text enclosed by a decoration.
Better use &#39;outline&#39; for setting one or more of the individual outline properties.</p>
</div>
</div>



<a name="ThemableDecorationRenderOptions.outlineStyle"></a><span class="ts" id=261 data-target="#details-261" data-toggle="collapse"><span class="ident">outlineStyle</span><span>?</span><span>: </span><a class="type-intrinsic">string</a></span>
<div class="details collapse" id="details-261">
<div class="comment"><p>CSS styling property that will be applied to text enclosed by a decoration.
Better use &#39;outline&#39; for setting one or more of the individual outline properties.</p>
</div>
</div>



<a name="ThemableDecorationRenderOptions.outlineWidth"></a><span class="ts" id=262 data-target="#details-262" data-toggle="collapse"><span class="ident">outlineWidth</span><span>?</span><span>: </span><a class="type-intrinsic">string</a></span>
<div class="details collapse" id="details-262">
<div class="comment"><p>CSS styling property that will be applied to text enclosed by a decoration.
Better use &#39;outline&#39; for setting one or more of the individual outline properties.</p>
</div>
</div>



<a name="ThemableDecorationRenderOptions.overviewRulerColor"></a><span class="ts" id=278 data-target="#details-278" data-toggle="collapse"><span class="ident">overviewRulerColor</span><span>?</span><span>: </span><a class="type-intrinsic">string</a> &#124; <a class="type-ref" href="#ThemeColor">ThemeColor</a></span>
<div class="details collapse" id="details-278">
<div class="comment"><p>The color of the decoration in the overview ruler. Use rgba() and define transparent colors to play well with other decorations.</p>
</div>
</div>



<a name="ThemableDecorationRenderOptions.textDecoration"></a><span class="ts" id=271 data-target="#details-271" data-toggle="collapse"><span class="ident">textDecoration</span><span>?</span><span>: </span><a class="type-intrinsic">string</a></span>
<div class="details collapse" id="details-271">
<div class="comment"><p>CSS styling property that will be applied to text enclosed by a decoration.</p>
</div>
</div>

### <a name="ThemeColor"></a><span class="code-item" id=247>ThemeColor</span>



<div class="comment"><p>A reference to one of the workbench colors as defined in <a href="https://code.visualstudio.com/docs/getstarted/theme-color-reference">https://code.visualstudio.com/docs/getstarted/theme-color-reference</a>.
Using a theme color is preferred over a custom color as it gives theme authors and users the possibility to change the color.</p>
</div>

#### Constructors



<a name="ThemeColor.new ThemeColor"></a><span class="ts" id=249 data-target="#details-249" data-toggle="collapse"><span class="ident">new ThemeColor</span><span>(</span><span class="ident">id</span><span>: </span><a class="type-intrinsic">string</a><span>)</span><span>: </span><a class="type-ref" href="#ThemeColor">ThemeColor</a></span>
<div class="details collapse" id="details-249">
<div class="comment"><p>Creates a reference to a theme color.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="id"></a><span class="ts" id=250 data-target="#details-250" data-toggle="collapse"><span class="ident">id</span><span>: </span><a class="type-intrinsic">string</a></span></td><td><div class="comment"><p>of the color. The available colors are listed in <a href="https://code.visualstudio.com/docs/getstarted/theme-color-reference">https://code.visualstudio.com/docs/getstarted/theme-color-reference</a>.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#ThemeColor">ThemeColor</a></span></td><td><div class="comment"></div></td></tr>
</table>
</div>
</div>

### <a name="ThemeIcon"></a><span class="code-item" id=251>ThemeIcon</span>



<div class="comment"><p>A reference to a named icon. Currently only <a href="#ThemeIcon.File">File</a> and <a href="#ThemeIcon.Folder">Folder</a> are supported.
Using a theme icon is preferred over a custom icon as it gives theme authors the possibility to change the icons.</p>
</div>

#### Static



<a name="ThemeIcon.File"></a><span class="ts" id=252 data-target="#details-252" data-toggle="collapse"><span class="ident">File</span><span>: </span><a class="type-ref" href="#ThemeIcon">ThemeIcon</a></span>
<div class="details collapse" id="details-252">
<div class="comment"><p>Reference to a icon representing a file. The icon is taken from the current file icon theme or a placeholder icon.</p>
</div>
</div>



<a name="ThemeIcon.Folder"></a><span class="ts" id=253 data-target="#details-253" data-toggle="collapse"><span class="ident">Folder</span><span>: </span><a class="type-ref" href="#ThemeIcon">ThemeIcon</a></span>
<div class="details collapse" id="details-253">
<div class="comment"><p>Reference to a icon representing a folder. The icon is taken from the current file icon theme or a placeholder icon.</p>
</div>
</div>

#### Constructors



<a name="ThemeIcon.new ThemeIcon"></a><span class="ts" id=255 data-target="#details-255" data-toggle="collapse"><span class="ident">new ThemeIcon</span><span>(</span><span class="ident">id</span><span>: </span><a class="type-intrinsic">string</a><span>)</span><span>: </span><a class="type-ref" href="#ThemeIcon">ThemeIcon</a></span>
<div class="details collapse" id="details-255">
<div class="comment"></div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="id"></a><span class="ts" id=256 data-target="#details-256" data-toggle="collapse"><span class="ident">id</span><span>: </span><a class="type-intrinsic">string</a></span></td><td><div class="comment"></div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#ThemeIcon">ThemeIcon</a></span></td><td><div class="comment"></div></td></tr>
</table>
</div>
</div>

### <a name="TreeDataProvider"></a><span class="code-item" id=1652>TreeDataProvider&lt;T&gt;</span>



<div class="comment"><p>A data provider that provides tree data</p>
</div>

#### Events



<a name="TreeDataProvider.onDidChangeTreeData"></a><span class="ts" id=1654 data-target="#details-1654" data-toggle="collapse"><span class="ident">onDidChangeTreeData</span><span>?</span><span>: </span><a class="type-ref" href="#Event">Event</a>&lt;<a class="type-intrinsic">T</a> &#124; <a class="type-intrinsic">undefined</a> &#124; <a class="type-intrinsic">null</a>&gt;</span>
<div class="details collapse" id="details-1654">
<div class="comment"><p>An optional event to signal that an element or root has changed.
This will trigger the view to update the changed element/root and its children recursively (if shown).
To signal that root has changed, do not pass any argument or pass <code>undefined</code> or <code>null</code>.</p>
</div>
</div>

#### Methods



<a name="TreeDataProvider.getChildren"></a><span class="ts" id=1659 data-target="#details-1659" data-toggle="collapse"><span class="ident">getChildren</span><span>(</span><span class="ident">element</span><span>?</span><span>: </span><a class="type-intrinsic">T</a><span>)</span><span>: </span><a class="type-ref" href="#ProviderResult">ProviderResult</a>&lt;<a class="type-intrinsic">T</a>[]&gt;</span>
<div class="details collapse" id="details-1659">
<div class="comment"><p>Get the children of <code>element</code> or root if no element is passed.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="element"></a><span class="ts" id=1660 data-target="#details-1660" data-toggle="collapse"><span class="ident">element</span><span>?</span><span>: </span><a class="type-intrinsic">T</a></span></td><td><div class="comment"><p>The element from which the provider gets children. Can be <code>undefined</code>.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#ProviderResult">ProviderResult</a>&lt;<a class="type-intrinsic">T</a>[]&gt;</span></td><td><div class="comment"><p>Children of <code>element</code> or root if no element is passed.</p>
</div></td></tr>
</table>
</div>
</div>



<a name="TreeDataProvider.getParent"></a><span class="ts" id=1662 data-target="#details-1662" data-toggle="collapse"><span class="ident">getParent</span><span>(</span><span class="ident">element</span><span>: </span><a class="type-intrinsic">T</a><span>)</span><span>: </span><a class="type-ref" href="#ProviderResult">ProviderResult</a>&lt;<a class="type-intrinsic">T</a>&gt;</span>
<div class="details collapse" id="details-1662">
<div class="comment"><p>Optional method to return the parent of <code>element</code>.
Return <code>null</code> or <code>undefined</code> if <code>element</code> is a child of root.</p>
<p><strong>NOTE:</strong> This method should be implemented in order to access <a href="#TreeView.reveal">reveal</a> API.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="element"></a><span class="ts" id=1663 data-target="#details-1663" data-toggle="collapse"><span class="ident">element</span><span>: </span><a class="type-intrinsic">T</a></span></td><td><div class="comment"><p>The element for which the parent has to be returned.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#ProviderResult">ProviderResult</a>&lt;<a class="type-intrinsic">T</a>&gt;</span></td><td><div class="comment"><p>Parent of <code>element</code>.</p>
</div></td></tr>
</table>
</div>
</div>



<a name="TreeDataProvider.getTreeItem"></a><span class="ts" id=1656 data-target="#details-1656" data-toggle="collapse"><span class="ident">getTreeItem</span><span>(</span><span class="ident">element</span><span>: </span><a class="type-intrinsic">T</a><span>)</span><span>: </span><a class="type-ref" href="#TreeItem">TreeItem</a> &#124; <a class="type-ref" href="#Thenable">Thenable</a>&lt;<a class="type-ref" href="#TreeItem">TreeItem</a>&gt;</span>
<div class="details collapse" id="details-1656">
<div class="comment"><p>Get <a href="#TreeItem">TreeItem</a> representation of the <code>element</code></p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="element"></a><span class="ts" id=1657 data-target="#details-1657" data-toggle="collapse"><span class="ident">element</span><span>: </span><a class="type-intrinsic">T</a></span></td><td><div class="comment"><p>The element for which <a href="#TreeItem">TreeItem</a> representation is asked for.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#TreeItem">TreeItem</a> &#124; <a class="type-ref" href="#Thenable">Thenable</a>&lt;<a class="type-ref" href="#TreeItem">TreeItem</a>&gt;</span></td><td><div class="comment"><p>(#TreeItem) representation of the element</p>
</div></td></tr>
</table>
</div>
</div>

### <a name="TreeItem"></a><span class="code-item" id=1664>TreeItem</span>



<div class="comment"></div>

#### Constructors



<a name="TreeItem.new TreeItem"></a><span class="ts" id=1678 data-target="#details-1678" data-toggle="collapse"><span class="ident">new TreeItem</span><span>(</span><span class="ident">label</span><span>: </span><a class="type-intrinsic">string</a>, <span class="ident">collapsibleState</span><span>?</span><span>: </span><a class="type-ref" href="#TreeItemCollapsibleState">TreeItemCollapsibleState</a><span>)</span><span>: </span><a class="type-ref" href="#TreeItem">TreeItem</a></span>
<div class="details collapse" id="details-1678">
<div class="comment"></div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="label"></a><span class="ts" id=1679 data-target="#details-1679" data-toggle="collapse"><span class="ident">label</span><span>: </span><a class="type-intrinsic">string</a></span></td><td><div class="comment"><p>A human-readable string describing this item</p>
</div></td></tr>
<tr><td><a name="collapsibleState"></a><span class="ts" id=1680 data-target="#details-1680" data-toggle="collapse"><span class="ident">collapsibleState</span><span>?</span><span>: </span><a class="type-ref" href="#TreeItemCollapsibleState">TreeItemCollapsibleState</a></span></td><td><div class="comment"><p>(#TreeItemCollapsibleState) of the tree item. Default is <a href="#TreeItemCollapsibleState.None">TreeItemCollapsibleState.None</a></p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#TreeItem">TreeItem</a></span></td><td><div class="comment"></div></td></tr>
</table>
</div>
</div>



<a name="TreeItem.new TreeItem"></a><span class="ts" id=1681 data-target="#details-1681" data-toggle="collapse"><span class="ident">new TreeItem</span><span>(</span><span class="ident">resourceUri</span><span>: </span><a class="type-ref" href="#Uri">Uri</a>, <span class="ident">collapsibleState</span><span>?</span><span>: </span><a class="type-ref" href="#TreeItemCollapsibleState">TreeItemCollapsibleState</a><span>)</span><span>: </span><a class="type-ref" href="#TreeItem">TreeItem</a></span>
<div class="details collapse" id="details-1681">
<div class="comment"></div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="resourceUri"></a><span class="ts" id=1682 data-target="#details-1682" data-toggle="collapse"><span class="ident">resourceUri</span><span>: </span><a class="type-ref" href="#Uri">Uri</a></span></td><td><div class="comment"><p>The <a href="#Uri">uri</a> of the resource representing this item.</p>
</div></td></tr>
<tr><td><a name="collapsibleState"></a><span class="ts" id=1683 data-target="#details-1683" data-toggle="collapse"><span class="ident">collapsibleState</span><span>?</span><span>: </span><a class="type-ref" href="#TreeItemCollapsibleState">TreeItemCollapsibleState</a></span></td><td><div class="comment"><p>(#TreeItemCollapsibleState) of the tree item. Default is <a href="#TreeItemCollapsibleState.None">TreeItemCollapsibleState.None</a></p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#TreeItem">TreeItem</a></span></td><td><div class="comment"></div></td></tr>
</table>
</div>
</div>

#### Properties



<a name="TreeItem.collapsibleState"></a><span class="ts" id=1675 data-target="#details-1675" data-toggle="collapse"><span class="ident">collapsibleState</span><span>?</span><span>: </span><a class="type-ref" href="#TreeItemCollapsibleState">TreeItemCollapsibleState</a></span>
<div class="details collapse" id="details-1675">
<div class="comment"><p><a href="#TreeItemCollapsibleState">TreeItemCollapsibleState</a> of the tree item.</p>
</div>
</div>



<a name="TreeItem.command"></a><span class="ts" id=1674 data-target="#details-1674" data-toggle="collapse"><span class="ident">command</span><span>?</span><span>: </span><a class="type-ref" href="#Command">Command</a></span>
<div class="details collapse" id="details-1674">
<div class="comment"><p>The <a href="#Command">command</a> that should be executed when the tree item is selected.</p>
</div>
</div>



<a name="TreeItem.contextValue"></a><span class="ts" id=1676 data-target="#details-1676" data-toggle="collapse"><span class="ident">contextValue</span><span>?</span><span>: </span><a class="type-intrinsic">string</a></span>
<div class="details collapse" id="details-1676">
<div class="comment"><p>Context value of the tree item. This can be used to contribute item specific actions in the tree.
For example, a tree item is given a context value as <code>folder</code>. When contributing actions to <code>view/item/context</code>
using <code>menus</code> extension point, you can specify context value for key <code>viewItem</code> in <code>when</code> expression like <code>viewItem == folder</code>.</p>

<pre><code>    &quot;contributes&quot;: {
        &quot;menus&quot;: {
            &quot;view/item/context&quot;: [
                {
                    &quot;command&quot;: &quot;extension.deleteFolder&quot;,
                    &quot;when&quot;: &quot;viewItem == folder&quot;
                }
            ]
        }
    }
</code></pre><p>This will show action <code>extension.deleteFolder</code> only for items with <code>contextValue</code> is <code>folder</code>.</p>
</div>
</div>



<a name="TreeItem.description"></a><span class="ts" id=1671 data-target="#details-1671" data-toggle="collapse"><span class="ident">description</span><span>?</span><span>: </span><a class="type-intrinsic">string</a> &#124; <a class="type-intrinsic">boolean</a></span>
<div class="details collapse" id="details-1671">
<div class="comment"><p>A human readable string which is rendered less prominent.
When <code>true</code>, it is derived from <a href="#TreeItem.resourceUri">resourceUri</a> and when <code>falsy</code>, it is not shown.</p>
</div>
</div>



<a name="TreeItem.iconPath"></a><span class="ts" id=1667 data-target="#details-1667" data-toggle="collapse"><span class="ident">iconPath</span><span>?</span><span>: </span><a class="type-intrinsic">string</a> &#124; <a class="type-ref" href="#Uri">Uri</a> &#124; {dark: <a class="type-intrinsic">string</a> &#124; <a class="type-ref" href="#Uri">Uri</a>, light: <a class="type-intrinsic">string</a> &#124; <a class="type-ref" href="#Uri">Uri</a>} &#124; <a class="type-ref" href="#ThemeIcon">ThemeIcon</a></span>
<div class="details collapse" id="details-1667">
<div class="comment"><p>The icon path or <a href="#ThemeIcon">ThemeIcon</a> for the tree item.
When <code>falsy</code>, <a href="#ThemeIcon.Folder">Folder Theme Icon</a> is assigned, if item is collapsible otherwise <a href="#ThemeIcon.File">File Theme Icon</a>.
When a <a href="#ThemeIcon">ThemeIcon</a> is specified, icon is derived from the current file icon theme for the specified theme icon using <a href="#TreeItem.resourceUri">resourceUri</a> (if provided).</p>
</div>
</div>



<a name="TreeItem.id"></a><span class="ts" id=1666 data-target="#details-1666" data-toggle="collapse"><span class="ident">id</span><span>?</span><span>: </span><a class="type-intrinsic">string</a></span>
<div class="details collapse" id="details-1666">
<div class="comment"><p>Optional id for the tree item that has to be unique across tree. The id is used to preserve the selection and expansion state of the tree item.</p>
<p>If not provided, an id is generated using the tree item&#39;s label. <strong>Note</strong> that when labels change, ids will change and that selection and expansion state cannot be kept stable anymore.</p>
</div>
</div>



<a name="TreeItem.label"></a><span class="ts" id=1665 data-target="#details-1665" data-toggle="collapse"><span class="ident">label</span><span>?</span><span>: </span><a class="type-intrinsic">string</a></span>
<div class="details collapse" id="details-1665">
<div class="comment"><p>A human-readable string describing this item. When <code>falsy</code>, it is derived from <a href="#TreeItem.resourceUri">resourceUri</a>.</p>
</div>
</div>



<a name="TreeItem.resourceUri"></a><span class="ts" id=1672 data-target="#details-1672" data-toggle="collapse"><span class="ident">resourceUri</span><span>?</span><span>: </span><a class="type-ref" href="#Uri">Uri</a></span>
<div class="details collapse" id="details-1672">
<div class="comment"><p>The <a href="#Uri">uri</a> of the resource representing this item.</p>
<p>Will be used to derive the <a href="#TreeItem.label">label</a>, when it is not provided.
Will be used to derive the icon from current icon theme, when <a href="#TreeItem.iconPath">iconPath</a> has <a href="#ThemeIcon">ThemeIcon</a> value.</p>
</div>
</div>



<a name="TreeItem.tooltip"></a><span class="ts" id=1673 data-target="#details-1673" data-toggle="collapse"><span class="ident">tooltip</span><span>?</span><span>: </span><a class="type-intrinsic">string</a> &#124; <a class="type-intrinsic">undefined</a></span>
<div class="details collapse" id="details-1673">
<div class="comment"><p>The tooltip text when you hover over this item.</p>
</div>
</div>

### <a name="TreeItemCollapsibleState"></a><span class="code-item" id=1684>TreeItemCollapsibleState</span>



<div class="comment"><p>Collapsible state of the tree item</p>
</div>

#### Enumeration members



<a name="TreeItemCollapsibleState.Collapsed"></a><span class="ts" id=1686 data-target="#details-1686" data-toggle="collapse"><span class="ident">Collapsed</span></span>
<div class="details collapse" id="details-1686">
<em>1</em>
</div>



<a name="TreeItemCollapsibleState.Expanded"></a><span class="ts" id=1687 data-target="#details-1687" data-toggle="collapse"><span class="ident">Expanded</span></span>
<div class="details collapse" id="details-1687">
<em>2</em>
</div>



<a name="TreeItemCollapsibleState.None"></a><span class="ts" id=1685 data-target="#details-1685" data-toggle="collapse"><span class="ident">None</span></span>
<div class="details collapse" id="details-1685">
<em>0</em>
</div>

### <a name="TreeView"></a><span class="code-item" id=1624>TreeView&lt;T&gt;</span>



<div class="comment"><p>Represents a Tree view</p>
</div>

#### Events



<a name="TreeView.onDidChangeSelection"></a><span class="ts" id=1629 data-target="#details-1629" data-toggle="collapse"><span class="ident">onDidChangeSelection</span><span>: </span><a class="type-ref" href="#Event">Event</a>&lt;<a class="type-ref" href="#TreeViewSelectionChangeEvent">TreeViewSelectionChangeEvent</a>&lt;<a class="type-intrinsic">T</a>&gt;&gt;</span>
<div class="details collapse" id="details-1629">
<div class="comment"><p>Event that is fired when the <a href="#TreeView.selection">selection</a> has changed</p>
</div>
</div>



<a name="TreeView.onDidChangeVisibility"></a><span class="ts" id=1631 data-target="#details-1631" data-toggle="collapse"><span class="ident">onDidChangeVisibility</span><span>: </span><a class="type-ref" href="#Event">Event</a>&lt;<a class="type-ref" href="#TreeViewVisibilityChangeEvent">TreeViewVisibilityChangeEvent</a>&gt;</span>
<div class="details collapse" id="details-1631">
<div class="comment"><p>Event that is fired when <a href="#TreeView.visible">visibility</a> has changed</p>
</div>
</div>



<a name="TreeView.onDidCollapseElement"></a><span class="ts" id=1627 data-target="#details-1627" data-toggle="collapse"><span class="ident">onDidCollapseElement</span><span>: </span><a class="type-ref" href="#Event">Event</a>&lt;<a class="type-ref" href="#TreeViewExpansionEvent">TreeViewExpansionEvent</a>&lt;<a class="type-intrinsic">T</a>&gt;&gt;</span>
<div class="details collapse" id="details-1627">
<div class="comment"><p>Event that is fired when an element is collapsed</p>
</div>
</div>



<a name="TreeView.onDidExpandElement"></a><span class="ts" id=1626 data-target="#details-1626" data-toggle="collapse"><span class="ident">onDidExpandElement</span><span>: </span><a class="type-ref" href="#Event">Event</a>&lt;<a class="type-ref" href="#TreeViewExpansionEvent">TreeViewExpansionEvent</a>&lt;<a class="type-intrinsic">T</a>&gt;&gt;</span>
<div class="details collapse" id="details-1626">
<div class="comment"><p>Event that is fired when an element is expanded</p>
</div>
</div>

#### Static



<a name="TreeView.from"></a><span class="ts" id=1641 data-target="#details-1641" data-toggle="collapse"><span class="ident">from</span><span>(</span><span>...</span><span class="ident">disposableLikes</span><span>: </span>{dispose: () =&gt; <a class="type-intrinsic">any</a>}[]<span>)</span><span>: </span><a class="type-ref" href="#Disposable">Disposable</a></span>
<div class="details collapse" id="details-1641">
<div class="comment"><p>Combine many disposable-likes into one. Use this method
when having objects with a dispose function which are not
instances of Disposable.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="disposableLikes"></a><span class="ts" id=1642 data-target="#details-1642" data-toggle="collapse"><span>...</span><span class="ident">disposableLikes</span><span>: </span>{dispose: () =&gt; <a class="type-intrinsic">any</a>}[]</span></td><td><div class="comment"><p>Objects that have at least a <code>dispose</code>-function member.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#Disposable">Disposable</a></span></td><td><div class="comment"><p>Returns a new disposable which, upon dispose, will
dispose all provided disposables.</p>
</div></td></tr>
</table>
</div>
</div>

#### Constructors



<a name="TreeView.new TreeView"></a><span class="ts" id=1648 data-target="#details-1648" data-toggle="collapse"><span class="ident">new TreeView</span><span>(</span><span class="ident">callOnDispose</span><span>: </span><a class="type-ref" href="#Function">Function</a><span>)</span><span>: </span><a class="type-ref" href="#TreeView">TreeView</a></span>
<div class="details collapse" id="details-1648">
<div class="comment"><p>Creates a new Disposable calling the provided function
on dispose.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="callOnDispose"></a><span class="ts" id=1649 data-target="#details-1649" data-toggle="collapse"><span class="ident">callOnDispose</span><span>: </span><a class="type-ref" href="#Function">Function</a></span></td><td><div class="comment"><p>Function that disposes something.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#TreeView">TreeView</a></span></td><td><div class="comment"></div></td></tr>
</table>
</div>
</div>

#### Properties



<a name="TreeView.selection"></a><span class="ts" id=1628 data-target="#details-1628" data-toggle="collapse"><span class="ident">selection</span><span>: </span><a class="type-intrinsic">T</a>[]</span>
<div class="details collapse" id="details-1628">
<div class="comment"><p>Currently selected elements.</p>
</div>
</div>



<a name="TreeView.visible"></a><span class="ts" id=1630 data-target="#details-1630" data-toggle="collapse"><span class="ident">visible</span><span>: </span><a class="type-intrinsic">boolean</a></span>
<div class="details collapse" id="details-1630">
<div class="comment"><p><code>true</code> if the <a href="#TreeView">tree view</a> is visible otherwise <code>false</code>.</p>
</div>
</div>

#### Methods



<a name="TreeView.dispose"></a><span class="ts" id=1651 data-target="#details-1651" data-toggle="collapse"><span class="ident">dispose</span><span>(</span><span>)</span><span>: </span><a class="type-intrinsic">any</a></span>
<div class="details collapse" id="details-1651">
<div class="comment"><p>Dispose this object.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-intrinsic">any</a></span></td><td><div class="comment"></div></td></tr>
</table>
</div>
</div>



<a name="TreeView.reveal"></a><span class="ts" id=1633 data-target="#details-1633" data-toggle="collapse"><span class="ident">reveal</span><span>(</span><span class="ident">element</span><span>: </span><a class="type-intrinsic">T</a>, <span class="ident">options</span><span>?</span><span>: </span>{expand: <a class="type-intrinsic">boolean</a> &#124; <a class="type-intrinsic">number</a>, focus: <a class="type-intrinsic">boolean</a>, select: <a class="type-intrinsic">boolean</a>}<span>)</span><span>: </span><a class="type-ref" href="#Thenable">Thenable</a>&lt;<a class="type-intrinsic">void</a>&gt;</span>
<div class="details collapse" id="details-1633">
<div class="comment"><p>Reveals the given element in the tree view.
If the tree view is not visible then the tree view is shown and element is revealed.</p>
<p>By default revealed element is selected.
In order to not to select, set the option <code>select</code> to <code>false</code>.
In order to focus, set the option <code>focus</code> to <code>true</code>.
In order to expand the revealed element, set the option <code>expand</code> to <code>true</code>. To expand recursively set <code>expand</code> to the number of levels to expand.
<strong>NOTE:</strong> You can expand only to 3 levels maximum.</p>
<p><strong>NOTE:</strong> <a href="#TreeDataProvider">TreeDataProvider</a> is required to implement <a href="#TreeDataProvider.getParent">getParent</a> method to access this API.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="element"></a><span class="ts" id=1634 data-target="#details-1634" data-toggle="collapse"><span class="ident">element</span><span>: </span><a class="type-intrinsic">T</a></span></td><td><div class="comment"></div></td></tr>
<tr><td><a name="options"></a><span class="ts" id=1635 data-target="#details-1635" data-toggle="collapse"><span class="ident">options</span><span>?</span><span>: </span>{expand: <a class="type-intrinsic">boolean</a> &#124; <a class="type-intrinsic">number</a>, focus: <a class="type-intrinsic">boolean</a>, select: <a class="type-intrinsic">boolean</a>}</span></td><td><div class="comment"></div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#Thenable">Thenable</a>&lt;<a class="type-intrinsic">void</a>&gt;</span></td><td><div class="comment"></div></td></tr>
</table>
</div>
</div>

### <a name="TreeViewExpansionEvent"></a><span class="code-item" id=1616>TreeViewExpansionEvent&lt;T&gt;</span>



<div class="comment"><p>The event that is fired when an element in the <a href="#TreeView">TreeView</a> is expanded or collapsed</p>
</div>

#### Properties



<a name="TreeViewExpansionEvent.element"></a><span class="ts" id=1618 data-target="#details-1618" data-toggle="collapse"><span class="ident">element</span><span>: </span><a class="type-intrinsic">T</a></span>
<div class="details collapse" id="details-1618">
<div class="comment"><p>Element that is expanded or collapsed.</p>
</div>
</div>

### <a name="TreeViewOptions"></a><span class="code-item" id=1612>TreeViewOptions&lt;T&gt;</span>



<div class="comment"><p>Options for creating a <a href="#TreeView">TreeView</a></p>
</div>

#### Properties



<a name="TreeViewOptions.showCollapseAll"></a><span class="ts" id=1615 data-target="#details-1615" data-toggle="collapse"><span class="ident">showCollapseAll</span><span>?</span><span>: </span><a class="type-intrinsic">boolean</a></span>
<div class="details collapse" id="details-1615">
<div class="comment"><p>Whether to show collapse all action or not.</p>
</div>
</div>



<a name="TreeViewOptions.treeDataProvider"></a><span class="ts" id=1614 data-target="#details-1614" data-toggle="collapse"><span class="ident">treeDataProvider</span><span>: </span><a class="type-ref" href="#TreeDataProvider">TreeDataProvider</a>&lt;<a class="type-intrinsic">T</a>&gt;</span>
<div class="details collapse" id="details-1614">
<div class="comment"><p>A data provider that provides tree data.</p>
</div>
</div>

### <a name="TreeViewSelectionChangeEvent"></a><span class="code-item" id=1619>TreeViewSelectionChangeEvent&lt;T&gt;</span>



<div class="comment"><p>The event that is fired when there is a change in <a href="#TreeView.selection">tree view&#39;s selection</a></p>
</div>

#### Properties



<a name="TreeViewSelectionChangeEvent.selection"></a><span class="ts" id=1621 data-target="#details-1621" data-toggle="collapse"><span class="ident">selection</span><span>: </span><a class="type-intrinsic">T</a>[]</span>
<div class="details collapse" id="details-1621">
<div class="comment"><p>Selected elements.</p>
</div>
</div>

### <a name="TreeViewVisibilityChangeEvent"></a><span class="code-item" id=1622>TreeViewVisibilityChangeEvent</span>



<div class="comment"><p>The event that is fired when there is a change in <a href="#TreeView.visible">tree view&#39;s visibility</a></p>
</div>

#### Properties



<a name="TreeViewVisibilityChangeEvent.visible"></a><span class="ts" id=1623 data-target="#details-1623" data-toggle="collapse"><span class="ident">visible</span><span>: </span><a class="type-intrinsic">boolean</a></span>
<div class="details collapse" id="details-1623">
<div class="comment"><p><code>true</code> if the <a href="#TreeView">tree view</a> is visible otherwise <code>false</code>.</p>
</div>
</div>

### <a name="TypeDefinitionProvider"></a><span class="code-item" id=621>TypeDefinitionProvider</span>



<div class="comment"><p>The type definition provider defines the contract between extensions and
the go to type definition feature.</p>
</div>

#### Methods



<a name="TypeDefinitionProvider.provideTypeDefinition"></a><span class="ts" id=623 data-target="#details-623" data-toggle="collapse"><span class="ident">provideTypeDefinition</span><span>(</span><span class="ident">document</span><span>: </span><a class="type-ref" href="#TextDocument">TextDocument</a>, <span class="ident">position</span><span>: </span><a class="type-ref" href="#Position">Position</a>, <span class="ident">token</span><span>: </span><a class="type-ref" href="#CancellationToken">CancellationToken</a><span>)</span><span>: </span><a class="type-ref" href="#ProviderResult">ProviderResult</a>&lt;<a class="type-ref" href="#Definition">Definition</a> &#124; <a class="type-ref" href="#DefinitionLink">DefinitionLink</a>[]&gt;</span>
<div class="details collapse" id="details-623">
<div class="comment"><p>Provide the type definition of the symbol at the given position and document.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="document"></a><span class="ts" id=624 data-target="#details-624" data-toggle="collapse"><span class="ident">document</span><span>: </span><a class="type-ref" href="#TextDocument">TextDocument</a></span></td><td><div class="comment"><p>The document in which the command was invoked.</p>
</div></td></tr>
<tr><td><a name="position"></a><span class="ts" id=625 data-target="#details-625" data-toggle="collapse"><span class="ident">position</span><span>: </span><a class="type-ref" href="#Position">Position</a></span></td><td><div class="comment"><p>The position at which the command was invoked.</p>
</div></td></tr>
<tr><td><a name="token"></a><span class="ts" id=626 data-target="#details-626" data-toggle="collapse"><span class="ident">token</span><span>: </span><a class="type-ref" href="#CancellationToken">CancellationToken</a></span></td><td><div class="comment"><p>A cancellation token.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#ProviderResult">ProviderResult</a>&lt;<a class="type-ref" href="#Definition">Definition</a> &#124; <a class="type-ref" href="#DefinitionLink">DefinitionLink</a>[]&gt;</span></td><td><div class="comment"><p>A definition or a thenable that resolves to such. The lack of a result can be
signaled by returning <code>undefined</code> or <code>null</code>.</p>
</div></td></tr>
</table>
</div>
</div>

### <a name="Uri"></a><span class="code-item" id=391>Uri</span>



<div class="comment"><p>A universal resource identifier representing either a file on disk
or another resource, like untitled resources.</p>
</div>

#### Static



<a name="Uri.file"></a><span class="ts" id=396 data-target="#details-396" data-toggle="collapse"><span class="ident">file</span><span>(</span><span class="ident">path</span><span>: </span><a class="type-intrinsic">string</a><span>)</span><span>: </span><a class="type-ref" href="#Uri">Uri</a></span>
<div class="details collapse" id="details-396">
<div class="comment"><p>Create an URI from a file system path. The <a href="#Uri.scheme">scheme</a>
will be <code>file</code>.</p>
<p>The <em>difference</em> between <code>Uri#parse</code> and <code>Uri#file</code> is that the latter treats the argument
as path, not as stringified-uri. E.g. <code>Uri.file(path)</code> is <em>not</em> the same as
<code>Uri.parse(&#39;file://&#39; + path)</code> because the path might contain characters that are
interpreted (# and ?). See the following sample:</p>

<pre><code class="lang-ts">const good = URI.file(&#39;/coding/c#/project1&#39;);
good.scheme === &#39;file&#39;;
good.path === &#39;/coding/c#/project1&#39;;
good.fragment === &#39;&#39;;

const bad = URI.parse(&#39;file://&#39; + &#39;/coding/c#/project1&#39;);
bad.scheme === &#39;file&#39;;
bad.path === &#39;/coding/c&#39;; // path is now broken
bad.fragment === &#39;/project1&#39;;
</code></pre>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="path"></a><span class="ts" id=397 data-target="#details-397" data-toggle="collapse"><span class="ident">path</span><span>: </span><a class="type-intrinsic">string</a></span></td><td><div class="comment"><p>A file system or UNC path.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#Uri">Uri</a></span></td><td><div class="comment"><p>A new Uri instance.</p>
</div></td></tr>
</table>
</div>
</div>



<a name="Uri.parse"></a><span class="ts" id=393 data-target="#details-393" data-toggle="collapse"><span class="ident">parse</span><span>(</span><span class="ident">value</span><span>: </span><a class="type-intrinsic">string</a><span>)</span><span>: </span><a class="type-ref" href="#Uri">Uri</a></span>
<div class="details collapse" id="details-393">
<div class="comment"><p>Create an URI from a string, e.g. <code>http://www.msft.com/some/path</code>,
<code>file:///usr/home</code>, or <code>scheme:with/path</code>.</p>
<ul>
<li><em>see</em> - <a href="#Uri.toString">Uri.toString</a></li>
</ul>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="value"></a><span class="ts" id=394 data-target="#details-394" data-toggle="collapse"><span class="ident">value</span><span>: </span><a class="type-intrinsic">string</a></span></td><td><div class="comment"><p>The string value of an Uri.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#Uri">Uri</a></span></td><td><div class="comment"><p>A new Uri instance.</p>
</div></td></tr>
</table>
</div>
</div>

#### Constructors



<a name="Uri.new Uri"></a><span class="ts" id=399 data-target="#details-399" data-toggle="collapse"><span class="ident">new Uri</span><span>(</span><span class="ident">scheme</span><span>: </span><a class="type-intrinsic">string</a>, <span class="ident">authority</span><span>: </span><a class="type-intrinsic">string</a>, <span class="ident">path</span><span>: </span><a class="type-intrinsic">string</a>, <span class="ident">query</span><span>: </span><a class="type-intrinsic">string</a>, <span class="ident">fragment</span><span>: </span><a class="type-intrinsic">string</a><span>)</span><span>: </span><a class="type-ref" href="#Uri">Uri</a></span>
<div class="details collapse" id="details-399">
<div class="comment"><p>Use the <code>file</code> and <code>parse</code> factory functions to create new <code>Uri</code> objects.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="scheme"></a><span class="ts" id=400 data-target="#details-400" data-toggle="collapse"><span class="ident">scheme</span><span>: </span><a class="type-intrinsic">string</a></span></td><td><div class="comment"></div></td></tr>
<tr><td><a name="authority"></a><span class="ts" id=401 data-target="#details-401" data-toggle="collapse"><span class="ident">authority</span><span>: </span><a class="type-intrinsic">string</a></span></td><td><div class="comment"></div></td></tr>
<tr><td><a name="path"></a><span class="ts" id=402 data-target="#details-402" data-toggle="collapse"><span class="ident">path</span><span>: </span><a class="type-intrinsic">string</a></span></td><td><div class="comment"></div></td></tr>
<tr><td><a name="query"></a><span class="ts" id=403 data-target="#details-403" data-toggle="collapse"><span class="ident">query</span><span>: </span><a class="type-intrinsic">string</a></span></td><td><div class="comment"></div></td></tr>
<tr><td><a name="fragment"></a><span class="ts" id=404 data-target="#details-404" data-toggle="collapse"><span class="ident">fragment</span><span>: </span><a class="type-intrinsic">string</a></span></td><td><div class="comment"></div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#Uri">Uri</a></span></td><td><div class="comment"></div></td></tr>
</table>
</div>
</div>

#### Properties



<a name="Uri.authority"></a><span class="ts" id=406 data-target="#details-406" data-toggle="collapse"><span class="ident">authority</span><span>: </span><a class="type-intrinsic">string</a></span>
<div class="details collapse" id="details-406">
<div class="comment"><p>Authority is the <code>www.msft.com</code> part of <code>http://www.msft.com/some/path?query#fragment</code>.
The part between the first double slashes and the next slash.</p>
</div>
</div>



<a name="Uri.fragment"></a><span class="ts" id=409 data-target="#details-409" data-toggle="collapse"><span class="ident">fragment</span><span>: </span><a class="type-intrinsic">string</a></span>
<div class="details collapse" id="details-409">
<div class="comment"><p>Fragment is the <code>fragment</code> part of <code>http://www.msft.com/some/path?query#fragment</code>.</p>
</div>
</div>



<a name="Uri.fsPath"></a><span class="ts" id=410 data-target="#details-410" data-toggle="collapse"><span class="ident">fsPath</span><span>: </span><a class="type-intrinsic">string</a></span>
<div class="details collapse" id="details-410">
<div class="comment"><p>The string representing the corresponding file system path of this Uri.</p>
<p>Will handle UNC paths and normalize windows drive letters to lower-case. Also
uses the platform specific path separator.</p>
<ul>
<li>Will <em>not</em> validate the path for invalid characters and semantics.</li>
<li>Will <em>not</em> look at the scheme of this Uri.</li>
<li>The resulting string shall <em>not</em> be used for display purposes but
for disk operations, like <code>readFile</code> et al.</li>
</ul>
<p>The <em>difference</em> to the <a href="#Uri.path"><code>path</code></a>-property is the use of the platform specific
path separator and the handling of UNC paths. The sample below outlines the difference:</p>

<pre><code class="lang-ts">const u = URI.parse(&#39;file://server/c$/folder/file.txt&#39;)
u.authority === &#39;server&#39;
u.path === &#39;/shares/c$/file.txt&#39;
u.fsPath === &#39;\\server\c$\folder\file.txt&#39;
</code></pre>
</div>
</div>



<a name="Uri.path"></a><span class="ts" id=407 data-target="#details-407" data-toggle="collapse"><span class="ident">path</span><span>: </span><a class="type-intrinsic">string</a></span>
<div class="details collapse" id="details-407">
<div class="comment"><p>Path is the <code>/some/path</code> part of <code>http://www.msft.com/some/path?query#fragment</code>.</p>
</div>
</div>



<a name="Uri.query"></a><span class="ts" id=408 data-target="#details-408" data-toggle="collapse"><span class="ident">query</span><span>: </span><a class="type-intrinsic">string</a></span>
<div class="details collapse" id="details-408">
<div class="comment"><p>Query is the <code>query</code> part of <code>http://www.msft.com/some/path?query#fragment</code>.</p>
</div>
</div>



<a name="Uri.scheme"></a><span class="ts" id=405 data-target="#details-405" data-toggle="collapse"><span class="ident">scheme</span><span>: </span><a class="type-intrinsic">string</a></span>
<div class="details collapse" id="details-405">
<div class="comment"><p>Scheme is the <code>http</code> part of <code>http://www.msft.com/some/path?query#fragment</code>.
The part before the first colon.</p>
</div>
</div>

#### Methods



<a name="Uri.toJSON"></a><span class="ts" id=424 data-target="#details-424" data-toggle="collapse"><span class="ident">toJSON</span><span>(</span><span>)</span><span>: </span><a class="type-intrinsic">any</a></span>
<div class="details collapse" id="details-424">
<div class="comment"><p>Returns a JSON representation of this Uri.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-intrinsic">any</a></span></td><td><div class="comment"><p>An object.</p>
</div></td></tr>
</table>
</div>
</div>



<a name="Uri.toString"></a><span class="ts" id=421 data-target="#details-421" data-toggle="collapse"><span class="ident">toString</span><span>(</span><span class="ident">skipEncoding</span><span>?</span><span>: </span><a class="type-intrinsic">boolean</a><span>)</span><span>: </span><a class="type-intrinsic">string</a></span>
<div class="details collapse" id="details-421">
<div class="comment"><p>Returns a string representation of this Uri. The representation and normalization
of a URI depends on the scheme.</p>
<ul>
<li>The resulting string can be safely used with <a href="#Uri.parse">Uri.parse</a>.</li>
<li>The resulting string shall <em>not</em> be used for display purposes.</li>
</ul>
<p><em>Note</em> that the implementation will encode <em>aggressive</em> which often leads to unexpected,
but not incorrect, results. For instance, colons are encoded to <code>%3A</code> which might be unexpected
in file-uri. Also <code>&amp;</code> and <code>=</code> will be encoded which might be unexpected for http-uris. For stability
reasons this cannot be changed anymore. If you suffer from too aggressive encoding you should use
the <code>skipEncoding</code>-argument: <code>uri.toString(true)</code>.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="skipEncoding"></a><span class="ts" id=422 data-target="#details-422" data-toggle="collapse"><span class="ident">skipEncoding</span><span>?</span><span>: </span><a class="type-intrinsic">boolean</a></span></td><td><div class="comment"><p>Do not percentage-encode the result, defaults to <code>false</code>. Note that
    the <code>#</code> and <code>?</code> characters occurring in the path will always be encoded.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-intrinsic">string</a></span></td><td><div class="comment"><p>A string representation of this Uri.</p>
</div></td></tr>
</table>
</div>
</div>



<a name="Uri.with"></a><span class="ts" id=412 data-target="#details-412" data-toggle="collapse"><span class="ident">with</span><span>(</span><span class="ident">change</span><span>: </span>{authority: <a class="type-intrinsic">string</a>, fragment: <a class="type-intrinsic">string</a>, path: <a class="type-intrinsic">string</a>, query: <a class="type-intrinsic">string</a>, scheme: <a class="type-intrinsic">string</a>}<span>)</span><span>: </span><a class="type-ref" href="#Uri">Uri</a></span>
<div class="details collapse" id="details-412">
<div class="comment"><p>Derive a new Uri from this Uri.</p>

<pre><code class="lang-ts">let file = Uri.parse(&#39;before:some/file/path&#39;);
let other = file.with({ scheme: &#39;after&#39; });
assert.ok(other.toString() === &#39;after:some/file/path&#39;);
</code></pre>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="change"></a><span class="ts" id=413 data-target="#details-413" data-toggle="collapse"><span class="ident">change</span><span>: </span>{authority: <a class="type-intrinsic">string</a>, fragment: <a class="type-intrinsic">string</a>, path: <a class="type-intrinsic">string</a>, query: <a class="type-intrinsic">string</a>, scheme: <a class="type-intrinsic">string</a>}</span></td><td><div class="comment"><p>An object that describes a change to this Uri. To unset components use <code>null</code> or
 the empty string.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#Uri">Uri</a></span></td><td><div class="comment"><p>A new Uri that reflects the given change. Will return <code>this</code> Uri if the change
 is not changing anything.</p>
</div></td></tr>
</table>
</div>
</div>

### <a name="UriHandler"></a><span class="code-item" id=1608>UriHandler</span>



<div class="comment"><p>A uri handler is responsible for handling system-wide <a href="#Uri">uris</a>.</p>
<ul>
<li><em>see</em> - <a href="#window.registerUriHandler">window.registerUriHandler</a>.</li>
</ul>
</div>

#### Methods



<a name="UriHandler.handleUri"></a><span class="ts" id=1610 data-target="#details-1610" data-toggle="collapse"><span class="ident">handleUri</span><span>(</span><span class="ident">uri</span><span>: </span><a class="type-ref" href="#Uri">Uri</a><span>)</span><span>: </span><a class="type-ref" href="#ProviderResult">ProviderResult</a>&lt;<a class="type-intrinsic">void</a>&gt;</span>
<div class="details collapse" id="details-1610">
<div class="comment"><p>Handle the provided system-wide <a href="#Uri">uri</a>.</p>
<ul>
<li><em>see</em> - <a href="#window.registerUriHandler">window.registerUriHandler</a>.</li>
</ul>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="uri"></a><span class="ts" id=1611 data-target="#details-1611" data-toggle="collapse"><span class="ident">uri</span><span>: </span><a class="type-ref" href="#Uri">Uri</a></span></td><td><div class="comment"></div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#ProviderResult">ProviderResult</a>&lt;<a class="type-intrinsic">void</a>&gt;</span></td><td><div class="comment"></div></td></tr>
</table>
</div>
</div>

### <a name="ViewColumn"></a><span class="code-item" id=1228>ViewColumn</span>



<div class="comment"><p>Denotes a location of an editor in the window. Editors can be arranged in a grid
and each column represents one editor location in that grid by counting the editors
in order of their appearance.</p>
</div>

#### Enumeration members



<a name="ViewColumn.Active"></a><span class="ts" id=1229 data-target="#details-1229" data-toggle="collapse"><span class="ident">Active</span></span>
<div class="details collapse" id="details-1229">
<em> -1</em>
</div>



<a name="ViewColumn.Beside"></a><span class="ts" id=1230 data-target="#details-1230" data-toggle="collapse"><span class="ident">Beside</span></span>
<div class="details collapse" id="details-1230">
<em> -2</em>
</div>



<a name="ViewColumn.Eight"></a><span class="ts" id=1238 data-target="#details-1238" data-toggle="collapse"><span class="ident">Eight</span></span>
<div class="details collapse" id="details-1238">
<em>8</em>
</div>



<a name="ViewColumn.Five"></a><span class="ts" id=1235 data-target="#details-1235" data-toggle="collapse"><span class="ident">Five</span></span>
<div class="details collapse" id="details-1235">
<em>5</em>
</div>



<a name="ViewColumn.Four"></a><span class="ts" id=1234 data-target="#details-1234" data-toggle="collapse"><span class="ident">Four</span></span>
<div class="details collapse" id="details-1234">
<em>4</em>
</div>



<a name="ViewColumn.Nine"></a><span class="ts" id=1239 data-target="#details-1239" data-toggle="collapse"><span class="ident">Nine</span></span>
<div class="details collapse" id="details-1239">
<em>9</em>
</div>



<a name="ViewColumn.One"></a><span class="ts" id=1231 data-target="#details-1231" data-toggle="collapse"><span class="ident">One</span></span>
<div class="details collapse" id="details-1231">
<em>1</em>
</div>



<a name="ViewColumn.Seven"></a><span class="ts" id=1237 data-target="#details-1237" data-toggle="collapse"><span class="ident">Seven</span></span>
<div class="details collapse" id="details-1237">
<em>7</em>
</div>



<a name="ViewColumn.Six"></a><span class="ts" id=1236 data-target="#details-1236" data-toggle="collapse"><span class="ident">Six</span></span>
<div class="details collapse" id="details-1236">
<em>6</em>
</div>



<a name="ViewColumn.Three"></a><span class="ts" id=1233 data-target="#details-1233" data-toggle="collapse"><span class="ident">Three</span></span>
<div class="details collapse" id="details-1233">
<em>3</em>
</div>



<a name="ViewColumn.Two"></a><span class="ts" id=1232 data-target="#details-1232" data-toggle="collapse"><span class="ident">Two</span></span>
<div class="details collapse" id="details-1232">
<em>2</em>
</div>

### <a name="Webview"></a><span class="code-item" id=1563>Webview</span>



<div class="comment"><p>A webview displays html content, like an iframe.</p>
</div>

#### Events



<a name="Webview.onDidReceiveMessage"></a><span class="ts" id=1566 data-target="#details-1566" data-toggle="collapse"><span class="ident">onDidReceiveMessage</span><span>: </span><a class="type-ref" href="#Event">Event</a>&lt;<a class="type-intrinsic">any</a>&gt;</span>
<div class="details collapse" id="details-1566">
<div class="comment"><p>Fired when the webview content posts a message.</p>
</div>
</div>

#### Properties



<a name="Webview.html"></a><span class="ts" id=1565 data-target="#details-1565" data-toggle="collapse"><span class="ident">html</span><span>: </span><a class="type-intrinsic">string</a></span>
<div class="details collapse" id="details-1565">
<div class="comment"><p>Contents of the webview.</p>
<p>Should be a complete html document.</p>
</div>
</div>



<a name="Webview.options"></a><span class="ts" id=1564 data-target="#details-1564" data-toggle="collapse"><span class="ident">options</span><span>: </span><a class="type-ref" href="#WebviewOptions">WebviewOptions</a></span>
<div class="details collapse" id="details-1564">
<div class="comment"><p>Content settings for the webview.</p>
</div>
</div>

#### Methods



<a name="Webview.postMessage"></a><span class="ts" id=1568 data-target="#details-1568" data-toggle="collapse"><span class="ident">postMessage</span><span>(</span><span class="ident">message</span><span>: </span><a class="type-intrinsic">any</a><span>)</span><span>: </span><a class="type-ref" href="#Thenable">Thenable</a>&lt;<a class="type-intrinsic">boolean</a>&gt;</span>
<div class="details collapse" id="details-1568">
<div class="comment"><p>Post a message to the webview content.</p>
<p>Messages are only delivered if the webview is visible.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="message"></a><span class="ts" id=1569 data-target="#details-1569" data-toggle="collapse"><span class="ident">message</span><span>: </span><a class="type-intrinsic">any</a></span></td><td><div class="comment"><p>Body of the message.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#Thenable">Thenable</a>&lt;<a class="type-intrinsic">boolean</a>&gt;</span></td><td><div class="comment"></div></td></tr>
</table>
</div>
</div>

### <a name="WebviewOptions"></a><span class="code-item" id=1559>WebviewOptions</span>



<div class="comment"><p>Content settings for a webview.</p>
</div>

#### Properties



<a name="WebviewOptions.enableCommandUris"></a><span class="ts" id=1561 data-target="#details-1561" data-toggle="collapse"><span class="ident">enableCommandUris</span><span>?</span><span>: </span><a class="type-intrinsic">boolean</a></span>
<div class="details collapse" id="details-1561">
<div class="comment"><p>Controls whether command uris are enabled in webview content or not.</p>
<p>Defaults to false.</p>
</div>
</div>



<a name="WebviewOptions.enableScripts"></a><span class="ts" id=1560 data-target="#details-1560" data-toggle="collapse"><span class="ident">enableScripts</span><span>?</span><span>: </span><a class="type-intrinsic">boolean</a></span>
<div class="details collapse" id="details-1560">
<div class="comment"><p>Controls whether scripts are enabled in the webview content or not.</p>
<p>Defaults to false (scripts-disabled).</p>
</div>
</div>



<a name="WebviewOptions.localResourceRoots"></a><span class="ts" id=1562 data-target="#details-1562" data-toggle="collapse"><span class="ident">localResourceRoots</span><span>?</span><span>: </span><a class="type-ref" href="#ReadonlyArray">ReadonlyArray</a>&lt;<a class="type-ref" href="#Uri">Uri</a>&gt;</span>
<div class="details collapse" id="details-1562">
<div class="comment"><p>Root paths from which the webview can load local (filesystem) resources using the <code>vscode-resource:</code> scheme.</p>
<p>Default to the root folders of the current workspace plus the extension&#39;s install directory.</p>
<p>Pass in an empty array to disallow access to any local resources.</p>
</div>
</div>

### <a name="WebviewPanel"></a><span class="code-item" id=1573>WebviewPanel</span>



<div class="comment"><p>A panel that contains a webview.</p>
</div>

#### Events



<a name="WebviewPanel.onDidChangeViewState"></a><span class="ts" id=1585 data-target="#details-1585" data-toggle="collapse"><span class="ident">onDidChangeViewState</span><span>: </span><a class="type-ref" href="#Event">Event</a>&lt;<a class="type-ref" href="#WebviewPanelOnDidChangeViewStateEvent">WebviewPanelOnDidChangeViewStateEvent</a>&gt;</span>
<div class="details collapse" id="details-1585">
<div class="comment"><p>Fired when the panel&#39;s view state changes.</p>
</div>
</div>



<a name="WebviewPanel.onDidDispose"></a><span class="ts" id=1586 data-target="#details-1586" data-toggle="collapse"><span class="ident">onDidDispose</span><span>: </span><a class="type-ref" href="#Event">Event</a>&lt;<a class="type-intrinsic">void</a>&gt;</span>
<div class="details collapse" id="details-1586">
<div class="comment"><p>Fired when the panel is disposed.</p>
<p>This may be because the user closed the panel or because <code>.dispose()</code> was
called on it.</p>
<p>Trying to use the panel after it has been disposed throws an exception.</p>
</div>
</div>

#### Properties



<a name="WebviewPanel.active"></a><span class="ts" id=1583 data-target="#details-1583" data-toggle="collapse"><span class="ident">active</span><span>: </span><a class="type-intrinsic">boolean</a></span>
<div class="details collapse" id="details-1583">
<div class="comment"><p>Whether the panel is active (focused by the user).</p>
</div>
</div>



<a name="WebviewPanel.iconPath"></a><span class="ts" id=1576 data-target="#details-1576" data-toggle="collapse"><span class="ident">iconPath</span><span>?</span><span>: </span><a class="type-ref" href="#Uri">Uri</a> &#124; {dark: <a class="type-ref" href="#Uri">Uri</a>, light: <a class="type-ref" href="#Uri">Uri</a>}</span>
<div class="details collapse" id="details-1576">
<div class="comment"><p>Icon for the panel shown in UI.</p>
</div>
</div>



<a name="WebviewPanel.options"></a><span class="ts" id=1581 data-target="#details-1581" data-toggle="collapse"><span class="ident">options</span><span>: </span><a class="type-ref" href="#WebviewPanelOptions">WebviewPanelOptions</a></span>
<div class="details collapse" id="details-1581">
<div class="comment"><p>Content settings for the webview panel.</p>
</div>
</div>



<a name="WebviewPanel.title"></a><span class="ts" id=1575 data-target="#details-1575" data-toggle="collapse"><span class="ident">title</span><span>: </span><a class="type-intrinsic">string</a></span>
<div class="details collapse" id="details-1575">
<div class="comment"><p>Title of the panel shown in UI.</p>
</div>
</div>



<a name="WebviewPanel.viewColumn"></a><span class="ts" id=1582 data-target="#details-1582" data-toggle="collapse"><span class="ident">viewColumn</span><span>?</span><span>: </span><a class="type-ref" href="#ViewColumn">ViewColumn</a></span>
<div class="details collapse" id="details-1582">
<div class="comment"><p>Editor position of the panel. This property is only set if the webview is in
one of the editor view columns.</p>
</div>
</div>



<a name="WebviewPanel.viewType"></a><span class="ts" id=1574 data-target="#details-1574" data-toggle="collapse"><span class="ident">viewType</span><span>: </span><a class="type-intrinsic">string</a></span>
<div class="details collapse" id="details-1574">
<div class="comment"><p>Identifies the type of the webview panel, such as <code>&#39;markdown.preview&#39;</code>.</p>
</div>
</div>



<a name="WebviewPanel.visible"></a><span class="ts" id=1584 data-target="#details-1584" data-toggle="collapse"><span class="ident">visible</span><span>: </span><a class="type-intrinsic">boolean</a></span>
<div class="details collapse" id="details-1584">
<div class="comment"><p>Whether the panel is visible.</p>
</div>
</div>



<a name="WebviewPanel.webview"></a><span class="ts" id=1580 data-target="#details-1580" data-toggle="collapse"><span class="ident">webview</span><span>: </span><a class="type-ref" href="#Webview">Webview</a></span>
<div class="details collapse" id="details-1580">
<div class="comment"><p>Webview belonging to the panel.</p>
</div>
</div>

#### Methods



<a name="WebviewPanel.dispose"></a><span class="ts" id=1592 data-target="#details-1592" data-toggle="collapse"><span class="ident">dispose</span><span>(</span><span>)</span><span>: </span><a class="type-intrinsic">any</a></span>
<div class="details collapse" id="details-1592">
<div class="comment"><p>Dispose of the webview panel.</p>
<p>This closes the panel if it showing and disposes of the resources owned by the webview.
Webview panels are also disposed when the user closes the webview panel. Both cases
fire the <code>onDispose</code> event.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-intrinsic">any</a></span></td><td><div class="comment"></div></td></tr>
</table>
</div>
</div>



<a name="WebviewPanel.reveal"></a><span class="ts" id=1588 data-target="#details-1588" data-toggle="collapse"><span class="ident">reveal</span><span>(</span><span class="ident">viewColumn</span><span>?</span><span>: </span><a class="type-ref" href="#ViewColumn">ViewColumn</a>, <span class="ident">preserveFocus</span><span>?</span><span>: </span><a class="type-intrinsic">boolean</a><span>)</span><span>: </span><a class="type-intrinsic">void</a></span>
<div class="details collapse" id="details-1588">
<div class="comment"><p>Show the webview panel in a given column.</p>
<p>A webview panel may only show in a single column at a time. If it is already showing, this
method moves it to a new column.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="viewColumn"></a><span class="ts" id=1589 data-target="#details-1589" data-toggle="collapse"><span class="ident">viewColumn</span><span>?</span><span>: </span><a class="type-ref" href="#ViewColumn">ViewColumn</a></span></td><td><div class="comment"><p>View column to show the panel in. Shows in the current <code>viewColumn</code> if undefined.</p>
</div></td></tr>
<tr><td><a name="preserveFocus"></a><span class="ts" id=1590 data-target="#details-1590" data-toggle="collapse"><span class="ident">preserveFocus</span><span>?</span><span>: </span><a class="type-intrinsic">boolean</a></span></td><td><div class="comment"><p>When <code>true</code>, the webview will not take focus.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-intrinsic">void</a></span></td><td><div class="comment"></div></td></tr>
</table>
</div>
</div>

### <a name="WebviewPanelOnDidChangeViewStateEvent"></a><span class="code-item" id=1593>WebviewPanelOnDidChangeViewStateEvent</span>



<div class="comment"><p>Event fired when a webview panel&#39;s view state changes.</p>
</div>

#### Properties



<a name="WebviewPanelOnDidChangeViewStateEvent.webviewPanel"></a><span class="ts" id=1594 data-target="#details-1594" data-toggle="collapse"><span class="ident">webviewPanel</span><span>: </span><a class="type-ref" href="#WebviewPanel">WebviewPanel</a></span>
<div class="details collapse" id="details-1594">
<div class="comment"><p>Webview panel whose view state changed.</p>
</div>
</div>

### <a name="WebviewPanelOptions"></a><span class="code-item" id=1570>WebviewPanelOptions</span>



<div class="comment"><p>Content settings for a webview panel.</p>
</div>

#### Properties



<a name="WebviewPanelOptions.enableFindWidget"></a><span class="ts" id=1571 data-target="#details-1571" data-toggle="collapse"><span class="ident">enableFindWidget</span><span>?</span><span>: </span><a class="type-intrinsic">boolean</a></span>
<div class="details collapse" id="details-1571">
<div class="comment"><p>Controls if the find widget is enabled in the panel.</p>
<p>Defaults to false.</p>
</div>
</div>



<a name="WebviewPanelOptions.retainContextWhenHidden"></a><span class="ts" id=1572 data-target="#details-1572" data-toggle="collapse"><span class="ident">retainContextWhenHidden</span><span>?</span><span>: </span><a class="type-intrinsic">boolean</a></span>
<div class="details collapse" id="details-1572">
<div class="comment"><p>Controls if the webview panel&#39;s content (iframe) is kept around even when the panel
is no longer visible.</p>
<p>Normally the webview panel&#39;s html context is created when the panel becomes visible
and destroyed when it is hidden. Extensions that have complex state
or UI can set the <code>retainContextWhenHidden</code> to make VS Code keep the webview
context around, even when the webview moves to a background tab. When a webview using
<code>retainContextWhenHidden</code> becomes hidden, its scripts and other dynamic content are suspended.
When the panel becomes visible again, the context is automatically restored
in the exact same state it was in originally. You cannot send messages to a
hidden webview, even with <code>retainContextWhenHidden</code> enabled.</p>
<p><code>retainContextWhenHidden</code> has a high memory overhead and should only be used if
your panel&#39;s context cannot be quickly saved and restored.</p>
</div>
</div>

### <a name="WebviewPanelSerializer"></a><span class="code-item" id=1595>WebviewPanelSerializer</span>



<div class="comment"><p>Restore webview panels that have been persisted when vscode shuts down.</p>
<p>There are two types of webview persistence:</p>
<ul>
<li>Persistence within a session.</li>
<li>Persistence across sessions (across restarts of VS Code).</li>
</ul>
<p>A <code>WebviewPanelSerializer</code> is only required for the second case: persisting a webview across sessions.</p>
<p>Persistence within a session allows a webview to save its state when it becomes hidden
and restore its content from this state when it becomes visible again. It is powered entirely
by the webview content itself. To save off a persisted state, call <code>acquireVsCodeApi().setState()</code> with
any json serializable object. To restore the state again, call <code>getState()</code></p>

<pre><code class="lang-js">// Within the webview
const vscode = acquireVsCodeApi();

// Get existing state
const oldState = vscode.getState() || { value: 0 };

// Update state
setState({ value: oldState.value + 1 })
</code></pre>
<p>A <code>WebviewPanelSerializer</code> extends this persistence across restarts of VS Code. When the editor is shutdown,
VS Code will save off the state from <code>setState</code> of all webviews that have a serializer. When the
webview first becomes visible after the restart, this state is passed to <code>deserializeWebviewPanel</code>.
The extension can then restore the old <code>WebviewPanel</code> from this state.</p>
</div>

#### Methods



<a name="WebviewPanelSerializer.deserializeWebviewPanel"></a><span class="ts" id=1597 data-target="#details-1597" data-toggle="collapse"><span class="ident">deserializeWebviewPanel</span><span>(</span><span class="ident">webviewPanel</span><span>: </span><a class="type-ref" href="#WebviewPanel">WebviewPanel</a>, <span class="ident">state</span><span>: </span><a class="type-intrinsic">any</a><span>)</span><span>: </span><a class="type-ref" href="#Thenable">Thenable</a>&lt;<a class="type-intrinsic">void</a>&gt;</span>
<div class="details collapse" id="details-1597">
<div class="comment"><p>Restore a webview panel from its seriailzed <code>state</code>.</p>
<p>Called when a serialized webview first becomes visible.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="webviewPanel"></a><span class="ts" id=1598 data-target="#details-1598" data-toggle="collapse"><span class="ident">webviewPanel</span><span>: </span><a class="type-ref" href="#WebviewPanel">WebviewPanel</a></span></td><td><div class="comment"><p>Webview panel to restore. The serializer should take ownership of this panel. The
serializer must restore the webview&#39;s <code>.html</code> and hook up all webview events.</p>
</div></td></tr>
<tr><td><a name="state"></a><span class="ts" id=1599 data-target="#details-1599" data-toggle="collapse"><span class="ident">state</span><span>: </span><a class="type-intrinsic">any</a></span></td><td><div class="comment"><p>Persisted state from the webview content.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#Thenable">Thenable</a>&lt;<a class="type-intrinsic">void</a>&gt;</span></td><td><div class="comment"><p>Thanble indicating that the webview has been fully restored.</p>
</div></td></tr>
</table>
</div>
</div>

### <a name="WindowState"></a><span class="code-item" id=1606>WindowState</span>



<div class="comment"><p>Represents the state of a window.</p>
</div>

#### Properties



<a name="WindowState.focused"></a><span class="ts" id=1607 data-target="#details-1607" data-toggle="collapse"><span class="ident">focused</span><span>: </span><a class="type-intrinsic">boolean</a></span>
<div class="details collapse" id="details-1607">
<div class="comment"><p>Whether the current window is focused.</p>
</div>
</div>

### <a name="WorkspaceConfiguration"></a><span class="code-item" id=1128>WorkspaceConfiguration</span>



<div class="comment"><p>Represents the configuration. It is a merged view of</p>
<ul>
<li>Default configuration</li>
<li>Global configuration</li>
<li>Workspace configuration (if available)</li>
<li>Workspace folder configuration of the requested resource (if available)</li>
</ul>
<p><em>Global configuration</em> comes from User Settings and shadows Defaults.</p>
<p><em>Workspace configuration</em> comes from Workspace Settings and shadows Global configuration.</p>
<p><em>Workspace Folder configuration</em> comes from <code>.vscode</code> folder under one of the <a href="#workspace.workspaceFolders">workspace folders</a>.</p>
<p><em>Note:</em> Workspace and Workspace Folder configurations contains <code>launch</code> and <code>tasks</code> settings. Their basename will be
part of the section identifier. The following snippets shows how to retrieve all configurations
from <code>launch.json</code>:</p>

<pre><code class="lang-ts">// launch.json configuration
const config = workspace.getConfiguration(&#39;launch&#39;, vscode.window.activeTextEditor.document.uri);

// retrieve values
const values = config.get(&#39;configurations&#39;);
</code></pre>
<p>Refer to <a href="https://code.visualstudio.com/docs/getstarted/settings">Settings</a> for more information.</p>
</div>

#### Methods



<a name="WorkspaceConfiguration.get"></a><span class="ts" id=1130 data-target="#details-1130" data-toggle="collapse"><span class="ident">get</span><span>&lt;</span>T<span>&gt;</span><span>(</span><span class="ident">section</span><span>: </span><a class="type-intrinsic">string</a><span>)</span><span>: </span><a class="type-intrinsic">T</a> &#124; <a class="type-intrinsic">undefined</a></span>
<div class="details collapse" id="details-1130">
<div class="comment"><p>Return a value from this configuration.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="section"></a><span class="ts" id=1132 data-target="#details-1132" data-toggle="collapse"><span class="ident">section</span><span>: </span><a class="type-intrinsic">string</a></span></td><td><div class="comment"><p>Configuration name, supports <em>dotted</em> names.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-intrinsic">T</a> &#124; <a class="type-intrinsic">undefined</a></span></td><td><div class="comment"><p>The value <code>section</code> denotes or <code>undefined</code>.</p>
</div></td></tr>
</table>
</div>
</div>



<a name="WorkspaceConfiguration.get"></a><span class="ts" id=1133 data-target="#details-1133" data-toggle="collapse"><span class="ident">get</span><span>&lt;</span>T<span>&gt;</span><span>(</span><span class="ident">section</span><span>: </span><a class="type-intrinsic">string</a>, <span class="ident">defaultValue</span><span>: </span><a class="type-intrinsic">T</a><span>)</span><span>: </span><a class="type-intrinsic">T</a></span>
<div class="details collapse" id="details-1133">
<div class="comment"><p>Return a value from this configuration.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="section"></a><span class="ts" id=1135 data-target="#details-1135" data-toggle="collapse"><span class="ident">section</span><span>: </span><a class="type-intrinsic">string</a></span></td><td><div class="comment"><p>Configuration name, supports <em>dotted</em> names.</p>
</div></td></tr>
<tr><td><a name="defaultValue"></a><span class="ts" id=1136 data-target="#details-1136" data-toggle="collapse"><span class="ident">defaultValue</span><span>: </span><a class="type-intrinsic">T</a></span></td><td><div class="comment"><p>A value should be returned when no value could be found, is <code>undefined</code>.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-intrinsic">T</a></span></td><td><div class="comment"><p>The value <code>section</code> denotes or the default.</p>
</div></td></tr>
</table>
</div>
</div>



<a name="WorkspaceConfiguration.has"></a><span class="ts" id=1138 data-target="#details-1138" data-toggle="collapse"><span class="ident">has</span><span>(</span><span class="ident">section</span><span>: </span><a class="type-intrinsic">string</a><span>)</span><span>: </span><a class="type-intrinsic">boolean</a></span>
<div class="details collapse" id="details-1138">
<div class="comment"><p>Check if this configuration has a certain value.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="section"></a><span class="ts" id=1139 data-target="#details-1139" data-toggle="collapse"><span class="ident">section</span><span>: </span><a class="type-intrinsic">string</a></span></td><td><div class="comment"><p>Configuration name, supports <em>dotted</em> names.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-intrinsic">boolean</a></span></td><td><div class="comment"><p><code>true</code> if the section doesn&#39;t resolve to <code>undefined</code>.</p>
</div></td></tr>
</table>
</div>
</div>



<a name="WorkspaceConfiguration.inspect"></a><span class="ts" id=1141 data-target="#details-1141" data-toggle="collapse"><span class="ident">inspect</span><span>&lt;</span>T<span>&gt;</span><span>(</span><span class="ident">section</span><span>: </span><a class="type-intrinsic">string</a><span>)</span><span>: </span>{defaultValue: <a class="type-intrinsic">T</a>, globalValue: <a class="type-intrinsic">T</a>, key: <a class="type-intrinsic">string</a>, workspaceFolderValue: <a class="type-intrinsic">T</a>, workspaceValue: <a class="type-intrinsic">T</a>} &#124; <a class="type-intrinsic">undefined</a></span>
<div class="details collapse" id="details-1141">
<div class="comment"><p>Retrieve all information about a configuration setting. A configuration value
often consists of a <em>default</em> value, a global or installation-wide value,
a workspace-specific value and a folder-specific value.</p>
<p>The <em>effective</em> value (returned by <a href="#WorkspaceConfiguration.get"><code>get</code></a>)
is computed like this: <code>defaultValue</code> overwritten by <code>globalValue</code>,
<code>globalValue</code> overwritten by <code>workspaceValue</code>. <code>workspaceValue</code> overwritten by <code>workspaceFolderValue</code>.
Refer to <a href="https://code.visualstudio.com/docs/getstarted/settings">Settings Inheritance</a>
for more information.</p>
<p><em>Note:</em> The configuration name must denote a leaf in the configuration tree
(<code>editor.fontSize</code> vs <code>editor</code>) otherwise no result is returned.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="section"></a><span class="ts" id=1143 data-target="#details-1143" data-toggle="collapse"><span class="ident">section</span><span>: </span><a class="type-intrinsic">string</a></span></td><td><div class="comment"><p>Configuration name, supports <em>dotted</em> names.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts">{defaultValue: <a class="type-intrinsic">T</a>, globalValue: <a class="type-intrinsic">T</a>, key: <a class="type-intrinsic">string</a>, workspaceFolderValue: <a class="type-intrinsic">T</a>, workspaceValue: <a class="type-intrinsic">T</a>} &#124; <a class="type-intrinsic">undefined</a></span></td><td><div class="comment"><p>Information about a configuration setting or <code>undefined</code>.</p>
</div></td></tr>
</table>
</div>
</div>



<a name="WorkspaceConfiguration.update"></a><span class="ts" id=1151 data-target="#details-1151" data-toggle="collapse"><span class="ident">update</span><span>(</span><span class="ident">section</span><span>: </span><a class="type-intrinsic">string</a>, <span class="ident">value</span><span>: </span><a class="type-intrinsic">any</a>, <span class="ident">configurationTarget</span><span>?</span><span>: </span><a class="type-ref" href="#ConfigurationTarget">ConfigurationTarget</a> &#124; <a class="type-intrinsic">boolean</a><span>)</span><span>: </span><a class="type-ref" href="#Thenable">Thenable</a>&lt;<a class="type-intrinsic">void</a>&gt;</span>
<div class="details collapse" id="details-1151">
<div class="comment"><p>Update a configuration value. The updated configuration values are persisted.</p>
<p>A value can be changed in</p>
<ul>
<li><a href="#ConfigurationTarget.Global">Global configuration</a>: Changes the value for all instances of the editor.</li>
<li><a href="#ConfigurationTarget.Workspace">Workspace configuration</a>: Changes the value for current workspace, if available.</li>
<li><a href="#ConfigurationTarget.WorkspaceFolder">Workspace folder configuration</a>: Changes the value for the
<a href="#workspace.workspaceFolders">Workspace folder</a> to which the current <a href="#WorkspaceConfiguration">configuration</a> is scoped to.</li>
</ul>
<p><em>Note 1:</em> Setting a global value in the presence of a more specific workspace value
has no observable effect in that workspace, but in others. Setting a workspace value
in the presence of a more specific folder value has no observable effect for the resources
under respective <a href="#workspace.workspaceFolders">folder</a>, but in others. Refer to
<a href="https://code.visualstudio.com/docs/getstarted/settings">Settings Inheritance</a> for more information.</p>
<p><em>Note 2:</em> To remove a configuration value use <code>undefined</code>, like so: <code>config.update(&#39;somekey&#39;, undefined)</code></p>
<p>Will throw error when</p>
<ul>
<li>Writing a configuration which is not registered.</li>
<li>Writing a configuration to workspace or folder target when no workspace is opened</li>
<li>Writing a configuration to folder target when there is no folder settings</li>
<li>Writing to folder target without passing a resource when getting the configuration (<code>workspace.getConfiguration(section, resource)</code>)</li>
<li>Writing a window configuration to folder target</li>
</ul>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="section"></a><span class="ts" id=1152 data-target="#details-1152" data-toggle="collapse"><span class="ident">section</span><span>: </span><a class="type-intrinsic">string</a></span></td><td><div class="comment"><p>Configuration name, supports <em>dotted</em> names.</p>
</div></td></tr>
<tr><td><a name="value"></a><span class="ts" id=1153 data-target="#details-1153" data-toggle="collapse"><span class="ident">value</span><span>: </span><a class="type-intrinsic">any</a></span></td><td><div class="comment"><p>The new value.</p>
</div></td></tr>
<tr><td><a name="configurationTarget"></a><span class="ts" id=1154 data-target="#details-1154" data-toggle="collapse"><span class="ident">configurationTarget</span><span>?</span><span>: </span><a class="type-ref" href="#ConfigurationTarget">ConfigurationTarget</a> &#124; <a class="type-intrinsic">boolean</a></span></td><td><div class="comment"><p>The <a href="#ConfigurationTarget">configuration target</a> or a boolean value.</p>

<pre><code>- If `true` configuration target is `ConfigurationTarget.Global`.
- If `false` configuration target is `ConfigurationTarget.Workspace`.
- If `undefined` or `null` configuration target is
`ConfigurationTarget.WorkspaceFolder` when configuration is resource specific
`ConfigurationTarget.Workspace` otherwise.
</code></pre></div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#Thenable">Thenable</a>&lt;<a class="type-intrinsic">void</a>&gt;</span></td><td><div class="comment"></div></td></tr>
</table>
</div>
</div>

### <a name="WorkspaceEdit"></a><span class="code-item" id=784>WorkspaceEdit</span>



<div class="comment"><p>A workspace edit is a collection of textual and files changes for
multiple resources and documents.</p>
<p>Use the <a href="#workspace.applyEdit">applyEdit</a>-function to apply a workspace edit.</p>
</div>

#### Properties



<a name="WorkspaceEdit.size"></a><span class="ts" id=785 data-target="#details-785" data-toggle="collapse"><span class="ident">size</span><span>: </span><a class="type-intrinsic">number</a></span>
<div class="details collapse" id="details-785">
<div class="comment"><p>The number of affected resources of textual or resource changes.</p>
</div>
</div>

#### Methods



<a name="WorkspaceEdit.createFile"></a><span class="ts" id=811 data-target="#details-811" data-toggle="collapse"><span class="ident">createFile</span><span>(</span><span class="ident">uri</span><span>: </span><a class="type-ref" href="#Uri">Uri</a>, <span class="ident">options</span><span>?</span><span>: </span>{ignoreIfExists: <a class="type-intrinsic">boolean</a>, overwrite: <a class="type-intrinsic">boolean</a>}<span>)</span><span>: </span><a class="type-intrinsic">void</a></span>
<div class="details collapse" id="details-811">
<div class="comment"><p>Create a regular file.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="uri"></a><span class="ts" id=812 data-target="#details-812" data-toggle="collapse"><span class="ident">uri</span><span>: </span><a class="type-ref" href="#Uri">Uri</a></span></td><td><div class="comment"><p>Uri of the new file..</p>
</div></td></tr>
<tr><td><a name="options"></a><span class="ts" id=813 data-target="#details-813" data-toggle="collapse"><span class="ident">options</span><span>?</span><span>: </span>{ignoreIfExists: <a class="type-intrinsic">boolean</a>, overwrite: <a class="type-intrinsic">boolean</a>}</span></td><td><div class="comment"><p>Defines if an existing file should be overwritten or be
ignored. When overwrite and ignoreIfExists are both set overwrite wins.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-intrinsic">void</a></span></td><td><div class="comment"></div></td></tr>
</table>
</div>
</div>



<a name="WorkspaceEdit.delete"></a><span class="ts" id=797 data-target="#details-797" data-toggle="collapse"><span class="ident">delete</span><span>(</span><span class="ident">uri</span><span>: </span><a class="type-ref" href="#Uri">Uri</a>, <span class="ident">range</span><span>: </span><a class="type-ref" href="#Range">Range</a><span>)</span><span>: </span><a class="type-intrinsic">void</a></span>
<div class="details collapse" id="details-797">
<div class="comment"><p>Delete the text at the given range.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="uri"></a><span class="ts" id=798 data-target="#details-798" data-toggle="collapse"><span class="ident">uri</span><span>: </span><a class="type-ref" href="#Uri">Uri</a></span></td><td><div class="comment"><p>A resource identifier.</p>
</div></td></tr>
<tr><td><a name="range"></a><span class="ts" id=799 data-target="#details-799" data-toggle="collapse"><span class="ident">range</span><span>: </span><a class="type-ref" href="#Range">Range</a></span></td><td><div class="comment"><p>A range.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-intrinsic">void</a></span></td><td><div class="comment"></div></td></tr>
</table>
</div>
</div>



<a name="WorkspaceEdit.deleteFile"></a><span class="ts" id=818 data-target="#details-818" data-toggle="collapse"><span class="ident">deleteFile</span><span>(</span><span class="ident">uri</span><span>: </span><a class="type-ref" href="#Uri">Uri</a>, <span class="ident">options</span><span>?</span><span>: </span>{ignoreIfNotExists: <a class="type-intrinsic">boolean</a>, recursive: <a class="type-intrinsic">boolean</a>}<span>)</span><span>: </span><a class="type-intrinsic">void</a></span>
<div class="details collapse" id="details-818">
<div class="comment"><p>Delete a file or folder.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="uri"></a><span class="ts" id=819 data-target="#details-819" data-toggle="collapse"><span class="ident">uri</span><span>: </span><a class="type-ref" href="#Uri">Uri</a></span></td><td><div class="comment"><p>The uri of the file that is to be deleted.</p>
</div></td></tr>
<tr><td><a name="options"></a><span class="ts" id=820 data-target="#details-820" data-toggle="collapse"><span class="ident">options</span><span>?</span><span>: </span>{ignoreIfNotExists: <a class="type-intrinsic">boolean</a>, recursive: <a class="type-intrinsic">boolean</a>}</span></td><td><div class="comment"></div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-intrinsic">void</a></span></td><td><div class="comment"></div></td></tr>
</table>
</div>
</div>



<a name="WorkspaceEdit.entries"></a><span class="ts" id=833 data-target="#details-833" data-toggle="collapse"><span class="ident">entries</span><span>(</span><span>)</span><span>: </span>[<a class="type-ref" href="#Uri">Uri</a>, <a class="type-ref" href="#TextEdit">TextEdit</a>[]][]</span>
<div class="details collapse" id="details-833">
<div class="comment"><p>Get all text edits grouped by resource.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts">[<a class="type-ref" href="#Uri">Uri</a>, <a class="type-ref" href="#TextEdit">TextEdit</a>[]][]</span></td><td><div class="comment"><p>A shallow copy of <code>[Uri, TextEdit[]]</code>-tuples.</p>
</div></td></tr>
</table>
</div>
</div>



<a name="WorkspaceEdit.get"></a><span class="ts" id=808 data-target="#details-808" data-toggle="collapse"><span class="ident">get</span><span>(</span><span class="ident">uri</span><span>: </span><a class="type-ref" href="#Uri">Uri</a><span>)</span><span>: </span><a class="type-ref" href="#TextEdit">TextEdit</a>[]</span>
<div class="details collapse" id="details-808">
<div class="comment"><p>Get the text edits for a resource.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="uri"></a><span class="ts" id=809 data-target="#details-809" data-toggle="collapse"><span class="ident">uri</span><span>: </span><a class="type-ref" href="#Uri">Uri</a></span></td><td><div class="comment"><p>A resource identifier.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#TextEdit">TextEdit</a>[]</span></td><td><div class="comment"><p>An array of text edits.</p>
</div></td></tr>
</table>
</div>
</div>



<a name="WorkspaceEdit.has"></a><span class="ts" id=801 data-target="#details-801" data-toggle="collapse"><span class="ident">has</span><span>(</span><span class="ident">uri</span><span>: </span><a class="type-ref" href="#Uri">Uri</a><span>)</span><span>: </span><a class="type-intrinsic">boolean</a></span>
<div class="details collapse" id="details-801">
<div class="comment"><p>Check if a text edit for a resource exists.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="uri"></a><span class="ts" id=802 data-target="#details-802" data-toggle="collapse"><span class="ident">uri</span><span>: </span><a class="type-ref" href="#Uri">Uri</a></span></td><td><div class="comment"><p>A resource identifier.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-intrinsic">boolean</a></span></td><td><div class="comment"><p><code>true</code> if the given resource will be touched by this edit.</p>
</div></td></tr>
</table>
</div>
</div>



<a name="WorkspaceEdit.insert"></a><span class="ts" id=792 data-target="#details-792" data-toggle="collapse"><span class="ident">insert</span><span>(</span><span class="ident">uri</span><span>: </span><a class="type-ref" href="#Uri">Uri</a>, <span class="ident">position</span><span>: </span><a class="type-ref" href="#Position">Position</a>, <span class="ident">newText</span><span>: </span><a class="type-intrinsic">string</a><span>)</span><span>: </span><a class="type-intrinsic">void</a></span>
<div class="details collapse" id="details-792">
<div class="comment"><p>Insert the given text at the given position.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="uri"></a><span class="ts" id=793 data-target="#details-793" data-toggle="collapse"><span class="ident">uri</span><span>: </span><a class="type-ref" href="#Uri">Uri</a></span></td><td><div class="comment"><p>A resource identifier.</p>
</div></td></tr>
<tr><td><a name="position"></a><span class="ts" id=794 data-target="#details-794" data-toggle="collapse"><span class="ident">position</span><span>: </span><a class="type-ref" href="#Position">Position</a></span></td><td><div class="comment"><p>A position.</p>
</div></td></tr>
<tr><td><a name="newText"></a><span class="ts" id=795 data-target="#details-795" data-toggle="collapse"><span class="ident">newText</span><span>: </span><a class="type-intrinsic">string</a></span></td><td><div class="comment"><p>A string.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-intrinsic">void</a></span></td><td><div class="comment"></div></td></tr>
</table>
</div>
</div>



<a name="WorkspaceEdit.renameFile"></a><span class="ts" id=825 data-target="#details-825" data-toggle="collapse"><span class="ident">renameFile</span><span>(</span><span class="ident">oldUri</span><span>: </span><a class="type-ref" href="#Uri">Uri</a>, <span class="ident">newUri</span><span>: </span><a class="type-ref" href="#Uri">Uri</a>, <span class="ident">options</span><span>?</span><span>: </span>{ignoreIfExists: <a class="type-intrinsic">boolean</a>, overwrite: <a class="type-intrinsic">boolean</a>}<span>)</span><span>: </span><a class="type-intrinsic">void</a></span>
<div class="details collapse" id="details-825">
<div class="comment"><p>Rename a file or folder.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="oldUri"></a><span class="ts" id=826 data-target="#details-826" data-toggle="collapse"><span class="ident">oldUri</span><span>: </span><a class="type-ref" href="#Uri">Uri</a></span></td><td><div class="comment"><p>The existing file.</p>
</div></td></tr>
<tr><td><a name="newUri"></a><span class="ts" id=827 data-target="#details-827" data-toggle="collapse"><span class="ident">newUri</span><span>: </span><a class="type-ref" href="#Uri">Uri</a></span></td><td><div class="comment"><p>The new location.</p>
</div></td></tr>
<tr><td><a name="options"></a><span class="ts" id=828 data-target="#details-828" data-toggle="collapse"><span class="ident">options</span><span>?</span><span>: </span>{ignoreIfExists: <a class="type-intrinsic">boolean</a>, overwrite: <a class="type-intrinsic">boolean</a>}</span></td><td><div class="comment"><p>Defines if existing files should be overwritten or be
ignored. When overwrite and ignoreIfExists are both set overwrite wins.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-intrinsic">void</a></span></td><td><div class="comment"></div></td></tr>
</table>
</div>
</div>



<a name="WorkspaceEdit.replace"></a><span class="ts" id=787 data-target="#details-787" data-toggle="collapse"><span class="ident">replace</span><span>(</span><span class="ident">uri</span><span>: </span><a class="type-ref" href="#Uri">Uri</a>, <span class="ident">range</span><span>: </span><a class="type-ref" href="#Range">Range</a>, <span class="ident">newText</span><span>: </span><a class="type-intrinsic">string</a><span>)</span><span>: </span><a class="type-intrinsic">void</a></span>
<div class="details collapse" id="details-787">
<div class="comment"><p>Replace the given range with given text for the given resource.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="uri"></a><span class="ts" id=788 data-target="#details-788" data-toggle="collapse"><span class="ident">uri</span><span>: </span><a class="type-ref" href="#Uri">Uri</a></span></td><td><div class="comment"><p>A resource identifier.</p>
</div></td></tr>
<tr><td><a name="range"></a><span class="ts" id=789 data-target="#details-789" data-toggle="collapse"><span class="ident">range</span><span>: </span><a class="type-ref" href="#Range">Range</a></span></td><td><div class="comment"><p>A range.</p>
</div></td></tr>
<tr><td><a name="newText"></a><span class="ts" id=790 data-target="#details-790" data-toggle="collapse"><span class="ident">newText</span><span>: </span><a class="type-intrinsic">string</a></span></td><td><div class="comment"><p>A string.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-intrinsic">void</a></span></td><td><div class="comment"></div></td></tr>
</table>
</div>
</div>



<a name="WorkspaceEdit.set"></a><span class="ts" id=804 data-target="#details-804" data-toggle="collapse"><span class="ident">set</span><span>(</span><span class="ident">uri</span><span>: </span><a class="type-ref" href="#Uri">Uri</a>, <span class="ident">edits</span><span>: </span><a class="type-ref" href="#TextEdit">TextEdit</a>[]<span>)</span><span>: </span><a class="type-intrinsic">void</a></span>
<div class="details collapse" id="details-804">
<div class="comment"><p>Set (and replace) text edits for a resource.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="uri"></a><span class="ts" id=805 data-target="#details-805" data-toggle="collapse"><span class="ident">uri</span><span>: </span><a class="type-ref" href="#Uri">Uri</a></span></td><td><div class="comment"><p>A resource identifier.</p>
</div></td></tr>
<tr><td><a name="edits"></a><span class="ts" id=806 data-target="#details-806" data-toggle="collapse"><span class="ident">edits</span><span>: </span><a class="type-ref" href="#TextEdit">TextEdit</a>[]</span></td><td><div class="comment"><p>An array of text edits.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-intrinsic">void</a></span></td><td><div class="comment"></div></td></tr>
</table>
</div>
</div>

### <a name="WorkspaceFolder"></a><span class="code-item" id=1804>WorkspaceFolder</span>



<div class="comment"><p>A workspace folder is one of potentially many roots opened by the editor. All workspace folders
are equal which means there is no notion of an active or master workspace folder.</p>
</div>

#### Properties



<a name="WorkspaceFolder.index"></a><span class="ts" id=1807 data-target="#details-1807" data-toggle="collapse"><span class="ident">index</span><span>: </span><a class="type-intrinsic">number</a></span>
<div class="details collapse" id="details-1807">
<div class="comment"><p>The ordinal number of this workspace folder.</p>
</div>
</div>



<a name="WorkspaceFolder.name"></a><span class="ts" id=1806 data-target="#details-1806" data-toggle="collapse"><span class="ident">name</span><span>: </span><a class="type-intrinsic">string</a></span>
<div class="details collapse" id="details-1806">
<div class="comment"><p>The name of this workspace folder. Defaults to
the basename of its <a href="#Uri.path">uri-path</a></p>
</div>
</div>



<a name="WorkspaceFolder.uri"></a><span class="ts" id=1805 data-target="#details-1805" data-toggle="collapse"><span class="ident">uri</span><span>: </span><a class="type-ref" href="#Uri">Uri</a></span>
<div class="details collapse" id="details-1805">
<div class="comment"><p>The associated uri for this workspace folder.</p>
<p><em>Note:</em> The <a href="#Uri">Uri</a>-type was intentionally chosen such that future releases of the editor can support
workspace folders that are not stored on the local disk, e.g. <code>ftp://server/workspaces/foo</code>.</p>
</div>
</div>

### <a name="WorkspaceFolderPickOptions"></a><span class="code-item" id=504>WorkspaceFolderPickOptions</span>



<div class="comment"><p>Options to configure the behaviour of the <a href="#WorkspaceFolder">workspace folder</a> pick UI.</p>
</div>

#### Properties



<a name="WorkspaceFolderPickOptions.ignoreFocusOut"></a><span class="ts" id=506 data-target="#details-506" data-toggle="collapse"><span class="ident">ignoreFocusOut</span><span>?</span><span>: </span><a class="type-intrinsic">boolean</a></span>
<div class="details collapse" id="details-506">
<div class="comment"><p>Set to <code>true</code> to keep the picker open when focus moves to another part of the editor or to another window.</p>
</div>
</div>



<a name="WorkspaceFolderPickOptions.placeHolder"></a><span class="ts" id=505 data-target="#details-505" data-toggle="collapse"><span class="ident">placeHolder</span><span>?</span><span>: </span><a class="type-intrinsic">string</a></span>
<div class="details collapse" id="details-505">
<div class="comment"><p>An optional string to show as place holder in the input box to guide the user what to pick on.</p>
</div>
</div>

### <a name="WorkspaceFoldersChangeEvent"></a><span class="code-item" id=1801>WorkspaceFoldersChangeEvent</span>



<div class="comment"><p>An event describing a change to the set of <a href="#workspace.workspaceFolders">workspace folders</a>.</p>
</div>

#### Properties



<a name="WorkspaceFoldersChangeEvent.added"></a><span class="ts" id=1802 data-target="#details-1802" data-toggle="collapse"><span class="ident">added</span><span>: </span><a class="type-ref" href="#WorkspaceFolder">WorkspaceFolder</a>[]</span>
<div class="details collapse" id="details-1802">
<div class="comment"><p>Added workspace folders.</p>
</div>
</div>



<a name="WorkspaceFoldersChangeEvent.removed"></a><span class="ts" id=1803 data-target="#details-1803" data-toggle="collapse"><span class="ident">removed</span><span>: </span><a class="type-ref" href="#WorkspaceFolder">WorkspaceFolder</a>[]</span>
<div class="details collapse" id="details-1803">
<div class="comment"><p>Removed workspace folders.</p>
</div>
</div>

### <a name="WorkspaceSymbolProvider"></a><span class="code-item" id=744>WorkspaceSymbolProvider</span>



<div class="comment"><p>The workspace symbol provider interface defines the contract between extensions and
the <a href="https://code.visualstudio.com/docs/editor/editingevolved#_open-symbol-by-name">symbol search</a>-feature.</p>
</div>

#### Methods



<a name="WorkspaceSymbolProvider.provideWorkspaceSymbols"></a><span class="ts" id=746 data-target="#details-746" data-toggle="collapse"><span class="ident">provideWorkspaceSymbols</span><span>(</span><span class="ident">query</span><span>: </span><a class="type-intrinsic">string</a>, <span class="ident">token</span><span>: </span><a class="type-ref" href="#CancellationToken">CancellationToken</a><span>)</span><span>: </span><a class="type-ref" href="#ProviderResult">ProviderResult</a>&lt;<a class="type-ref" href="#SymbolInformation">SymbolInformation</a>[]&gt;</span>
<div class="details collapse" id="details-746">
<div class="comment"><p>Project-wide search for a symbol matching the given query string.</p>
<p>The <code>query</code>-parameter should be interpreted in a <em>relaxed way</em> as the editor will apply its own highlighting
and scoring on the results. A good rule of thumb is to match case-insensitive and to simply check that the
characters of <em>query</em> appear in their order in a candidate symbol. Don&#39;t use prefix, substring, or similar
strict matching.</p>
<p>To improve performance implementors can implement <code>resolveWorkspaceSymbol</code> and then provide symbols with partial
<a href="#SymbolInformation.location">location</a>-objects, without a <code>range</code> defined. The editor will then call
<code>resolveWorkspaceSymbol</code> for selected symbols only, e.g. when opening a workspace symbol.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="query"></a><span class="ts" id=747 data-target="#details-747" data-toggle="collapse"><span class="ident">query</span><span>: </span><a class="type-intrinsic">string</a></span></td><td><div class="comment"><p>A non-empty query string.</p>
</div></td></tr>
<tr><td><a name="token"></a><span class="ts" id=748 data-target="#details-748" data-toggle="collapse"><span class="ident">token</span><span>: </span><a class="type-ref" href="#CancellationToken">CancellationToken</a></span></td><td><div class="comment"><p>A cancellation token.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#ProviderResult">ProviderResult</a>&lt;<a class="type-ref" href="#SymbolInformation">SymbolInformation</a>[]&gt;</span></td><td><div class="comment"><p>An array of document highlights or a thenable that resolves to such. The lack of a result can be
signaled by returning <code>undefined</code>, <code>null</code>, or an empty array.</p>
</div></td></tr>
</table>
</div>
</div>



<a name="WorkspaceSymbolProvider.resolveWorkspaceSymbol"></a><span class="ts" id=750 data-target="#details-750" data-toggle="collapse"><span class="ident">resolveWorkspaceSymbol</span><span>(</span><span class="ident">symbol</span><span>: </span><a class="type-ref" href="#SymbolInformation">SymbolInformation</a>, <span class="ident">token</span><span>: </span><a class="type-ref" href="#CancellationToken">CancellationToken</a><span>)</span><span>: </span><a class="type-ref" href="#ProviderResult">ProviderResult</a>&lt;<a class="type-ref" href="#SymbolInformation">SymbolInformation</a>&gt;</span>
<div class="details collapse" id="details-750">
<div class="comment"><p>Given a symbol fill in its <a href="#SymbolInformation.location">location</a>. This method is called whenever a symbol
is selected in the UI. Providers can implement this method and return incomplete symbols from
<a href="#WorkspaceSymbolProvider.provideWorkspaceSymbols"><code>provideWorkspaceSymbols</code></a> which often helps to improve
performance.</p>
</div>
<div class="signature">
<table class="table table-bordered">
<tr><th>Parameter</th><th>Description</th></tr>
<tr><td><a name="symbol"></a><span class="ts" id=751 data-target="#details-751" data-toggle="collapse"><span class="ident">symbol</span><span>: </span><a class="type-ref" href="#SymbolInformation">SymbolInformation</a></span></td><td><div class="comment"><p>The symbol that is to be resolved. Guaranteed to be an instance of an object returned from an
earlier call to <code>provideWorkspaceSymbols</code>.</p>
</div></td></tr>
<tr><td><a name="token"></a><span class="ts" id=752 data-target="#details-752" data-toggle="collapse"><span class="ident">token</span><span>: </span><a class="type-ref" href="#CancellationToken">CancellationToken</a></span></td><td><div class="comment"><p>A cancellation token.</p>
</div></td></tr>
<tr><th>Returns</th><th>Description</th></tr>
<tr><td><span class="ts"><a class="type-ref" href="#ProviderResult">ProviderResult</a>&lt;<a class="type-ref" href="#SymbolInformation">SymbolInformation</a>&gt;</span></td><td><div class="comment"><p>The resolved symbol or a thenable that resolves to that. When no result is returned,
the given <code>symbol</code> is used.</p>
</div></td></tr>
</table>
</div>
</div>

