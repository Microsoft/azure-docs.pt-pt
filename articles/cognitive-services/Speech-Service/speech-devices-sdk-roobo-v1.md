---
title: Dispositivos de fala SDK Roobo Smart Audio Dev Kit v1 - Serviço de fala
titleSuffix: Azure Cognitive Services
description: Pré-requisitos e instruções para começar com os Dispositivos de Fala SDK, Roobo Smart Audio Dev Kit v1.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 73eb1225ffc5fd01f9a27ca99ad2b059d45a36cf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "95015295"
---
# <a name="device-roobo-smart-audio-dev-kit"></a>Dispositivo: Roobo Smart Audio Dev Kit

Este artigo fornece informações específicas do dispositivo para o Kit Dev De áudio Inteligente Roobo.

## <a name="set-up-the-development-kit"></a>Configurar o kit de desenvolvimento

1. O kit de desenvolvimento tem dois conectores MICRO USB. O conector esquerdo é para alimentar o kit de desenvolvimento e é destacado como Power na imagem abaixo. O certo é controlá-lo, e está marcado Debug na imagem.

    ![Ligação do kit dev](media/speech-devices-sdk/qsg-1.png)

1. Ligue o kit de desenvolvimento utilizando um cabo USB micro para ligar a porta de alimentação a um PC ou adaptador de energia. Um indicador de energia verde acende-se por baixo da placa superior.

1. Para controlar o kit de desenvolvimento, ligue a porta de depurar a um computador utilizando um segundo cabo USB micro. É essencial utilizar um cabo de alta qualidade para garantir comunicações fiáveis.

1. Oriente o seu kit de desenvolvimento para a configuração circular ou linear.

    |Configuração do kit de desenvolvimento|Orientação|
    |-----------------------------|------------|
    |Circular|Vertical, com microfones virados para o teto|
    |Linear|De lado, com microfones virados para si (mostrados na seguinte imagem)|

    ![Orientação linear do kit dev](media/speech-devices-sdk/qsg-2.png)

1. Instale os certificados e desemote as permissões do dispositivo de som. Digite os seguintes comandos numa janela de pedido de comando de comando:

   ```powershell
   adb push C:\SDSDK\Android-Sample-Release\scripts\roobo_setup.sh /data/
   adb shell
   cd /data/
   chmod 777 roobo_setup.sh
   ./roobo_setup.sh
   exit
   ```

    > [!NOTE]
    > Estes comandos utilizam a Ponte Android Debug, `adb.exe` que faz parte da instalação do Android Studio. Esta ferramenta está localizada em C:\User \[ name]\AppData\Local\Android\Sdk\platform-tools. Pode adicionar este diretório ao seu caminho para que seja mais conveniente `adb` invocar. Caso contrário, deve especificar o caminho completo para a instalação de adb.exe em todos os comandos que `adb` invocarem .
    >
    > Se vir um `no devices/emulators found` erro, verifique se o cabo USB está ligado e é um cabo de alta qualidade. Pode utilizar `adb devices` para verificar se o seu computador pode falar com o kit de desenvolvimento, uma vez que irá devolver uma lista de dispositivos.
    >
    > [!TIP]
    > Mude o microfone e o altifalante do seu PC para se certificar de que está a trabalhar com os microfones do kit de desenvolvimento. Desta forma, não ativará acidentalmente o dispositivo com áudio do PC.

1. Se quiser ligar um altifalante ao kit dev, pode ligá-lo à linha de áudio. Deve escolher um altifalante de boa qualidade com uma ficha analógica de 3,5 mm.

    ![Áudio Vysor](media/speech-devices-sdk/qsg-14.png)

## <a name="development-information"></a>Informação sobre desenvolvimento

Para obter mais informações sobre o desenvolvimento, consulte o [guia de desenvolvimento da Roobo.](http://dwn.roo.bo/server_upload/ddk/ROOBO%20Dev%20Kit-User%20Guide.pdf)

## <a name="audio"></a>Áudio

Roobo fornece uma ferramenta que captura todo o áudio para memória flash. Pode ajudá-lo a resolver problemas de áudio. Uma versão da ferramenta é fornecida para cada configuração do kit de desenvolvimento. No  [site roobo,](http://ddk.roobo.com/)selecione o seu dispositivo e, em seguida, selecione o link **Roobo Tools** na parte inferior da página.

## <a name="next-steps"></a>Passos seguintes

* [Executar o aplicativo de amostra Android](./speech-devices-sdk-quickstart.md?pivots=platform-android%253fpivots%253dplatform-android)