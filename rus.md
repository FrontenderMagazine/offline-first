# Проектирование веб-приложений по принципу «автономность прежде всего»

Когда дело касается разработки приложений, мы часто исходим из предположения, 
что наши пользователи очень сильно похожи на нас. Мы представляем что они 
пользуются самыми современными устройствами, последними версиями программного 
обеспечения и имеют максимально быстрое подключение к сети. И даже если у нас 
есть целый зоопарк из старых устройств и браузеров для тестирования, наибольшее 
количество времени мы уделяем разработке под привычные современные настольные 
устройства с постоянным подключением к сети. 

Сбой подключения к сети или низкая скорость подключения кажутся нам всего лишь 
временной проблемой, необходимой реакцией на которую является разве что 
сообщение об ошибке. С этой точки зрения есть соблазн считать проблемы 
соединения с интернетом, мобильным или ещё каким-либо, чем-то что со временем 
решится само по себе, когда зона покрытия расширится и скорость соединения 
станет быстрее. Такой подход действительно работает пока наши пользователи 
пребывают над землёй в больших хорошо развитых, но не слишком плотно населённых 
городах.

Однако что же происходит когда они опускаются в метро, садятся в самолёт, 
предпринимают длительное путешествие по суше или же переезжают в сельскую 
местность? Или же когда они оказываются в неблагоприятном угле комнаты или 
становятся частью огромной толпы? Наш тщательно спроектированный интерфейс 
приложения становится источником огорчения, потому он слишком сильно зависит от 
эфемерной связи с сервером. 

Такая зависимость от сети вызвана пренебрежением прописной истины: отсутствие 
подключения к сети - это суровая реальность. Если вы передвигаетесь, в какой-то 
момент подключение к сети будет утрачено. Тем не менее, в этом нет ничего 
страшного. С этой проблемой можно разобраться. 

## Обобщим достижения на сегодняшний день

Раньше веб-приложения полностью зависели от сервера: на нём выполнялась вся 
работа, а в клиенте просто выводился результат. Любой сбой подключения был 
существенной проблемой: без интернета приложение использовать было невозможно.

Эта проблема решена, отчасти благодаря усовершенствованию клиентов, которое 
позволяет большинству алгоритмов приложения выполняться в браузере, например, 
как в Google Docs. Однако для обеспечения качественного пользовательского опыта 
в режиме вне сети нужна возможность сохранения данных в клиенте и их 
синхронизации с массивом данных на сервере. К счастью, встроенные в браузер базы 
данных развиваются и для этого уже существует большое количество решений вроде 
[derby.js][1], [Lawnchair][2], [Hoodie][3], [Firebase][4], [remotestorage.io][5], 
[Sencha touch][6] и др., так что поиск решений для технических аспектов 
становится более простым.

Однако у нас есть проблемы поважнее и посложнее: разработка приложений и их 
интерфейсов с учётом переменного подключения к сети ведёт к изобилию новых 
сценариев и трудностей.

Как известно, несколько образцов пользовательского опыта с реализованным 
подходом «автономность прежде всего» уже существует, один из них является 
особенно распространённым: ваша электронная почта. Письма сохраняются в папке 
исходящей почты даже когда вы вне сети. Когда вы подключаетесь к интернету, они 
отправляются. Просто и незаметно, и главное работает.

Опыт приёма входящей почты не менее хорошо отлажен: при каждом переподключении к 
сети новые сообщения с сервера размещаются в верхней части списка входящей почты. 
В промежутке между подключениями у вас есть доступ к более менее полному архиву 
всех сообщений, полученных до этого момента, так что приложение никогда не 
оказывается пустым. Все три сценария (сбой при отправке данных с клиента, сбой 
при получении данных с сервера/при отправке с сервера, доступность локальных 
данных в режиме оффлайн) хорошо продуманы.

С электронной почтой дела обстоят просто, как видите. Она не поддаётся 
редактированию, легко может быть упорядочена в виде списка, состоит 
преимущественно из текста, исключает возможность конфликтов, кроме того, принцип 
хранения локальных копий писем хорошо знаком всем пользователям. Однако 
перспективы намного более широкие. Как организовать эти сценарии, к примеру, в 
приложении для совместного рисования? Что если мы имеем дело не с неизменными 
документами вроде электронных писем, а с маркерами карты с редактируемыми 
параметрами, MIDI-треками, задачами и действиями? Браузер - это наиболее 
оптимальная рабочая среда для нового приложения и если учитывать [Закон Этвуда][7] 
(«Любое приложение, которое может быть написано на JavaScript, в конце концов 
будет написано на JavaScript»), какие ещё немыслимые действия мы будем выполнять 
в браузере через несколько лет? Стоит ли и дальше считать режим вне сети 
пограничным случаем и ограничивать реакцию на него нестабильными компонентами, 
раздражающими пустыми страницами и сообщениями об ошибках с паническим оттенком?

