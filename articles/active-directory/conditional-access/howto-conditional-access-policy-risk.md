---
title: Acesso Condicional - Acesso Condicional baseado em risco - Diretório Ativo Azure
description: Criar políticas de acesso condicional para permitir melhorias na Proteção de Identidade às políticas
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 05/26/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: b9cfba377aba30d4687bab4ba7c5a311c70c4905
ms.sourcegitcommit: fc718cc1078594819e8ed640b6ee4bef39e91f7f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "83995161"
---
# <a name="conditional-access-risk-based-conditional-access"></a>Acesso Condicional: Acesso Condicional baseado no risco

As organizações com licenças Azure AD Premium P2 podem criar políticas de Acesso Condicional incorporando deteções de risco de Proteção de Identidade Azure AD. Existem três políticas padrão que podem ser ativadas fora da caixa. 

* Exija que todos os utilizadores se registem para autenticação multi-factor Azure.
* Exija uma alteração de senha para utilizadores de alto risco.
* Requerer a autenticação de vários fatores para utilizadores com risco de inscrição média ou elevada.

## <a name="require-all-users-to-register-for-azure-multi-factor-authentication"></a>Exigir que todos os utilizadores se registem para autenticação multi-factor Azure

Ativar esta política exigirá que todos os utilizadores se registem para autenticação multi-factor Azure no prazo de 14 dias. 

1. Inicie sessão no **portal do Azure**.
1. Clique em **Todos os serviços** e, em seguida, procure **Azure AD Identity Protection**.
1. Clique em **Registo na MFA**.
1. Em **Atribuições,** selecione **Utilizadores**.
   1. Em **Incluir,** selecione **Todos os utilizadores**.
   1. Em **Excluir**, **selecione Selecione utilizadores excluídos,** escolha as contas de acesso de emergência ou break-glass da sua organização e selecione **Select**. 
   1. Selecione **Done** (Concluído).
1. Definir a política de **aplicação** para **ligado**.
1. Clique em **Guardar**.

## <a name="require-a-password-change-high-risk-users"></a>Exigir uma alteração de senha utilizadores de alto risco

A Microsoft trabalha com investigadores, entidades responsáveis pela aplicação da lei, várias equipas de segurança da Microsoft e outras origens fidedignas para localizar os pares de nome de utilizador e palavra-passe. Quando um destes pares corresponde a uma conta no seu ambiente, pode ser acionada uma alteração de palavra-passe baseada em risco, com a política seguinte.

1. Inicie sessão no **portal do Azure**.
1. Clique em **Todos os serviços** e, em seguida, procure **Azure AD Identity Protection**.
1. Clique na política de **risco do Utilizador**.
1. Em **Atribuições**, selecione **Utilizadores**
   1. Em **Incluir,** selecione **Todos os utilizadores**.
   1. Em **Excluir**, **selecione Selecione utilizadores excluídos,** escolha as contas de acesso de emergência ou break-glass da sua organização e selecione **Select**.
   1. Selecione **Done** (Concluído).
1. Em **Condições,** selecione **o risco do utilizador**e, em seguida, escolha **High**.
   1. Clique em **Selecionar** e depois **Feito**.
1. No acesso **dos controlos,**  >  **Access**escolha permitir **o acesso**e, em seguida, selecione Exigir alteração **de palavra-passe**.
   1. Clique em **Selecionar**.
1. Definir a política de **aplicação** para **ligado**.
1. Clique em **Guardar**.

## <a name="require-mfa-medium-or-high-sign-in-risk-users"></a>Exigir utilizadores de risco de inscrição média ou alta de MFA

A maioria dos utilizadores tem um comportamento normal que pode ser controlado. Quando fugirem a esta norma, pode ser arriscado permitir-lhes iniciar sessão. Pode querer bloquear esse utilizador ou talvez pedir-lhes que realizem a autenticação de vários fatores para provar que são realmente quem dizem ser. Para ativar uma política que exija a MFA quando é detetado um risco de início de sessão, ative a política seguinte.

1. Inicie sessão no **portal do Azure**.
1. Clique em **Todos os serviços** e, em seguida, procure **Azure AD Identity Protection**.
1. Clique na **política de risco de iniciar sessão**
1. Em **Atribuições**, selecione **Utilizadores**
   1. Em **Incluir,** selecione **Todos os utilizadores**.
   1. Em **Excluir**, **selecione Selecione utilizadores excluídos,** escolha as contas de acesso de emergência ou break-glass da sua organização e selecione **Select**.
   1. Selecione **Done** (Concluído).
1. Em **Condições**, selecione **o risco de iniciar sessão**e, em seguida, escolha Médio e **superior**.
   1. Clique em **Selecionar** e depois **Feito**.
1. No acesso **dos controlos,**  >  **Access**escolha permitir **o acesso**e, em seguida, selecione **Exigir autenticação de vários fatores**.
   1. Clique em **Selecionar**.
1. Definir a política de **aplicação** para **ligado**.
1. Clique em **Guardar**.

## <a name="next-steps"></a>Próximos passos

[Políticas comuns de acesso condicional](concept-conditional-access-policy-common.md)

[Determine o impacto utilizando o modo apenas de relatório de acesso condicional](howto-conditional-access-report-only.md)

[Simular o sinal de comportamento usando a ferramenta de acesso condicional O que se a ferramenta](troubleshoot-conditional-access-what-if.md)

[Como funciona: Multi-Factor Authentication do Azure](../authentication/concept-mfa-howitworks.md)

[O que é o Azure Active Directory Identity Protection?](../identity-protection/overview.md)
