---
title: Habilitar Azure Monitor para VMs usando Azure Policy | Microsoft Docs
description: Este artigo descreve como habilitar Azure Monitor para VMs para várias máquinas virtuais do Azure ou conjuntos de dimensionamento de máquinas virtuais usando Azure Policy.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/07/2019
ms.author: magoedte
ms.openlocfilehash: cbb471d337bd386b6c5f2c7a960565ef29855c9c
ms.sourcegitcommit: e1b6a40a9c9341b33df384aa607ae359e4ab0f53
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/27/2019
ms.locfileid: "71338228"
---
# <a name="enable-azure-monitor-for-vms-preview-by-using-azure-policy"></a>Habilitar Azure Monitor para VMs (versão prévia) usando Azure Policy

Este artigo explica como habilitar a Azure Monitor para VMs (versão prévia) para máquinas virtuais do Azure ou conjuntos de dimensionamento de máquinas virtuais usando Azure Policy. No final desse processo, você terá configurado com êxito a habilitação do Log Analytics e dos agentes de dependência e das máquinas virtuais identificadas que não são compatíveis.

Para descobrir, gerenciar e habilitar Azure Monitor para VMs para todas as suas máquinas virtuais do Azure ou conjuntos de dimensionamento de máquinas virtuais, você pode usar o Azure Policy ou Azure PowerShell. Azure Policy é o método que recomendamos porque você pode gerenciar definições de política para controlar com eficiência suas assinaturas a fim de garantir a conformidade consistente e a habilitação automática de VMs provisionadas recentemente. Estas definições de política:

* Implemente o agente Log Analytics e o agente de dependência.
* Relatório sobre os resultados de compatibilidade.
* Correção para VMs não compatíveis.

Se você estiver interessado em realizar essas tarefas com Azure PowerShell ou um modelo de Azure Resource Manager, consulte [habilitar Azure monitor para VMs (versão prévia) usando Azure PowerShell ou modelos de Azure Resource Manager](vminsights-enable-at-scale-powershell.md).

## <a name="manage-policy-coverage-feature-overview"></a>Visão geral do recurso gerenciar cobertura de política

Originalmente, a experiência com Azure Policy para gerenciar e implantar as definições de política para Azure Monitor para VMs foi feita exclusivamente de Azure Policy. O recurso gerenciar cobertura de política torna mais simples e fácil descobrir, gerenciar e habilitar em escala a iniciativa **habilitar Azure monitor para VMs** , que inclui as definições de política mencionadas anteriormente. Você pode acessar esse novo recurso na guia **introdução** no Azure monitor para VMs. Selecione **gerenciar cobertura de política** para abrir a página de **cobertura de política de Azure monitor para VMs** .

![Guia de introdução de Azure Monitor de VMs](./media/vminsights-enable-at-scale-policy/get-started-page-01.png)

A partir daqui, você pode verificar e gerenciar a cobertura da iniciativa em seus grupos de gerenciamento e assinaturas. Você pode entender quantas VMs existem em cada um dos grupos de gerenciamento e assinaturas e seu status de conformidade.

![Azure Monitor para VMs página Gerenciar política](./media/vminsights-enable-at-scale-policy/manage-policy-page-01.png)

Essas informações são úteis para ajudá-lo a planejar e executar seu cenário de governança para Azure Monitor para VMs de um local central. Embora Azure Policy forneça uma exibição de conformidade quando uma política ou iniciativa é atribuída a um escopo, com essa nova página, você pode descobrir onde a política ou a iniciativa não está atribuída e atribuí-la em vigor. Todas as ações como atribuir, exibir e editar redirecionamento para Azure Policy diretamente. A página de **cobertura da política de Azure monitor para VMs** é uma experiência expandida e integrada somente para a iniciativa **habilitar Azure monitor para VMs**.

Nessa página, você também pode configurar seu espaço de trabalho do Log Analytics para Azure Monitor para VMs, que:

- Instala as soluções de instalação Mapa do Serviço e Infrastructure Insights.
- Habilita os contadores de desempenho do sistema operacional usados pelos gráficos de desempenho, pastas de trabalho e suas consultas e alertas de log personalizados.

![Azure Monitor para VMs configurar espaço de trabalho](./media/vminsights-enable-at-scale-policy/manage-policy-page-02.png)

