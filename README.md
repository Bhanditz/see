see
===

see.js: See and debug local variables.

The see.js interactive eval panel can be used to inspect
and change local variable state within nested scopes and
closures without stopping at breakpoints first.  This
package also provides a simple in-page logging facility
with tree view inspection of objects, and it provides support
for both JavaScript and CoffeeScript.

Overview
--------

Any local scope can be debugged by calling eval(see.init())
within the scope of interest.  For example, the following nicely
encapsulated code would normally be painful to debug without the
added see line:

<pre>
(function() {
  var private_var = 0;
  function myclosuremaker() {
    eval(see.init());
    var counter = 0;
    return function() { ++counter; }
  }
  var inc = myclosuremaker();
  inc();
})();
</pre>

When see.init() is called, it shows a debugging eval panel at
the bottom of the page, and it returns a bit of script that,
when evaled, sets up an eval closure in the current scope.

The debugging eval panel works like the Firebug or Chrome debugger
console, except that it has visibility into local variable scope.
In this example, "counter" and "private_var" and "inc" and
"myclosuremaker" will all be visible symbols that can be used and
manipulated.

It is also possible to attach to multiple scopes with a single program
by adding the following at the scope of interest:

<pre>
eval(see.scope('scopename'));
</pre>

To switch scopes within the interactive panel, just enter ":" followed
by the scope name, for example, ":scopename".  ":top" goes to global
scope, and ":" goes back to the default scope defined at init.

![Screenshot of see panel](see-usage.png?raw=true)

The see.js script originally started as a teaching tool in a
CoffeeScript environment, so it also supports use of CoffeeScript
as the console language.  Here it how to initialize see.js to
interpret code entered in the panel as CoffeeScript instead of
Javascript:

<pre>
see.init(eval(see.cs))
</pre>

The top-level see function logs output to the see panel.  Logged
objects are shown in a tree view of the object state at the
moment when the object is logged.

<pre>
see(a, b, c);
</pre>


Detailed usage
--------------

<pre>
see.init();               // Creates the interactive panel.
see.init({height: 30, title: 'test panel'});   // Sets some options.
eval(see.init());         // Sets the default scope to local scope.
eval(see.scope('name'));  // Type ":name" in the panel to use this scope.
see(a, b, c);             // Logs values into the panel.
see.loghtml('&lt;b>ok&lt;/b>'); // Logs HTML without escaping.
r = see.repr(a, 3);       // Builds a tree representation of a to depth 3.
x = see.noconflict();     // Relinguishes use of the 'see' name; use 'x'.
</pre>

Options to pass to init
-----------------------
<table>
<tr><td>eval</td><td>The default function (or closure) to use to evaluate expressions.</td></tr>
<tr><td>this</td><td>The object to use as "this" within the evaluation.</td></tr>
<tr><td>depth</td><td>The depth to which to traverse logged and evaluated objects.</td></tr>
<tr><td>height</td><td>The pixel height of the interactive panel.</td></tr>
<tr><td>title</td><td>A title shown at the top of the panel.</td></tr>
<tr><td>panel</td><td>false if no interactive panel is desired.</td></tr>
<tr><td>console</td><td>Set to window.console to echo logging to the console also.</td></tr>
<tr><td>history</td><td>Set to false to disable localStorage use for interactive history.</td></tr>
<tr><td>linestyle</td><td>CSS style for a single log line.</td></tr>
<tr><td>element</td><td>(if panel is false) - The element into which to logging is done.</td></tr>
<tr><td>autoscroll</td><td>(if panel is false) - The element to autoscroll to bottom.</td></tr>
<tr><td>jQuery</td><td>A local copy of jQuery to reuse instead of loading one.</td></tr>
<tr><td>coffee</td><td>The CoffeeScript compiler object, for coffeescript support.</td></tr>
<tr><td>noconflict</td><td>Name to use instead of "see".</td></tr>
</table>

Some implementation notes on interactions
-----------------------------------------

When see.init() is called, a private (noconflict) copy of jQuery is
loaded if jQuery is not already present on the page (unless the 'panel'
option is false, or the 'jQuery' option is explicity supplied).

Every expression entered in the panel is stored to '_loghistory' in
localStorage unless the 'history' option is set to false.
