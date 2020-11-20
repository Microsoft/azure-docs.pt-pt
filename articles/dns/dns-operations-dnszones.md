---
title: Gerir zonas de DNS em Azure DNS - PowerShell Microsoft Docs
description: Pode gerir zonas DNS utilizando a Azure Powershell. Este artigo descreve como atualizar, excluir e criar zonas DNS no Azure DNS
services: dns
documentationcenter: na
author: rohinkoul
ms.service: dns
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/19/2018
ms.author: rohink
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 6abcca9d9888dc8968d7233e7aee6cd76aa215f7
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94965753"
---
# <a name="how-to-manage-dns-zones-using-powershell"></a>Como gerir as Zonas DNS usando o PowerShell

> [!div class="op_single_selector"]
> * [Portal](dns-operations-dnszones-portal.md)
> * [PowerShell](dns-operations-dnszones.md)
> * [CLI clássico de Azure](./dns-operations-dnszones-cli.md)
> * [CLI do Azure](dns-operations-dnszones-cli.md)

Este artigo mostra-lhe como gerir as suas zonas DNS utilizando o Azure PowerShell. Também pode gerir as suas zonas DNS utilizando a plataforma transversal [Azure CLI](dns-operations-dnszones-cli.md) ou o portal Azure.

Este guia trata especificamente das zonas públicas de DNS. Para obter informações sobre a utilização do Azure PowerShell para gerir zonas privadas em Azure DNS, consulte [Começar com as Zonas Privadas Azure DNS utilizando a Azure PowerShell](private-dns-getstarted-powershell.md).

[!INCLUDE [dns-create-zone-about](../../includes/dns-create-zone-about-include.md)]

[!INCLUDE [dns-powershell-setup](../../includes/dns-powershell-setup-include.md)]


## <a name="create-a-dns-zone"></a>Criar uma zona DNS

Uma zona DNS é criada ao utilizar o cmdlet `New-AzureRmDnsZone`.

O exemplo a seguir cria uma zona DENS chamada *contoso.com* no grupo de recursos chamado *MyResourceGroup*:

```powershell
New-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup
```

O exemplo a seguir mostra como criar uma zona de DNS com [duas tags Azure Resource Manager](dns-zones-records.md#tags), *project = demo* e *env = teste*:

```powershell
New-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup -Tag @{ project="demo"; env="test" }
```

O Azure DNS também suporta zonas privadas de DNS.  Para saber mais sobre zonas DNS privadas, veja [Utilizar o DNS do Azure para domínios privados](private-dns-overview.md). Para obter um exemplo de como criar uma zona DNS privada, veja [Começar a utilizar zonas privadas do DNS do Azure com o PowerShell](./private-dns-getstarted-powershell.md).

## <a name="get-a-dns-zone"></a>Obtenha uma zona DE DNS

Para recuperar uma zona DE DNS, utilize o `Get-AzureRmDnsZone` cmdlet. Esta operação devolve um objeto de zona DNS correspondente a uma zona existente no Azure DNS. O objeto contém dados sobre a zona (como o número de conjuntos de recordes), mas não contém os próprios conjuntos de registos `Get-AzureRmDnsRecordSet` (ver).

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

É possível efetuar alterações a um recurso de zona DNS com `Set-AzureRmDnsZone`. Este cmdlet não atualiza qualquer um dos conjuntos de registos de DNS na zona (veja [Com gerir recursos DNS](dns-operations-recordsets.md)). Só é utilizado para atualizar propriedades do recurso da própria zona. As propriedades da zonaificável estão atualmente limitadas às ['tags' do Gestor de Recursos Azure para o recurso de zona.](dns-zones-records.md#tags)

Utilize uma das duas seguintes formas de atualizar uma zona DE DNS:

### <a name="specify-the-zone-using-the-zone-name-and-resource-group"></a>Especificar a zona usando o nome da zona e o grupo de recursos

Esta abordagem substitui as etiquetas de zona existentes pelos valores especificados.

```powershell
Set-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup -Tag @{ project="demo"; env="test" }
```

### <a name="specify-the-zone-using-a-zone-object"></a>Especifique o horário com um objeto de $zone

Esta abordagem recupera o objeto de zona existente, modifica as etiquetas e, em seguida, comete as alterações. Desta forma, as etiquetas existentes podem ser preservadas.

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

Ao utilizar `Set-AzureRmDnsZone` com um objeto $zone, os [controlos Etag](dns-zones-records.md#etags) são utilizados para garantir que as alterações simultâneas não são substituídas. Pode utilizar o interruptor opcional `-Overwrite` para suprimir estas verificações.

## <a name="delete-a-dns-zone"></a>Excluir uma Zona DNS

As zonas DE DNS podem ser eliminadas utilizando o `Remove-AzureRmDnsZone` cmdlet.

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

Tal como `Set-AzureRmDnsZone` com , especificar a zona utilizando um `$zone` objeto permite que as verificações Etag garantam que as alterações simultâneas não sejam eliminadas. Use o `-Overwrite` interruptor para suprimir estas verificações.

## <a name="confirmation-prompts"></a>Pedidos de confirmação

Todos os cmdlets `New-AzureRmDnsZone`, `Set-AzureRmDnsZone` e `Remove-AzureRmDnsZone` suportam pedidos de confirmação.

`New-AzureRmDnsZone` e `Set-AzureRmDnsZone` pedem a confirmação, se a variável de preferência `$ConfirmPreference` do PowerShell tiver um valor de `Medium` ou inferior. Devido ao elevado impacto potencial de eliminação de uma zona DNS, o cmdlet `Remove-AzureRmDnsZone` solicita a confirmação se a variável `$ConfirmPreference` do PowerShell tiver qualquer valor diferente de `None`.

Dado que o valor predefinido para `$ConfirmPreference` é `High`, apenas `Remove-AzureRmDnsZone` solicita a confirmação por predefinição.

Pode substituir a definição `$ConfirmPreference` atual com o parâmetro `-Confirm`. Se especificar `-Confirm` ou `-Confirm:$True` , o cmdlet irá pedir-lhe a confirmação antes de ser executado. Se especificar `-Confirm:$False`, o cmdlet não solicita a confirmação.

Para obter mais informações sobre `-Confirm` e `$ConfirmPreference`, veja [Sobre as Variáveis de Preferência](/powershell/module/microsoft.powershell.core/about/about_preference_variables).

## <a name="next-steps"></a>Passos seguintes

Saiba como [gerir recordes e registos](dns-operations-recordsets.md) na sua zona de DNS.
<br>
Saiba como [delegar o seu domínio no Azure DNS](dns-domain-delegation.md).
<br>
Reveja a documentação de referência do [Azure DNS PowerShell](/powershell/module/azurerm.dns).