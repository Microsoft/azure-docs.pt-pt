---
title: Use análise de comportamento de entidade para detetar ameaças avançadas | Microsoft Docs
description: Ativar o Comportamento do Utilizador e da Entidade Analytics em Azure Sentinel, e configurar fontes de dados
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/25/2021
ms.author: yelevin
ms.openlocfilehash: 0dccd8ac4f852688bf7e59e7ed96c4458c08d18b
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/26/2021
ms.locfileid: "98784733"
---
# <a name="enable-user-and-entity-behavior-analytics-ueba-in-azure-sentinel"></a>Ativar o Comportamento do Utilizador e da Entidade Analytics (UEBA) em Azure Sentinel 

> [!IMPORTANT]
>
> - As funcionalidades ueba e páginas de entidades estão agora em **Disponibilidade Geral** em **_todas __* geografias e regiões do Azure Sentinel.

## <a name="prerequisites"></a>Pré-requisitos

Para ativar ou desativar esta função (estes pré-requisitos não são necessários para utilizar a funcionalidade):

- O seu utilizador deve ser membro do Azure Ative Directory da sua organização e não um utilizador convidado.

- O seu utilizador deve ser atribuído às funções de *Administrador Global** ou Administrador **de Segurança** em Azure AD.

- O seu utilizador deve ser atribuído pelo menos a uma das **seguintes funções Azure** [(Saiba mais sobre a Azure RBAC](roles.md)):
    - **Azure Sentinel Contributor** nos níveis de espaço de trabalho ou grupo de recursos.
    - **Log Analytics Contributor** nos níveis de grupo de recursos ou subscrição.

- O seu espaço de trabalho não deve ter quaisquer bloqueios de recursos Azure aplicados a ele. [Saiba mais sobre o bloqueio de recursos Azure](../azure-resource-manager/management/lock-resources.md).

## <a name="how-to-enable-user-and-entity-behavior-analytics"></a>Como ativar a análise de comportamento do utilizador e da entidade

1. A partir do menu de navegação Azure Sentinel, selecione **Comportamento da Entidade**.

1. Sob o título **Ligue-o,** ligue o toggle para **On**.

1. Clique no botão **Selecionar fontes de dados.**

1. No painel **de seleção** de fontes de dados, marque as caixas de verificação ao lado das fontes de dados nas quais pretende ativar a UEBA e, em seguida, selecione **Apply**.

    > [!NOTE]
    >
    > Na metade inferior do painel de seleção de fontes de **dados,** verá uma lista de fontes de dados apoiadas pela UEBA que ainda não ativou. 
    >
    > Uma vez ativado a UEBA, terá a opção, ao ligar novas fontes de dados, de as ativar diretamente a partir do painel de conector de dados, se forem capazes de o UEBA.

1. Selecione **Vá à pesquisa de entidades**. Isto irá levá-lo ao painel de pesquisa da entidade, que a partir de agora será o que você vê quando você escolher **o comportamento** da Entidade a partir do menu principal Azure Sentinel.

## <a name="next-steps"></a>Próximos passos
Neste documento, aprendeu a ativar e configurar a Análise de Comportamento do Utilizador e entidade (UEBA) em Azure Sentinel. Para saber mais sobre Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade nos seus dados e potenciais ameaças.](quickstart-get-visibility.md)
- Começa [a detetar ameaças com o Azure Sentinel.](tutorial-detect-threats-built-in.md)
