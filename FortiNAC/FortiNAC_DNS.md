**Создание подсетей из которых будут запросы**

```powershell
Add-DnsServerClientSubnet -Name "Yakimanka_Subnet" -IPv4Subnet "10.2.56.0/24"
Add-DnsServerClientSubnet -Name "Ordinka_Subnet" -IPv4Subnet "10.2.25.0/24"

```
**Посмотреть список созаднных подсетей**

```powershell
Get-DnsServerClientSubnet
```

**Создание DNS области для каждой подсети**

```powershell
Add-DnsServerZoneScope -ZoneName “infosec.com” -Name “YakimankaScope”
Add-DnsServerZoneScope -ZoneName “infosec.com” -Name “OrdinkaScope”
```

**Создание DNS-записей**

```powershell
Add-DnsServerResourceRecord -ZoneName “infosec.com” -A -Name “fnac” -IPv4Address “10.2.56.4” -ZoneScope “YakimankaScope”
Add-DnsServerResourceRecord -ZoneName “infosec.com” -A -Name “fnac” -IPv4Address “10.2.25.3” -ZoneScope “OrdinkaScope”
```

**Вывод всех DNS записей для заданной области**

```powershell
Get-DnsServerResourceRecord -ZoneName “infosec.com” -ZoneScope "YakimankaScope"
```

**Создание DNS политики, которые связывают IP подсети, DNS области и A записи**

```powershell
Add-DnsServerQueryResolutionPolicy -Name “YakimankaPolicy” -Action ALLOW -ClientSubnet “eq,Yakimanka_Subnet” -ZoneScope “YakimankaScope,1” -ZoneName “infosec.com” –PassThru
Add-DnsServerQueryResolutionPolicy -Name “OrdinkaPolicy” -Action ALLOW -ClientSubnet “eq,Ordinka_Subnet” -ZoneScope “OrdinkaScope,1” -ZoneName “infosec.com” -PassThru
```
**Ввод всех политик DNS**

```powershell
Get-DnsServerQueryResolutionPolicy -ZoneName "infosec.com"
```

**Запретить DNS серверу возвращать DNS адреса для определенного пространства имен (домена**

```powershell
Add-DnsServerQueryResolutionPolicy -Name 'BlockFidhingPolicy' -Action IGNORE -FQDN "EQ,*.cberbank.ru"
```