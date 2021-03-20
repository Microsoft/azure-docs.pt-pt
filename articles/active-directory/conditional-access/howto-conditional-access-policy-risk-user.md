---
title: Acesso Condicional baseado no risco do utilizador - Diretório Ativo Azure
description: Criar políticas de acesso condicional usando o risco do utilizador de proteção de identidade
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 07/02/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: d5423a61c8febee72f32935f3dee4e9f9e868630
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "89049082"
---
# <a name="conditional-access-user-risk-based-conditional-access"></a>Acesso Condicional: Acesso Condicional baseado no risco do utilizador

A Microsoft trabalha com investigadores, aplicação da lei, várias equipas de segurança na Microsoft, e outras fontes fidedignas para encontrar o nome de utilizador vazado e os pares de palavras-passe. Organizações com licenças Azure AD Premium P2 podem criar políticas de acesso condicional incorporando deteções de [risco de proteção de identidade Azure AD.](../identity-protection/concept-identity-protection-risks.md#user-risk) 

Existem dois locais onde esta apólice pode ser atribuída. As organizações devem escolher uma das seguintes opções para permitir uma política de acesso condicional baseada no risco do utilizador que exija uma alteração segura da palavra-passe.

## <a name="enable-with-conditional-access-policy"></a>Ativar com política de acesso condicional

1. Inscreva-se no **portal Azure** como administrador global, administrador de segurança ou administrador de acesso condicional.
1. Navegue pelo Acesso Condicional de Segurança **do Diretório Ativo Azure**  >    >  .
1. Selecione **Nova política**.
1. Dê um nome à sua política. Recomendamos que as organizações criem um padrão significativo para os nomes das suas políticas. Para mais informações, [desa um ajuste de padrões de nomeação para as suas políticas.](./plan-conditional-access.md#set-naming-standards-for-your-policies)
1. Em **Atribuições**, selecione **Utilizadores e grupos**.
   1. Em **Incluir**, selecione **Todos os utilizadores**.
   1. Em **'Excluir',** selecione **Utilizadores e grupos** e escolha as contas de acesso de emergência ou break-glass da sua organização. 
   1. Selecione **Concluído**.
1. No **âmbito de aplicações ou ações cloud**  >  **Inclua**, selecione todas as **aplicações em nuvem**.
1. Em **Condições**  >  **Risco do utilizador**, definir **Configurar** para **Sim**. Ao abrigo **do configure os níveis de risco do utilizador necessários para que a política seja aplicada selecione** **High**, em seguida, selecione **Feito**.
1. Sob **controlos de acesso**  >  **Grant**, selecione **Grant access**, Require password **change**, e selecione **Select**.
1. Confirme as suas definições e defina **Ativar** a política para **on**.
1. Selecione **Criar** para criar para ativar a sua política.

## <a name="enable-through-identity-protection"></a>Ativar através da Proteção de Identidade

1. Inicie sessão no **portal do Azure**.
1. Selecione **Todos os serviços** e, em seguida, navegue **para Azure AD Identity Protection**.
1. Selecione **a política de risco do utilizador**.
1. Em **Atribuições**, selecione **Utilizadores.**
   1. Em **Incluir**, selecione **Todos os utilizadores**.
   1. Em **'Excluir'**( **Selecione Selecionar utilizadores excluídos),** escolha o acesso de emergência da sua organização ou contas de break-glass e selecione **Select**.
   1. Selecione **Concluído**.
1. Em **Condições**, selecione **risco de utilizador,** em seguida, escolha **Alto**.
   1. **Selecione Selecione** e, em **seguida, Feito**.
1. No **Acesso ao**  >  **Controlo**, escolha **Permitir o acesso** e, em seguida, selecione **Requerer a alteração da palavra-passe**.
   1. Selecione **Selecionar**.
1. Definir **a política de execução** para **on**.
1. Selecione **Guardar**.

## <a name="next-steps"></a>Passos seguintes

[Políticas comuns de acesso condicional](concept-conditional-access-policy-common.md)

[Acesso Condicional baseado no risco do início de sessão](howto-conditional-access-policy-risk.md)

[Determinar o impacto utilizando o modo de relatório de acesso condicional](howto-conditional-access-insights-reporting.md)

[Simular sinal no comportamento usando o acesso condicional E se a ferramenta](troubleshoot-conditional-access-what-if.md)

[O que é o Azure Active Directory Identity Protection?](../identity-protection/overview-identity-protection.md)