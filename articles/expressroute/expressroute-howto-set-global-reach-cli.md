---
title: 'Configure o alcance Global do ExpressRoute: CLI do Azure | Documentos da Microsoft'
description: Este artigo ajuda-o a ligar os circuitos do ExpressRoute em conjunto para garantir uma privada, de rede entre as redes no local e ativar o alcance Global.
services: expressroute
author: jaredr80
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/12/2018
ms.author: jaredro
ms.custom: seodec18
ms.openlocfilehash: 28df8f59944ccac9d731e15a558d864beed2f3ae
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56817699"
---
# <a name="configure-expressroute-global-reach-by-using-the-azure-cli"></a>Configurar o alcance Global do ExpressRoute com a CLI do Azure

Este artigo ajuda-o a configurar o alcance Global do Azure ExpressRoute com a CLI do Azure. Para obter mais informações, veja [Alcance Global do ExpressRoute](expressroute-global-reach.md).
 
Antes de iniciar a configuração, conclua os seguintes requisitos:

* Instale a versão mais recente da CLI do Azure. Veja [Instalar a CLI do Azure](/cli/azure/install-azure-cli) e [Introdução à CLI do Azure](/cli/azure/get-started-with-azure-cli).
* Compreender o ExpressRoute-aprovisionamento [fluxos de trabalho](expressroute-workflows.md).
* Certifique-se de que circuitos de ExpressRoute estão no Estado aprovisionado.
* Certifique-se de peering privado do Azure está configurado em seus circuitos do ExpressRoute.  

### <a name="sign-in-to-your-azure-account"></a>Inicie sessão na sua conta do Azure

Para iniciar a configuração, inicie sessão na sua conta do Azure. O seguinte comando abre o browser predefinido e pede-lhe as credenciais de início de sessão para a sua conta do Azure:  

```azurecli
az login
```

Se tiver várias subscrições do Azure, verifique as subscrições da conta:

```azurecli
az account list
```

Especifique a subscrição que pretende utilizar:

```azurecli
az account set --subscription <your subscription ID>
```

### <a name="identify-your-expressroute-circuits-for-configuration"></a>Identificar os circuitos do ExpressRoute para configuração

Pode ativar o alcance Global do ExpressRoute entre quaisquer dois circuitos do ExpressRoute, desde que eles estejam localizados no países com suporte e foram criados em diferentes localizações de peering. Se a sua subscrição é a proprietária de ambos os circuitos, pode escolher qualquer circuito para executar a configuração, conforme explicado neste artigo. Se dois circuitos estiverem em diferentes subscrições do Azure, deve ter a autorização de uma subscrição do Azure e têm de introduzir a chave de autorização ao executar o comando de configuração na subscrição do Azure.

## <a name="enable-connectivity-between-your-on-premises-networks"></a>Ativar a conectividade entre as redes no local

Ao executar o comando para ativar a conectividade, tenha em atenção os seguintes requisitos para valores de parâmetro:

* *circuito do elemento de rede* deve ser o ID de recurso completo. Por exemplo:

  > /subscriptions/{your_subscription_id}/resourceGroups/{your_resource_group}/providers/Microsoft.Network/expressRouteCircuits/{your_circuit_name}

* *prefixo de endereço* tem de ser uma sub-rede de IPv4 "/ 29" (por exemplo, "10.0.0.0/29"). Endereços IP são utilizadas nesta sub-rede para estabelecer a conectividade entre os dois circuitos do ExpressRoute. Não tem de utilizar endereços nesta sub-rede nas redes virtuais do Azure ou nas suas redes no local.

Execute o seguinte comando da CLI para ligar dois circuitos do ExpressRoute:

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

Quando esta operação estiver concluída, terá conectividade entre as redes no local em ambos os lados por meio de seus dois circuitos do ExpressRoute.

## <a name="enable-connectivity-between-expressroute-circuits-in-different-azure-subscriptions"></a>Ativar a conectividade entre os circuitos do ExpressRoute em diferentes subscrições do Azure

Se não forem os dois circuitos na mesma subscrição do Azure, terá de autorização. A seguinte configuração, gerar autorização na subscrição de circuito do 2 e passa a chave de autorização para o circuito do 1.

1. Gere uma chave de autorização:

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

1. Anote o ID de recurso e a chave de autorização para o circuito 2.

1. Execute o seguinte comando na circuito 1, passando na chave de ID e a autorização do recurso de circuito do 2:

   ```azurecli
   az network express-route peering connection create -g <ResourceGroupName> --circuit-name <Circuit1Name> --peering-name AzurePrivatePeering -n <ConnectionName> --peer-circuit <Circuit2ResourceID> --address-prefix <__.__.__.__/29> --authorization-key <authorizationKey>
   ```

Quando esta operação estiver concluída, terá conectividade entre as redes no local em ambos os lados por meio de seus dois circuitos do ExpressRoute.

## <a name="get-and-verify-the-configuration"></a>Obter e verificar a configuração

Utilize o seguinte comando para verificar a configuração no circuito onde a configuração foi efetuada (circuito 1 no exemplo anterior):

```azurecli
az network express-route show -n <CircuitName> -g <ResourceGroupName>
```

Na saída da CLI, verá *CircuitConnectionStatus*. Indica se a conectividade entre os dois circuitos é estabelecida ("ligado") ou não estabelecer ("desligado"). 

## <a name="disable-connectivity-between-your-on-premises-networks"></a>Desativar a conectividade entre as redes no local

Para desativar a conectividade, execute o seguinte comando contra o circuito onde a configuração foi efetuada (circuito 1 no exemplo anterior).

```azurecli
az network express-route peering connection delete -g <ResourceGroupName> --circuit-name <Circuit1Name> --peering-name AzurePrivatePeering -n <ConnectionName>
```

Utilize o ```show``` comando para verificar o estado.

Quando esta operação estiver concluída, já não terá conectividade entre as redes no local através de circuitos de ExpressRoute.

## <a name="next-steps"></a>Passos Seguintes

* [Saiba mais sobre o alcance Global do ExpressRoute](expressroute-global-reach.md)
* [Verificar a conectividade do ExpressRoute](expressroute-troubleshooting-expressroute-overview.md)
* [Ligar um circuito do ExpressRoute para uma rede virtual](expressroute-howto-linkvnet-arm.md)
