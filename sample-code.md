---
layout: page
title: Sample Code
permalink: /sample-code/
---

You can get a pretty good idea of how to write programs for AtomVM by looking at examples.  This page has a few to wet your appetite!

> Note. A passing familiarity with Erlang syntax would be helpful for following along, but most readers familiar with curly-brace programming can use common sense when reading the code snippets below.  For anyone who needs or wants a tutorial introduction to Erlang, we can't recommend [Learn you some Erlang for Great Good](https://learnyousomeerlang.com) enough.

## Hello, World!

Okay, we can't have an set of example programs without including good ol' `Hello world!`.

    %% erlang
    -module(hello_world).

    -export([start/0]).

    start() ->
        io:format("Hello world!~n").

Erlangers will be familiar with module declarations.  This tells the compiler what the name of the module is.

You also need to tell the compiler which functions to export.  This is given as list of `function-name/arity` expressions, where `arity` is the number of arguments to the function.  In this case, we export the `start` function with 0 arguments.

All AtomVM programs must contain a module with an exported `start/0` function.  This is the entrypoint into the program, where the AtomVM virtual machine will begin executing the program.

And in this case, the function just prints "Hello world!" to the console, and exits.  (The `~n` tells AtomVM to append a newline to the end of the output, but I bet you guessed that already.)

## Blinky

The "Hello world!" of the IoT world is "blinky", a program that toggles an LED on and off, every second.  It is a good program to use to test basic functionality of an application.

    -module(blinky).
    -export([start/0]).

    -define(PIN, 2).

    start() ->
        gpio:set_pin_mode(?PIN, output),
        loop(?PIN, low).

    loop(Pin, Level) ->
        io:format("Setting pin ~p ~p~n", [Pin, Level]),
        gpio:digital_write(Pin, Level),
        timer:sleep(1000),
        loop(Pin, toggle(Level)).

    toggle(high) ->
        low;
    toggle(low) ->
        high.

Like the Hello World program, this program declares a module and exports the `start/0` function.

Many development boards come with an LED on GPIO pin 2, so let's assume we are working with such a board, or that we have attached an LED (and accompanying resistor) to GPIO pin 2.

We set the pin mode of pin 2 to `output`, so that we can change the pin's value:

    gpio:set_pin_mode(?PIN, output)

We then enter the "loop" in the `loop` function.  We start by setting the pin to the specified level via the `gpio:digital_write/2` function, sleeping 1000 milliseconds (1 second), and then calling the `loop` function recursively, passing in the toggled value of the input level:

    loop(Pin, Level) ->
        io:format("Setting pin ~p ~p~n", [Pin, Level]),
        gpio:digital_write(Pin, Level),
        timer:sleep(1000),
        loop(Pin, toggle(Level)).

The toggling between high and low is done via the `toggle` function, which simply flips the value from `high` to `low`, and conversely:

    toggle(high) ->
        low;
    toggle(low) ->
        high.

For newcomers to Erlang, this function definition may look strange.  There are two definitions of the same function!  How can that be?  Well, in Erlang, the parameters that match the head of the function definition will be the portion of the function that will be executed.  This is exactly the way we define many functions in mathematics.

Newcomers to Erlang may also be concerned that this function is a recursive function that loops forever.  Won't the program crash, because it will run out of space on the stack?

Again, this problem has been solved now for decades, going back to the early years of functional programming languages.  This function is known as a _tail recursive_ function, meaning that the last function call in the function definition is a recursive call to itself.  In this case, the compiler knows that the function is tail recursive, and instead of pushing a new frame onto the runtime stack, the

> Exercise for the reader: What about data that is local to the function?  Doesn't a tail recursive function call create a memory leak?  Explain why not.  (Hint: Is any data that is not passed into the tail recursive function as a parameter of any value?)

## Blinky2

The `blinky` program is nice and small, but there is a small problem with it.  The program runs in a non-terminating loop (which in itself, is fine), but the AtomVM VM is stuck in that loop, and can't do anything else.  How do we get our programs to do more than one thing at a time?

For this, we need to spawn our functions in what are called Erlang processes.  Processes are not true operating system-level processes, but they share some of the same properties as OS-level processes, and in many ways AtomVM provides a very OS-like environment for your application.

Like an operating system schedules OS processes for execution on a CPU, the AtomVM virtual machine schedules Erlang processes independently from one another and executes them in parallel.  You can therefore spawn multiple processes, and the AtomVM virtual machine will _pre-emptively_ execute the processes in "parallel", even if it means time-slicing multiple processes on a device with a single CPU core.

The change to the above program is very simple.  We already have the `toggle/3` function defined.  All we need to do is spawn the function in a new process.  Syntactically, this looks like the following:

    spawn(fun() -> toggle(2, 1000, low) end)

There are several variants of the built-in `spawn` function, but the above example takes an anonymous function, which just calls the `toggle/3` function with the desired parameters.

Let's pretend we have another LED attached to pin 4.  We can spawn another function that toggles that LED at a different time interval (say, half a second):

    spawn(fun() -> toggle(4, 500, low) end)

We now have two separate functions running in parallel, once toggling the LED on pin 2 once a second, and the other toggling the LED on pin 4 every 500ms.

We don't want the program to terminate, because the processes are running in the background, so we tell the `start/0` function to just sleep forever after it has spawned the two processes:

    timer:sleep(infinity)

Here is the full program, in its entirety:

    %% erlang
    -module(blinky2).

    -export([start/0]).

    start() ->
        gpio:set_pin_mode(2, output),
        gpio:set_pin_mode(4, output),
        spawn(fun() -> toggle(2, 1000, low) end),
        spawn(fun() -> toggle(4, 500, low) end),
        timer:sleep(infinity).

    toggle(Pin, SleepMs, low) ->
        gpio:digital_write(Pin, low),
        timer:sleep(SleepMs),
        toggle(Pin, SleepMs, high);
    toggle(Pin, SleepMs, high) ->
        gpio:digital_write(Pin, high),
        timer:sleep(SleepMs),
        toggle(Pin, SleepMs, low).





## More to come!
