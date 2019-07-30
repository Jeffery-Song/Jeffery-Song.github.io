---
layout: single
title: "Add Bash-here to right-click context"
date: 2018-01-23
---



Method to add `Bash here` in your right-click context.

## Basic Idea

In cmd/powershell, `bash` command will get into wsl bash and set current directory.

In regedit, add items to `HKEY_CLASSES_ROOT\Directory\Background\shell` to add thing in your  right-click context at a directory's blank background. 

Further more, `HKEY_CLASSES_ROOT\Folder\shell` or `HKEY_CLASSES_ROOT\Directory\shell` is for right-click on a folder.

## Add `Bash here` in right click context at dir's background

If the `bash.exe` is in your system PATH, these codes would work. Otherwise, you have to specify the path of `bash.exe`.

```reg
Windows Registry Editor Version 5.00

[HKEY_CLASSES_ROOT\Directory\Background\shell\WSL]
@="Bash here"

[HKEY_CLASSES_ROOT\Directory\Background\shell\WSL\command]
@="bash"
```

Save texts above to a `.reg` file and run it.

Another interesting thing, when I install Everything, I forgot to add it to my right click context. So in Powershell, I ran `& 'C:\Program Files\Everything\Everything.exe' --help` and find out `-p` parameter can pass the current folder to Everything and open an instance. Thus:

```reg
Windows Registry Editor Version 5.00

[HKEY_CLASSES_ROOT\Directory\Background\shell\Everything]

@="Everything here"

[HKEY_CLASSES_ROOT\Directory\Background\shell\Everything\command]

@="C:\\Program Files\\Everything\\Everything.exe -p ."
```

