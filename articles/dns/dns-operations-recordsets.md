---
title: Gerenciar registros DNS no DNS do Azure usando o Azure PowerShell | Microsoft Docs
description: Gerenciando registros e conjuntos de registros DNS no DNS do Azure ao hospedar seu domínio no DNS do Azure. Todos os comandos do PowerShell para operações em conjuntos e registros de registros.
services: dns
documentationcenter: na
author: asudbring
manager: timlt
ms.assetid: 7136a373-0682-471c-9c28-9e00d2add9c2
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.custom: H1Hack27Feb2017
ms.workload: infrastructure-services
ms.date: 12/21/2016
ms.author: allensu
ms.openlocfilehash: c11a5c4a3cfe18fbc203ad641ab1de866915bcc4
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74211678"
---
# <a name="manage-dns-records-and-recordsets-in-azure-dns-using-azure-powershell"></a>Gerenciar registros DNS e conjuntos de registros no DNS do Azure usando o Azure PowerShell

> [!div class="op_single_selector"]
> * [Portal do Azure](dns-operations-recordsets-portal.md)
> * [CLI clássica do Azure](dns-operations-recordsets-cli-nodejs.md)
> * [CLI do Azure](dns-operations-recordsets-cli.md)
> * [PowerShell](dns-operations-recordsets.md)

Este artigo mostra como gerenciar registros DNS para sua zona DNS usando Azure PowerShell. Os registros DNS também podem ser gerenciados usando o [CLI do Azure](dns-operations-recordsets-cli.md) de plataforma cruzada ou o [portal do Azure](dns-operations-recordsets-portal.md).

