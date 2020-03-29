---
title: Dispositivos de Fala SDK Roobo Smart Audio Dev Kit v2 - Serviço de Discurso
titleSuffix: Azure Cognitive Services
description: Pré-requisitos e instruções para começar com o SDK, Roobo Smart Audio Dev Kit v2.
services: cognitive-services
author: anushapatnala
manager: wellsi
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: v-anusp
ms.openlocfilehash: 2f325c80877068c53d690bd7ff74f768dab3a174
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80371584"
---
# <a name="device-roobo-smart-audio-dev-kit-v2"></a>Dispositivo: Roobo Smart Audio Dev Kit v2

Este artigo fornece informações específicas do dispositivo para o Roobo Smart Audio Dev Kit2.

## <a name="set-up-the-development-kit"></a>Configurar o kit de desenvolvimento

1. O kit de desenvolvimento tem dois conectores micro USB. O conector esquerdo é para alimentar o kit de desenvolvimento e é destacado como Power na imagem abaixo. O certo é controlá-lo, e está marcado Debug na imagem. 
    ![Ligando o kit de v](media/speech-devices-sdk/roobo-v2-connections.png)
1. Ligue o kit de desenvolvimento utilizando um cabo micro USB para ligar a porta de alimentação a um PC ou adaptador de energia. Um indicador de energia verde acender-se-á por baixo da placa superior.
1. Para controlar o kit de desenvolvimento, ligue a porta de depuração a um computador utilizando um segundo cabo MICRO USB. É essencial utilizar um cabo de alta qualidade para garantir comunicações fiáveis.
1. Oriente o seu kit de desenvolvimento Circularmente - Vertical, com microfones virados para o teto como mostrado acima


## <a name="development-information"></a>Informação sobre desenvolvimento

Para mais informações sobre o desenvolvimento, consulte o guia de [desenvolvimento do Roobo.](http://dwn.roo.bo/server_upload/ddk/ROOBO%20Dev%20Kit-User%20Guide.pdf)

## <a name="audio-recordplay"></a>Gravação de áudio/reprodução

As operações áudio DDK2 podem ser realizadas das seguintes formas:
* Utilize bibliotecas alsa de código aberto e suas aplicações.
* Utilize interface appmainprog para fazer o desenvolvimento da aplicação. DDK2 áudio - estrutura de software relacionada usa a estrutura padrão da ALSA, pode usar libasound. Então, para desenvolver software diretamente. Para que possa usar o registo e a reprodução da ALSA diretamente para gravar e reproduzir áudio.
