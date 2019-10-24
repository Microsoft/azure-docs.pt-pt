---
title: Texto moderado com listas de termos personalizados-Content Moderator
titleSuffix: Azure Cognitive Services
description: Use a API de gerenciamento de lista para criar listas personalizadas de termos a serem usados com a API de moderação de texto.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: pafarley
ms.openlocfilehash: 828e8ae68286d7c208462d77a31a764427c79637
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/22/2019
ms.locfileid: "72755274"
---
# <a name="moderate-with-custom-term-lists-in-the-api-console"></a>Moderado com listas de termos personalizados no console de API

A lista global predefinida de termos no Azure Content Moderator é suficiente para a maioria das necessidades de moderação de conteúdo. No entanto, poderá ter de filtrar termos que são específicos da sua organização. Por exemplo, poderá querer etiquetar nomes de concorrentes para nova revisão. 

Use a [API de gerenciamento de lista](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f) para criar listas personalizadas de termos a serem usados com a API de moderação de texto. A operação de **tela de texto** examina o texto em busca de profanação e também compara o texto com as listas negras personalizadas e compartilhadas.

> [!NOTE]
> Existe um limite máximo de **5 listas de termos**, sendo que cada lista **não pode exceder 10 000 termos**.
>

Você pode usar a API de gerenciamento de lista para executar as seguintes tarefas:
- Criar uma lista.
- Adicionar termos a uma lista.
- Filtrar termos em relação aos termos de uma lista.
- Eliminar termos de uma lista.
- Eliminar uma lista.
- Editar informações da lista.
- Atualizar o índice para que as alterações à lista sejam incluídas numa nova análise.

## <a name="use-the-api-console"></a>Usar o console de API

Antes de poder testar a API no console online, você precisa da sua chave de assinatura. Essa chave está localizada na guia **configurações** , na caixa **OCP-APIM-Subscription-Key** . Para obter mais informações, veja [Descrição geral](overview.md).

## <a name="refresh-search-index"></a>Atualizar índice de pesquisa

Depois de fazer alterações em uma lista de termos, você deve atualizar seu índice para que as alterações sejam incluídas nas verificações futuras. Esta etapa é semelhante a como um mecanismo de pesquisa em sua área de trabalho (se habilitado) ou um mecanismo de pesquisa da Web atualiza continuamente seu índice para incluir novos arquivos ou páginas.

1. Na [referência da API de gerenciamento da lista de termos](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f), no menu à esquerda, selecione listas de **termos**e, em seguida, selecione **Atualizar índice de pesquisa**. 

   A página **lista de termos – atualizar índice de pesquisa** é aberta.

2. Para **abrir o console de teste de API**, selecione a região que melhor descreve seu local. 

   ![Listas de termos – atualizar seleção de região da página de índice de pesquisa](images/test-drive-region.png)

   O **termo listas-atualizar console da API de índice de pesquisa** é aberto.

3. Na caixa **listar** , insira a ID da lista. Insira sua chave de assinatura e, em seguida, selecione **Enviar**.

   ![Termo lista API-atualizar pesquisa do console de índice Pesquisar caixa de conteúdo](images/try-terms-list-refresh-1.png)

## <a name="create-a-term-list"></a>Criar uma lista de termos
1. Vá para a [referência da API de gerenciamento da lista de termos](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f). 

   A página **listas de termos-criação** é aberta.

2. Para **abrir o console de teste de API**, selecione a região que melhor descreve seu local. 

   ![Listas de termos – criar seleção de região de página](images/test-drive-region.png)

   O **termo listas-criar console de** API é aberto.
 
3. Na caixa **OCP-APIM-Subscription-Key** , insira sua chave de assinatura.

4. Na caixa **corpo da solicitação** , insira valores para **nome** (por exemplo, myList) e **Descrição**.

   ![Listas de termos – criar nome e descrição do corpo da solicitação do console](images/try-terms-list-create-1.png)

5. Use espaços reservados de pares chave-valor para atribuir metadados mais descritivos à sua lista.

       {
          "Name": "MyExclusionList",
          "Description": "MyListDescription",
          "Metadata": 
          {
             "Category": "Competitors",
             "Type": "Exclude"
          }
       }

   Adicione metadados da lista como pares de chave-valor e não termos reais.
 
