---
title: Limite o âmbito de implementação de alterações de automatização e de inventário de automatização do Azure
description: Este artigo diz como trabalhar com configurações de âmbito para limitar o âmbito de uma implementação de Rastreio e Inventário de Alterações.
services: automation
ms.date: 03/04/2020
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 49655d11858086b16099a1864fd4d2dc5988f02a
ms.sourcegitcommit: 6a9f01bbef4b442d474747773b2ae6ce7c428c1f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84117432"
---
# <a name="limit-change-tracking-and-inventory-deployment-scope"></a>Limite de rastreio de alterações e âmbito de implementação de inventário

Este artigo descreve como trabalhar com configurações de âmbito ao utilizar a funcionalidade de Rastreio e Inventário de [Alterações](change-tracking.md) para implementar alterações nos seus VMs. Para mais informações, consulte soluções de monitorização de [segmentação no Monitor Azure (Pré-visualização)](https://docs.microsoft.com/azure/azure-monitor/insights/solution-targeting). 

## <a name="about-scope-configurations"></a>Sobre configurações de âmbito

Uma configuração de âmbito é um grupo de uma ou mais pesquisas guardadas (consultas) usadas para limitar o âmbito de Rastreio de Alterações e Inventário a computadores específicos. A configuração de âmbito é utilizada dentro do espaço de trabalho log Analytics para direcionar os computadores para ativar. Quando adiciona um computador às alterações da funcionalidade, o computador também é adicionado a uma pesquisa guardada no espaço de trabalho.

## <a name="set-the-scope-limit"></a>Definir o limite de âmbito

Para limitar o âmbito para a sua implementação de Rastreio de Alterações e Inventário:

1. Na sua conta de Automação, selecione **Linked Workspace** sob **recursos relacionados.**

2. Clique **em ir para o espaço de trabalho**.

3. Selecione Configurações de **âmbito (pré-visualização)** em **fontes**de dados do espaço de trabalho .

4. Selecione a elipse à direita da configuração de `MicrosoftDefaultScopeConfig-ChangeTracking` âmbito e clique em **Editar**. 

5. No painel de edição, selecione **Select Computer Groups**. O painel de Grupos de Computadores mostra as pesquisas guardadas que são usadas para criar a configuração de âmbito. A pesquisa guardada utilizada pela Change Tracking and Inventory é:

    |Name     |Categoria  |Alias  |
    |---------|---------|---------|
    |MicrosoftDefaultComputerGroup     |  ChangeTracking       | ChangeTracking__MicrosoftDefaultComputerGroup        |

6. Selecione a pesquisa guardada para visualizar e editar a consulta usada para povoar o grupo. A imagem seguinte mostra a consulta e os seus resultados:

    ![Pesquisas guardadas](media/automation-scope-configurations-change-tracking/logsearch.png)

## <a name="next-steps"></a>Próximos passos

* Para trabalhar com o Change Tracking e Inventário, consulte Gerir o Rastreio e O Inventário de [Alterações](change-tracking-file-contents.md).
* Para resolver problemas de funcionalidades gerais, consulte problemas de rastreio e inventário de mudança de resolução de [problemas.](troubleshoot/change-tracking.md)