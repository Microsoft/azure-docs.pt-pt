---
title: Gerir registos DNS em Azure DNS usando Azure PowerShell Microsoft Docs
description: Gerir os recordes de DNS e os registos no Azure DNS ao hospedar o seu domínio no Azure DNS. Todos os comandos PowerShell para operações em recordes e registos.
services: dns
documentationcenter: na
author: rohinkoul
ms.service: dns
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.custom: H1Hack27Feb2017
ms.workload: infrastructure-services
ms.date: 12/21/2016
ms.author: rohink
ms.openlocfilehash: f4e713f54ab4702b21763dc9fc6c7b606f94a945
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94965719"
---
# <a name="manage-dns-records-and-recordsets-in-azure-dns-using-azure-powershell"></a>Gerir registos e registos DNS em Azure DNS usando a Azure PowerShell

> [!div class="op_single_selector"]
> * [Portal do Azure](dns-operations-recordsets-portal.md)
> * [CLI clássico de Azure](./dns-operations-recordsets-cli.md)
> * [CLI do Azure](dns-operations-recordsets-cli.md)
> * [PowerShell](dns-operations-recordsets.md)

Este artigo mostra-lhe como gerir os registos DNS para a sua zona de DNS utilizando o Azure PowerShell. Os registos DNS também podem ser geridos utilizando a plataforma transversal [Azure CLI](dns-operations-recordsets-cli.md) ou o [portal Azure](dns-operations-recordsets-portal.md).

Os exemplos deste artigo assumem que já [instalou o Azure PowerShell, assinou e criou uma zona DE DNS.](dns-operations-dnszones.md)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="introduction"></a>Introdução

Antes de criar registos DNS no DNS do Azure, terá primeiro de compreender como o DNS do Azure organiza os registos DNS em conjuntos de registos DNS.

[!INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]

Para obter mais informações sobre os registos DNS no DNS do Azure, veja [Zonas e registos DNS](dns-zones-records.md).


## <a name="create-a-new-dns-record"></a>Criar um novo registo DNS

