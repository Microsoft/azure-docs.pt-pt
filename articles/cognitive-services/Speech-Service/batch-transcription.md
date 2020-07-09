---
title: O que é transcrição de lote - Serviço de fala
titleSuffix: Azure Cognitive Services
description: A transcrição do lote é ideal se quiser transcrever uma grande quantidade de áudio no armazenamento, como a Azure Blobs. Ao utilizar a API de REST dedicada, pode apontar para ficheiros áudio com uma assinatura de acesso partilhado (SAS) URI e receber assincronamente transcrições.
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/18/2020
ms.author: wolfma
ms.openlocfilehash: 9804992aee318fdc34815bdbe4187144704cd667
ms.sourcegitcommit: 51718f41d36192b9722e278237617f01da1b9b4e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/19/2020
ms.locfileid: "85099766"
---
# <a name="what-is-batch-transcription"></a>O que é transcrição do lote?

A transcrição do lote é um conjunto de operações de API REST que permite transcrever uma grande quantidade de áudio no armazenamento. Pode apontar para ficheiros áudio com uma assinatura de acesso partilhado (SAS) URI e receber assincronamente os resultados da transcrição. Com a nova API v3.0, tem a opção de transcrever um ou mais ficheiros áudio, ou processar um recipiente de armazenamento inteiro.

A transcrição assíncronosa de discurso a texto é apenas uma das características. Pode utilizar a transcrição do lote REST APIs para ligar para os seguintes métodos:



|    Operação de Transcrição de Lote                                             |    Método    |    REST API Call                                   |
|------------------------------------------------------------------------------|--------------|----------------------------------------------------|
|    Cria uma nova transcrição.                                              |    POST      |    speechtotext/v3.0/transcriptions            |
|    Recupera uma lista de transcrições para a subscrição autenticada.    |    GET       |    speechtotext/v3.0/transcriptions            |
|    Obtém uma lista de locais apoiados para transcrições offline.              |    GET       |    speechtotext/v3.0/transcriptions/locales    |
|    Atualiza os detalhes mutáveis da transcrição identificada pelo seu ID.    |    PATCH     |    speechtotext/v3.0/transcriptions/{id}       |
|    Elimina a tarefa de transcrição especificada.                                 |    DELETE    |    speechtotext/v3.0/transcriptions/{id}       |
|    Obtém a transcrição identificada pela identificação dada.                        |    GET       |    speechtotext/v3.0/transcriptions/{id}       |
|    Obtém os ficheiros de resultados da transcrição identificados pela identificação dada.    |    GET       |    speechtotext/v3.0/transcriptions/{id}/files |




Pode rever e testar a API detalhada, que está disponível como [documento Swagger](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0).

Os trabalhos de transcrição de lote são programados com a melhor base de esforço. Atualmente, não há estimativa para quando um emprego muda para o estado de funcionamento. Sob a carga normal do sistema, deve acontecer em minutos. Uma vez no estado de execução, a transcrição real é processada mais rapidamente do que o áudio em tempo real.

Ao lado da API de fácil utilização, não precisa de implementar pontos finais personalizados e não tem quaisquer requisitos de concordância a observar.

## <a name="prerequisites"></a>Pré-requisitos

### <a name="subscription-key"></a>Chave de Subscrição

