---
title: Gerenciar zonas DNS no DNS do Azure-PowerShell | Microsoft Docs
description: Você pode gerenciar as zonas DNS usando o Azure PowerShell. Este artigo descreve como atualizar, excluir e criar zonas DNS no DNS do Azure
services: dns
documentationcenter: na
author: vhorne
manager: timlt
ms.assetid: a67992ab-8166-4052-9b28-554c5a39e60c
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/19/2018
ms.author: victorh
ms.openlocfilehash: dd238be6dbfcd14480b2c0cf03236902ef39e722
ms.sourcegitcommit: 4d177e6d273bba8af03a00e8bb9fe51a447196d0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/04/2019
ms.locfileid: "71959415"
---
# <a name="how-to-manage-dns-zones-using-powershell"></a>Como gerenciar Zonas DNS usando o PowerShell

> [!div class="op_single_selector"]
> * [Portal](dns-operations-dnszones-portal.md)
> * [PowerShell](dns-operations-dnszones.md)
> * [CLI clássica do Azure](dns-operations-dnszones-cli-nodejs.md)
> * [CLI do Azure](dns-operations-dnszones-cli.md)

Este artigo mostra como gerenciar suas zonas DNS usando Azure PowerShell. Você também pode gerenciar suas zonas DNS usando o [CLI do Azure](dns-operations-dnszones-cli.md) de plataforma cruzada ou o portal do Azure.

Este guia trata especificamente das zonas DNS públicas. Para obter informações sobre como usar Azure PowerShell para gerenciar zonas privadas no DNS do Azure, consulte Introdução [ao zonas privadas do DNS do Azure usando Azure PowerShell](private-dns-getstarted-powershell.md).

[!INCLUDE [dns-create-zone-about](../../includes/dns-create-zone-about-include.md)]

[!INCLUDE [dns-powershell-setup](../../includes/dns-powershell-setup-include.md)]


## <a name="create-a-dns-zone"></a>Criar uma zona DNS

Uma zona DNS é criada ao utilizar o cmdlet `New-AzureRmDnsZone`.

O exemplo a seguir cria uma zona DNS chamada *contoso.com* no grupo de recursos chamado *MyResource*Group:

```powershell
New-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup
```

