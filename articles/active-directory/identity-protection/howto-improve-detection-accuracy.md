---
title: Como melhorar a precisão de detecção no Azure Active Directory Identity Protection (atualizado) | Microsoft Docs
description: Como melhorar a precisão de detecção no Azure Active Directory Identity Protection (atualizado).
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 01/31/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 32bb8de7970fc167a6a95e9d9c3c71e4e1dc0150
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/19/2019
ms.locfileid: "68333951"
---
# <a name="how-to-improve-the-detection-accuracy"></a>Como: Melhorar a precisão da detecção 

A proteção de identidade fornece mecanismos para fornecer comentários ao Azure AD sobre as detecções de risco em seu ambiente. Para fornecer comentários, você pode confirmar o status do usuário arriscado ou evento de entrada detectado. Os usuários da Microsoft têm esse feedback para tomar medidas sobre as detecções atuais de riscos e melhorar a precisão das detecções futuras. 

## <a name="what-is-detection"></a>O que é detecção?

Detecção é o processo de identificar atividades suspeitas em conjunto com suas contas de usuário. As atividades suspeitas que o Azure AD pode detectar são chamadas de [evento de risco](../reports-monitoring/concept-risk-events.md). O processo de detecção é baseado em algoritmos de aprendizado de máquina adaptáveis e heurística para detectar eventos de risco para os usuários.

Os resultados da detecção são usados para determinar se os usuários e as entradas estão em risco. 

## <a name="how-can-i-improve-the-detection-accuracy"></a>Como posso melhorar a precisão da detecção?

Como a detecção é um processo automatizado, é possível que o Azure AD relate falsos positivos. Você pode melhorar a precisão da detecção ao fornecer comentários ao Azure AD sobre os resultados da detecção.

Há três maneiras de melhorar a precisão da detecção: Confirme a entrada comprometida, confirme a entrada segura e ignore o risco do usuário. Você pode fazer isso nos seguintes relatórios:

- **Relatório de entradas arriscadas-** No relatório de entradas arriscadas, você pode confirmar se as entradas estão seguras ou comprometidas
- **Relatório de usuários arriscados-** No relatório usuários arriscados, você pode ignorar o risco do usuário 

Seus comentários são processados pelo Azure AD para melhorar a precisão dos resultados da detecção. Normalmente, você fornece comentários como parte de um risco de usuário ou uma investigação de risco de entrada. Para obter mais informações, consulte [como investigar usuários e entradas arriscadas](howto-investigate-risky-users-signins.md).

## <a name="confirm-compromised"></a>Confirmar comprometido

Confirmando um evento de entrada como sinais comprometidos ao Azure AD que a entrada não foi autorizada pelo proprietário da identidade. Quando você selecionar "confirmar comprometido", o Azure AD

- Aumente o risco de usuário do usuário afetado para alta.
- Ajuda para otimizar o aprendizado de máquina que detecta eventos de risco
- Execute medidas adicionais para proteger ainda mais sua organização

Para confirmar uma entrada comprometida:

- **O relatório de entradas arriscadas** -essa opção permite que você confirme uma entrada comprometida para um ou mais eventos de entrada.

   ![Dispensar o risco de utilizador](./media/howto-improve-detection-accuracy/07.png)

- **A exibição de detalhes do relatório de entradas arriscadas** -essa opção permite que você confirme uma conta comprometida para o evento de entrada selecionado no relatório de entradas arriscadas. 

   ![Dispensar o risco de utilizador](./media/howto-improve-detection-accuracy/04.png)
 
## <a name="confirm-safe"></a>Confirmar seguro

Confirmando um evento de logon como um sinal seguro para o Azure AD que a entrada **foi** autorizada pelo respectivo proprietário de identidade. Quando você seleciona "confirmar segurança", o Azure AD irá:

- Reverter a contribuição de risco do usuário das entradas selecionadas
- Fechar os eventos de risco subjacentes
- Ajuda para otimizar o aprendizado de máquina que detecta eventos de risco
- Execute medidas adicionais para proteger ainda mais sua organização
 
Para confirmar uma entrada segura no:

- **O relatório de entradas arriscadas** -essa opção permite que você confirme uma entrada segura para um ou mais eventos de entrada.

   ![Dispensar o risco de utilizador](./media/howto-improve-detection-accuracy/08.png)

- **A exibição de detalhes do relatório de entradas arriscadas** -essa opção permite que você confirme uma entrada segura para o evento de entrada selecionado no relatório de entradas arriscadas. 

   ![Dispensar o risco de utilizador](./media/howto-improve-detection-accuracy/05.png)

## <a name="dismiss-user-risk"></a>Dispensar o risco de utilizador

Se você já executou ações de correção para um usuário de risco ou acredita que elas foram erroneamente marcadas como arriscadas, você pode descartar o risco de um usuário. Ignorar o risco de um usuário restaura o usuário para um estado não arriscado. Todos os eventos de risco e entradas arriscados anteriores para o usuário selecionado serão ignorados.

Você pode ignorar o risco do usuário relatado em:

- **O relatório usuários arriscados** -essa opção permite que você ignore o risco do usuário para um ou mais usuários selecionados.

   ![Dispensar o risco de utilizador](./media/howto-improve-detection-accuracy/02.png)

- **A exibição de detalhes** – essa opção permite que você ignore o risco do usuário selecionado no relatório de risco do usuário. 

   ![Dispensar o risco de utilizador](./media/howto-improve-detection-accuracy/01.png)

**O que você deve saber:**

- Você não pode reverter esta ação.
- Pode levar alguns minutos para que essa ação seja concluída, motivo pelo qual você não deve reenviar sua solicitação.
- Você só poderá executar essa ação se o AD gerenciar as credenciais do usuário. 

## <a name="best-practices"></a>Melhores práticas

Ignorar o risco de um usuário é uma maneira de desbloqueá-los se eles tiverem sido bloqueados pela política de risco do usuário e não puderem ser remediados por não ter a redefinição de senha e/ou a MFA habilitada. Nessa situação, é melhor garantir que o usuário se registre para redefinição de senha e MFA para que eles possam remediar todos os futuros eventos de risco.

## <a name="next-steps"></a>Passos Seguintes

Para obter uma visão geral do Azure AD Identity Protection, consulte a [visão geral Azure ad Identity Protection](overview-v2.md).
