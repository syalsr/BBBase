# OSI - Open Systems Interconnection
Модель взаимодействия открытых систем - принята в качестве стандарта комитетом стандартизации ISO в 1983 г. Открытая - значит построенная на открытых спецификациях, т.е. общедоступных.

OSI не является сетевой архитектурой, т.к. не определяет протоколы, они описаны в отдельных стандартах. OSI должна состоять из 7 уровней:

![[OSI.excalidraw]]

1. Физический уровень - Передача битов по каналу связи, основная задача представить биты информации в виде сигналов которые передаются по [[Каналы связи]], на этом уровне работают концентраторы(hub)
2. [[Канальный уровень]] 
3. [[Сетевой уровень]] - Объединяет сети, построенные на основе разных технологий
	1. Создание составной сети, согласование различий в сетях
	2. Адресация (сетевые или глобальные адреса)
	3. Определение маршрута пересылки пакетов в составной сети (маршрутизация)
4. [[Транспортный уровень]] - Обеспечивает передачу данных между процессами на хостах.
	1. Управление надежностью: Может предоставлять надежность выше, чем у сети и Наиболее популярный сервис защищенный от ошибок канал с гарантированным порядком следования сообщений
	2. Сквозной уровень - Сообщения доставляются от источника адресату и Предыдущие уровни используют принцип звеньев цепи
5. [[Сеансовый уровень]] - Позволяет устанавливать сеансы связи
	1. Управление диалогом (очерёдность передачи сообщений)
	2. Управление маркерами (предотвращение одновременного выполнения критичной операции)
	3. Синхронизация (метки в сообщениях для возобновления передачи в случае сбоя)
6. [[Представления уровень]] - Обеспечивает согласование синтаксиса и семантики передаваемых данных
	1. Форматы представления символов
	2. Форматы чисел
	3. Шифрование и дешифрование
7.[[ Прикладной уровень]] - Набор приложений, полезных пользователям
	1. Гипертекстовые Web страницы
	2. Социальные сети
	3. Видео и аудио связь
	4. Электронная почта
	5. Доступ к разделяемым файлам
	6. и многое другое

Сетевое оборудование
| OSI        | Оборудование              |
| ---------- | ------------------------- |
| Сетевой    | Маршрутизатор             |
| Канальный  | Коммутатор, точка доступа |
| Физический | Концентратор               |

На практике OSI не используется.

Протоколы сессионного уровне позволяют выполнять докачку если закачка прервалась, следят за состоянием сессии

Уровень представлений - преобразование данных, конвертации. Если узел которому вы отправляете файл, не понимает формат, то файл как-то преобразуется

Уровень приложений - http ftp