---
title: Corrigir palavras incorretas-LUIS
titleSuffix: Azure Cognitive Services
description: Palavras com erros ortográficos corretas em expressões com adicionando Bing ortográfica verificar a API V7 às consultas de ponto final do LUIS.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: diberry
ms.openlocfilehash: 51b0d02443df872a7fae13116ea77b13d05055fa
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74225456"
---
# <a name="correct-misspelled-words-with-bing-spell-check"></a>Palavras com erros ortográficos corretas com verificação de ortografia do Bing

Você pode integrar seu aplicativo LUIS com a [API v7 do verificação ortográfica do Bing](https://azure.microsoft.com/services/cognitive-services/spell-check/) para corrigir palavras incorretas em declarações antes de Luis prever a pontuação e as entidades do expressão. 

[!INCLUDE [Not supported in V3 API prediction endpoint](./includes/v2-support-only.md)]


## <a name="create-first-key-for-bing-spell-check-v7"></a>Criar a primeira chave para V7 de verificação de ortografia do Bing

Sua [primeira chave de API v7 do verificação ortográfica do Bing](https://azure.microsoft.com/try/cognitive-services/?api=spellcheck-api) é gratuita. 

![Criar chave gratuita](./media/luis-tutorial-bing-spellcheck/free-key.png)

<a name="create-subscription-key"></a>

## <a name="create-endpoint-key"></a>Criar chave de ponto final
Se a sua chave gratuita expirou, crie uma chave de ponto final.

1. Inicie sessão no [Portal do Azure](https://portal.azure.com). 

2. Selecione **criar um recurso** no canto superior esquerdo.

3. Na caixa de pesquisa, introduza `Bing Spell Check API V7`.

    ![A API V7 de verificação de pesquisa de ortografia do Bing](./media/luis-tutorial-bing-spellcheck/portal-search.png)

4. Selecione o serviço. 

5. É apresentado um painel de informações à direita que contém informações, incluindo o aviso Legal. Selecione **criar** para iniciar o processo de criação de assinatura. 

6. No painel seguinte, introduza as definições de serviço. Aguarde que o processo de criação de serviço concluir.

    ![Introduza as definições de serviço](./media/luis-tutorial-bing-spellcheck/subscription-settings.png)

7. Selecione **todos os recursos** no título **favoritos** na navegação do lado esquerdo.

8. Selecione o novo serviço. Seu tipo é **Serviços cognitivas** e o local é **global**. 

9. No painel principal, selecione **chaves** para ver as novas chaves.

    ![Obter chaves](./media/luis-tutorial-bing-spellcheck/grab-keys.png)

10. Copie a primeira chave. Só precisa de uma das duas chaves. 

<!--
## Using the key in LUIS test panel
There are two places in LUIS to use the key. The first is in the [test panel](luis-interactive-test.md#view-bing-spell-check-corrections-in-test-panel). The key isn't saved into LUIS but instead is a session variable. You need to set the key every time you want the test panel to apply the Bing Spell Check API v7 service to the utterance. See [instructions](luis-interactive-test.md#view-bing-spell-check-corrections-in-test-panel) in the test panel for setting the key.
-->
## <a name="adding-the-key-to-the-endpoint-url"></a>Adicionar a chave para o URL de ponto final
A consulta de ponto final tem da chave transmitida os parâmetros de cadeia de caracteres de consulta para cada consulta que pretende aplicar a correção ortográfica. Pode ter um chatbot que chama o LUIS ou pode chamar o ponto de extremidade do LUIS API diretamente. Independentemente de como o ponto final é chamado, cada chamada tem de incluir as informações necessárias para correção ortográfica será funcione corretamente.

O ponto final do URL tem vários valores que precisam ser passados corretamente. A chave da API de verificação ortográfica do Bing v7 é apenas mais um deles. Você deve definir o parâmetro de **verificação ortográfica** como true e deve definir o valor de **Bing-corretor-check-Subscription-Key** para o valor da chave:

`https://{region}.api.cognitive.microsoft.com/luis/v2.0/apps/{appID}?subscription-key={luisKey}&spellCheck=**true**&bing-spell-check-subscription-key=**{bingKey}**&verbose=true&timezoneOffset=0&q={utterance}`

## <a name="send-misspelled-utterance-to-luis"></a>Enviar a expressão com erros ortográficos para LUIS
1. Em um navegador da Web, copie a cadeia de caracteres anterior e substitua o `region`, `appId`, `luisKey`e `bingKey` pelos seus próprios valores. Certifique-se de usar a região do ponto de extremidade, se for diferente da [região](luis-reference-regions.md)de publicação.

2. Adicione uma expressão com erros ortográficos, como "até que ponto é o mountainn?". Em inglês, `mountain`, com uma `n`, é a grafia correta. 

3. Selecione enter para enviar a consulta para o LUIS.

4. LUIS responde com um resultado JSON para `How far is the mountain?`. Se Verificação Ortográfica do Bing API v7 detectar um erro de ortografia, o campo `query` na resposta JSON do aplicativo LUIS conterá a consulta original e o campo `alteredQuery` conterá a consulta corrigida enviada ao LUIS.

```json
{
  "query": "How far is the mountainn?",
  "alteredQuery": "How far is the mountain?",
  "topScoringIntent": {
    "intent": "Concierge",
    "score": 0.183866
  },
  "entities": []
}
```

## <a name="ignore-spelling-mistakes"></a>Ignorar erros de ortografia

Se você não quiser usar o serviço do Verificação Ortográfica do Bing API v7, será necessário adicionar a grafia correta e incorreta. 

Duas soluções são:

* Exemplo de rótulo declarações que têm todas as diferentes ortografias para que o LUIS possa aprender a grafia adequada, bem como erros de digitação. Esta opção requer maior esforço de etiquetagem que usar um corretor ortográfico.
* Crie uma lista de frases com todas as variações da palavra. Com essa solução, você não precisa rotular as variações de palavras no exemplo declarações. 

## <a name="publishing-page"></a>Página de publicação
A página de [publicação](luis-how-to-publish-app.md) tem uma caixa de seleção **habilitar verificador ortográfico do Bing** . Esta é uma conveniência para criar a chave e compreender como o URL de ponto final é alterado. Ainda terá de utilizar os parâmetros de ponto final correto para ter corrigida em cada ocorrência de pronunciação de ortografia. 

> [!div class="nextstepaction"]
> [Saiba mais sobre o exemplo declarações](luis-how-to-add-example-utterances.md)
