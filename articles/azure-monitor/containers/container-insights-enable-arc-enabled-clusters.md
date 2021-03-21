---
title: Configure Azure Arc permitiu que kubernetes cluster com insights de contentores | Microsoft Docs
description: Este artigo descreve como configurar a monitorização com insights de contentores no Arco Azure habilitados agrupamentos kubernetes.
ms.topic: conceptual
ms.date: 09/23/2020
ms.openlocfilehash: 307f9d9928042410dc9b4443aba5c019c592980c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101711302"
---
# <a name="enable-monitoring-of-azure-arc-enabled-kubernetes-cluster"></a>Ativar a monitorização de clusters do Kubernetes preparados para o Azure Arc

Os insights de contentores proporcionam uma experiência de monitorização rica para os clusters Azure Kubernetes Service (AKS) e AKS Engine. Este artigo descreve como permitir a monitorização dos seus clusters Kubernetes alojados fora de Azure que estão habilitados com Azure Arc, para obter uma experiência de monitorização semelhante.

Os insights do recipiente podem ser ativados para uma ou mais implementações existentes de Kubernetes usando um script PowerShell ou Bash.

## <a name="supported-configurations"></a>Configurações suportadas

Os insights do contentor suportam a monitorização do Arco Azure ativado kubernetes (pré-visualização) conforme descrito no artigo de [visão geral,](container-insights-overview.md) exceto para as seguintes características:

- Dados ao Vivo (pré-visualização)

O seguinte é oficialmente suportado com insights do contentor:

- As versões de Kubernetes e a política de suporte são as mesmas que as versões [suportadas pela AKS.](../../aks/supported-kubernetes-versions.md)

- Os seguintes tempos de funcionação do contentor são suportados: Estivador, Moby e CRI compatívels com tempos de execução tais como CRI-O e ContainerD.

- Linux OS lançados para os nós de mestre e trabalhador apoiados são: Ubuntu (18.04 LTS e 16.04 LTS).

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de que tem o seguinte:

- Uma área de trabalho do Log Analytics.

    Os insights de contentores suportam um espaço de trabalho Log Analytics nas regiões listadas nos Produtos Azure [por região.](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=monitor) Para criar o seu próprio espaço de trabalho, pode ser criado através do [Azure Resource Manager,](../logs/resource-manager-workspace.md)através do [PowerShell,](../logs/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json)ou no [portal Azure.](../logs/quick-create-workspace.md)