Essa opção não está relacionada a nenhuma ação de política. Ele está disponível para fornecer uma maneira fácil de atender aos [pré-requisitos](vminsights-enable-overview.md) necessários para habilitar o Azure monitor para VMs.  

### <a name="what-information-is-available-on-this-page"></a>Quais informações estão disponíveis nesta página?
A tabela a seguir fornece uma análise das informações apresentadas na página cobertura de política e como interpretá-las.

| Função | Descrição | 
|----------|-------------| 
| **Âmbito** | O grupo de gerenciamento e as assinaturas que você tem ou acesso herdado com a capacidade de detalhar a hierarquia do grupo de gerenciamento.|
| **Função** | Sua função para o escopo, que pode ser leitor, proprietário ou colaborador. Em alguns casos, pode aparecer em branco para indicar que você pode ter acesso à assinatura, mas não ao grupo de gerenciamento ao qual ela pertence. As informações em outras colunas variam de acordo com sua função. A função é fundamental para determinar quais dados você pode ver e as ações que podem ser executadas em termos de atribuição de políticas ou iniciativas (proprietário), edição ou exibição da conformidade. |
| **Total de VMs** | Número de VMs sob esse escopo. Para um grupo de gerenciamento, é uma soma das VMs aninhadas no grupo de gerenciamento assinaturas ou filho. |
| **Cobertura de atribuição** | Porcentagem de VMs cobertas pela política ou iniciativa. |
| **Status da atribuição** | Informações sobre o status da sua atribuição de política ou iniciativa. |
| **VMs em conformidade** | Número de VMs que estão em conformidade com a política ou iniciativa. Para a iniciativa **habilitar Azure monitor para VMs**, esse é o número de VMs que têm o agente de log Analytics e o agente de dependência. Em alguns casos, ele pode aparecer em branco devido a nenhuma atribuição, nenhuma VM ou permissões insuficientes. As informações são fornecidas em **estado de conformidade**. |
| **Conformidade** | O número de conformidade geral é a soma de recursos distintos que são compatíveis divididos pela soma de todos os recursos distintos. |
| **Estado de Compatibilidade** | Informações sobre o estado de conformidade da sua política ou atribuição de iniciativa.|

Quando você atribui a política ou iniciativa, o escopo selecionado na atribuição pode ser o escopo listado ou um subconjunto dele. Por exemplo, você pode ter criado uma atribuição para uma assinatura (escopo de política) e não um grupo de gerenciamento (escopo de cobertura). Nesse caso, o valor da **cobertura de atribuição** indica as VMs no escopo da política ou da iniciativa dividida pelas VMs no escopo de cobertura. Em outro caso, você pode ter excluído algumas VMs, grupos de recursos ou uma assinatura do escopo da política. Se o valor estiver em branco, indica que a política ou iniciativa não existe ou você não tem permissão. As informações são fornecidas sob o **status da atribuição**.

## <a name="enable-by-using-azure-policy"></a>Ativar através da política do Azure

Para ativar o Azure Monitor para VMs com a política do Azure no seu inquilino:

- Atribua a iniciativa a um escopo: grupo de gerenciamento, assinatura ou grupo de recursos.
- Revise e corrija os resultados de conformidade.

