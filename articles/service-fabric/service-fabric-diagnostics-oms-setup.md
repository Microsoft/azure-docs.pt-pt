---
title: Configurar o monitoramento com logs de Azure Monitor
description: Saiba como configurar logs de Azure Monitor para visualizar e analisar eventos para monitorar seus clusters de Service Fabric do Azure.
author: srrengar
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: srrengar
ms.openlocfilehash: cf0fab9942dcbb7ee09e554f2c9ba8738f208009
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/02/2020
ms.locfileid: "75609932"
---
# <a name="set-up-azure-monitor-logs-for-a-cluster"></a>Configurar logs de Azure Monitor para um cluster

Azure Monitor logs é nossa recomendação para monitorar eventos no nível do cluster. Pode configurar a área de trabalho do Log Analytics através do Azure Resource Manager, o PowerShell ou o Azure Marketplace. Se você mantiver um modelo atualizado do Resource Manager de sua implantação para uso futuro, use o mesmo modelo para configurar o ambiente de logs de Azure Monitor. Implantação por meio de mercado é mais fácil se já tiver um cluster implementado com o diagnóstico ativado. Se não tiver acesso ao nível da subscrição na conta para a qual estiver a implementar, implemente com o PowerShell ou o modelo do Resource Manager.

> [!NOTE]
> Para configurar logs de Azure Monitor para monitorar o cluster, você precisa ter o diagnóstico habilitado para exibir eventos em nível de cluster ou de plataforma. Consulte a [como configurar diagnósticos em clusters do Windows](service-fabric-diagnostics-event-aggregation-wad.md) e [como configurar diagnósticos em clusters do Linux](service-fabric-diagnostics-oms-syslog.md) para obter mais informações

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="deploy-a-log-analytics-workspace-by-using-azure-marketplace"></a>Implementar uma área de trabalho do Log Analytics com o Azure Marketplace

Se pretender adicionar uma área de trabalho do Log Analytics depois de implementar um cluster, vá para o Azure Marketplace no portal e procure **análise do Service Fabric**. Esta é uma solução personalizada para implementações do Service Fabric, que tem dados específicos para o Service Fabric. Este processo irá criar a solução (o dashboard para ver informações) e a área de trabalho (a agregação dos dados de cluster subjacente).

1. Selecione **New** no menu de navegação esquerdo. 

2. Procure **análise de recursos de infraestrutura do serviço**. Selecione o recurso que aparece.

3. Selecione **Criar**.

    ![Análise do Service Fabric no Marketplace](media/service-fabric-diagnostics-event-analysis-oms/service-fabric-analytics.png)

4. Na janela de criação de análise do Service Fabric, selecione **Selecione uma área de trabalho** para o **área de trabalho OMS** campo e, em seguida **criar uma nova área de trabalho**. Preencha as entradas necessárias. O único requisito aqui é que a subscrição para o cluster do Service Fabric e a área de trabalho é o mesmo. Quando as entradas foram validadas, sua área de trabalho inicie a implementação. A implementação demora apenas alguns minutos.

5. Quando terminar, selecione **criar** novamente na parte inferior da janela de criação de análise do Service Fabric. Certifique-se de que a área de trabalho nova é apresentada debaixo **área de trabalho OMS**. Esta ação adiciona a solução para a área de trabalho que criou.

Se você estiver usando o Windows, continue com as etapas a seguir para conectar Azure Monitor logs à conta de armazenamento em que os eventos de cluster estão armazenados. 

>[!NOTE]
>Há suporte para a solução Análise do Service Fabric apenas para clusters do Windows. Para clusters do Linux, confira nosso artigo sobre [como configurar logs de Azure monitor para clusters do Linux](service-fabric-diagnostics-oms-syslog.md).  

### <a name="connect-the-log-analytics-workspace-to-your-cluster"></a>Ligar a área de trabalho do Log Analytics ao seu cluster 

1. A área de trabalho precisa de estar ligado para os dados de diagnóstico originários do seu cluster. Vá para o grupo de recursos em que criou a solução de análise do Service Fabric. Selecione **ServiceFabric\<nameOfWorkspace\>**  e vá para a página de descrição geral. A partir daí, pode alterar as definições de solução, as definições de área de trabalho e aceder a área de trabalho do Log Analytics.

2. No menu de navegação esquerdo, sob **origens de dados de área de trabalho**, selecione **registos de contas de armazenamento**.

3. Sobre o **registos de conta de armazenamento** página, selecione **Add** na parte superior para adicionar registos do seu cluster para a área de trabalho.

