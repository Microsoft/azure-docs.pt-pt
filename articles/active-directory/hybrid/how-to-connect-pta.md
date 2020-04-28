---
title: 'Azure AD Connect: Autenticação pass-through [ Microsoft Docs'
description: Este artigo descreve a Autenticação Pass-through do Azure Ative Directory (Azure AD) e como permite os sign-ins Da Azure AD validando as palavras-passe dos utilizadores contra o Diretório Ativo no local.
services: active-directory
keywords: o que é a Autenticação pass-through Azure AD, instalar Diretório Ativo, componentes necessários para Azure AD, SSO, Sign-on Único
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/21/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: d4b52a3025bfb15e2679709353cebf28254a75c2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77185508"
---
# <a name="user-sign-in-with-azure-active-directory-pass-through-authentication"></a>Início de sessão do utilizador com a Autenticação Pass-through do Azure Active Directory

## <a name="what-is-azure-active-directory-pass-through-authentication"></a>O que é a autenticação de passagem de diretório ativo azure?

A Autenticação pass-through do Azure Ative Directory (Azure AD) permite que os seus utilizadores acedam tanto nas instalações como nas aplicações baseadas na nuvem utilizando as mesmas palavras-passe. Esta funcionalidade proporciona uma experiência melhor aos seus utilizadores - menos uma palavra-passe para memorizar - e reduz os custos com o suporte de TI, uma vez que é menos provável que os utilizadores se esqueçam de como iniciar sessão. Quando os utilizadores entram na utilização do Azure AD, esta funcionalidade valida as palavras-passe dos utilizadores diretamente contra o seu Diretório Ativo no local.

