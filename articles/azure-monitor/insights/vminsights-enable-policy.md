---
title: Ativar o Monitor Azure para VMs utilizando a política do Azure
description: Descreve como ativa o Azure Monitor para VMs para várias máquinas virtuais Azure ou conjuntos de balanças de máquinas virtuais utilizando a Política Azure.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/27/2020
ms.openlocfilehash: 240c96016304c009c36485869ac15f5f38076fb7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90088294"
---
# <a name="enable-azure-monitor-for-vms-by-using-azure-policy"></a>Ativar o Monitor Azure para VMs utilizando a política do Azure
Este artigo explica como permitir o Azure Monitor para VMs para máquinas virtuais Azure ou máquina virtual híbrida ligada ao Arco Azure (pré-visualização) utilizando a Política Azure. A Azure Policy permite-lhe atribuir definições de política que instalam os agentes necessários para O Azure Monitor para VMs em todo o seu ambiente Azure e ativar automaticamente a monitorização de VMs à medida que cada máquina virtual é criada. O Azure Monitor for VMs fornece uma funcionalidade que lhe permite descobrir e remediar VMs não conformes no seu ambiente. Utilize esta funcionalidade em vez de trabalhar diretamente com a Azure Policy.

Se não estiver familiarizado com a Política Azure, obtenha uma breve introdução [no Deploy Azure Monitor em escala utilizando a Política Azure](../deploy-scale.md).

