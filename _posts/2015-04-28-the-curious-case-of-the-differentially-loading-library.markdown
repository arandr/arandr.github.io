---
title: The Case Of The Differentially Loading Library
layout: default
type: post
---


This morning I had a problem with testing the shared library my project depends on. Specifically, when linking my project with Google Test and the library, my test using a library function hung forever. 
However, if I loaded the library explicitly at runtime, looked for the symbol for the function, and called it, the test passed.

Understanding the why of this requires some explanation of shared libraries.

##What shared libraries are##

A library is a set of programming building blocks (usually functions) that an application can leverage. A shared library is the same, except several applications can use 
the same library -- that is, share the same code, and the same code file mapped in memory, for that library. This is especially useful for system libraries, that by definition need 
to be shared by several applications, but it is a reasonably common way to package code that one may wish to reuse across several programs, or use as a plug-in.

While static (non-shared) libraries are physically linked within the executable file, and treated by the runtime system as integral parts of their executable, this is not the case of shared 
libraries, which are treated as separate entities and placed within their own file, often within a commonly-accessible location where the system will know where to find them the next time 
they're needed. This may or may not have [licensing implications](http://programmers.stackexchange.com/questions/167773/how-does-the-gpl-static-vs-dynamic-linking-rule-apply-to-interpreted-languages).

How does the system know a shared library is needed? It turns out there are two ways (and this works somewhat similarly on Windows and Linux, but unless specifically mentioned, I am mostly talking about Windows. 
A good overview of shared libraries under Linux is [this](http://www.ibm.com/developerworks/library/l-dynamic-libraries/), and [this](http://www.akkadia.org/drepper/dsohowto.pdf) has great detail). The first way, called
 implicit or load-time linking, uses the shared library not too differently from a static one. You tell the linker to use this library, and it will place within your executable a reference to the name of your shared library. 
 That way, when the executable is run, the shared library will be fetched by the OS and made accessible to your process. The address for any function called by your process will also be fetched and resolved. If a shared library 
 linked in that way is not found when the process is run, the process will crash.

The second provides you an interface where you tell the OS, at runtime, that your program will be using this library and you want to know the address to that function. Since it is more manual and fiddly, it is usually used only 
when you have a legitimate reason to want to load the library manually (for example, you may not want to load it in all code paths; or it may be a plugin and you may want to detect if it exists or not on your user's machine to 
prevent a crash). When you call a function to load the library, it will resolve the path in the same way it would have were the library linked in, except if your library is not found, an error code will be returned instead. This 
means that in this method you can recover from DLLs not found.

Now, that's good, but the choices here are between crash and error, not between hang and perfectly smooth execution.
So let's talk a bit more about shared libraries.

##Program entry points##

Executables have entry points. For example, in C and C++ the function main() is associated with the executable's entry point. If you are compiling an executable, and you 
have a main function, this function will be where the program execution starts.

Well, shared libraries also have entry points. Usually the entry points are intended for initialization code that is needed for the whole library and will be called when a 
program loads it. (Creating global variables, loading configurations,etc.)

If using implicit linking, all the libraries on which the executable depends will be loaded and their initialization functions called as soon as the program starts 
(and before the program's code is run), and the order in which this happens is not configurable. If using explicit linking, the entry point will be called after the LoadLibrary() or 
dlopen() call. Those calls can recover from the entry point not being found (and will return an error and unload the library), whereas the entry point not being found will lead to a crash 
if the executable used implicit linking. On Windows, if your entry point function returns false, the same thing will happen.

But that's not the end of it.

The usual recommendation is your entry point should do as close to nothing as possible. On Windows, for example, you should avoid pretty much anything to do with synchronization, and 
of course, you [shouldn't load another library](https://msdn.microsoft.com/en-us/library/windows/desktop/dn633971%28v=vs.85%29.aspx). You also shouldn't exit from the current thread.

##The solution##

As it turns out, whoever wrote that particular entry point assumed it would never be loaded using load-time linking. 
(This was a fair assumption -- to be fair, our codebase has a pretty important plugin architecture, and people usually use delayload 95% of the rest of the time.) 
However, in test frameworks using run-time linking is a giant pain and delay-loading a specific library means your test suite has to link differently from the other ones just 
because that one DLL flies in the face of Microsoft's own recommendations for What Not To Do In A DLL Entry Point. 

Therefore, when the entry point's entirely superfluous assert failed at load time, the process stayed there, deadlocked. When using run-time linking, the test code above the 
LoadLibrary call had initialized some variables correctly, so that that one assert no longer failed, and everything ran fine.

Solution: Don't write asserts in entry points.    