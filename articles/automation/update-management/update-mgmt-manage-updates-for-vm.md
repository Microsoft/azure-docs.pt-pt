---
title: Gerir atualizações e patches para os seus VMs na Azure Automation
description: Este artigo diz como utilizar a Gestão de Atualização para gerir atualizações e patches para os seus VMs Azure e não-Azure.
services: automation
ms.subservice: update-management
ms.topic: conceptual
ms.date: 07/28/2020
ms.custom: mvc
ms.openlocfilehash: e4f630931e3cd79a46a539955b45d72e5b573271
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87450531"
---
# <a name="manage-updates-and-patches-for-your-vms"></a>Gerir atualizações e patches para os seus VMs

Atualizações de software na Azure Automation Update Management fornece um conjunto de ferramentas e recursos que podem ajudar a gerir a complexa tarefa de rastrear e aplicar atualizações de software a máquinas em Azure e nuvem híbrida. Um processo eficaz de gestão de atualização de software é necessário para manter a eficiência operacional, superar problemas de segurança e reduzir os riscos de aumento de ameaças à segurança cibernética. No entanto, devido à natureza evolutiva da tecnologia e ao aparecimento contínuo de novas ameaças de segurança, a gestão eficiente das atualizações de software exige uma atenção consistente e contínua.

> [!NOTE]
> A Update Management suporta a implementação de atualizações de primeira parte e o pré-download das mesmos. Este suporte requer alterações nos sistemas que estão a ser atualizados. Consulte [as definições de Configuração da Atualização do Windows para a Gestão de Atualizações de Automação Azure](update-mgmt-configure-wuagent.md) para aprender a configurar estas definições nos seus sistemas.

Antes de tentar gerir as atualizações para os seus VMs, certifique-se de que ativou a Gestão de Atualização sobre eles utilizando um destes métodos:

* [Ativar a Gestão de Atualizações a partir de uma conta de Automatização](update-mgmt-enable-automation-account.md)
* [Ativar a Gestão de Atualização navegando no portal Azure](update-mgmt-enable-portal.md)
* [Ativar a Gestão de Atualizações a partir de um runbook](update-mgmt-enable-runbook.md)
* [Ativar a Gestão de Atualizações a partir de uma VM do Azure](update-mgmt-enable-vm.md)

## <a name="limit-the-scope-for-the-deployment"></a><a name="scope-configuration"></a>Limitar o âmbito de implantação

A Atualização Management utiliza uma configuração de âmbito dentro do espaço de trabalho para direcionar os computadores para receber atualizações. Para obter mais informações, consulte [o âmbito de implementação da Gestão de Atualização de Limites](update-mgmt-scope-configuration.md).

## <a name="compliance-assessment"></a>Avaliação de conformidade

Antes de implementar atualizações de software para as suas máquinas, reveja os resultados da avaliação da conformidade da atualização para máquinas ativadas. Para cada atualização de software, o seu estado de conformidade é registado e, após a conclusão da avaliação, é recolhido e reencaminhado a granel para os registos do Azure Monitor.

Numa máquina Windows, a verificação de conformidade é executada a cada 12 horas por defeito. Além da verificação programada, a verificação para conformidade com a atualização é iniciada no prazo de 15 minutos após o reinício do agente Log Analytics para o Windows, antes da instalação da atualização e após a instalação da atualização. Também é importante rever as nossas recomendações sobre como [configurar o cliente do Windows Update](update-mgmt-configure-wuagent.md) com a Update Management para evitar quaisquer problemas que impeçam que seja gerido corretamente.

Para uma máquina Linux, a verificação de conformidade é efetuada de hora em hora por defeito. Se o agente Log Analytics do Linux for reiniciado, é iniciado um exame de conformidade dentro de 15 minutos.

Os resultados de conformidade são apresentados na Gestão de Atualização para cada máquina avaliada. Para uma nova máquina ativada para a gestão, pode demorar até 30 minutos para o painel apresentar dados atualizados a partir da sua gestão.

[Reveja as atualizações de software](update-mgmt-view-update-assessments.md) para saber como visualizar os resultados de conformidade.

## <a name="deploy-updates"></a>Implementar atualizações

Depois de rever os resultados da conformidade, a fase de implementação da atualização do software é o processo de implementação de atualizações de software. Para instalar atualizações, agende uma implementação que se alinhe com o seu horário de lançamento e janela de serviço. Pode escolher quais os tipos de atualização a incluir na implementação. Por exemplo, pode incluir atualizações de segurança ou críticas e excluir update rollups.

[Reveja as atualizações](update-mgmt-deploy-updates.md) de software para saber como agendar uma implementação de atualização.

## <a name="review-update-deployments"></a>Rever implementações de atualização

Após a implementação estar concluída, reveja o processo para determinar o sucesso da implementação da atualização por máquina ou grupo alvo. Consulte [o estado de implementação da revisão](update-mgmt-deploy-updates.md#check-deployment-status) para saber como pode monitorizar o estado de implantação.

## <a name="next-steps"></a>Passos seguintes

* Para aprender a criar alertas para o notificar sobre os resultados da atualização, consulte [criar alertas para a Gestão de Atualização.](update-mgmt-configure-alerts.md)

* Pode [consultar os registos do Azure Monitor](update-mgmt-query-logs.md) para analisar avaliações de atualizações, implementações e outras tarefas de gestão relacionadas. Inclui consultas pré-definidas para ajudá-lo a começar.