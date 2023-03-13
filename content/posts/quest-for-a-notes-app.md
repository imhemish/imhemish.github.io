+++
title = "Quest for a Notes app"
date = "2023-02-20"
[taxonomies]
tags = ['opensource', 'workflow', 'rant']
[extra]
fediid = '109812008389269847'
cover = "paper-notes-adaptive.png"
+++
My hunt for a plain markdown capable Notes app is stopped. Why was I searching a good notes app to fit my workflow even though there are countless offline and online notes available?
<!-- more --> 

# The problem
There are a lot of notes app available out there, with syncing capabilities. But, what are the problems with them?

#### Vendor lock-in
Most of notes services have vendor lock in. If I use something like OneNote, Simplenote, Evernote; I am bound to use their service through their apps and website and services. There is no way I can use the software I like for notes.

#### Syncing
There are some good offline note applications available, but they can't sync. I am a great sucker for syncing capabilities. If I change something on my mobile, I want to be be reflected back on my laptop, and also backed up on cloud. While online services come with syncing, they have vendor lock in, as discussed in previous point.

#### Standards? huh...
There are really no standards for a notes app. All the apps and services just implement their own wheel, thus leveraging vendor lock in and making moving between services hard. It is a recent phenomenon that some notes services provide you a way to write your notes in markdown format, but they still their own methods to store that data in the databases, thus again vendor lock in. There are some apps I found which work great and just store notes in markdown, but still they use some metadata with markdown, some use ```+++``` to start and stop metadata tags, some use ```---``` and some just append tags in this metadata, some just use folder categories.

#### Native apps
While most notes app on Android and IOS work pretty natively, a lot of good apps on Desktop side are really just electron apps. Though I am not opposed to electron, I am not a fan of it. I am ok with running 1 or 2 apps with Electron like [VSCodium](https://vscodium.com/), but everything in Electron, hell no!

# The solutions

#### Vendor lock-in
The most easiest solution to vendor lock in is to make notes in a standard format, like markdown.

#### Syncing
The most easiest solution for syncing is to just use markdown, and use [Syncthing](https://syncthing.net/) to sync the notes between devices, you may even use some self hosted cloud server, or any other cloud solution, in my case I use MEGA until i start earning and get my own self hosted server.

#### Standards?
It would be good to not use any specific or niche features. Just use plain markdown things like links, bullets, headings. That's pretty much it. For categorising the notes, just organise them into different directories.

#### Native apps
The only good solutions I found are [QOwnNotes](https://www.qownnotes.org/) and [Paper](https://beta.flathub.org/apps/details/io.posidon.Paper). QOwnNotes is made in Qt while Paper is made in GTK4/Libadwaita. Although these apps may use some of their own data in the folder of your notes, you may just ignore syncing it. Like Paper generates a ```.trash``` folder, just don't use trash in Paper and you would be good to go. QOwnNotes may store markdown metadata or make a sqlite database to store metadata, but they still use plain markdown for storing notes directly into filesystem tree. Just don't use specific features like tags and use directories to cagtegorise your notes and you are good to go. So, currently I use Paper, for the sake it is good to look at, and is adaptive (this may help if I start using a Linux Mobile phone some day; or it is also helpful when using a tiling window manager).

![Maximised window of Paper](/images/paper-notes-full.png)
![Adapted small window of Paper](/images/paper-notes-adaptive.png)

But what for Android? The most worth I have found are [Epsilon Notes](http://epsilonexpert.com/e/index.php) and [Obsidian](https://obsidian.md/). Both store the notes in a plain markdown format in the filesystem. Though Obsidian saves tags in markdown metadata, you can just get away with it by not using tags and just using directory based categories. If you do not use tags, it just does not append any metadata. You can optionally include date in markdown metadata, but if you do not use it, it just works like that.
# But why not ```<insert app>```
Just because I am very specific in choosing stuff for my usecases, I can't even tolerate something missing that I want. I am a sucker for native GUI apps, plus apps storing data in plain formats, that are easily ```grep```able or manipulatable via standard command line tools. Syncing is an important aspect for me, as I keep switching between my phone and laptop and want access to my data at the instant.

# Conclusion
So, I spent a lot of time and installed and uninstalled a lot of apps, and I have finally found the tools which work for me. Thanks for reading my rant.
