# Proxy Filter                              

**[English version](https://github.com/RynelHub/proxy-filter/blob/main/README_EN.md)**      
**[中文版](https://github.com/RynelHub/proxy-filter/blob/main/README_CN.md)**

**Актуальная версия:** 1.2.0  
*[Скачайте актуальную версию здесь](https://github.com/RynelHub/proxy-filter/releases/latest)*

*Подробности изменений смотрите в [CHANGELOG.md](https://github.com/RynelHub/proxy-filter/blob/main/CHANGELOG.md)*

---

**Proxy Filter** – Windows-утилита для массовой фильтрации и конвертации подписок **VLESS** и **Trojan**.

**Вход**: URL подписок в форматах **Clash (YAML)** и **V2Ray (base64/plain text)**.

**Выход**: Текстовые файлы с VLESS/Trojan-ссылками, отфильтрованными по наличию TLS/Reality и порта 443, разбитые на удобные части для загрузки в клиенты.

Поддерживается **фильтрация по кодам стран и доменам**.

Обработка **нескольких сотен подписок** занимает не больше 10-15 минут.

---

## 1. 🧐 Так было до **Proxy Filter**...

Вы, скорее всего, уже неоднократно выполняли эти однообразные действия:

- Искали ссылки на подписки с конфигурациями прокси
- Скачивали файлы подписок по этим ссылкам
- Расшифровывали base64 (если конфигурации были закодированы)
- Конвертировали YAML → V2Ray, если использовали другой клиент
- Исправляли ошибки (неправильные отступы, лишние символы, HTML-разметка)
- Фильтровали прокси по нужным параметрам
- Копировали нужные конфигурации в отдельный файл для загрузки в менеджер

😩 И так **каждые несколько дней**...

---

## 2. 🚀 Что делает **Proxy Filter**?

**Proxy Filter** берёт на себя всю рутину!

💡 Теперь вам нужно только вставить URL подписки — программа сделает всё сама:

- Автоматически скачает и обработает файлы
- Расшифрует base64, если это потребуется
- Исправит ошибки в разметке (кавычки, отступы, некорректные символы)
- Преобразует конфигурации между форматами (YAML → V2Ray)
- Отфильтрует прокси по вашим критериям
- Создаст удобные файлы для дальнейшего использования

---

## 3. 💡 Почему **Proxy Filter** — это удобно?

- Простота — нужны только ссылки на подписки
- Скорость — обработка занимает считаные минуты
- Автоматизация — никаких ручных действий!

⚡ Занимайтесь важными делами, а рутину оставьте **Proxy Filter**! 🏖

---

## 4. Входные данные

Для работы программы требуется файл `input_urls.txt`, содержащий список URLs подписок (по одному URL на строку).

Что такое `подписка` (англ. `subscription`)?

Подписка — это веб-страница или файл с перечнем конфигураций прокси-серверов (от десятков до десятков тысяч записей). Иногда их называют `аэропортами`, так как в китайском сообществе пользователей прокси подписки с конфигурациями называют `机场` (читается как "цзичан"), что в переводе и означает "аэропорт".

Файл `input_urls.txt` содержит **примеры подписок** для ознакомления. Однако они могут быть устаревшими или недоступными. 

Вам нужно **самостоятельно** добавить актуальные подписки, найденные в сторонних источниках.

**Proxy Filter поддерживает следующие форматы подписок:**
- Clash (YAML)
- V2Ray (plain text)
- V2Ray (base64 encoded)

---

## 5. Результаты работы программы

После завершения обработки загруженных файлов подписок в папке `output` создаются текстовые файлы, содержащие отфильтрованные конфигурации прокси-серверов.

**Proxy Filter имеет следующие неизменяемые настройки фильтрации:**
- **Обнаруживаемые протоколы:** VLESS, Trojan  
- **Порт:** 443
- **Наличие TLS (включая TLS Reality)**
- **Географическая принадлежность прокси:** согласно значению параметра `COUNTRY_LIST` в файле `config.ini` 
- **Удаление дубликатов конфигураций** (без гарантии полного отсутствия дубликатов в итоговых файлах).  

Каждый итоговый файл будет содержать не более **N** строк конфигураций, согласно значению параметра `CHUNK_SIZE_WRITE` в файле `config.ini`, следующего вида:
- vless://<строка конфигурации>
- trojan://<строка конфигурации>

---

## 6. Основной функционал

### Загрузка файлов с подписками
- Многопоточная загрузка по URL из файла `input_urls.txt`.
- Рандомизация User-Agent и задержки между повторными попытками скачивания.

### Обработка и исправление содержимого файлов
- Исправление кодировки файлов, если необходимо
- Пропуск пустых файлов и файлов с некорректным содержимым
- Автоматическое исправление ошибок в загруженных файлах (отступы, кавычки, html теги и пр.).

### Конвертация форматов файлов
- Преобразование данных из YAML и BASE64 в текстовый формат.

### Возможность настройки маски для фильтрации конфигураций прокси
- Любые домены и поддомены, включая географические

### Возможность настройки максимального количества строк в файлах с отфильтрованными конфигурациями

### Возможность выбора одного из двух режимов фильтрации
- Удаление конфигураций прокси по маске из параметра `COUNTRY_LIST` (если вам нужны все конфигурации из подписки, кроме некоторых), это режим работы по-умолчанию
- Сохранение конфигураций прокси по маске из параметра `COUNTRY_LIST` (если вам нужны только некоторые конфигурации из подписки, а все остальные не нужны)

### Возможность настройки области поиска совпадений с маской из параметра `COUNTRY_LIST`:
- servername
- sni
- host
- строка комментария

### Фильтрация данных
- Исключение неподходящих конфигураций по заданным правилам.
- Попытка определения географической принадлежности серверов (в т.ч. с помощью анализа эмодзи флагов стран в комментариях).

### Удаление дублирующихся конфигураций

### Логирование
- Все операции записываются в файл `logs/app.log`.
- Поддерживается ротация логов (до 3 файлов с максимальным объемом 5 МБ каждый).

### Подсчет времени выполнения
- В конце работы указывается общее время фильтрации.

---

## 7. Инструкция по использованию

1. **Подготовьте файл с URLs подписок:**
   - Откройте файл `input_urls.txt`.
   - Добавьте ссылки на подписки, по одной на строку.
   - Сохраните файл.

2. **Если вы не хотите использовать настройки по-умолчанию, поменяйте их в файле `config.ini`, например:**
   - Укажите коды стран или произвольные домены, для фильтрации итогового списка прокси.
   - Измените режим работы фильтра.
   - Задайте область поиска совпадений.
   - Укажите, нужно ли удалять временные файлы.
   - Сохраните файл.

3. **Запустите программу:**
   - Дважды щелкните на `proxy-filter.exe`.

4. **Ожидайте завершения работы:**
   - После обработки подписок в папке `output` появятся файлы, содержащие отфильтрованные строки конфигураций.

5. **Лог-файл с описанием подробностей работы программы можно найти в папке `logs`**

---

## 8. Особые указания

### Объем данных
- Если подписки содержат большое количество строк (десятки тысяч), программа может работать **дольше**. Это нормально.

### Проверка доступности серверов
- Программа **не проверяет** доступность серверов. Используйте внешние инструменты для этого.

### Работа через прокси
- В настоящее время программа **не поддерживает** системные прокси. Отключите их перед использованием.

---

## 9. Ошибки и обратная связь

Программа поставляется "как есть". Если вы столкнулись с ошибками или необычным поведением:
- Убедитесь, что используете операционную систему Microsoft Windows (текущая версия программы Proxy Filter рассчитана только на эту ОС).
- Проверьте, правильно ли заполнен файл `input_urls.txt`.
- Проверьте настройки программы в файле `config.ini`.
- Отключите системный прокси.
- Убедитесь, что программа имеет доступ к сети.
- Проверьте файл `logs/app.log` для получения дополнительной информации.

Если всё равно ничего не получается, вы можете создать новую **[Issue](https://github.com/RynelHub/proxy-filter/issues/new)**.

Отзывы, комментарии, предложения, критику и другую обратную связь оставляйте в **[Discussions](https://github.com/RynelHub/proxy-filter/discussions/new)**.

Пишите о вашем опыте использования **Proxy Filter**. Давайте вместе сделаем его еще лучше.

---

## 10. Поддержка проекта

Друзья, **Proxy Filter** является некоммерческим проектом. Это означает, что дистрибутив распространяется абсолютно бесплатно, а разработка ведется на энтузиазме автора.

Если вам нравится этот проект, поддержите его материально!

Ваша помощь очень мотивирует, и помогает проекту (и автору) развиваться.

**Спасибо, что используете Proxy Filter!**

### [💰 Поддержать **Proxy Filter**](https://rynelhub.github.io/donations/)

---

## 11. Заявление об ограничении ответственности

Создатель программы прикладывает максимум усилий для обеспечения ее стабильной работы. Однако **полное отсутствие ошибок не гарантируется**.
