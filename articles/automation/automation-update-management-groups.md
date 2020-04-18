---
title: Utilize grupos dinâmicos com a Gestão de Atualização de Automação Azure
description: Este artigo descreve como grupos dinâmicos trabalham com a Azure Automation Update Management.
services: automation
ms.subservice: update-management
ms.date: 11/20/2019
ms.topic: conceptual
ms.openlocfilehash: 29a72eb1fe7b8be18cd2160fc63160e408378585
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/17/2020
ms.locfileid: "81617433"
---
# <a name="use-dynamic-groups-with-update-management"></a>Utilizar grupos dinâmicos com a Gestão de Atualizações

A Atualização de Gestão permite-lhe direcionar um grupo dinâmico de VMs Azure ou não-Azure para implementações de atualizações. Estes grupos, definidos por consultas, são avaliados no momento da implementação para que não tenha de editar a sua implementação para adicionar máquinas.

## <a name="azure-machines"></a>Máquinas Azure

Grupos dinâmicos não funcionam com VMs clássicos. Ao definir a sua consulta, os seguintes itens podem ser usados em conjunto para povoar um grupo dinâmico:

* Subscrição
* Grupos de recursos
* Localizações
* Etiquetas

![Selecione grupos](./media/automation-update-management/select-groups.png)

Para pré-visualizar os resultados de um grupo dinâmico, clique em **Pré-visualização**. A pré-visualização mostra a adesão ao grupo no momento em curso. No exemplo, estamos à procura de máquinas com a etiqueta `Role` para o grupo **BackendServer**. Se mais máquinas tiverem esta etiqueta adicionada, são adicionadas a quaisquer futuras implementações contra esse grupo.

![grupos de pré-visualização](./media/automation-update-management/preview-groups.png)

## <a name="non-azure-machines"></a>Máquinas não-Azure

Para máquinas não-Azure, as pesquisas guardadas, também referidas como grupos informáticos, são usadas para criar o grupo dinâmico. Para aprender a criar uma pesquisa guardada, consulte [Criar um grupo de computadores](../azure-monitor/platform/computer-groups.md#creating-a-computer-group). Assim que o seu grupo for criado, pode selecioná-lo a partir da lista de pesquisas guardadas. Clique em **Pré-visualizar** os computadores na pesquisa guardada nessa altura.

![Selecione grupos](./media/automation-update-management/select-groups-2.png)

## <a name="next-steps"></a>Passos seguintes

Depois de criar um grupo dinâmico, pode [criar uma Implementação](automation-tutorial-update-management.md)de Atualização .
