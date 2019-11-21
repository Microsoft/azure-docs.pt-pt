---
title: Manage DNS zones in Azure DNS - Azure CLI | Microsoft Docs
description: You can manage DNS zones using Azure CLI. This article shows how to update, delete and create DNS zones on Azure DNS.
services: dns
documentationcenter: na
author: asudbring
manager: timlt
ms.assetid: 8ab63bc4-5135-4ed8-8c0b-5f0712b9afed
ms.service: dns
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/27/2017
ms.author: allensu
ms.openlocfilehash: e1a3c401de32beb9757011ac306443334da8b867
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74211928"
---
# <a name="how-to-manage-dns-zones-in-azure-dns-using-the-azure-cli"></a>How to manage DNS Zones in Azure DNS using the Azure CLI

> [!div class="op_single_selector"]
> * [Portal](dns-operations-dnszones-portal.md)
> * [PowerShell](dns-operations-dnszones.md)
> * [CLI do Azure](dns-operations-dnszones-cli.md)


This guide shows how to manage your DNS zones by using the cross-platform Azure CLI, which is available for Windows, Mac and Linux. You can also manage your DNS zones using [Azure PowerShell](dns-operations-dnszones.md) or the Azure portal.

This guide specifically deals with Public DNS zones. For information on using Azure CLI to manage Private Zones in Azure DNS, see [Get started with Azure DNS Private Zones using Azure CLI](private-dns-getstarted-cli.md).

## <a name="introduction"></a>Introdução

[!INCLUDE [dns-create-zone-about](../../includes/dns-create-zone-about-include.md)]

## <a name="set-up-azure-cli-for-azure-dns"></a>Configurar CLI do Azure para o Azure DNS

### <a name="before-you-begin"></a>Antes de começar

Antes de iniciar a configuração, verifique se tem os seguintes itens.

