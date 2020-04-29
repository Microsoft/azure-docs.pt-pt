---
title: Imagens moderadas com listas personalizadas e a consola API - Moderador de Conteúdo
titleSuffix: Azure Content Moderator
description: Utiliza a API de Gestão de Listas em Moderador de Conteúdo Azure para criar listas personalizadas de imagens.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: pafarley
ms.openlocfilehash: 27d9b12d9e1a0237050243c2b5f07edaa8d8857a
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "72757198"
---
# <a name="moderate-with-custom-image-lists-in-the-api-console"></a>Moderado com listas de imagem personalizadas na consola API

Utiliza a API de Gestão de [Listas](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f672) em Moderador de Conteúdo Azure para criar listas personalizadas de imagens. Utilize as listas personalizadas de imagens com a API de Moderação de Imagem. A operação de moderação de imagem avalia a sua imagem. Se criar listas personalizadas, a operação também a compara com as imagens nas suas listas personalizadas. Pode utilizar listas personalizadas para bloquear ou permitir a imagem.

> [!NOTE]
> Existe um limite máximo de **5 listas de imagens**, sendo que cada lista **não pode exceder 10 000 imagens**.
>

Utiliza a API de Gestão de Listas para fazer as seguintes tarefas:

- Criar uma lista.
- Adicione imagens a uma lista.
- Ver imagens contra as imagens numa lista.
- Apagar imagens de uma lista.
- Eliminar uma lista.
- Editar informações da lista.
- Atualizar o índice para que as alterações à lista sejam incluídas numa nova análise.

## <a name="use-the-api-console"></a>Utilize a consola API
Antes de poder testar a API na consola online, precisa da sua chave de subscrição. Isto está localizado no separador **Definições,** na caixa **De-Assinatura-Chave Ocp-Apim..** Para obter mais informações, veja [Descrição geral](overview.md).

## <a name="refresh-search-index"></a>Atualizar índice de pesquisa

Depois de fazer alterações numa lista de imagens, tem de atualizar o seu índice para que as alterações sejam incluídas em futuras digitalizações. Este passo é semelhante ao modo como um motor de pesquisa no seu ambiente de trabalho (se ativado) ou um motor de pesquisa web atualiza continuamente o seu índice para incluir novos ficheiros ou páginas.

1. Na [referência API](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f672)de Gestão da Lista de Imagem, no menu esquerdo, selecione **Listas**de Imagens, e, em seguida, selecione Índice de **Pesquisa de Atualização**.

   As **Listas de Imagem -** Página do Índice de Pesquisa de Refresh abre- se.

2. Para **a consola de teste API aberta,** selecione a região que mais descreve a sua localização. 
 
    ![Listas de Imagens - Seleção da página do Índice de Pesquisa de Refresh](images/test-drive-region.png)

    As **Listas de Imagens - Atualização da** consola API do Índice de Pesquisa abre.

3. Na caixa **listId,** introduza o ID da lista. Introduza a sua chave de subscrição e, em seguida, selecione **Enviar**.

   ![Listas de imagens - Refresh Search Index Caixa de conteúdo de resposta da consola](images/try-image-list-refresh-1.png)


## <a name="create-an-image-list"></a>Criar uma lista de imagens

1. Vá à [referência da API](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f672)de Gestão da Lista de Imagens .

   As **Listas de Imagem - Criar** a página abre. 

3. Para **a consola de teste API aberta,** selecione a região que mais descreve a sua localização.

   ![Listas de Imagens - Criar a seleção da região de página](images/test-drive-region.png)

   As **Listas de Imagem - Create** API consola abre.
 
4. Na caixa **Ocp-Apim-Subscription-Key,** introduza a sua chave de subscrição.

5. Na caixa **de corpo de pedido,** introduza valores para **nome** (por exemplo, MyList) e **Descrição**.

   ![Listas de Imagens - Criar consola Solicitar nome e descrição do corpo](images/try-terms-list-create-1.png)

6. Utilize espaços reservados de par de valor chave para atribuir metadados mais descritivos à sua lista.

       {
          "Name": "MyExclusionList",
          "Description": "MyListDescription",
          "Metadata": 
          {
            "Category": "Competitors",
            "Type": "Exclude"
          }
       }

   Adicione metadados da lista como pares de valor-chave, e não as imagens reais.
 
