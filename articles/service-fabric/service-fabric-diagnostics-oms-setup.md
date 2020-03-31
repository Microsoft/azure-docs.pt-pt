---
title: Instale a monitorização com os registos do Monitor Azure
description: Aprenda a configurar registos do Azure Monitor para visualizar e analisar eventos para monitorizar os seus clusters de Tecido de Serviço Azure.
author: srrengar
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: srrengar
ms.openlocfilehash: cf0fab9942dcbb7ee09e554f2c9ba8738f208009
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75609932"
---
# <a name="set-up-azure-monitor-logs-for-a-cluster"></a>Configurar registos do Monitor Azure para um cluster

Os registos do Azure Monitor são a nossa recomendação para monitorizar os eventos de nível de cluster. Pode configurar o espaço de trabalho log Analytics através do Azure Resource Manager, PowerShell ou Azure Marketplace. Se mantiver um modelo atualizado do Gestor de Recursos da sua implementação para uso futuro, utilize o mesmo modelo para configurar o ambiente de registos do Monitor Azure. A implantação via Marketplace é mais fácil se já tiver um cluster implantado com diagnósticos ativados. Se não tiver acesso ao nível de subscrição na conta para a qual está a implementar, implemente utilizando o PowerShell ou o modelo de Gestor de Recursos.

> [!NOTE]
> Para configurar os registos do Monitor Azure para monitorizar o seu cluster, é necessário ter diagnósticos habilitados para visualizar eventos ao nível do cluster ou ao nível da plataforma. Consulte [como configurar diagnósticos em clusters Windows](service-fabric-diagnostics-event-aggregation-wad.md) e [como configurar diagnósticos em clusters Linux](service-fabric-diagnostics-oms-syslog.md) para mais

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="deploy-a-log-analytics-workspace-by-using-azure-marketplace"></a>Implementar um espaço de trabalho de Log Analytics utilizando o Azure Marketplace

Se quiser adicionar um espaço de trabalho de Log Analytics depois de ter implantado um cluster, vá ao Azure Marketplace no portal e procure o **Service Fabric Analytics**. Esta é uma solução personalizada para implementações de Tecido de Serviço que tem dados específicos do Tecido de Serviço. Neste processo, irá criar tanto a solução (o dashboard para visualizar insights) como o espaço de trabalho (a agregação dos dados subjacentes ao cluster).

1. Selecione **Novo** no menu de navegação à esquerda. 

2. Pesquisa por Análise de **Tecido de Serviço**. Selecione o recurso que aparece.

3. Selecione **Criar**.

    ![Análise de Tecido de Serviço no Mercado](media/service-fabric-diagnostics-event-analysis-oms/service-fabric-analytics.png)

4. Na janela de criação service Fabric Analytics, **selecione Selecione um espaço** de trabalho para o campo **Workspace OMS** e, em seguida, **crie um novo espaço**de trabalho . Preencha as entradas necessárias. O único requisito aqui é que a subscrição para o cluster Service Fabric e o espaço de trabalho é o mesmo. Quando as suas entradas tiverem sido validadas, o seu espaço de trabalho começa a ser implantado. O destacamento leva apenas alguns minutos.

5. Quando terminar, selecione **Criar** novamente na parte inferior da janela de criação service Fabric Analytics. Certifique-se de que o novo espaço de trabalho aparece sob o **workspace OMS**. Esta ação adiciona a solução ao espaço de trabalho que criou.

Se estiver a utilizar o Windows, continue com os seguintes passos para ligar os registos do Monitor Azure à conta de armazenamento onde os eventos do cluster estão armazenados. 

>[!NOTE]
>A solução Service Fabric Analytics só é suportada para clusters Windows. Para os clusters Linux, consulte o nosso artigo sobre [como configurar registos do Monitor Azure para os clusters Linux](service-fabric-diagnostics-oms-syslog.md).  

### <a name="connect-the-log-analytics-workspace-to-your-cluster"></a>Ligue o espaço de trabalho log Analytics ao seu cluster 

1. O espaço de trabalho precisa de ser ligado aos dados de diagnóstico provenientes do seu cluster. Vá ao grupo de recursos em que criou a solução Service Fabric Analytics. Selecione **ServiceFabric\<\> nomeOfWorkspace** e vá para a sua página geral. A partir daí, pode alterar as definições de solução, configurações do espaço de trabalho e aceder ao espaço de trabalho do Log Analytics.

2. No menu de navegação à esquerda, em Fontes de **Dados do Espaço de Trabalho,** selecione **registos**de contas de armazenamento .

