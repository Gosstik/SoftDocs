# Активация JetBrains IDE

1) Скачиваем `jetbra.zip` с сайта <https://3.jetbra.in/>, распаковываем и кладём в папку `/opt`.

2) Патчим `javaagent`: вставляем в `<ide_folder>/bin/<ide_name>64.vmoptions` строку

    ```text
    -javaagent:/opt/jetbra/ja-netfilter.jar=jetbrains
    ```

3) Выходим из аккаунта JetBrains и подбираем суперключ для нужной IDE: <https://3.jetbra.in/>

Если есть проблемы, то можно почитать `readme.md` в папке `jetbra`.
