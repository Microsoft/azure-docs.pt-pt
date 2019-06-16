---
title: Gerir zonas DNS no DNS do Azure - CLI do Azure | Documentos da Microsoft
description: Pode gerir zonas DNS com a CLI do Azure. Este artigo mostra como atualizar, eliminar e criar zonas DNS no DNS do Azure.
services: dns
documentationcenter: na
author: vhorne
manager: timlt
ms.assetid: 8ab63bc4-5135-4ed8-8c0b-5f0712b9afed
ms.service: dns
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/27/2017
ms.author: victorh
ms.openlocfilehash: df741b34e1268c547723af87401760197d395780
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "61293834"
---
# <a name="how-to-manage-dns-zones-in-azure-dns-using-the-azure-cli"></a>Como gerir zonas DNS no DNS do Azure com a CLI do Azure

> [!div class="op_single_selector"]
> * [Portal](dns-operations-dnszones-portal.md)
> * [PowerShell](dns-operations-dnszones.md)
> * [CLI do Azure](dns-operations-dnszones-cli.md)


Este guia mostra como gerir as suas zonas DNS ao utilizar a CLI do Azure para várias plataformas, que está disponível para Windows, Mac e Linux. Também pode gerir as suas zonas DNS usando [do Azure PowerShell](dns-operations-dnszones.md) ou o portal do Azure.

Este guia especificamente lida com zonas de DNS público. Para obter informações sobre como utilizar a CLI do Azure para gerir as zonas privadas no DNS do Azure, consulte [comece com as zonas privadas do DNS do Azure com a CLI do Azure](private-dns-getstarted-cli.md).

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

Escolha qual das subscrições do Azure utilizar.

```azurecli
az account set --subscription "subscription name"
```

### <a name="optional-to-installuse-azure-dns-private-zones-feature-public-preview"></a>Opcional: Para instalar/utilizar a funcionalidade Zonas Privadas do DNS do Azure (Pré-visualização Pública)
A funcionalidade de Zona Privada do DNS do Azure foi lançada em Pré-visualização Pública através de uma extensão para a CLI do Azure. Instalar a extensão “dns” da CLI do Azure 
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

Todos os comandos da CLI do Azure relacionados ao DNS do Azure, comece com `az network dns`. A ajuda está disponível para cada comando com o `--help` opção (krátká forma `-h`).  Por exemplo:

```azurecli
az network dns --help
az network dns zone --help
az network dns zone create --help
```

## <a name="create-a-dns-zone"></a>Criar uma zona DNS

Uma zona DNS é criada ao utilizar o comando `az network dns zone create`. Para obter ajuda, consulte `az network dns zone create -h`.

O exemplo seguinte cria uma zona DNS denominada *contoso.com* no grupo de recursos chamado *MyResourceGroup*:

```azurecli
az network dns zone create --resource-group MyResourceGroup --name contoso.com
```

### <a name="to-create-a-dns-zone-with-tags"></a>Para criar uma zona DNS com etiquetas

O exemplo seguinte mostra como criar uma zona DNS com duas [etiquetas do Azure Resource Manager](dns-zones-records.md#tags), *project = demo* e *env = test*, utilizando o `--tags` (parâmetro krátká forma `-t`):

```azurecli
az network dns zone create --resource-group MyResourceGroup --name contoso.com --tags "project=demo" "env=test"
```

## <a name="get-a-dns-zone"></a>Obter uma zona DNS

Para obter uma zona DNS, utilize `az network dns zone show`. Para obter ajuda, consulte `az network dns zone show --help`.

O exemplo seguinte devolve a zona DNS *contoso.com* e os respetivos dados associados do grupo de recursos *MyResourceGroup*. 

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

Este comando não atualiza qualquer um dos conjuntos de registos de DNS na zona (veja [Como gerir recursos DNS](dns-operations-recordsets-cli.md)). Só é utilizado para atualizar propriedades do recurso da própria zona. Estas propriedades estão atualmente limitadas para o [do Azure Resource Manager "etiquetas"](dns-zones-records.md#tags) para o recurso de zona.

O exemplo seguinte mostra como atualizar as etiquetas numa zona DNS. As etiquetas existentes são substituídas pelo valor especificado.

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

O exemplo seguinte mostra como eliminar a zona *contoso.com* do grupo de recursos *MyResourceGroup*.

```azurecli
az network dns zone delete --resource-group myresourcegroup --name contoso.com
```

## <a name="next-steps"></a>Passos Seguintes

Saiba como [gerir conjuntos de registos e registos](dns-getstarted-create-recordset-cli.md) na sua zona DNS.

Saiba como [delegar o domínio ao DNS do Azure](dns-domain-delegation.md).

