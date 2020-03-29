---
title: 'Azure AD Connect: Autenticação pass-through - Limitações atuais / Microsoft Docs'
description: Este artigo descreve as limitações atuais do Diretório Ativo Azure (Azure AD) A autenticação pass-through
services: active-directory
keywords: Autenticação de passagem de ligação Azure AD, instala o Diretório Ativo, componentes necessários para Azure AD, SSO, Single Sign-on
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "60347754"
---
# <a name="azure-active-directory-pass-through-authentication-current-limitations"></a>Autenticação pass-through do Diretório Ativo Azure: Limitações atuais

>[!IMPORTANT]
>A Autenticação Pass-through do Azure Ative Directory (Azure AD) é uma funcionalidade gratuita, e não é necessário nenhuma edições pagas do Azure AD para a utilizar. A Autenticação Pass-through só está disponível na instância mundial do Azure AD, e não na nuvem microsoft [Azure Germany](https://www.microsoft.de/cloud-deutschland) ou na [nuvem do Governo Microsoft Azure](https://azure.microsoft.com/features/gov/).

## <a name="supported-scenarios"></a>Cenários suportados

Os seguintes cenários são apoiados:

- Sessão de sessão de utilizadores para aplicações baseadas em navegador web.
- User ins ins to Outlook customers using legacy protocols tais como Exchange ActiveSync, EAS, SMTP, POP e IMAP.
- Inscrições de utilizadores para aplicações de clientes do Legacy Office e aplicações do Office que suportam [a autenticação moderna](https://www.microsoft.com/en-us/microsoft-365/blog/2015/11/19/updated-office-365-modern-authentication-public-preview): Versões Office 2013 e 2016.
- Inscrições de utilizadores em aplicações de protocolo sinuosos, tais como a versão 1.0 da PowerShell e outras.
- A Azure AD junta-se a dispositivos Windows 10.
- Palavras-passe de aplicativopara autenticação multi-factor.

## <a name="unsupported-scenarios"></a>Cenários não suportados

Os seguintes cenários _não_ são suportados:

- Deteção de utilizadores com [credenciais vazadas.](../reports-monitoring/concept-risk-events.md#leaked-credentials)
- Os Serviços de Domínio Azure AD precisam de sincronização de hash de palavra-passe para serem ativados no inquilino. Por isso, os inquilinos que usam a Autenticação Pass-through _apenas_ não trabalham para cenários que necessitem de Serviços de Domínio Azure AD.
- A Autenticação Pass-through não está integrada com a [Azure AD Connect Health](whatis-hybrid-identity-health.md).

> [!IMPORTANT]
> Como uma suposição para _cenários_ não suportados (exceto a integração azure AD Connect Health), ative a sincronização de hash de password na página de [funcionalidades Opcionais](how-to-connect-install-custom.md#optional-features) no assistente Azure AD Connect.
> 
> [!NOTE]
> Ativar a sincronização de hash de palavra-passe dá-lhe a opção de falhar a autenticação se a sua infraestrutura no local for interrompida. Esta falha da autenticação pass-through para a sincronização de hash password não é automática. Terá de mudar manualmente o método de entrada utilizando o Azure AD Connect. Se o servidor que executa o Azure AD Connect se apagar, necessitará da ajuda do Suporte da Microsoft para desativar a autenticação pass-through.

## <a name="next-steps"></a>Passos seguintes
- [Início rápido](how-to-connect-pta-quick-start.md): Levantar-se e correr com autenticação de passe Azure AD.
- [Migrar de AD FS para a Autenticação Pass-through](https://aka.ms/ADFSTOPTADPDownload) - Um guia detalhado para migrar de AD FS (ou outras tecnologias da federação) para a Autenticação Pass-through.
- [Smart Lockout](../authentication/howto-password-smart-lockout.md): Aprenda a configurar a capacidade de bloqueio inteligente no seu inquilino para proteger as contas do utilizador.
- [Mergulho profundo técnico](how-to-connect-pta-how-it-works.md): Compreenda como funciona a função de Autenticação Pass-through.
- [Perguntas frequentes](how-to-connect-pta-faq.md): Encontre respostas para perguntas frequentes sobre a funcionalidade de Autenticação Pass-through.
- [Resolução de problemas](tshoot-connect-pass-through-authentication.md): Aprenda a resolver problemas comuns com a função de Autenticação Pass-through.
- [Mergulho profundo de segurança](how-to-connect-pta-security-deep-dive.md): Obtenha informações técnicas profundas sobre a função de autenticação pass-through.
- [Azure AD Seamless SSO](how-to-connect-sso.md): Saiba mais sobre esta funcionalidade complementar.
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): Utilize o Fórum de Diretório Ativo Azure para apresentar novos pedidos de funcionalidades.