>[!VIDEO https://www.youtube.com/embed/PyeAC85Gm7w]

Esta funcionalidade é uma alternativa à [Sincronização hash de senha azure AD,](how-to-connect-password-hash-synchronization.md)que proporciona o mesmo benefício de autenticação em nuvem às organizações. No entanto, certas organizações que pretendam impor as suas políticas de segurança e palavra-passe do Diretório Ativo no local podem optar por utilizar a Autenticação Pass-through. Reveja [este guia](https://docs.microsoft.com/azure/security/fundamentals/choose-ad-authn) para uma comparação dos vários métodos de entrada em Anúncios Azure e como escolher o método de entrada certo para a sua organização.

![Autenticação pass-through azure AD](./media/how-to-connect-pta/pta1.png)

Pode combinar a autenticação pass-through com a funcionalidade [Single Sign-On sem emenda.](how-to-connect-sso.md) Desta forma, quando os seus utilizadores acedem a aplicações nas suas máquinas corporativas dentro da sua rede corporativa, não precisam de escrever as suas palavras-passe para iniciar sessão.

## <a name="key-benefits-of-using-azure-ad-pass-through-authentication"></a>Principais benefícios da utilização da autenticação pass-through azure AD

- *Experiência de utilizador excecional*
  - Os utilizadores usam as mesmas palavras-passe para iniciar sessão tanto no local como nas aplicações baseadas na nuvem.
  - Os utilizadores passam menos tempo a falar com o helpdesk de TI a resolver problemas relacionados com a palavra-passe.
  - Os utilizadores podem completar tarefas [de gestão de senhas de autosserviço](../authentication/active-directory-passwords-overview.md) na nuvem.
- *Fácil de implementar & administrar*
  - Não é necessário implementações complexas no local ou configuração da rede.
  - Precisa apenas de um agente leve para ser instalado no local.
  - Sem despesas de gestão. O agente recebe automaticamente melhorias e correções de erros.
- *Proteger*
  - As palavras-passe no local nunca são armazenadas na nuvem de qualquer forma.
  - Protege as suas contas de utilizador trabalhando perfeitamente com as políticas de [Acesso Condicional Azure AD,](../active-directory-conditional-access-azure-portal.md)incluindo a Autenticação Multi-Factor (MFA), [bloqueando a autenticação do legado](../conditional-access/concept-conditional-access-conditions.md) e [filtrando ataques](../authentication/howto-password-smart-lockout.md)de senha de força bruta .
  - O agente só faz ligações de saída a partir da sua rede. Portanto, não existe qualquer obrigação de instalar o agente numa rede de perímetro, também conhecida como DMZ.
  - A comunicação entre um agente e a AD Azure é assegurada com recurso à autenticação baseada em certificados. Estes certificados são automaticamente renovados a cada poucos meses pela Azure AD.
- *Elevada disponibilidade*
  - Agentes adicionais podem ser instalados em vários servidores no local para fornecer uma alta disponibilidade de pedidos de sessão.

## <a name="feature-highlights"></a>Destaques de recurso

- Suporta o login do utilizador em todas as aplicações baseadas no navegador web e em aplicações de clientes do Microsoft Office que usam [a autenticação moderna.](https://aka.ms/modernauthga)
- Os nomes de utilizador de entrada de sessão`userPrincipalName`podem ser o nome de utilizador padrão no `Alternate ID`local ( ) ou outro atributo configurado no Azure AD Connect (conhecido como ).
- A funcionalidade funciona [perfeitamente](../active-directory-conditional-access-azure-portal.md) com funcionalidades de Acesso Condicional, como a Autenticação Multi-Factor (MFA) para ajudar a proteger os seus utilizadores.
- Integrado com a gestão de passwords de [autosserviço](../authentication/active-directory-passwords-overview.md)baseada na nuvem, incluindo a redação de palavras-passe para o Ative Directory e a proteção de passwords no local, proibindo senhas comumente usadas.
- Ambientes multiflorestais são apoiados se houver fundos florestais entre as suas florestas ad e se o encaminhamento do sufixo de nome estiver corretamente configurado.
- É uma funcionalidade gratuita, e não é preciso edições pagas de Azure AD para usá-lo.
- Pode ser ativado através do [Azure AD Connect](whatis-hybrid-identity.md).
- Utiliza um agente leve no local que ouve e responde a pedidos de validação de passwords.
- A instalação de vários agentes proporciona uma elevada disponibilidade de pedidos de sessão.
- Protege [protects](../authentication/howto-password-smart-lockout.md) as suas contas no local contra ataques de senha de força bruta na nuvem.

## <a name="next-steps"></a>Passos seguintes

- [Quickstart](how-to-connect-pta-quick-start.md) - Prepare-se e execute a autenticação de passagem do Azure AD Pass-through.
- [Migrar de AD FS para a Autenticação Pass-through](https://github.com/Identity-Deployment-Guides/Identity-Deployment-Guides/blob/master/Authentication/Migrating%20from%20Federated%20Authentication%20to%20Pass-through%20Authentication.docx?raw=true) - Um guia detalhado para migrar de AD FS (ou outras tecnologias da federação) para a Autenticação Pass-through.
- [Smart Lockout](../authentication/howto-password-smart-lockout.md) - Configure a capacidade de bloqueio inteligente no seu inquilino para proteger as contas do utilizador.
- [Limitações atuais](how-to-connect-pta-current-limitations.md) - Saiba quais os cenários suportados e quais não são.
- [Mergulho Técnico Deep](how-to-connect-pta-how-it-works.md) - Entenda como esta funcionalidade funciona.
- [Perguntas frequentes](how-to-connect-pta-faq.md) - Respostas a perguntas frequentes.
- [Troubleshoot](tshoot-connect-pass-through-authentication.md) - Aprenda a resolver problemas comuns com a funcionalidade.
- [Security Deep Dive](how-to-connect-pta-security-deep-dive.md) - Informações técnicas adicionais sobre a funcionalidade.
- [Azure AD Seamless SSO](how-to-connect-sso.md) - Saiba mais sobre esta funcionalidade complementar.
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) - Para arquivar novos pedidos de funcionalidades.
