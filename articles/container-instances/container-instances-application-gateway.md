---
title: Endereço IP estático para grupo de contentores
description: Crie um grupo de contentores numa rede virtual e utilize uma porta de aplicação Azure para expor um endereço IP frontal estático a uma aplicação web contentorizada
ms.topic: article
ms.date: 03/16/2020
ms.openlocfilehash: 5c3a14f93af3ecc614dc296f0a4d2815d7a64a66
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79481794"
---
# <a name="expose-a-static-ip-address-for-a-container-group"></a>Expor um endereço IP estático para um grupo de contentores

Este artigo mostra uma maneira de expor um endereço IP público estático para um grupo de [contentores](container-instances-container-groups.md) utilizando uma porta de [aplicação](../application-gateway/overview.md)Azure . Siga estes passos quando necessitar de um ponto de entrada estático para uma aplicação contentorizada virada para o exterior que funciona em Instâncias de Contentores Azure. 

Neste artigo utiliza-se o Azure CLI para criar os recursos para este cenário:

* Uma rede virtual Azure
* Um grupo de contentores implantado [na rede virtual (pré-visualização)](container-instances-vnet.md) que acolhe uma pequena aplicação web
* Um portal de aplicação com um endereço IP frontend público, um ouvinte para hospedar um site na porta de entrada, e uma rota para o grupo de contentores backend

Enquanto o gateway de aplicação for executado e o grupo de contentores expor um endereço IP privado estável na subnet delegada da rede, o grupo de contentores está acessível neste endereço IP público.