4. Selecione **conta de armazenamento** para adicionar a conta apropriada criada no seu cluster. Se utilizou o nome predefinido, a conta de armazenamento for **sfdg\<resourceGroupName\>** . Também pode confirmar isto com o modelo Azure Resource Manager utilizado para implementar o seu cluster, ao verificar o valor utilizado para **applicationDiagnosticsStorageAccountName**. Se o nome não aparecer, desloque para baixo e selecione **carregar mais**. Selecione o nome de conta de armazenamento.

5. Especifique o tipo de dados. Defina-o como **eventos de Service Fabric**.

6. Certifique-se de que a origem é automaticamente definida como **WADServiceFabric\*EventTable**.

7. Selecione **OK** para ligar a sua área de trabalho para registos do seu cluster.

    ![Adicionar logs de conta de armazenamento a logs de Azure Monitor](media/service-fabric-diagnostics-event-analysis-oms/add-storage-account.png)

A conta são agora aparece como parte da sua conta de armazenamento registos nas origens de dados da sua área de trabalho.

Adicionou a solução de análise do Service Fabric numa área de trabalho do Log Analytics que agora está corretamente ligada a plataforma do seu cluster e a tabela de logs do aplicativo. Pode adicionar outras fontes para a área de trabalho da mesma forma.


## <a name="deploy-azure-monitor-logs-with-azure-resource-manager"></a>Implantar logs de Azure Monitor com Azure Resource Manager

Quando implementar um cluster utilizando um modelo do Resource Manager, o modelo cria uma nova área de trabalho do Log Analytics, adiciona a solução de Service Fabric para a área de trabalho e configura-o para ler dados a partir das tabelas de armazenamento adequado.

Pode utilizar e modificar [este modelo de exemplo](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-OMS-UnSecure) para satisfazer os seus requisitos. Este modelo faz o seguinte

* Cria um cluster do Service Fabric de 5 nós
* Cria uma área de trabalho do Log Analytics e a solução de Service Fabric
* Configura o agente do Log Analytics para recolher e enviar 2 contadores de desempenho de exemplo para a área de trabalho
* Configura WAD para recolher o Service Fabric e envia-os para as tabelas de armazenamento do Azure (WADServiceFabric * EventTable)
* Configura a área de trabalho do Log Analytics para ler os eventos a partir dessas tabelas


Você pode implantar o modelo como uma atualização do Resource Manager para o cluster usando a API `New-AzResourceGroupDeployment` no módulo Azure PowerShell. Um comando de exemplo seria:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "<resourceGroupName>" -TemplateFile "<templatefile>.json" 
``` 

O Azure Resource Manager Deteta que este comando é uma atualização de um recurso existente. Só processa as alterações entre o modelo de dirigir a implementação existente e o novo modelo fornecido.

## <a name="deploy-azure-monitor-logs-with-azure-powershell"></a>Implantar logs de Azure Monitor com Azure PowerShell

Você também pode implantar o recurso do log Analytics por meio do PowerShell usando o comando `New-AzOperationalInsightsWorkspace`. Para utilizar este método, certifique-se de que instalou [do Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps). Utilize este script para criar uma nova área de trabalho do Log Analytics e adicionar a solução de Service Fabric para o mesmo: 

```powershell

$SubID = "<subscription ID>"
$ResourceGroup = "<Resource group name>"
$Location = "<Resource group location>"
$WorkspaceName = "<Log Analytics workspace name>"
$solution = "ServiceFabric"

# Sign in to Azure and access the correct subscription
Connect-AzAccount
Select-AzSubscription -SubscriptionId $SubID 

# Create the resource group if needed
try {
    Get-AzResourceGroup -Name $ResourceGroup -ErrorAction Stop
} catch {
    New-AzResourceGroup -Name $ResourceGroup -Location $Location
}

New-AzOperationalInsightsWorkspace -Location $Location -Name $WorkspaceName -Sku Standard -ResourceGroupName $ResourceGroup
Set-AzOperationalInsightsIntelligencePack -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -IntelligencePackName $solution -Enabled $true

```

Quando terminar, siga as etapas na seção anterior para conectar Azure Monitor logs à conta de armazenamento apropriada.

Também pode adicionar outras soluções ou fazer outras modificações a sua área de trabalho do Log Analytics com o PowerShell. Para saber mais, consulte [gerenciar logs de Azure monitor usando o PowerShell](../azure-monitor/platform/powershell-workspace-configuration.md).

## <a name="next-steps"></a>Passos seguintes
* [Implementar o agente do Log Analytics](service-fabric-diagnostics-oms-agent.md) para os nós para recolher contadores de desempenho e recolher estatísticas de docker e registos para os seus contentores
* Familiarize-se com os recursos de [pesquisa de logs e consulta](../log-analytics/log-analytics-log-searches.md) oferecidos como parte dos logs de Azure monitor
* [Usar o designer de exibição para criar exibições personalizadas em logs de Azure Monitor](../azure-monitor/platform/view-designer.md)
