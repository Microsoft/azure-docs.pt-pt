---
title: Tutorial - criar um gateway de aplicação com o redirecionamento de URL baseado no caminho - CLI do Azure
description: Neste tutorial, saiba como criar um gateway de aplicação com o URL com base no caminho redirecionado tráfego com a CLI do Azure.
services: application-gateway
author: vhorne
manager: jpconnock
ms.service: application-gateway
ms.topic: tutorial
ms.workload: infrastructure-services
ms.date: 7/14/2018
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: e0b7995a8234ddb5927c4ef3e1ddd31fab9a00b3
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57996412"
---
# <a name="tutorial-create-an-application-gateway-with-url-path-based-redirection-using-the-azure-cli"></a>Tutorial: Criar um gateway de aplicação com o redirecionamento baseado no caminho de URL com a CLI do Azure

Pode utilizar a CLI do Azure para configurar [regras de encaminhamento com base no caminho do URL](application-gateway-url-route-overview.md) quando cria um [gateway de aplicação](application-gateway-introduction.md). Neste tutorial, vai criar conjuntos de back-end através de [conjuntos de dimensionamento de máquinas virtuais](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md). Em seguida, vai criar regras de encaminhamento de URL que asseguram que o tráfego da Web é redirecionado para o conjunto de back-end adequado.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Configurar a rede
> * Criar um gateway de aplicação
> * Adicionar serviços de escuta e regras de encaminhamento
> * Criar conjuntos de dimensionamento de máquinas virtuais para conjuntos de back-end

O exemplo seguinte mostra o tráfego do site proveniente das portas 8080 e 8081 e que está a ser direcionado para os mesmos conjuntos de back-end:

![Exemplo de encaminhamento de URL](./media/tutorial-url-redirect-cli/scenario.png)

Se preferir, pode executar este tutorial com o [Azure PowerShell](tutorial-url-redirect-powershell.md).

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se optar por instalar e usar a CLI localmente, este tópico requer a execução da versão 2.0.4 ou posterior da CLI do Azure. Para localizar a versão, execute `az --version`. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Um grupo de recursos é um contentor lógico no qual os recursos do Azure são implementados e geridos. Crie um grupo de recursos com [az group create](/cli/azure/group).

O exemplo seguinte cria um grupo de recursos com o nome *myResourceGroupAG* na localização *eastus*.

```azurecli-interactive 
az group create --name myResourceGroupAG --location eastus
```

## <a name="create-network-resources"></a>Criar recursos de rede 

Crie a rede virtual denominada *myVNet* e a sub-rede denominada *myAGSubnet* com [az network vnet create](/cli/azure/network/vnet). Em seguida, pode adicionar a sub-rede denominada *myBackendSubnet* que é necessária para os servidores de back-end, com [az network vnet subnet create](/cli/azure/network/vnet/subnet). Crie o endereço IP público denominado *myAGPublicIPAddress* com [az network public-ip create](/cli/azure/network/public-ip).

```azurecli-interactive
az network vnet create \
  --name myVNet \
  --resource-group myResourceGroupAG \
  --location eastus \
  --address-prefix 10.0.0.0/16 \
  --subnet-name myAGSubnet \
  --subnet-prefix 10.0.1.0/24

az network vnet subnet create \
  --name myBackendSubnet \
  --resource-group myResourceGroupAG \
  --vnet-name myVNet \
  --address-prefix 10.0.2.0/24

az network public-ip create \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress
```

## <a name="create-an-application-gateway"></a>Criar um gateway de aplicação

Utilize [az network application-gateway create](/cli/azure/network/application-gateway) para criar o gateway de aplicação denominado myAppGateway. Quando cria um gateway de aplicação com a CLI do Azure, especifica informações de configuração, tais como a capacidade, o sku e as definições de HTTP. O gateway de aplicação é atribuído a *myAGSubnet* e *myPublicIPAddress* que criou anteriormente.

```azurecli-interactive
az network application-gateway create \
  --name myAppGateway \
  --location eastus \
  --resource-group myResourceGroupAG \
  --vnet-name myVNet \
  --subnet myAGsubnet \
  --capacity 2 \
  --sku Standard_Medium \
  --http-settings-cookie-based-affinity Disabled \
  --frontend-port 80 \
  --http-settings-port 80 \
  --http-settings-protocol Http \
  --public-ip-address myAGPublicIPAddress
```

 A criação do gateway de aplicação pode demorar vários minutos. Depois de criado o gateway de aplicação, pode ver estas novas funcionalidades:

- *appGatewayBackendPool* - um gateway de aplicação tem de ter, pelo menos, um conjunto de endereços de back-end.
- *appGatewayBackendHttpSettings* - especifica que a porta 80 e um protocolo HTTP são utilizados para a comunicação.
- *appGatewayHttpListener* - o serviço de escuta predefinido associado a *appGatewayBackendPool*.
- *appGatewayFrontendIP* - atribui *myAGPublicIPAddress* a *appGatewayHttpListener*.
- *rule1* - a regra de encaminhamento predefinida associada a *appGatewayHttpListener*.


### <a name="add-backend-pools-and-ports"></a>Adicionar conjuntos de back-end e portas

Pode adicionar conjuntos de endereços de back-end denominados *imagesBackendPool* e *videoBackendPool* ao gateway de aplicação, com [az network application-gateway address-pool create](/cli/azure/network/application-gateway/address-pool). Adicione as portas de front-end para os conjuntos, com [az network application-gateway frontend-port create](/cli/azure/network/application-gateway/frontend-port). 

```azurecli-interactive
az network application-gateway address-pool create \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --name imagesBackendPool

az network application-gateway address-pool create \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --name videoBackendPool

az network application-gateway frontend-port create \
  --port 8080 \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --name bport

az network application-gateway frontend-port create \
  --port 8081 \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --name rport
```

## <a name="add-listeners-and-rules"></a>Adicionar serviços de escuta e regras

### <a name="add-listeners"></a>Adicionar serviços de escuta

Adicione os serviços de escuta de back-end denominados *backendListener* e *redirectedListener* que são necessários para encaminhar o tráfego, com [az network application-gateway http-listener create](/cli/azure/network/application-gateway/http-listener).


```azurecli-interactive
az network application-gateway http-listener create \
  --name backendListener \
  --frontend-ip appGatewayFrontendIP \
  --frontend-port bport \
  --resource-group myResourceGroupAG \
  --gateway-name myAppGateway

az network application-gateway http-listener create \
  --name redirectedListener \
  --frontend-ip appGatewayFrontendIP \
  --frontend-port rport \
  --resource-group myResourceGroupAG \
  --gateway-name myAppGateway
```

### <a name="add-the-default-url-path-map"></a>Adicionar o mapa de caminho de URL predefinido

Os mapas de caminho de URL asseguram que são encaminhados URLs específicos para conjuntos de back-end específicos. Pode criar o URL de mapas de caminho denominados *imagePathRule* e *videoPathRule*, com [az network application-gateway url-path-mau create](/cli/azure/network/application-gateway/url-path-map) e [az network application-gateway url-path-map rule create](/cli/azure/network/application-gateway/url-path-map/rule)

```azurecli-interactive
az network application-gateway url-path-map create \
  --gateway-name myAppGateway \
  --name urlpathmap \
  --paths /images/* \
  --resource-group myResourceGroupAG \
  --address-pool imagesBackendPool \
  --default-address-pool appGatewayBackendPool \
  --default-http-settings appGatewayBackendHttpSettings \
  --http-settings appGatewayBackendHttpSettings \
  --rule-name imagePathRule

az network application-gateway url-path-map rule create \
  --gateway-name myAppGateway \
  --name videoPathRule \
  --resource-group myResourceGroupAG \
  --path-map-name urlpathmap \
  --paths /video/* \
  --address-pool videoBackendPool
```

### <a name="add-redirection-configuration"></a>Adicionar configuração de redirecionamento

Pode configurar o redirecionamento para o serviço de escuta com [az network application-gateway redirect-config create](/cli/azure/network/application-gateway/redirect-config).

```azurecli-interactive
az network application-gateway redirect-config create \
  --gateway-name myAppGateway \
  --name redirectConfig \
  --resource-group myResourceGroupAG \
  --type Found \
  --include-path true \
  --include-query-string true \
  --target-listener backendListener
```

### <a name="add-the-redirection-url-path-map"></a>Adicionar o mapa de caminho de URL de redirecionamento

```azurecli-interactive
az network application-gateway url-path-map create \
  --gateway-name myAppGateway \
  --name redirectpathmap \
  --paths /images/* \
  --resource-group myResourceGroupAG \
  --redirect-config redirectConfig \
  --rule-name redirectPathRule
```

### <a name="add-routing-rules"></a>Adicionar regras de encaminhamento

