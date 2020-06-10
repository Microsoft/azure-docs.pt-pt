---
title: Integre o Cofre da Chave Azure com Kubernetes
description: Neste tutorial, você vai aceder e obter segredos do Azure Key Vault usando o controlador CSI (Interface de armazenamento de contentores) secrets Store (Interface de armazenamento de contentores) para depois montar em cápsulas Kubernetes.
author: taytran0
ms.author: t-trtr
ms.service: key-vault
ms.topic: tutorial
ms.date: 06/04/2020
ms.openlocfilehash: e945a30ca1fcd62fdfccd16d4e853540dbf73d8a
ms.sourcegitcommit: ce44069e729fce0cf67c8f3c0c932342c350d890
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/09/2020
ms.locfileid: "84637169"
---
# <a name="tutorial-configure-and-run-the-azure-key-vault-provider-for-secret-store-csi-driver-on-kubernetes"></a>Tutorial: Configurar e executar o fornecedor Azure Key Vault para motorista CSI da Loja Secreta em Kubernetes

Neste tutorial, você vai aceder e obter segredos do Azure Key Vault usando o controlador CSI (Interface de armazenamento de contentores) da Secrets Store para depois montar em cápsulas Kubernetes.

Neste tutorial, vai aprender a:

> [!div class="checklist"]
> * Criar um principal de serviço
> * Implementar um cluster de serviço Azure Kubernetes
> * Instalar controlador CSI da Loja de Leme e Segredos
> * Crie um cofre de chaves Azure e estabeleça segredos
> * Crie o seu próprio objeto SecretProviderClass
> * Desdobre a sua cápsula com segredos montados a partir do Cofre de Chaves

## <a name="prerequisites"></a>Pré-requisitos

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

Antes de iniciar este tutorial, instale o [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli-windows?view=azure-cli-latest).

## <a name="create-a-service-principal-or-use-managed-identities"></a>Criar um principal serviço ou usar identidades geridas

Se planeia usar identidades geridas, pode passar para a secção seguinte.

Crie um chefe de serviço para controlar os recursos que podem ser acedidos a partir do seu Cofre de Chaves Azure. O acesso deste diretor de serviço é restringido pelas funções que lhe são atribuídas. Esta funcionalidade dá-lhe controlo sobre como o diretor de serviço pode gerir os seus segredos. No exemplo abaixo, o nome do diretor de serviço é **contosoServicePrincipal**.

```azurecli
az ad sp create-for-rbac --name contosoServicePrincipal --skip-assignment
```
Esta operação devolve uma série de pares de chaves/valores:

![Imagem](../media/kubernetes-key-vault-1.png)

Copie o **appID** e **a palavra-passe.** Vai precisar destas credenciais mais tarde.



## <a name="deploy-an-azure-kubernetes-service-cluster-using-azure-cli"></a>Implementar um cluster de serviço Azure Kubernetes usando O Azure CLI

Não precisa de utilizar a Azure Cloud Shell, o seu Comando Imediato (Terminal) com Azure CLI instalado serve. 

Siga este [guia](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough) e complete as seguintes secções: **Criar um grupo de recursos,** **criar cluster AKS**e **ligar ao cluster**.

**Nota:** Se planeia usar a identidade do casulo em vez de um diretor de serviço. Certifique-se de que o ativa ao criar o cluster Kubernetes, como mostra abaixo:

```azurecli
az aks create -n contosoAKSCluster -g contosoResourceGroup --kubernetes-version 1.16.9 --node-count 1 --enable-managed-identity
```

