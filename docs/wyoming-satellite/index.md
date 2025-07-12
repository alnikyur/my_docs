# How to configure music/radio transmit on satellite  
1. Install `mpd` (music player daemon) on satellite using next command:  
```
apt install mpd
```
2. Change config file `/etc/mpd.conf` 
```
music_directory         "/var/lib/mpd/music"
playlist_directory              "/var/lib/mpd/playlists"
db_file                 "/var/lib/mpd/tag_cache"
state_file                      "/var/lib/mpd/state"
sticker_file                   "/var/lib/mpd/sticker.sql"
user                            "maestro"
bind_to_address                 "0.0.0.0"
port                            "6600"
log_level                       "verbose"
zeroconf_enabled                "yes"
zeroconf_name                   "Music Player @ %h"
input {
        plugin "curl"
}
decoder {
        plugin                  "hybrid_dsd"
        enabled                 "no"
}
decoder {
        plugin        "wildmidi"
        enabled       "no"
}
audio_output {
        type            "pulse"
        name            "PulseAudio Output"
}
filesystem_charset              "UTF-8"
```
3. Disable `mpd` service using `systemctl disable mpd.service` and then stop `mpd` using `systemctl stop mpd.service`
4. Prepare config for `~/.asound.rc`  
````
pcm.capture_hw {
    type hw
    card seeed2micvoicec
    device 0
}

pcm.playback_hw {
    type hw
    card seeed2micvoicec
    device 0
}

pcm.capture {
    type dsnoop
    ipc_key 1024
    slave {
        pcm "capture_hw"
        channels 1
        rate 16000
        format S16_LE
    }
}

pcm.playback {
    type dmix
    ipc_key 2048
    slave {
        pcm "playback_hw"
        channels 2
        rate 44100
        format S16_LE
    }
}

pcm.duplex {
    type asym
    playback.pcm "playback"
    capture.pcm  "capture"
}

pcm.!default {
    type plug
    slave.pcm "duplex"
}
````
5. Run `pulseaudio` in non-root user environment if it not running yet:  
```
pulseaduio --start
```
6. Run `mpd` in the same user environment (`!!!NON-ROOT!!!`) as `pulseaudio`:  
```
mpd --no-daemon &
```
7. Run `wyoming-satellite` using pulseaudio for aplay (use also alias for hardware arecord such as `CARD=Device,DEV=0`):  
```
script/run  --name 'zeropi' \
            --uri 'tcp://0.0.0.0:10700' \
            --mic-command 'arecord -D plughw:CARD=Device,DEV=0 -r 16000 -c 1 -f S16_LE -t raw' \
            --snd-command 'aplay -D pulse -r 22050 -c 1 -f S16_LE -t raw' \\
            --wake-uri 'tcp://127.0.0.1:10400' \
            --wake-word-name 'hey_jarvis' \
            --awake-wav /home/maestro/wyoming-satellite/startup.wav \
            --done-wav /home/maestro/wyoming-satellite/acknowledged.wav \
            --debug
```
Now `mpd` and `wyoming-satellite` can work together using the same audiooutput through `pulseaudio`. 
Configuration for voice assistant:  
- /config/configuration.yaml
``` 
# Loads default set of integrations. Do not remove.
default_config:

# Load frontend themes from the themes folder
frontend:
  themes: !include_dir_merge_named themes

automation: !include automations.yaml
script: !include scripts.yaml
scene: !include scenes.yaml

intent_script:
  StopRadio:
    speech:
      text: "Останавливаю радио"
    action:
      - service: media_player.media_stop
        target:
          entity_id: media_player.music_player_daemon
  PlayClassicRadio:
    action:
      - delay: "00:00:02"
      - service: media_player.play_media
        target:
          entity_id: media_player.music_player_daemon
        data:
          media_content_type: "audio/mpeg"
          media_content_id: "https://icecast.walmradio.com:8443/classic"
```
- /config/custom_sentences/ru/assistant.yaml  
```
language: "ru"
intents:
  StopRadio:
    data:
      - sentences:
          - "выключи радио"
          - "останови музыку"
          - "останови радио"
          - "пауза"
  PlayClassicRadio:
    data:
      - sentences:
          - "включи классическую радиостанцию"
          - "включи радио классика"
          - "запусти классическую музыку"
          - "радио"
```
Then restart HA via web-gui and test.
