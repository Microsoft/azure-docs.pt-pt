---
title: Texto moderado com listas de termo personalizadas - Moderador de Conteúdo
titleSuffix: Azure Cognitive Services
description: Utilize a API de Gestão de Listas para criar listas personalizadas de termos para utilizar com a API de Moderação de Texto.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: pafarley
ms.openlocfilehash: 16cfb6c15a4d17ff3fb4f7f41f59f9f80af1e9e7
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "75382128"
---
# <a name="moderate-with-custom-term-lists-in-the-api-console"></a>Moderado com listas de termo personalizados na consola API

A lista global predefinida de termos no Azure Content Moderator é suficiente para a maioria das necessidades de moderação de conteúdo. No entanto, poderá ter de filtrar termos que são específicos da sua organização. Por exemplo, poderá querer etiquetar nomes de concorrentes para nova revisão. 

Utilize a API de Gestão de [Listas](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f) para criar listas personalizadas de termos para utilizar com a API de Moderação de Texto. O **Texto - Operação** screen digitaliza o seu texto para profanação, e também compara texto com listas de bloqueio personalizadas e partilhadas.

> [!NOTE]
> Existe um limite máximo de **5 listas de termos**, sendo que cada lista **não pode exceder 10 000 termos**.
>

Pode utilizar a API de Gestão de Listas para fazer as seguintes tarefas:
- Criar uma lista.
- Adicionar termos a uma lista.
- Filtrar termos em relação aos termos de uma lista.
- Eliminar termos de uma lista.
- Eliminar uma lista.
- Editar informações da lista.
- Atualizar o índice para que as alterações à lista sejam incluídas numa nova análise.

## <a name="use-the-api-console"></a>Utilize a consola API

Antes de poder testar a API na consola online, precisa da sua chave de subscrição. Esta chave está localizada no separador **Definições,** na caixa **Desinuine-Subscrição-Chave Ocp-Apim.** Para obter mais informações, veja [Descrição geral](overview.md).

## <a name="refresh-search-index"></a>Atualizar índice de pesquisa

Depois de fazer alterações a uma lista de prazos, tem de atualizar o seu índice para que as alterações sejam incluídas em futuras digitalizações. Este passo é semelhante ao modo como um motor de pesquisa no seu ambiente de trabalho (se ativado) ou um motor de pesquisa web atualiza continuamente o seu índice para incluir novos ficheiros ou páginas.

1. Na [referência API](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f)de Gestão de Listas terminosas, no menu esquerdo, selecione **Listas**de Prazos, e, em seguida, selecione Índice de **Pesquisa de Atualização**. 

   Abre a página do Índice de Pesquisa de **Prazos - Refresh Search Index.**

2. Para **a consola de teste API aberta,** selecione a região que mais descreve a sua localização. 

   ![Listas de Prazos - Seleção da página do Índice de Pesquisa de Refresh](images/test-drive-region.png)

   Abre-se a consola API do Índice de **Pesquisa de Refresh.**

3. Na caixa **listId,** introduza o ID da lista. Introduza a sua chave de subscrição e, em seguida, selecione **Enviar**.

   ![Listas de termo API - Refresh Search Index Caixa de conteúdo de resposta da consola](images/try-terms-list-refresh-1.png)

## <a name="create-a-term-list"></a>Criar uma lista de termos
1. Vá à referência da API de Gestão de [Listas de Prazos](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f). 

   As Listas de **Prazos - Criar** página abre.

2. Para **a consola de teste API aberta,** selecione a região que mais descreve a sua localização. 

   ![Listas de Prazos - Criar a seleção da região de página](images/test-drive-region.png)

   As Listas de **Prazos - Criar** a consola API abre.
 
3. Na caixa **Ocp-Apim-Subscription-Key,** introduza a sua chave de subscrição.

4. Na caixa **de corpo de pedido,** introduza valores para **nome** (por exemplo, MyList) e **Descrição**.

   ![Listas de Prazos - Criar o nome e descrição do corpo do pedido de consola](images/try-terms-list-create-1.png)

5. Utilize espaços reservados de par de valor chave para atribuir metadados mais descritivos à sua lista.

       {
          "Name": "MyExclusionList",
          "Description": "MyListDescription",
          "Metadata": 
          {
             "Category": "Competitors",
             "Type": "Exclude"
          }
       }

   Adicione metadados da lista como pares de valor-chave, e não termos reais.
 
