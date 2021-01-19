---
title: Migrar de v2 para v3 REST API - Serviço de fala
titleSuffix: Azure Cognitive Services
description: Este documento ajuda os desenvolvedores a migrar código de v2 para v3 na API dos serviços de fala para texto.
services: cognitive-services
author: bexxx
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: rbeckers
ms.custom: devx-track-csharp
ms.openlocfilehash: 9c8016b566db8be1b7f5c5ddb8d92123d6673db5
ms.sourcegitcommit: 9d9221ba4bfdf8d8294cf56e12344ed05be82843
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/19/2021
ms.locfileid: "98569849"
---
# <a name="migrate-code-from-v20-to-v30-of-the-rest-api"></a>Código migrar de v2.0 a v3.0 da API REST

Em comparação com o v2, a versão v3 dos serviços de fala REST API para discurso-texto é mais fiável, mais fácil de usar e mais consistente com APIs para serviços semelhantes. A maioria das equipas pode migrar de V2 para V3 em um ou dois dias.

## <a name="forward-compatibility"></a>Compatibilidade a prazo

Todas as entidades da V2 podem ainda ser encontradas na V3 API com a mesma identidade. Quando o esquema de um resultado tiver mudado, (por exemplo, transcrições), o resultado de um GET na versão v3 da API utiliza o esquema v3. O resultado de um GET na versão v2 da API utiliza o mesmo esquema v2. As entidades recém-criadas na V3 **não** estão   disponíveis em respostas das APIs v2. 

## <a name="migration-steps"></a>Passos de migração

Esta é uma lista resumida de itens que precisa de ter em conta quando se prepara para a migração. Os detalhes são encontrados nos links individuais. Dependendo da sua utilização atual da API, nem todos os passos listados aqui podem ser aplicados. Apenas algumas alterações requerem alterações não triviais no código de chamada. A maioria das alterações requer apenas uma alteração aos nomes dos artigos. 

Alterações gerais: 

