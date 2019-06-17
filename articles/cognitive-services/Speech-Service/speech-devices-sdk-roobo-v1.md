---
title: Voz dispositivos SDK Roobo Smart áudio Dev Kit v1 - serviços de voz
titleSuffix: Azure Cognitive Services
description: Pré-requisitos e instruções para começar a trabalhar com o SDK de dispositivos de voz, Kit de programação de áudio inteligente Roobo v1.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: erhopf
ms.openlocfilehash: 7c1a13a44d9db8ed029ce798f0bb34944a1a65a7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65409060"
---
# <a name="device-roobo-smart-audio-dev-kit"></a>Dispositivo: Kit de programação de áudio de Roobo inteligente

Este artigo fornece informações específicas do dispositivo para o Kit de programação de áudio inteligente da Roobo.

## <a name="set-up-the-development-kit"></a>Configurar o kit de desenvolvimento

1. O kit de desenvolvimento tem dois conectores USB micro. O conector do esquerda é criar o kit de desenvolvimento e está realçado como poder na imagem abaixo. O direito de um é para controlá-lo e está marcada como depurar na imagem.

    ![ligar o kit de desenvolvimento](media/speech-devices-sdk/qsg-1.png)

1. O kit de desenvolvimento de energia usando um cabo USB micro para ligar a porta de energia para um PC ou ligar o adaptador. Um indicador de power verde serão apresentados em quadro superior.

1. Para controlar o kit de desenvolvimento, ligue a porta de depuração para um computador com um segundo micro cabo USB. É essencial para utilizar um cabo de alta qualidade para assegurar a comunicação confiáveis.

1. Orientar o seu kit de desenvolvimento por qualquer configuração do circular ou linear.

    |Configuração do kit de desenvolvimento|Orientação|
    |-----------------------------|------------|
    |Circular|Upright, com microfones voltada para o limite|
    |Linear|No seu lado, com microfones voltada para (mostrado na imagem seguinte)|

    ![orientação do kit de programação linear](media/speech-devices-sdk/qsg-2.png)

1. Instalar os certificados e definir as permissões do dispositivo de som. Escreva os seguintes comandos numa janela de linha de comando:

   ```powershell
   adb push C:\SDSDK\Android-Sample-Release\scripts\roobo_setup.sh /data/
   adb shell
   cd /data/
   chmod 777 roobo_setup.sh
   ./roobo_setup.sh
   exit
   ```

    > [!NOTE]
    > Estes comandos utilizem a ponte de depuração do Android, `adb.exe`, que faz parte da instalação do Android Studio. Esta ferramenta está localizada no C:\Users\[nome de utilizador] \AppData\Local\Android\Sdk\platform ferramentas. Pode adicionar esse diretório ao seu caminho para que seja mais conveniente para invocar `adb`. Caso contrário, tem de especificar o caminho completo para a instalação do adb.exe em todos os comandos que invoquem `adb`.
    >
    > Se vir um erro `no devices/emulators found` verificar, em seguida, o cabo USB está ligado e é um cabo de alta qualidade. Pode usar `adb devices` para verificar que o computador pode se comunicar com o kit de desenvolvimento como irá devolver uma lista de dispositivos.
    >
    > [!TIP]
    > Mudo microfone e palestrante Certifique-se de que está a trabalhar com microfones o kit de desenvolvimento do seu PC. Dessa forma, não acidentalmente aciona o dispositivo com o áudio do PC.

1. Se quiser anexar um palestrante para o kit de desenvolvimento, pode ligá-la na linha de áudio horizontalmente. Deve escolher um palestrante de boa qualidade com um plug analógico de 3,5 mm.

    ![Vysor áudio](media/speech-devices-sdk/qsg-14.png)

## <a name="development-information"></a>Informações de desenvolvimento

Para obter mais informações de desenvolvimento, consulte a [guia de desenvolvimento Roobo](http://dwn.roo.bo/server_upload/ddk/ROOBO%20Dev%20Kit-User%20Guide.pdf).

## <a name="audio"></a>Áudio

Roobo fornece uma ferramenta que captura todos os áudio a memória flash. Ele pode ajudar a resolver problemas de áudio. Uma versão da ferramenta é fornecida para cada configuração de kit de desenvolvimento. Sobre o [Roobo site](https://ddk.roobo.com/), selecione o seu dispositivo e, em seguida, selecione o **Roobo ferramentas** link na parte inferior da página.

## <a name="next-steps"></a>Passos Seguintes

* [Executar a aplicação de exemplo do Android](speech-devices-sdk-android-quickstart.md)
