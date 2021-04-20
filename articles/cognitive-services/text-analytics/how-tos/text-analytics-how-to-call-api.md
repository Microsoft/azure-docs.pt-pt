---
title: Chamar a API de Análise de Texto
titleSuffix: Azure Cognitive Services
description: Este artigo explica como pode chamar o Azure Cognitive Services Text Analytics API e o Carteiro.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 03/01/2021
ms.author: aahi
ms.custom: references_regions
ms.openlocfilehash: 5790c7c62b9d97df9683773170301b6e09a47667
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/19/2021
ms.locfileid: "107728487"
---
# <a name="how-to-call-the-text-analytics-rest-api"></a>Como chamar a API de Sms Analytics REST

Neste artigo, utilizamos a API E o [Carteiro](https://www.postman.com/downloads/) Text Analytics REST para demonstrar conceitos-chave. A API fornece vários pontos finais sincronizados e assíncronos para a utilização das características do serviço. 

## <a name="create-a-text-analytics-resource"></a>Criar um recurso de Análise de Texto

> [!NOTE]
> * Necessitará de um recurso Text Analytics utilizando um [nível de preços](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/) Standard (S) se quiser utilizar os `/analyze` pontos finais ou `/health` finais. O `/analyze` ponto final está incluído no seu [nível de preços.](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/)

Antes de utilizar a API text Analytics, terá de criar um recurso Azure com uma chave e ponto final para as suas aplicações. 

1.  Primeiro, vá ao [portal Azure](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics) e crie um novo recurso Text Analytics, se ainda não o tiver. Escolha um [nível de preços](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/).

2.  Selecione a região que pretende utilizar para o seu ponto final.  Por favor, note que os `/analyze` `/health` pontos finais estão disponíveis apenas nas seguintes regiões: West US 2, East US 2, Central US, North Europe e West Europe.

3.  Crie o recurso Text Analytics e vá para a "lâmina de teclas e ponto final" no lado esquerdo da página. Copie a chave a ser usada mais tarde quando ligar para as APIs. Vais acrescentar isto mais tarde como um valor para o `Ocp-Apim-Subscription-Key` cabeçalho.

4. Para verificar o número de registos de texto enviados através do seu recurso Text Analytics:

    1. Navegue para o seu recurso Text Analytics no portal Azure. 
    2. Clique **em Métricas,** localizadas em **Monitorização** no menu de navegação à esquerda. 
    3. Selecione *registos de texto processados* na caixa de entrega **para métrica**.
    
Um disco de texto é de 1000 caracteres.

## <a name="change-your-pricing-tier"></a>Altere o seu nível de preços 

Se tiver um recurso de Análise de Texto existente utilizando o nível de preços S0-S4, deverá atualizá-lo para utilizar o [nível de preços](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/)Standard (S). Os níveis de preços S0-S4 serão retirados. Para atualizar os preços do seu recurso:

1. Navegue para o seu recurso Text Analytics no [portal Azure](https://portal.azure.com/).
2. Selecione **o nível de preços** no menu de navegação à esquerda. Estará abaixo **da gestão de recursos.** 
3. Escolha o nível de preços Standard (S). Em seguida, clique em **Selecionar**.

Também pode criar um novo recurso Text Analytics com o nível de preços Standard (S) e migrar as suas aplicações para usar as credenciais para o novo recurso. 

## <a name="using-the-api-synchronously"></a>Usando a API sincronizadamente

Pode ligar para o Text Analytics de forma sincronizada (para cenários de baixa latência). Tem de ligar para cada API (recurso) separadamente quando utilizar a API sincronizada. Se precisar de ligar para várias funcionalidades, consulte a secção abaixo sobre como ligar para o Text Analytics de forma assíncronal. 

## <a name="using-the-api-asynchronously"></a>Usando a API assíncronea

A partir de v3.1-pré-visualização.3, a API text Analytics fornece dois pontos finais assíncronos: 

* O `/analyze` ponto final para Análise de Texto permite-lhe analisar o mesmo conjunto de documentos de texto com múltiplas funcionalidades de análise de texto numa chamada API. Anteriormente, para utilizar várias funcionalidades, precisaria de fazer chamadas API separadas para cada operação. Considere esta capacidade quando necessitar de analisar grandes conjuntos de documentos com mais de uma funcionalidade de Text Analytics.

* O `/health` ponto final para a Análise de Texto para a saúde, que pode extrair e rotular informações médicas relevantes a partir de documentos clínicos.  

Por favor, note que os pontos finais /analise e /saúde só estão disponíveis nas seguintes regiões: West US 2, East US 2, Central US, North Europe e West Europe.

Consulte a tabela abaixo para ver quais as características que podem ser utilizadas assíncroneamente. Note que apenas algumas funcionalidades podem ser chamadas do `/analyze` ponto final. 

| Funcionalidade | Synchronous (Síncrono) | Assíncrono |
|--|--|--|
| Deteção de idioma | ✔ |  |
| Análise de sentimentos | ✔ |  |
| Mineração de opinião | ✔ |  |
| Extração de expressões-chave | ✔ | ✔* |
| Reconhecimento de Entidades Nomeadas (incluindo PII e PHI) | ✔ | ✔* |
| Ligação de entidades | ✔ | ✔* |
| Análise de texto para a saúde (recipiente) | ✔ |  |
| Análise de Texto para a saúde (API) |  | ✔  |

`*` - Chamado assíncronosamente através do `/analyze` ponto final.


[!INCLUDE [text-analytics-api-references](../includes/text-analytics-api-references.md)]

<a name="json-schema"></a>

## <a name="api-request-formats"></a>Formatos de pedido da API

Pode enviar chamadas sincronizadas e assíncronos para a API de Análise de Texto.

#### <a name="synchronous"></a>[Synchronous (Síncrono)](#tab/synchronous)

### <a name="synchronous-requests"></a>Pedidos sincronizados

O formato para pedidos de API é o mesmo para todas as operações sincronizadas. Os documentos são apresentados num objeto JSON como texto cru e não estruturado. XML não é suportado. O esquema JSON consiste nos elementos descritos abaixo.

| Elemento | Valores válidos | Necessário? | Utilização |
|---------|--------------|-----------|-------|
|`id` |O tipo de dados é string, mas na prática os IDs de documento tendem a ser inteiros. | Necessário | O sistema utiliza os IDs que fornece para estruturar a saída. Códigos linguísticos, frases-chave e pontuações de sentimento são gerados para cada ID no pedido.|
|`text` | Texto cru não estruturado, até 5.120 caracteres. | Necessário | Para a deteção de idiomas, o texto pode ser expresso em qualquer língua. Para análise de sentimentos, extração de frases-chave e identificação de entidades, o texto deve estar numa [língua suportada](../language-support.md). |
|`language` | Código [ISO 639-1](https://en.wikipedia.org/wiki/List_of_ISO_639-1_codes) de 2 caracteres para uma [língua suportada](../language-support.md) | Varia | Necessário para a análise de sentimentos, extração de frases-chave e ligação de entidades; opcional para a deteção de linguagem. Não há erro se o excluirmos, mas a análise é enfraquecida sem ela. O código linguístico deve corresponder ao `text` que fornece. |

Segue-se um exemplo de um pedido de API para os pontos finais sincronizados de Text Analytics. 

```json
{
  "documents": [
    {
      "language": "en",
      "id": "1",
      "text": "Sample text to be sent to the text analytics api."
    }
  ]
}
```

#### <a name="asynchronous"></a>[Assíncrono](#tab/asynchronous)

### <a name="asynchronous-requests-to-the-analyze-endpoint"></a>Pedidos assíncronos para o `/analyze` ponto final

> [!NOTE]
> O último pré-relanço da biblioteca de clientes Text Analytics permite-lhe ligar para operações de Análise Assíncronica utilizando um objeto cliente. Pode encontrar exemplos no GitHub:
* [C#](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics)
* [Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics/)
* [Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/textanalytics/azure-ai-textanalytics)

O `/analyze` ponto final permite-lhe escolher qual das funcionalidades de Análise de Texto suportadas que pretende utilizar numa única chamada API. Este ponto final suporta atualmente:

* Extração de Expressões-Chave 
* Reconhecimento de Entidades Nomeadas (incluindo PII e PHI)
* Ligar à Entidade

| Elemento | Valores válidos | Necessário? | Utilização |
|---------|--------------|-----------|-------|
|`displayName` | String | Opcional | Usado como nome de exibição para o identificador único para o trabalho.|
|`analysisInput` | Inclui o `documents` campo abaixo | Necessário | Contém a informação para os documentos que pretende enviar. |
|`documents` | Inclui os `id` campos e `text` campos abaixo | Necessário | Contém informações para cada documento enviado e o texto em bruto do documento. |
|`id` | String | Necessário | Os IDs que fornece são usados para estruturar a saída. |
|`text` | Texto cru não estruturado, até 125.000 caracteres. | Necessário | Deve estar na língua inglesa, que é a única língua atualmente apoiada. |
|`tasks` | Inclui as seguintes funcionalidades de Análise de Texto: `entityRecognitionTasks` `entityLinkingTasks` , ou `keyPhraseExtractionTasks` `entityRecognitionPiiTasks` . | Necessário | Um ou mais dos recursos de Text Analytics que pretende utilizar. Note que `entityRecognitionPiiTasks` tem um parâmetro opcional que pode ser definido para ou e para a `domain` `pii` `phi` `pii-categories` deteção de tipos de entidades selecionadas. Se o `domain` parâmetro não for especificado, o sistema falha em `pii` . |
|`parameters` | Inclui os `model-version` campos e `stringIndexType` campos abaixo | Necessário | Este campo está incluído nas tarefas de recurso acima escolhidas. Contêm informações sobre a versão modelo que pretende utilizar e o tipo de índice. |
|`model-version` | String | Necessário | Especifique qual a versão do modelo que está a ser chamado de que pretende utilizar.  |
|`stringIndexType` | String | Necessário | Especifique o descodificador de texto que corresponda ao seu ambiente de programação.  Os tipos suportados são `textElement_v8` (predefinido), `unicodeCodePoint` `utf16CodeUnit` . . Consulte o [artigo Text offset](../concepts/text-offsets.md#offsets-in-api-version-31-preview) para obter mais informações.  |
|`domain` | String | Opcional | Só se aplica como parâmetro da `entityRecognitionPiiTasks` tarefa e pode ser definido para ou `pii` `phi` . Não é especificado `pii` se não especificado.  |

```json
{
    "displayName": "My Job",
    "analysisInput": {
        "documents": [
            {
                "id": "doc1",
                "text": "It's incredibly sunny outside! I'm so happy"
            },
            {
                "id": "doc2",
                "text": "Pike place market is my favorite Seattle attraction."
            }
        ]
    },
    "tasks": {
        "entityRecognitionTasks": [
            {
                "parameters": {
                    "model-version": "latest",
                    "stringIndexType": "TextElements_v8"
                }
            }
        ],
        "entityLinkingTasks": [
            {
                "parameters": {
                    "model-version": "latest",
                    "stringIndexType": "TextElements_v8"
                }
            }
        ],
        "keyPhraseExtractionTasks": [{
            "parameters": {
                "model-version": "latest"
            }
        }],
        "entityRecognitionPiiTasks": [{
            "parameters": {
                "model-version": "latest",
                "stringIndexType": "TextElements_v8",
                "domain": "phi",
                "pii-categories":"default"
            }
        }]
    }
}

```

### <a name="asynchronous-requests-to-the-health-endpoint"></a>Pedidos assíncronos para o `/health` ponto final

O formato dos pedidos de API ao Text Analytics para a API de saúde é o mesmo que para o seu recipiente. Os documentos são apresentados num objeto JSON como texto cru e não estruturado. XML não é suportado. O esquema JSON consiste nos elementos descritos abaixo.  Preencha e envie o formulário de [pedido dos Serviços Cognitivos](https://aka.ms/csgate) para solicitar o acesso ao Texto Analytics para pré-visualização do público em saúde. Não será cobrado para Text Analytics para uso de saúde. 

| Elemento | Valores válidos | Necessário? | Utilização |
|---------|--------------|-----------|-------|
|`id` |O tipo de dados é string, mas na prática os IDs de documento tendem a ser inteiros. | Necessário | O sistema utiliza os IDs que fornece para estruturar a saída. |
|`text` | Texto cru não estruturado, até 5.120 caracteres. | Necessário | Note que apenas o texto em inglês está atualmente suportado. |
|`language` | Código [ISO 639-1](https://en.wikipedia.org/wiki/List_of_ISO_639-1_codes) de 2 caracteres para uma [língua suportada](../language-support.md) | Necessário | Apenas `en` está atualmente apoiado. |

Segue-se um exemplo de um pedido da API para o Texto Analytics para os pontos finais de saúde. 

```json
example.json

{
  "documents": [
    {
      "language": "en",
      "id": "1",
      "text": "Subject was administered 100mg remdesivir intravenously over a period of 120 min"
    }
  ]
}
```

---

>[!TIP]
> Consulte o artigo [Data and Rate Limits](../concepts/data-limits.md) para obter informações sobre as taxas e limites de tamanho para o envio de dados para a API de Análise de Texto.


## <a name="set-up-a-request"></a>Configurar um pedido 

No Carteiro (ou noutra ferramenta de teste web da API), adicione o ponto final para a funcionalidade que pretende utilizar. Utilize a tabela abaixo para encontrar o formato de ponto final apropriado e `<your-text-analytics-resource>` substitua-a pelo seu ponto final de recurso. Por exemplo:

`https://my-resource.cognitiveservices.azure.com/text/analytics/v3.0/languages`

#### <a name="synchronous"></a>[Synchronous (Síncrono)](#tab/synchronous)

### <a name="endpoints-for-sending-synchronous-requests"></a>Pontos finais para envio de pedidos sincronizados

| Funcionalidade | Tipo de pedido | Pontos finais de recursos |
|--|--|--|
| Deteção de idioma | POST | `<your-text-analytics-resource>/text/analytics/v3.0/languages` |
| Análise de sentimentos | POST | `<your-text-analytics-resource>/text/analytics/v3.0/sentiment` |
| Mineração de Opinião | POST | `<your-text-analytics-resource>/text/analytics/v3.0/sentiment?opinionMining=true` |
| Extração de expressões-chave | POST | `<your-text-analytics-resource>/text/analytics/v3.0/keyPhrases` |
| Reconhecimento de entidade nomeada - geral | POST | `<your-text-analytics-resource>/text/analytics/v3.0/entities/recognition/general` |
| Reconhecimento de entidade nomeada - PII | POST | `<your-text-analytics-resource>/text/analytics/v3.0/entities/recognition/pii` |
| Reconhecimento de entidade nomeada - PHI | POST |  `<your-text-analytics-resource>/text/analytics/v3.0/entities/recognition/pii?domain=phi` |

#### <a name="asynchronous"></a>[Assíncrono](#tab/asynchronous)

### <a name="endpoints-for-sending-asynchronous-requests-to-the-analyze-endpoint"></a>Pontos finais para envio de pedidos assíncronos para o `/analyze` ponto final

| Funcionalidade | Tipo de pedido | Pontos finais de recursos |
|--|--|--|
| Submeter trabalho de análise | POST | `https://<your-text-analytics-resource>/text/analytics/v3.1-preview.4/analyze` |
| Obtenha estado de análise e resultados | GET | `https://<your-text-analytics-resource>/text/analytics/v3.1-preview.4/analyze/jobs/<Operation-Location>` |

### <a name="endpoints-for-sending-asynchronous-requests-to-the-health-endpoint"></a>Pontos finais para envio de pedidos assíncronos para o `/health` ponto final

| Funcionalidade | Tipo de pedido | Pontos finais de recursos |
|--|--|--|
| Enviar Análise de Texto para trabalho de saúde  | POST | `https://<your-text-analytics-resource>/text/analytics/v3.1-preview.4/entities/health/jobs` |
| Obtenha o estado de trabalho e os resultados | GET | `https://<your-text-analytics-resource>/text/analytics/v3.1-preview.4/entities/health/jobs/<Operation-Location>` |
| Cancelar trabalho | DELETE | `https://<your-text-analytics-resource>/text/analytics/v3.1-preview.4/entities/health/jobs/<Operation-Location>` |

--- 

Depois de ter o seu ponto final, no Carteiro (ou noutra ferramenta de teste web API):

1. Escolha o tipo de pedido para a funcionalidade que pretende utilizar.
2. Cole no ponto final do funcionamento adequado que pretende da tabela acima.
3. Desa estação os três cabeçalhos de pedido:

   + `Ocp-Apim-Subscription-Key`: a sua chave de acesso, obtida a partir do portal Azure
   + `Content-Type`: aplicação/json
   + `Accept`: aplicação/json

    Se estiver a usar o Carteiro, o seu pedido deve parecer semelhante à imagem que se segue, assumindo um `/keyPhrases` ponto final.
    
    ![Solicitar screenshot com ponto final e cabeçalhos](../media/postman-request-keyphrase-1.png)
    
4. Escolha **cru** para o formato do **Corpo**
    
    ![Solicitar screenshot com configurações do corpo](../media/postman-request-body-raw.png)

5. Cole em alguns documentos JSON num formato válido. Utilize os exemplos na secção de formulário de pedido da **API** acima, e para mais informações e exemplos, consulte os tópicos abaixo:

      + [Deteção linguística](text-analytics-how-to-language-detection.md)
      + [Extração de frase-chave](text-analytics-how-to-keyword-extraction.md)
      + [Análise de sentimentos](text-analytics-how-to-sentiment-analysis.md)
      + [Reconhecimento de entidades](text-analytics-how-to-entity-linking.md)

## <a name="send-the-request"></a>Enviar o pedido

Submeta o pedido da API. Se esboçar a chamada para um ponto final sincronizado, a resposta será exibida imediatamente, como um único documento JSON, com um item para cada documento ID fornecido no pedido.

Se fez a chamada para os `/analyze` pontos finais assíncronos ou `/health` finais, verifique se recebeu um código de resposta 202. terá de obter a resposta para ver os resultados:

1. Na resposta da API, encontre o `Operation-Location` do cabeçalho, que identifica o trabalho que enviou para a API. 
2. Crie um pedido GET para o ponto final que usou. consulte o [quadro acima](#set-up-a-request) para o formato endpoint, e reveja a documentação de referência da [API](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-3/operations/AnalyzeStatus). Por exemplo:

    `https://my-resource.cognitiveservices.azure.com/text/analytics/v3.1-preview.4/analyze/jobs/<Operation-Location>`

3. Adicione o `Operation-Location` pedido.

4. A resposta será um único documento JSON, com um item para cada documento ID fornecido no pedido.

Por favor, note que tanto para assíncronos `/analyze` como `/health` para operações, os resultados do pedido GET no passo 2 acima estão disponíveis por 24 horas a partir do momento em que o trabalho foi criado.  Este tempo é indicado pelo `expirationDateTime` valor na resposta GET.  Após este período de tempo, os resultados são purgados e já não estão disponíveis para recuperação.    

## <a name="example-api-responses"></a>Exemplo respostas da API
 
# <a name="synchronous"></a>[Synchronous (Síncrono)](#tab/synchronous)

### <a name="example-responses-for-synchronous-operation"></a>Respostas de exemplo para operação sincronizada

As respostas sincronizadas do ponto final variam consoante o ponto final que utilizar. Consulte os seguintes artigos, por exemplo, respostas.

+ [Deteção linguística](text-analytics-how-to-language-detection.md#step-3-view-the-results)
+ [Extração de frase-chave](text-analytics-how-to-keyword-extraction.md#step-3-view-results)
+ [Análise de sentimentos](text-analytics-how-to-sentiment-analysis.md#view-the-results)
+ [Reconhecimento de entidades](text-analytics-how-to-entity-linking.md#view-results)

# <a name="asynchronous"></a>[Assíncrono](#tab/asynchronous)

### <a name="example-responses-for-asynchronous-operations"></a>Respostas de exemplo para operações assíncronos

Se for bem sucedido, o pedido GET ao `/analyze` ponto final devolverá um objeto que contenha as tarefas atribuídas. Por exemplo, `keyPhraseExtractionTasks`. Estas tarefas contêm o objeto de resposta da função de Análise de Texto apropriado. Consulte os seguintes artigos para mais informações.

+ [Extração de frase-chave](text-analytics-how-to-keyword-extraction.md#step-3-view-results)
+ [Reconhecimento de entidades](text-analytics-how-to-entity-linking.md#view-results)
+ [Análise de Texto para a saúde](text-analytics-for-health.md#hosted-asynchronous-web-api-response)

--- 

## <a name="see-also"></a>Ver também

* [Descrição geral da Análise de Texto](../overview.md)
* [Perguntas Mais Frequentes (FAQ)](../text-analytics-resource-faq.md)</br>
* [Página de produto da Análise de Texto](//go.microsoft.com/fwlink/?LinkID=759712)
* [Utilização da biblioteca de clientes Text Analytics](../quickstarts/client-libraries-rest-api.md)
* [Novidades](../whats-new.md)
