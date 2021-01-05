---
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 01/22/2020
ms.author: trbye
ms.openlocfilehash: dc569050b78a5797808f2e2e000019ba516ba22e
ms.sourcegitcommit: 44844a49afe8ed824a6812346f5bad8bc5455030
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/23/2020
ms.locfileid: "97739246"
---
**Interativo**
- Destinado a cenários de comando e controlo.
- Tem um valor de tempo de segmentação de X.
- No final de uma expressão reconhecida, o serviço deixa de processar áudio a partir desse ID de pedido e termina a curva. A ligação não está fechada.
- O limite máximo para o reconhecimento é de 20.
- A chamada típica de carbono para invocar `RecognizeOnceAsync` é.

**Conversação**
- Destinado a reconhecimentos mais longos.
- Tem um valor de tempo de segmentação de Y. (Y!= X)
- Processará várias expressões completas sem terminar a volta.
- Acabará com a volta por muito silêncio.
- O carbono continuará com um novo ID de pedido e reproduzir o áudio conforme necessário.
- O serviço desligar-se-á à força após 10 minutos de reconhecimento da fala.
- O carbono reconectará e reproduzirá áudio não reconhecido.
- Invocado em Carbono `StartContinuousRecognition` com.

**Ditado**
- Permite que os utilizadores especifiquem a pontuação falando-a.
- Invocado em Carbono especificando `EnableDictation` no `SpeechConfig` objeto independentemente da chamada da API que inicia o reconhecimento.
- O agrupamento de<sup>1ª</sup> festa devolve `speech.fragment` mensagens para resultados intermédios, as mensagens de retorno<sup>do 3º</sup> `speech.hypothesis` partido.