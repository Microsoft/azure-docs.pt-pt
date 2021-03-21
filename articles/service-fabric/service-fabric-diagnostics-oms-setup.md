---
title: Configurar monitorização com registos do Monitor Azure
description: Saiba como configurar registos do Azure Monitor para visualizar e analisar eventos para monitorizar os seus clusters de Tecido de Serviço Azure.
author: srrengar
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: srrengar
ms.openlocfilehash: ba62ac80b2f8d318d0d13e81e88cc63a8d893a2b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100570343"
---
# <a name="set-up-azure-monitor-logs-for-a-cluster"></a>Configurar registos do Monitor Azure para um cluster

Os registos do Azure Monitor são a nossa recomendação para monitorizar eventos de nível de cluster. Pode configurar o espaço de trabalho do Log Analytics através do Azure Resource Manager, PowerShell ou Azure Marketplace. Se mantiver um modelo atualizado de Gestor de Recursos da sua implementação para utilização futura, utilize o mesmo modelo para configurar o ambiente de registos do Monitor Azure. A implementação via Marketplace é mais fácil se já tiver um cluster implantado com diagnósticos ativados. Se não tiver acesso ao nível de subscrição na conta para a qual está a implementar, utilize o PowerShell ou o modelo de Gestor de Recursos.

> [!NOTE]
> Para configurar registos do Azure Monitor para monitorizar o seu cluster, é necessário ter diagnósticos habilitados para visualizar eventos ao nível do cluster ou ao nível da plataforma. Consulte [como configurar diagnósticos em clusters Windows](service-fabric-diagnostics-event-aggregation-wad.md) e [como configurar diagnósticos em clusters Linux](service-fabric-diagnostics-oms-syslog.md) para mais

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="deploy-a-log-analytics-workspace-by-using-azure-marketplace"></a>Implementar um espaço de trabalho Log Analytics utilizando o Azure Marketplace

Se quiser adicionar um espaço de trabalho log Analytics depois de ter implantado um cluster, vá ao Azure Marketplace no portal e procure o **Service Fabric Analytics**. Esta é uma solução personalizada para implementações de Tecidos de Serviço que tem dados específicos do Service Fabric. Neste processo, criará tanto a solução (o dashboard para visualizar insights) como o espaço de trabalho (a agregação dos dados subjacentes ao cluster).

1. Selecione **Novo** no menu de navegação à esquerda. 

2. Pesquisa por **Service Fabric Analytics**. Selecione o recurso que aparece.

3. Selecione **Criar**.

    ![Análise de Tecido de Serviço no Mercado](media/service-fabric-diagnostics-event-analysis-oms/service-fabric-analytics.png)

4. Na janela de criação de Service Fabric Analytics, **selecione Selecione um espaço de trabalho** para o campo **OMS Workspace** e, em seguida, **Crie um novo espaço de trabalho**. Preencha as entradas necessárias. O único requisito aqui é que a subscrição para o cluster de Tecido de Serviço e o espaço de trabalho é o mesmo. Quando as suas entradas foram validadas, o seu espaço de trabalho começa a ser implantado. A implantação leva apenas alguns minutos.

5. Quando terminar, **selecione Criar** novamente na parte inferior da janela de criação de Service Fabric Analytics. Certifique-se de que o novo espaço de trabalho aparece no espaço **de trabalho da OMS**. Esta ação adiciona a solução ao espaço de trabalho que criou.

Se estiver a utilizar o Windows, continue com os seguintes passos para ligar os registos do Azure Monitor à conta de armazenamento onde os eventos do cluster estão armazenados. 

>[!NOTE]
>A solução Service Fabric Analytics só é suportada para clusters Windows. Para os clusters Linux, consulte o nosso artigo sobre [como configurar registos do Azure Monitor para clusters Linux.](service-fabric-diagnostics-oms-syslog.md)  

### <a name="connect-the-log-analytics-workspace-to-your-cluster"></a>Ligue o espaço de trabalho log Analytics ao seu cluster 

1. O espaço de trabalho precisa de ser ligado aos dados de diagnóstico provenientes do seu cluster. Vá ao grupo de recursos em que criou a solução Service Fabric Analytics. Selecione **ServiceFabric \<nameOfWorkspace\>** e vá para a sua página geral. A partir daí, pode alterar as definições de solução, configurações de espaço de trabalho e aceder ao espaço de trabalho Log Analytics.

