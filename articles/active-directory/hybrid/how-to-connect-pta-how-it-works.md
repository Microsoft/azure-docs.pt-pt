---
title: 'Azure AD Connect: Autenticação pass-through - como funciona | Documentos da Microsoft'
description: Este artigo descreve como funciona a autenticação de pass-through do Azure Active Directory
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
ms.date: 07/19/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 59cd52dbdf6c13900cde592aeb52d8bf9abf850f
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60347795"
---
# <a name="azure-active-directory-pass-through-authentication-technical-deep-dive"></a>Autenticação pass-through do Azure Active Directory: Análise técnica aprofundada
Este artigo é uma visão geral de como funciona a autenticação pass-through do Azure Active directory (Azure AD). Para técnica e informações de segurança, consulte a [detalhada da segurança](how-to-connect-pta-security-deep-dive.md) artigo.

## <a name="how-does-azure-active-directory-pass-through-authentication-work"></a>Como funciona a autenticação de pass-through do Azure Active Directory?

>[!NOTE]
>Como um pré-requisito para a autenticação pass-through funcione, os utilizadores têm de ser aprovisionado com o Azure AD a partir do Active Directory no local com o Azure AD Connect. Autenticação pass-through não é aplicável aos utilizadores apenas na cloud.

Quando um utilizador tenta iniciar sessão a uma aplicação protegida pelo Azure AD, e se a autenticação pass-through está ativada no inquilino, ocorrem os seguintes passos:

1. O utilizador tenta aceder a uma aplicação, por exemplo, [Outlook Web App](https://outlook.office365.com/owa/).
2. Se o utilizador não está já iniciou sessão, o utilizador é redirecionado para o Azure AD **sessão do utilizador** página.
3. O utilizador introduz o respetivo nome de utilizador na página de início de sessão do Azure AD e, em seguida, seleciona a **seguinte** botão.
4. O utilizador introduz a palavra-passe na página de início de sessão do Azure AD e, em seguida, seleciona a **iniciar sessão** botão.
5. Azure AD, na receção do pedido para iniciar sessão, coloca o nome de utilizador e palavra-passe (encriptadas utilizando a chave pública dos agentes de autenticação) numa fila.
6. Um agente de autenticação no local obtém o nome de utilizador e palavra-passe encriptada da fila. Tenha em atenção que o agente não consultar frequentemente pedidos da fila, mas obtém pedidos através de uma ligação persistente previamente estabelecida.
7. O agente desencripta a palavra-passe utilizando a respetiva chave privada.
8. O agente valida o nome de utilizador e palavra-passe no Active Directory, utilizando APIs padrão do Windows, que é um mecanismo semelhante para os serviços de Federação do Active Directory (AD FS) utiliza. O nome de utilizador pode ser qualquer um dos locais padrão nome de usuário, geralmente `userPrincipalName`, ou outro atributo que configurou no Azure AD Connect (conhecido como `Alternate ID`).
9. O controlador de domínio do Active Directory no local (DC) avalia a solicitação e retorna a resposta adequada (êxito, falha, palavra-passe expirada ou utilizador bloqueadas) para o agente.
10. O agente de autenticação, por sua vez, retorna essa resposta para o Azure AD.
11. O Azure AD avalia a resposta e responde ao usuário conforme apropriado. Por exemplo, do Azure AD assina o utilizador imediatamente ou pedidos para o Azure multi-factor Authentication.
12. Se o utilizador inicie sessão for bem-sucedida, o utilizador pode aceder à aplicação.

O diagrama seguinte ilustra a todos os componentes e as etapas envolvidas:

![Autenticação pass-through](./media/how-to-connect-pta-how-it-works/pta2.png)

## <a name="next-steps"></a>Passos Seguintes
- [Limitações atuais](how-to-connect-pta-current-limitations.md): Saiba quais cenários são suportados e quais não são.
- [Início Rápido](how-to-connect-pta-quick-start.md): Começar a trabalhar na autenticação pass-through do Azure AD.
- [Migrar do AD FS para autenticação pass-through](https://aka.ms/adfstoPTADP) -um guia detalhado para migrar do AD FS (ou outras tecnologias de Federação) para autenticação pass-through.
- [Bloqueio do smart](../authentication/howto-password-smart-lockout.md): Configure a capacidade de bloqueio inteligente no seu inquilino para proteger contas de utilizador.
- [Perguntas mais frequentes sobre](how-to-connect-pta-faq.md): Encontre respostas para perguntas mais frequentes.
- [Resolver problemas de](tshoot-connect-pass-through-authentication.md): Saiba como resolver problemas comuns com a funcionalidade de autenticação pass-through.
- [Detalhada da segurança](how-to-connect-pta-security-deep-dive.md): Obtenha informações técnicas detalhadas sobre a funcionalidade de autenticação pass-through.
- [SSO totalmente integrado do Azure AD](how-to-connect-sso.md): Saiba mais sobre esta funcionalidade complementar.
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): Utilize o Fórum do Azure Active Directory para pedidos de novas funcionalidades de ficheiros.

