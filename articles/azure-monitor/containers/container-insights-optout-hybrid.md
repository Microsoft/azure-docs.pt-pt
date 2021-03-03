---
title: Como parar de monitorizar o seu cluster híbrido Kubernetes | Microsoft Docs
description: Este artigo descreve como pode parar de monitorizar o seu cluster híbrido Kubernetes com insights de contentores.
ms.topic: conceptual
ms.date: 06/16/2020
ms.openlocfilehash: e8708d6b860683cc96a806160ccc7c8e33949ab2
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101713699"
---
# <a name="how-to-stop-monitoring-your-hybrid-cluster"></a>Como parar de monitorizar o seu cluster híbrido

Depois de ativar a monitorização do seu cluster Kubernetes, pode parar de monitorizar o cluster com insights do Contentor se decidir que já não o quer monitorizar. Este artigo mostra como fazê-lo para os seguintes ambientes:

- Motor AKS em Azure e Azure Stack
- Versão OpenShift 4 e superior
- Kubernetes preparado para o Azure Arc (pré-visualização)

## <a name="how-to-stop-monitoring-using-helm"></a>Como parar a monitorização usando o Helm

Aplicam-se os seguintes passos aos seguintes ambientes:

- Motor AKS em Azure e Azure Stack
- Versão OpenShift 4 e superior

1. Para identificar primeiro o lançamento do gráfico de timoneiro de insights do recipiente instalado no seu cluster, executar o seguinte comando de leme.

    ```
    helm list
    ```

    A saída assemelha-se-á ao seguinte:

    ```
    NAME                            NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                           APP VERSION
    azmon-containers-release-1      default         3               2020-04-21 15:27:24.1201959 -0700 PDT   deployed        azuremonitor-containers-2.7.0   7.0.0-1
    ```

    *azmon-containers-release-1* representa a versão do gráfico de leme para insights de contentores.

2. Para eliminar a versão da tabela, executar o seguinte comando de leme.

    `helm delete <releaseName>`

    Exemplo:

    `helm delete azmon-containers-release-1`

    Isto removerá a libertação do cluster. Pode verificar com a execução do `helm list` comando:

    ```
    NAME                            NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                           APP VERSION
    ```

A alteração de configuração pode demorar alguns minutos a ser concluída. Como helm rastreia os seus lançamentos mesmo depois de os ter apagado, pode auditar a história de um cluster, e até mesmo desapafe um lançamento com `helm rollback` .

## <a name="how-to-stop-monitoring-on-arc-enabled-kubernetes"></a>Como parar a monitorização no Arc permitiu que Kubernetes

### <a name="using-powershell"></a>Com o PowerShell

1. Faça o download e guarde o script para uma pasta local que configura o seu cluster com o add-on de monitorização utilizando os seguintes comandos:

    ```powershell
    wget https://aka.ms/disable-monitoring-powershell-script -OutFile disable-monitoring.ps1
    ```

2. Configure a `$azureArcClusterResourceId` variável definindo os valores correspondentes para `subscriptionId` , e representando o `resourceGroupName` `clusterName` ID de recurso do seu recurso de cluster Kubernetes ativado pelo Arco Azure.

    ```powershell
    $azureArcClusterResourceId = "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Kubernetes/connectedClusters/<clusterName>"
    ```

3. Configure a `$kubeContext` variável com o **contexto kube** do seu cluster executando o comando `kubectl config get-contexts` . Se quiser utilizar o contexto atual, desa um valor para `""` .

    ```powershell
    $kubeContext = "<kubeContext name of your k8s cluster>"
    ```

4. Executar o seguinte comando para parar de monitorizar o cluster.

    ```powershell
    .\disable-monitoring.ps1 -clusterResourceId $azureArcClusterResourceId -kubeContext $kubeContext
    ```

