---
title: 'Azure ExpressRoute: Configure ExpressRoute Direct: CLI'
description: Saiba como utilizar o Azure CLI para configurar o Azure ExpressRoute Direct para ligar diretamente à rede global da Microsoft.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 12/14/2020
ms.author: duau
ms.custom: devx-track-azurecli
ms.openlocfilehash: 9f35a698510f8637c3fe66528e6d64e5cd87b693
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/07/2021
ms.locfileid: "106553826"
---
# <a name="configure-expressroute-direct-by-using-the-azure-cli"></a>Configure ExpressRoute Direct usando o Azure CLI

O ExpressRoute Direct dá-lhe a capacidade de se conectar diretamente à rede global da Microsoft através de localizações de observação estrategicamente distribuídas em todo o mundo. Para mais informações, consulte [Sobre o ExpressRoute Direct Connect](expressroute-erdirect-about.md).

## <a name="before-you-begin"></a>Antes de começar

Antes de utilizar o ExpressRoute Direct, tem primeiro de inscrever a sua subscrição. Antes de utilizar o ExpressRoute Direct, tem primeiro de inscrever a sua subscrição. Para se inscrever, por favor faça o seguinte através da Azure PowerShell:
1.  Faça o súmis no Azure e selecione a subscrição que deseja inscrever.

    ```azurepowershell-interactive
    Connect-AzAccount 

    Select-AzSubscription -Subscription "<SubscriptionID or SubscriptionName>"
    ```

2. Registe a sua subscrição para Visualização Pública utilizando o seguinte comando:
    ```azurepowershell-interactive
    Register-AzProviderFeature -FeatureName AllowExpressRoutePorts -ProviderNamespace Microsoft.Network
    ```

Uma vez inscrito, verifique se o fornecedor de recursos **Microsoft.Network** está registado na sua subscrição. O registo de um fornecedor de recursos configura a sua subscrição para trabalhar com o fornecedor de recursos.

## <a name="create-the-resource"></a><a name="resources"></a>Criar o recurso

1. Inscreva-se no Azure e selecione a subscrição que contém ExpressRoute. O recurso ExpressRoute Direct e os seus circuitos ExpressRoute devem estar na mesma subscrição. No Azure CLI, executar os seguintes comandos:

   ```azurecli
   az login
   ```

   Verifique as subscrições da conta: 

   ```azurecli
   az account list 
   ```

   Selecione a subscrição para a qual pretende criar um circuito ExpressRoute:

   ```azurecli
   az account set --subscription "<subscription ID>"
   ```

2. Re-registrar a sua subscrição para Microsoft.Network para aceder à viadutportslocalização e expressrouteport APIs

   ```azurecli
   az provider register --namespace Microsoft.Network
   ```
