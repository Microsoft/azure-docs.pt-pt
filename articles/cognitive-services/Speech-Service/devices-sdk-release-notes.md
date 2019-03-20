---
title: Documentação do SDK de dispositivos de voz
titleSuffix: Azure Cognitive Services
description: Notas de versão – o que foi alterado nas versões mais recentes
services: cognitive-services
author: wsturman
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 08/07/2018
ms.author: gracez
ms.openlocfilehash: 4d802b9f71edee1eec4b2c92881e2a8796db2865
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58005517"
---
# <a name="release-notes-of-cognitive-services-speech-devices-sdk"></a>Notas de versão do SDK de dispositivos de voz dos serviços cognitivos

As seguintes alterações de lista secções nas versões mais recentes.

## <a name="cognitive-services-speech-devices-sdk-131-2019-mar-release"></a>Dispositivos de voz dos serviços cognitivos SDK 1.3.1: Versão de Março de 2019 

* Atualizar o [SDK de voz](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) componente para a versão 1.3.1. Para obter mais informações, consulte seus [notas de versão](https://aka.ms/csspeech/whatsnew). 
*   Palavra de reativação atualizado manipulação, veja alterações significativas.
*   Aplicação de exemplo adiciona a escolha de idioma para o reconhecimento de fala e a tradução.

**Alterações recentes** 

*   [Instalação de uma palavra de reativação](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-devices-sdk-qsg#run-a-sample-application) foi simplificado, faz agora parte da aplicação e não precisa de uma instalação separada no dispositivo.
*   O reconhecimento de word reativação foi alterado e dois eventos são suportados.
    - RecognizingKeyword, indica o resultado de conversão de voz contém o texto de palavra-chave (não verificado).
    - RecognizedKeyword, indica esse reconhecimento de palavra-chave concluído ao reconhecer a palavra-chave determinada.


## <a name="cognitive-services-speech-devices-sdk-110-2018-nov-release"></a>Dispositivos de voz dos serviços cognitivos SDK 1.1.0: Versão de Novembro de 2018 

* Atualizar o [SDK de voz](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) componente para a versão 1.1.0. Para obter mais informações, consulte seus [notas de versão](https://aka.ms/csspeech/whatsnew). 
* Sem dúvida precisão de reconhecimento de voz de campo foi aprimorada com o nosso algoritmo de processamento de áudio aprimorada.
* Aplicação de exemplo foi adicionado suporte de reconhecimento de voz chinês.

## <a name="cognitive-services-speech-devices-sdk-101-2018-oct-release"></a>Dispositivos de voz dos serviços cognitivos SDK 1.0.1: Versão de Outubro de 2018 

* Atualizar o [SDK de voz](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) componente para a versão 1.0.1. Para obter mais informações, consulte seus [notas de versão](https://aka.ms/csspeech/whatsnew). 
* Precisão de reconhecimento de voz será aperfeiçoada com nosso algoritmo de processamento de áudio aprimorada  
* Um bug de sessão de áudio de reconhecimento contínua é fixa.

**Alterações recentes** 

* Com esta versão é introduzido um número de alterações de última hora. Verifique se existem [esta página](https://aka.ms/csspeech/breakingchanges_1_0_0) para obter mais detalhes relacionados com as APIs. 
* Os arquivos de modelo KWS não são compatíveis com o SDK de dispositivos de voz 1.0.1. Os arquivos do Word reativação existentes serão eliminados depois dos novos ficheiros de reativação Word são escritos para o dispositivo. 

## <a name="cognitive-services-speech-devices-sdk-050-2018-aug-release"></a>Dispositivos de voz dos serviços cognitivos SDK versão 0.5.0: Versão de Agosto de 2018

* Melhorada a precisão de reconhecimento de fala corrigindo um bug no código de processamento de áudio.
* Atualizar o [SDK de voz](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) componente para a versão versão 0.5.0. Para obter mais informações, consulte seus [notas de versão](releasenotes.md#cognitive-services-speech-sdk-050-2018-july-release).

## <a name="cognitive-services-speech-devices-sdk-0212733-2018-may-release"></a>Dispositivos de voz dos serviços cognitivos SDK 0.2.12733: Versão de Maio de 2018

A primeira versão de pré-visualização pública do SDK de dispositivos de voz dos serviços cognitivos.
