---
title: Serviço de Reconhecimento de Altifalantes Azure
titleSuffix: Azure Cognitive Services
description: O Reconhecimento de Altifalantes de Serviços Cognitivos Azure fornece algoritmos que verificam e identificam os altifalantes pelas suas características de voz únicas. O Reconhecimento dos Oradores é usado para responder à pergunta "quem está a falar?".
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/27/2020
ms.author: trbye
ms.openlocfilehash: 2d4ce6f274efbd4d8afe2ac48856b0fc312f0a09
ms.sourcegitcommit: 223cea58a527270fe60f5e2235f4146aea27af32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/01/2020
ms.locfileid: "84261788"
---
# <a name="what-is-the-azure-speaker-recognition-service"></a>O que é o serviço de reconhecimento de altifalantes Azure?

O serviço de Reconhecimento de Altifalantes fornece algoritmos que verificam e identificam os altifalantes pelas suas características de voz únicas. O Reconhecimento dos Oradores é usado para responder à pergunta "quem está a falar?". Fornece dados de formação áudio para um único altifalante, o que cria um perfil de inscrição baseado nas características únicas da voz do orador. Em seguida, pode cruzar as amostras de voz áudio contra este perfil para verificar se o altifalante é a mesma pessoa (verificação do altifalante) ou cruzar amostras de voz áudio contra um *grupo* de perfis de altifalantes inscritos, para ver se corresponde a algum perfil do grupo (identificação do altifalante). Em contraste, [a diarização](batch-transcription.md#speaker-separation-diarization) dos altifalantes agrupe segmentos de áudio por altifalante numa operação de lote.

## <a name="speaker-verification"></a>Verificação de Orador

A Verificação do Altifalante simplifica o processo de verificação da identidade de um altifalante inscrito com frases-passe ou entrada de voz em formato livre. Pode ser usado para verificar indivíduos para compromissos seguros e sem atritos com clientes numa ampla gama de soluções, desde a verificação de identidade do cliente em call centers até ao acesso a instalações sem contacto.

### <a name="how-does-speaker-verification-work"></a>Como funciona a Verificação do Orador?

![Como funciona a verificação do orador](media/speaker-recognition/speaker-rec.png)

A verificação do altifalante pode ser dependente de texto ou independente de texto. **A** verificação dependente de texto significa que os altifalantes têm de escolher a mesma palavra-passe para utilizar durante as fases de inscrição e verificação. A verificação **independente de textos** significa que os falantes podem falar em linguagem quotidiana nas frases de inscrição e verificação.

Para a verificação **dependente de texto,** a voz do orador é inscrita dizendo uma palavra-passe a partir de um conjunto de frases predefinidas. As características de voz são extraídas da gravação áudio para formar uma assinatura de voz única, enquanto a frase de passe escolhida também é reconhecida. Juntos, a assinatura de voz e a palavra-passe são usadas para verificar o altifalante. 

**A** verificação independente por texto não tem restrições sobre o que o orador diz durante a inscrição ou na amostra de áudio a verificar, uma vez que apenas extrai características de voz para marcar semelhanças. 

As APIs não se destinam a determinar se o áudio é de uma pessoa viva ou de uma imitação/gravação de um altifalante inscrito. 

## <a name="speaker-identification"></a>Identificação de Orador

A identificação do altifalante é utilizada para determinar a identidade de um orador desconhecido dentro de um grupo de altifalantes inscritos. A identificação dos altifalantes permite-lhe atribuir a fala a colunas individuais e desbloquear valor de cenários com vários altifalantes, tais como:

* Soluções de apoio para a produtividade do encontro remoto 
* Construa personalização de dispositivos multiutilizadores

### <a name="how-does-speaker-identification-work"></a>Como funciona a Identificação do Orador?

A inscrição para identificação de altifalantes é **independente de texto**, o que significa que não há restrições ao que o orador diz no áudio. Semelhante à Verificação do Altifalante, na fase de inscrição a voz do orador é gravada e as características de voz são extraídas para formar uma assinatura de voz única. Na fase de identificação, a amostra de voz de entrada é comparada com uma lista especificada de vozes inscritas (até 50 em cada pedido).

## <a name="data-security-and-privacy"></a>Segurança e privacidade dos dados

Os dados de inscrição dos altifalantes são armazenados num sistema seguro, incluindo o áudio de voz para inscrição e as funcionalidades de assinatura de voz. O áudio de fala para a inscrição só é usado quando o algoritmo é atualizado, e as funcionalidades precisam de ser extraídas novamente. O serviço não mantém a gravação de voz ou as características de voz extraídas que são enviadas para o serviço durante a fase de reconhecimento. 

Controla-se quanto tempo os dados devem ser retidos. Pode criar, atualizar e apagar dados de inscrição para colunas individuais através de chamadas API. Quando a subscrição for eliminada, todos os dados de inscrição do orador associados à subscrição também serão eliminados. 

Tal como acontece com todos os recursos dos Serviços Cognitivos, os desenvolvedores que utilizam o serviço de Reconhecimento de Altifalantes devem estar cientes das políticas da Microsoft sobre os dados dos clientes. Deve certificar-se de que recebeu as permissões adequadas dos utilizadores para reconhecimento de altifalantes. Para mais informações, consulte a [página de Serviços Cognitivos](https://azure.microsoft.com/support/legal/cognitive-services-compliance-and-privacy/)   no Microsoft Trust Center. 

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> * Consulte o tutorial de [vídeo](https://azure.microsoft.com/resources/videos/speaker-recognition-text-independent-verification-developer-tutorial/) para verificar o orador independente de texto.
