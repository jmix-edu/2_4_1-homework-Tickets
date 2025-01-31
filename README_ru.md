### Подготовленный проект
Данный проект содержит следующие элементы из предметной области "бронирование авиабилетов":
- Сущности Airline (авиакомпания), Airport (аэропорт), Flight (рейс), Ticket (билет).
- Spring бин TicketService с уже реализованными операциями над данными:
  - searchFlights() - долгая операция по поиску билетов с заданными параметрами фильтра.
  - saveTicket() - бронирование и сохранение билета в БД.

### Задание
В этом задании требуется реализовать пользовательский интерфейс приложения: экраны просмотра и редактирования справочных данных,
а также экраны поиска, бронирования и просмотра авиабилетов.

#### 1. Экраны справочников Airline и Airport
Создайте в файле главного меню приложения отдельное меню верхнего уровня - "Reference data".
- Используйте дизайнер главного меню.
- Выберите и назначьте для этого меню подходящую иконку.

Создайте CRUD экран для Airline:
- Используйте шаблон Master-detail view.
- Поместите экран в меню Reference data. 

Создайте CRUD экраны для Airport:
- Используйте шаблон Entity list and detail views для одновременного создания обоих экранов.
- Поместите экран просмотра списка (list view) в меню Reference data.
- Экран редактора должен открываться как модальный диалог. Содержимое диалога должно быть поджато по ширине и высоте, без лишнего пустого пространства.

#### 2. Экраны справочника Flight
Создайте CRUD экраны для рейсов (Flight):
- Используйте шаблон Entity list and detail views.
- Поместите экран просмотра списка в меню Reference data.
- Как в таблице рейсов, так и в форме редактора должны присутствовать ссылочные поля: fromAirport, toAirport, airline.

Донастройте экран редактора:
- Он должен открываться как модальный диалог.
- Поменяйте компонент ввода для airline на другой - на поле для асинхронного поиска по строке (EntityComboBox with items fetch callback).
Опции должны искаться по критерию "атрибут name содержит", без учета регистра.   

Реализуйте простой статичный фильтр по нескольким полям в экране Flights List View:
- Удалите стандартный Filter из экрана.
- На его место поместите GroupBox с горизонтальным расположением, и в нем поля фильтра:
  - Airport - список с аэропортами, EntityComboBox.
  - Take off from - поле типа дата-время.
  - Take off to - поле типа дата-время.
- Реализовать логику фильтрации прямо в экране через jpql *условия запросов* (jpql <condition/>) для загрузчика данных (loader) рейсов. Условия следующие:
  - Значение в фильтре "Airport" должно совпадать с "From Airport" рейса ИЛИ с "To Airport" рейса 
  - "Take off date" рейса >= значению в "Take off from" 
  - "Take off date" рейса < значения в "Take off to" 
- Настроить связь между полями фильтра и загрузчиком данных - по правилам фасета DataLoadCoordinator (декларативно в XML).
Фильтр должен применяться сразу после изменения значений в полях.

#### 3. Экран поиска авиабилетов
Создайте экран для поиска и бронирования авиабилетов "Ticket reservation".
- Используйте шаблон Blank view при создании экрана.
- Поместите пункт меню для экрана на верхний уровень.
- Выберите и присвойте пункту меню подходящую иконку.

Экран состоит из пользовательского фильтра по нескольким полям, таблицы рейсов и индикатора прогресса.

Требования к фильтру:
- Располагается сверху экрана, на всю ширину. Реализован как GroupBox с полями и кнопкой. Подпись группы - Filter. Содержит поля:
 - From - список аэропортов, EntityComboBox.
 - To - список аэропортов, EntityComboBox.
 - Departure date - поле даты. Использовать компонент, показывающий только дату.
 - Кнопка Search - запускает поиск. Подобрать подходящую иконку для кнопки.

Таблица рейсов:
 - Отображает найденные рейсы.
 - Столбцы: Number, From airport, To airport, Airline, Take off date.
 - Без стандартных действий (без CRUD).
 - Занимает все свободное место по горизонтали и вертикали.

Загрузка данных в таблицу:
 - Логика загрузки уже реализована в сервисе - TicketService#searchFlights(), необходимо вызывать этот метод. 
 - Метод ищет рейсы долго, несколько секунд, поэтому для загрузки данных необходимо запускать фоновую задачу (BackgroundTask или UiAsyncTasks).
 - Загрузка инициируется по нажатию кнопки Search пользователем.  
 - Перед началом загрузки проверить, что хотя бы одно поле фильтра заполнено.
Если все пустые - то вывести уведомление-warning "Please fill at least one filter field".
 - Поместить на экран индикатор прогресса поиска (progressBar), неопределённый. Например, между фильтром и таблицей, на всю ширину.
На время работы фоновой задачи по загрузке данных - показывать этот индикатор (в остальное время он должен быть скрыт).

#### 4. Интерфейс для бронирования и просмотра авиабилета.
Создайте отдельный экран для просмотра авиабилета (Ticket):
- Используйте соответствующий шаблон.
- Экран должен отображать данные без возможности редактирования.
- Отображать на экране все поля по билету: reservationId, passportNumber, passengerName, telephone.
- И вдобавок отображать поля по его рейсу: number, airline, fromAirport, toAirport, takeOffDate (с помощью *контейнера свойств* instanceContainer).

Добавьте в экран TicketReservation действие по бронированию билета:
- Действие должно быть доступно как генерируемая колонка (Renderer) в таблице рейсов.
- Подпись колонки - "Actions".
- Содержимое колонки - ссылка (Button) "Reserve", по нажатию запускает процесс покупки билета на соответствующий рейс.

Последовательность действий по покупке билета:
- Показать диалог для ввода дополнительных полей билета.
- Создать билет (Ticket), заполнить все его поля и сохранить в БД через метод TicketService#saveTicket().
- После этого открыть экран просмотра с созданным билетом в диалоговом режиме. 

Диалог для ввода дополнительных полей билета:
- Вместо реализации своего экрана-диалога (прямого наследника класса View или его реализаций), добавить в текущий экран InputDialog для ввода параметров, и использовать его (dialogs.createInputDialog()).
- Заголовок диалога - "Reserve ticket"
- Набор кнопок: OK и Cancel.
- Параметры: Passenger name, Passport number, Telephone. Все - строковые, обязательные.
- Если диалог закрыт по Cancel - прервать процесс покупки билета.
- Введенные пользователем значения проставить в создаваемый Ticket.
