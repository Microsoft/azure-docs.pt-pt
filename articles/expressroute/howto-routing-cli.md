---
title: 'Como configurar o emparelhamento para um circuito-ExpresssRoute: CLI do Azure | Microsoft Docs'
description: Este artigo ajuda você a criar e provisionar o emparelhamento privado, público e da Microsoft de um circuito do ExpressRoute. Este artigo também mostra como verificar o estado, atualizar ou eliminar peerings no seu circuito.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: cherylmc
ms.custom: seodec18
ms.openlocfilehash: 8d6b361bf7e1b18c44719a8cdbe2e958ac63006d
ms.sourcegitcommit: c4700ac4ddbb0ecc2f10a6119a4631b13c6f946a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2019
ms.locfileid: "72965775"
---
# <a name="create-and-modify-peering-for-an-expressroute-circuit-using-cli"></a>Criar e modificar o emparelhamento para um circuito do ExpressRoute usando a CLI

Este artigo ajuda você a criar e gerenciar a configuração/emparelhamento de roteamento para um circuito do ExpressRoute no modelo de implantação do Gerenciador de recursos usando a CLI. Você também pode verificar o status, atualizar ou excluir e desprovisionar emparelhamentos para um circuito do ExpressRoute. Se você quiser usar um método diferente para trabalhar com o circuito, selecione um artigo na lista a seguir:

> [!div class="op_single_selector"]
> * [Portal do Azure](expressroute-howto-routing-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-routing-arm.md)
> * [CLI do Azure](howto-routing-cli.md)
> * [Vídeo-emparelhamento privado](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-private-peering-for-your-expressroute-circuit)
> * [Vídeo – emparelhamento público](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-public-peering-for-your-expressroute-circuit)
> * [Vídeo-emparelhamento da Microsoft](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-microsoft-peering-for-your-expressroute-circuit)
> * [PowerShell (clássico)](expressroute-howto-routing-classic.md)
> 

## <a name="configuration-prerequisites"></a>Pré-requisitos da configuração

* Antes de começar, instale a versão mais recente dos comandos da CLI (2.0 ou posterior). Para obter informações sobre como instalar os comandos da CLI, veja [Instalar a CLI do Azure](/cli/azure/install-azure-cli).
* Verifique se você examinou os [pré-requisitos](expressroute-prerequisites.md), [os requisitos de roteamento](expressroute-routing.md)e as páginas de [fluxo de trabalho](expressroute-workflows.md) antes de começar a configuração.
* Deve ter um circuito ExpressRoute ativo. Siga as instruções para [Criar um circuito ExpressRoute](howto-circuit-cli.md) e solicite ao seu fornecedor de conectividade para ativar o circuito antes de continuar. O circuito do ExpressRoute deve estar em um estado provisionado e habilitado para que você possa executar os comandos neste artigo.

Estas instruções aplicam-se apenas aos circuitos criados com fornecedores de serviços que fornecem serviços de conectividade de Camada 2. Se você estiver usando um provedor de serviços que oferece serviços gerenciados de camada 3 (normalmente um IPVPN, como o MPLS), seu provedor de conectividade configurará e gerenciará o roteamento para você.

Você pode configurar um, dois ou todos os três emparelhamentos (privado do Azure, público do Azure e Microsoft) para um circuito do ExpressRoute. Pode configurar peerings em qualquer ordem que escolha. No entanto, tem de confirmar que conclui a configuração de cada peering, um de cada vez. Para obter mais informações sobre domínios de roteamento e emparelhamentos, consulte [domínios de roteamento do ExpressRoute](expressroute-circuit-peerings.md).

## <a name="msft"></a>Emparelhamento da Microsoft

Esta seção ajuda você a criar, obter, atualizar e excluir a configuração de emparelhamento da Microsoft para um circuito do ExpressRoute.

> [!IMPORTANT]
> O emparelhamento da Microsoft de circuitos do ExpressRoute que foram configurados antes de 1º de agosto de 2017 terá todos os prefixos de serviço anunciados por meio do emparelhamento da Microsoft, mesmo que os filtros de rota não estejam definidos. O emparelhamento da Microsoft de circuitos do ExpressRoute que são configurados em ou após 1º de agosto de 2017 não terão prefixos anunciados até que um filtro de rota seja anexado ao circuito. Para obter mais informações, consulte [configurar um filtro de rota para o emparelhamento da Microsoft](how-to-routefilter-powershell.md).
> 
> 

