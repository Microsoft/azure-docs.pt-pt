---
title: Gerir zonas DNS em Azure DNS - Portal Azure [ Microsoft Docs
description: Pode gerir zonas DNS utilizando o portal Azure. Este artigo descreve como atualizar, excluir e criar zonas de DNS em DNS Azure
services: dns
documentationcenter: na
author: rohinkoul
manager: timlt
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/18/2017
ms.author: rohink
ms.openlocfilehash: 002f210048c18c6dd99dfb5981bacce8666ee563
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76936771"
---
# <a name="how-to-manage-dns-zones-in-the-azure-portal"></a>Como gerir zonas DNS no portal Azure

> [!div class="op_single_selector"]
> * [Portal](dns-operations-dnszones-portal.md)
> * [PowerShell](dns-operations-dnszones.md)
> * [ClI clássico azure](dns-operations-dnszones-cli-nodejs.md)
> * [Azure CLI](dns-operations-dnszones-cli.md)

Este artigo mostra-lhe como gerir as suas zonas DeNs utilizando o portal Azure. Também pode gerir as suas zonas DNS utilizando o [Azure CLI](dns-operations-dnszones-cli.md) de plataforma cruzada ou o Azure [PowerShell](dns-operations-dnszones.md).

## <a name="create-a-dns-zone"></a>Criar uma zona DNS

1. Iniciar sessão no portal do Azure
2. No menu Hub, navegue para **Criar um recurso > zona DNS** de rede > para abrir a lâmina de **zona DNS Create.**

    ![Zona DNS](./media/dns-operations-dnszones-portal/openzone650.png)

4. No painel **Criar zona DNS**, introduza os valores seguinte e clique em **Criar**:


   | **Definição** | **Valor** | **Detalhes** |
   |---|---|---|
   |**Nome**|contoso.com|O nome da zona DNS|
   |**Assinatura**|[A sua subscrição]|Selecione uma subscrição para criar a zona DNS.|
   |**Grupo de recursos**|**Criar novo:** contosoDNSRG|Crie um grupo de recursos. O nome do grupo de recursos tem de ser exclusivo dentro da subscrição que selecionou. Para saber mais sobre grupos de recursos, leia o artigo de visão geral [do Gestor de Recursos.](../azure-resource-manager/management/overview.md?toc=%2fazure%2fdns%2ftoc.json#resource-groups)|
   |**Localização**|E.U.A. Oeste||

> [!NOTE]
> Esta definição refere à localização do grupo de recursos e não tem qualquer impacto na zona DNS. A localização da zona DNS é sempre "global" e não está apresentada.

## <a name="list-dns-zones"></a>Listar zonas DNS

No portal Azure, navegue para **mais serviços** > zonas**DNS****em rede.** >  Cada zona DNS é um recurso próprio, e informações como o número de conjuntos de registos e servidores de nomes são visualizados a partir desta vista. A coluna **NAME SERVERS** não está na vista predefinida. Para adicioná-lo, clique em **Colunas,** selecione **servidores de nomes,** e, em seguida, clique **em Done**.

![listando zonas de DNS](./media/dns-operations-dnszones-portal/listzones.png)

## <a name="delete-a-dns-zone"></a>Eliminar uma zona DNS

Navegue para uma zona DNS no portal. Na lâmina da **zona DNS,** clique na **zona Deeliminar**. Em seguida, é-lhe pedido que confirme que pretende eliminar a zona DNS. A eliminação de uma zona DNS também elimina todos os registos contidos na zona.

## <a name="next-steps"></a>Passos seguintes

Saiba como trabalhar com a sua Zona DNS e registos visitando [O Get começou com o Azure DNS utilizando o portal Azure](dns-getstarted-portal.md).