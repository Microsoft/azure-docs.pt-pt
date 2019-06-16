---
title: Como utilizar o Batch transcrição - serviços de voz
titlesuffix: Azure Cognitive Services
description: Transcrição de batch é ideal se quiser transcrição de uma grande quantidade de áudio em armazenamento, como Blobs do Azure. Ao utilizar a API de REST dedicada, que pode apontar para arquivos de áudio com uma assinatura de acesso partilhado (SAS) URI e assincronamente receber transcrições.
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 2/20/2019
ms.author: panosper
ms.custom: seodec18
ms.openlocfilehash: 1828cdce66104424cc7845fea89127219e6b77a0
ms.sourcegitcommit: e5dcf12763af358f24e73b9f89ff4088ac63c6cb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/14/2019
ms.locfileid: "67137271"
---
# <a name="why-use-batch-transcription"></a>Por que usar a transcrição de Batch?

Transcrição de batch é ideal se quiser transcrição de uma grande quantidade de áudio em armazenamento, como Blobs do Azure. Ao utilizar a API de REST dedicada, que pode apontar para arquivos de áudio com uma assinatura de acesso partilhado (SAS) URI e assincronamente receber transcrições.

## <a name="prerequisites"></a>Pré-requisitos

### <a name="subscription-key"></a>Chave de subscrição

