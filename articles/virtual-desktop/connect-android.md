---
title: Conecte-se ao Windows Virtual Desktop a partir do Android - Azure
description: Como ligar-se ao Windows Virtual Desktop utilizando o cliente Android.
author: Heidilohr
ms.topic: how-to
ms.date: 03/25/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 79d88d218d04d00def19bdd2aa98f76d2f128de1
ms.sourcegitcommit: 98854e3bd1ab04ce42816cae1892ed0caeedf461
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/07/2020
ms.locfileid: "88007765"
---
# <a name="connect-to-windows-virtual-desktop-with-the-android-client"></a>Conecte-se ao Windows Virtual Desktop com o cliente Android

> Aplica-se a: Android 4.1 e posteriormente, Chromebooks com ChromeOS 53 e mais tarde.

>[!IMPORTANT]
>Este conteúdo aplica-se ao Windows Virtual Desktop com objetos de ambiente de trabalho virtuais do Windows, gestor de recursos do Azure. Se estiver a utilizar o Windows Virtual Desktop (clássico) sem objetos Azure Resource Manager, consulte [este artigo](./virtual-desktop-fall-2019/connect-android-2019.md).

Pode aceder aos recursos do Windows Virtual Desktop a partir do seu dispositivo Android com o nosso cliente descarregador. Também pode utilizar o cliente Android em dispositivos Chromebook que suportam a Google Play Store. Este guia irá dizer-lhe como configurar o cliente Android.

## <a name="install-the-android-client"></a>Instale o cliente Android

Para começar, [faça o download](https://play.google.com/store/apps/details?id=com.microsoft.rdc.androidx) e instale o cliente no seu dispositivo Android.

## <a name="subscribe-to-a-feed"></a>Subscreva um feed

Subscreva o feed fornecido pelo seu administrador para obter a lista de recursos geridos a que pode aceder no seu dispositivo Android.

Para subscrever um feed:

1. No Centro de Ligação, toque **+** e toque no Feed de Recursos **Remotos**.
2. Introduza o URL de alimentação no campo **URL de alimentação.** O URL de alimentação pode ser um URL ou um endereço de e-mail.
   - Se utilizar um URL, use o que o seu administrador lhe deu, <https://rdweb.wvd.microsoft.com/api/arm/feeddiscovery> normalmente.
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
