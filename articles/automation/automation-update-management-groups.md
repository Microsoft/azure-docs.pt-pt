---
title: Utilize grupos dinâmicos com a Gestão de Atualização de Automação Azure
description: Este artigo diz como usar grupos dinâmicos com a Azure Automation Update Management.
services: automation
ms.subservice: update-management
ms.date: 11/20/2019
ms.topic: conceptual
ms.openlocfilehash: c440862f1379983d4644fe99f33207456f747d23
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/25/2020
ms.locfileid: "83830944"
---
# <a name="use-dynamic-groups-with-update-management"></a>Utilizar grupos dinâmicos com a Gestão de Atualizações

A Atualização de Gestão permite-lhe direcionar um grupo dinâmico de VMs Azure ou não-Azure para implementações de atualizações. A utilização de um grupo dinâmico impede-o de editar a sua implementação para atualizar as máquinas.

> [!NOTE]
> Grupos dinâmicos não funcionam com VMs clássicos.

Pode definir grupos dinâmicos para máquinas Azure ou não-Azure a partir da gestão de **Atualização** no portal Azure. Ver [Gerir atualizações para várias máquinas virtuais Azure](manage-update-multi.md).

Um grupo dinâmico é definido por uma consulta que a Azure Automation avalia no momento da implantação. Mesmo que uma consulta de grupo dinâmico recupere um grande número de máquinas, a Azure Automation só pode processar um máximo de 1000 máquinas de cada vez. Veja [Subscrição do Azure e limites de serviço, quotas e restrições](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#update-management). 

> [!NOTE]
> Se espera atualizar mais de 1000 máquinas, recomendamos que divida as atualizações entre vários horários de atualização. 

## <a name="define-dynamic-groups-for-azure-machines"></a>Definir grupos dinâmicos para máquinas Azure

Ao definir uma consulta dinâmica de grupo para máquinas Azure, pode utilizar os seguintes itens para povoar o grupo dinâmico:

* Subscrição
* Grupos de recursos
* Localizações
* Etiquetas

![Selecione grupos](./media/automation-update-management/select-groups.png)

Para pré-visualizar os resultados da sua consulta dinâmica do grupo, clique em **Pré-visualização**. A pré-visualização mostra a adesão ao grupo no momento em curso. No exemplo, estamos à procura de máquinas com a etiqueta `Role` para o grupo **BackendServer**. Se mais máquinas tiverem esta etiqueta adicionada, são adicionadas a quaisquer futuras implementações contra esse grupo.

![grupos de pré-visualização](./media/automation-update-management/preview-groups.png)

## <a name="define-dynamic-groups-for-non-azure-machines"></a>Definir grupos dinâmicos para máquinas não-Azure

Um grupo dinâmico para máquinas não-Azure utiliza pesquisas guardadas, também chamadas de grupos informáticos. Para aprender a criar uma pesquisa guardada, consulte [Criar um grupo de computadores](../azure-monitor/platform/computer-groups.md#creating-a-computer-group). Assim que a sua pesquisa guardada for criada, pode selecioná-la a partir da lista de pesquisas guardadas na **gestão de Atualização** no portal Azure. Clique em **Pré-visualizar** os computadores na pesquisa guardada.

![Selecione grupos](./media/automation-update-management/select-groups-2.png)

## <a name="next-steps"></a>Passos seguintes

Para trabalhar com a Atualização, consulte [Gerir atualizações e patches para os seus VMs Azure](automation-tutorial-update-management.md).
