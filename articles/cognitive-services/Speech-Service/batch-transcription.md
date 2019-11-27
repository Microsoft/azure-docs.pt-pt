---
title: Como usar a transcrição do lote – serviço de fala
titleSuffix: Azure Cognitive Services
description: A transcrição do lote é ideal se você quiser transcrever uma grande quantidade de áudio no armazenamento, como BLOBs do Azure. Usando a API REST dedicada, você pode apontar para arquivos de áudio com um URI de assinatura de acesso compartilhado (SAS) e receber transcrições de forma assíncrona.
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: panosper
ms.openlocfilehash: 158a99b1691e59fa58207f3c9291ca9d37a6679c
ms.sourcegitcommit: 36eb583994af0f25a04df29573ee44fbe13bd06e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/26/2019
ms.locfileid: "74538122"
---
# <a name="why-use-batch-transcription"></a>Por que usar a transcrição do lote?

A transcrição do lote é ideal se você quiser transcrever uma grande quantidade de áudio no armazenamento, como BLOBs do Azure. Usando a API REST dedicada, você pode apontar para arquivos de áudio com um URI de assinatura de acesso compartilhado (SAS) e receber transcrições de forma assíncrona.

## <a name="prerequisites"></a>Pré-requisitos

### <a name="subscription-key"></a>Chave de assinatura

