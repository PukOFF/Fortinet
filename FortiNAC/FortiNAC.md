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

***

### L3 Polling

***


### Change VLAN 
There are also some options that affect the management behavior for this device. Если **VLAN Switching Enabled**
не выбран, FortiNAC никогда не изменит VLAN на данном устройстве.

***

### Persistent Agent (Setting server IP)
* Настройка адреса сервера для Windows машин настраивается через реестр
* Взаимодействие с FortiNAC каждые 15 мин

## Host Manual Registration

### Register as Device
* Параметр «Device in Host View» моделирует устройство как хост, и оно будет отображаться и управляться как хост.
* Представление «Device in Topology» отобразит хост в дереве топологии. Обратите внимание, что политики безопасности не
применяется к устройствам, смоделированным с помощью параметра «Device in Topology».
* Параметр «Device in Host View and Topology» отобразит устройство в обоих местах.
* Раскрывающийся список Device Type используется для ручного назначения типа устройства и будет включать в себя все значения по умолчанию и административно созданные типы устройств

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
Image Type - хост зарегистрирован как устройство

## Host State

* Состояние **«Rogue»** присваивается, если устройство не классифицируется в базе данных FortiNAC. Это может быть что угодно — принтер, кард-ридер, конечная станция и так далее. Мошеннические устройства представлены значком с изображением ноутбук с вопросительным знаком на экране.
* Состояние **«At-Risk»** указывает на то, что хосту не удалось выполнить сканирование. Это может быть проверка соответствия политике или административное сканирование. Узлы, находящиеся в группе риска, представлены значком ноутбука с красным крестом в верхней части экрана. Правый угол экрана ноутбука.
* Состояние **«Disabled»** указывает на то, что хост был административно отключен в FortiNAC. Это может выполняться вручную пользователем с правами администратора или в результате автоматизированного действия. Отключенный хост представлен значком с изображением ноутбука с крестиком над ним.
* Состояние **«Disabled»**  указывает на то, что ни одна запись пользователя в настоящее время не связана с данным входом в систему. Отслеживание пользователей с помощью агентов — это один из способов сбора информации о вошедших в систему пользователях. А не
аутентифицированный хост представлен значком, изображающим ноутбук с красной буквой А в кружке в левом верхнем углу. Угол экрана ноутбука

**«Isolation networks»** are used to enforce access based on the state of a host. Each isolation network uses a
captive portal web page to inform and assist the end user. In wired environments, these isolation networks are
defined as VLAN IDs. In wireless environments, how they are defined may vary from vendor to vendor. The
isolation network values used will depend on how traffic is segmented by that vendor. For example, Fortinet
wireless access would be defined using a VLAN name, while Aruba would use a role value. Note that host
state alone does not cause isolation. Isolation occurs only if the host point of connection is configured for
enforcement for the current host state.
**«Registration»** is the process of on-boarding a host. This process will convert a host from being a rogue to being
classified. The registration process, when carried out as an on-boarding exercise, takes place in the
registration isolation network. The portal page is configured to provide on-boarding options.
The Quarantine isolation network is where hosts with an at-risk state are isolated. Remediation is the process
of an at-risk host resolving the issues that caused it to be marked as at-risk. The portal page is configured to
provide remediation steps to assist the user in clearing the at-risk state.
The Dead End isolation network is where hosts that have been designated as disabled are moved. By default
there is no external access, not even to domains on the allowed domain list. The Dead End portal page is
configured to inform the end user that they have been denied access to the network.
The Authentication captive network is where hosts that have no logged in user are isolated. The
authentication portal is configured to provide end-user authentication
The Isolation network is a special network that will handle hosts of any of the abnormal states. This means
hosts of different states can all be isolated to a single network but continue to get customized captive portal
pages based on their state
The Shared Media network is another special purpose semi-captive network. Within this network, all hosts are
designated as being in one of two groups: hosts that are in any state other than normal, and hosts that are in
the normal state. For hosts that are in an abnormal state, this network works like the isolation network, with
each host getting the appropriate captive portal for its state. Hosts that are trusted will be granted production
access. This special network allows for access control to be extended to non-managed points of connection,
such as unsupported or non-manageable switches or access points

## Logic to Determine Isolation



Логика, используемая FortiNAC при принятии решения об изоляции хоста, представлена ​​на этом слайде. Когда конечная точка подключается к сети, FortiNAC ищет ее в базе данных, чтобы определить ее состояние. Если хост не существует в базе данных и не соответствует ни одному включенному правилу профилирования устройств, он будет добавлен и присвоено состояние **«Rogue»**. FortiNAC использует первый столбец в качестве столбца для ввода, начиная сверху и работает вниз. Например, если к сети подключен хост со статусом мошенника, FortiNAC будет использовать третья строка вниз, чтобы определить, необходима ли изоляция. После того, как соответствующая строка была определена,
Затем FortiNAC читает вправо, применяя логику И между первым и вторым столбцами. Если столбец один и два столбца в той же строке оба истинны, то хост будет перемещен в ограниченную сеть, показанную на колонка третья. В графическом интерфейсе хост будет представлен значком в четвертом столбце.
Например, если хост со статусом **«Rogue»** подключается к порту в группе портов принудительной регистрации, FortiNAC изолирует этот хост, переместив его в присоединенную сеть регистрации. Все верхние четыре ряда функционируют точно так же, за небольшим исключением первой строки, где параметр местоположения определяется устройством
группа, а не группа портов.

***

## System Groups

The result of this process is as follows:
Unknown or rogue endpoints that connect to any port in Building 1, which is any port in any of the three floor
groups, will be isolated to the registration captive network. A host that has failed a policy or administrative
scan, and has had its host state changed to at-risk, would be isolated to the quarantine captive network if it
connected to any port in the second or third-floor port groups. Any other host state would result in the host
being granted default network access. A change in the point of connection could also change the provisioned
access. For example, a rogue host connecting to a conference room port it would be granted default access.
An at-risk host connecting to a conference room port or a first-floor port would also be granted default access.
Those examples assume that the ports within the conference room ports group are not also members of any
other group. The logic that applies to these results was shown on the previous slide in the logic table


## Dynamic Change ACL
Select the Host Based option in the CLI Configurations section will prevent FortiNAC from making the
VLAN change, and instead it will only apply the CLI configuration. Host-based CLI configurations are designed
to dynamically insert or remove ACL entries, enforcing isolation using ACLs