> [!NOTE]
> O Azure cobra por um gateway de aplicação com base no tempo em que o gateway é provisionado e disponível, bem como a quantidade de dados que processa. Ver [preços](https://azure.microsoft.com/pricing/details/application-gateway/).

## <a name="create-virtual-network"></a>Criar a rede virtual

Num caso típico, pode já ter uma rede virtual Azure. Se não tiver um, crie um como mostrado com os seguintes comandos exemplo. A rede virtual necessita de subredes separadas para o gateway de aplicação e para o grupo de contentores.

Se precisar de um, crie um grupo de recursos Azure. Por exemplo:

```azureci
az group create --name myResourceGroup --location eastus
```

Criar uma rede virtual com a [rede az vnet criar][az-network-vnet-create] comando. Este comando cria a sub-rede *myAGSubnet* na rede.

```azurecli
az network vnet create \
  --name myVNet \
  --resource-group myResourceGroup \
  --location eastus \
  --address-prefix 10.0.0.0/16 \
  --subnet-name myAGSubnet \
  --subnet-prefix 10.0.1.0/24
```

Utilize a [subnet az rede vnet criar][az-network-vnet-subnet-create] comando para criar uma sub-rede para o grupo de contentores de backend. Aqui chama-se *myACISubnet.*

```azurecli
az network vnet subnet create \
  --name myACISubnet \
  --resource-group myResourceGroup \
  --vnet-name myVNet   \
  --address-prefix 10.0.2.0/24
```

Utilize a [rede az public-ip criar][az-network-public-ip-create] comando para criar um recurso IP público estático. Num passo posterior, este endereço é configurado como a extremidade frontal do gateway da aplicação.

```azurecli
az network public-ip create \
  --resource-group myResourceGroup \
  --name myAGPublicIPAddress \
  --allocation-method Static \
  --sku Standard
```

## <a name="create-container-group"></a>Criar grupo de contentores

Executar o [seguinte recipiente az criar][az-container-create] para criar um grupo de contentores na rede virtual configurada no passo anterior. 

O grupo é implantado na subnet *myACISubnet* e contém um único `aci-helloworld` contentor de *aplicação* chamado que puxa a imagem. Como mostrado em outros artigos na documentação, esta imagem embala uma pequena aplicação web escrita no Node.js que serve uma página html estática. 

```azurecli
az container create \
  --name appcontainer \
  --resource-group myResourceGroup \
  --image mcr.microsoft.com/azuredocs/aci-helloworld \
  --vnet myVNet \
  --subnet myACISubnet
```

Quando implantado com sucesso, o grupo de contentores é atribuído a um endereço IP privado na rede virtual. Por exemplo, executar o seguinte comando de demonstração de [contentores az][az-container-show] para recuperar o endereço IP do grupo:

```azurecli
az container show \
  --name appcontainer --resource-group myResourceGroup \
  --query ipAddress.ip --output tsv
```

O resultado é semelhante a: `10.0.2.4`.

Para utilização num passo posterior, guarde o endereço IP numa variável ambiental:

```azurecli
ACI_IP=$(az container show \
  --name appcontainer \
  --resource-group myResourceGroup \
  --query ipAddress.ip --output tsv)
```

## <a name="create-application-gateway"></a>Criar um gateway de aplicação

Crie um portal de aplicação na rede virtual, seguindo os passos no gateway da [aplicação.](../application-gateway/quick-create-cli.md) O seguinte comando de [aplicação de rede Az cria][az-network-application-gateway-create] uma porta de entrada com um endereço IP frontal público e uma rota para o grupo de contentores backend. Consulte a documentação do Gateway de [Aplicação](/azure/application-gateway/) para obter detalhes sobre as definições do gateway.

```azurecli
az network application-gateway create \
  --name myAppGateway \
  --location eastus \
  --resource-group myResourceGroup \
  --capacity 2 \
  --sku Standard_v2 \
  --http-settings-protocol http \
  --public-ip-address myAGPublicIPAddress \
  --vnet-name myVNet \
  --subnet myAGSubnet \
  --servers "$ACI_IP" 
```


Pode levar até 15 minutos para o Azure criar o portal de aplicação. 

## <a name="test-public-ip-address"></a>Teste endereço IP público
  
Agora pode testar o acesso à aplicação web que funciona no grupo de contentores atrás do gateway da aplicação.

Executar o comando [público-ip da rede az][az-network-public-ip-show] para recuperar o endereço IP público frontend da porta de entrada:

```azurecli
az network public-ip show \
--resource-group myresourcegroup \
--name myAGPublicIPAddress \
--query [ipAddress] \
--output tsv
```

A saída é um endereço `52.142.18.133`IP público, semelhante a: .

Para ver a aplicação web em execução quando configurada com sucesso, navegue para o endereço IP público do gateway no seu navegador. O acesso bem sucedido é semelhante a:

![Captura de ecrã do browser a mostrar a aplicação em execução numa instância do contentor do Azure](./media/container-instances-application-gateway/aci-app-app-gateway.png)

## <a name="next-steps"></a>Passos seguintes

* Consulte um [modelo de arranque rápido](https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-wordpress-vnet) para criar um grupo de contentores com uma instância de contentor WordPress como um servidor de backend atrás de um gateway de aplicação.
* Também pode configurar um gateway de candidatura com um certificado para a rescisão do SSL. Veja a [visão geral](../application-gateway/ssl-overview.md) e o [tutorial.](../application-gateway/create-ssl-portal.md)
* Dependendo do seu cenário, considere utilizar outras soluções de equilíbrio de carga Azure com instâncias de contentores Azure. Por exemplo, utilize o [Gestor de Tráfego Azure](../traffic-manager/traffic-manager-overview.md) para distribuir o tráfego em várias instâncias de contentores e em várias regiões. Ver esta publicação do [blog.](https://aaronmsft.com/posts/azure-container-instances/)

[az-network-vnet-create]:  /cli/azure/network/vnet#az-network-vnet-create
[az-network-vnet-subnet-create]: /cli/azure/network/vnet/subnet#az-network-vnet-subnet-create
[az-network-public-ip-create]: /cli/azure/network/public-ip#az-network-public-ip-create
[az-network-public-ip-show]: /cli/azure/network/public-ip#az-network-public-ip-show
[az-network-application-gateway-create]: /cli/azure/network/application-gateway#az-network-application-gateway-create
[az-container-create]: /cli/azure/container#az-container-create
[az-container-show]: /cli/azure/container#az-container-show