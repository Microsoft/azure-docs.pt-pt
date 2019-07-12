---
title: incluir ficheiro
description: incluir ficheiro
services: cdn
author: SyntaxC4
ms.service: azure-cdn
ms.topic: include
ms.date: 05/24/2018
ms.author: cfowler
ms.custom: include file
ms.openlocfilehash: 8aa6cb3f10b86a6821cd93190ecc2135508739cb
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/05/2019
ms.locfileid: "67593995"
---
## <a name="create-a-new-cdn-profile"></a>Criar um novo perfil da CDN

Um perfil da CDN é um contentor de pontos finais da CDN e especifica um escalão de preço.

1. No portal do Azure portal, na parte superior esquerda, selecione **Criar um recurso**. 
    
    O painel **Novo** é apresentado.
   
2. Selecione **Web + Móvel** e, em seguida, selecione **CDN**.
   
    ![Selecione o recurso CDN](./media/cdn-create-profile/cdn-new-resource.png)

    É apresentado o painel do **perfil da CDN**.

3. Nas definições do perfil da CDN, utilize os valores especificados na tabela seguinte:
   
    | Definição  | Valor |
    | -------- | ----- |
    | **Name** | Introduza *my-cdn-perfil-123* como nome do perfil. Este nome deve ser globalmente exclusivo; se já estiver a ser utilizado, poderá introduzir um diferente. |
    | **Subscrição** | Selecione uma subscrição do Azure na lista pendente. |
    | **Grupo de recursos** | Selecione **Criar novo** e introduza *my-resource-group-123* como nome do grupo de recursos. Se já estiver a ser utilizado, poderá introduzir um diferente ou selecionar **Utilizar existente** e selecionar **my-resource-group-123** na lista pendente. | 
    | **Localização do grupo de recursos** | Selecione **EUA Central** na lista pendente. |
    | **Escalão de preço** | Selecione **Standard da Verizon** na lista pendente. |
    | **Agora, crie um novo ponto final da CDN** | Deixe por selecionar. |  
   
    ![Novo perfil da CDN](./media/cdn-create-profile/cdn-new-profile.png)

4. Selecione **Afixar ao dashboard** para guardar o perfil ao seu dashboard depois de criado.
    
5. Selecione **Criar** para criar o perfil. 

    Apenas para os perfis **CDN do Azure Standard da Microsoft**, a conclusão do perfil demora normalmente duas horas. 

