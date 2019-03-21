---
title: Tutorial - Encaminhar o tráfego Web com base no URL - CLI do Azure
description: Neste tutorial, saiba como utilizar a CLI do Azure para encaminhar o tráfego Web com base no URL para conjuntos dimensionáveis específicos de servidores.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: tutorial
ms.date: 10/25/2018
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 4f0c93c41a468b62baf1ec50d030f235d36a8dd2
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58006474"
---
# <a name="tutorial-route-web-traffic-based-on-the-url-using-the-azure-cli"></a>Tutorial: Encaminhar o tráfego web com base na URL com a CLI do Azure

Enquanto administrador de TI que gere tráfego da Web, deve ajudar os seus clientes ou utilizadores a obterem as informações que precisam o mais rapidamente possível. Uma forma de o fazer é otimizar a sua experiência ao encaminhar diferentes tipos de tráfego da Web para recursos de servidor diferente. Este tutorial mostra-lhe como utilizar a CLI do Azure para definir e configurar o encaminhamento do Gateway de Aplicação para diferentes tipos de tráfego da sua aplicação. O encaminhamento, em seguida, direciona o tráfego para agrupamentos de servidores diferentes com base no URL.

![Exemplo de encaminhamento de URL](./media/tutorial-url-route-cli/scenario.png)

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar um grupo de recursos para os recursos de rede que precisa
> * Criar recursos de rede
> * Criar um gateway de aplicação para o tráfego proveniente da sua aplicação
> * Especificar agrupamentos de servidores e regras de encaminhamento para os diferentes tipos de tráfego
> * Criar um conjunto de dimensionamento para cada conjunto, para que o conjunto possa dimensionar automaticamente
> * Executar um teste para que possa verificar que os diferentes tipos de tráfego vão para o conjunto correto

Se preferir, pode concluir este tutorial com o [Azure PowerShell](tutorial-url-route-powershell.md) ou o [portal do Azure](create-url-route-portal.md).

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar a CLI localmente, este tutorial requer a execução da versão 2.0.4 ou posterior da CLI do Azure. Para localizar a versão, execute `az --version`. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Um grupo de recursos é um contentor lógico no qual os recursos do Azure são implementados e geridos. Criar um grupo de recursos com `az group create`.

O exemplo seguinte cria um grupo de recursos com o nome *myResourceGroupAG* na localização *eastus*.

```azurecli-interactive
az group create --name myResourceGroupAG --location eastus
```

## <a name="create-network-resources"></a>Criar recursos de rede

Crie a rede virtual denominada *myVNet* e a sub-rede denominada *myAGSubnet* com `az network vnet create`. Em seguida, adicione uma sub-rede denominada *myBackendSubnet*, que é precisa para os servidores de back-end, com `az network vnet subnet create`. Crie o endereço IP público com o nome *myAGPublicIPAddress* com `az network public-ip create`.

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

## <a name="create-the-app-gateway-with-a-url-map"></a>Criar o gateway de aplicação com um mapa de URL

Utilize `az network application-gateway create` para criar um gateway de aplicação com o nome *myAppGateway*. Quando cria um gateway de aplicação com a CLI do Azure, especifica informações de configuração, tais como a capacidade, o sku e as definições de HTTP. O gateway de aplicação é atribuído a *myAGSubnet* e *myAGPublicIPAddress* que criou anteriormente.

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


|Funcionalidade  |Descrição  |
|---------|---------|
|appGatewayBackendPool     |Um gateway de aplicação tem de ter, pelo menos, um conjunto de endereços de back-end.|
|appGatewayBackendHttpSettings     |Especifica que a porta 80 e um protocolo HTTP são utilizados para a comunicação.|
|appGatewayHttpListener     |O serviço de escuta predefinido associado a appGatewayBackendPool|
|appGatewayFrontendIP     |Aribui myAGPublicIPAddress a appGatewayHttpListener.|
|rule1     |A regra de encaminhamento predefinida associada a appGatewayHttpListener.|

