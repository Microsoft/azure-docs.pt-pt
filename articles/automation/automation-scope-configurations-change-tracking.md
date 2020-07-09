---
title: Limite Azure Automation Change Tracking and Inventory Deployment Scope
description: Este artigo diz como trabalhar com configurações de âmbito para limitar o âmbito de uma implementação de Change Tracking e Inventory.
services: automation
ms.date: 03/04/2020
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 49655d11858086b16099a1864fd4d2dc5988f02a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84117432"
---
# <a name="limit-change-tracking-and-inventory-deployment-scope"></a>Limite de rastreio de alteração e implementação de inventário

Este artigo descreve como trabalhar com configurações de âmbito quando se utiliza a função [de Rastreio e Inventário de Alterações](change-tracking.md) para implementar alterações nos seus VMs. Para obter mais informações, consulte [soluções de monitorização de direcionamento no Azure Monitor (Preview)](https://docs.microsoft.com/azure/azure-monitor/insights/solution-targeting). 

## <a name="about-scope-configurations"></a>Sobre configurações de âmbito

Uma configuração de âmbito é um grupo de uma ou mais pesquisas guardadas (consultas) usadas para limitar o âmbito de rastreio e inventário de alterações a computadores específicos. A configuração de âmbito é utilizada dentro do espaço de trabalho Do Log Analytics para direcionar os computadores para ativar. Quando adiciona um computador às alterações da funcionalidade, o computador também é adicionado a uma pesquisa guardada no espaço de trabalho.

## <a name="set-the-scope-limit"></a>Definir o limite de âmbito

Para limitar o âmbito de aplicação da sua implementação de 'Change Tracking and Inventory':

1. Na sua conta Automation, selecione **Linked Workspace** em **recursos relacionados.**

2. Clique **em Ir ao espaço de trabalho.**

3. Selecione **Configurações de âmbito (pré-visualização)** em **Fontes de Dados do Espaço de Trabalho**.

4. Selecione a elipse à direita da configuração do `MicrosoftDefaultScopeConfig-ChangeTracking` âmbito e clique em **Editar**. 

5. No painel de edição, **selecione Select Computer Groups**. O painel de grupos de computador mostra as pesquisas guardadas que são usadas para criar a configuração do âmbito. A pesquisa guardada utilizada por Change Tracking and Inventory é:

    |Name     |Categoria  |Alias  |
    |---------|---------|---------|
    |MicrosoftDefaultComputerGroup     |  ChangeTracking       | ChangeTracking__MicrosoftDefaultComputerGroup        |

6. Selecione a procura guardada para visualizar e editar a consulta utilizada para povoar o grupo. A imagem a seguir mostra a consulta e os seus resultados:

    ![Pesquisas guardadas](media/automation-scope-configurations-change-tracking/logsearch.png)

## <a name="next-steps"></a>Próximos passos

* Para trabalhar com o Change Tracking and Inventory, consulte [Gerir o Tracking e o Inventário de Mudanças.](change-tracking-file-contents.md)
* Para resolver problemas gerais de recursos, consulte [problemas de rastreio e inventário de alterações de resolução de problemas](troubleshoot/change-tracking.md).