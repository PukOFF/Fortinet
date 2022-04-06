# **Задача 1**:

\ color {red} {**Настройка Captive Portal для возможности скачивания**} $

### Результат:

**Cattive Portal** работает, но приходится "мудрить" так как *change of authorized* не работает

***

# **Задача 2**: 

**Получение имени пользователя при аторизации рабочей станции по dot1x или mab**

### Результат:
    
**FortiNAC** не получает имя пользователя даже при использовании **Persistent Agent**

***

# **Задача 3**:

**Возможность контролировать Multihoming подключение**

### Результат:

* Отслеживание происходит при активации пункта **Detected Multihoming**;
* **Advanced Scan Control** не позволяет настроить действия для MultiHoming. Дополнительнные настройки сканирования работую по стаусу ;профиля сканирования **Scan: OS-Check**. Если сканирование пройдет, а двойного подключения не будет, то общий результат будет **True**;


# **Задача 4**:

**Возможность авторизации локальных пользователей FortiNAC**

* Отключить на конечной рабочей стнации проверки аутентификации средствами 802.1x
* Создать пользователя в локальной базе FortiNAC (**Users & Hosts > User Accounts**):
    * *User ID:* local;
    * *Password:* password;

* Создать правило профилирования для применения MAB (**Users & Hosts > Device Profiling Rules**):
    * *Registration:* Automatic;
    * *Type:* Windows;
    * *Role:* Windows_MAB;
    * *Register as:* Device in Host View;
* Порт коммутатора должен находиться в следующих группах:
    * Force Authentiction;
    * Reset Force Default;
    * Role Based Access;
* Создать профиль для хоста (**User/Host Profiles**):
    * *Name:* Corporate_MAB;
    * *Who/What by Attribute:*
        * Host:
            * Persistent Agent: Yes;
            * Persistent Communication: Yes;
            * Role: Windows_MAB;
* Создать политику аутентификации для использования локальной базы данных (**Policy & Objects**):
    * *Name:* Local_Authentication;
    * *User/Host Profile:* Corporate_MAB;
    * *Authetication Configuration:* Local:
        * *Name:* Local;
        * *Invalid Credentials Message:* Invalid Credential;
        * *Enable Authentication:*:
            * *Time in Producton before Authentication:* 0;
            * *Time Offline before Deauthentication:* 10;
        
* Создать политики сетевого доступа (**Policy & Objects**):
    * Default_Policy(Для доступа в изолоированный VLAN и последующей регистрации при профилроовании):
        * *Name:* Default_Policy;
        * *User/Host Profile:* Global Authentication Conversion;
        * *Network Access Configuration:* Isolate
    * Only_MAB_Policy(Для доступа в корпоративный VLAN и последующей аутентификацией средствами Persistent Agent):
        * *Name:* Only_MAB_Policy;
        * *User/Host Profile:* Corporate_MAB;
        * *Network Access Configuration:* Corporate;
* В настройках Persistent Agent установить тип аутентификации Local(**System>Settings>Persistent Agent>Credential Configuration**):
    * Enable Registration
    * Register As Device
    * Authetication Type: Local