---
title: Integrar o Azure Key Vault no Kubernetes
description: Neste tutorial, você acede e obtém segredos do seu cofre de chaves Azure usando o controlador Secrets Store Container Storage Interface (CSI) para montar em cápsulas Kubernetes.
author: ShaneBala-keyvault
ms.author: sudbalas
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 09/25/2020
ms.openlocfilehash: b7d587f2be5141f7de82e9294b1fdb9fba4a6a41
ms.sourcegitcommit: b4880683d23f5c91e9901eac22ea31f50a0f116f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/11/2020
ms.locfileid: "94488648"
---
# <a name="tutorial-configure-and-run-the-azure-key-vault-provider-for-the-secrets-store-csi-driver-on-kubernetes"></a>Tutorial: Configurar e executar o fornecedor Azure Key Vault para o motorista CSI Secrets Store em Kubernetes

> [!IMPORTANT]
> O CSI Driver é um projeto de código aberto que não é suportado pelo suporte técnico da Azure. Por favor, informe todos os comentários e problemas relacionados com a integração do CSI Driver Key Vault no link github na parte inferior da página. Esta ferramenta é fornecida para que os utilizadores se auto-instalem em clusters e recolham feedback da nossa comunidade.

Neste tutorial, você acede e obtém segredos do seu cofre de chaves Azure usando o controlador Secrets Store Container Storage Interface (CSI) para montar os segredos em cápsulas Kubernetes.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Crie um principal de serviço ou utilize identidades geridas.
> * Implementar um cluster Azure Kubernetes Service (AKS) utilizando o Azure CLI.
> * Instale o leme e o controlador CSI Secrets Store.
> * Crie um cofre de chaves Azure e desconfiem dos vossos segredos.
> * Crie o seu próprio objeto SecretProviderClass.
> * Atribua o seu principal de serviço ou utilize identidades geridas.
> * Desdobre a sua cápsula com segredos montados do cofre das chaves.

## <a name="prerequisites"></a>Pré-requisitos

* Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

* Antes de iniciar este tutorial, instale o [Azure CLI](/cli/azure/install-azure-cli-windows?view=azure-cli-latest).

## <a name="create-a-service-principal-or-use-managed-identities"></a>Criar um principal serviço ou usar identidades geridas

Se planeia usar identidades geridas, pode passar para a secção seguinte.

Crie um chefe de serviço para controlar quais os recursos que podem ser acedidos a partir do seu cofre de chaves Azure. O acesso deste diretor de serviço é restringido pelas funções que lhe são atribuídas. Esta funcionalidade dá-lhe controlo sobre como o diretor de serviço pode gerir os seus segredos. No exemplo seguinte, o nome do diretor de serviço é *contosoServicePrincipal*.

```azurecli
az ad sp create-for-rbac --name contosoServicePrincipal --skip-assignment
```
Esta operação devolve uma série de pares chave/valor:

![Screenshot mostrando o appId e senha para contosoServicePrincipal](../media/kubernetes-key-vault-1.png)

Copie as credenciais **de appId** e **password** para posterior utilização.

## <a name="flow-for-using-managed-identity"></a>Fluxo para utilização da Identidade Gerida

Este diagrama ilustra o fluxo de integração do Cofre AKS-Key para identidade gerida:

![Diagrama que mostra o fluxo de integração do Cofre AKS-Key para identidade gerida](../media/aks-key-vault-integration-flow.png)

## <a name="deploy-an-azure-kubernetes-service-aks-cluster-by-using-the-azure-cli"></a>Implementar um cluster Azure Kubernetes Service (AKS) utilizando o Azure CLI

Não precisas de usar a Azure Cloud Shell. O seu pedido de comando (terminal) com o CLI Azure instalado será suficiente. 

Complete as secções "Criar um grupo de recursos", "Criar cluster AKS" e "Ligar ao cluster" em [Implementar um cluster de serviço Azure Kubernetes utilizando o CLI Azure](../../aks/kubernetes-walkthrough.md). 

> [!NOTE] 
> Se pretender utilizar uma identidade de vagem em vez de um principal de serviço, certifique-se de que o ativa quando criar o cluster Kubernetes, como mostra o seguinte comando:
>
> ```azurecli
> az aks create -n contosoAKSCluster -g contosoResourceGroup --kubernetes-version 1.16.9 --node-count 1 --enable-managed-identity
> ```