3. Listar todos os locais onde o ExpressRoute Direct é suportado:
    
   ```azurecli
   az network express-route port location list
   ```

   **Saída de exemplo**
  
   ```output
   [
   {
    "address": "21715 Filigree Court, DC2, Building F, Ashburn, VA 20147",
    "availableBandwidths": [],
    "contact": "support@equinix.com",
    "id": "/subscriptions/<subscriptionID>/providers/Microsoft.Network/expressRoutePortsLocations/Equinix-Ashburn-DC2",
    "location": null,
    "name": "Equinix-Ashburn-DC2",
    "provisioningState": "Succeeded",
    "tags": null,
    "type": "Microsoft.Network/expressRoutePortsLocations"
   },
   {
    "address": "1950 N. Stemmons Freeway, Suite 1039A, DA3, Dallas, TX 75207",
    "availableBandwidths": [],
    "contact": "support@equinix.com",
    "id": "/subscriptions/<subscriptionID>/providers/Microsoft.Network/expressRoutePortsLocations/Equinix-Dallas-DA3",
    "location": null,
    "name": "Equinix-Dallas-DA3",
    "provisioningState": "Succeeded",
    "tags": null,
    "type": "Microsoft.Network/expressRoutePortsLocations"
   },
   {
    "address": "111 8th Avenue, New York, NY 10011",
    "availableBandwidths": [],
    "contact": "support@equinix.com",
    "id": "/subscriptions/<subscriptionID>/providers/Microsoft.Network/expressRoutePortsLocations/Equinix-New-York-NY5",
    "location": null,
    "name": "Equinix-New-York-NY5",
    "provisioningState": "Succeeded",
    "tags": null,
    "type": "Microsoft.Network/expressRoutePortsLocations"
   },
   {
    "address": "11 Great Oaks Blvd, SV1, San Jose, CA 95119",
    "availableBandwidths": [],
    "contact": "support@equinix.com",
    "id": "/subscriptions/<subscriptionID>/providers/Microsoft.Network/expressRoutePortsLocations/Equinix-San-Jose-SV1",
    "location": null,
    "name": "Equinix-San-Jose-SV1",
    "provisioningState": "Succeeded",
    "tags": null,
    "type": "Microsoft.Network/expressRoutePortsLocations"
   },
   {
    "address": "2001 Sixth Ave., Suite 350, SE2, Seattle, WA 98121",
    "availableBandwidths": [],
    "contact": "support@equinix.com",
    "id": "/subscriptions/<subscriptionID>/providers/Microsoft.Network/expressRoutePortsLocations/Equinix-Seattle-SE2",
    "location": null,
    "name": "Equinix-Seattle-SE2",
    "provisioningState": "Succeeded",
    "tags": null,
    "type": "Microsoft.Network/expressRoutePortsLocations"
   }
   ]
   ```
4. Determinar se uma das localizações listadas no passo anterior tem largura de banda disponível:

   ```azurecli
   az network express-route port location show -l "Equinix-Ashburn-DC2"
   ```

   **Saída de exemplo**

   ```output
   {
   "address": "21715 Filigree Court, DC2, Building F, Ashburn, VA 20147",
   "availableBandwidths": [
    {
      "offerName": "100 Gbps",
      "valueInGbps": 100
    }
   ],
   "contact": "support@equinix.com",
   "id": "/subscriptions/<subscriptionID>/providers/Microsoft.Network/expressRoutePortsLocations/Equinix-Ashburn-DC2",
   "location": null,
   "name": "Equinix-Ashburn-DC2",
   "provisioningState": "Succeeded",
   "tags": null,
   "type": "Microsoft.Network/expressRoutePortsLocations"
   }
   ```
