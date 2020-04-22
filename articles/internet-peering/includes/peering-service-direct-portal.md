---
title: incluir ficheiro
titleSuffix: Azure
description: incluir ficheiro
services: internet-peering
author: derekolo
ms.service: internet-peering
ms.topic: include
ms.date: 3/18/2020
ms.author: derekol
ms.openlocfilehash: e5804aa1b005e670d8b430b1c0a3bd62efd0bb06
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687056"
---
1. Selecione uma ligação de pares que deseja ativar para o Serviço de Peering Azure. Em seguida, selecione **...**  >  **Editar ligação**.
    > [!div class="mx-imgBorder"]
    > ![Ligação de pares Ligação Editar](../media/setup-direct-modify-editconnection.png)
1. Sob **a utilização do serviço de peering,** selecione **Ativado** e, em seguida, selecione **Save**.
    > [!div class="mx-imgBorder"]
    > ![Ligação de pares Enable Peering Service](../media/setup-direct-modify-editconnectionsettings-peering-service.png)
1. No ecrã **overview,** vê os detalhes da implementação. Depois de terminar a sua implementação, selecione **Ir para o recurso**.
    > [!div class="mx-imgBorder"]
    > ![A sua implementação está completa](../media/setup-direct-modify-overview-deployment-complete.png)

1. No painel **prefixos registados,** **selecione Adicionar prefixo registado**.
    > [!div class="mx-imgBorder"]
    > ![Adicionar prefixo registado](../media/setup-direct-modify-add-registered-prefix.png)
1. Registe um prefixo selecionando um **Nome** e um **Prefixo** e selecionando **Save**.
    > [!div class="mx-imgBorder"]
    >  ![Registar um prefixo](../media/setup-direct-modify-register-a-prefix.png) 

1. Depois de criado um prefixo, vê-lo na lista de **Prefixos Registados**. Selecione o **nome** do prefixo para ver mais detalhes.
    > [!div class="mx-imgBorder"]
    > ![Prefixos e ligações registados](../media/setup-direct-modify-registered-prefixes.png)
1. Na página de prefixo registada, você vê todos os detalhes, que incluem a **chave Prefix** para cada prefixo. Esta chave deve ser fornecida ao cliente atribuído este prefixo ao seu fornecedor ISP. O cliente pode então registar o seu prefixo dentro da sua subscrição utilizando esta chave.
    > [!div class="mx-imgBorder"]
    > ![Prefixo com tecla prefixo](../media/setup-direct-modify-registered-prefix-detail.png)