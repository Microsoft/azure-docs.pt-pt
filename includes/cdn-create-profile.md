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
ms.openlocfilehash: d73a8d743a948cbd94a3af81fe2e77c45c0eeb67
ms.sourcegitcommit: 309a9d26f94ab775673fd4c9a0ffc6caa571f598
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/09/2020
ms.locfileid: "82996221"
---
## <a name="create-a-new-cdn-profile"></a>Criar um novo perfil da CDN

Um perfil da CDN é um contentor de pontos finais da CDN e especifica um escalão de preço.

1. No portal Azure, selecione **Criar um recurso** (na parte superior esquerda). O painel **Novo** é apresentado.
   
1. Procure e selecione **CDN,** então selecione **Criar:**
   
    ![Selecione o recurso CDN](./media/cdn-create-profile/cdn-new-resource.png)

    O painel de **perfil da CDN** aparece.

1. Introduza os seguintes valores:
   
    | Definição  | Valor |
    | -------- | ----- |
    | **Nome** | Introduza *o perfil cdn-123* para o seu nome de perfil. Este nome deve ser globalmente único; se já está em uso, insira um nome diferente. |
    | **Subscrição** | Selecione uma subscrição do Azure na lista pendente. |
    | **Grupo de recursos** | Selecione **Criar novo** e introduza *cDNQuickstart-rg* para o nome do seu grupo de recursos, ou selecione **Use o existente** e escolha *CDNQuickstart-rg* se já tiver o grupo. | 
    | **Localização do grupo de recursos** | Selecione um local perto de si da lista de lançamentos. |
    | **Nível de preços** | Selecione uma opção **Standard Akamai** na lista de drop-down. (O tempo de implantação para o nível Akamai é de cerca de um minuto. O nível da Microsoft demora cerca de 10 minutos e os níveis Verizon demoram cerca de 90 minutos.) |
    | **Agora, crie um novo ponto final da CDN** | Deixe por selecionar. |  
   
    ![Novo perfil da CDN](./media/cdn-create-profile/cdn-new-profile.png)

1. Selecione **Criar** para criar o perfil.

