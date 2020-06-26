---
title: Ligue os dados do Azure Ative Directory ao Azure Sentinel Microsoft Docs
description: Saiba como ligar os dados do Azure Ative Directory ao Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 0a8f4a58-e96a-4883-adf3-6b8b49208e6a
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: yelevin
ms.openlocfilehash: 5ec8a37a0c782a5426b87f785af2d047ca35aa22
ms.sourcegitcommit: dfa5f7f7d2881a37572160a70bac8ed1e03990ad
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/25/2020
ms.locfileid: "85374819"
---
# <a name="connect-data-from-azure-active-directory-azure-ad"></a>Ligar dados do Azure Ative Directory (Azure AD)



O Azure Sentinel permite-lhe recolher dados do [Azure Ative Directory](../active-directory/fundamentals/active-directory-whatis.md) e transmiti-lo para o Azure Sentinel. Pode optar por transmitir [registos de inscrição](../active-directory/reports-monitoring/concept-sign-ins.md) e [registos](../active-directory/reports-monitoring/concept-audit-logs.md) de auditoria .

## <a name="prerequisites"></a>Pré-requisitos

- Se pretender exportar dados de inscrição a partir da Azure AD, deve ter uma licença Azure AD P1 ou P2.

- Utilizador com permissões de administração ou de administração de segurança globais no inquilino a partir do seguinte.

- Para poder ver o estado da ligação, tem de ter permissão para aceder aos registos de diagnóstico Azure AD. 


## <a name="connect-to-azure-active-directory"></a>Ligue-se ao Diretório Ativo Azure

1. Em Azure Sentinel, selecione **Conectores** de dados do menu de navegação.

1. Na galeria de conectores de dados, selecione **O Diretório Ativo Azure** e, em seguida, clique no botão de página do **conector Open.**

1. Marque as caixas de verificação ao lado dos registos que pretende transmitir para Azure Sentinel e clique em **Connect**.

1. Pode selecionar se deseja que os alertas do Azure AD gerem automaticamente incidentes em Azure Sentinel. No **âmbito do Create incidents** select **Enable** to enable the pred pred alytic rule that creates incidents automaticly from alerts gerados no serviço de segurança conectado. Em seguida, pode editar esta regra sob **a análise** e, em seguida, **as regras Ative**.

1. Para utilizar o esquema relevante no Log Analytics para consulta de alertas Azure AD, tipo `SigninLogs` ou na janela de `AuditLogs` consulta.




## <a name="next-steps"></a>Passos seguintes
Neste documento, aprendeu a ligar o Azure Ative Directory ao Azure Sentinel. Para saber mais sobre Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade nos seus dados e potenciais ameaças.](quickstart-get-visibility.md)
- Começa [a detetar ameaças com o Azure Sentinel.](tutorial-detect-threats-built-in.md)