### <a name="to-create-microsoft-peering"></a>Para criar peering da Microsoft

1. Instale a versão mais recente do CLI do Azure. Use a versão mais recente da CLI (interface de linha de comando) do Azure. * Examine os [pré-requisitos](expressroute-prerequisites.md) e os [fluxos de trabalho](expressroute-workflows.md) antes de começar a configuração.

   ```azurecli-interactive
   az login
   ```

   Selecione a assinatura para a qual você deseja criar o circuito de ExpressRoute.

   ```azurecli-interactive
   az account set --subscription "<subscription ID>"
   ```
2. Crie um circuito ExpressRoute. Siga as instruções para criar um [circuito ExpressRoute](howto-circuit-cli.md) e solicite ao fornecedor de conectividade que o aprovisione. Se seu provedor de conectividade oferece serviços gerenciados de camada 3, você pode solicitar que seu provedor de conectividade habilite o emparelhamento da Microsoft para você. Nesse caso, não necessita de seguir as instruções indicadas nas secções seguintes. No entanto, se seu provedor de conectividade não gerenciar o roteamento para você, depois de criar o circuito, continue sua configuração usando as próximas etapas. 

3. Verifique o circuito do ExpressRoute para certificar-se de que ele está provisionado e também habilitado. Utilize o seguinte exemplo:

   ```azurecli-interactive
   az network express-route list
   ```

   A resposta é semelhante ao seguinte exemplo:

   ```azurecli
   "allowClassicOperations": false,
   "authorizations": [],
   "circuitProvisioningState": "Enabled",
   "etag": "W/\"1262c492-ffef-4a63-95a8-a6002736b8c4\"",
   "gatewayManagerEtag": null,
   "id": "/subscriptions/81ab786c-56eb-4a4d-bb5f-f60329772466/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/MyCircuit",
   "location": "westus",
   "name": "MyCircuit",
   "peerings": [],
   "provisioningState": "Succeeded",
   "resourceGroup": "ExpressRouteResourceGroup",
   "serviceKey": "1d05cf70-1db5-419f-ad86-1ca62c3c125b",
   "serviceProviderNotes": null,
   "serviceProviderProperties": {
    "bandwidthInMbps": 200,
    "peeringLocation": "Silicon Valley",
    "serviceProviderName": "Equinix"
   },
   "serviceProviderProvisioningState": "Provisioned",
   "sku": {
    "family": "UnlimitedData",
    "name": "Standard_MeteredData",
    "tier": "Standard"
   },
   "tags": null,
   "type": "Microsoft.Network/expressRouteCircuits]
   ```

4. Configure o peering da Microsoft para o circuito. Confirme que tem as seguintes informações antes de continuar.

   * Uma sub-rede /30 para a ligação primária. Esta tem de ser um prefixo IPv4 público válido detido por si e registado num RIR/TIR.
   * Uma sub-rede /30 para a ligação secundária. Esta tem de ser um prefixo IPv4 público válido detido por si e registado num RIR/TIR.
   * Um ID de VLAN válido para estabelecer este peering. Assegure que nenhum peering no circuito utiliza o mesmo ID de VLAN.
   * Número AS para peering. Pode utilizar números AS de 2 e 4 bytes.
   * Prefixos anunciados: tem de fornecer uma lista de todos os prefixos que planeia anunciar durante a sessão de BGP. São aceites apenas prefixos de endereços IP públicos. Se você planeja enviar um conjunto de prefixos, pode enviar uma lista separada por vírgulas. Estes prefixos têm de ser registados para si num RIR/TIR.
   * **Opcional-** ASN do cliente: se você estiver anunciando prefixos que não estão registrados para o número as de emparelhamento, você pode especificar o número as para o qual eles são registrados.
   * Nome do registo de encaminhamento: pode especificar o RIR/TIR de acordo com o número AS e os prefixos aos quais estão registados.
   * **Opcional-** Um hash MD5 se você optar por usar um.

   Execute o exemplo a seguir para configurar o emparelhamento da Microsoft para seu circuito:

   ```azurecli-interactive
   az network express-route peering create --circuit-name MyCircuit --peer-asn 100 --primary-peer-subnet 123.0.0.0/30 -g ExpressRouteResourceGroup --secondary-peer-subnet 123.0.0.4/30 --vlan-id 300 --peering-type MicrosoftPeering --advertised-public-prefixes 123.1.0.0/24
   ```

### <a name="getmsft"></a>Para exibir detalhes de emparelhamento da Microsoft

