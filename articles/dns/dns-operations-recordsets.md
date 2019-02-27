---
title: Gerir registos DNS no DNS do Azure com o Azure PowerShell | Documentos da Microsoft
description: Gerir conjuntos de registos DNS e registos no DNS do Azure ao alojar o seu domínio no DNS do Azure. Todos os comandos do PowerShell para operações em conjuntos de registos e registos.
services: dns
documentationcenter: na
author: vhorne
manager: timlt
ms.assetid: 7136a373-0682-471c-9c28-9e00d2add9c2
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.custom: H1Hack27Feb2017
ms.workload: infrastructure-services
ms.date: 12/21/2016
ms.author: victorh
ms.openlocfilehash: fa1f90cf0236a589d1df96658c672a555195be6b
ms.sourcegitcommit: 24906eb0a6621dfa470cb052a800c4d4fae02787
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2019
ms.locfileid: "56888819"
---
# <a name="manage-dns-records-and-recordsets-in-azure-dns-using-azure-powershell"></a>Gerir registos DNS e conjuntos de registros no DNS do Azure com o Azure PowerShell

> [!div class="op_single_selector"]
> * [Portal do Azure](dns-operations-recordsets-portal.md)
> * [CLI clássica do Azure](dns-operations-recordsets-cli-nodejs.md)
> * [CLI do Azure](dns-operations-recordsets-cli.md)
> * [PowerShell](dns-operations-recordsets.md)

Este artigo mostra-lhe como gerir registos DNS para a sua zona DNS com o Azure PowerShell. Registos DNS também podem ser geridos utilizando a Multiplataforma [CLI do Azure](dns-operations-recordsets-cli.md) ou o [portal do Azure](dns-operations-recordsets-portal.md).

Os exemplos neste artigo partem do princípio de que já [instalado Azure PowerShell, iniciou sessão e criou uma zona DNS](dns-operations-dnszones.md).

## <a name="introduction"></a>Introdução

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Antes de criar registos DNS no DNS do Azure, terá primeiro de compreender como o DNS do Azure organiza os registos DNS em conjuntos de registos DNS.

[!INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]

Para obter mais informações sobre os registos DNS no DNS do Azure, veja [Zonas e registos DNS](dns-zones-records.md).


## <a name="create-a-new-dns-record"></a>Criar um novo registo DNS

