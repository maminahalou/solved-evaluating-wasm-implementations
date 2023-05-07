Download Link: https://assignmentchef.com/product/solved-evaluating-wasm-implementations
<br>
<h1>Required Code Generator Changes</h1>

<h2>Program Termination</h2>

In order to terminate the program when a precondition, postcondition, or loop invariant check fails, a new genUnreachable() function must be exported from the code generator modules. This function will simply append the <a href="https://developer.mozilla.org/en-US/docs/WebAssembly/Reference/Control_flow/unreachable">unreachable</a> instruction to the WASM code. Behaviour for the AST generator (CGast) is up to the implementor to determine.

<strong>Sections System</strong>

A system must be created to generate parsed code into a buffer so it can be used elsewhere. To accomplish this, we can temporarily write all WASM lines from the code generator to “sections” stored in a dictionary that can be used later in other places.

My suggested data structure for this is a global dictionary of arrays. A global variable can be used to track the current section that is being written to. For example:

activeSection = “a1b2c3d4”

sections = { “a1b2c3d4”: [“i32.const 5”, “local.set $0”] }

The following functions must be exported from the code generator modules:

<ul>

 <li>startSec(): Asserts that there is not an active section. Generates a unique id, creates a new section at that id, sets it to be the active section, and returns the id.</li>

 <li>endSec(): Asserts that there is an active section. Clears the global active section variable.</li>

 <li>hasSec(id): Returns whether the specified section exists in the sections dictionary.</li>

 <li>useSec(id): Writes the specified section using the write(wasm) function created below.</li>

 <li>deleteSec(id): Asserts that the specified section is not the active section. Deletes the specified section from the sections dictionary.</li>

</ul>

For this system to work the current asm.append(wasm) and asm.extend(wasm) calls must be replaced with a function that writes lines to the active section if it exists or mirror the existing behaviour otherwise. A suggested function signature is write(wasm) where wasm is a string or list of strings.

One limitation of this system is that nested sections are not allowed. The system could be extended in the future to include this capability if desired.


