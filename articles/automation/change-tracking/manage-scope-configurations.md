---
title: Limite Azure Automation Change Tracking and Inventory Deployment Scope
description: Este artigo diz como trabalhar com configurações de âmbito para limitar o âmbito de uma implementação de Change Tracking e Inventory.
services: automation
ms.subservice: change-inventory-management
ms.date: 10/14/2020
ms.topic: conceptual
ms.openlocfilehash: 6eb9a20920f0a340491459f0875fc85b90dfa193
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92209991"
---
# <a name="limit-change-tracking-and-inventory-deployment-scope"></a>Limite de rastreio de alteração e implementação de inventário

Este artigo descreve como trabalhar com configurações de âmbito quando se utiliza a função [de Rastreio e Inventário de Alterações](overview.md) para implementar alterações nos seus VMs. Para obter mais informações, consulte [soluções de monitorização de direcionamento no Azure Monitor (Preview)](../../azure-monitor/insights/solution-targeting.md).

## <a name="about-scope-configurations"></a>Sobre configurações de âmbito

Uma configuração de âmbito é um grupo de uma ou mais pesquisas guardadas (consultas) usadas para limitar o âmbito de rastreio e inventário de alterações a computadores específicos. A configuração de âmbito é utilizada dentro do espaço de trabalho Do Log Analytics para direcionar os computadores para ativar. Quando adiciona um computador às alterações da funcionalidade, o computador também é adicionado a uma pesquisa guardada no espaço de trabalho.

## <a name="set-the-scope-limit"></a>Definir o limite de âmbito

Para limitar o âmbito de aplicação da sua implementação de 'Change Tracking and Inventory':

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

2. No portal Azure, selecione **Todos os serviços**. Na lista de recursos, escreva **Automatização**. À medida que começa a escrever, a lista filtra sugestões com base na sua entrada. Selecione **Contas de Automatização**.

3. Na sua lista de contas de Automação, selecione a conta que escolheu quando ativou o 'Tracking and Inventory'.

4. Na sua conta Automation, selecione **Linked Workspace** em **recursos relacionados.**

5. Clique **em Ir ao espaço de trabalho.**

6. Selecione **Configurações de âmbito (pré-visualização)** em **Fontes de Dados do Espaço de Trabalho**.

7. Selecione a elipse à direita da configuração do  `MicrosoftDefaultScopeConfig-ChangeTracking` âmbito e clique em **Editar**.

8. No painel de edição, **selecione Select Computer Groups**. O painel de grupos de computador mostra as pesquisas guardadas que são usadas para criar a configuração do âmbito. A pesquisa guardada utilizada por Change Tracking and Inventory é:

    |Name     |Categoria  |Alias  |
    |---------|---------|---------|
    |MicrosoftDefaultComputerGroup     |  ChangeTracking       | ChangeTracking__MicrosoftDefaultComputerGroup        |

9. Selecione a procura guardada para visualizar e editar a consulta utilizada para povoar o grupo. A imagem a seguir mostra a consulta e os seus resultados:

    ![Pesquisas guardadas](media/manage-scope-configurations/logsearch.png)

## <a name="next-steps"></a>Passos seguintes

* Para trabalhar com o Change Tracking and Inventory, consulte [Gerir o Tracking e o Inventário de Mudanças.](manage-change-tracking.md)
* Para resolver problemas gerais de recursos, consulte [problemas de rastreio e inventário de alterações de resolução de problemas](../troubleshoot/change-tracking.md).
