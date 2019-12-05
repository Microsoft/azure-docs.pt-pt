---
title: Como usar a transcrição do lote – serviço de fala
titleSuffix: Azure Cognitive Services
description: Transcrição de batch é ideal se quiser transcrição de uma grande quantidade de áudio em armazenamento, como Blobs do Azure. Ao utilizar a API de REST dedicada, que pode apontar para arquivos de áudio com uma assinatura de acesso partilhado (SAS) URI e assincronamente receber transcrições.
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: panosper
ms.openlocfilehash: 2cccd17ce04b3954a7d0720d9ba25bbe792da3b6
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/04/2019
ms.locfileid: "74806342"
---
# <a name="why-use-batch-transcription"></a>Por que usar a transcrição de Batch?

Transcrição de batch é ideal se quiser transcrição de uma grande quantidade de áudio em armazenamento, como Blobs do Azure. Ao utilizar a API de REST dedicada, que pode apontar para arquivos de áudio com uma assinatura de acesso partilhado (SAS) URI e assincronamente receber transcrições.

## <a name="prerequisites"></a>Pré-requisitos

### <a name="subscription-key"></a>Chave de Subscrição

Com todas as funcionalidades do serviço de voz, criar uma chave de subscrição do [portal do Azure](https://portal.azure.com) seguindo nossas [guia de introdução](get-started.md). Se pretender obter transcrições de nossos modelos de linha de base, a criação de uma chave é tudo o que precisa fazer.

>[!NOTE]
> Uma assinatura padrão (S0) para o serviço de fala é necessária para usar a transcrição do lote. Chaves de subscrição gratuita (F0) não irão funcionar. Para obter mais informações, consulte [preços e limites](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

### <a name="custom-models"></a>Modelos personalizados

Se você planeja personalizar modelos acústicos ou de idioma, siga as etapas em [personalizar modelos acústicos](how-to-customize-acoustic-models.md) e [personalizar modelos de linguagem](how-to-customize-language-model.md). Para usar os modelos criados na transcrição do lote, você precisa de suas IDs de modelo. Essa ID não é a ID do ponto de extremidade que você encontra no modo de exibição detalhes do ponto de extremidade, é a ID do modelo que você pode recuperar ao selecionar os detalhes dos modelos.

## <a name="the-batch-transcription-api"></a>A API de transcrição do Batch

A API de transcrição do Batch oferece assíncrona transcrição de voz em texto, juntamente com funcionalidades adicionais. É uma API REST que expõe métodos para:

1. Criar pedidos de processamento de batch
1. Estado de consulta
1. Baixar o transcrições

> [!NOTE]
> A API de transcrição do Batch é ideal para os centros de chamada, que normalmente se acumular milhares de horas de áudio. Ele facilita transcrever grandes volumes de gravações de áudio.

### <a name="supported-formats"></a>Formatos suportados

A API de transcrição do Batch suporta os seguintes formatos:

| Formato | Codec | Velocidade de transmissão | Taxa de exemplo |
|--------|-------|---------|-------------|
| WAV | PCM | 16-bit | 8 ou 16 estéreo mono, kHz, |
| MP3 | PCM | 16-bit | 8 ou 16 estéreo mono, kHz, |
| OGG | OPUS | 16-bit | 8 ou 16 estéreo mono, kHz, |

Para fluxos de áudio estéreo, a API de transcrição do Batch divide o canal do esquerda e direito durante a transcrição. Os dois ficheiros JSON com o resultado são todos criados a partir de um canal único. Os carimbos de data / por expressão permitem aos programadores criar uma transcrição final ordenada. Essa solicitação de exemplo inclui propriedades de filtragem de profanação, pontuação e carimbos de data/hora de nível de palavra.

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
> A API de transcrição do Batch utiliza um serviço REST para pedir transcrições, o respetivo estado e resultados associados. Pode utilizar a API em qualquer linguagem. A seguinte secção descreve como a API é utilizada.

### <a name="configuration-properties"></a>Propriedades de configuração

Use essas propriedades opcionais para configurar a transcrição:

| Parâmetro | Descrição |
|-----------|-------------|
| `ProfanityFilterMode` | Especifica como lidar com linguagem inapropriada nos resultados de reconhecimento. Aceite os valores são `None` que desativa a filtragem de palavras ofensivas `masked` que substitui a linguagem inapropriada por asteriscos, `removed` linguagem inapropriada todos os que remove o resultado ou `tags` que adiciona as etiquetas de "linguagem inapropriada". A predefinição é `masked`. |
| `PunctuationMode` | Especifica como lidar com a pontuação nos resultados de reconhecimento. Aceite os valores são `None` que desativa a pontuação, `dictated` implica que pontuação explícita, `automatic` que permite que o Decodificador lidar com a pontuação, ou `dictatedandautomatic` que implica ditado marcas de pontuação ou automático. |
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

Tome nota da configuração assíncrona para áudio de lançamentos e a receção de estado de transcrição. O cliente que criou é um cliente de HTTP do .NET. Há uma `PostTranscriptions` método para enviar os detalhes do arquivo de áudio e um `GetTranscriptions` método para receber os resultados. `PostTranscriptions` Retorna um identificador, e `GetTranscriptions` utiliza para criar um identificador para obter o estado de transcrição.

O código de exemplo atual não especifica um modelo personalizado. O serviço utiliza os modelos de linha de base para fotografar o ficheiro ou ficheiros. Para especificar os modelos, pode passar o mesmo método que os IDs de modelo para o acústicos e o modelo de idioma.

> [!NOTE]
> Para transcrições de linha de base, você não precisa declarar a ID para os modelos de linha de base. Se você especificar apenas uma ID de modelo de linguagem (e nenhuma ID de modelo acústico), um modelo acústico correspondente será selecionado automaticamente. Se você especificar apenas uma ID de modelo acústico, um modelo de idioma correspondente será selecionado automaticamente.

## <a name="download-the-sample"></a>Transferir o exemplo

Você pode encontrar o exemplo no diretório `samples/batch` no repositório de [exemplo do GitHub](https://aka.ms/csspeech/samples).

> [!NOTE]
> Os trabalhos de transcrição do lote são agendados em uma base de melhor esforço, não há nenhuma estimativa de tempo para quando um trabalho mudar para o estado de execução. Uma vez no estado de execução, a transcrição real é processada mais rápido do que o áudio em tempo real.

## <a name="next-steps"></a>Passos seguintes

* [Obter a subscrição de avaliação de Voz](https://azure.microsoft.com/try/cognitive-services/)