Tal como acontece com todas as funcionalidades do serviço Speech, cria uma chave de subscrição a partir do [portal Azure](https://portal.azure.com) seguindo o nosso [guia Iniciar .](get-started.md)

>[!NOTE]
> É necessária uma subscrição padrão (S0) para o serviço de fala para a transcrição do lote. As teclas de subscrição gratuitas (F0) não funcionam. Para mais informações, consulte [os preços e os limites.](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)

### <a name="custom-models"></a>Modelos personalizados

Se pretende personalizar modelos, siga os passos na [personalização acústica](how-to-customize-acoustic-models.md) e [personalização linguística.](how-to-customize-language-model.md) Para utilizar os modelos criados na transcrição do lote, precisa da localização do modelo. Pode recuperar a localização do modelo quando inspeciona os detalhes do modelo `self` (propriedade). Um ponto final personalizado não é *necessário* para o serviço de transcrição do lote.

## <a name="the-batch-transcription-api"></a>A API de Transcrição de Lote

### <a name="supported-formats"></a>Formatos suportados

A API de Transcrição de Lote suporta os seguintes formatos:

| Formato | Codec | Bitrate | Taxa de amostra                     |
|--------|-------|---------|---------------------------------|
| WAV    | PCM   | 16 bits  | 8 kHz ou 16 kHz, mono ou estéreo |
| MP3    | PCM   | 16 bits  | 8 kHz ou 16 kHz, mono ou estéreo |
| OGG    | OPUS  | 16 bits  | 8 kHz ou 16 kHz, mono ou estéreo |

Para os fluxos de áudio estéreo, os canais esquerdo e direito são divididos durante a transcrição. Para cada canal, está a ser criado um ficheiro de resultados JSON. Os tempos gerados por expressão permitem ao desenvolvedor criar uma transcrição final ordenada.

### <a name="configuration"></a>Configuração

Os parâmetros de configuração são fornecidos como JSON (um ou mais ficheiros individuais):

```json
{
  "contentUrls": [
    "<URL to an audio file to transcribe>",
  ],
  "properties": {
    "wordLevelTimestampsEnabled": true
  },
  "locale": "en-US",
  "displayName": "Transcription of file using default model for en-US"
}
```

Os parâmetros de configuração são fornecidos como JSON (processamento de um recipiente de armazenamento inteiro):

```json
{
  "contentContainerUrl": "<SAS URL to the Azure blob container to transcribe>",
  "properties": {
    "wordLevelTimestampsEnabled": true
  },
  "locale": "en-US",
  "displayName": "Transcription of container using default model for en-US"
}
```

Para utilizar modelos treinados personalizados em transcrições de lotes, podem ser referenciados como mostrado abaixo:

```json
{
  "contentUrls": [
    "<URL to an audio file to transcribe>",
  ],
  "properties": {
    "wordLevelTimestampsEnabled": true
  },
  "locale": "en-US",
  "model": {
    "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/models/{id}"
  },
  "displayName": "Transcription of file using default model for en-US"
}
```


### <a name="configuration-properties"></a>Propriedades de configuração

Utilize estas propriedades opcionais para configurar a transcrição:

:::row:::
   :::column span="1":::
      **Parâmetro**
   :::column-end:::
   :::column span="2":::
      **Descrição**
:::row-end:::
:::row:::
   :::column span="1":::
      `profanityFilterMode`
   :::column-end:::
   :::column span="2":::
      Especifica como lidar com a profanação nos resultados do reconhecimento. Os valores aceites são `None` para desativar a filtragem da profanação, `Masked` para substituir a profanação por asteriscos, `Removed` para remover toda a profanação do resultado, ou `Tags` para adicionar etiquetas de "profanação". A predefinição é `Masked`.
:::row-end:::
:::row:::
   :::column span="1":::
      `punctuationMode`
   :::column-end:::
   :::column span="2":::
      Especifica como lidar com a pontuação nos resultados do reconhecimento. Os valores aceites são `None` para desativar a pontuação, `Dictated` para implicar uma pontuação explícita (falada), `Automatic` para deixar o descodificador lidar com a pontuação, ou `DictatedAndAutomatic` para usar a pontuação ditada e automática. A predefinição é `DictatedAndAutomatic`.
:::row-end:::
:::row:::
   :::column span="1":::
      `wordLevelTimestampsEnabled`
   :::column-end:::
   :::column span="2":::
      Especifica se os tempos de tempo de palavra devem ser adicionados à saída. Os valores aceites são `true` para permitir que os tempos de nível de palavras e `false` (o valor predefinido) o desativem.
:::row-end:::
:::row:::
   :::column span="1":::
      `diarizationEnabled`
   :::column-end:::
   :::column span="2":::
      Especifica que a análise de diarização deve ser efetuada na entrada, que se espera que seja um canal mono contendo duas vozes. Os valores aceites estão `true` a permitir a diarização e `false` (o valor predefinido) a desativá-la. Também requer ser `wordLevelTimestampsEnabled` definido como verdadeiro.
:::row-end:::
:::row:::
   :::column span="1":::
      `channels`
   :::column-end:::
   :::column span="2":::
      Um conjunto opcional de números de canal para processar. Aqui, um subconjunto dos canais disponíveis no ficheiro áudio pode ser especificado para ser processado `0` (por exemplo, apenas). Se não for especificado, os canais `0` e `1` são transcritos como padrão.
:::row-end:::
:::row:::
   :::column span="1":::
      `timeToLive`
   :::column-end:::
   :::column span="2":::
      Uma duração opcional para apagar automaticamente as transcrições após a conclusão da transcrição. O `timeToLive` que é útil nas transcrições de processamento em massa para garantir que serão eventualmente eliminadas (por `PT12H` exemplo). Se não for especificada ou `PT0H` definida, a transcrição não será apagada automaticamente.
:::row-end:::
:::row:::
   :::column span="1":::
      `destinationContainerUrl`
   :::column-end:::
   :::column span="2":::
      URL opcional com [serviço SAS](../../storage/common/storage-sas-overview.md) a um recipiente escrito em Azure. O resultado está guardado neste recipiente. Quando não especificado, a Microsoft armazena os resultados num recipiente de armazenamento gerido pela Microsoft. Quando a transcrição for eliminada chamando [a transcrição de Apagar,](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/DeleteTranscription)os dados dos resultados também serão eliminados.
:::row-end:::

### <a name="storage"></a>Armazenamento

A transcrição do lote suporta [o armazenamento Azure Blob](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview) para ler transcrições de áudio e escrita para armazenamento.

## <a name="the-batch-transcription-result"></a>O resultado da transcrição do lote

Para cada áudio de entrada, está a ser criado um ficheiro de resultados de transcrição. Pode obter a lista de ficheiros de resultados ligando para [obter ficheiros de transcrições](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/GetTranscriptionFiles). Este método devolve uma lista de ficheiros de resultados para esta transcrição. Para encontrar o ficheiro de transcrição de um ficheiro de entrada específico, filtre todos os ficheiros devolvidos com `kind`  ==  `Transcription` e `name`  ==  `{originalInputName.suffix}.json` .

Cada resultado da transcrição arquiva o seu formato:

```json
{
  "source": "...",                                                 // the sas url of a given contentUrl or the path relative to the root of a given container
  "timestamp": "2020-06-16T09:30:21Z",                             // creation time of the transcription, ISO 8601 encoded timestamp, combined date and time
  "durationInTicks": 41200000,                                     // total audio duration in ticks (1 tick is 100 nanoseconds)
  "duration": "PT4.12S",                                           // total audio duration, ISO 8601 encoded duration
  "combinedRecognizedPhrases": [                                   // concatenated results for simple access in single string for each channel
    {
      "channel": 0,                                                // channel number of the concatenated results
      "lexical": "hello world",
      "itn": "hello world",
      "maskedITN": "hello world",
      "display": "Hello world."
    }
  ],
  "recognizedPhrases": [                                           // results for each phrase and each channel individually
    {
      "recognitionStatus": "Success",                              // recognition state, e.g. "Success", "Failure"
      "channel": 0,                                                // channel number of the result
      "offset": "PT0.07S",                                         // offset in audio of this phrase, ISO 8601 encoded duration 
      "duration": "PT1.59S",                                       // audio duration of this phrase, ISO 8601 encoded duration
      "offsetInTicks": 700000.0,                                   // offset in audio of this phrase in ticks (1 tick is 100 nanoseconds)
      "durationInTicks": 15900000.0,                               // audio duration of this phrase in ticks (1 tick is 100 nanoseconds)
      
      // possible transcriptions of the current phrase with confidences
      "nBest": [
        {
          "confidence": 0.898652852,                               // confidence value for the recognition of the whole phrase
          "speaker": 1,                                            // if `diarizationEnabled` is `true`, this is the identified speaker (1 or 2), otherwise this property is not present
          "lexical": "hello world",
          "itn": "hello world",
          "maskedITN": "hello world",
          "display": "Hello world.",
          
          // if wordLevelTimestampsEnabled is `true`, there will be a result for each word of the phrase, otherwise this property is not present
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

O resultado contém estas formas:

:::row:::
   :::column span="1":::
      **Formulário**
   :::column-end:::
   :::column span="2":::
      **Conteúdo**
:::row-end:::
:::row:::
   :::column span="1":::
      `lexical`
   :::column-end:::
   :::column span="2":::
      As palavras reais reconhecidas.
:::row-end:::
:::row:::
   :::column span="1":::
      `itn`
   :::column-end:::
   :::column span="2":::
      Forma inversamente normalizada do texto reconhecido. São aplicadas abreviaturas ("doutor smith" a "dr smith"), números de telefone e outras transformações.
:::row-end:::
:::row:::
   :::column span="1":::
      `maskedITN`
   :::column-end:::
   :::column span="2":::
      O formulário ITN com máscara de profanação aplicada.
:::row-end:::
:::row:::
   :::column span="1":::
      `display`
   :::column-end:::
   :::column span="2":::
      A forma de exibição do texto reconhecido. Estão incluídas a pontuação e a capitalização adicionadas.
:::row-end:::

## <a name="speaker-separation-diarization"></a>Separação de altifalantes (Diarização)

A diarização é o processo de separação dos altifalantes numa peça de áudio. O nosso pipeline Batch suporta a diarização e é capaz de reconhecer dois altifalantes em gravações de canais mono. A funcionalidade não está disponível em gravações estéreo.

A saída da transcrição com a diarização ativada contém uma `Speaker` entrada para cada frase transcrita. Se a diarização não for utilizada, a propriedade `Speaker` não está presente na saída JSON. Para a diarização apoiamos duas vozes, para que os altifalantes sejam identificados como `1` ou `2` .

Para solicitar a diarização, basta adicionar o parâmetro relevante no pedido HTTP, conforme mostrado abaixo.

 ```json
{
  "contentUrls": [
    "<URL to an audio file to transcribe>",
  ],
  "properties": {
    "diarizationEnabled": true,
    "wordLevelTimestampsEnabled": true,
    "punctuationMode": "DictatedAndAutomatic",
    "profanityFilterMode": "Masked"
  },
  "locale": "en-US",
  "displayName": "Transcription of file using default model for en-US"
}
```

Os tempos de tempo do nível de palavra devem ser ativados como indicam os parâmetros do pedido acima indicados.

## <a name="best-practices"></a>Melhores práticas

O serviço de transcrição pode lidar com um grande número de transcrições submetidas. Pode consultar o estado das suas transcrições através de uma `GET` [transcrição](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/GetTranscriptions)na Get . Ligue Para apagar regularmente a [transcrição](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/DeleteTranscription) do serviço assim que tiver recuperado os resultados. Alternativamente definir `timeToLive` a propriedade para um valor razoável para garantir uma eventual eliminação dos resultados.

## <a name="sample-code"></a>Código de exemplo

As amostras completas estão disponíveis no [repositório de amostras gitHub](https://aka.ms/csspeech/samples) dentro do `samples/batch` subdiretório.

Por favor, atualize o código da amostra com as informações de subscrição, a região de serviço, o SAS URI apontando para o ficheiro áudio para transcrever, e localização do modelo no caso de pretender utilizar um modelo personalizado.

[!code-csharp[Configuration variables for batch transcription](~/samples-cognitive-services-speech-sdk/samples/batch/csharp/program.cs#transcriptiondefinition)]

O código de amostra define o cliente e submete o pedido de transcrição. Em seguida, ele sonda para a informação de estado e detalhes de impressão sobre o progresso da transcrição.

[!code-csharp[Code to check batch transcription status](~/samples-cognitive-services-speech-sdk/samples/batch/csharp/program.cs#transcriptionstatus)]

Para obter todos os detalhes sobre as chamadas anteriores, consulte o nosso [documento Swagger](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0). Para a amostra completa mostrada aqui, vá ao [GitHub](https://aka.ms/csspeech/samples) na `samples/batch` subdiretória.

Tome nota da configuração assíncronea para publicar áudio e receber o estado de transcrição. O cliente que cria é um cliente .NET HTTP. Há um `PostTranscriptions` método para enviar os detalhes do ficheiro áudio e um `GetTranscriptions` método para receber os estados. `PostTranscriptions`devolve uma pega, e `GetTranscriptions` usa-a para criar uma pega para obter o estado de transcrição.

O código de amostra atual não especifica um modelo personalizado. O serviço utiliza o modelo de base para transcrever o ficheiro ou ficheiros. Para especificar o modelo, pode passar o mesmo método que a referência do modelo para o modelo personalizado.

> [!NOTE]
> Para transcrições de base, não é necessário declarar o ID para o modelo de base.

## <a name="download-the-sample"></a>Transferir o exemplo

Pode encontrar a amostra no `samples/batch` diretório do [repositório de amostras GitHub.](https://aka.ms/csspeech/samples)

## <a name="next-steps"></a>Passos seguintes

- [Obter a subscrição de avaliação de Voz](https://azure.microsoft.com/try/cognitive-services/)
