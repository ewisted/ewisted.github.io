---
layout: post
title:  "An Introduction"
date:   2019-04-09 00:02:22 -0500
categories: blazor setup
---
Although I'd love to jump right into the meat of what this blog will be about, I'm going to instead start this off with a little introduction about my background in development, and what I'll be learning in the coming months.

## A little about me
I started getting into coding with Minecraft mods in Java as well as some small Javascript projects back when I was in high school, but never did anything serious. It wasn't until last year actually that I started coding in C# and .NET Framework after working on a couple projects with some other developer friends. It's actually one of those projects that I'll be working on in this blog, but I'll talk about that a little later. 

Anyways, I noticed pretty quickly how much support Microsoft gives to their developer community through the .NET family. As I'm deep diving a language for the first time, I'm naturally a bit overwhelmed at times with all the different parts that go into a high level framework but Microsoft's documentation is suprisingly easy to read and I have a ton of support from the friends I'm working on these projects with (including one that actually works for Microsoft). Because of this I was able to pick up the core concepts pretty quickly, and since I'm still at a pretty basic level of knowledge, I think blogging about my experience learning this new framework could be useful to someone else who's just starting out and might not have the same support system to help them learn that I do. So I'm going to start with what I'm learning right now, which is Blazor. If you're not familiar with what the Blazor framework is yet, I'd strongly recommend checking out Microsoft's documentation on it [here][blazor-docs].

## The meat
Now that I've that out of the way, we can finally get into that meat I talked about at the beginning of this post. The project I'll be using to apply all this new stuff to is a music streaming syncronization app. We use Spotify's API to remotely control users' spotify players in order to sync them up together in a playback session with a spotify playlist. Those playback sessions can be accessed a couple different ways. One is through the use of Discord's API, which allows us to sync up a playback session to a voice channel. Users can then send commands to our Discord bot to leave, join, change the current playlist, and a lot of other features. The second one is the one we'll be focusing on, and that's the web app. 

Currently, we're using Angular to build our web front-end, and I've learned a good amount about it in the last few months. Luckily for me, Blazor shares some similar concepts to Angular which should make the transition a lot easier. One of the first concepts I ran into while learning Angular are `components`, which are essentially chunks of UI with some Typescript logic behind them that can be injected into web page dynamically. Blazor also has the concept of components in the form of `Razor Components` that function in much the same way. There's still HTML code to build the UI, but C# is used for the logic instead of Typescript. Here's a sample code snippet from Microsoft's example app that shows what that looks like:

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

It's a bit of a shock coming from only having built a front-end in Angular before, but as I read more into the docs, it started to make a lot more sense. So as I understand it, we've got some header text, a paragraph tag that displays an integer, and a button to increment said integer. I learned awhile back that in C# syntax, the `@` symbol is an escape to use a reserved word like `@event`, but here it's used as an escape from HTML to execute C# code. `@page` sets the route for the component and `@functions` is where most of the logic goes. It's also used here within HTML tags to access a variable `currentCount` and call a function `IncrementCount`, which were both instantiated within `@functions`. This is a pretty basic component, but it really helped me break down the core concepts into managable chunks that are a bit easier to understand, which is how I usually try to approach learning things that are completely new to me.

## Conclusion
So yeah, that's pretty much where I'm at with my knowlege of Blazor right now. I'm pretty excited to come back to this initial post in a few months and see how far I've gotten. I'll try to update this blog a few times a week with what I've been working on, and hopefully by the end, I'll have a cohesive journal that documents my experience learning Blazor.

To close out this post, I'm still pretty new to all of this stuff, so if I get anything wrong along the way or if there's somthing I can do better don't hesitate to let me know. Likewise, if there's somthing you're stuggling on that I've already covered, feel free to reach out. Maybe my beginner perspective can make it easier for me to explain it to someone at a similar level. Anyways, thanks for reading this far in and I hope this blog ends up helping your journey into Blazor as well!

[blazor-docs]: https://docs.microsoft.com/en-us/aspnet/core/client-side/spa/blazor/?view=aspnetcore-3.0
[webassembly-docs]:   https://webassembly.org/