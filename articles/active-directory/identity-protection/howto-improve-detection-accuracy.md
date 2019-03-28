---
title: Como melhorar a precisão de deteção no Azure Active Directory Identity Protection (atualizadas) | Documentos da Microsoft
description: Como melhorar a precisão de deteção no Azure Active Directory Identity Protection (atualizados).
services: active-directory
keywords: proteção de identidade do Azure Active Directory, descoberta de aplicações na cloud, gestão de aplicações, a segurança, a risco, a nível de risco, a vulnerabilidade, a política de segurança
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: mtillman
ms.assetid: e7434eeb-4e98-4b6b-a895-b5598a6cccf1
ms.service: active-directory
ms.subservice: identity-protection
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/31/2019
ms.author: joflore
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7724d69a9294b420ca061d5ad26ad64826372203
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2019
ms.locfileid: "58517679"
---
# <a name="how-to-improve-the-detection-accuracy"></a>Como: Melhorar a exatidão da deteção 

Identity Protection fornece mecanismos para enviar comentários para o Azure AD nas deteções de risco no seu ambiente. Para enviar comentários, pode confirmar o estado de utilizador de risco detetado ou evento de início de sessão. Os utilizadores do Microsoft seus comentários para tomar medidas em deteções de risco atual e melhorar a precisão de deteções futuras. 


## <a name="what-is-detection"></a>O que é detecção?

A deteção é o processo de identificar atividades suspeitas em conjunto com as contas de utilizador. As atividades suspeitas do Azure AD pode detetar são chamadas [evento de risco](../reports-monitoring/concept-risk-events.md). O processo de deteção baseia-se de machine learning e heurística para detetar eventos de risco para os utilizadores de algoritmos.

Os resultados de deteção são utilizados para determinar se os utilizadores e inícios de sessão estão em risco. 


## <a name="how-can-i-improve-the-detection-accuracy"></a>Como posso melhorar a precisão de deteção?

Uma vez que a deteção é um processo automatizado, é possível que o Azure AD reporta falsos positivos. Pode melhorar a precisão de deteção, fornecendo comentários ao Azure AD sobre os resultados de deteção.

Existem três formas de aumentar a exatidão da deteção: Confirme comprometido início de sessão, comfirm seguro início de sessão e descartar o risco de utilizador. Pode fazê-lo dos seguintes relatórios:

- **Relatório de inícios de sessão arriscados -** no relatório de inícios de sessão de risco, pode confirmar que os inícios de sessão estão seguros ou comprometido

- **Relatório de utilizadores de risco -** no relatório de utilizadores de risco, pode ignorar o risco de utilizador 

Seus comentários é processado pelo Azure AD para melhorar a precisão dos resultados da deteção. Normalmente, fornecer comentários como parte de um risco de utilizador ou a investigação de risco de início de sessão. Para obter mais informações, consulte [como investigar a utilizadores de risco e inícios de sessão](howto-investigate-risky-users-signins.md).


## <a name="confirm-compromised"></a>Confirmar comprometido

Confirmar um evento de início de sessão como comprometido sinaliza para o Azure AD, que o início de sessão não foi autorizado pelo proprietário de identidade. Ao selecionar "Confirmar comprometido", do Azure AD irá

- Aumente o risco de utilizador do utilizador afetado como alto.

- Ajudar a otimizar o machine learning que Deteta eventos de risco
 
- Executar medidas adicionais para proteger ainda mais a sua organização



Para confirmar um comprometido início de sessão:

- **O relatório de inícios de sessão arriscados** -esta opção permite-lhe confirmar um comprometido início de sessão para um ou mais início de sessão eventos.

    ![Dispensar o risco de utilizador](./media/howto-improve-detection-accuracy/07.png)

- **A vista de detalhes do relatório de inícios de sessão arriscados** -esta opção permite-lhe confirmar uma conta comprometida para o evento selecionado início de sessão no relatório de inícios de sessão de risco. 

    ![Dispensar o risco de utilizador](./media/howto-improve-detection-accuracy/04.png)


 
## <a name="confirm-safe"></a>Confirmar seguro


Confirmar um evento de início de sessão como sinais seguros para o Azure AD que o início de sessão **foi** autorizada pelo proprietário do respectivo de identidade. Ao selecionar "Confirmar seguro", do Azure AD irá:

- Reverter a contribuição de risco de utilizador de inícios de sessão selecionados

- Fechar eventos de risco subjacentes

- Ajudar a otimizar o machine learning que Deteta eventos de risco

- Executar medidas adicionais para proteger ainda mais a sua organização
 

Para confirmar um seguro início de sessão em:

- **O relatório de inícios de sessão arriscados** -esta opção permite-lhe confirmar um seguro início de sessão para um ou mais início de sessão eventos.

    ![Dispensar o risco de utilizador](./media/howto-improve-detection-accuracy/08.png)

- **A vista de detalhes do relatório de inícios de sessão arriscados** -esta opção permite-lhe confirmar um seguro início de sessão para o evento selecionado início de sessão no relatório de inícios de sessão de risco. 

    ![Dispensar o risco de utilizador](./media/howto-improve-detection-accuracy/05.png)




## <a name="dismiss-user-risk"></a>Dispensar o risco de utilizador

Se já efetuou ações de remediação para um utilizador de risco, ou se considerar que incorretamente foram marcados como perigoso, que pode ignorar o risco de um utilizador. A dispensar o risco de um utilizador restaura o utilizador para um Estado não risco. Todas as anteriores inícios de sessão de risco e o risco, eventos para o utilizador selecionado serão ser descartados.


Pode ignorar o risco de utilizador comunicadas em:

- **O relatório de utilizadores de risco** – esta opção permite-lhe a ignorar o risco de utilizador para um ou mais utilizadores selecionados.

    ![Dispensar o risco de utilizador](./media/howto-improve-detection-accuracy/02.png)

- **A vista de detalhes** -esta opção permite-lhe descartar o risco de utilizador para o utilizador selecionado no relatório de risco do utilizador. 

    ![Dispensar o risco de utilizador](./media/howto-improve-detection-accuracy/01.png)


**O que deve saber:**

- Não é possível reverter esta ação.

- Pode demorar alguns minutos para que esta ação seja concluída, por isso, não deverá submeter novamente o pedido.

- Só pode efetuar esta ação se AD gere as credenciais do utilizador. 



## <a name="best-practices"></a>Melhores práticas

A dispensar o risco de um utilizador é uma forma de desbloqueá-los, se o ter sido bloqueados pela política de risco de utilizador e não é possível auto-remediar devido a não ter a reposição de palavra-passe e/ou MFA ativada. Nesta situação, é melhor garantir que o utilizador, então, registra para obter a MFA e de reposição de palavra-passe, para que sejam capazes de Self-remediar quaisquer eventos de risco futuras.


## <a name="next-steps"></a>Passos Seguintes

Para obter uma visão geral do Azure AD Identity Protection, consulte a [descrição geral do Azure AD Identity Protection](overview-v2.md).