1. [Desfice a variável ambiente PATH](https://www.java.com/en/download/help/path.xml) para o ficheiro "kubectl.exe" que foi descarregado.
1. Verifique a sua versão Kubernetes utilizando o comando abaixo. Este comando irá desativar a versão do cliente e do servidor. A versão do cliente é o "kubectl.exe" que instalou enquanto a versão do servidor é o Azure Kubernetes Services em que o seu cluster está a funcionar.
    ```azurecli
    kubectl version
    ```
1. Certifique-se de que a sua versão Kubernetes é **v1.16.0** ou superior. Este comando irá atualizar tanto o cluster Kubernetes como o conjunto de nós. Pode levar alguns minutos para executar. Neste exemplo, o grupo de recursos é **o ContosoResourceGroup** e o cluster Kubernetes é **contosoAKSCluster.**
    ```azurecli
    az aks upgrade --kubernetes-version 1.16.9 --name contosoAKSCluster --resource-group contosoResourceGroup
    ```
1. Exiba os metadados do cluster AKS que criou usando o comando abaixo. Copie o **principalId,** **clienteId,** **subscriçãoId**e **nodeResourceGroup**.
    ```azurecli
    az aks show --name contosoAKSCluster --resource-group contosoResourceGroup
    ```

    Esta é a saída com ambos os parâmetros realçados.
    
    ![](../media/kubernetes-key-vault-5.png) ![ Imagem de imagem](../media/kubernetes-key-vault-6.png)
    
## <a name="install-helm-and-secrets-store-csi-driver"></a>Instalar controlador CSI da Loja de Leme e Segredos

Terá de instalar o [Helm](https://helm.sh/docs/intro/install/) para instalar o controlador CSI Secrets Store.

A interface do controlador [CSI Secrets Store](https://github.com/Azure/secrets-store-csi-driver-provider-azure/blob/master/charts/csi-secrets-store-provider-azure/README.md) permite-lhe obter conteúdo secreto armazenado numa instância Azure Key Vault e usar a interface do condutor para montar esses conteúdos secretos em pods Kubernetes.

1. Verifique a versão Helm e certifique-se de que é v3 ou maior:
    ```azurecli
    helm version
    ```
1. Instale o controlador CSI Secrets Store e o fornecedor Azure Key Vault para o condutor:
    ```azurecli
    helm repo add csi-secrets-store-provider-azure https://raw.githubusercontent.com/Azure/secrets-store-csi-driver-provider-azure/master/charts

    helm install csi-secrets-store-provider-azure/csi-secrets-store-provider-azure --generate-name
    ```

## <a name="create-an-azure-key-vault-and-set-secrets"></a>Crie um cofre de chaves Azure e estabeleça segredos

Siga este [guia](https://docs.microsoft.com/azure/key-vault/secrets/quick-create-cli) para criar o seu próprio Cofre de Chaves e desemboe os seus segredos.

**Nota:** Não precisa de usar o Azure Cloud Shell ou criar um novo grupo de recursos. A utilização do grupo de recursos criado anteriormente para o cluster Kubernetes está bem.

## <a name="create-your-own-secretproviderclass-object"></a>Crie o seu próprio objeto SecretProviderClass

Utilize este [modelo](https://github.com/Azure/secrets-store-csi-driver-provider-azure/blob/master/examples/v1alpha1_secretproviderclass.yaml) fornecido para criar o seu próprio objeto SecretProviderClass personalizado para fornecer parâmetros específicos do fornecedor para o controlador CSI Secrets Store. Este objeto fornecerá acesso de identidade ao seu Cofre de Chaves.

Utilizando a amostra ficheiro SECRETProviderClass YAML fornecido. Vai preencher os parâmetros que faltam. São necessários os seguintes parâmetros:

1.  **utilizadorAssignedIdentityID:** ID do cliente do diretor de serviço
1.  **nome keyvault:** Nome do Cofre de Chaves
1.  **objetos:** Este objeto conterá todo o conteúdo secreto que pretende montar
    1.  **nome de objeto:** Nome do conteúdo secreto
    1.  **objectType:** Tipo de objeto (segredo, chave, certificado)
1.  **Grupo de recursos:** Nome do grupo de recursos
1.  **subscriçãoId:** ID de assinatura do Cofre chave
1.  **inquilinoID:** ID do inquilino (isto é, ID do Diretório) do Cofre-Chave

Abaixo está o modelo atualizado, descarregue-o como um ficheiro .yaml e preencha os campos necessários correspondentes. Neste exemplo, o Cofre chave é **contosoKeyVault5** e tem dois segredos, **o segredo1** e **o segredo2.**

**Nota:** Se estiver a utilizar identidades geridas, a entidade de utilização de **campoPodId tem** de ser **verdadeira** e deixar o utilizador de **campoAssignedIdentityID** com apenas citações **""**. 

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
    userAssignedIdentityID: "servicePrincipalClientID"       # [REQUIRED] If using a Service Principal, use the client id to specify which user assigned managed identity to use. If using a user assigned identity as the VM's managed identity, then specify the identity's client id. If empty, then defaults to use the system assigned identity on the VM
                                                             #     az ad sp show --id http://contosoServicePrincipal --query appId -o tsv
                                                             #     the above command will return the Client ID of your service principal
    keyvaultName: "contosoKeyVault5"          # [REQUIRED] the name of the Key Vault
                                              #     az keyvault show --name contosoKeyVault5
                                              #     the above command will displays the Key Vault metadata, which includes the subscription ID, resource group name, Key Vault 
    cloudName: ""                             # [OPTIONAL for Azure] if not provided, azure environment will default to AzurePublicCloud
    objects:  |
      array:
        - |
          objectName: secret1                 # [REQUIRED] object name
                                              #     az keyvault secret list --vault-name “contosoKeyVault5”
                                              #     the above command will display a list of secret names from your Key Vault
          objectType: secret                  # [REQUIRED] object types: secret, key or cert
          objectVersion: ""                   # [OPTIONAL] object versions, default to latest if empty
        - |
          objectName: secret2
          objectType: secret
          objectVersion: ""
    resourceGroup: "contosoResourceGroup"     # [REQUIRED] the resource group name of the Key Vault
    subscriptionId: "subscriptionID"          # [REQUIRED] the subscription ID of the Key Vault
    tenantId: "tenantID"                      # [REQUIRED] the tenant ID of the Key Vault
```
Abaixo está a saída da consola para "az keyvault show --name contosoKeyVault5" com os metadados destacados relevantes:

![Imagem](../media/kubernetes-key-vault-2.png)

## <a name="assign-your-service-principal-or-use-managed-identities"></a>Atribua o seu principal serviço ou utilize identidades geridas

### <a name="using-service-principal"></a>Utilização de Principal de Serviço

Se utilizar um diretor de serviço. Terá de dar permissão ao seu diretor de serviço para aceder ao seu Cofre-Chave e recuperar segredos. Atribua o papel **de "Reader"** e dê ao serviço permissão principal para **"obter"** segredos do seu Cofre de Chaves, completando os passos abaixo:

1. Atribua o principal de serviço ao cofre de chaves existente. O parâmetro **$AZURE_CLIENT_ID** é o **appId** que copiou depois de criar o seu principal de serviço.
    ```azurecli
    az role assignment create --role Reader --assignee $AZURE_CLIENT_ID --scope /subscriptions/$SUBID/resourcegroups/$KEYVAULT_RESOURCE_GROUP/providers/Microsoft.KeyVault/vaults/$KEYVAULT_NAME
    ```

    Abaixo está a saída do comando: 

    ![Imagem](../media/kubernetes-key-vault-3.png)

1. Dê permissão principal de serviço para obter segredos:
    ```azurecli
    az keyvault set-policy -n $KEYVAULT_NAME --secret-permissions get --spn $AZURE_CLIENT_ID
    ```

1. Agora que configuraste o teu diretor de serviço para ter permissão para ler segredos do teu Cofre de Chaves. O **$AZURE_CLIENTE_SECRET** é a **palavra-passe** do seu diretor de serviço. Adicione as suas credenciais principais de serviço como um segredo Kubernetes acessível pelo controlador CSI Secrets Store:
    ```azurecli
    kubectl create secret generic secrets-store-creds --from-literal clientid=$AZURE_CLIENT_ID --from-literal clientsecret=$AZURE_CLIENT_SECRET
    ```

**Nota:** Se receber um erro mais tarde ao implementar o pod Kubernetes sobre um ID secreto do cliente inválido. Pode ter um ID secreto do cliente mais antigo que tenha expirado ou reposto. Para resolver este problema, elimine os seus segredos "segredos-loja-creds" e crie um novo com o iD secreto do cliente atual. Executar o comando abaixo para eliminar os seus "segredos-loja-creds":
```azurecli
kubectl delete secrets secrets-store-creds
```

Se esqueceu o ID do cliente do seu chefe de serviço, pode repor-o usando o seguinte comando:

```azurecli
az ad sp credential reset --name contosoServicePrincipal --credential-description "APClientSecret" --query password -o tsv
```

### <a name="using-managed-identities"></a>Utilização de Identidades Geridas

Se utilizar identidades geridas, atribua funções específicas ao cluster AKS que criou. 
1. Para criar/listar/ler uma identidade gerida atribuída pelo utilizador, o seu cluster AKS precisa de ser atribuído à função [de Contribuinte de Identidade Gerida.](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#managed-identity-contributor) Certifique-se de que o **$clientId** é do cluster Kubernetes.

    ```azurecli
    az role assignment create --role "Managed Identity Contributor" --assignee $clientId --scope /subscriptions/$SUBID/resourcegroups/$NODE_RESOURCE_GROUP
    
    az role assignment create --role "Virtual Machine Contributor" --assignee $clientId --scope /subscriptions/$SUBID/resourcegroups/$NODE_RESOURCE_GROUP
    ```

1. Instale a identidade do Diretório Ativo Azure (Azure AD) em AKS.
    ```azurecli
    helm repo add aad-pod-identity https://raw.githubusercontent.com/Azure/aad-pod-identity/master/charts

    helm install pod-identity aad-pod-identity/aad-pod-identity
    ```

1. Criar uma identidade AD Azure. Copie o **clienteId** e **o directorid.**
    ```azurecli
    az identity create -g $resourceGroupName -n $identityName
    ```

1. Atribua o papel de leitor à Identidade AD Azure que acabaste de criar para o teu Cofre de Chaves. Então dê permissão de identidade para obter segredos do seu Cofre de Chaves. Vais usar o **clienteid** e **o diretor** da Identidade Azure que acabaste de criar.
    ```azurecli
    az role assignment create --role "Reader" --assignee $principalId --scope /subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups/contosoResourceGroup/providers/Microsoft.KeyVault/vaults/contosoKeyVault5

    az keyvault set-policy -n contosoKeyVault5 --secret-permissions get --spn $clientId
    ```

## <a name="deploy-your-pod-with-mounted-secrets-from-key-vault"></a>Desdobre a sua cápsula com segredos montados a partir do Cofre de Chaves

O comando abaixo configurará o seu objeto SecretProviderClass:
```azurecli
kubectl apply -f secretProviderClass.yaml
```

### <a name="using-service-principal"></a>Utilização de Principal de Serviço

Se utilizar um diretor de serviço. O comando abaixo irá implantar as suas cápsulas Kubernetes com o SecretProviderClass e os segredos-loja-creds que configuraste. Aqui está o modelo para a implantação [de linux](https://github.com/Azure/secrets-store-csi-driver-provider-azure/blob/master/examples/nginx-pod-secrets-store-inline-volume-secretproviderclass.yaml) e [janelas.](https://github.com/Azure/secrets-store-csi-driver-provider-azure/blob/master/examples/windows-pod-secrets-store-inline-volume-secret-providerclass.yaml)
```azurecli
kubectl apply -f updateDeployment.yaml
```

### <a name="using-managed-identities"></a>Utilização de Identidades Geridas

Se utilizar identidades geridas. Irá criar uma **AzureIdentity** no seu cluster que irá referenciar a identidade que criou anteriormente. Em seguida, crie uma **AzureIdentityBinding** que irá referenciar a **entidade AzureId** que criou. Utilize o modelo abaixo, preencha os parâmetros correspondentes e guarde-o como **podIdentityAndBinding.yaml**.  
```yml
apiVersion: aadpodidentity.k8s.io/v1
kind: AzureIdentity
metadata:
    name: "azureIdentityName"               # The name of your Azure Idenity
spec:
    type: 0                                 # Set type: 0 for Managed Service Identity
    resourceID: /subscriptions/<SUBID>/resourcegroups/<RESOURCEGROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<AZUREIDENTITYNAME>
    clientID: "managedIdentityClientId"     # The clientId of your Azure Identity that you created earlier
---
apiVersion: aadpodidentity.k8s.io/v1
kind: AzureIdentityBinding
metadata:
    name: azure-pod-identity-binding
spec:
    azureIdentity: "azureIdentityName"      # The name of your Azure Idenity
    selector: azure-pod-identity-binding-selector
```
    
Execute o seguinte comando para executar a ligação:

```azurecli
kubectl apply -f podIdentityAndBinding.yaml
```

Em seguida, a implantação real da cápsula. Abaixo está o ficheiro YAML de implantação que utilizará a ligação de identidade do casulo a partir do último passo. Guarde este ficheiro como **podBindingDeployment.yaml**.

```yml
kind: Pod
apiVersion: v1
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
```

Executar o seguinte comando para implantar a sua cápsula:

```azurecli
kubectl apply -f podBindingDeployment.yaml
```
### <a name="check-status-and-secret-content"></a>Verifique o estado e o conteúdo secreto 
Para mostrar as cápsulas que implementou:
```azurecli
kubectl get pods
```

Para verificar o estado da sua cápsula, utilize o seguinte comando:
```azurecli
kubectl describe pod/nginx-secrets-store-inline
```

![Imagem](../media/kubernetes-key-vault-4.png)

A cápsula implantada deve estar no estado "Running". Na secção "Eventos" na parte inferior, todos os tipos de eventos à esquerda são classificados como "Normal".
Assim que verificares que a cápsula está a funcionar, podes verificar se a tua cápsula tem os segredos do teu Cofre de Chaves.

Para mostrar todos os segredos que a cápsula tem:
```azurecli
kubectl exec -it nginx-secrets-store-inline -- ls /mnt/secrets-store/
```

Para obter conteúdo de um segredo específico:
```azurecli
kubectl exec -it nginx-secrets-store-inline -- cat /mnt/secrets-store/secret1
```

Verifique o conteúdo do segredo exibido.

## <a name="next-steps"></a>Próximos passos

Certifique-se de que o seu Cofre de Chaves é recuperável:
> [!div class="nextstepaction"]
> [Ligue a eliminação suave](https://docs.microsoft.com/azure/key-vault/general/soft-delete-clid)
