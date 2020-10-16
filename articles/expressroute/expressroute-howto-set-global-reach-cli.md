---
title: 'Azure ExpressRoute: Configure ExpressRoute Global Reach: CLI'
description: Saiba como ligar os circuitos ExpressRoute em conjunto para fazer uma rede privada entre as suas redes no local e ativar o Global Reach utilizando o Azure CLI.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 12/12/2018
ms.author: duau
ms.custom: devx-track-azurecli
ms.openlocfilehash: 16a86982813b667ed5c761da27c8e9e5a43ab6cc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91322500"
---
# <a name="configure-expressroute-global-reach-by-using-the-azure-cli"></a>Configure ExpressRoute Global Reach usando o Azure CLI

Este artigo ajuda-o a configurar o Azure ExpressRoute Global Reach utilizando o Azure CLI. Para obter mais informações, veja [Alcance Global do ExpressRoute](expressroute-global-reach.md).
 
Antes de iniciar a configuração, preencha os seguintes requisitos:

* Instale a versão mais recente do Azure CLI. Veja [Instalar a CLI do Azure](/cli/azure/install-azure-cli) e [Introdução à CLI do Azure](/cli/azure/get-started-with-azure-cli).
* Compreenda os fluxos de trabalho de fornecimento de [circuitos](expressroute-workflows.md)ExpressRoute .
* Certifique-se de que os seus circuitos ExpressRoute estão no estado de Provisionamento.
* Certifique-se de que o espreitamento privado do Azure está configurado nos seus circuitos ExpressRoute.  

### <a name="sign-in-to-your-azure-account"></a>Inicie sessão na sua conta do Azure

Para iniciar a configuração, inicie sação na sua conta Azure. O seguinte comando abre o seu navegador predefinido e solicita-lhe as credenciais de inscrição para a sua conta Azure:  

```azurecli
az login
```

Se tiver várias subscrições do Azure, verifique as subscrições da conta:

```azurecli
az account list
```

Especificar a subscrição que pretende utilizar:

```azurecli
az account set --subscription <your subscription ID>
```

### <a name="identify-your-expressroute-circuits-for-configuration"></a>Identifique os seus circuitos ExpressRoute para configuração

Você pode ativar ExpressRoute Global Reach entre dois circuitos ExpressRoute, desde que estejam localizados em países/regiões apoiados e foram criados em diferentes locais de observação. Se a sua subscrição possuir ambos os circuitos, pode escolher qualquer circuito para executar a configuração, como explicado mais tarde neste artigo. Se os dois circuitos estiverem em diferentes subscrições do Azure, tem de ter autorização de uma subscrição do Azure e deve passar na sua chave de autorização quando executar o comando de configuração na outra subscrição do Azure.

## <a name="enable-connectivity-between-your-on-premises-networks"></a>Permitir a conectividade entre as suas redes no local

Ao executar o comando para permitir a conectividade, note os seguintes requisitos para valores de parâmetros:

* *circuito par* deve ser o ID de recursos completo. Por exemplo:

  > /subscrições/{your_subscription_id}/resourceGroups/{your_resource_group}/providers/Microsoft.Network/expressRouteCircuits/{your_circuit_name}

* *o prefixo de endereço* deve ser uma sub-rede IPv4 "/29" (por exemplo, "10.0.0.0.0/29"). Utilizamos endereços IP nesta sub-rede para estabelecer conectividade entre os dois circuitos ExpressRoute. Não deve utilizar endereços nesta sub-rede nas suas redes virtuais Azure ou nas suas redes no local.

Executar o seguinte comando CLI para ligar dois circuitos ExpressRoute:

```azurecli
az network express-route peering connection create -g <ResourceGroupName> --circuit-name <Circuit1Name> --peering-name AzurePrivatePeering -n <ConnectionName> --peer-circuit <Circuit2ResourceID> --address-prefix <__.__.__.__/29>
```

A saída do CLI é assim:

```output
{
  "addressPrefix": "<__.__.__.__/29>",
  "authorizationKey": null,
  "circuitConnectionStatus": "Connected",
  "etag": "W/\"48d682f9-c232-4151-a09f-fab7cb56369a\"",
  "expressRouteCircuitPeering": {
    "id": "/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/expressRouteCircuits/<Circuit1Name>/peerings/AzurePrivatePeering",
    "resourceGroup": "<ResourceGroupName>"
  },
  "id": "/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/expressRouteCircuits/<Circuit1Name>/peerings/AzurePrivatePeering/connections/<ConnectionName>",
  "name": "<ConnectionName>",
  "peerExpressRouteCircuitPeering": {
    "id": "/subscriptions/<SubscriptionID>/resourceGroups/<Circuit2ResourceGroupName>/providers/Microsoft.Network/expressRouteCircuits/<Circuit2Name>/peerings/AzurePrivatePeering",
    "resourceGroup": "<Circuit2ResourceGroupName>"
  },
  "provisioningState": "Succeeded",
  "resourceGroup": "<ResourceGroupName>",
  "type": "Microsoft.Network/expressRouteCircuits/peerings/connections"
}
```

Quando esta operação estiver concluída, terá conectividade entre as suas redes no local de ambos os lados através dos seus dois circuitos ExpressRoute.

## <a name="enable-connectivity-between-expressroute-circuits-in-different-azure-subscriptions"></a>Permitir a conectividade entre circuitos ExpressRoute em diferentes subscrições Azure

Se os dois circuitos não estiverem na mesma assinatura Azure, precisa de autorização. Na configuração seguinte, gera autorização na subscrição do circuito 2 e passa a chave de autorização para o circuito 1.

1. Gerar uma chave de autorização:

   ```azurecli
   az network express-route auth create --circuit-name <Circuit2Name> -g <Circuit2ResourceGroupName> -n <AuthorizationName>
   ```

   A saída do CLI é assim:

   ```output
   {
     "authorizationKey": "<authorizationKey>",
     "authorizationUseStatus": "Available",
     "etag": "W/\"cfd15a2f-43a1-4361-9403-6a0be00746ed\"",
     "id": "/subscriptions/<SubscriptionID>/resourceGroups/<Circuit2ResourceGroupName>/providers/Microsoft.Network/expressRouteCircuits/<Circuit2Name>/authorizations/<AuthorizationName>",
     "name": "<AuthorizationName>",
     "provisioningState": "Succeeded",
     "resourceGroup": "<Circuit2ResourceGroupName>",
     "type": "Microsoft.Network/expressRouteCircuits/authorizations"
   }
   ```

1. Tome nota tanto do ID do recurso como da chave de autorização do circuito 2.

1. Executar o seguinte comando contra o circuito 1, passando na chave de iD de recursos do circuito 2 e chave de autorização:

   ```azurecli
   az network express-route peering connection create -g <ResourceGroupName> --circuit-name <Circuit1Name> --peering-name AzurePrivatePeering -n <ConnectionName> --peer-circuit <Circuit2ResourceID> --address-prefix <__.__.__.__/29> --authorization-key <authorizationKey>
   ```

Quando esta operação estiver concluída, terá conectividade entre as suas redes no local de ambos os lados através dos seus dois circuitos ExpressRoute.

## <a name="get-and-verify-the-configuration"></a>Obter e verificar a configuração

Utilize o seguinte comando para verificar a configuração do circuito onde a configuração foi feita (circuito 1 no exemplo anterior):

```azurecli
az network express-route show -n <CircuitName> -g <ResourceGroupName>
```

Na saída CLI, verá *CircuitConnectionStatus*. Diz-lhe se a conectividade entre os dois circuitos está estabelecida ("Conectado") ou não estabelecido ("Desligado"). 

## <a name="disable-connectivity-between-your-on-premises-networks"></a>Desativar a conectividade entre as suas redes no local

Para desativar a conectividade, corram o seguinte comando contra o circuito onde a configuração foi feita (circuito 1 no exemplo anterior).

```azurecli
az network express-route peering connection delete -g <ResourceGroupName> --circuit-name <Circuit1Name> --peering-name AzurePrivatePeering -n <ConnectionName>
```

Utilize o ```show``` comando para verificar o estado.

Quando esta operação estiver concluída, deixará de ter conectividade entre as suas redes no local através dos circuitos ExpressRoute.

## <a name="next-steps"></a>Passos seguintes

* [Saiba mais sobre o ExpressRoute Global Reach](expressroute-global-reach.md)
* [Verificar conectividade ExpressRoute](expressroute-troubleshooting-expressroute-overview.md)
* [Ligue um circuito ExpressRoute a uma rede virtual](expressroute-howto-linkvnet-arm.md)
