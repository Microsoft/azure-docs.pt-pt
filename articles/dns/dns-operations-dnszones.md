---
title: Gerir zonas DNS no DNS do Azure - PowerShell | Microsoft Docs
description: Pode gerir zonas DNS com o Azure Powershell. Este artigo descreve como atualizar, eliminar e criar zonas DNS no DNS do Azure
services: dns
documentationcenter: na
author: KumudD
manager: timlt
ms.assetid: a67992ab-8166-4052-9b28-554c5a39e60c
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/19/2018
ms.author: kumud
ms.openlocfilehash: e7b0bc32d3fa8fbcf73298b6988655fca7cfa793
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/03/2018
ms.locfileid: "32772658"
---
# <a name="how-to-manage-dns-zones-using-powershell"></a>Como gerir zonas DNS com o PowerShell

> [!div class="op_single_selector"]
> * [Portal](dns-operations-dnszones-portal.md)
> * [PowerShell](dns-operations-dnszones.md)
> * [CLI do Azure 1.0](dns-operations-dnszones-cli-nodejs.md)
> * [CLI 2.0 do Azure](dns-operations-dnszones-cli.md)

Este artigo mostra como gerir as zonas DNS com o Azure PowerShell. Também pode gerir as zonas DNS com a plataforma [CLI do Azure](dns-operations-dnszones-cli.md) ou o portal do Azure.

Este guia especificamente lida com zonas DNS público. Para obter informações sobre como utilizar o Azure PowerShell para gerir privada zonas no DNS do Azure, consulte [começar com zonas de DNS privado do Azure com o Azure PowerShell](private-dns-getstarted-powershell.md).

[!INCLUDE [dns-create-zone-about](../../includes/dns-create-zone-about-include.md)]

[!INCLUDE [dns-powershell-setup](../../includes/dns-powershell-setup-include.md)]


## <a name="create-a-dns-zone"></a>Criar uma zona DNS

Uma zona DNS é criada ao utilizar o cmdlet `New-AzureRmDnsZone`.

O exemplo seguinte cria uma zona DNS denominada *contoso.com* no grupo de recursos chamado *MyResourceGroup*:

```powershell
New-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup
```

O exemplo seguinte mostra como criar uma zona DNS com dois [etiquetas do Azure Resource Manager](dns-zones-records.md#tags), *projeto = demonstração* e *env = test*:

```powershell
New-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup -Tag @{ project="demo"; env="test" }
```

O DNS do Azure também suporta agora zonas DNS privadas (atualmente em pré-visualização pública).  Para saber mais sobre zonas DNS privadas, veja [Utilizar o DNS do Azure para domínios privados](private-dns-overview.md). Para obter um exemplo de como criar uma zona DNS privada, veja [Começar a utilizar zonas privadas do DNS do Azure com o PowerShell](./private-dns-getstarted-powershell.md).

## <a name="get-a-dns-zone"></a>Obter uma zona DNS

Para obter uma zona DNS, utilize o `Get-AzureRmDnsZone` cmdlet. Esta operação devolve um objeto de zona DNS correspondente a uma zona existente no DNS do Azure. O objecto contém dados sobre a zona (por exemplo, o número de conjuntos de registos), mas não contém os conjuntos de registo próprios (consulte `Get-AzureRmDnsRecordSet`).

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

É possível efetuar alterações a um recurso de zona DNS com `Set-AzureRmDnsZone`. Este cmdlet não atualiza qualquer um dos conjuntos de registos de DNS na zona (veja [Com gerir recursos DNS](dns-operations-recordsets.md)). Só é utilizado para atualizar propriedades do recurso da própria zona. As propriedades da zona gravável são actualmente limitadas à [do Azure Resource Manager 'etiquetas' para o recurso de zona](dns-zones-records.md#tags).

Utilize uma das duas formas a seguir para atualizar uma zona DNS:

### <a name="specify-the-zone-using-the-zone-name-and-resource-group"></a>Especifique o horário a utilizar o grupo de recursos e nome de zona

Esta abordagem substitui as etiquetas de zona existente com os valores especificados.

```powershell
Set-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup -Tag @{ project="demo"; env="test" }
```

### <a name="specify-the-zone-using-a-zone-object"></a>Especifique o horário com um objeto de $zone

Esta abordagem obtém o objeto de zona existente, modifica as etiquetas e, em seguida, consolida as alterações. Desta forma, podem ser preservadas etiquetas existentes.

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

Quando utilizar `Set-AzureRmDnsZone` com um objeto de $zone [Etag verifica](dns-zones-records.md#etags) são utilizados para garantir que as alterações em simultâneo não são substituídas. Pode utilizar o opcional `-Overwrite` comutador para suprimir estas verificações.

## <a name="delete-a-dns-zone"></a>Eliminar uma zona DNS

Zonas DNS podem ser eliminadas utilizando o `Remove-AzureRmDnsZone` cmdlet.

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

Tal como com `Set-AzureRmDnsZone`, especificando a zona utilizando um `$zone` objeto permite Etag verificações garantir que as alterações em simultâneo não são eliminadas. Utilize o `-Overwrite` comutador para suprimir estas verificações.

## <a name="confirmation-prompts"></a>Pedidos de confirmação

Todos os cmdlets `New-AzureRmDnsZone`, `Set-AzureRmDnsZone` e `Remove-AzureRmDnsZone` suportam pedidos de confirmação.

`New-AzureRmDnsZone` e `Set-AzureRmDnsZone` pedem a confirmação, se a variável de preferência `$ConfirmPreference` do PowerShell tiver um valor de `Medium` ou inferior. Devido ao elevado impacto potencial de eliminação de uma zona DNS, o cmdlet `Remove-AzureRmDnsZone` solicita a confirmação se a variável `$ConfirmPreference` do PowerShell tiver qualquer valor diferente de `None`.

Dado que o valor predefinido para `$ConfirmPreference` é `High`, apenas `Remove-AzureRmDnsZone` solicita a confirmação por predefinição.

Pode substituir a definição `$ConfirmPreference` atual com o parâmetro `-Confirm`. Se especificar `-Confirm` ou `-Confirm:$True` , o cmdlet irá pedir-lhe a confirmação antes de ser executado. Se especificar `-Confirm:$False`, o cmdlet não solicita a confirmação.

Para obter mais informações sobre `-Confirm` e `$ConfirmPreference`, veja [Sobre as Variáveis de Preferência](https://msdn.microsoft.com/powershell/reference/5.1/Microsoft.PowerShell.Core/about/about_Preference_Variables).

## <a name="next-steps"></a>Passos Seguintes

Saiba como [gerir conjuntos de registos e registos](dns-operations-recordsets.md) na sua zona DNS.
<br>
Saiba como [delegar o domínio ao DNS do Azure](dns-domain-delegation.md).
<br>
Reveja o [documentação de referência do PowerShell de DNS do Azure](/powershell/module/azurerm.dns).

