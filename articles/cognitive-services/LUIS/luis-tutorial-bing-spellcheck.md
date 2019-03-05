---
title: Palavras com erros ortográficos corretas
titleSuffix: Azure
description: Palavras com erros ortográficos corretas em expressões com adicionando Bing ortográfica verificar a API V7 às consultas de ponto final do LUIS.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 03/04/2019
ms.author: diberry
ms.openlocfilehash: 1e5536b08b3b78f35426207369f444e6eb21c87d
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2019
ms.locfileid: "57341808"
---
# <a name="correct-misspelled-words-with-bing-spell-check"></a>Palavras com erros ortográficos corretas com verificação de ortografia do Bing

Pode integrar a sua aplicação LUIS com [Bing ortográfica verificar a API V7](https://azure.microsoft.com/services/cognitive-services/spell-check/) para corrigir palavras com erros ortográficos em expressões com antes do LUIS prevê a pontuação e entidades da expressão. 

## <a name="create-first-key-for-bing-spell-check-v7"></a>Criar a primeira chave para V7 de verificação de ortografia do Bing
Sua [primeira chave da API de verificação ortográfica do Bing v7](https://azure.microsoft.com/try/cognitive-services/?api=spellcheck-api) é gratuito. 

![Criar chave gratuita](./media/luis-tutorial-bing-spellcheck/free-key.png)

< um nome "Criar-subscription-key" ></a>
## <a name="create-endpoint-key"></a>Criar chave de ponto final
Se a sua chave gratuita expirou, crie uma chave de ponto final.

1. Inicie sessão no [Portal do Azure](https://portal.azure.com). 

2. Selecione **criar um recurso** no canto superior esquerdo.

3. Na caixa de pesquisa, introduza `Bing Spell Check API V7`.

    ![A API V7 de verificação de pesquisa de ortografia do Bing](./media/luis-tutorial-bing-spellcheck/portal-search.png)

4. Selecione o serviço. 

5. É apresentado um painel de informações à direita que contém informações, incluindo o aviso Legal. Selecione **criar** para iniciar o processo de criação de subscrição. 

6. No painel seguinte, introduza as definições de serviço. Aguarde que o processo de criação de serviço concluir.

    ![Introduza as definições de serviço](./media/luis-tutorial-bing-spellcheck/subscription-settings.png)

7. Selecione **todos os recursos** sob a **Favoritos** título na navegação do lado esquerdo.

8. Selecione o novo serviço. Jeho typ Je **dos serviços cognitivos** e a localização é **global**. 

9. No painel principal, selecione **chaves** para ver as suas novas chaves.

    ![Obter chaves](./media/luis-tutorial-bing-spellcheck/grab-keys.png)

10. Copie a primeira chave. Só precisa de uma das duas chaves. 

## <a name="using-the-key-in-luis-test-panel"></a>Utilizando a chave no painel de teste de LUIS
Existem dois locais no LUIS para utilizar a chave. A primeira é no [painel de teste](luis-interactive-test.md#view-bing-spell-check-corrections-in-test-panel). A chave não é guardada no LUIS, mas em vez disso, é uma variável de sessão. Tem de definir a chave sempre que desejar que o painel de teste para aplicar o serviço da API de verificação ortográfica do Bing v7 para a expressão. Ver [instruções](luis-interactive-test.md#view-bing-spell-check-corrections-in-test-panel) no painel de teste para definir a chave.

## <a name="adding-the-key-to-the-endpoint-url"></a>Adicionar a chave para o URL de ponto final
A consulta de ponto final tem da chave transmitida os parâmetros de cadeia de caracteres de consulta para cada consulta que pretende aplicar a correção ortográfica. Pode ter um chatbot que chama o LUIS ou pode chamar o ponto de extremidade do LUIS API diretamente. Independentemente de como o ponto final é chamado, cada chamada tem de incluir as informações necessárias para correção ortográfica será funcione corretamente.

O ponto final do URL tem vários valores que precisam ser passados corretamente. A chave da API de verificação ortográfica do Bing v7 é apenas mais um deles. Tem de definir o **verificação ortográfica** parâmetro para true e tem de definir o valor de **bing-ortográfica-check-subscription-key** para o valor da chave:

`https://{region}.api.cognitive.microsoft.com/luis/v2.0/apps/{appID}?subscription-key={luisKey}&spellCheck=**true**&bing-spell-check-subscription-key=**{bingKey}**&verbose=true&timezoneOffset=0&q={utterance}`

## <a name="send-misspelled-utterance-to-luis"></a>Enviar a expressão com erros ortográficos para LUIS
1. Num browser, copie a cadeia de caracteres anterior e substitua a `region`, `appId`, `luisKey`, e `bingKey` pelos seus próprios valores. Certifique-se de utilizar a região de ponto final, se for diferente da sua publicação [região](luis-reference-regions.md).

2. Adicione uma expressão com erros ortográficos, como "até que ponto é o mountainn?". Em inglês, `mountain`, com um `n`, é a ortografia correta. 

3. Selecione enter para enviar a consulta para o LUIS.

4. LUIS responde com um resultado JSON para `How far is the mountain?`. Se a API de verificação ortográfica do Bing v7 detectar um problema de ortografia, o `query` campo na resposta JSON da aplicação LUIS contém a consulta original e o `alteredQuery` campo contém a consulta corrigida enviada para o LUIS.

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
Se não quiser utilizar o serviço da API de verificação ortográfica do Bing v7, pode Etiquetar expressões que têm erros de ortografia, de modo a que o LUIS pode aprender a ortografia correta, bem como erros de digitação. Esta opção requer maior esforço de etiquetagem que usar um corretor ortográfico.

## <a name="publishing-page"></a>Página de publicação
O [publicação](luis-how-to-publish-app.md) página tem uma **corretor ortográfico Bing ativar** caixa de verificação. Esta é uma conveniência para criar a chave e compreender como o URL de ponto final é alterado. Ainda terá de utilizar os parâmetros de ponto final correto para ter corrigida em cada ocorrência de pronunciação de ortografia. 

> [!div class="nextstepaction"]
> [Saiba mais sobre expressões de exemplo](luis-how-to-add-example-utterances.md)