Assim como acontece com todos os recursos do serviço de fala, você cria uma chave de assinatura do [portal do Azure](https://portal.azure.com) seguindo nosso [Guia de introdução](get-started.md). Se você planeja obter transcrições de nossos modelos de linha de base, a criação de uma chave é tudo o que você precisa fazer.

>[!NOTE]
> Uma assinatura padrão (S0) para serviços de fala é necessária para usar a transcrição do lote. As chaves de assinatura gratuitas (F0) não funcionarão. Para obter informações adicionais, consulte [preços e limites](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

### <a name="custom-models"></a>Modelos personalizados

Se você planeja personalizar modelos acústicos ou de idioma, siga as etapas em [personalizar modelos acústicos](how-to-customize-acoustic-models.md) e [personalizar modelos de linguagem](how-to-customize-language-model.md). Para usar os modelos criados na transcrição do lote, você precisa de suas IDs de modelo. Essa ID não é a ID do ponto de extremidade que você encontra no modo de exibição detalhes do ponto de extremidade, é a ID do modelo que você pode recuperar ao selecionar os detalhes dos modelos.

## <a name="the-batch-transcription-api"></a>A API de transcrição do lote

A API de transcrição do lote oferece uma transcrição de fala a texto assíncrona, juntamente com recursos adicionais. É uma API REST que expõe métodos para:

1. Criando solicitações de processamento em lote
1. Status da consulta
1. Baixando transcrições

> [!NOTE]
> A API de transcrição do lote é ideal para call centers, que normalmente acumulam milhares de horas de áudio. Ele facilita transcrever grandes volumes de gravações de áudio.

### <a name="supported-formats"></a>Formatos com suporte

A API de transcrição do lote dá suporte aos seguintes formatos:

| Formato | Codec | 720p | Taxa de amostra |
|--------|-------|---------|-------------|
| WAV | PCM | 16 bits | 8 ou 16 kHz, mono, estéreo |
| MP | PCM | 16 bits | 8 ou 16 kHz, mono, estéreo |
| OGG | OPUS | 16 bits | 8 ou 16 kHz, mono, estéreo |

Para fluxos de áudio estéreo, a API de transcrição do lote divide o canal esquerdo e direito durante a transcrição. Os dois arquivos JSON com o resultado são criados a partir de um único canal. Os carimbos de data/hora por expressão permitem que o desenvolvedor crie uma transcrição final ordenada. Essa solicitação de exemplo inclui propriedades de filtragem de profanação, pontuação e carimbos de data/hora de nível de palavra.

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
    "ProfanityFilterMode": "Masked",
    "PunctuationMode": "DictatedAndAutomatic",
    "AddWordLevelTimestamps" : "True",
    "AddSentiment" : "True"
  }
}
```

> [!NOTE]
> A API de transcrição do lote usa um serviço REST para solicitar transcrições, seu status e os resultados associados. Você pode usar a API de qualquer idioma. A próxima seção descreve como a API é usada.

### <a name="configuration-properties"></a>Propriedades de configuração

Use essas propriedades opcionais para configurar a transcrição:

| Parâmetro | Descrição |
|-----------|-------------|
| `ProfanityFilterMode` | Especifica como lidar com profanação em resultados de reconhecimento. Os valores aceitos são `None` que desabilita a filtragem de profanação, `masked` que substitui profanação por asteriscos, `removed` que remove toda a profanaidade do resultado ou `tags` que adiciona marcas de "profanação". A configuração padrão é `masked`. |
| `PunctuationMode` | Especifica como lidar com a pontuação nos resultados do reconhecimento. Os valores aceitos são `None` que desabilita a pontuação, `dictated` que implica Pontuação explícita, `automatic` que permite que o decodificador lide com pontuação ou `dictatedandautomatic` que implica em marcas de Pontuação ditadas ou automáticas. |
 | `AddWordLevelTimestamps` | Especifica se os carimbos de data/hora de nível de palavra devem ser adicionados à saída. Os valores aceitos são `true` que habilita os carimbos de data e hora de nível de palavra e `false` (o valor padrão) para desabilitá-lo. |
 | `AddSentiment` | Especifica um sentimentos que deve ser adicionado ao expressão. Os valores aceitos são `true` que habilita a expressão e a `false` (o valor padrão) para desabilitá-lo. |
 | `AddDiarization` | Especifica que a análise de diarization deve ser executada na entrada que é esperada como canal mono contendo duas vozes. Os valores aceitos são `true` que habilita diarization e `false` (o valor padrão) para desabilitá-lo. Ele também requer que `AddWordLevelTimestamps` seja definido como true.|

### <a name="storage"></a>Armazenamento

A transcrição do lote dá suporte ao [armazenamento de BLOBs do Azure](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview) para leitura de áudio e gravação de transcrições no armazenamento.

## <a name="speaker-separation-diarization"></a>Separação de alto-falantes (Diarization)

Diarization é o processo de separar os alto-falantes em um pedaço de áudio. Nosso pipeline de lote dá suporte a Diarization e é capaz de reconhecer dois alto-falantes em gravações de canal mono.

Para solicitar que sua solicitação de transcrição de áudio seja processada para diarization, basta adicionar o parâmetro relevante na solicitação HTTP, conforme mostrado abaixo.

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

Os carimbos de data/hora de nível de palavra também teriam que ser ' ativados ', uma vez que os parâmetros na solicitação acima indicam. 

O áudio correspondente conterá os alto-falantes identificados por um número (atualmente, damos suporte apenas a duas vozes, de modo que os alto-falantes serão identificados como ' orador 1 ' e ' orador 2 ') seguidos da saída da transcrição.

Observe também que o Diarization não está disponível em gravações de estéreo. Além disso, toda a saída JSON conterá a marca do orador. Se diarization não for usado, ele mostrará ' orador: NULL ' na saída JSON.

> [!NOTE]
> O Diarization está disponível em todas as regiões e em todas as localidades!

## <a name="sentiment"></a>Sentimento

O sentimentos é um novo recurso da API de transcrição do lote e é um recurso importante no domínio do Call Center. Os clientes podem usar os parâmetros de `AddSentiment` para suas solicitações para

1.  Obtenha informações sobre a satisfação do cliente
2.  Obtenha informações sobre o desempenho dos agentes (equipe fazendo as chamadas)
3.  Identifique o ponto exato no tempo em que uma chamada levou uma direção negativa
4.  Identifique o que deu certo ao transformar chamadas negativas em positivos
5.  Identificar o que os clientes gostam e o que eles não gostam sobre um produto ou um serviço

A notação é pontuada por segmento de áudio em que um segmento de áudio é definido como o tempo se sobrepõe entre o início do expressão (deslocamento) e o silêncio de detecção do fluxo de fim de byte. O texto inteiro dentro desse segmento é usado para calcular as opiniões. NÃO calculamos valores de opiniões agregados para toda a chamada ou toda a fala de cada canal. Essas agregações são deixadas para o proprietário do domínio para serem aplicadas.

As opiniões são aplicadas no formato léxico.

Um exemplo de saída JSON é semelhante ao seguinte:

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
O recurso usa um modelo de sentimentos, que está atualmente em versão beta.

## <a name="sample-code"></a>Código de exemplo

Exemplos completos estão disponíveis no [repositório de exemplo do GitHub](https://aka.ms/csspeech/samples) dentro do subdiretório `samples/batch`.

Você precisa personalizar o código de exemplo com suas informações de assinatura, a região de serviço, o URI de SAS apontando para o arquivo de áudio para transcrever e IDs de modelo, caso deseje usar um modelo acústico ou de linguagem personalizado.

[!code-csharp[Configuration variables for batch transcription](~/samples-cognitive-services-speech-sdk/samples/batch/csharp/program.cs#batchdefinition)]

O código de exemplo configurará o cliente e enviará a solicitação de transcrição. Em seguida, ele pesquisará informações de status e imprimirá detalhes sobre o progresso da transcrição.

[!code-csharp[Code to check batch transcription status](~/samples-cognitive-services-speech-sdk/samples/batch/csharp/program.cs#batchstatus)]

Para obter detalhes completos sobre as chamadas anteriores, consulte nosso [documento do Swagger](https://westus.cris.ai/swagger/ui/index). Para obter o exemplo completo mostrado aqui, vá para [GitHub](https://aka.ms/csspeech/samples) no subdiretório `samples/batch`.

Anote a configuração assíncrona para postar áudio e receber o status da transcrição. O cliente que você cria é um cliente HTTP .NET. Há um método `PostTranscriptions` para enviar os detalhes do arquivo de áudio e um método `GetTranscriptions` para receber os resultados. `PostTranscriptions` retorna um identificador e `GetTranscriptions` o usa para criar um identificador para obter o status de transcrição.

O código de exemplo atual não especifica um modelo personalizado. O serviço usa os modelos de linha de base para transcrever o arquivo ou os arquivos. Para especificar os modelos, você pode passar o mesmo método que as IDs de modelo para o acústica e o modelo de linguagem.

> [!NOTE]
> Para transcrições de linha de base, você não precisa declarar a ID para os modelos de linha de base. Se você especificar apenas uma ID de modelo de linguagem (e nenhuma ID de modelo acústico), um modelo acústico correspondente será selecionado automaticamente. Se você especificar apenas uma ID de modelo acústico, um modelo de idioma correspondente será selecionado automaticamente.

## <a name="download-the-sample"></a>Transferir o exemplo

Você pode encontrar o exemplo no diretório `samples/batch` no repositório de [exemplo do GitHub](https://aka.ms/csspeech/samples).

> [!NOTE]
> Os trabalhos de transcrição do lote são agendados em uma base de melhor esforço, não há nenhuma estimativa de tempo para quando um trabalho mudar para o estado de execução. Uma vez no estado de execução, a transcrição real é processada mais rápido do que o áudio em tempo real.

## <a name="next-steps"></a>Passos seguintes

* [Obter a subscrição de avaliação de Voz](https://azure.microsoft.com/try/cognitive-services/)
