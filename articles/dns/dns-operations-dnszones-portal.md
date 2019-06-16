---
title: Gerir zonas DNS no DNS do Azure - portal do Azure | Documentos da Microsoft
description: Pode gerir zonas DNS com o portal do Azure. Este artigo descreve como atualizar, excluir e criar zonas DNS no DNS do Azure
services: dns
documentationcenter: na
author: vhorne
manager: timlt
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/18/2017
ms.author: victorh
ms.openlocfilehash: d0a20de8738e8c7b2719a9de85d5fd16aa5778cf
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60926343"
---
# <a name="how-to-manage-dns-zones-in-the-azure-portal"></a>Como gerir zonas DNS no portal do Azure

> [!div class="op_single_selector"]
> * [Portal](dns-operations-dnszones-portal.md)
> * [PowerShell](dns-operations-dnszones.md)
> * [CLI clássica do Azure](dns-operations-dnszones-cli-nodejs.md)
> * [CLI do Azure](dns-operations-dnszones-cli.md)

Este artigo mostra-lhe como gerir as suas zonas DNS com o portal do Azure. Também pode gerir as suas zonas DNS com a Multiplataforma [CLI do Azure](dns-operations-dnszones-cli.md) ou do Azure [PowerShell](dns-operations-dnszones.md).

## <a name="create-a-dns-zone"></a>Criar uma zona DNS

1. Iniciar sessão no portal do Azure
2. No Hub menu, navegue para **criar um recurso > redes > zona DNS** para abrir o **criar zona DNS** painel.

    ![Zona DNS](./media/dns-operations-dnszones-portal/openzone650.png)

4. No painel **Criar zona DNS**, introduza os valores seguinte e clique em **Criar**:


   | **Definição** | **Valor** | **Detalhes** |
   |---|---|---|
   |**Nome**|contoso.com|O nome da zona DNS|
   |**Subscrição**|[A sua subscrição]|Selecione uma subscrição para criar a zona DNS.|
   |**Grupo de recursos**|**Criar novo:** contosoDNSRG|Crie um grupo de recursos. O nome do grupo de recursos tem de ser exclusivo dentro da subscrição que selecionou. Para saber mais sobre grupos de recursos, veja o artigo de descrição geral do [Resource Manager](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fdns%2ftoc.json#resource-groups).|
   |**Location**|EUA Oeste||

> [!NOTE]
> Esta definição refere à localização do grupo de recursos e não tem qualquer impacto na zona DNS. A localização da zona DNS é sempre "global" e não está apresentada.

## <a name="list-dns-zones"></a>Listar zonas DNS

No portal do Azure, navegue até **mais serviços** > **rede** > **zonas DNS**. Cada zona DNS é o seu próprio de recursos e informações como o número de conjuntos de registos e servidores de nomes são visíveis a partir desta vista. A coluna **servidores de nomes** não está no modo de exibição padrão. Para adicioná-lo, clique em **colunas**, selecione **servidores de nomes**e, em seguida, clique em **feito**.

![listar zonas DNS](./media/dns-operations-dnszones-portal/listzones.png)

## <a name="delete-a-dns-zone"></a>Eliminar uma zona DNS

Navegue para uma zona DNS no portal. Sobre o **zona DNS** painel, clique em **eliminar zona**. Em seguida, é-lhe pedido para confirmar que pretende eliminar a zona DNS. Eliminar uma zona DNS também elimina a todos os registos que estão contidos na zona.

## <a name="next-steps"></a>Passos Seguintes

Saiba como trabalhar com a sua zona DNS e registos ao visitar [introdução ao DNS do Azure com o portal do Azure](dns-getstarted-portal.md).