Você pode obter detalhes de configuração usando o exemplo a seguir:

```azurecli-interactive
az network express-route peering show -g ExpressRouteResourceGroup --circuit-name MyCircuit --name AzureMicrosoftPeering
```
> [!IMPORTANT]
> A Microsoft verifica se os ' prefixos públicos publicados ' e ' peer ASN ' (ou ' cliente ASN ') especificados estão atribuídos a você no registro de roteamento da Internet. Se você estiver obtendo os prefixos públicos de outra entidade e se a atribuição não for registrada com o registro de roteamento, a validação automática não será concluída e exigirá validação manual. Se a validação automática falhar, você verá "AdvertisedPublicPrefixesState" como "validação necessária" na saída do comando acima. 
> 
> Se você vir a mensagem "validação necessária", colete os documentos que mostram os prefixos públicos são atribuídos à sua organização pela entidade que está listada como o proprietário dos prefixos no registro de roteamento e envie esses documentos para validação manual por abrindo um tíquete de suporte, conforme mostrado abaixo. 
> 
>

O resultado é semelhante ao seguinte exemplo:

```azurecli
{
  "azureAsn": 12076,
  "etag": "W/\"2e97be83-a684-4f29-bf3c-96191e270666\"",
  "gatewayManagerEtag": "18",
  "id": "/subscriptions/9a0c2943-e0c2-4608-876c-e0ddffd1211b/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/MyCircuit/peerings/AzureMicrosoftPeering",
  "lastModifiedBy": "Customer",
  "microsoftPeeringConfig": {
    "advertisedPublicPrefixes": [
        ""
      ],
     "advertisedPublicPrefixesState": "",
     "customerASN": ,
     "routingRegistryName": ""
  }
  "name": "AzureMicrosoftPeering",
  "peerAsn": ,
  "peeringType": "AzureMicrosoftPeering",
  "primaryAzurePort": "",
  "primaryPeerAddressPrefix": "",
  "provisioningState": "Succeeded",
  "resourceGroup": "ExpressRouteResourceGroup",
  "routeFilter": null,
  "secondaryAzurePort": "",
  "secondaryPeerAddressPrefix": "",
  "sharedKey": null,
  "state": "Enabled",
  "stats": null,
  "vlanId": 100
}
```

### <a name="updatemsft"></a>Para atualizar a configuração de emparelhamento da Microsoft

Você pode atualizar qualquer parte da configuração. Os prefixos anunciados do circuito estão sendo atualizados de 123.1.0.0/24 para 124.1.0.0/24 no exemplo a seguir:

```azurecli-interactive
az network express-route peering update --circuit-name MyCircuit -g ExpressRouteResourceGroup --peering-type MicrosoftPeering --advertised-public-prefixes 124.1.0.0/24
```

### <a name="addIPv6msft"></a>Para adicionar configurações de emparelhamento do IPv6 da Microsoft a uma configuração IPv4 existente

```azurecli-interactive
az network express-route peering update -g ExpressRouteResourceGroup --circuit-name MyCircuit --peering-type MicrosoftPeering --ip-version ipv6 --primary-peer-subnet 2002:db00::/126 --secondary-peer-subnet 2003:db00::/126 --advertised-public-prefixes 2002:db00::/126
```

### <a name="deletemsft"></a>Para excluir o emparelhamento da Microsoft

Você pode remover a configuração de emparelhamento executando o seguinte exemplo:

```azurecli-interactive
az network express-route peering delete -g ExpressRouteResourceGroup --circuit-name MyCircuit --name MicrosoftPeering
```

## <a name="private"></a>Emparelhamento privado do Azure

Esta seção ajuda você a criar, obter, atualizar e excluir a configuração de emparelhamento privado do Azure para um circuito do ExpressRoute.

### <a name="to-create-azure-private-peering"></a>Para criar um peering privado do Azure

1. Instale a versão mais recente do CLI do Azure. Você deve usar a versão mais recente da CLI (interface de linha de comando) do Azure. * Examine os [pré-requisitos](expressroute-prerequisites.md) e os [fluxos de trabalho](expressroute-workflows.md) antes de começar a configuração.

   ```azurecli-interactive
   az login
   ```

   Selecione a subscrição com a qual pretende criar o circuito ExpressRoute

   ```azurecli-interactive
   az account set --subscription "<subscription ID>"
   ```
