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
ms.openlocfilehash: 60752cf1b3c05ab7817083e70310ba7b40227dec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80129869"
---
1. Clique numa ligação de pares que pretende ativar para o Serviço de Peering e, em seguida, clique no **...**  > Editar botão de **ligação.**
    > [!div class="mx-imgBorder"]
    > ![Edição de ligação de pares](../media/setup-direct-modify-editconnection.png)
1. Na secção ***Utilização para o Serviço de Peering,*** clique em **Ativado** e **Salvar**.
    > [!div class="mx-imgBorder"]
    > ![Peering Connection Enable Peering Service](../media/setup-direct-modify-editconnectionsettings-peering-service.png)
1. No ecrã Overview, verá os detalhes da implementação. Assim que a sua implementação estiver concluída, clique em **Ir para o recurso**.
    > [!div class="mx-imgBorder"]
    > ![A sua implantação está completa](../media/setup-direct-modify-overview-deployment-complete.png)

1. Em seguida, verá em Definições **Prefixos Registados**. Clique em **Adicionar Prefixo Registado**.
    > [!div class="mx-imgBorder"]
    > ![Prefixos e Ligações Registados](../media/setup-direct-modify-add-registered-prefix.png)
1. Registe um prefixo selecionando um **Nome** e um **Prefixo** e clique em **Guardar**
    > [!div class="mx-imgBorder"]
    >  ![Registar um Prefixo](../media/setup-direct-modify-register-a-prefix.png) 

1. Uma vez criado um prefixo, irá vê-lo na lista de Prefixos Registados. Clique no **nome** do prefixo para ver mais detalhes.
    > [!div class="mx-imgBorder"]
    > ![Prefixos e Ligações Registados](../media/setup-direct-modify-registered-prefixes.png)
1. Na página de prefixo registada, verá todos os detalhes para incluir a **tecla Prefix** para cada prefixo. Esta chave terá de ser fornecida ao cliente atribuído este prefixo ao seu fornecedor ISP. O cliente pode então registar o seu prefixo dentro da sua subscrição com esta chave.
    > [!div class="mx-imgBorder"]
    > ![Prefixo com tecla prefixo](../media/setup-direct-modify-registered-prefix-detail.png)