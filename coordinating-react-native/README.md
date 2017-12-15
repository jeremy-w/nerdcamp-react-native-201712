# Coordinating React Native
React Native makes it easy to rapidly iterate on a view.

Coordinators reduce the view (along with its view controller) to the "front panel" of a device: there's a display, and a bunch of buttons/actions that make stuff happen elsewhere, but the views and controllers no longer _do_ anything in themselves. They're just an I/O channel.

I have a hunch you can mash these two together in a very nice way.


## Pick a sample project
Let's start with the first example of Coordinators in the wild that I could find: The [Backchannel SDK](https://github.com/backchannel/BackchannelSDK-iOS). The code is MIT-licensed, so as long as we preserve attribution, we can pretty much go hog wild.


## Pick a view controller to convert to React Native
The public entry point is `-[Backchannel presentModallyOverViewController:]`. This stands up the entry point coordinator, `BAKMessagesCoordinator`, with a navigation controller, then tells it to `-start`.

This pushes a `BAKChannelListViewController`. That sounds promising.


## Figure out what the VC looks like and does
Per [its header](https://github.com/backchannel/BackchannelSDK-iOS/blob/6ba01ff72dc64e78aaa3914cc8dba64bebd0cbf7/Source/Channel%20List/BAKChannelListViewController.h), this view controller has one "button", represented by its delegate protocol. The button is pressed when a channel has been selected. (Well, OK, that's more like one button per row, but they all do the same thing.)

It gets some config info and a delegate passed in. I smell `props`; the delegate could be converted to a callback `handleSelectChannel(channel)` and passed in alongside the other config.

It's got a `view` and a `tableView`, and both of them are `UITableView`s. Hmm. And the implementation shows that [`-tableView` just returns `self.view`.](https://github.com/backchannel/BackchannelSDK-iOS/blob/6ba01ff72dc64e78aaa3914cc8dba64bebd0cbf7/Source/Channel%20List/BAKChannelListViewController.m#L46-L48) Go figure. :shrug:

There's a cell class, `BAKChannelCell`, that we'll need to convert into a component. Surprise surprise. Also a custom `BAKChannelHeaderView`.

The cell shows a name and icon. The icon is loaded dynamically. There's some messy state management that I'm going to hope React can smooth over here, around whether it's been loaded already or what.

When the view gets loaded, it stands up a `BAKRemoteDataSource` and hands it a `BAKChannelsRequest` template configured by the `configuration` prop. It also hands the data source a cache name based on the hosting app's bundle ID. For a first pass, let's leave `BAKRemoteDataSource` and friends on the far side of the native bridge, and just expose them to React Native for use from there.


## Get a workbench app up and running
Looks like there's one embedded in the Backchannel SDK. It's used in part as a demo, and in part as a test host for the SDK framework.


## Init the world
Now we need to summon React Native into the repo.


## Create some components
- BAKChannelList
- BAKChannelCell
    - Let's expose this as-is to React Native to demo using a custom native view.
    - Then let's convert this into pure React Native.


## Expose our data source service
We're not showing anything yet. Oops. We aren't actually fetching the data used to populate the table.

We're not going to rewrite that logic in JS just now. Instead, we'll expose the native code as-is to React Native for use from JS.
