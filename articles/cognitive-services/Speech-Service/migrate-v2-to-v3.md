---
title: Migrar de v2 para v3 REST API - Serviço de fala
titleSuffix: Azure Cognitive Services
description: Em comparação com v2, a nova versão API v3 contém um conjunto de pequenas alterações de rutura. Este documento ajudará a migrar para a nova versão principal num instante.
services: cognitive-services
author: bexxx
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: rbeckers
ms.custom: devx-track-csharp
ms.openlocfilehash: dd1dae963781cc0caacc25938e700a4c70a1f51a
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/05/2020
ms.locfileid: "96738026"
---
# <a name="migration-from-v20-to-v30-of-speech-to-text-rest-api"></a>Migração de v2.0 para v3.0 de discurso para texto REST API

A versão v3 do discurso REST API melhora em relação à versão anterior da API no que diz respeito à fiabilidade e facilidade de utilização. O layout API alinha-se mais de perto com outras APIs de Azure ou Serviços Cognitivos. Isto ajuda-o a aplicar as suas habilidades existentes ao usar a nossa API de fala.

A visão geral da API está disponível como [documento Swagger](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0). Isto é ideal para lhe dar uma visão geral da API e testar a nova API.

Estamos a fornecer amostras para O C e Python. Para transcrições de lote, encontrará as amostras no [repositório de amostras GitHub](https://aka.ms/csspeech/samples) dentro do `samples/batch` subdiretório.

## <a name="forward-compatibility"></a>Compatibilidade a prazo

Para garantir uma migração suave para a V3, todas as entidades da V2 podem também ser encontradas na V3 API com a mesma identidade. Se houver uma alteração do esquema de resultados (por exemplo, transcrições), as respostas para um GET na versão V3 da API estarão no esquema v3. Se executar o GET na versão V2 da API, o esquema de resultados estará no formato V2. As entidades recém-criadas na V3 **não estão** disponíveis na V2.

## <a name="breaking-changes"></a>Alterações interruptivas

A lista de alterações de rutura foi classificada pela magnitude das alterações necessárias para se adaptar. Há apenas algumas alterações que requerem uma alteração não trivial no código de chamada. A maioria das mudanças requer rebatizadores simples. O tempo que as equipas demoraram a migrar de V2 para V3 variaram entre algumas horas e alguns dias. No entanto, os benefícios de uma maior estabilidade, de um código mais simples, de respostas mais rápidas rapidamente compensaram o investimento. 

### <a name="host-name-changes"></a>Alterações no nome do anfitrião

Os nomes dos anfitriões mudaram de {region}.cris.ai para {region}.api.cognitive.microsoft.com. Nesta mudança, os caminhos já não contêm "api/" porque faz parte do nome de hospedeiro. Consulte o [documento Swagger](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0) para obter uma descrição completa das regiões e caminhos.

### <a name="identity-of-an-entity"></a>Identidade de uma entidade

A propriedade `id` foi substituída `self` por. Em v2, um utilizador da API tinha de saber como estão a ser criados os nossos caminhos na API. Isto não era extensível e exigia trabalho desnecessário do utilizador. A propriedade `id` (uuid) é substituída por `self` (string), que é a localização da entidade (URL). O valor ainda é único entre todas as suas entidades. Se `id` for armazenado como uma cadeia no seu código, um simples renomeão é suficiente para suportar o novo esquema. Pode agora utilizar o `self` conteúdo como URL para todas as chamadas REST para a sua entidade (GET, PATCH, DELETE).

Se a entidade tiver uma funcionalidade adicional disponível sob outros caminhos, estão listados em `links` . Um bom exemplo é uma transcrição, que tem um método separado `GET` para o conteúdo da transcrição.

v2 transcrição:

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

transcrição v3:

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

Dependendo da implementação do seu cliente, pode não ser suficiente para mudar o nome da propriedade. Recomendamos aproveitar a utilização dos valores devolvidos `self` e `links` como os urls-alvo do seu descanso, e não gerar os caminhos no seu cliente. Ao utilizar os URLs devolvidos, pode ter a certeza de que futuras alterações nos caminhos não irão quebrar o código do seu cliente.

### <a name="working-with-collections-of-entities"></a>Trabalhar com coleções de entidades

Anteriormente, a V2 API devolveu todas as entidades disponíveis numa resposta. Para permitir um controlo mais fino sobre o tamanho de resposta esperado, em v3 todas as respostas das coleções são paginadas. Tem controlo sobre a contagem de entidades devolvidas e a compensação da página. Este comportamento torna fácil prever o tempo de funcionamento do processador de resposta e é consistente com outras APIs do Azure.

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

A propriedade `values` contém um subconjunto das entidades de recolha disponíveis. A contagem e a compensação podem ser controladas utilizando os parâmetros de consulta `skip` e `top` . Quando `@nextLink` não é nulo, há mais dados disponíveis e o próximo lote de dados pode ser recuperado fazendo um GET on `$.@nextLink` .

Esta alteração requer a `GET` chamada para a recolha em loop até que todos os elementos tenham sido devolvidos.

### <a name="creating-transcriptions"></a>Criação de transcrições

Uma descrição detalhada sobre como criar transcrição pode ser encontrada na [transcrição do Lote Como-a.](./batch-transcription.md)

A criação de transcrições foi alterada em v3 para permitir a definição explícita de opções específicas de transcrição. Todas as propriedades de configuração (opcional) podem agora ser definidas na `properties` propriedade.
Também a versão v3 suporta agora vários ficheiros de entrada e, portanto, requer uma lista de URLs e não um único URL como exigido por v2. O nome da propriedade foi renomeado `recordingsUrl` `contentUrls` de. A funcionalidade de analisar o sentimento nas transcrições foi removida na V3. Recomendamos a utilização da [Análise de Texto](https://azure.microsoft.com/en-us/services/cognitive-services/text-analytics/) do Serviço Cognitivo da Microsoft.

O novo `timeToLive` imóvel em baixo `properties` pode ajudar a podar as entidades existentes concluídas. A `timeToLive` especifica uma duração após a qual uma entidade completa será automaticamente eliminada. Fixá-lo a um valor elevado (por `PT12H` exemplo) quando as entidades são continuamente rastreadas, consumidas e eliminadas e, portanto, geralmente processadas muito antes de passarem 12 horas.

v2 transcrição POST pedido corpo:

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

v3 transcrição POST pedido corpo:

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

### <a name="format-of-v3-transcription-results"></a>Formato dos resultados da transcrição v3

O esquema dos resultados da transcrição foi ligeiramente alterado para alinhar com as transcrições criadas por pontos finais em tempo real. Uma descrição aprofundada do novo formato pode ser encontrada na transcrição do [Lote Como-a.](./batch-transcription.md) O esquema do resultado é publicado no nosso [repositório de amostras GitHub](https://aka.ms/csspeech/samples) sob `samples/batch/transcriptionresult_v3.schema.json` .

Os nomes da propriedade são agora de camelo e os valores para canal e alto-falante estão usando tipos inteiros. Para alinhar o formato de durações com outras APIs Azure, encontra-se agora em formatos como descrito na ISO 8601.

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

### <a name="getting-the-content-of-entities-and-the-results"></a>Obtenção do conteúdo das entidades e dos resultados

Em v2, as ligações aos ficheiros de entrada ou resultado foram delineadas com o resto dos metadados da entidade. Como uma melhoria na V3, existe uma clara separação entre metadados de entidade, que é devolvido por um GET e `$.self` os detalhes e credenciais para aceder aos ficheiros de resultados. Esta separação ajuda a proteger os dados dos clientes e permite um controlo fino sobre a duração da validade das credenciais.

Na v3, existe um imóvel chamado `files` em links no caso de a entidade expor dados (conjuntos de dados, transcrições, pontos finais, avaliações). Um GET on `$.links.files` devolverá uma lista de ficheiros e URL SAS para aceder ao conteúdo de cada ficheiro. Para controlar a duração da validade dos URLs SAS, o parâmetro de consulta `sasValidityInSeconds` pode ser utilizado para especificar o tempo de vida útil.

v2 transcrição:

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

transcrição v3:

```json
{
    "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/9891c965-bb32-4880-b14b-6d44efb158f3",
    "links": {
      "files": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/9891c965-bb32-4880-b14b-6d44efb158f3/files"
    } 
}
```

Em seguida, um GET on `$.links.files` resultaria em:

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

`kind`Indica o formato de conteúdo do ficheiro. Para transcrições, os ficheiros `TranscriptionReport` são o resumo do trabalho e ficheiros do tipo são o resultado do trabalho em `Transcription` si.

### <a name="customizing-models"></a>Modelos de personalização

Antes da V3, havia uma distinção entre um "modelo acústico" e um "modelo linguístico" quando um modelo estava a ser treinado. Esta distinção resultou na necessidade de especificar vários modelos ao criar pontos finais ou transcrições. Para simplificar este processo para um chamador, removemos as diferenças e tornamos todos dependentes do conteúdo dos conjuntos de dados que estão sendo usados para a formação de modelos. Com esta alteração, a criação do modelo suporta agora conjuntos de dados mistos (dados linguísticos e dados acústicos). Pontos finais e transcrições agora requerem apenas um modelo.

Com esta alteração, a necessidade de um `kind` in the POST foi removida e a lata agora pode conter `datasets[]` vários conjuntos de dados dos mesmos tipos ou mistos.

Para melhorar os resultados de um modelo treinado, os dados acústicos são automaticamente utilizados internamente para a formação linguística. Em geral, os modelos criados através da API v3 fornecem resultados mais precisos do que os modelos criados com a API v2.

### <a name="retrieving-base-and-custom-models"></a>Recuperação de bases e modelos personalizados

De forma a simplificar a obtenção dos modelos disponíveis, a V3 separou as coleções de "modelos base" dos "modelos personalizados" do cliente. As duas rotas são agora `GET /speechtotext/v3.0/models/base` `GET /speechtotext/v3.0/models/` e.

Anteriormente, todos os modelos foram devolvidos juntos numa única resposta.

### <a name="name-of-an-entity"></a>Nome de uma entidade

O nome da propriedade `name` é renomeado para `displayName` . Isto está alinhado com outras AZure APIs para não indicar propriedades de identidade. O valor deste imóvel não deve ser único e pode ser alterado após a criação de entidades com um `PATCH` .

v2 transcrição:

```json
{
    "name": "Transcription using locale en-US"
}
```

transcrição v3:

```json
{
    "displayName": "Transcription using locale en-US"
}
```

### <a name="accessing-referenced-entities"></a>Acesso a entidades referenciadas

Na V2 as entidades referenciadas sempre foram delineadas, por exemplo, os modelos usados de um ponto final. A nidificação das entidades resultou em grandes respostas e os consumidores raramente consumiam o conteúdo aninhado. Para reduzir o tamanho da resposta e melhorar o desempenho de todos os utilizadores da API, as entidades referenciadas já não estão inendo na resposta. Em vez disso, está a ser utilizada uma referência à outra entidade, que pode ser diretamente utilizada. Esta referência pode ser usada para um subsequente `GET` (é um URL também), seguindo o mesmo padrão que o `self` link.

v2 transcrição:

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

transcrição v3:

```json
{
  "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/9891c965-bb32-4880-b14b-6d44efb158f3",
  "model": {
    "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/models/021a72d0-54c4-43d3-8254-27336ead9037"
  }
}
```

No caso de necessitar de consumir os detalhes de um modelo referenciado, como mostrado no exemplo acima, simplificar a emissão de um GET em `$.model.self` .

### <a name="retrieving-endpoint-logs"></a>Recuperação de registos de pontos finais

A versão v2 do serviço suportava registar as respostas dos pontos finais. Para recuperar os resultados de um ponto final com v2, foi preciso criar uma "exportação de dados", que representasse uma imagem instantânea dos resultados definidos por um intervalo de tempo. O processo de exportação de lotes de dados tornou-se inflexível. A API v3 dá acesso a cada ficheiro individual e permite a iteração através deles.

Um ponto final v3 com sucesso:

```json
{
  "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/endpoints/afa0669c-a01e-4693-ae3a-93baf40f26d6",
  "links": {
    "logs": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/endpoints/afa0669c-a01e-4693-ae3a-93baf40f26d6/files/logs" 
  }
}
```

Resposta do `$.links.logs` GET:

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

A paginação para registos de pontos finais funciona semelhante a todas as outras coleções, exceto que não é possível especificar qualquer compensação. Devido à grande quantidade de dados disponíveis, a paginação orientada pelo servidor teve de ser implementada.

Em v3, cada registo de ponto final pode ser eliminado individualmente, emitindo um DELETE no `self` ficheiro ou utilizando o DELETE em `$.links.logs` . Para especificar os dados finais, o parâmetro de consulta `endDate` pode ser adicionado ao pedido.

### <a name="using-custom-properties"></a>Utilização de propriedades "personalizadas"

Para separar as propriedades personalizadas das propriedades de configuração opcional, todas as propriedades explicitamente nomeadas estão agora localizadas na `properties` propriedade e todas as propriedades definidas os chamadores estão agora localizados na `customProperties` propriedade.

v2 entidade de transcrição

```json
{
  "properties": {
    "customerDefinedKey": "value",
    "diarizationEnabled": "False",
    "wordLevelTimestampsEnabled": "False"
  }
}
```

v3 entidade de transcrição

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

Esta alteração também permitiu o uso de tipos corretos em todas as propriedades explicitamente nomeadas `properties` (por exemplo, bool em vez de string).

### <a name="response-headers"></a>Cabeçalhos de resposta

v3 já não devolve o cabeçalho `Operation-Location` para além do cabeçalho `Location` nos pedidos do POST. O valor de ambos os cabeçalhos costumava ser exatamente o mesmo. Agora só `Location` está a ser devolvido.

Uma vez que a nova versão da API é agora gerida pela Azure API management (APIM), os cabeçalhos relacionados com o estrangulamento `X-RateLimit-Limit` `X-RateLimit-Remaining` , e não `X-RateLimit-Reset` estão contidos nos cabeçalhos de resposta.

### <a name="accuracy-tests"></a>Testes de precisão

Os testes de precisão foram renomeados para avaliações porque o novo nome descreve melhor o que representam. Os caminhos noticiosos são, por exemplo, "https://{region}.api.cognitive.microsoft.com/speechtotext/v3.0/evaluations".
