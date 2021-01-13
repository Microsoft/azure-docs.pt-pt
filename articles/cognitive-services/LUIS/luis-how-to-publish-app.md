---
title: Publicar app - LUIS
titleSuffix: Azure Cognitive Services
description: Quando terminar de construir e testar a sua aplicação LUIS ativa, disponibilize-a para a aplicação do seu cliente publicando-a no ponto final.
services: cognitive-services
author: aahill
manager: nitinme
ms.author: aahi
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 01/12/2021
ms.openlocfilehash: 8db0f5fa39c7f489db0e30e98ee2684c74eee7e8
ms.sourcegitcommit: c136985b3733640892fee4d7c557d40665a660af
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/13/2021
ms.locfileid: "98180035"
---
# <a name="publish-your-active-trained-app-to-a-staging-or-production-endpoint"></a>Publique a sua app ativa e treinada para um ponto final de encenação ou produção

Quando terminar de construir, treinar e testar a sua aplicação LUIS ativa, disponibilize-a para a aplicação do seu cliente publicando-a no ponto final.

## <a name="publishing"></a>Publicar
1. Inscreva-se no [portal LUIS](https://www.luis.ai)e selecione o seu recurso **de Subscrição** e **Autoria** para ver as aplicações atribuídas a esse recurso de autoria.
1. Abra a sua aplicação selecionando o seu nome na página **My Apps.**
1. Para publicar no ponto final, **selecione Publicar** no painel superior e direito.

    ![Publique botão em cima, barra de navegação direita](./media/luis-how-to-publish-app/publish-top-nav-bar.png)

1. Selecione as definições para o ponto final de previsão publicado e, em seguida, **selecione Publicar**.

    ![Selecione as definições de publicação e, em seguida, selecione Publicar botão](./media/luis-how-to-publish-app/publish-pop-up.png)

### <a name="publishing-slots"></a>Slots de publicação

Selecione a ranhura correta quando a janela pop-up aparecer:

* Transição
* Produção

Ao utilizar ambas as ranhuras de publicação, isto permite-lhe ter duas versões diferentes da sua aplicação disponíveis nos pontos finais publicados ou a mesma versão em dois pontos finais diferentes.

### <a name="publishing-regions"></a>Regiões editoriais

A aplicação é publicada para todas as regiões associadas à previsão LUIS recursos adicionais no portal LUIS a partir da página **Manage**  ->  **[Azure Resources.](luis-how-to-azure-subscription.md#assign-a-resource-to-an-app)**

Por exemplo, para uma app criada no [www.luis.ai](https://www.luis.ai)– se criar um recurso LUIS em duas regiões, **westus** e **eastus**– e adicioná-las à app como recursos, a app é publicada em ambas as regiões. Para obter mais informações sobre as regiões LUIS, consulte [Regiões.](luis-reference-regions.md)

> [!TIP]
> Existem 3 regiões de autoria. Deve ser autor na região a quem pretende publicar. Se precisar de publicar para todas as regiões, precisa de gerir o seu processo de autoria e o modelo treinado resultante em todas as 3 regiões de autoria.


## <a name="configuring-publish-settings"></a>Configurar configurações de publicação

Depois de selecionar a ranhura, configurar as definições de publicação para:

* Análise de sentimentos
* [Correção ortográfica](luis-tutorial-bing-spellcheck.md)
* Escorva da fala

Depois de publicar, estas definições estão disponíveis para revisão a partir da página de definições de **'Publicar'** da secção **Manage.** Pode alterar as definições com cada publicação. Se cancelar uma publicação, quaisquer alterações que tenha feito durante a publicação também serão canceladas.

### <a name="when-your-app-is-published"></a>Quando a sua aplicação é publicada

Quando a sua aplicação é publicada com sucesso, uma notificação de sucesso aparece no topo do navegador. A notificação também inclui um link para os pontos finais.

Se precisar do URL do ponto final, selecione o link. Também pode chegar aos URLs de ponto final selecionando **Gerir** no menu superior e, em seguida, selecione **Azure Resources** no menu esquerdo.

## <a name="sentiment-analysis"></a>Análise de sentimentos

<a name="enable-sentiment-analysis"></a>

A análise de sentimento permite que a LUIS se integre com [a Text Analytics](https://azure.microsoft.com/services/cognitive-services/text-analytics/) para fornecer análise de sentimento e frase-chave.

Não tem de fornecer uma chave text Analytics e não existe qualquer taxa de faturação para este serviço na sua conta Azure.

Os dados do sentimento são uma pontuação entre 1 e 0 indicando o sentimento positivo (mais próximo de 1) ou negativo (mais próximo de 0) dos dados. O rótulo de sentimento de `positive` , e é por cultura `neutral` `negative` apoiada. Atualmente, apenas o inglês suporta rótulos de sentimento.

Para obter mais informações sobre a resposta do ponto final da JSON com a análise do sentimento, consulte [a análise do Sentimento](luis-reference-prebuilt-sentiment.md)

## <a name="spelling-correction"></a>Correção ortográfica

A API de previsão V3 suporta agora a API Bing Spellcheck. Pode adicionar verificação ortográfica à sua aplicação, incluindo a chave do seu recurso de pesquisa Bing no cabeçalho dos seus pedidos. Pode utilizar um recurso Bing existente se já possuir um, ou [criar um novo](https://portal.azure.com/#create/Microsoft.BingSearch) para utilizar esta funcionalidade. 

|Chave do cabeçalho|Valor do cabeçalho|
|--|--|
|`mkt-bing-spell-check-key`|Chaves encontradas em **Keys e Lâmina Endpoint** do seu recurso|

Exemplo de previsão de saída para uma consulta mal ortoleta:

```json
{
  "query": "bouk me a fliht to kayro",
  "prediction": {
    "alteredQuery": "book me a flight to cairo",
    "topIntent": "book a flight",
    "intents": {
      "book a flight": {
        "score": 0.9480589
      }
      "None": {
        "score": 0.0332136229
      }
    },
    "entities": {}
  }
}
```

As correções à ortografia são feitas antes da previsão da expressão do utilizador LUIS. Pode ver qualquer alteração à expressão original, incluindo a ortografia, na resposta.

## <a name="speech-priming"></a>Escorva da fala

O priming da fala é o processo de utilização do envio do modelo LUIS aos serviços de fala antes da conversão do texto para a fala. Isto permite que o serviço de fala forneça conversão de fala com mais precisão para o seu modelo. Isto permite que os pedidos e respostas do bot Speech e LUIS numa chamada, fazendo uma chamada de discurso e recebendo de volta uma resposta LUIS. Proporciona menos latência em geral.

## <a name="next-steps"></a>Passos seguintes

* Consulte [As teclas](./luis-how-to-azure-subscription.md) de gestão para adicionar teclas à chave de subscrição Azure ao LUIS e como definir a tecla Bing Spell Check e incluir todas as intenções nos resultados.
* Consulte [Train e teste a sua aplicação](luis-interactive-test.md) para obter instruções sobre como testar a sua aplicação publicada na consola de teste.