2. Crie um circuito ExpressRoute. Siga as instruções para criar um [circuito ExpressRoute](howto-circuit-cli.md) e solicite ao fornecedor de conectividade que o aprovisione. Se seu provedor de conectividade oferece serviços gerenciados de camada 3, você pode pedir que seu provedor de conectividade habilite o emparelhamento privado do Azure para você. Nesse caso, não necessita de seguir as instruções indicadas nas secções seguintes. No entanto, se seu provedor de conectividade não gerenciar o roteamento para você, depois de criar o circuito, continue sua configuração usando as próximas etapas.

3. Verifique o circuito do ExpressRoute para certificar-se de que ele está provisionado e também habilitado. Utilize o seguinte exemplo:

   ```azurecli-interactive
   az network express-route show --resource-group ExpressRouteResourceGroup --name MyCircuit
   ```

   A resposta é semelhante ao seguinte exemplo:

   ```azurecli
   "allowClassicOperations": false,
   "authorizations": [],
   "circuitProvisioningState": "Enabled",
   "etag": "W/\"1262c492-ffef-4a63-95a8-a6002736b8c4\"",
   "gatewayManagerEtag": null,
   "id": "/subscriptions/81ab786c-56eb-4a4d-bb5f-f60329772466/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/MyCircuit",
   "location": "westus",
   "name": "MyCircuit",
   "peerings": [],
   "provisioningState": "Succeeded",
   "resourceGroup": "ExpressRouteResourceGroup",
   "serviceKey": "1d05cf70-1db5-419f-ad86-1ca62c3c125b",
   "serviceProviderNotes": null,
   "serviceProviderProperties": {
   "bandwidthInMbps": 200,
   "peeringLocation": "Silicon Valley",
   "serviceProviderName": "Equinix"
   },
   "serviceProviderProvisioningState": "Provisioned",
   "sku": {
    "family": "UnlimitedData",
    "name": "Standard_MeteredData",
    "tier": "Standard"
   },
   "tags": null,
   "type": "Microsoft.Network/expressRouteCircuits]
   ```

4. Configure o peering privado do Azure para o circuito. Confirme que tem os seguintes itens antes de continuar com os passos seguintes:

   * Uma sub-rede /30 para a ligação primária. A sub-rede não deve fazer parte de nenhum espaço de endereço reservado para redes virtuais.
   * Uma sub-rede /30 para a ligação secundária. A sub-rede não deve fazer parte de nenhum espaço de endereço reservado para redes virtuais.
   * Um ID de VLAN válido para estabelecer este peering. Assegure que nenhum peering no circuito utiliza o mesmo ID de VLAN.
   * Número AS para peering. Pode utilizar números AS de 2 e 4 bytes. Pode utilizar um número AS privado para este peering. Assegure que não está a utilizar 65515.
   * **Opcional-** Um hash MD5 se você optar por usar um.

   Use o exemplo a seguir para configurar o emparelhamento privado do Azure para seu circuito:

   ```azurecli-interactive
   az network express-route peering create --circuit-name MyCircuit --peer-asn 100 --primary-peer-subnet 10.0.0.0/30 -g ExpressRouteResourceGroup --secondary-peer-subnet 10.0.0.4/30 --vlan-id 200 --peering-type AzurePrivatePeering
   ```

   Se você optar por usar um hash MD5, use o exemplo a seguir:

   ```azurecli-interactive
   az network express-route peering create --circuit-name MyCircuit --peer-asn 100 --primary-peer-subnet 10.0.0.0/30 -g ExpressRouteResourceGroup --secondary-peer-subnet 10.0.0.4/30 --vlan-id 200 --peering-type AzurePrivatePeering --SharedKey "A1B2C3D4"
   ```

   > [!IMPORTANT]
   > Assegure que especifica o seu número AS como ASN de peering, não cliente ASN.
   > 
   > 

### <a name="getprivate"></a>Para exibir detalhes de emparelhamento privado do Azure

Você pode obter detalhes de configuração usando o exemplo a seguir:

```azurecli-interactive
az network express-route peering show -g ExpressRouteResourceGroup --circuit-name MyCircuit --name AzurePrivatePeering
```

O resultado é semelhante ao seguinte exemplo:

