---
title: Imagens moderadas com listas personalizadas e a consola API - Moderador de Conteúdo
titleSuffix: Azure Content Moderator
description: Utiliza a API de Gestão de Listas no Moderador de Conteúdo Azure para criar listas personalizadas de imagens.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: pafarley
ms.openlocfilehash: 0035d367017c92bd151c27e14d744ef41eace069
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85800153"
---
# <a name="moderate-with-custom-image-lists-in-the-api-console"></a>Moderado com listas de imagens personalizadas na consola API

Utiliza a [API de Gestão de Listas](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f672) no Moderador de Conteúdo Azure para criar listas personalizadas de imagens. Utilize as listas personalizadas de imagens com a API de Moderação de Imagem. A operação de moderação de imagem avalia a sua imagem. Se criar listas personalizadas, a operação também a compara com as imagens das suas listas personalizadas. Pode utilizar listas personalizadas para bloquear ou permitir a imagem.

> [!NOTE]
> Existe um limite máximo de **5 listas de imagens**, sendo que cada lista **não pode exceder 10 000 imagens**.
>

Utiliza a API de Gestão de Listas para fazer as seguintes tarefas:

- Criar uma lista.
- Adicione imagens a uma lista.
- Imagens de ecrã contra as imagens numa lista.
- Apague imagens de uma lista.
- Eliminar uma lista.
- Editar informações da lista.
- Atualizar o índice para que as alterações à lista sejam incluídas numa nova análise.

## <a name="use-the-api-console"></a>Utilize a consola API
Antes de poder testar a API na consola online, precisa da sua chave de subscrição. Isto está localizado no **separador Definições,** na caixa **Ocp-Apim-Subscription-Key.** Para obter mais informações, veja [Descrição geral](overview.md).

## <a name="refresh-search-index"></a>Atualizar índice de pesquisa

Depois de fazer alterações numa lista de imagens, tem de atualizar o seu índice para que as alterações sejam incluídas em futuras análises. Este passo é semelhante ao modo como um motor de busca no seu ambiente de trabalho (se ativado) ou um motor de pesquisa web atualiza continuamente o seu índice para incluir novos ficheiros ou páginas.

1. Na [referência API de Gestão de Listas de Imagem,](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f672)no menu esquerdo, selecione **Listas de Imagem**e, em seguida, selecione **Índice de Procura de Atualização**.

   As **Listas de Imagens - A página do Índice de Pesquisa de Atualização** abre.

2. Para **a consola de testes API Aberta,** selecione a região que mais descreve a sua localização. 
 
    ![Listas de imagens - Seleção da região da página do Índice de Pesquisa de Atualização](images/test-drive-region.png)

    As **Listas de Imagens - Atualização do Índice de Pesquisa** API abre.

3. Na caixa **listId,** introduza o ID da lista. Introduza a sua chave de subscrição e, em seguida, selecione **Enviar**.

   ![Listas de imagens - Caixa de conteúdo de resposta à resposta à consola do Índice de Pesquisa de Atualização](images/try-image-list-refresh-1.png)


## <a name="create-an-image-list"></a>Criar uma lista de imagens

1. Aceda à referência da [API de Gestão de Listas de Imagem.](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f672)

   As **Listas de Imagem - Criar** página abre. 

3. Para **a consola de testes API Aberta,** selecione a região que mais descreve a sua localização.

   ![Listas de imagens - Criar seleção de regiões de página](images/test-drive-region.png)

   As **Listas de Imagens - Criar** consola API abre.
 
4. Na caixa **Ocp-Apim-Subscription-Key,** insira a sua chave de subscrição.

5. Na caixa **de ferramentas Request,** introduza valores para **Nome** (por exemplo, MyList) e **Descrição**.

   ![Listas de imagens - Criar consola Pedir nome e descrição do corpo](images/try-terms-list-create-1.png)

6. Utilize os espaços reservados de pares de valor chave para atribuir metadados mais descritivos à sua lista.

    ```json
    {
        "Name": "MyExclusionList",
        "Description": "MyListDescription",
        "Metadata": 
        {
            "Category": "Competitors",
            "Type": "Exclude"
        }
    }
    ```

   Adicione metadados de lista como pares de valor-chave, e não as imagens reais.
 