6. Selecione **Enviar**. Sua lista é criada. Observe o valor de **ID** associado à nova lista. Você precisa dessa ID para outras funções de gerenciamento de lista de termos.

   ![Listas de termos – a caixa criar conteúdo de resposta do console mostra a ID da lista](images/try-terms-list-create-2.png)
 
7. Adicione termos a myList. No menu à esquerda, em **termo**, selecione **Adicionar termo**. 

   A página **termo-adicionar termo** é aberta. 

8. Para **abrir o console de teste de API**, selecione a região que melhor descreve seu local. 

   ![Termo – seleção de região da página Adicionar termo](images/test-drive-region.png)

   O termo console de API de **adição** de termo é aberto.
 
9. Na caixa **listar** , insira a ID da lista que você gerou e selecione um valor para **idioma**. Insira sua chave de assinatura e, em seguida, selecione **Enviar**.

   ![Parâmetros de consulta do console de adição de termos](images/try-terms-list-create-3.png)
 
10. Para verificar se o termo foi adicionado à lista, no menu à esquerda, selecione **termo**e, em seguida, selecione **obter todos os termos**. 

    O **termo o console de API obter todos os termos** é aberto.

11. Na caixa **listar** , insira a ID da lista e, em seguida, insira sua chave de assinatura. Selecione **Enviar**.

12. Na caixa **conteúdo da resposta** , verifique os termos que você inseriu.

    ![Termo-obter todos os termos caixa de conteúdo de resposta do console lista os termos que você inseriu](images/try-terms-list-create-4.png)
 
13. Adicione mais alguns termos. Agora que você criou uma lista de termos personalizada, tente [digitalizar algum texto](try-text-api.md) usando a lista de termos personalizados. 

## <a name="delete-terms-and-lists"></a>Eliminar termos e listas

Eliminar um termo ou uma lista é simples. Você usa a API para realizar as seguintes tarefas:

- Eliminar um termo. (**Excluir termo**)
- Eliminar todos os termos numa lista sem eliminar a lista. (**Termo-excluir todos os termos**)
- Eliminar uma lista e todo o conteúdo da mesma. (**Listas de termos-excluir**)

Este exemplo exclui um único termo.

1. Na [referência da API de gerenciamento da lista de termos](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f), no menu à esquerda, selecione **termo**e, em seguida, selecione **excluir**. 

   A **exclusão de termos** é aberta.

2. Para **abrir o console de teste de API**, selecione a região que melhor descreve seu local. 

   ![Termo – Excluir seleção de região de página](images/test-drive-region.png)

   O console de API de **exclusão de termos** é aberto.
  
3. Na caixa **listar** , insira a ID da lista da qual você deseja excluir um termo. Essa ID é o número (em nosso exemplo, **122**) que é retornado no console **Lists-Get Details** para myList. Insira o termo e selecione um idioma.
 
   ![Termos-excluir parâmetros de consulta do console](images/try-terms-list-delete-1.png)

4. Insira sua chave de assinatura e, em seguida, selecione **Enviar**.

5. Para verificar se o termo foi excluído, use o **termo listas-obter todos os** consoles.

   ![Lista de termos – a caixa obter todo o conteúdo de resposta do console mostra que o termo foi excluído](images/try-terms-list-delete-2.png)
 
## <a name="change-list-information"></a>Alterar informações da lista

Você pode editar o nome e a descrição de uma lista e adicionar itens de metadados.

1. Na [referência da API de gerenciamento da lista de termos](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f), no menu à esquerda, selecione listas de **termos**e, em seguida, selecione **atualizar detalhes**. 

   A página **listas de termos – detalhes da atualização** é aberta.

2. Para **abrir o console de teste de API**, selecione a região que melhor descreve seu local. 

   ![Listas de termos – seleção de região de página de detalhes de atualização](images/test-drive-region.png)

   O **termo listas-console de API de detalhes de atualização** é aberto.

3. Na caixa **listar** , insira a ID da lista e, em seguida, insira sua chave de assinatura.

4. Na caixa **corpo da solicitação** , faça suas edições e, em seguida, selecione **Enviar**.

   ![Listas de termos – edições de corpo de solicitação do console de detalhes de atualização](images/try-terms-list-change-1.png)
 

## <a name="next-steps"></a>Passos seguintes

Use a API REST em seu código ou comece com o [termo listas de início rápido do .net](term-lists-quickstart-dotnet.md) para integrar com seu aplicativo.
