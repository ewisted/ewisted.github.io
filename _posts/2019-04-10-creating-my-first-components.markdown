---
layout: post
title:  "Creating My First Components and Establishing Routing Between Them"
date:   2019-04-10 01:02:22 -0500
categories: blazor
---
Let me start this off by saying I'm not the one who initially set up Blazor in our Synthbot project, since it's stuctured as an enterprise application that runs on Azure, and that's not the best place to start for someone who's still relatively new to web apps. So one of the other devs got Microsoft's Blazor example running within Synthbot for me to run wild with. With that in mind, I'll be building off the example app as I go in order to have a template to follow while getting started. [Here's a link][blazor-getting-started] to build the starter example from Microsoft's ever-so-helpful docs on Blazor.

With that out of the way, lets get to the content of this post.

## Goal
As I mentioned in my introduction post, Synthbot has the concept of playback-sessions. These sessions are the core of the app and are the first piece of data I want to make a Blazor page for. The goal for the day is to establish a page that lists all playback sessions and a nested page for each individual session's info that can be routed to. Eventually this will be changed to show only the authenticated user's accessible playback sessions, but for now, all playback sessions that are active will do. Baby steps right?

## Creating the components
The first component to be created is the `SessionsList` component, which will display a list of all active playback sessions in a table. Conventiently, the example app already has a component that displays a list of data in a table. [Here's a link to it in the docs][weather-component].

This does exactly what I'm trying to do with playback sessions, but for weather forcasts. Since Microsoft knows better than I do how to display data in their framework, I'm not going to stray too far off the beaten path here. All I need to do is change the controller method we're hitting to a controller method that gives us playback sessions instead of weather forcasts, which was already set up for the Angular App. Then I need to change the table header and the data displayed to match the new model. So I went ahead and did that and here's the result:

{% highlight cs %}
@page "/playback-sessions"
@using Synthbot.Blazor.Shared
@inject HttpClient Http

<h1>Playback Sessions</h1>

<p>These are all the playback sessions from the db</p>

@if (sessions == null)
{
	<p><em>Loading...</em></p>
}
else
{
	<table class="table">
		<thead>
			<tr>
				<th>Id</th>
				<th>Name</th>
				<th>Playlist URI</th>
				<th>Now Playing Song URI</th>
			</tr>
		</thead>
		<tbody>
			@foreach (var session in sessions)
			{
				<tr>
					<td>@session.Id</td>
					<td>@session.Name</td>
					<td>@session.SpotifyPlaylistUri</td>
					<td>@session.CurrentSongPlayback?.SpotifySongUri</td>
				</tr>
			}
		</tbody>
	</table>
}

@functions {
	PlaybackSession[] sessions;

	protected override async Task OnInitAsync()
	{
		sessions = await Http.GetJsonAsync<PlaybackSession[]>("services/playbacksvc/api/PlaybackSession/GetPlaybackSessions");
	}
}
{% endhighlight %}

Yeah I know this is pretty much a copy-paste from the docs, but it'll serve as a good starting point to build off of. I'll leave this as is for now and move on to the next component, which will be the `SessionInfo` component. I want this to be nested within `SessionsList`, so I modeled the pages directory to mirror that. 

```
C:\SYNTHBOT\SYNTHBOT.BLAZOR.CLIENT\PAGES
│   Counter.cshtml
│   FetchData.cshtml
│   Index.cshtml
│   _ViewImports.cshtml
│
└───PlaybackSessions
    │   SessionsList.cshtml
    │
    └───Session
            SessionInfo.cshtml
```

I want to focus on getting the core fuctionality setup rather than the design for now, so all `SessionInfo` will display is the currently playing track in the playback session. But before I can display anything I need a way to tell `SessionInfo` what session to display. Thankfully, there's another feature Blazor shares with Angular, and that's routing.

## Getting routing working
Back in the Angular App, the way I handled this was to setup an `onclick` listener on each table row that would call a Typescipt function to route the user to the session page for the row they clicked on. In Blazor, I can implement this in almost the same way. 

### Routing in code
The first step is to create a new method in the functions section of `SessionsList` to route the user to the `SessionInfo` component. It took me awhile to figure out how to do this in C# code, but after searching for awhile I came across [this page][uri-helper] that shows how to do it using an `IUriHelper` interface from an obscure, undocumented (as of the writing of this) WebUtilities nuget package provided by Microsoft. It was here that I experienced my first real frustration with how new Blazor is. Since the writing of that page, Microsoft changed `IUriHelper` into a `WebAssemblyUriHelper` class. Since there was little to no documentation on this package, it took me awhile to figure that out. But thankfully, once I did, everything worked the way it was supposed to. Here's what I ended up adding to `SessionsList`.

{% highlight cs %}
...
@inject Microsoft.AspNetCore.Blazor.Services.WebAssemblyUriHelper UriHelper

...

@functions {
    ...

    private void GoToSession(string id)
	{
		UriHelper.NavigateTo($"/session-info/{id}");
	}
}
{% endhighlight %}

Before I move on to the next step, I just want to clarify just in case it's not obvious that the `id` parameter passed into this new `GoToSession` function will be the id of the session we want to route the user to. 

### The onClick listener
Next, I need to set up the onClick listener for each table row in the body of the table. Since the rows are generated in a loop, I only needed to change one line of code to accomplish this. My first reaction was to call the method the same way other parts of the example do, by escaping the html with `@` and call `GoToSession` directly. This didn't work however, since I could only figure out how to call functions that return void that way. The solution I came up with was to call it inside a lambda function, which worked. Here's what that looks like.

{% highlight cs %}
<tbody>
    @foreach (var session in sessions)
    {
        <tr onclick=@(e => GoToSession(session.Id))>
            <td>@session.Id</td>
            <td>@session.Name</td>
            <td>@session.SpotifyPlaylistUri</td>
            <td>@session.CurrentSongPlayback?.SpotifySongUri</td>
        </tr>
    }
</tbody>
{% endhighlight %}

### Passing the session id to the session info component
The last thing I need to do to get this functioning is getting the session id into `SessionInfo` when the user is routed there. This way, the component will act as a template to display info for whatever playback session the user is interested in. Blazor actually makes this part really simple. We can tell `SessionInfo` to expect a session id in its page definition like so `@page "/session-info/{Id}"`. From here, I just need to define a private field with a `[Parameter]` attribute. As long as it has the same name as what I typed in the brackets for the page definition, Blazor will automatically set the field's value to the id that was passed in. The code for `SessionInfo` is pretty basic but here it is anyway.

{% highlight cs %}
@page "/session-info/{Id}"
@using Synthbot.Blazor.Shared
@inject HttpClient Http

<h1>Playback Session Info</h1>

<p>Some info on the selected playback session.</p>

@if (session == null)
{
	<p><em>Loading...</em></p>
}
else
{
	<p>Now Playing</p>
	<p>@session.CurrentSongPlayback.SpotifySongUri</p>
}

@functions {
	[Parameter]
	string Id { get; set; }

	PlaybackSession session;

	protected override async Task OnInitAsync()
	{
		session = await Http.GetJsonAsync<PlaybackSession>($"services/playbacksvc/api/PlaybackSession/GetPlaybackSessionById?sessionId={Id}");
	}
}
{% endhighlight %}

## The result
Here's a little preview of what the (not-so) finished product looks like.

![](/assets/2019-04-10-creating-my-first-components/result.gif)

## Conclusion
Well, the design is horrid for now obviously, but the important thing is that we can now navigate from component to component in C# code. That's what this individual post was about, so this is a good spot to leave off on until the next post, in which I'll start actually filling out these two components. But that's enough progress for one day, see ya in the next post.

[blazor-getting-started]: https://docs.microsoft.com/en-us/aspnet/core/client-side/spa/blazor/get-started?view=aspnetcore-3.0&tabs=visual-studio
[weather-component]: https://docs.microsoft.com/en-us/aspnet/core/tutorials/build-your-first-razor-components-app?view=aspnetcore-3.0#dependency-injection
[uri-helper]: https://learn-blazor.com/pages/router/#accessing-query-parameters