As regras de encaminhamento associam os mapas de caminho de URL aos serviços de escuta que criou. Pode adicionar as regras denominadas *defaultRule* e *redirectedRule* com [az network application-gateway rule create](/cli/azure/network/application-gateway/rule).

```azurecli-interactive
az network application-gateway rule create \
  --gateway-name myAppGateway \
  --name defaultRule \
  --resource-group myResourceGroupAG \
  --http-listener backendListener \
  --rule-type PathBasedRouting \
  --url-path-map urlpathmap \
  --address-pool appGatewayBackendPool

az network application-gateway rule create \
  --gateway-name myAppGateway \
  --name redirectedRule \
  --resource-group myResourceGroupAG \
  --http-listener redirectedListener \
  --rule-type PathBasedRouting \
  --url-path-map redirectpathmap \
  --address-pool appGatewayBackendPool
```

## <a name="create-virtual-machine-scale-sets"></a>Criar conjuntos de dimensionamento de máquinas virtuais

Neste exemplo, vai criar três conjuntos de dimensionamento de máquinas virtuais que suportam os três conjuntos de back-end que criou. Os conjuntos de dimensionamento que criar são denominados *myvmss1*, *myvmss2* e *myvmss3*. Cada conjunto de dimensionamento contém duas instâncias de máquina virtual onde vai instalar o NGINX.

```azurecli-interactive
for i in `seq 1 3`; do
  if [ $i -eq 1 ]
  then
    poolName="appGatewayBackendPool" 
  fi
  if [ $i -eq 2 ]
  then
    poolName="imagesBackendPool"
  fi
  if [ $i -eq 3 ]
  then
    poolName="videoBackendPool"
  fi

  az vmss create \
    --name myvmss$i \
    --resource-group myResourceGroupAG \
    --image UbuntuLTS \
    --admin-username azureuser \
    --admin-password Azure123456! \
    --instance-count 2 \
    --vnet-name myVNet \
    --subnet myBackendSubnet \
    --vm-sku Standard_DS2 \
    --upgrade-policy-mode Automatic \
    --app-gateway myAppGateway \
    --backend-pool-name $poolName
done
```

### <a name="install-nginx"></a>Instalar o NGINX

```azurecli-interactive
for i in `seq 1 3`; do
  az vmss extension set \
    --publisher Microsoft.Azure.Extensions \
    --version 2.0 \
    --name CustomScript \
    --resource-group myResourceGroupAG \
    --vmss-name myvmss$i \
    --settings '{ "fileUris": ["https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/application-gateway/iis/install_nginx.sh"], "commandToExecute": "./install_nginx.sh" }'

done
```

## <a name="test-the-application-gateway"></a>Testar o gateway de aplicação

Para obter o endereço IP público do gateway de aplicação, utilize [az network public-ip show](/cli/azure/network/public-ip#az-network-public-ip-show). Copie o endereço IP público e cole-o na barra de endereço do browser. Como, por exemplo `http://40.121.222.19`, `http://40.121.222.19:8080/images/test.htm`, `http://40.121.222.19:8080/video/test.htm`, ou `http://40.121.222.19:8081/images/test.htm`.

```azurepowershell-interactive
az network public-ip show \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --query [ipAddress] \
  --output tsv
```

![Testar o URL base no gateway de aplicação](./media/tutorial-url-redirect-cli/application-gateway-nginx.png)

Altere o URL para http://&lt;ip-address&gt;:8080/images/test.html, substituindo o endereço IP de &lt;ip-address&gt;, e deverá ver algo semelhante ao seguinte exemplo:

![Testar o URL de imagens no gateway de aplicação](./media/tutorial-url-redirect-cli/application-gateway-nginx-images.png)

Altere o URL para http://&lt;ip-address&gt;:8080/video/test.html, substituindo o endereço IP de &lt;ip-address&gt;, e deverá ver algo semelhante ao seguinte exemplo:

![Testar o URL de vídeo no gateway de aplicação](./media/tutorial-url-redirect-cli/application-gateway-nginx-video.png)

Agora, altere o URL para http://&lt;&gt;:8081/images/test.htm, substituindo o endereço IP de &lt;ip-address&gt;, e deverá ver o tráfego redirecionado novamente para o conjunto de back-end de imagens em http://&lt;ip-address&gt;:8080/images.

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não forem necessários, remova o grupo de recursos, o gateway de aplicação e todos os recursos relacionados.

```azurecli-interactive
az group delete --name myResourceGroupAG --location eastus
```
## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Saiba mais sobre o que pode fazer com o gateway de aplicação](application-gateway-introduction.md)
