---
layout: post
title:  "An Introduction"
date:   2019-04-09 00:02:22 -0500
categories: blazor setup
---
Although I'd love to jump right into the meat of what this blog will be about, I'm going to instead start this off with a little introduction about me, my background in development, and why I think Blazor is such a great framework to start experimenting with right now.

## A little about me
I started getting into coding with Minecraft mods in Java as well as some small Javascript projects back when I was in high school. It wasn't until last year actually that I made the switch over to C# and .NET Framework as my language of choice after working on a couple projects with some other developer friends. It's actually one of those projects that I'll be working on in this blog, but I'll talk about that a little later. 

Anyways, I noticed pretty quickly how much support Microsoft gives to their developer community through the .NET family. I've grown to love almost every aspect of it, from Visual Studio with its powerful intellisense tool, to LINQ and Entity Framework. But the biggest addition that I'm most excited about right now is Blazor. If you're not familiar with what the Blazor framework does yet, I'd strongly recommend checking out Microsoft's documentation on it [here][blazor-docs]. But I'll include a small summary in case that's too much reading.

## A short summary on Blazor and WebAssembly
Blazor uses a new technology called [WebAssembly][webassembly-docs] to run C# code in the client's browser. WebAssembly will change the way we write code for the browser by allowing us to compile high-level languages like C# into a standardized low level assembly language built specifically for the web. This will remove the need to write javascript completely (thank god) and build front-ends in the same language used for the back-end. Think of all the benefits that gives to the development flow; Models can be shared between all parts of an application without the need for a client to translate them to TypeScript interfaces, nuget packages can now be used on the front-end alongside any important npm packages that don't exist on nuget yet (yep, you can still use those in Blazor), and any C# classes from other parts of the application can theoretically be injected and used in Blazor with the same dependancy inversion syntax used everywhere else. All in all, it provides much needed consistancy to the development of web applications as a whole.

## The meat
Now that I've (hopefully) sold you on why Blazor is so great, we can finally get into that meat I talked about at the beginning of this post. The project I'll be using to apply all this new stuff to is a music streaming syncronization app. We use Spotify's API to remotely control users' spotify players in order to sync them up together in a playback session with a spotify playlist. Those playback sessions can be accessed a couple different ways. One is through the use of Discord's API, which allows us to sync up a playback session to a voice channel. Users can then send commands to our Discord bot to leave, join, change the current playlist, and a lot of other features. The second one is the one we'll be focusing on, and that's the web app. 

Currently, we're using Angular to build our web front-end, but luckily, Blazor shares some similar concepts to Angular which should make the transition a lot easier. One of the first concepts you run into while learning Angular are `components`, which are essentially chunks of UI with some Typescript logic behind them that can be injected into web page dynamically. Blazor also has the concept of components in the form of `Razor Components` that function in much the same way. You have your HTML code to build the UI, but instead of using Typescript for the logic, you use C#. Here's a sample code snippet from Microsoft's example app that shows what that looks like:

{% highlight cs %}
@page "/counter"

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" onclick="@IncrementCount">Click me</button>

@functions {
	int currentCount = 0;

	void IncrementCount()
	{
		currentCount++;
	}
}
{% endhighlight %}

Alright, I'll dive in to a little of whats going on here. As you can see, we've got some header text, a paragraph tag that displays an integer, and a button to increment said integer. If you're familiar with C# syntax, you'll know that the `@` symbol is an escape to use a reserved word like `@event`. Here it's used as an escape from HTML to execute C# code. `@page` sets the route for the component and `@functions` is where most of the logic goes. It's also used here within HTML tags to access a variable `currentCount` and call a function `IncrementCount`, which were both instantiated within `@functions`. This is a really basic example, but it demonstrates the core design model pretty well.

## Conclusion
To close out this post, I won't go into the specifics on the core architecture of how Blazor interacts with these components as Microsoft's documentation will do a much better job of that than I can and this post is already getting long, but it's still important to know. So if you haven't already check out those docs and in the coming posts I'll detail how I go about converting our Angular app into a shiny new Blazor app. If you have any questions or feedback (I'm far from perfect) along the way, feel free to send me an email (should be at the bottom of the page). Anyways, thanks for reading this far in and I hope this blog ends up helping your journey into Blazor as well!

[blazor-docs]: https://docs.microsoft.com/en-us/aspnet/core/client-side/spa/blazor/?view=aspnetcore-3.0
[webassembly-docs]:   https://webassembly.org/