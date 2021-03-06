---
author: v-jaswel
ms.service: cognitive-services
ms.topic: include
ms.date: 10/09/2020
ms.author: v-jawe
ms.openlocfilehash: 0964872d03d6e321d25d51a18edbb4a6f0be8a4f
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/10/2020
ms.locfileid: "94425483"
---
Neste arranque rápido, aprende-se a converter a fala em texto utilizando o serviço de fala e o cURL.

Para uma visão de alto nível dos conceitos Fala-a-Texto, consulte o artigo [de visão geral.](../../../speech-to-text.md)

## <a name="prerequisites"></a>Pré-requisitos

Este artigo pressupõe que tem uma conta Azure e subscrição do serviço de fala. Se não tiver uma conta e subscrição, [experimente gratuitamente o serviço Desemação](../../../overview.md#try-the-speech-service-for-free).

## <a name="convert-speech-to-text"></a>Converter voz em texto

Com um pedido de comando, executar o seguinte comando. Terá de inserir os seguintes valores no comando.
- A chave de subscrição do serviço de discurso.
- A sua região de serviço de discurso.
- O caminho do ficheiro áudio de entrada. Pode gerar ficheiros áudio utilizando [texto-a-discurso](../../../get-started-text-to-speech.md).

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speech-to-text.sh" id="request":::

Deve receber uma resposta como a seguinte.

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speech-to-text.sh" id="response":::

Para obter mais informações consulte a [referência API do REST de voz a texto](../../../rest-speech-to-text.md).