#### <a name="using-service-principal"></a>Usando o principal de serviço
O script *disable-monitoring.ps1* utiliza o login do dispositivo interativo. Se preferir login não interativo, pode utilizar um principal de serviço existente ou criar um novo que tenha as permissões necessárias, conforme descrito nos [Pré-Requisitos.](container-insights-enable-arc-enabled-clusters.md#prerequisites) Para utilizar o principal do serviço, terá de passar $servicePrincipalClientId, $servicePrincipalClientSecret e $tenantId parâmetros com valores do principal de serviço que pretendeu utilizar para enable-monitoring.ps1 script.

```powershell
$subscriptionId = "<subscription Id of the Azure Arc connected cluster resource>"
$servicePrincipal = New-AzADServicePrincipal -Role Contributor -Scope "/subscriptions/$subscriptionId"

$servicePrincipalClientId =  $servicePrincipal.ApplicationId.ToString()
$servicePrincipalClientSecret = [System.Net.NetworkCredential]::new("", $servicePrincipal.Secret).Password
$tenantId = (Get-AzSubscription -SubscriptionId $subscriptionId).TenantId
```

Por exemplo:

```powershell
\disable-monitoring.ps1 -clusterResourceId $azureArcClusterResourceId -kubeContext $kubeContext -servicePrincipalClientId $servicePrincipalClientId -servicePrincipalClientSecret $servicePrincipalClientSecret -tenantId $tenantId
```


### <a name="using-bash"></a>Usando a bash

1. Faça o download e guarde o script para uma pasta local que configura o seu cluster com o add-on de monitorização utilizando os seguintes comandos:

    ```bash
    curl -o disable-monitoring.sh -L https://aka.ms/disable-monitoring-bash-script
    ```

2. Configure a `azureArcClusterResourceId` variável definindo os valores correspondentes para `subscriptionId` , e representando o `resourceGroupName` `clusterName` ID de recurso do seu recurso de cluster Kubernetes ativado pelo Arco Azure.

    ```bash
    export azureArcClusterResourceId="/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Kubernetes/connectedClusters/<clusterName>"
    ```

3. Configure a `kubeContext` variável com o **contexto kube** do seu cluster executando o comando `kubectl config get-contexts` .

    ```bash
    export kubeContext="<kubeContext name of your k8s cluster>"
    ```

4. Para parar de monitorizar o seu cluster, existem diferentes comandos fornecidos com base no seu cenário de implantação.

    Executar o seguinte comando para parar de monitorizar o cluster utilizando o contexto atual.

    ```bash
    bash disable-monitoring.sh --resource-id $azureArcClusterResourceId
    ```

    Executar o seguinte comando para parar de monitorizar o cluster especificando um contexto

    ```bash
    bash disable-monitoring.sh --resource-id $azureArcClusterResourceId --kube-context $kubeContext
    ```

#### <a name="using-service-principal"></a>Usando o principal de serviço
O *disable-monitoring.sh* de script de bash utiliza o login do dispositivo interativo. Se preferir login não interativo, pode utilizar um principal de serviço existente ou criar um novo que tenha as permissões necessárias, conforme descrito nos [Pré-Requisitos.](container-insights-enable-arc-enabled-clusters.md#prerequisites) Para usar o principal do serviço, você terá que passar -- cliente-id, --cliente-secreto e --tenant-id valores de id de serviço que você pretendeu usar para *enable-monitoring.sh* script de bash.

```bash
subscriptionId="<subscription Id of the Azure Arc connected cluster resource>"
servicePrincipal=$(az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/${subscriptionId}")
servicePrincipalClientId=$(echo $servicePrincipal | jq -r '.appId')

servicePrincipalClientSecret=$(echo $servicePrincipal | jq -r '.password')
tenantId=$(echo $servicePrincipal | jq -r '.tenant')
```

Por exemplo:

```bash
bash disable-monitoring.sh --resource-id $azureArcClusterResourceId --kube-context $kubeContext --client-id $servicePrincipalClientId --client-secret $servicePrincipalClientSecret  --tenant-id $tenantId
```

## <a name="next-steps"></a>Passos seguintes

Se o espaço de trabalho Log Analytics foi criado apenas para suportar a monitorização do cluster e já não é necessário, tem de o eliminar manualmente. Se não estiver familiarizado com a forma de eliminar um espaço de trabalho, consulte [Eliminar um espaço de trabalho Azure Log Analytics](../logs/delete-workspace.md).