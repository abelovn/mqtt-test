
#  MQTT + Telegraf + InfluxDB + Grafana + Nginx

Test project

![App Screenshot](https://snipboard.io/6as24I.jpg)



## Deployment

To deploy this project run

```bash
git clone https://github.com/abelovn/mqtt-test.git

cd mqtt-test/

docker-compose build --no-cache && docker-compose up
```


## Comment



настроить influxdb через реверс прокси не удалось. Проблема известа (https://github.com/influxdata/influxdb/issues/21009)

В графане данное поведение можно решить черз root_url в ini файле. В InfluxDB данная настрока не применяется.

Как вариант (улучшение) всесто InfluxDB можно использовать\проверить Prometheus или всю статику от InfluxDB перенести в контейнер с nginx.

Данный способ затратен и нужно учитовать время\стоимость доработки.


## Натройка Influx, Telegraf, Grafana


[habr article link ](https://habr.com/en/post/680902)

Дальше использована ручная натройка Influx, Telegraf, Grafana
(вязто из habr)

### InfluxDB 
по адресу http://localhost_IP:8086 


и нажимаем “GET STARTED” и первым делом создаем учетную запись, указываем название организации и корзины для данных (Organization Name и Bucket Name -> IoT). После заполнения нажать «CONTINUE», а в следующем окне «QUICK START»


### Telegraf 
Для этого нам сперва необходимо скопировать токен доступа из InfluxDB. 

В левом боковом меню нажимаем Load Data -> API Tokens -> admin’s Token -> копируем через “COPY TO CLIPBOARD” и сохраняем где-нибудь.


Отредактировать конфигурационный файл  Telegraf:
```bash
vi telegraf/conf/telegraf.conf 
```
Заменить токен и IP на свои <localhost IP>

Отправить данные на брокер MQTT Mosquito

Зайти в контейр, выполнив
```bash
docker exec -it mqtt-test_mosquitto_1 sh
```
Отправить данные
```bash
mosquitto_pub -h <localhost IP> -p 1883 -t "Sample Data" -m "36.5" -u "user" -P "pass"
```

А в InfluxDB проверить, что в Data Explorer -> IoT -> mqtt_consumer есть топик Sample Dataи данные. Для этого необходимо настроить пространство ( нужно поменять тип график на Gauge, aggregate function указать last и нажать SUBMIT):

Cкопировать скрипт получения данных из БД. Делается это через SCRIPT EDITOR (рядом с SUBMIT со скриншота выше).

### Grafana

http://localhost_IP/grafana

Первая авторизация происходит по логину/паролю admin/admin, после чего система попросит установить новый пароль. 

После чего попадаем на приветственное пространство Grafana. Далее, нам необходимо добавить интеграцию (подключение) с InfluxDB. Нажимаем значок настроек -> Data Sources -> Add Data Sourse -> InfluxDB -> Query Language (Flux) -> URL -> Basic Auth (off) -> Organozation(IoT) -> Default Bicket (IoT) -> Token (Берется в InfluxDB -> Load Data -> Api Tokens -> Admin`stoken) -> Save&Test (должен быть ok)

После того, как Grafana успешно подключилась к InfluxDB и обнаружила все корзины с данными, можно добавить дашборд: Dashboards -> New Dashboard -> Add New Panel -> вставляем данные их SCRIPT EDITOR -> Apply -> Save -> Имя дашборда

![App Screenshot](https://snipboard.io/YUiXqO.jpg)

![App Screenshot](https://snipboard.io/AuiKNW.jpg)


### Дополнительно

Также в окружении подняты контейнры nodered

http://localhost_IP:1880

Как доп ф-ционал можно осуществлять публикация и подписка MQTT с помощью Node Red


## Optimizations

 - изпользовать Prometheus
 - перенести статику в Nginx
 - выпусти SSL саертификаты и настроить безопастное подключение
 - осуществить backup\restore docker volumes для переноса настроек Influxdb и дашбордов

