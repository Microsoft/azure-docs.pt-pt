---
title: Imagens moderadas com listas personalizadas e a consola de API - Content Moderator
titlesuffix: Azure Content Moderator
description: Utilize a API de gestão de lista no Azure Content Moderator para criar listas personalizadas de imagens.
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: 7efa5114a903ba88010ec44f2f1038331df62948
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58075702"
---
# <a name="moderate-with-custom-image-lists-in-the-api-console"></a>Moderar com listas de imagens personalizadas na consola de API

Utilizar o [API de gestão da lista](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f672) no Azure Content Moderator para criar listas personalizadas de imagens. Utilize as listas personalizadas de imagens com a API de moderação de imagens. A operação de moderação de imagem avalia a sua imagem. Se criar listas personalizadas, a operação também compara-as com as imagens em suas listas personalizadas. Pode utilizar listas personalizadas para bloquear ou permitir a imagem.

> [!NOTE]
> Existe um limite máximo de **5 listas de imagens**, sendo que cada lista **não pode exceder 10 000 imagens**.
>

Utilize a API de gestão da lista para efetuar as seguintes tarefas:

- Criar uma lista.
- Adicione imagens a uma lista.
- Imagens de ecrã em relação as imagens numa lista.
- Elimine imagens a partir de uma lista.
- Eliminar uma lista.
- Editar informações da lista.
- Atualizar o índice para que as alterações à lista sejam incluídas numa nova análise.

## <a name="use-the-api-console"></a>Utilizar a consola de API
Antes de pode testar a API na consola do online, terá a chave de subscrição. Isto está localizado no **configurações** separador a **Ocp-Apim-Subscription-Key** caixa. Para obter mais informações, veja [Descrição geral](overview.md).

## <a name="refresh-search-index"></a>Atualizar o índice de pesquisa

Depois de efetuar alterações a uma lista de imagem, tem de atualizar o seu índice para que as alterações a serem incluídos em verificações futuras. Este passo é semelhante a como um mecanismo de pesquisa no ambiente de trabalho (se ativada) ou um mecanismo de pesquisa web continuamente atualiza seu índice para incluir novos ficheiros ou páginas.

1. Na [referência de API de gestão da lista de imagens](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f672), no menu da esquerda, selecione **apresenta uma lista de imagem**e, em seguida, selecione **atualizar o índice de pesquisa**.

   O **lista de imagem - atualizar o índice de pesquisa** é aberta a página.

2. Para **consola de teste de API aberta**, selecione a região que melhor descreve sua localização. 
 
    ![Listas de imagens - seleção de região de página de índice de pesquisa de atualização](images/test-drive-region.png)

    O **lista de imagem - atualizar o índice de pesquisa** é aberta a consola de API.

3. Na **listId** , introduza o ID da lista. Introduza a chave de subscrição e, em seguida, selecione **enviar**.

   ![Listas de imagens - caixa de conteúdo de resposta do índice de pesquisa de atualização de consola](images/try-image-list-refresh-1.png)


## <a name="create-an-image-list"></a>Criar uma lista de imagens

1. Vá para o [referência da API de gestão de lista de imagem](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f672).

   O **apresenta uma lista de imagem - criar** é aberta a página. 

3. Para **consola de teste de API aberta**, selecione a região que melhor descreve sua localização.

   ![Listas de imagem - criar a seleção de região de página](images/test-drive-region.png)

   O **apresenta uma lista de imagem - criar** é aberta a consola de API.
 
4. Na **Ocp-Apim-Subscription-Key** , introduza a chave de subscrição.

5. Na **corpo do pedido** , introduza valores para **nome** (por exemplo, MyList) e **Descrição**.

   ![Listas de imagem - criar o nome de corpo de pedido de consola e uma descrição](images/try-terms-list-create-1.png)

6. Utilize marcadores de posição de par chave-valor para atribuir mais descritivo metadados à sua lista.

       {
          "Name": "MyExclusionList",
          "Description": "MyListDescription",
          "Metadata": 
          {
            "Category": "Competitors",
            "Type": "Exclude"
          }
       }

   Adicione metadados de lista como pares chave-valor e não as imagens reais.
 
7. Selecione **Enviar**. A lista é criada. Tenha em atenção a **ID** valor que estão associado com a nova lista. Vai precisar deste ID para outras funções de gestão da lista de imagem.

   ![Listas de imagem - criar consola resposta conteúdo caixa mostra o ID de lista](images/try-terms-list-create-2.png)
 
