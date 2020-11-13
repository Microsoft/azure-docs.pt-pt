---
title: 'CLI: Implementar ponto final privado para web app com Azure CLI'
description: Saiba como usar o Azure CLI para implementar o Private Endpoint para a sua Web App
author: ericgre
ms.assetid: a56faf72-7237-41e7-85ce-da8346f2bcaa
ms.devlang: azurecli
ms.topic: sample
ms.date: 07/06/2020
ms.author: ericg
ms.service: app-service
ms.workload: web
ms.openlocfilehash: 7246bb3453f6e4863faf15dbcbfdf8534481ddad
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/12/2020
ms.locfileid: "94561453"
---
# <a name="create-an-app-service-app-and-deploy-private-endpoint-using-azure-cli"></a>Crie uma aplicação de Serviço de Aplicações e implemente o Private Endpoint usando o Azure CLI

Este script de amostra cria uma aplicação no Serviço de Aplicações com os seus recursos relacionados e, em seguida, implementa um Ponto Final Privado.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Este tutorial requer a versão 2.0.28 ou posterior do Azure CLI. Se utilizar o Azure Cloud Shell, a versão mais recente já está instalada.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Antes de criar qualquer recurso, tem de criar um grupo de recursos para hospedar a Web App, a Rede Virtual e outros componentes da rede. Crie um grupo de recursos com [az group create](/cli/azure/group). Este exemplo cria um grupo de recursos chamado *myResourceGroup* na *localização francecentral:*

```azurecli-interactive
az group create --name myResourceGroup --location francecentral 
```

## <a name="create-an-app-service-plan"></a>Criar um Plano do Serviço de Aplicações

Precisa de criar um Plano de Serviço de Aplicações para hospedar a sua Web App.
Crie um Plano de Serviço de Aplicações com [plano de serviço de app az criar.](/cli/azure/appservice/plan?view=azure-cli-latest#az-appservice-plan-create)
Este exemplo cria o Plano de Serviço de Aplicações nomeado *myAppServicePlan* na localização *francecentral* com *sku P1V2* e apenas um trabalhador: 

```azurecli-interactive
az appservice plan create \
--name myAppServicePlan \
--resource-group myResourceGroup \
--location francecentral \
--sku P1V2 \
--number-of-workers 1
```

## <a name="create-a-web-app"></a>Criar uma aplicação Web

Agora que tem um Plano de Serviço de Aplicações, pode implementar uma Web App.
Criar uma Web App com [az appservice plan create](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create.
Este exemplo cria uma Web App chamada *mySiteName* no Plano nomeado *myAppServicePlan*

```azurecli-interactive
az webapp create \
--name mySiteName \
--resource-group myResourceGroup \
--plan myAppServicePlan
```

## <a name="create-a-vnet"></a>Criar uma VNet

Criar uma Rede Virtual com [a rede Az vnet criar.](/cli/azure/network/vnet) Este exemplo cria uma Rede Virtual padrão chamada *myVNet* com uma sub-rede chamada *mySubnet* :

```azurecli-interactive
az network vnet create \
--name myVNet \
--resource-group myResourceGroup \
--location francecentral \
--address-prefixes 10.8.0.0/16 \
--subnet-name mySubnet \
--subnet-prefixes 10.8.100.0/24
```

## <a name="configure-the-subnet"></a>Configure a Sub-rede 

É necessário atualizar a sub-rede para desativar as políticas privadas de rede de pontos finais. Atualize uma configuração de sub-rede chamada *mySubnet* com [atualização da sub-rede de rede az:](https://docs.microsoft.com/cli/azure/network/vnet/subnet?view=azure-cli-latest#az-network-vnet-subnet-update)

```azurecli-interactive
az network vnet subnet update \
--name mySubnet \
--resource-group myResourceGroup \
--vnet-name myVNet \
--disable-private-endpoint-network-policies true
```

## <a name="create-the-private-endpoint"></a>Criar o Ponto Final Privado

Crie o Ponto Final Privado para a sua Web App com [a criação de ponto de terminação privada da rede AZ.](/cli/azure/network/private-endpoint) Este exemplo cria um Ponto Final Privado nomeado *myPrivateEndpoint* no vNet *myVNet* na sub-rede *mySubnet* com uma ligação chamada *myConnectionName* ao ID de recursos da minha Aplicação Web /subscrições/SubscriçãoID/resourceGroups/myResourceGroup/providers/Microsoft.Web/sites/myWebApp, o parâmetro de grupo são *sites* para tipo de Aplicação Web. 

```azurecli-interactive
az network private-endpoint create \
--name myPrivateEndpoint \
--resource-group myResourceGroup \
--vnet-name myVNet \
--subnet mySubnet \
--connection-name myConnectionName \
--private-connection-resource-id /subscriptions/SubscriptionID/resourceGroups/myResourceGroup/providers/Microsoft.Web/sites/myWebApp \
--group-id sites
```

## <a name="configure-the-private-zone"></a>Configure a zona privada

No final, você precisa criar uma zona privada de DNS chamada *privatelink.azurewebsites.net* ligado ao VNet para resolver o nome DNS da Web App.


```azurecli-interactive
az network private-dns zone create \
--name privatelink.azurewebsites.net \
--resource-group myResourceGroup

az network private-dns link vnet create \
--name myDNSLink \
--resource-group myResourceGroup \
--registration-enabled false \
--virtual-network myVNet \
--zone-name privatelink.azurewebsites.net

az network private-endpoint dns-zone-group create \
--name myZoneGroup \
--resource-group myResourceGroup \
--endpoint-name myPrivateEndpoint \
--private-dns-zone privatelink.azurewebsites.net \
--zone-name privatelink.azurewebsites.net
```






[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]


## <a name="next-steps"></a>Passos seguintes

- Para obter mais informações sobre a CLI do Azure, veja [Documentação da CLI do Azure](/cli/azure).
- Pode ver exemplos do script da CLI do Serviço de Aplicações adicionais na [documentação do Serviço de Aplicações do Azure](../samples-cli.md).