* Uma subscrição do Azure. Se ainda não tiver uma subscrição do Azure, pode ativar os [Benefícios de subscritor do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou inscrever-se numa [conta gratuita](https://azure.microsoft.com/pricing/free-trial/).

* Instalar a versão mais recente da CLI do Azure, disponível para Windows, Linux ou Mac. Existem mais informações disponíveis em [Instalar a CLI do Azure](https://docs.microsoft.com/cli/azure/install-az-cli2).

### <a name="sign-in-to-your-azure-account"></a>Inicie sessão na sua conta do Azure

Abra uma janela de consola e autentique com as suas credenciais. Para obter mais informações, veja [Iniciar sessão no Azure a partir da CLI do Azure](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest)

```
az login
```

### <a name="select-the-subscription"></a>Selecionar a subscrição

Verifique as subscrições da conta.

```
az account list
```

Escolha qual das suas subscrições do Azure utilizar.

```azurecli
az account set --subscription "subscription name"
```

### <a name="optional-to-installuse-azure-dns-private-zones-feature"></a>Optional: To install/use Azure DNS Private Zones feature
The Azure DNS Private Zone feature is available via an extension to the Azure CLI. Instalar a extensão “dns” da CLI do Azure 
```
az extension add --name dns
``` 

### <a name="create-a-resource-group"></a>Criar um grupo de recursos

O Azure Resource Manager requer que todos os grupos de recursos especifiquem uma localização, Isto é utilizado como a localização predefinida para recursos nesse grupo de recursos. No entanto, uma vez que todos os recursos DNS são globais, não regionais, a opção de localização do grupo de recursos não tem impacto no DNS do Azure.

Pode ignorar este passo se estiver a utilizar um grupo de recursos existente.

```azurecli
az group create --name myresourcegroup --location "West US"
```

## <a name="getting-help"></a>Obter ajuda

All Azure CLI commands relating to Azure DNS start with `az network dns`. Help is available for each command using the `--help` option (short form `-h`).  Por exemplo:

```azurecli
az network dns --help
az network dns zone --help
az network dns zone create --help
```

## <a name="create-a-dns-zone"></a>Criar uma zona DNS

Uma zona DNS é criada ao utilizar o comando `az network dns zone create`. Para obter ajuda, consulte `az network dns zone create -h`.

The following example creates a DNS zone called *contoso.com* in the resource group called *MyResourceGroup*:

```azurecli
az network dns zone create --resource-group MyResourceGroup --name contoso.com
```

### <a name="to-create-a-dns-zone-with-tags"></a>To create a DNS zone with tags

The following example shows how to create a DNS zone with two [Azure Resource Manager tags](dns-zones-records.md#tags), *project = demo* and *env = test*, by using the `--tags` parameter (short form `-t`):

```azurecli
az network dns zone create --resource-group MyResourceGroup --name contoso.com --tags "project=demo" "env=test"
```

## <a name="get-a-dns-zone"></a>Get a DNS zone

To retrieve a DNS zone, use `az network dns zone show`. Para obter ajuda, consulte `az network dns zone show --help`.

The following example returns the DNS zone *contoso.com* and its associated data from resource group *MyResourceGroup*. 

```azurecli
az network dns zone show --resource-group myresourcegroup --name contoso.com
```

O exemplo seguinte é a resposta.

```json
{
  "etag": "00000002-0000-0000-3d4d-64aa3689d201",
  "id": "/subscriptions/147a22e9-2356-4e56-b3de-1f5842ae4a3b/resourceGroups/myresourcegroup/providers/Microsoft.Network/dnszones/contoso.com",
  "location": "global",
  "maxNumberOfRecordSets": 5000,
  "name": "contoso.com",
  "nameServers": [
    "ns1-04.azure-dns.com.",
    "ns2-04.azure-dns.net.",
    "ns3-04.azure-dns.org.",
    "ns4-04.azure-dns.info."
  ],
  "numberOfRecordSets": 4,
  "resourceGroup": "myresourcegroup",
  "tags": {},
  "type": "Microsoft.Network/dnszones"
}
```

Tenha em atenção que os registos DNS não são devolvidos pelo `az network dns zone show`. Para listar os registos DNS, utilize `az network dns record-set list`.


## <a name="list-dns-zones"></a>Listar zonas DNS

Para enumerar as zonas DNS, utilize `az network dns zone list`. Para obter ajuda, consulte `az network dns zone list --help`.

Especificar o grupo de recursos lista apenas essas zonas no grupo de recursos:

```azurecli
az network dns zone list --resource-group MyResourceGroup
```

A omissão do grupo de recursos lista todas as zonas na subscrição:

```azurecli
az network dns zone list 
```

## <a name="update-a-dns-zone"></a>Atualizar uma zona DNS

É possível efetuar alterações a um recurso de zona DNS com `az network dns zone update`. Para obter ajuda, consulte `az network dns zone update --help`.

Este comando não atualiza qualquer um dos conjuntos de registos de DNS na zona (veja [Como gerir recursos DNS](dns-operations-recordsets-cli.md)). Só é utilizado para atualizar propriedades do recurso da própria zona. These properties are currently limited to the [Azure Resource Manager 'tags'](dns-zones-records.md#tags) for the zone resource.

The following example shows how to update the tags on a DNS zone. The existing tags are replaced by the value specified.

```azurecli
az network dns zone update --resource-group myresourcegroup --name contoso.com --set tags.team=support
```

## <a name="delete-a-dns-zone"></a>Eliminar uma zona DNS

As zonas DNS podem ser eliminadas com `az network dns zone delete`. Para obter ajuda, consulte `az network dns zone delete --help`.

> [!NOTE]
> A eliminação de uma zona DNS também elimina todos os registos DNS na zona. Esta operação não pode ser anulada. Se a zona DNS estiver em utilização, os serviços que utilizam a zona irão falhar quando a zona for eliminada.
>
>Para proteção contra a eliminação acidental de uma zona, veja [Como proteger zonas e registos DNS](dns-protect-zones-recordsets.md).

Este comando solicita a confirmação. O `--yes` comutador opcional suprime esta linha de comandos.

The following example shows how to delete the zone *contoso.com* from resource group *MyResourceGroup*.

```azurecli
az network dns zone delete --resource-group myresourcegroup --name contoso.com
```

## <a name="next-steps"></a>Passos seguintes

Learn how to [manage record sets and records](dns-getstarted-create-recordset-cli.md) in your DNS zone.

Learn how to [delegate your domain to Azure DNS](dns-domain-delegation.md).

