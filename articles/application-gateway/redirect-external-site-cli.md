---
title: Redirecionamento de tráfego externo usando Aplicativo Azure gateway de CLI
description: Saiba como criar um gateway de aplicativo que redireciona o tráfego da Web interno para o pool apropriado usando o CLI do Azure.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/13/2019
ms.author: victorh
ms.openlocfilehash: fc955b4959bb20628463f7699a0b66ec2b89a393
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74011600"
---
# <a name="create-an-application-gateway-with-external-redirection-using-the-azure-cli"></a>Criar um gateway de aplicativo com redirecionamento externo usando o CLI do Azure

Você pode usar o CLI do Azure para configurar o [redirecionamento de tráfego da Web](multiple-site-overview.md) ao criar um [Gateway de aplicativo](overview.md). Neste tutorial, você configura um ouvinte e uma regra que redireciona o tráfego da Web que chega ao gateway de aplicativo para um site externo.

Neste artigo, vai aprender a:

> [!div class="checklist"]
> * Configurar a rede
> * Criar um ouvinte e uma regra de redirecionamento
> * Para criar um gateway de aplicação

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

## <a name="create-an-application-gateway"></a>Para criar um gateway de aplicação

Pode utilizar [az network application-gateway create](/cli/azure/network/application-gateway) para criar o gateway de aplicação denominado *myAppGateway*. Quando cria um gateway de aplicação com a CLI do Azure, especifica informações de configuração, tais como a capacidade, sku e definições de HTTP. O gateway de aplicativo é atribuído a *myAGSubnet* e *myPublicIPAddress* que você criou anteriormente. 

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

### <a name="add-the-redirection-configuration"></a>Adicionar a configuração de redirecionamento

Adicione a configuração de redirecionamento que envia o tráfego do *www\.consoto.org* ao ouvinte para *www\.contoso.com* ao gateway de aplicativo usando [AZ Network Application-Gateway Redirect-config Create](/cli/azure/network/application-gateway/redirect-config).

```azurecli-interactive
az network application-gateway redirect-config create \
  --name myredirect \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --type Temporary \
  --target-url "https://bing.com"
```

### <a name="add-a-listener-and-routing-rule"></a>Adicionar um ouvinte e uma regra de roteamento

Um ouvinte é necessário para habilitar o gateway de aplicativo para rotear adequadamente o tráfego. Crie o ouvinte usando [AZ Network Application-Gateway http-Listener Create](/cli/azure/network/application-gateway) com a porta de front-end criada com [AZ Network Application-Gateway front-Port Create](/cli/azure/network/application-gateway). Uma regra é necessária para que o ouvinte saiba onde enviar o tráfego de entrada. Crie uma regra básica chamada *redirectrule,* usando [AZ Network Application-Gateway regra Create](/cli/azure/network/application-gateway).

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

Você deve ver *Bing.com* aparecem no navegador.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> * Configurar a rede
> * Criar um ouvinte e uma regra de redirecionamento
> * Para criar um gateway de aplicação
