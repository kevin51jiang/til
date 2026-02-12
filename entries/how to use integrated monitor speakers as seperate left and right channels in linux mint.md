---
title: how to use integrated monitor speakers as seperate left and right channels in linux mint
permalink: how-to-use-integrated-monitor-speakers-as-seperate-left-and-right-channels-in-linux-mint
date: 2026-02-11T22:26:33-08:00
tags: 
---



I've always wanted nice surround sound, but for some reason I could never bring myself to actually buy speakers for the better sound and bring (more) clutter to my desk.

However Linux Mint by default only allows you to output to a single device at once, bleh. I have *4* speakers just sitting there, and you tell me that I can only use 2?

I've searched online at multiiple different forums and I even spent a good Friday night trying to get this to work a few months ago. But alas AI was not smart enough at that time. Now with Gemini 3 Flash it was suprisingly competent.

The ideal state I am going for, is having the left monitor be a single "Front Left" speaker, and the right monitor be a single "Front Right" speaker.


```
::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::..
:............................................***************************=...........................
:..-++++++++++++++...........................*-------------------------+=...........................
:..-++++++++++++++.......................=...***************************=..:+.......................
:..-++++++++++++++....................+@%#@:..............................-*-=*+....................
:..-++++++++++++++.................+@%##%@+................................:*+--=*=.................
:..-++++++++++++++.............:+@%##%@+......................................-*+--=*=..............
:..-++++++++++++++..........:*@%##%@+............................................-*+--+*-...........
:..-++++++++++++++.......:#@%##%@=..................................................-*+--+*-........
:..-++++++++++++++.....#@###%@-........................................................=*=--+*-.....
:..-++++++++++++++..%@###@@-..............................................................+#=--**:..
:..-++++++++++++++:@##%%-....................................................................+*=-=*.
:..-++++++++++++++..#-.........................................................................:+*..
:..-++++++++++++++..................................................................................
:..-++++++++++++++..................................................................................
:..-++++++++++++++..................................................................................
:..-++++++++++++++..................................................................................
:..-++++++++++++++.........................-----------------------------:...........................
:..-++++++++++++++........................:*+@#%%#%%#%*%#*%#@%%@%%#%##%#*...........................
:..-++++++++++++++........................:%#%@#%%#%%#%%%@%%%%@%#*#%#*%#*...........................
:..-++++++++++++++........................:%*#%%%%@%%@%@%%%%%%%%%*#%##%#*...........................
:..-++++++++++++++........................:%##@@%@@@@%@@%@@%@@%%***%%%%%*...........................
:..-++++++++++++++........................:%#%#%%@%%%%%%%%@%%%%%%%%@@%@%*...........................
:..-++++++++++++++........................:******##########********#####+...........................
:..-++++++++++++++..................................................................................
:..-++++++++++++++..................................................................................
:..-++++++++++++++..................................................................................
:..-++++++++++++++..................................................................................
:..-++++++++++++++..................................................................................
:..:::::::::::::::..................................................................................
....:...............................................................................................
```

> Bird's eye view of my desk, featuring a computer on the left, and 3 monitors angled at the user. The two monitors on the wings have built in speakers.



### My machine:
```bash
# Linux Mint 22.3 Cinnamon, plus:
~ ❯ pactl --version
pactl 16.1
Compiled with libpulse 16.1.0
Linked with libpulse 16.1.0

~ ❯ pipewire --version
pipewire
Compiled with libpipewire 1.0.5
Linked with libpipewire 1.0.5

~ ❯ wireplumber --version
wireplumber
Compiled with libwireplumber 0.4.17
Linked with libwireplumber 0.4.17
```




## Instructions

1. Create these files:


<details>

<summary>

`pipewire.conf.d/50-combined-monitors.conf`:

</summary>

```toml
# Combined Monitors: stereo split across two HDMI outputs
# Left channel (FL) → HDMI 0 (Monitor Left, pro-output-8)
# Right channel (FR) → HDMI 2 (Monitor Right, pro-output-3)
#
# Requires pro-audio profile on the NVidia card
# (set via wireplumber/main.lua.d/51-nvidia-pro-audio.lua)

context.objects = [
    { factory = adapter
        args = {
            factory.name     = support.null-audio-sink
            node.name        = "combined-monitors"
            node.description = "Combined Monitors (Stereo Split)"
            media.class      = "Audio/Sink"
            audio.channels   = 2
            audio.position   = "FL,FR"
            # This allows the sink volume to affect the signal sent to loopbacks
            monitor.channel-volumes = true
            priority.session = 2000
            priority.driver  = 2000
        }
    }
]

context.modules = [
    { name = libpipewire-module-loopback
        args = {
            node.description = "Monitor 0 (Left)"
            capture.props = {
                node.name           = "combined_left_capture"
                target.object       = "combined-monitors"
                stream.capture.sink = true
                audio.position      = [ FL ]
            }
            playback.props = {
                node.name           = "combined_left_playback"
                target.object       = "alsa_output.pci-0000_2d_00.1.pro-output-8"
                node.dont-fallback  = true
                audio.position      = [ MONO ]
            }
        }
    }
    { name = libpipewire-module-loopback
        args = {
            node.description = "Monitor 2 (Right)"
            capture.props = {
                node.name           = "combined_right_capture"
                target.object       = "combined-monitors"
                stream.capture.sink = true
                audio.position      = [ FR ]
            }
            playback.props = {
                node.name           = "combined_right_playback"
                target.object       = "alsa_output.pci-0000_2d_00.1.pro-output-3"
                node.dont-fallback  = true
                audio.position      = [ MONO ]
            }
        }
    }
]
```

</summary>


And also...

<details>

<summary>

`~/.config/wireplumber/main.lua.d/99-nvidia-pro-audio.lua`:

</summary>

```lua
-- Force NVidia HDMI card to pro-audio profile
print("MONITOR CONFIG: Forcing Nvidia Card to Pro Audio")

rule = {
  matches = {
    {
      { "device.name", "matches", "alsa_card.pci-0000_2d_00.1" },
    },
  },
  apply_properties = {
    ["device.profile"] = "pro-audio",
    ["api.acp.auto-profile"] = false,
    ["api.acp.auto-port"] = false,
  },
}

table.insert(alsa_monitor.rules, rule)
```

</details>

2. Restart your computer

3. Done!

Useful commands for troubleshooting:
```bash
# view the "cables" of the input/output devices from pipewire's world
pw-link -l

# Clear local state of wireplumber
rm -rf ~/.local/state/wireplumber/*

# Restart pipewire/wireplumber to pick up new settings
systemctl --user restart pipewire wireplumber
```

