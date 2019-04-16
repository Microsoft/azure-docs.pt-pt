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
ms.openlocfilehash: b389d86fe4d23e3f4ee1c66e4270a74351098129
ms.sourcegitcommit: 48a41b4b0bb89a8579fc35aa805cea22e2b9922c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/15/2019
ms.locfileid: "59579364"
---
# <a name="why-use-batch-transcription"></a>Por que usar a transcrição de Batch?

Transcrição de batch é ideal se quiser transcrição de uma grande quantidade de áudio em armazenamento, como Blobs do Azure. Ao utilizar a API de REST dedicada, que pode apontar para arquivos de áudio com uma assinatura de acesso partilhado (SAS) URI e assincronamente receber transcrições.

## <a name="prerequisites"></a>Pré-requisitos

### <a name="subscription-key"></a>Chave de Subscrição

Com todas as funcionalidades do serviço de voz, criar uma chave de subscrição do [portal do Azure](https://portal.azure.com) seguindo nossas [guia de introdução](get-started.md). Se pretender obter transcrições de nossos modelos de linha de base, a criação de uma chave é tudo o que precisa fazer.

>[!NOTE]
> Uma subscrição standard (S0) para serviços de voz é necessário para utilizar a transcrição do batch. Chaves de subscrição gratuita (F0) não irão funcionar. Para obter mais informações, consulte [preços e limites](https://azure.microsoft.com/en-us/pricing/details/cognitive-services/speech-services/).

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
  "models": ["<optional acoustic model ID>, <optional language model ID>"],
  "locale": "<local to us, for example en-US>",
  "name": "<user define name of the transcription batch>",
  "description": "<optional description of the transcription>",
  "properties": {
    "ProfanityFilterMode": "Masked",
    "PunctuationMode": "DictatedAndAutomatic",
    "AddWordLevelTimestamps" : "True"
  }
}
```

> [!NOTE]
> A API de transcrição do Batch utiliza um serviço REST para pedir transcrições, o respetivo estado e resultados associados. Pode utilizar a API em qualquer linguagem. A seguinte secção descreve como a API é utilizada.

### <a name="configuration-properties"></a>Propriedades de configuração

| Parâmetro | Descrição | Obrigatório / opcional |
|-----------|-------------|---------------------|
| `ProfanityFilterMode` | Especifica como lidar com linguagem inapropriada nos resultados de reconhecimento. Aceite os valores são `none` que desativa a filtragem de palavras ofensivas `masked` que substitui a linguagem inapropriada por asteriscos, `removed` linguagem inapropriada todos os que remove o resultado ou `tags` que adiciona as etiquetas de "linguagem inapropriada". A predefinição é `masked`. | Opcional |
| `PunctuationMode` | Especifica como lidar com a pontuação nos resultados de reconhecimento. Aceite os valores são `none` que desativa a pontuação, `dictated` implica que pontuação explícita, `automatic` que permite que o Decodificador lidar com a pontuação, ou `dictatedandautomatic` que implica ditado marcas de pontuação ou automático. | Opcional |
 | `AddWordLevelTimestamps` | Especifica se os carimbos de nível de word devem ser adicionados à saída. Aceite os valores são `true` que permite aos carimbos de nível do word e `false` (o valor predefinido) para desabilitá-lo. | Opcional |

### <a name="storage"></a>Armazenamento

O batch suporta transcrição [armazenamento de Blobs do Azure](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview) para a leitura de áudio e transcrições de escrita para armazenamento.

## <a name="webhooks"></a>Webhooks 

Sondagem de status de transcrição pode não ser o melhor desempenho ou proporcionar a melhor experiência de utilizador. Para consultar para obter o estado, pode registrar retornos de chamada, o que o cliente serão notificado quando as tarefas de transcrição de longa execução forem concluídas.

Para obter mais detalhes, consulte [Webhooks](webhooks.md).

## <a name="sample-code"></a>Código de exemplo

O exemplo completo está disponível na [repositório de exemplo do GitHub](https://aka.ms/csspeech/samples) dentro do `samples/batch` subdiretório.

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
