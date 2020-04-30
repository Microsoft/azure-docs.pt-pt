---
title: Quickstart:Criar um perfil para HA de aplicações - Azure CLI - Gestor de Tráfego Azure
description: Este artigo de arranque rápido descreve como criar um perfil de Gestor de Tráfego para construir uma aplicação web altamente disponível.
services: traffic-manager
author: rohinkoul
mnager: twooley
Customer intent: As an IT admin, I want to direct user traffic to ensure high availability of web applications.
ms.service: traffic-manager
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/29/2019
ms.author: rohink
ms.openlocfilehash: e19850243498fc24c9a726f4603590df15f3a046
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "79531520"
---
# <a name="quickstart-create-a-traffic-manager-profile-for-a-highly-available-web-application-using-azure-cli"></a>Quickstart: Criar um perfil de Gestor de Tráfego para uma aplicação web altamente disponível usando o Azure CLI

Este quickstart descreve como criar um perfil de Gestor de Tráfego que proporciona alta disponibilidade para a sua aplicação web.

Neste arranque rápido, irá criar duas instâncias de uma aplicação web. Cada um deles está a funcionar numa região de Azure diferente. Criará um perfil de Gestor de Tráfego baseado na [prioridade do ponto final.](traffic-manager-routing-methods.md#priority-traffic-routing-method) O perfil direciona o tráfego do utilizador para o site principal que executa a aplicação web. O Gestor de Tráfego monitoriza continuamente a aplicação web. Se o site principal não estiver disponível, fornece falha automática ao site de backup.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) agora.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar a CLI localmente, este tutorial requer que execute uma versão da CLI do Azure que seja a 2.0.28 ou posterior. Para localizar a versão, execute `az --version`. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Criar um grupo de recursos
Crie um grupo de recursos com [az group create](https://docs.microsoft.com/cli/azure/group). Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos.

O exemplo seguinte cria um grupo de recursos chamado *myResourceGroup* na localização *oriental:*

```azurecli-interactive

  az group create \
    --name myResourceGroup \
    --location eastus

```

## <a name="create-a-traffic-manager-profile"></a>Criar um perfil do Gestor de Tráfego

Criar um perfil de Gestor de Tráfego utilizando o perfil de [gestor de tráfego da rede Az](https://docs.microsoft.com/cli/azure/network/traffic-manager/profile?view=azure-cli-latest#az-network-traffic-manager-profile-create) que direciona o tráfego do utilizador com base na prioridade do ponto final.

No exemplo seguinte, substitua **<profile_name>** por um nome único de perfil do Traffic Manager.

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

Para este arranque rápido, você precisará de dois casos de uma aplicação web implantada em duas regiões azure diferentes *(Leste dos EUA* e *Europa Ocidental).* Cada um servirá como pontos finais primários e falhados para o Gestor de Tráfego.

### <a name="create-web-app-service-plans"></a>Criar planos de serviço de aplicações web
Crie planos de serviço de aplicações web utilizando [o plano de appservice az criar](https://docs.microsoft.com/cli/azure/appservice/plan?view=azure-cli-latest#az-appservice-plan-create) para as duas instâncias da aplicação web que irá implementar em duas regiões azure diferentes.

No exemplo seguinte, substitua **<appspname_eastus>** e<appspname_westeurope **>** por um nome único do plano de serviço de aplicações

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
Crie dois casos que a aplicação web usando [a az webapp criar](https://docs.microsoft.com/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) nos planos do App Service nas regiões *leste dos EUA* e West *Europe* Azure.

No exemplo seguinte, substitua **<app1name_eastus>** e<app2name_westeurope **>** por um nome único de aplicação e substitua<**appspname_eastus>** e<**appspname_westeurope>** pelo nome utilizado para criar os planos do Serviço de Aplicações na secção anterior.

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
Adicione as duas Aplicações Web como pontos finais do Gestor de Tráfego utilizando o [ponto final do gestor de tráfego da rede Az criar](https://docs.microsoft.com/cli/azure/network/traffic-manager/endpoint?view=azure-cli-latest#az-network-traffic-manager-endpoint-create) para o perfil do Gestor de Tráfego da seguinte forma:

- Determine o id da Web App e adicione a Aplicação Web localizada na região *de East US* Azure como o principal ponto final para direcionar todo o tráfego do utilizador. 
- Determine o id da Web App e adicione a Aplicação Web localizada na região *west europe* azure como o ponto final falhado. 

Quando o ponto final principal não está disponível, o tráfego automaticamente se encaminha para o ponto final de falha.

No exemplo seguinte, substitua **<app1name_eastus>** e<app2name_westeurope **>** pelos Nomes das Aplicações criados para cada região na secção anterior, substitua<**appspname_eastus>** e<appspname_westeurope **>** pelo nome utilizado para criar os planos do Serviço de Aplicações na secção anterior e substituir<**profile_name>** pelo nome de perfil utilizado na secção anterior. 

**Ponto final dos EUA Oriental**

```azurecli-interactive

az webapp show \
    --name <app1name_eastus> \
    --resource-group myResourceGroup \
    --query id

```

Tome nota do id apresentado na saída e use no seguinte comando para adicionar o ponto final:

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

Tome nota do id apresentado na saída e use no seguinte comando para adicionar o ponto final:

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

Nesta secção, você verificará o nome de domínio do seu perfil de Gestor de Tráfego. Também configurará o ponto final principal para não estar disponível. Finalmente, pode ver que a aplicação da web ainda está disponível. É porque o Gestor de Tráfego envia o tráfego para o ponto final do fracasso.

No exemplo seguinte, substitua **<app1name_eastus>** e<app2name_westeurope **>** pelos Nomes das Aplicações criados para cada região na secção anterior, substitua<**appspname_eastus>** e<appspname_westeurope **>** pelo nome utilizado para criar os planos do Serviço de Aplicações na secção anterior e substituir<**profile_name>** pelo nome de perfil utilizado na secção anterior.

### <a name="determine-the-dns-name"></a>Determinar o nome DNS

Determine o nome DNS do perfil do Gestor de Tráfego utilizando o perfil do [gestor de tráfego da rede Az](https://docs.microsoft.com/cli/azure/network/traffic-manager/profile?view=azure-cli-latest#az-network-traffic-manager-profile-show).

```azurecli-interactive

az network traffic-manager profile show \
    --name <profile_name> \
    --resource-group myResourceGroup \
    --query dnsConfig.fqdn

```

Copie o valor **relativodnsnome.** O nome DNS do seu perfil do Traffic Manager é *http://<* nome de *>.trafficmanager.net*. 

### <a name="view-traffic-manager-in-action"></a>Ver o Gestor de Tráfego em ação
1. Num navegador web, introduza o nome DNS do seu perfil de Traffic Manager *(http://<* nome de *>.trafficmanager.net*) para visualizar o website predefinido da sua Web App.

    > [!NOTE]
    > Neste cenário de arranque rápido, todos os pedidos de rota para o ponto final primário. Está definido para a **Prioridade 1**.
2. Para ver o Traffic Manager falhar em ação, desative o seu site principal utilizando a [atualização do gestor de pontofinal](https://docs.microsoft.com/cli/azure/network/traffic-manager/endpoint?view=azure-cli-latest#az-network-traffic-manager-endpoint-update)do gestor de tráfego da rede Az .

   ```azurecli-interactive

    az network traffic-manager endpoint update \
        --name <app1name_eastus> \
        --resource-group myResourceGroup \
        --profile-name <profile_name> \
        --type azureEndpoints \
        --endpoint-status Disabled
    
   ```

3. Copie o nome DNS do seu perfil de Traffic Manager *(http://<* nome de *>.trafficmanager.net*) para ver o website numa nova sessão de navegador web.
4. Verifique se a aplicação da web ainda está disponível.

## <a name="clean-up-resources"></a>Limpar recursos

Quando terminar, elimine os grupos de recursos, aplicações web e todos os recursos relacionados usando a eliminação do [grupo Az](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-delete).

```azurecli-interactive

az group delete \
    --resource-group myResourceGroup

```

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, criou um perfil de Gestor de Tráfego que proporciona uma elevada disponibilidade para a sua aplicação web. Para saber mais sobre o tráfego de encaminhamento, continue para os tutoriais do Gestor de Tráfego.

> [!div class="nextstepaction"]
> [Traffic Manager tutorials](tutorial-traffic-manager-improve-website-response.md) (Tutoriais do Gestor de Tráfego)
