---
title: Ativar o Arco Azure em Kubernetes no dispositivo GPU Azure Stack Edge Pro/ Microsoft Docs
description: Descreve como ativar o Azure Arc num cluster Kubernetes existente no seu dispositivo GPU Azure Stack Edge Pro.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 11/12/2020
ms.author: alkohli
ms.openlocfilehash: 342f6a2c4761104823694f2181b3ffa8726a441e
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96449418"
---
# <a name="enable-azure-arc-on-kubernetes-cluster-on-your-azure-stack-edge-pro-gpu-device"></a>Ativar o Azure Arc no cluster Kubernetes no seu dispositivo GPU Azure Stack Edge Pro

Este artigo mostra-lhe como ativar o Azure Arc num cluster Kubernetes existente no seu dispositivo Azure Stack Edge Pro. 

Este procedimento destina-se a quem tenha revisto as [cargas de trabalho de Kubernetes no dispositivo Azure Stack Edge Pro](azure-stack-edge-gpu-kubernetes-workload-management.md) e esteja familiarizado com os conceitos de [What is Azure Arc enabled Kubernetes (Preview)?](../azure-arc/kubernetes/overview.md)


## <a name="prerequisites"></a>Pré-requisitos

Antes de poder ativar o Azure Arc no cluster Kubernetes, certifique-se de que completou os seguintes pré-requisitos no seu dispositivo Azure Stack Edge Pro e o cliente que utilizará para aceder ao dispositivo:

### <a name="for-device"></a>Para o dispositivo

1. Tem credenciais de entrada num dispositivo Azure Stack Edge Pro de 1 nó.
    1. O dispositivo está ativado. Ver [Ativar o dispositivo](azure-stack-edge-gpu-deploy-activate.md).
    1. O dispositivo tem o papel computacional configurado através do portal Azure e tem um cluster Kubernetes. Consulte [o cálculo Configure](azure-stack-edge-gpu-deploy-configure-compute.md).

1. Tem acesso ao proprietário da assinatura. Necessitaria deste acesso durante o passo de atribuição de funções para o seu diretor de serviço.
 

### <a name="for-client-accessing-the-device"></a>Para o cliente aceder ao dispositivo

