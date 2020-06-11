---
title: Ativar o Add-On do Controlador ingresso para o novo cluster AKS com o novo Gateway de aplicações Azure
description: Utilize este tutorial para ativar o Add-On do Controlador de Entrada para o seu novo cluster AKS com um novo Gateway de aplicações
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 06/10/2020
ms.author: caya
ms.openlocfilehash: 8595b7fc37a46dbb27dec4d1388e4b0251606411
ms.sourcegitcommit: eeba08c8eaa1d724635dcf3a5e931993c848c633
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/10/2020
ms.locfileid: "84670937"
---
# <a name="tutorial-enable-application-gateway-ingress-controller-add-on-for-a-new-aks-cluster-with-a-new-application-gateway-through-azure-cli-preview"></a>Tutorial: Ativar o addon do Controlador de Entradas de Gateway de Aplicação para um novo cluster AKS com um novo Gateway de aplicações através do Azure CLI (Pré-visualização)

Pode utilizar o Azure CLI para ativar o addon [do Controlador de Entrada de Entrada de Aplicação (AGIC),](ingress-controller-overview.md) que está atualmente em pré-visualização, para o seu cluster [Azure Kubernetes Services (AKS).](https://azure.microsoft.com/services/kubernetes-service/) Neste tutorial, irá criar um cluster AKS com o addon AGIC ativado que criará automaticamente um Gateway de Aplicação para utilizar. Em seguida, irá implementar uma aplicação de amostra, que utilizará o addon AGIC para expor a aplicação através do Gateway de Aplicação. O addon fornece uma forma muito mais rápida de implementar a AGIC para o seu cluster AKS do que [anteriormente através do Helm](ingress-controller-overview.md#difference-between-helm-deployment-and-aks-add-on) e também oferece uma experiência totalmente gerida.    

Neste tutorial, vai aprender a:

> [!div class="checklist"]
> * Criar um grupo de recursos 
> * Criar um novo cluster AKS com add-on AGIC ativado 
> * Implementar uma aplicação de amostra utilizando o AGIC para Ingress no cluster AKS
> * Verifique se a aplicação é acessível através do Gateway de Aplicação

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar a CLI localmente, este tutorial requer a execução da versão 2.0.4 ou posterior da CLI do Azure. Para localizar a versão, execute `az --version`. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)](/cli/azure/install-azure-cli).

