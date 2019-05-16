---
title: Ativar o Azure Monitor para VMs com o Azure Policy | Documentos da Microsoft
description: Este artigo descreve como ativar Azure Monitor para as VMs para várias máquinas virtuais do Azure ou os conjuntos de dimensionamento de máquina virtual com o Azure Policy.
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
ms.openlocfilehash: 9664ad5272ffeb55bd85e3d4c4f4b70d05e97702
ms.sourcegitcommit: bb85a238f7dbe1ef2b1acf1b6d368d2abdc89f10
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/10/2019
ms.locfileid: "65524149"
---
# <a name="enable-azure-monitor-for-vms-preview-using-azure-policy"></a>Ativar o Azure Monitor para VMs (pré-visualização) com o Azure Policy

Este artigo explica como ativar o Azure Monitor para VMs (pré-visualização) para máquinas virtuais do Azure ou os conjuntos de dimensionamento de máquina virtual com o Azure Policy. No final deste processo, terá com êxito configurado a ativar o Log Analytics e os agentes de dependência e identificámos as máquinas virtuais que não estão em conformidade.

Para descobrir, gerir e ativar o Azure Monitor para as VMs para todas as suas máquinas virtuais do Azure ou os conjuntos de dimensionamento de máquinas virtuais, pode utilizar a política do Azure ou do Azure PowerShell. O Azure Policy é o método recomendado, uma vez que pode gerir as definições de política para efetivamente regular as suas subscrições para garantir a conformidade consistente e automática que ative o recentemente aprovisionado VMs. Estas definições de política:

* Implemente o agente Log Analytics e o agente de dependência.
* Relatório sobre os resultados de compatibilidade.
* Remediar para VMs incompatíveis.

Se estiver interessado em conseguir isso com o modelo do Azure PowerShell ou do Azure Resource Manager, veja [ativar com o modelo do Azure PowerShell ou do Resource Manager](vminsights-enable-at-scale-powershell.md). 

## <a name="manage-policy-coverage-feature-overview"></a>Descrição geral da funcionalidade de abrangência da apólice de gerir

Originalmente, a experiência com o Azure Policy para gerir e implementar as definições de política para o Azure Monitor para VMs foi realizada exclusivamente do Azure Policy. Com o **gerir abrangência da apólice** recurso, ela torna mais simples e fácil de descobrir, gerir e ativar à escala a **ativar o Azure Monitor para VMs** iniciativa, que inclui as definições de política mencionei anteriormente. Pode aceder a esta nova funcionalidade do **começar** separador no Azure Monitor para as VMs ao selecionar **gerir abrangência da apólice**. Abre a página de abrangência da apólice de VMs. 

![O Azure Monitor da guia de introdução às VMs](./media/vminsights-enable-at-scale-policy/get-started-page-01.png)

Aqui pode verificar e gerenciar cobertura para a iniciativa em seus grupos de gestão e subscrições, bem como compreender o número de VMs existe em cada um dos grupos de gestão e as subscrições e o respetivo estado de conformidade.   

![O Azure Monitor para a página de política de gerir VMs](./media/vminsights-enable-at-scale-policy/manage-policy-page-01.png)

Estas informações são úteis para planejar e executar o seu cenário de Governança para o Azure Monitor para VMs a partir de um local central. Enquanto a política do Azure fornece uma visão de conformidade, quando uma política/iniciativa é atribuída a um âmbito, com esta nova página pode descobrir onde a/iniciativa de política não está atribuído e atribuí-lo no local. Todas as ações (atribuir, ver, editar) redirecionar para o Azure Policy diretamente. O Azure Monitor para a página de abrangência da apólice de VMs é uma experiência de expandida e integrada para apenas a iniciativa **ativar o Azure Monitor para VMs**. 

A partir desta página também pode configurar sua área de trabalho do Log Analytics para o Azure Monitor para as VMs, que executa o seguinte:

- Instala as soluções de mapa do serviço de instalação e informações de infraestrutura
- Permite que os contadores de desempenho do sistema operativo utilizados pelos gráficos de desempenho, de pastas de trabalho e de suas consultas de registo personalizado e alertas.

![Área de trabalho de configurar o Azure Monitor para VMs](./media/vminsights-enable-at-scale-policy/manage-policy-page-02.png)

