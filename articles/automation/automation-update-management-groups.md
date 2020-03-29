---
title: Utilize grupos dinâmicos com a Gestão de Atualizações do Azure
description: Este artigo descreve como grupos dinâmicos trabalham com a Azure Automation Update Management.
services: automation
ms.subservice: update-management
ms.date: 11/20/2019
ms.topic: conceptual
ms.openlocfilehash: 678b3f361e4456a2c482896f7d7dc20d530b917b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75420405"
---
# <a name="use-dynamic-groups-with-update-management"></a>Utilizar grupos dinâmicos com a Gestão de Atualizações

A Update Management fornece a capacidade de direcionar um grupo dinâmico de VMs Azure ou não-Azure para implementações de atualizações. Estes grupos são avaliados no momento da implantação, pelo que não é necessário editar a sua implementação para adicionar máquinas.

## <a name="azure-machines"></a>Máquinas Azure

Estes grupos são definidos por uma consulta, quando uma implementação de atualização começa, os membros desse grupo são avaliados. Grupos dinâmicos não funcionam com VMs clássicos. Ao definir a sua consulta, os seguintes itens podem ser usados em conjunto para povoar o grupo dinâmico:

* Subscrição
* Grupos de recursos
* Localizações
* Etiquetas

![Selecione grupos](./media/automation-update-management/select-groups.png)

Para pré-visualizar os resultados de um grupo dinâmico, clique no botão **Pré-visualização.** Esta pré-visualização mostra a adesão ao grupo nessa altura, neste exemplo, estamos à procura de máquinas com a tag **Role** é igual a **BackendServer**. Se mais máquinas tiverem esta etiqueta adicionada, serão adicionadas a quaisquer futuras implementações contra esse grupo.

![grupos de pré-visualização](./media/automation-update-management/preview-groups.png)

## <a name="non-azure-machines"></a>Máquinas não-Azure

Para máquinas não-Azure, as pesquisas guardadas também referidas como grupos informáticos, são usadas para criar o grupo dinâmico. Para aprender a criar uma pesquisa guardada, consulte [Criar um grupo de computadores](../azure-monitor/platform/computer-groups.md#creating-a-computer-group). Assim que o seu grupo for criado, pode selecioná-lo a partir da lista de pesquisas guardadas. Clique em **Pré-visualizar** os computadores na pesquisa guardada nessa altura.

![Selecione grupos](./media/automation-update-management/select-groups-2.png)

## <a name="next-steps"></a>Passos seguintes

Depois de criar um grupo dinâmico, pode [criar uma implementação](automation-tutorial-update-management.md) de atualização
