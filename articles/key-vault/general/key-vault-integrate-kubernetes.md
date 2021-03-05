---
title: Integrar o Azure Key Vault no Kubernetes
description: Neste tutorial, você acede e obtém segredos do seu cofre de chaves Azure usando o controlador Secrets Store Container Storage Interface (CSI) para montar em cápsulas Kubernetes.
author: msmbaldwin
ms.author: mbaldwin
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 09/25/2020
ms.openlocfilehash: b130fd3f85b676f0a394ad95730181ff499dac96
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2021
ms.locfileid: "102216501"
---
# <a name="tutorial-configure-and-run-the-azure-key-vault-provider-for-the-secrets-store-csi-driver-on-kubernetes"></a>Tutorial: Configurar e executar o fornecedor Azure Key Vault para o motorista CSI Secrets Store em Kubernetes

> [!IMPORTANT]
> Secrets Store CSI Driver é um projeto de código aberto que não é suportado pelo suporte técnico da Azure. Por favor, informe todos os comentários e problemas relacionados com a integração do CSI Driver Key Vault no link github na parte inferior da página. Esta ferramenta é fornecida para que os utilizadores se auto-instalem em clusters e recolham feedback da nossa comunidade.

Neste tutorial, você acede e obtém segredos do seu cofre de chaves Azure usando o controlador Secrets Store Container Storage Interface (CSI) para montar os segredos em cápsulas Kubernetes.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Use identidades geridas.
> * Implementar um cluster Azure Kubernetes Service (AKS) utilizando o Azure CLI.
> * Instale helm, o controlador CSI Secrets Store e o Fornecedor de Cofre de Chaves Azure para o controlador CSI.
> * Crie um cofre de chaves Azure e desconfiem dos vossos segredos.
> * Crie o seu próprio objeto SecretProviderClass.
> * Desdobre a sua cápsula com segredos montados do cofre das chaves.

## <a name="prerequisites"></a>Pré-requisitos

* Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

* Antes de iniciar este tutorial, instale o [Azure CLI](/cli/azure/install-azure-cli-windows).

Este tutorial pressupõe que está a gerir o Serviço Azure Kubernetes em nós Linux.

## <a name="use-managed-identities"></a>Utilizar identidades geridas

Este diagrama ilustra o fluxo de integração do Cofre AKS-Key para identidade gerida:

![Diagrama que mostra o fluxo de integração do Cofre AKS-Key para identidade gerida](../media/aks-key-vault-integration-flow.png)

## <a name="deploy-an-azure-kubernetes-service-aks-cluster-by-using-the-azure-cli"></a>Implementar um cluster Azure Kubernetes Service (AKS) utilizando o Azure CLI

Não precisas de usar a Azure Cloud Shell. O seu pedido de comando (terminal) com o CLI Azure instalado será suficiente. 

Complete as secções "Criar um grupo de recursos", "Criar cluster AKS" e "Ligar ao cluster" em [Implementar um cluster de serviço Azure Kubernetes utilizando o CLI Azure](../../aks/kubernetes-walkthrough.md). 

