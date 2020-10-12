---
title: Texto moderado com listas de termos personalizados - Moderador de Conteúdo
titleSuffix: Azure Cognitive Services
description: Utilize a API de Gestão de Listas para criar listas personalizadas de termos a utilizar com a API de Moderação de Texto.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: pafarley
ms.openlocfilehash: f1dfe88741a304da92901d3997c746654336ef54
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85800045"
---
# <a name="moderate-with-custom-term-lists-in-the-api-console"></a>Moderado com listas de prazos personalizados na consola API

A lista global predefinida de termos no Azure Content Moderator é suficiente para a maioria das necessidades de moderação de conteúdo. No entanto, poderá ter de filtrar termos que são específicos da sua organização. Por exemplo, poderá querer etiquetar nomes de concorrentes para nova revisão. 

Utilize a [API de Gestão de Listas](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f) para criar listas personalizadas de termos a utilizar com a API de Moderação de Texto. A operação **Text - Screen** analisa o seu texto por profanação, e também compara texto com listas de bloqueios personalizadas e partilhadas.

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

Antes de poder testar a API na consola online, precisa da sua chave de subscrição. Esta chave está localizada no **separador Definições,** na caixa **Ocp-Apim-Subscription-Key.** Para obter mais informações, veja [Descrição geral](overview.md).

## <a name="refresh-search-index"></a>Atualizar índice de pesquisa

Depois de fazer alterações numa lista de termos, tem de atualizar o seu índice para que as alterações sejam incluídas em futuras análises. Este passo é semelhante ao modo como um motor de busca no seu ambiente de trabalho (se ativado) ou um motor de pesquisa web atualiza continuamente o seu índice para incluir novos ficheiros ou páginas.

1. Na [referência API de Gestão de Listas de Prazos,](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f)no menu esquerdo, selecione **Listas de Prazos**e, em seguida, selecione **Índice de Procura de Atualização**. 

   As **Listas de Prazos - A página do Índice de Procura de Atualização** abre.

2. Para **a consola de testes API Aberta,** selecione a região que mais descreve a sua localização. 

   ![Listas de Mandatos - Seleção da região da página do Índice de Pesquisa de Atualização](images/test-drive-region.png)

   As **Listas de Prazos - Atualização do Índice de Pesquisa** API abre.

3. Na caixa **listId,** introduza o ID da lista. Introduza a sua chave de subscrição e, em seguida, selecione **Enviar**.

   ![Listas de termos API - Appa do Índice de Resposta à resposta à consola de pesquisa](images/try-terms-list-refresh-1.png)

## <a name="create-a-term-list"></a>Criar uma lista de termos
1. Aceda à referência da [API de Gestão de Listas de Prazos.](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f) 

   As **Listas de Prazos - Criar** página abre.

2. Para **a consola de testes API Aberta,** selecione a região que mais descreve a sua localização. 

   ![Listas de Mandatos - Criar seleção de regiões de página](images/test-drive-region.png)

   As **Listas de Prazos - Criar** consola API abre.
 
3. Na caixa **Ocp-Apim-Subscription-Key,** insira a sua chave de subscrição.

4. Na caixa **de ferramentas Request,** introduza valores para **Nome** (por exemplo, MyList) e **Descrição**.

   ![Listas de Prazos - Criar consola Pedir nome e descrição do corpo](images/try-terms-list-create-1.png)

5. Utilize os espaços reservados de pares de valor chave para atribuir metadados mais descritivos à sua lista.

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

   Adicione metadados de lista como pares de valor-chave, e não termos reais.
 
6. Selecione **Send** (Enviar). A sua lista foi criada. Note o valor **de ID** que está associado à nova lista. Precisa deste ID para outras funções de gestão de listas de mandatos.

   ![Listas de Prazos - Criar caixa de conteúdo de resposta à consola mostra o ID da lista](images/try-terms-list-create-2.png)
 
7. Adicione termos à MyList. No menu esquerdo, em **Term**, selecione **Add Term**. 

   Abre-se a página **Termo - Adicionar Prazo.** 