1. [Desfie a variável ambiente PATH](https://www.java.com/en/download/help/path.xml) para o ficheiro *kubectl.exe* que descarregou.
1. Verifique a sua versão Kubernetes utilizando o seguinte comando, que produz a versão do cliente e do servidor. A versão do cliente é o *ficheirokubectl.exe* que instalou, e a versão do servidor é o Azure Kubernetes Services (AKS) em que o seu cluster está a funcionar.
    ```azurecli
    kubectl version
    ```
1. Certifique-se de que a sua versão Kubernetes é 1.16.0 ou mais tarde. Para os agrupamentos de janelas, certifique-se de que a sua versão Kubernetes é 1.18.0 ou mais tarde. O comando seguinte atualiza tanto o cluster Kubernetes como o conjunto de nós. O comando pode levar alguns minutos para ser executado. Neste exemplo, o grupo de recursos é *o ContosoResourceGroup,* e o cluster Kubernetes é *contosoAKSCluster*.
    ```azurecli
    az aks upgrade --kubernetes-version 1.16.9 --name contosoAKSCluster --resource-group contosoResourceGroup
    ```
1. Para exibir os metadados do cluster AKS que criou, utilize o seguinte comando. Copie o **principalId,** **clientId,** **subscriçãoId** e **nodeResourceGroup** para posterior utilização. Se o cluster ASK não foi criado com identidades geridas ativadas, o **principalid** e **clienteId** será nulo. 

    ```azurecli
    az aks show --name contosoAKSCluster --resource-group contosoResourceGroup
    ```

    A saída mostra ambos os parâmetros realçados:
    
    ![Screenshot do CLI Azure com valores principalid e clienteId destacado ](../media/kubernetes-key-vault-2.png) ![ Screenshot do CLI Azure com valores de subscriçãoId e nodeResourceGroup destacados](../media/kubernetes-key-vault-3.png)
    
## <a name="install-helm-and-the-secrets-store-csi-driver"></a>Instale o leme e o controlador CSI secrets Store

Para instalar o controlador CSI Secrets Store, primeiro tem de instalar [o Helm](https://helm.sh/docs/intro/install/).

Com a interface do controlador [CSI Secrets Store,](https://github.com/Azure/secrets-store-csi-driver-provider-azure/blob/master/charts/csi-secrets-store-provider-azure/README.md) pode obter os segredos que estão armazenados na sua instância de cofre de chave Azure e, em seguida, usar a interface do condutor para montar os conteúdos secretos em cápsulas Kubernetes.

1. Verifique se a versão Helm é v3 ou posterior:
    ```azurecli
    helm version
    ```
1. Instale o controlador CSI Secrets Store e o fornecedor Azure Key Vault para o condutor:
    ```azurecli
    helm repo add csi-secrets-store-provider-azure https://raw.githubusercontent.com/Azure/secrets-store-csi-driver-provider-azure/master/charts

    helm install csi-secrets-store-provider-azure/csi-secrets-store-provider-azure --generate-name
    ```

## <a name="create-an-azure-key-vault-and-set-your-secrets"></a>Crie um cofre de chaves Azure e desconfiem dos seus segredos

Para criar o seu próprio cofre-chave e definir os seus segredos, siga as instruções em [Conjunto e recupere um segredo do Azure Key Vault utilizando o Azure CLI](../secrets/quick-create-cli.md).

> [!NOTE] 
> Não precisa de usar a Azure Cloud Shell ou criar um novo grupo de recursos. Pode utilizar o grupo de recursos que criou anteriormente para o cluster Kubernetes.

## <a name="create-your-own-secretproviderclass-object"></a>Crie o seu próprio objeto SecretProviderClass

Para criar o seu próprio objeto SecretProviderClass personalizado com parâmetros específicos do fornecedor para o controlador CSI Secrets Store, [utilize este modelo](https://github.com/Azure/secrets-store-csi-driver-provider-azure/blob/master/examples/v1alpha1_secretproviderclass_service_principal.yaml). Este objeto fornecerá acesso de identidade ao seu cofre de chaves.

Na amostra, o ficheiro IML da SecretProviderClass preenche os parâmetros em falta. São necessários os seguintes parâmetros:

* **utilizadorAssignedIdentityID** : # [REQUIRED] Se estiver a utilizar um principal de serviço, utilize o ID do cliente para especificar qual a identidade gerida atribuída pelo utilizador a utilizar. Se estiver a usar uma identidade atribuída ao utilizador como identidade gerida pelo VM, especifique a identificação do cliente da identidade. Se o valor estiver vazio, é por defeito utilizar a identidade atribuída ao sistema no VM 
* **keyvaultName** : O nome do seu cofre chave
* **objetos** : O recipiente para todo o conteúdo secreto que pretende montar
    * **nome do objeto** : O nome do conteúdo secreto
    * **objectType** : O tipo de objeto (segredo, chave, certificado)
* **grupo** de recursos : O nome do grupo de recursos # [OBRIGATÓRIO para a versão < 0.0.4] o grupo de recursos do KeyVault
* **subscriçãoId** : O ID de subscrição do seu cofre-chave # [OBRIGATÓRIO para a versão < 0.0.4] o ID de subscrição do KeyVault
* **tenantID** : O ID do inquilino, ou iD diretório, do seu cofre chave

Documentação de todos os campos necessários está disponível aqui: [Link](https://github.com/Azure/secrets-store-csi-driver-provider-azure#create-a-new-azure-key-vault-resource-or-use-an-existing-one)

O modelo atualizado é mostrado no seguinte código. Descarregue-o como um ficheiro YAML e preencha os campos necessários. Neste exemplo, o cofre chave é **contosoKeyVault5**. Tem dois segredos, **o segredo1** e **o segredo2.**

> [!NOTE] 
> Se estiver a utilizar identidades geridas, desaprote o valor **da entidade usePodId** como *verdadeiro* , e desaprote o **valoridárioidídeo** do utilizador como um par de aspas **(""** ). 

```yaml
apiVersion: secrets-store.csi.x-k8s.io/v1alpha1
kind: SecretProviderClass
metadata:
  name: azure-kvname
spec:
  provider: azure
  parameters:
    usePodIdentity: "false"                   # [REQUIRED] Set to "true" if using managed identities
    useVMManagedIdentity: "false"             # [OPTIONAL] if not provided, will default to "false"
    userAssignedIdentityID: "servicePrincipalClientID"       # [REQUIRED] If you're using a service principal, use the client id to specify which user-assigned managed identity to use. If you're using a user-assigned identity as the VM's managed identity, specify the identity's client id. If the value is empty, it defaults to use the system-assigned identity on the VM
                                                             #     az ad sp show --id http://contosoServicePrincipal --query appId -o tsv
                                                             #     the preceding command will return the client ID of your service principal
    keyvaultName: "contosoKeyVault5"          # [REQUIRED] the name of the key vault
                                              #     az keyvault show --name contosoKeyVault5
                                              #     the preceding command will display the key vault metadata, which includes the subscription ID, resource group name, key vault 
    cloudName: ""                                # [OPTIONAL for Azure] if not provided, Azure environment will default to AzurePublicCloud
    objects:  |
      array:
        - |
          objectName: secret1                 # [REQUIRED] object name
                                              #     az keyvault secret list --vault-name "contosoKeyVault5"
                                              #     the above command will display a list of secret names from your key vault
          objectType: secret                  # [REQUIRED] object types: secret, key, or cert
          objectVersion: ""                   # [OPTIONAL] object versions, default to latest if empty
        - |
          objectName: secret2
          objectType: secret
          objectVersion: ""
    resourceGroup: "contosoResourceGroup"     # [REQUIRED] the resource group name of the key vault
    subscriptionId: "subscriptionID"          # [REQUIRED] the subscription ID of the key vault
    tenantId: "tenantID"                      # [REQUIRED] the tenant ID of the key vault
```
A imagem a seguir mostra a saída da consola para **a az keyvault show -- nome contosoKeyVault5** com os metadados destacados relevantes:

![Screenshot mostrando a saída da consola para "az keyvault show -- nome contosoKeyVault5"](../media/kubernetes-key-vault-4.png)

## <a name="assign-your-service-principal-or-use-managed-identities"></a>Atribua o seu principal serviço ou utilize identidades geridas

### <a name="assign-a-service-principal"></a>Atribuir um principal de serviço

Se estiver a usar um diretor de serviço, conceda permissões para aceder ao cofre e recuperar segredos. Atribua o papel de *Leitor* e conceda ao serviço permissões principais para *obter* segredos do seu cofre chave, fazendo o seguinte comando:

1. Atribua o seu principal de serviço ao cofre de chaves existente. O **parâmetro $AZURE_CLIENT_ID** é o **appId** que copiou depois de ter criado o seu principal de serviço.
    ```azurecli
    az role assignment create --role Reader --assignee $AZURE_CLIENT_ID --scope /subscriptions/$SUBID/resourcegroups/$KEYVAULT_RESOURCE_GROUP/providers/Microsoft.KeyVault/vaults/$KEYVAULT_NAME
    ```

    A saída do comando é mostrada na seguinte imagem: 

    ![Screenshot mostrando o valor principal](../media/kubernetes-key-vault-5.png)

1. Conceda as permissões principais do serviço para obter segredos:
    ```azurecli
    az keyvault set-policy -n $KEYVAULT_NAME --secret-permissions get --spn $AZURE_CLIENT_ID
    az keyvault set-policy -n $KEYVAULT_NAME --key-permissions get --spn $AZURE_CLIENT_ID
    ```

1. Agora configuraste o teu diretor de serviço com permissões para ler segredos do teu cofre. O **$AZURE_CLIENTE_SECRET** é a palavra-passe do seu responsável de serviço. Adicione as suas credenciais principais de serviço como um segredo kubernetes que é acessível pelo motorista CSI Secrets Store:
    ```azurecli
    kubectl create secret generic secrets-store-creds --from-literal clientid=$AZURE_CLIENT_ID --from-literal clientsecret=$AZURE_CLIENT_SECRET
    ```

> [!NOTE] 
> Se estiver a implementar o pod Kubernetes e receber um erro sobre um ID secreto do cliente inválido, poderá ter um ID secreto do cliente mais antigo que tenha expirado ou reposto. Para resolver este problema, elimine o segredo dos *seus segredos e* crie um novo com o iD secreto do cliente atual. Para eliminar os seus *segredos-loja-creeds,* executar o seguinte comando:
>
> ```azurecli
> kubectl delete secrets secrets-store-creds
> ```

Se esqueceu o ID do cliente do seu chefe de serviço, pode repor o mesmo utilizando o seguinte comando:

```azurecli
az ad sp credential reset --name contosoServicePrincipal --credential-description "APClientSecret" --query password -o tsv
```

### <a name="use-managed-identities"></a>Utilizar identidades geridas

Se estiver a usar identidades geridas, atribua funções específicas ao cluster AKS que criou. 

1. Para criar, listar ou ler uma identidade gerida atribuída pelo utilizador, o seu cluster AKS precisa de ser atribuído à função [de Operador de Identidade Gerida.](../../role-based-access-control/built-in-roles.md#managed-identity-operator) Certifique-se de que o **$clientId** é o cliente do cluster Kubernetes. Para o âmbito, estará sob o seu serviço de subscrição Azure, especificamente o grupo de recursos de nó que foi feito quando o cluster AKS foi criado. Este âmbito garantirá que apenas os recursos dentro desse grupo sejam afetados pelas funções abaixo atribuídas. 

    ```azurecli
    RESOURCE_GROUP=contosoResourceGroup
    az role assignment create --role "Managed Identity Operator" --assignee $clientId --scope /subscriptions/$SUBID/resourcegroups/$RESOURCE_GROUP
    
    az role assignment create --role "Managed Identity Operator" --assignee $clientId --scope /subscriptions/$SUBID/resourcegroups/$NODE_RESOURCE_GROUP
    
    az role assignment create --role "Virtual Machine Contributor" --assignee $clientId --scope /subscriptions/$SUBID/resourcegroups/$NODE_RESOURCE_GROUP
    ```

1. Instale a identidade do Azure Ative Directory (Azure AD) em AKS.
    ```azurecli
    helm repo add aad-pod-identity https://raw.githubusercontent.com/Azure/aad-pod-identity/master/charts

    helm install pod-identity aad-pod-identity/aad-pod-identity
    ```

1. Criar uma identidade AD Azure. Na saída, copie o **clienteId** e **o principalid** para posterior utilização.
    ```azurecli
    az identity create -g $resourceGroupName -n $identityName
    ```

1. Atribua o papel de *Leitor* à identidade AD AZure que criou no passo anterior para o seu cofre chave e, em seguida, conceda as permissões de identidade para obter segredos do seu cofre chave. Use o **clienteId** e **o principalid** da identidade AD Azure.
    ```azurecli
    az role assignment create --role "Reader" --assignee $principalId --scope /subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups/contosoResourceGroup/providers/Microsoft.KeyVault/vaults/contosoKeyVault5

    az keyvault set-policy -n contosoKeyVault5 --secret-permissions get --spn $clientId
    az keyvault set-policy -n contosoKeyVault5 --key-permissions get --spn $clientId
    ```

## <a name="deploy-your-pod-with-mounted-secrets-from-your-key-vault"></a>Desdobre a sua cápsula com segredos montados a partir do seu cofre de chaves

Para configurar o seu objeto SecretProviderClass, executar o seguinte comando:
```azurecli
kubectl apply -f secretProviderClass.yaml
```

### <a name="use-a-service-principal"></a>Use um principal de serviço

Se estiver a usar um diretor de serviço, use o seguinte comando para implantar as suas cápsulas Kubernetes com a SecretProviderClass e os segredos-loja-creds que configuraste anteriormente. Aqui estão os modelos de implementação:
* Para [Linux](https://github.com/Azure/secrets-store-csi-driver-provider-azure/blob/master/examples/nginx-pod-inline-volume-service-principal.yaml)
* Para [janelas](https://github.com/Azure/secrets-store-csi-driver-provider-azure/blob/master/examples/windows-pod-secrets-store-inline-volume-secret-providerclass.yaml)

```azurecli
kubectl apply -f updateDeployment.yaml
```

### <a name="use-managed-identities"></a>Utilizar identidades geridas

Se estiver a utilizar identidades geridas, crie uma *AzureIdentity* no seu cluster que refira a identidade que criou anteriormente. Em seguida, crie uma *AzureIdentityBinding* que faz referência à entidade AzureId que criou. Preencha os parâmetros no modelo seguinte e, em seguida, guarde-os como *podIdentityAndBinding.yaml*.  

```yml
apiVersion: aadpodidentity.k8s.io/v1
kind: AzureIdentity
metadata:
    name: "azureIdentityName"               # The name of your Azure identity
spec:
    type: 0                                 # Set type: 0 for managed service identity
    resourceID: /subscriptions/<SUBID>/resourcegroups/<RESOURCEGROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<AZUREIDENTITYNAME>
    clientID: "managedIdentityClientId"     # The clientId of the Azure AD identity that you created earlier
---
apiVersion: aadpodidentity.k8s.io/v1
kind: AzureIdentityBinding
metadata:
    name: azure-pod-identity-binding
spec:
    azureIdentity: "azureIdentityName"      # The name of your Azure identity
    selector: azure-pod-identity-binding-selector
```
    
Execute o seguinte comando para executar a ligação:

```azurecli
kubectl apply -f podIdentityAndBinding.yaml
```

Em seguida, desloque a cápsula. O código a seguir é o ficheiro YAML de implantação, que utiliza a ligação de identidade do casulo a partir do passo anterior. Guarde este ficheiro como *podBindingDeployment.yaml*.

```yml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-secrets-store-inline
  labels:
    aadpodidbinding: azure-pod-identity-binding-selector
spec:
  containers:
    - name: nginx
      image: nginx
      volumeMounts:
        - name: secrets-store-inline
          mountPath: "/mnt/secrets-store"
          readOnly: true
  volumes:
    - name: secrets-store-inline
      csi:
        driver: secrets-store.csi.k8s.io
        readOnly: true
        volumeAttributes:
          secretProviderClass: azure-kvname
        nodePublishSecretRef:           # Only required when using service principal mode
          name: secrets-store-creds     # Only required when using service principal mode
```

Executar o seguinte comando para implantar a sua cápsula:

```azurecli
kubectl apply -f podBindingDeployment.yaml
```

### <a name="check-the-pod-status-and-secret-content"></a>Verifique o estado do pod e o conteúdo secreto 

Para exibir as cápsulas que lançou, executar o seguinte comando:
```azurecli
kubectl get pods
```

Para verificar o estado da sua cápsula, verifique o seguinte comando:
```azurecli
kubectl describe pod/nginx-secrets-store-inline
```

![Screenshot da saída Azure CLI mostrando o estado "Running" da cápsula e mostrando todos os eventos como "Normal" ](../media/kubernetes-key-vault-6.png)

Na janela de saída, a cápsula implantada deve estar no estado *de Funcionamento.* Na secção **Eventos** na parte inferior, todos os tipos de eventos são apresentados *normalmente.*

Depois de verificares que a cápsula está a funcionar, podes verificar se a cápsula contém os segredos do teu cofre.

Para mostrar todos os segredos que estão contidos na cápsula, executar o seguinte comando:
```azurecli
kubectl exec -it nginx-secrets-store-inline -- ls /mnt/secrets-store/
```

Para exibir o conteúdo de um segredo específico, executar o seguinte comando:
```azurecli
kubectl exec -it nginx-secrets-store-inline -- cat /mnt/secrets-store/secret1
```

Verifique se o conteúdo do segredo está exposto.

## <a name="next-steps"></a>Passos seguintes

Para ajudar a garantir que o cofre da chave é recuperável, consulte:
> [!div class="nextstepaction"]
> [Ligue a eliminação suave](./soft-delete-cli.md)