5. Crie um recurso ExpressRoute Direct baseado na localização que escolheu nos passos anteriores.

   O ExpressRoute Direct suporta a encapsulação QinQ e Dot1Q. Se selecionar o QinQ, cada circuito ExpressRoute é atribuído dinamicamente a uma S-Tag e é único em todo o recurso ExpressRoute Direct. Cada C-Tag no circuito deve ser único no circuito, mas não através do recurso ExpressRoute Direct.  

   Se selecionar o encapsulamento do Dot1Q, tem de gerir a singularidade da C-Tag (VLAN) em todo o recurso ExpressRoute Direct.  

   > [!IMPORTANT]
   > ExpressRoute Direct pode ser apenas um tipo de encapsulamento. Não é possível alterar o tipo de encapsulamento depois de criar o recurso ExpressRoute Direct.
   > 
 
   ```azurecli
   az network express-route port create -n $name -g $RGName --bandwidth 100 gbps  --encapsulation QinQ | Dot1Q --peering-location $PeeringLocationName -l $AzureRegion 
   ```

   > [!NOTE]
   > Também pode definir o atributo **encapsulamento** para **Dot1Q**. 
   >

   **Saída de exemplo**

   ```output
   {
   "allocationDate": "Wednesday, October 17, 2018",
   "bandwidthInGbps": 100,
   "circuits": null,
   "encapsulation": "Dot1Q",
   "etag": "W/\"<etagnumber>\"",
   "etherType": "0x8100",
   "id": "/subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.Network/expressRoutePorts/Contoso-Direct",
   "links": [
    {
      "adminState": "Disabled",
      "connectorType": "LC",
      "etag": "W/\"<etagnumber>\"",
      "id": "/subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.Network/expressRoutePorts/Contoso-Direct/links/link1",
      "interfaceName": "HundredGigE2/2/2",
      "name": "link1",
      "patchPanelId": "PPID",
      "provisioningState": "Succeeded",
      "rackId": "RackID",
      "resourceGroup": "Contoso-Direct-rg",
      "routerName": "tst-09xgmr-cis-1",
      "type": "Microsoft.Network/expressRoutePorts/links"
    },
    {
      "adminState": "Disabled",
      "connectorType": "LC",
      "etag": "W/\"<etagnumber>\"",
      "id": "/subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.Network/expressRoutePorts/Contoso-Direct/links/link2",
      "interfaceName": "HundredGigE2/2/2",
      "name": "link2",
      "patchPanelId": "PPID",
      "provisioningState": "Succeeded",
      "rackId": "RackID",
      "resourceGroup": "Contoso-Direct-rg",
      "routerName": "tst-09xgmr-cis-2",
      "type": "Microsoft.Network/expressRoutePorts/links"
    }
   ],
   "location": "westus",
   "mtu": "1500",
   "name": "Contoso-Direct",
   "peeringLocation": "Equinix-Ashburn-DC2",
   "provisionedBandwidthInGbps": 0.0,
   "provisioningState": "Succeeded",
   "resourceGroup": "Contoso-Direct-rg",
   "resourceGuid": "02ee21fe-4223-4942-a6bc-8d81daabc94f",
   "tags": null,
   "type": "Microsoft.Network/expressRoutePorts"
   }  
   ```

## <a name="generate-the-letter-of-authorization-loa"></a><a name="resources"></a>Gerar a Carta de Autorização (LOA)

Insira o nome de recurso ExpressRoute Direct recentemente criado, nome de grupo de recursos e um nome de cliente para escrever o LOA e (opcionalmente) definir um local de arquivo para armazenar o documento. Se um caminho de ficheiro não for referenciado, o documento será transferido para o diretório atual.

```azurecli
az network express-route port generate-loa -n Contoso-Direct -g Contoso-Direct-rg --customer-name Contoso --destination C:\Users\SampleUser\Downloads\LOA.pdf
```

## <a name="change-adminstate-for-links"></a><a name="state"></a>Alterar AdminState para links

Utilize este processo para realizar um teste de camada 1. Certifique-se de que cada ligação cruzada está corretamente remendada em cada router nas portas primárias e secundárias.

1. Definir links para **Ativado**. Repita este passo para definir cada ligação para **Ativado**.

   Links[0] é a porta primária e links[1] é a porta secundária.

   ```azurecli
   az network express-route port update -n Contoso-Direct -g Contoso-Direct-rg --set links[0].adminState="Enabled"
   ```
   ```azurecli
   az network express-route port update -n Contoso-Direct -g Contoso-Direct-rg --set links[1].adminState="Enabled"
   ```
   **Saída de exemplo**

   ```output
   {
   "allocationDate": "Wednesday, October 17, 2018",
   "bandwidthInGbps": 100,
   "circuits": null,
   "encapsulation": "Dot1Q",
   "etag": "W/\"<etagnumber>\"",
   "etherType": "0x8100",
   "id": "/subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.Network/expressRoutePorts/Contoso-Direct",
   "links": [
    {
      "adminState": "Enabled",
      "connectorType": "LC",
      "etag": "W/\"<etagnumber>\"",
      "id": "/subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.Network/expressRoutePorts/Contoso-Direct/links/link1",
      "interfaceName": "HundredGigE2/2/2",
      "name": "link1",
      "patchPanelId": "PPID",
      "provisioningState": "Succeeded",
      "rackId": "RackID",
      "resourceGroup": "Contoso-Direct-rg",
      "routerName": "tst-09xgmr-cis-1",
      "type": "Microsoft.Network/expressRoutePorts/links"
    },
    {
      "adminState": "Enabled",
      "connectorType": "LC",
      "etag": "W/\"<etagnumber>\"",
      "id": "/subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.Network/expressRoutePorts/Contoso-Direct/links/link2",
      "interfaceName": "HundredGigE2/2/2",
      "name": "link2",
      "patchPanelId": "PPID",
      "provisioningState": "Succeeded",
      "rackId": "RackID",
      "resourceGroup": "Contoso-Direct-rg",
      "routerName": "tst-09xgmr-cis-2",
      "type": "Microsoft.Network/expressRoutePorts/links"
    }
   ],
   "location": "westus",
   "mtu": "1500",
   "name": "Contoso-Direct",
   "peeringLocation": "Equinix-Ashburn-DC2",
   "provisionedBandwidthInGbps": 0.0,
   "provisioningState": "Succeeded",
   "resourceGroup": "Contoso-Direct-rg",
   "resourceGuid": "<resourceGUID>",
   "tags": null,
   "type": "Microsoft.Network/expressRoutePorts"
   }
   ```

   Utilize o mesmo procedimento para descer as portas utilizando `AdminState = "Disabled"` .

