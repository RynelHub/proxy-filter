# Proxy Filter

**Версия:** 1.1.0  
*[Актуальная версия](https://github.com/RynelHub/proxy-filter/releases/latest): подробности изменений смотрите в [CHANGELOG.md](https://github.com/RynelHub/proxy-filter/blob/main/CHANGELOG.md).*

---

**Proxy Filter** — инструмент для автоматической обработки, фильтрации и конвертации конфигураций прокси-серверов. Программа поддерживает форматы **Clash (YAML)** и **V2Ray (plain text/base64)**.

---

## 1. Входные данные

Для работы программы требуется файл `input_urls.txt`, содержащий список URLs подписок (по одному URL на строку).  
Примеры подписок уже добавлены в файл по-умолчанию. Вы можете добавить дополнительные подписки, полученные из сторонних источников.

**Поддерживаемые форматы подписок:**
- Clash (YAML)
- V2Ray (plain text)
- V2Ray (base64 encoded)

---

## 2. Результаты работы программы

После завершения обработки в папке `output` создаются текстовые файлы, содержащие отфильтрованные прокси-серверы.

**Правила фильтрации:**
- **Протоколы:** VLESS, Trojan  
- **Порт:** 443  
- **Наличие TLS (включая TLS Reality)**  
- **Географическая принадлежность серверов:** согласно значению параметра `BLOCKED_COUNTRY` в файле `config.ini` 
- **Удаление дубликатов конфигураций** (гарантия полного отсутствия дубликатов не предоставляется).  

Каждый итоговый файл будет содержать не более **N** строк конфигураций, согласно значению параметра `CHUNK_SIZE_WRITE` в файле `config.ini`

---

## 3. Основной функционал

### Обработка исходных файлов
- Многопоточная загрузка данных по URL из файла `input_urls.txt`.
- Рандомизация User-Agent и задержки между повторными попытками скачивания.

### Проверка и исправление файлов
- Автоматическое исправление ошибок в загруженных файлах.

### Фильтрация данных
- Исключение неподходящих строк по заданным правилам.
- Попытка определения географической принадлежности серверов.

### Конвертация форматов
- Преобразование данных из YAML и BASE64 в текстовый формат.

### Логирование
- Все операции записываются в файл `logs/app.log`.
- Поддерживается ротация логов (максимум 3 файла объемом до 1 МБ).

### Подсчет времени выполнения
- В конце работы указывается общее время обработки.

---

## 4. Инструкция по использованию

1. **Подготовьте файл с URLs подписок:**
   - Откройте файл `input_urls.txt`.
   - Добавьте ссылки на подписки, по одной на строку.
   - Сохраните файл.

2. **Поменяйте настройки в файле `config.ini`, если необходимо:**
   - Например, можно указать коды стран, для фильтрации итогового списка прокси по географическому признаку.
   - Сохраните файл.

3. **Запустите программу:**
   - Дважды щелкните на `proxy-filter.exe`.

4. **Ожидайте завершения работы:**
   - После обработки все данные появятся в папке `output`.

5. **Лог-файл с описанием подробностей работы программы можно найти в папке `logs`**

---

## 5. Особые указания

### Объем данных
- Если подписки содержат большое количество строк (десятки тысяч), программа может работать **продолжительное время**. Это нормально.

### Проверка доступности серверов
- Программа **не проверяет** доступность серверов. Используйте внешние инструменты для этого.

### Работа через прокси
- В настоящее время программа **не поддерживает** системные прокси. Отключите их перед использованием.

---

## 6. Ошибки и обратная связь

Программа поставляется "как есть". Если вы столкнулись с ошибками или необычным поведением:
- Убедитесь, что используете операционную систему Microsoft Windows (текущая версия программы Proxy Filter рассчитана только на эту ОС).
- Проверьте, правильно ли заполнен файл `input_urls.txt`.
- Проверьте настройки программы в файле `config.ini`.
- Убедитесь, что программа имеет доступ к сети.
- Проверьте файл `logs/app.log` для получения дополнительной информации.

---

## 7. Заявление об ограничении ответственности

Создатели программы приложили максимум усилий для ее стабильной работы. Однако **полное отсутствие ошибок не гарантируется**.
