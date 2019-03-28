---
title: Experiências de início de sessão com o Azure AD Identity Protection | Documentos da Microsoft
description: Fornece uma descrição geral da experiência do usuário quando Identity Protection tem atenuados ou remediar a um utilizador ou quando necessita de uma política de autenticação multifator.
services: active-directory
keywords: proteção de identidade do Azure Active Directory, descoberta de aplicações na cloud, gestão de aplicações, a segurança, a risco, a nível de risco, a vulnerabilidade, a política de segurança
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: daveba
ms.assetid: de5bf637-75a7-4104-b6d8-03686372a319
ms.service: active-directory
ms.subservice: identity-protection
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2018
ms.author: joflore
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 449f808e98c4e0db2972071e160f5335153a88f2
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2019
ms.locfileid: "58520137"
---
# <a name="sign-in-experiences-with-azure-ad-identity-protection"></a>Experiências de início de sessão com o Azure AD Identity Protection
Com o Azure Active Directory Identity Protection, pode:

* exigir que os utilizadores se registarem na autenticação multifator
* lidar com o risco de inícios de sessão e utilizadores comprometidos

A resposta do sistema a essas questões tem um impacto na experiência de início de sessão do utilizador, porque diretamente iniciar sessão, fornecendo um nome de utilizador e uma palavra-passe não será possível deixa de poder. São necessários passos adicionais para obter um utilizador com segurança sobre o negócio.

Este artigo fornece uma descrição geral da experiência de início de sessão do utilizador para todos os casos que podem ocorrer.

**Multi-Factor Authentication**

* Registo de autenticação multifator

**Início de sessão em risco**

* Recuperação de início de sessão arriscada
* Risco início de sessão bloqueado
* Registo de autenticação multifator durante um risco início de sessão

**Utilizadores em risco**

* Recuperação de conta comprometida
* Conta comprometida bloqueada

## <a name="multi-factor-authentication-registration"></a>Registo de autenticação multifator
A melhor experiência de utilizador para ambos, o fluxo de recuperação de conta comprometida e risco fluxo início de sessão, é quando o utilizador Self-pode recuperar. Se os utilizadores estiverem registados para autenticação multifator, que já têm um número de telefone associado à sua conta que pode ser usada para passar os desafios de segurança. Nenhum envolvimento de suporte técnico ou o administrador de ajuda é necessário para recuperar a partir de comprometimento de conta. Portanto, é altamente recomendado para obter os seus utilizadores se registou para o multi-factor authentication. 

Os administradores podem definir uma política que requer que os utilizadores configurar as suas contas para verificação de segurança adicional. Esta política permite aos utilizadores ignorar o registo de autenticação multifator durante 14 dias. O período de tolerância de 14 dias não é configurável.

**O registo de autenticação multifator tem três passos:**

1. No primeiro passo, o utilizador recebe uma notificação sobre o requisito para definir a conta para multi-factor authentication. 
   
    ![Remediação](./media/flows/140.png "remediação")
2. Para configurar a autenticação multifator, terá de permitir que o sistema sabe como pretende ser contactado.
   
    ![Remediação](./media/flows/141.png "remediação")
3. O sistema envia um desafio para e precisa responder.
   
    ![Remediação](./media/flows/142.png "remediação")

## <a name="risky-sign-in-recovery"></a>Recuperação de início de sessão arriscada
Quando um administrador tiver configurado uma política para os riscos de início de sessão, os utilizadores afetados são notificados quando o utilizador tentar iniciar sessão. 

**O fluxo de início de sessão arriscado tem duas etapas:** 

1. O usuário será informado de que algo invulgar foi detetado sobre o início de sessão, tais como iniciar sessão a partir de uma nova localização, dispositivo ou aplicação. 
   
    ![Remediação](./media/flows/120.png "remediação")
2. O utilizador é obrigado a provar a sua identidade por resolver um desafio de segurança. Se o utilizador está registado na autenticação multifator que precisam para arredondar enganar um código de segurança para o número de telefone. Como essa é apenas um risco início de sessão e não uma conta comprometida, o utilizador não terá de alterar a palavra-passe neste fluxo. 
   
    ![Remediação](./media/flows/121.png "remediação")

## <a name="risky-sign-in-blocked"></a>Risco início de sessão bloqueado
Os administradores também podem optar por definir uma política de início de sessão de risco para bloquear os utilizadores durante o início de sessão dependendo do nível de risco. Para obter desbloqueado, os utilizadores finais têm de contactar um administrador ou o suporte técnico ou podem tentar iniciar sessão a partir de um dispositivo ou localização familiar. A Self-recuperação por resolver a autenticação multifator não é uma opção em vez disso.

![Remediação](./media/flows/200.png "remediação")

## <a name="compromised-account-recovery"></a>Recuperação de conta comprometida
Quando tiver sido configurada uma política de segurança de risco do utilizador, os utilizadores que o utilizador de cumprir os especificado na política de nível de risco (e, portanto, pressupõe-se comprometido) deve passar pelo fluxo de recuperação de comprometimento de utilizador antes de poderem iniciar sessão. 

**O fluxo de recuperação de comprometimento de usuário tem três passos:**

1. O usuário será informado de que a segurança de conta está em risco devido a atividade suspeita ou fuga de credenciais.
   
    ![Remediação](./media/flows/101.png "remediação")
2. O utilizador é obrigado a provar a sua identidade por resolver um desafio de segurança. Se o utilizador está registado na autenticação multifator Self-eles podem recuperar sejam comprometidos. Eles precisam arredondar enganar um código de segurança para o número de telefone. 
   
   ![Remediação](./media/flows/110.png "remediação")
3. Por fim, o utilizador é forçado a alterar a palavra-passe, uma vez que outra pessoa poderá ter tido acesso à sua conta. 
   Capturas de ecrã desta experiência de estão abaixo.
   
   ![Remediação](./media/flows/111.png "remediação")

## <a name="compromised-account-blocked"></a>Conta comprometida bloqueada
Para obter um utilizador que foi bloqueado por uma política de segurança de risco do utilizador desbloqueada, o utilizador tem de contactar um administrador ou suporte técnico. A Self-recuperação por resolver a autenticação multifator não é uma opção em vez disso.

![Remediação](./media/flows/104.png "remediação")

## <a name="reset-password"></a>Repor palavra-passe
Se comprometido utilizadores são impedidos de iniciar sessão, um administrador pode gerar uma palavra-passe temporária para os mesmos. Os utilizadores têm de alterar a palavra-passe durante o início de um próximo início de sessão.

![Remediação](./media/flows/160.png "remediação")

## <a name="see-also"></a>Consulte também
* [O Azure Active Directory Identity Protection](../active-directory-identityprotection.md) 

