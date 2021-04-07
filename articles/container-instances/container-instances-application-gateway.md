---
title: Endereço IP estático para grupo de contentores
description: Crie um grupo de contentores numa rede virtual e use uma porta de entrada de aplicação Azure para expor um endereço IP estático para uma aplicação web contentorizada
ms.topic: article
ms.date: 03/16/2020
ms.openlocfilehash: 0131780fdb04a71837d5ae9bf5498bf2bd499f8a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98035058"
---
# <a name="expose-a-static-ip-address-for-a-container-group"></a>Expor um endereço IP estático para um grupo de contentores

Este artigo mostra uma maneira de expor um endereço IP público estático para um [grupo de contentores](container-instances-container-groups.md) utilizando um [gateway de aplicação](../application-gateway/overview.md)Azure . Siga estes passos quando precisar de um ponto de entrada estático para uma aplicação contentorizada virada para o exterior que funciona em Instâncias de Contentores Azure. 

Neste artigo utiliza-se o CLI Azure para criar os recursos para este cenário:

* Uma rede virtual Azure
* Um grupo de contentores implantado [na rede virtual](container-instances-vnet.md) que acolhe uma pequena aplicação web
* Um gateway de aplicação com um endereço IP frontal público, um ouvinte para hospedar um site no gateway, e uma rota para o grupo de contentores backend

Enquanto o gateway de aplicação for executado e o grupo de contentores expor um endereço IP privado estável na sub-rede delegada da rede, o grupo de contentores está acessível neste endereço IP público.

