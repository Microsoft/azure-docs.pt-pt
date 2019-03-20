---
title: Texto moderado com listas de termos personalizados - Content Moderator
titlesuffix: Azure Cognitive Services
description: Utilize a API de gestão da lista para criar listas personalizadas de termos para utilizar com a API de moderação de texto.
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: 28029fe92a207dba85e2ab5a22c08879b7172925
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58105782"
---
# <a name="moderate-with-custom-term-lists-in-the-api-console"></a>Moderar com listas de termos personalizada na consola de API

A lista global predefinida de termos no Azure Content Moderator é suficiente para a maioria das necessidades de moderação de conteúdo. No entanto, poderá ter de filtrar termos que são específicos da sua organização. Por exemplo, poderá querer etiquetar nomes de concorrentes para nova revisão. 

Utilizar o [API de gestão da lista](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f) para criar listas personalizadas de termos para utilizar com a API de moderação de texto. O **texto - ecrã** operação analisa o texto relativamente a profanidades e também compara o texto em relação a listas de bloqueios personalizadas e partilhados.

> [!NOTE]
> Existe um limite máximo de **5 listas de termos**, sendo que cada lista **não pode exceder 10 000 termos**.
>

Pode utilizar a API de gestão da lista para efetuar as seguintes tarefas:
- Criar uma lista.
- Adicionar termos a uma lista.
- Filtrar termos em relação aos termos de uma lista.
- Eliminar termos de uma lista.
- Eliminar uma lista.
- Editar informações da lista.
- Atualizar o índice para que as alterações à lista sejam incluídas numa nova análise.

## <a name="use-the-api-console"></a>Utilizar a consola de API

Antes de pode testar a API na consola do online, terá a chave de subscrição. Esta chave está localizada no **configurações** separador a **Ocp-Apim-Subscription-Key** caixa. Para obter mais informações, veja [Descrição geral](overview.md).

## <a name="refresh-search-index"></a>Atualizar o índice de pesquisa

Depois de efetuar alterações a uma lista de termo, tem de atualizar o seu índice para que as alterações a serem incluídos em verificações futuras. Este passo é semelhante a como um mecanismo de pesquisa no ambiente de trabalho (se ativada) ou um mecanismo de pesquisa web continuamente atualiza seu índice para incluir novos ficheiros ou páginas.

1. Na [referência da API de gestão de lista do termo](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f), no menu da esquerda, selecione **apresenta uma lista do termo**e, em seguida, selecione **atualizar o índice de pesquisa**. 

   O **termo lista - atualizar o índice de pesquisa** é aberta a página.

2. Para **consola de teste de API aberta**, selecione a região que melhor descreve sua localização. 

   ![Listas de termos - seleção de região de página de índice de pesquisa de atualização](images/test-drive-region.png)

   O **termo lista - atualizar o índice de pesquisa** é aberta a consola de API.

3. Na **listId** , introduza o ID da lista. Introduza a chave de subscrição e, em seguida, selecione **enviar**.

   ![Listas de termos API - caixa de conteúdo de resposta do índice de pesquisa de atualização de consola](images/try-terms-list-refresh-1.png)

## <a name="create-a-term-list"></a>Criar uma lista de termos
1. Vá para o [referência da API de gestão de lista do termo](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f). 

   O **apresenta uma lista de termo - criar** é aberta a página.

2. Para **consola de teste de API aberta**, selecione a região que melhor descreve sua localização. 

   ![Prazo listas - criar a seleção de região de página](images/test-drive-region.png)

   O **apresenta uma lista de termo - criar** é aberta a consola de API.
 
3. Na **Ocp-Apim-Subscription-Key** , introduza a chave de subscrição.

4. Na **corpo do pedido** , introduza valores para **nome** (por exemplo, MyList) e **Descrição**.

   ![Prazo listas - criar o nome de corpo de pedido de consola e uma descrição](images/try-terms-list-create-1.png)

5. Utilize marcadores de posição de par chave-valor para atribuir mais descritivo metadados à sua lista.

       {
          "Name": "MyExclusionList",
          "Description": "MyListDescription",
          "Metadata": 
          {
             "Category": "Competitors",
             "Type": "Exclude"
          }
       }

   Adicione metadados de lista como pares chave-valor bem como termos não reais.
 
6. Selecione **Enviar**. A lista é criada. Tenha em atenção a **ID** valor que estão associado com a nova lista. Vai precisar deste ID para outras funções de gerenciamento de lista do termo.

   ![Prazo listas - criar consola resposta conteúdo caixa mostra o ID de lista](images/try-terms-list-create-2.png)
 
