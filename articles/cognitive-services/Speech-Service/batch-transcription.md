---
title: Como utilizar a transcrição do lote - Serviço de fala
titleSuffix: Azure Cognitive Services
description: A transcrição do lote é ideal se quiser transcrever uma grande quantidade de áudio no armazenamento, como a Azure Blobs. Ao utilizar a API de REST dedicada, pode apontar para ficheiros áudio com uma assinatura de acesso partilhado (SAS) URI e receber assincronamente transcrições.
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/23/2020
ms.author: wolfma
ms.custom: devx-track-csharp
ms.openlocfilehash: e48fead4d4364fd84f178388dbfb9158296e687b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "98659976"
---
# <a name="how-to-use-batch-transcription"></a>Como utilizar a transcrição do lote

A transcrição do lote é um conjunto de operações de API REST que permite transcrever uma grande quantidade de áudio no armazenamento. Pode apontar para ficheiros áudio usando um URI típico ou uma [assinatura de acesso partilhado (SAS)](../../storage/common/storage-sas-overview.md) URI e receber assincronamente os resultados da transcrição. Com a API v3.0, pode transcrever um ou mais ficheiros áudio ou processar um recipiente de armazenamento inteiro.

Pode utilizar a transcrição do lote REST APIs para ligar para os seguintes métodos:

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

Os trabalhos de transcrição de lote são programados com a melhor base de esforço.
Não se pode estimar quando um trabalho vai mudar para o estado de funcionamento, mas deve acontecer dentro de minutos sob a carga normal do sistema. Uma vez no estado de funcionamento, a transcrição ocorre mais rapidamente do que a velocidade de reprodução de tempo de funcionamento áudio.

## <a name="prerequisites"></a>Pré-requisitos