3. Na página de registos da **conta de Armazenamento,** selecione **Adicionar** na parte superior para adicionar os registos do seu cluster ao espaço de trabalho.

4. Selecione **a conta de Armazenamento** para adicionar a conta adequada criada no seu cluster. Se usou o nome predefinido, a conta de armazenamento é **sfdg\<resourceGroupName\>**. Também pode confirmar isso com o modelo do Gestor de Recursos Azure utilizado para implementar o seu cluster, verificando o valor utilizado para **aplicaçõesDiagnosticsStorageAccountName**. Se o nome não aparecer, desloque-se e selecione **Carregar mais**. Selecione o nome da conta de armazenamento.

5. Especifique o Tipo de Dados. Deite-o para eventos de **tecido de serviço.**

6. Certifique-se de que a Fonte está automaticamente definida para **WADServiceFabric\*EventTable**.

7. Selecione **OK** para ligar o seu espaço de trabalho aos registos do seu cluster.

    ![Adicione registos de conta de armazenamento aos registos do Monitor Azure](media/service-fabric-diagnostics-event-analysis-oms/add-storage-account.png)

A conta aparece agora como parte dos registos da sua conta de armazenamento nas fontes de dados do seu espaço de trabalho.

Adicionou a solução Service Fabric Analytics num espaço de trabalho de Log Analytics que está agora corretamente ligado à plataforma e à tabela de registos de aplicações do seu cluster. Pode adicionar fontes adicionais ao espaço de trabalho da mesma forma.


## <a name="deploy-azure-monitor-logs-with-azure-resource-manager"></a>Implementar registos do Monitor Azure com o Gestor de Recursos Azure

Quando implementa um cluster utilizando um modelo de Gestor de Recursos, o modelo cria um novo espaço de trabalho log Analytics, adiciona a solução de Tecido de Serviço ao espaço de trabalho e configura-o para ler dados das tabelas de armazenamento apropriadas.

Pode utilizar e modificar [este modelo](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-OMS-UnSecure) de amostra para satisfazer os seus requisitos. Este modelo faz o seguinte

* Cria um cluster de tecido de serviço de 5 nós
* Cria um espaço de trabalho log Analytics e solução de tecido de serviço
* Configura o agente Log Analytics para recolher e enviar 2 contadores de desempenho de amostra para o espaço de trabalho
* Configures WAD para recolher tecido de serviço e envia-os para mesas de armazenamento Azure (WADServiceFabric*EventTable)
* Configura o espaço de trabalho do Log Analytics para ler os eventos destas tabelas


Pode implementar o modelo como um upgrade do `New-AzResourceGroupDeployment` Gestor de Recursos para o seu cluster utilizando a API no módulo PowerShell Azure. Um comando de exemplo seria:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "<resourceGroupName>" -TemplateFile "<templatefile>.json" 
``` 

O Gestor de Recursos Azure deteta que este comando é uma atualização para um recurso existente. Apenas processa as alterações entre o modelo que conduz a implantação existente e o novo modelo fornecido.

## <a name="deploy-azure-monitor-logs-with-azure-powershell"></a>Implementar registos do Monitor Azure com a Azure PowerShell

Também pode implantar o seu recurso de `New-AzOperationalInsightsWorkspace` análise de registo através do PowerShell utilizando o comando. Para utilizar este método, certifique-se de que instalou [o Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps). Utilize este script para criar um novo espaço de trabalho log Analytics e adicione-lhe a solução De Tecido de Serviço: 

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

Quando terminar, siga os passos na secção anterior para ligar os registos do Monitor Azure à conta de armazenamento apropriada.

Também pode adicionar outras soluções ou fazer outras modificações no seu espaço de trabalho Log Analytics utilizando o PowerShell. Para saber mais, consulte [os registos do Manage Azure Monitor utilizando o PowerShell](../azure-monitor/platform/powershell-workspace-configuration.md).

## <a name="next-steps"></a>Passos seguintes
* [Coloque o agente Log Analytics](service-fabric-diagnostics-oms-agent.md) nos seus nódosos para recolher contadores de desempenho e recolher estatísticas e registos para os seus recipientes
* Familiarize-se com as funcionalidades de [pesquisa de registos e consulta](../log-analytics/log-analytics-log-searches.md) oferecidas como parte dos registos do Monitor Azure
* [Use o Designer de Vistas para criar vistas personalizadas nos registos do Monitor Azure](../azure-monitor/platform/view-designer.md)
