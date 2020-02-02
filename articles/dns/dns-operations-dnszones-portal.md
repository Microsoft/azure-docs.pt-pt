---
title: Gerenciar zonas DNS no DNS do Azure-portal do Azure | Microsoft Docs
description: Você pode gerenciar zonas DNS usando o portal do Azure. Este artigo descreve como atualizar, excluir e criar zonas DNS no DNS do Azure
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
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/01/2020
ms.locfileid: "76936771"
---
# <a name="how-to-manage-dns-zones-in-the-azure-portal"></a>Como gerenciar Zonas DNS no portal do Azure

> [!div class="op_single_selector"]
> * [Portal](dns-operations-dnszones-portal.md)
> * [PowerShell](dns-operations-dnszones.md)
> * [CLI clássica do Azure](dns-operations-dnszones-cli-nodejs.md)
> * [CLI do Azure](dns-operations-dnszones-cli.md)

Este artigo mostra como gerenciar suas zonas DNS usando o portal do Azure. Você também pode gerenciar suas zonas DNS usando o [CLI do Azure](dns-operations-dnszones-cli.md) de plataforma cruzada ou o Azure [PowerShell](dns-operations-dnszones.md).

## <a name="create-a-dns-zone"></a>Criar uma zona DNS

1. Iniciar sessão no portal do Azure
2. No menu Hub, navegue para **criar um recurso > rede > zona DNS** para abrir a folha **criar zona DNS** .

    ![Zona DNS](./media/dns-operations-dnszones-portal/openzone650.png)

4. No painel **Criar zona DNS**, introduza os valores seguinte e clique em **Criar**:


   | **Definição** | **Valor** | **Detalhes** |
   |---|---|---|
   |**Nome**|contoso.com|O nome da zona DNS|
   |**Subscrição**|[A sua subscrição]|Selecione uma subscrição para criar a zona DNS.|
   |**Grupo de recursos**|**Criar novo:** contosoDNSRG|Crie um grupo de recursos. O nome do grupo de recursos tem de ser exclusivo dentro da subscrição que selecionou. Para saber mais sobre grupos de recursos, veja o artigo de descrição geral do [Resource Manager](../azure-resource-manager/management/overview.md?toc=%2fazure%2fdns%2ftoc.json#resource-groups).|
   |**Localização**|E.U.A. Oeste||

> [!NOTE]
> Esta definição refere à localização do grupo de recursos e não tem qualquer impacto na zona DNS. A localização da zona DNS é sempre "global" e não está apresentada.

## <a name="list-dns-zones"></a>Listar zonas DNS

Na portal do Azure, navegue até **mais serviços** > **rede** > **zonas DNS**. Cada zona DNS é seu próprio recurso e informações como o número de conjuntos de registros e servidores de nomes são visíveis nessa exibição. Os **servidores de nome** de coluna não estão no modo de exibição padrão. Para adicioná-lo, clique em **colunas**, selecione **servidores de nomes**e clique em **concluído**.

![listando zonas DNS](./media/dns-operations-dnszones-portal/listzones.png)

## <a name="delete-a-dns-zone"></a>Eliminar uma zona DNS

Navegue até uma zona DNS no Portal. Na folha **zona DNS** , clique em **excluir zona**. Em seguida, você será solicitado a confirmar que está querendo excluir a zona DNS. Excluir uma zona DNS também exclui todos os registros contidos na zona.

## <a name="next-steps"></a>Passos seguintes

Saiba como trabalhar com a zona e os registros DNS visitando introdução [ao DNS do Azure usando o portal do Azure](dns-getstarted-portal.md).