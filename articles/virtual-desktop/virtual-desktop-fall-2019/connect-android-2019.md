---
title: Conecte-se ao Windows Virtual Desktop (clássico) do Android - Azure
description: Como ligar-se ao Windows Virtual Desktop (clássico) utilizando o cliente Android.
author: Heidilohr
ms.topic: how-to
ms.date: 03/30/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 7de94bdd28b51b87b3b06d62685dcc1921a1c6f3
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/06/2021
ms.locfileid: "106445300"
---
# <a name="connect-to-windows-virtual-desktop-classic-with-the-android-client"></a>Conecte-se ao Windows Virtual Desktop (clássico) com o cliente Android

> Aplica-se a: Android 4.1 e posteriormente, Chromebooks com ChromeOS 53 e mais tarde.

>[!IMPORTANT]
>Este conteúdo aplica-se ao Windows Virtual Desktop (clássico), que não suporta objetos de desktop virtual do Gestor de Recursos Azure Windows. Se estiver a tentar gerir os objetos de ambiente de trabalho virtuais do Azure Resource Manager Windows, consulte [este artigo](../connect-android.md).

Pode aceder aos recursos do Windows Virtual Desktop a partir do seu dispositivo Android com o nosso cliente descarregador. Também pode utilizar o cliente Android em dispositivos Chromebook que suportam a Google Play Store. Este guia irá dizer-lhe como configurar o cliente Android.

## <a name="install-the-android-client"></a>Instale o cliente Android

Para começar, [faça o download](https://play.google.com/store/apps/details?id=com.microsoft.rdc.androidx) e instale o cliente no seu dispositivo Android.

## <a name="subscribe-to-a-feed"></a>Subscreva um feed

Subscreva o feed fornecido pelo seu administrador para obter a lista de recursos geridos a que pode aceder no seu dispositivo Android.

Para subscrever um feed:

1. No Centro de Ligação, toque **+** e toque no Feed de Recursos **Remotos**.
2. Introduza o URL de alimentação no campo **URL de alimentação.** O URL de alimentação pode ser um URL ou um endereço de e-mail.
   - Se utilizar um URL, use o que o seu administrador lhe deu, <https://rdweb.wvd.microsoft.com> normalmente.
   - Para utilizar o e-mail, insira o seu endereço de e-mail. O cliente procurará um URL associado ao seu endereço de e-mail se o seu administrador configurar o servidor dessa forma.
3. Toque **EM SEGUINTE**.
4. Forneça as suas credenciais quando solicitado.
   - Para **o nome de utilizador,** forneça ao utilizador a permissão para aceder aos recursos.
   - Para **senha**, forneça a palavra-passe associada ao nome de utilizador.
   - Também pode ser solicitado que forneça fatores adicionais se a sua administração configurar a autenticação dessa forma.

Após a subscrição, o Centro de Ligação deve exibir os recursos remotos.

Uma vez subscrito um feed, o conteúdo do feed será atualizado automaticamente regularmente. Os recursos podem ser adicionados, alterados ou removidos com base em alterações efetuadas pelo seu administrador.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre como usar o cliente Android, confira [Consulte o cliente Android.](/windows-server/remote/remote-desktop-services/clients/remote-desktop-android/)