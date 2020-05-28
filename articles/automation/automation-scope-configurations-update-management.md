---
title: Limite âmbito de implementação de gestão de atualização de automatização azure
description: Este artigo diz como usar configurações de âmbito para limitar o âmbito de uma implementação de Gestão de Atualizações.
services: automation
ms.date: 03/04/2020
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 72065b388f348da1d268f875a10d5b13d2f8cf3b
ms.sourcegitcommit: 6a9f01bbef4b442d474747773b2ae6ce7c428c1f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84117393"
---
# <a name="limit-update-management-deployment-scope"></a>Limite âmbito de implementação de gestão de atualização

Este artigo descreve como trabalhar com configurações de âmbito ao utilizar a funcionalidade ['Gestão de Actualizações'](automation-update-management.md) para implementar atualizações e patches para os seus VMs. Para mais informações, consulte soluções de monitorização de [segmentação no Monitor Azure (Pré-visualização)](https://docs.microsoft.com/azure/azure-monitor/insights/solution-targeting). 

## <a name="about-scope-configurations"></a>Sobre configurações de âmbito

Uma configuração de âmbito é um grupo de uma ou mais pesquisas guardadas (consultas) usadas para limitar o âmbito de Gestão de Atualizações a computadores específicos. A configuração de âmbito é utilizada dentro do espaço de trabalho log Analytics para direcionar os computadores para ativar. Quando adiciona um computador para receber atualizações da Atualização, o computador também é adicionado a uma pesquisa guardada no espaço de trabalho.

## <a name="set-the-scope-limit"></a>Definir o limite de âmbito

Para limitar o âmbito de aplicação da sua implementação de Gestão de Atualizações:

1. Na sua conta de Automação, selecione **Linked Workspace** sob **recursos relacionados.**

2. Clique **em ir para o espaço de trabalho**.

3. Selecione Configurações de **âmbito (pré-visualização)** em **fontes**de dados do espaço de trabalho .

4. Selecione a elipse à direita da configuração de `MicrosoftDefaultScopeConfig-Updates` âmbito e clique em **Editar**. 

5. No painel de edição, expanda **Select Computer Groups**. O painel de Grupos de Computadores mostra as pesquisas guardadas que são usadas para criar a configuração de âmbito. A pesquisa guardada utilizada pela Atualização gestão é:

    |Name     |Categoria  |Alias  |
    |---------|---------|---------|
    |MicrosoftDefaultComputerGroup     | Atualizações        | Updates__MicrosoftDefaultComputerGroup         |

6. Selecione a pesquisa guardada para visualizar e editar a consulta usada para povoar o grupo. A imagem seguinte mostra a consulta e os seus resultados:

    ![Pesquisas guardadas](media/automation-scope-configurations-update-management/logsearch.png)

## <a name="next-steps"></a>Próximos passos

* Para trabalhar com a funcionalidade, consulte ['Gerir atualizações e patches' para os seus VMs Azure](automation-tutorial-update-management.md).
* Para resolver erros de funcionalidades, consulte problemas de Gestão de Atualização de Resolução de [Problemas](troubleshoot/update-management.md).
* Para resolver os erros do agente de atualização do Windows, consulte problemas de agente de [atualização do Windows](troubleshoot/update-agent-issues.md).
* Para resolver os erros do agente de atualização do Linux, consulte problemas de agente de [atualização Da Troubleshoot Linux](troubleshoot/update-agent-issues-linux.md).