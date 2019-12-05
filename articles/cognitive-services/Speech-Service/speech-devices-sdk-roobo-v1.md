---
title: Speech Devices SDK Roobo Smart Audio dev kit v1 – Speech Service
titleSuffix: Azure Cognitive Services
description: Pré-requisitos e instruções para começar a usar o SDK de dispositivos de fala, kit de desenvolvimento de áudio inteligente Roobo v1.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 9add5b063b67ddcc4cd5bf93e7f5b570b004e5ca
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/04/2019
ms.locfileid: "74815591"
---
# <a name="device-roobo-smart-audio-dev-kit"></a>Dispositivo: kit de desenvolvimento de áudio inteligente Roobo

Este artigo fornece informações específicas do dispositivo para o kit de desenvolvimento de áudio inteligente do Roobo.

## <a name="set-up-the-development-kit"></a>Configurar o kit de desenvolvimento

1. O kit de desenvolvimento tem dois conectores USB. O conector esquerdo é para ligar o kit de desenvolvimento e é realçado como uma potência na imagem abaixo. A certa é controlá-la e está marcada como depurar na imagem.

    ![ligar o kit de desenvolvimento](media/speech-devices-sdk/qsg-1.png)

1. Ligue o kit de desenvolvimento usando um cabo micro USB para conectar a porta de alimentação a um PC ou adaptador de energia. Um indicador de energia verde se acenderá abaixo do quadro superior.

1. Para controlar o kit de desenvolvimento, conecte a porta de depuração a um computador usando um segundo cabo micro USB. É essencial usar um cabo de alta qualidade para garantir comunicações confiáveis.

1. Orientar o seu kit de desenvolvimento por qualquer configuração do circular ou linear.

    |Configuração do kit de desenvolvimento|Orientação|
    |-----------------------------|------------|
    |Circular|Upright, com microfones voltada para o limite|
    |Linear|No seu lado, com microfones voltada para (mostrado na imagem seguinte)|

    ![orientação do kit de programação linear](media/speech-devices-sdk/qsg-2.png)

1. Instale os certificados e defina as permissões do dispositivo de som. Escreva os seguintes comandos numa janela de linha de comando:

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
    > Se você vir um erro `no devices/emulators found`, verifique se o cabo USB está conectado e se é um cabo de alta qualidade. Você pode usar `adb devices` para verificar se o seu computador pode se comunicar com o kit de desenvolvimento, pois ele retornará uma lista de dispositivos.
    >
    > [!TIP]
    > Mudo microfone e palestrante Certifique-se de que está a trabalhar com microfones o kit de desenvolvimento do seu PC. Dessa forma, não acidentalmente aciona o dispositivo com o áudio do PC.

1. Se você quiser anexar um palestrante ao kit de desenvolvimento, poderá conectá-lo à linha de saída de áudio. Você deve escolher um palestrante de boa qualidade com um plugue analógico de 3,5 mm.

    ![Vysor áudio](media/speech-devices-sdk/qsg-14.png)

## <a name="development-information"></a>Informações de desenvolvimento

Para obter mais informações de desenvolvimento, consulte o [Guia de desenvolvimento do Roobo](http://dwn.roo.bo/server_upload/ddk/ROOBO%20Dev%20Kit-User%20Guide.pdf).

## <a name="audio"></a>Áudio

O Roobo fornece uma ferramenta que captura todos os áudios para memória flash. Ele pode ajudar a resolver problemas de áudio. Uma versão da ferramenta é fornecida para cada configuração de kit de desenvolvimento. No [site do Roobo](https://ddk.roobo.com/), selecione seu dispositivo e, em seguida, selecione o link **Ferramentas do Roobo** na parte inferior da página.

## <a name="next-steps"></a>Passos seguintes

* [Executar o aplicativo de exemplo do Android](speech-devices-sdk-android-quickstart.md)
