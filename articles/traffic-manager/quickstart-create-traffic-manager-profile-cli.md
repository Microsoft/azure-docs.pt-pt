---
title: 'Início rápido: criar um perfil para HA de aplicativos-CLI do Azure-Gerenciador de tráfego do Azure'
description: Este artigo de início rápido descreve como criar um perfil do Gerenciador de tráfego para criar um aplicativo Web altamente disponível.
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
# <a name="quickstart-create-a-traffic-manager-profile-for-a-highly-available-web-application-using-azure-cli"></a>Início rápido: criar um perfil do Gerenciador de tráfego para um aplicativo Web altamente disponível usando CLI do Azure

Este guia de início rápido descreve como criar um perfil do Gerenciador de tráfego que fornece alta disponibilidade para seu aplicativo Web.

Neste guia de início rápido, você criará duas instâncias de um aplicativo Web. Cada um deles está sendo executado em uma região diferente do Azure. Você criará um perfil do Gerenciador de tráfego com base na [prioridade do ponto de extremidade](traffic-manager-routing-methods.md#priority-traffic-routing-method). O perfil direciona o tráfego do usuário para o site primário que está executando o aplicativo Web. O Gerenciador de tráfego monitora continuamente o aplicativo Web. Se o site primário não estiver disponível, ele fornecerá o failover automático para o site de backup.

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

Crie um perfil do Gerenciador de tráfego usando [AZ Network Traffic-Manager Profile Create](https://docs.microsoft.com/cli/azure/network/traffic-manager/profile?view=azure-cli-latest#az-network-traffic-manager-profile-create) que direciona o tráfego do usuário com base na prioridade do ponto de extremidade.

No exemplo a seguir, substitua **< profile_name >** com um nome de perfil exclusivo do Gerenciador de tráfego.

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

## <a name="create-web-apps"></a>Criar aplicativos Web

Para este guia de início rápido, você precisará de duas instâncias de um aplicativo Web implantado em duas regiões diferentes do Azure (*leste dos EUA* e *Europa Ocidental*). Cada um servirá como pontos de extremidade primários e de failover para o Gerenciador de tráfego.

### <a name="create-web-app-service-plans"></a>Criar planos do serviço de aplicativo Web
Crie planos de serviço de aplicativo Web usando [AZ appservice Plan Create](https://docs.microsoft.com/cli/azure/appservice/plan?view=azure-cli-latest#az-appservice-plan-create) para as duas instâncias do aplicativo Web que serão implantadas em duas regiões diferentes do Azure.

No exemplo a seguir, substitua **< appspname_eastus >** e **< appspname_westeurope** > com um nome de plano do serviço de aplicativo exclusivo

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
### <a name="create-a-web-app-in-the-app-service-plan"></a>Criar um aplicativo Web no plano do serviço de aplicativo
Crie duas instâncias o aplicativo Web usando [AZ webapp Create](https://docs.microsoft.com/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) nos planos do serviço de aplicativo nas regiões *leste dos EUA* e *Europa Ocidental* Azure.

No exemplo a seguir, substitua **< app1name_eastus >** e **<** App2name_westeurope > com um nome de aplicativo exclusivo e substitua **<** appspname_eastus > e **<** appspname_westeurope > pelo nome usado para criar os planos do serviço de aplicativo na seção anterior.

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
Adicione os dois aplicativos Web como pontos de extremidade do Gerenciador de tráfego usando [AZ Network Traffic-Manager Endpoint Create](https://docs.microsoft.com/cli/azure/network/traffic-manager/endpoint?view=azure-cli-latest#az-network-traffic-manager-endpoint-create) para o perfil do Gerenciador de tráfego da seguinte maneira:

- Determine a ID do aplicativo Web e adicione o aplicativo Web localizado na região *leste dos EUA* do Azure como o ponto de extremidade primário para rotear todo o tráfego do usuário. 
- Determine a ID do aplicativo Web e adicione o aplicativo Web localizado na região *Europa Ocidental* Azure como o ponto de extremidade de failover. 

Quando o ponto de extremidade primário não está disponível, o tráfego roteia automaticamente para o ponto de extremidade de failover.

No exemplo a seguir, substitua **< app1name_eastus >** e **< app2name_westeurope** > com os nomes de aplicativo criados para cada região na seção anterior, substitua **<** appspname_eastus > e **<** appspname_westeurope > pelo nome usado para criar os planos do serviço de aplicativo na seção anterior e substitua **<** profile_name > pelo nome do perfil usado na seção anterior. 

**Ponto de extremidade leste dos EUA**

```azurecli-interactive

az webapp show \
    --name <app1name_eastus> \
    --resource-group myResourceGroup \
    --query id

```
Anote a ID exibida na saída e use no comando a seguir para adicionar o ponto de extremidade:

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

**Ponto de extremidade Europa Ocidental**

```azurecli-interactive

az webapp show \
    --name <app2name_westeurope> \
    --resource-group myResourceGroup \
    --query id

```
Anote a ID exibida na saída e use no comando a seguir para adicionar o ponto de extremidade:

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

## <a name="test-your-traffic-manager-profile"></a>Testar seu perfil do Gerenciador de tráfego

Nesta seção, você verificará o nome de domínio do seu perfil do Gerenciador de tráfego. Você também configurará o ponto de extremidade primário como indisponível. Por fim, você verá que o aplicativo Web ainda está disponível. É porque o Traffic Manager envia o tráfego para o ponto de extremidade de failover.

No exemplo a seguir, substitua **< app1name_eastus >** e **< app2name_westeurope** > com os nomes de aplicativo criados para cada região na seção anterior, substitua **<** appspname_eastus > e **<** appspname_westeurope > pelo nome usado para criar os planos do serviço de aplicativo na seção anterior e substitua **<** profile_name > pelo nome do perfil usado na seção anterior.

### <a name="determine-the-dns-name"></a>Determinar o nome DNS

Determine o nome DNS do perfil do Gerenciador de tráfego usando [AZ Network Traffic-Manager Profile show](https://docs.microsoft.com/cli/azure/network/traffic-manager/profile?view=azure-cli-latest#az-network-traffic-manager-profile-show).

```azurecli-interactive

az network traffic-manager profile show \
    --name <profile_name> \
    --resource-group myResourceGroup \
    --query dnsConfig.fqdn

```

Copie o valor **RelativeDnsName** . O nome DNS do seu perfil do Gerenciador de tráfego é *http://<* relativednsname *>. trafficmanager. net*. 

### <a name="view-traffic-manager-in-action"></a>Ver o Gestor de Tráfego em ação
1. Em um navegador da Web, insira o nome DNS do seu perfil do Gerenciador de tráfego (*http://<* relativednsname *>. trafficmanager. net*) para exibir o site padrão do seu aplicativo Web.

    > [!NOTE]
    > Neste cenário de início rápido, todas as solicitações são roteadas para o ponto de extremidade primário. Ele é definido como **prioridade 1**.
2. Para exibir o failover do Gerenciador de tráfego em ação, desabilite o site primário usando [AZ Network Traffic-Manager Endpoint Update](https://docs.microsoft.com/cli/azure/network/traffic-manager/endpoint?view=azure-cli-latest#az-network-traffic-manager-endpoint-update).

   ```azurecli-interactive

    az network traffic-manager endpoint update \
        --name <app1name_eastus> \
        --resource-group myResourceGroup \
        --profile-name <profile_name> \
        --type azureEndpoints \
        --endpoint-status Disabled
    
   ```

3. Copie o nome DNS do seu perfil do Gerenciador de tráfego (*http://<* relativednsname *>. trafficmanager. net*) para exibir o site em uma nova sessão do navegador da Web.
4. Verifique se o aplicativo Web ainda está disponível.

## <a name="clean-up-resources"></a>Limpar recursos

Quando terminar, exclua os grupos de recursos, aplicativos Web e todos os recursos relacionados usando [AZ Group Delete](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-delete).

```azurepowershell-interactive

az group delete \
    --resource-group myResourceGroup

```

## <a name="next-steps"></a>Passos Seguintes

Neste guia de início rápido, você criou um perfil do Gerenciador de tráfego que fornece alta disponibilidade para seu aplicativo Web. Para saber mais sobre o tráfego de roteamento, continue para os tutoriais do Gerenciador de tráfego.

> [!div class="nextstepaction"]
> [Traffic Manager tutorials](tutorial-traffic-manager-improve-website-response.md) (Tutoriais do Gestor de Tráfego)