Esta opção não está relacionado com quaisquer ações de política e está disponível para fornecer uma forma fácil para satisfazer os [pré-requisitos](vminsights-enable-overview.md) necessário para ativar o Azure Monitor para as VMs.  

### <a name="what-information-is-available-on-this-page"></a>As informações que estão disponíveis nesta página?
A tabela seguinte fornece uma divisão de quais informações são apresentadas na página de cobertura de política e como interpretar.

| Função | Descrição | 
|----------|-------------| 
| **Âmbito** | Grupo de gestão e as subscrições que tiver ou herdada acesso a hierarquia de grupo com capacidade de pesquisar através de gestão.|
| **Função** | Sua função para o âmbito, pode ser leitor proprietário ou contribuinte. Em alguns casos, pode aparecer em branco para indicar que pode ter acesso à subscrição, mas não para o grupo de gestão que pertence. Informações nas outras colunas irão variar consoante o seu cargo, pois é a chave para determinar quais dados pode ver e ações que pode efetuar em termos de atribuição de iniciativa/política (proprietário), edição dos mesmos ou ao visualizar a conformidade. |
| **Total de VMs** | Número de VMs nesse âmbito. Para um grupo de gestão, é uma soma de VMs aninhadas em subscrições e/ou do grupo de gestão do subordinado. |
| **Cobertura de atribuição** | Percentagem de VMs que estão abrangidas por iniciativa/política. |
| **Estado da atribuição** | Nesta coluna, pode encontrar informações sobre o estado da sua política / iniciativa atribuição. |
| **VMs em conformidade** | Número de VMs que estão em conformidade com a política/iniciativa. Para a iniciativa **ativar o Azure Monitor para VMs** este é o número de VMs que tenham o agente do Log Analytics e o agente de dependência. Em alguns casos, pode aparecer em branco devido a não atribuição, ou não existem VMs ou sem permissões suficientes. São fornecidas informações em estado de conformidade. |
| **Conformidade** | O número de conformidade geral é a soma dos recursos distintos que estão em conformidade dividida pela soma de todos os recursos distintos. |
| **Estado de Compatibilidade** | Informações sobre o estado de conformidade para a sua política / iniciativa atribuição.|

Quando atribui a iniciativa/política, o âmbito selecionado na atribuição de pode ser o âmbito listados ou um subconjunto do mesmo. Por exemplo, poderá ter criado uma atribuição para uma subscrição (âmbito da política) e não um grupo de gestão (escopo de cobertura). Neste caso, o valor de **cobertura de atribuição** indicaria dividido por VMs no âmbito da cobertura de âmbito de VMs na política (ou iniciativa). Em outro caso, pode excluiu algumas VMs, grupos de recursos ou uma subscrição de âmbito de política. Se o valor está em branco, indica que o/iniciativa de política não existe ou não tem permissão (as informações são fornecidas no estado de atribuição).

## <a name="enable-using-azure-policy"></a>Ativar com o Azure Policy

Para ativar o Azure Monitor para VMs com a política do Azure no seu inquilino:

- Atribuir iniciativa a um âmbito: grupo de gestão, subscrição ou grupo de recursos
- Rever e corrigir os resultados de compatibilidade