- Para ativar e aceder às funcionalidades em insights de Contentores, é necessário ser membro da função *Azure Contributor* na subscrição Azure, e membro da função [*Dedesema do Log Analytics Contributor*](../logs/manage-access.md#manage-access-using-azure-permissions) do espaço de trabalho Log Analytics configurado com insights de Contentores.

- É membro do [papel do Contribuinte](../../role-based-access-control/built-in-roles.md#contributor) no recurso cluster Azure Arc.

- Para visualizar os dados de monitorização, é membro da permissão de função do [*leitor log Analytics*](../logs/manage-access.md#manage-access-using-azure-permissions) com o espaço de trabalho Log Analytics configurado com insights de Contentores.

- [Cliente HELM](https://helm.sh/docs/using_helm/) a bordo do gráfico de insights do contentor para o cluster kubernetes especificado.

- São necessárias as seguintes informações de configuração de procuração e firewall para que a versão contentorizada do agente Log Analytics para o Linux comunique com o Azure Monitor:

    |Recursos do Agente|Portas |
    |------|---------|
    |`*.ods.opinsights.azure.com` |Porto 443 |
    |`*.oms.opinsights.azure.com` |Porto 443 |
    |`*.dc.services.visualstudio.com` |Porto 443 |

- O agente contentorizado requer `cAdvisor secure port: 10250` kubelet's ou ser aberto em todos os nós do cluster para recolher `unsecure port :10255` métricas de desempenho. Recomendamos que configure `secure port: 10250` no cAdvisor do Kubelet se ainda não estiver configurado.

- O agente contentor exige que sejam especificadas no contentor as seguintes variáveis ambientais, a fim de comunicar com o serviço API da Kubernetes dentro do cluster para recolher dados de inventário - `KUBERNETES_SERVICE_HOST` e `KUBERNETES_PORT_443_TCP_PORT` .

    >[!IMPORTANT]
    >A versão de agente mínimo suportada para monitorizar os clusters Kubernetes ativados pelo Arco é ciprod04162020 ou mais tarde.

- [O PowerShell Core](/powershell/scripting/install/installing-powershell?view=powershell-6&preserve-view=true) é necessário se ativar a monitorização utilizando o método scripted PowerShell.

- [A versão bash 4](https://www.gnu.org/software/bash/) é necessária se ativar a monitorização utilizando o método de escrita Bash.

## <a name="identify-workspace-resource-id"></a>Identificar iD de recursos de espaço de trabalho

Para permitir a monitorização do seu cluster utilizando o script PowerShell ou bash que descarregou anteriormente e integrou-se com um espaço de trabalho log analytics existente, execute os seguintes passos para identificar primeiro o ID completo do seu espaço de trabalho Log Analytics. Isto é necessário para o `workspaceResourceId` parâmetro quando executar o comando para ativar o addon de monitorização contra o espaço de trabalho especificado. Se não tiver um espaço de trabalho para especificar, pode saltar incluindo o `workspaceResourceId` parâmetro e deixar o script criar um novo espaço de trabalho para si.

1. Listar todas as subscrições a que tem acesso utilizando o seguinte comando:

    ```azurecli
    az account list --all -o table
    ```

    A saída assemelha-se-á ao seguinte:

    ```azurecli
    Name                                  CloudName    SubscriptionId                        State    IsDefault
    ------------------------------------  -----------  ------------------------------------  -------  -----------
    Microsoft Azure                       AzureCloud   0fb60ef2-03cc-4290-b595-e71108e8f4ce  Enabled  True
    ```

    Copie o valor para **SubscriçãoId**.

2. Mude para a subscrição que hospeda o espaço de trabalho Log Analytics utilizando o seguinte comando:

    ```azurecli
    az account set -s <subscriptionId of the workspace>
    ```

3. O exemplo a seguir mostra a lista de espaços de trabalho nas suas subscrições no formato JSON predefinido.

    ```
    az resource list --resource-type Microsoft.OperationalInsights/workspaces -o json
    ```

    Na saída, encontre o nome do espaço de trabalho e, em seguida, copie o ID completo do recurso daquele espaço de trabalho Log Analytics sob o **ID** de campo .

## <a name="enable-monitoring-using-powershell"></a>Permitir a monitorização através do PowerShell

1. Faça o download e guarde o script para uma pasta local que configura o seu cluster com o add-on de monitorização utilizando os seguintes comandos:

    ```powershell
    Invoke-WebRequest https://aka.ms/enable-monitoring-powershell-script -OutFile enable-monitoring.ps1
    ```

2. Configure a `$azureArcClusterResourceId` variável definindo os valores correspondentes para `subscriptionId` , e representando o `resourceGroupName` `clusterName` ID de recurso do seu recurso de cluster Kubernetes ativado pelo Arco Azure.

    ```powershell
    $azureArcClusterResourceId = "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Kubernetes/connectedClusters/<clusterName>"
    ```

3. Configure a `$kubeContext` variável com o **contexto kube** do seu cluster executando o comando `kubectl config get-contexts` . Se quiser utilizar o contexto atual, desa um valor para `""` .

    ```powershell
    $kubeContext = "<kubeContext name of your k8s cluster>"
    ```

4. Se pretender utilizar o espaço de trabalho Azure Monitor Log Analytics, configuure a variável `$logAnalyticsWorkspaceResourceId` com o valor correspondente representando o ID de recurso do espaço de trabalho. Caso contrário, desacorde a variável `""` e o script cria um espaço de trabalho predefinido no grupo de recursos predefinidos da subscrição do cluster se já não existir na região. O espaço de trabalho padrão criado assemelha-se ao formato *defaultWorkspace- \<SubscriptionID> - \<Region>*.

    ```powershell
    $logAnalyticsWorkspaceResourceId = "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/microsoft.operationalinsights/workspaces/<workspaceName>"
    ```

5. Se o seu cluster Kubernetes ativado pelo Arco comunicar através de um servidor proxy, configuure a variável `$proxyEndpoint` com o URL do servidor proxy. Se o cluster não comunicar através de um servidor proxy, então pode definir o valor para `""` .  Para mais informações, consulte [o ponto final de proxy Configure mais](#configure-proxy-endpoint) tarde neste artigo.

6. Executar o seguinte comando para ativar a monitorização.

    ```powershell
    .\enable-monitoring.ps1 -clusterResourceId $azureArcClusterResourceId -kubeContext $kubeContext -workspaceResourceId $logAnalyticsWorkspaceResourceId -proxyEndpoint $proxyEndpoint
    ```

Depois de ter ativado a monitorização, pode demorar cerca de 15 minutos até poder ver as métricas de saúde para o cluster.

### <a name="using-service-principal"></a>Usando o principal de serviço
O script *enable-monitoring.ps1* utiliza o login do dispositivo interativo. Se preferir login não interativo, pode utilizar um principal de serviço existente ou criar um novo que tenha as permissões necessárias, conforme descrito nos [Pré-Requisitos.](#prerequisites) Para utilizar o principal do serviço, terá de passar $servicePrincipalClientId, $servicePrincipalClientSecret e $tenantId parâmetros com valores do principal de serviço que pretendeu utilizar para *enable-monitoring.ps1* script.

```powershell
$subscriptionId = "<subscription Id of the Azure Arc connected cluster resource>"
$servicePrincipal = New-AzADServicePrincipal -Role Contributor -Scope "/subscriptions/$subscriptionId"
```

A atribuição de funções abaixo só é aplicável se estiver a utilizar o espaço de trabalho do Log Analytics existente numa Subscrição Azure diferente do recurso Cluster Conectado Arc K8s.

```powershell
$logAnalyticsWorkspaceResourceId = "<Azure Resource Id of the Log Analytics Workspace>" # format of the Azure Log Analytics workspace should be /subscriptions/<subId>/resourcegroups/<rgName>/providers/microsoft.operationalinsights/workspaces/<workspaceName>
New-AzRoleAssignment -RoleDefinitionName 'Log Analytics Contributor'  -ObjectId $servicePrincipal.Id -Scope  $logAnalyticsWorkspaceResourceId

$servicePrincipalClientId =  $servicePrincipal.ApplicationId.ToString()
$servicePrincipalClientSecret = [System.Net.NetworkCredential]::new("", $servicePrincipal.Secret).Password
$tenantId = (Get-AzSubscription -SubscriptionId $subscriptionId).TenantId
```

Por exemplo:

```powershell
.\enable-monitoring.ps1 -clusterResourceId $azureArcClusterResourceId -servicePrincipalClientId $servicePrincipalClientId -servicePrincipalClientSecret $servicePrincipalClientSecret -tenantId $tenantId -kubeContext $kubeContext -workspaceResourceId $logAnalyticsWorkspaceResourceId -proxyEndpoint $proxyEndpoint
```



## <a name="enable-using-bash-script"></a>Ativar usando o script de bash

Execute os seguintes passos para permitir a monitorização utilizando o roteiro de bash fornecido.

1. Faça o download e guarde o script para uma pasta local que configura o seu cluster com o add-on de monitorização utilizando os seguintes comandos:

    ```bash
    curl -o enable-monitoring.sh -L https://aka.ms/enable-monitoring-bash-script
    ```

2. Configure a `azureArcClusterResourceId` variável definindo os valores correspondentes para `subscriptionId` , e representando o `resourceGroupName` `clusterName` ID de recurso do seu recurso de cluster Kubernetes ativado pelo Arco Azure.

    ```bash
    export azureArcClusterResourceId="/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Kubernetes/connectedClusters/<clusterName>"
    ```

3. Configure a `kubeContext` variável com o **contexto kube** do seu cluster executando o comando `kubectl config get-contexts` . Se quiser utilizar o contexto atual, desa um valor para `""` .

    ```bash
    export kubeContext="<kubeContext name of your k8s cluster>"
    ```

4. Se pretender utilizar o espaço de trabalho Azure Monitor Log Analytics, configuure a variável `logAnalyticsWorkspaceResourceId` com o valor correspondente representando o ID de recurso do espaço de trabalho. Caso contrário, desacorde a variável `""` e o script cria um espaço de trabalho predefinido no grupo de recursos predefinidos da subscrição do cluster se já não existir na região. O espaço de trabalho padrão criado assemelha-se ao formato *defaultWorkspace- \<SubscriptionID> - \<Region>*.

    ```bash
    export logAnalyticsWorkspaceResourceId="/subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/microsoft.operationalinsights/workspaces/<workspaceName>"
    ```

5. Se o seu cluster Kubernetes ativado pelo Arco comunicar através de um servidor proxy, configuure a variável `proxyEndpoint` com o URL do servidor proxy. Se o cluster não comunicar através de um servidor proxy, então pode definir o valor para `""` . Para mais informações, consulte [o ponto final de proxy Configure mais](#configure-proxy-endpoint) tarde neste artigo.

6. Para ativar a monitorização no seu cluster, existem diferentes comandos fornecidos com base no seu cenário de implantação.

    Executar o seguinte comando para permitir a monitorização com opções predefinidos, como utilizar o contexto kube atual, criar um espaço de trabalho padrão log analytics e sem especificar um servidor proxy:

    ```bash
    bash enable-monitoring.sh --resource-id $azureArcClusterResourceId
    ```

    Executar o seguinte comando para criar um espaço de trabalho padrão do Log Analytics e sem especificar um servidor proxy:

    ```bash
   bash enable-monitoring.sh --resource-id $azureArcClusterResourceId --kube-context $kubeContext
    ```

    Executar o seguinte comando para utilizar um espaço de trabalho log analytics existente e sem especificar um servidor proxy:

    ```bash
    bash enable-monitoring.sh --resource-id $azureArcClusterResourceId --kube-context $kubeContext  --workspace-id $logAnalyticsWorkspaceResourceId
    ```

    Executar o seguinte comando para utilizar um espaço de trabalho do Log Analytics existente e especificar um servidor proxy:

    ```bash
    bash enable-monitoring.sh --resource-id $azureArcClusterResourceId --kube-context $kubeContext  --workspace-id $logAnalyticsWorkspaceResourceId --proxy $proxyEndpoint
    ```

Depois de ter ativado a monitorização, pode demorar cerca de 15 minutos até poder ver as métricas de saúde para o cluster.

### <a name="using-service-principal"></a>Usando o principal de serviço
O *enable-monitoring.sh* de script de bash utiliza o login do dispositivo interativo. Se preferir login não interativo, pode utilizar um principal de serviço existente ou criar um novo que tenha as permissões necessárias, conforme descrito nos [Pré-Requisitos.](#prerequisites) Para usar o principal do serviço, você terá que passar -- cliente-id, --cliente-secreto e --tenant-id valores de id de serviço que você pretendeu usar para *enable-monitoring.sh* script de bash.

```bash
subscriptionId="<subscription Id of the Azure Arc connected cluster resource>"
servicePrincipal=$(az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/${subscriptionId}")
servicePrincipalClientId=$(echo $servicePrincipal | jq -r '.appId')
```

A atribuição de funções abaixo só é aplicável se estiver a utilizar o espaço de trabalho do Log Analytics existente numa Subscrição Azure diferente do recurso Cluster Conectado Arc K8s.

```bash
logAnalyticsWorkspaceResourceId="<Azure Resource Id of the Log Analytics Workspace>" # format of the Azure Log Analytics workspace should be /subscriptions/<subId>/resourcegroups/<rgName>/providers/microsoft.operationalinsights/workspaces/<workspaceName>
az role assignment create --role 'Log Analytics Contributor' --assignee $servicePrincipalClientId --scope $logAnalyticsWorkspaceResourceId

servicePrincipalClientSecret=$(echo $servicePrincipal | jq -r '.password')
tenantId=$(echo $servicePrincipal | jq -r '.tenant')
```

Por exemplo:

```bash
bash enable-monitoring.sh --resource-id $azureArcClusterResourceId --client-id $servicePrincipalClientId --client-secret $servicePrincipalClientSecret  --tenant-id $tenantId --kube-context $kubeContext  --workspace-id $logAnalyticsWorkspaceResourceId --proxy $proxyEndpoint
```

## <a name="configure-proxy-endpoint"></a>Configure ponto final de procuração

Com o agente contentorizado para insights de contentores, pode configurar um ponto final de procuração para permitir que ele se comunique através do seu servidor proxy. A comunicação entre o agente contentorizado e o Azure Monitor pode ser um servidor de procuração HTTP ou HTTPS, e a autenticação anónima e básica (nome de utilizador/palavra-passe) é suportada.

O valor de configuração proxy tem a seguinte sintaxe: `[protocol://][user:password@]proxyhost[:port]`

> [!NOTE]
>Se o seu servidor proxy não necessitar de autenticação, ainda precisa de especificar um nome de utilizador/palavra-passe psuedo. Este pode ser qualquer nome de utilizador ou senha.

|Propriedade| Descrição |
|--------|-------------|
|Protocolo | http ou https |
|utilizador | Nome de utilizador opcional para autenticação por procuração |
|palavra-passe | Senha opcional para autenticação por procuração |
|proxyhost | Endereço ou FQDN do servidor proxy |
|porta | Número de porta opcional para o servidor proxy |

Por exemplo: `http://user01:password@proxy01.contoso.com:3128`

Se especificar o protocolo como **http,** os pedidos HTTP são criados utilizando a ligação segura SSL/TLS. O seu servidor proxy deve suportar os protocolos SSL/TLS.

### <a name="configure-using-powershell"></a>Configurar com o PowerShell

Especifique o nome de utilizador e a palavra-passe, o endereço IP ou fQDN e o número da porta para o servidor proxy. Por exemplo:

```powershell
$proxyEndpoint = https://<user>:<password>@<proxyhost>:<port>
```

### <a name="configure-using-bash"></a>Configure usando a bash

Especifique o nome de utilizador e a palavra-passe, o endereço IP ou fQDN e o número da porta para o servidor proxy. Por exemplo:

```bash
export proxyEndpoint=https://<user>:<password>@<proxyhost>:<port>
```

## <a name="next-steps"></a>Passos seguintes

- Com a monitorização habilitada a recolher a saúde e a utilização de recursos do seu cluster Kubernetes ativado pelo Arco e cargas de trabalho em execução, aprenda [a utilizar](container-insights-analyze.md) insights de Contentores.

- Por predefinição, o agente contentor recolhe os registos de contentores stdout/stderr de todos os recipientes que estão a funcionar em todos os espaços de nome, exceto o sistema kube. Para configurar a recolha de registos de contentores específica para espaços de nome ou espaços de nome específicos, reveja a [configuração do agente Do Contentor Insights](container-insights-agent-config.md) para configurar as definições de recolha de dados desejadas no seu ficheiro de configurações ConfigMap.

- Para raspar e analisar as métricas prometheus do seu cluster, [reveja métricas configure Prometheus raspando](container-insights-prometheus-integration.md)

- Para aprender a parar de monitorizar o seu Arco, o agrupamento Kubernetes permitiu que os Kubernetes se insights do Contentor, veja [como parar de monitorizar o seu cluster híbrido.](container-insights-optout-hybrid.md#how-to-stop-monitoring-on-arc-enabled-kubernetes)