```azurecli
{
  "azureAsn": 12076,
  "etag": "W/\"2e97be83-a684-4f29-bf3c-96191e270666\"",
  "gatewayManagerEtag": "18",
  "id": "/subscriptions/9a0c2943-e0c2-4608-876c-e0ddffd1211b/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/MyCircuit/peerings/AzurePrivatePeering",
  "ipv6PeeringConfig": null,
  "lastModifiedBy": "Customer",
  "microsoftPeeringConfig": null,
  "name": "AzurePrivatePeering",
  "peerAsn": 7671,
  "peeringType": "AzurePrivatePeering",
  "primaryAzurePort": "",
  "primaryPeerAddressPrefix": "",
  "provisioningState": "Succeeded",
  "resourceGroup": "ExpressRouteResourceGroup",
  "routeFilter": null,
  "secondaryAzurePort": "",
  "secondaryPeerAddressPrefix": "",
  "sharedKey": null,
  "state": "Enabled",
  "stats": null,
  "vlanId": 100
}
```

### <a name="updateprivate"></a>Para atualizar a configuração de emparelhamento privado do Azure

Você pode atualizar qualquer parte da configuração usando o exemplo a seguir. Neste exemplo, a ID de VLAN do circuito está sendo atualizada de 100 para 500.

```azurecli-interactive
az network express-route peering update --vlan-id 500 -g ExpressRouteResourceGroup --circuit-name MyCircuit --name AzurePrivatePeering
```

### <a name="deleteprivate"></a>Para excluir o emparelhamento privado do Azure

Você pode remover a configuração de emparelhamento executando o seguinte exemplo:

> [!WARNING]
> Você deve garantir que todas as redes virtuais e conexões de Alcance Global ExpressRoute sejam removidas antes de executar este exemplo. 
> 
> 

```azurecli-interactive
az network express-route peering delete -g ExpressRouteResourceGroup --circuit-name MyCircuit --name AzurePrivatePeering
```

## <a name="public"></a>Emparelhamento público do Azure

Esta seção ajuda você a criar, obter, atualizar e excluir a configuração de emparelhamento público do Azure para um circuito do ExpressRoute.

> [!Note]
> O emparelhamento público do Azure foi preterido para novos circuitos. Para obter mais informações, consulte [emparelhamento de ExpressRoute](expressroute-circuit-peerings.md).
>

### <a name="to-create-azure-public-peering"></a>Para criar um peering público do Azure

1. Instale a versão mais recente do CLI do Azure. Você deve usar a versão mais recente da CLI (interface de linha de comando) do Azure. * Examine os [pré-requisitos](expressroute-prerequisites.md) e os [fluxos de trabalho](expressroute-workflows.md) antes de começar a configuração.

   ```azurecli-interactive
   az login
   ```

   Selecione a assinatura para a qual você deseja criar o circuito de ExpressRoute.

   ```azurecli-interactive
   az account set --subscription "<subscription ID>"
   ```
2. Crie um circuito ExpressRoute.  Siga as instruções para criar um [circuito ExpressRoute](howto-circuit-cli.md) e solicite ao fornecedor de conectividade que o aprovisione. Se seu provedor de conectividade oferece serviços gerenciados de camada 3, você pode pedir que seu provedor de conectividade habilite o emparelhamento público do Azure para você. Nesse caso, não necessita de seguir as instruções indicadas nas secções seguintes. No entanto, se seu provedor de conectividade não gerenciar o roteamento para você, depois de criar o circuito, continue sua configuração usando as próximas etapas.

3. Verifique o circuito do ExpressRoute para garantir que ele seja provisionado e também habilitado. Utilize o seguinte exemplo:

   ```azurecli-interactive
   az network express-route list
   ```

   A resposta é semelhante ao seguinte exemplo:

   ```azurecli
   "allowClassicOperations": false,
   "authorizations": [],
   "circuitProvisioningState": "Enabled",
   "etag": "W/\"1262c492-ffef-4a63-95a8-a6002736b8c4\"",
   "gatewayManagerEtag": null,
   "id": "/subscriptions/81ab786c-56eb-4a4d-bb5f-f60329772466/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/MyCircuit",
   "location": "westus",
   "name": "MyCircuit",
   "peerings": [],
   "provisioningState": "Succeeded",
   "resourceGroup": "ExpressRouteResourceGroup",
   "serviceKey": "1d05cf70-1db5-419f-ad86-1ca62c3c125b",
   "serviceProviderNotes": null,
   "serviceProviderProperties": {
    "bandwidthInMbps": 200,
    "peeringLocation": "Silicon Valley",
    "serviceProviderName": "Equinix"
   },
   "serviceProviderProvisioningState": "Provisioned",
   "sku": {
    "family": "UnlimitedData",
    "name": "Standard_MeteredData",
    "tier": "Standard"
   },
   "tags": null,
   "type": "Microsoft.Network/expressRouteCircuits]
   ```

