---
title: Hospedar vários web sites usando CLI
titleSuffix: Azure Application Gateway
description: Saiba como criar um gateway de aplicação que aloja vários sites através da CLI do Azure.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/13/2019
ms.author: victorh
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 350962aed89d04c5508e7b2c50e8a838cd5a7174
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "94566151"
---
# <a name="create-an-application-gateway-that-hosts-multiple-web-sites-using-the-azure-cli"></a>Criar um gateway de aplicações que acolhe vários web sites usando o Azure CLI

Pode utilizar a CLI do Azure para [configurar o alojamento de vários sites](multiple-site-overview.md) quando cria um [gateway de aplicação](overview.md). Neste artigo, você define piscinas de endereços de backend usando conjuntos de escala de máquinas virtuais. Em seguida, vai configurar os serviços de escuta e as regras com base nos domínios que possui para assegurar que o tráfego Web chega aos servidores adequados nos conjuntos. Este artigo pressupõe que possui vários domínios e utiliza exemplos de *www \. contoso.com* e *www \. fabrikam.com*.

Neste artigo, vai aprender a:

* Configurar a rede
* Criar um gateway de aplicação
* Criar serviços de escuta de back-end
* Criar regras de encaminhamento
* Criar conjuntos de dimensionamento de máquinas virtuais com conjuntos de back-end
* Criar um registo CNAME no seu domínio

:::image type="content" source="./media/tutorial-multiple-sites-cli/scenario.png" alt-text="Gateway de aplicação multi-site":::

Se preferir, pode concluir este procedimento utilizando [a Azure PowerShell](tutorial-multiple-sites-powershell.md).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

 - Este tutorial requer a versão 2.0.4 ou posterior do CLI Azure. Se utilizar o Azure Cloud Shell, a versão mais recente já está instalada.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Um grupo de recursos é um contentor lógico no qual os recursos do Azure são implementados e geridos. Crie um grupo de recursos com [az group create](/cli/azure/group).

O exemplo seguinte cria um grupo de recursos com o nome *myResourceGroupAG* na localização *eastus*.

```azurecli-interactive
az group create --name myResourceGroupAG --location eastus
```

## <a name="create-network-resources"></a>Criar recursos de rede

Crie a rede virtual denominada e a sub-rede denominada *myAGSubnet* com [az network vnet create](/cli/azure/network/vnet). Em seguida, pode adicionar a sub-rede que é necessária para os servidores de back-end, com [az network vnet subnet create](/cli/azure/network/vnet/subnet). Crie o endereço IP público denominado *myAGPublicIPAddress* com [az network public-ip create](/cli/azure/network/public-ip).

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
  --name myAGPublicIPAddress \
  --allocation-method Static \
  --sku Standard
```

## <a name="create-the-application-gateway"></a>Criar o gateway de aplicação

Pode utilizar [az network application-gateway create](/cli/azure/network/application-gateway#az-network-application-gateway-create) para criar o gateway de aplicação. Quando cria um gateway de aplicação com a CLI do Azure, especifica informações de configuração, tais como a capacidade, sku e definições de HTTP. O gateway de aplicação é atribuído a *myAGSubnet* e *myAGPublicIPAddress* que criou anteriormente. 

```azurecli-interactive
az network application-gateway create \
  --name myAppGateway \
  --location eastus \
  --resource-group myResourceGroupAG \
  --vnet-name myVNet \
  --subnet myAGsubnet \
  --capacity 2 \
  --sku Standard_v2 \
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

### <a name="add-the-backend-pools"></a>Adicionar os conjuntos de back-end

