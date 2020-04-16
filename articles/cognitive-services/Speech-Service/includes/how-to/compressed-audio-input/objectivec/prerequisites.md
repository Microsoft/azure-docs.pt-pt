---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: trbye
ms.openlocfilehash: 7106e139108681e1908b20d2daac5e619a63555d
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81421982"
---
O manuseamento do áudio comprimido é implementado utilizando [gStreamer](https://gstreamer.freedesktop.org). Por razões de licenciamento, os binários GStreamer não são compilados e ligados ao SDK da Fala. Em vez disso, uma biblioteca de invólucros contendo estas funções precisa de ser construída e enviada com as aplicações usando o SDK.

Para construir esta biblioteca de invólucros, primeiro descarregue e instale o [GStreamer SDK](https://gstreamer.freedesktop.org/data/pkg/ios/1.16.0/gstreamer-1.0-devel-1.16.0-ios-universal.pkg). Em seguida, descarregue o projeto **Xcode** para a [biblioteca de invólucros.](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/objective-c/ios/compressed-streams/GStreamerWrapper)

Abra o projeto em **Xcode** e construa-o para o alvo **genérico do dispositivo iOS** -- *não* funcionará para construí-lo para um alvo específico.

O passo de construção gerará um pacote de enquadramento dinâmico `GStreamerWrapper.framework`com uma biblioteca dinâmica para todas as arquiteturas necessárias com o nome de .

Esta estrutura deve ser incluída em todas as aplicações que utilizam fluxos de áudio comprimidos com o serviço de fala SDK.

Aplique as seguintes definições no seu projeto **Xcode** para o concretizar:

1. Copie `GStreamerWrapper.framework` o que acabou de construir e o quadro do SDK do Discurso dos Serviços Cognitivos, que pode transferir daqui [para](https://aka.ms/csspeech/iosbinary)o diretório que contém o seu projeto de amostra.
1. Ajuste os caminhos para as estruturas nas *Definições*do Projeto .
   1. No separador **Geral** sob o cabeçalho **Embedded Binaries,** adicione a biblioteca SDK como uma estrutura: **Adicione binários incorporados** > **Adicione outros...** > Navegar ao diretório que escolheu e selecione ambos os quadros.
   1. Vá para o separador **Build Settings** (Definições de Compilação) e ative a definição **All** (Tudo).
1. Adicione o diretório `$(SRCROOT)/..` a _Framework Search Paths_ (Caminhos de Pesquisa da Arquitetura) no cabeçalho **Search Paths** (Caminhos de Pesquisa).
