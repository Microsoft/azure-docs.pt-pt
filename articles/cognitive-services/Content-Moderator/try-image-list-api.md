---
title: Imagens moderadas com listas personalizadas e o console de API-Content Moderator
titleSuffix: Azure Content Moderator
description: Você usa a API de gerenciamento de lista no Azure Content Moderator para criar listas personalizadas de imagens.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: pafarley
ms.openlocfilehash: 27d9b12d9e1a0237050243c2b5f07edaa8d8857a
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/22/2019
ms.locfileid: "72757198"
---
# <a name="moderate-with-custom-image-lists-in-the-api-console"></a>Moderado com listas de imagens personalizadas no console de API

Você usa a [API de gerenciamento de lista](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f672) no Azure Content moderator para criar listas personalizadas de imagens. Use as listas personalizadas de imagens com a API de moderação de imagem. A operação de moderação de imagem avalia sua imagem. Se você criar listas personalizadas, a operação também a compara com as imagens em suas listas personalizadas. Você pode usar listas personalizadas para bloquear ou permitir a imagem.

> [!NOTE]
> Existe um limite máximo de **5 listas de imagens**, sendo que cada lista **não pode exceder 10 000 imagens**.
>

Você usa a API de gerenciamento de lista para realizar as seguintes tarefas:

- Criar uma lista.
- Adicionar imagens a uma lista.
- Imagens de tela em relação às imagens em uma lista.
- Excluir imagens de uma lista.
- Eliminar uma lista.
- Editar informações da lista.
- Atualizar o índice para que as alterações à lista sejam incluídas numa nova análise.

## <a name="use-the-api-console"></a>Usar o console de API
Antes de poder testar a API no console online, você precisa da sua chave de assinatura. Isso está localizado na guia **configurações** , na caixa **OCP-APIM-Subscription-Key** . Para obter mais informações, veja [Descrição geral](overview.md).

## <a name="refresh-search-index"></a>Atualizar índice de pesquisa

Depois de fazer alterações em uma lista de imagens, você deve atualizar seu índice para que as alterações sejam incluídas nas verificações futuras. Esta etapa é semelhante a como um mecanismo de pesquisa em sua área de trabalho (se habilitado) ou um mecanismo de pesquisa da Web atualiza continuamente seu índice para incluir novos arquivos ou páginas.

1. Na [referência da API de gerenciamento da lista de imagens](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f672), no menu à esquerda, selecione listas de **imagens**e, em seguida, selecione **Atualizar índice de pesquisa**.

   A página **listas de imagens – atualizar índice de pesquisa** é aberta.

2. Para **abrir o console de teste de API**, selecione a região que melhor descreve seu local. 
 
    ![Listas de imagens – seleção atualizar região da página de índice de pesquisa](images/test-drive-region.png)

    O console **lista de imagens – atualizar pesquisa de API de índice** é aberto.

3. Na caixa **listar** , insira a ID da lista. Insira sua chave de assinatura e, em seguida, selecione **Enviar**.

   ![Listas de imagens – atualizar caixa de conteúdo de resposta do console de índice de pesquisa](images/try-image-list-refresh-1.png)


## <a name="create-an-image-list"></a>Criar uma lista de imagens

1. Vá para a [referência da API de gerenciamento da lista de imagens](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f672).

   A página **listas de imagens-criar** é aberta. 

3. Para **abrir o console de teste de API**, selecione a região que melhor descreve seu local.

   ![Listas de imagens – criar seleção de região de página](images/test-drive-region.png)

   O console **listas de imagens-criar** API é aberto.
 
4. Na caixa **OCP-APIM-Subscription-Key** , insira sua chave de assinatura.

5. Na caixa **corpo da solicitação** , insira valores para **nome** (por exemplo, myList) e **Descrição**.

   ![Listas de imagens-criar nome e descrição do corpo da solicitação do console](images/try-terms-list-create-1.png)

6. Use espaços reservados de pares chave-valor para atribuir metadados mais descritivos à sua lista.

       {
          "Name": "MyExclusionList",
          "Description": "MyListDescription",
          "Metadata": 
          {
            "Category": "Competitors",
            "Type": "Exclude"
          }
       }

   Adicione metadados da lista como pares de chave-valor e não as imagens reais.
 
