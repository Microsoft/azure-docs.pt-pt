---
title: Use identidades geridas por cápsulas Azure Ative pod no Serviço Azure Kubernetes (Pré-visualização)
description: Saiba como utilizar identidades geridas por cápsulas AAD no Serviço Azure Kubernetes (AKS)
services: container-service
ms.topic: article
ms.date: 12/01/2020
ms.openlocfilehash: 22b7a03a8598aa6e4b7c392567905d467776360c
ms.sourcegitcommit: f82e290076298b25a85e979a101753f9f16b720c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/04/2021
ms.locfileid: "99557354"
---
# <a name="use-azure-active-directory-pod-managed-identities-in-azure-kubernetes-service-preview"></a>Use identidades geridas por cápsulas Azure Ative pod no Serviço Azure Kubernetes (Pré-visualização)

As identidades geridas pelo Azure Ative Directory usam primitivos Kubernetes para associar [identidades geridas para recursos][az-managed-identities] e identidades Azure Ative (AAD) com cápsulas. Os administradores criam identidades e encadernações como primitivos Kubernetes que permitem que as cápsulas acedam aos recursos da Azure que dependem da AAD como fornecedor de identidade.

> [!NOTE]
> Se tiver uma instalação existente de AADPODIDENTITY, deve remover a instalação existente. Ativar esta funcionalidade significa que o componente MIC não é necessário.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="before-you-begin"></a>Antes de começar

Deve ter o seguinte recurso instalado:

* O Azure CLI, versão 2.8.0 ou mais tarde
* A `azure-preview` versão de extensão 0.4.68 ou mais tarde

### <a name="limitations"></a>Limitações

* Um máximo de 50 identidades de vagem são permitidas para um cluster.
* Um máximo de 50 cápsulas de exceção de identidade são permitidas para um cluster.
* Identidades geridas por pod estão disponíveis apenas nas piscinas de nóleiros Linux.

### <a name="register-the-enablepodidentitypreview"></a>Registar o `EnablePodIdentityPreview`

Registar a `EnablePodIdentityPreview` função:

```azurecli
az feature register --name EnablePodIdentityPreview --namespace Microsoft.ContainerService
```

### <a name="install-the-aks-preview-azure-cli"></a>Instale o `aks-preview` Azure CLI

Também precisa da versão de extensão Azure CLI *de pré-visualização aks* 0.4.64 ou posterior. Instale a extensão Azure CLI *de pré-visualização aks* utilizando o comando [de adicionar extensão az.][az-extension-add] Ou instale quaisquer atualizações disponíveis utilizando o comando [de atualização de extensão az.][az-extension-update]

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

## <a name="create-an-aks-cluster-with-managed-identities"></a>Criar um cluster AKS com identidades geridas

Crie um cluster AKS com uma identidade gerida e uma identidade gerida por cápsulas. Os seguintes comandos usam [o grupo AZ][az-group-create] para criar um grupo de recursos chamado *myResourceGroup* e os [az aks criam][az-aks-create] comando para criar um cluster AKS chamado *myAKSCluster* no grupo de recursos *myResourceGroup.*

```azurecli-interactive
az group create --name myResourceGroup --location eastus
az aks create -g myResourceGroup -n myAKSCluster --enable-managed-identity --enable-pod-identity --network-plugin azure
```

Use [credenciais az aks][az-aks-get-credentials] para iniciar sação no seu cluster AKS. Este comando também descarrega e configura o certificado de `kubectl` cliente no seu computador de desenvolvimento.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

## <a name="create-an-identity"></a>Criar uma identidade

Crie uma identidade usando [a identidade az criar][az-identity-create] e definir as *variáveis IDENTITY_CLIENT_ID* e *IDENTITY_RESOURCE_ID.*

```azurecli-interactive
az group create --name myIdentityResourceGroup --location eastus
export IDENTITY_RESOURCE_GROUP="myIdentityResourceGroup"
export IDENTITY_NAME="application-identity"
az identity create --resource-group ${IDENTITY_RESOURCE_GROUP} --name ${IDENTITY_NAME}
export IDENTITY_CLIENT_ID="$(az identity show -g ${IDENTITY_RESOURCE_GROUP} -n ${IDENTITY_NAME} --query clientId -otsv)"
export IDENTITY_RESOURCE_ID="$(az identity show -g ${IDENTITY_RESOURCE_GROUP} -n ${IDENTITY_NAME} --query id -otsv)"
```

## <a name="create-a-pod-identity"></a>Criar uma identidade de vagem

Crie uma identidade de pod para o cluster utilizando `az aks pod-identity add` .

> [!IMPORTANT]
> Tem de ter as permissões adequadas, tais `Owner` como, na sua subscrição para criar a identidade e a função vinculativas.

```azurecli-interactive
export POD_IDENTITY_NAME="my-pod-identity"
export POD_IDENTITY_NAMESPACE="my-app"
az aks pod-identity add --resource-group myResourceGroup --cluster-name myAKSCluster --namespace ${POD_IDENTITY_NAMESPACE}  --name ${POD_IDENTITY_NAME} --identity-resource-id ${IDENTITY_RESOURCE_ID}
```

