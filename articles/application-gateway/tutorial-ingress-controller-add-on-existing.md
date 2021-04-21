---
title: 'Tutorial: Ativar Add-On do Controlador de Ingress para o cluster AKS existente com gateway de aplicação Azure existente'
description: Utilize este tutorial para ativar o controlador ingress Add-On para o seu cluster AKS existente com um Gateway de aplicação existente
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: tutorial
ms.date: 03/02/2021
ms.author: caya
ms.openlocfilehash: 2fe615da256099c3135f607a7b6f8095bb93b442
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107772852"
---
# <a name="tutorial-enable-application-gateway-ingress-controller-add-on-for-an-existing-aks-cluster-with-an-existing-application-gateway"></a>Tutorial: Ativar o addon do controlador de entrada de entrada de aplicação para um cluster AKS existente com um Gateway de aplicação existente

Pode utilizar o Azure CLI ou o Portal para ativar o addon [do Controlador de Entradas de Gateway de Aplicação (AGIC)](ingress-controller-overview.md) para um cluster de [Serviços Azure Kubernetes (AKS)](https://azure.microsoft.com/services/kubernetes-service/) existente. Neste tutorial, você aprenderá a usar o addon AGIC para expor a sua aplicação Kubernetes num cluster AKS existente através de um Gateway de aplicação existente implantado em redes virtuais separadas. Começará por criar um cluster AKS numa rede virtual e um Gateway de aplicação numa rede virtual separada para simular os recursos existentes. Em seguida, irá ativar o add-on AGIC, acompanhar as duas redes virtuais em conjunto, e implementar uma aplicação de amostra que será exposta através do Gateway de Aplicação utilizando o addon AGIC. Se estiver a ativar o addon AGIC para um Gateway de aplicação existente e o cluster AKS existente na mesma rede virtual, então pode saltar o passo de observação abaixo. O addon fornece uma forma muito mais rápida de implementar a AGIC para o seu cluster AKS do que [anteriormente através do Helm](ingress-controller-overview.md#difference-between-helm-deployment-and-aks-add-on) e também oferece uma experiência totalmente gerida.  

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar um grupo de recursos 
> * Criar um novo cluster AKS 
> * Criar um novo Gateway de Aplicações 
> * Ativar o addon AGIC no cluster AKS existente através do Azure CLI 
> * Ativar o addon AGIC no cluster AKS existente através do Portal 
> * Peer the Application Gateway virtual network with the AKS cluster virtual network
> * Implementar uma aplicação de amostra utilizando o AGIC para Ingress no cluster AKS
> * Verifique se a aplicação é acessível através do Gateway de Aplicação

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Em Azure, aloca recursos relacionados a um grupo de recursos. Crie um grupo de recursos utilizando [o grupo Az create](/cli/azure/group#az_group_create). O exemplo a seguir cria um grupo de recursos chamado *myResourceGroup* na localização central do *Canadá* (região). 

```azurecli-interactive
az group create --name myResourceGroup --location canadacentral
```

## <a name="deploy-a-new-aks-cluster"></a>Implementar um novo cluster AKS

Irá agora implementar um novo cluster AKS, para simular ter um cluster AKS existente para o qual pretende ativar o addon AGIC.  

No exemplo seguinte, estará a implementar um novo cluster AKS chamado *myCluster* usando [Azure CNI](../aks/concepts-network.md#azure-cni-advanced-networking) e [Identidades Geridas](../aks/use-managed-identity.md) no grupo de recursos que criou, *o myResourceGroup.*

```azurecli-interactive
az aks create -n myCluster -g myResourceGroup --network-plugin azure --enable-managed-identity 
```

Para configurar parâmetros adicionais para o `az aks create` comando, visite referências [aqui.](/cli/azure/aks#az_aks_create) 

## <a name="deploy-a-new-application-gateway"></a>Implementar um novo Gateway de Aplicações 

Irá agora implementar um novo Gateway de Aplicações, para simular ter um Gateway de Aplicação existente que pretende utilizar para carregar o tráfego de equilíbrio para o seu cluster AKS, *myCluster*. O nome do Gateway de Aplicação será *myApplicationGateway,* mas primeiro terá de criar um recurso IP público, chamado *myPublicIp,* e uma nova rede virtual chamada *myVnet* com espaço de endereço 11.0.0.0/8, e uma sub-rede com o espaço de endereço 11.1.0.0/16 chamada *mySubnet*, e implementar o seu Gateway de aplicação no *meuSubnet* usando *o meuPubnet*. 

Ao utilizar um cluster AKS e Gateway de aplicação em redes virtuais separadas, os espaços de endereço das duas redes virtuais não devem sobrepor-se. O espaço de endereço predefinido que um cluster AKS implementa é de 10.0.0.0/8, pelo que definimos o prefixo de endereço virtual do Gateway de Aplicação para 11.0.0.0/8. 

```azurecli-interactive
az network public-ip create -n myPublicIp -g MyResourceGroup --allocation-method Static --sku Standard
az network vnet create -n myVnet -g myResourceGroup --address-prefix 11.0.0.0/8 --subnet-name mySubnet --subnet-prefix 11.1.0.0/16 
az network application-gateway create -n myApplicationGateway -l canadacentral -g myResourceGroup --sku Standard_v2 --public-ip-address myPublicIp --vnet-name myVnet --subnet mySubnet
```

> [!NOTE]
> O controlador de entrada de entrada de aplicação (AGIC) **suporta apenas** o Application Gateway v2 SKUs (Standard e WAF), e **não** o Gateway de Aplicação v1 SKUs. 

## <a name="enable-the-agic-add-on-in-existing-aks-cluster-through-azure-cli"></a>Ativar o addon AGIC no cluster AKS existente através do Azure CLI 

Se quiser continuar a utilizar o Azure CLI, pode continuar a ativar o addon AGIC no cluster AKS que criou, *myCluster,* e especificar o addon AGIC para utilizar o Gateway de Aplicação existente que criou, *myApplicationGateway*.

```azurecli-interactive
appgwId=$(az network application-gateway show -n myApplicationGateway -g myResourceGroup -o tsv --query "id") 
az aks enable-addons -n myCluster -g myResourceGroup -a ingress-appgw --appgw-id $appgwId
```

## <a name="enable-the-agic-add-on-in-existing-aks-cluster-through-portal"></a>Ativar o addon AGIC no cluster AKS existente através do Portal 

Se quiser utilizar o portal Azure para ativar o addon AGIC, vá [até ( https://aka.ms/azure/portal/aks/agic) ](https://aka.ms/azure/portal/aks/agic) e navegue para o seu cluster AKS através da ligação Portal. A partir daí, vá ao separador Networking dentro do seu cluster AKS. Verá uma secção de controlador de entrada de gateway de aplicação, que lhe permite ativar/desativar o addon do controlador de entrada utilizando o Portal UI. Verifique a caixa ao lado de "Enable ingresss controller", e selecione o Gateway de Aplicação que criou, *myApplicationGateway* a partir do menu suspenso. 

![Portal do Controlador de Entrada de Gateway de Aplicação](./media/tutorial-ingress-controller-add-on-existing/portal-ingress-controller-add-on.png)

## <a name="peer-the-two-virtual-networks-together&quot;></a>Par as duas redes virtuais juntas

Uma vez que implementámos o cluster AKS na sua própria rede virtual e o Application Gateway noutra rede virtual, terás de espreitar as duas redes virtuais em conjunto para que o tráfego flua do Gateway de Aplicação para as cápsulas do cluster. Espreitar as duas redes virtuais requer executar o comando Azure CLI duas vezes diferentes, para garantir que a ligação é bidisal. O primeiro comando criará uma ligação de espreitar da rede virtual Application Gateway para a rede virtual AKS; o segundo comando criará uma ligação de espreitar na outra direção.

```azurecli-interactive
nodeResourceGroup=$(az aks show -n myCluster -g myResourceGroup -o tsv --query &quot;nodeResourceGroup")
aksVnetName=$(az network vnet list -g $nodeResourceGroup -o tsv --query "[0].name")

aksVnetId=$(az network vnet show -n $aksVnetName -g $nodeResourceGroup -o tsv --query "id")
az network vnet peering create -n AppGWtoAKSVnetPeering -g myResourceGroup --vnet-name myVnet --remote-vnet $aksVnetId --allow-vnet-access

appGWVnetId=$(az network vnet show -n myVnet -g myResourceGroup -o tsv --query "id")
az network vnet peering create -n AKStoAppGWVnetPeering -g $nodeResourceGroup --vnet-name $aksVnetName --remote-vnet $appGWVnetId --allow-vnet-access
```

## <a name="deploy-a-sample-application-using-agic"></a>Implementar uma aplicação de amostra usando a AGIC 

Irá agora implementar uma aplicação de amostra para o cluster AKS que criou e que utilizará o addon AGIC para Ingress e ligará o Gateway de Aplicação ao cluster AKS. Primeiro, terás credenciais para o aglomerado AKS que implementaste, executando o `az aks get-credentials` comando. 

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

## <a name="clean-up-resources"></a>Limpar os recursos

Quando já não forem necessários, remova o grupo de recursos, o gateway de aplicação e todos os recursos relacionados.

```azurecli-interactive
az group delete --name myResourceGroup 
```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Saiba mais sobre a desativação do addon AGIC](./ingress-controller-disable-addon.md)
