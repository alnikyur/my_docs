# Home assistant weather configuration RU support
1. Go to `/config/custom_sentences/ru/assistant.yaml` and paste code below:  
```
language: "ru"
intents:
  TellTime:
    data:
      - sentences:
          - "который час"
          - "сколько времени"
          - "скажи время"
  WeatherQuery:
    data:
      - sentences:
          - "Какая сейчас погода"
          - "Что с погодой"
          - "Расскажи про погоду"
          - "Погода"
```  
2. Go to `/config/configuration.yaml` and paste code below:  
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
  TellTime:
    speech:
      text: "Сейчас {{ now().strftime('%H:%M') }}"
  WeatherQuery:
    speech:
      text: >
        Сейчас {{ state_attr('weather.forecast_home_assistant', 'temperature') | round(0) }} градусов Цельсия и {{
          {
            'sunny': 'солнечно',
            'cloudy': 'облачно',
            'partlycloudy': 'переменная облачность',
            'rainy': 'дождливо',
            'pouring': 'льёт как из ведра',
            'lightning': 'гроза',
            'lightning-rainy': 'гроза с дождём',
            'snowy': 'снежно',
            'fog': 'туман',
            'windy': 'ветрено',
            'hail': 'град',
            'exceptional': 'необычная погода',
            'clear-night': 'ясная ночь'
          }[states('weather.forecast_home_assistant')] | default(states('weather.forecast_home_assistant'))
        }}.
logger:
  default: info
  logs:
    homeassistant.components.intent: debug
```  
3. in HA go to `settings->voice_assistant` and enable listen local commands firstly.  
4. Restart HA `system->restart_ha`  
5. Check phrase, for example ` какая сейчас погода`, you should see the answer with appropriate current weather status.