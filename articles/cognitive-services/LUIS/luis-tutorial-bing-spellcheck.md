---
title: Corrigir palavras mal escritas - LUIS
titleSuffix: Azure Cognitive Services
description: Corrija as palavras mal escritas em declarações adicionando Bing Spell Check API V7 a consultas de ponto final LUIS.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "74225456"
---
# <a name="correct-misspelled-words-with-bing-spell-check"></a>Corrigir palavras mal escritas com Bing Spell Check

Pode integrar a sua app LUIS com [bing Spell Check API V7](https://azure.microsoft.com/services/cognitive-services/spell-check/) para corrigir palavras mal escritas em declarações antes que luis preveja a pontuação e as entidades da expressão. 

[!INCLUDE [Not supported in V3 API prediction endpoint](./includes/v2-support-only.md)]


## <a name="create-first-key-for-bing-spell-check-v7"></a>Criar a primeira chave para Bing Spell Check V7

A sua [primeira tecla API v7 de Bing Spell Check](https://azure.microsoft.com/try/cognitive-services/?api=spellcheck-api) é gratuita. 

![Criar chave gratuita](./media/luis-tutorial-bing-spellcheck/free-key.png)

<a name="create-subscription-key"></a>

## <a name="create-endpoint-key"></a>Criar a chave Endpoint
Se a sua chave livre expirar, crie uma chave de ponto final.

1. Faça login no [portal Azure.](https://portal.azure.com) 

2. Selecione **Criar um recurso** no canto superior esquerdo.

3. Na caixa de pesquisa, introduza `Bing Spell Check API V7`.

    ![Pesquisa por Bing Spell Check API V7](./media/luis-tutorial-bing-spellcheck/portal-search.png)

4. Selecione o serviço. 

5. Um painel de informação parece ter o direito de conter informações, incluindo o Aviso Legal. Selecione **Criar** para iniciar o processo de criação de subscrição. 

6. No painel seguinte, introduza as definições de serviço. Aguarde que o processo de criação de serviço termine.

    ![Introduza as definições de serviço](./media/luis-tutorial-bing-spellcheck/subscription-settings.png)

7. Selecione **todos os recursos** sob o título **favoritos** na navegação do lado esquerdo.

8. Selecione o novo serviço. O seu tipo é **Serviços Cognitivos** e a localização é **global.** 

9. No painel principal, selecione **Keys** para ver as suas novas teclas.

    ![Agarrar chaves](./media/luis-tutorial-bing-spellcheck/grab-keys.png)

10. Copie a primeira chave. Só precisas de uma das duas chaves. 

<!--
## Using the key in LUIS test panel
There are two places in LUIS to use the key. The first is in the [test panel](luis-interactive-test.md#view-bing-spell-check-corrections-in-test-panel). The key isn't saved into LUIS but instead is a session variable. You need to set the key every time you want the test panel to apply the Bing Spell Check API v7 service to the utterance. See [instructions](luis-interactive-test.md#view-bing-spell-check-corrections-in-test-panel) in the test panel for setting the key.
-->
## <a name="adding-the-key-to-the-endpoint-url"></a>Adicionar a chave ao URL do ponto final
A consulta de ponto final precisa da chave passada nos parâmetros de corda de consulta para cada consulta que pretende aplicar a correção ortográfica. Você pode ter um chatbot que ligue para LUIS ou você pode chamar a API final do LUIS diretamente. Independentemente da forma como o ponto final é chamado, cada chamada deve incluir as informações necessárias para que as correções ortográficas funcionem corretamente.

O URL do ponto final tem vários valores que precisam de ser aprovados corretamente. A chave Bing Spell Check API v7 é apenas mais uma destas. Tem de definir o parâmetro **spellCheck** como verdadeiro e deve definir o valor da chave de subscrição de **bing-spell-check-check-assinatura** para o valor-chave:

`https://{region}.api.cognitive.microsoft.com/luis/v2.0/apps/{appID}?subscription-key={luisKey}&spellCheck=**true**&bing-spell-check-subscription-key=**{bingKey}**&verbose=true&timezoneOffset=0&q={utterance}`

## <a name="send-misspelled-utterance-to-luis"></a>Envie pronunciamento mal escrito ao LUIS
1. Num navegador web, copie a corda `region` `appId`anterior `luisKey`e `bingKey` substitua a, e com os seus próprios valores. Certifique-se de utilizar a região de ponto final, se for diferente da sua [região](luis-reference-regions.md)editorial.

2. Adicione uma expressão mal escrita como "Qual a distância da montanha?". Em inglês, `mountain`com `n`um, é a ortografia correta. 

3. Selecione entrar para enviar a consulta para LUIS.

4. LUIS responde com um resultado `How far is the mountain?`JSON para . Se bing Spell Check API v7 detetar `query` um erro ortográfico, o campo na resposta JSON da aplicação LUIS contém a consulta original, e o `alteredQuery` campo contém a consulta corrigida enviada para LUIS.

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

## <a name="ignore-spelling-mistakes"></a>Ignore erros ortográficos

Se não quiser utilizar o serviço Bing Spell Check API v7, tem de adicionar a ortografia correta e incorreta. 

Duas soluções são:

* Rotule as expressões que têm as diferentes ortografias para que o LUIS possa aprender a ortografia adequada, bem como os tipografias. Esta opção requer mais esforço de rotulagem do que a utilização de um verificador ortográfico.
* Crie uma lista de frases com todas as variações da palavra. Com esta solução, não é necessário rotular as variações da palavra nas declarações de exemplo. 

## <a name="publishing-page"></a>Página editorial
A página [editorial](luis-how-to-publish-app.md) tem uma caixa de **verificação de verificação de verificadores de feitiços Enable Bing.** Esta é uma conveniência para criar a chave e entender como o URL final point muda. Ainda tem de utilizar os parâmetros finais corretos para que a ortografia seja corrigida para cada expressão. 

> [!div class="nextstepaction"]
> [Saiba mais sobre as expressões de exemplo](luis-how-to-add-example-utterances.md)
