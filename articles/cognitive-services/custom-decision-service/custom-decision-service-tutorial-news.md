---
title: 'Tutorial: Personalização de artigo-Serviço de Decisão Personalizada'
titlesuffix: Azure Cognitive Services
description: Um tutorial para a personalização de artigos para a tomada de decisões contextuais.
services: cognitive-services
author: slivkins
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-decision-service
ms.topic: tutorial
ms.date: 05/08/2018
ms.author: slivkins
ROBOTS: NOINDEX
ms.openlocfilehash: f7eafed9db25fba904d98ddea652671dc45aa01d
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68707223"
---
# <a name="tutorial-article-personalization-for-contextual-decision-making"></a>Tutorial: Personalização de artigo para tomada de decisão contextual

Este tutorial centra-se na personalização da seleção de artigos na página inicial de um site. O Serviço de Decisão Personalizada afeta, por exemplo, *múltiplas* listas de artigos na página inicial. A página pode ser um site de notícias que aborda apenas política e desporto. Mostraria três listas classificadas de artigos: política, desporto e recentes.

## <a name="applications-and-action-sets"></a>Aplicações e conjuntos de ações

Eis como ajustar o seu cenário à estrutura. Imaginemos três aplicações, uma para cada lista que está a ser otimizada: app-politics, app-sports e app-recent. Para especificar os artigos candidatos para cada aplicação, existem dois conjuntos de ações: um para política e outro para desporto. O conjunto de ações para a app-recent é fornecido automaticamente como uma junção dos outros dois conjuntos.

> [!TIP]
> Os conjuntos de ações podem ser partilhados entre aplicações no Serviço de Decisão Personalizada.

## <a name="prepare-action-set-feeds"></a>Preparar os feeds dos conjuntos de ações

O Serviço de Decisão Personalizada consome conjuntos de ações através dos feeds RSS ou Atom fornecidos pelo cliente. Dois feeds são fornecidos por si: um para política e outro para desporto. Suponha que são fornecidos a partir de `http://www.domain.com/feeds/<feed-name>`.

Cada feed fornece uma lista de artigos. No RSS, cada artigo é especificado pelo elemento `<item>` da seguinte forma:

```xml
<rss version="2.0"><channel>
   <item>
      <title><![CDATA[article title]]></title>
      <link>"article url"</link>
      <pubDate>publication date</pubDate>
    </item>
</channel></rss>
```

A ordem dos artigos é importante, uma vez que especifica a classificação predefinida que é a sua melhor indicação da forma como os artigos devem ser ordenados. A classificação padrão é então usada para comparação de desempenho no painel.

