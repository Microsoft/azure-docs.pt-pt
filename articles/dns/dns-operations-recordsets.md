---
title: Gerir os registos dNS em Azure DNS usando o Azure PowerShell / Microsoft Docs
description: Gerir os recordes e registos do DNS do DNS do Azure ao hospedar o seu domínio no Azure DNS. Todos os comandos PowerShell para operações em recordes e recordes.
services: dns
documentationcenter: na
author: rohinkoul
manager: timlt
ms.assetid: 7136a373-0682-471c-9c28-9e00d2add9c2
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.custom: H1Hack27Feb2017
ms.workload: infrastructure-services
ms.date: 12/21/2016
ms.author: rohink
ms.openlocfilehash: b9244d9b2bdc9cb20195bbc103c0b1eb48a9de63
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76932538"
---
# <a name="manage-dns-records-and-recordsets-in-azure-dns-using-azure-powershell"></a>Gerir registos e recordes de DNS em DNS Azure usando O Azure PowerShell

> [!div class="op_single_selector"]
> * [Portal do Azure](dns-operations-recordsets-portal.md)
> * [ClI clássico azure](dns-operations-recordsets-cli-nodejs.md)
> * [Azure CLI](dns-operations-recordsets-cli.md)
> * [PowerShell](dns-operations-recordsets.md)

Este artigo mostra-lhe como gerir os registos dNS para a sua zona DNS utilizando o Azure PowerShell. Os registos DNS também podem ser geridos utilizando o [azure CLI](dns-operations-recordsets-cli.md) cross-platform ou o [portal Azure](dns-operations-recordsets-portal.md).

