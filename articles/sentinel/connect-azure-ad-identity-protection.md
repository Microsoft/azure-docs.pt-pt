---
title: Ligue os dados de Proteção de Identidade Azure AD ao Azure Sentinel
description: Saiba como transmitir registos e alertas da Azure AD Identity Protection para o Azure Sentinel para ver dashboards, criar alertas personalizados e melhorar a investigação.
author: yelevin
manager: rkarlin
ms.assetid: 91c870e5-2669-437f-9896-ee6c7fe1d51d
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: how-to
ms.date: 06/24/2020
ms.author: yelevin
ms.openlocfilehash: f420e80f10072c440f5401c042d6370a061b1a7f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98632246"
---
# <a name="connect-data-from-azure-active-directory-azure-ad-identity-protection"></a>Ligar dados do Azure Ative Directory (Azure AD) Proteção de Identidade

Pode transmitir registos da [Azure AD Identity Protection](../active-directory/identity-protection/overview-identity-protection.md) para o Azure Sentinel para transmitir alertas no Azure Sentinel para visualizar dashboards, criar alertas personalizados e melhorar a investigação. A Azure Ative Directory Identity Protection fornece uma visão consolidada dos utilizadores de risco, deteções de riscos e vulnerabilidades, com a capacidade de remediar o risco imediatamente, e definir políticas para remediar automaticamente eventos futuros. O serviço baseia-se na experiência da Microsoft em proteger as identidades dos consumidores e obtém uma precisão tremenda do sinal de mais de 13 mil milhões de logins por dia. 

## <a name="prerequisites"></a>Pré-requisitos

- Você deve ter uma [assinatura Azure AD Premium P2](https://azure.microsoft.com/pricing/details/active-directory/).
- Deve ter um utilizador com permissões globais de administrador ou administrador de segurança.

## <a name="connect-to-azure-ad-identity-protection"></a>Ligue-se à Proteção de Identidade AD AZure

Se tiver uma subscrição Azure AD Premium P2, a Azure AD Identity Protection está incluída. Se alguma [política estiver ativada](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md) e gerar alertas, os dados de alerta podem ser facilmente transmitidos para Azure Sentinel.

1. Em Azure Sentinel, selecione **os conectores de dados** e, em seguida, clique no azulejo **Azure AD Identity Protection.**

1. Clique **em Connect** para começar a transmitir eventos de Proteção de Identidade Azure AD em Azure Sentinel.

1. Para utilizar o esquema relevante no Log Analytics para os alertas de Proteção de Identidade AZure AD, procure por **SecurityAlert**.

Se quiser testar o conector, pode [simular deteções](../active-directory/identity-protection/howto-identity-protection-simulate-risk.md) para gerar alertas de amostras que serão transmitidos para Azure Sentinel.

## <a name="next-steps"></a>Passos seguintes

Neste documento, aprendeu a ligar a Azure AD Identity Protection ao Azure Sentinel. Para saber mais sobre Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade nos seus dados e potenciais ameaças.](quickstart-get-visibility.md)
- Começa [a detetar ameaças com o Azure Sentinel.](tutorial-detect-threats-built-in.md)