> [!NOTE]
> Para utilizar a Política Azure com conjuntos de balanças de máquinas virtuais Azure ou para trabalhar diretamente com a Azure Policy para ativar máquinas virtuais Azure, consulte [o Monitor de Implantação em escala utilizando a Política Azure](../deploy-scale.md#azure-monitor-for-vms).

## <a name="prerequisites"></a>Pré-requisitos
- [Criar e configurar um espaço de trabalho Log Analytics](vminsights-configure-workspace.md).
- Consulte [sistemas operativos suportados](vminsights-enable-overview.md#supported-operating-systems) para garantir que o sistema operativo da máquina virtual ou da balança de máquinas virtuais que está a ativar é suportado. 


## <a name="azure-monitor-for-vms-initiative"></a>Azure Monitor para a iniciativa VMs
O Azure Monitor for VMs fornece definições de política construídas para instalar o agente Log Analytics e o agente de dependência em máquinas virtuais Azure. A iniciativa **Enable Azure Monitor for VMs** inclui cada uma destas definições políticas. Atribua esta iniciativa a um grupo de gestão, subscrição ou grupo de recursos para instalar automaticamente os agentes em quaisquer máquinas virtuais Windows ou Linux Azure nesse âmbito.

## <a name="open-policy-coverage-feature"></a>Funcionalidade de Cobertura de Política Aberta
Para aceder ao **Azure Monitor para cobertura de política de VMs,** vá às **máquinas Virtuais** no menu **Azure Monitor** no portal Azure. Selecione **Outras opções de embarque** e, em seguida, **Ative** em Enable **using policy**.

[![Monitor Azure da VMs Get Started tab](./media/vminsights-enable-at-scale-policy/get-started-page.png)](./media/vminsights-enable-at-scale-policy/get-started-page.png#lightbox)

## <a name="create-new-assignment"></a>Criar nova atribuição
Se ainda não tiver uma atribuição, crie uma nova clicando em **'Atribuir' Política.**

[![Criar atribuição](media/vminsights-enable-at-scale-policy/create-assignment.png)](media/vminsights-enable-at-scale-policy/create-assignment.png#lightbox)

Esta é a mesma página para atribuir uma iniciativa na Política Azure, exceto que é codificada com o âmbito que selecionou e o Enable Azure Monitor para a definição de iniciativa **VMs.** Pode alterar opcionalmente o **nome de atribuição** e adicionar uma **Descrição**. Selecione **Exclusões** se quiser fornecer uma exclusão ao âmbito. Por exemplo, o seu âmbito pode ser um grupo de gestão, e você pode especificar uma subscrição nesse grupo de gestão para ser excluído da atribuição.

[![Atribuir iniciativa](media/vminsights-enable-at-scale-policy/assign-initiative.png)](media/vminsights-enable-at-scale-policy/assign-initiative.png#lightbox)

Na página **Parâmetros,** selecione um **espaço de trabalho Log Analytics** para ser utilizado por todas as máquinas virtuais na atribuição. Se quiser especificar diferentes espaços de trabalho para diferentes máquinas virtuais, então tem de criar várias atribuições, cada uma com o seu próprio âmbito. 

   > [!NOTE]
   > Se o espaço de trabalho estiver fora do âmbito da atribuição, conceda permissões *ao Log Analytics Contributor* para o ID principal da atribuição de políticas. Se não fizeres isto, podes ver uma falha de implantação como `The client '343de0fe-e724-46b8-b1fb-97090f7054ed' with object id '343de0fe-e724-46b8-b1fb-97090f7054ed' does not have authorization to perform action 'microsoft.operationalinsights/workspaces/read' over scope ...`

[![Área de trabalho](media/vminsights-enable-at-scale-policy/assignment-workspace.png)](media/vminsights-enable-at-scale-policy/assignment-workspace.png#lightbox)

Clique **em 'Rever + Criar'** para rever os detalhes da atribuição antes de clicar em **Criar** para criá-la. Não crie uma tarefa de reparação neste momento, uma vez que provavelmente necessitará de múltiplas tarefas de reparação para ativar as máquinas virtuais existentes. Consulte os resultados de conformidade de [Remediato](#remediate-compliance-results) abaixo.

## <a name="review-compliance"></a>Conformidade de revisão
Uma vez criada uma atribuição, pode rever e gerir a cobertura da iniciativa **Enable Azure Monitor for VMs** através dos seus grupos de gestão e subscrições. Isto mostrará quantas máquinas virtuais existem em cada um dos grupos de gestão ou subscrições e o seu estado de conformidade.

[![Monitor Azure para página de Política de Gestão de VMs](media/vminsights-enable-at-scale-policy/manage-policy-page-01.png)](media/vminsights-enable-at-scale-policy/manage-policy-page-01.png#lightbox)


A tabela a seguir fornece uma descrição das informações neste ponto de vista.

| Função | Descrição | 
|----------|-------------| 
| **Âmbito** | Grupo de gestão e subscrições a que tem ou herdou acesso com capacidade de perfurar através da hierarquia do grupo de gestão.|
| **Role** | O seu papel no âmbito, que pode ser leitor, proprietário ou contribuinte. Isto ficará em branco se tiver acesso à subscrição, mas não ao grupo de gestão a que pertence. Esta função determina os dados que pode ver e as ações que pode realizar em termos de atribuição de políticas ou iniciativas (proprietário), edição ou visualização do cumprimento. |
| **Total VMs** | Número total de VMs nesse âmbito, independentemente do seu estatuto. Para um grupo de gestão, esta é uma soma total de VMs aninhados sob as assinaturas ou grupos de gestão de crianças. |
| **Cobertura de Atribuição** | Por cento dos VMs que são abrangidos pela iniciativa. |
| **Estado da atribuição** | **Sucesso** - Todos os VMs no âmbito têm os agentes de Log Analytics e Dependência destacados para eles.<br>**Aviso** - A subscrição não está sob um grupo de gestão.<br>**Not Started** - Uma nova missão foi adicionada.<br>**Lock** - Não tem privilégios suficientes para o grupo de gestão.<br>**Em branco** - Não existem VMs ou uma apólice não é atribuída. |
| **VMs conformes** | Número de VMs que são compatíveis, que é o número de VMs que têm tanto o agente Log Analytics como o agente de Dependência instalados. Isto ficará em branco se não houver atribuições, nem VMs no âmbito, ou se não houver permissões adequadas. |
| **Conformidade** | O número global de conformidade é a soma de recursos distintos que são compatíveis divididos pela soma de todos os recursos distintos. |
| **Estado de conformidade** | **Em conformidade** - Todos os VMs no âmbito das máquinas virtuais têm os agentes de Log Analytics e Dependência destacados para eles ou quaisquer novos VMs no âmbito sujeito à atribuição ainda não foram avaliados.<br>**Incompatíveis** - Existem VMs que foram avaliados mas não estão habilitados e podem exigir reparação.<br>**Not Started** - Uma nova missão foi adicionada.<br>**Lock** - Não tem privilégios suficientes para o grupo de gestão.<br>**Em branco** - Não é atribuída nenhuma apólice.  |


Quando atribuir a iniciativa, o âmbito selecionado na atribuição pode ser o âmbito listado ou um subconjunto da iniciativa. Por exemplo, pode ter criado uma atribuição para uma subscrição (âmbito de política) e não um grupo de gestão (âmbito de cobertura). Neste caso, o valor da Cobertura de **Atribuição** indica os VM no âmbito de iniciativa dividido pelos VMs no âmbito de cobertura. Noutro caso, pode ter excluído alguns VMs, grupos de recursos ou uma subscrição do âmbito de aplicação da política. Se o valor estiver em branco, indica que a apólice ou a iniciativa não existem ou não têm permissão. A informação é fornecida no âmbito **do Estado de Atribuição**.


## <a name="remediate-compliance-results"></a>Corrigir resultados de conformidade
A iniciativa será aplicada às máquinas virtuais à medida que forem criadas ou modificadas, mas não será aplicada aos VM existentes. Se a sua atribuição não mostrar conformidade a 100%, criar tarefas de reparação para avaliar e ativar os VM existentes, selecione **Ver Conformidade** selecionando a elipse (...).

[![Ver conformidade](media/vminsights-enable-at-scale-policy/view-compliance.png)](media/vminsights-enable-at-scale-policy/view-compliance.png#lightbox)

A página **Compliance** lista atribuições correspondentes ao filtro especificado e se estão em conformidade. Clique numa atribuição para ver os seus detalhes.

[![Conformidade da política para os VMs Azure](./media/vminsights-enable-at-scale-policy/policy-view-compliance.png)](./media/vminsights-enable-at-scale-policy/policy-view-compliance.png#lightbox)

A página **de conformidade da Iniciativa** enumera as definições políticas da iniciativa e se cada uma está em conformidade.

[![Detalhes de conformidade](media/vminsights-enable-at-scale-policy/compliance-details.png)](media/vminsights-enable-at-scale-policy/compliance-details.png#lightbox)

Clique numa definição de política para ver os seus detalhes. Os cenários que as definições de política mostrarão como fora do cumprimento incluem:

* O agente de log Analytics ou agente de dependência não está implantado. Criar uma tarefa de reparação para mitigar.
* A imagem VM (OS) não está identificada na definição de política. Os critérios da política de implantação incluem apenas VMs que são implantados a partir de imagens VM bem conhecidas do Azure. Verifique a documentação para ver se o VM OS está suportado.
* Os VM não estão a registar-se no espaço de trabalho especificado do Log Analytics. Alguns VMs no âmbito da iniciativa estão ligados a um espaço de trabalho Log Analytics que não seja o especificado na atribuição de políticas.

[![Detalhes de conformidade com a política](media/vminsights-enable-at-scale-policy/policy-compliance-details.png)](media/vminsights-enable-at-scale-policy/policy-compliance-details.png#lightbox)

Para criar uma tarefa de remediação para mitigar problemas de conformidade, clique em **Criar Tarefa de Remediação**. 

[![Nova tarefa de reparação](media/vminsights-enable-at-scale-policy/new-remediation-task.png)](media/vminsights-enable-at-scale-policy/new-remediation-task.png#lightbox)

Clique **em Remediar** para criar a tarefa de remediação e, em seguida, **Remediar** para iniciá-la. Provavelmente terá de criar múltiplas tarefas de reparação, uma para cada definição de política. Não se pode criar uma tarefa de reparação para uma iniciativa.

[![Screenshot mostra o painel de remediação da política para monitores Máquinas Virtuais.](media/vminsights-enable-at-scale-policy/remediation.png)](media/vminsights-enable-at-scale-policy/remediation.png#lightbox)


Uma vez concluídas as tarefas de reparação, os seus VMs devem estar em conformidade com os agentes instalados e ativados para O Monitor Azure para VMs. 

## <a name="next-steps"></a>Passos seguintes

Agora que a monitorização está ativada para as suas máquinas virtuais, esta informação está disponível para análise com o Azure Monitor para VMs. 

- Para ver as dependências de aplicações descobertas, consulte [o Monitor do Azure para o Mapa de VMs](vminsights-maps.md). 
- Para identificar estrangulamentos e utilização global com o desempenho do seu VM, consulte o desempenho do [See Azure VM](vminsights-performance.md). 
