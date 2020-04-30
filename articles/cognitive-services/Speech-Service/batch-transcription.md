---
title: O que é transcrição de lote - Serviço de fala
titleSuffix: Azure Cognitive Services
description: A transcrição do lote é ideal se quiser transcrever uma grande quantidade de áudio no armazenamento, como o Azure Blobs. Ao utilizar a API repousada, pode apontar ficheiros áudio com uma assinatura de acesso partilhado (SAS) URI e receber transcrições assíncrona.
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/18/2020
ms.author: wolfma
ms.openlocfilehash: 46bfabfb2ccf091fd5dc0fcf0e9b447bad7c34d1
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "82208623"
---
# <a name="what-is-batch-transcription"></a>O que é a transcrição do lote?

A transcrição do lote é um conjunto de operações rest API que lhe permite transcrever uma grande quantidade de áudio no armazenamento. Pode apontar ficheiros áudio com uma assinatura de acesso partilhado (SAS) URI e receber os resultados da transcrição.

A transcrição asíncrona fala-a-texto é apenas uma das características. Pode utilizar as APIs de transcrição do lote PARA chamar os seguintes métodos:



|    Operação de Transcrição de Lote                                             |    Método    |    CHAMADA REST API                                   |
|------------------------------------------------------------------------------|--------------|----------------------------------------------------|
|    Cria uma nova transcrição.                                              |    POST      |    api/speechtotext/v2.0/transcrições            |
|    Recupera uma lista de transcrições para a subscrição autenticada.    |    GET       |    api/speechtotext/v2.0/transcrições            |
|    Obtém uma lista de locais apoiados para transcrições offline.              |    GET       |    api/speechtotext/v2.0/transcrições/locais    |
|    Atualiza os detalhes mutáveis da transcrição identificadas pelo seu ID.    |    PATCH     |    api/speechtotext/v2.0/transcriptions/{id}       |
|    Elimina a tarefa de transcrição especificada.                                 |    DELETE    |    api/speechtotext/v2.0/transcriptions/{id}       |
|    A transcrição é identificada pela identificação dada.                        |    GET       |    api/speechtotext/v2.0/transcriptions/{id}       |




