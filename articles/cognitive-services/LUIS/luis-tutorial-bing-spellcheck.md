---
title: Corrigir palavras incorretas-LUIS
titleSuffix: Azure Cognitive Services
description: Corrija palavras incorretas em declarações adicionando Verificação Ortográfica do Bing API v7 às consultas de ponto de extremidade LUIS.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: 9c8babac8450bdfd170d3d18b338ba3d64383a67
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73499031"
---
# <a name="correct-misspelled-words-with-bing-spell-check"></a>Corrigir palavras incorretas com o Verificação Ortográfica do Bing

Você pode integrar seu aplicativo LUIS com a [API v7 do verificação ortográfica do Bing](https://azure.microsoft.com/services/cognitive-services/spell-check/) para corrigir palavras incorretas em declarações antes de Luis prever a pontuação e as entidades do expressão. 

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

[!INCLUDE [Not supported in V3 API prediction endpoint](./includes/v2-support-only.md)]


## <a name="create-first-key-for-bing-spell-check-v7"></a>Criar a primeira chave para Verificação Ortográfica do Bing v7
Sua [primeira chave de API v7 do verificação ortográfica do Bing](https://azure.microsoft.com/try/cognitive-services/?api=spellcheck-api) é gratuita. 

![Criar chave gratuita](./media/luis-tutorial-bing-spellcheck/free-key.png)

< um nome "Create-Subscription-Key" ></a>
## <a name="create-endpoint-key"></a>Criar chave de ponto de extremidade
Se a chave gratuita expirou, crie uma chave de ponto de extremidade.

1. Inicie sessão no [Portal do Azure](https://portal.azure.com). 

2. Selecione **criar um recurso** no canto superior esquerdo.

3. Na caixa de pesquisa, introduza `Bing Spell Check API V7`.

    ![Procurar Verificação Ortográfica do Bing API v7](./media/luis-tutorial-bing-spellcheck/portal-search.png)

4. Selecione o serviço. 

5. Um painel de informações é exibido à direita contendo informações, incluindo o aviso legal. Selecione **criar** para iniciar o processo de criação de assinatura. 

6. No painel seguinte, insira suas configurações de serviço. Aguarde a conclusão do processo de criação de serviço.

    ![Inserir configurações de serviço](./media/luis-tutorial-bing-spellcheck/subscription-settings.png)

7. Selecione **todos os recursos** no título **favoritos** na navegação do lado esquerdo.

8. Selecione o novo serviço. Seu tipo é **Serviços cognitivas** e o local é **global**. 

9. No painel principal, selecione **chaves** para ver as novas chaves.

    ![Capturar chaves](./media/luis-tutorial-bing-spellcheck/grab-keys.png)

10. Copie a primeira chave. Você precisa apenas de uma das duas chaves. 

## <a name="using-the-key-in-luis-test-panel"></a>Usando a chave no painel de teste do LUIS
Há dois locais no LUIS para usar a chave. O primeiro está no [painel de teste](luis-interactive-test.md#view-bing-spell-check-corrections-in-test-panel). A chave não é salva em LUIS, mas sim é uma variável de sessão. Você precisa definir a chave toda vez que desejar que o painel de teste aplique o serviço de Verificação Ortográfica do Bing API v7 ao expressão. Consulte as [instruções](luis-interactive-test.md#view-bing-spell-check-corrections-in-test-panel) no painel de teste para definir a chave.

## <a name="adding-the-key-to-the-endpoint-url"></a>Adicionando a chave à URL do ponto de extremidade
A consulta de ponto de extremidade precisa da chave passada nos parâmetros de cadeia de caracteres de consulta para cada consulta que você deseja aplicar à correção ortográfica. Você pode ter um chatbot que chama LUIS ou você pode chamar a API de ponto de extremidade LUIS diretamente. Independentemente de como o ponto de extremidade é chamado, cada chamada deve incluir as informações necessárias para que as correções ortográficas funcionem corretamente.

A URL do ponto de extremidade tem vários valores que precisam ser passados corretamente. A chave de API v7 do Verificação Ortográfica do Bing é apenas outra delas. Você deve definir o parâmetro de **verificação ortográfica** como true e deve definir o valor de **Bing-corretor-check-Subscription-Key** para o valor da chave:

`https://{region}.api.cognitive.microsoft.com/luis/v2.0/apps/{appID}?subscription-key={luisKey}&spellCheck=**true**&bing-spell-check-subscription-key=**{bingKey}**&verbose=true&timezoneOffset=0&q={utterance}`

## <a name="send-misspelled-utterance-to-luis"></a>Enviar expressão de ortografia para LUIS
1. Em um navegador da Web, copie a cadeia de caracteres anterior e substitua o `region`, `appId`, `luisKey`e `bingKey` pelos seus próprios valores. Certifique-se de usar a região do ponto de extremidade, se for diferente da [região](luis-reference-regions.md)de publicação.

2. Adicione um expressão com uma grafia incorreta, como "até onde está a montanha?". Em inglês, `mountain`, com uma `n`, é a grafia correta. 

3. Selecione Enter para enviar a consulta para LUIS.

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

* Exemplo de rótulo declarações que têm todas as diferentes ortografias para que o LUIS possa aprender a grafia adequada, bem como erros de digitação. Essa opção requer mais esforço de rotulagem do que usar um verificador ortográfico.
* Crie uma lista de frases com todas as variações da palavra. Com essa solução, você não precisa rotular as variações de palavras no exemplo declarações. 

## <a name="publishing-page"></a>Página de publicação
A página de [publicação](luis-how-to-publish-app.md) tem uma caixa de seleção **habilitar verificador ortográfico do Bing** . Essa é uma conveniência para criar a chave e entender como a URL do ponto de extremidade é alterada. Você ainda precisa usar os parâmetros de ponto de extremidade corretos para ter correção ortográfica para cada expressão. 

> [!div class="nextstepaction"]
> [Saiba mais sobre o exemplo declarações](luis-how-to-add-example-utterances.md)
