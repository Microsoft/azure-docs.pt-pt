---
title: Autenticação virtual do Windows Desktop - Azure
description: Métodos de autenticação para o Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 02/26/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 32e113bf02a2cbd21b70805396c212a1f9467382
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/06/2021
ms.locfileid: "106448241"
---
# <a name="supported-authentication-methods"></a>Métodos de autenticação suportados

Neste artigo, vamos dar-lhe uma breve visão geral dos tipos de autenticação que pode utilizar no Windows Virtual Desktop.

## <a name="session-host-authentication"></a>Autenticação do anfitrião da sessão

O Windows Virtual Desktop suporta tanto o NT LAN Manager (NTLM) como o Kerberos para a autenticação do anfitrião da sessão. No entanto, para utilizar kerberos, o cliente precisa de obter bilhetes de segurança Kerberos de um serviço do Key Distribution Center (KDC) em execução num controlador de domínio. Para conseguir bilhetes, o cliente precisa de uma linha de visão direta para o controlador de domínio. Pode obter uma linha de visão direta usando a sua rede corporativa. Também pode utilizar uma ligação VPN à sua rede corporativa ou configurar um [servidor KDC Proxy](key-distribution-center-proxy.md).

Estes são os métodos de inscrição atualmente suportados:

- Cliente do Ambiente de Trabalho do Windows
    - Nome de utilizador e palavra-passe
    - Smartcard
    - Windows Hello for Business (apenas confiança de certificado)
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
>Smartcard e Windows Hello para Negócios só podem usar Kerberos para iniciar sinse. A sessão com kerberos requer uma linha de visão para o controlador de domínio ou um [servidor KDC Proxy](key-distribution-center-proxy.md).

## <a name="hybrid-identity"></a>Identidade híbrida

O Windows Virtual Desktop suporta [identidades híbridas](../active-directory/hybrid/whatis-hybrid-identity.md) através do Azure Ative Directory (AD), incluindo as federadas utilizando os Serviços da Federação de Diretório Ativo (ADFS). Uma vez que os utilizadores devem ser detetáveis através do Azure AD, o Windows Virtual Desktop não suporta implementações autónomas do Ative Directory com ADFS.

## <a name="single-sign-on-sso"></a>Início de sessão único (SSO)

O Windows Virtual Desktop não suporta atualmente serviços da Federação de Diretórios Ativos (ADFS) para SSO.

A única maneira de evitar ser solicitado para as suas credenciais para o anfitrião da sessão é salvá-las no cliente. Recomendamos que o faça apenas com dispositivos seguros para evitar que outros utilizadores acedam aos seus recursos.

## <a name="next-steps"></a>Passos seguintes

Curioso sobre outras formas de manter o seu destacamento seguro? Confira [as melhores práticas de segurança.](security-guide.md)
