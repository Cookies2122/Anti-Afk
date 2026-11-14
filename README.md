<h1 align=center><code>Anti-Afk</code></h1>

<div align=center>
  <img src=https://img.shields.io/badge/Counter--Strike%202-Metamod%202.0%20(KHook)-orange?style=flat-square alt=metamod>
  <a href=https://github.com/Cookies2122/Anti-Afk/commits><img src=https://img.shields.io/github/last-commit/Cookies2122/Anti-Afk?style=flat-square alt="last commit"></a>
  <a href=LICENSE><img src=https://img.shields.io/github/license/Cookies2122/Anti-Afk?style=flat-square alt=license></a>
</div>
<br>

Следит за игроками, которые стоят AFK. Сначала предупреждает в чате, потом убивает, а после нескольких предупреждений переводит в наблюдатели или кикает.

> Автор плагина: [ABKAM](https://github.com/ABKAM2023). Здесь версия 1.1 под новый SDK и Metamod 2.0, оригинал: [ABKAM2023/Anti-Afk](https://github.com/ABKAM2023/Anti-Afk).

## Что нужно

- [Metamod:Source](https://github.com/alliedmodders/metamod-source) 2.0 (KHook)
- [Utils](https://github.com/Pisex/cs2-menus)

## Установка

Соберите плагин (раздел [Сборка](#сборка)) и скопируйте содержимое `build/package` в папку `csgo` на сервере. В пакете лежит:

| Файл | Куда |
| --- | --- |
| `AntiAfk.so` | `addons/AntiAfk/` |
| `AntiAfk.vdf` | `addons/metamod/` |
| `settings.ini` | `addons/configs/AntiAfk/` |
| `antiafk.phrases.txt` | `addons/translations/` |

После этого перезапустите сервер или смените карту.

## Настройки

### settings.ini

`addons/configs/AntiAfk/settings.ini`

| Ключ | По умолчанию | Что делает |
| --- | --- | --- |
| `initial_warning_time` | `15` | через сколько секунд бездействия написать первое предупреждение |
| `warning_interval` | `35` | через сколько секунд бездействия наказать |
| `max_warnings` | `3` | сколько предупреждений до финальной меры, до этого игрока просто убивают |
| `kick_instead_of_spec` | `0` | финальная мера: 0 перевести в наблюдатели, 1 кикнуть |
| `aim_move_threshold_deg` | `1.0` | на сколько градусов нужно повернуть прицел, чтобы это считалось активностью |
| `debug_log` | `0` | отладка в консоль, 1 включить |

<details>
<summary>Весь файл settings.ini</summary>

```
"AntiAfk"
{
    // Через сколько секунд выводить ПЕРВОЕ предупреждение.
    "initial_warning_time" "15"

    // Общее время бездействия (сек), по истечении которого применяется действие
    // (предупреждение/суицид/финальная мера).
    "warning_interval" "35"

    // Количество «мягких» предупреждений до финальной меры.
    // Пока warnCount < max_warnings - 1 - игроку делают суицид. Затем - финальная мера.
    "max_warnings" "3"

    // 0 - финальная мера: перевести в наблюдатели; 1 - кикнуть.
    "kick_instead_of_spec" "0"

    // Минимальный угол (в градусах) изменения направления взгляда,
    // который считается активностью (сброс AFK).
    "aim_move_threshold_deg" "1.0"

    // показывает диагностические сообщения в консоли
    "debug_log"      "0"    
}
```

</details>

## Переводы

Файл `addons/translations/antiafk.phrases.txt`, языки: ru, en. Его можно править под себя.

<details>
<summary>Все фразы</summary>

```
"Phrases"
{
    "AFK_Prefix"
    {
        "en"        "{RED}[AFK]{DEFAULT}"
        "ru"        "{RED}[AFK]{DEFAULT}"        
    }

    "AFK_CountdownKick"
    {
        "en"        "You are idle, and in {RED}%d {DEFAULT}seconds you will be kicked for AFK."
        "ru"        "Вы не двигаетесь, и через {RED}%d {DEFAULT}секунд вас кикнут за AFK."        
    }

    "AFK_CountdownSpec"
    {
        "en"        "You are idle, and in {RED}%d {DEFAULT}seconds you will be moved to spectators."
        "ru"        "Вы не двигаетесь, и через {RED}%d {DEFAULT}секунд вас переведут в наблюдатели."        
    }

    "AFK_CountdownKill"
    {
        "en"        "You are idle, and in {RED}%d {DEFAULT}seconds you will be killed for AFK."
        "ru"        "Вы не двигаетесь, и через {RED}%d {DEFAULT}секунд вас убьют за AFK."        
    }

    "AFK_WarningKill"
    {
        "en"        "Warning {RED}%d{DEFAULT}/{RED}%d{DEFAULT}: you were killed for AFK."
        "ru"        "Предупреждение {RED}%d{DEFAULT}/{RED}%d{DEFAULT}: вас убили за AFK."        
    }

    "AFK_SpecFinal"
    {
        "en"        "You have been moved to spectators due to AFK."
        "ru"        "Вы были переведены в наблюдатели из-за AFK."        
    }
}
```

</details>

## Что изменено

По сравнению с оригиналом:

- Переведено на новый SDK ([Wend4r/sourcesdk](https://github.com/Wend4r/sourcesdk)), старый после обновления CS2 от 12.11.2026 уже не подходит.
- Работает только на Metamod 2.0 (KHook), старый Metamod на SourceHook больше не поддерживается.
- Заголовки Utils обновлены под текущую версию, имена интерфейсов теперь `UTILS_INTERFACE` и `MENUS_INTERFACE`.
- Своя функция `GameEntitySystem()` убрана, в новом SDK entity system берётся через `IGameResourceService`.
- SchemaEntity обновлена: актуальные оффсеты виртуальных функций.
- При выгрузке плагин проверяет, что Utils ещё загружен. Раньше `meta unload` после выгрузки Utils мог уронить сервер.
- Перевод в наблюдатели и убийство за AFK идут через `ChangeTeam` и `CommitSuicide` из SchemaEntity, их оффсеты обновлены (104 и 387). Со старыми вызывалась не та функция.

## Сборка

Собирается на Linux. Нужны clang, python3, cmake и [AMBuild](https://github.com/alliedmodders/ambuild). Все команды ниже выполняются из одной папки.

**1. SDK.** Нужен [sourcesdk](https://github.com/Wend4r/sourcesdk) не старее обновления CS2 от 12.11.2026. Собирается один раз:

```bash
git clone --recursive https://github.com/Wend4r/sourcesdk hl2sdk-cs2
cmake -S hl2sdk-cs2 -B hl2sdk-cs2/build -DCMAKE_BUILD_TYPE=Release -DCMAKE_C_COMPILER=clang -DCMAKE_CXX_COMPILER=clang++ -DSOURCESDK_AM_DEFINES=ON -DSOURCESDK_USE_MINIMAL_PROTOS=OFF
cmake --build hl2sdk-cs2/build -j$(nproc)
export HL2SDKCS2=$PWD/hl2sdk-cs2
```

**2. Metamod.** Нужен Metamod 2.0 с KHook, ветка `master`. Со старым Metamod на SourceHook сборка остановится с ошибкой.

```bash
git clone --recursive https://github.com/alliedmodders/metamod-source
```

**3. Плагин.** Папка SchemaEntity должна лежать рядом с папкой плагина.

```bash
git clone https://github.com/{o}/SchemaEntity
git clone https://github.com/Cookies2122/Anti-Afk
cd Anti-Afk
mkdir build && cd build
python3 ../configure.py --enable-optimize --sdks=cs2 --targets=x86_64 --mms_path=$PWD/../../metamod-source --hl2sdk-manifests=../hl2sdk-manifests
ambuild
```

Готовые файлы появятся в `build/package`.

## Автор

Плагин написал [ABKAM](https://github.com/ABKAM2023), оригинал: [ABKAM2023/Anti-Afk](https://github.com/ABKAM2023/Anti-Afk).
Перенос на новый SDK и Metamod 2.0, исправления: \_ded\_cookies.

## Лицензия

GPL-3.0, текст в файле [LICENSE](LICENSE). В коде плагина лицензия тоже указана как GPL. Авторские права на оригинальный код у ABKAM.
