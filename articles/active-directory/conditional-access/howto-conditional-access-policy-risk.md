---
title: Acesso Condicional baseado no risco de acesso - Diretório Ativo Azure
description: Criar políticas de acesso condicional usando o risco de acesso à proteção de identidade
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
ms.openlocfilehash: a09c4513206bea3462577ecba49b5d77b655b0e0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "91628269"
---
# <a name="conditional-access-sign-in-risk-based-conditional-access"></a>Acesso Condicional: Acesso condicional baseado no risco de inscrição

A maioria dos utilizadores tem um comportamento normal que pode ser controlado. Quando fugirem a esta norma, pode ser arriscado permitir-lhes iniciar sessão. Pode querer bloquear esse utilizador ou talvez apenas pedir-lhes para realizar a autenticação de vários fatores para provar que são realmente quem dizem ser. 

Um risco de entrada representa a probabilidade de um dado pedido de autenticação não ser autorizado pelo proprietário da identidade. As organizações com licenças Azure AD Premium P2 podem criar políticas de acesso condicional que incorporam [deteções de risco de acesso à identidade Azure AD.](../identity-protection/concept-identity-protection-risks.md#sign-in-risk)

Existem dois locais onde esta apólice pode ser atribuída. As organizações devem escolher uma das seguintes opções para permitir uma política de acesso condicional baseada no risco de entrada que exija uma alteração segura da palavra-passe.

## <a name="enable-with-conditional-access-policy"></a>Ativar com política de acesso condicional

1. Inscreva-se no **portal Azure** como administrador global, administrador de segurança ou administrador de acesso condicional.
1. Navegue pelo Acesso Condicional de Segurança **do Diretório Ativo Azure**  >    >  .
1. Selecione **Nova política**.
1. Dê um nome à sua política. Recomendamos que as organizações criem um padrão significativo para os nomes das suas políticas.
1. Em **Atribuições**, selecione **Utilizadores e grupos**.
   1. Em **Incluir**, selecione **Todos os utilizadores**.
   1. Em **'Excluir',** selecione **Utilizadores e grupos** e escolha as contas de acesso de emergência ou break-glass da sua organização. 
   1. Selecione **Concluído**.
1. No **âmbito de aplicações ou ações cloud**  >  **Inclua**, selecione todas as **aplicações em nuvem**.
1. Em **Condições**  >  **de risco de inscrição,** definir **Configurar** para **Sim**. Em **Selecione o nível de risco de inscrição esta política aplicar-se-á a** 
   1. Selecione **Alto** e **Médio**.
   1. Selecione **Concluído**.
1. Sob **controlos de acesso**  >  **Grant**, selecione Grant **access**, **Require multi-factor authentication**, e selecione **Select**.
1. Confirme as suas definições e defina **Ativar** a política para **on**.
1. Selecione **Criar** para criar para ativar a sua política.

## <a name="enable-through-identity-protection"></a>Ativar através da Proteção de Identidade

1. Inicie sessão no **portal do Azure**.
1. Selecione **Todos os serviços** e, em seguida, navegue **para Azure AD Identity Protection**.
1. Selecione **a política de risco de inscrição**.
1. Em **Atribuições**, selecione **Utilizadores.**
   1. Em **Incluir**, selecione **Todos os utilizadores**.
   1. Em **'Excluir'**( **Selecione Selecionar utilizadores excluídos),** escolha o acesso de emergência da sua organização ou contas de break-glass e selecione **Select**.
   1. Selecione **Concluído**.
1. Em **Condições**, selecione **o risco de inscrição,** em seguida, escolha Médio e **superior**.
   1. **Selecione Selecione** e, em **seguida, Feito**.
1. No **Acesso ao**  >  **Controlo**, escolha **Permitir o acesso** e, em seguida, selecione **Requera a autenticação multi-factor**.
   1. Selecione **Selecionar**.
1. Definir **a política de execução** para **on**.
1. Selecione **Guardar**.

## <a name="next-steps"></a>Passos seguintes

[Políticas comuns de acesso condicional](concept-conditional-access-policy-common.md)

[Acesso Condicional baseado no risco do utilizador](howto-conditional-access-policy-risk-user.md)

[Determinar o impacto utilizando o modo de relatório de acesso condicional](howto-conditional-access-insights-reporting.md)

[Simular sinal no comportamento usando o acesso condicional E se a ferramenta](troubleshoot-conditional-access-what-if.md)

[O que é o Azure Active Directory Identity Protection?](../identity-protection/overview-identity-protection.md)
