---
title: Reorientação externa do tráfego utilizando cli - Gateway de aplicação Azure
description: Aprenda a criar um portal de aplicações que redirecione o tráfego interno da web para a piscina apropriada usando o Azure CLI.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/13/2019
ms.author: victorh
ms.openlocfilehash: fc955b4959bb20628463f7699a0b66ec2b89a393
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74011600"
---
# <a name="create-an-application-gateway-with-external-redirection-using-the-azure-cli"></a>Criar um portal de aplicação com reorientação externa utilizando o Azure CLI

Pode utilizar o Azure CLI para configurar a [redirecção](multiple-site-overview.md) do tráfego web quando criar um gateway de [aplicação](overview.md). Neste tutorial, configura um ouvinte e regra que redireciona o tráfego web que chega à porta de entrada da aplicação para um site externo.

Neste artigo, vai aprender a:

> [!div class="checklist"]
> * Configurar a rede
> * Criar uma regra de escuta e reorientação
> * Criar um gateway de aplicação

Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se optar por instalar e usar a CLI localmente, este tópico requer a execução da versão 2.0.4 ou posterior da CLI do Azure. Para localizar a versão, execute `az --version`. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)](/cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Um grupo de recursos é um contentor lógico no qual os recursos do Azure são implementados e geridos. Crie um grupo de recursos com [az group create](/cli/azure/group).

O exemplo seguinte cria um grupo de recursos com o nome *myResourceGroupAG* na localização *eastus*.

```azurecli-interactive 
az group create --name myResourceGroupAG --location eastus
```

## <a name="create-network-resources"></a>Criar recursos de rede 

Crie a rede virtual denominada *myVNet* e a sub-rede denominada *myAGSubnet* com [az network vnet create](/cli/azure/network/vnet). Crie o endereço IP público denominado *myAGPublicIPAddress* com [az network public-ip create](/cli/azure/network/public-ip). Estes recursos são utilizados para fornecer conectividade de rede ao gateway de aplicação e aos respetivos recursos associados.

```azurecli-interactive
az network vnet create \
  --name myVNet \
  --resource-group myResourceGroupAG \
  --location eastus \
  --address-prefix 10.0.0.0/16 \
  --subnet-name myAGSubnet \
  --subnet-prefix 10.0.1.0/24
az network public-ip create \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress
```

## <a name="create-an-application-gateway"></a>Criar um gateway de aplicação

Pode utilizar [az network application-gateway create](/cli/azure/network/application-gateway) para criar o gateway de aplicação denominado *myAppGateway*. Quando cria um gateway de aplicação com a CLI do Azure, especifica informações de configuração, tais como a capacidade, sku e definições de HTTP. O gateway da aplicação é atribuído ao *myAGSubnet* e *ao myPublicIPAddress* que criou anteriormente. 

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
  --frontend-port 8080 \
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

### <a name="add-the-redirection-configuration"></a>Adicione a configuração de reorientação

Adicione a configuração de redirecionamento que envia tráfego de *www\.consoto.org* ao ouvinte para www *\.contoso.com* ao gateway de aplicação utilizando a criação de [redirect-config](/cli/azure/network/application-gateway/redirect-config)de gateway de aplicação da rede Az .

```azurecli-interactive
az network application-gateway redirect-config create \
  --name myredirect \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --type Temporary \
  --target-url "https://bing.com"
```

### <a name="add-a-listener-and-routing-rule"></a>Adicione uma regra de escuta e encaminhamento

É necessário um ouvinte que permita que a porta de entrada de aplicação encaminhe adequadamente o tráfego. Crie o ouvinte utilizando o [portal de aplicação de linha Az http-listener criar](/cli/azure/network/application-gateway) com a porta frontend criada com a criação de porta [frontend-gateway de aplicação de rede az](/cli/azure/network/application-gateway). É necessária uma regra para que o ouvinte saiba para onde enviar o tráfego. Criar uma regra básica chamada *redirecionamentoRule* utilizando a regra de [gateway de aplicação da rede Az](/cli/azure/network/application-gateway).

```azurecli-interactive
az network application-gateway frontend-port create \
  --port 80 \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --name redirectPort
az network application-gateway http-listener create \
  --name redirectListener \
  --frontend-ip appGatewayFrontendIP \
  --frontend-port redirectPort \
  --resource-group myResourceGroupAG \
  --gateway-name myAppGateway
az network application-gateway rule create \
  --gateway-name myAppGateway \
  --name redirectRule \
  --resource-group myResourceGroupAG \
  --http-listener redirectListener \
  --rule-type Basic \
  --redirect-config myredirect
```

## <a name="test-the-application-gateway"></a>Testar o gateway de aplicação

Para obter o endereço IP público do gateway de aplicação, pode utilizar [az network public-ip show](/cli/azure/network/public-ip). Copie o endereço IP público e cole-o na barra de endereço do browser.

Deve ver *bing.com* aparecer no seu navegador.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> * Configurar a rede
> * Criar uma regra de escuta e reorientação
> * Criar um gateway de aplicação
