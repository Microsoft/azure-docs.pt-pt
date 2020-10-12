---
title: Use a análise de comportamento da entidade para detetar ameaças avançadas Microsoft Docs
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
ms.date: 09/15/2020
ms.author: yelevin
ms.openlocfilehash: c55ea0e7753faa6dc21b955d63a57d96e3849f70
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90997116"
---
# <a name="enable-user-and-entity-behavior-analytics-ueba-in-azure-sentinel"></a>Ativar o Comportamento do Utilizador e da Entidade Analytics (UEBA) em Azure Sentinel 



## <a name="prerequisites"></a>Pré-requisitos

- O seu utilizador deve ser atribuído às funções **de Administrador Global** ou Administrador de **Segurança** em AZure AD, a fim de permitir ou desativar a UEBA, mas não executá-la.

## <a name="how-to-enable-user-and-entity-behavior-analytics"></a>Como ativar a análise de comportamento do utilizador e da entidade

1. A partir do menu de navegação Azure Sentinel, selecione **Comportamento da Entidade (pré-visualização)**.

1. Sob o título **Ligue-o,** ligue o toggle para **On**.

1. Clique no botão **Selecionar fontes de dados.**

1. No painel **de seleção** de fontes de dados, marque as caixas de verificação ao lado das fontes de dados nas quais pretende ativar a UEBA e, em seguida, selecione **Apply**.

    > [!NOTE]
    >
    > Na metade inferior do painel de seleção de fontes de **dados,** verá uma lista de fontes de dados apoiadas pela UEBA que ainda não ativou. 
    >
    > Uma vez ativado a UEBA, terá a opção, ao ligar novas fontes de dados, de as ativar diretamente a partir do painel de conector de dados, se forem capazes de o UEBA.

1. Selecione **Vá à pesquisa de entidades**. Isto irá levá-lo ao painel de pesquisa da entidade, que a partir de agora será o que você vê quando você escolher **o comportamento** da Entidade a partir do menu principal Azure Sentinel.

## <a name="next-steps"></a>Passos seguintes
Neste documento, aprendeu a ativar e configurar a Análise de Comportamento do Utilizador e entidade (UEBA) em Azure Sentinel. Para saber mais sobre Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade nos seus dados e potenciais ameaças.](quickstart-get-visibility.md)
- Começa [a detetar ameaças com o Azure Sentinel.](tutorial-detect-threats-built-in.md)