Os exemplos deste artigo assumem que já [instalou o Azure PowerShell, assinou e criou uma zona DNS](dns-operations-dnszones.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="introduction"></a>Introdução

Antes de criar registos DNS no DNS do Azure, terá primeiro de compreender como o DNS do Azure organiza os registos DNS em conjuntos de registos DNS.

[!INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]

Para obter mais informações sobre os registos DNS no DNS do Azure, veja [Zonas e registos DNS](dns-zones-records.md).


## <a name="create-a-new-dns-record"></a>Criar um novo recorde de DNS

Se o seu novo disco tiver o mesmo nome e tipo que um disco existente, precisa [adicioná-lo ao conjunto](#add-a-record-to-an-existing-record-set)de registos existente . Se o seu novo disco tiver um nome e tipo diferentes para todos os registos existentes, precisa de criar um novo recorde. 

### <a name="create-a-records-in-a-new-record-set"></a>Criar registos 'A' num novo recorde

Pode criar conjuntos de registos com o cmdlet `New-AzDnsRecordSet`. Ao criar um conjunto de discos, é necessário especificar o nome do disco, a zona, a hora de viver (TTL), o tipo de disco e os registos a criar.

Os parâmetros para adicionar registos a um conjunto de registos variam consoante o tipo do conjunto de registos. Por exemplo, ao utilizar um conjunto de registos do tipo 'A', é necessário especificar o endereço IP utilizando o parâmetro `-IPv4Address`. Outros parâmetros são usados para outros tipos de discos. Consulte exemplos adicionais de tipo de gravação para mais detalhes.

O exemplo seguinte cria um conjunto de registos com o nome relativo 'www' na Zona DNS 'contoso.com'. O nome totalmente qualificado do conjunto de recordes é "www.contoso.com". O tipo de disco é 'A', e o TTL é de 3600 segundos. O conjunto de registos contém um único recorde, com o endereço IP '1.2.3.4'.

```powershell
New-AzDnsRecordSet -Name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -IPv4Address "1.2.3.4") 
```

Para criar um recorde estabelecido no 'ápice' de uma zona (neste caso,\@'contoso.com'), utilize o nome do conjunto de registos ' (excluindo as aspas):

```powershell
New-AzDnsRecordSet -Name "@" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -IPv4Address "1.2.3.4") 
```

Se precisar de criar um conjunto de discos contendo mais de um disco, primeiro `New-AzDnsRecordSet` crie uma matriz local e adicione os registos, em seguida, passe a matriz para o seguinte:

```powershell
$aRecords = @()
$aRecords += New-AzDnsRecordConfig -IPv4Address "1.2.3.4"
$aRecords += New-AzDnsRecordConfig -IPv4Address "2.3.4.5"
New-AzDnsRecordSet -Name www –ZoneName "contoso.com" -ResourceGroupName MyResourceGroup -Ttl 3600 -RecordType A -DnsRecords $aRecords
```

[Os metadados de conjuntos](dns-zones-records.md#tags-and-metadata) de registo podem ser usados para associar dados específicos da aplicação a cada conjunto de registos, como pares de valor-chave. O exemplo seguinte mostra como criar um conjunto de recordes com duas entradas de metadados, "dept=finança" e "ambiente=produção".

```powershell
New-AzDnsRecordSet -Name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -IPv4Address "1.2.3.4") -Metadata @{ dept="finance"; environment="production" } 
```

O Azure DNS também suporta conjuntos de discos 'vazios', que podem funcionar como um espaço reservado para reservar um nome DNS antes de criar registos DNS. Os conjuntos de registos vazios são visíveis no plano de controlo DoNs Azure, mas aparecem nos servidores de nome SN Azure. O exemplo seguinte cria um conjunto de registos vazio:

```powershell
New-AzDnsRecordSet -Name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords @()
```

## <a name="create-records-of-other-types"></a>Criar registos de outros tipos

Tendo visto em detalhe como criar registos 'A', os seguintes exemplos mostram como criar registos de outros tipos de discos suportados pelo Azure DNS.

Em cada caso, mostramos como criar um recorde contendo um único recorde. Os exemplos anteriores dos registos 'A' podem ser adaptados para criar conjuntos de registos de outros tipos que contenham vários registos, com metadados ou para criar conjuntos de registos vazios.

Não damos um exemplo para criar um conjunto de registos SOA, uma vez que os SOAs são criados e eliminados com cada zona DNS e não podem ser criados ou eliminados separadamente. No entanto, [o SOA pode ser modificado, como mostra um exemplo posterior](#to-modify-an-soa-record).

### <a name="create-an-aaaa-record-set-with-a-single-record"></a>Criar um conjunto de registos AAAA com um único registo

```powershell
New-AzDnsRecordSet -Name "test-aaaa" -RecordType AAAA -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Ipv6Address "2607:f8b0:4009:1803::1005") 
```

### <a name="create-a-caa-record-set-with-a-single-record"></a>Crie um recorde caa com um único recorde

```powershell
New-AzDnsRecordSet -Name "test-caa" -RecordType CAA -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Caaflags 0 -CaaTag "issue" -CaaValue "ca1.contoso.com") 
```

### <a name="create-a-cname-record-set-with-a-single-record"></a>Criar um conjunto de registos CNAME com um único registo

> [!NOTE]
> As normas DNS não permitem registos CNAME`-Name '@'`no ápice de uma zona , nem permitem estabelecidos recordes que contenham mais de um recorde.
> 
> Para mais informações, consulte [os registos do CNAME](dns-zones-records.md#cname-records).


```powershell
New-AzDnsRecordSet -Name "test-cname" -RecordType CNAME -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Cname "www.contoso.com") 
```

### <a name="create-an-mx-record-set-with-a-single-record"></a>Criar um conjunto de registos MX com um único registo

Neste exemplo, usamos o nome\@do disco ' para criar um registo MX no ápice da zona (neste caso, 'contoso.com').


```powershell
New-AzDnsRecordSet -Name "@" -RecordType MX -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Exchange "mail.contoso.com" -Preference 5) 
```

### <a name="create-an-ns-record-set-with-a-single-record"></a>Criar um conjunto de registos NS com um único registo

```powershell
New-AzDnsRecordSet -Name "test-ns" -RecordType NS -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Nsdname "ns1.contoso.com") 
```

### <a name="create-a-ptr-record-set-with-a-single-record"></a>Criar um conjunto de registos PTR com um único registo

Neste caso, 'my-arpa-zone.com' representa a zona de procura inversa ARPA que representa a sua gama IP. Cada conjunto de registos PTR nesta zona corresponde a um endereço IP neste intervalo de IPs. O nome de registo '10' é o último octeto do endereço IP dentro desta gama IP representado por este registo.

```powershell
New-AzDnsRecordSet -Name 10 -RecordType PTR -ZoneName "my-arpa-zone.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Ptrdname "myservice.contoso.com") 
```

### <a name="create-an-srv-record-set-with-a-single-record"></a>Criar um conjunto de registos SRV com um único registo

Ao criar um conjunto de [registos SRV,](dns-zones-records.md#srv-records)especifique o * \_serviço* e * \_* o protocolo no nome do conjunto de registos. Não há necessidade de\@incluir ' no nome do recorde definido ao criar um recorde SRV estabelecido no ápice da zona.

```powershell
New-AzDnsRecordSet -Name "_sip._tls" -RecordType SRV -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Priority 0 -Weight 5 -Port 8080 -Target "sip.contoso.com") 
```


### <a name="create-a-txt-record-set-with-a-single-record"></a>Crie um recorde de TXT com um único recorde

O exemplo que se segue mostra como criar um disco TXT. Para obter mais informações sobre o comprimento máximo da corda suportado nos registos TXT, consulte [os registos TXT](dns-zones-records.md#txt-records).

```powershell
New-AzDnsRecordSet -Name "test-txt" -RecordType TXT -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Value "This is a TXT record") 
```


## <a name="get-a-record-set"></a>Obter um recorde

Para recuperar um conjunto de `Get-AzDnsRecordSet`registos existente, utilize . Este cmdlet devolve um objeto local que representa o recorde estabelecido no DNS Azure.

Tal `New-AzDnsRecordSet`como acontece, o nome do conjunto de registos dado deve ser um nome *relativo,* o que significa que deve excluir o nome da zona. Também precisa especificar o tipo de registo e a zona que contém o conjunto de discos.

O exemplo que se segue mostra como recuperar um conjunto de recordes. Neste exemplo, a zona é `-ZoneName` especificada utilizando os e `-ResourceGroupName` parâmetros.

```powershell
$rs = Get-AzDnsRecordSet -Name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
```

Em alternativa, também pode especificar a zona utilizando `-Zone` um objeto de zona, passado utilizando o parâmetro.

```powershell
$zone = Get-AzDnsZone -Name "contoso.com" -ResourceGroupName "MyResourceGroup"
$rs = Get-AzDnsRecordSet -Name "www" -RecordType A -Zone $zone
```

## <a name="list-record-sets"></a>Conjuntos de registos de listas

Também pode `Get-AzDnsZone` utilizar para listar conjuntos de discos `-Name` numa `-RecordType` zona, omitindo os e/ou parâmetros.

O exemplo seguinte devolve todos os recordes estabelecidos na zona:

```powershell
$recordsets = Get-AzDnsRecordSet -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
```

O exemplo seguinte mostra como todos os conjuntos de registode um dado tipo podem ser recuperados especificando o tipo de registo enquanto omitem o nome do conjunto de registos:

```powershell
$recordsets = Get-AzDnsRecordSet -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
```

Para recuperar todos os conjuntos de discos com um determinado nome, através de tipos de discos, é necessário recuperar todos os conjuntos de discos e, em seguida, filtrar os resultados:

```powershell
$recordsets = Get-AzDnsRecordSet -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" | where {$_.Name.Equals("www")}
```

Em todos os exemplos acima referidos, a `-ZoneName` zona `-ResourceGroupName`pode ser especificada utilizando os parâmetros e parâmetros (como mostrado), ou especificando um objeto de zona:

```powershell
$zone = Get-AzDnsZone -Name "contoso.com" -ResourceGroupName "MyResourceGroup"
$recordsets = Get-AzDnsRecordSet -Zone $zone
```

## <a name="add-a-record-to-an-existing-record-set"></a>Adicione um recorde a um recorde existente

Para adicionar um recorde a um recorde existente, siga os seguintes três passos:

1. Obtenha o recorde existente

    ```powershell
    $rs = Get-AzDnsRecordSet -Name www –ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -RecordType A
    ```

2. Adicione o novo recorde ao recorde local. Esta é uma operação off-line.

    ```powershell
    Add-AzDnsRecordConfig -RecordSet $rs -Ipv4Address "5.6.7.8"
    ```

3. Comprometa a mudança de volta ao serviço Azure DNS. 

    ```powershell
    Set-AzDnsRecordSet -RecordSet $rs
    ```

A `Set-AzDnsRecordSet` utilização *substitui* o registo existente estabelecido no Azure DNS (e todos os registos que contém) com o conjunto de registos especificado. Os controlos de [etag](dns-zones-records.md#etags) são utilizados para garantir que as alterações simultâneas não sejam substituídas. Pode utilizar o `-Overwrite` interruptor opcional para suprimir estes controlos.

Esta sequência de operações também pode ser *canalizada,* o que significa que passa o objeto de conjunto de registos utilizando o tubo em vez de o passar como parâmetro:

```powershell
Get-AzDnsRecordSet -Name "www" –ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -RecordType A | Add-AzDnsRecordConfig -Ipv4Address "5.6.7.8" | Set-AzDnsRecordSet
```

Os exemplos acima mostram como adicionar um recorde 'A' a um conjunto de registos existente do tipo 'A'. Uma sequência de operações semelhante é usada para adicionar registos `-Ipv4Address` a `Add-AzDnsRecordConfig` conjuntos de registos de outros tipos, substituindo o parâmetro de outros parâmetros específicos de cada tipo de registo. Os parâmetros para cada tipo de `New-AzDnsRecordConfig` disco são os mesmos que para o cmdlet, como mostram os exemplos de tipo de registo adicional acima.

Os conjuntos de registos do tipo 'CNAME' ou 'SOA' não podem conter mais do que um registo. Esta restrição decorre das normas DNS. Não é uma limitação do DNS Azure.

## <a name="remove-a-record-from-an-existing-record-set"></a>Remova um recorde de um conjunto de recordes existente

O processo de remoção de um registo de um conjunto de registos é semelhante ao processo para adicionar um recorde a um recorde existente:

1. Obtenha o recorde existente

    ```powershell
    $rs = Get-AzDnsRecordSet -Name www –ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -RecordType A
    ```

2. Retire o registo do objeto de conjunto de registos local. Esta é uma operação off-line. O registo que está a ser removido deve ser uma correspondência exata com um recorde existente em todos os parâmetros.

    ```powershell
    Remove-AzDnsRecordConfig -RecordSet $rs -Ipv4Address "5.6.7.8"
    ```

3. Comprometa a mudança de volta ao serviço Azure DNS. Utilize o `-Overwrite` interruptor opcional para suprimir [as verificações do Etag](dns-zones-records.md#etags) para alterações simultâneas.

    ```powershell
    Set-AzDnsRecordSet -RecordSet $Rs
    ```

A utilização da sequência acima para remover o último registo de um conjunto de registos não elimina o conjunto de registos, mas deixa um conjunto de registos vazio. Para remover um disco inteiramente estabelecido, consulte [Apagar um conjunto de registos](#delete-a-record-set).

Da mesma forma que a adição de registos a um conjunto de registos, a sequência de operações para remover um conjunto de registos também pode ser canalizada:

```powershell
Get-AzDnsRecordSet -Name www –ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -RecordType A | Remove-AzDnsRecordConfig -Ipv4Address "5.6.7.8" | Set-AzDnsRecordSet
```

Diferentes tipos de registosão suportados através da `Remove-AzDnsRecordSet`passagem dos parâmetros específicos do tipo adequados para . Os parâmetros para cada tipo de `New-AzDnsRecordConfig` disco são os mesmos que para o cmdlet, como mostram os exemplos de tipo de registo adicional acima.


## <a name="modify-an-existing-record-set"></a>Modificar um conjunto de registos existente

Os passos para modificar um conjunto de registos existente são semelhantes aos passos que toma ao adicionar ou remover registos de um conjunto de registos:

1. Recupere o registo existente, utilizando `Get-AzDnsRecordSet`.
2. Modificar o objeto estabelecido local por:
    * Adicionar ou remover registos
    * Alteração dos parâmetros dos registos existentes
    * Alterar os metadados e tempo de vida (TTL)
3. Comprometa as suas `Set-AzDnsRecordSet` alterações utilizando o cmdlet. Isto *substitui* o recorde existente estabelecido no DNS Azure com o conjunto de registos especificado.

Ao `Set-AzDnsRecordSet`utilizar, as [verificações do Etag](dns-zones-records.md#etags) são utilizadas para garantir que as alterações simultâneas não sejam substituídas. Pode utilizar o `-Overwrite` interruptor opcional para suprimir estes controlos.

### <a name="to-update-a-record-in-an-existing-record-set"></a>Para atualizar um recorde num conjunto de recordes existente

Neste exemplo, alteramos o endereço IP de um registo 'A' existente:

```powershell
$rs = Get-AzDnsRecordSet -name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
$rs.Records[0].Ipv4Address = "9.8.7.6"
Set-AzDnsRecordSet -RecordSet $rs
```

### <a name="to-modify-an-soa-record"></a>Para modificar um registo SOA

Não é possível adicionar ou remover registos do registo SOA criado automaticamente no ápice da zona (`-Name "@"`incluindo marcas de cotação). No entanto, pode modificar qualquer um dos parâmetros dentro do registo SOA (exceto "Host") e o conjunto de discos TTL.

O exemplo que se segue mostra como alterar a propriedade de *e-mail* do registo SOA:

```powershell
$rs = Get-AzDnsRecordSet -Name "@" -RecordType SOA -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
$rs.Records[0].Email = "admin.contoso.com"
Set-AzDnsRecordSet -RecordSet $rs
```

### <a name="to-modify-ns-records-at-the-zone-apex"></a>Para modificar os registos de NS no ápice da zona

O registo NS estabelecido no ápice da zona é automaticamente criado com cada zona DNS. Contém os nomes dos servidores de nome SN Azure atribuídos à zona.

Pode adicionar servidores de nome adicionais a este conjunto de registos NS, para suportar domínios de co-hospedagem com mais de um fornecedor DNS. Também pode modificar o TTL e os metadados para este conjunto de registos. No entanto, não é possível remover ou modificar os servidores de nome DNS Do DNS do Azure pré-povoados.

Note que isto se aplica apenas ao registo nS estabelecido no ápice da zona. Outros conjuntos de registos de NS na sua zona (como usado para delegar zonas infantis) podem ser modificados sem restrições.

O exemplo seguinte mostra como adicionar um servidor de nome adicional ao registo NS estabelecido no ápice da zona:

```powershell
$rs = Get-AzDnsRecordSet -Name "@" -RecordType NS -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
Add-AzDnsRecordConfig -RecordSet $rs -Nsdname ns1.myotherdnsprovider.com
Set-AzDnsRecordSet -RecordSet $rs
```

### <a name="to-modify-record-set-metadata"></a>Para modificar metadados de conjunto de registos

[Os metadados de conjuntos](dns-zones-records.md#tags-and-metadata) de registo podem ser usados para associar dados específicos da aplicação a cada conjunto de registos, como pares de valor-chave.

O exemplo que se segue mostra como modificar os metadados de um conjunto de registos existente:

```powershell
# Get the record set
$rs = Get-AzDnsRecordSet -Name www -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"

# Add 'dept=finance' name-value pair
$rs.Metadata.Add('dept', 'finance') 

# Remove metadata item named 'environment'
$rs.Metadata.Remove('environment')  

# Commit changes
Set-AzDnsRecordSet -RecordSet $rs
```


## <a name="delete-a-record-set"></a>Apagar um conjunto de registos

Os conjuntos de registopodem `Remove-AzDnsRecordSet` ser eliminados utilizando o cmdlet. A eliminação de um conjunto de registos também elimina todos os registos dentro do conjunto de registos.

> [!NOTE]
> Não é possível eliminar os conjuntos de registoS`-Name '@'`SOA e NS no ápice da zona ( ).  O Azure DNS criou-os automaticamente quando a zona foi criada e elimina-as automaticamente quando a zona é eliminada.

O exemplo que se segue mostra como apagar um conjunto de registos. Neste exemplo, o nome do conjunto de registos, o tipo de conjunto de registos, o nome da zona e o grupo de recursos são especificados explicitamente.

```powershell
Remove-AzDnsRecordSet -Name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
```

Em alternativa, o conjunto de registos pode ser especificado pelo nome e pelo tipo, e a zona especificada com um objeto:

```powershell
$zone = Get-AzDnsZone -Name "contoso.com" -ResourceGroupName "MyResourceGroup"
Remove-AzDnsRecordSet -Name "www" -RecordType A -Zone $zone
```

Como terceira opção, o conjunto de registos em si pode ser especificado usando um objeto de conjunto de registos:

```powershell
$rs = Get-AzDnsRecordSet -Name www -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
Remove-AzDnsRecordSet -RecordSet $rs
```

Quando especifica o conjunto de registos a eliminar utilizando um objeto de conjunto de registos, as [verificações do Etag](dns-zones-records.md#etags) são utilizadas para garantir que as alterações simultâneas não sejam eliminadas. Pode utilizar o `-Overwrite` interruptor opcional para suprimir estes controlos.

O objeto definido de gravação também pode ser canalizado em vez de ser passado como parâmetro:

```powershell
Get-AzDnsRecordSet -Name www -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" | Remove-AzDnsRecordSet
```

## <a name="confirmation-prompts"></a>Pedidos de confirmação

Todos os cmdlets `New-AzDnsRecordSet`, `Set-AzDnsRecordSet` e `Remove-AzDnsRecordSet` suportam pedidos de confirmação.

Cada cmdlet solicita confirmação `$ConfirmPreference` se a variável `Medium` de preferência PowerShell tem um valor ou inferior. Uma vez que `$ConfirmPreference` `High`o valor predefinido para o is é , estas solicitações não são dadas quando se utilizam as definições padrão powerShell.

Pode substituir a definição `$ConfirmPreference` atual com o parâmetro `-Confirm`. Se especificar `-Confirm` ou `-Confirm:$True` , o cmdlet irá pedir-lhe a confirmação antes de ser executado. Se especificar `-Confirm:$False`, o cmdlet não solicita a confirmação. 

Para obter mais informações sobre `-Confirm` e `$ConfirmPreference`, veja [Sobre as Variáveis de Preferência](/powershell/module/microsoft.powershell.core/about/about_preference_variables).

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [zonas e registos em Azure DNS.](dns-zones-records.md)
<br>
Aprenda a [proteger as suas zonas e registos](dns-protect-zones-recordsets.md) ao utilizar o Azure DNS.
<br>
Reveja a documentação de referência do [Azure DNS PowerShell](/powershell/module/az.dns).
