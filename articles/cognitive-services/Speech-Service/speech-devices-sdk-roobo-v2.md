---
title: Dispositivos de fala SDK Roobo Smart Audio Dev Kit v2 - Serviço de Fala
titleSuffix: Azure Cognitive Services
description: Pré-requisitos e instruções para começar com os Dispositivos de Fala SDK, Roobo Smart Audio Dev Kit v2.
services: cognitive-services
author: anushapatnala
manager: wellsi
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: v-anusp
ms.openlocfilehash: 2f325c80877068c53d690bd7ff74f768dab3a174
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "80371584"
---
# <a name="device-roobo-smart-audio-dev-kit-v2"></a>Dispositivo: Roobo Smart Audio Dev Kit v2

Este artigo fornece informações específicas do dispositivo para o Roobo Smart Audio Dev Kit2.

## <a name="set-up-the-development-kit"></a>Configurar o kit de desenvolvimento

1. O kit de desenvolvimento tem dois conectores MICRO USB. O conector esquerdo é para alimentar o kit de desenvolvimento e é destacado como Power na imagem abaixo. O certo é controlá-lo, e está marcado Debug na imagem. 
    ![Ligação do kit dev](media/speech-devices-sdk/roobo-v2-connections.png)
1. Ligue o kit de desenvolvimento utilizando um cabo USB micro para ligar a porta de alimentação a um PC ou adaptador de energia. Um indicador de energia verde acende-se por baixo da placa superior.
1. Para controlar o kit de desenvolvimento, ligue a porta de depurar a um computador utilizando um segundo cabo USB micro. É essencial utilizar um cabo de alta qualidade para garantir comunicações fiáveis.
1. Oriente o seu kit de desenvolvimento Circularmente - Vertical, com microfones virados para o teto como mostrado acima


## <a name="development-information"></a>Informação sobre desenvolvimento

Para obter mais informações sobre o desenvolvimento, consulte o [guia de desenvolvimento da Roobo.](http://dwn.roo.bo/server_upload/ddk/ROOBO%20Dev%20Kit-User%20Guide.pdf)

## <a name="audio-recordplay"></a>Gravação/Reprodução áudio

As operações de áudio DDK2 podem ser realizadas das seguintes formas:
* Utilize bibliotecas de código aberto ALSA e suas aplicações.
* Utilize a interface appmainprog para fazer o desenvolvimento da aplicação. Áudio DDK2 - estrutura de software relacionado usa a estrutura padrão da ALSA, você pode usar libasound. Então, desenvolver software diretamente. Assim, pode utilizar o arecord da ALSA e reproduzir-se diretamente para gravar e reproduzir áudio.