### <a name="add-image-and-video-backend-pools-and-a-port"></a>Adicionar conjuntos e uma porta de back-end de imagens e vídeos

Adicione conjuntos de back-end denominados *imagesBackendPool* e *videoBackendPool* ao gateway de aplicação, com `az network application-gateway address-pool create`. Adicionar a porta de front-end aos conjuntos com `az network application-gateway frontend-port create`.

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
  --name port8080
```

### <a name="add-a-backend-listener"></a>Adicionar um serviço de escuta de back-end

Utilize *para adicionar o serviço de escuta do back-end denominado*backendListener`az network application-gateway http-listener create` que é preciso para encaminhar o tráfego.


```azurecli-interactive
az network application-gateway http-listener create \
  --name backendListener \
  --frontend-ip appGatewayFrontendIP \
  --frontend-port port8080 \
  --resource-group myResourceGroupAG \
  --gateway-name myAppGateway
```

### <a name="add-a-url-path-map"></a>Adicionar um mapa de caminho do URL

Os mapas de caminho de URL asseguram que são encaminhados URLs específicos para conjuntos de back-end específicos. Crie os mapa de caminho de URL com o nome *imagePathRule* e *videoPathRule* com `az network application-gateway url-path-map create` e `az network application-gateway url-path-map rule create`.

```azurecli-interactive
az network application-gateway url-path-map create \
  --gateway-name myAppGateway \
  --name myPathMap \
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
  --path-map-name myPathMap \
  --paths /video/* \
  --address-pool videoBackendPool
```

### <a name="add-a-routing-rule"></a>Adicionar uma regra de encaminhamento

A regra de encaminhamento associa os mapas do URL ao serviço de escuta que criou. Adicione uma regra com o nome *rule2* com `az network application-gateway rule create`.

```azurecli-interactive
az network application-gateway rule create \
  --gateway-name myAppGateway \
  --name rule2 \
  --resource-group myResourceGroupAG \
  --http-listener backendListener \
  --rule-type PathBasedRouting \
  --url-path-map myPathMap \
  --address-pool appGatewayBackendPool
```

## <a name="create-vm-scale-sets"></a>Criar conjuntos de dimensionamento de VMs

Neste tutorial, vai criar três conjuntos de dimensionamento de máquinas virtuais que suportam os três conjuntos de back-end que criou. Vai criar conjuntos de dimensionamento denominados *myvmss1*, *myvmss2* e *myvmss3*. Cada conjunto de dimensionamento contém duas instâncias de máquina virtual onde vai instalar o NGINX.

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

Para obter o endereço IP público do gateway de aplicação, utilize az network public-ip show. Copie o endereço IP público e cole-o na barra de endereço do browser. Como, por exemplo `http://40.121.222.19`, `http://40.121.222.19:8080/images/test.htm`, ou `http://40.121.222.19:8080/video/test.htm`.

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --query [ipAddress] \
  --output tsv
```

![Testar o URL base no gateway de aplicação](./media/tutorial-url-route-cli/application-gateway-nginx.png)

Altere o URL para http://&lt;ip-address&gt;:8080/images/test.html, substituindo o endereço IP de &lt;ip-address&gt;, e deverá ver algo semelhante ao seguinte exemplo:

![Testar o URL de imagens no gateway de aplicação](./media/tutorial-url-route-cli/application-gateway-nginx-images.png)

Altere o URL para http://&lt;ip-address&gt;:8080/video/test.html, substituindo o endereço IP de &lt;ip-address&gt;, e deverá ver algo semelhante ao seguinte exemplo.

![Testar o URL de vídeo no gateway de aplicação](./media/tutorial-url-route-cli/application-gateway-nginx-video.png)

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não forem precisos, remova o grupo de recursos, o gateway de aplicação e todos os recursos relacionados.

```azurecli-interactive
az group delete --name myResourceGroupAG --location eastus
```

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Criar um gateway de aplicação com o redirecionamento com base no caminho do URL](./tutorial-url-redirect-cli.md)
