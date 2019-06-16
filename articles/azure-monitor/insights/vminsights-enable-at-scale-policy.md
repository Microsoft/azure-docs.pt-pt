---
title: Ativar o Azure Monitor para VMs com a política do Azure | Documentos da Microsoft
description: Este artigo descreve como ativar o Azure Monitor para as VMs para várias máquinas virtuais do Azure ou conjuntos de dimensionamento de máquina virtual utilizando a política do Azure.
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
ms.openlocfilehash: fda79a7ea361a6b44798d18b79ffd763055087a7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67122643"
---
# <a name="enable-azure-monitor-for-vms-preview-by-using-azure-policy"></a>Ativar o Azure Monitor para VMs (pré-visualização) com a política do Azure

Este artigo explica como ativar o Azure Monitor para VMs (pré-visualização) para máquinas virtuais do Azure ou conjuntos de dimensionamento de máquina virtual utilizando a política do Azure. No final deste processo, terá com êxito configurado a ativar o Log Analytics e os agentes de dependência e identificámos as máquinas virtuais que não estão em conformidade.

Para descobrir, gerir e ativar o Azure Monitor para as VMs para todas as suas máquinas virtuais do Azure ou os conjuntos de dimensionamento de máquinas virtuais, pode utilizar a política do Azure ou do Azure PowerShell. O Azure Policy é o método que é recomendado porque pode gerir as definições de política para efetivamente regular as suas subscrições para garantir a conformidade consistente e automática que ative o recentemente aprovisionado VMs. Estas definições de política:

* Implemente o agente Log Analytics e o agente de dependência.
* Relatório sobre os resultados de compatibilidade.
* Remediar para VMs não compatíveis.

Se tiver interesse em realizar essas tarefas com o Azure PowerShell ou a um modelo Azure Resource Manager, veja [ativar o Azure Monitor para VMs (pré-visualização) com modelos do Azure PowerShell ou do Azure Resource Manager](vminsights-enable-at-scale-powershell.md).

## <a name="manage-policy-coverage-feature-overview"></a>Descrição geral da funcionalidade de abrangência da apólice de gerir

Originalmente, a experiência com o Azure Policy para gerir e implementar as definições de política para o Azure Monitor para VMs foi feita exclusivamente do Azure Policy. A funcionalidade de abrangência da apólice gerir torna mais simples e fácil de descobrir, gerir e ativar à escala a **ativar o Azure Monitor para VMs** iniciativa, que inclui as definições de política mencionadas anteriormente. Pode aceder a esta nova funcionalidade do **começar** separador no Azure Monitor para as VMs. Selecione **gerir abrangência da apólice** para abrir o **Azure Monitor para VMs abrangência da apólice** página.

![O Azure Monitor da guia de introdução às VMs](./media/vminsights-enable-at-scale-policy/get-started-page-01.png)

A partir daqui, pode verificar e gerenciar cobertura para a iniciativa em seus grupos de gestão e subscrições. Também poderá perceber o número de VMs existe em cada um dos grupos de gestão e as subscrições e o respetivo estado de conformidade.

![O Azure Monitor para a página de política de gerir VMs](./media/vminsights-enable-at-scale-policy/manage-policy-page-01.png)

Estas informações são úteis para ajudar a planejar e executar o seu cenário de Governança para o Azure Monitor para VMs a partir de um local central. Embora a política do Azure fornece uma visão de conformidade, quando uma política ou iniciativa é atribuída a um âmbito, com esta nova página pode detetar em que a política ou iniciativa não foi atribuída e atribua-a no local. Todas as ações como atribuir, ver e editar diretamente o redirecionamento para o Azure Policy. O **do Azure Monitor para VMs abrangência da apólice** página é uma experiência de expandida e integrada para apenas a iniciativa **ativar o Azure Monitor para VMs**.

Nesta página, também pode configurar sua área de trabalho do Log Analytics para o Azure Monitor para as VMs, que:

- Instala as soluções de mapa do serviço de instalação e informações de infraestrutura.
- Permite que os contadores de desempenho do sistema operativo utilizados pelos gráficos de desempenho, de pastas de trabalho e de suas consultas de registo personalizado e alertas.

![Área de trabalho de configurar o Azure Monitor para VMs](./media/vminsights-enable-at-scale-policy/manage-policy-page-02.png)

