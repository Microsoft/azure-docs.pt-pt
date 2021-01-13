---
title: Palavras corretas e mal ortoletas - LUIS
titleSuffix: Azure Cognitive Services
description: Corrigir palavras mal escritas em expressões adicionando Bing Spell Check API V7 a consultas de ponto final LUIS.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 01/12/2021
ms.openlocfilehash: f416fe8ef4f6e89d07e6065d4c9435642d9bacb9
ms.sourcegitcommit: c136985b3733640892fee4d7c557d40665a660af
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/13/2021
ms.locfileid: "98179644"
---
# <a name="correct-misspelled-words-with-bing-search-resource"></a>Palavras corretas mal ortoletas com recurso de pesquisa Bing

Pode integrar a sua app LUIS com [bing Search](https://ms.portal.azure.com/#create/Microsoft.BingSearch) para corrigir palavras mal soletradas em expressões antes que a LUIS preveja a pontuação e as entidades da expressão.

## <a name="create-endpoint-key"></a>Criar chave de ponto final

Para criar um recurso Bing Search no portal Azure, siga estas instruções:

1. Faça login no [portal Azure](https://portal.azure.com).

2. **Selecione Criar um recurso** no canto superior esquerdo.

3. Na caixa de pesquisa, `Bing Search V7` insira e selecione o serviço.

4. Um painel de informação aparece no direito de conter informações, incluindo o Aviso Legal. Selecione **Criar** para iniciar o processo de criação de subscrição.

    :::image type="content" source="./media/luis-tutorial-bing-spellcheck/bing-search-resource-portal.png" alt-text="Bing Spell Check Recurso API V7":::

5. No painel seguinte, insira as definições de serviço. Aguarde que o processo de criação de serviços termine.

6. Depois de criar o recurso, vá à lâmina **Keys e Endpoint** à esquerda. 

7. Copie uma das chaves a adicionar ao cabeçalho do seu pedido de previsão. Só vai precisar de uma das duas chaves.

8. Adicione a chave no `mkt-bing-spell-check-key` cabeçalho do pedido de previsão.

<!--
## Using the key in LUIS test panel
There are two places in LUIS to use the key. The first is in the [test panel](luis-interactive-test.md#view-bing-spell-check-corrections-in-test-panel). The key isn't saved into LUIS but instead is a session variable. You need to set the key every time you want the test panel to apply the Bing Spell Check API v7 service to the utterance. See [instructions](luis-interactive-test.md#view-bing-spell-check-corrections-in-test-panel) in the test panel for setting the key.
-->
## <a name="adding-the-key-to-the-endpoint-url"></a>Adicionar a chave ao URL do ponto final
Para cada consulta em que pretende aplicar correção ortográfica, a consulta de ponto final necessita da tecla de recurso Bing Spellcheck passada no parâmetro do cabeçalho de consulta. Você pode ter um chatbot que ligue para LUIS ou você pode ligar diretamente para a API do ponto final LUIS. Independentemente da forma como o ponto final é chamado, cada chamada deve incluir as informações necessárias no pedido do cabeçalho para que as correções ortográficas funcionem corretamente. Deve definir o valor com **a chave de verificação ortográfica mkt-bing-spell-para** o valor da chave.


## <a name="send-misspelled-utterance-to-luis"></a>Enviar declarações mal ortoletadas para LUIS
1. Adicione uma expressão mal ortoleta na consulta de previsão que enviará como "Até onde está a montanha?". Em `mountain` inglês, com `n` um, é a ortografia correta.

2. LUIS responde com um resultado JSON para `How far is the mountain?` . Se bing Spell Check API v7 detetar um erro ortográfico, o `query` campo na resposta JSON da aplicação LUIS contém a consulta original, e o `alteredQuery` campo contém a consulta corrigida enviada para LUIS.

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

## <a name="ignore-spelling-mistakes"></a>Ignorar erros ortográficos

Se não quiser utilizar o serviço Bing Search API v7, tem de adicionar a ortografia correta e incorreta.

Duas soluções são:

* Etiquetar palavras de exemplo que têm todas as diferentes ortografias para que LUIS possa aprender a ortografia adequada, bem como tipografias. Esta opção requer mais esforço de rotulagem do que a utilização de um verificador ortográfica.
* Crie uma lista de frases com todas as variações da palavra. Com esta solução, não é necessário rotular as variações da palavra nas palavras de exemplo.


> [!div class="nextstepaction"]
> [Saiba mais sobre palavras de exemplo](./luis-how-to-add-entities.md)
