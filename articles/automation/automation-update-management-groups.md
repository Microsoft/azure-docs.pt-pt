---
title: Utilize grupos dinâmicos com a Azure Automation Update Management
description: Este artigo diz como utilizar grupos dinâmicos com a Azure Automation Update Management.
services: automation
ms.subservice: update-management
ms.date: 11/20/2019
ms.topic: conceptual
ms.openlocfilehash: 25a306b3281079e8476a67ee70c2ca2cfffdd30c
ms.sourcegitcommit: ec682dcc0a67eabe4bfe242fce4a7019f0a8c405
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/09/2020
ms.locfileid: "86185743"
---
# <a name="use-dynamic-groups-with-update-management"></a>Utilizar grupos dinâmicos com a Gestão de Atualizações

A Atualização De gestão permite-lhe direcionar um grupo dinâmico de VMs Azure ou não-Azure para implementações de atualização. A utilização de um grupo dinâmico impede-o de editar a sua implementação para atualizar máquinas.

> [!NOTE]
> Grupos dinâmicos não funcionam com VMs clássicos.

Pode definir grupos dinâmicos para máquinas Azure ou não-Azure da gestão de **Atualização** no portal Azure. Consulte [As atualizações para várias máquinas virtuais Azure](manage-update-multi.md).

Um grupo dinâmico é definido por uma consulta que a Azure Automation avalia no momento da implementação. Mesmo que uma consulta dinâmica de grupo recupere um grande número de máquinas, a Azure Automation pode processar apenas um máximo de 1000 máquinas de cada vez. Veja [Subscrição do Azure e limites de serviço, quotas e restrições](../azure-resource-manager/management/azure-subscription-service-limits.md#update-management). 

> [!NOTE]
> Se espera atualizar mais de 1000 máquinas, recomendamos que divida as atualizações entre vários horários de atualização. 

## <a name="define-dynamic-groups-for-azure-machines"></a>Defina grupos dinâmicos para máquinas Azure

Ao definir uma consulta dinâmica de grupo para máquinas Azure, pode utilizar os seguintes itens para povoar o grupo dinâmico:

* Subscrição
* Grupos de recursos
* Localizações
* Etiquetas

![Selecione grupos](./media/automation-update-management/select-groups.png)

Para visualizar os resultados da sua consulta dinâmica de grupo, clique em **Preview**. A pré-visualização mostra a adesão ao grupo no momento atual. No exemplo, estamos à procura de máquinas com a etiqueta `Role` para o grupo **BackendServer.** Se mais máquinas tiverem esta etiqueta adicionada, são adicionadas a quaisquer futuras implementações contra esse grupo.

![grupos de pré-visualização](./media/automation-update-management/preview-groups.png)

## <a name="define-dynamic-groups-for-non-azure-machines"></a>Defina grupos dinâmicos para máquinas não-Azure

Um grupo dinâmico para máquinas não-Azure usa pesquisas guardadas, também chamadas de grupos de computador. Para aprender a criar uma pesquisa guardada, consulte [criar um grupo de computadores](../azure-monitor/platform/computer-groups.md#creating-a-computer-group). Uma vez criada a sua pesquisa guardada, pode selecioná-la na lista de pesquisas guardadas na **gestão de Atualização** no portal Azure. Clique em **Pré-visualização** para pré-visualizar os computadores na pesquisa guardada.

![Selecione grupos](./media/automation-update-management/select-groups-2.png)

## <a name="next-steps"></a>Passos seguintes

Para trabalhar com a Gestão de Atualização, consulte [Gerir atualizações e patches para os seus VMs Azure](automation-tutorial-update-management.md).