Se o seu novo registo tiver o mesmo nome e tipo de registo existente, tem de [o adicionar ao conjunto de registos existente](#add-a-record-to-an-existing-record-set). Se o seu novo disco tiver um nome e um tipo diferentes de todos os registos existentes, tem de criar um novo conjunto de discos. 

### <a name="create-a-records-in-a-new-record-set"></a>Criar registos 'A' num novo conjunto de recordes

Pode criar conjuntos de registos com o cmdlet `New-AzDnsRecordSet`. Ao criar um conjunto de recordes, é necessário especificar o nome do conjunto de registos, a zona, o tempo de vida (TTL), o tipo de gravação e os registos a criar.

Os parâmetros para adicionar registos a um conjunto de registos variam consoante o tipo do conjunto de registos. Por exemplo, ao utilizar um conjunto de registos do tipo 'A', é necessário especificar o endereço IP utilizando o parâmetro `-IPv4Address` . Outros parâmetros são utilizados para outros tipos de registo. Consulte exemplos adicionais de tipo de registo para obter mais detalhes.

O exemplo a seguir cria um conjunto de registos com o nome relativo 'www' na zona de DNS 'contoso.com'. O nome totalmente qualificado do recorde é "www.contoso.com". O tipo de recorde é 'A', e o TTL é de 3600 segundos. O conjunto de registos contém um único recorde, com endereço IP '1.2.3.4'.

```powershell
New-AzDnsRecordSet -Name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -IPv4Address "1.2.3.4") 
```

Para criar um recorde estabelecido no 'ápice' de uma zona (neste caso, 'contoso.com'), utilize o nome de conjunto de \@ registos » (excluindo aspas):

```powershell
New-AzDnsRecordSet -Name "@" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -IPv4Address "1.2.3.4") 
```

Se precisar de criar um conjunto de registos que contenha mais de um registo, primeiro crie uma matriz local e adicione os registos e, em seguida, passe a matriz da `New-AzDnsRecordSet` seguinte forma:

```powershell
$aRecords = @()
$aRecords += New-AzDnsRecordConfig -IPv4Address "1.2.3.4"
$aRecords += New-AzDnsRecordConfig -IPv4Address "2.3.4.5"
New-AzDnsRecordSet -Name www –ZoneName "contoso.com" -ResourceGroupName MyResourceGroup -Ttl 3600 -RecordType A -DnsRecords $aRecords
```

[Os metadados de conjunto de registos](dns-zones-records.md#tags-and-metadata) podem ser usados para associar dados específicos da aplicação a cada conjunto de registos, como pares de valor-chave. O exemplo a seguir mostra como criar um conjunto de recordes com duas entradas de metadados, "dept=finance" e "environment=production".

```powershell
New-AzDnsRecordSet -Name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -IPv4Address "1.2.3.4") -Metadata @{ dept="finance"; environment="production" } 
```

O Azure DNS também suporta conjuntos de discos 'vazios', que podem funcionar como um espaço reservado para reservar um nome DNS antes de criar registos DNS. Os conjuntos de registos vazios são visíveis no plano de controlo Azure DNS, mas aparecem nos servidores de nomes Azure DNS. O exemplo a seguir cria um conjunto de registos vazio:

```powershell
New-AzDnsRecordSet -Name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords @()
```

## <a name="create-records-of-other-types"></a>Criar registos de outros tipos

Tendo visto em detalhe como criar registos 'A', os seguintes exemplos mostram como criar registos de outros tipos de discos suportados pelo Azure DNS.

Em cada caso, mostramos como criar um conjunto de discos contendo um único disco. Os exemplos anteriores para registos 'A' podem ser adaptados para criar conjuntos de registos de outros tipos que contenham múltiplos registos, com metadados, ou para criar conjuntos de registos vazios.

Não damos um exemplo para criar um conjunto de registos SOA, uma vez que os SOAs são criados e eliminados com cada zona de DNS e não podem ser criados ou eliminados separadamente. No entanto, [o SOA pode ser modificado, como mostra um exemplo posterior.](#to-modify-an-soa-record)

### <a name="create-an-aaaa-record-set-with-a-single-record"></a>Criar um conjunto de registos AAAA com um único registo

```powershell
New-AzDnsRecordSet -Name "test-aaaa" -RecordType AAAA -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Ipv6Address "2607:f8b0:4009:1803::1005") 
```

### <a name="create-a-caa-record-set-with-a-single-record"></a>Crie um conjunto de recordes caa com um único recorde

```powershell
New-AzDnsRecordSet -Name "test-caa" -RecordType CAA -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Caaflags 0 -CaaTag "issue" -CaaValue "ca1.contoso.com") 
```

### <a name="create-a-cname-record-set-with-a-single-record"></a>Criar um conjunto de registos CNAME com um único registo

> [!NOTE]
> As normas DNS não permitem registos CNAME no ápice de uma zona ( `-Name '@'` ), nem permitem conjuntos de recordes que contenham mais de um recorde.
> 
> Para mais informações, consulte [os registos da CNAME.](dns-zones-records.md#cname-records)


```powershell
New-AzDnsRecordSet -Name "test-cname" -RecordType CNAME -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Cname "www.contoso.com") 
```

### <a name="create-an-mx-record-set-with-a-single-record"></a>Criar um conjunto de registos MX com um único registo

Neste exemplo, usamos o nome de recorde \@ ' ' para criar um disco MX no ápice da zona (neste caso, 'contoso.com').


```powershell
New-AzDnsRecordSet -Name "@" -RecordType MX -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Exchange "mail.contoso.com" -Preference 5) 
```

### <a name="create-an-ns-record-set-with-a-single-record"></a>Criar um conjunto de registos NS com um único registo

```powershell
New-AzDnsRecordSet -Name "test-ns" -RecordType NS -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Nsdname "ns1.contoso.com") 
```

### <a name="create-a-ptr-record-set-with-a-single-record"></a>Criar um conjunto de registos PTR com um único registo

Neste caso, o 'my-arpa-zone.com' representa a zona de procura inversa arpa que representa o seu intervalo de IP. Cada conjunto de registos PTR nesta zona corresponde a um endereço IP neste intervalo de IPs. O nome de registo '10' é o último octeto do endereço IP dentro deste intervalo IP representado por este registo.

```powershell
New-AzDnsRecordSet -Name 10 -RecordType PTR -ZoneName "my-arpa-zone.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Ptrdname "myservice.contoso.com") 
```

### <a name="create-an-srv-record-set-with-a-single-record"></a>Criar um conjunto de registos SRV com um único registo

Ao criar um [conjunto de registos SRV,](dns-zones-records.md#srv-records)especifique o *\_ serviço* e o *\_ protocolo* no nome do conjunto de registos. Não há necessidade de incluir \@ ' no nome recorde ao criar um recorde DEV estabelecido no ápice da zona.

```powershell
New-AzDnsRecordSet -Name "_sip._tls" -RecordType SRV -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Priority 0 -Weight 5 -Port 8080 -Target "sip.contoso.com") 
```


### <a name="create-a-txt-record-set-with-a-single-record"></a>Crie um conjunto de registos TXT com um único recorde

O exemplo a seguir mostra como criar um registo TXT. Para obter mais informações sobre o comprimento máximo da corda suportado nos registos TXT, consulte [os registos TXT](dns-zones-records.md#txt-records).

```powershell
New-AzDnsRecordSet -Name "test-txt" -RecordType TXT -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Value "This is a TXT record") 
```


## <a name="get-a-record-set"></a>Obter um recorde definido

Para recuperar um recorde existente, `Get-AzDnsRecordSet` utilize. Este cmdlet devolve um objeto local que representa o recorde estabelecido no Azure DNS.

Tal como `New-AzDnsRecordSet` acontece, o nome de conjunto de registos dado deve ser um nome *relativo,* o que significa que deve excluir o nome da zona. Também precisa de especificar o tipo de gravação e a zona que contém o recorde.

O exemplo a seguir mostra como recuperar um recorde. Neste exemplo, a zona é especificada utilizando os `-ZoneName` parâmetros e `-ResourceGroupName` parâmetros.

```powershell
$rs = Get-AzDnsRecordSet -Name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
```

Em alternativa, também pode especificar a zona utilizando um objeto de zona, passado usando o `-Zone` parâmetro.

```powershell
$zone = Get-AzDnsZone -Name "contoso.com" -ResourceGroupName "MyResourceGroup"
$rs = Get-AzDnsRecordSet -Name "www" -RecordType A -Zone $zone
```

## <a name="list-record-sets"></a>Conjuntos de recordes de listas

Também pode utilizar `Get-AzDnsZone` para listar conjuntos de registos numa zona, omitindo os `-Name` parâmetros e/ou. `-RecordType`

O exemplo a seguir devolve todos os recordes na zona:

```powershell
$recordsets = Get-AzDnsRecordSet -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
```

O exemplo a seguir mostra como todos os conjuntos de registos de um determinado tipo podem ser recuperados especificando o tipo de registo enquanto omitem o nome do conjunto de registos:

```powershell
$recordsets = Get-AzDnsRecordSet -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
```

Para recuperar todos os conjuntos de recordes com um nome próprio, através de tipos de registo, é necessário recuperar todos os conjuntos de recordes e, em seguida, filtrar os resultados:

```powershell
$recordsets = Get-AzDnsRecordSet -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" | where {$_.Name.Equals("www")}
```

Em todos os exemplos acima, a zona pode ser especificada através da utilização `-ZoneName` dos parâmetros e `-ResourceGroupName` parâmetros (como mostrado), ou especificando um objeto de zona:

```powershell
$zone = Get-AzDnsZone -Name "contoso.com" -ResourceGroupName "MyResourceGroup"
$recordsets = Get-AzDnsRecordSet -Zone $zone
```

## <a name="add-a-record-to-an-existing-record-set"></a>Adicione um recorde a um conjunto de recordes existente

Para adicionar um recorde a um recorde existente, siga os três passos seguintes:

1. Obtenha o recorde existente

    ```powershell
    $rs = Get-AzDnsRecordSet -Name www –ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -RecordType A
    ```

2. Adicione o novo recorde ao recorde local. Esta é uma operação fora de linha.

    ```powershell
    Add-AzDnsRecordConfig -RecordSet $rs -Ipv4Address "5.6.7.8"
    ```

3. Volte a comprometer a alteração no serviço Azure DNS. 

    ```powershell
    Set-AzDnsRecordSet -RecordSet $rs
    ```

A utilização `Set-AzDnsRecordSet` *substitui* o recorde existente estabelecido no Azure DNS (e todos os registos que contém) pelo conjunto de registos especificado. [Os controlos Etag](dns-zones-records.md#etags) são utilizados para garantir que as alterações simultâneas não sejam substituídas. Pode utilizar o interruptor opcional `-Overwrite` para suprimir estas verificações.

Esta sequência de operações também pode ser *canalizada,* o que significa que você passa o objeto recorde usando o tubo em vez de passá-lo como um parâmetro:

```powershell
Get-AzDnsRecordSet -Name "www" –ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -RecordType A | Add-AzDnsRecordConfig -Ipv4Address "5.6.7.8" | Set-AzDnsRecordSet
```

Os exemplos acima mostram como adicionar um recorde 'A' a um conjunto de registos existente do tipo 'A'. Uma sequência de operações semelhante é usada para adicionar registos a conjuntos de outros tipos, substituindo o `-Ipv4Address` parâmetro de `Add-AzDnsRecordConfig` outros parâmetros específicos de cada tipo de registo. Os parâmetros para cada tipo de registo são os mesmos que para o `New-AzDnsRecordConfig` cmdlet, como mostrado em exemplos adicionais de tipo de registo acima.

Os conjuntos recorde do tipo 'CNAME' ou 'SOA' não podem conter mais do que um registo. Esta restrição decorre das normas DNS. Não é uma limitação do Azure DNS.

## <a name="remove-a-record-from-an-existing-record-set"></a>Remova um recorde de um conjunto de recordes existente

O processo para remover um recorde de um conjunto de recordes é semelhante ao processo para adicionar um recorde a um conjunto de recordes existente:

1. Obtenha o recorde existente

    ```powershell
    $rs = Get-AzDnsRecordSet -Name www –ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -RecordType A
    ```

2. Retire o registo do objeto local. Esta é uma operação fora de linha. O registo que está a ser removido deve ser exato com um registo existente em todos os parâmetros.

    ```powershell
    Remove-AzDnsRecordConfig -RecordSet $rs -Ipv4Address "5.6.7.8"
    ```

3. Volte a comprometer a alteração no serviço Azure DNS. Utilize o interruptor opcional `-Overwrite` para suprimir as [verificações Etag](dns-zones-records.md#etags) para alterações simultâneas.

    ```powershell
    Set-AzDnsRecordSet -RecordSet $Rs
    ```

A utilização da sequência acima para remover o último registo de um conjunto de registos não apaga o recorde definido, mas deixa um recorde vazio. Para remover totalmente um recorde, consulte [Eliminar um recorde estabelecido](#delete-a-record-set).

Da mesma forma, a adição de registos a um recorde, a sequência de operações para remover um conjunto de recordes também pode ser canalizada:

```powershell
Get-AzDnsRecordSet -Name www –ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -RecordType A | Remove-AzDnsRecordConfig -Ipv4Address "5.6.7.8" | Set-AzDnsRecordSet
```

Diferentes tipos de registo são suportados passando os parâmetros específicos do tipo adequado para `Remove-AzDnsRecordSet` . Os parâmetros para cada tipo de registo são os mesmos que para o `New-AzDnsRecordConfig` cmdlet, como mostrado em exemplos adicionais de tipo de registo acima.


## <a name="modify-an-existing-record-set"></a>Modificar um conjunto de recordes existente

Os passos para modificar um conjunto de registos existentes são semelhantes aos passos que toma ao adicionar ou remover registos de um conjunto de recordes:

1. Recupere o recorde existente estabelecido utilizando `Get-AzDnsRecordSet` .
2. Modifique o objeto estabelecido de recorde local por:
    * Adicionar ou remover registos
    * Alteração dos parâmetros dos registos existentes
    * Alterar o recorde definir metadados e tempo para viver (TTL)
3. Cometa as suas alterações utilizando o `Set-AzDnsRecordSet` cmdlet. Isto *substitui* o recorde existente estabelecido no Azure DNS pelo conjunto de registos especificado.

Ao `Set-AzDnsRecordSet` utilizar, são [utilizados controlos Etag](dns-zones-records.md#etags) para garantir que as alterações simultâneas não sejam substituídas. Pode utilizar o interruptor opcional `-Overwrite` para suprimir estas verificações.

### <a name="to-update-a-record-in-an-existing-record-set"></a>Para atualizar um recorde num conjunto de recordes existente

Neste exemplo, alteramos o endereço IP de um registo 'A' existente:

```powershell
$rs = Get-AzDnsRecordSet -name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
$rs.Records[0].Ipv4Address = "9.8.7.6"
Set-AzDnsRecordSet -RecordSet $rs
```

### <a name="to-modify-an-soa-record"></a>Para modificar um registo SOA

Não é possível adicionar ou remover registos do registo SOA automaticamente criado no ápice da zona `-Name "@"` (, incluindo as marcas de cotação). No entanto, pode modificar qualquer um dos parâmetros dentro do registo SOA (exceto "Host") e o recorde definido TTL.

O exemplo a seguir mostra como alterar a propriedade *de Email* do registo SOA:

```powershell
$rs = Get-AzDnsRecordSet -Name "@" -RecordType SOA -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
$rs.Records[0].Email = "admin.contoso.com"
Set-AzDnsRecordSet -RecordSet $rs
```

### <a name="to-modify-ns-records-at-the-zone-apex"></a>Para modificar os registos NS no ápice da zona

O recorde NS estabelecido no ápice da zona é automaticamente criado com cada zona DE DNS. Contém os nomes dos servidores de nomeS DNS Azure atribuídos à zona.

Pode adicionar servidores de nome adicionais a este conjunto de registos NS, para suportar domínios de co-hospedagem com mais de um fornecedor de DNS. Também pode modificar o TTL e metadados para este conjunto de registos. No entanto, não é possível remover ou modificar os servidores de nomeS DNS pré-povoados.

Note que isto se aplica apenas ao recorde NS estabelecido no ápice da zona. Outros conjuntos de registos NS na sua zona (como usado para delegar zonas infantis) podem ser modificados sem restrições.

O exemplo a seguir mostra como adicionar um servidor de nome adicional ao registo NS estabelecido no ápice da zona:

```powershell
$rs = Get-AzDnsRecordSet -Name "@" -RecordType NS -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
Add-AzDnsRecordConfig -RecordSet $rs -Nsdname ns1.myotherdnsprovider.com
Set-AzDnsRecordSet -RecordSet $rs
```

### <a name="to-modify-record-set-metadata"></a>Para modificar metadados recorde

[Os metadados de conjunto de registos](dns-zones-records.md#tags-and-metadata) podem ser usados para associar dados específicos da aplicação a cada conjunto de registos, como pares de valor-chave.

O exemplo a seguir mostra como modificar os metadados de um conjunto de registos existente:

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


## <a name="delete-a-record-set"></a>Apagar um conjunto de recordes

Os conjuntos de registos podem ser eliminados utilizando o `Remove-AzDnsRecordSet` cmdlet. A eliminação de um recorde também elimina todos os recordes dentro do recorde estabelecido.

> [!NOTE]
> Não é possível eliminar os conjuntos de registos SOA e NS no ápice da zona `-Name '@'` ().  O Azure DNS criou-os automaticamente quando a zona foi criada e elimina-as automaticamente quando a zona é eliminada.

O exemplo a seguir mostra como apagar um conjunto de registos. Neste exemplo, o nome de conjunto de registos, o tipo de conjunto de registos, o nome da zona e o grupo de recursos são especificados explicitamente.

```powershell
Remove-AzDnsRecordSet -Name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
```

Alternativamente, o conjunto de registos pode ser especificado pelo nome e pelo tipo, e a zona especificada com um objeto:

```powershell
$zone = Get-AzDnsZone -Name "contoso.com" -ResourceGroupName "MyResourceGroup"
Remove-AzDnsRecordSet -Name "www" -RecordType A -Zone $zone
```

Como terceira opção, o conjunto de recordes em si pode ser especificado usando um objeto recorde definido:

```powershell
$rs = Get-AzDnsRecordSet -Name www -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
Remove-AzDnsRecordSet -RecordSet $rs
```

Quando especificar o recorde definido para ser eliminado utilizando um objeto de recorde, são [utilizados controlos Etag](dns-zones-records.md#etags) para garantir que as alterações simultâneas não sejam eliminadas. Pode utilizar o interruptor opcional `-Overwrite` para suprimir estas verificações.

O objeto de recorde também pode ser canalizado em vez de ser passado como um parâmetro:

```powershell
Get-AzDnsRecordSet -Name www -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" | Remove-AzDnsRecordSet
```

## <a name="confirmation-prompts"></a>Pedidos de confirmação

Todos os cmdlets `New-AzDnsRecordSet`, `Set-AzDnsRecordSet` e `Remove-AzDnsRecordSet` suportam pedidos de confirmação.

Cada cmdlet solicita a confirmação se a `$ConfirmPreference` variável de preferência PowerShell tem um valor `Medium` ou inferior. Uma vez que o valor predefinido `$ConfirmPreference` `High` é, estas indicações não são dadas quando utilizam as definições padrão do PowerShell.

Pode substituir a definição `$ConfirmPreference` atual com o parâmetro `-Confirm`. Se especificar `-Confirm` ou `-Confirm:$True` , o cmdlet irá pedir-lhe a confirmação antes de ser executado. Se especificar `-Confirm:$False`, o cmdlet não solicita a confirmação. 

Para obter mais informações sobre `-Confirm` e `$ConfirmPreference`, veja [Sobre as Variáveis de Preferência](/powershell/module/microsoft.powershell.core/about/about_preference_variables).

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [zonas e registos em Azure DNS](dns-zones-records.md).
<br>
Aprenda a [proteger as suas zonas e registos](dns-protect-zones-recordsets.md) ao utilizar o Azure DNS.
<br>
Reveja a documentação de referência do [Azure DNS PowerShell](/powershell/module/az.dns).