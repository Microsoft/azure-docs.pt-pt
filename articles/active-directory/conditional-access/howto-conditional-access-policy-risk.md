---
title: Acesso condicional-acesso condicional com base em risco-Azure Active Directory
description: Criar políticas de acesso condicional para habilitar aprimoramentos de proteção de identidade para políticas
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 08/16/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 64d1b3e2f36256164420ae6b2e699f0ef48e2e78
ms.sourcegitcommit: 5ded08785546f4a687c2f76b2b871bbe802e7dae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/19/2019
ms.locfileid: "69576553"
---
# <a name="conditional-access-risk-based-conditional-access"></a>Acesso condicional: Acesso condicional com base em risco

Organizações com licenças Azure AD Premium P2 podem criar políticas de acesso condicional que incorporam Azure AD Identity Protection eventos de risco. Há três políticas padrão que podem ser habilitadas prontamente. 

* Exigir que todos os usuários se registrem para a autenticação multifator do Azure.
* Exigir uma alteração de senha para usuários que são de alto risco.
* Exigir autenticação multifator para usuários com risco de entrada médio ou alto.

## <a name="require-all-users-to-register-for-azure-multi-factor-authentication"></a>Exigir que todos os usuários se registrem para a autenticação multifator do Azure

Habilitar essa política exigirá que todos os usuários se registrem para a autenticação multifator do Azure dentro de 14 dias. 

1. Inicie sessão no **portal do Azure**.
1. Clique em **Todos os serviços** e, em seguida, procure **Azure AD Identity Protection**.
1. Clique em **Registo na MFA**.
1. Em **atribuições**, selecione **usuários**.
   1. Em **incluir**, selecione **todos os usuários**.
   1. Em **excluir**, selecione **Selecionar usuários excluídos**, escolha o acesso de emergência da sua organização ou contas de vidro e selecione **selecionar**. 
   1. Selecione **Done** (Concluído).
1. Defina **aplicar política** como **ativado**.
1. Clique em **Guardar**.

## <a name="require-a-password-change-high-risk-users"></a>Exigir uma alteração de senha de usuários de alto risco

A Microsoft trabalha com investigadores, entidades responsáveis pela aplicação da lei, várias equipas de segurança da Microsoft e outras origens fidedignas para localizar os pares de nome de utilizador e palavra-passe. Quando um destes pares corresponde a uma conta no seu ambiente, pode ser acionada uma alteração de palavra-passe baseada em risco, com a política seguinte.

1. Inicie sessão no **portal do Azure**.
1. Clique em **Todos os serviços** e, em seguida, procure **Azure AD Identity Protection**.
1. Clique em **política de risco do usuário**.
1. Em **atribuições**, selecione **usuários**
   1. Em **incluir**, selecione **todos os usuários**.
   1. Em **excluir**, selecione **Selecionar usuários excluídos**, escolha o acesso de emergência da sua organização ou contas de vidro e selecione **selecionar**.
   1. Selecione **Done** (Concluído).
1. Em **condições**, selecione **risco do usuário**e, em seguida, escolha **alta**.
   1. Clique em **selecionar** e em **concluído**.
1. Em **controles** > de**acesso**, escolha **permitir acesso**e, em seguida, selecione **exigir alteração de senha**.
   1. Clique em **Selecionar**.
1. Defina **aplicar política** como **ativado**.
1. Clique em **Guardar**.

## <a name="require-mfa-medium-or-high-sign-in-risk-users"></a>Exigir usuários de risco de entrada média ou alta MFA

A maioria dos utilizadores tem um comportamento normal que pode ser controlado. Quando fugirem a esta norma, pode ser arriscado permitir-lhes iniciar sessão. Talvez você queira bloquear esse usuário ou talvez apenas pedir que eles executem a autenticação multifator para provar que eles são realmente quem dizem que estão. Para ativar uma política que exija a MFA quando é detetado um risco de início de sessão, ative a política seguinte.

1. Inicie sessão no **portal do Azure**.
1. Clique em **Todos os serviços** e, em seguida, procure **Azure AD Identity Protection**.
1. Clique na **política de risco de entrada**
1. Em **atribuições**, selecione **usuários**
   1. Em **incluir**, selecione **todos os usuários**.
   1. Em **excluir**, selecione **Selecionar usuários excluídos**, escolha o acesso de emergência da sua organização ou contas de vidro e selecione **selecionar**.
   1. Selecione **Done** (Concluído).
1. Em **condições**, selecione **risco de entrada**e, em seguida, escolha **médio e superior**.
   1. Clique em **selecionar** e em **concluído**.
1. Em **controles** > de**acesso**, escolha **permitir acesso**e, em seguida, selecione **exigir autenticação**multifator.
   1. Clique em **Selecionar**.
1. Defina **aplicar política** como **ativado**.
1. Clique em **Guardar**.

## <a name="next-steps"></a>Passos Seguintes

[Políticas comuns de acesso condicional](concept-conditional-access-policy-common.md)

[Simular comportamento de entrada usando a ferramenta de What If de acesso condicional](troubleshoot-conditional-access-what-if.md)

[Como funciona: Azure Multi-Factor Authentication](../authentication/concept-mfa-howitworks.md) (Como funciona: Multi-Factor Authentication do Azure)

[O que é Azure Active Directory Identity Protection?](../identity-protection/overview.md)
