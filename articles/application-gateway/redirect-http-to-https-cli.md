---
title: Redirecionamento de HTTP para HTTPS usando a CLI
titleSuffix: Azure Application Gateway
description: Saiba como criar um gateway de aplicação e adicionar um certificado para terminação SSL com a CLI do Azure.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/15/2019
ms.author: victorh
ms.openlocfilehash: ff615507723b949105fc2b604d6bff869bdb33dc
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2019
ms.locfileid: "74108766"
---
# <a name="create-an-application-gateway-with-http-to-https-redirection-using-the-azure-cli"></a>Criar um gateway de aplicativo com HTTP para redirecionamento HTTPS usando o CLI do Azure

Você pode usar o CLI do Azure para criar um [Gateway de aplicativo](overview.md) com um certificado para terminação SSL. Uma regra de roteamento é utilizada para redirecionar o tráfego HTTP para a porta HTTPS no seu gateway de aplicação. Neste exemplo, também de criar uma [conjunto de dimensionamento de máquina virtual](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) para o conjunto de back-end do gateway de aplicação que contém duas instâncias de máquina virtual.

Neste artigo, vai aprender a:

> [!div class="checklist"]
> * Criar um certificado autoassinado
> * Configurar uma rede
> * Criar um gateway de aplicação com o certificado
> * Adicionar uma regra de serviço de escuta e o redirecionamento
> * Criar um conjunto de dimensionamento de máquinas virtuais com o conjunto de back-end predefinido

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se optar por instalar e usar a CLI localmente, este tópico requer a execução da versão 2.0.4 ou posterior da CLI do Azure. Para localizar a versão, execute `az --version`. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

## <a name="create-a-self-signed-certificate"></a>Criar um certificado autoassinado

Para utilização em produção, deve importar um certificado válido assinado por um fornecedor fidedigno. Neste tutorial, vai criar um certificado autoassinado e um ficheiro pfx com o comando openssl.

```azurecli-interactive
openssl req -x509 -sha256 -nodes -days 365 -newkey rsa:2048 -keyout privateKey.key -out appgwcert.crt
```

Introduza valores que façam sentido para o seu certificado. Pode aceitar os valores predefinidos.

```azurecli-interactive
openssl pkcs12 -export -out appgwcert.pfx -inkey privateKey.key -in appgwcert.crt
```

Introduza a palavra-passe para o certificado. Neste exemplo, *Azure123456!* é a palavra-passe utilizada.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos:

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

## <a name="create-the-application-gateway"></a>Criar o gateway de aplicação