## <a name="create-a-circuit"></a><a name="circuit"></a>Criar um circuito

Por predefinição, pode criar 10 circuitos na subscrição que contém o recurso ExpressRoute Direct. O Microsoft Support pode aumentar o limite por defeito. É responsável por rastrear a largura de banda astecada e utilizada. Largura de banda aprovisionada é a soma da largura de banda de todos os circuitos do recurso ExpressRoute Direct. A largura de banda utilizada é o uso físico das interfaces físicas subjacentes.

Pode utilizar larguras de banda de circuitos adicionais no ExpressRoute Direct apenas para suportar os cenários descritos aqui. As larguras de banda são de 40 Gbps e 100 Gbps.

**SkuTier** pode ser Local, Standard ou Premium.

**SkuFamily** só pode ser MedidodData. Ilimitado não é suportado no ExpressRoute Direct.

Criar um circuito no recurso ExpressRoute Direct:

  ```azurecli
  az network express-route create --express-route-port "/subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.Network/expressRoutePorts/Contoso-Direct" -n "Contoso-Direct-ckt" -g "Contoso-Direct-rg" --sku-family MeteredData --sku-tier Standard --bandwidth 100 Gbps
  ```

  Outras larguras de banda incluem 5 Gbps, 10 Gbps e 40 Gbps.

  **Saída de exemplo**

  ```output
  {
  "allowClassicOperations": false,
  "allowGlobalReach": false,
  "authorizations": [],
  "bandwidthInGbps": 100.0,
  "circuitProvisioningState": "Enabled",
  "etag": "W/\"<etagnumber>\"",
  "expressRoutePort": {
    "id": "/subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.Network/expressRoutePorts/Contoso-Direct",
    "resourceGroup": "Contoso-Direct-rg"
  },
  "gatewayManagerEtag": "",
  "id": "/subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.Network/expressRouteCircuits/ERDirect-ckt-cli",
  "location": "westus",
  "name": "ERDirect-ckt-cli",
  "peerings": [],
  "provisioningState": "Succeeded",
  "resourceGroup": "Contoso-Direct-rg",
  "serviceKey": "<serviceKey>",
  "serviceProviderNotes": null,
  "serviceProviderProperties": null,
  "serviceProviderProvisioningState": "Provisioned",
  "sku": {
    "family": "MeteredData",
    "name": "Standard_MeteredData",
    "tier": "Standard"
  },
  "stag": null,
  "tags": null,
  "type": "Microsoft.Network/expressRouteCircuits"
  }  
  ```

## <a name="next-steps"></a>Passos seguintes

Para mais informações sobre o ExpressRoute Direct, consulte a [visão geral.](expressroute-erdirect-about.md)
