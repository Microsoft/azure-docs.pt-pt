---
title: 'Quickstart: Criar um perfil para HA de aplicações - Azure CLI - Azure Traffic Manager'
description: Este artigo de arranque rápido descreve como criar um perfil de Gestor de Tráfego para construir uma aplicação web altamente disponível utilizando o Azure CLI.
services: traffic-manager
author: duongau
manager: kumud
ms.service: traffic-manager
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/09/2020
ms.author: duau
ms.custom: devx-track-azurecli
ms.openlocfilehash: 42870c1a539916cde018667921d913b164fb6b20
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107537688"
---
# <a name="quickstart-create-a-traffic-manager-profile-for-a-highly-available-web-application-using-azure-cli"></a>Quickstart: Criar um perfil de Gestor de Tráfego para uma aplicação web altamente disponível usando o Azure CLI

Este quickstart descreve como criar um perfil de Gestor de Tráfego que oferece alta disponibilidade para a sua aplicação web.

Neste arranque rápido, criará duas instâncias de uma aplicação web. Cada um deles está a correr numa região diferente do Azure. Você vai criar um perfil de Gestor de Tráfego baseado na [prioridade do ponto final](traffic-manager-routing-methods.md#priority-traffic-routing-method). O perfil direciona o tráfego do utilizador para o site primário que executa a aplicação web. O Gestor de Tráfego monitoriza continuamente a aplicação web. Se o site principal não estiver disponível, fornece falha automática no site de reserva.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Este artigo requer a versão 2.0.28 ou posterior do Azure CLI. Se utilizar o Azure Cloud Shell, a versão mais recente já está instalada.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos
Crie um grupo de recursos com [az group create](/cli/azure/group). Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos.

O exemplo a seguir cria um grupo de recursos chamado *myResourceGroup* na localização *este:*

```azurecli-interactive

  az group create \
    --name myResourceGroup \
    --location eastus

```

## <a name="create-a-traffic-manager-profile"></a>Criar um perfil do Gestor de Tráfego

Crie um perfil de Gestor de Tráfego utilizando [o perfil de gestor de tráfego da rede Az que](/cli/azure/network/traffic-manager/profile#az-network-traffic-manager-profile-create) direcione o tráfego do utilizador com base na prioridade do ponto final.

No exemplo seguinte, substitua **<profile_name>** por um nome de perfil único do Gestor de Tráfego.

```azurecli-interactive

az network traffic-manager profile create \
    --name <profile_name> \
    --resource-group myResourceGroup \
    --routing-method Priority \
    --path "/" \
    --protocol HTTP \
    --unique-dns-name <profile_name> \
    --ttl 30 \
    --port 80

```

## <a name="create-web-apps"></a>Criar aplicativos web

Para este arranque rápido, você precisará de duas instâncias de uma aplicação web implantada em duas regiões diferentes de Azure (*Leste dos EUA* e Europa *Ocidental).* Cada um servirá como ponto final primário e de saída para o Gestor de Tráfego.

### <a name="create-web-app-service-plans"></a>Criar planos de serviço de aplicativos web
Crie planos de serviço de aplicações web utilizando o [plano de serviço de aplicações AZ criar](/cli/azure/appservice/plan#az-appservice-plan-create) para as duas instâncias da aplicação web que irá implementar em duas regiões diferentes de Azure.

No exemplo seguinte, substitua **<appspname_eastus>** e **<appspname_westeurope>** por um nome exclusivo do Plano de Serviço de Aplicação

```azurecli-interactive

az appservice plan create \
    --name <appspname_eastus> \
    --resource-group myResourceGroup \
    --location eastus \
    --sku S1

az appservice plan create \
    --name <appspname_westeurope> \
    --resource-group myResourceGroup \
    --location westeurope \
    --sku S1

```

### <a name="create-a-web-app-in-the-app-service-plan"></a>Criar uma aplicação web no plano de serviço de aplicações
Crie dois casos que a aplicação web usando o [az webapp criar](/cli/azure/webapp#az-webapp-create) nos planos do Serviço de Aplicações nas regiões Azure *dos EUA* e Da *Europa Ocidental.*

No exemplo seguinte, substitua **<app1name_eastus>** e **<app2name_westeurope>** por um Nome de Aplicação único, e substitua **<appspname_eastus>** e **<appspname_westeurope>** pelo nome utilizado para criar os planos do Serviço de Aplicações na secção anterior.

```azurecli-interactive

az webapp create \
    --name <app1name_eastus> \
    --plan <appspname_eastus> \
    --resource-group myResourceGroup

az webapp create \
    --name <app2name_westeurope> \
    --plan <appspname_westeurope> \
    --resource-group myResourceGroup

```

## <a name="add-traffic-manager-endpoints"></a>Adicionar pontos finais do Gestor de Tráfego
Adicione as duas Aplicações Web como pontos finais do Traffic Manager utilizando [o ponto final do gestor de tráfego da rede Az para](/cli/azure/network/traffic-manager/endpoint#az-network-traffic-manager-endpoint-create) o perfil do Gestor de Tráfego da seguinte forma:

- Determine o ID da Aplicação Web e adicione a Web App localizada na região *de East US* Azure como o principal ponto final para encaminhar todo o tráfego do utilizador. 
- Determine o ID da Aplicação Web e adicione a Aplicação Web localizada na região do Azure da *Europa Ocidental* como o ponto final de failover. 

Quando o ponto final principal não está disponível, o tráfego liga-se automaticamente para o ponto final de saída.

No exemplo seguinte, substitua **<app1name_eastus>** e **<app2name_westeurope>** pelos Nomes de Aplicações criados para cada região na secção anterior. Em seguida, substitua **<profile_name>** pelo nome de perfil utilizado na secção anterior. 

**Ponto final leste dos EUA**

```azurecli-interactive

az webapp show \
    --name <app1name_eastus> \
    --resource-group myResourceGroup \
    --query id

```

Tome nota do ID exibido na saída e use no seguinte comando para adicionar o ponto final:

```azurecli-interactive

az network traffic-manager endpoint create \
    --name <app1name_eastus> \
    --resource-group myResourceGroup \
    --profile-name <profile_name> \
    --type azureEndpoints \
    --target-resource-id <ID from az webapp show> \
    --priority 1 \
    --endpoint-status Enabled
```

**Ponto final da Europa Ocidental**

```azurecli-interactive

az webapp show \
    --name <app2name_westeurope> \
    --resource-group myResourceGroup \
    --query id

```

Tome nota do ID exibido na saída e use no seguinte comando para adicionar o ponto final:

```azurecli-interactive

az network traffic-manager endpoint create \
    --name <app1name_westeurope> \
    --resource-group myResourceGroup \
    --profile-name <profile_name> \
    --type azureEndpoints \
    --target-resource-id <ID from az webapp show> \
    --priority 2 \
    --endpoint-status Enabled

```

## <a name="test-your-traffic-manager-profile"></a>Teste o seu perfil de Gestor de Tráfego

Nesta secção, você verificará o nome de domínio do seu perfil de Gestor de Tráfego. Também configurará o ponto final principal para não estar disponível. Finalmente, pode ver que a aplicação web ainda está disponível. É porque o Gerente de Tráfego envia o tráfego para o ponto final de falha.

No exemplo seguinte, substitua **<app1name_eastus>** e **<app2name_westeurope>** pelos Nomes de Aplicações criados para cada região na secção anterior. Em seguida, substitua **<profile_name>** pelo nome de perfil utilizado na secção anterior.

### <a name="determine-the-dns-name"></a>Determinar o nome DNS

Determine o nome DNS do perfil do Gestor de Tráfego utilizando [o perfil do gestor de tráfego da rede Az](/cli/azure/network/traffic-manager/profile#az-network-traffic-manager-profile-show).

```azurecli-interactive

az network traffic-manager profile show \
    --name <profile_name> \
    --resource-group myResourceGroup \
    --query dnsConfig.fqdn

```

Copie o valor **relativo do Nome Denas.** O nome DNS do seu perfil de Gestor de Tráfego é *http://<* nome relativo *>.trafficmanager.net*. 

### <a name="view-traffic-manager-in-action"></a>Ver o Gestor de Tráfego em ação
1. Num navegador web, insira o nome DNS do seu perfil de Gestor de Tráfego (*http://<* nome relativo *>.trafficmanager.net*) para ver o website padrão da sua Web App.

    > [!NOTE]
    > Neste cenário de arranque rápido, todos os pedidos de rota para o ponto final principal. Está definido para **a Prioridade 1**.
2. Para ver o Traffic Manager failover em ação, desative o seu site principal utilizando [a atualização do ponto final do gestor de tráfego da rede Az](/cli/azure/network/traffic-manager/endpoint#az-network-traffic-manager-endpoint-update).

   ```azurecli-interactive

    az network traffic-manager endpoint update \
        --name <app1name_eastus> \
        --resource-group myResourceGroup \
        --profile-name <profile_name> \
        --type azureEndpoints \
        --endpoint-status Disabled
    
   ```

3. Copie o nome DNS do seu perfil de Gestor de Tráfego (*http://<* nome relativo *>.trafficmanager.net*) para ver o site numa nova sessão de navegador web.
4. Verifique se a aplicação web ainda está disponível.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando terminar, elimine os grupos de recursos, aplicações web e todos os recursos relacionados usando [o grupo AZ delete](/cli/azure/group#az-group-delete).

```azurecli-interactive

az group delete \
    --resource-group myResourceGroup

```

## <a name="next-steps"></a>Passos seguintes

Neste quickstart, criou um perfil de Gestor de Tráfego que proporciona uma elevada disponibilidade para a sua aplicação web. Para saber mais sobre o tráfego de encaminhamento, continue para os tutoriais do Traffic Manager.

> [!div class="nextstepaction"]
> [Traffic Manager tutorials](tutorial-traffic-manager-improve-website-response.md) (Tutoriais do Gestor de Tráfego)