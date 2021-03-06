---
layout: post
title: Improving mpv as a music player with Bash/Zsh shell functions
tags: [Linux,Bash,Zsh]
---

This is part of a 3-post series on using mpv and Python
for music organization and playing in Linux and OSX.

+ [Improving mpv as a music player with Lua scripts.][mpv-lua-scripting]
+ [Using Python to organize a music directory.][music-organizer]

[mpv][mpv] is a fork of [mplayer][mplayer] and adds
bug patches, an improved command-line interface, and
an experimental Lua scripting option.
This article provides an overview of Bash/Zsh shell functions
I use to slightly improve mpv's capabilities as a music player.

I source [mpv/shellrc.sh][mpv/shellrc.sh] in my zshrc and bashrc
files to load the following aliases and functions.
These are all contained in my [dotfiles][dotfiles] repository on Github.

+ `mpvnova` uses `mpv` with no video for audio only.
+ `mpvshuf` uses `mpvnova` and infinitely shuffles.
+ `mpvp` uses `mpvshuf` to read a playlist.
+ `playcurrentdir` or `pcd` will use `mpvshuf` to play all the files in the
   current directory tree.
+ `playdir` or `pd` will use `mpvshuf` to play all the files in the
   directories provided on the command line.

{% highlight bash %}
# shellrc.sc
# Source this to add additional shell features for mpv.

alias mpvnova='mpv --no-video'
alias mpvshuf='mpvnova --shuffle --loop inf'
alias mpvp='mpvshuf --playlist'

playcurrentdir() {
  mpvp <(find "$PWD" -type f -follow)
}
alias pcd='playcurrentdir'

playdir() {
  if [[ $# == 0 ]]; then
    echo "playdir requires one or more directories on input."
  else
    if [[ $(uname) == "Linux" ]]; then READLINK=readlink;
    else READLINK=greadlink; fi
    mpvshuf --playlist <(find "$@" -type f -follow -exec $READLINK -f {} \;)
    unset READLINK
  fi
}
alias pd='playdir'
{% endhighlight %}

[dotfiles]: https://github.com/bamos/dotfiles
[mpv]: http://mpv.io
[mplayer]: http://www.mplayerhq.hu

[mpv-lua-scripting]: /2014/07/05/mpv-lua-scripting
[music-organizer]: /2014/07/05/music-organizer/

[mpv/shellrc.sh]: https://github.com/bamos/dotfiles/blob/master/.mpv/shellrc.sh
