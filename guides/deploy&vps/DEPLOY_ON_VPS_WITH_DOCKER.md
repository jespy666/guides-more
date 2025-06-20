## Гайд по деплою сервиса на `VPS` с помощью `Docker` и `GitHub`
___

*прим.*
- `username` - **Ваш произвольный username**
- `password` - **Ваш произвольный пароль**
- `email` - **Ваша почта**
- `IP` - **IP адрес вашего VPS**
- `repo` - **Репозиторий на GitHub**
- `profile` - **Ваш профиль на GitHub**

### Этап 1 - Настройка удалённого VPS
1. Создать Виртуальный сервер (VPS) с Docker ПО
2. Получить логин и пароль для входа на `{email}`
3. Войти на VPS используя терминал командой:  
`ssh root@{IP}` -> Ввести пароль, который пришел на `{email}`
4. Обновить компоненты командой:  
`sudo apt update`
5. Создать пользователя на VPS командой:  
`sudo adduser {username}` -> Ввести и запомнить `{password}`
6. Добавить пользователя `{username}`в группы `sudo` и `docker`:  
`usermod -aG sudo {username} && usermod -aG docker {username}`  
7. Перезайти в VPS под новым пользователем:  
`ctrl+D` & `ssh {username}@{IP}` -> Ввести `{password}`
8. Создать ключ `SSH` доступа командой:  
`ssh-keygen -t rsa -b 4096 -C "{email}"` -> Cогласиться со всеми пунктами   
Далее в папке `home/{usename}/.ssh` появятся 2 файла. 
9. Создать запись в `authorized_keys`:  
`cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys`
10. [Установка русской локали](https://github.com/jespy666/guides-more/blob/main/guides/deploy%26vps/SETUP_RU_LOCALE_ON_VPS.mdhttps://github.com/jespy666/guides-more/blob/main/guides/deploy%26vps/SETUP_RU_LOCALE_ON_VPS.md) *(При необходимости)*

### Этап 2 - Настройка GitHub
1. Авторизация GitHub репозитория и профиля с VPS:
   - Находясь в VPS выполнить команду:  
       `cat ~/.ssh/id_rsa.pub` -> скопировать то, что вывелось на экран **целиком**
   - Зайти в настройки `{profile}` -> `SSH and GPG keys` -> `New SSH key`:  
       `name` - любое название  
       `key` - то, что скопировали из `cat ~/.ssh/id_rsa.pub`
   - Находясь в VPS выполнить команду:  
       `cat ~/.ssh/id_rsa` -> скопировать то, что вывелось на экран **целиком**
   - Зайти в настройки `{repo}` -> `Secret and Variables` -> `Actions` -> `New repository secret (x3)`:  
       `SSH_PRIVATE_KEY` - то, что скопировали из `cat ~/.ssh/id_rsa`  
       `SSH_HOST` - `{IP}`  
       `SSH_USER` - `{username}`
2. Создание Workflow для CD:
   - Создать GitHub Actions:  
       `{repo}` -> `Actions` -> `New workflow` -> `set up a workflow yourself` (любое название)
   - Заполнить содержимое Workflow из [Примера](https://github.com/jespy666/guides-more/blob/main/pipelines/deploy_on_vps.yml)

### Этап 3 - Процесс деплоя
1. Находясь на VPS клонируем репозиторий:  
`git clone "your_repo_clone_url_from_ssh"`
2. Создать файл `.env` в корне проекта с переменными окружения *(При необходимости)*:  
`nano .env` -> заполнить
3. Находясь в `{repo}` создать `pull request` в ветку `production`
4. Сделать `merge` или ждать `approve` от `ревьюера`
5. Запустится процесс деплоя, логи которого можно отслеживать в `Actions`
6. При критических ошибках выполнить `revert` в `pull request`

___
`v0.1.0`