Esta opção não está relacionado com quaisquer ações de política. Está disponível para fornecer uma forma fácil para satisfazer os [pré-requisitos](vminsights-enable-overview.md) necessário para ativar o Azure Monitor para as VMs.  

### <a name="what-information-is-available-on-this-page"></a>As informações que estão disponíveis nesta página?
A tabela seguinte fornece uma análise detalhada das informações que são apresentadas na página de cobertura de política e como interpretar.

| Função | Descrição | 
|----------|-------------| 
| **Âmbito** | Grupo de gestão e as subscrições que tiver ou herdada acesso a capacidade de desagregar a hierarquia de grupo de gestão.|
| **Função** | Sua função para o escopo, que poderá ser leitor, proprietário ou contribuinte. Em alguns casos, poderá aparecer em branco para indicar que poderá ter acesso à subscrição, mas não para o grupo de gestão que pertence. Informações nas outras colunas variam consoante o seu cargo. A função é a chave para determinar quais dados pode ver e ações que pode efetuar em termos de atribuir políticas ou de iniciativas (proprietário), edição dos mesmos ou ao visualizar a conformidade. |
| **Total de VMs** | Número de VMs nesse âmbito. Para um grupo de gestão, é uma soma de VMs aninhadas em subscrições ou do grupo de gestão do subordinado. |
| **Cobertura de atribuição** | Percentagem de VMs que estão abrangidas pela política ou iniciativa. |
| **Estado da atribuição** | Informações sobre o estado da atribuição de política ou iniciativa. |
| **VMs em conformidade** | Número de VMs que estão em conformidade com a política ou iniciativa. Para a iniciativa **ativar o Azure Monitor para VMs**, este é o número de VMs que tenham o agente do Log Analytics e o agente de dependência. Em alguns casos, poderá ser apresentada em branco devido a não atribuição, não existem VMs ou sem permissões suficientes. São fornecidas informações sob **estado de conformidade**. |
| **Conformidade** | O número de conformidade geral é a soma dos recursos distintos que estão em conformidade divididos pela soma de todos os recursos distintos. |
| **Estado de Compatibilidade** | Informações sobre o estado de conformidade para a atribuição de política ou iniciativa.|

Quando atribui a política ou iniciativa, o âmbito selecionado na atribuição de pode ser o âmbito listados ou um subconjunto do mesmo. Por exemplo, pode ter criado uma atribuição para uma subscrição (âmbito da política) e não um grupo de gestão (escopo de cobertura). Neste caso, o valor de **cobertura de atribuição** indica as VMs na política ou iniciativa âmbito dividido pelas VMs no âmbito de cobertura. Em outro caso, poderá excluiu algumas VMs, grupos de recursos ou uma subscrição de âmbito de política. Se o valor está em branco, indica que a política ou iniciativa não existe ou não tem permissão. São fornecidas informações sob **estado de atribuição**.

## <a name="enable-by-using-azure-policy"></a>Ativar através da política do Azure

Para ativar o Azure Monitor para VMs com a política do Azure no seu inquilino:

- Atribuir iniciativa a um âmbito: grupo de gestão, subscrição ou grupo de recursos.
- Rever e corrigir os resultados de compatibilidade.

