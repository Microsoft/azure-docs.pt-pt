---
title: 'Azure AD Connect: Autenticação de passagem | Microsoft Docs'
description: Este artigo descreve a autenticação de passagem do Azure Active Directory (AD do Azure) e como ela permite entradas do Azure AD validando as senhas dos usuários no Active Directory local.
services: active-directory
keywords: o que é Azure AD Connect autenticação de passagem, instalar Active Directory, componentes necessários para o Azure AD, SSO, logon único
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
ms.openlocfilehash: 814c81b6092c4af3778617e165a0bdbce09d71d7
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/05/2019
ms.locfileid: "68779114"
---
# <a name="user-sign-in-with-azure-active-directory-pass-through-authentication"></a>Entrada do usuário com Azure Active Directory autenticação de passagem

## <a name="what-is-azure-active-directory-pass-through-authentication"></a>O que é Azure Active Directory autenticação de passagem?

A autenticação de passagem do Azure Active Directory (AD do Azure) permite que os usuários entrem em aplicativos locais e baseados em nuvem usando as mesmas senhas. Esta funcionalidade proporciona uma experiência melhor aos seus utilizadores - menos uma palavra-passe para memorizar - e reduz os custos com o suporte de TI, uma vez que é menos provável que os utilizadores se esqueçam de como iniciar sessão. Quando os usuários entram usando o Azure AD, esse recurso valida as senhas dos usuários diretamente em seu Active Directory local.

>[!VIDEO https://www.youtube.com/embed/PyeAC85Gm7w]

Esse recurso é uma alternativa à [sincronização de hash de senha do Azure ad](how-to-connect-password-hash-synchronization.md), que fornece o mesmo benefício da autenticação de nuvem para as organizações. No entanto, certas organizações que desejam impor suas políticas locais de segurança Active Directory e senha, podem optar por usar a autenticação de passagem em vez disso. Examine [este guia](https://docs.microsoft.com/azure/security/fundamentals/choose-ad-authn) para obter uma comparação dos vários métodos de entrada do Azure AD e como escolher o método de entrada correto para sua organização.

![Autenticação de passagem do Azure AD](./media/how-to-connect-pta/pta1.png)

Você pode combinar a autenticação de passagem com o recurso de [logon único contínuo](how-to-connect-sso.md) . Dessa forma, quando os usuários estiverem acessando aplicativos em seus computadores corporativos dentro de sua rede corporativa, eles não precisarão digitar suas senhas para entrar.

## <a name="key-benefits-of-using-azure-ad-pass-through-authentication"></a>Principais benefícios de usar a autenticação de passagem do Azure AD

- *Experiência de utilizador excecional*
  - Os usuários usam as mesmas senhas para entrar em aplicativos locais e baseados em nuvem.
  - Os usuários gastam menos tempo se comunicando com a assistência técnica de ti resolvendo problemas relacionados a senhas.
  - Os usuários podem concluir tarefas de [Gerenciamento de senhas](../authentication/active-directory-passwords-overview.md) de autoatendimento na nuvem.
- *Fácil de implantar & administrar*
  - Não há necessidade de implantações locais complexas ou configuração de rede.
  - Precisa de apenas um agente leve para ser instalado no local.
  - Sem sobrecarga de gerenciamento. O agente recebe automaticamente melhorias e correções de bugs.
- *Proteger*
  - As senhas locais nunca são armazenadas na nuvem em nenhum formato.
  - O protege suas contas de usuário trabalhando diretamente com [as políticas de acesso condicional do Azure ad](../active-directory-conditional-access-azure-portal.md), incluindo a MFA (autenticação multifator), o [bloqueio de autenticação herdada](../conditional-access/conditions.md) e a [filtragem de ataques de senha de força bruta](../authentication/howto-password-smart-lockout.md).
  - O agente faz apenas conexões de saída de dentro de sua rede. Portanto, não há nenhum requisito para instalar o agente em uma rede de perímetro, também conhecido como DMZ.
  - A comunicação entre um agente e o Azure AD é protegida usando a autenticação baseada em certificado. Esses certificados são renovados automaticamente a cada poucos meses pelo Azure AD.
- *Altamente disponível*
  - Agentes adicionais podem ser instalados em vários servidores locais para fornecer alta disponibilidade de solicitações de entrada.

## <a name="feature-highlights"></a>Destaques de recursos

- Dá suporte à entrada do usuário em todos os aplicativos baseados em navegador da Web e em Microsoft Office aplicativos cliente que usam [autenticação moderna](https://aka.ms/modernauthga).
- Os nomes de usuário de entrada podem ser o nome de acesso padrão local (`userPrincipalName`) ou outro atributo configurado no Azure ad Connect (conhecido como `Alternate ID`).
- O recurso funciona diretamente com recursos de [acesso condicional](../active-directory-conditional-access-azure-portal.md) , como o MFA (autenticação multifator) para ajudar a proteger seus usuários.
- Integrado com o gerenciamento de [senhas](../authentication/active-directory-passwords-overview.md)de autoatendimento baseado em nuvem, incluindo o Write-back de senha para Active Directory local e proteção por senha, proibindo senhas comumente usadas.
- Ambientes de várias florestas têm suporte se houver relações de confiança entre suas florestas do AD e se o roteamento de sufixo de nome estiver configurado corretamente.
- É um recurso gratuito, e você não precisa de nenhuma edição paga do Azure AD para usá-lo.
- Ele pode ser habilitado por meio de [Azure ad Connect](whatis-hybrid-identity.md).
- Ele usa um agente local leve que escuta e responde a solicitações de validação de senha.
- A instalação de vários agentes fornece alta disponibilidade de solicitações de entrada.
- Ele [protege](../authentication/howto-password-smart-lockout.md) suas contas locais contra ataques de senha de força bruta na nuvem.

## <a name="next-steps"></a>Passos seguintes

- [Início rápido](how-to-connect-pta-quick-start.md) -obtenha a autenticação de passagem do Azure AD em execução.
- [Migrar de AD FS para autenticação de passagem](https://github.com/Identity-Deployment-Guides/Identity-Deployment-Guides/blob/master/Authentication/Migrating%20from%20Federated%20Authentication%20to%20Pass-through%20Authentication.docx?raw=true) – um guia detalhado para migrar do AD FS (ou outras tecnologias de Federação) para a autenticação de passagem.
- [Bloqueio inteligente](../authentication/howto-password-smart-lockout.md) – configure o recurso de bloqueio inteligente em seu locatário para proteger as contas de usuário.
- [Limitações atuais](how-to-connect-pta-current-limitations.md) -saiba quais cenários têm suporte e quais não são.
- [Aprofundamento técnico](how-to-connect-pta-how-it-works.md) – entenda como esse recurso funciona.
- [Perguntas](how-to-connect-pta-faq.md) frequentes-respostas para perguntas frequentes.
- [Solução de problemas](tshoot-connect-pass-through-authentication.md) -saiba como resolver problemas comuns com o recurso.
- Aprofundamento de [segurança](how-to-connect-pta-security-deep-dive.md) -mais informações técnicas detalhadas sobre o recurso.
- [SSO contínuo do Azure ad](how-to-connect-sso.md) -saiba mais sobre esse recurso complementar.
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) -para o arquivamento de novas solicitações de recursos.
