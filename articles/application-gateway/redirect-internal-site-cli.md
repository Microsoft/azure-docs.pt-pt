---
title: Criar um gateway de aplicação com o redirecionamento interno – CLI do Azure | Documentos da Microsoft
description: Saiba como criar um gateway de aplicação que redireciona o tráfego da web internos para o conjunto adequado, com a CLI do Azure.
services: application-gateway
author: vhorne
manager: jpconnock
editor: tysonn
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 7/14/2018
ms.author: victorh
ms.openlocfilehash: e44beed592e013a089011c2a774b7300f2b3f854
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58101997"
---
# <a name="create-an-application-gateway-with-internal-redirection-using-the-azure-cli"></a>Criar um gateway de aplicação com o redirecionamento interno com a CLI do Azure

Pode utilizar a CLI do Azure para configurar [redirecionamento de tráfego da web](multiple-site-overview.md) quando cria um [gateway de aplicação](overview.md). Neste tutorial, vai definir um conjunto de back-end com um conjunto de dimensionamento de máquinas virtuais. Em seguida, configurar serviços de escuta e regras com base nos domínios que é proprietário para se certificar de que o tráfego de web chega ao conjunto adequado. Este tutorial parte do princípio de que seu vários exemplos de domínios e utilizações de *www\.contoso.com* e *www\.contoso.org*.

Neste artigo, vai aprender a:

> [!div class="checklist"]
> * Configurar a rede
> * Criar um gateway de aplicação
> * Adicionar serviços de escuta e a regra de redirecionamento
> * Criar um conjunto com o conjunto de back-end de dimensionamento de máquina virtual
> * Criar um registo CNAME no seu domínio

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

