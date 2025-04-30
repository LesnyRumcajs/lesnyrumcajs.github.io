---
layout: post
title:  "Pulldown terminal in Linux"
date:   2019-09-16 16:00:00 +0200
categories: linux
---
## Motivation
My preferred setup is (and was, for the long time) a pulldown terminal I can call with some key binding. Because of this, for some time I was stuck with terminal emulators that support this either out of the box or via plugins.

Some of the emulators I used:
* Linux: Tilix or Guake
* Windows: ConEmu or Cmder
* MacOS: iTerm2

There is a lot of them but still, such whim comes with a cost of limited selection. Moreover, if the terminal emulator is not cross-platform you may end up having to work with different terminals on different machines (which was the case for me). Having to memorize all the shortcuts, config features and quirks is a real nuissance.

Enough rant. Onto solution!
## Solution
As a cross-platform terminal emulator I use [kitty][kitty]. I am not going to advertise it, all that matters in this case is that I can use the same (or almost the same) config on both Linux Fedora 30 and MacOS.

To create a global shortcut I used [sxhkd][sxhkd] with [tdrop][tdrop]. `sxhkd` should be available in major repositories. As for `tdrop` I had to just clone and install it.
Add the following config to `~/.config/sxhkd/sxhkdrc`:
```
ctrl + super + k
  tdrop -ma -w -4 -y "$PANEL_HEIGHT" -s dropdown kitty
```
See the `-s` flag in the configuration? It will immidiately start a `tmux` session. Some terminal emulators may not support it though.

Then put an entry for `autostart` in `~/.config/autostart/sxhkd-agent.desktop`:
{% highlight ini %}
[Desktop Entry]
Type=Application
Encoding=UTF-8
Name=SXHKD Agent
Exec=/usr/bin/sxhkd
Terminal=false
{% endhighlight %}

Log-out, log-in.
## Result
<kbd><img src="/assets/img/posts/2019/pulldown-kitty.png" alt="Pulldown terminal in Linux" /></kbd>

Done! A nice kitty terminal available with consistent `ctrl + super + k`. Available anywhere, anytime. No special GNOME plugins for every terminal required. `ctrl + shift + f10` to make it fullscreen.

[kitty]: https://sw.kovidgoyal.net/kitty
[sxhkd]: https://github.com/baskerville/sxhkd
[tdrop]: https://github.com/noctuid/tdrop