4. Configure o peering público do Azure para o circuito. Verifique se você tem as seguintes informações antes de prosseguir.

   * Uma sub-rede /30 para a ligação primária. Esta tem de ser um prefixo IPv4 válido.
   * Uma sub-rede /30 para a ligação secundária. Esta tem de ser um prefixo IPv4 válido.
   * Um ID de VLAN válido para estabelecer este peering. Assegure que nenhum peering no circuito utiliza o mesmo ID de VLAN.
   * Número AS para peering. Pode utilizar números AS de 2 e 4 bytes.
   * **Opcional-** Um hash MD5 se você optar por usar um.

   Execute o exemplo a seguir para configurar o emparelhamento público do Azure para seu circuito:

   ```azurecli-interactive
   az network express-route peering create --circuit-name MyCircuit --peer-asn 100 --primary-peer-subnet 12.0.0.0/30 -g ExpressRouteResourceGroup --secondary-peer-subnet 12.0.0.4/30 --vlan-id 200 --peering-type AzurePublicPeering
   ```

   Se você optar por usar um hash MD5, use o exemplo a seguir:

   ```azurecli-interactive
   az network express-route peering create --circuit-name MyCircuit --peer-asn 100 --primary-peer-subnet 12.0.0.0/30 -g ExpressRouteResourceGroup --secondary-peer-subnet 12.0.0.4/30 --vlan-id 200 --peering-type AzurePublicPeering --SharedKey "A1B2C3D4"
   ```

   > [!IMPORTANT]
   > Assegure que especifica o seu número AS como ASN de peering, não cliente ASN.

### <a name="getpublic"></a>Para exibir detalhes de emparelhamento público do Azure

Você pode obter detalhes de configuração usando o exemplo a seguir:

```azurecli
az network express-route peering show -g ExpressRouteResourceGroup --circuit-name MyCircuit --name AzurePublicPeering
```

O resultado é semelhante ao seguinte exemplo:

```azurecli
{
  "azureAsn": 12076,
  "etag": "W/\"2e97be83-a684-4f29-bf3c-96191e270666\"",
  "gatewayManagerEtag": "18",
  "id": "/subscriptions/9a0c2943-e0c2-4608-876c-e0ddffd1211b/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/MyCircuit/peerings/AzurePublicPeering",
  "lastModifiedBy": "Customer",
  "microsoftPeeringConfig": null,
  "name": "AzurePublicPeering",
  "peerAsn": 7671,
  "peeringType": "AzurePublicPeering",
  "primaryAzurePort": "",
  "primaryPeerAddressPrefix": "",
  "provisioningState": "Succeeded",
  "resourceGroup": "ExpressRouteResourceGroup",
  "routeFilter": null,
  "secondaryAzurePort": "",
  "secondaryPeerAddressPrefix": "",
  "sharedKey": null,
  "state": "Enabled",
  "stats": null,
  "vlanId": 100
}
```

### <a name="updatepublic"></a>Para atualizar a configuração de emparelhamento público do Azure

Você pode atualizar qualquer parte da configuração usando o exemplo a seguir. Neste exemplo, a ID de VLAN do circuito está sendo atualizada de 200 para 600.

```azurecli-interactive
az network express-route peering update --vlan-id 600 -g ExpressRouteResourceGroup --circuit-name MyCircuit --name AzurePublicPeering
```

### <a name="deletepublic"></a>Para excluir o emparelhamento público do Azure

Você pode remover a configuração de emparelhamento executando o seguinte exemplo:

```azurecli-interactive
az network express-route peering delete -g ExpressRouteResourceGroup --circuit-name MyCircuit --name AzurePublicPeering
```

## <a name="next-steps"></a>Passos seguintes

Passo seguinte, [Ligar uma VNet a um circuito ExpressRoute](howto-linkvnet-cli.md).

* Para obter mais informações sobre o fluxo de trabalho do ExpressRoute, veja [Fluxos de trabalho do ExpressRoute](expressroute-workflows.md).
* Para obter mais informações sobre peering do circuito, veja [Circuitos ExpressRoute e domínios de encaminhamento](expressroute-circuit-peerings.md).
* Para obter mais informações sobre como trabalhar com redes virtuais, veja [Descrição geral da rede virtual](../virtual-network/virtual-networks-overview.md).
