---
title: Quickstart:Create a profile for HA of applications - Azure CLI - Azure Traffic Manager
description: This quickstart article describes how to create a Traffic Manager profile to build a highly available web application.
services: traffic-manager
author: asudbring
mnager: twooley
Customer intent: As an IT admin, I want to direct user traffic to ensure high availability of web applications.
ms.service: traffic-manager
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/29/2019
ms.author: allensu
ms.openlocfilehash: b724a3e469c5dd8f7b4c4f30adef00c58c5c47c5
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2019
ms.locfileid: "74483913"
---
# <a name="quickstart-create-a-traffic-manager-profile-for-a-highly-available-web-application-using-azure-cli"></a>Quickstart: Create a Traffic Manager profile for a highly available web application using Azure CLI

This quickstart describes how to create a Traffic Manager profile that delivers high availability for your web application.

In this quickstart, you'll create two instances of a web application. Each of them is running in a different Azure region. You'll create a Traffic Manager profile based on [endpoint priority](traffic-manager-routing-methods.md#priority-traffic-routing-method). The profile directs user traffic to the primary site running the web application. Traffic Manager continuously monitors the web application. If the primary site is unavailable, it provides automatic failover to the backup site.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) agora.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar a CLI localmente, este tutorial requer que execute uma versão da CLI do Azure que seja a 2.0.28 ou posterior. Para localizar a versão, execute `az --version`. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Criar um grupo de recursos
Crie um grupo de recursos com [az group create](https://docs.microsoft.com/cli/azure/group). Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos.

O exemplo seguinte cria um grupo de recursos com o nome *myResourceGroup* na localização *eastus*:

```azurecli-interactive

  az group create \
    --name myResourceGroup \
    --location eastus

```

## <a name="create-a-traffic-manager-profile"></a>Criar um perfil do Gestor de Tráfego

Create a Traffic Manager profile using [az network traffic-manager profile create](https://docs.microsoft.com/cli/azure/network/traffic-manager/profile?view=azure-cli-latest#az-network-traffic-manager-profile-create) that directs user traffic based on endpoint priority.

In the following example, replace **<profile_name>** with a unique Traffic Manager profile name.

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

## <a name="create-web-apps"></a>Create web apps

For this quickstart, you'll need two instances of a web application deployed in two different Azure regions (*East US* and *West Europe*). Each will serve as primary and failover endpoints for Traffic Manager.

### <a name="create-web-app-service-plans"></a>Create web app service plans
Create web app service plans using [az appservice plan create](https://docs.microsoft.com/cli/azure/appservice/plan?view=azure-cli-latest#az-appservice-plan-create) for the two instances of the web application that you will deploy in two different Azure regions.

In the following example, replace **<appspname_eastus>** and **<appspname_westeurope>** with a unique App Service Plan Name

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
### <a name="create-a-web-app-in-the-app-service-plan"></a>Create a web app in the app service plan
Create two instances the web application using [az webapp create](https://docs.microsoft.com/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) in the App Service plans in the *East US* and *West Europe* Azure regions.

In the following example, replace **<app1name_eastus>** and **<app2name_westeurope>** with a unique App Name, and replace **<appspname_eastus>** and **<appspname_westeurope>** with the name used to create the App Service plans in the previous section.

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
Add the two Web Apps as Traffic Manager endpoints using [az network traffic-manager endpoint create](https://docs.microsoft.com/cli/azure/network/traffic-manager/endpoint?view=azure-cli-latest#az-network-traffic-manager-endpoint-create) to the Traffic Manager profile as follows:

- Determine the Web App id and add the Web App located in the *East US* Azure region as the primary endpoint to route all the user traffic. 
- Determinet the Web App id and add the Web App located in the *West Europe* Azure region as the failover endpoint. 

When the primary endpoint is unavailable, traffic automatically routes to the failover endpoint.

In the following example, replace **<app1name_eastus>** and **<app2name_westeurope>** with the App Names created for each region in the previous section, replace **<appspname_eastus>** and **<appspname_westeurope>** with the name used to create the App Service plans in the previous section, and replace **<profile_name>** with the profile name used in the previous section. 

**East US endpoint**

```azurecli-interactive

az webapp show \
    --name <app1name_eastus> \
    --resource-group myResourceGroup \
    --query id

```
Make note of id displayed in output and use in the following command to add the endpoint:

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

**West Europe endpoint**

```azurecli-interactive

az webapp show \
    --name <app2name_westeurope> \
    --resource-group myResourceGroup \
    --query id

```
Make note of id displayed in output and use in the following command to add the endpoint:

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

## <a name="test-your-traffic-manager-profile"></a>Test your Traffic Manager profile

In this section, you'll check the domain name of your Traffic Manager profile. You'll also configure the primary endpoint to be unavailable. Finally, you get to see that the web app is still available. It's because Traffic Manager sends the traffic to the failover endpoint.

In the following example, replace **<app1name_eastus>** and **<app2name_westeurope>** with the App Names created for each region in the previous section, replace **<appspname_eastus>** and **<appspname_westeurope>** with the name used to create the App Service plans in the previous section, and replace **<profile_name>** with the profile name used in the previous section.

### <a name="determine-the-dns-name"></a>Determinar o nome DNS

Determine the DNS name of the Traffic Manager profile using [az network traffic-manager profile show](https://docs.microsoft.com/cli/azure/network/traffic-manager/profile?view=azure-cli-latest#az-network-traffic-manager-profile-show).

```azurecli-interactive

az network traffic-manager profile show \
    --name <profile_name> \
    --resource-group myResourceGroup \
    --query dnsConfig.fqdn

```

Copy the **RelativeDnsName** value. The DNS name of your Traffic Manager profile is *http://<* relativednsname *>.trafficmanager.net*. 

### <a name="view-traffic-manager-in-action"></a>Ver o Gestor de Tráfego em ação
1. In a web browser, enter the DNS name of your Traffic Manager profile (*http://<* relativednsname *>.trafficmanager.net*) to view your Web App's default website.

    > [!NOTE]
    > In this quickstart scenario, all requests route to the primary endpoint. It is set to **Priority 1**.
2. To view Traffic Manager failover in action, disable your primary site using [az network traffic-manager endpoint update](https://docs.microsoft.com/cli/azure/network/traffic-manager/endpoint?view=azure-cli-latest#az-network-traffic-manager-endpoint-update).

   ```azurecli-interactive

    az network traffic-manager endpoint update \
        --name <app1name_eastus> \
        --resource-group myResourceGroup \
        --profile-name <profile_name> \
        --type azureEndpoints \
        --endpoint-status Disabled
    
   ```

3. Copy the DNS name of your Traffic Manager profile (*http://<* relativednsname *>.trafficmanager.net*) to view the website in a new web browser session.
4. Verify that the web app is still available.

## <a name="clean-up-resources"></a>Limpar recursos

When you're done, delete the resource groups, web applications, and all related resources using [az group delete](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-delete).

```azurepowershell-interactive

az group delete \
    --resource-group myResourceGroup

```

## <a name="next-steps"></a>Passos seguintes

In this quickstart, you created a Traffic Manager profile that provides high availability for your web application. To learn more about routing traffic, continue to the Traffic Manager tutorials.

> [!div class="nextstepaction"]
> [Traffic Manager tutorials](tutorial-traffic-manager-improve-website-response.md) (Tutoriais do Gestor de Tráfego)
