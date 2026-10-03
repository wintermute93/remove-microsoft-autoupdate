# remove-microsoft-autoupdate

[English](README.md) | [Português (Brasil)](README.pt-BR.md) | **Русский**

Небольшой shell-скрипт, который удаляет Microsoft AutoUpdate (MAU) из macOS.

## Зачем

Стоит установить на Mac Word, Excel, Teams, Edge или любое другое
приложение Microsoft, и вместе с ним появляется Microsoft AutoUpdate,
хотите вы этого или нет. После этого он:

- запускается при входе в систему и постоянно работает в фоне;
- показывает окна с обновлениями в самый неподходящий момент;
- ставит вспомогательную утилиту, работающую от root
  (`com.microsoft.autoupdate.helper`), в которой уже находили уязвимости
  повышения привилегий;
- скачивает сотни мегабайт обновлений, когда сам решит;
- продолжает работать, даже если отключить автообновление в его настройках.

Если вы хотите сами решать, когда обновлять Office, или просто не хотите
держать ещё один фоновый сервис Microsoft, штатного деинсталлятора у MAU
нет. Приходится искать его файлы и удалять вручную. Этот скрипт делает
это за вас.

## Что делает скрипт

1. Показывает все известные ему файлы MAU с размерами, чтобы было видно,
   что найдено, до того как что-либо будет изменено.
2. Спрашивает подтверждение.
3. Выгружает launch daemon и agent и завершает запущенные процессы MAU.
4. Удаляет приложение, вспомогательную утилиту, plist-файлы launchd, кэши
   и настройки, как системные, так и текущего пользователя.
5. Удаляет запись об установке MAU (`pkgutil --forget`).

Удаляемые пути:

```
/Library/Application Support/Microsoft/MAU2.0
/Library/LaunchAgents/com.microsoft.update.agent.plist
/Library/LaunchDaemons/com.microsoft.autoupdate.helper.plist
/Library/PrivilegedHelperTools/com.microsoft.autoupdate.helper
/Library/Preferences/com.microsoft.autoupdate2.plist
/Library/Caches/com.microsoft.autoupdate.helper
~/Library/Preferences/com.microsoft.autoupdate2.plist
~/Library/Preferences/com.microsoft.autoupdate.fba.plist
~/Library/Caches/com.microsoft.autoupdate2
~/Library/Caches/com.microsoft.autoupdate.fba
~/Library/HTTPStorages/com.microsoft.autoupdate2
~/Library/Application Support/Microsoft AU Daemon
```

Сам Office не затрагивается. Word, Excel, PowerPoint, Outlook и остальные
работают как прежде, просто перестают обновляться сами.

## Использование

```sh
git clone https://github.com/wintermute93/remove-microsoft-autoupdate.git
cd remove-microsoft-autoupdate

./remove-microsoft-autoupdate --dry-run   # посмотреть, что будет удалено
sudo ./remove-microsoft-autoupdate
```

Параметры:

```
-n, --dry-run   показать, что будет удалено, ничего не меняя
-y, --yes       не спрашивать подтверждение
-h, --help      показать справку
-V, --version   показать версию
```

Чтобы установить скрипт в систему:

```sh
sudo install -m 755 remove-microsoft-autoupdate /usr/local/bin/
```

## Примечания

- Установка или обновление Office через `.pkg`-установщик Microsoft
  возвращает MAU. В этом случае просто запустите скрипт ещё раз.
- Без MAU Office не обновляется сам. Обновления можно скачать на странице
  [Office for Mac update history](https://learn.microsoft.com/officeupdates/update-history-office-for-mac),
  там же есть отдельный установщик MAU, если он когда-нибудь понадобится.
- Office, установленный из Mac App Store, обновляется через App Store и от
  MAU не зависит.

## Лицензия

MIT
