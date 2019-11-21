---
title: Manage DNS zones in Azure DNS - Azure portal | Microsoft Docs
description: You can manage DNS zones using the Azure portal. This article describes how to update, delete and create DNS zones on Azure DNS
services: dns
documentationcenter: na
author: asudbring
manager: timlt
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/18/2017
ms.author: allensu
ms.openlocfilehash: 5d4cc57c4cb5db7f04d604c8ccbc408df1a3e707
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74211900"
---
# <a name="how-to-manage-dns-zones-in-the-azure-portal"></a>How to manage DNS Zones in the Azure portal

> [!div class="op_single_selector"]
> * [Portal](dns-operations-dnszones-portal.md)
> * [PowerShell](dns-operations-dnszones.md)
> * [CLI clássica do Azure](dns-operations-dnszones-cli-nodejs.md)
> * [CLI do Azure](dns-operations-dnszones-cli.md)

This article shows you how to manage your DNS zones by using the Azure portal. You can also manage your DNS zones using the cross-platform [Azure CLI](dns-operations-dnszones-cli.md) or the Azure [PowerShell](dns-operations-dnszones.md).

## <a name="create-a-dns-zone"></a>Criar uma zona DNS

1. Iniciar sessão no portal do Azure
2. On the Hub menu, navigate to **Create a resource > Networking > DNS zone** to open the **Create DNS zone** blade.

    ![Zona DNS](./media/dns-operations-dnszones-portal/openzone650.png)

4. No painel **Criar zona DNS**, introduza os valores seguinte e clique em **Criar**:


   | **Definição** | **Valor** | **Detalhes** |
   |---|---|---|
   |**Nome**|contoso.com|O nome da zona DNS|
   |**Subscrição**|[A sua subscrição]|Selecione uma subscrição para criar a zona DNS.|
   |**Grupo de recursos**|**Criar novo:** contosoDNSRG|Crie um grupo de recursos. O nome do grupo de recursos tem de ser exclusivo dentro da subscrição que selecionou. Para saber mais sobre grupos de recursos, veja o artigo de descrição geral do [Resource Manager](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fdns%2ftoc.json#resource-groups).|
   |**Localização**|Oeste dos E.U.A.||

> [!NOTE]
> Esta definição refere à localização do grupo de recursos e não tem qualquer impacto na zona DNS. A localização da zona DNS é sempre "global" e não está apresentada.

## <a name="list-dns-zones"></a>Listar zonas DNS

In the Azure portal, navigate to **More services** > **Networking** > **DNS zones**. Each DNS zone is its own resource, and information such as number of record-sets and name servers are viewable from this view. The column **NAME SERVERS** is not in the default view. To add it, click **Columns**, select **Name servers**, and then click **Done**.

![listing DNS zones](./media/dns-operations-dnszones-portal/listzones.png)

## <a name="delete-a-dns-zone"></a>Eliminar uma zona DNS

Navigate to a DNS zone in the portal. On the **DNS zone** blade, click **Delete zone**. You are then prompted to confirm you are wanting to delete the DNS zone. Deleting a DNS zone also deletes all records that are contained in the zone.

## <a name="next-steps"></a>Passos seguintes

Learn how to work with your DNS Zone and records by visiting [Get started with Azure DNS using the Azure portal](dns-getstarted-portal.md).