Para obter mais informações sobre a atribuição de política do Azure, consulte [descrição geral da política do Azure](../../governance/policy/overview.md#policy-assignment) e reveja a [descrição geral de grupos de gestão](../../governance/management-groups/overview.md) antes de continuar.

### <a name="policies-for-azure-vms"></a>Políticas para VMs do Azure

As definições de política para uma VM do Azure são listadas na tabela a seguir.

|Name |Descrição |Type |
|-----|------------|-----|
|\[Versão\]prévia: Ativar o Azure Monitor para VMs |Habilite Azure Monitor para as máquinas virtuais no escopo especificado (grupo de gerenciamento, assinatura ou grupo de recursos). Aceita a área de trabalho do Log Analytics como um parâmetro. |Iniciativa |
|\[Versão\]prévia: Implantação do agente de dependência de auditoria – imagem da VM (SO) não listada |Relata as VMs como não compatíveis se a imagem de VM (SO) não estiver definida na lista e o agente não estiver instalado. |Política |
|\[Versão\]prévia: Implantação de agente de Log Analytics de auditoria – imagem de VM (SO) não listada |Relata as VMs como não compatíveis se a imagem de VM (SO) não estiver definida na lista e o agente não estiver instalado. |Política |
|\[Versão\]prévia: Implantar o agente de dependência para VMs Linux |Implante o agente de dependência para VMs do Linux se a imagem de VM (SO) estiver definida na lista e o agente não estiver instalado. |Política |
|\[Versão\]prévia: Implantar o agente de dependência para VMs do Windows |Implante o agente de dependência para VMs do Windows se a imagem de VM (SO) estiver definida na lista e o agente não estiver instalado. |Política |
|\[Versão\]prévia: Implantar o agente de Log Analytics para VMs Linux |Implante Log Analytics agente para VMs Linux se a imagem de VM (SO) estiver definida na lista e o agente não estiver instalado. |Política |
|\[Versão\]prévia: Implantar o agente de Log Analytics para VMs do Windows |Implante Log Analytics agente para VMs do Windows se a imagem de VM (SO) estiver definida na lista e o agente não estiver instalado. |Política |

### <a name="policies-for-azure-virtual-machine-scale-sets"></a>Políticas para conjuntos de dimensionamento de máquinas virtuais do Azure

As definições de política para um conjunto de dimensionamento de máquinas virtuais do Azure estão listadas na tabela a seguir.

|Name |Descrição |Type |
|-----|------------|-----|
|\[Versão\]prévia: Habilitar Azure Monitor para conjuntos de dimensionamento de máquinas virtuais |Habilite Azure Monitor para os conjuntos de dimensionamento de máquinas virtuais no escopo especificado (grupo de gerenciamento, assinatura ou grupo de recursos). Aceita a área de trabalho do Log Analytics como um parâmetro. Nota: Se a política de atualização do conjunto de dimensionamento estiver definida como manual, aplique a extensão a todas as VMs no conjunto chamando a atualização nelas. Na CLI, isso é AZ vmss Update-instances. |Iniciativa |
|\[Versão\]prévia: Auditoria de implantação de agente de dependência em conjuntos de dimensionamento de máquinas virtuais – imagem de VM (SO) não listada |Relata o conjunto de dimensionamento de máquinas virtuais como não compatível se a imagem da VM (SO) não estiver definida na lista e o agente não estiver instalado. |Política |
|\[Versão\]prévia: Auditoria de implantação de agente de Log Analytics em conjuntos de dimensionamento de máquinas virtuais – imagem de VM (SO) não listada |Relata o conjunto de dimensionamento de máquinas virtuais como não compatível se a imagem da VM (SO) não estiver definida na lista e o agente não estiver instalado. |Política |
|\[Versão\]prévia: Implantar o agente de dependência para conjuntos de dimensionamento de máquinas virtuais do Linux |Implante o agente de dependência para conjuntos de dimensionamento de máquinas virtuais do Linux se a imagem de VM (SO) estiver definida na lista e o agente não estiver instalado. |Política |
|\[Versão\]prévia: Implantar o agente de dependência para conjuntos de dimensionamento de máquinas virtuais do Windows |Implante o agente de dependência para conjuntos de dimensionamento de máquinas virtuais do Windows se a imagem de VM (SO) estiver definida na lista e o agente não estiver instalado. |Política |
|\[Versão\]prévia: Implantar o agente de Log Analytics para conjuntos de dimensionamento de máquinas virtuais do Linux |Implante o agente de Log Analytics para conjuntos de dimensionamento de máquinas virtuais do Linux se a imagem de VM (SO) estiver definida na lista e o agente não estiver instalado. |Política |
|\[Versão\]prévia: Implantar o agente de Log Analytics para conjuntos de dimensionamento de máquinas virtuais do Windows |Implante o agente de Log Analytics para conjuntos de dimensionamento de máquinas virtuais do Windows se a imagem de VM (SO) estiver definida na lista e o agente não estiver instalado. |Política |

Política de autónomo (não incluída com a iniciativa) é descrita aqui:

|Nome |Descrição |Type |
|-----|------------|-----|
|\[Versão\]prévia: Log Analytics do espaço de trabalho de auditoria para VM – relatar incompatibilidade |Relate as VMs como não compatíveis se elas não estiverem registradas no espaço de trabalho Log Analytics especificado na política ou na atribuição de iniciativa. |Política |

### <a name="assign-the-azure-monitor-initiative"></a>Atribuir a iniciativa de Azure Monitor
Para criar a atribuição de política na página de **cobertura da política de Azure monitor para VMs** , siga estas etapas. Para compreender como concluir estes passos, veja [criar uma atribuição de política do portal do Azure](../../governance/policy/assign-policy-portal.md).

Quando você atribui a política ou iniciativa, o escopo selecionado na atribuição pode ser o escopo listado aqui ou um subconjunto dele. Por exemplo, você pode ter criado uma atribuição para a assinatura (escopo de política) e não o grupo de gerenciamento (escopo de cobertura). Nesse caso, a porcentagem de cobertura indicaria as VMs no escopo da política ou da iniciativa dividida pelas VMs no escopo de cobertura. Em outro caso, você pode ter excluído algumas VMs ou grupos de recursos ou uma assinatura do escopo da política. Se estiver em branco, indica que a política ou iniciativa não existe ou você não tem permissões. As informações são fornecidas sob o **status da atribuição**.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

2. No portal do Azure, selecione **Monitor**. 

3. Escolha **máquinas virtuais (versão prévia)** na seção **insights** .
 
4. Selecione a guia **introdução** . Na página, selecione **gerenciar cobertura de política**.

5. Selecione um grupo de gerenciamento ou uma assinatura da tabela. Selecione **escopo** selecionando as reticências (...). No exemplo, um escopo limita a atribuição de política a um agrupamento de máquinas virtuais para imposição.

6. Na página **atribuição de Azure Policy** , ela é preenchida previamente com a iniciativa **habilitar Azure monitor para VMs**. 
    A caixa **nome da atribuição** é preenchida automaticamente com o nome da iniciativa, mas você pode alterá-la. Você também pode adicionar uma descrição opcional. A caixa **atribuído por** é preenchida automaticamente com base em quem está conectado. Esse valor é opcional.

7. (Opcional) Para remover um ou mais recursos do âmbito, selecione **exclusões**.

8. Na **área de trabalho do Log Analytics** pendente lista para a região suportada, selecione uma área de trabalho.

   > [!NOTE]
   > Se a área de trabalho está além do escopo da atribuição, conceder *Contribuidor do Log Analytics* permissões para o ID de Principal. a atribuição de política Se você não fizer isso, poderá ver uma falha de implantação como `The client '343de0fe-e724-46b8-b1fb-97090f7054ed' with object id '343de0fe-e724-46b8-b1fb-97090f7054ed' does not have authorization to perform action 'microsoft.operationalinsights/workspaces/read' over scope ...` para conceder acesso, examine [como configurar manualmente a identidade gerenciada](../../governance/policy/how-to/remediate-resources.md#manually-configure-the-managed-identity).
   > 
   >  A caixa de seleção **identidade gerenciada** é marcada porque a iniciativa que está sendo atribuída inclui uma política com o efeito *deployIfNotExists* .
    
9. Na **localização de gerir a identidade** pendente, selecione a região adequada.

10. Selecione **Atribuir**.

Depois de criar a atribuição, a página de **cobertura de política de Azure monitor para VMs** atualiza a cobertura de **atribuição**, o **status de atribuição**, as **VMs em conformidade**e o estado de **conformidade** para refletir as alterações. 

A matriz a seguir mapeia cada Estado de conformidade possível para a iniciativa.  

| Estado de conformidade | Descrição | 
|------------------|-------------|
| **Em conformidade** | Todas as VMs no escopo têm a Log Analytics e os agentes de dependência implantados nelas.|
| **Não compatível** | Nem todas as VMs no escopo têm a Log Analytics e os agentes de dependência implantados e podem exigir correção.|
| **Não iniciado** | Uma nova atribuição foi adicionada. |
| **Proprietário** | Você não tem privilégios suficientes para o grupo de gerenciamento. <sup>1</sup> | 
| **Em branco** | Nenhuma política foi atribuída. | 

<sup>1</sup> se você não tiver acesso ao grupo de gerenciamento, peça ao proprietário para fornecer acesso. Ou então, exiba a conformidade e gerencie atribuições por meio dos grupos de gerenciamento filho ou assinaturas. 

A tabela a seguir mapeia cada possível status de atribuição para a iniciativa.

| Status da atribuição | Descrição | 
|------------------|-------------|
| **Êxito** | Todas as VMs no escopo têm a Log Analytics e os agentes de dependência implantados nelas.|
| **Alerta** | A assinatura não está sob um grupo de gerenciamento.|
| **Não iniciado** | Uma nova atribuição foi adicionada. |
| **Proprietário** | Você não tem privilégios suficientes para o grupo de gerenciamento. <sup>1</sup> | 
| **Em branco** | Não existem VMs ou uma política não está atribuída. | 
| **ação** | Atribua uma política ou edite uma atribuição. | 

<sup>1</sup> se você não tiver acesso ao grupo de gerenciamento, peça ao proprietário para fornecer acesso. Ou então, exiba a conformidade e gerencie atribuições por meio dos grupos de gerenciamento filho ou assinaturas.

## <a name="review-and-remediate-the-compliance-results"></a>Rever e corrigir os resultados de compatibilidade

O exemplo a seguir é para uma VM do Azure, mas também se aplica aos conjuntos de dimensionamento de máquinas virtuais. Para saber como examinar os resultados de conformidade, consulte [identificar resultados](../../governance/policy/assign-policy-portal.md#identify-non-compliant-resources)de não conformidade. Na página **cobertura da política de Azure monitor para VMs** , selecione um grupo de gerenciamento ou uma assinatura da tabela. Selecione **Exibir conformidade** selecionando as reticências (...).   

![Conformidade com a política para VMs do Azure](./media/vminsights-enable-at-scale-policy/policy-view-compliance-01.png)

Com base nos resultados das políticas incluídas na iniciativa, as VMs são relatadas como não compatíveis nos seguintes cenários:

* O agente de Log Analytics ou o agente de dependência não está implantado.  
    Este cenário é comum para um âmbito com VMs existentes. Para atenuá-lo, implante os agentes necessários [Criando tarefas de correção](../../governance/policy/how-to/remediate-resources.md) em uma política não compatível.  
    - \[Versão\]prévia: Implantar o agente de dependência para VMs Linux
    - \[Versão\]prévia: Implantar o agente de dependência para VMs do Windows
    - \[Versão\]prévia: Implantar o agente de Log Analytics para VMs Linux
    - \[Versão\]prévia: Implantar o agente de Log Analytics para VMs do Windows

* A imagem de VM (SO) não é identificada na definição de política.  
    Os critérios da política de implementação incluem apenas VMs que são implementadas a partir de imagens de VM do Azure bem conhecidas. Verifique a documentação para ver se o SO da VM é suportado. Se não é suportada, duplicar a política de implementação e a atualização ou modificá-lo para criar a imagem em conformidade.  
    - \[Versão\]prévia: Implantação do agente de dependência de auditoria – imagem da VM (SO) não listada
    - \[Versão\]prévia: Implantação de agente de Log Analytics de auditoria – imagem de VM (SO) não listada

* VMs não são de iniciar sessão para a área de trabalho do Log Analytics especificada.  
    É possível que algumas VMs no âmbito da iniciativa estão a iniciar sessão a uma área de trabalho do Log Analytics diferente daquele que é especificada na atribuição de política. Essa política é uma ferramenta para identificar quais VMs estão relatando para um espaço de trabalho não compatível.  
    - \[Versão\]prévia: Log Analytics do espaço de trabalho de auditoria para VM – relatar incompatibilidade

## <a name="edit-an-initiative-assignment"></a>Editar uma atribuição de iniciativa

A qualquer momento depois de atribuir uma iniciativa a um grupo de gerenciamento ou assinatura, você pode editá-la para modificar as seguintes propriedades:

- Nome da atribuição
- Descrição
- Atribuído por
- Área de trabalho do Log Analytics
- Exceções

## <a name="next-steps"></a>Passos seguintes

Agora que o monitoramento está habilitado para suas máquinas virtuais, essas informações estão disponíveis para análise com Azure Monitor para VMs. 

- Para saber como usar o recurso de integridade, consulte [exibir Azure monitor para VMs integridade](vminsights-health.md). 
- Para ver dependências de aplicações detetadas, consulte [vista do Azure Monitor para o mapa de VMs](vminsights-maps.md). 
- Para identificar afunilamentos e a utilização geral com o desempenho da VM, consulte [Exibir o desempenho da VM do Azure](vminsights-performance.md). 
- Para ver dependências de aplicações detetadas, consulte [vista do Azure Monitor para o mapa de VMs](vminsights-maps.md).
