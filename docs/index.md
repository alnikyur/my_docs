# Wyoming-sattelite install guide

First clone this [repo](https://github.com/rhasspy/wyoming-satellite)

```
cd /home/maestro/wyoming-openwakeword
script/run --uri 'tcp://0.0.0.0:10400'
```

```
cd /home/maestro/wyoming-satellite
script/run  --name 'my satellite' \
			--uri 'tcp://0.0.0.0:10700' \
			--mic-command 'arecord -D plughw:0 -r 16000 -c 1 -f S16_LE -t raw' \
			--snd-command 'aplay -D plughw:1 -r 22050 -c 1 -f S16_LE -t raw' \
			--wake-uri 'tcp://127.0.0.1:10400' \
			--wake-word-name 'hey_jarvis' \
			--awake-wav /home/maestro/wyoming-satellite/synth.wav \
			--done-wav /home/maestro/wyoming-satellite/pickupCoin.wav \
			--vad \
			--debug
```

Example:

```
script/run  --name 'zeropi' \
			--uri 'tcp://0.0.0.0:10700' \
			--mic-command 'arecord -D plughw:0 -r 16000 -c 1 -f S16_LE -t raw' \
			--snd-command 'aplay -D plughw:1 -r 22050 -c 1 -f S16_LE -t raw' \
			--wake-uri 'tcp://127.0.0.1:10400' \
			--wake-word-name 'hey_jarvis' \
			--debug
```

### Installing systemd service:

`sudo nano /etc/systemd/system/wyoming-satellite.service`

```
[Unit]
Description=Wyoming Satellite
Wants=network-online.target
After=network-online.target

[Service]
Type=simple
ExecStart=/home/maestro/sounds/wyoming-satellite/script/run --name 'raspi_zero' \
                                                     --uri 'tcp://0.0.0.0:10700' \
                                                     --mic-command 'arecord -D plughw:CARD=seeed2micvoicec,DEV=0 -r 16000 -c 1 -f S16_LE -t raw' \
                                                     --snd-command 'aplay -D plughw:CARD=seeed2micvoicec,DEV=0 -r 22050 -c 1 -f S16_LE -t raw' \
                                                     --awake-wav /home/maestro/sounds/startup.wav \
                                                     --done-wav /home/maestro/sounds/acknowledged.wav \
                                                     --debug
ExecStartPost=/usr/bin/aplay /home/maestro/sounds/starting_up.wav
WorkingDirectory=/home/maestro/sounds/wyoming-satellite
StandardOutput=append:/var/log/wyoming-satellite/satellite.log
StandardError=append:/var/log/wyoming-satellite/satellite.log
Restart=always
RestartSec=1

[Install]
WantedBy=default.target
```

### How to install and use local LLM Ollama in HA:
ollama config:

```
$env:OLLAMA_HOST="0.0.0.0"
ollama serve
```


### Testing mic and speakers:

```
speaker-test -D plughw:1,0 -t sine -f 440 -c 2
arecord -D plughw:0,0 -r 16000 -f cd -t wav -d 5 test.wav
aplay -D plughw:1,0 -r 22050 test.wav
```

### Troubleshooting
If you speak `wake_word` but the system cannot answer or cant recognize you do the next steps:  
1. Check `alsamixer` microphone settings, there should be at least `50%` amplify on it.  
2. Check the logs of `whisper` and `piper` in `system ->logs` in HA web-interface.  
3. Test your mic and audio-output using above command `Testing mic and speakers`.  
4. Restart HA and satellite device.