> [!NOTE]
> A azure cobra por um gateway de aplicação com base no tempo que o gateway é a provisionado e disponível, bem como a quantidade de dados que processa. Ver [preços.](https://azure.microsoft.com/pricing/details/application-gateway/)

## <a name="create-virtual-network"></a>Criar a rede virtual

Num caso típico, pode já ter uma rede virtual Azure. Se não tiver um, crie um como mostrado com os seguintes comandos de exemplo. A rede virtual necessita de sub-redes separadas para o gateway de aplicação e para o grupo de contentores.

Se precisar de um, crie um grupo de recursos Azure. Por exemplo:

```azureci
az group create --name myResourceGroup --location eastus
```

Criar uma rede virtual com o [vnet de rede az criar][az-network-vnet-create] comando. Este comando cria a *sub-rede myAGSubnet* na rede.

```azurecli
az network vnet create \
  --name myVNet \
  --resource-group myResourceGroup \
  --location eastus \
  --address-prefix 10.0.0.0/16 \
  --subnet-name myAGSubnet \
  --subnet-prefix 10.0.1.0/24
```

Utilize a [sub-rede vnet de rede az para][az-network-vnet-subnet-create] criar uma sub-rede para o grupo de recipientes de backend. Aqui chama-se *myACISubnet.*

```azurecli
az network vnet subnet create \
  --name myACISubnet \
  --resource-group myResourceGroup \
  --vnet-name myVNet   \
  --address-prefix 10.0.2.0/24
```

Utilize o comando [público-ip da rede az][az-network-public-ip-create] para criar um recurso IP público estático. Num passo posterior, este endereço é configurado como a extremidade frontal do gateway de aplicação.

```azurecli
az network public-ip create \
  --resource-group myResourceGroup \
  --name myAGPublicIPAddress \
  --allocation-method Static \
  --sku Standard
```

## <a name="create-container-group"></a>Criar grupo de contentores

Executar o [seguinte recipiente az criar][az-container-create] para criar um grupo de contentores na rede virtual que configuraste no passo anterior. 

O grupo é implantado na sub-rede *myACISubnet* e contém um único exemplo chamado *appcontainer* que puxa a `aci-helloworld` imagem. Como mostrado em outros artigos na documentação, esta imagem embala uma pequena aplicação web escrita em Node.js que serve uma página de HTML estática. 

```azurecli
az container create \
  --name appcontainer \
  --resource-group myResourceGroup \
  --image mcr.microsoft.com/azuredocs/aci-helloworld \
  --vnet myVNet \
  --subnet myACISubnet
```

Quando implementado com sucesso, o grupo de contentores é atribuído um endereço IP privado na rede virtual. Por exemplo, executar o seguinte comando [de demonstração de recipiente az][az-container-show] para recuperar o endereço IP do grupo:

```azurecli
az container show \
  --name appcontainer --resource-group myResourceGroup \
  --query ipAddress.ip --output tsv
```

O resultado é semelhante a: `10.0.2.4`.

Para ser utilizado num passo posterior, guarde o endereço IP numa variável ambiental:

```azurecli
ACI_IP=$(az container show \
  --name appcontainer \
  --resource-group myResourceGroup \
  --query ipAddress.ip --output tsv)
```

> [!IMPORTANT]
> Se o grupo de contentores for parado, iniciado ou reiniciado, o grupo de contentores ip privado está sujeito a alterações. Se isto acontecer, terá de atualizar a configuração do gateway de aplicações.

## <a name="create-application-gateway"></a>Criar um gateway de aplicação

Crie um gateway de aplicação na rede virtual, seguindo os passos no gateway de [aplicação quickstart](../application-gateway/quick-create-cli.md). O comando [de aplicação-gateway de aplicação az][az-network-application-gateway-create] seguinte cria um gateway com um endereço IP frontal público e uma rota para o grupo de contentores backend. Consulte a [documentação do Gateway](../application-gateway/index.yml) de Aplicação para obter mais detalhes sobre as definições do gateway.

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


Pode levar até 15 minutos para a Azure criar o gateway de aplicação. 

## <a name="test-public-ip-address"></a>Testar endereço IP público
  
Agora pode testar o acesso à aplicação web em execução no grupo de contentores por detrás do gateway de aplicações.

Executar o comando [de show público-ip da rede az][az-network-public-ip-show] para recuperar o endereço IP público frontend do gateway:

```azurecli
az network public-ip show \
--resource-group myresourcegroup \
--name myAGPublicIPAddress \
--query [ipAddress] \
--output tsv
```

A saída é um endereço IP público, semelhante a: `52.142.18.133` .

Para ver a aplicação web em execução quando configurada com sucesso, navegue para o endereço IP público do gateway no seu navegador. O acesso com sucesso é semelhante a:

![Captura de ecrã do browser a mostrar a aplicação em execução numa instância do contentor do Azure](./media/container-instances-application-gateway/aci-app-app-gateway.png)

## <a name="next-steps"></a>Passos seguintes

* Consulte um [modelo de arranque rápido](https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-wordpress-vnet) para criar um grupo de contentores com uma instância de contentor WordPress como um servidor de backend atrás de um gateway de aplicações.
* Também pode configurar um gateway de aplicação com um certificado de rescisão SSL. Veja a [visão geral](../application-gateway/ssl-overview.md) e o [tutorial.](../application-gateway/create-ssl-portal.md)
* Dependendo do seu cenário, considere utilizar outras soluções de equilíbrio de carga Azure com instâncias de contentores Azure. Por exemplo, use [o Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) para distribuir o tráfego em várias instâncias de contentores e em várias regiões. Consulte esta [publicação de blog.](https://aaronmsft.com/posts/azure-container-instances/)

[az-network-vnet-create]:  /cli/azure/network/vnet#az-network-vnet-create
[az-network-vnet-subnet-create]: /cli/azure/network/vnet/subnet#az-network-vnet-subnet-create
[az-network-public-ip-create]: /cli/azure/network/public-ip#az-network-public-ip-create
[az-network-public-ip-show]: /cli/azure/network/public-ip#az-network-public-ip-show
[az-network-application-gateway-create]: /cli/azure/network/application-gateway#az-network-application-gateway-create
[az-container-create]: /cli/azure/container#az-container-create
[az-container-show]: /cli/azure/container#az-container-show