1. [Mude o nome do anfitrião](#host-name-changes)

1. [Mude o nome do id da propriedade para si mesmo no seu código cliente](#identity-of-an-entity) 

1. [Alterar código para iterar sobre coleções de entidades](#working-with-collections-of-entities)

1. [Mude o nome da propriedade para exibir Nome no seu código cliente](#name-of-an-entity)

1. [Ajustar a recuperação dos metadados das entidades referenciadas](#accessing-referenced-entities)

1. Se utilizar a transcrição do Lote: 

    * [Ajuste o código para criar transcrições de lotes](#creating-transcriptions) 

    * [Adaptar código ao novo esquema de resultados da transcrição](#format-of-v3-transcription-results)

    * [Ajuste o código para a recuperação dos resultados](#getting-the-content-of-entities-and-the-results)

1. Se utilizar APIs de treino/teste de modelos personalizados: 

    * [Aplicar modificações na formação de modelos personalizados](#customizing-models)

    * [Alterar como os modelos de base e personalizados são recuperados](#retrieving-base-and-custom-models)

    * [Rebatize a precisão do segmento do caminho para avaliações no código do seu cliente](#accuracy-tests)

1. Se utilizar apis pontos finais:

    * [Alterar a forma como os registos de ponto final são recuperados](#retrieving-endpoint-logs)

1. Outras pequenas alterações: 

    * [Passe todas as propriedades personalizadas como propriedades personalizadas em vez de propriedades nos seus pedidos POST](#using-custom-properties)

    * [Leia o local a partir do cabeçalho de resposta Localização em vez de Operação-Localização](#response-headers)

## <a name="breaking-changes"></a>Alterações interruptivas

### <a name="host-name-changes"></a>Alterações no nome do anfitrião

Os nomes dos anfitriões do ponto final mudaram de `{region}.cris.ai` `{region}.api.cognitive.microsoft.com` . Os caminhos para os novos pontos finais já não contêm `api/` porque faz parte do nome de hospedeiro. O [documento Swagger](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0) lista regiões e caminhos válidos.
>[!IMPORTANT]
>Mude o nome de anfitrião `{region}.cris.ai` para onde a `{region}.api.cognitive.microsoft.com` região é a região da sua assinatura de discurso. Remova também `api/` de qualquer caminho no seu código cliente.

### <a name="identity-of-an-entity"></a>Identidade de uma entidade

A propriedade `id` é `self` agora. Em v2, um utilizador da API tinha de saber como estão a ser criados os nossos caminhos na API. Isto não era extensível e exigia trabalho desnecessário do utilizador. A propriedade `id` (uuid) é substituída por `self` (string), que é a localização da entidade (URL). O valor ainda é único entre todas as suas entidades. Se `id` for armazenado como uma cadeia no seu código, um renomeão é suficiente para suportar o novo esquema. Agora pode utilizar o `self` conteúdo como URL para o , e REST chama a sua `GET` `PATCH` `DELETE` entidade.

Se a entidade tiver funcionalidades adicionais disponíveis através de outros caminhos, estão listadas em `links` . O exemplo seguinte para a transcrição mostra um método separado `GET` do conteúdo da transcrição:
>[!IMPORTANT]
>Mude o nome da propriedade `id` para o seu código `self` cliente. Mude o tipo de `uuid` para `string` se necessário. 

**v2 transcrição:**

```json
{
    "id": "9891c965-bb32-4880-b14b-6d44efb158f3",
    "createdDateTime": "2019-01-07T11:34:12Z",
    "lastActionDateTime": "2019-01-07T11:36:07Z",
    "status": "Succeeded",
    "locale": "en-US",
    "name": "Transcription using locale en-US"
}
```

**transcrição v3:**

```json
{
    "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/9891c965-bb32-4880-b14b-6d44efb158f3",
    "createdDateTime": "2019-01-07T11:34:12Z",
    "lastActionDateTime": "2019-01-07T11:36:07Z",
    "status": "Succeeded",
    "locale": "en-US", 
    "displayName": "Transcription using locale en-US",
    "links": {
      "files": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/9891c965-bb32-4880-b14b-6d44efb158f3/files"
    }
}
```

Dependendo da implementação do seu código, pode não ser suficiente para mudar o nome da propriedade. Recomendamos a utilização dos `self` `links` valores devolvidos e valores como urls-alvo das suas chamadas REST, em vez de gerar caminhos no seu cliente. Ao utilizar os URLs devolvidos, pode ter a certeza de que futuras alterações nos caminhos não irão quebrar o código do seu cliente.

### <a name="working-with-collections-of-entities"></a>Trabalhar com coleções de entidades

Anteriormente, a V2 API devolveu todas as entidades disponíveis. Para permitir um controlo mais fino sobre o tamanho de resposta esperado em v3, todos os resultados da recolha são paginados. Tem controlo sobre a contagem de entidades devolvidas e o desfasamento inicial da página. Este comportamento facilita a previsão do tempo de funcionação do processador de resposta.

A forma básica da resposta é a mesma para todas as coleções:

```json
{
    "values": [
        {     
        }
    ],
    "@nextLink": "https://{region}.api.cognitive.microsoft.com/speechtotext/v3.0/{collection}?skip=100&top=100"
}
```

A `values` propriedade contém um subconjunto das entidades de recolha disponíveis. A contagem e a compensação podem ser controladas utilizando os `skip` `top` parâmetros e os parâmetros de consulta. Quando `@nextLink` não `null` está, há mais dados disponíveis e o próximo lote de dados pode ser recuperado fazendo um GET on `$.@nextLink` .

Esta alteração requer a `GET` chamada para a recolha em loop até que todos os elementos tenham sido devolvidos.

>[!IMPORTANT]
>Quando a resposta de um GET `speechtotext/v3.0/{collection}` conter um valor em , continue a emitir até que não esteja definido para recuperar todos `$.@nextLink` os `GETs` `$.@nextLink` `$.@nextLink` elementos dessa coleção.

### <a name="creating-transcriptions"></a>Criação de transcrições

Uma descrição detalhada sobre como criar lotes de transcrições pode ser encontrada na [transcrição do Lote Como-to](./batch-transcription.md).

A API de transcrição v3 permite definir opções de transcrição específicas explicitamente. Todas as propriedades de configuração (opcional) podem agora ser definidas na `properties` propriedade.
A versão v3 também suporta vários ficheiros de entrada, pelo que requer uma lista de URLs em vez de um único URL como v2 fez. O nome da propriedade v2 `recordingsUrl` está agora `contentUrls` em v3. A funcionalidade de analisar o sentimento nas transcrições foi removida em v3. Consulte a [Análise de Texto](https://azure.microsoft.com/en-us/services/cognitive-services/text-analytics/) do Serviço Cognitivo da Microsoft para obter opções de análise de sentimentos.

O novo `timeToLive` imóvel em baixo `properties` pode ajudar a podar as entidades existentes concluídas. A `timeToLive` especifica uma duração após a qual uma entidade completa será eliminada automaticamente. Fixá-lo a um valor elevado (por `PT12H` exemplo) quando as entidades são continuamente rastreadas, consumidas e eliminadas e, portanto, geralmente processadas muito antes de passarem 12 horas.

**v2 transcrição POST pedido corpo:**

```json
{
  "locale": "en-US",
  "name": "Transcription using locale en-US",
  "recordingsUrl": "https://contoso.com/mystoragelocation",
  "properties": {
    "AddDiarization": "False",
    "AddWordLevelTimestamps": "False",
    "PunctuationMode": "DictatedAndAutomatic",
    "ProfanityFilterMode": "Masked"
  }
}
```

**v3 transcrição POST pedido corpo:**

```json
{
  "locale": "en-US",
  "displayName": "Transcription using locale en-US",
  "contentUrls": [
    "https://contoso.com/mystoragelocation",
    "https://contoso.com/myotherstoragelocation"
  ],
  "properties": {
    "diarizationEnabled": false,
    "wordLevelTimestampsEnabled": false,
    "punctuationMode": "DictatedAndAutomatic",
    "profanityFilterMode": "Masked"
  }
}
```
>[!IMPORTANT]
>Mude o nome da propriedade `recordingsUrl` para e passe uma variedade de `contentUrls` urls em vez de um único url. Passe as definições para `diarizationEnabled` ou como em vez de `wordLevelTimestampsEnabled` `bool` `string` .

### <a name="format-of-v3-transcription-results"></a>Formato dos resultados da transcrição v3

O esquema dos resultados da transcrição mudou ligeiramente para alinhar-se com as transcrições criadas por pontos finais em tempo real. Encontre uma descrição aprofundada do novo formato na transcrição do [Lote Como-a-](./batch-transcription.md) O esquema do resultado é publicado no nosso [repositório de amostras GitHub](https://aka.ms/csspeech/samples) sob `samples/batch/transcriptionresult_v3.schema.json` .

Os nomes dos imóveis são agora de camelo e os valores para `channel` e agora usam tipos `speaker` inteiros. O formato para durações agora utiliza a estrutura descrita na ISO 8601, que corresponde à formatação de duração utilizada noutras APIs Azure.

Amostra de um resultado de transcrição v3. As diferenças são descritas nos comentários.

```json
{
  "source": "...",                      // (new in v3) was AudioFileName / AudioFileUrl
  "timestamp": "2020-06-16T09:30:21Z",  // (new in v3)
  "durationInTicks": 41200000,          // (new in v3) was AudioLengthInSeconds
  "duration": "PT4.12S",                // (new in v3)
  "combinedRecognizedPhrases": [        // (new in v3) was CombinedResults
    {
      "channel": 0,                     // (new in v3) was ChannelNumber
      "lexical": "hello world",
      "itn": "hello world",
      "maskedITN": "hello world",
      "display": "Hello world."
    }
  ],
  "recognizedPhrases": [                // (new in v3) was SegmentResults
    {
      "recognitionStatus": "Success",   // 
      "channel": 0,                     // (new in v3) was ChannelNumber
      "offset": "PT0.07S",              // (new in v3) new format, was OffsetInSeconds
      "duration": "PT1.59S",            // (new in v3) new format, was DurationInSeconds
      "offsetInTicks": 700000.0,        // (new in v3) was Offset
      "durationInTicks": 15900000.0,    // (new in v3) was Duration

      // possible transcriptions of the current phrase with confidences
      "nBest": [
        {
          "confidence": 0.898652852,phrase
          "speaker": 1,
          "lexical": "hello world",
          "itn": "hello world",
          "maskedITN": "hello world",
          "display": "Hello world.",

          "words": [
            {
              "word": "hello",
              "offset": "PT0.09S",
              "duration": "PT0.48S",
              "offsetInTicks": 900000.0,
              "durationInTicks": 4800000.0,
              "confidence": 0.987572
            },
            {
              "word": "world",
              "offset": "PT0.59S",
              "duration": "PT0.16S",
              "offsetInTicks": 5900000.0,
              "durationInTicks": 1600000.0,
              "confidence": 0.906032
            }
          ]
        }
      ]
    }
  ]
}
```
>[!IMPORTANT]
>Deserialize o resultado da transcrição para o novo tipo, como mostrado acima. Em vez de um único ficheiro por canal de áudio, distinguir canais verificando o valor da propriedade de `channel` cada elemento em `recognizedPhrases` . Existe agora um único ficheiro de resultados para cada ficheiro de entrada.


### <a name="getting-the-content-of-entities-and-the-results"></a>Obtenção do conteúdo das entidades e dos resultados

Em v2, as ligações aos ficheiros de entrada ou resultado foram delineadas com o resto dos metadados da entidade. Como uma melhoria na V3, existe uma clara separação entre metadados de entidade (que são devolvidos por um GET on `$.self` ) e os detalhes e credenciais para aceder aos ficheiros de resultados. Esta separação ajuda a proteger os dados do cliente e permite um controlo fino sobre a duração da validade das credenciais.

Em v3, `links` incluir uma subalto chamada `files` caso a entidade exponha dados (conjuntos de dados, transcrições, pontos finais ou avaliações). Um GET on `$.links.files` devolverá uma lista de ficheiros e um URL SAS para aceder ao conteúdo de cada ficheiro. Para controlar a duração da validade dos URLs SAS, o parâmetro de consulta `sasValidityInSeconds` pode ser utilizado para especificar o tempo de vida útil.

**v2 transcrição:**

```json
{
  "id": "9891c965-bb32-4880-b14b-6d44efb158f3",
  "status": "Succeeded",
  "reportFileUrl": "https://contoso.com/report.txt?st=2018-02-09T18%3A07%3A00Z&se=2018-02-10T18%3A07%3A00Z&sp=rl&sv=2017-04-17&sr=b&sig=6c044930-3926-4be4-be76-f728327c53b5",
  "resultsUrls": {
    "channel_0": "https://contoso.com/audiofile1.wav?st=2018-02-09T18%3A07%3A00Z&se=2018-02-10T18%3A07%3A00Z&sp=rl&sv=2017-04-17&sr=b&sig=6c044930-3926-4be4-be76-f72832e6600c",
    "channel_1": "https://contoso.com/audiofile2.wav?st=2018-02-09T18%3A07%3A00Z&se=2018-02-10T18%3A07%3A00Z&sp=rl&sv=2017-04-17&sr=b&sig=3e0163f1-0029-4d4a-988d-3fba7d7c53b5"
  }
}
```

**transcrição v3:**

```json
{
    "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/9891c965-bb32-4880-b14b-6d44efb158f3",
    "links": {
      "files": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/9891c965-bb32-4880-b14b-6d44efb158f3/files"
    } 
}
```

**Um GET on `$.links.files` resultaria em:**

```json
{
  "values": [
    {
      "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/9891c965-bb32-4880-b14b-6d44efb158f3/files/f23e54f5-ed74-4c31-9730-2f1a3ef83ce8",
      "name": "Name",
      "kind": "Transcription",
      "properties": {
        "size": 200
      },
      "createdDateTime": "2020-01-13T08:00:00Z",
      "links": {
        "contentUrl": "https://customspeech-usw.blob.core.windows.net/artifacts/mywavefile1.wav.json?st=2018-02-09T18%3A07%3A00Z&se=2018-02-10T18%3A07%3A00Z&sp=rl&sv=2017-04-17&sr=b&sig=e05d8d56-9675-448b-820c-4318ae64c8d5"
      }
    },
    {
      "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/9891c965-bb32-4880-b14b-6d44efb158f3/files/28bc946b-c251-4a86-84f6-ea0f0a2373ef",
      "name": "Name",
      "kind": "TranscriptionReport",
      "properties": {
        "size": 200
      },
      "createdDateTime": "2020-01-13T08:00:00Z",
      "links": {
        "contentUrl": "https://customspeech-usw.blob.core.windows.net/artifacts/report.json?st=2018-02-09T18%3A07%3A00Z&se=2018-02-10T18%3A07%3A00Z&sp=rl&sv=2017-04-17&sr=b&sig=e05d8d56-9675-448b-820c-4318ae64c8d5"
      }
    }
  ],
  "@nextLink": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/9891c965-bb32-4880-b14b-6d44efb158f3/files?skip=2&top=2"
}
```

A `kind` propriedade indica o formato de conteúdo do ficheiro. Para transcrições, os ficheiros `TranscriptionReport` são o resumo do trabalho e ficheiros do tipo são o resultado do trabalho em `Transcription` si.

>[!IMPORTANT]
>Para obter os resultados das operações, utilize um `GET` `/speechtotext/v3.0/{collection}/{id}/files` on, eles já não estão contidos nas respostas de `GET` on ou `/speechtotext/v3.0/{collection}/{id}` `/speechtotext/v3.0/{collection}` .

### <a name="customizing-models"></a>Modelos de personalização

Antes da V3, havia uma distinção entre um _modelo acústico_ e um _modelo de linguagem_ quando um modelo estava a ser treinado. Esta distinção resultou na necessidade de especificar vários modelos ao criar pontos finais ou transcrições. Para simplificar este processo para um chamador, removemos as diferenças e fizemos com que tudo dependesse do conteúdo dos conjuntos de dados que estão a ser utilizados para a formação de modelos. Com esta alteração, a criação do modelo suporta agora conjuntos de dados mistos (dados linguísticos e dados acústicos). Pontos finais e transcrições agora requerem apenas um modelo.

Com esta alteração, a necessidade de um `kind` na `POST` operação foi removida e a `datasets[]` matriz pode agora conter vários conjuntos de dados dos mesmos tipos ou mistos.

Para melhorar os resultados de um modelo treinado, os dados acústicos são automaticamente utilizados internamente durante o treino linguístico. Em geral, os modelos criados através da API v3 fornecem resultados mais precisos do que os modelos criados com a API v2.

>[!IMPORTANT]
>Para personalizar tanto a parte do modelo acústico como a parte do modelo de linguagem, passe todos os conjuntos de dados de linguagem e acústica necessários `datasets[]` no POST para `/speechtotext/v3.0/models` . Isto irá criar um único modelo com ambas as peças personalizadas.

### <a name="retrieving-base-and-custom-models"></a>Recuperação de bases e modelos personalizados

Para simplificar a obtenção dos modelos disponíveis, a V3 separou as coleções de "modelos base" dos "modelos personalizados" do cliente. As duas rotas são agora `GET /speechtotext/v3.0/models/base` `GET /speechtotext/v3.0/models/` e.

Em v2, todos os modelos foram devolvidos juntos numa única resposta.

>[!IMPORTANT]
>Para obter uma lista de modelos base fornecidos para personalização, use `GET` em `/speechtotext/v3.0/models/base` . Pode encontrar os seus próprios modelos personalizados com um `GET` on `/speechtotext/v3.0/models` .

### <a name="name-of-an-entity"></a>Nome de uma entidade

A `name` propriedade é `displayName` agora. Isto é consistente com outras AZure APIs para não indicar propriedades de identidade. O valor deste imóvel não deve ser único e pode ser alterado após a criação de entidade com uma `PATCH` operação.

**v2 transcrição:**

```json
{
    "name": "Transcription using locale en-US"
}
```

**transcrição v3:**

```json
{
    "displayName": "Transcription using locale en-US"
}
```

>[!IMPORTANT]
>Mude o nome da propriedade `name` para o seu código `displayName` cliente.

### <a name="accessing-referenced-entities"></a>Acesso a entidades referenciadas

Na V2, as entidades referenciadas foram sempre inlineadas, por exemplo, os modelos usados de um ponto final. A nidificação das entidades resultou em grandes respostas e os consumidores raramente consumiam o conteúdo aninhado. Para diminuir o tamanho da resposta e melhorar o desempenho, as entidades referenciadas já não estão inendo na resposta. Em vez disso, uma referência à outra entidade aparece, e pode ser diretamente usada para um subsequente `GET` (é um URL também), seguindo o mesmo padrão que o `self` link.

**v2 transcrição:**

```json
{
  "id": "9891c965-bb32-4880-b14b-6d44efb158f3",
  "models": [
    {
      "id": "827712a5-f942-4997-91c3-7c6cde35600b",
      "modelKind": "Language",
      "lastActionDateTime": "2019-01-07T11:36:07Z",
      "status": "Running",
      "createdDateTime": "2019-01-07T11:34:12Z",
      "locale": "en-US",
      "name": "Acoustic model",
      "description": "Example for an acoustic model",
      "datasets": [
        {
          "id": "702d913a-8ba6-4f66-ad5c-897400b081fb",
          "dataImportKind": "Language",
          "lastActionDateTime": "2019-01-07T11:36:07Z",
          "status": "Succeeded",
          "createdDateTime": "2019-01-07T11:34:12Z",
          "locale": "en-US",
          "name": "Language dataset",
        }
      ]
    },
  ]
}
```

**transcrição v3:**

```json
{
  "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/9891c965-bb32-4880-b14b-6d44efb158f3",
  "model": {
    "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/models/021a72d0-54c4-43d3-8254-27336ead9037"
  }
}
```

Se precisar de consumir os detalhes de um modelo referenciado como mostrado no exemplo acima, basta emitir um GET on `$.model.self` .

>[!IMPORTANT]
>Para recuperar os metadados de entidades referenciadas, emita um GET `$.{referencedEntity}.self` on, por exemplo, para recuperar o modelo de uma transcrição fazer um `GET` on `$.model.self` .


### <a name="retrieving-endpoint-logs"></a>Recuperação de registos de pontos finais

A versão v2 do serviço suportava os resultados do ponto final de registo. Para recuperar os resultados de um ponto final com v2, criaria uma "exportação de dados", que representasse uma imagem instantânea dos resultados definidos por um intervalo de tempo. O processo de exportação de lotes de dados era inflexível. A API v3 dá acesso a cada ficheiro individual e permite a iteração através deles.

**Um ponto final v3 com sucesso:**

```json
{
  "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/endpoints/afa0669c-a01e-4693-ae3a-93baf40f26d6",
  "links": {
    "logs": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/endpoints/afa0669c-a01e-4693-ae3a-93baf40f26d6/files/logs" 
  }
}
```

**Resposta do `$.links.logs` GET:**

```json
{
  "values": [
    {
      "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/endpoints/6d72ad7e-f286-4a6f-b81b-a0532ca6bcaa/files/logs/2019-09-20_080000_3b5f4628-e225-439d-bd27-8804f9eed13f.wav",
      "name": "2019-09-20_080000_3b5f4628-e225-439d-bd27-8804f9eed13f.wav",
      "kind": "Audio",
      "properties": {
        "size": 12345
      },
      "createdDateTime": "2020-01-13T08:00:00Z",
      "links": {
        "contentUrl": "https://customspeech-usw.blob.core.windows.net/artifacts/2019-09-20_080000_3b5f4628-e225-439d-bd27-8804f9eed13f.wav?st=2018-02-09T18%3A07%3A00Z&se=2018-02-10T18%3A07%3A00Z&sp=rl&sv=2017-04-17&sr=b&sig=e05d8d56-9675-448b-820c-4318ae64c8d5"
      }
    }    
  ],
  "@nextLink": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/endpoints/afa0669c-a01e-4693-ae3a-93baf40f26d6/files/logs?top=2&SkipToken=2!188!MDAwMDk1ITZhMjhiMDllLTg0MDYtNDViMi1hMGRkLWFlNzRlOGRhZWJkNi8yMDIwLTA0LTAxLzEyNDY0M182MzI5NGRkMi1mZGYzLTRhZmEtOTA0NC1mODU5ZTcxOWJiYzYud2F2ITAwMDAyOCE5OTk5LTEyLTMxVDIzOjU5OjU5Ljk5OTk5OTlaIQ--"
}
```

A paginação para registos de pontos finais funciona semelhante a todas as outras coleções, exceto que não é possível especificar qualquer compensação. Devido à grande quantidade de dados disponíveis, a paginação é determinada pelo servidor.

Em v3, cada registo de ponto final pode ser eliminado individualmente através da emissão de uma `DELETE` operação num `self` ficheiro ou através da utilização de `DELETE` `$.links.logs` . Para especificar uma data de fim, o parâmetro de consulta `endDate` pode ser adicionado ao pedido.

>[!IMPORTANT]
>Em vez de criar exportações de registos em `/api/speechtotext/v2.0/endpoints/{id}/data` uso para aceder a `/v3.0/endpoints/{id}/files/logs/` ficheiros de registo individualmente. 

### <a name="using-custom-properties"></a>Usando propriedades personalizadas

Para separar as propriedades personalizadas das propriedades de configuração opcional, todas as propriedades explicitamente nomeadas estão agora localizadas na `properties` propriedade e todas as propriedades definidas pelos chamadores estão agora localizadas na `customProperties` propriedade.

**v2 entidade de transcrição:**

```json
{
  "properties": {
    "customerDefinedKey": "value",
    "diarizationEnabled": "False",
    "wordLevelTimestampsEnabled": "False"
  }
}
```

**v3 entidade de transcrição:**

```json
{
  "properties": {
    "diarizationEnabled": false,
    "wordLevelTimestampsEnabled": false 
  },
  "customProperties": {
    "customerDefinedKey": "value"
  }
}
```

Esta alteração também permite utilizar tipos corretos em todas as propriedades explicitamente nomeadas `properties` (por exemplo, boolean em vez de string).

>[!IMPORTANT]
>Passe todas as propriedades personalizadas como `customProperties` em vez de nos seus `properties` `POST` pedidos.

### <a name="response-headers"></a>Cabeçalhos de resposta

v3 já não devolve o `Operation-Location` cabeçalho para além do `Location` cabeçalho sobre `POST` pedidos. O valor de ambos os cabeçalhos na V2 foi o mesmo. Agora só `Location` é devolvido.

Uma vez que a nova versão da API é agora gerida pela Azure API management (APIM), os cabeçalhos relacionados com o estrangulamento `X-RateLimit-Limit` `X-RateLimit-Remaining` , e não `X-RateLimit-Reset` estão contidos nos cabeçalhos de resposta.

>[!IMPORTANT]
>Leia o local do cabeçalho de resposta `Location` em vez de `Operation-Location` . No caso de um código de resposta 429, leia o `Retry-After` valor do cabeçalho em vez de `X-RateLimit-Limit` , ou `X-RateLimit-Remaining` `X-RateLimit-Reset` .


### <a name="accuracy-tests"></a>Testes de precisão

Os testes de precisão foram renomeados para avaliações porque o novo nome descreve melhor o que representam. Os novos caminhos são: `https://{region}.api.cognitive.microsoft.com/speechtotext/v3.0/evaluations` .

>[!IMPORTANT]
>Mude o nome do segmento de caminho `accuracytests` para o seu código `evaluations` cliente.


## <a name="next-steps"></a>Passos seguintes

Examine todas as características destas APIs de REST geralmente utilizadas fornecidas pelos serviços de fala:

* [API REST de conversão de voz em texto](rest-speech-to-text.md)
* [Documento swagger](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0) para v3 da API REST
* Para o código de amostra para efetuar transcrições de lote, consulte o [repositório de amostras GitHub](https://aka.ms/csspeech/samples) no `samples/batch` subdiretório.
