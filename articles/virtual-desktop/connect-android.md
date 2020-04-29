---
title: Connect to Windows Virtual Desktop from Android - Azure
description: Como ligar ao Windows Virtual Desktop utilizando o cliente Android.
services: virtual-desktop
author: heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: d2990c82efbcdac7d453f920301787b8c83db1e9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80295365"
---
# <a name="connect-with-the-android-client"></a>Ligar ao cliente Android

> Aplica-se a: Android 4.1 e mais tarde, Chromebooks com ChromeOS 53 e mais tarde.

>[!NOTE]
> A capacidade de aceder aos recursos do Windows Virtual Desktop do cliente Android encontra-se atualmente disponível na pré-visualização.

Pode aceder aos recursos do Windows Virtual Desktop a partir do seu dispositivo Android com o nosso cliente descarregável. Também pode utilizar o cliente Android em dispositivos Chromebook que suportam a Google Play Store. Este guia dir-lhe-á como configurar o cliente Android.

## <a name="install-the-android-client"></a>Instale o cliente Android

Para começar, [faça o download](https://play.google.com/store/apps/details?id=com.microsoft.rdc.androidx) e instale o cliente no seu dispositivo Android.

## <a name="subscribe-to-a-feed"></a>Subscreva um feed

Subscreva o feed fornecido pelo seu administrador para obter a lista de recursos geridos a que pode aceder no seu dispositivo Android.

Para subscrever um feed:

1. No Centro de **+** Ligação, toque , toque em Alimentação de **Recursos Remotos**.
2. Introduza o URL de alimentação no campo **URL** feed. O URL de alimentação pode ser um URL ou um endereço de e-mail.
   - Se utilizar um URL, use o que o <https://rdweb.wvd.microsoft.com>seu administrador lhe deu, normalmente .
   - Para utilizar o seu email, insira o seu endereço de e-mail. O cliente procurará um URL associado ao seu endereço de e-mail se o seu administrador configurar o servidor dessa forma.
3. Toque **EM NEXT**.
4. Forneça as suas credenciais quando solicitado.
   - Para **o nome do utilizador,** dê ao utilizador o nome do utilizador com permissão para aceder aos recursos.
   - Para **palavra-passe,** forneça a palavra-passe associada ao nome do utilizador.
   - Também pode ser solicitado a fornecer fatores adicionais se a sua administração configurar a autenticação dessa forma.

Após a subscrição, o Centro de Ligação deve exibir os recursos remotos.

Uma vez subscrito um feed, o conteúdo do feed irá atualizar-se automaticamente regularmente. Os recursos podem ser adicionados, alterados ou removidos com base nas alterações feitas pelo seu administrador.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre como usar o cliente Android, confira [Get started com o cliente Android](/windows-server/remote/remote-desktop-services/clients/remote-desktop-android/).
