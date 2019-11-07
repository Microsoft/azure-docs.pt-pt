---
title: Conectar-se à área de trabalho virtual do Windows do iOS-Azure
description: Como se conectar à área de trabalho virtual do Windows usando o cliente iOS.
services: virtual-desktop
author: heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: helohr
ms.openlocfilehash: bfc7efa6e8ead3b53704e3c9bd189b18cb787618
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/05/2019
ms.locfileid: "73605839"
---
# <a name="connect-with-the-ios-client"></a>Ligar ao cliente de iOS

> Aplica-se a: iOS 8,0 ou posterior. Compatível com iPhone, iPad e iPod Touch.

>[!NOTE]
> O cliente iOS ainda está em visualização no momento.

Você pode acessar os recursos da área de trabalho virtual do Windows em seu dispositivo iOS com nosso cliente baixável. Este guia lhe dirá como configurar o cliente iOS.

## <a name="install-the-ios-beta-client"></a>Instalar o cliente iOS beta
Para instalar o cliente iOS beta:

1. Instale o aplicativo [Apple TestFlight](https://apps.apple.com/us/app/testflight/id899247664) em seu dispositivo IOS.
2. Em seu dispositivo iOS, abra um navegador e navegue até [aka.ms/rdiosbeta](https://aka.ms/rdiosbeta).
3. No rótulo **etapa 2: ingressar na versão beta**, selecione **Iniciar teste**.
4. Quando você for Redirecionado para o aplicativo TestFlight, selecione **aceitar**e, em seguida, selecione **instalar**.

## <a name="subscribe-to-a-feed"></a>Assinar um feed

Assine o feed fornecido pelo administrador para obter a lista de recursos gerenciados que você pode acessar em seu dispositivo iOS.

Para assinar um feed:

1. No centro de conexões, toque em **+** e, em seguida, toque em **adicionar espaço de trabalho**.
2. Insira a URL do feed no campo **URL do feed** . A URL do feed pode ser uma URL ou um endereço de email.
   - Se você usar uma URL, use aquela que o administrador lhe forneceu. Normalmente, a URL é <https://rdweb.wvd.microsoft.com>.
   - Para usar o email, insira seu endereço de email. Isso instrui o cliente a procurar uma URL associada ao seu endereço de email se o administrador configurou o servidor dessa maneira.
3. Toque **em Avançar**.
4. Forneça suas credenciais quando solicitado.
   - Para **nome de usuário**, dê ao nome de usuário permissão para acessar recursos.
   - Para **senha**, forneça a senha associada ao nome de usuário.
   - Você também poderá ser solicitado a fornecer fatores adicionais se o administrador configurou a autenticação dessa maneira.
5. Toque em **salvar**.

Depois disso, a central de conexões deve exibir os recursos remotos.

Após a assinatura de um feed, o conteúdo do feed será atualizado automaticamente regularmente. Os recursos podem ser adicionados, alterados ou removidos com base nas alterações feitas pelo administrador.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre como usar o cliente iOS beta, confira a documentação [introdução ao cliente Ios](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-ios) .
