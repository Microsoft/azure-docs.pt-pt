---
title: Configurar log analytics espaço de trabalho para insights VM
description: Descreve como criar e configurar o espaço de trabalho Log Analytics utilizado por insights VM.
ms.topic: conceptual
ms.custom: references_regions
author: bwren
ms.author: bwren
ms.date: 12/22/2020
ms.openlocfilehash: 5a0e04772cfc1c9be77c0ad8b32b0e93be8b9f54
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102046725"
---
# <a name="configure-log-analytics-workspace-for-vm-insights"></a>Configurar log analytics espaço de trabalho para insights VM
Os conhecimentos VM recolhem os seus dados a partir de um ou mais espaços de trabalho do Log Analytics no Azure Monitor. Antes dos agentes de bordo, deve criar e configurar um espaço de trabalho. Este artigo descreve os requisitos do espaço de trabalho e configura-o para insights VM.

## <a name="overview"></a>Descrição Geral
Uma única subscrição pode utilizar qualquer número de espaços de trabalho dependendo dos seus requisitos. A única exigência do espaço de trabalho é que esteja localizado num local suportado e configurado com a solução *VMInsights.*

Uma vez configurado o espaço de trabalho, pode utilizar qualquer uma das opções disponíveis para instalar os agentes necessários na máquina virtual e no conjunto de escala de máquina virtual e especificar um espaço de trabalho para que eles enviem os seus dados. Os conhecimentos VM recolherão dados de qualquer espaço de trabalho configurado na sua subscrição.

> [!NOTE]
> Quando ativa insights em VM numa única máquina virtual ou conjunto de escala de máquina virtual utilizando o portal Azure, é-lhe dada a opção de selecionar um espaço de trabalho existente ou criar um novo. A solução *VMInsights* será instalada neste espaço de trabalho se ainda não estiver. Em seguida, pode utilizar este espaço de trabalho para outros agentes.


## <a name="create-log-analytics-workspace"></a>Criar uma área de trabalho do Log Analytics

>[!NOTE]
>As informações descritas nesta secção também são aplicáveis à [solução Mapa de Serviço.](service-map.md) 

Aceder aos espaços de trabalho do Log Analytics no portal Azure a partir do menu **de espaços de trabalho Log Analytics.**

