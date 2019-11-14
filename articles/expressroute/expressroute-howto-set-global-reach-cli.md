---
title: 'Azure ExpressRoute: configurar o ExpressRoute Alcance Global: CLI'
description: Este artigo ajuda-o a ligar os circuitos do ExpressRoute em conjunto para garantir uma privada, de rede entre as redes no local e ativar o alcance Global.
services: expressroute
author: jaredr80
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/12/2018
ms.author: jaredro
ms.openlocfilehash: eda0011ea4d259d0e60cb894c2b42325ddfc2eb7
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74076623"
---
# <a name="configure-expressroute-global-reach-by-using-the-azure-cli"></a>Configurar Alcance Global do ExpressRoute usando o CLI do Azure

Este artigo ajuda você a configurar o Azure ExpressRoute Alcance Global usando o CLI do Azure. Para obter mais informações, veja [Alcance Global do ExpressRoute](expressroute-global-reach.md).
 
Antes de iniciar a configuração, conclua os seguintes requisitos:

* Instale a versão mais recente do CLI do Azure. Veja [Instalar a CLI do Azure](/cli/azure/install-azure-cli) e [Introdução à CLI do Azure](/cli/azure/get-started-with-azure-cli).
* Entenda os [fluxos de trabalho](expressroute-workflows.md)de provisionamento de circuito do ExpressRoute.
* Verifique se os circuitos do ExpressRoute estão no estado provisionado.
* Certifique-se de peering privado do Azure está configurado em seus circuitos do ExpressRoute.  

### <a name="sign-in-to-your-azure-account"></a>Inicie sessão na sua conta do Azure

Para iniciar a configuração, entre em sua conta do Azure. O comando a seguir abre o navegador padrão e solicita as credenciais de entrada para sua conta do Azure:  

```azurecli
az login
```

Se você tiver várias assinaturas do Azure, verifique as assinaturas da conta:

```azurecli
az account list
```

Especifique a assinatura que você deseja usar:

```azurecli
az account set --subscription <your subscription ID>
```

### <a name="identify-your-expressroute-circuits-for-configuration"></a>Identificar os circuitos do ExpressRoute para configuração

Você pode habilitar o ExpressRoute Alcance Global entre dois circuitos do ExpressRoute, desde que eles estejam localizados em países/regiões com suporte e criados em diferentes locais de emparelhamento. Se sua assinatura possuir ambos os circuitos, você poderá escolher um dos circuitos para executar a configuração, conforme explicado posteriormente neste artigo. Se os dois circuitos estiverem em assinaturas diferentes do Azure, você deverá ter autorização de uma assinatura do Azure e deverá passar sua chave de autorização quando você executar o comando de configuração na outra assinatura do Azure.

## <a name="enable-connectivity-between-your-on-premises-networks"></a>Ativar a conectividade entre as redes no local

Ao executar o comando para habilitar a conectividade, observe os seguintes requisitos para os valores de parâmetro:

* o *circuito de mesmo nível* deve ser a ID de recurso completa. Por exemplo:

  > /subscriptions/{your_subscription_id}/resourceGroups/{your_resource_group}/providers/Microsoft.Network/expressRouteCircuits/{your_circuit_name}

* o *prefixo de endereço* deve ser uma sub-rede IPv4 "/29" (por exemplo, "10.0.0.0/29"). Usamos endereços IP nessa sub-rede para estabelecer a conectividade entre os dois circuitos do ExpressRoute. Você não deve usar endereços nessa sub-rede em suas redes virtuais do Azure ou em suas redes locais.

Execute o seguinte comando da CLI para conectar dois circuitos do ExpressRoute:

```azurecli
az network express-route peering connection create -g <ResourceGroupName> --circuit-name <Circuit1Name> --peering-name AzurePrivatePeering -n <ConnectionName> --peer-circuit <Circuit2ResourceID> --address-prefix <__.__.__.__/29>
```

A saída da CLI tem esta aparência:

```azurecli
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

Quando essa operação for concluída, você terá conectividade entre suas redes locais em ambos os lados através dos dois circuitos do ExpressRoute.

## <a name="enable-connectivity-between-expressroute-circuits-in-different-azure-subscriptions"></a>Habilitar a conectividade entre circuitos do ExpressRoute em diferentes assinaturas do Azure

Se os dois circuitos não estiverem na mesma assinatura do Azure, você precisará de autorização. Na configuração a seguir, você gera autorização na assinatura do circuito 2 e passa a chave de autorização para o circuito 1.

1. Gerar uma chave de autorização:

   ```azurecli
   az network express-route auth create --circuit-name <Circuit2Name> -g <Circuit2ResourceGroupName> -n <AuthorizationName>
   ```

   A saída da CLI tem esta aparência:

   ```azurecli
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

1. Anote a ID do recurso e a chave de autorização para o circuito 2.

1. Execute o seguinte comando no circuito 1, passando a ID de recurso e a chave de autorização do circuito 2:

   ```azurecli
   az network express-route peering connection create -g <ResourceGroupName> --circuit-name <Circuit1Name> --peering-name AzurePrivatePeering -n <ConnectionName> --peer-circuit <Circuit2ResourceID> --address-prefix <__.__.__.__/29> --authorization-key <authorizationKey>
   ```

Quando essa operação for concluída, você terá conectividade entre suas redes locais em ambos os lados através dos dois circuitos do ExpressRoute.

## <a name="get-and-verify-the-configuration"></a>Obter e verificar a configuração

Use o comando a seguir para verificar a configuração no circuito em que a configuração foi feita (circuito 1 no exemplo anterior):

```azurecli
az network express-route show -n <CircuitName> -g <ResourceGroupName>
```

Na saída da CLI, você verá *CircuitConnectionStatus*. Ele informa se a conectividade entre os dois circuitos é estabelecida ("conectada") ou não estabelecida ("desconectado"). 

## <a name="disable-connectivity-between-your-on-premises-networks"></a>Desativar a conectividade entre as redes no local

Para desabilitar a conectividade, execute o seguinte comando no circuito em que a configuração foi feita (circuito 1 no exemplo anterior).

```azurecli
az network express-route peering connection delete -g <ResourceGroupName> --circuit-name <Circuit1Name> --peering-name AzurePrivatePeering -n <ConnectionName>
```

Use o comando ```show``` para verificar o status.

Quando essa operação for concluída, você não terá mais conectividade entre suas redes locais por meio de circuitos do ExpressRoute.

## <a name="next-steps"></a>Passos seguintes

* [Saiba mais sobre o alcance Global do ExpressRoute](expressroute-global-reach.md)
* [Verificar a conectividade do ExpressRoute](expressroute-troubleshooting-expressroute-overview.md)
* [Vincular um circuito do ExpressRoute a uma rede virtual](expressroute-howto-linkvnet-arm.md)
