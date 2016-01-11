---
layout: post
title: "how to use hidden vnc client on osx"
date: "2016-01-10 19:32:20 -0600"
---

OS X has a built in VNC (Screen Sharing) server, but it is a little known feature that it also has a built in VNC client. No additional software is necessary.

To setup the server go to Preferences->Sharing and check the "On" checkbox next to "Screen Sharing". Make sure to only allow access to Administrators or a specific user/user group.

![Screen Sharing](https://www.evernote.com/shard/s11/sh/3fc136cf-b5eb-4493-a847-77ece45ee078/e7f6f5cb5c5e9109/res/9496f235-b7bc-43e6-8aba-d82447759a3e/skitch.png?resizeSmall&width=832)

You should see your computer name on the Screen Sharing configuration screen.
To connect to your computer from another one using VNC open Finder and press `⌘+k`. In the Server Address enter `vnc://` followed by your computer name or IP address. 
![Connect To Server](https://www.evernote.com/shard/s11/sh/707ca29d-4e63-42c8-93eb-7cde2c804994/affda38f2119864c/res/624fab17-eb19-4357-89be-81e7f33fb1e6/skitch.png?resizeSmall&width=832)

h/t Dillon Hafer


