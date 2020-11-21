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
ms.date: 11/19/2019
ms.openlocfilehash: 9d2a1702ea131e9b1b4bf5e586f4290db3aff7ff
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2020
ms.locfileid: "95018774"
---
# <a name="correct-misspelled-words-with-bing-spell-check"></a>Palavras corretas escritas com Bing Spell Check

Pode integrar a sua app LUIS com [Bing Spell Check API V7](https://azure.microsoft.com/services/cognitive-services/spell-check/) para corrigir palavras mal escritas em expressões antes que a LUIS preveja a pontuação e as entidades da expressão.

[!INCLUDE [Not supported in V3 API prediction endpoint](./includes/v2-support-only.md)]

## <a name="create-endpoint-key"></a>Criar chave de ponto final

Para criar um recurso Bing Spell Check no portal Azure, siga estas instruções:

1. Faça login no [portal Azure](https://portal.azure.com).

2. **Selecione Criar um recurso** no canto superior esquerdo.

3. Na caixa de pesquisa, introduza `Bing Spell Check API V7`.

    ![Pesquisa por Bing Spell Check API V7](./media/luis-tutorial-bing-spellcheck/portal-search.png)

4. Selecione o serviço.

5. Um painel de informação aparece no direito de conter informações, incluindo o Aviso Legal. Selecione **Criar** para iniciar o processo de criação de subscrição.

6. No painel seguinte, insira as definições de serviço. Aguarde que o processo de criação de serviços termine.

    ![Insira as definições de serviço](./media/luis-tutorial-bing-spellcheck/subscription-settings.png)

7. Selecione **todos os recursos** sob o título **favoritos** na navegação do lado esquerdo.

8. Selecione o novo serviço. O seu tipo é **Serviços Cognitivos** e a localização é **global.**

9. No painel principal, selecione **Keys** para ver as suas novas teclas.

    ![Pegue as chaves](./media/luis-tutorial-bing-spellcheck/grab-keys.png)

10. Copie a primeira chave. Só precisa de uma das duas chaves.

<!--
## Using the key in LUIS test panel
There are two places in LUIS to use the key. The first is in the [test panel](luis-interactive-test.md#view-bing-spell-check-corrections-in-test-panel). The key isn't saved into LUIS but instead is a session variable. You need to set the key every time you want the test panel to apply the Bing Spell Check API v7 service to the utterance. See [instructions](luis-interactive-test.md#view-bing-spell-check-corrections-in-test-panel) in the test panel for setting the key.
-->
## <a name="adding-the-key-to-the-endpoint-url"></a>Adicionar a chave ao URL do ponto final
A consulta de ponto final necessita da chave passada nos parâmetros de cadeia de consulta para cada consulta que pretende aplicar a correção ortográfica. Você pode ter um chatbot que ligue para LUIS ou você pode ligar diretamente para a API do ponto final LUIS. Independentemente da forma como o ponto final é chamado, cada chamada deve incluir as informações necessárias para que as correções ortográficas funcionem corretamente.

O URL de ponto final tem vários valores que precisam de ser aprovados corretamente. A chave Bing Spell Check API v7 é apenas mais uma destas. Tem de definir o parâmetro **spellCheck** para ser verdadeiro e deve definir o valor da **tecla de subscrição de verificação ortográfica para** o valor chave:

`https://{region}.api.cognitive.microsoft.com/luis/v2.0/apps/{appID}?subscription-key={luisKey}&spellCheck=true&bing-spell-check-subscription-key={bingKey}&verbose=true&timezoneOffset=0&q={utterance}`

## <a name="send-misspelled-utterance-to-luis"></a>Enviar declarações mal ortoletadas para LUIS
1. Num navegador web, copie a cadeia anterior e substitua o `region` , e pelos seus `appId` `luisKey` `bingKey` próprios valores. Certifique-se de que utiliza a região do ponto final, se for diferente da sua [região](luis-reference-regions.md)editorial.

2. Adicione uma expressão mal ortoleta como "Até onde está a montanha?". Em `mountain` inglês, com `n` um, é a ortografia correta.

3. Selecione insira para enviar a consulta para LUIS.

4. LUIS responde com um resultado JSON para `How far is the mountain?` . Se bing Spell Check API v7 detetar um erro ortográfico, o `query` campo na resposta JSON da aplicação LUIS contém a consulta original, e o `alteredQuery` campo contém a consulta corrigida enviada para LUIS.

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

Se não quiser utilizar o serviço Bing Spell API v7, tem de adicionar a ortografia correta e incorreta.

Duas soluções são:

* Etiquetar palavras de exemplo que têm todas as diferentes ortografias para que LUIS possa aprender a ortografia adequada, bem como tipografias. Esta opção requer mais esforço de rotulagem do que a utilização de um verificador ortográfica.
* Crie uma lista de frases com todas as variações da palavra. Com esta solução, não é necessário rotular as variações da palavra nas palavras de exemplo.

## <a name="publishing-page"></a>Página de publicação
A página [de publicação](luis-how-to-publish-app.md) tem uma caixa de verificação **de cheques de cheques de ativação.** Esta é uma conveniência para criar a chave e entender como o URL de ponto final muda. Ainda tem de utilizar os parâmetros corretos do ponto final para que a ortografia seja corrigida para cada expressão.

> [!div class="nextstepaction"]
> [Saiba mais sobre palavras de exemplo](./luis-how-to-add-entities.md)