Опыт использования вебсайта или приложения без подключения к интернету должен 
быть значительно лучшим, менее обескураживающим и более насыщенным возможностями. 
Нам нужен аналог систематизации отзывчивого веб-дизайна: четкий перечень 
руководств и шаблонов для мира с множеством устройств и без подключения к сети.

## Жизненный цикл подключения к сети

У большинства веб-приложений есть две точки отказа, связанных с подключением к 
сети: отправка данных на сервер и получение данных с сервера/отправка данных с 
сервера. В зависимости от функциональных задач приложения можно

* вывести или демонстративно спрятать уведомление о состоянии подключения и его 
изменениях (например, клиент чата выводит для пользователя сообщение, что новые 
сообщения не будут тотчас отосланы);
* активировать функциональные возможности создания и редактирования на стороне 
клиента в режиме вне сети и уведомить пользователей что их данные в полной 
безопасности и будут отосланы на сервер после подключения (представьте 
приложение для обмена фотографиями, которое позволяет создавать и делиться ими в 
любых условиях); и 
* деактивировать, модифицировать или даже спрятать опции, которые не работают 
без подключения к сети, вместо того чтобы позволить людям безуспешно пробовать 
ими воспользоваться (представьте кнопку «отправить», которая знает когда нужно 
превратиться в кнопку «отправить позже, при подключении к сети»).

Другие проблемы возникают когда состояние подключения изменяется в процессе 
работы с приложением, например, сервер хочет обновить объект или изображение, на 
которое пользователь смотрит, или даже редактирует, в данный момент. В таком 
случае нужно

* уведомить пользователя о наличии новых, возможно несовместимых, данных; и
* при необходимости предоставить пользователю удобный способ для устранения 
конфликта данных.

Давайте рассмотрим примеры из реальной жизни.

## Сценарии при возникновения проблем с подключением

### Потеря локальных данных

Отключение сети во время использования Google Docs в браузере отличном от Chrome 
может привести к обескураживающему результату: редактирование документа 
становится невозможным. Хотя вы всё ещё можете читать текст документа, 
копировать его части также нельзя. С текстом или таблицей нельзя производить 
никаких действий, даже скопировать их в другую программу чтобы продолжить работу 
в ней. И в то же время, в сравнении с предыдущими версиями, нынешняя значительно 
улучшена, так как раньше огромное оповещение об отключении от сети перекрывало 
документ так, что его нельзя было даже просмотреть. 

Это распространённое явление в нативных и веб-приложениях: данные, к которым у 
вас только что был доступ, внезапно становятся недоступными когда пропадает 
подключение к сети. По возможности приложения должны оставаться в неизменном 
состоянии и сохранять возможность доступа к данным в них даже если они не могут 
быть изменены. Для этого необходимо сохранение локальных данных, которые могут 
быть использованы в качестве фолбека когда доступ к серверу невозможен, так 
чтобы пользователи не испытывали неудобства от пустого приложения.

### Восприятие отключения от сети в качестве ошибки

Хватит считать отсутствие подключения ошибкой. Ваше приложение должно уметь 
справляться с разрывами подключения и продолжить работу с максимальной 
изящностью. Не отображайте окна, которые не могут быть заполнены данными и 
убедитесь что сообщения об ошибках правильно сформулированы. Возьмём Instagram: 
когда пользователь не может опубликовать фотографию, Instagram называет это 
неудавшимся действием вместо того чтобы заверить пользователя что изображение не 
было потеряно и оно просто будет опубликовано позже. Ничего страшного не 
случилось. Возможно лучше даже будет частично изменить надписи в интерфейсе в 
зависимости от состояния подключения к сети, например «сохранить» заменить на 
«сохранить на устройстве».

Иногда может потребоваться полная блокировка некоторых возможностей, однако чаще 
можно обойтись и без неё. Например:

* Если нельзя обновить новостную ленту, отобразите старую ленту и 
соответствующее сообщение. Не стоит удалять старые данные, затем делать попытку 
получить новые, обнаруживать что эта попытка оказалась неудачной и в результате 
показывать пользователю бесполезный пустой  интерфейс. 
* Если ваше приложение предусматривает возможность локального создания данных 
пользователем, позвольте им воспользоваться этой возможностью и сообщите им что 
созданные данные будут сохранены и отправлены на сервер чуть позже. Можно также 
вывести дополнительный запрос на подтверждение согласия пользователя перед их 
отправкой. Опять на ум приходит Instagram: он знает где было сделано фото, 
однако в офлайн-режиме не может спросить у Foursquare как называется это место. 
При возобновлении подключения к сети Instagram мог бы вывести пользователю 
предложение вернуться к фотографии и выбрать расположение с помощью Foursquare. 

### Разрешение конфликтов между разными версиями данных

Если ваше приложение предлагает возможность совместного редактирования или 
какую-либо другую форму совместного использования на нескольких устройствах, в 
определённый момент вполне вероятно создание конфликтующих версий объектов. Его 
нельзя предотвратить, но можно предложить простой способ разрешения конфликта, 
который был бы удобен даже для тех, кто и не слышал о понятии конфликт 
синхронизации.

Возьмём сервис Evernote, одной из главных задач которого является синхронизация 
заметок: конфликты решаются с помощью простой конкатенации обеих версий заметки. 
Для больше чем двух строчек текста это потребовало бы чрезмерных когнитивных 
затрат и последующего редактирования.

[Draft][8], с другой стороны, предлагает простое и красивое решение конфликтов 
при совместной работе. В нём обе версии и различия между ними отображаются в 
трёх отдельных колонках, для каждого различия предложена кнопка «принять» и 
«сбросить». Интуитивное и визуально привлекательное решение конфликтов между 
версиями без сомнений возможно, по крайней мере для текста.

Не всегда есть необходимость в решении с пошаговым подтверждением изменений. В 
большинстве случаев достаточно предоставить удобный интерфейс с выделением 
изменений и дать возможность пользователю выбрать, которая из версий должна 
победить в данном конфликте.

Нас, однако, поджидают и другие типы конфликтов и множество из них не связаны с 
текстом: спорное расположение маркеров на карте, цвета диаграмм, линии в 
рисунках и бесконечное множество других, которые мы пока даже не представляем.

Но не для всех технических проблем необходимы технические решения. Представьте 
двух официантов с беспроводными устройствами для оформления заказа в большом 
многоэтажном ресторане. Один подсоединён к серверу ресторана, а другой находится 
на самом верхнем этаже, где подключение постоянно пропадает. Оба приняли заказы 
на бутылку редкого дорого вина, которая есть в ресторане в одном экземпляре. 
Устройство официанта, у которого нет подключения к серверу, не может знать об 
этом конфликте. Однако оно может знать о риске конфликта (низком количестве на 
складе, собственных проблемах с подключением к сети) и посоветовать официанту 
соответствующий ответ клиенту за столиком («Прекрасный выбор. Позвольте мне 
проверить есть ли это вино сейчас в наличии»).

### Предусмотрение потребностей пользователя

В некоторых случаях приложения могут предусмотрительно предпринимать действия с 
низкой затратой ресурсов для последствующего улучшения пользовательского опыта. 
Когда программа Google Maps определяет, что я подключился к wifi в другой стране, 
она могла бы быстро кешировать данные о моем окружении для дальнейшего 
использования без подключения к сети или в режиме роуминга. 

Однако в большинстве случаев контент слишком велик для предварительного 
кеширования, например видео с сайта новостей. В таких случаях пользователь 
должен подтвердить свое желание синхронизировать данные, что предполагает 
скачивание видео на устройство и просмотр в другом приложении. Любой контекст 
для видео, представленный в режиме онлайн, например соответствующая информация 
или ветки комментариев, будут потеряны, как и возможность для пользователя 
комментировать видео.

### Обновление хронологических данных

Все эти примеры касаются отправки данных на сервер, однако аспекты связанные с 
отправкой данных с сервера также важны: что можно сделать когда сервер обновляет 
активную область просмотра и отправляет данные, которые не могут быть добавлены 
в начало списка без причинения неудобств пользователю? Хронологические данные 
часто приводят к такой проблеме.

Например, если вы используете iMessage на нескольких устройствах, сообщения 
иногда отображаются не в хронологической последовательности. iMessage мог бы 
отсортировать их в правильном порядке, все-таки у них есть временные метки, но 
вместо это он отображает их в порядке загрузки на устройство. Это делает новые 
сообщения максимально заметными, но также вносит неразбериху. 

