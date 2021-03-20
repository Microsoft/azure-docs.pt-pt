---
title: 'Azure AD Connect: | de autenticação pass-through Microsoft Docs'
description: Este artigo descreve a autenticação pass-through do Azure Ative (Azure AD) e como permite a entrada de Azure AD, validando as palavras-passe dos utilizadores contra o Ative Directory no local.
services: active-directory
keywords: o que é Azure AD Connect Authentication Pass-through, instalar Ative Directory, componentes necessários para Azure AD, SSO, Single Sign-on
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 10/21/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0b8f613cb7c75d9dd6af1fcf62f9d484398072c6
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "89279470"
---
# <a name="user-sign-in-with-azure-active-directory-pass-through-authentication"></a>Início de sessão do utilizador com a Autenticação Pass-through do Azure Active Directory

## <a name="what-is-azure-active-directory-pass-through-authentication"></a>O que é a autenticação pass-through do Azure Ative Directory?

A Azure Ative Directory (Azure AD) A Autenticação pass-through permite que os seus utilizadores entrem no local e aplicações baseadas na nuvem usando as mesmas palavras-passe. Esta funcionalidade proporciona uma experiência melhor aos seus utilizadores - menos uma palavra-passe para memorizar - e reduz os custos com o suporte de TI, uma vez que é menos provável que os utilizadores se esqueçam de como iniciar sessão. Quando os utilizadores iniciarem sação utilizando o Azure AD, esta funcionalidade valida as palavras-passe dos utilizadores diretamente contra o seu Ative Directy no local.

>[!VIDEO https://www.youtube.com/embed/PyeAC85Gm7w]

Esta funcionalidade é uma alternativa à [sincronização de Hash Synchronization Azure AD Password](how-to-connect-password-hash-synchronization.md), que proporciona o mesmo benefício da autenticação em nuvem às organizações. No entanto, certas organizações que pretendam impor as suas políticas de segurança e palavra-passe do Ative Directory, podem optar por utilizar a Autenticação Pass-through. [Reveja este guia](./choose-ad-authn.md) para uma comparação dos vários métodos de inscrição Azure AD e como escolher o método de inscrição certo para a sua organização.

![Autenticação pass-through Azure AD](./media/how-to-connect-pta/pta1.png)

Pode combinar a autenticação pass-through com a [função 'Sign-On' sem emenda.](how-to-connect-sso.md) Desta forma, quando os seus utilizadores estão a aceder a aplicações nas suas máquinas corporativas dentro da sua rede corporativa, não precisam de escrever as suas palavras-passe para iniciar sing.

## <a name="key-benefits-of-using-azure-ad-pass-through-authentication"></a>Principais benefícios da utilização da autenticação pass-through Azure AD

- *Experiência de utilizador excecional*
  - Os utilizadores usam as mesmas palavras-passe para assinar em aplicações no local e na nuvem.
  - Os utilizadores passam menos tempo a falar com o it helpdesk para resolver problemas relacionados com a palavra-passe.
  - Os utilizadores podem completar tarefas [de gestão de senhas de autosserviço](../authentication/concept-sspr-howitworks.md) na nuvem.
- *Fácil de implantar & administrar*
  - Não há necessidade de implementações complexas no local ou configuração de rede.
  - Só precisa de um agente leve para ser instalado no local.
  - Sem despesas de gestão. O agente recebe automaticamente melhorias e correções de erros.
- *Seguro*
  - As palavras-passe no local nunca são armazenadas na nuvem sob qualquer forma.
  - Protege as suas contas de utilizador trabalhando perfeitamente com [as políticas de acesso condicional Azure AD](../conditional-access/overview.md), incluindo a Autenticação Multi-Factor (MFA), [bloqueando a autenticação do legado](../conditional-access/concept-conditional-access-conditions.md) e [filtrando ataques de senha de força bruta](../authentication/howto-password-smart-lockout.md).
  - O agente só faz ligações de saída dentro da sua rede. Portanto, não há necessidade de instalar o agente numa rede de perímetro, também conhecida como DMZ.
  - A comunicação entre um agente e a Azure AD é assegurada através da autenticação baseada em certificados. Estes certificados são automaticamente renovados a cada poucos meses pela Azure AD.
- *Tenha elevada disponibilidade*
  - Agentes adicionais podem ser instalados em vários servidores no local para fornecer alta disponibilidade de pedidos de inscrição.

## <a name="feature-highlights"></a>Destaques de recursos

- Suporta o login do utilizador em todas as aplicações baseadas no navegador web e nas aplicações de clientes do Microsoft Office que utilizam [a autenticação moderna.](https://aka.ms/modernauthga)
- Os nomes de utilizador de inscrição podem ser o nome de utilizador predefinido no local `userPrincipalName` () ou outro atributo configurado no Azure AD Connect (conhecido como `Alternate ID` ).
- A funcionalidade funciona perfeitamente com funcionalidades [de Acesso Condicional,](../conditional-access/overview.md) como a Autenticação Multi-Factor (MFA) para ajudar a proteger os seus utilizadores.
- Integrado com a [gestão de senhas de autosserviço](../authentication/concept-sspr-howitworks.md)baseadas na nuvem, incluindo a gravação de passwords para o Ative Directory e proteção de passwords no local, proibindo palavras-passe geralmente utilizadas.
- Ambientes multi-florestais são apoiados se houver fundos florestais entre as suas florestas de AD e se o encaminhamento de sufixo de nome estiver corretamente configurado.
- É uma funcionalidade gratuita e não precisa de edições pagas da Azure AD para usá-lo.
- Pode ser ativado através do [Azure AD Connect](whatis-hybrid-identity.md).
- Usa um agente leve no local que ouve e responde a pedidos de validação de palavras-passe.
- A instalação de vários agentes proporciona uma elevada disponibilidade de pedidos de inscrição.
- [Protege as](../authentication/howto-password-smart-lockout.md) suas contas no local contra ataques de senhas de força bruta na nuvem.

## <a name="next-steps"></a>Passos seguintes

- [Quickstart](how-to-connect-pta-quick-start.md) - Levante-se e execute a autenticação pass-through Azure.
- [Migrar de AD FS para Autenticação Pass-through](https://github.com/Identity-Deployment-Guides/Identity-Deployment-Guides/blob/master/Authentication/Migrating%20from%20Federated%20Authentication%20to%20Pass-through%20Authentication.docx?raw=true) - Um guia detalhado para migrar de FS AD (ou outras tecnologias da federação) para a Autenticação Pass-through.
- [Smart Lockout](../authentication/howto-password-smart-lockout.md) - Configurar a capacidade de bloqueio inteligente no seu inquilino para proteger as contas do utilizador.
- [Limitações atuais](how-to-connect-pta-current-limitations.md) - Saiba quais os cenários que são apoiados e quais não são.
- [Mergulho Profundo Técnico](how-to-connect-pta-how-it-works.md) - Entenda como funciona esta funcionalidade.
- [Perguntas frequentes](how-to-connect-pta-faq.md) - Respostas a perguntas frequentes.
- [Resolução de problemas](tshoot-connect-pass-through-authentication.md) - Aprenda a resolver problemas comuns com a funcionalidade.
- [Security Deep Dive](how-to-connect-pta-security-deep-dive.md) - Informações técnicas adicionais profundas sobre a funcionalidade.
- [Azure AD Seamless SSO](how-to-connect-sso.md) - Saiba mais sobre esta característica complementar.
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) - Para arquivar novos pedidos de funcionalidades.