7. Selecione **Enviar**. Sua lista é criada. Observe o valor de **ID** associado à nova lista. Você precisa dessa ID para outras funções de gerenciamento de lista de imagens.

   ![Listas de imagens – a caixa criar conteúdo de resposta do console mostra a ID da lista](images/try-terms-list-create-2.png)
 
8. Em seguida, adicione imagens a myList. No menu à esquerda, selecione **imagem**e, em seguida, selecione **Adicionar imagem**.

   A página **imagem-adicionar imagem** é aberta. 

9. Para **abrir o console de teste de API**, selecione a região que melhor descreve seu local.

   ![Imagem – adicionar seleção de região de página de imagem](images/test-drive-region.png)

   O console **Image-Add Image** API é aberto.
 
10. Na caixa **listar** , insira a ID da lista que você gerou e, em seguida, insira a URL da imagem que você deseja adicionar. Insira sua chave de assinatura e, em seguida, selecione **Enviar**.

11. Para verificar se a imagem foi adicionada à lista, no menu à esquerda, selecione **imagem**e, em seguida, selecione **obter todas as IDs de imagem**.

    A **imagem-obter todas as IDs de imagem console de** API é aberta.
  
12. Na caixa **listar** , insira a ID da lista e, em seguida, insira sua chave de assinatura. Selecione **Enviar**.

    ![Imagem – caixa de conteúdo do console obter todas as IDs de imagem lista as imagens que você inseriu](images/try-image-list-create-11.png)
 
10. Adicione mais algumas imagens. Agora que você criou uma lista de imagens Personalizada, tente [avaliar as imagens](try-image-api.md) usando a lista de imagens personalizadas. 

## <a name="delete-images-and-lists"></a>Excluir imagens e listas

A exclusão de uma imagem ou de uma lista é simples. Você pode usar a API para realizar as seguintes tarefas:

- Elimine uma imagem. (**Imagem-excluir**)
- Exclua todas as imagens em uma lista sem excluir a lista. (**Imagem-excluir todas as imagens**)
- Eliminar uma lista e todo o conteúdo da mesma. (**Listas de imagens-excluir**)

Este exemplo exclui uma única imagem:

1. Na [referência da API de gerenciamento da lista de imagens](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f672), no menu à esquerda, selecione **imagem**e, em seguida, selecione **excluir**. 

   A página **imagem-exclusão** é aberta.

2. Para **abrir o console de teste de API**, selecione a região que melhor descreve seu local. 

   ![Imagem-Excluir seleção de região de página](images/test-drive-region.png)
 
   O console de API de **exclusão de imagem** é aberto.
 
3. Na caixa **listar** , insira a ID da lista da qual excluir uma imagem.  Este é o número retornado no console **Image-obter todas as IDs de imagem** para myList. Em seguida, insira a **imageid** da imagem a ser excluída. 

Em nosso exemplo, a ID da lista é **58953**, o valor de **ContentSource**. A ID da imagem é **59021**, o valor de **ContentIds**.

1. Insira sua chave de assinatura e, em seguida, selecione **Enviar**.

1. Para verificar se a imagem foi excluída, use o console **Image-obter todas as IDs de imagem** .
 
## <a name="change-list-information"></a>Alterar informações da lista

Você pode editar o nome e a descrição de uma lista e adicionar itens de metadados.

1. Na [referência da API de gerenciamento da lista de imagens](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f672), no menu à esquerda, selecione listas de **imagens**e, em seguida, selecione **atualizar detalhes**. 

   A página **listas de imagens – detalhes da atualização** é aberta.

2. Para **abrir o console de teste de API**, selecione a região que melhor descreve seu local.  

    ![Listas de imagens-seleção de região de página de detalhes de atualização](images/test-drive-region.png)

    O console de API **listas de imagens – atualizar detalhes** é aberto.
 
3. Na caixa **listar** , insira a ID da lista e, em seguida, insira sua chave de assinatura.

4. Na caixa **corpo da solicitação** , faça suas edições e, em seguida, selecione o botão **Enviar** na página.

   ![Listas de imagens-edições de corpo de solicitação do console de detalhes de atualização](images/try-terms-list-change-1.png)
 

## <a name="next-steps"></a>Passos seguintes

Use a API REST em seu código ou comece com a [imagem lista o início rápido do .net](image-lists-quickstart-dotnet.md) para integrar com seu aplicativo.
