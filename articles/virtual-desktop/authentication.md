---
title: Autenticação virtual do Windows Desktop - Azure
description: Métodos de autenticação para o Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/27/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 8328fa9c9e32a22e8e5093aa343f333cd1914ddc
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/27/2020
ms.locfileid: "89038668"
---
# <a name="supported-authentication-methods"></a>Métodos de autenticação suportados

Neste artigo, vamos dar-lhe uma breve visão geral dos tipos de autenticação que pode utilizar no Windows Virtual Desktop.

## <a name="session-host-authentication"></a>Autenticação do anfitrião da sessão

O Windows Virtual Desktop suporta tanto o NT LAN Manager (NTLM) como o Kerberos para a autenticação do anfitrião da sessão. No entanto, para utilizar kerberos, o cliente precisa de obter bilhetes de segurança Kerberos de um serviço do Key Distribution Center (KDC) em execução num controlador de domínio. Para conseguir bilhetes, o cliente precisa de uma linha de visão direta para o controlador de domínio. Pode obter uma linha de visão direta usando a sua rede corporativa. Também pode utilizar uma ligação VPN à sua rede corporativa.

Estes são os métodos de inscrição atualmente suportados:

- Cliente do Ambiente de Trabalho do Windows
    - Nome de utilizador e palavra-passe
    - Smartcard
    - Windows Hello
- Cliente da Windows Store
    - Nome de utilizador e palavra-passe
- Cliente Web
    - Nome de utilizador e palavra-passe
- Android
    - Nome de utilizador e palavra-passe
- iOS
    - Nome de utilizador e palavra-passe
- macOS
    - Nome de utilizador e palavra-passe

>[!NOTE]
>Smartcard e Windows Hello só podem usar Kerberos para iniciar sinsus. A inscrição com kerberos requer uma linha de visão para o controlador de domínio.

## <a name="single-sign-on-sso"></a>Único sinal (SSO)

O Windows Virtual Desktop não suporta atualmente serviços da Federação de Diretórios Ativos (ADFS) para autenticação ou SSO.

A única maneira de evitar ser solicitado para as suas credenciais para o anfitrião da sessão é salvá-las no cliente. Recomendamos que o faça apenas com dispositivos seguros para evitar que outros utilizadores acedam aos seus recursos.

## <a name="next-steps"></a>Passos seguintes

Curioso sobre outras formas de manter o seu destacamento seguro? Confira [as melhores práticas de segurança.](security-guide.md)