Adicione os pools de backend que são necessários para conter os servidores backend usando [a az rede aplicação-gateway endereço-pool criar](/cli/azure/network/application-gateway/address-pool#az-network-application-gateway-address-pool-create)
```azurecli-interactive
az network application-gateway address-pool create \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --name contosoPool

az network application-gateway address-pool create \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --name fabrikamPool
```

### <a name="add-listeners"></a>Adicionar serviços de escuta

Adicione os ouvintes que são necessários para encaminhar o tráfego usando [a az rede de gateway http-listener criar](/cli/azure/network/application-gateway/http-listener#az-network-application-gateway-http-listener-create).

>[!NOTE]
> Com o Application Gateway ou WAF v2 SKU, também pode configurar até 5 nomes de anfitriões por ouvinte e pode utilizar caracteres wildcard no nome do anfitrião. Consulte [os nomes dos anfitriões wildcard no ouvinte](multiple-site-overview.md#wildcard-host-names-in-listener-preview) para obter mais informações.
>Para utilizar vários nomes de anfitriões e caracteres wildcard num ouvinte que utilize o Azure CLI, tem de usar `--host-names` em vez de `--host-name` . Com os nomes dos anfitriões, pode mencionar até cinco nomes de anfitriões como valores separados pelo espaço. Por exemplo, `--host-names "*.contoso.com *.fabrikam.com"`

```azurecli-interactive
az network application-gateway http-listener create \
  --name contosoListener \
  --frontend-ip appGatewayFrontendIP \
  --frontend-port appGatewayFrontendPort \
  --resource-group myResourceGroupAG \
  --gateway-name myAppGateway \
  --host-name www.contoso.com

az network application-gateway http-listener create \
  --name fabrikamListener \
  --frontend-ip appGatewayFrontendIP \
  --frontend-port appGatewayFrontendPort \
  --resource-group myResourceGroupAG \
  --gateway-name myAppGateway \
  --host-name www.fabrikam.com   
  ```

### <a name="add-routing-rules"></a>Adicionar regras de encaminhamento

As regras são processadas na ordem em que estão listadas. O tráfego é direcionado usando a primeira regra que corresponde independentemente da especificidade. Por exemplo, se tiver uma regra com um serviço de escuta básico e uma regra com uma escuta de vários sites, ambas na mesma porta, a regra com o serviço de escuta de vários sites tem de estar listada antes da regra com o serviço de escuta básico, para que a regra de vários sites funcione conforme esperado. 

Neste exemplo, cria-se duas novas regras e elimina a regra padrão criada quando implementou o gateway de aplicações. Pode adicionar a regra com [az network application-gateway rule create](/cli/azure/network/application-gateway/rule#az-network-application-gateway-rule-create).

```azurecli-interactive
az network application-gateway rule create \
  --gateway-name myAppGateway \
  --name contosoRule \
  --resource-group myResourceGroupAG \
  --http-listener contosoListener \
  --rule-type Basic \
  --address-pool contosoPool

az network application-gateway rule create \
  --gateway-name myAppGateway \
  --name fabrikamRule \
  --resource-group myResourceGroupAG \
  --http-listener fabrikamListener \
  --rule-type Basic \
  --address-pool fabrikamPool

az network application-gateway rule delete \
  --gateway-name myAppGateway \
  --name rule1 \
  --resource-group myResourceGroupAG
```

## <a name="create-virtual-machine-scale-sets"></a>Criar conjuntos de dimensionamento de máquinas virtuais

Neste exemplo, vai criar três conjuntos de dimensionamento de máquinas virtuais que suportam os três conjuntos de back-end no gateway de aplicação. Os conjuntos de dimensionamento que criar são denominados *myvmss1*, *myvmss2* e *myvmss3*. Cada conjunto de dimensionamento contém duas instâncias de máquina virtual onde vai instalar o IIS.

```azurecli-interactive
for i in `seq 1 2`; do

  if [ $i -eq 1 ]
  then
    poolName="contosoPool"
  fi

  if [ $i -eq 2 ]
  then
    poolName="fabrikamPool"
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
for i in `seq 1 2`; do

  az vmss extension set \
    --publisher Microsoft.Azure.Extensions \
    --version 2.0 \
    --name CustomScript \
    --resource-group myResourceGroupAG \
    --vmss-name myvmss$i \
    --settings '{ "fileUris": ["https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/application-gateway/iis/install_nginx.sh"],
  "commandToExecute": "./install_nginx.sh" }'

done
```

## <a name="create-a-cname-record-in-your-domain"></a>Criar um registo CNAME no seu domínio

Depois de criar o gateway de aplicação com o respetivo endereço IP público, pode obter o endereço DNS e utilizá-lo para criar um registo CNAME no seu domínio. Pode utilizar [az network public-ip show](/cli/azure/network/public-ip#az-network-public-ip-show) para obter o endereço DNS do gateway de aplicação. Copie o valor *fqdn* de DNSSettings e utilize-o como o valor do registo CNAME que criar. 

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --query [dnsSettings.fqdn] \
  --output tsv
```

A utilização de registos A não é recomendada porque o VIP pode mudar quando o gateway de aplicação recomeçar.

## <a name="test-the-application-gateway"></a>Testar o gateway de aplicação

Introduza o nome de domínio na barra de endereço do seu browser. Como, http: \/ /www.contoso.com.

![Testar o site contoso no gateway de aplicação](./media/tutorial-multiple-sites-cli/application-gateway-nginxtest1.png)

Altere o endereço para o outro domínio, e deverá ver algo semelhante ao seguinte exemplo:

![Testar o site fabrikam no gateway de aplicação](./media/tutorial-multiple-sites-cli/application-gateway-nginxtest2.png)

## <a name="clean-up-resources"></a>Limpar os recursos

Quando já não forem necessários, remova o grupo de recursos, o gateway de aplicação e todos os recursos relacionados.

```azurecli-interactive
az group delete --name myResourceGroupAG
```

## <a name="next-steps"></a>Passos seguintes

[Criar um gateway de aplicação com regras de encaminhamento com base no caminho de URL](./tutorial-url-route-cli.md)
