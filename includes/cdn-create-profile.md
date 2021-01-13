---
title: incluir ficheiro
description: incluir ficheiro
services: cdn
author: SyntaxC4
ms.service: azure-cdn
ms.topic: include
ms.date: 04/30/2020
ms.author: cfowler
ms.custom: include file
ms.openlocfilehash: c6352ee9d29e4e45aa4be449046a0715fee06047
ms.sourcegitcommit: 16887168729120399e6ffb6f53a92fde17889451
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/13/2021
ms.locfileid: "98165914"
---
## <a name="create-a-new-cdn-profile"></a>Criar um novo perfil da CDN

Um perfil da CDN é um contentor de pontos finais da CDN e especifica um escalão de preço.

1. No portal Azure, selecione **Criar um recurso** (na parte superior esquerda). O painel **Novo** é apresentado.
   
1. Procure e selecione **CDN,** em seguida, **selecione Criar**:
   
    ![Selecione o recurso CDN](./media/cdn-create-profile/cdn-new-resource.png)

    Aparece o painel de **perfil cdn.**

1. Introduza os seguintes valores:
   
    | Definição  | Valor |
    | -------- | ----- |
    | **Nome** | *Insira o perfil do CDN-123* para o seu nome de perfil. Este nome deve ser globalmente único; se já estiver em uso, insira um nome diferente. |
    | **Subscrição** | Selecione uma subscrição do Azure na lista pendente. |
    | **Grupo de recursos** | Selecione **Criar novo** e introduza *CDNQuickstart-rg* para o nome do seu grupo de recursos, ou selecione **Use existente** e escolha *CDNQuickstart-rg* se já tiver o grupo. | 
    | **Localização do grupo de recursos** | Selecione uma localização perto de si na lista de drop-down. |
    | **Escalão de preço** | Selecione uma opção **Standard Akamai** da lista de drop-down. (O tempo de implantação para o nível Akamai é de cerca de um minuto. O nível da Microsoft demora cerca de 10 minutos e os níveis de Verizon demoram cerca de 30 minutos.) |
    | **Agora, crie um novo ponto final da CDN** | Deixe por selecionar. |  
   
    ![Novo perfil da CDN](./media/cdn-create-profile/cdn-new-profile.png)

1. Selecione **Criar** para criar o perfil.

