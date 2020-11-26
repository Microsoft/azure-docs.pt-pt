---
title: 'Tutorial: Ativar o addon do Controlador de Entrada para um novo cluster AKS com uma nova instância Azure Application Gateway'
description: Utilize este tutorial para aprender a usar o CLI Azure para ativar o addon do Controlador Ingress para o seu novo cluster AKS com uma nova instância de Gateway de aplicação.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: tutorial
ms.date: 09/24/2020
ms.author: caya
ms.openlocfilehash: 627d5b15a861c3d564cb4db33b366d3227092d37
ms.sourcegitcommit: 192f9233ba42e3cdda2794f4307e6620adba3ff2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/26/2020
ms.locfileid: "96296272"
---
# <a name="tutorial-enable-the-ingress-controller-add-on-preview-for-a-new-aks-cluster-with-a-new-application-gateway-instance"></a>Tutorial: Ativar o addon do Controlador de Ingress (pré-visualização) para um novo cluster AKS com uma nova instância de Gateway de aplicações

Pode utilizar o Azure CLI para ativar o addon do Controlador de Entradas de [Gateway (AGIC)](ingress-controller-overview.md) para um cluster [Azure Kubernetes Services (AKS).](https://azure.microsoft.com/services/kubernetes-service/) O addon está atualmente em pré-visualização.

Neste tutorial, você vai criar um cluster AKS com o addon AGIC ativado. A criação do cluster criará automaticamente uma instância Azure Application Gateway para usar. Em seguida, irá implementar uma aplicação de amostra que utilizará o addon para expor a aplicação através do Application Gateway. 

O addon fornece uma maneira muito mais rápida de implantar a AGIC para o seu cluster AKS do que [anteriormente através do Helm.](ingress-controller-overview.md#difference-between-helm-deployment-and-aks-add-on) Também oferece uma experiência totalmente gerida.    

Neste tutorial, vai aprender a:

> [!div class="checklist"]
> * Crie um grupo de recursos. 
> * Crie um novo cluster AKS com o addon AGIC ativado. 
> * Implementar uma aplicação de amostra utilizando a AGIC para a entrada no cluster AKS.
> * Verifique se a aplicação está acessível através do Application Gateway.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

 - Este tutorial requer a versão 2.0.4 ou posterior do CLI Azure. Se utilizar o Azure Cloud Shell, a versão mais recente já está instalada. Se utilizar o Azure CLI, tem de instalar a extensão de pré-visualização no CLI utilizando o seguinte comando, se não for já:
    ```azurecli-interactive
    az extension add --name aks-preview
    ```

 - Registe a bandeira de recurso *AKS-IngressApplicationGatewayAddon* utilizando o comando [de registo de funcionalidades AZ,](/cli/azure/feature#az-feature-register) como mostra o exemplo seguinte. Terá de o fazer apenas uma vez por subscrição enquanto o addon ainda estiver em pré-visualização.
    ```azurecli-interactive
    az feature register --name AKS-IngressApplicationGatewayAddon --namespace Microsoft.ContainerService
    ```

   Pode levar alguns minutos para que o estado `Registered` apareça. Pode verificar o estado de registo utilizando o comando [da lista de funcionalidades AZ:](/cli/azure/feature#az-feature-register)
    ```azurecli-interactive
    az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKS-IngressApplicationGatewayAddon')].{Name:name,State:properties.state}"
    ```

 - Quando estiver pronto, reencaite o registo do fornecedor de recursos Microsoft.ContainerService utilizando o comando [de registo do fornecedor az:](/cli/azure/provider#az-provider-register)
    ```azurecli-interactive
    az provider register --namespace Microsoft.ContainerService
    ```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Em Azure, aloca recursos relacionados a um grupo de recursos. Crie um grupo de recursos utilizando [o grupo Az create](/cli/azure/group#az-group-create). O exemplo a seguir cria um grupo de recursos chamado *myResourceGroup* na localização central do *Canadá* (região): 

```azurecli-interactive
az group create --name myResourceGroup --location canadacentral
```

## <a name="deploy-an-aks-cluster-with-the-add-on-enabled"></a>Implementar um cluster AKS com o add-on ativado

Irá agora implantar um novo cluster AKS com o addon AGIC ativado. Se não fornecer uma instância de Gateway de aplicação existente para usar neste processo, criaremos automaticamente e configuraremos uma nova instância de Gateway de aplicação para servir o tráfego ao cluster AKS.  

> [!NOTE]
> O addon do Controlador de Entrada de Gateway de Aplicação suporta *apenas* o Application Gateway v2 SKUs (Standard e WAF), e *não* o Application Gateway v1 SKUs. Quando estiver a implementar uma nova instância 'Application Gateway' através do add-on AGIC, pode implementar apenas um Gateway de Aplicação Standard_v2 SKU. Se pretender ativar o addon para um Gateway de aplicação WAF_v2 SKU, utilize qualquer um destes métodos:
>
> - Ativar o WAF no Gateway de Aplicações através do portal. 
> - Crie primeiro a instância WAF_v2 Application Gateway e, em seguida, siga instruções sobre como [ativar o addon AGIC com um cluster AKS existente e a instância de Gateway de aplicação existente](tutorial-ingress-controller-add-on-existing.md). 

No exemplo seguinte, você vai implementar um novo cluster AKS chamado *myCluster* usando [Azure CNI](../aks/concepts-network.md#azure-cni-advanced-networking) e [identidades geridas](../aks/use-managed-identity.md). O addon AGIC será ativado no grupo de recursos que criou, *o myResourceGroup*. 

A implantação de um novo cluster AKS com o addon AGIC ativado sem especificar uma instância de Gateway de aplicação existente significará uma criação automática de uma Standard_v2 instância de Gateway de aplicação SKU. Assim, também irá especificar o nome e o espaço de endereço da sub-rede da instância Application Gateway. O nome da instância Do Gateway de Aplicação será *myApplicationGateway*, e o espaço de endereço da sub-rede que estamos a usar é 10.2.0.0/16. Certifique-se de que adicionou ou atualizou a extensão de pré-visualização de aks no início deste tutorial. 

```azurecli-interactive
az aks create -n myCluster -g myResourceGroup --network-plugin azure --enable-managed-identity -a ingress-appgw --appgw-name myApplicationGateway --appgw-subnet-prefix "10.2.0.0/16" --generate-ssh-keys
```

Para configurar parâmetros adicionais para o `az aks create` comando, consulte [estas referências](/cli/azure/aks?view=azure-cli-latest#az-aks-create). 

> [!NOTE]
> O cluster AKS que criou aparecerá no grupo de recursos que criou, *o myResourceGroup*. No entanto, a instância de Gateway de aplicação criada automaticamente estará no grupo de recursos de nó, onde estão os pools do agente. O grupo de recursos de nó é nomeado *MC_resource grupo-name_cluster-name_location* por padrão, mas pode ser modificado. 

## <a name="deploy-a-sample-application-by-using-agic"></a>Implementar uma aplicação de amostra utilizando a AGIC

Agora vais implementar uma aplicação de amostra para o cluster AKS que criaste. A aplicação utilizará o addon AGIC para entrada e ligará a instância gateway de aplicação ao cluster AKS. 

Primeiro, obtenha credenciais para o cluster AKS executando o `az aks get-credentials` comando: 

```azurecli-interactive
az aks get-credentials -n myCluster -g myResourceGroup
```

Agora que tem credenciais, faça o seguinte comando para configurar uma aplicação de amostra que usa a AGIC para entrar no cluster. A AGIC atualizará a instância do Gateway de Aplicação que configurara anteriormente com as correspondentes regras de encaminhamento para a nova aplicação de amostra que implementou.  

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/master/docs/examples/aspnetapp.yaml 
```

## <a name="check-that-the-application-is-reachable"></a>Verifique se a aplicação está acessível

Agora que a instância 'Application Gateway' está configurada para servir o tráfego ao cluster AKS, vamos verificar se a sua aplicação está acessível. Primeiro, obtenha o endereço IP da entrada: 

```azurecli-interactive
kubectl get ingress
```

Verifique se a aplicação de amostra que criou está a ser em execução por:

- Visitando o endereço IP da instância 'Application Gateway' que obteve da execução do comando anterior.
- A utilização `curl` . 

O Gateway de Aplicação pode demorar um minuto a receber a atualização. Se o Gateway de Aplicação ainda estiver em estado **de Atualização** no portal, deixe-o terminar antes de tentar chegar ao endereço IP. 

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não precisar deles, remova o grupo de recursos, a instância De Gateway de Aplicação e todos os recursos relacionados:

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Saiba como desativar o add-on da AGIC](./ingress-controller-disable-addon.md)
