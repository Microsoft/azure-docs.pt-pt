---
title: 'Azure AD Connect: Autenticação pass-through - limitações atuais | Documentos da Microsoft'
description: Este artigo descreve as limitações atuais da autenticação de pass-through do Azure Active Directory (Azure AD)
services: active-directory
keywords: Autenticação do Azure AD Connect pass-through, Active Directory, de instalação necessários componentes para o Azure AD, SSO, Single Sign-on
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/04/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 97dc67d46b08bf5765c59806b45edd82f38720cd
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58011966"
---
# <a name="azure-active-directory-pass-through-authentication-current-limitations"></a>Autenticação pass-through do Azure Active Directory: Limitações atuais

>[!IMPORTANT]
>Autenticação de pass-through do Azure Active Directory (Azure AD) é uma funcionalidade gratuita e não é necessário qualquer nas edições pagas do Azure AD para utilizá-lo. Autenticação pass-through só está disponível na instância a nível mundial do Azure AD e não no [cloud do Microsoft Azure Alemanha](https://www.microsoft.de/cloud-deutschland) ou o [cloud do Microsoft Azure Government](https://azure.microsoft.com/features/gov/).

## <a name="supported-scenarios"></a>Cenários suportados

São suportados os seguintes cenários:

- Utilizador inícios de sessão para aplicativos web baseados em navegador.
- Utilizador inícios de sessão para os clientes Outlook usando protocolos legados, tais como o Exchange ActiveSync, EAS, SMTP, POP e IMAP.
- Inícios de usuário para aplicativos herdados de cliente do Office e aplicativos do Office que suportam [autenticação moderna](https://www.microsoft.com/en-us/microsoft-365/blog/2015/11/19/updated-office-365-modern-authentication-public-preview): Office 2013 e versões de 2016.
- Utilizador inícios de sessão para aplicações de protocolo legados, tais como o PowerShell versão 1.0 e outros.
- Associa do Azure AD para dispositivos Windows 10.
- Palavras-passe de aplicação para o multi-factor Authentication.

## <a name="unsupported-scenarios"></a>Cenários não suportados

Os cenários seguintes são _não_ suportados:

- Deteção de utilizadores com [fuga de credenciais](../reports-monitoring/concept-risk-events.md#leaked-credentials).
- O Azure AD Domain Services precisa de sincronização de Hash de palavra-passe para ser ativada no inquilino. Portanto, os inquilinos que utilizam a autenticação pass-through _apenas_ não funcionam para cenários que precisam de serviços de domínio do Azure AD.
- Autenticação pass-through não é integrada com [do Azure AD Connect Health](whatis-hybrid-identity-health.md).

> [!IMPORTANT]
> Como solução para cenários não suportados _apenas_ (exceto a integração do Azure AD Connect Health), ativar a sincronização de Hash de palavra-passe no [funcionalidades opcionais](how-to-connect-install-custom.md#optional-features) página no Assistente do Azure AD Connect.
> 
> [!NOTE]
> Ativar a sincronização de Hash de palavra-passe dá-lhe a opção de autenticação de ativação pós-falha se a sua infraestrutura no local é interrompida. Esta ativação pós-falha da autenticação pass-through para a sincronização de Hash de palavra-passe não é automática. Terá de mudar o método de início de sessão manualmente utilizando o Azure AD Connect. Se o servidor com o Azure AD Connect ficar inativo, é necessária a ajuda de Support da Microsoft para desativar a autenticação pass-through.

## <a name="next-steps"></a>Passos Seguintes
- [Guia de introdução](how-to-connect-pta-quick-start.md): Começar a trabalhar com a autenticação pass-through do Azure AD.
- [Migrar do AD FS para autenticação pass-through](https://aka.ms/ADFSTOPTADPDownload) -um guia detalhado para migrar do AD FS (ou outras tecnologias de Federação) para autenticação pass-through.
- [Bloqueio do smart](../authentication/howto-password-smart-lockout.md): Saiba como configurar a capacidade de bloqueio inteligente no seu inquilino para proteger contas de utilizador.
- [Análise técnica aprofundada](how-to-connect-pta-how-it-works.md): Compreenda como funciona o recurso de autenticação pass-through.
- [Perguntas mais frequentes sobre](how-to-connect-pta-faq.md): Encontre respostas para perguntas mais frequentes sobre a funcionalidade de autenticação pass-through.
- [Resolver problemas de](tshoot-connect-pass-through-authentication.md): Saiba como resolver problemas comuns com a funcionalidade de autenticação pass-through.
- [Detalhada da segurança](how-to-connect-pta-security-deep-dive.md): Obtenha informações técnicas detalhadas sobre a funcionalidade de autenticação pass-through.
- [SSO totalmente integrado do Azure AD](how-to-connect-sso.md): Saiba mais sobre esta funcionalidade complementar.
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): Utilize o Fórum do Azure Active Directory para pedidos de novas funcionalidades de ficheiros.