> [!NOTE] 
> Se pretender utilizar a identidade do casulo, o plugin de rede recomendado é `azure` . Consulte o [doc](https://azure.github.io/aad-pod-identity/docs/configure/aad_pod_identity_on_kubenet/) para mais detalhes. Crie o cluster Kubernetes, como mostra o seguinte comando:
>
> ```azurecli
> az aks create -n contosoAKSCluster -g contosoResourceGroup --kubernetes-version 1.16.9 --node-count 1 --enable-managed-identity
> ```

1. [Desfie a variável ambiente PATH](https://www.java.com/en/download/help/path.xml) para o ficheiro *kubectl.exe* que descarregou.
2. Verifique a sua versão Kubernetes utilizando o seguinte comando, que produz a versão do cliente e do servidor. A versão do cliente é o *ficheirokubectl.exe* que instalou, e a versão do servidor é o Azure Kubernetes Services (AKS) em que o seu cluster está a funcionar.
    ```azurecli
    kubectl version
    ```
3. Certifique-se de que a sua versão Kubernetes é 1.16.0 ou mais tarde. Para os agrupamentos de janelas, certifique-se de que a sua versão Kubernetes é 1.18.0 ou mais tarde. O comando seguinte atualiza tanto o cluster Kubernetes como o conjunto de nós. O comando pode levar alguns minutos para ser executado. Neste exemplo, o grupo de recursos é *o ContosoResourceGroup,* e o cluster Kubernetes é *contosoAKSCluster*.
    ```azurecli
    az aks upgrade --kubernetes-version 1.16.9 --name contosoAKSCluster --resource-group contosoResourceGroup
    ```
4. Para exibir os metadados do cluster AKS que criou, utilize o seguinte comando. Copie o **principalId,** **clientId,** **subscriçãoId** e **nodeResourceGroup** para posterior utilização. Se o cluster AKS não foi criado com identidades geridas ativadas, o **principalid** e **clienteid** será nulo. 

    ```azurecli
    az aks show --name contosoAKSCluster --resource-group contosoResourceGroup
    ```

    A saída mostra ambos os parâmetros realçados:
    
    ![Screenshot do CLI Azure com valores principalid e clienteId destacado ](../media/kubernetes-key-vault-2.png) ![ Screenshot do CLI Azure com valores de subscriçãoId e nodeResourceGroup destacados](../media/kubernetes-key-vault-3.png)
    
## <a name="install-helm-and-the-secrets-store-csi-driver"></a>Instale o leme e o controlador CSI secrets Store
> [!NOTE]
> Abaixo a instalação funciona apenas em AKS em Linux. Para obter mais informações sobre a instalação do controlador CSI Secrets Store, consulte [o Fornecedor de Cofres chave Azure para o controlador CSI Secrets Store](https://azure.github.io/secrets-store-csi-driver-provider-azure/getting-started/installation/) 

Para instalar o controlador CSI Secrets Store e o fornecedor Azure Key Vault, primeiro tem de instalar [o Helm](https://helm.sh/docs/intro/install/).

Com a interface do controlador [CSI Secrets Store,](https://azure.github.io/secrets-store-csi-driver-provider-azure/) pode obter os segredos que estão armazenados na sua instância Azure Key Vault e, em seguida, usar a interface do condutor para montar os conteúdos secretos em cápsulas Kubernetes.

1. Verifique se a versão Helm é v3 ou posterior:
    ```azurecli
    helm version
    ```
1. Instale o controlador CSI Secrets Store e o fornecedor Azure Key Vault para o condutor:
    ```azurecli
    helm repo add csi-secrets-store-provider-azure https://raw.githubusercontent.com/Azure/secrets-store-csi-driver-provider-azure/master/charts

    helm install csi-secrets-store-provider-azure/csi-secrets-store-provider-azure --generate-name
    ```
> [!NOTE] 
> Se planeia utilizar o controlador CSI da Secrets Store e o fornecedor Azure Key Vault nos nos acenos das janelas, ative o condutor e o fornecedor nos nos acenos do windows utilizando os [valores de configuração](https://github.com/Azure/secrets-store-csi-driver-provider-azure/tree/master/charts/csi-secrets-store-provider-azure#configuration) do leme

## <a name="create-an-azure-key-vault-and-set-your-secrets"></a>Crie um cofre de chaves Azure e desconfiem dos seus segredos

Para criar o seu próprio cofre-chave e definir os seus segredos, siga as instruções em [Conjunto e recupere um segredo do Azure Key Vault utilizando o Azure CLI](../secrets/quick-create-cli.md).

> [!NOTE] 
> Não precisa de usar a Azure Cloud Shell ou criar um novo grupo de recursos. Pode utilizar o grupo de recursos que criou anteriormente para o cluster Kubernetes.

## <a name="create-your-own-secretproviderclass-object"></a>Crie o seu próprio objeto SecretProviderClass

Para criar o seu próprio objeto SecretProviderClass personalizado com parâmetros específicos do fornecedor para o controlador CSI Secrets Store, [utilize este modelo](https://github.com/Azure/secrets-store-csi-driver-provider-azure/blob/master/examples/service-principal/v1alpha1_secretproviderclass_service_principal.yaml). Este objeto fornecerá acesso de identidade ao seu cofre de chaves.

Na amostra, o ficheiro IML da SecretProviderClass preenche os parâmetros em falta. São necessários os seguintes parâmetros:

* **keyvaultName**: O nome do seu cofre chave
* **objetos**: lista de todos os conteúdos secretos que pretende montar
    * **nome do objeto**: O nome do conteúdo secreto
    * **objectType**: O tipo de objeto (segredo, chave, certificado)
* **tenantID**: O ID do inquilino, ou iD diretório, do seu cofre chave

Documentação de todos os campos necessários e configurações suportadas está disponível aqui: [Link](https://azure.github.io/secrets-store-csi-driver-provider-azure/getting-started/usage/#create-your-own-secretproviderclass-object)

O modelo atualizado é mostrado no seguinte código. Descarregue-o como um ficheiro YAML e preencha os campos necessários. Neste exemplo, o cofre chave é **contosoKeyVault5**. Tem dois segredos, **o segredo1** e **o segredo2.**

> [!NOTE] 
> Se estiver a utilizar identidades de pod, desaprote o valor **da utilizaçãoPodIdentity** como *verdadeiro*, e desaprote o valor **de IMU do utilizador** como um par de aspas **(""**). Se estiver a utilizar identidades geridas, desajuste o valor **da entidade utilizaVMManagedId como** *verdadeiro*, e desajuste o valor **de IMSindentityID** do utilizador como clienteID da identidade atribuída ao utilizador.

```yaml
apiVersion: secrets-store.csi.x-k8s.io/v1alpha1
kind: SecretProviderClass
metadata:
  name: azure-kvname
spec:
  provider: azure
  parameters:
    usePodIdentity: "false"                                                 # [OPTIONAL] if not provided, will default to "false". Set to "true" if using pod identities.
    useVMManagedIdentity: "false"                                           # [OPTIONAL] if not provided, will default to "false". Set to "true" if using managed identities.
    userAssignedIdentityID: "<clientID of user-assigned managed identity"   # [OPTIONAL] If you're using managed identities, use the client id to specify which user-assigned managed identity to use. If the value is empty, it defaults to use the system-assigned identity on the VM
    keyvaultName: "contosoKeyVault5"                                        # [REQUIRED] the name of the key vault
                                                                            #     az keyvault show --name contosoKeyVault5
                                                                            #     the preceding command will display the key vault metadata, which includes the subscription ID, resource group name, key vault 
    cloudName: ""                                                           # [OPTIONAL] if not provided, Azure environment will default to AzurePublicCloud
    objects:  |
      array:
        - |
          objectName: secret1                                               # [REQUIRED] object name
                                                                            #     az keyvault secret list --vault-name "contosoKeyVault5"
                                                                            #     the above command will display a list of secret names from your key vault
          objectType: secret                                                # [REQUIRED] object types: secret, key, or cert
          objectVersion: ""                                                 # [OPTIONAL] object versions, default to latest if empty
        - |
          objectName: secret2
          objectType: secret
          objectVersion: ""
    tenantId: "tenantID"                                                    # [REQUIRED] the tenant ID of the key vault
```
A imagem a seguir mostra a saída da consola para **a az keyvault show -- nome contosoKeyVault5** com os metadados destacados relevantes:

![Screenshot mostrando a saída da consola para "az keyvault show -- nome contosoKeyVault5"](../media/kubernetes-key-vault-4.png)

## <a name="install-azure-active-directory-azure-ad-pod-identity"></a>Instalar a identidade do Casulo Azure Ative (Azure AD)

1. Atribua funções específicas ao cluster AKS que criou. 

    Documentação para todas as atribuições de funções necessárias com a identidade do pod do Azure Ative (Azure AD) aqui: [Link](https://azure.github.io/aad-pod-identity/docs/getting-started/role-assignment/)

    ```azurecli
    RESOURCE_GROUP=contosoResourceGroup
    
    az role assignment create --role "Managed Identity Operator" --assignee $clientId --scope /subscriptions/<SUBID>/resourcegroups/$RESOURCE_GROUP
    
    az role assignment create --role "Virtual Machine Contributor" --assignee $clientId --scope /subscriptions/<SUBID>/resourcegroups/$RESOURCE_GROUP
    ```

2. Instale a identidade do Azure Ative Directory (Azure AD) em AKS.

    > [!NOTE] 
    > Se utilizar um cluster AKS com plugin de rede kubenet, reveja este [doc](https://azure.github.io/aad-pod-identity/docs/configure/aad_pod_identity_on_kubenet/) sobre como implantar a identidade do casulo no cluster.

    ```azurecli
    helm repo add aad-pod-identity https://raw.githubusercontent.com/Azure/aad-pod-identity/master/charts

    helm install pod-identity aad-pod-identity/aad-pod-identity
    ```

3. Criar uma identidade gerida User-Assigned. Na saída, copie o **clienteId** para utilização posterior.
    ```azurecli
    az identity create -g $resourceGroupName -n $identityName
    ```

4. Conceda as permissões de identidade para obter segredos do seu cofre. Utilize o **clientId** a partir da identidade gerida atribuída pelo Utilizador.
    ```azurecli
    az keyvault set-policy -n contosoKeyVault5 --secret-permissions get --spn $clientId
    az keyvault set-policy -n contosoKeyVault5 --key-permissions get --spn $clientId
    az keyvault set-policy -n contosoKeyVault5 --certificate-permissions get --spn $clientId
    ```

## <a name="deploy-your-pod-with-mounted-secrets-from-your-key-vault"></a>Desdobre a sua cápsula com segredos montados a partir do seu cofre de chaves

Para configurar o seu objeto SecretProviderClass, executar o seguinte comando:
```azurecli
kubectl apply -f secretProviderClass.yaml
```

### <a name="use-managed-identities"></a>Utilizar identidades geridas

Se estiver a usar identidades de pod, crie uma *entidade AzureId* no seu cluster que refira a identidade que criou anteriormente. Em seguida, crie uma *AzureIdentityBinding* que faz referência à entidade AzureId que criou. Preencha os parâmetros no modelo seguinte e, em seguida, guarde-os como *podIdentityAndBinding.yaml*.  

```yml
apiVersion: aadpodidentity.k8s.io/v1
kind: AzureIdentity
metadata:
    name: azureIdentityName                 # The name of your Azure identity
spec:
    type: 0                                 # Set type: 0 for managed identity
    resourceID: /subscriptions/<SUBID>/resourcegroups/<RESOURCEGROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<AZUREIDENTITYNAME>
    clientID: "<managed identity clientID>"   # The clientId of the User-assigned managed identity that you created earlier
---
apiVersion: aadpodidentity.k8s.io/v1
kind: AzureIdentityBinding
metadata:
    name: azure-pod-identity-binding
spec:
    azureIdentity: "azureIdentityName"      # The name of your Azure identity
    selector: azure-pod-identity-binding-selector
```
    
Executar o seguinte comando para configurar a ligação:

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
    aadpodidbinding: azure-pod-identity-binding-selector # The selector defined in AzureIdentityBinding in the previous step
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
kubectl exec nginx-secrets-store-inline -- ls /mnt/secrets-store/
```

Para exibir o conteúdo de um segredo específico, executar o seguinte comando:
```azurecli
kubectl exec nginx-secrets-store-inline -- cat /mnt/secrets-store/secret1
```

Verifique se o conteúdo do segredo está exposto.

## <a name="next-steps"></a>Passos seguintes

Para ajudar a garantir que o cofre da chave é recuperável, consulte:
> [!div class="nextstepaction"]
> [Ligue a eliminação suave](./key-vault-recovery.md)
