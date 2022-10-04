**Создание подсетей из которых будут запросы**

```powershell
Add-DnsServerClientSubnet -Name "Yakimanka_Subnet" -IPv4Subnet "10.2.56.0/24"
Add-DnsServerClientSubnet -Name "Ordinka_Subnet" -IPv4Subnet "10.2.24.0/24"

```
**Посмотреть список созаднных подсетей**

```powershell
Get-DnsServerClientSubnet
```

**Создание DNS области для каждой подсети**

```powershell
Add-DnsServerZoneScope -ZoneName “infosec.com” -Name “Yakimanka_Scope”
Add-DnsServerZoneScope -ZoneName “infosec.com” -Name “Ordinka_Scope”
```

**Создание DNS-записей**

```powershell
Add-DnsServerResourceRecord -ZoneName “infosec.com” -A -Name “fnac” -IPv4Address “10.2.56.4” -ZoneScope “Yakimanka_Scope”
Add-DnsServerResourceRecord -ZoneName “infosec.com” -A -Name “fnac” -IPv4Address “10.2.25.3” -ZoneScope “Ordinka_Scope”
```

**Вывод всех DNS записей для заданной области**

```powershell
Get-DnsServerResourceRecord -ZoneName “infosec.com” -ZoneScope "Yakimanka_Scope"
```

**Создание DNS политики, которые связывают IP подсети, DNS области и A записи**

```powershell
Add-DnsServerQueryResolutionPolicy -Name “Yakimanka_Policy” -Action ALLOW -ClientSubnet “eq,Yakimanka_Subnet” -ZoneScope “Yakimanka_Scope,1” -ZoneName “infosec.com” –PassThru
Add-DnsServerQueryResolutionPolicy -Name “Ordinka_Policy” -Action ALLOW -ClientSubnet “eq,Ordinka_Subnet” -ZoneScope “Ordinka_Scope,1” -ZoneName “infosec.com” -PassThru
```

**Удаление политики DNS**

---
```powershell
Remove-DnsServerQueryResolutionPolicy -ZoneName "infosec.com" -Name "YakimankaPolicy"
Remove-DnsServerQueryResolutionPolicy -ZoneName "infosec.com" -Name "OrdinkaPolicy"
```
---

**Ввод всех политик DNS**

```powershell
Get-DnsServerQueryResolutionPolicy -ZoneName "infosec.com"
```

**Запретить DNS серверу возвращать DNS адреса для определенного пространства имен (домена**

```powershell
Add-DnsServerQueryResolutionPolicy -Name 'BlockFidhingPolicy' -Action IGNORE -FQDN "EQ,*.cberbank.ru"
```