7. Selecione **Enviar**. A sua lista está criada. Note o valor de **IDENTIFICAÇÃO** associado à nova lista. Precisa deste ID para outras funções de gestão da lista de imagens.

   ![Listas de imagens - Criar caixa de conteúdo de resposta de consola mostra o ID da lista](images/try-terms-list-create-2.png)
 
8. Em seguida, adicione imagens ao MyList. No menu esquerdo, selecione **Imagem**, e, em seguida, selecione **Adicionar Imagem**.

   A **página Image - Adicionar imagem** abre. 

9. Para **a consola de teste API aberta,** selecione a região que mais descreve a sua localização.

   ![Imagem - Adicionar seleção da página de imagem](images/test-drive-region.png)

   A **imagem - Adicionar** a consola Image API abre.
 
10. Na caixa **listId,** introduza o ID da lista que gerou e introduza o URL da imagem que pretende adicionar. Introduza a sua chave de subscrição e, em seguida, selecione **Enviar**.

11. Para verificar se a imagem foi adicionada à lista, no menu esquerdo, selecione **Imagem**, e, em seguida, selecione **Get All Image Ids**.

    A consola Image **- Get All Image Ids** API abre.
  
12. Na caixa **listId,** introduza o ID da lista e introduza a chave de subscrição. Selecione **Enviar**.

    ![Imagem - Obtenha todos os ids de consola De imagem A caixa de conteúdo de resposta lista as imagens que introduziu](images/try-image-list-create-11.png)
 
10. Adicione mais algumas imagens. Agora que criou uma lista personalizada de imagens, tente [avaliar as imagens](try-image-api.md) utilizando a lista de imagens personalizadas. 

## <a name="delete-images-and-lists"></a>Apagar imagens e listas

Apagar uma imagem ou uma lista é simples. Pode utilizar a API para fazer as seguintes tarefas:

- Elimine uma imagem. (**Imagem - Excluir)**
- Apague todas as imagens numa lista sem apagar a lista. (**Imagem - Apagar Todas as Imagens)**
- Eliminar uma lista e todo o conteúdo da mesma. (Listas de**Imagens - Excluir)**

Este exemplo elimina uma única imagem:

1. Na [referência API](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f672)de Gestão da Lista de Imagem, no menu esquerdo, selecione **Imagem**, e, em seguida, **selecione Delete**. 

   A **Imagem - Apagar** a página abre.

2. Para **a consola de teste API aberta,** selecione a região que mais descreve a sua localização. 

   ![Imagem - Excluir a seleção da região da página](images/test-drive-region.png)
 
   A **consola Image - Delete** API abre.
 
3. Na caixa **listId,** introduza a identificação da lista para eliminar uma imagem a partir de.  Este é o número devolvido na consola **Image - Get All Image Ids** para MyList. Em seguida, introduza o **ImageId** da imagem para apagar. 

No nosso exemplo, o ID da lista é **58953,** o valor para **ContentSource**. O ID de imagem é **59021,** o valor para **Os Idóides.**

1. Introduza a sua chave de subscrição e, em seguida, selecione **Enviar**.

1. Para verificar se a imagem foi eliminada, utilize a consola **Image - Get All Image Ids.**
 
## <a name="change-list-information"></a>Alterar informações sobre listas

Pode editar o nome e descrição de uma lista e adicionar itens de metadados.

1. Na [referência API](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f672)de Gestão da Lista de Imagens, no menu esquerdo, selecione **Listas**de Imagens, e, em seguida, selecione Detalhes de **Atualização**. 

   As **Listas de Imagens -** Página de Detalhes de Atualização abre.

2. Para **a consola de teste API aberta,** selecione a região que mais descreve a sua localização.  

    ![Listas de Imagens - Seleção da página de Dados de Atualização](images/test-drive-region.png)

    As **Listas de Imagens -** Atualização Detalhes da consola API abre.
 
3. Na caixa **listId,** introduza o ID da lista e introduza a chave de subscrição.

4. Na caixa **de corpo do Pedido,** faça as suas edições e, em seguida, selecione o botão **Enviar** na página.

   ![Listas de Imagens - Atualizações detalhes consola solicitar edificações do corpo](images/try-terms-list-change-1.png)
 

## <a name="next-steps"></a>Passos seguintes

Utilize a API REST no seu código ou comece com as [listas de imagem .NET quickstart](image-lists-quickstart-dotnet.md) para integrar com a sua aplicação.
