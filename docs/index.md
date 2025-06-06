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

How to install and use local LLM Ollama in HA:
ollama config:

```
$env:OLLAMA_HOST="0.0.0.0"
ollama serve
```


Testing mic and speakers:

```
speaker-test -D plughw:1,0 -t sine -f 440 -c 2
arecord -D plughw:0,0 -r 16000 -f cd -t wav -d 5 test.wav
aplay -D plughw:1,0 -r 22050 test.wav
```