7. Adicione termos ao MyList. No menu à esquerda, em **termo**, selecione **termo adicionar**. 

   O **prazo - adicionar o termo** é aberta a página. 

8. Para **consola de teste de API aberta**, selecione a região que melhor descreve sua localização. 

   ![Prazo - adicionar seleção de região de página do termo](images/test-drive-region.png)

   O **prazo - adicionar o termo** é aberta a consola de API.
 
9. Na **listId** caixa, introduza o ID de lista que gerou e selecione um valor para **linguagem**. Introduza a chave de subscrição e, em seguida, selecione **enviar**.

   ![Prazo – adicionar parâmetros de consulta de consola do termo](images/try-terms-list-create-3.png)
 
10. Para verificar que o termo foi adicionado à lista, no menu à esquerda, selecione **termo**e, em seguida, selecione **obter todos os termos**. 

    O **termo - obter todos os termos** é aberta a consola de API.

11. Na **listId** caixa, introduza o ID de lista e, em seguida, introduza a chave de subscrição. Selecione **Enviar**.

12. Na **conteúdo de resposta** caixa, verifique se os termos que introduziu.

    ![Termo - listas de caixa de conteúdo de resposta de consola de todos os termos de Get os termos que introduziu](images/try-terms-list-create-4.png)
 
13. Adicione alguns termos mais. Agora que criou uma lista personalizada de termos, experimente [verificação algum texto](try-text-api.md) , utilizando a lista de termo personalizado. 

## <a name="delete-terms-and-lists"></a>Eliminar termos e listas

Eliminar um termo ou uma lista é simples. Utilize a API para realizar as seguintes tarefas:

- Eliminar um termo. (**Prazo – eliminar**)
- Eliminar todos os termos numa lista sem eliminar a lista. (**Prazo – eliminar todos os termos**)
- Eliminar uma lista e todo o conteúdo da mesma. (**Listas de termos - Delete**)

Este exemplo elimina um único termo.

1. Na [referência da API de gestão de lista do termo](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f), no menu da esquerda, selecione **termo**e, em seguida, selecione **eliminar**. 

   O **prazo – eliminar** abre.

2. Para **consola de teste de API aberta**, selecione a região que melhor descreve sua localização. 

   ![Termo - seleção de região de página de eliminação](images/test-drive-region.png)

   O **prazo – eliminar** é aberta a consola de API.
  
3. Na **listId** , introduza o ID da lista que pretende eliminar um termo de. Este ID é o número (no nosso exemplo, **122**) que é devolvido na **termo lista - detalhes do obter** consola para MyList. Introduza o termo e selecione um idioma.
 
   ![Termo - parâmetros de consulta da consola de eliminação](images/try-terms-list-delete-1.png)

4. Introduza a chave de subscrição e, em seguida, selecione **enviar**.

5. Para verificar que o termo foi eliminado, utilize o **apresenta uma lista de termo - Obtenha tudo** consola.

   ![Prazo listas – obter resposta de consola todos os conteúdos de caixa mostra que o termo é eliminado](images/try-terms-list-delete-2.png)
 
## <a name="change-list-information"></a>Informações de alteração de lista

Pode editar o nome e uma descrição de uma lista e adicionar itens de metadados.

1. Na [referência da API de gestão de lista do termo](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f), no menu da esquerda, selecione **apresenta uma lista do termo**e, em seguida, selecione **detalhes da atualização**. 

   O **termo lista - detalhes da atualização** é aberta a página.

2. Para **consola de teste de API aberta**, selecione a região que melhor descreve sua localização. 

   ![Listas de termos - seleção de região de página de detalhes da atualização](images/test-drive-region.png)

   O **termo lista - detalhes da atualização** é aberta a consola de API.

3. Na **listId** caixa, introduza o ID de lista e, em seguida, introduza a chave de subscrição.

4. Na **corpo do pedido** caixa, faça as edições e, em seguida, selecione **enviar**.

   ![Listas de termos - detalhes da atualização da consola edições de corpo de pedido](images/try-terms-list-change-1.png)
 

## <a name="next-steps"></a>Passos Seguintes

Utilizar a API REST no seu código ou começar com o [termo apresenta uma lista de início rápido para .NET](term-lists-quickstart-dotnet.md) para integrar com a sua aplicação.