Para obter mais informações sobre a atribuição de política do Azure, consulte [descrição geral da política do Azure](../../governance/policy/overview.md#policy-assignment) e reveja a [descrição geral de grupos de gestão](../../governance/management-groups/index.md) antes de continuar.

### <a name="policies-for-azure-vms"></a>Políticas para as VMs do Azure

As definições de política para uma VM do Azure estão listadas na tabela seguinte.

|Name |Descrição |Tipo |
|-----|------------|-----|
|[Pré-visualização]: Ativar o Azure Monitor para VMs |Ative o Azure Monitor para as máquinas virtuais no âmbito especificado (grupo de gestão, subscrição ou grupo de recursos). Aceita a área de trabalho do Log Analytics como um parâmetro. |Iniciativa |
|[Pré-visualização]: Auditar a implementação de agente de dependência – imagem de VM (SO) não listada |Relatórios de VMs como não conforme se a imagem de VM (SO) não está definida na lista e o agente não está instalado. |Política |
|[Pré-visualização]: Auditar a implementação do agente do Log Analytics – imagem de VM (SO) não listada |Relatórios de VMs como não conforme se a imagem de VM (SO) não está definida na lista e o agente não está instalado. |Política |
|[Pré-visualização]: Implementar o agente de dependência para VMs do Linux |Implemente o agente de dependência para VMs do Linux se a imagem de VM (SO) é definida na lista e o agente não está instalado. |Política |
|[Pré-visualização]: Implementar o agente de dependência para VMs do Windows |Implemente o agente de dependência para VMs do Windows se a imagem de VM (SO) é definida na lista e o agente não está instalado. |Política |
|[Pré-visualização]: Implementar o agente do Log Analytics para VMs do Linux |Implemente o agente do Log Analytics para VMs do Linux se a imagem de VM (SO) é definida na lista e o agente não está instalado. |Política |
|[Pré-visualização]: Implementar o agente do Log Analytics para VMs do Windows |Implemente o agente do Log Analytics para VMs do Windows se a imagem de VM (SO) é definida na lista e o agente não está instalado. |Política |

### <a name="policies-for-azure-virtual-machine-scale-sets"></a>Conjuntos de dimensionamento de políticas de máquina virtual do Azure

As definições de política para um conjunto de dimensionamento de máquina virtual do Azure estão listadas na tabela seguinte.

|Name |Descrição |Tipo |
|-----|------------|-----|
|[Pré-visualização]: Ativar o Azure Monitor para conjuntos de dimensionamento de máquina virtual |Ative o Azure Monitor para os conjuntos de dimensionamento de máquina virtual no âmbito especificado (grupo de gestão, subscrição ou grupo de recursos). Aceita a área de trabalho do Log Analytics como um parâmetro. Nota: Se a sua política de atualização de conjunto de dimensionamento está definida como Manual, aplique a extensão de todas as VMS no conjunto de chamando atualização nos mesmos. Na CLI, este é az vmss update-instances. |Iniciativa |
|[Pré-visualização]: Auditar dependência implementação do agente em conjuntos de dimensionamento de máquinas virtuais – imagem de VM (SO) não listada |Relatórios de dimensionamento de máquinas virtuais definido como não conforme se a imagem de VM (SO) não está definida na lista e o agente não está instalado. |Política |
|[Pré-visualização]: Auditar a implementação do agente do Log Analytics em conjuntos de dimensionamento de máquinas virtuais – imagem de VM (SO) não listada |Relatórios de dimensionamento de máquinas virtuais definido como não conforme se a imagem de VM (SO) não está definida na lista e o agente não está instalado. |Política |
|[Pré-visualização]: Implementar o agente de dependência para conjuntos de dimensionamento de máquinas virtuais do Linux |Implemente o agente de dependência para Linux em conjuntos de dimensionamento de máquina virtual se a imagem de VM (SO) é definida na lista e o agente não está instalado. |Política |
|[Pré-visualização]: Implementar o agente de dependência para conjuntos de dimensionamento de máquinas virtuais do Windows |Implemente o agente de dependência para conjuntos de dimensionamento de máquina virtual se a imagem de VM (SO) é definida na lista e não está instalado o agente do Windows. |Política |
|[Pré-visualização]: Implementar o agente do Log Analytics para conjuntos de dimensionamento de máquinas virtuais do Linux |Implemente o agente do Log Analytics para Linux em conjuntos de dimensionamento de máquina virtual se a imagem de VM (SO) é definido na lista e o agente não está instalado. |Política |
|[Pré-visualização]: Implementar o agente do Log Analytics para conjuntos de dimensionamento de máquinas virtuais do Windows |Implemente o agente de Log Analytics para Windows conjuntos de dimensionamento de máquina virtual se a imagem de VM (SO) é definida na lista e o agente não está instalado. |Política |

Política de autónomo (não incluída com a iniciativa) é descrita aqui:

|Nome |Descrição |Tipo |
|-----|------------|-----|
|[Pré-visualização]: Área de trabalho do Log Analytics de auditoria para a VM – erro de correspondência de relatório |VMs de relatório como não conforme se eles não estão a iniciar a área de trabalho do Log Analytics especificada na atribuição de política ou iniciativa. |Política |

### <a name="assign-the-azure-monitor-initiative"></a>Atribuir a iniciativa de Azure Monitor
Para criar a atribuição de política do **do Azure Monitor para VMs abrangência da apólice** página, siga estes passos. Para compreender como concluir estes passos, veja [criar uma atribuição de política do portal do Azure](../../governance/policy/assign-policy-portal.md).

Quando atribui a política ou iniciativa, o âmbito selecionado na atribuição de pode ser o âmbito listados aqui ou um subconjunto do mesmo. Por exemplo, pode ter criado uma atribuição para a subscrição (âmbito da política) e não o grupo de gestão (escopo de cobertura). Neste caso, a porcentagem de cobertura indicaria as VMs na política ou iniciativa âmbito dividido pelas VMs no âmbito da cobertura. Em outro caso, poderá excluiu algumas VMs, ou grupos de recursos ou uma subscrição do âmbito de política. Se estiver em branco, indica que a política ou iniciativa não existe ou não tem permissões. São fornecidas informações sob **estado de atribuição**.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

2. No portal do Azure, selecione **Monitor**. 

3. Escolher **máquinas virtuais (pré-visualização)** no **Insights** secção.
 
4. Selecione o **começar** separador. Na página, selecione **abrangência da apólice gerir**.

5. Selecione um grupo de gestão ou uma subscrição da tabela. Selecione **âmbito** ao selecionar as reticências (...). No exemplo, um âmbito limita a atribuição de política para um agrupamento de máquinas virtuais para a imposição.

6. Sobre o **atribuição de política do Azure** página, ele tem previamente preenchido com a iniciativa **ativar o Azure Monitor para VMs**. 
    O **nome da atribuição** caixa é preenchida automaticamente com o nome da iniciativa, mas pode alterá-lo. Também pode adicionar uma descrição opcional. O **atribuído pelo** caixa é preenchida automaticamente com base em quem está conectado. Este valor é opcional.

7. (Opcional) Para remover um ou mais recursos do âmbito, selecione **exclusões**.

8. Na **área de trabalho do Log Analytics** pendente lista para a região suportada, selecione uma área de trabalho.

   > [!NOTE]
   > Se a área de trabalho está além do escopo da atribuição, conceder *Contribuidor do Log Analytics* permissões para o ID de Principal. a atribuição de política Se não fizer isso, poderá ver uma falha de implementação, como `The client '343de0fe-e724-46b8-b1fb-97090f7054ed' with object id '343de0fe-e724-46b8-b1fb-97090f7054ed' does not have authorization to perform action 'microsoft.operationalinsights/workspaces/read' over scope ...` para conceder acesso, reveja [como configurar manualmente a identidade gerida](../../governance/policy/how-to/remediate-resources.md#manually-configure-the-managed-identity).
   > 
   >  O **identidade gerido** caixa de verificação está selecionada porque a iniciativa que está sendo atribuída inclui uma política com o *deployIfNotExists* efeito.
    
9. Na **localização de gerir a identidade** pendente, selecione a região adequada.

10. Selecione **Atribuir**.

Depois de criar a atribuição, o **do Azure Monitor para VMs abrangência da apólice** página atualizações **cobertura de atribuição**, **estado da atribuição**, **compatível VMs**, e **estado de conformidade** para refletir as alterações. 

A matriz seguinte mapeia cada Estado de compatibilidade possíveis para a iniciativa.  

| Estado de conformidade | Descrição | 
|------------------|-------------|
| **Em conformidade** | Todas as VMs no âmbito têm os agentes do Log Analytics e dependência implementados nos mesmos.|
| **Não conforme** | Nem todas as VMs no âmbito de ter o Log Analytics e agentes de dependência implementado nos mesmos e podem exigir a remediação.|
| **Não foi iniciada** | Foi adicionada uma nova atribuição. |
| **bloqueio** | Não tem privilégios suficientes para o grupo de gestão. <sup>1</sup> | 
| **Em branco** | Nenhuma política é atribuída. | 

<sup>1</sup> se não tiver acesso ao grupo de gestão, peça ao proprietário para fornecer acesso. Ou, ver a conformidade e gerir atribuições por meio de grupos de gestão subordinado ou subscrições. 

A tabela seguinte mapeia cada Estado de atribuição de possíveis para a iniciativa.

| Estado da atribuição | Descrição | 
|------------------|-------------|
| **Êxito** | Todas as VMs no âmbito têm os agentes do Log Analytics e dependência implementados nos mesmos.|
| **Aviso** | A subscrição não está num grupo de gestão.|
| **Não foi iniciada** | Foi adicionada uma nova atribuição. |
| **bloqueio** | Não tem privilégios suficientes para o grupo de gestão. <sup>1</sup> | 
| **Em branco** | Não existem VMs existem ou não foi atribuída uma política. | 
| **ação** | Atribuir uma política ou editar uma atribuição. | 

<sup>1</sup> se não tiver acesso ao grupo de gestão, peça ao proprietário para fornecer acesso. Ou, ver a conformidade e gerir atribuições por meio de grupos de gestão subordinado ou subscrições.

## <a name="review-and-remediate-the-compliance-results"></a>Rever e corrigir os resultados de compatibilidade

O exemplo seguinte é para uma VM do Azure, mas também se aplica a conjuntos de dimensionamento de máquina virtual. Para saber como rever os resultados de compatibilidade, veja [identificar os resultados de não conformidade](../../governance/policy/assign-policy-portal.md#identify-non-compliant-resources). Sobre o **do Azure Monitor para cobertura de política de VMs** , selecione um grupo de gestão ou uma subscrição da tabela. Selecione **ver conformidade** ao selecionar as reticências (...).   

![Conformidade com a política para VMs do Azure](./media/vminsights-enable-at-scale-policy/policy-view-compliance-01.png)

As VMs com base nos resultados das políticas incluídos com a iniciativa, são comunicadas como incompatíveis nos seguintes cenários:

* Agente do log Analytics ou o agente de dependência não está implementada.  
    Este cenário é comum para um âmbito com VMs existentes. Para o resolver, implantar os agentes necessários pelo [criar tarefas de atualização](../../governance/policy/how-to/remediate-resources.md) numa diretiva não conforme.  
    - [Pré-visualização]: Deploy Dependency agent for Linux VMs
    - [Pré-visualização]: Deploy Dependency agent for Windows VMs
    - [Pré-visualização]: Deploy Log Analytics agent for Linux VMs
    - [Pré-visualização]: Deploy Log Analytics agent for Windows VMs

* Imagem de VM (SO) não está identificada na definição de política.  
    Os critérios da política de implementação incluem apenas VMs que são implementadas a partir de imagens de VM do Azure bem conhecidas. Verifique a documentação para ver se o SO da VM é suportado. Se não é suportada, duplicar a política de implementação e a atualização ou modificá-lo para criar a imagem em conformidade.  
    - [Pré-visualização]: Auditar a implementação de agente de dependência – imagem de VM (SO) não listada
    - [Pré-visualização]: Auditar a implementação do agente do Log Analytics – imagem de VM (SO) não listada

* VMs não são de iniciar sessão para a área de trabalho do Log Analytics especificada.  
    É possível que algumas VMs no âmbito da iniciativa estão a iniciar sessão a uma área de trabalho do Log Analytics diferente daquele que é especificada na atribuição de política. Esta política é uma ferramenta para identificar quais as VMs que comunicam com uma área de trabalho não conforme.  
    - [Pré-visualização]: Audit Log Analytics workspace for VM – Report mismatch

## <a name="edit-an-initiative-assignment"></a>Editar uma atribuição de iniciativa

Em qualquer altura depois de atribuir uma iniciativa a um grupo de gestão ou uma subscrição, pode editá-lo para modificar as propriedades seguintes:

- Nome da atribuição
- Descrição
- Atribuído por
- Área de trabalho do log Analytics
- Exceções

## <a name="next-steps"></a>Passos Seguintes

Agora que a monitorização estiver ativada para as suas máquinas virtuais, estas informações são disponíveis para análise com o Azure Monitor para as VMs. 

- Para saber como utilizar a funcionalidade de estado de funcionamento, veja [vista do Azure Monitor de estado de funcionamento de VMs](vminsights-health.md). 
- Para ver dependências de aplicações detetadas, consulte [vista do Azure Monitor para o mapa de VMs](vminsights-maps.md). 
- Para identificar afunilamentos e a utilização geral com o desempenho da sua VM, consulte [o desempenho da VM do Azure de modo de exibição](vminsights-performance.md). 
- Para ver dependências de aplicações detetadas, consulte [vista do Azure Monitor para o mapa de VMs](vminsights-maps.md).