Para obter mais informações sobre o formato do feed, veja a [Referência à API](custom-decision-service-api-reference.md#action-set-api-customer-provided).

## <a name="register-a-new-app"></a>Registar uma nova aplicação

1. Inicie sessão com a sua [conta Microsoft](https://portal.ds.microsoft.com/). No friso, clique em **O Meu Portal**.

2. Para registar uma nova aplicação, clique no botão **Nova Aplicação**.

    ![Portal do Serviço de Decisão Personalizada](./media/custom-decision-service-tutorial/portal.png)

3. Introduza um nome exclusivo para a sua aplicação na caixa de texto **ID da Aplicação**. Se este nome já estiver a ser utilizado por outro cliente, o sistema irá pedir-lhe para escolher um ID de aplicação diferente. Selecione a caixa de verificação **Avançadas** e introduza a [cadeia de ligação](../../storage/common/storage-configure-connection-string.md) da sua conta de armazenamento do Azure. Normalmente, a mesma conta de armazenamento é utilizada para todas as suas aplicações.

    ![Caixa de diálogo Nova Aplicação](./media/custom-decision-service-tutorial/new-app-dialog.png)

    Depois de registar as três aplicações no cenário acima, as mesmas são apresentadas:

    ![Lista de aplicações](./media/custom-decision-service-tutorial/apps.png)

    Pode regressar a esta lista ao clicar no botão **Aplicações**.

4. Na caixa de diálogo **Nova Aplicação**, especifique um feed de ações. Os feeds de ações também podem ser especificados ao clicar no botão **Feeds** e, em seguida, ao clicar no botão **Novo Feed**. Introduza um **Nome** para o novo feed, o **URL** a partir do qual é fornecido e a **Hora de Atualização**. A hora de atualização especifica a frequência com que o Serviço de Decisão Personalizada deve atualizar o feed.

    ![Caixa de diálogo Novo Feed](./media/custom-decision-service-tutorial/new-feed-dialog.png)

    Os feeds de ações podem ser utilizados por qualquer aplicação, independentemente do local onde estejam especificados. Depois de especificar ambos os feeds de ações num cenário, os mesmos são apresentados:

    ![Lista de feeds](./media/custom-decision-service-tutorial/feeds.png)

    Pode regressar a esta lista ao clicar no botão **Feeds**.

## <a name="use-the-apis"></a>Utilizar as APIs

O Serviço de Decisão Personalizada classifica os artigos através da API de Classificação. Para utilizar esta API, insira o seguinte código no cabeçalho HTML da página inicial:

```html
<!-- Define the "callback function" to render UI -->
<script> function callback(data) { … } </script>

<!--  call Ranking API after callback() is defined, separately for each app -->
<script src="https://ds.microsoft.com/api/v2/app-politics/rank/feed-politics" async></script>
<script src="https://ds.microsoft.com/api/v2/app-sports/rank/feed-sports" async></script>
<script src="https://ds.microsoft.com/api/v2/app-recent/rank/feed-politics/feed-sports" async></script>
<!-- NB: action feeds for 'app-recent' are listed one after another. -->
```

A resposta HTTP da API de Classificação é uma cadeia formatada em JSONP. Para a app-politics, por exemplo, a cadeia é semelhante à seguinte:

```json
callback({
   "ranking":[{"id":"url1"}, {"id":"url2"}, {"id":"url3"}],
   "eventId":"<opaque event string>",
   "appId":"app-politics",
   "actionSets":[{"id":"feed-politics","lastRefresh":"date"}] });
```

Em seguida, o browser executa essa cadeia como uma chamada para a função `callback()`. O argumento `data` na função `callback()` contém o ID da aplicação e a classificação de URLs a compor. Em particular, a função `callback()` deve utilizar o valor `data.appId` para distinguir as três aplicações. O valor `eventId` é utilizado internamente pelo Serviço de Decisão Personalizada para fazer coincidir a classificação fornecida com o clique correspondente, se existir.

> [!TIP]
> A função `callback()` pode verificar cada feed de ações relativamente à atualização ao utilizar o campo `lastRefresh`. Se um determinado feed não estiver suficientemente atualizado, a função `callback()` poderá ignorar a classificação fornecida, chamar esse feed diretamente e utilizar a classificação predefinida fornecida pelo feed.

Para obter mais informações sobre as especificações e opções adicionais fornecidas pela API de Classificação, veja a [Referência à API](custom-decision-service-api-reference.md).

As opções de artigos principais do utilizador são devolvidas ao chamar a API de Recompensa. Quando uma opção de artigo principal é recebida, o seguinte código deve ser invocado na página inicial:

```javascript
$.ajax({
    type: "POST",
    url: '//ds.microsoft.com/api/v2/<appId>/reward/<eventId>',
    contentType: "application/json" })
```

Utilizar os valores `appId` e `eventId` no código de processamento de cliques requer algum cuidado. Por exemplo, pode implementar a função `callback()` da seguinte forma:

```javascript
function callback(data) {
    $.map(data.ranking, function (element) {
        //custom rendering function given content info
        render({appId:data.appId,
                article:element,
                onClick: element.id != data.rewardAction ? null :
                   function() {
                       $.ajax({
                       type: "POST",
                       url: '//ds.microsoft.com/api/v2/' + data.appId + '/reward/' + data.eventId,
                       contentType: "application/json" })}
                });
}}
```

Neste exemplo, a função `render()` é implementada para processar um determinado artigo para uma determinada aplicação. Esta função introduz o ID da aplicação e o artigo (no formato da API de Classificação). O parâmetro `onClick` é a função que deve ser chamada a partir da função `render()` para processar um clique. Este parâmetro verifica se o clique está no bloco principal. Em seguida, chama a API de Recompensa com o ID de aplicação e o ID de evento adequados.

## <a name="next-steps"></a>Passos Seguintes

* Veja a [Referência à API](custom-decision-service-api-reference.md) para saber mais sobre a funcionalidade fornecida.