Pode utilizar [az network application-gateway create](/cli/azure/network/application-gateway#az-network-application-gateway-create) para criar o gateway de aplicação denominado *myAppGateway*. Quando cria um gateway de aplicação com a CLI do Azure, especifica informações de configuração, tais como a capacidade, sku e definições de HTTP. 

O gateway de aplicação é atribuído a *myAGSubnet* e *myAGPublicIPAddress* que criou anteriormente. Neste exemplo, vai associar o certificado que criou e a respetiva palavra-passe quando criar o gateway de aplicação. 

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
  --frontend-port 443 \
  --http-settings-port 80 \
  --http-settings-protocol Http \
  --public-ip-address myAGPublicIPAddress \
  --cert-file appgwcert.pfx \
  --cert-password "Azure123456!"

```

 A criação do gateway de aplicação pode demorar vários minutos. Depois de criado o gateway de aplicação, pode ver estas novas funcionalidades do mesmo:

- *appGatewayBackendPool* - um gateway de aplicação tem de ter, pelo menos, um conjunto de endereços de back-end.
- *appGatewayBackendHttpSettings* - especifica que a porta 80 e um protocolo HTTP são utilizados para a comunicação.
- *appGatewayHttpListener* - o serviço de escuta predefinido associado a *appGatewayBackendPool*.
- *appGatewayFrontendIP* - atribui *myAGPublicIPAddress* a *appGatewayHttpListener*.
- *rule1* - a regra de encaminhamento predefinida associada a *appGatewayHttpListener*.

## <a name="add-a-listener-and-redirection-rule"></a>Adicionar uma regra de serviço de escuta e o redirecionamento

### <a name="add-the-http-port"></a>Adicionar a porta HTTP

Você pode usar [AZ Network Application-Gateway front-Port Create](/cli/azure/network/application-gateway/frontend-port#az-network-application-gateway-frontend-port-create) para adicionar a porta http ao gateway de aplicativo.

```azurecli-interactive
az network application-gateway frontend-port create \
  --port 80 \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --name httpPort
```

### <a name="add-the-http-listener"></a>Adicionar o ouvinte HTTP

Você pode usar [AZ Network Application-Gateway http-Listener Create](/cli/azure/network/application-gateway/http-listener#az-network-application-gateway-http-listener-create) para adicionar o ouvinte chamado *MyListener* ao gateway de aplicativo.

```azurecli-interactive
az network application-gateway http-listener create \
  --name myListener \
  --frontend-ip appGatewayFrontendIP \
  --frontend-port httpPort \
  --resource-group myResourceGroupAG \
  --gateway-name myAppGateway
```

### <a name="add-the-redirection-configuration"></a>Adicionar a configuração de redirecionamento

Adicione a configuração de redirecionamento de HTTP para HTTPS ao gateway de aplicativo usando [AZ Network Application-Gateway Redirect-config Create](/cli/azure/network/application-gateway/redirect-config#az-network-application-gateway-redirect-config-create).

```azurecli-interactive
az network application-gateway redirect-config create \
  --name httpToHttps \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --type Permanent \
  --target-listener appGatewayHttpListener \
  --include-path true \
  --include-query-string true
```

### <a name="add-the-routing-rule"></a>Adicionar a regra de roteamento

Adicione a regra de roteamento denominada *rule2* com a configuração de redirecionamento para o gateway de aplicativo usando [AZ Network Application-Gateway regra Create](/cli/azure/network/application-gateway/rule#az-network-application-gateway-rule-create).

```azurecli-interactive
az network application-gateway rule create \
  --gateway-name myAppGateway \
  --name rule2 \
  --resource-group myResourceGroupAG \
  --http-listener myListener \
  --rule-type Basic \
  --redirect-config httpToHttps
```

## <a name="create-a-virtual-machine-scale-set"></a>Criar um conjunto de dimensionamento de máquinas virtuais

Neste exemplo, você cria um conjunto de dimensionamento de máquinas virtuais chamado *myvmss* que fornece servidores para o pool de back-end no gateway de aplicativo. As máquinas virtuais no conjunto de dimensionamento são associadas a *myBackendSubnet* e *appGatewayBackendPool*. Para criar o conjunto de dimensionamento, pode utilizar [az vmss create](/cli/azure/vmss#az-vmss-create).

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

## <a name="test-the-application-gateway"></a>Testar o gateway de aplicação

Para obter o endereço IP público do gateway de aplicação, pode utilizar [az network public-ip show](/cli/azure/network/public-ip). Copie o endereço IP público e cole-o na barra de endereço do browser.

```azurepowershell-interactive
az network public-ip show \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --query [ipAddress] \
  --output tsv
```

![Aviso de segurança](./media/redirect-http-to-https-cli/application-gateway-secure.png)

Para aceitar o aviso de segurança se tiver utilizado um certificado autoassinado, selecione **Detalhes** e, em seguida **Aceda à página Web**. O site NGINX protegido é apresentado como no exemplo seguinte:

![Testar o URL base no gateway de aplicação](./media/redirect-http-to-https-cli/application-gateway-nginxtest.png)

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Criar um certificado autoassinado
> * Configurar uma rede
> * Criar um gateway de aplicação com o certificado
> * Adicionar uma regra de serviço de escuta e o redirecionamento
> * Criar um conjunto de dimensionamento de máquinas virtuais com o conjunto de back-end predefinido