Pode rever e testar a API detalhada, que está disponível como [documento Swagger,](https://westus.cris.ai/swagger/ui/index#/Custom%20Speech%20transcriptions%3A)sob a rubrica `Custom Speech transcriptions`.

Os trabalhos de transcrição de lotes são programados com o melhor esforço. Atualmente, não existe uma estimativa para quando um emprego muda para o estado de funcionamento. Sob a carga normal do sistema, deve acontecer dentro de minutos. Uma vez no estado de execução, a transcrição real é processada mais rapidamente do que o áudio em tempo real.

Junto à API de fácil utilização, não precisa de implementar pontos finais personalizados, e não tem quaisquer requisitos de condivisão para observar.

## <a name="prerequisites"></a>Pré-requisitos

### <a name="subscription-key"></a>Chave de Subscrição

Tal como acontece com todas as funcionalidades do serviço Speech, cria uma chave de subscrição do [portal Azure](https://portal.azure.com) seguindo o nosso [guia Get started](get-started.md).

>[!NOTE]
> É necessária uma subscrição padrão (S0) para o serviço de Fala para utilizar a transcrição do lote. As chaves de subscrição gratuitas (F0) não funcionam. Para mais informações, consulte [preços e limites.](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)

### <a name="custom-models"></a>Modelos personalizados

Se pretende personalizar modelos acústicos ou linguísticos, siga os passos em [Personalizar modelos acústicos](how-to-customize-acoustic-models.md) e modelos de linguagem de [personalização de design.](how-to-customize-language-model.md) Para utilizar os modelos criados na transcrição de lotes, precisa das suas identificações de modelo. Pode recuperar o ID do modelo quando inspecionar os detalhes do modelo. Não é necessário um ponto final personalizado implantado para o serviço de transcrição do lote.

## <a name="the-batch-transcription-api"></a>A API de Transcrição de Lote

### <a name="supported-formats"></a>Formatos suportados

A API de Transcrição de Lote suporta os seguintes formatos:

| Formato | Codec | Bitrate | Taxa de amostragem                     |
|--------|-------|---------|---------------------------------|
| WAV    | PCM   | 16 bits  | 8 kHz ou 16 kHz, mono ou estéreo |
| MP3    | PCM   | 16 bits  | 8 kHz ou 16 kHz, mono ou estéreo |
| OGG    | OPUS  | 16 bits  | 8 kHz ou 16 kHz, mono ou estéreo |

Para os fluxos de áudio estéreo, os canais esquerdo e direito são divididos durante a transcrição. Para cada canal, está a ser criado um ficheiro de resultados da JSON. Os carimbos de tempo gerados por expressão permitem ao desenvolvedor criar uma transcrição final ordenada.

### <a name="configuration"></a>Configuração

Os parâmetros de configuração são fornecidos como JSON:

```json
{
  "recordingsUrl": "<URL to the Azure blob to transcribe>",
  "models": [{"Id":"<optional acoustic model ID>"},{"Id":"<optional language model ID>"}],
  "locale": "<locale to use, for example en-US>",
  "name": "<user defined name of the transcription batch>",
  "description": "<optional description of the transcription>",
  "properties": {
    "ProfanityFilterMode": "None | Removed | Tags | Masked",
    "PunctuationMode": "None | Dictated | Automatic | DictatedAndAutomatic",
    "AddWordLevelTimestamps" : "True | False",
    "AddSentiment" : "True | False",
    "AddDiarization" : "True | False",
    "TranscriptionResultsContainerUrl" : "<service SAS URI to Azure container to store results into (write permission required)>"
  }
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
      `ProfanityFilterMode`
   :::column-end:::
   :::column span="2":::
      Especifica como lidar com a profanação nos resultados de reconhecimento. Os valores aceites `None` são desativar a filtragem de palavrões, `Masked` substituir a profanação por asteriscos, `Removed` remover toda a profanação do resultado, ou `Tags` adicionar tags de "profanação". A predefinição é `Masked`.
:::row-end:::
:::row:::
   :::column span="1":::
      `PunctuationMode`
   :::column-end:::
   :::column span="2":::
      Especifica como lidar com a pontuação nos resultados do reconhecimento. Os valores aceites `None` são `Dictated` desativar a pontuação, implicar `Automatic` pontuação explícita (falada), deixar o `DictatedAndAutomatic` descodificador lidar com pontuação, ou usar pontuação ditada e automática. A predefinição é `DictatedAndAutomatic`.
:::row-end:::
:::row:::
   :::column span="1":::
      `AddWordLevelTimestamps`
   :::column-end:::
   :::column span="2":::
      Especifica se os selos de tempo do nível de palavra devem ser adicionados à saída. Os valores aceites são `true` para `false` permitir que os selos de tempo do nível de palavra e (o valor predefinido) os desativem.
:::row-end:::
:::row:::
   :::column span="1":::
      `AddSentiment`
   :::column-end:::
   :::column span="2":::
      Especifica se a análise de sentimento deve ser aplicada à expressão. Os valores aceites são `true` para ativar e `false` (o valor predefinido) desativá-lo. Consulte a [Análise de Sentimentos](#sentiment-analysis) para mais detalhes.
:::row-end:::
:::row:::
   :::column span="1":::
      `AddDiarization`
   :::column-end:::
   :::column span="2":::
      Especifica que a análise da diarização deve ser efetuada na entrada, que se espera que seja um canal mono contendo duas vozes. Os valores aceites estão `true` a permitir a diarização e `false` (o valor predefinido) a desativar. Também precisa `AddWordLevelTimestamps` ser definido para a verdade.
:::row-end:::
:::row:::
   :::column span="1":::
      `TranscriptionResultsContainerUrl`
   :::column-end:::
   :::column span="2":::
      URL opcional com [serviço SAS](../../storage/common/storage-sas-overview.md) para um recipiente reemputável em Azure. O resultado está guardado neste recipiente.
:::row-end:::

### <a name="storage"></a>Armazenamento

A transcrição do lote suporta [o armazenamento de Azure Blob](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview) para ler transcrições áudio e escrita para armazenamento.

## <a name="the-batch-transcription-result"></a>O resultado da transcrição do lote

Para o áudio de entrada mono, está a ser criado um ficheiro de resultados de transcrição. Para áudio de entrada estéreo, estão a ser criados dois ficheiros de resultados de transcrição. Cada um tem esta estrutura:

```json
{
  "AudioFileResults":[
    {
      "AudioFileName": "Channel.0.wav | Channel.1.wav"      'maximum of 2 channels supported'
      "AudioFileUrl": null                                  'always null'
      "AudioLengthInSeconds": number                        'Real number. Two decimal places'
      "CombinedResults": [
        {
          "ChannelNumber": null                             'always null'
          "Lexical": string
          "ITN": string
          "MaskedITN": string
          "Display": string
        }
      ]
      SegmentResults:[                                      'for each individual segment'
        {
          "RecognitionStatus": "Success | Failure"
          "ChannelNumber": null
          "SpeakerId": null | "1 | 2"                       'null if no diarization
                                                             or stereo input file, the
                                                             speakerId as a string if
                                                             diarization requested for
                                                             mono audio file'
          "Offset": number                                  'time in ticks (1 tick is 100 nanosec)'
          "Duration": number                                'time in ticks (1 tick is 100 nanosec)'
          "OffsetInSeconds" : number                        'Real number. Two decimal places'
          "DurationInSeconds" : number                      'Real number. Two decimal places'
          "NBest": [
            {
              "Confidence": number                          'between 0 and 1'
              "Lexical": string
              "ITN": string
              "MaskedITN": string
              "Display": string
              "Sentiment":
                {                                           'this is omitted if sentiment is
                                                             not requested'
                  "Negative": number                        'between 0 and 1'
                  "Neutral": number                         'between 0 and 1'
                  "Positive": number                        'between 0 and 1'
                }
              "Words": [
                {
                  "Word": string
                  "Offset": number                          'time in ticks (1 tick is 100 nanosec)'
                  "Duration": number                        'time in ticks (1 tick is 100 nanosec)'
                  "OffsetInSeconds": number                 'Real number. Two decimal places'
                  "DurationInSeconds": number               'Real number. Two decimal places'
                  "Confidence": number                      'between 0 and 1'
                }
              ]
            }
          ]
        }
      ]
    }
  ]
}
```

O resultado contém estes formulários:

:::row:::
   :::column span="1":::
      **Formulário**
   :::column-end:::
   :::column span="2":::
      **Conteúdo**
:::row-end:::
:::row:::
   :::column span="1":::
      `Lexical`
   :::column-end:::
   :::column span="2":::
      As palavras verdadeiras foram reconhecidas.
:::row-end:::
:::row:::
   :::column span="1":::
      `ITN`
   :::column-end:::
   :::column span="2":::
      Forma inversa-por texto normalizada do texto reconhecido. As abreviaturas ("doctor smith" a "dr smith"), os números de telefone e outras transformações são aplicadas.
:::row-end:::
:::row:::
   :::column span="1":::
      `MaskedITN`
   :::column-end:::
   :::column span="2":::
      A forma ITN com máscara profana aplicada.
:::row-end:::
:::row:::
   :::column span="1":::
      `Display`
   :::column-end:::
   :::column span="2":::
      A forma de exibição do texto reconhecido. Estão incluídas pontuações adicionais e capitalização.
:::row-end:::

## <a name="speaker-separation-diarization"></a>Separação de altifalantes (Diarização)

A diarização é o processo de separação dos altifalantes numa peça de áudio. O nosso pipeline Batch suporta a diarização e é capaz de reconhecer dois altifalantes em gravações de mono canal. A funcionalidade não está disponível em gravações estéreo.

Toda a saída `SpeakerId`de transcrição contém um . Se a diarização não `"SpeakerId": null` for utilizada, mostra-se na saída JSON. Para a diarização apoiamos duas vozes, `"1"` para `"2"`que os altifalantes sejam identificados como ou .

Para solicitar a diarização, basta adicionar o parâmetro relevante no pedido HTTP, como mostrado abaixo.

 ```json
{
  "recordingsUrl": "<URL to the Azure blob to transcribe>",
  "models": [{"Id":"<optional acoustic model ID>"},{"Id":"<optional language model ID>"}],
  "locale": "<locale to us, for example en-US>",
  "name": "<user defined name of the transcription batch>",
  "description": "<optional description of the transcription>",
  "properties": {
    "AddWordLevelTimestamps" : "True",
    "AddDiarization" : "True"
  }
}
```

Os selos temporais de nível de palavras também teriam de ser "ligados", como indicam os parâmetros do pedido acima indicados.

## <a name="sentiment-analysis"></a>Análise de sentimentos

A funcionalidade de sentimento estima o sentimento expresso no áudio. O sentimento é expresso por um `Negative`valor `Neutral`entre `Positive` 0 e 1 para , e sentimento. Por exemplo, a análise de sentimentos pode ser usada em cenários de call center:

- Obtenha informações sobre a satisfação do cliente
- Obtenha informações sobre o desempenho dos agentes (equipa que recebe as chamadas)
- Encontre o ponto exato no tempo quando uma chamada tomou uma volta em uma direção negativa
- O que correu bem ao transformar uma chamada negativa numa direção positiva
- Identifique o que os clientes gostam e o que não gostam de um produto ou de um serviço

O sentimento é pontuado por segmento de áudio com base na forma lexical. Todo o texto dentro desse segmento de áudio é usado para calcular o sentimento. Nenhum sentimento agregado está a ser calculado para toda a transcrição. A análise de sentimentos está atualmente disponível apenas na língua inglesa.

> [!NOTE]
> Recomendamos a utilização do Microsoft Text Analytics API. Oferece características mais avançadas além da análise de sentimentos como a extração de frases-chave, deteção automática de linguagem, e muito mais. Pode encontrar informações e amostras na documentação do [Text Analytics.](https://azure.microsoft.com/services/cognitive-services/text-analytics/)
>

Uma amostra de saída JSON parece abaixo:

```json
{
  "AudioFileResults": [
    {
      "AudioFileName": "Channel.0.wav",
      "AudioFileUrl": null,
      "SegmentResults": [
        {
          "RecognitionStatus": "Success",
          "ChannelNumber": null,
          "Offset": 400000,
          "Duration": 13300000,
          "NBest": [
            {
              "Confidence": 0.976174,
              "Lexical": "what's the weather like",
              "ITN": "what's the weather like",
              "MaskedITN": "what's the weather like",
              "Display": "What's the weather like?",
              "Words": null,
              "Sentiment": {
                "Negative": 0.206194,
                "Neutral": 0.793785,
                "Positive": 0.0
              }
            }
          ]
        }
      ]
    }
  ]
}
```

## <a name="best-practices"></a>Melhores práticas

O serviço de transcrição pode lidar com um grande número de transcrições submetidas. Pode consultar o estado das suas transcrições `GET` através de um método de [transcrição](https://westus.cris.ai/swagger/ui/index#/Custom%20Speech%20transcriptions%3A/GetTranscriptions). Mantenha a informação devolvida a um `take` tamanho razoável especificando o parâmetro (algumas centenas). [Elimine](https://westus.cris.ai/swagger/ui/index#/Custom%20Speech%20transcriptions%3A/DeleteTranscription) as transcrições regularmente do serviço assim que tiver recuperado os resultados. Isto garante respostas rápidas das chamadas de gestão de transcrição.

## <a name="sample-code"></a>Código de exemplo

As amostras completas estão disponíveis no [repositório da amostra GitHub](https://aka.ms/csspeech/samples) dentro do `samples/batch` subdiretório.

Tem de personalizar o código da amostra com as suas informações de subscrição, a região de serviço, o SAS URI apontando para o ficheiro áudio para transcrever, e identificações de modelo si, caso pretenda utilizar um modelo acústico ou linguístico personalizado.

[!code-csharp[Configuration variables for batch transcription](~/samples-cognitive-services-speech-sdk/samples/batch/csharp/program.cs#batchdefinition)]

O código da amostra configura o cliente e submete o pedido de transcrição. Em seguida, sondagens para a informação do estado e imprimir detalhes sobre o progresso da transcrição.

[!code-csharp[Code to check batch transcription status](~/samples-cognitive-services-speech-sdk/samples/batch/csharp/program.cs#batchstatus)]

Para mais detalhes sobre as chamadas anteriores, consulte o nosso [documento Swagger.](https://westus.cris.ai/swagger/ui/index) Para a amostra completa mostrada aqui, vá `samples/batch` ao [GitHub](https://aka.ms/csspeech/samples) no subdiretório.

Tome nota da configuração assíncrona para publicar áudio e receber o estado da transcrição. O cliente que cria é um cliente .NET HTTP. Há um `PostTranscriptions` método para enviar os detalhes `GetTranscriptions` do ficheiro áudio e um método para receber os resultados. `PostTranscriptions`devolve uma pega `GetTranscriptions` e usa-a para criar uma pega para obter o estado de transcrição.

O código de amostra atual não especifica um modelo personalizado. O serviço utiliza os modelos de base para transcrever o ficheiro ou os ficheiros. Para especificar os modelos, pode passar o mesmo método que os IDs modelo para o modelo acústico e o modelo linguístico.

> [!NOTE]
> Para transcrições de base, não precisa de declarar o ID para os modelos de base. Se especificar apenas um ID do modelo de idioma (e nenhum ID de modelo acústico), é automaticamente selecionado um modelo acústico correspondente. Se especificar apenas um ID de modelo acústico, é selecionado automaticamente um modelo de linguagem correspondente.

## <a name="download-the-sample"></a>Transferir o exemplo

Pode encontrar a amostra `samples/batch` no diretório no [repositório da amostra GitHub](https://aka.ms/csspeech/samples).

## <a name="next-steps"></a>Passos seguintes

- [Obter a subscrição de avaliação de Voz](https://azure.microsoft.com/try/cognitive-services/)
