---
title: Limite o âmbito de implementação da atualização de atualização de automação do Azure
description: Este artigo diz como usar configurações de âmbito para limitar o âmbito de uma implementação de Gestão de Atualização.
services: automation
ms.date: 03/04/2020
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: aafd284122ca61ba2b668186095b88003be2775c
ms.sourcegitcommit: cee72954f4467096b01ba287d30074751bcb7ff4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/30/2020
ms.locfileid: "87450372"
---
# <a name="limit-update-management-deployment-scope"></a>Limite de implementação de gestão de atualização

Este artigo descreve como trabalhar com configurações de âmbito quando se utiliza a função [de Gestão](update-mgmt-overview.md) de Atualização para implementar atualizações e patches nos seus VMs. Para obter mais informações, consulte [soluções de monitorização de direcionamento no Azure Monitor (Preview)](../../azure-monitor/insights/solution-targeting.md).

## <a name="about-scope-configurations"></a>Sobre configurações de âmbito

Uma configuração de âmbito é um grupo de uma ou mais pesquisas guardadas (consultas) usadas para limitar o âmbito de Gestão de Atualização a computadores específicos. A configuração de âmbito é utilizada dentro do espaço de trabalho Do Log Analytics para direcionar os computadores para ativar. Quando adiciona um computador para receber atualizações da Atualização Management, o computador também é adicionado a uma pesquisa guardada no espaço de trabalho.

## <a name="set-the-scope-limit"></a>Definir o limite de âmbito

Para limitar o âmbito de aplicação da sua gestão de atualização:

1. Na sua conta Automation, selecione **Linked Workspace** em **recursos relacionados.**

2. Selecione **Vá para o espaço de trabalho**.

3. Selecione **Configurações de âmbito (pré-visualização)** em **Fontes de Dados do Espaço de Trabalho**.

4. Selecione a elipse à direita da configuração do `MicrosoftDefaultScopeConfig-Updates` âmbito e **selecione Editar**.

5. No painel de edição, **expanda os grupos de computador selecionados.** O painel de grupos de computador mostra as pesquisas guardadas que são usadas para criar a configuração do âmbito. A pesquisa guardada utilizada pela Update Management é:

    |Nome     |Categoria  |Alias  |
    |---------|---------|---------|
    |MicrosoftDefaultComputerGroup     | Atualizações        | Updates__MicrosoftDefaultComputerGroup         |

6. Selecione a procura guardada para visualizar e editar a consulta utilizada para povoar o grupo. A imagem a seguir mostra a consulta e os seus resultados:

    [![Pesquisas guardadas](./media/update-mgmt-scope-configuration/logsearch.png)](./media/update-mgmt-scope-configuration/logsearch-expanded.png#lightbox)

## <a name="next-steps"></a>Passos seguintes

Pode [consultar os registos do Azure Monitor](update-mgmt-query-logs.md) para analisar avaliações de atualizações, implementações e outras tarefas de gestão relacionadas. Inclui consultas pré-definidas para ajudá-lo a começar.
