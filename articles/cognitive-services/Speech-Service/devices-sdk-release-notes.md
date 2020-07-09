---
title: Documentação SDK de dispositivos de fala
titleSuffix: Azure Cognitive Services
description: As notas de lançamento fornecem um registo de atualizações, melhorias, correções de erros e alterações no SDK dos Dispositivos de Fala. Este artigo é atualizado com cada lançamento do SDK dos Dispositivos de Fala.
services: cognitive-services
author: wsturman
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: wellsi
ms.openlocfilehash: a2fe1c7c1ac8799d615c26fdaee40b92bf3e294b
ms.sourcegitcommit: 6fd28c1e5cf6872fb28691c7dd307a5e4bc71228
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/23/2020
ms.locfileid: "85212501"
---
# <a name="release-notes-speech-devices-sdk"></a>Notas de lançamento: Dispositivos de Fala SDK

As seguintes secções listam alterações nas versões mais recentes.

## <a name="speech-devices-sdk-1110"></a>Dispositivos de fala SDK 1.11.0:

- Suporte para [geometrias arbitrárias da matriz do microfone](how-to-devices-microphone-array-configuration.md) e definição do ângulo de trabalho através de um ficheiro de [configuração](https://aka.ms/sdsdk-micarray-json).
- Suporte para [Urbetter DDK](http://www.urbetter.com/products_56/278.html).
- Binários libertados para o [altifalante GGEC](https://aka.ms/sdsdk-download-speaker) utilizado na nossa [amostra de Voice Assistant](https://aka.ms/sdsdk-speaker).
- Lançou binários para [Linux ARM32](https://aka.ms/sdsdk-download-linux-arm32) e [Linux ARM 64](https://aka.ms/sdsdk-download-linux-arm64) para Raspberry Pi e dispositivos semelhantes.
- Atualize o componente [SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) do discurso para a versão 1.11.0. Para mais informações, consulte as suas [notas de lançamento.](https://aka.ms/csspeech/whatsnew)

## <a name="speech-devices-sdk-190"></a>Dispositivos de fala SDK 1.9.0:

- São fornecidos binários iniciais para [Urbetter DDK](https://aka.ms/sdsdk-download-urbetter) (Linux ARM64).
- Roobo v1 agora usa Maven para o Discurso SDK
- Atualize o componente [SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) do discurso para a versão 1.9.0. Para mais informações, consulte as suas [notas de lançamento.](https://aka.ms/csspeech/whatsnew)

## <a name="speech-devices-sdk-170"></a>Dispositivos de fala SDK 1.7.0:

- Linux ARM está agora apoiado.
- São fornecidos binários iniciais para [Roobo v2 DDK](https://aka.ms/sdsdk-download-roobov2) (Linux ARM64).
- Os utilizadores do Windows podem utilizar `AudioConfig.fromDefaultMicrophoneInput()` ou `AudioConfig.fromMicrophoneInput(deviceName)` especificar o microfone a utilizar.
- O tamanho da biblioteca foi otimizado.
- Suporte para reconhecimento de várias voltas utilizando o mesmo objeto de reconhecimento de voz/intenção.
- Corrija um problema ocasional em que o processo deixaria de responder enquanto parava de ser reconhecido.
- As aplicações da amostra contêm agora um ficheiro participantes da amostra.propriedades para demonstrar o formato do ficheiro.
- Atualize o componente [SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) do discurso para a versão 1.7.0. Para mais informações, consulte as suas [notas de lançamento.](https://aka.ms/csspeech/whatsnew)

## <a name="speech-devices-sdk-160"></a>Dispositivos de fala SDK 1.6.0:

- Suporte [Azure Kinect DK](https://azure.microsoft.com/services/kinect-dk/) no Windows e Linux com [aplicação](https://aka.ms/sdsdk-download) comum de amostra
- Atualize o componente [SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) do discurso para a versão 1.6.0. Para mais informações, consulte as suas [notas de lançamento.](https://aka.ms/csspeech/whatsnew)

## <a name="speech-devices-sdk-151"></a>Dispositivos de fala SDK 1.5.1:

- Incluir [a Transcrição de Conversação](conversation-transcription-service.md) na aplicação da amostra.
- Atualize o componente [SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) do discurso para a versão 1.5.1. Para mais informações, consulte as suas [notas de lançamento.](https://aka.ms/csspeech/whatsnew)

## <a name="speech-devices-sdk-150-2019-may-release"></a>Dispositivos de fala SDK 1.5.0: Lançamento 2019-maio

- Dispositivos de fala SDK é agora GA e já não é uma pré-visualização fechada.
- Atualize o componente [SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) do discurso para a versão 1.5.0. Para mais informações, consulte as suas [notas de lançamento.](https://aka.ms/csspeech/whatsnew)
- A nova tecnologia de palavras-chave traz melhorias significativas de qualidade, ver Breaking Changes.
- Novo oleoduto de processamento de áudio para um melhor reconhecimento de campo distante.

**Quebra de mudanças**

- Devido à nova tecnologia de palavras-chave, todas as palavras-chave devem ser recriadas no nosso portal de palavras-chave melhorado. Para remover completamente as palavras-chave antigas do dispositivo desinstale a antiga aplicação.
  - adb desinstalar com.microsoft.cognitiveservices.speech.samples.sdsdkstarterapp

## <a name="speech-devices-sdk-140-2019-apr-release"></a>Dispositivos de fala SDK 1.4.0: Lançamento 2019-abr

- Atualize o componente [SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) do discurso para a versão 1.4.0. Para mais informações, consulte as suas [notas de lançamento.](https://aka.ms/csspeech/whatsnew)

## <a name="speech-devices-sdk-131-2019-mar-release"></a>Dispositivos de fala SDK 1.3.1: Lançamento 2019-Mar

- Atualize o componente [SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) do discurso para a versão 1.3.1. Para mais informações, consulte as suas [notas de lançamento.](https://aka.ms/csspeech/whatsnew)
- Tratamento atualizado de palavras-chave, ver Breaking Changes.
- A aplicação da amostra adiciona a escolha da linguagem tanto para o reconhecimento da fala como para a tradução.

**Quebra de mudanças**

- [A instalação de uma palavra-chave](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-devices-sdk-create-kws) foi simplificada, agora faz parte da aplicação e não necessita de instalação separada no dispositivo.
- O reconhecimento de palavras-chave mudou e dois eventos são apoiados.
  - `RecognizingKeyword,`indica que o resultado da fala contém texto de palavra-chave (não verificado).
  - `RecognizedKeyword`, indica que o reconhecimento de palavras-chave foi concluído reconhecendo a palavra-chave dada.

## <a name="speech-devices-sdk-110-2018-nov-release"></a>Dispositivos de fala SDK 1.1.0: Lançamento 2018-nov

- Atualize o componente [SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) do discurso para a versão 1.1.0. Para mais informações, consulte as suas [notas de lançamento.](https://aka.ms/csspeech/whatsnew)
- A precisão de reconhecimento do Discurso de Far Field foi melhorada com o nosso algoritmo de processamento de áudio melhorado.
- A aplicação da amostra acrescentou suporte de reconhecimento de voz chinês.

## <a name="speech-devices-sdk-101-2018-oct-release"></a>Dispositivos de fala SDK 1.0.1: Lançamento 2018-out

- Atualize o componente [SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) do discurso para a versão 1.0.1. Para mais informações, consulte as suas [notas de lançamento.](https://aka.ms/csspeech/whatsnew)
- A precisão do reconhecimento de voz será melhorada com o nosso algoritmo de processamento de áudio melhorado
- Um bug de sessão áudio de reconhecimento contínuo é corrigido.

**Quebra de mudanças**

- Com este lançamento são introduzidas várias alterações de rutura. Por favor, consulte [esta página](https://aka.ms/csspeech/breakingchanges_1_0_0) para obter detalhes relativos às APIs.
- Os ficheiros do modelo KWS não são compatíveis com dispositivos de fala SDK 1.0.1. Os ficheiros de palavras-chave existentes serão eliminados após a escrita dos novos ficheiros de palavras-chave para o dispositivo.

## <a name="speech-devices-sdk-050-2018-aug-release"></a>Dispositivos de fala SDK 0.5.0: Lançamento 2018-agosto

- Melhorou a precisão do reconhecimento da fala fixando um bug no código de processamento de áudio.
- Atualize o componente [SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) do discurso para a versão 0.5.0. Para mais informações, consulte as suas [notas de lançamento.](releasenotes.md#cognitive-services-speech-sdk-050-2018-july-release)

## <a name="speech-devices-sdk-0212733-2018-may-release"></a>Dispositivos de Fala SDK 0.2.12733: Lançamento 2018-maio

O primeiro lançamento público de pré-visualização dos Dispositivos de Fala dos Serviços Cognitivos SDK.
