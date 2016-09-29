---
title: Bringing bullshit to Arch Linux
layout: post
date: 2016-09-29 08:25:00 +0100
category: foss
tags:
    - Arch Linux
    - Open Source
---
On September 3, I was hanging around in the [\#archlinux-offtopic][irc-archlinux-offtopic] IRC channel, and when [chee][github-chee] came up with the idea to make a Linux program which would print a random imgur link, I asked for more fascinating ideas like that one. I think I might actually realize that first idea at some point, but I'll save that for later. Anyway, chee also pointed me to this script simply called [bullshit][man-bullshit], originally written as part of the exotic [9front][9front-homepage] operating system, and I wanted this on my system, so I figured I might as well port it. Here is what it does:

```
$ bullshit
managed virtual API out-scaling backend
```

Bullshit's [original source code][githubusercontent-rc/bin/bullshit] was written for the [Plan 9 shell][man-rc] called rc, but the script really mostly consists of a 50-line [awk][man-awk] command, which I could obviously keep without modification, while the remaining five lines were one line of shebang, and four lines of setting a variable. That was quite easily ported, the script now looks like [this][githubusercontent-sh/bin/bullshit], I installed this locally, had some fun, and from then on the whole thing remained largely untouched for a couple of weeks.

Some weeks later I was at a local [chaos][ccc] gathering, and after talking for a while about all kinds of silly programs, a guy who goes by he handle of djerun (who was running Arch Linux as well) also wanted this script. So this was when I created an Arch Linux package for the first time in my life.

Writing an Arch Linux package can be quite straightforward. I found some unrelated PKGBUILD in a remote corner of my file system, copied it over to my source directory, put the two source files needed for bullshit into a subdirectory called `src`, and then edited the file so that it looked like [this][githubusercontent-old-PKGBUILD]. I changed the `pkgname`, added a more or less sensible `pkgdesc`, changed the `url`, and replaced the install command of the old PKGBUILD with two for my new files. You can really tell from the first line that this was really just a quick hack done without much sophistication. Mind you, I should have done this differently.

So I uploaded this to github, and everything was fine, djerun got the package and everyone was happy, or maybe not everyone, but at least the both of us.

Except the next afternoon I resolved making this package available for everyone in the [Arch Linux AUR][aur].

Of course I had to rework the PKGBUILD file. Most of it was pretty easy. In this particular case, I could not find any sensible license for this script. I saw that some of 9front has been licensed under GPL v2, so I made GPL v2 happen. The other licenses floating around in the atmosphere of that operating system, by the way, have been [quite][plan9-stallman] [awkward][lucent-ycombinator]. Anyway, my PKGBUILD now looked like this:

```sh
# Maintainer: Fabian Schmidt <fceschmidt at posteo dot de>
pkgname=bullshit
pkgver=0.0
pkgrel=1
pkgdesc="Assemble a stream of technology bullshit from words in a file"
arch=('any')
url="https://github.com/fceschmidt/bullshit-arch"
license=('GPL2')
depends=('gawk')
source=('https://raw.githubusercontent.com/fceschmidt/bullshit-arch/master/src/bullshit.tar.gz')
md5sums=('70f68f9ae1758f4e7677b2a5d141ee7b')

package(){
 install -Dm755 $srcdir/bullshit "$pkgdir/usr/bin/bullshit"
 install -Dm644 $srcdir/bullshit_lib "$pkgdir/usr/lib/bullshit"
}
```

You can see what changed compared with the first draft. Really, if you are trying to get something into the AUR, it is still a fairly simple process, but it is good to read up about the requirements before you start. I already had a repository for this project. That was a mistake. The two major mistakes I made were these:

1. I put my sources into a subdirectory of the repository root. Do not do this, your package will be automatically rejected.
2. I did not have a `.SRCINFO` file in every single commit in my history.

The way to go about creating a package in the AUR is different from what I had thought: You don't put the PKGBUILD into the same repository as your sources, no matter how big they are. As far as the AUR is concerned, you create the PKGBUILD, and that's about it. Inside the PKGBUILD, you get your sources from a tarball hosted somewhere else. You validate the contents against an MD5 checksum. And then, the files are automatically extracted into the `src` subdirectory, and the rest of the process works just as before.

So I created a new repository and tried my best to do everything right from the beginning this time around. And now you can also [have bullshit on your system][aur-bullshit]!

[irc-archlinux-offtopic]: irc://chat.freenode.net/archlinux-offtopic
[github-chee]: https://github.com/chee
[man-bullshit]: http://man.9front.org/1/bullshit
[9front-homepage]: http://9front.org/
[githubusercontent-rc/bin/bullshit]: https://raw.githubusercontent.com/enukane/9front-work/master/rc/bin/bullshit
[man-rc]: http://man.9front.org/1/rc
[man-awk]: http://man.9front.org/1/awk
[githubusercontent-sh/bin/bullshit]: https://raw.githubusercontent.com/fceschmidt/bullshit-arch/master/src/bullshit
[githubusercontent-old-PKGBUILD]: https://raw.githubusercontent.com/fceschmidt/bullshit-arch/48c55e2994a873f60fa392761483210733175bfc/PKGBUILD
[aur]: https://aur.archlinux.org/
[plan9-stallman]: http://www.linuxtoday.com/developer/2000070200704OPLFSW
[lucent-ycombinator]: https://news.ycombinator.com/item?id=7232042
[aur-bullshit]: https://aur.archlinux.org/packages/bullshit/
[ccc]: https://www.ccc.de/en/