Os exemplos neste artigo pressupõem que você já tenha [instalado Azure PowerShell, conectado e criado uma zona DNS](dns-operations-dnszones.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="introduction"></a>Introdução

Antes de criar registos DNS no DNS do Azure, terá primeiro de compreender como o DNS do Azure organiza os registos DNS em conjuntos de registos DNS.

[!INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]

Para obter mais informações sobre os registos DNS no DNS do Azure, veja [Zonas e registos DNS](dns-zones-records.md).


## <a name="create-a-new-dns-record"></a>Criar um novo registro DNS

Se o novo registro tiver o mesmo nome e tipo de um registro existente, você precisará [adicioná-lo ao conjunto de registros existente](#add-a-record-to-an-existing-record-set). Se o novo registro tiver um nome e tipo diferentes para todos os registros existentes, você precisará criar um novo conjunto de registros. 

### <a name="create-a-records-in-a-new-record-set"></a>Criar registros ' A ' em um novo conjunto de registros

Pode criar conjuntos de registos com o cmdlet `New-AzDnsRecordSet`. Ao criar um conjunto de registros, você precisa especificar o nome do conjunto de registros, a zona, a TTL (vida útil), o tipo de registro e os registros a serem criados.

Os parâmetros para adicionar registos a um conjunto de registos variam consoante o tipo do conjunto de registos. Por exemplo, ao usar um conjunto de registros do tipo ' A ', você precisa especificar o endereço IP usando o parâmetro `-IPv4Address`. Outros parâmetros são usados para outros tipos de registro. Consulte exemplos adicionais de tipo de registro para obter detalhes.

O exemplo a seguir cria um conjunto de registros com o nome relativo ' www ' na zona DNS ' contoso.com '. O nome totalmente qualificado do conjunto de registros é ' www.contoso.com '. O tipo de registro é ' A ' e o TTL é de 3600 segundos. O conjunto de registros contém um único registro, com o endereço IP ' 1.2.3.4 '.

```powershell
New-AzDnsRecordSet -Name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -IPv4Address "1.2.3.4") 
```

Para criar um conjunto de registros no ' Apex ' de uma zona (nesse caso, ' contoso.com '), use o nome do conjunto de registros '\@' (excluindo as aspas):

```powershell
New-AzDnsRecordSet -Name "@" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -IPv4Address "1.2.3.4") 
```

Se você precisar criar um conjunto de registros contendo mais de um registro, primeiro crie uma matriz local e adicione os registros e, em seguida, passe a matriz para `New-AzDnsRecordSet` da seguinte maneira:

```powershell
$aRecords = @()
$aRecords += New-AzDnsRecordConfig -IPv4Address "1.2.3.4"
$aRecords += New-AzDnsRecordConfig -IPv4Address "2.3.4.5"
New-AzDnsRecordSet -Name www –ZoneName "contoso.com" -ResourceGroupName MyResourceGroup -Ttl 3600 -RecordType A -DnsRecords $aRecords
```

Os [metadados do conjunto de registros](dns-zones-records.md#tags-and-metadata) podem ser usados para associar dados específicos do aplicativo a cada conjunto de registros, como pares chave-valor. O exemplo a seguir mostra como criar um conjunto de registros com duas entradas de metadados, ' Dept = Finance ' e ' Environment = Production '.

```powershell
New-AzDnsRecordSet -Name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -IPv4Address "1.2.3.4") -Metadata @{ dept="finance"; environment="production" } 
```

O DNS do Azure também dá suporte a conjuntos de registros "vazios", que podem agir como um espaço reservado para reservar um nome DNS antes de criar registros DNS. Os conjuntos de registros vazios são visíveis no plano de controle de DNS do Azure, mas aparecem nos servidores de nome DNS do Azure. O exemplo a seguir cria um conjunto de registros vazio:

```powershell
New-AzDnsRecordSet -Name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords @()
```

## <a name="create-records-of-other-types"></a>Criar registros de outros tipos

Tendo visto em detalhes como criar registros ' A ', os exemplos a seguir mostram como criar registros de outros tipos de registro com suporte do DNS do Azure.

Em cada caso, mostramos como criar um conjunto de registros contendo um único registro. Os exemplos anteriores para os registros ' A ' podem ser adaptados para criar conjuntos de registros de outros tipos contendo vários registros, com metadados ou para criar conjuntos de registros vazios.

Não damos um exemplo para criar um conjunto de registros SOA, já que SOAs são criados e excluídos com cada zona DNS e não podem ser criados ou excluídos separadamente. No entanto, [a soa pode ser modificada, conforme mostrado em um exemplo posterior](#to-modify-an-soa-record).

### <a name="create-an-aaaa-record-set-with-a-single-record"></a>Criar um conjunto de registos AAAA com um único registo

```powershell
New-AzDnsRecordSet -Name "test-aaaa" -RecordType AAAA -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Ipv6Address "2607:f8b0:4009:1803::1005") 
```

### <a name="create-a-caa-record-set-with-a-single-record"></a>Criar um conjunto de registros CAA com um único registro

```powershell
New-AzDnsRecordSet -Name "test-caa" -RecordType CAA -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Caaflags 0 -CaaTag "issue" -CaaValue "ca1.contoso.com") 
```

### <a name="create-a-cname-record-set-with-a-single-record"></a>Criar um conjunto de registos CNAME com um único registo

> [!NOTE]
> Os padrões de DNS não permitem registros CNAME no Apex de uma zona (`-Name '@'`), nem permitem conjuntos de registros que contenham mais de um registro.
> 
> Para obter mais informações, consulte [CNAME Records](dns-zones-records.md#cname-records).


```powershell
New-AzDnsRecordSet -Name "test-cname" -RecordType CNAME -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Cname "www.contoso.com") 
```

### <a name="create-an-mx-record-set-with-a-single-record"></a>Criar um conjunto de registos MX com um único registo

Neste exemplo, usamos o nome do conjunto de registros '\@' para criar um registro MX no Apex da zona (neste caso, ' contoso.com ').


```powershell
New-AzDnsRecordSet -Name "@" -RecordType MX -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Exchange "mail.contoso.com" -Preference 5) 
```

### <a name="create-an-ns-record-set-with-a-single-record"></a>Criar um conjunto de registos NS com um único registo

```powershell
New-AzDnsRecordSet -Name "test-ns" -RecordType NS -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Nsdname "ns1.contoso.com") 
```

### <a name="create-a-ptr-record-set-with-a-single-record"></a>Criar um conjunto de registos PTR com um único registo

Nesse caso, ' my-arpa-zone.com ' representa a zona de pesquisa inversa de ARPA que representa o intervalo de IP. Cada conjunto de registos PTR nesta zona corresponde a um endereço IP neste intervalo de IPs. O nome do registro ' 10 ' é o último octeto do endereço IP nesse intervalo de IP representado por esse registro.

```powershell
New-AzDnsRecordSet -Name 10 -RecordType PTR -ZoneName "my-arpa-zone.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Ptrdname "myservice.contoso.com") 
```

### <a name="create-an-srv-record-set-with-a-single-record"></a>Criar um conjunto de registos SRV com um único registo

Ao criar um [conjunto de registros SRV](dns-zones-records.md#srv-records), especifique o *serviço de\_* e o *protocolo\_* no nome do conjunto de registros. Não é necessário incluir '\@' no nome do conjunto de registros ao criar um conjunto de registros SRV definido no Apex da zona.

```powershell
New-AzDnsRecordSet -Name "_sip._tls" -RecordType SRV -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Priority 0 -Weight 5 -Port 8080 -Target "sip.contoso.com") 
```


### <a name="create-a-txt-record-set-with-a-single-record"></a>Criar um conjunto de registros TXT com um único registro

O exemplo a seguir mostra como criar um registro TXT. Para obter mais informações sobre o comprimento máximo de cadeia de caracteres com suporte em registros TXT, consulte [registros txt](dns-zones-records.md#txt-records).

```powershell
New-AzDnsRecordSet -Name "test-txt" -RecordType TXT -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Value "This is a TXT record") 
```


## <a name="get-a-record-set"></a>Obter um conjunto de registros

Para recuperar um conjunto de registros existente, use `Get-AzDnsRecordSet`. Esse cmdlet retorna um objeto local que representa o conjunto de registros no DNS do Azure.

Assim como ocorre com `New-AzDnsRecordSet`, o nome do conjunto de registros fornecido deve ser um nome *relativo* , o que significa que ele deve excluir o nome da zona. Você também precisa especificar o tipo de registro e a zona que contém o conjunto de registros.

O exemplo a seguir mostra como recuperar um conjunto de registros. Neste exemplo, a zona é especificada usando os parâmetros `-ZoneName` e `-ResourceGroupName`.

```powershell
$rs = Get-AzDnsRecordSet -Name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
```

Como alternativa, você também pode especificar a zona usando um objeto de zona, passado usando o parâmetro `-Zone`.

```powershell
$zone = Get-AzDnsZone -Name "contoso.com" -ResourceGroupName "MyResourceGroup"
$rs = Get-AzDnsRecordSet -Name "www" -RecordType A -Zone $zone
```

## <a name="list-record-sets"></a>Listar conjuntos de registros

Você também pode usar `Get-AzDnsZone` para listar conjuntos de registros em uma zona, omitindo os parâmetros `-Name` e/ou `-RecordType`.

O exemplo a seguir retorna todos os conjuntos de registros na zona:

```powershell
$recordsets = Get-AzDnsRecordSet -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
```

O exemplo a seguir mostra como todos os conjuntos de registros de um determinado tipo podem ser recuperados especificando o tipo de registro ao omitir o nome do conjunto de registros:

```powershell
$recordsets = Get-AzDnsRecordSet -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
```

Para recuperar todos os conjuntos de registros com um determinado nome, entre os tipos de registro, você precisa recuperar todos os conjuntos de registros e, em seguida, filtrar os resultados:

```powershell
$recordsets = Get-AzDnsRecordSet -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" | where {$_.Name.Equals("www")}
```

Em todos os exemplos acima, a zona pode ser especificada usando os parâmetros `-ZoneName` e `-ResourceGroupName`(como mostrado) ou especificando um objeto de zona:

```powershell
$zone = Get-AzDnsZone -Name "contoso.com" -ResourceGroupName "MyResourceGroup"
$recordsets = Get-AzDnsRecordSet -Zone $zone
```

## <a name="add-a-record-to-an-existing-record-set"></a>Adicionar um registro a um conjunto de registros existente

Para adicionar um registro a um conjunto de registros existente, siga as três etapas a seguir:

1. Obter o conjunto de registros existente

    ```powershell
    $rs = Get-AzDnsRecordSet -Name www –ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -RecordType A
    ```

2. Adicione o novo registro ao conjunto de registros local. Esta é uma operação off-line.

    ```powershell
    Add-AzDnsRecordConfig -RecordSet $rs -Ipv4Address "5.6.7.8"
    ```

3. Confirme a alteração de volta para o serviço DNS do Azure. 

    ```powershell
    Set-AzDnsRecordSet -RecordSet $rs
    ```

O uso de `Set-AzDnsRecordSet` *substitui* o conjunto de registros existente no DNS do Azure (e todos os registros que ele contém) pelo conjunto de registros especificado. As [verificações de eTag](dns-zones-records.md#etags) são usadas para garantir que as alterações simultâneas não sejam substituídas. Você pode usar a opção de `-Overwrite` opcional para suprimir essas verificações.

Essa sequência de operações também pode ser *canalizada*, o que significa que você passa o objeto do conjunto de registros usando o pipe em vez de passá-lo como um parâmetro:

```powershell
Get-AzDnsRecordSet -Name "www" –ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -RecordType A | Add-AzDnsRecordConfig -Ipv4Address "5.6.7.8" | Set-AzDnsRecordSet
```

Os exemplos acima mostram como adicionar um registro ' A ' a um conjunto de registros existente do tipo ' A '. Uma sequência semelhante de operações é usada para adicionar registros a conjuntos de registros de outros tipos, substituindo o parâmetro `-Ipv4Address` de `Add-AzDnsRecordConfig` com outros parâmetros específicos de cada tipo de registro. Os parâmetros para cada tipo de registro são os mesmos para o cmdlet `New-AzDnsRecordConfig`, conforme mostrado nos exemplos de tipo de registro adicionais acima.

Os conjuntos de registros do tipo ' CNAME ' ou ' SOA ' não podem conter mais de um registro. Essa restrição surge dos padrões de DNS. Não é uma limitação do DNS do Azure.

## <a name="remove-a-record-from-an-existing-record-set"></a>Remover um registro de um conjunto de registros existente

O processo para remover um registro de um conjunto de registros é semelhante ao processo para adicionar um registro a um conjunto de registros existente:

1. Obter o conjunto de registros existente

    ```powershell
    $rs = Get-AzDnsRecordSet -Name www –ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -RecordType A
    ```

2. Remova o registro do objeto do conjunto de registros local. Esta é uma operação off-line. O registro que está sendo removido deve ser uma correspondência exata com um registro existente em todos os parâmetros.

    ```powershell
    Remove-AzDnsRecordConfig -RecordSet $rs -Ipv4Address "5.6.7.8"
    ```

3. Confirme a alteração de volta para o serviço DNS do Azure. Use a opção `-Overwrite` opcional para suprimir as [verificações de eTag](dns-zones-records.md#etags) para alterações simultâneas.

    ```powershell
    Set-AzDnsRecordSet -RecordSet $Rs
    ```

Usar a sequência acima para remover o último registro de um conjunto de registros não exclui o conjunto de registros, em vez disso deixa um conjunto de registros vazio. Para remover totalmente um conjunto de registros, consulte [excluir um conjunto de registros](#delete-a-record-set).

Da mesma forma que adicionar registros a um conjunto de registros, a sequência de operações para remover um conjunto de registros também pode ser canalizada:

```powershell
Get-AzDnsRecordSet -Name www –ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -RecordType A | Remove-AzDnsRecordConfig -Ipv4Address "5.6.7.8" | Set-AzDnsRecordSet
```

Tipos de registro diferentes têm suporte passando os parâmetros específicos de tipo apropriados para `Remove-AzDnsRecordSet`. Os parâmetros para cada tipo de registro são os mesmos para o cmdlet `New-AzDnsRecordConfig`, conforme mostrado nos exemplos de tipo de registro adicionais acima.


## <a name="modify-an-existing-record-set"></a>Modificar um conjunto de registros existente

As etapas para modificar um conjunto de registros existente são semelhantes às etapas que você seguir ao adicionar ou remover registros de um conjunto de registros:

1. Recupere o conjunto de registros existente usando `Get-AzDnsRecordSet`.
2. Modifique o objeto do conjunto de registros local:
    * Adicionando ou removendo registros
    * Alterando os parâmetros de registros existentes
    * Alterando os metadados do conjunto de registros e TTL (vida útil)
3. Confirme suas alterações usando o cmdlet `Set-AzDnsRecordSet`. Isso *substitui* o conjunto de registros existente no DNS do Azure com o conjunto de registros especificado.

Ao usar `Set-AzDnsRecordSet`, as [verificações de eTag](dns-zones-records.md#etags) são usadas para garantir que as alterações simultâneas não sejam substituídas. Você pode usar a opção de `-Overwrite` opcional para suprimir essas verificações.

### <a name="to-update-a-record-in-an-existing-record-set"></a>Para atualizar um registro em um conjunto de registros existente

Neste exemplo, alteramos o endereço IP de um registro ' A ' existente:

```powershell
$rs = Get-AzDnsRecordSet -name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
$rs.Records[0].Ipv4Address = "9.8.7.6"
Set-AzDnsRecordSet -RecordSet $rs
```

### <a name="to-modify-an-soa-record"></a>Para modificar um registro SOA

Você não pode adicionar ou remover registros do conjunto de registros SOA criado automaticamente no Apex da zona (`-Name "@"`, incluindo aspas). No entanto, você pode modificar qualquer um dos parâmetros no registro SOA (exceto "host") e o TTL do conjunto de registros.

O exemplo a seguir mostra como alterar a propriedade *email* do registro soa:

```powershell
$rs = Get-AzDnsRecordSet -Name "@" -RecordType SOA -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
$rs.Records[0].Email = "admin.contoso.com"
Set-AzDnsRecordSet -RecordSet $rs
```

### <a name="to-modify-ns-records-at-the-zone-apex"></a>Para modificar os registros NS no Apex da zona

O conjunto de registros NS no Apex da zona é criado automaticamente com cada zona DNS. Ele contém os nomes dos servidores de nome DNS do Azure atribuídos à zona.

Você pode adicionar servidores de nomes adicionais a esse conjunto de registros NS para dar suporte a domínios de hospedagem com mais de um provedor de DNS. Você também pode modificar o TTL e os metadados para este conjunto de registros. No entanto, não é possível remover ou modificar os servidores de nomes DNS do Azure populados previamente.

Observe que isso se aplica somente ao conjunto de registros NS no Apex da zona. Outros conjuntos de registros NS em sua zona (como usados para delegar zonas filhas) podem ser modificados sem restrição.

O exemplo a seguir mostra como adicionar um servidor de nome adicional ao conjunto de registros NS no Apex da zona:

```powershell
$rs = Get-AzDnsRecordSet -Name "@" -RecordType NS -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
Add-AzDnsRecordConfig -RecordSet $rs -Nsdname ns1.myotherdnsprovider.com
Set-AzDnsRecordSet -RecordSet $rs
```

### <a name="to-modify-record-set-metadata"></a>Para modificar os metadados do conjunto de registros

Os [metadados do conjunto de registros](dns-zones-records.md#tags-and-metadata) podem ser usados para associar dados específicos do aplicativo a cada conjunto de registros, como pares chave-valor.

O exemplo a seguir mostra como modificar os metadados de um conjunto de registros existente:

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


## <a name="delete-a-record-set"></a>Excluir um conjunto de registros

Os conjuntos de registros podem ser excluídos usando o cmdlet `Remove-AzDnsRecordSet`. Excluir um conjunto de registros também exclui todos os registros dentro do conjunto de registros.

> [!NOTE]
> Não é possível excluir os conjuntos de registros SOA e NS no Apex da zona (`-Name '@'`).  O DNS do Azure criou esses itens automaticamente quando a zona foi criada e os exclui automaticamente quando a zona é excluída.

O exemplo a seguir mostra como excluir um conjunto de registros. Neste exemplo, o nome do conjunto de registros, o tipo de conjunto de registros, o nome da zona e o grupo de recursos são especificados explicitamente.

```powershell
Remove-AzDnsRecordSet -Name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
```

Como alternativa, o conjunto de registros pode ser especificado pelo nome e tipo e pela zona especificada usando um objeto:

```powershell
$zone = Get-AzDnsZone -Name "contoso.com" -ResourceGroupName "MyResourceGroup"
Remove-AzDnsRecordSet -Name "www" -RecordType A -Zone $zone
```

Como uma terceira opção, o próprio conjunto de registros pode ser especificado usando um objeto de conjunto de registros:

```powershell
$rs = Get-AzDnsRecordSet -Name www -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
Remove-AzDnsRecordSet -RecordSet $rs
```

Quando você especifica o conjunto de registros a ser excluído usando um objeto de conjunto de registros, as [verificações de eTag](dns-zones-records.md#etags) são usadas para garantir que as alterações simultâneas não sejam excluídas. Você pode usar a opção de `-Overwrite` opcional para suprimir essas verificações.

O objeto do conjunto de registros também pode ser canalizado em vez de ser passado como um parâmetro:

```powershell
Get-AzDnsRecordSet -Name www -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" | Remove-AzDnsRecordSet
```

## <a name="confirmation-prompts"></a>Pedidos de confirmação

Todos os cmdlets `New-AzDnsRecordSet`, `Set-AzDnsRecordSet` e `Remove-AzDnsRecordSet` suportam pedidos de confirmação.

Cada cmdlet solicitará uma confirmação se a variável de preferência `$ConfirmPreference` PowerShell tiver um valor de `Medium` ou inferior. Como o valor padrão para `$ConfirmPreference` é `High`, esses prompts não são fornecidos ao usar as configurações padrão do PowerShell.

Pode substituir a definição `$ConfirmPreference` atual com o parâmetro `-Confirm`. Se especificar `-Confirm` ou `-Confirm:$True` , o cmdlet irá pedir-lhe a confirmação antes de ser executado. Se especificar `-Confirm:$False`, o cmdlet não solicita a confirmação. 

Para obter mais informações sobre `-Confirm` e `$ConfirmPreference`, veja [Sobre as Variáveis de Preferência](/powershell/module/microsoft.powershell.core/about/about_preference_variables).

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [zonas e registros no DNS do Azure](dns-zones-records.md).
<br>
Saiba como [proteger suas zonas e registros](dns-protect-zones-recordsets.md) ao usar o DNS do Azure.
<br>
Examine a [documentação de referência do PowerShell do DNS do Azure](/powershell/module/az.dns).
