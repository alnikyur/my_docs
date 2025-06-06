# Find chatID in Telegram

How to find chatID in Telegram:
```
https://api.telegram.org/bot<API_TOKEN>/getUpdates
```
Then send message in the chat and update page, you will find chatID here, see example below:
```
"message":{"message_id":1044,"from":{"id":406930263,"is_bot":false,"first_name":"Aleksey (Maestro)","username":"aleksey_nikulin","language_code":"ru"},"chat":{"id":406930263,"first_name":"Aleksey (Maestro)","username":"aleksey_nikulin","type":"private"},"date":1749051598,"text":"/ledoff","entities":[{"offset":0,"length":7,"type":"bot_command"}]}}]}
```
