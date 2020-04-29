---
title: Ligue os dados da AD Azure ao Azure Sentinel Microsoft Docs
description: Saiba como ligar dados do Diretório Ativo Azure ao Azure Sentinel.
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
ms.openlocfilehash: be9241a6156621d3f90dbab2da5bebeb463b4232
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77588625"
---
# <a name="connect-data-from-azure-active-directory"></a>Conectar dados do Diretório Ativo azure



O Azure Sentinel permite-lhe recolher dados do [Azure Ative Directory](../active-directory/fundamentals/active-directory-whatis.md) e transmiti-lo para o Azure Sentinel. Pode optar por transmitir [registos de login](../active-directory/reports-monitoring/concept-sign-ins.md) e [registos](../active-directory/reports-monitoring/concept-audit-logs.md) de auditoria .

## <a name="prerequisites"></a>Pré-requisitos

- Se pretender exportar dados de inscrição a partir do Ative Directory, deve ter uma licença Azure AD P1 ou P2.

- Utilizador com permissões de administração ou administração de segurança globais no inquilino que deseja transmitir os registos de.

- Para poder ver o estado de ligação, deve ter permissão para aceder aos registos de diagnóstico da AD Azure. 


## <a name="connect-to-azure-ad"></a>Ligar ao Azure AD

1. No Azure Sentinel, selecione **conectores de Dados** e, em seguida, clique no azulejo **Azure Ative Directory.**

1. Ao lado dos registos que pretende transmitir para O Sentinel a dispor, clique em **Connect**.

1. Pode selecionar se pretende que os alertas da AD Azure gerem automaticamente incidentes em Azure Sentinel. Em caso **de criar incidentes,** selecione **Ativar** para ativar a regra analítica padrão que cria incidentes automaticamente a partir de alertas gerados no serviço de segurança conectado. Em seguida, pode editar esta regra de acordo com **as regras** **Analytics** e, em seguida, Ative .

1. Para utilizar o esquema relevante no Log Analytics para os alertas Da AD Azure, procure **SigninLogs** e **AuditLogs**.




## <a name="next-steps"></a>Passos seguintes
Neste documento, aprendeu a ligar a Azure AD ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Aprenda a [obter visibilidade nos seus dados e ameaças potenciais.](quickstart-get-visibility.md)
- Começar [a detetar ameaças com o Azure Sentinel.](tutorial-detect-threats-built-in.md)
