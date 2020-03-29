---
title: 'Azure AD Connect: Autenticação pass-through - Como funciona Microsoft Docs'
description: Este artigo descreve como funciona a autenticação de passagem de diretório ativo azure
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
ms.date: 07/19/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 59cd52dbdf6c13900cde592aeb52d8bf9abf850f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "60347795"
---
# <a name="azure-active-directory-pass-through-authentication-technical-deep-dive"></a>Autenticação de passagem por curso de diretório ativo Azure: mergulho técnico profundo
Este artigo é uma visão geral de como funciona o diretório Azure Ative (Azure AD) Pass-through Authentication. Para obter informações técnicas e de segurança profundas, consulte o artigo de [mergulho profundo da Segurança.](how-to-connect-pta-security-deep-dive.md)

## <a name="how-does-azure-active-directory-pass-through-authentication-work"></a>Como funciona a autenticação de passagem de diretório ativo azure?

>[!NOTE]
>Como pré-requisito para que a Autenticação Pass-through funcione, os utilizadores precisam de ser aprovisionados no Azure AD a partir do Diretório Ativo no local utilizando o Azure AD Connect. A autenticação pass-through não se aplica apenas a utilizadores com nuvem.

Quando um utilizador tenta iniciar sessão numa aplicação protegida pela Azure AD, e se a Autenticação Pass-through estiver ativada no inquilino, ocorrem os seguintes passos:

1. O utilizador tenta aceder a uma aplicação, por exemplo, [aplicação Web do Outlook.](https://outlook.office365.com/owa/)
2. Se o utilizador ainda não estiver inscrito, o utilizador é redirecionado para a página **de entrada do Utilizador** AD Azure.
3. O utilizador introduz o seu nome de utilizador na página de sinal de Anúncio Azure e, em seguida, seleciona o botão **Seguinte.**
4. O utilizador introduz a sua palavra-passe na página de sinal de Anúncio Do Azure e, em seguida, seleciona o botão **'Sinal' no** botão.
5. A Azure AD, ao receber o pedido de inscrição, coloca o nome de utilizador e a palavra-passe (encriptado utilizando a chave pública dos Agentes de Autenticação) numa fila.
6. Um Agente de Autenticação no local recupera o nome de utilizador e a palavra-passe encriptada da fila. Note que o Agente não faz frequentemente sondagem para pedidos da fila, mas recupera pedidos sobre uma ligação persistente pré-estabelecida.
7. O agente desencripta a palavra-passe utilizando a sua chave privada.
8. O agente valida o nome de utilizador e a palavra-passe contra o Ative Directory utilizando APIs padrão do Windows, que é um mecanismo semelhante ao que os Serviços da Federação de DiretórioS Ativos (AD FS) usam. O nome de utilizador pode ser o nome `userPrincipalName`de utilizador predefinido no local, normalmente, `Alternate ID`ou outro atributo configurado no Azure AD Connect (conhecido como ).
9. O controlador de domínio Ative Directory (DC) no local avalia o pedido e devolve ao agente a resposta adequada (sucesso, falha, senha expirada ou utilizador bloqueado).
10. O Agente de Autenticação, por sua vez, devolve esta resposta à Azure AD.
11. A Azure AD avalia a resposta e responde ao utilizador conforme apropriado. Por exemplo, a AD Azure ou assina o utilizador imediatamente ou solicita a autenticação de multi-factores Azure.
12. Se o utilizador iniciar o seu registo, o utilizador pode aceder à aplicação.

O seguinte diagrama ilustra todos os componentes e passos envolvidos:

![Autenticação pass-through](./media/how-to-connect-pta-how-it-works/pta2.png)

## <a name="next-steps"></a>Passos seguintes
- [Limitações atuais](how-to-connect-pta-current-limitations.md): Saiba quais os cenários suportados e quais não são.
- [Início rápido](how-to-connect-pta-quick-start.md): Levantar-se e funcionar na Autenticação de Passagem De AD Azure.
- [Migrar de AD FS para a Autenticação Pass-through](https://aka.ms/adfstoPTADP) - Um guia detalhado para migrar de AD FS (ou outras tecnologias da federação) para a Autenticação Pass-through.
- [Smart Lockout](../authentication/howto-password-smart-lockout.md): Configure a capacidade de bloqueio inteligente no seu inquilino para proteger as contas do utilizador.
- [Perguntas frequentes](how-to-connect-pta-faq.md): Encontre respostas para perguntas frequentes.
- [Resolução de problemas](tshoot-connect-pass-through-authentication.md): Aprenda a resolver problemas comuns com a função de Autenticação Pass-through.
- [Segurança Deep Dive](how-to-connect-pta-security-deep-dive.md): Obtenha informações técnicas profundas sobre a função de autenticação pass-through.
- [Azure AD Seamless SSO](how-to-connect-sso.md): Saiba mais sobre esta funcionalidade complementar.
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): Utilize o Fórum de Diretório Ativo Azure para apresentar novos pedidos de funcionalidades.

