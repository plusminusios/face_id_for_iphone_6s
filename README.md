# FaceIDFor6s 👁️

> Твик для джейлбрейка, который добавляет **Face ID на iPhone 6s** через фронтальную камеру

---

## Как это работает

iPhone 6s не имеет датчика TrueDepth, но этот твик обходит это ограничение:

1. Перехватывает системный класс `LAContext` и сообщает iOS что Face ID доступен
2. Когда приложение или экран блокировки запрашивает биометрию — включается фронтальная камера
3. `Vision.framework` анализирует каждый кадр и ищет лицо
4. Если лицо найдено нужное количество раз — аутентификация проходит успешно

---

## Требования

| Что | Версия |
|---|---|
| Устройство | iPhone 6s / 6s Plus |
| iOS | 15.x |
| Джейлбрейк | Dopamine (rootless) или Palera1n (rootful) |
| Substrate | Ellekit / Substitute / Libhooker |

---

## Установка

### Способ 1 — Через GitHub Actions (рекомендуется)

1. Создай репозиторий на [github.com](https://github.com)
2. Загрузи все файлы проекта
3. Создай файл `.github/workflows/build.yml`:

```yaml
name: Build DEB
on: [push]
jobs:
  build:
    runs-on: macos-latest
    steps:
      - uses: actions/checkout@v4
      - name: Install Theos
        uses: Randomblock1/theos-action@v1
      - name: Build
        run: gmake package FINALPACKAGE=1
      - name: Upload DEB
        uses: actions/upload-artifact@v4
        with:
          name: FaceIDFor6s.deb
          path: packages/*.deb
```

4. Подожди пока GitHub соберёт пакет (зелёная галочка ✅)
5. Скачай `.deb` из раздела **Actions → Artifacts**

### Способ 2 — Вручную через Theos

```bash
# Установить Theos
bash -c "$(curl -fsSL https://raw.githubusercontent.com/theos/theos/master/bin/install-theos)"

# Собрать
cd FaceIDFor6s
make package FINALPACKAGE=1
```

---

## Установка на телефон

**Через AirDrop:**
- Скинь `.deb` по AirDrop → открой в Sileo → установи

**Через SSH:**
```bash
scp packages/*.deb root@<ip-телефона>:/var/root/
ssh root@<ip-телефона> "dpkg -i /var/root/*.deb && killall -9 SpringBoard"
```

---

## Настройки

После установки зайди в **Настройки → FaceID для iPhone 6s**

| Параметр | Описание |
|---|---|
| Включить FaceID | Включить или выключить твик |
| Строгость | Низкая / Средняя / Высокая — сколько кадров нужно для успеха |
| Таймаут | Сколько секунд ждать лицо перед ошибкой |

---

## Структура проекта

```
FaceIDFor6s/
├── Tweak.x                        — основной код твика
├── Makefile                       — настройки сборки
├── FaceIDFor6s.plist              — фильтр внедрения
├── Prefs/
│   ├── RootListController.m       — контроллер настроек
│   └── Resources/
│       ├── Root.plist             — разметка экрана настроек
│       └── Info.plist             — метаданные бандла настроек
└── layout/
    └── DEBIAN/
        └── control                — метаданные пакета для Sileo
```

---

## Важные ограничения

> ⚠️ Это **не настоящий Face ID**

- Нет инфракрасной подсветки и 3D-сканирования
- Безопасность ниже оригинального Face ID — фотография теоретически может сработать
- Некоторые банковские приложения с дополнительной защитой могут не работать
- Работает только на джейлбрейкнутых устройствах

---

## Лицензия

MIT — используй свободно на свой страх и риск.
