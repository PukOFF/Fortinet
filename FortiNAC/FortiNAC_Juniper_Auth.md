## **Juniper Command**

---

`set authenticator interface interface-name supplicant multiple` - аутентификация нескольких singlesingle-securemultiple источников

### **Возможные варианты аутентификации**:

* одинпроцентный (`supplicant`) — аутентификация только первого о конце устройства. Всем другим конечным устройствам, которые подключаются к порту позже, разрешен полный доступ без дальнейшей аутентификации. Фактически они могут использовать аутентификацию первого конечного устройства.

* `one-secure supplicant` — позволяет подключаться к порту только одному конечному устройству. Подключаться к другому о конечному устройству не разрешается до тех пор, пока не завершится выход первого устройства.

* `multiple supplicant` — позволяет нескольким конечным устройствам подключаться к порту. Каждое конечное устройство аутентификация идет по отдельности.

`set authenticator interface interface-name  reauthentication interval seconds` - включение повторной аутентификации с заданным интервалом

`set protocols dot1x authenticator interface interface-name no-reauthentication` - отключить реаутентификацию конечного устройства

`set authenticator interface interface-name  supplicant-timeout seconds` - задание интервала ожидания для supplicant

`set authenticator interface interface-name  server-timeout seconds` - время простоя интерфейса перед повторной аутентификаией на RADIUS-сервере

`set authenticator interface interface-name  transmit-period seconds` - время (в секундах) ожидания интерфейса перед повторной отправкой исходных EAPOL к необходимому устройству

`set authenticator interface interface-name  maximum-requests number` -  максимальное количество повторных запросов EAPOL пакета перед истечением сеанса аутентификации

`set authenticator interface interface-name  retries number` -  количество попыток коммутатора аутентификации порта после первоначального сбоя. Порт остается в состоянии ожидания в течение тихого периода после попытки аутентификации

`set server-fail deny` - 

`set protocols dot1x authenticator interface interface-name authentication-order [dot1x mac-radius]` - порядок аутентификации конечного устройства

`insert protocols dot1x authenticator interface interface-name authentication-order authentication-method before authentication-method` - указание очередности использованя методов аутентифкации

`set protocols dot1x authenticator radius-reachability query-period` - настройка периода опроса RADIUS сервера