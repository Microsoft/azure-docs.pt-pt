---
title: Experiências de entrada com Azure AD Identity Protection | Microsoft Docs
description: Fornece uma visão geral da experiência do usuário quando a proteção de identidade diminuiu ou corrigiu um usuário ou quando a autenticação multifator é exigida por uma política.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 01/15/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1e513027eed44ec7649f41f8786882aed8511bc6
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/19/2019
ms.locfileid: "68335490"
---
# <a name="sign-in-experiences-with-azure-ad-identity-protection"></a>Experiências de entrada com Azure AD Identity Protection

Com Azure Active Directory Identity Protection, você pode:

* exigir que os usuários se registrem para autenticação multifator
* manipular entradas arriscadas e usuários comprometidos

A resposta do sistema para esses problemas tem um impacto na experiência de entrada de um usuário, pois entrar diretamente fornecendo um nome de usuário e uma senha não será mais possível. Etapas adicionais são necessárias para tornar um usuário com segurança de volta para o negócio.

Este artigo fornece uma visão geral da experiência de entrada de um usuário para todos os casos que podem ocorrer.

**Multi-Factor Authentication**

* Registro de autenticação multifator

**Entrar em risco**

* Recuperação de entrada arriscada
* Entrada arriscada bloqueada
* Registro de autenticação multifator durante uma entrada arriscada

**Usuário em risco**

* Recuperação de conta comprometida
* Conta comprometida bloqueada

## <a name="multi-factor-authentication-registration"></a>Registro de autenticação multifator
A melhor experiência de usuário para ambos, o fluxo de recuperação de conta comprometida e o fluxo de entrada arriscado, é quando o usuário pode se recuperar automaticamente. Se os usuários estiverem registrados para a autenticação multifator, eles já terão um número de telefone associado à sua conta que pode ser usada para passar desafios de segurança. Nenhum suporte técnico ou de administrador é necessário para recuperar-se do comprometimento da conta. Portanto, é altamente recomendável que os usuários sejam registrados para autenticação multifator. 

Os administradores podem definir uma política que exige que os usuários configurem suas contas para verificação de segurança adicional. Essa política permite que os usuários ignorem o registro da autenticação multifator por até 14 dias. O período de carência de 14 dias não é configurável.

**O registro da autenticação multifator tem três etapas:**

1. Na primeira etapa, o usuário recebe uma notificação sobre a necessidade de definir a conta para a autenticação multifator. 
   
    ![Correção](./media/flows/140.png "Correção")
2. Para configurar a autenticação multifator, você precisa permitir que o sistema saiba como você deseja entrar em contato.
   
    ![Correção](./media/flows/141.png "Correção")
3. O sistema envia um desafio para você e você precisa responder.
   
    ![Correção](./media/flows/142.png "Correção")

## <a name="risky-sign-in-recovery"></a>Recuperação de entrada arriscada
Quando um administrador configurou uma política para riscos de entrada, os usuários afetados são notificados quando tentam entrar. 

**O fluxo de entrada arriscada tem duas etapas:** 

1. O usuário é informado de que algo incomum foi detectado sobre sua entrada, como entrar de um novo local, dispositivo ou aplicativo. 
   
    ![Correção](./media/flows/120.png "Correção")
2. O usuário precisa provar sua identidade resolvendo um desafio de segurança. Se o usuário estiver registrado para autenticação multifator, precisará fazer ida e volta um código de segurança para seu número de telefone. Como essa é apenas uma entrada arriscada e não uma conta comprometida, o usuário não precisará alterar a senha nesse fluxo. 
   
    ![Correção](./media/flows/121.png "Correção")

## <a name="risky-sign-in-blocked"></a>Entrada arriscada bloqueada
Os administradores também podem optar por definir uma política de risco de entrada para bloquear os usuários após a entrada, dependendo do nível de risco. Para serem desbloqueados, os usuários finais devem entrar em contato com um administrador ou um suporte técnico ou podem tentar entrar de um dispositivo ou local conhecido. A recuperação automática ao resolver a autenticação multifator não é uma opção nesse caso.

![Correção](./media/flows/200.png "Correção")

## <a name="compromised-account-recovery"></a>Recuperação de conta comprometida
Quando uma política de segurança de risco do usuário é configurada, os usuários que atendem ao nível de risco do usuário especificado na política (e, portanto, são considerados comprometidos) devem passar pelo fluxo de recuperação de comprometimento do usuário antes que possam entrar. 

**O fluxo de recuperação de comprometimento do usuário tem três etapas:**

1. O usuário é informado de que sua segurança de conta está em risco devido à atividade suspeita ou credenciais vazadas.
   
    ![Correção](./media/flows/101.png "Correção")
2. O usuário precisa provar sua identidade resolvendo um desafio de segurança. Se o usuário estiver registrado para a autenticação multifator, ele poderá se recuperar do comprometimento. Eles precisarão fazer ida e volta um código de segurança para seu número de telefone. 
   
   ![Correção](./media/flows/110.png "Correção")
3. Por fim, o usuário é forçado a alterar sua senha, já que outra pessoa pode ter tido acesso à sua conta. 
   As capturas de tela dessa experiência estão abaixo.
   
   ![Correção](./media/flows/111.png "Correção")

## <a name="compromised-account-blocked"></a>Conta comprometida bloqueada
Para obter um usuário que foi bloqueado por uma política de segurança de risco do usuário desbloqueada, o usuário deve entrar em contato com um administrador ou assistência técnica. A recuperação automática ao resolver a autenticação multifator não é uma opção nesse caso.

![Correção](./media/flows/104.png "Correção")

## <a name="reset-password"></a>Repor palavra-passe
Se os usuários comprometidos estiverem impedidos de entrar, um administrador poderá gerar uma senha temporária para eles. Os usuários terão que alterar sua senha durante a próxima entrada.

![Correção](./media/flows/160.png "Correção")

## <a name="see-also"></a>Consulte também

* [Azure Active Directory Identity Protection](../active-directory-identityprotection.md) 
