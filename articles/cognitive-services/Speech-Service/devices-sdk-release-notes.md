---
title: Documentação SDK dispositivos de fala
titleSuffix: Azure Cognitive Services
description: As notas de lançamento fornecem um registo de atualizações, melhorias, correções de erros e alterações no SDK dos Dispositivos de Fala. Este artigo é atualizado com cada lançamento do SDK dispositivos de fala.
services: cognitive-services
author: wsturman
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: wellsi
ms.openlocfilehash: 93bedbf4275dacc3ec84e6df9c55e7e9a7b9d632
ms.sourcegitcommit: 31236e3de7f1933be246d1bfeb9a517644eacd61
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/04/2020
ms.locfileid: "82780917"
---
# <a name="release-notes-speech-devices-sdk"></a>Notas de lançamento: Dispositivos de fala SDK

As seguintes secções listam alterações nos lançamentos mais recentes.

## <a name="speech-devices-sdk-1110"></a>Dispositivos de fala SDK 1.11.0:

- Suporte para [geometrias arbitrárias](how-to-devices-microphone-array-configuration.md) de matriz de microfone e definição do ângulo de trabalho através de um ficheiro de [configuração](https://aka.ms/sdsdk-micarray-json).
- Suporte para [Urbetter DDK](http://www.urbetter.com/products_56/278.html).
- Binários lançados para o [altifalante GGEC](https://aka.ms/sdsdk-download-speaker) utilizados na nossa [amostra de Assistente](https://aka.ms/sdsdk-speaker)de Voz .
- Binários lançados para [Linux ARM32](https://aka.ms/sdsdk-download-linux-arm32) e [Linux ARM 64](https://aka.ms/sdsdk-download-linux-arm64) para Raspberry Pi e dispositivos similares.
- Atualizou o componente [Speech SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) para a versão 1.11.0. Para mais informações, consulte as suas notas de [lançamento.](https://aka.ms/csspeech/whatsnew)

## <a name="speech-devices-sdk-190"></a>Dispositivos de fala SDK 1.9.0:

- Binários iniciais para [Urbetter DDK](https://aka.ms/sdsdk-download-urbetter) (Linux ARM64) são fornecidos.
- Roobo v1 agora usa Maven para o SDK de Discurso
- Atualizou o componente [Speech SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) para a versão 1.9.0. Para mais informações, consulte as suas notas de [lançamento.](https://aka.ms/csspeech/whatsnew)

## <a name="speech-devices-sdk-170"></a>Dispositivos de fala SDK 1.7.0:

- A Linux ARM está agora apoiada.
- Binários iniciais para [Roobo v2 DDK](https://aka.ms/sdsdk-download-roobov2) são fornecidos (Linux ARM64).
- Os utilizadores `AudioConfig.fromDefaultMicrophoneInput()` do `AudioConfig.fromMicrophoneInput(deviceName)` Windows podem utilizar ou especificar o microfone a utilizar.
- O tamanho da biblioteca foi otimizado.
- Suporte para reconhecimento de várias voltas utilizando o mesmo objeto de reconhecimento de fala/intenção.
- Corrija o enforcamento ocasional que ocorreria enquanto parava o reconhecimento.
- As aplicações de amostra agora contêm um ficheiro sample participants.properties para demonstrar o formato do ficheiro.
- Atualizou o componente [Speech SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) para a versão 1.7.0. Para mais informações, consulte as suas notas de [lançamento.](https://aka.ms/csspeech/whatsnew)

## <a name="speech-devices-sdk-160"></a>Dispositivos de fala SDK 1.6.0:

- Suporte [Azure Kinect DK](https://azure.microsoft.com/services/kinect-dk/) no Windows e Linux com [aplicação](https://aka.ms/sdsdk-download) de amostra comum
- Atualizou o componente [Speech SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) para a versão 1.6.0. Para mais informações, consulte as suas notas de [lançamento.](https://aka.ms/csspeech/whatsnew)

## <a name="speech-devices-sdk-151"></a>Dispositivos de fala SDK 1.5.1:

- Inclua [a Transcrição](conversation-transcription-service.md) de Conversas na aplicação de amostras.
- Atualizou o componente [Speech SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) para a versão 1.5.1. Para mais informações, consulte as suas notas de [lançamento.](https://aka.ms/csspeech/whatsnew)

## <a name="speech-devices-sdk-150-2019-may-release"></a>Dispositivos de Fala SDK 1.5.0: lançamento de 2019-maio

- Dispositivos de Fala O SDK é agora GA e já não é uma pré-visualização fechada.
- Atualizou o componente [Speech SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) para a versão 1.5.0. Para mais informações, consulte as suas notas de [lançamento.](https://aka.ms/csspeech/whatsnew)
- A nova tecnologia de palavras-chave traz melhorias significativas de qualidade, ver Breaking Changes.
- Novo oleoduto de processamento de áudio para um melhor reconhecimento de campo.

**Alterações interruptivas**

- Devido à nova tecnologia de palavras-chave, todas as palavras-chave devem ser recriadas no nosso portal de palavras-chave melhorado. Para remover totalmente as palavras-chave antigas do dispositivo desinstalar a aplicação antiga.
  - adb uninstall com.microsoft.cognitiveservices.speech.samples.sdsdkstarterapp

## <a name="speech-devices-sdk-140-2019-apr-release"></a>Dispositivos de Fala SDK 1.4.0: Lançamento 2019-abr

- Atualizou o componente [Speech SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) para a versão 1.4.0. Para mais informações, consulte as suas notas de [lançamento.](https://aka.ms/csspeech/whatsnew)

## <a name="speech-devices-sdk-131-2019-mar-release"></a>Dispositivos de Fala SDK 1.3.1: Lançamento 2019-Mar

- Atualizou o componente [Speech SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) para a versão 1.3.1. Para mais informações, consulte as suas notas de [lançamento.](https://aka.ms/csspeech/whatsnew)
- Manuseamento atualizado de palavras-chave, ver Breaking Changes.
- A aplicação da amostra adiciona a escolha da linguagem tanto para reconhecimento da fala como para tradução.

**Alterações interruptivas**

- [A instalação de uma palavra-chave](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-devices-sdk-create-kws) foi simplificada, agora faz parte da aplicação e não necessita de instalação separada no dispositivo.
- O reconhecimento das palavras-chave mudou e dois eventos são apoiados.
  - `RecognizingKeyword,`indica que o resultado da fala contém texto de palavra-chave (não verificado).
  - `RecognizedKeyword`, indica que o reconhecimento de palavras-chave completou o reconhecimento da palavra-chave.

## <a name="speech-devices-sdk-110-2018-nov-release"></a>Dispositivos de Fala SDK 1.1.0: Lançamento de 2018-nov

- Atualizou o componente [Speech SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) para a versão 1.1.0. Para mais informações, consulte as suas notas de [lançamento.](https://aka.ms/csspeech/whatsnew)
- A precisão de reconhecimento da Fala de Far Field foi melhorada com o nosso algoritmo de processamento de áudio melhorado.
- A aplicação da amostra acrescentou apoio ao reconhecimento da fala chinesa.

## <a name="speech-devices-sdk-101-2018-oct-release"></a>Dispositivos de Fala SDK 1.0.1: Lançamento de 2018-out

- Atualizou o componente [Speech SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) para a versão 1.0.1. Para mais informações, consulte as suas notas de [lançamento.](https://aka.ms/csspeech/whatsnew)
- A precisão do reconhecimento da fala será melhorada com o nosso algoritmo de processamento de áudio melhorado
- É fixado um bug de sessão áudio de reconhecimento contínuo.

**Alterações interruptivas**

- Com esta libertação são introduzidas várias alterações de rutura. Consulte [esta página](https://aka.ms/csspeech/breakingchanges_1_0_0) para obter detalhes relativos às APIs.
- Os ficheiros do modelo KWS não são compatíveis com dispositivos de fala SDK 1.0.1. Os ficheiros de palavras-chave existentes serão eliminados após a escrita dos novos ficheiros de palavras-chave no dispositivo.

## <a name="speech-devices-sdk-050-2018-aug-release"></a>Dispositivos de Fala SDK 0.5.0: Lançamento de 2018-Aug

- Melhorou a precisão do reconhecimento da fala fixando um bug no código de processamento de áudio.
- Atualizou o componente [Speech SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) para a versão 0.5.0. Para mais informações, consulte as suas notas de [lançamento.](releasenotes.md#cognitive-services-speech-sdk-050-2018-july-release)

## <a name="speech-devices-sdk-0212733-2018-may-release"></a>Dispositivos de Fala SDK 0.2.12733: lançamento de 2018-maio

A primeira versão pública da SDK dos Dispositivos de Fala dos Serviços Cognitivos.
