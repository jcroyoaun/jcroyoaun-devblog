Forge Already Running error message in Endeca
=========
NOTE: I'm assuming you are using a Unix-like operating system :

When you get a "Forge Already Running" error message run :

```
ps -ef | grep PlatformServices
```

Something like this would show up (where 15828 is the Process ID) :
```
15828  2130  2 17:31 pts/0    00:00:03 <ENDECA-HOME>/PlatformServices/11.0.0/bin/forge
```

You can safely kill the Process ID if you need to proceed with something that required the Forge not to be running!

```
kill 15828
```