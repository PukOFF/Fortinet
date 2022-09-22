<span style="color:red">**Задача**</span>: *Обеспечить интеграцию FortiNAC и SOAR для автоматизации реагирования на инцинденты ИБ*

<span style="color:green">**Решение:**</span>

---
```
Возможны следующие действия:
    * Disable host by ip
    * Disable host by mac
    * Set role (by id)
    * Disable port on switch
```
Для изменения Role необходимо знать ID хоста. ID можно вытащить их запроса хоста **Get host by MAC** или **Get host by IP**


При изменение роли хоста, политика изменяется динамически. В случае опечатки в поле **Role** при запросе POST ошибка не возникает. Параметр устанавливается согласно передаваемого значения.

Для возможности изменения настроек порта коммутатора необходимо знать параметре **`legacyDBID`**, который доступен из запроса **Get Port** чере API.
Для изменения настроек порта в теле указываются следующие данные:


### <span style="color:yellow">*PortPropertiesRequest*</span>

    * ids (optional)
        array[Long] format: int64
    * name (optional)
        String
    * adminStatus (optional)
        Boolean
    * defaultVlan (optional)
        String
    * currentVlan (optional)
        String
    * newUplink (optional)
        Integer format: int32
    * clearUplink (optional)
        Boolean
    * note (optional)
        String