8. Em seguida, adicione imagens ao MyList. No menu da esquerda, selecione **imagem**e, em seguida, selecione **adicionar imagem**.

   O **imagem - adicionar imagem** é aberta a página. 

9. Para **consola de teste de API aberta**, selecione a região que melhor descreve sua localização.

   ![Imagem - adicionar seleção de região da página de imagem](images/test-drive-region.png)

   O **imagem - adicionar imagem** é aberta a consola de API.
 
10. Na **listId** caixa, introduza o ID de lista que gerou e, em seguida, introduza o URL da imagem que pretende adicionar. Introduza a chave de subscrição e, em seguida, selecione **enviar**.

11. Para verificar que a imagem foi adicionada à lista, no menu à esquerda, selecione **imagem**e, em seguida, selecione **obter todos os Ids de imagem**.

    O **imagem - obter todos os Ids de imagem** é aberta a consola de API.
  
12. Na **listId** caixa, introduza o ID de lista e, em seguida, introduza a chave de subscrição. Selecione **Enviar**.

    ![Imagem - Get todos os Ids de imagem consola as imagens que introduziu apresenta uma lista de caixa de conteúdo de resposta](images/try-image-list-create-11.png)
 
10. Adicione algumas imagens mais. Agora que criou uma lista personalizada de imagens, experimente [avaliar imagens](try-image-api.md) , utilizando a lista de imagem personalizada. 

## <a name="delete-images-and-lists"></a>Eliminar imagens e listas

A eliminação de uma imagem ou uma lista é simples. Pode usar a API para realizar as seguintes tarefas:

- Elimine uma imagem. (**Imagem – eliminar**)
- Elimine todas as imagens numa lista sem a eliminação da lista. (**Imagem – eliminar todas as imagens**)
- Eliminar uma lista e todo o conteúdo da mesma. (**Listas de imagens - Delete**)

Este exemplo elimina uma única imagem:

1. Na [referência de API de gestão da lista de imagens](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f672), no menu da esquerda, selecione **imagem**e, em seguida, selecione **eliminar**. 

   O **imagem – eliminar** é aberta a página.

2. Para **consola de teste de API aberta**, selecione a região que melhor descreve sua localização. 

   ![Imagem - seleção de região de página de eliminação](images/test-drive-region.png)
 
   O **imagem – eliminar** é aberta a consola de API.
 
3. Na **listId** , introduza o ID da lista de uma imagem a partir de eliminar.  Este é o número retornado no **imagem - obter todos os Ids de imagem** consola para MyList. Em seguida, introduza o **ImageId** da imagem a eliminar. 

No nosso exemplo, é o ID da lista **58953**, o valor de **ContentSource**. A imagem do ID é **59021**, o valor de **ContentIds**.

1. Introduza a chave de subscrição e, em seguida, selecione **enviar**.

1. Para verificar que a imagem foi eliminada, utilize o **imagem - obter todos os Ids de imagem** consola.
 
## <a name="change-list-information"></a>Informações de alteração de lista

Pode editar o nome e uma descrição de uma lista e adicionar itens de metadados.

1. Na [referência de API de gestão da lista de imagens](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f672), no menu da esquerda, selecione **apresenta uma lista de imagem**e, em seguida, selecione **detalhes da atualização**. 

   O **imagem lista - detalhes da atualização** é aberta a página.

2. Para **consola de teste de API aberta**, selecione a região que melhor descreve sua localização.  

    ![Listas de imagens - seleção de região de página de detalhes da atualização](images/test-drive-region.png)

    O **imagem lista - detalhes da atualização** é aberta a consola de API.
 
3. Na **listId** caixa, introduza o ID de lista e, em seguida, introduza a chave de subscrição.

4. Na **corpo do pedido** caixa, faça as edições e, em seguida, selecione a **enviar** botão na página.

   ![Listas de imagens - detalhes da atualização da consola edições de corpo de pedido](images/try-terms-list-change-1.png)
 

## <a name="next-steps"></a>Passos Seguintes

Utilizar a API REST no seu código ou começar com o [apresenta uma lista de imagem do guia de introdução do .NET](image-lists-quickstart-dotnet.md) para integrar com a sua aplicação.