Registar a bandeira de recurso *AKS-IngressApplicationGatewayAddon* utilizando o comando [de registo de funcionalidades AZ,](https://docs.microsoft.com/cli/azure/feature#az-feature-register) como mostra o exemplo seguinte; só terá de o fazer uma vez por subscrição enquanto o add-on ainda está em pré-visualização:
```azurecli-interactive
az feature register --name AKS-IngressApplicationGatewayAddon --namespace microsoft.containerservice
```

Pode levar alguns minutos para o estado mostrar registado. Pode verificar o estado de registo utilizando o comando [da lista de recursos az:](https://docs.microsoft.com/cli/azure/feature#az-feature-register)
```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKS-IngressApplicationGatewayAddon')].{Name:name,State:properties.state}"
```

Quando estiver pronto, reaprovi o registo do fornecedor de recursos Microsoft.ContainerService utilizando o comando [de registo do fornecedor az:](https://docs.microsoft.com/cli/azure/provider#az-provider-register)
```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

Certifique-se de instalar/atualizar a extensão de pré-visualização aks para este tutorial; utilizar os seguintes comandos Azure CLI
```azurecli-interactive
az extension add --name aks-preview
az extension list
```
```azurecli-interactive
az extension update --name aks-preview
az extension list
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Em Azure, aloca recursos relacionados a um grupo de recursos. Crie um grupo de recursos utilizando [o grupo Az create](/cli/azure/group#az-group-create). O exemplo a seguir cria um grupo de recursos chamado *myResourceGroup* na localização central do *Canadá* (região). 

```azurecli-interactive
az group create --name myResourceGroup --location canadacentral
```

## <a name="deploy-a-new-aks-cluster-with-agic-add-on-enabled"></a>Implementar um novo cluster AKS com add-on AGIC ativado

Irá agora implantar um novo cluster AKS com o addon AGIC ativado. Quando implementar um novo cluster AKS com o addon AGIC ativado e não fornecer um Gateway de aplicação existente para usar, criaremos e configuraremos automaticamente um novo Gateway de aplicações para servir o tráfego ao cluster AKS.  

> [!NOTE]
> O controlador de entrada de entrada de aplicação (AGIC) **suporta apenas** o Application Gateway v2 SKUs (Standard e WAF), e **não** o Gateway de Aplicação v1 SKUs. Ao implementar um novo Gateway de aplicações através do add-on AGIC, só é possível implementar um Gateway de aplicação Standard_v2 SKU. Se pretender ativar o addon AGIC para um Gateway de aplicação WAF_v2 SKU, por favor, ative o WAF no Gateway de Aplicações através do Portal ou crie primeiro o Gateway de Aplicação WAF_v2 e, em seguida, siga instruções sobre como ativar o [addon AGIC com um cluster AKS existente e gateway de aplicação existente](tutorial-ingress-controller-add-on-existing.md). 

No exemplo seguinte, irá implementar um novo cluster AKS chamado *myCluster* usando [Azure CNI](https://docs.microsoft.com/azure/aks/concepts-network#azure-cni-advanced-networking) e [Identidades Geridas](https://docs.microsoft.com/azure/aks/use-managed-identity) com o addon AGIC ativado no grupo de recursos que criou, *myResourceGroup*. Uma vez que a implementação de um novo cluster AKS com o addon AGIC ativado sem especificar um Gateway de aplicação existente significará uma criação automática de um gateway de aplicação SKU Standard_v2, também estará especificando o nome e o espaço de endereço de sub-rede do Gateway de aplicação. O nome do Gateway de aplicações será *myApplicationGateway* e o espaço de endereço da sub-rede que estamos a usar é 10.2.0.0/16. Certifique-se de que adicionou/atualizou a extensão de pré-visualização de aks no início deste tutorial. 

```azurecli-interactive
az aks create -n myCluster -g myResourceGroup --network-plugin azure --enable-managed-identity -a ingress-appgw --appgw-name myApplicationGateway --appgw-subnet-prefix "10.2.0.0/16" 
```

Para configurar parâmetros adicionais para o `az aks create` comando, visite referências [aqui.](https://docs.microsoft.com/cli/azure/aks?view=azure-cli-latest#az-aks-create) 

> [!NOTE]
> O cluster AKS que criou aparecerá no grupo de recursos que criou, *o myResourceGroup*. No entanto, o Application Gateway criado automaticamente viverá no grupo de recursos de nó, onde estão os pools do agente. Por padrão, o grupo de recursos de nó será nomeado *MC_resource grupo-name_cluster-name_location,* mas pode ser modificado. 

## <a name="deploy-a-sample-application-using-agic"></a>Implementar uma aplicação de amostra usando a AGIC

Irá agora implementar uma aplicação de amostra para o cluster AKS que criou e utilizará o addon AGIC para Ingress e ligará o Gateway de Aplicação ao cluster AKS. Primeiro, terás credenciais para o aglomerado AKS que implementaste, executando o `az aks get-credentials` comando. 

```azurecli-interactive
az aks get-credentials -n myCluster -g myResourceGroup
```

Assim que tiver as credenciais para o cluster que criou, executar o seguinte comando para configurar uma aplicação de amostra que usa AGIC para Ingress para o cluster. A AGIC atualizará o Gateway de Aplicação que criou anteriormente com as respetivas regras de encaminhamento para a nova aplicação de amostra que implementou.  

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/master/docs/examples/aspnetapp.yaml 
```

## <a name="check-that-the-application-is-reachable"></a>Verifique se a aplicação está acessível

Agora que o Gateway de Aplicação está configurado para servir o tráfego para o cluster AKS, vamos verificar se a sua aplicação está acessível. Primeiro terá o endereço IP do Ingress. 

```azurecli-interactive
kubectl get ingress
```

Verifique se a aplicação de amostra que criou está a funcionar visitando o endereço IP do Gateway de aplicação que obteve ao executar o comando acima ou consulte `curl` . Pode levar um minuto para o Application Gateway obter a atualização, por isso, se o Gateway de Aplicações ainda estiver num estado de "Atualização" no Portal, deixe-o terminar antes de tentar chegar ao endereço IP. 

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não forem necessários, remova o grupo de recursos, o gateway de aplicação e todos os recursos relacionados.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Próximos passos
* [Saiba mais sobre a desativação do addon AGIC](./ingress-controller-disable-addon.md)
* [Saiba mais sobre quais anotações são apoiadas com a AGIC](./ingress-controller-annotations.md)
* [Problemas de resolução de problemas com a AGIC](./ingress-controller-troubleshoot.md)