Crie a rede virtual denominada *myVNet* e a sub-rede denominada *myAGSubnet* com [az network vnet create](/cli/azure/network/vnet). Em seguida, pode adicionar a sub-rede denominada *myBackendSubnet* que é necessária para o conjunto de back-end de servidores usando [criar a sub-rede de vnet de rede de az](/cli/azure/network/vnet/subnet). Crie o endereço IP público denominado *myAGPublicIPAddress* com [az network public-ip create](/cli/azure/network/public-ip#az-network-public-ip-create).

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

Pode utilizar [az network application-gateway create](/cli/azure/network/application-gateway) para criar o gateway de aplicação denominado *myAppGateway*. Quando cria um gateway de aplicação com a CLI do Azure, especifica informações de configuração, tais como a capacidade, o sku e as definições de HTTP. O gateway de aplicação é atribuído a *myAGSubnet* e *myAGPublicIPAddress* que criou anteriormente. 

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

A criação do gateway de aplicação pode demorar vários minutos. Depois de criado o gateway de aplicação, pode ver estas novas funcionalidades do mesmo:

- *appGatewayBackendPool* - um gateway de aplicação tem de ter, pelo menos, um conjunto de endereços de back-end.
- *appGatewayBackendHttpSettings* - especifica que a porta 80 e um protocolo HTTP são utilizados para a comunicação.
- *appGatewayHttpListener* - o serviço de escuta predefinido associado a *appGatewayBackendPool*.
- *appGatewayFrontendIP* - atribui *myAGPublicIPAddress* a *appGatewayHttpListener*.
- *rule1* - a regra de encaminhamento predefinida associada a *appGatewayHttpListener*.


## <a name="add-listeners-and-rules"></a>Adicionar serviços de escuta e regras 

É necessário um serviço de escuta para permitir ao gateway de aplicação encaminhar o tráfego adequadamente para o conjunto de back-end. Neste tutorial, vai criar dois serviços de escuta para os seus dois domínios. Neste exemplo, os serviços de escuta são criados para os domínios de *www\.contoso.com* e *www\.contoso.org*.

Adicione os serviços de escuta de back-end que são necessários para encaminhar o tráfego, com [az network application-gateway http-listener create](/cli/azure/network/application-gateway/http-listener#az-network-application-gateway-http-listener-create).

```azurecli-interactive
az network application-gateway http-listener create \
  --name contosoComListener \
  --frontend-ip appGatewayFrontendIP \
  --frontend-port appGatewayFrontendPort \
  --resource-group myResourceGroupAG \
  --gateway-name myAppGateway \
  --host-name www.contoso.com
az network application-gateway http-listener create \
  --name contosoOrgListener \
  --frontend-ip appGatewayFrontendIP \
  --frontend-port appGatewayFrontendPort \
  --resource-group myResourceGroupAG \
  --gateway-name myAppGateway \
  --host-name www.contoso.org   
  ```

### <a name="add-the-redirection-configuration"></a>Adicionar a configuração de redirecionamento

Adicionar a configuração de redirecionamento que envia o tráfego a partir *www\.consoto.org* para o serviço de escuta para *www\.contoso.com* no gateway de aplicação utilizando o [redirecionamento de gateway de aplicação de rede de az-config criar](/cli/azure/network/application-gateway/redirect-config#az-network-application-gateway-redirect-config-create).

```azurecli-interactive
az network application-gateway redirect-config create \
  --name orgToCom \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --type Permanent \
  --target-listener contosoListener \
  --include-path true \
  --include-query-string true
```

### <a name="add-routing-rules"></a>Adicionar regras de encaminhamento

As regras são processadas na ordem em que são criados e o tráfego é direcionado através da primeira regra que corresponda ao URL enviado para o gateway de aplicação. Por exemplo, se tiver uma regra com um serviço de escuta básico e uma regra com uma escuta de vários sites, ambas na mesma porta, a regra com o serviço de escuta de vários sites tem de estar listada antes da regra com o serviço de escuta básico, para que a regra de vários sites funcione conforme esperado. 

Neste exemplo, criará duas novas regras e eliminar a regra predefinida que foi criada.  Pode adicionar a regra com [az network application-gateway rule create](/cli/azure/network/application-gateway/rule#az-network-application-gateway-rule-create).

```azurecli-interactive
az network application-gateway rule create \
  --gateway-name myAppGateway \
  --name contosoComRule \
  --resource-group myResourceGroupAG \
  --http-listener contosoComListener \
  --rule-type Basic \
  --address-pool appGatewayBackendPool
az network application-gateway rule create \
  --gateway-name myAppGateway \
  --name contosoOrgRule \
  --resource-group myResourceGroupAG \
  --http-listener contosoOrgListener \
  --rule-type Basic \
  --redirect-config orgToCom
az network application-gateway rule delete \
  --gateway-name myAppGateway \
  --name rule1 \
  --resource-group myResourceGroupAG
```

## <a name="create-virtual-machine-scale-sets"></a>Criar conjuntos de dimensionamento de máquinas virtuais

Neste exemplo, vai criar um conjunto de dimensionamento de máquina virtual que suporta o conjunto de back-end que criou. O conjunto de dimensionamento que criar com o nome *myvmss* e contém duas instâncias de máquina virtual no qual instala o NGINX.

```azurecli-interactive
az vmss create \
  --name myvmss \
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
  --backend-pool-name appGatewayBackendPool
```

### <a name="install-nginx"></a>Instalar o NGINX

Execute este comando na janela do shell:

```azurecli-interactive
az vmss extension set \
  --publisher Microsoft.Azure.Extensions \
  --version 2.0 \
  --name CustomScript \
  --resource-group myResourceGroupAG \
  --vmss-name myvmss \
  --settings '{ "fileUris": ["https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/application-gateway/iis/install_nginx.sh"],
  "commandToExecute": "./install_nginx.sh" }'
```

## <a name="create-cname-record-in-your-domain"></a>Criar registo CNAME no seu domínio

Depois de criar o gateway de aplicação com o respetivo endereço IP público, pode obter o endereço DNS e utilizá-lo para criar um registo CNAME no seu domínio. Pode utilizar [az network public-ip show](/cli/azure/network/public-ip#az-network-public-ip-show) para obter o endereço DNS do gateway de aplicação. Copie o valor *fqdn* de DNSSettings e utilize-o como o valor do registo CNAME que criar. Não é recomendada a utilização de registos A, uma vez que o VIP pode ser alterado no reinício do gateway de aplicação.

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --query [dnsSettings.fqdn] \
  --output tsv
```

## <a name="test-the-application-gateway"></a>Testar o gateway de aplicação

Introduza o nome de domínio na barra de endereço do seu browser. Como, por exemplo, http://www.contoso.com.

![Testar o site contoso no gateway de aplicação](./media/redirect-internal-site-cli/application-gateway-nginxtest.png)

Alterar o endereço para o seu domínio, por exemplo http://www.contoso.org e deverá ver que o tráfego foi redirecionado para o serviço de escuta para www\.contoso.com.

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, ficou a saber como:

> * Configurar a rede
> * Criar um gateway de aplicação
> * Adicionar serviços de escuta e a regra de redirecionamento
> * Criar um conjunto com o conjunto de back-end de dimensionamento de máquina virtual
> * Criar um registo CNAME no seu domínio