6. Selecione **Enviar**. A sua lista está criada. Note o valor de **IDENTIFICAÇÃO** associado à nova lista. Você precisa deste ID para outras funções de gestão de listas de prazos.

   ![Listas de Prazos - Criar caixa de conteúdo de resposta de consola mostra o ID da lista](images/try-terms-list-create-2.png)
 
7. Adicione termos ao MyList. No menu esquerdo, em **Termo,** **selecione Adicionar Termo**. 

   O **Termo - Adicionar** página Termo abre. 

8. Para **a consola de teste API aberta,** selecione a região que mais descreve a sua localização. 

   ![Termo - Adicionar seleção de página de termo seleção de região](images/test-drive-region.png)

   Abre-se a consola Term **- Add** Term API.
 
9. Na caixa **listId,** introduza o ID da lista que gerou e selecione um valor para **o idioma**. Introduza a sua chave de subscrição e, em seguida, selecione **Enviar**.

   ![Termo - Adicionar parâmetros de consulta de consola de termo](images/try-terms-list-create-3.png)
 
10. Para verificar se o termo foi adicionado à lista, no menu esquerdo, selecione **Term**, e, em seguida, selecione **Obter Todos os Termos**. 

    O **Termo - Obter Todos os Termos** Api consola abre.

11. Na caixa **listId,** introduza o ID da lista e introduza a chave de subscrição. Selecione **Enviar**.

12. Na caixa de **conteúdo resposta,** verifique os termos introduzidos.

    ![Termo - Obtenha todos os termos consola A caixa de conteúdo de resposta lista os termos que introduziu](images/try-terms-list-create-4.png)
 
13. Adicione mais alguns termos. Agora que criou uma lista personalizada de termos, experimente [digitalizar algum texto](try-text-api.md) utilizando a lista de termos personalizados. 

## <a name="delete-terms-and-lists"></a>Eliminar termos e listas

Eliminar um termo ou uma lista é simples. Utiliza a API para fazer as seguintes tarefas:

- Eliminar um termo. (**Termo - Excluir)**
- Eliminar todos os termos numa lista sem eliminar a lista. (**Termo - Excluir Todos os Termos)**
- Eliminar uma lista e todo o conteúdo da mesma. (Listas de**Prazos - Excluir)**

Este exemplo elimina um único termo.

1. Na [referência API](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f)de Gestão de Lista seletiva , no menu esquerdo, selecione **Termo**, e, em seguida, **selecione Delete**. 

   O **Termo - Apagar** abre.

2. Para **a consola de teste API aberta,** selecione a região que mais descreve a sua localização. 

   ![Termo - Eliminar a seleção da região de página](images/test-drive-region.png)

   O **Termo - Eliminar** a consola API abre.
  
3. Na caixa **listId,** introduza a identificação da lista a que pretende eliminar um termo. Este ID é o número (no nosso exemplo, **122**) que é devolvido nas **Listas** de Prazo - Obtenha detalhes para a consola MyList. Insira o termo e selecione um idioma.
 
   ![Termo - Eliminar parâmetros de consulta de consola](images/try-terms-list-delete-1.png)

4. Introduza a sua chave de subscrição e, em seguida, selecione **Enviar**.

5. Para verificar se o termo foi eliminado, utilize as **Listas** de Prazo - Get All consola.

   ![Listas de Prazos - Obter Todas as caixas de conteúdo de resposta da consola mostra que o termo é eliminado](images/try-terms-list-delete-2.png)
 
## <a name="change-list-information"></a>Alterar informações sobre listas

Pode editar o nome e descrição de uma lista e adicionar itens de metadados.

1. Na [referência API](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f)de Gestão de Listas terminárias, no menu esquerdo, selecione Listas de **Prazos**e, em seguida, selecione Detalhes de **Atualização**. 

   As Listas de **Prazos -** Página de Detalhes de Atualização abre.

2. Para **a consola de teste API aberta,** selecione a região que mais descreve a sua localização. 

   ![Listas de Prazos - Atualização Detalhes da seleção da região](images/test-drive-region.png)

   As **Listas de Prazos -** Atualização Detalhes Da consola API abre.

3. Na caixa **listId,** introduza o ID da lista e introduza a chave de subscrição.

4. Na caixa **de corpo solicitar,** faça as suas edições e, em seguida, selecione **Enviar**.

   ![Listas de Prazos - Atualizações detalhes consola solicitar edificações do corpo](images/try-terms-list-change-1.png)
 

## <a name="next-steps"></a>Passos seguintes

Utilize a API REST no seu código ou comece com as [listas de prazo .NET quickstart](term-lists-quickstart-dotnet.md) para integrar com a sua aplicação.