Se o seu novo registo tiver o mesmo nome e tipo como um registo existente, terá [adicioná-lo para o conjunto de registos existente](#add-a-record-to-an-existing-record-set). Se o seu novo registo tiver um nome e tipo diferentes para todos os registos existentes, terá de criar um novo conjunto de registos. 

### <a name="create-a-records-in-a-new-record-set"></a>Criar registos de "A" num novo conjunto de registos

Pode criar conjuntos de registos com o cmdlet `New-AzDnsRecordSet`. Ao criar um conjunto de registos, tem de especificar o conjunto de registos nome, a zona, a hora para live (TTL), o tipo de registo e os registos a ser criada.

Os parâmetros para adicionar registos a um conjunto de registos variam consoante o tipo do conjunto de registos. Por exemplo, quando utilizar um conjunto de registos do tipo "A", tem de especificar o endereço IP utilizando o parâmetro `-IPv4Address`. Outros parâmetros são utilizados para outros tipos de registos. Veja exemplos de tipo de registo adicionais para obter detalhes.

O exemplo seguinte cria um conjunto de registos com o nome relativo "www" na zona DNS "contoso.com". O nome completamente qualificado do conjunto de registos é "www.contoso.com". O tipo de registo é "A", e o TTL é de 3600 segundos. O conjunto de registos contém um único registo, com o endereço IP "1.2.3.4".

```powershell
New-AzDnsRecordSet -Name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -IPv4Address "1.2.3.4") 
```

Para criar um conjunto de registos no 'vértice"de uma zona (neste caso,"contoso.com"), utilize o nome do conjunto de registos"\@' (excluindo as aspas):

```powershell
New-AzDnsRecordSet -Name "@" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -IPv4Address "1.2.3.4") 
```

Se precisar de criar um conjunto que contém mais de um registo de registos, primeiro crie uma matriz local e adicionar os registros, em seguida, transmita a matriz ao `New-AzDnsRecordSet` da seguinte forma:

```powershell
$aRecords = @()
$aRecords += New-AzDnsRecordConfig -IPv4Address "1.2.3.4"
$aRecords += New-AzDnsRecordConfig -IPv4Address "2.3.4.5"
New-AzDnsRecordSet -Name www –ZoneName "contoso.com" -ResourceGroupName MyResourceGroup -Ttl 3600 -RecordType A -DnsRecords $aRecords
```

[Metadados do conjunto de registos](dns-zones-records.md#tags-and-metadata) pode ser utilizado para associar dados específicos a cada conjunto de registos, como pares chave-valor. O exemplo seguinte mostra como criar um conjunto de registos com duas entradas de metadados, ' Departamento = finance' e ' Ambiente = produção ".

```powershell
New-AzDnsRecordSet -Name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -IPv4Address "1.2.3.4") -Metadata @{ dept="finance"; environment="production" } 
```

O DNS do Azure também suporta conjuntos de registos 'empty', que podem atuar como um marcador de posição para reservar um nome DNS antes de criar registos DNS. Conjuntos de registos vazios são visíveis no painel de controlo de DNS do Azure, mas são apresentadas nos servidores de nome DNS do Azure. O exemplo seguinte cria um conjunto de registos vazio:

```powershell
New-AzDnsRecordSet -Name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords @()
```

## <a name="create-records-of-other-types"></a>Criar registos de outros tipos

Depois de ter visto em detalhe como criar registos de "A", os exemplos seguintes mostram como criar registos de outros tipos de registos suportados pelo DNS do Azure.

Em cada caso, vamos mostrar como criar um conjunto que contém um único registo de registos. Os exemplos anteriores para "A" registos podem ser adaptados para criar conjuntos de registos de outros tipos que contém vários registos, com metadados, ou para criar conjuntos de registos vazios.

Não podemos dar um exemplo para criar um conjunto de registos SOA, uma vez que SOAs são criados e eliminado com cada zona DNS e não pode ser criada ou eliminada separadamente. No entanto, [pode ser modificado a SOA, como mostra um exemplo posterior](#to-modify-an-SOA-record).

### <a name="create-an-aaaa-record-set-with-a-single-record"></a>Criar um conjunto de registos AAAA com um único registo

```powershell
New-AzDnsRecordSet -Name "test-aaaa" -RecordType AAAA -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Ipv6Address "2607:f8b0:4009:1803::1005") 
```

### <a name="create-a-caa-record-set-with-a-single-record"></a>Criar um registo de CAA definido com um único registo

```powershell
New-AzDnsRecordSet -Name "test-caa" -RecordType CAA -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Caaflags 0 -CaaTag "issue" -CaaValue "ca1.contoso.com") 
```

### <a name="create-a-cname-record-set-with-a-single-record"></a>Criar um conjunto de registos CNAME com um único registo

> [!NOTE]
> As normas DNS não permitem registos CNAME no vértice da zona de um (`-Name '@'`), nem que permitem a conjuntos de registos que contêm mais de um registo.
> 
> Para obter mais informações, consulte [registos CNAME](dns-zones-records.md#cname-records).


```powershell
New-AzDnsRecordSet -Name "test-cname" -RecordType CNAME -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Cname "www.contoso.com") 
```

### <a name="create-an-mx-record-set-with-a-single-record"></a>Criar um conjunto de registos MX com um único registo

Neste exemplo, utilizamos o nome de conjunto de registos "\@" para criar um registro MX no vértice da zona (neste caso, "contoso.com").


```powershell
New-AzDnsRecordSet -Name "@" -RecordType MX -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Exchange "mail.contoso.com" -Preference 5) 
```

### <a name="create-an-ns-record-set-with-a-single-record"></a>Criar um conjunto de registos NS com um único registo

```powershell
New-AzDnsRecordSet -Name "test-ns" -RecordType NS -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Nsdname "ns1.contoso.com") 
```

### <a name="create-a-ptr-record-set-with-a-single-record"></a>Criar um conjunto de registos PTR com um único registo

Neste caso, "meu-arpa-My-arpa-Zone.com" representa a zona de pesquisa inversa ARPA, que representa o seu intervalo de IP. Cada conjunto de registos PTR nesta zona corresponde a um endereço IP neste intervalo de IPs. O nome do registo "10" é o último octeto do endereço IP neste intervalo de IP representado por este registo.

```powershell
New-AzDnsRecordSet -Name 10 -RecordType PTR -ZoneName "my-arpa-zone.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Ptrdname "myservice.contoso.com") 
```

### <a name="create-an-srv-record-set-with-a-single-record"></a>Criar um conjunto de registos SRV com um único registo

Ao criar uma [conjunto de registos SRV](dns-zones-records.md#srv-records), especifique a  *\_serviço* e  *\_protocolo* no nome do conjunto de registos. Não é necessário incluir "\@" no nome do conjunto de registos quando criar um registo SRV definido no vértice da zona.

```powershell
New-AzDnsRecordSet -Name "_sip._tls" -RecordType SRV -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Priority 0 -Weight 5 -Port 8080 -Target "sip.contoso.com") 
```


### <a name="create-a-txt-record-set-with-a-single-record"></a>Criar um registo TXT definido com um único registo

O exemplo seguinte mostra como criar um registo TXT. Para obter mais informações sobre o comprimento da cadeia máximo suportado em registos TXT, consulte [registos TXT](dns-zones-records.md#txt-records).

```powershell
New-AzDnsRecordSet -Name "test-txt" -RecordType TXT -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Value "This is a TXT record") 
```


## <a name="get-a-record-set"></a>Obter um conjunto de registos

Para obter um conjunto de registos existente, utilize `Get-AzDnsRecordSet`. Este cmdlet devolve um objeto local que representa o conjunto de registos no DNS do Azure.

Tal como acontece com `New-AzDnsRecordSet`, o nome do conjunto de registos fornecido tem de ser um *relativo* nome, que significa que tem de excluir o nome da zona. Também tem de especificar o tipo de registo e definir a zona que contém o registo.

O exemplo seguinte mostra como recuperar um conjunto de registos. Neste exemplo, a zona é especificada com o `-ZoneName` e `-ResourceGroupName` parâmetros.

```powershell
$rs = Get-AzDnsRecordSet -Name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
```

Em alternativa, também pode especificar a zona com um objeto de zona, passado com o `-Zone` parâmetro.

```powershell
$zone = Get-AzDnsZone -Name "contoso.com" -ResourceGroupName "MyResourceGroup"
$rs = Get-AzDnsRecordSet -Name "www" -RecordType A -Zone $zone
```

## <a name="list-record-sets"></a>Lista os conjuntos de registos

Também pode utilizar `Get-AzDnsZone` para conjuntos de registos de lista numa zona, omitindo o `-Name` e/ou `-RecordType` parâmetros.

O exemplo seguinte devolve o registo de todos os conjuntos na zona:

```powershell
$recordsets = Get-AzDnsRecordSet -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
```

O exemplo seguinte mostra como todos os conjuntos de um determinado tipo de registos pode ser obtido ao especificar o tipo de registo enquanto omitir o registo de nome do conjunto:

```powershell
$recordsets = Get-AzDnsRecordSet -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
```

Para obter todos os conjuntos de registos com um determinado nome, entre tipos de registo, terá de obter todos os conjuntos de registos e, em seguida, filtrar os resultados:

```powershell
$recordsets = Get-AzDnsRecordSet -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" | where {$_.Name.Equals("www")}
```

Nos exemplos acima, a zona pode ser especificada através da utilização a `-ZoneName` e `-ResourceGroupName`parâmetros (conforme mostrado), ou especificando um objeto de zona:

```powershell
$zone = Get-AzDnsZone -Name "contoso.com" -ResourceGroupName "MyResourceGroup"
$recordsets = Get-AzDnsRecordSet -Zone $zone
```

## <a name="add-a-record-to-an-existing-record-set"></a>Adicionar um registro a um conjunto de registos existente

Para adicionar um registo a um conjunto de registos existente, siga os seguintes três passos:

1. Obter o conjunto de registos existente

    ```powershell
    $rs = Get-AzDnsRecordSet -Name www –ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -RecordType A
    ```

2. Adicione o novo Registro ao conjunto de registos local. Esta é uma operação offline.

    ```powershell
    Add-AzDnsRecordConfig -RecordSet $rs -Ipv4Address "5.6.7.8"
    ```

3. Confirme a alteração novamente para o serviço de DNS do Azure. 

    ```powershell
    Set-AzDnsRecordSet -RecordSet $rs
    ```

Usando `Set-AzDnsRecordSet` *substitui* existente conjunto de registos no DNS do Azure (e todos os registos nele contidos) com o conjunto de registos especificado. [Verificações de ETag](dns-zones-records.md#etags) são utilizados para garantir que as alterações em simultâneo não são substituídas. Pode usar o opcional `-Overwrite` comutador para suprimir estas verificações.

Esta sequência de operações também pode ser *enviada por pipe*, que significa que passar o objeto de conjunto de registos ao usando o pipe, em vez de usá-la como um parâmetro:

```powershell
Get-AzDnsRecordSet -Name "www" –ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -RecordType A | Add-AzDnsRecordConfig -Ipv4Address "5.6.7.8" | Set-AzDnsRecordSet
```

Os exemplos acima mostram como adicionar um registo "A" a um conjunto de registos existente do tipo "A". Uma sequência de operações de semelhante é usada para adicionar registros a conjuntos de registos de outros tipos, substituindo os `-Ipv4Address` parâmetro do `Add-AzDnsRecordConfig` com outros parâmetros específicos para cada tipo de registo. Os parâmetros para cada tipo de registo são os mesmos que para o `New-AzDnsRecordConfig` cmdlet, conforme ilustrado nos exemplos de tipo de registo adicionais acima.

Conjuntos de registos do tipo 'CNAME' ou 'SOA' não podem conter mais de um registo. Esta restrição surge das normas DNS. Não é uma limitação do DNS do Azure.

## <a name="remove-a-record-from-an-existing-record-set"></a>Remover um registo de um conjunto de registos existente

O processo para remover um registo de um conjunto de registos é semelhante ao processo para adicionar um registo para um conjunto de registos existente:

1. Obter o conjunto de registos existente

    ```powershell
    $rs = Get-AzDnsRecordSet -Name www –ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -RecordType A
    ```

2. Remova o registo do objeto de conjunto de registos locais. Esta é uma operação offline. O registo que está a ser removido tem de ser uma correspondência exata com um registo existente em todos os parâmetros.

    ```powershell
    Remove-AzDnsRecordConfig -RecordSet $rs -Ipv4Address "5.6.7.8"
    ```

3. Confirme a alteração novamente para o serviço de DNS do Azure. Utilizar o opcional `-Overwrite` comutador para suprimir [Etag verifica](dns-zones-records.md#etags) para alterações em simultâneo.

    ```powershell
    Set-AzDnsRecordSet -RecordSet $Rs
    ```

Utilizar a sequência de acima para remover o último registo de um conjunto de registos não elimina o conjunto de registos, em vez disso, ele deixa um conjunto de registos vazio. Para remover um conjunto inteiramente de registos, consulte [eliminar um conjunto de registos](#delete-a-record-set).

Da mesma forma para adicionar registos a um conjunto de registos, a seqüência de operações para remover um conjunto de registos também pode ser direcionada:

```powershell
Get-AzDnsRecordSet -Name www –ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -RecordType A | Remove-AzDnsRecordConfig -Ipv4Address "5.6.7.8" | Set-AzDnsRecordSet
```

Diferentes tipos de registos são suportados ao transmitir os parâmetros específicos do tipo adequados para `Remove-AzDnsRecordSet`. Os parâmetros para cada tipo de registo são os mesmos que para o `New-AzDnsRecordConfig` cmdlet, conforme ilustrado nos exemplos de tipo de registo adicionais acima.


## <a name="modify-an-existing-record-set"></a>Modificar um conjunto de registos existente

Os passos para modificar um conjunto de registos existente são semelhantes para os passos a que efetuar quando adicionar ou remover registos de um conjunto de registos:

1. Obter o conjunto através da utilização de registos existente `Get-AzDnsRecordSet`.
2. Modificar o objeto de conjunto de registos locais por:
    * Adição ou remoção de registos
    * Alterar os parâmetros de registos existentes
    * Alterar o registo definir metadados e a hora para live (TTL)
3. Consolide as suas alterações com o `Set-AzDnsRecordSet` cmdlet. Isso *substitui* existente conjunto de registos no DNS do Azure com o conjunto de registos especificado.

Ao usar `Set-AzDnsRecordSet`, [Etag verifica](dns-zones-records.md#etags) são utilizados para garantir que as alterações em simultâneo não são substituídas. Pode usar o opcional `-Overwrite` comutador para suprimir estas verificações.

### <a name="to-update-a-record-in-an-existing-record-set"></a>Para atualizar um registo num conjunto de registos existente

Neste exemplo, podemos alterar o endereço IP do registo "A" existente:

```powershell
$rs = Get-AzDnsRecordSet -name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
$rs.Records[0].Ipv4Address = "9.8.7.6"
Set-AzDnsRecordSet -RecordSet $rs
```

### <a name="to-modify-an-soa-record"></a>Para modificar um registo SOA

Não é possível adicionar ou remover registos do criado automaticamente SOA conjunto de registos no vértice da zona (`-Name "@"`, incluindo o entre aspas duplas). No entanto, pode modificar qualquer um dos parâmetros no registo SOA (exceto "Host") e o registo de definir o valor de TTL.

O exemplo seguinte mostra como alterar o *E-Mail* propriedade do registo SOA:

```powershell
$rs = Get-AzDnsRecordSet -Name "@" -RecordType SOA -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
$rs.Records[0].Email = "admin.contoso.com"
Set-AzDnsRecordSet -RecordSet $rs
```

### <a name="to-modify-ns-records-at-the-zone-apex"></a>Para modificar registos NS no vértice da zona

O conjunto no vértice da zona de registos NS é criado automaticamente com cada zona DNS. Contém os nomes dos servidores de nome DNS do Azure atribuídos à zona.

Pode adicionar servidores para este registo NS definidos, para oferecer suporte a domínios co-hospedagem com mais de um fornecedor DNS de nome adicionais. Também pode modificar o valor de TTL e os metadados para este conjunto de registos. No entanto, não é possível remover ou modificar os servidores de nomes DNS do Azure preenchidos previamente.

Tenha em atenção que isto aplica-se apenas para o conjunto no vértice da zona de registos NS. Outros conjuntos de registos NS na sua zona (como utilizado para delegar zonas subordinadas) podem ser modificados sem restrição.

O exemplo seguinte mostra como adicionar um servidor de nome adicionais para o conjunto no vértice da zona de registos NS:

```powershell
$rs = Get-AzDnsRecordSet -Name "@" -RecordType NS -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
Add-AzDnsRecordConfig -RecordSet $rs -Nsdname ns1.myotherdnsprovider.com
Set-AzDnsRecordSet -RecordSet $rs
```

### <a name="to-modify-record-set-metadata"></a>Para modificar os metadados do conjunto de registos

[Metadados do conjunto de registos](dns-zones-records.md#tags-and-metadata) pode ser utilizado para associar dados específicos a cada conjunto de registos, como pares chave-valor.

O exemplo seguinte mostra como modificar os metadados de um conjunto de registos existente:

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


## <a name="delete-a-record-set"></a>Eliminar um conjunto de registos

Conjuntos de registos podem ser eliminados ao utilizar o `Remove-AzDnsRecordSet` cmdlet. Eliminar um conjunto de registos também elimina a todos os registos nesse conjunto de registos.

> [!NOTE]
> Não é possível eliminar a SOA e conjuntos de registos NS no vértice da zona (`-Name '@'`).  O DNS do Azure criada-estes automaticamente quando a zona foi criada e elimina automaticamente quando a zona for eliminada.

O exemplo seguinte mostra como eliminar um conjunto de registos. Neste exemplo, o nome do conjunto de registos, o tipo de conjunto de registos, o nome da zona e o grupo de recursos cada especificados explicitamente.

```powershell
Remove-AzDnsRecordSet -Name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
```

Em alternativa, o conjunto de registos pode ser especificado pelo nome e tipo e a zona especificada, utilizando um objeto:

```powershell
$zone = Get-AzDnsZone -Name "contoso.com" -ResourceGroupName "MyResourceGroup"
Remove-AzDnsRecordSet -Name "www" -RecordType A -Zone $zone
```

Como terceira opção, o conjunto próprio de registos podem ser especificado um objeto de conjunto de registos:

```powershell
$rs = Get-AzDnsRecordSet -Name www -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
Remove-AzDnsRecordSet -RecordSet $rs
```

Quando especificar o conjunto sejam eliminados utilizando um objeto de conjunto de registos, de registos [Etag verifica](dns-zones-records.md#etags) são utilizados para garantir que as alterações em simultâneo não são eliminadas. Pode usar o opcional `-Overwrite` comutador para suprimir estas verificações.

O objeto de conjunto de registos também pode ser direcionado, em vez de ser transmitido como um parâmetro:

```powershell
Get-AzDnsRecordSet -Name www -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" | Remove-AzDnsRecordSet
```

## <a name="confirmation-prompts"></a>Pedidos de confirmação

Todos os cmdlets `New-AzDnsRecordSet`, `Set-AzDnsRecordSet` e `Remove-AzDnsRecordSet` suportam pedidos de confirmação.

Cada cmdlet solicita a confirmação se o `$ConfirmPreference` variável de preferência de PowerShell tem um valor de `Medium` ou inferior. Desde o valor predefinido para `$ConfirmPreference` é `High`, esses prompts não são fornecidos ao utilizar as predefinições do PowerShell.

Pode substituir a definição `$ConfirmPreference` atual com o parâmetro `-Confirm`. Se especificar `-Confirm` ou `-Confirm:$True` , o cmdlet irá pedir-lhe a confirmação antes de ser executado. Se especificar `-Confirm:$False`, o cmdlet não solicita a confirmação. 

Para obter mais informações sobre `-Confirm` e `$ConfirmPreference`, veja [Sobre as Variáveis de Preferência](https://msdn.microsoft.com/powershell/reference/5.1/Microsoft.PowerShell.Core/about/about_Preference_Variables).

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre [zonas e registos no DNS do Azure](dns-zones-records.md).
<br>
Saiba como [proteger suas zonas e registos](dns-protect-zones-recordsets.md) ao utilizar o DNS do Azure.
<br>
Reveja os [documentação de referência do PowerShell de DNS do Azure](/powershell/module/azurerm.dns).
