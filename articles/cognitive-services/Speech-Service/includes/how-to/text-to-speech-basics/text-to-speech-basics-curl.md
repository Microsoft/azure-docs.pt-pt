---
author: v-jaswel
ms.service: cognitive-services
ms.topic: include
ms.date: 10/09/2020
ms.author: v-jawe
ms.openlocfilehash: 8a877e1773431053c5ad7344209076cb868a0ee3
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/10/2020
ms.locfileid: "94424790"
---
Neste arranque rápido, aprende-se a converter texto em discurso utilizando o serviço de fala e o cURL.

Para uma visão de alto nível dos conceitos text-to-speech, consulte o artigo [de visão geral.](../../../text-to-speech.md)

## <a name="prerequisites"></a>Pré-requisitos

Este artigo pressupõe que tem uma conta Azure e subscrição do serviço de fala. Se não tiver uma conta e subscrição, [experimente gratuitamente o serviço Desemação](../../../overview.md#try-the-speech-service-for-free).

## <a name="convert-text-to-speech"></a>Converter texto em discurso

Com um pedido de comando, executar o seguinte comando. Terá de inserir os seguintes valores no comando.
- A chave de subscrição do serviço de discurso.
- A sua região de serviço de discurso.

Pode também pretender alterar os seguintes valores.
- O `X-Microsoft-OutputFormat` valor do cabeçalho, que controla o formato de saída de áudio. Pode encontrar uma lista de formatos de saída de áudio suportados na [referência API de REPOUSO text-to-speech](../../../rest-text-to-speech.md#audio-outputs).
- A voz de saída. Para obter uma lista de vozes disponíveis para o seu ponto final do Discurso, consulte a secção seguinte.
- O ficheiro de saída. Neste exemplo, direcionamos a resposta do servidor para um ficheiro chamado `output.wav` .

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/text-to-speech.sh":::

## <a name="list-available-voices-for-your-speech-endpoint"></a>Listar as vozes disponíveis para o seu ponto final do Discurso

Para listar as vozes disponíveis para o seu ponto final do Discurso, executar o seguinte comando.

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/get-voices.sh" id="request":::

Deve receber uma resposta como a seguinte.

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/get-voices.sh" id="response":::