Para obter mais informações sobre a atribuição de política do Azure, consulte [descrição geral da política do Azure](../../governance/policy/overview.md#policy-assignment) e reveja a [descrição geral de grupos de gestão](../../governance/management-groups/index.md) antes de continuar.

### <a name="policies-for-azure-vms"></a>Políticas para as VMs do Azure

As definições de política para uma VM do Azure estão listadas na tabela a seguir:

|Name |Descrição |Type |
|-----|------------|-----|
|[Pré-visualização]: Ativar o Azure Monitor para VMs |Ative o Azure Monitor para as máquinas virtuais (VMs) no âmbito especificado (grupo de gestão, subscrição ou grupo de recursos). Aceita a área de trabalho do Log Analytics como um parâmetro. |Iniciativa |
|[Pré-visualização]: Implementação de agente de dependência de auditoria – imagem de VM (SO) não listados |Relatórios de VMs como não conforme se a imagem de VM (SO) não está definido na lista e o agente não está instalado. |Política |
|[Pré-visualização]: Implementação de agente da análise de registo de auditoria – imagem de VM (SO) não listados |Relatórios de VMs como não conforme se a imagem de VM (SO) não está definido na lista e o agente não está instalado. |Política |
|[Pré-visualização]: Implementar o agente de dependência para VMs do Linux |Implemente o agente de dependência para VMs do Linux, se a imagem de VM (SO) é definido na lista e o agente não está instalado. |Política |
|[Pré-visualização]: Implementar o agente de dependência para VMs do Windows |Implemente o agente de dependência para as VMs do Windows se a imagem de VM (SO) é definido na lista e o agente não está instalado. |Política |
|[Pré-visualização]: Implementar o agente de análise de registo para VMs do Linux |Implemente agente do Log Analytics para VMs do Linux, se a imagem de VM (SO) é definido na lista e o agente não está instalado. |Política |
|[Pré-visualização]: Implementar o agente de análise de registo para VMs do Windows |Implemente o agente de análise de registo para Windows VMs se a imagem de VM (SO) é definido na lista e o agente não está instalado. |Política |

### <a name="policies-for-azure-virtual-machine-scale-sets"></a>Conjuntos de dimensionamento de políticas de máquina virtual do Azure

As definições de política para um conjunto de dimensionamento de máquina virtual do Azure estão listadas na tabela a seguir:

|Name |Descrição |Type |
|-----|------------|-----|
|[Pré-visualização]: Ativar o Azure Monitor para conjuntos de dimensionamento VM (VMSS) |Ative o Azure Monitor para os conjuntos de dimensionamento de máquina virtual no âmbito especificado (gestão de grupo, subscrição ou grupo de recursos). Aceita a área de trabalho do Log Analytics como parâmetro. Nota: se o seu upgradePolicy de conjunto de dimensionamento está definido como Manual, terá de aplicar a extensão de todas as VMS no conjunto de chamando atualização nos mesmos. Na CLI, isso seria az vmss update-instances. |Iniciativa |
|[Pré-visualização]: Auditar a implementação de agente de dependência no VMSS – a imagem de VM (SO) não listados |Dimensionamento de máquinas virtuais de relatórios definido como não conforme se a imagem de VM (SO) não está definido na lista e o agente não está instalado. |Política |
|[Pré-visualização]: Auditar a implementação de agente do Log Analytics no VMSS – a imagem de VM (SO) não listados |Dimensionamento de máquinas virtuais de relatórios definido como não conforme se a imagem de VM (SO) não está definido na lista e o agente não está instalado. |Política |
|[Pré-visualização]: Implementar o agente de dependência para conjuntos de dimensionamento de VM do Linux (VMSS) |Implemente o agente de dependência para o Linux em conjuntos de dimensionamento de máquina virtual se a imagem de VM (SO) é definido na lista e o agente não está instalado. |Política |
|[Pré-visualização]: Implementar o agente de dependência para conjuntos de dimensionamento VM do Windows (VMSS) |Implemente a dependência agente para Windows conjuntos de dimensionamento de máquina virtual se a imagem de VM (SO) é definido na lista e o agente não está instalado. |Política |
|[Pré-visualização]: Implementar o agente de análise de registo para conjuntos de dimensionamento de VM do Linux (VMSS) |Implemente o agente do Log Analytics para o Linux em conjuntos de dimensionamento de máquina virtual se a imagem de VM (SO) é definido na lista e o agente não está instalado. |Política |
|[Pré-visualização]: Implementar o agente de análise de registo para conjuntos de dimensionamento VM do Windows (VMSS) |Implemente o Log Analytics agente para Windows conjuntos de dimensionamento de máquina virtual se a imagem de VM (SO) é definido na lista e o agente não está instalado. |Política |

Política de autónomo (não incluída com a iniciativa) é descrita aqui:

|Nome |Descrição |Type |
|-----|------------|-----|
|[Pré-visualização]: Área de trabalho do auditoria Log Analytics para VM - erro de correspondência de relatório |Relatórios VMs como não conforme se eles não estão a iniciar a área de trabalho do Log Analytics especificada na atribuição de iniciativa de política /. |Política |

### <a name="assign-the-azure-monitor-initiative"></a>Atribuir a iniciativa de Azure Monitor
Para criar a atribuição de política de Monitor do Azure para a página de abrangência da apólice de VMs, execute os seguintes passos. Para compreender como concluir estes passos, veja [criar uma atribuição de política do portal do Azure](../../governance/policy/assign-policy-portal.md).

Quando atribui a iniciativa/política, o âmbito selecionado na atribuição de pode ser o âmbito listados aqui ou um subconjunto do mesmo. Por exemplo, pode criar atribuição para a subscrição (âmbito da política) e não o grupo de gestão (escopo de cobertura) em que % de cobertura maiúsculas indicaria dividido por VMs no âmbito da cobertura de âmbito de VMs na política (ou iniciativa). Em alguns casos, poderá excluiu algumas VMs ou RGs ou subscrição do âmbito da política.  Caso seja em branco, indica que a política de qualquer um dos / iniciativa não existe ou não tem permissões (informações fornecidas no estado de atribuição)  

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

2. No portal do Azure, selecione **Monitor**. 

3. Escolher **máquinas virtuais (pré-visualização)** no **Insights** secção.
 
4. Selecione o **começar** separador e, na página selecionar **gerir abrangência da apólice**.

5. Selecione um grupo de gestão ou de uma subscrição da tabela e selecione **âmbito** ao clicar nas reticências (...).      No nosso exemplo, um âmbito limita a atribuição de política para um agrupamento de máquinas virtuais para a imposição.

6. Sobre o **atribuição de política do Azure** página é previamente preenchido com a iniciativa **ativar o Azure Monitor para VMs**. 
    O **nome da atribuição** caixa é preenchida automaticamente com o nome da iniciativa, mas pode alterá-lo. Também pode adicionar uma descrição opcional. O **atribuído pelo** caixa é preenchida automaticamente com base em quem está conectado e este valor é opcional.

7. (Opcional) Para remover um ou mais recursos do âmbito, selecione **exclusões**.

8. Na **área de trabalho do Log Analytics** pendente lista para a região suportada, selecione uma área de trabalho.

   > [!NOTE]
   > Se a área de trabalho está além do escopo da atribuição, conceder *Contribuidor do Log Analytics* permissões para o ID de Principal. a atribuição de política Se não fizer isso, poderá ver uma falha de implementação, tais como: `The client '343de0fe-e724-46b8-b1fb-97090f7054ed' with object id '343de0fe-e724-46b8-b1fb-97090f7054ed' does not have authorization to perform action 'microsoft.operationalinsights/workspaces/read' over scope ...` Para conceder acesso, reveja [como configurar manualmente a identidade gerida](../../governance/policy/how-to/remediate-resources.md#manually-configure-the-managed-identity).
   > 
   >  O **identidade gerido** caixa de verificação está selecionada, porque a iniciativa que está sendo atribuída inclui uma política com o *deployIfNotExists* efeito.
    
9. Na **localização de gerir a identidade** pendente, selecione a região adequada.

10. Selecione **Atribuir**.

Depois de criar a atribuição, o Azure Monitor para a página de abrangência da apólice VMs atualizará a cobertura de atribuição, o estado de atribuição, VMs de conformidade e estado de conformidade para refletir as alterações. 

A matriz seguinte mapeia cada Estado de compatibilidade possíveis para a iniciativa.  

| Estado de conformidade | Descrição | 
|------------------|-------------|
| **Em conformidade** | Todas as VMs no âmbito têm os agentes do Log Analytics e dependência implementados nos mesmos.|
| **Não conforme** | Nem todas as VMs no âmbito de ter o Log Analytics e agentes de dependência implementado nos mesmos e podem necessitar de remediação.|
| **Não foi iniciada** | Foi adicionada uma nova atribuição. |
| **bloqueio** | Não tem privilégios suficientes para o grupo de gestão. <sup>1</sup> | 
| **Em branco** | Sem política atribuída. | 

<sup>1</sup> se não tiver acesso ao grupo de gestão, peça ao proprietário para fornecer acesso ou a ver a conformidade e a gerir atribuições através de grupos de gestão subordinado ou subscrições. 

A tabela seguinte mapeia cada Estado de atribuição de possíveis para a iniciativa.

| Estado da Atribuição | Descrição | 
|------------------|-------------|
| **Êxito** | Todas as VMs no âmbito têm os agentes do Log Analytics e dependência implementados nos mesmos.|
| **Aviso** | A subscrição não está num grupo de gestão.|
| **Não foi iniciada** | Foi adicionada uma nova atribuição. |
| **bloqueio** | Não tem privilégios suficientes para o grupo de gestão. <sup>1</sup> | 
| **Em branco** | Existem não existem VMs ou política não atribuído. | 
| **ação** | Atribuir políticas ou editar atribuição | 

<sup>1</sup> se não tiver acesso ao grupo de gestão, peça ao proprietário para fornecer acesso ou a ver a conformidade e a gerir atribuições através de grupos de gestão subordinado ou subscrições. 

## <a name="review-and-remediate-the-compliance-results"></a>Rever e corrigir os resultados de compatibilidade

O exemplo seguinte é para uma VM do Azure, mas também se aplica a conjuntos de dimensionamento de máquina virtual. Pode saber como rever os resultados de compatibilidade, lendo [identificar os resultados de não conformidade](../../governance/policy/assign-policy-portal.md#identify-non-compliant-resources). No Monitor do Azure para a página de abrangência da apólice de VMs, selecione um grupo de gestão ou de uma subscrição da tabela e selecione **ver conformidade** ao clicar nas reticências (...).   

![Conformidade com a política para VMs do Azure](./media/vminsights-enable-at-scale-policy/policy-view-compliance-01.png)

As VMs com base nos resultados das políticas incluídos com a iniciativa, são comunicadas como não conforme nos seguintes cenários:

* O log Analytics ou o agente de dependência não está implementado.  
    Este cenário é comum para um âmbito com VMs existentes. Para o resolver, implantar os agentes necessários pelo [criar tarefas de atualização](../../governance/policy/how-to/remediate-resources.md) numa política de conformidade.  
    - [Pré-visualização]: Deploy Dependency Agent for Linux VMs
    - [Pré-visualização]: Deploy Dependency Agent for Windows VMs
    - [Pré-visualização]: Deploy Log Analytics Agent for Linux VMs
    - [Pré-visualização]: Deploy Log Analytics Agent for Windows VMs

* Imagem de VM (SO) não está identificado na definição de política.  
    Os critérios da política de implementação incluem apenas VMs que são implementadas a partir de imagens de VM do Azure bem conhecidas. Verifique a documentação para ver se o SO da VM é suportado. Se não é suportada, duplicar a política de implementação e a atualização ou modificá-lo para criar a imagem em conformidade.  
    - [Pré-visualização]: Implementação de agente de dependência de auditoria – imagem de VM (SO) não listados
    - [Pré-visualização]: Implementação de agente da análise de registo de auditoria – imagem de VM (SO) não listados

* VMs não são de iniciar sessão para a área de trabalho do Log Analytics especificada.  
    É possível que algumas VMs no âmbito da iniciativa estão a iniciar sessão a uma área de trabalho do Log Analytics diferente daquele que é especificada na atribuição de política. Esta política é uma ferramenta para identificar quais as VMs que comunicam com uma área de trabalho de não conformidade.  
    - [Pré-visualização]: Audit Log Analytics Workspace for VM - Report Mismatch

## <a name="edit-initiative-assignment"></a>Editar atribuição iniciativa

Em qualquer altura depois de atribuir uma iniciativa a um grupo de gestão ou uma subscrição, pode editá-lo para modificar as propriedades seguintes:

- Nome da atribuição
- Descrição
- Atribuída pela
- Área de trabalho do Log Analytics
- Exceções

## <a name="next-steps"></a>Passos Seguintes

Agora que a monitorização estiver ativada para as suas máquinas virtuais, estas informações são disponíveis para análise com o Azure Monitor para as VMs. Para saber como utilizar a funcionalidade de estado de funcionamento, veja [vista do Azure Monitor de estado de funcionamento de VMs](vminsights-health.md). Para ver dependências de aplicações detetadas, consulte [vista do Azure Monitor para o mapa de VMs](vminsights-maps.md). Para identificar afunilamentos e a utilização geral com o desempenho de VMs, veja [vista de desempenho da VM do Azure](vminsights-performance.md), ou para ver dependências de aplicações detetadas, consulte [vista de Azure Monitor para o mapa de VMs](vminsights-maps.md).