Tal como acontece com todas as funcionalidades do serviço Speech, cria uma chave de subscrição a partir do [portal Azure](https://portal.azure.com) seguindo o nosso [guia Iniciar .](overview.md#try-the-speech-service-for-free)

>[!NOTE]
> É necessária uma subscrição padrão (S0) para o serviço de fala para a transcrição do lote. As teclas de subscrição gratuitas (F0) não funcionam. Para mais informações, consulte [os preços e os limites.](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)

Se pretende personalizar modelos, siga os passos na [personalização acústica](./how-to-custom-speech-train-model.md) e [personalização linguística.](./how-to-custom-speech-train-model.md) Para utilizar os modelos criados na transcrição do lote, precisa da localização do modelo. Pode recuperar a localização do modelo quando inspeciona os detalhes do modelo `self` (propriedade). Um ponto final personalizado não é *necessário* para o serviço de transcrição do lote.

>[!NOTE]
> Como parte da API REST, a Transcrição do Lote tem um conjunto de [quotas e limites](speech-services-quotas-and-limits.md#batch-transcription), que encorajamos a rever. Para tirar o máximo partido da capacidade de transcrição do lote para transcrever eficientemente um grande número de ficheiros áudio, recomendamos sempre o envio de vários ficheiros por pedido ou apontando para um recipiente de Armazenamento Blob com os ficheiros de áudio para transcrever. O serviço transcreverá os ficheiros simultaneamente reduzindo o tempo de reviravolta. A utilização de vários ficheiros num único pedido é muito simples e simples - consulte a secção [de Configuração.](#configuration) 

## <a name="batch-transcription-api"></a>API de transcrição de lote

A API de transcrição do lote suporta os seguintes formatos:

| Formato | Codec | Bits por amostra | Taxa de amostra             |
|--------|-------|---------|---------------------------------|
| WAV    | PCM   | 16 bits  | 8 kHz ou 16 kHz, mono ou estéreo |
| MP3    | PCM   | 16 bits  | 8 kHz ou 16 kHz, mono ou estéreo |
| OGG    | OPUS  | 16 bits  | 8 kHz ou 16 kHz, mono ou estéreo |

Para os fluxos de áudio estéreo, os canais esquerdo e direito são divididos durante a transcrição. Está a ser criado um ficheiro de resultados JSON para cada canal.
Para criar uma transcrição final ordenada, use os tempos gerados por expressão.

### <a name="configuration"></a>Configuração

Os parâmetros de configuração são fornecidos como JSON. 

**Transcrever um ou mais ficheiros individuais.** Se tiver mais de um ficheiro para transcrever, recomendamos o envio de vários ficheiros num único pedido. O exemplo abaixo é usar três ficheiros:

```json
{
  "contentUrls": [
    "<URL to an audio file 1 to transcribe>",
    "<URL to an audio file 2 to transcribe>",
    "<URL to an audio file 3 to transcribe>"
  ],
  "properties": {
    "wordLevelTimestampsEnabled": true
  },
  "locale": "en-US",
  "displayName": "Transcription of file using default model for en-US"
}
```

**A processar um contentor de armazenamento inteiro.** O Contentor [SAS](../../storage/common/storage-sas-overview.md) deve conter `r` permissões (ler) e `l` (lista) :

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

**Use um modelo personalizado treinado numa transcrição de lote.** O exemplo é usar três ficheiros:

```json
{
  "contentUrls": [
    "<URL to an audio file 1 to transcribe>",
    "<URL to an audio file 2 to transcribe>",
    "<URL to an audio file 3 to transcribe>"
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
      Opcional, predefinições a `Masked` . Especifica como lidar com a profanação nos resultados do reconhecimento. Os valores aceites são `None` para desativar a filtragem da profanação, `Masked` para substituir a profanação por asteriscos, `Removed` para remover toda a profanação do resultado, ou `Tags` para adicionar etiquetas de "profanação".
:::row-end:::
:::row:::
   :::column span="1":::
      `punctuationMode`
   :::column-end:::
   :::column span="2":::
      Opcional, predefinições a `DictatedAndAutomatic` . Especifica como lidar com a pontuação nos resultados do reconhecimento. Os valores aceites são `None` para desativar a pontuação, `Dictated` para implicar uma pontuação explícita (falada), `Automatic` para deixar o descodificador lidar com a pontuação, ou `DictatedAndAutomatic` para usar a pontuação ditada e automática.
:::row-end:::
:::row:::
   :::column span="1":::
      `wordLevelTimestampsEnabled`
   :::column-end:::
   :::column span="2":::
      Opcional, `false` por defeito. Especifica se os tempos de tempo de palavra devem ser adicionados à saída.
:::row-end:::
:::row:::
   :::column span="1":::
      `diarizationEnabled`
   :::column-end:::
   :::column span="2":::
      Opcional, `false` por defeito. Especifica que a análise de diarização deve ser efetuada na entrada, que se espera que seja um canal mono contendo duas vozes. Nota: Requer `wordLevelTimestampsEnabled` que se esteja definido para `true` .
:::row-end:::
:::row:::
   :::column span="1":::
      `channels`
   :::column-end:::
   :::column span="2":::
      Opcional, `0` e `1` transcrito por defeito. Uma série de números de canal para processar. Aqui, um subconjunto dos canais disponíveis no ficheiro áudio pode ser especificado para ser processado (por exemplo `0` apenas).
:::row-end:::
:::row:::
   :::column span="1":::
      `timeToLive`
   :::column-end:::
   :::column span="2":::
      Opcional, sem eliminação por defeito. Uma duração para apagar automaticamente as transcrições após a conclusão da transcrição. O `timeToLive` é útil nas transcrições de processamento em massa para garantir que serão eventualmente eliminadas (por exemplo, durante `PT12H` 12 horas).
:::row-end:::
:::row:::
   :::column span="1":::
      `destinationContainerUrl`
   :::column-end:::
   :::column span="2":::
      URL opcional com [SAS ad hoc](../../storage/common/storage-sas-overview.md) para um recipiente escrito em Azure. O resultado está guardado neste recipiente. A SAS com a política de acesso armazenada **não** é suportada. Quando não especificado, a Microsoft armazena os resultados num recipiente de armazenamento gerido pela Microsoft. Quando a transcrição for eliminada chamando [a transcrição de Apagar,](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/DeleteTranscription)os dados dos resultados também serão eliminados.
:::row-end:::

### <a name="storage"></a>Armazenamento

A transcrição do lote pode ler áudio a partir de um URI de internet visível pelo público, e pode ler áudio ou escrever transcrições usando um SAS URI com [armazenamento Azure Blob](../../storage/blobs/storage-blobs-overview.md).

## <a name="batch-transcription-result"></a>Resultado da transcrição do lote

Para cada entrada áudio, é criado um ficheiro de resultados de transcrição.
A operação [de ficheiros de transcrições get](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/GetTranscriptionFiles) devolve uma lista de ficheiros de resultados para esta transcrição. Para encontrar o ficheiro de transcrição de um ficheiro de entrada específico, filtre todos os ficheiros devolvidos com `kind`  ==  `Transcription` e `name`  ==  `{originalInputName.suffix}.json` .

Cada ficheiro de resultados de transcrição tem este formato:

```json
{
  "source": "...",                      // sas url of a given contentUrl or the path relative to the root of a given container
  "timestamp": "2020-06-16T09:30:21Z",  // creation time of the transcription, ISO 8601 encoded timestamp, combined date and time
  "durationInTicks": 41200000,          // total audio duration in ticks (1 tick is 100 nanoseconds)
  "duration": "PT4.12S",                // total audio duration, ISO 8601 encoded duration
  "combinedRecognizedPhrases": [        // concatenated results for simple access in single string for each channel
    {
      "channel": 0,                     // channel number of the concatenated results
      "lexical": "hello world",
      "itn": "hello world",
      "maskedITN": "hello world",
      "display": "Hello world."
    }
  ],
  "recognizedPhrases": [                // results for each phrase and each channel individually
    {
      "recognitionStatus": "Success",   // recognition state, e.g. "Success", "Failure"          
      "speaker": 1,                     // if `diarizationEnabled` is `true`, this is the identified speaker (1 or 2), otherwise this property is not present
      "channel": 0,                     // channel number of the result
      "offset": "PT0.07S",              // offset in audio of this phrase, ISO 8601 encoded duration 
      "duration": "PT1.59S",            // audio duration of this phrase, ISO 8601 encoded duration
      "offsetInTicks": 700000.0,        // offset in audio of this phrase in ticks (1 tick is 100 nanoseconds)
      "durationInTicks": 15900000.0,    // audio duration of this phrase in ticks (1 tick is 100 nanoseconds)

      // possible transcriptions of the current phrase with confidences
      "nBest": [
        {
          "confidence": 0.898652852,    // confidence value for the recognition of the whole phrase
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

O resultado contém os seguintes campos:

:::row:::
   :::column span="1":::
      **Campo**
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

## <a name="speaker-separation-diarization"></a>Separação de altifalantes (diarização)

A diarização é o processo de separação dos altifalantes numa peça de áudio. O pipeline do lote suporta a diarização e é capaz de reconhecer dois altifalantes em gravações de canais mono. A funcionalidade não está disponível em gravações estéreo.

A saída da transcrição com a diarização ativada contém uma `Speaker` entrada para cada frase transcrita. Se a diarização não for utilizada, a `Speaker` propriedade não está presente na saída JSON. Para a diarização apoiamos duas vozes, para que os altifalantes sejam identificados como `1` ou `2` .

Para solicitar a diarização, adicione definir o `diarizationEnabled` imóvel para `true` gostar do pedido HTTP mostra abaixo.

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

O serviço de transcrição do lote pode lidar com um grande número de transcrições submetidas. Pode consultar o estado das suas transcrições com [transcrições.](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/GetTranscriptions)
Ligue Para apagar regularmente a [transcrição](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/DeleteTranscription) do serviço assim que tiver recuperado os resultados. Alternativamente definir `timeToLive` propriedade para garantir uma eventual eliminação dos resultados.

## <a name="sample-code"></a>Código de exemplo

As amostras completas estão disponíveis no [repositório de amostras gitHub](https://aka.ms/csspeech/samples) dentro do `samples/batch` subdiretório.

Atualize o código de amostra com as informações de subscrição, região de serviço, URI apontando para o ficheiro áudio para transcrever e localização do modelo se estiver a utilizar um modelo personalizado.

[!code-csharp[Configuration variables for batch transcription](~/samples-cognitive-services-speech-sdk/samples/batch/csharp/program.cs#transcriptiondefinition)]

O código de amostra define o cliente e submete o pedido de transcrição. Em seguida, ele sonda para a informação de estado e detalhes de impressão sobre o progresso da transcrição.

```csharp
// get the status of our transcriptions periodically and log results
int completed = 0, running = 0, notStarted = 0;
while (completed < 1)
{
    completed = 0; running = 0; notStarted = 0;

    // get all transcriptions for the user
    paginatedTranscriptions = null;
    do
    {
        // <transcriptionstatus>
        if (paginatedTranscriptions == null)
        {
            paginatedTranscriptions = await client.GetTranscriptionsAsync().ConfigureAwait(false);
        }
        else
        {
            paginatedTranscriptions = await client.GetTranscriptionsAsync(paginatedTranscriptions.NextLink).ConfigureAwait(false);
        }

        // delete all pre-existing completed transcriptions. If transcriptions are still running or not started, they will not be deleted
        foreach (var transcription in paginatedTranscriptions.Values)
        {
            switch (transcription.Status)
            {
                case "Failed":
                case "Succeeded":
                    // we check to see if it was one of the transcriptions we created from this client.
                    if (!createdTranscriptions.Contains(transcription.Self))
                    {
                        // not created form here, continue
                        continue;
                    }

                    completed++;

                    // if the transcription was successful, check the results
                    if (transcription.Status == "Succeeded")
                    {
                        var paginatedfiles = await client.GetTranscriptionFilesAsync(transcription.Links.Files).ConfigureAwait(false);

                        var resultFile = paginatedfiles.Values.FirstOrDefault(f => f.Kind == ArtifactKind.Transcription);
                        var result = await client.GetTranscriptionResultAsync(new Uri(resultFile.Links.ContentUrl)).ConfigureAwait(false);
                        Console.WriteLine("Transcription succeeded. Results: ");
                        Console.WriteLine(JsonConvert.SerializeObject(result, SpeechJsonContractResolver.WriterSettings));
                    }
                    else
                    {
                        Console.WriteLine("Transcription failed. Status: {0}", transcription.Properties.Error.Message);
                    }

                    break;

                case "Running":
                    running++;
                    break;

                case "NotStarted":
                    notStarted++;
                    break;
            }
        }

        // for each transcription in the list we check the status
        Console.WriteLine(string.Format("Transcriptions status: {0} completed, {1} running, {2} not started yet", completed, running, notStarted));
    }
    while (paginatedTranscriptions.NextLink != null);

    // </transcriptionstatus>
    // check again after 1 minute
    await Task.Delay(TimeSpan.FromMinutes(1)).ConfigureAwait(false);
}
```

Para obter todos os detalhes sobre as chamadas anteriores, consulte o nosso [documento Swagger](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0). Para a amostra completa mostrada aqui, vá ao [GitHub](https://aka.ms/csspeech/samples) na `samples/batch` subdiretória.

Esta amostra utiliza uma configuração assíncronea para publicar áudio e receber o estado de transcrição.
O `PostTranscriptions` método envia os detalhes do ficheiro áudio e o método recebe os `GetTranscriptions` estados.
`PostTranscriptions` devolve uma pega, e `GetTranscriptions` usa-a para criar uma pega para obter o estado de transcrição.

Este código de amostra não especifica um modelo personalizado. O serviço utiliza o modelo de base para transcrever o ficheiro ou ficheiros. Para especificar o modelo, pode passar o mesmo método que a referência do modelo para o modelo personalizado.

> [!NOTE]
> Para transcrições de base, não é necessário declarar o ID para o modelo de base.

## <a name="next-steps"></a>Passos seguintes

- [Referência da API do texto v3](https://centralus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/CopyModelToSubscription)