Com todas as funcionalidades do serviço de voz, criar uma chave de subscrição do [portal do Azure](https://portal.azure.com) seguindo nossas [guia de introdução](get-started.md). Se pretender obter transcrições de nossos modelos de linha de base, a criação de uma chave é tudo o que precisa fazer.

>[!NOTE]
> Uma subscrição standard (S0) para serviços de voz é necessário para utilizar a transcrição do batch. Chaves de subscrição gratuita (F0) não irão funcionar. Para obter mais informações, consulte [preços e limites](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

### <a name="custom-models"></a>Modelos personalizados

Se pretender personalizar modelos acústicos ou linguagem, siga os passos em [personalizar modelos acústicos](how-to-customize-acoustic-models.md) e [personalizando modelos de linguagem](how-to-customize-language-model.md). Para utilizar os modelos criados na transcrição do batch tem suas IDs de modelo. Este ID não é o ID de ponto final que encontrar na vista de detalhes do ponto final, é o ID de modelo que pode recuperar ao selecionar os detalhes dos modelos.

## <a name="the-batch-transcription-api"></a>A API de transcrição do Batch

A API de transcrição do Batch oferece assíncrona transcrição de voz em texto, juntamente com funcionalidades adicionais. É uma API REST que expõe métodos para:

1. Criar pedidos de processamento de batch
1. Estado de consulta
1. Baixar o transcrições

> [!NOTE]
> A API de transcrição do Batch é ideal para os centros de chamada, que normalmente se acumular milhares de horas de áudio. Ela torna mais fácil de transcrever grandes volumes de gravações de áudio.

### <a name="supported-formats"></a>Formatos suportados

A API de transcrição do Batch suporta os seguintes formatos:

| Formato | Codec | Velocidade de transmissão | Taxa de exemplo |
|--------|-------|---------|-------------|
| WAV | PCM | 16-bit | 8 ou 16 estéreo mono, kHz, |
| MP3 | PCM | 16-bit | 8 ou 16 estéreo mono, kHz, |
| OGG | OPUS | 16-bit | 8 ou 16 estéreo mono, kHz, |

Para fluxos de áudio estéreo, a API de transcrição do Batch divide o canal do esquerda e direito durante a transcrição. Os dois ficheiros JSON com o resultado são todos criados a partir de um canal único. Os carimbos de data / por expressão permitem aos programadores criar uma transcrição final ordenada. Este pedido de exemplo inclui propriedades para filtragem de linguagem inapropriada, pontuação e carimbos de nível do word. 

### <a name="configuration"></a>Configuração

Parâmetros de configuração são fornecidos como JSON:

```json
{
  "recordingsUrl": "<URL to the Azure blob to transcribe>",
  "models": [{"Id":"<optional acoustic model ID>"},{"Id":"<optional language model ID>"}],
  "locale": "<locale to us, for example en-US>",
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

Utilize estas propriedades opcionais para configurar a transcrição:

| Parâmetro | Descrição |
|-----------|-------------|
| `ProfanityFilterMode` | Especifica como lidar com linguagem inapropriada nos resultados de reconhecimento. Aceite os valores são `none` que desativa a filtragem de palavras ofensivas `masked` que substitui a linguagem inapropriada por asteriscos, `removed` linguagem inapropriada todos os que remove o resultado ou `tags` que adiciona as etiquetas de "linguagem inapropriada". A predefinição é `masked`. |
| `PunctuationMode` | Especifica como lidar com a pontuação nos resultados de reconhecimento. Aceite os valores são `none` que desativa a pontuação, `dictated` implica que pontuação explícita, `automatic` que permite que o Decodificador lidar com a pontuação, ou `dictatedandautomatic` que implica ditado marcas de pontuação ou automático. |
 | `AddWordLevelTimestamps` | Especifica se os carimbos de nível de word devem ser adicionados à saída. Aceite os valores são `true` que permite aos carimbos de nível do word e `false` (o valor predefinido) para desabilitá-lo. |
 | `AddSentiment` | Especifica o sentimento deve ser adicionado para a expressão. Aceite os valores são `true` que permite que o sentimento por expressão e `false` (o valor predefinido) para desabilitá-lo. |

### <a name="storage"></a>Armazenamento

O batch suporta transcrição [armazenamento de Blobs do Azure](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview) para a leitura de áudio e transcrições de escrita para armazenamento.

## <a name="webhooks"></a>Webhooks 

Sondagem de status de transcrição pode não ser o melhor desempenho ou proporcionar a melhor experiência de utilizador. Para consultar para obter o estado, pode registrar retornos de chamada, o que o cliente serão notificado quando as tarefas de transcrição de longa execução forem concluídas.

Para obter mais detalhes, consulte [Webhooks](webhooks.md).

## <a name="speaker-separation-diarization"></a>Separação de orador (Diarization)

Diarization é o processo de separar oradores num trecho de áudio. Nosso pipeline do Batch suporta Diarization e é capaz de reconhecer dois oradores no gravações de canal mono.

Para pedir que o seu pedido de transcrição de áudio é processado para diarization, simplesmente precisa adicionar o parâmetro relevante na solicitação HTTP, conforme mostrado abaixo.

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

Carimbos de nível do Word também teria que ser "ativado" como indicam os parâmetros no pedido acima. 

O áudio correspondente irá conter os oradores identificados por um número (atualmente suportamos que apenas dois vozes, para que os oradores serão identificados como "1 orador ' e"Orador 2") seguido pela saída transcrição.

Observe também que Diarization não está disponível no gravações estéreo. Além disso, todos os JSON saída irá conter a marca de orador. Se não for utilizado diarization, ele mostrará ' orador: Nulo ' no JSON de saída.

Regiões suportadas estão listados abaixo.

| Idioma | Localidade |
|--------|-------|
| Português | en-US |
| Chinês | zh-CN |
| Deutsch | de-DE |

## <a name="sentiment"></a>Sentimento

Sentimento é um novo recurso na API de transcrição do Batch e é um recurso importante no domínio do Centro de chamada. Os clientes podem utilizar o `AddSentiment` parâmetros para os pedidos para 

1.  Obtenha informações sobre a satisfação do cliente
2.  Obtenha informações sobre o desempenho dos agentes (team colocar as chamadas)
3.  Identificar o ponto exato no tempo quando uma chamada demorou uma folheada numa direção negativa
4.  Identificar o que correu bem quando ativar chamadas negativas para positivo
5.  Identificar o que, como os clientes e o que eles não gosto sobre um produto ou um serviço

Sentimento é classificado por segmento de áudio em que um segmento de áudio é definido como o intervalo de tempo entre o início da expressão (deslocamento) e o silêncio de deteção de fim do fluxo de bytes. Todo o texto dentro desse segmento é utilizado para calcular o sentimento. NÃO podemos calcular quaisquer valores de sentimentos de agregação para a chamada de toda ou a voz inteiro de cada canal. Estas agregações restantes para o proprietário do domínio ainda mais a aplicar.

Sentimento é aplicado no formulário léxico.

Um exemplo de saída JSON é semelhante a abaixo:

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
A funcionalidade utiliza um modelo de sentimentos, que está atualmente na versão Beta.

## <a name="sample-code"></a>Código de exemplo

Amostras completas estão disponíveis no [repositório de exemplo do GitHub](https://aka.ms/csspeech/samples) dentro do `samples/batch` subdiretório.

Terá de personalizar o código de exemplo com as suas informações de subscrição, a região do serviço, o URI de SAS que aponta para o arquivo de áudio para transcreva e IDs de modelo, no caso de que pretende utilizar um modelo de idioma ou acústicos personalizado. 

[!code-csharp[Configuration variables for batch transcription](~/samples-cognitive-services-speech-sdk/samples/batch/csharp/program.cs#batchdefinition)]

O código de exemplo irá configurar o cliente e submeter o pedido de transcrição. Em seguida, irá efetuar consultas para impressão detalhes sobre o progresso de transcrição e informações de estado.

[!code-csharp[Code to check batch transcription status](~/samples-cognitive-services-speech-sdk/samples/batch/csharp/program.cs#batchstatus)]

Para obter detalhes completos sobre as chamadas anteriores, consulte nosso [documento Swagger](https://westus.cris.ai/swagger/ui/index). Para o exemplo completo mostrado aqui, aceda a [GitHub](https://aka.ms/csspeech/samples) no `samples/batch` subdiretório.

Tome nota da configuração assíncrona para áudio de lançamentos e a receção de estado de transcrição. O cliente que criou é um cliente de HTTP do .NET. Há uma `PostTranscriptions` método para enviar os detalhes do arquivo de áudio e um `GetTranscriptions` método para receber os resultados. `PostTranscriptions` Retorna um identificador, e `GetTranscriptions` utiliza para criar um identificador para obter o estado de transcrição.

O código de exemplo atual não especifica um modelo personalizado. O serviço utiliza os modelos de linha de base para fotografar o ficheiro ou ficheiros. Para especificar os modelos, pode passar o mesmo método que os IDs de modelo para o acústicos e o modelo de idioma.

> [!NOTE]
> Para transcrições de linha de base, não precisa declarar o ID para os modelos de linha de base. Se especificar apenas uma linguagem de ID de modelo (e nenhum ID de modelo acústico), um modelo acústico correspondente é selecionado automaticamente. Se especificar apenas um ID de modelo acústico, um modelo de idioma correspondente é selecionado automaticamente.

## <a name="download-the-sample"></a>Transferir o exemplo

Pode encontrar o exemplo a `samples/batch` diretório na [repositório de exemplo do GitHub](https://aka.ms/csspeech/samples).

> [!NOTE]
> São agendadas tarefas de transcrição do batch na base de melhor esforço, não há nenhuma estimativa de tempo para quando uma tarefa será alterado para o estado de execução. Uma vez no estado de execução, a transcrição real é processada mais depressa do que o áudio em tempo real.

## <a name="next-steps"></a>Passos Seguintes

* [Obter a subscrição de avaliação de Voz](https://azure.microsoft.com/try/cognitive-services/)
