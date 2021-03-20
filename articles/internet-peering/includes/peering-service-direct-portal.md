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
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "81687056"
---
1. Selecione uma ligação de espreitamento que deseja ativar para o Serviço de Peering Azure. Em seguida, selecione **...**  >  **Editar a ligação**.
    > [!div class="mx-imgBorder"]
    > ![Ligação de espreitamento Ligação Editar](../media/setup-direct-modify-editconnection.png)
1. Em **Utilização para Serviço de Observação,** selecione **Ativado** e, em seguida, selecione **Guardar**.
    > [!div class="mx-imgBorder"]
    > ![Conexão de perso ativar o serviço de espreitamento](../media/setup-direct-modify-editconnectionsettings-peering-service.png)
1. No ecrã **geral,** vê os detalhes da implementação. Depois de terminar a sua implementação, selecione **Ir para o recurso**.
    > [!div class="mx-imgBorder"]
    > ![A sua implantação está completa](../media/setup-direct-modify-overview-deployment-complete.png)

1. No painel **de prefixos registados,** selecione **Adicionar prefixo registado**.
    > [!div class="mx-imgBorder"]
    > ![Adicionar prefixo registado](../media/setup-direct-modify-add-registered-prefix.png)
1. Registe um prefixo selecionando um **Nome** e um **Prefixo** e selecionando **Save**.
    > [!div class="mx-imgBorder"]
    >  ![Registar um prefixo](../media/setup-direct-modify-register-a-prefix.png) 

1. Depois de criado um prefixo, vê-o na lista de **Prefixos Registados.** Selecione o **Nome** do prefixo para ver mais detalhes.
    > [!div class="mx-imgBorder"]
    > ![Prefixos e ligações registados](../media/setup-direct-modify-registered-prefixes.png)
1. Na página de prefixo registada, vê todos os detalhes, que incluem a **chave prefixo** para cada prefixo. Esta chave deve ser fornecida ao cliente que alocou este prefixo ao seu fornecedor ISP. O cliente pode então registar o seu prefixo dentro da sua subscrição utilizando esta chave.
    > [!div class="mx-imgBorder"]
    > ![Prefixo com chave de prefixo](../media/setup-direct-modify-registered-prefix-detail.png)