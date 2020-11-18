---
title: 'Azure AD Connect: Authentication pass-through - Como funciona Microsoft Docs'
description: Este artigo descreve como funciona a autenticação pass-through do Azure Ative Directory
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
ms.date: 07/19/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: fe92f761ac0b16da7c3cc3c69c1fa4b00f4e7579
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/18/2020
ms.locfileid: "94836364"
---
# <a name="azure-active-directory-pass-through-authentication-technical-deep-dive"></a>Autenticação pass-through do Diretório Ativo Azure: Mergulho profundo técnico
Este artigo é uma visão geral de como funciona o diretório Azure Ative (Azure AD) De autenticação pass-through. Para obter informações técnicas e de segurança profundas, consulte o artigo [de mergulho profundo da Segurança.](how-to-connect-pta-security-deep-dive.md)

## <a name="how-does-azure-active-directory-pass-through-authentication-work"></a>Como funciona o Azure Ative Directory Authentication?

>[!NOTE]
>Como pré-requisito para a autenticação pass-through para funcionar, os utilizadores precisam de ser a provisionados no Azure AD a partir de Ative Directory no local usando Azure AD Connect. A autenticação pass-through não se aplica apenas aos utilizadores na nuvem.

Quando um utilizador tenta iniciar sação numa aplicação protegida pela Azure AD, e se a Autenticação Pass-through estiver ativada no arrendatário, ocorrem os seguintes passos:

1. O utilizador tenta aceder a uma aplicação, por exemplo, [a Outlook Web App](https://outlook.office365.com/owa/).
2. Se o utilizador ainda não tiver assinado, o utilizador é redirecionado para a página **de inscrição do utilizador** Azure AD.
3. O utilizador introduz o seu nome de utilizador no sinal Azure AD na página e, em seguida, seleciona o botão **Seguinte.**
4. O utilizador introduz a sua palavra-passe no sinal AZure AD na página e, em seguida, seleciona o botão **'Sinal' na** página.
5. A Azure AD, ao receber o pedido de inscrição, coloca o nome de utilizador e palavra-passe (encriptado utilizando a chave pública dos Agentes de Autenticação) numa fila.
6. Um Agente de Autenticação no local recupera o nome de utilizador e a palavra-passe encriptada da fila. Note que o Agente não faz frequentemente sondagens para pedidos da fila, mas recupera pedidos sobre uma ligação persistente pré-estabelecida.
7. O agente desencripta a palavra-passe utilizando a sua chave privada.
8. O agente valida o nome de utilizador e a palavra-passe contra o Ative Directory utilizando apis padrão do Windows, que é um mecanismo semelhante ao que os Serviços da Federação de Diretório Ativo (AD FS) utilizam. O nome de utilizador pode ser o nome de utilizador padrão no local, `userPrincipalName` normalmente, ou outro atributo configurado no Azure AD Connect (conhecido como `Alternate ID` ).
9. O controlador de domínio Ative Directory (DC) no local avalia o pedido e devolve ao agente a resposta adequada (sucesso, falha, senha expirada ou utilizador bloqueado) ao agente.
10. O Agente de Autenticação, por sua vez, devolve esta resposta à Azure AD.
11. A Azure AD avalia a resposta e responde ao utilizador conforme apropriado. Por exemplo, a Azure AD ou assina imediatamente o utilizador ou solicita a autenticação multi-factor AD Azure.
12. Se o pedido de sedu máximo for bem sucedido, o utilizador poderá aceder à aplicação.

O diagrama a seguir ilustra todos os componentes e os passos envolvidos:

![Autenticação pass-through](./media/how-to-connect-pta-how-it-works/pta2.png)

## <a name="next-steps"></a>Passos seguintes
- [Limitações atuais](how-to-connect-pta-current-limitations.md): Saiba quais os cenários que são apoiados e quais não são.
- [Início Rápido](how-to-connect-pta-quick-start.md): Levante-se e corra na Autenticação Pass-through Azure.
- [Migrar de AD FS para Autenticação Pass-through](https://aka.ms/adfstoPTADP) - Um guia detalhado para migrar de FS AD (ou outras tecnologias da federação) para a Autenticação Pass-through.
- [Smart Lockout](../authentication/howto-password-smart-lockout.md): Configure a capacidade de bloqueio inteligente no seu inquilino para proteger as contas do utilizador.
- [Perguntas frequentes](how-to-connect-pta-faq.md): Encontre respostas a perguntas frequentes.
- [Resolução de problemas](tshoot-connect-pass-through-authentication.md): Aprenda a resolver problemas comuns com a função De autenticação Pass-through.
- [Security Deep Dive](how-to-connect-pta-security-deep-dive.md): Obtenha informações técnicas profundas sobre a funcionalidade de Autenticação Pass-through.
- [Azure AD Seamless SSO](how-to-connect-sso.md): Saiba mais sobre esta característica complementar.
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): Utilize o Azure Ative Directory Forum para apresentar novos pedidos de funcionalidades.

