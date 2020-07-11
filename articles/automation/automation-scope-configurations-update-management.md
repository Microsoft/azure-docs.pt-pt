---
title: Limite o âmbito de implementação da atualização de atualização de automação do Azure
description: Este artigo diz como usar configurações de âmbito para limitar o âmbito de uma implementação de Gestão de Atualização.
services: automation
ms.date: 03/04/2020
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 8770762fa2d2ae6bc0584d75397829298a62e8c0
ms.sourcegitcommit: ec682dcc0a67eabe4bfe242fce4a7019f0a8c405
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/09/2020
ms.locfileid: "86185896"
---
# <a name="limit-update-management-deployment-scope"></a>Limite de implementação de gestão de atualização

Este artigo descreve como trabalhar com configurações de âmbito quando se utiliza a função [de Gestão](automation-update-management.md) de Atualização para implementar atualizações e patches nos seus VMs. Para obter mais informações, consulte [soluções de monitorização de direcionamento no Azure Monitor (Preview)](../azure-monitor/insights/solution-targeting.md). 

## <a name="about-scope-configurations"></a>Sobre configurações de âmbito

Uma configuração de âmbito é um grupo de uma ou mais pesquisas guardadas (consultas) usadas para limitar o âmbito de Gestão de Atualização a computadores específicos. A configuração de âmbito é utilizada dentro do espaço de trabalho Do Log Analytics para direcionar os computadores para ativar. Quando adiciona um computador para receber atualizações da Atualização Management, o computador também é adicionado a uma pesquisa guardada no espaço de trabalho.

## <a name="set-the-scope-limit"></a>Definir o limite de âmbito

Para limitar o âmbito de aplicação da sua gestão de atualização:

1. Na sua conta Automation, selecione **Linked Workspace** em **recursos relacionados.**

2. Clique **em Ir ao espaço de trabalho.**

3. Selecione **Configurações de âmbito (pré-visualização)** em **Fontes de Dados do Espaço de Trabalho**.

4. Selecione a elipse à direita da configuração do `MicrosoftDefaultScopeConfig-Updates` âmbito e clique em **Editar**. 

5. No painel de edição, **expanda os grupos de computador selecionados.** O painel de grupos de computador mostra as pesquisas guardadas que são usadas para criar a configuração do âmbito. A pesquisa guardada utilizada pela Update Management é:

    |Nome     |Categoria  |Alias  |
    |---------|---------|---------|
    |MicrosoftDefaultComputerGroup     | Atualizações        | Updates__MicrosoftDefaultComputerGroup         |

6. Selecione a procura guardada para visualizar e editar a consulta utilizada para povoar o grupo. A imagem a seguir mostra a consulta e os seus resultados:

    ![Pesquisas guardadas](media/automation-scope-configurations-update-management/logsearch.png)

## <a name="next-steps"></a>Passos seguintes

* Para trabalhar com a funcionalidade, consulte [Gerir atualizações e patches para os seus VMs Azure](automation-tutorial-update-management.md).
* Para resolver erros de funcionalidades, consulte [problemas de Gestão de Atualização de Resolução de Problemas](troubleshoot/update-management.md).
* Para resolver os erros do agente de atualização do Windows, consulte [problemas de agente de atualização do Windows de resolução de resolução](troubleshoot/update-agent-issues.md)de problemas .
* Para resolver os erros do agente de atualização linux, consulte [problemas de agente de atualização do Linux de resolução](troubleshoot/update-agent-issues-linux.md)de resolução de problemas .
