---
title: Utilize grupos dinâmicos com a Azure Automation Update Management
description: Este artigo diz como utilizar grupos dinâmicos com a Azure Automation Update Management.
services: automation
ms.subservice: update-management
ms.date: 07/28/2020
ms.topic: conceptual
ms.openlocfilehash: a2df2489e4b216b4b33524e35691bf93468edda0
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/20/2020
ms.locfileid: "92222416"
---
# <a name="use-dynamic-groups-with-update-management"></a>Utilizar grupos dinâmicos com a Gestão de Atualizações

A Atualização De gestão permite-lhe direcionar um grupo dinâmico de VMs Azure ou não-Azure para implementações de atualização. A utilização de um grupo dinâmico impede-o de editar a sua implementação para atualizar máquinas.

> [!NOTE]
> Grupos dinâmicos não funcionam com VMs clássicos.

Pode definir grupos dinâmicos para máquinas Azure ou não-Azure da gestão de **Atualização** no portal Azure. Ver [Gerir atualizações para VMs](manage-updates-for-vm.md).

Um grupo dinâmico é definido por uma consulta que a Azure Automation avalia no momento da implementação. Mesmo que uma consulta dinâmica de grupo recupere um grande número de máquinas, a Azure Automation pode processar apenas um máximo de 1000 máquinas de cada vez. Veja [Subscrição do Azure e limites de serviço, quotas e restrições](../../azure-resource-manager/management/azure-subscription-service-limits.md#update-management).

> [!NOTE]
> Se espera atualizar mais de 1000 máquinas, recomendamos que divida as atualizações entre vários horários de atualização. 

## <a name="define-dynamic-groups-for-azure-machines"></a>Defina grupos dinâmicos para máquinas Azure

Ao definir uma consulta dinâmica de grupo para máquinas Azure, pode utilizar os seguintes itens para povoar o grupo dinâmico:

* Subscrição
* Grupos de recursos
* Localizações
* Etiquetas

![Selecione grupos](./media/configure-groups/select-groups.png)

Para visualizar os resultados da sua consulta dinâmica de grupo, clique em **Preview**. A pré-visualização mostra a adesão ao grupo no momento atual. No exemplo, estamos à procura de máquinas com a etiqueta `Role` para o grupo **BackendServer.** Se mais máquinas tiverem esta etiqueta adicionada, são adicionadas a quaisquer futuras implementações contra esse grupo.

![grupos de pré-visualização](./media/configure-groups/preview-groups.png)

## <a name="define-dynamic-groups-for-non-azure-machines"></a>Defina grupos dinâmicos para máquinas não-Azure

Um grupo dinâmico para máquinas não-Azure usa pesquisas guardadas, também chamadas de grupos de computador. Para aprender a criar uma pesquisa guardada, consulte [criar um grupo de computadores](../../azure-monitor/platform/computer-groups.md#creating-a-computer-group). Uma vez criada a sua pesquisa guardada, pode selecioná-la na lista de pesquisas guardadas na **gestão de Atualização** no portal Azure. Clique em **Pré-visualização** para pré-visualizar os computadores na pesquisa guardada.

![A screenshot mostra a página de grupos Select para Non-Azure (Preview) e o painel de pré-visualização no lado direito.](./media/configure-groups/select-groups-2.png)

## <a name="next-steps"></a>Passos seguintes

Pode [consultar os registos do Azure Monitor](query-logs.md) para analisar avaliações de atualizações, implementações e outras tarefas de gestão relacionadas. Inclui consultas pré-definidas para ajudá-lo a começar.
