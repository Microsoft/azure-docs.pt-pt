---
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 01/22/2020
ms.author: dapine
ms.openlocfilehash: bb177963b824e6d5d80d5f60e8b9f995b6ecd121
ms.sourcegitcommit: c5661c5cab5f6f13b19ce5203ac2159883b30c0e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/01/2020
ms.locfileid: "80537012"
---
## <a name="speech-modes"></a>Modos de fala

**Interativo**
- Destinado a cenários de comando e controlo.
- Tem um tempo de segmentação fora do valor de X.
- No final de uma expressão reconhecida, o serviço para de processar áudio a partir desse pedido de identificação e termina a volta. A ligação não está fechada.
- O limite máximo para reconhecimento é de 20 anos.
- Chamada típica de `RecognizeOnceAsync`carbono para invocar é .

**Conversação**
- Destinado a reconhecimentos de maior execução.
- Tem um tempo de segmentação fora do valor de Y. (Y!= X)
- Processará várias expressões completas sem terminar a volta.
- Acabará com a volta por muito silêncio.
- O carbono continuará com um novo pedido de identificação e reprodução de áudio conforme necessário.
- O serviço desligar-se-á à força após 10 minutos de reconhecimento da fala.
- O carbono reconectar-se-á e reproduzirá áudio não reconhecido.
- Invocado em `StartContinuousRecognition`Carbono com .

**Ditado**
- Permite que os utilizadores especifiquem a pontuação falando-a.
- Invocado em Carbono `EnableDictation` especificando `SpeechConfig` sobre o objeto independentemente da chamada da API que inicia o reconhecimento.
- O<sup>1º</sup> agrupamento `speech.fragment` partidário devolve mensagens para resultados intermédios, as mensagens de devolução `speech.hypothesis` do<sup>3º</sup> partido.