8. Para **a consola de testes API Aberta,** selecione a região que mais descreve a sua localização. 

   ![Termo - Adicionar seleção da região da página de prazo](images/test-drive-region.png)

   Abre a consola **Term - Add Term** API.
 
9. Na caixa **listId,** introduza o ID da lista que gerou e selecione um valor para **o idioma**. Introduza a sua chave de subscrição e, em seguida, selecione **Enviar**.

   ![Termo - Adicionar parâmetros de consulta de consola de prazos](images/try-terms-list-create-3.png)
 
10. Para verificar se o termo foi adicionado à lista, no menu esquerdo, selecione **Term**, e, em seguida, selecione **Obter Todos os Termos**. 

    O **Termo - Obter todos os termos** a consola API abre.

11. Na caixa **listId,** introduza o ID da lista e, em seguida, introduza a sua chave de subscrição. Selecione **Send** (Enviar).

12. Na caixa de **conteúdo 'Resposta',** verifique os termos introduzidos.

    ![Termo - Obter todos os termos consola Caixa de conteúdo de resposta lista os termos que inseriu](images/try-terms-list-create-4.png)
 
13. Adicione mais alguns termos. Agora que criou uma lista de termos personalizados, tente [digitalizar algum texto](try-text-api.md) utilizando a lista de termos personalizados. 

## <a name="delete-terms-and-lists"></a>Eliminar termos e listas

Eliminar um termo ou uma lista é simples. Utiliza a API para fazer as seguintes tarefas:

- Eliminar um termo. (Termo **- Eliminar)**
- Eliminar todos os termos numa lista sem eliminar a lista. (Termo **- Eliminar todos os termos)**
- Eliminar uma lista e todo o conteúdo da mesma. **(Listas de Prazos - Eliminar)**

Este exemplo elimina um único termo.

1. Na [referência API de Gestão de Listas de Prazos,](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f)no menu esquerdo, selecione **Term**, e, em seguida, selecione **Delete**. 

   O **Termo - Eliminar** abre.

2. Para **a consola de testes API Aberta,** selecione a região que mais descreve a sua localização. 

   ![Termo - Excluir seleção da região de páginas](images/test-drive-region.png)

   Abre a consola **Term - Delete** API.
  
3. Na caixa **listId,** introduza o ID da lista de que pretende eliminar um termo de. Este ID é o número (no nosso exemplo, **122)** que é devolvido nas **Listas de Termos - Obter Detalhes** para a MyList. Insira o termo e selecione um idioma.
 
   ![Termo - Eliminar parâmetros de consulta de consola](images/try-terms-list-delete-1.png)

4. Introduza a sua chave de subscrição e, em seguida, selecione **Enviar**.

5. Para verificar se o termo foi eliminado, utilize as **Listas de Prazos - Obter todas as** consolas.

   ![Listas de Prazos - Obter todas as caixas de conteúdo de resposta de consola mostra que o termo é eliminado](images/try-terms-list-delete-2.png)
 
## <a name="change-list-information"></a>Alterar informações de lista

Pode editar o nome e descrição de uma lista e adicionar itens de metadados.

1. Na [referência API de Gestão de Listas de Prazos,](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f)no menu esquerdo, selecione **Listas de Prazos**e, em seguida, selecione **'Detalhes de Actualização'.** 

   Listas de Prazos - A página **de detalhes de atualização** abre.

2. Para **a consola de testes API Aberta,** selecione a região que mais descreve a sua localização. 

   ![Listas de Mandatos - Seleção da região da página de detalhes de atualização](images/test-drive-region.png)

   As **Listas de Prazos - Atualização A** consola API abre.

3. Na caixa **listId,** introduza o ID da lista e, em seguida, introduza a sua chave de subscrição.

4. Na caixa **de corpo Request,** faça as suas edições e, em seguida, selecione **Enviar**.

   ![Listas de prazos - Atualização Detalhes consola Pedido de edições corporais](images/try-terms-list-change-1.png)
 

## <a name="next-steps"></a>Passos seguintes

Utilize a API REST no seu código ou comece com as [listas Term .NET quickstart](term-lists-quickstart-dotnet.md) para integrar-se com a sua aplicação.