O exemplo a seguir mostra como criar uma zona DNS com duas [marcas Azure Resource Manager](dns-zones-records.md#tags), *Project = demo* e *env = Test*:

```powershell
New-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup -Tag @{ project="demo"; env="test" }
```

O DNS do Azure também dá suporte a zonas DNS privadas.  Para saber mais sobre zonas DNS privadas, veja [Utilizar o DNS do Azure para domínios privados](private-dns-overview.md). Para obter um exemplo de como criar uma zona DNS privada, veja [Começar a utilizar zonas privadas do DNS do Azure com o PowerShell](./private-dns-getstarted-powershell.md).

## <a name="get-a-dns-zone"></a>Obter uma zona DNS

Para recuperar uma zona DNS, use o cmdlet `Get-AzureRmDnsZone`. Esta operação retorna um objeto de zona DNS correspondente a uma zona existente no DNS do Azure. O objeto contém dados sobre a zona (como o número de conjuntos de registros), mas não contém os próprios conjuntos de registros (consulte `Get-AzureRmDnsRecordSet`).

```powershell
Get-AzureRmDnsZone -Name contoso.com –ResourceGroupName MyAzureResourceGroup

Name                  : contoso.com
ResourceGroupName     : myresourcegroup
Etag                  : 00000003-0000-0000-8ec2-f4879750d201
Tags                  : {project, env}
NameServers           : {ns1-01.azure-dns.com., ns2-01.azure-dns.net., ns3-01.azure-dns.org.,
                        ns4-01.azure-dns.info.}
NumberOfRecordSets    : 2
MaxNumberOfRecordSets : 5000
```

## <a name="list-dns-zones"></a>Listar zonas DNS

Ao omitir o nome da zona de `Get-AzureRmDnsZone`, pode enumerar todas as zonas de um grupo de recursos. Esta operação devolve uma matriz de objetos de zona.

```powershell
$zoneList = Get-AzureRmDnsZone -ResourceGroupName MyAzureResourceGroup
```

Ao omitir o nome da zona e o nome do grupo de recursos de `Get-AzureRmDnsZone`, pode enumerar todas as zonas na subscrição do Azure.

```powershell
$zoneList = Get-AzureRmDnsZone
```

## <a name="update-a-dns-zone"></a>Atualizar uma zona DNS

É possível efetuar alterações a um recurso de zona DNS com `Set-AzureRmDnsZone`. Este cmdlet não atualiza qualquer um dos conjuntos de registos de DNS na zona (veja [Com gerir recursos DNS](dns-operations-recordsets.md)). Só é utilizado para atualizar propriedades do recurso da própria zona. As propriedades de zona graváveis atualmente são limitadas às [Azure Resource Manager ' tags ' para o recurso de zona](dns-zones-records.md#tags).

Use uma das duas maneiras a seguir para atualizar uma zona DNS:

### <a name="specify-the-zone-using-the-zone-name-and-resource-group"></a>Especifique a zona usando o nome da zona e o grupo de recursos

Essa abordagem substitui as marcas de zona existentes pelos valores especificados.

```powershell
Set-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup -Tag @{ project="demo"; env="test" }
```

### <a name="specify-the-zone-using-a-zone-object"></a>Especifique o horário com um objeto de $zone

Essa abordagem recupera o objeto de zona existente, modifica as marcas e, em seguida, confirma as alterações. Dessa forma, as marcas existentes podem ser preservadas.

```powershell
# Get the zone object
$zone = Get-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup

# Remove an existing tag
$zone.Tags.Remove("project")

# Add a new tag
$zone.Tags.Add("status","approved")

# Commit changes
Set-AzureRmDnsZone -Zone $zone
```

Ao usar `Set-AzureRmDnsZone` com um objeto $zone, as [verificações de eTag](dns-zones-records.md#etags) são usadas para garantir que as alterações simultâneas não sejam substituídas. Você pode usar a opção opcional `-Overwrite` para suprimir essas verificações.

## <a name="delete-a-dns-zone"></a>Excluir uma zona DNS

As zonas DNS podem ser excluídas usando o cmdlet `Remove-AzureRmDnsZone`.

> [!NOTE]
> A eliminação de uma zona DNS também elimina todos os registos DNS na zona. Esta operação não pode ser anulada. Se a zona DNS estiver em utilização, os serviços que utilizam a zona irão falhar quando a zona for eliminada.
>
>Para proteção contra a eliminação acidental de uma zona, veja [Como proteger zonas e registos DNS](dns-protect-zones-recordsets.md).


Utilize uma das duas formas seguintes para eliminar uma zona DNS:

### <a name="specify-the-zone-using-the-zone-name-and-resource-group-name"></a>Especifique a zona com o nome da zona e o nome do grupo de recursos

```powershell
Remove-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup
```

### <a name="specify-the-zone-using-a-zone-object"></a>Especifique o horário com um objeto de $zone

Pode especificar a zona a eliminar com um objeto `$zone` devolvido por `Get-AzureRmDnsZone`.

```powershell
$zone = Get-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup
Remove-AzureRmDnsZone -Zone $zone
```

O objeto de zona também pode ser direcionado, em vez de ser transmitido como um parâmetro:

```powershell
Get-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup | Remove-AzureRmDnsZone

```

Assim como ocorre com `Set-AzureRmDnsZone`, especificar a zona usando um objeto `$zone` permite verificações de eTag para garantir que as alterações simultâneas não sejam excluídas. Use a opção `-Overwrite` para suprimir essas verificações.

## <a name="confirmation-prompts"></a>Pedidos de confirmação

Todos os cmdlets `New-AzureRmDnsZone`, `Set-AzureRmDnsZone` e `Remove-AzureRmDnsZone` suportam pedidos de confirmação.

`New-AzureRmDnsZone` e `Set-AzureRmDnsZone` pedem a confirmação, se a variável de preferência `$ConfirmPreference` do PowerShell tiver um valor de `Medium` ou inferior. Devido ao elevado impacto potencial de eliminação de uma zona DNS, o cmdlet `Remove-AzureRmDnsZone` solicita a confirmação se a variável `$ConfirmPreference` do PowerShell tiver qualquer valor diferente de `None`.

Dado que o valor predefinido para `$ConfirmPreference` é `High`, apenas `Remove-AzureRmDnsZone` solicita a confirmação por predefinição.

Pode substituir a definição `$ConfirmPreference` atual com o parâmetro `-Confirm`. Se especificar `-Confirm` ou `-Confirm:$True` , o cmdlet irá pedir-lhe a confirmação antes de ser executado. Se especificar `-Confirm:$False`, o cmdlet não solicita a confirmação.

Para obter mais informações sobre `-Confirm` e `$ConfirmPreference`, veja [Sobre as Variáveis de Preferência](/powershell/module/microsoft.powershell.core/about/about_preference_variables).

## <a name="next-steps"></a>Passos seguintes

Aprenda a [gerenciar conjuntos de registros e registros](dns-operations-recordsets.md) em sua zona DNS.
<br>
Saiba como [delegar seu domínio ao DNS do Azure](dns-domain-delegation.md).
<br>
Examine a [documentação de referência do PowerShell do DNS do Azure](/powershell/module/azurerm.dns).