Представьте более интуитивный способ сортировки: сообщения всегда отображаются в 
хронологическом порядке, вне зависимости от того когда они были загружены. На 
первый взгляд такой подход кажется более разумным, однако это значит что вам 
придётся пролистать список к более старым сообщениям чтобы увидеть те, которые 
были загружены только что, так как они были отправлены как ответ на более старые 
сообщения. Что ещё хуже, вы можете не заметить их вообще, так как они могут 
появиться в той части списка, куда вы даже не подумаете долистать.

Если данные отображаются в хронологическом порядке и сама последовательность 
данных имеет существенное значение, как например в чате (в отличие от 
электронной почты, где сообщения могут быть сгруппированы в ветки), возникает 
проблема с автономным режимом: данные, загруженные последними не обязательно 
являются самыми свежими и следовательно могут отображаться там, где пользователь 
не станет их искать. Можно сохранять контекст и последовательность, однако 
интерфейс должен также сообщать пользователю положение контента во временном 
пространстве.

### Подготовка к разнообразию типов данных

Многие примеры являются текстовыми, а даже если нет (например, маркер карты), 
для некоторых из них могут использоваться вспомогательные текстовые компоненты 
(например, список маркеров рядом с картой), которые могут упростить обновления и 
уведомления, связанные с синхронизацией.

Однако, все мы понимаем, что количество, разнообразие и сложность веб-приложений 
продолжит расти, а вместе с ними и разнообразие типов данных, с которыми будут 
работать пользователи. Некоторые будут предполагать совместное использование, 
большинство будет использоваться на нескольких устройствах, многие породят новые 
интересные проблемы с синхронизацией. Целесообразно было бы изучать их и 
разрабатывать общую словарную базу для сценариев работы вне сети и их решений.

## Анонимные борцы с проблемами работы вне сети

Когда мы начали спрашивать мнение по поводу этих проблем у разработчиков по 
всему миру, мы удивились насколько много людей поделились своими историями о 
горестях разработки с учётом отсутствия подключения к сети и признали что уже 
сталкивались с похожими проблемами, но никогда не обсуждали их с другими. 
Большинство сражались с ними самостоятельно, сдались или на время отложили их в 
сторону, но все втайне мечтали о том чтобы можно было где-нибудь получить совет 
по разработке приложения для режиме вне сети. 

У нас нет нужды сохранять анонимность. Мы можем взять на вооружение 
опубликованный 13 лет назад [призыв Джона Оллсоппа (John Allsopp)][9] к 
восприятию сети как непостоянной среды с множеством неизвестных и «признанию 
склонности вещей к метаморфозам». Сегодня мы понимаем что это выходит за рамки 
размеров экранов и соотношения их сторон, поддержки свойств и подходов к 
отображению, он включает в том числе и наше подключение к сети.

В этой ещё более непостоянной и несколько пугающей реальности нам будет нужна 
взаимная помощь. Мы должны убедиться что у нас и у наших последователей есть 
надёжные инструменты и шаблоны для того чтобы справиться с сюрпризами всё более 
мобильного мира, это входит в наши интересы и в интересы наших пользователей. 
Веб-разработка является достаточно сложным занятием и без потери лишнего времени 
на регулярное изобретение велосипеда.

Чтобы помочь друг другу и будущим поколениям дизайнеров, разработчиков и 
экспертов по пользовательским интерфейсам, мы приглашаем вас принять участие в 
дискуссии на [offlinefirst.org][10]. Наша конечная цель - создать справочник по 
работе вне сети, который включал бы шаблоны и анти-шаблоны пользовательских 
интерфейсов, описание технологических возможностей и исследования ментальных 
моделей пользователей; создание кладезя знаний, из которого можно было бы 
черпать и в который можно было бы вносить свою лепту, чтобы наши совместные 
усилия и опыт не пропали даром.

Пока мы хотели бы услышать что вы думаете по этому поводу: о вашем опыте в этой 
области, об известных вам технологиях, о ваших приёмах и советах, или просто о 
трудностях, с которыми вы сталкивались. Решить их будет нелегко, но они помогут 
улучшить опыт наших пользователей вне зависимости от места и времени когда они 
нуждаются в наших услугах. Разве не в этом состоит наше предназначение?

[1]: http://derbyjs.com/#introduction_to_racer
[2]: http://brian.io/lawnchair/
[3]: http://hood.ie/
[4]: https://www.firebase.com/
[5]: http://remotestorage.io/
[6]: http://www.sencha.com/learn/taking-sencha-touch-apps-offline/
[7]: http://www.codinghorror.com/blog/2007/07/the-principle-of-least-power.html
[8]: https://draftin.com/
[9]: http://alistapart.com/article/dao
[10]: http://offlinefirst.org/