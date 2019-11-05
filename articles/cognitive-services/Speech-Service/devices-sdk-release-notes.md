---
title: Documentação do SDK de dispositivos de fala
titleSuffix: Azure Cognitive Services
description: Notas de versão – o que mudou nas versões mais recentes
services: cognitive-services
author: wsturman
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: wellsi
ms.openlocfilehash: ba1db1ccba6a1849756e75c9b9f7078371da5bfb
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73464737"
---
# <a name="release-notes-of-cognitive-services-speech-devices-sdk"></a>Notas de versão do SDK de dispositivos de fala de serviços cognitivas
As seções a seguir listam as alterações nas versões mais recentes.

## <a name="speech-devices-sdk-160"></a>SDK de dispositivos de fala 1.6.0:

*   Suporte ao [Azure Kinect DK](https://azure.microsoft.com/services/kinect-dk/) no Windows e Linux com [aplicativo de exemplo](https://aka.ms/sdsdk-download) comum
*   Atualizado o componente [SDK de fala](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) para a versão 1.6.0. Para obter mais informações, consulte suas [notas de versão](https://aka.ms/csspeech/whatsnew).

## <a name="speech-devices-sdk-151"></a>SDK de dispositivos de fala 1.5.1:

*   Inclua [transcrição de conversa](conversation-transcription-service.md) no aplicativo de exemplo.
*   Atualizado o componente [SDK de fala](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) para a versão 1.5.1. Para obter mais informações, consulte suas [notas de versão](https://aka.ms/csspeech/whatsnew).

## <a name="cognitive-services-speech-devices-sdk-150-2019-may-release"></a>SDK de dispositivos de fala dos serviços de cognitiva: 2019 – lançamento de maio

*   O SDK de dispositivos de fala agora está disponível e não é mais uma visualização restrita.
*   Atualizado o componente [SDK de fala](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) para a versão 1.5.0. Para obter mais informações, consulte suas [notas de versão](https://aka.ms/csspeech/whatsnew).
*   A nova tecnologia de palavra-chave traz melhorias significativas de qualidade, consulte alterações significativas.
*   Novo pipeline de processamento de áudio para um reconhecimento de campo aprimorado.

**Alterações recentes**

*   Devido à nova palavra-chave Technology, todas as palavras-chave devem ser recriadas em nosso portal de palavra-chave aprimorado. Para remover totalmente as palavras-chave antigas do dispositivo, desinstale o aplicativo antigo.
    - desinstalação de ADB com. Microsoft. coginitiveservices. Speech. Samples. sdsdkstarterapp

## <a name="cognitive-services-speech-devices-sdk-140-2019-apr-release"></a>SDK de dispositivos de fala de serviços cognitivas 1.4.0:2019 – versão de abril

* Atualizado o componente [SDK de fala](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) para a versão 1.4.0. Para obter mais informações, consulte suas [notas de versão](https://aka.ms/csspeech/whatsnew).

## <a name="cognitive-services-speech-devices-sdk-131-2019-mar-release"></a>SDK de dispositivos de fala de serviços cognitivas 1.3.1:2019 – versão de mar

* Atualizado o componente [SDK de fala](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) para a versão 1.3.1. Para obter mais informações, consulte suas [notas de versão](https://aka.ms/csspeech/whatsnew).
*   Tratamento de palavra-chave atualizado, consulte alterações significativas.
*   O aplicativo de exemplo adiciona a opção de linguagem para reconhecimento de fala e tradução.

**Alterações recentes**

*   [A instalação de uma palavra-chave](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-devices-sdk-create-kws) foi simplificada, agora faz parte do aplicativo e não precisa de instalação separada no dispositivo.
*   O reconhecimento de palavra-chave foi alterado e há suporte para dois eventos.
    - RecognizingKeyword, indica que o resultado de fala contém o texto da palavra-chave (não verificado).
    - RecognizedKeyword, indica que o reconhecimento de palavra-chave foi concluído reconhecendo a palavra-chave fornecida.


## <a name="cognitive-services-speech-devices-sdk-110-2018-nov-release"></a>SDK 1.1.0 dos dispositivos de fala dos serviços cognitivas: 2018 – versão de novembro

* Atualizado o componente [SDK de fala](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) para a versão 1.1.0. Para obter mais informações, consulte suas [notas de versão](https://aka.ms/csspeech/whatsnew).
* A precisão do reconhecimento de fala de campo distante foi aprimorada com nosso algoritmo de processamento de áudio aprimorado.
* Aplicativo de exemplo suporte de reconhecimento de fala em chinês adicionado.

## <a name="cognitive-services-speech-devices-sdk-101-2018-oct-release"></a>SDK de dispositivos de fala de serviços cognitivas 1.0.1:2018 – versão de outubro

* Atualizado o componente [SDK de fala](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) para a versão 1.0.1. Para obter mais informações, consulte suas [notas de versão](https://aka.ms/csspeech/whatsnew).
* A precisão do reconhecimento de fala será aprimorada com nosso algoritmo de processamento de áudio aprimorado  
* Um bug de sessão de áudio de reconhecimento contínuo é fixo.

**Alterações recentes**

* Com essa versão, várias alterações significativas são introduzidas. Consulte [esta página](https://aka.ms/csspeech/breakingchanges_1_0_0) para obter detalhes relacionados às APIs.
* Os arquivos de modelo KWS não são compatíveis com o SDK do Speech Devices. Os arquivos de palavra-chave existentes serão excluídos depois que os novos arquivos de palavra-chave forem gravados no dispositivo.

## <a name="cognitive-services-speech-devices-sdk-050-2018-aug-release"></a>SDK de dispositivos de fala de serviços cognitivas 0.5.0:2018 – versão de agosto

* Melhoria da precisão do reconhecimento de fala corrigindo um bug no código de processamento de áudio.
* Atualizado o componente [SDK de fala](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) para a versão 0.5.0. Para obter mais informações, consulte suas [notas de versão](releasenotes.md#cognitive-services-speech-sdk-050-2018-july-release).

## <a name="cognitive-services-speech-devices-sdk-0212733-2018-may-release"></a>SDK de dispositivos de fala de serviços cognitivas 0.2.12733:2018 – lançamento de maio

A primeira versão de visualização pública do SDK dos dispositivos de fala dos serviços cognitivas.