1. Tem um sistema de clientes Windows que será utilizado para aceder ao dispositivo Azure Stack Edge Pro.
  
    - O cliente está a executar o Windows PowerShell 5.0 ou mais tarde. Para descarregar a versão mais recente do Windows PowerShell, aceda à [Instalação do Windows PowerShell](https://docs.microsoft.com/powershell/scripting/install/installing-powershell-core-on-windows).
    
    - Também pode ter qualquer outro cliente com um [sistema operativo suportado.](azure-stack-edge-gpu-system-requirements.md#supported-os-for-clients-connected-to-device) Este artigo descreve o procedimento quando se utiliza um cliente Windows. 
    
1. Completou o procedimento descrito no [Access the Kubernetes cluster no dispositivo Azure Stack Edge Pro](azure-stack-edge-gpu-create-kubernetes-cluster.md). Tu:
    
    - Instalado `kubectl` no cliente.    
    - Certifique-se de que a `kubectl` versão do cliente não é distorcida mais do que uma versão da versão master kubernetes em execução no seu dispositivo Azure Stack Edge Pro. 
      - Utilize `kubectl version` para verificar a versão do kubectl em execução no cliente. Tome nota da versão completa.
      - Na UI local do seu dispositivo Azure Stack Edge Pro, aceda à **atualização de Software** e note o número da versão do servidor Kubernetes. 
    
        ![Verifique o número da versão do servidor Kubernetes](media/azure-stack-edge-gpu-connect-powershell-interface/verify-kubernetes-version-1.png)      
      
      - Verifique se estas duas versões são compatíveis. 


## <a name="register-kubernetes-resource-providers"></a>Registar fornecedores de recursos Kubernetes

Antes de ativar o Azure Arc no cluster Kubernetes, terá de ativar e registar-se `Microsoft.Kubernetes` e contra a sua `Microsoft.KubernetesConfiguration` subscrição. 

1. Para ativar um fornecedor de recursos, no portal Azure, vá à subscrição que está a planear utilizar para a implementação. Vá aos **Fornecedores de Recursos.** 
1. No painel direito, procure os fornecedores que pretende adicionar. Neste exemplo, `Microsoft.Kubernetes` e `Microsoft.KubernetesConfiguration` .

    ![Registar fornecedores de recursos Kubernetes](media/azure-stack-edge-gpu-connect-powershell-interface/register-k8-resource-providers-1.png)

1. Selecione um fornecedor de recursos e a partir da parte superior da barra de comando, **selecione Registar.** As inscrições demoram vários minutos. 

    ![Registar fornecedores de recursos Kubernetes 2](media/azure-stack-edge-gpu-connect-powershell-interface/register-k8-resource-providers-2.png)

1. Refresque a UI até ver que o fornecedor de recursos está registado. Repita o processo para ambos os fornecedores de recursos.
    
    ![Registar fornecedores de recursos Kubernetes 3](media/azure-stack-edge-gpu-connect-powershell-interface/register-k8-resource-providers-4.png)

Também pode registar fornecedores de recursos através do `az cli` . Para obter mais informações, consulte [Registar os dois fornecedores da Azure Arc habilitados a Kubernetes](../azure-arc/kubernetes/connect-cluster.md#register-the-two-providers-for-azure-arc-enabled-kubernetes)

## <a name="create-service-principal-assign-role"></a>Criar o principal de serviço, atribuir função

1. Certifique-se de que tem `Subscription ID` e o nome do grupo de recursos que utilizou para a implementação de recursos para o seu serviço Azure Stack Edge. Para obter o ID de subscrição, vá ao seu recurso Azure Stack Edge no portal Azure. Navegue para **a visão geral > Essentials**.

    ![Obtenha iD de assinatura](media/azure-stack-edge-gpu-connect-powershell-interface/get-subscription-id-1.png)

    Para obter o nome do grupo de recursos, vá ao **Properties**.

    ![Obtenha o nome do grupo de recursos](media/azure-stack-edge-gpu-connect-powershell-interface/get-resource-group-name-1.png)

1. Para criar um principal de serviço, utilize o seguinte comando através do `az cli` .

    `az ad sp create-for-rbac --skip assignment --name "<Informative name for service principal>"`  

    Para obter informações sobre como iniciar sessão no `az cli` portal , Inicie a Cloud Shell no portal [Azure](../cloud-shell/quickstart-powershell.md#start-cloud-shell)

    Eis um exemplo. 
    
    ```azurecli
    PS /home/user> az ad sp create-for-rbac --skip-assignment --name "https://azure-arc-for-ase-k8s"
    {
      "appId": "aa8a082e-0fa1-4a82-b51c-e8b2a9fdaa8b",
      "displayName": "azure-arc-for-ase-k8s",
      "name": "https://azure-arc-for-ase-k8s",
      "password": "<password>",
      "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db47"
    }
    PS /home/user>
    ```

1. Tome nota do `appID` `name` , e como você vai usar `password` isto como entrada no comando `tenantID` seguinte.

1. Depois de criar o novo diretor de serviço, atribua o `Kubernetes Cluster - Azure Arc Onboarding` papel ao recém-criado principal. Este é um papel Azure incorporado (use o ID de papel no comando) com permissões limitadas. Utilize o seguinte comando:

    `az role assignment create --role 34e09817-6cbe-4d01-b1a2-e0eac5743d41 --assignee <appId-from-service-principal> --scope /subscriptions/<SubscriptionID>/resourceGroups/<Resource-group-name>`

    Eis um exemplo.
    
    ```azurecli
    PS /home/user> az role assignment create --role 34e09817-6cbe-4d01-b1a2-e0eac5743d41 --assignee aa8a082e-0fa1-4a82-b51c-e8b2a9fdaa8b --scope /subscriptions/062c67a6-019b-40af-a775-c4dc1abe56ed/resourceGroups/myaserg1
    {
      "canDelegate": null,
      "id": "/subscriptions/062c67a6-019b-40af-a775-c4dc1abe56ed/resourceGroups/myaserg1/providers/Microsoft.Authorization/roleAssignments/59272f92-e5ce-4aeb-9c0c-62532d8caf25",
      "name": "59272f92-e5ce-4aeb-9c0c-62532d8caf25",
      "principalId": "b045b3fe-8745-4097-9674-91cb0afaad91",
      "principalType": "ServicePrincipal",
      "resourceGroup": "myaserg1",
      "roleDefinitionId": "/subscriptions/062c67a6-019b-40af-a775-c4dc1abe56ed/providers/Microsoft.Authorization/roleDefinitions/34e09817-6cbe-4d01-b1a2-e0eac5743d41",
      "scope": "/subscriptions/062c67a6-019b-40af-a775-c4dc1abe56ed/resourceGroups/myaserg1",
      "type": "Microsoft.Authorization/roleAssignments"
    }
    PS /home/user>
    ```
    Para obter mais informações sobre como criar o principal de serviço e executar a atribuição de funções, consulte os passos na [Create a azure Arc-enabled onboarding Service Principal](../azure-arc/kubernetes/create-onboarding-service-principal.md).


## <a name="enable-arc-on-kubernetes-cluster"></a>Ativar o Arc no cluster do Kubernetes

Siga estes passos para configurar o cluster Kubernetes para a gestão do Arco Azure:

1. [Ligue-se à interface PowerShell](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface) do seu dispositivo.

1. Escreva:

    `Set-HcsKubernetesAzureArcAgent -SubscriptionId "<Your Azure Subscription Id>" -ResourceGroupName "<Resource Group Name>" -ResourceName "<Azure Arc resource name (shouldn't exist already)>" -Location "<Region associated with resource group>" -TenantId "<Tenant Id of service principal>" -ClientId "<App id of service principal>" -ClientSecret "<Password of service principal>"`


    > [!NOTE]
    > - Para colocar o Arco azul no seu dispositivo, certifique-se de que está a utilizar uma [região suportada para o Arco Azure.](../azure-arc/kubernetes/overview.md#supported-regions) 
    > - Use o `az account list-locations` comando para descobrir o nome exato da localização para passar no `Set-HcsKubernetesAzureArcAgent` cmdlet. Os nomes de localização são normalmente formatados sem qualquer espaço.
    
    Segue-se um exemplo:
   
    ```powershell
    [10.128.44.240]: PS>Set-HcsKubernetesAzureArcAgent -SubscriptionId "062c67a6-019b-40af-a775-c4dc1abe56ed" -ResourceGroupName "myaserg1" -ResourceName "myasetestresarc" -Location "westeurope" -TenantId "72f988bf-86f1-41af-91ab-2d7cd011db47" -ClientId "aa8a082e-0fa1-4a82-b51c-e8b2a9fdaa8b" -ClientSecret "<password>"
        [10.128.44.240]: PS>
    ```
    
    No portal Azure, deve ser criado um recurso com o nome fornecido no comando anterior.

    ![Ir para o recurso Azure Arc](media/azure-stack-edge-gpu-connect-powershell-interface/verify-azure-arc-enabled-1.png)

1. Para verificar se o Arco Azure está ativado com sucesso, executar o seguinte comando a partir da interface PowerShell:

    `kubectl get deployments -n azure-arc`

    Este comando encontra quaisquer aplicações que sejam implantadas no `azure-arc` espaço de nome correspondente ao Arco de Azure.

    Aqui está uma saída de amostra que mostra os agentes do Azure Arc que foram implantados no seu cluster Kubernetes no espaço de `azure-arc` nomes. 


    ```powershell
    [10.128.44.240]: PS>kubectl get deployments -n azure-arc
    NAME                        READY   UP-TO-DATE   AVAILABLE   AGE
    cluster-metadata-operator   1/1     1            1           45m
    clusteridentityoperator     1/1     1            1           45m
    config-agent                1/1     1            1           45m
    connect-agent               1/1     1            1           45m
    controller-manager          1/1     1            1           45m
    flux-logs-agent             1/1     1            1           45m
    metrics-agent               1/1     1            1           45m
    resource-sync-agent         1/1     1            1           45m
    [10.128.44.240]: PS>
    ```

    Também pode obter uma lista das cápsulas que estão a correr no seu cluster Kubernetes no `azure-arc` espaço de nomes. Uma cápsula é um recipiente de aplicação, ou processo, que funciona no seu cluster Kubernetes. 

    Utilize o seguinte comando:
    
    `kubectl get pods -n azure-arc`
    
    Aqui está uma amostra de saída.
    
    ```powershell
    [10.128.44.240]: PS>kubectl get pods -n azure-arc
    NAME                                         READY   STATUS    RESTARTS   AGE
    cluster-metadata-operator-64cbdf95b4-s2q52   2/2     Running   0          16m
    clusteridentityoperator-6f6dbccf7-nwnxg      3/3     Running   0          16m
    config-agent-7df5bf497b-mjm8k                3/3     Running   0          16m
    connect-agent-5d4c766764-m7h46               1/1     Running   0          16m
    controller-manager-777555fb57-t7tdp          3/3     Running   0          16m
    flux-logs-agent-845476c899-zcmtj             2/2     Running   0          16m
    metrics-agent-84d6fc8f4d-g9jkm               2/2     Running   0          16m
    resource-sync-agent-8f88dbf96-zgxjj          3/3     Running   0          16m
    [10.128.44.240]: PS>
    ```


Como mostra a produção anterior, o Azure Arc permitiu que a Kubernetes é constituída por alguns agentes (operadores) que funcionam no seu cluster implantado no `azure-arc` espaço de nomes.

- `config-agent`: observa o cluster ligado para os recursos de configuração do controlo de origem aplicados no cluster e atualiza o estado de conformidade
- `controller-manager`: é um operador de operadores e orquestra interações entre componentes do Arco Azure
- `metrics-agent`: recolhe métricas de outros agentes da Arc para garantir que estes agentes exibem um desempenho ótimo
- `cluster-metadata-operator`: recolhe metadados de cluster - versão de cluster, contagem de nós e versão agente Azure Arc
- `resource-sync-agent`: sincroniza os metadados de cluster acima mencionados para Azure
- `clusteridentityoperator`: Azure Arc habilitado kubernetes atualmente suporta a identidade atribuída ao sistema. ClusteridentityOperator mantém o certificado de identidade de serviço gerido (MSI) usado por outros agentes para comunicação com a Azure.
- `flux-logs-agent`: recolhe registos dos operadores de fluxo implantados como parte da configuração do controlo de fontes.
- `connect-agent`: fala com o recurso Azure Arc.

### <a name="remove-arc-from-the-kubernetes-cluster"></a>Remover arco do cluster Kubernetes

Para remover a gestão do Arco Azure, siga estes passos:

1. 1. [Ligue-se à interface PowerShell](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface) do seu dispositivo.
2. Escreva:

    `Remove-HcsKubernetesAzureArcAgent` 


> [!NOTE]
> Por predefinição, quando os recursos `yamls` são eliminados do repositório git, os recursos correspondentes não são eliminados do cluster Kubernetes. É necessário definir `--sync-garbage-collection`  no Arc OperatorParams para permitir a supressão de recursos quando eliminado do repositório de git. Para obter mais informações, consulte [Eliminar uma configuração](../azure-arc/kubernetes/use-gitops-connected-cluster.md#additional-parameters)

## <a name="next-steps"></a>Passos seguintes

Para entender como executar uma implementação do Arco Azure, consulte [implementar uma aplicação apátrida php guestbook com Redis via GitOps num dispositivo Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-stateless-application-git-ops-guestbook.md)