> [!NOTE]
> Quando ativa a identidade gerida pelo pod no seu cluster AKS, é adicionada uma *algase-aks-addon-exceção* ao espaço de nomes *do sistema kube.* Uma AzurePodIdentityExcepção permite que cápsulas com determinadas etiquetas acedam ao ponto final do Azure Instance Metadata Service (IMDS) sem serem intercetados pelo servidor de identidade gerida por nó (NMI). A *aks-addon-exception* permite que addons de primeira parte da AKS, como a identidade gerida por cápsulas AAD, funcionem sem ter de configurar manualmente uma AzurePodIdentityException. Opcionalmente, pode adicionar, remover e atualizar uma AzurePodIdentityExcepção utilizando `az aks pod-identity exception add` `az aks pod-identity exception delete` , ou `az aks pod-identity exception update` `kubectl` .

## <a name="run-a-sample-application"></a>Executar uma aplicação de amostra

Para que um casulo utilize identidade gerida por cápsulas AAD, o casulo necessita de uma etiqueta *de aadpodidbinding* com um valor que corresponda a um seletor de uma *AzureIdentityBinding*. Para executar uma aplicação de amostra usando a identidade gerida por pod AAD, crie um `demo.yaml` ficheiro com os seguintes conteúdos. Substitua *POD_IDENTITY_NAME*, *IDENTITY_CLIENT_ID*, e *IDENTITY_RESOURCE_GROUP* pelos valores dos passos anteriores. Substitua *SUBSCRIPTION_ID* pelo seu id de assinatura.

> [!NOTE]
> Nos passos anteriores, criou as *variáveis POD_IDENTITY_NAME*, *IDENTITY_CLIENT_ID* e *IDENTITY_RESOURCE_GROUP.* Pode utilizar um comando como `echo` mostrar o valor que definiu para variáveis, por exemplo `echo $IDENTITY_NAME` .

```yml
apiVersion: v1
kind: Pod
metadata:
  name: demo
  labels:
    aadpodidbinding: POD_IDENTITY_NAME
spec:
  containers:
  - name: demo
    image: mcr.microsoft.com/oss/azure/aad-pod-identity/demo:v1.6.3
    args:
      - --subscriptionid=SUBSCRIPTION_ID
      - --clientid=IDENTITY_CLIENT_ID
      - --resourcegroup=IDENTITY_RESOURCE_GROUP
    env:
      - name: MY_POD_NAME
        valueFrom:
          fieldRef:
            fieldPath: metadata.name
      - name: MY_POD_NAMESPACE
        valueFrom:
          fieldRef:
            fieldPath: metadata.namespace
      - name: MY_POD_IP
        valueFrom:
          fieldRef:
            fieldPath: status.podIP
  nodeSelector:
    kubernetes.io/os: linux
```

Note que a definição do pod tem uma etiqueta *de aadpodidbinding* com um valor que corresponde ao nome da identidade do pod que executou `az aks pod-identity add` no passo anterior.

Implemente `demo.yaml` para o mesmo espaço de nome que a sua identidade de vagem `kubectl apply` utilizando:

```azurecli-interactive
kubectl apply -f demo.yaml --namespace $POD_IDENTITY_NAMESPACE
```

Verifique se a aplicação da amostra funciona com sucesso utilizando `kubectl logs` .

```azurecli-interactive
kubectl logs demo --follow --namespace $POD_IDENTITY_NAMESPACE
```

Verifique se os registos mostram que o token foi adquirido com sucesso e a operação *GET* é bem sucedida.
 
```output
...
successfully doARMOperations vm count 0
successfully acquired a token using the MSI, msiEndpoint(http://169.254.169.254/metadata/identity/oauth2/token)
successfully acquired a token, userAssignedID MSI, msiEndpoint(http://169.254.169.254/metadata/identity/oauth2/token) clientID(xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx)
successfully made GET on instance metadata
...
```

## <a name="clean-up"></a>Limpeza

Para remover a identidade gerida pelo pod AAD do seu cluster, remova a aplicação da amostra e a identidade do casulo do cluster. Em seguida, remova a identidade.

```azurecli-interactive
kubectl delete pod demo --namespace $POD_IDENTITY_NAMESPACE
az aks pod-identity delete --name ${POD_IDENTITY_NAME} --namespace ${POD_IDENTITY_NAMESPACE} --resource-group myResourceGroup --cluster-name myAKSCluster
az identity delete -g ${IDENTITY_RESOURCE_GROUP} -n ${IDENTITY_NAME}
```

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre identidades geridas, consulte [identidades geridas para recursos Azure][az-managed-identities].

<!-- LINKS - external -->
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-extension-add]: /cli/azure/extension?view=azure-cli-latest#az-extension-add&preserve-view=true
[az-extension-update]: /cli/azure/extension?view=azure-cli-latest#az-extension-update&preserve-view=true
[az-group-create]: /cli/azure/group#az-group-create
[az-identity-create]: /cli/azure/identity?view=azure-cli-latest#az_identity_create
[az-managed-identities]: ../active-directory/managed-identities-azure-resources/overview.md
[az-role-assignment-create]: /cli/azure/role/assignment?view=azure-cli-latest#az_role_assignment_create