7. Selecione **Send** (Enviar). A sua lista foi criada. Note o valor **de ID** que está associado à nova lista. Precisa deste ID para outras funções de gestão de listas de imagem.

   ![Listas de imagens - Criar caixa de conteúdo de resposta a consolas mostra o ID da lista](images/try-terms-list-create-2.png)
 
8. Em seguida, adicione imagens à MyList. No menu esquerdo, selecione **Imagem**e, em seguida, selecione **Add Image**.

   A página **Image - Add Image** abre. 

9. Para **a consola de testes API Aberta,** selecione a região que mais descreve a sua localização.

   ![Imagem - Adicionar seleção da região da página de imagem](images/test-drive-region.png)

   A consola **Image - Add API** abre.
 
10. Na caixa **listId,** introduza o ID da lista que gerou e, em seguida, introduza o URL da imagem que pretende adicionar. Introduza a sua chave de subscrição e, em seguida, selecione **Enviar**.

11. Para verificar se a imagem foi adicionada à lista, no menu esquerdo, selecione **Imagem**e, em seguida, selecione **Get All Image Ids**.

    A **consola API image - Get All Image Ids** API abre.
  
12. Na caixa **listId,** introduza o ID da lista e, em seguida, introduza a sua chave de subscrição. Selecione **Send** (Enviar).

    ![Imagem - Obter todas as imagens Ids consola Caixa de conteúdo de resposta lista as imagens que inseriu](images/try-image-list-create-11.png)
 
10. Adicione mais algumas imagens. Agora que criou uma lista personalizada de imagens, tente [avaliar as imagens](try-image-api.md) utilizando a lista de imagens personalizadas. 

## <a name="delete-images-and-lists"></a>Apagar imagens e listas

Excluir uma imagem ou uma lista é simples. Pode utilizar a API para fazer as seguintes tarefas:

- Elimine uma imagem. (Imagem **- Excluir)**
- Elimine todas as imagens de uma lista sem apagar a lista. (Imagem **- Excluir todas as imagens)**
- Eliminar uma lista e todo o conteúdo da mesma. **(Listas de imagens - Excluir)**

Este exemplo elimina uma única imagem:

1. Na [referência API de Gestão de Listas de Imagem,](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f672)no menu esquerdo, selecione **Imagem**e, em seguida, selecione **Delete**. 

   A **página imagem - Apagar** abre.

2. Para **a consola de testes API Aberta,** selecione a região que mais descreve a sua localização. 

   ![Imagem - Excluir seleção da região da página](images/test-drive-region.png)
 
   A **consola Image - Delete** API abre.
 
3. Na caixa **listId,** introduza o ID da lista para eliminar uma imagem de.  Este é o número devolvido na consola **Image - Get All Image Ids** para MyList. Em seguida, introduza o **ImageId** da imagem para apagar. 

No nosso exemplo, o ID da lista é **58953,** o valor para **ContentSource.** O ID de imagem é **59021,** o valor para **Os Conteúdos.**

1. Introduza a sua chave de subscrição e, em seguida, selecione **Enviar**.

1. Para verificar se a imagem foi eliminada, utilize a consola **Image - Get All Image Ids.**
 
## <a name="change-list-information"></a>Alterar informações de lista

Pode editar o nome e descrição de uma lista e adicionar itens de metadados.

1. Na [referência API de Gestão de Listas de Imagem,](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f672)no menu esquerdo, selecione **Listas de Imagem**e, em seguida, selecione **'Detalhes de Actualização'.** 

   As **Listas de Imagens - Página de Detalhes de Atualização** abre.

2. Para **a consola de testes API Aberta,** selecione a região que mais descreve a sua localização.  

    ![Listas de imagens - Seleção da região da página de detalhes de atualização](images/test-drive-region.png)

    As **Listas de Imagens - Atualização Detalhes** A consola API abre.
 
3. Na caixa **listId,** introduza o ID da lista e, em seguida, introduza a sua chave de subscrição.

4. Na caixa **de corpo 'Pedido',** faça as suas edições e, em seguida, selecione o botão **Enviar** na página.

   ![Listas de imagens - Atualização Detalhes consola Pedido de edições corporais](images/try-terms-list-change-1.png)
 

## <a name="next-steps"></a>Passos seguintes

Utilize a API REST no seu código ou comece com as [listas de imagens .NET quickstart](image-lists-quickstart-dotnet.md) para integrar-se com a sua aplicação.
