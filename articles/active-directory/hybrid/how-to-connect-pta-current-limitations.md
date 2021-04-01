---
title: 'Azure AD Connect: Authentication Pass-through - Limitações atuais | Microsoft Docs'
description: Este artigo descreve as limitações atuais do Azure Ative Directory (Azure AD) Authentication Pass-through
services: active-directory
keywords: Autenticação pass-through Azure AD Connect, instalar Diretório Ativo, componentes necessários para Azure AD, SSO, Single Sign-on
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 09/04/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3e9c4489f59f72e4d0b5c7a0b911da188eb0828c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "89280201"
---
# <a name="azure-active-directory-pass-through-authentication-current-limitations"></a>Autenticação pass-through do Diretório Ativo Azure: Limitações atuais

>[!IMPORTANT]
>A Azure Ative Directory (Azure AD) A Authentication Pass-through é uma funcionalidade gratuita e não precisa de edições pagas do Azure AD para usá-lo. A autenticação pass-through só está disponível no caso mundial de Azure AD, e não na nuvem do [Microsoft Azure Germany](https://www.microsoft.de/cloud-deutschland) ou na nuvem do [Governo Microsoft Azure](https://azure.microsoft.com/features/gov/).

## <a name="supported-scenarios"></a>Cenários suportados

São apoiados os seguintes cenários:

- Insins de acesso ao utilizador para aplicações baseadas no navegador web.
- Logins de utilizador para clientes do Outlook utilizando protocolos legados como Exchange ActiveSync, EAS, SMTP, POP e IMAP.
- Logins de utilizador para aplicações de clientes do Office e aplicações de Escritório que suportam [a autenticação moderna](https://www.microsoft.com/en-us/microsoft-365/blog/2015/11/19/updated-office-365-modern-authentication-public-preview): Versões Office 2013 e 2016.
- As entradas do utilizador para aplicações de protocolos legados, tais como a versão 1.0 do PowerShell e outras.
- O Azure AD junta-se a dispositivos Windows 10.
- Palavras-passe de aplicativo para autenticação multi-factor.

## <a name="unsupported-scenarios"></a>Cenários não suportados

Os seguintes cenários _não_ são suportados:

- Deteção de utilizadores com [credenciais vazadas.](../identity-protection/overview-identity-protection.md)
- Os Serviços de Domínio Azure AD precisam da sincronização de password Hash para ser ativado no inquilino. Portanto, os inquilinos que usam a Autenticação Pass-through _apenas_ não funcionam para cenários que precisam de Serviços de Domínio AD AZure.
- A autenticação pass-through não está integrada com [a Azure AD Connect Health](./whatis-azure-ad-connect.md).

> [!IMPORTANT]
> Como uma solução alternativa _apenas_ para cenários não suportados (exceto a integração Azure AD Connect Health), permitir a sincronização de password hash na página [de funcionalidades opcionais](how-to-connect-install-custom.md#optional-features) no assistente Azure AD Connect.
> 
> [!NOTE]
> Ativar a sincronização de Hash da Password dá-lhe a opção de autenticação por failover se a sua infraestrutura no local estiver interrompida. Esta falha de autenticação pass-through para sincronização de hash password não é automática. Terá de mudar manualmente o método de inscrição através do Azure AD Connect. Se o servidor que executa o Azure AD Connect se desligar, necessitará da ajuda do Microsoft Support para desligar a Autenticação Pass-through.

## <a name="next-steps"></a>Passos seguintes
- [Início rápido](how-to-connect-pta-quick-start.md): Levante-se e corra com autenticação pass-through Azure.
- [Migrar de AD FS para Autenticação Pass-through](https://aka.ms/ADFSTOPTADPDownload) - Um guia detalhado para migrar de FS AD (ou outras tecnologias da federação) para a Autenticação Pass-through.
- [Smart Lockout](../authentication/howto-password-smart-lockout.md): Saiba como configurar a capacidade de Bloqueio Inteligente no seu inquilino para proteger as contas do utilizador.
- [Mergulho técnico profundo](how-to-connect-pta-how-it-works.md): Compreenda como funciona a função de autenticação pass-through.
- [Perguntas frequentes](how-to-connect-pta-faq.md): Encontre respostas a perguntas frequentes sobre a funcionalidade de Autenticação Pass-through.
- [Resolução de problemas](tshoot-connect-pass-through-authentication.md): Aprenda a resolver problemas comuns com a função De autenticação Pass-through.
- [Mergulho profundo de](how-to-connect-pta-security-deep-dive.md)segurança : Obtenha informações técnicas profundas sobre a função de autenticação pass-through.
- [Azure AD Seamless SSO](how-to-connect-sso.md): Saiba mais sobre esta característica complementar.
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): Utilize o Azure Ative Directory Forum para apresentar novos pedidos de funcionalidades.
