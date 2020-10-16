---
title: Visão geral do reconhecimento de oradores - Serviço de fala
titleSuffix: Azure Cognitive Services
description: O Reconhecimento de Altifalantes fornece algoritmos que verificam e identificam os altifalantes pelas suas características de voz únicas utilizando biometria de voz. O Reconhecimento dos Oradores é usado para responder à pergunta "quem está a falar?". Este artigo é uma visão geral dos benefícios e capacidades do serviço de reconhecimento de altifalantes.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/02/2020
ms.author: trbye
ms.custom: cog-serv-seo-aug-2020
keywords: reconhecimento de colunas, biometria de voz
ms.openlocfilehash: 2c5b73b93c22ef27d7b68455f5e1e5108f25c984
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89397264"
---
# <a name="what-is-speaker-recognition"></a>O que é o Reconhecimento de Orador?

O serviço de Reconhecimento de Altifalantes fornece algoritmos que verificam e identificam os altifalantes pelas suas características de voz únicas utilizando biometria de voz. O Reconhecimento dos Oradores é usado para responder à pergunta "quem está a falar?". Fornece dados de formação áudio para um único altifalante, o que cria um perfil de inscrição baseado nas características únicas da voz do orador. Em seguida, pode cruzar as amostras de voz áudio contra este perfil para verificar se o altifalante é a mesma pessoa (verificação do altifalante) ou cruzar amostras de voz áudio contra um *grupo* de perfis de altifalantes inscritos, para ver se corresponde a algum perfil do grupo (identificação do altifalante). Em contraste, [a diarização](batch-transcription.md#speaker-separation-diarization) dos altifalantes agrupe segmentos de áudio por altifalante numa operação de lote.

## <a name="speaker-verification"></a>Verificação de Orador

A Verificação do Altifalante simplifica o processo de verificação da identidade de um altifalante inscrito com frases-passe ou entrada de voz em formato livre. Pode ser usado para verificar indivíduos para compromissos seguros e sem atritos com clientes numa ampla gama de soluções, desde a verificação de identidade do cliente em call centers até ao acesso a instalações sem contacto.

### <a name="how-does-speaker-verification-work"></a>Como funciona a Verificação do Orador?

:::image type="content" source="media/speaker-recognition/speaker-rec.png" alt-text="Fluxograma de verificação do altifalante.":::

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

## <a name="common-questions-and-solutions"></a>Perguntas e soluções comuns

| Pergunta | Solução |
|---------|----------|
| Para que cenários pode ser utilizado o Reconhecimento do Orador? | Verificação do cliente de call center, check-in do paciente baseado em voz, transcrição de reunião, personalização de dispositivos multiutilizadores|
| Qual é a diferença entre Identificação e Verificação? | A identificação é o processo de deteção de qual membro de um grupo de oradores está a falar. A verificação é o ato de confirmar que um orador corresponde a uma voz conhecida ou **inscrita.**|
| Qual é a diferença entre a verificação independente de texto e texto? | A verificação dependente de texto requer uma frase-passe específica tanto para a inscrição como para o reconhecimento. A verificação independente de texto requer uma amostra de voz mais longa para a inscrição, mas qualquer coisa pode ser falada, incluindo durante o reconhecimento.|
| Que línguas são apoiadas? | Inglês, Francês, Espanhol, Chinês, Alemão, Italiano, Japonês e Português |
| Que regiões de Azure são apoiadas? | O Speaker Recognition é um serviço de pré-visualização, e atualmente apenas disponível na região oeste dos EUA.|
| Que formatos áudio são suportados? | Mono 16 bit, 16kHz PCM-codificado WAV |
| **Aceitar** e **Rejeitar** respostas não são precisas, como é que se afina o limiar? | Uma vez que o limiar ideal varia muito com cenários, a API decide se aceita ou "Rejeitar" simplesmente com base num limiar padrão de 0,5. Os utilizadores avançados são aconselhados a anular a decisão por defeito e a afinar o resultado com base no seu próprio cenário. |
| Pode inscrever um orador várias vezes? | Sim, para verificação dependente de texto, pode inscrever um altifalante até 50 vezes. Para verificação independente de texto ou identificação de altifalantes, pode inscrever-se com até 300 segundos de áudio. |
| Que dados são armazenados em Azure? | O áudio de inscrição é armazenado no serviço até que o perfil de voz seja [eliminado](speaker-recognition-basics.md#deleting-voice-profile-enrollments). As amostras de áudio de reconhecimento não são retidas ou armazenadas. |

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> * Preencha o artigo básico de reconhecimento de [altifalantes](speaker-recognition-basics.md) para um ensaio de padrões de design comuns que pode usar nas suas aplicações.
> * Consulte o tutorial de [vídeo](https://azure.microsoft.com/resources/videos/speaker-recognition-text-independent-verification-developer-tutorial/) para verificar o orador independente de texto.