2. No menu de navegação à esquerda, em **Fontes de Dados do Espaço De Trabalho,** selecione **registos de contas de armazenamento**.

3. Na página **de registos de conta de Armazenamento,** selecione **Adicionar** na parte superior para adicionar os registos do seu cluster ao espaço de trabalho.

4. Selecione **a conta de Armazenamento** para adicionar a conta adequada criada no seu cluster. Se usou o nome predefinido, a conta de armazenamento é **sfdg \<resourceGroupName\>**. Também pode confirmar isso com o modelo Azure Resource Manager utilizado para implantar o seu cluster, verificando o valor utilizado para **a aplicaçãoDiagnosticsStorageAccountName**. Se o nome não aparecer, desloque-se para baixo e selecione **Carregar mais**. Selecione o nome da conta de armazenamento.

5. Especifique o Tipo de Dados. Desa parte para **eventos de tecido de serviço.**

6. Certifique-se de que a Fonte está automaticamente definida para **WADServiceFabric \* EventTable**.

7. Selecione **OK** para ligar o seu espaço de trabalho aos registos do seu cluster.

    ![Adicione registos de conta de armazenamento aos registos do Monitor Azure](media/service-fabric-diagnostics-event-analysis-oms/add-storage-account.png)

A conta aparece agora como parte dos registos da sua conta de armazenamento nas fontes de dados do seu espaço de trabalho.

Adicionou a solução Service Fabric Analytics num espaço de trabalho Log Analytics que está agora corretamente ligado à plataforma do seu cluster e à tabela de registos de aplicações. Pode adicionar fontes adicionais ao espaço de trabalho da mesma forma.


## <a name="deploy-azure-monitor-logs-with-azure-resource-manager"></a>Implementar registos do Monitor Azure com o Gestor de Recursos Azure

Quando implementa um cluster utilizando um modelo de Gestor de Recursos, o modelo cria um novo espaço de trabalho Log Analytics, adiciona a solução de Tecido de Serviço ao espaço de trabalho e configura-o para ler dados a partir das tabelas de armazenamento apropriadas.

Pode utilizar e modificar [este modelo de amostra](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-OMS-UnSecure) para satisfazer os seus requisitos. Este modelo faz o seguinte

* Cria um cluster de tecido de serviço de nó de 5 node
* Cria um espaço de trabalho log analytics e solução de tecido de serviço
* Configura o agente Log Analytics para recolher e enviar 2 contadores de desempenho da amostra para o espaço de trabalho
* Configuram WAD para recolher tecido de serviço e envia-os para as mesas de armazenamento Azure (WADServiceFabric*EventTable)
* Configura o espaço de trabalho Log Analytics para ler os eventos destas tabelas


Pode implementar o modelo como um upgrade de Gestor de Recursos para o seu cluster utilizando a `New-AzResourceGroupDeployment` API no módulo Azure PowerShell. Um comando de exemplo seria:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "<resourceGroupName>" -TemplateFile "<templatefile>.json" 
``` 

O Gestor de Recursos Azure deteta que este comando é uma atualização para um recurso existente. Só processa as alterações entre o modelo que conduz a implantação existente e o novo modelo fornecido.

## <a name="deploy-azure-monitor-logs-with-azure-powershell"></a>Implementar registos do Monitor Azure com Azure PowerShell

Também pode implementar o seu recurso de análise de registo através do PowerShell utilizando o `New-AzOperationalInsightsWorkspace` comando. Para utilizar este método, certifique-se de que instalou [a Azure PowerShell](/powershell/azure/install-az-ps). Utilize este script para criar um novo espaço de trabalho log analytics e adicione a solução de Tecido de Serviço ao mesmo: 

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

Também pode adicionar outras soluções ou fazer outras modificações no seu espaço de trabalho Log Analytics utilizando o PowerShell. Para saber mais, consulte [os registos do Monitor Azure utilizando o PowerShell](../azure-monitor/logs/powershell-workspace-configuration.md).

## <a name="next-steps"></a>Passos seguintes
* [Coloque o agente Log Analytics](service-fabric-diagnostics-oms-agent.md) nos seus nós para recolher contadores de desempenho e recolher estatísticas e registos de estivadores para os seus contentores
* Familiarize-se com as funcionalidades [de pesquisa e consulta](../azure-monitor/logs/log-query-overview.md) de registo oferecidas como parte dos registos do Azure Monitor
* [Use o View Designer para criar vistas personalizadas em registos do Monitor Azure](../azure-monitor/visualize/view-designer.md)