[![Log Anlytics workspaces](media/vminsights-configure-workspace/log-analytics-workspaces.png)](media/vminsights-configure-workspace/log-analytics-workspaces.png#lightbox)

Pode criar um novo espaço de trabalho log Analytics utilizando qualquer um dos seguintes métodos. Consulte [a implementação de Registos do Monitor Azure](../logs/design-logs-deployment.md) para obter orientações sobre a determinação do número de espaços de trabalho que deve utilizar no seu ambiente e como conceber a sua estratégia de acesso.


* [Portal do Azure](../logs/quick-create-workspace.md)
* [CLI do Azure](../logs/quick-create-workspace-cli.md)
* [PowerShell](../logs/powershell-workspace-configuration.md)
* [Azure Resource Manager](../logs/resource-manager-workspace.md)

## <a name="supported-regions"></a>Regiões suportadas
Os conhecimentos VM suportam um espaço de trabalho Log Analytics em qualquer uma das [regiões suportadas pelo Log Analytics,](https://azure.microsoft.com/global-infrastructure/services/?products=monitor&regions=all) exceto para os seguintes:

- Alemanha Centro-Oeste
- Coreia do Sul Central

>[!NOTE]
>Pode monitorizar os VMs do Azure em qualquer região. Os VMs em si não se limitam às regiões apoiadas pelo espaço de trabalho Log Analytics.

## <a name="azure-role-based-access-control"></a>Controlo de acesso baseado em funções do Azure
Para ativar e aceder às funcionalidades em insights VM, tem de ter o [papel de colaborador](../logs/manage-access.md#manage-access-using-azure-permissions) do Log Analytics no espaço de trabalho. Para visualizar dados de desempenho, saúde e mapa, deve ter o [papel de leitor de monitorização](../roles-permissions-security.md#built-in-monitoring-roles) para o VM Azure. Para obter mais informações sobre como controlar o acesso a um espaço de trabalho do Log Analytics, consulte [Gerir espaços de trabalho.](../logs/manage-access.md)

## <a name="add-vminsights-solution-to-workspace"></a>Adicionar solução VMInsights ao espaço de trabalho
Antes de um espaço de trabalho Log Analytics poder ser utilizado com insights VM, deve ter a solução *VMInsights* instalada. Os métodos de configuração do espaço de trabalho são descritos nas seguintes secções.

> [!NOTE]
> Quando adicionar a solução *VMInsights* ao espaço de trabalho, todas as máquinas virtuais existentes ligadas ao espaço de trabalho começarão a enviar dados para a InsightsMetrics. Os dados relativos aos outros tipos de dados não serão recolhidos até que adicione o Agente de Dependência às máquinas virtuais existentes ligadas ao espaço de trabalho.

### <a name="azure-portal"></a>Portal do Azure
Existem três opções para configurar um espaço de trabalho existente utilizando o portal Azure. Cada um é descrito abaixo.

Para configurar um único espaço de trabalho, escolha a opção **Máquinas Virtuais** no menu **Azure Monitor,** selecione as **outras opções de embarque**, e, em seguida, **configure um espaço de trabalho**. Selecione uma subscrição e um espaço de trabalho e, em seguida, clique em **Configurar**.

[![Configurar a área de trabalho](../vm/media/vminsights-enable-policy/configure-workspace.png)](../vm/media/vminsights-enable-policy/configure-workspace.png#lightbox)

Para configurar vários espaços de trabalho, selecione o **separador de configuração do Espaço de Trabalho** no menu **Máquinas Virtuais** no menu **Monitor** no portal Azure. Desa estarda os valores do filtro para apresentar uma lista de espaços de trabalho existentes. Selecione a caixa ao lado de cada espaço de trabalho para ativar e, em seguida, clique em **Configurar selecionado**.

[![Configuração da área de trabalho](../vm/media/vminsights-enable-policy/workspace-configuration.png)](../vm/media/vminsights-enable-policy/workspace-configuration.png#lightbox)


Quando ativa insights em VM numa única máquina virtual ou conjunto de escala de máquina virtual utilizando o portal Azure, é-lhe dada a opção de selecionar um espaço de trabalho existente ou criar um novo. A solução *VMInsights* será instalada neste espaço de trabalho se ainda não estiver. Em seguida, pode utilizar este espaço de trabalho para outros agentes.

[![Ativar um único VM no portal](../vm/media/vminsights-enable-portal/enable-vminsights-vm-portal.png)](../vm/media/vminsights-enable-portal/enable-vminsights-vm-portal.png#lightbox)


### <a name="resource-manager-template"></a>Modelo do Resource Manager
Os modelos do Gestor de Recursos Azure para informações VM são fornecidos num ficheiro de arquivo (.zip) que pode [descarregar a partir do nosso repo GitHub](https://aka.ms/VmInsightsARMTemplates). Isto inclui um modelo chamado **ConfigureWorkspace** que configura um espaço de trabalho Log Analytics para insights VM. Você implementa este modelo usando qualquer um dos métodos padrão, incluindo os comandos PowerShell e CLI da amostra abaixo: 

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli
az deployment group create --name ConfigureWorkspace --resource-group my-resource-group --template-file CreateWorkspace.json  --parameters workspaceResourceId='/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/my-resource-group/providers/microsoft.operationalinsights/workspaces/my-workspace' workspaceLocation='eastus'

```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```powershell
New-AzResourceGroupDeployment -Name ConfigureWorkspace -ResourceGroupName my-resource-group -TemplateFile ConfigureWorkspace.json -workspaceResourceId /subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/my-resource-group/providers/microsoft.operationalinsights/workspaces/my-workspace -location eastus
```

---



## <a name="next-steps"></a>Passos seguintes
- Consulte [os agentes a bordo para obter informações sobre](vminsights-enable-overview.md) o VM para ligar agentes a insights VM.
- Consulte [soluções de monitorização de destino no Azure Monitor (Preview)](../insights/solution-targeting.md) para limitar a quantidade de dados enviados de uma solução para o espaço de trabalho.
