### L2 Polling
The three ways that L2 polling is triggered are:
• Manual polling: Manual polling is initiated when an administrative user right-clicks the device in the
topology tree and selects Poll for L2 (Hosts) Info, or clicks Network > L2 Polling.
• Scheduled: L2 polling is scheduled in the Network > L2 Polling view. You can change the default
scheduled intervals.
• Link Traps: Link traps received from an edge device trigger FortiNAC to perform an L2 poll to update its
awareness of devices that are connected on that edge device. The traps that trigger the poll are: Linkup,
Linkdown, WarmStart, and ColdStart. This trigger keeps FortiNAC up-to-date in real-time as devices
connect to and disconnect from edge device

### L3 Polling

***


### Change VLAN 
There are also some options that affect the management behavior for this device. If **VLAN Switching Enabled**
is not selected, FortiNAC will never change a VLAN on this device

***

### Persistent Agent (Setting server IP)
* Настройка адркса сервера для Windows машин настраивается через реестр
* Взаимодействие с FortiNAC каждые 15 мин

## Host Manual Registration

### Register as Device
* Параметр «Device in Host View» моделирует устройство как хост, и оно будет отображаться и управляться как хост.
* Представление «Device in Topology» отобразит хост в дереве топологии. Обратите внимание, что политики безопасности не
применяется к устройствам, смоделированным с помощью параметра «Device in Topology».
* Параметр «Device in Host View and Topology» отобразит устройство в обоих местах.
* Раскрывающийся список Device Type используется для ручного назначения типа устройства и будет включать в себя все значения по умолчанию и
административно созданные типы устройств

### Register as Host

* «Register Host to User» — это параметр по умолчанию, и его следует выбирать, если хост и запись пользователя должны иметь
постоянная ассоциация. Это обычно имеет место в ситуациях BYOD, таких как гости и подрядчики.
* «Register Host as Device» не устанавливает постоянную связь между конкретным пользователем и
хост, и это обычно используется для корпоративных активов или устройств IoT. Это эквивалентно устройству в
Параметр Host View из предыдущего слайда

## Troubleshooting

Client [ -ip | -mac ]

Host type «DynamicClient» - хост зарегистрирован 
Host type «RogueDynamicClient» - хост не зарегистрирован 
