---
title: Gerir zonas de DNS em Azure DNS - Portal Azure Microsoft Docs
description: Pode gerir as zonas DNS utilizando o portal Azure. Este artigo descreve como atualizar, excluir e criar zonas DNS no Azure DNS
services: dns
documentationcenter: na
author: rohinkoul
ms.service: dns
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/18/2017
ms.author: rohink
ms.openlocfilehash: 51e17812e1116f1e625685d5a818c18e25cdb2da
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94965804"
---
# <a name="how-to-manage-dns-zones-in-the-azure-portal"></a>Como gerir as Zonas DNS no portal Azure

> [!div class="op_single_selector"]
> * [Portal](dns-operations-dnszones-portal.md)
> * [PowerShell](dns-operations-dnszones.md)
> * [CLI clássico de Azure](./dns-operations-dnszones-cli.md)
> * [CLI do Azure](dns-operations-dnszones-cli.md)

Este artigo mostra-lhe como gerir as suas zonas de DNS utilizando o portal Azure. Também pode gerir as suas zonas DNS utilizando a plataforma transversal [Azure CLI](dns-operations-dnszones-cli.md) ou a Azure [PowerShell](dns-operations-dnszones.md).

## <a name="create-a-dns-zone"></a>Criar uma zona DNS

1. Iniciar sessão no portal do Azure
2. No menu Hub, navegue para **criar um recurso > de rede > zona DE DNS** para abrir a lâmina da **zona De criar DNS.**

    ![Zona DNS](./media/dns-operations-dnszones-portal/openzone650.png)

4. No painel **Criar zona DNS**, introduza os valores seguinte e clique em **Criar**:


   | **Definição** | **Valor** | **Detalhes** |
   |---|---|---|
   |**Name**|contoso.com|O nome da zona DNS|
   |**Subscrição**|[A sua subscrição]|Selecione uma subscrição para criar a zona DNS.|
   |**Grupo de recursos**|**Criar novo:** contosoDNSRG|Crie um grupo de recursos. O nome do grupo de recursos tem de ser exclusivo dentro da subscrição que selecionou. Para saber mais sobre grupos de recursos, leia o artigo Visão geral do [Gestor de Recursos.](../azure-resource-manager/management/overview.md?toc=%2fazure%2fdns%2ftoc.json#resource-groups)|
   |**Localização**|E.U.A. Oeste||

> [!NOTE]
> Esta definição refere à localização do grupo de recursos e não tem qualquer impacto na zona DNS. A localização da zona DNS é sempre "global" e não está apresentada.

## <a name="list-dns-zones"></a>Listar zonas DNS

No portal Azure, navegue para **mais serviços**  >  **em**  >  **rede zonas DNS**. Cada zona DE DNS é o seu próprio recurso, e informações como o número de conjuntos de registos e servidores de nomes são visualizantes a partir desta vista. O **SERVIDOR NOME DA** Coluna NÃO está na vista predefinida. Para adicioná-lo, clique em **Colunas**, selecione **Servidores de Nome** e, em seguida, clique em **'Fazer'**

![listagem de zonas DNS](./media/dns-operations-dnszones-portal/listzones.png)

## <a name="delete-a-dns-zone"></a>Eliminar uma zona DNS

Navegue para uma zona DE DNS no portal. Na lâmina da **zona DNS,** clique na **zona de eliminar**. Em seguida, é solicitado que confirme que pretende eliminar a zona DNS. A eliminação de uma zona de DNS também elimina todos os registos que estão contidos na zona.

## <a name="next-steps"></a>Passos seguintes

Saiba como trabalhar com a sua Zona DNS e registos visitando [Começar com o Azure DNS utilizando o portal Azure](dns-getstarted-portal.md).