---
title: Dispositivos de Fala SDK Roobo Smart Audio Dev Kit v1 - Serviço de fala
titleSuffix: Azure Cognitive Services
description: Pré-requisitos e instruções para começar com o SDK, Roobo Smart Audio Dev Kit v1.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 9add5b063b67ddcc4cd5bf93e7f5b570b004e5ca
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "74815591"
---
# <a name="device-roobo-smart-audio-dev-kit"></a>Dispositivo: Kit Dev Dev Dev Dev Dev Da Roobo Smart

Este artigo fornece informações específicas do dispositivo para o Kit Dev Dev Dev Da Roobo Smart Audio.

## <a name="set-up-the-development-kit"></a>Configurar o kit de desenvolvimento

1. O kit de desenvolvimento tem dois conectores micro USB. O conector esquerdo é para alimentar o kit de desenvolvimento e é destacado como Power na imagem abaixo. O certo é controlá-lo, e está marcado Debug na imagem.

    ![Ligando o kit de v](media/speech-devices-sdk/qsg-1.png)

1. Ligue o kit de desenvolvimento utilizando um cabo micro USB para ligar a porta de alimentação a um PC ou adaptador de energia. Um indicador de energia verde acender-se-á por baixo da placa superior.

1. Para controlar o kit de desenvolvimento, ligue a porta de depuração a um computador utilizando um segundo cabo MICRO USB. É essencial utilizar um cabo de alta qualidade para garantir comunicações fiáveis.

1. Oriente o seu kit de desenvolvimento para a configuração circular ou linear.

    |Configuração do kit de desenvolvimento|Orientação|
    |-----------------------------|------------|
    |Circular|Vertical, com microfones virados para o teto|
    |Linear|De lado, com microfones virados para si (mostradona imagem seguinte)|

    ![Orientação linear do kit de dev](media/speech-devices-sdk/qsg-2.png)

1. Instale os certificados e detete as permissões do dispositivo de som. Digite os seguintes comandos numa janela De Comando:

   ```powershell
   adb push C:\SDSDK\Android-Sample-Release\scripts\roobo_setup.sh /data/
   adb shell
   cd /data/
   chmod 777 roobo_setup.sh
   ./roobo_setup.sh
   exit
   ```

    > [!NOTE]
    > Estes comandos utilizam a Ponte `adb.exe`Android Debug, que faz parte da instalação do Android Studio. Esta ferramenta está localizada no\[nome do utilizador C:\Utilizadores]\AppData\Local\Android\Sdk\platform-tools. Pode adicionar este diretório ao seu caminho para `adb`que seja mais conveniente invocar. Caso contrário, deve especificar o caminho completo para a sua instalação `adb`de adb.exe em todos os comandos que invoquem .
    >
    > Se vir um `no devices/emulators found` erro, verifique se o cabo USB está ligado e é um cabo de alta qualidade. Pode utilizar `adb devices` para verificar se o seu computador pode falar com o kit de desenvolvimento, uma vez que devolverá uma lista de dispositivos.
    >
    > [!TIP]
    > Silenciar o microfone e o altifalante do seu PC para ter a certeza de que está a trabalhar com os microfones do kit de desenvolvimento. Desta forma, não acionar acidentalmente o dispositivo com áudio do PC.

1. Se quiser ligar um altifalante ao dev kit, pode ligá-lo à linha de áudio. Deve escolher um altifalante de boa qualidade com uma ficha analógica de 3,5 mm.

    ![Áudio vysor](media/speech-devices-sdk/qsg-14.png)

## <a name="development-information"></a>Informação sobre desenvolvimento

Para mais informações sobre o desenvolvimento, consulte o guia de [desenvolvimento do Roobo.](http://dwn.roo.bo/server_upload/ddk/ROOBO%20Dev%20Kit-User%20Guide.pdf)

## <a name="audio"></a>Áudio

Roobo fornece uma ferramenta que captura todo o áudio para piscar a memória. Pode ajudá-lo a resolver problemas de áudio. Uma versão da ferramenta é fornecida para cada configuração do kit de desenvolvimento. No site da [Roobo,](https://ddk.roobo.com/)selecione o seu dispositivo e, em seguida, selecione o link **Roobo Tools** na parte inferior da página.

## <a name="next-steps"></a>Passos seguintes

* [Executar a aplicação de amostras Android](speech-devices-sdk-android-quickstart.md)
