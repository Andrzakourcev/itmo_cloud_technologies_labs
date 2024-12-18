## Лабораторная работа 1. Знакомство с IaaS, PaaS, SaaS сервисами в облаке на примере Amazon Web Services (AWS). Создание сервисной модели.
# Сведения о работе

Цель работы: Знакомство с облачными сервисами. Понимание уровней абстракции над инфраструктурой в облаке. Формирование понимания типов потребления сервисов в сервисной-модели.

# Дано:

Слепок данных биллинга от провайдера после небольшой обработки в виде SQL-параметров. Символ % в начале/конце означает, что перед/после него может стоять любой набор символов.

Образец итогового соответствия, что желательно получить в конце.
# Необходимо:

Импортировать файл .csv в Excel или любую другую программу работы с таблицами. Для Excel делается на вкладке Данные – Из текстового / csv файла – выбрать файл, разделитель – точка с запятой.

Распределить потребление сервисов по иерархии, чтобы можно было провести анализ от большего к меньшему (напр. От всех вычислительных ресурсов Compute дойти до конкретного типа использования - Выделенной стойка в датацентре Dedicated host usage).

Алгоритм работы: Сопоставить входящие данные от провайдера с его же документацией. Написать в соответствие колонкам справа значения 5 колонок слева, которые бы однозначно классифицировали тип сервиса. Для столбцов IT Tower и Service Family значения можно выбрать из образца.

# Результат работы: 
(https://docs.google.com/spreadsheets/d/1uJIfzOqDRW3ZjUph5xOvjxGkGMHd80CZ/edit?gid=2090885109#gid=2090885109)

# Ход работы:
Основная часть информации была взята из официальной документации Amazon Web Services, там уже есть распределение всех сервисов и их краткое описание.

# Категории

1. Cloud services - подразделяется в нашем случае на Security and Identity, Analytics, Artificial Intelligence, Monitoring. 
   
   Amazon Macie - сервис для безопасности и конфиденциальности данных. Используется для обнаружения, классификации и защиты личных и важных данных в хранилищах.
   
   Amazon MSK - сервис для запуска и управления кластерами Apache Kafka. Используется для обработки и анализа потоков данных. Также может использоваться для потоковой аналитики, интеграции и сбора данных.
   
   Amazon Polly - сервис преобразования текста в речь. Используется для создания естественного звучания голосов на множестве языков, подходящего для автоматизированных приложений, виртуальных ассистентов и так далее.
   
   Amazon Personalize - сервис машинного обучения для создания персонализированных рекомендаций. Применяется для рекомендаций товаров, контента или предложений пользователям в реальном времени. Включает обучение моделей, управление пропускной способностью и расчет стоимости использования.

   Amazon S3 -  облачное хранилище объектов для хранения и извлечения данных в любом объеме. В нашем случае имеется тэг про мониторинг, так что Service Usage Type был указан как Monitoring Automation.

2. Storage
   
   В данном IT Tower рассматривалось только облачное хранилище S3, в зависимости от тега Usage Type получилось разное распределение по типу сервиса. Тем самым можно сделать вывод о том, что S3 используется для совершенно разных задач в зависимости от ваших запросов.
   
   Data Transfer - передача данных между Amazon S3 и внешними системами или внутри AWS.

   AWS Usage - общий объем использования AWS в байтах.
   
   Data Retrieval - извлечение данных из хранилища (стандартное или из определенных классов 
   хранения, таких как SIA или Zone-IA).
   
   S3 Replication - автоматическое копирование объектов между бакетами для обеспечения отказоустойчивости и соблюдения требований.
   
   CloudFront Integration - интеграция с CloudFront для ускорения доставки контента через глобальную сеть CDN.
   
   Timed Storage - хранение данных с учетом времени в разных классах.
   
   Early Deletion Fees - плата за удаление объектов до окончания минимального периода хранения в классах Glacier, Intelligent-Tiering, One Zone-IA, и Standard-IA.
   
   Storage Classes Requests - запросы к объектам в разных классах хранения.
   
   Tagging - использование тегов для классификации и управления затратами на хранение.
   
   Inventory - перечисление объектов в бакете для аудита и аналитики.
   
   Batch Operations - массовое выполнение задач, таких как копирование или тегирование объектов.
   
   RRS Byte Hours - хранение объектов с пониженной избыточностью (Reduced Redundancy Storage).
   
   Glacier Staging - временное размещение данных при загрузке в Glacier.

   Intelligent-Tiering Storage - автоматическое распределение данных между уровнями хранения в зависимости от частоты доступа.
   
   Отдельно выделен сервис AmazonES с типом - CloudFront. Он нужен для обработки передачи данных через сеть CDN для повышения скорости доступа к данным пользователей.

3. Search & Analytics

   Amazon ES (Elasticsearch Service) - это сервис для развертывани и управления поисковой и аналитической платформой ElasticSearch, которая используется для индексирования и поиска больших объемов данных, а также для анализа и мониторинга логов, метрик и других данных в реальном времени.
   
   Additional Costs - дополнительные затраты, включая налоги и сборы за использование сервиса.
   
   Data Transfer - передача данных из Amazon Elasticsearch Service за пределы AWS или в другие регионы. В зависимости от того идет передача между сервисами AWS или за пределы, может браться разная стоимость, она расчитывается по объему трафика. Естественно, между сервисами AWS передача дешевле.
   
   Storage - использование хранилища SSD (GP2) для высокопроизводительных операций Elasticsearch.
   
   Storage - использование HDD-хранилища для менее интенсивных операций, требующих большего объема при меньшей цене.
   
   Instance Usage - затраты на запуск и использование экземпляров Elasticsearch, включая вычислительные ресурсы.
   
   High Usage Instances - экземпляры с высоким уровнем загрузки, предназначенные для тяжелых рабочих нагрузок.
   
   Provisioned IOPS - настройка ввода-вывода на определенный уровень производительности для операций Elasticsearch, требующих высокой скорости работы с диском.

# Выводы

После проделанной работы можно сделать вывод о том, что AWS действительно сильный инструмент, который применяется что огромного количество задач - аналитика, вычисления, мониторинг, машинное обучение, базы данных, разработка ПО, защита данных и многое другое. Для каждого есть уже готовые сервисы от AWS, который применяется под каждый конкретную задачу. Мы познакомились с классификацией этих сервисов, чтобы в будущем применять их. Мы классифицировали и описали для чего применятся каждый из них. 












   
