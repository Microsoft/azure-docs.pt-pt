---
title: 'Azure ExpressRoute: Configure peering: CLI'
description: Este artigo ajuda-o a criar e fornecer o público privado, público e microsoft peering de um circuito ExpressRoute. Este artigo também mostra como verificar o estado, atualizar ou eliminar peerings no seu circuito.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: cherylmc
ms.custom: seodec18
ms.openlocfilehash: 91a1b6cc877b31fbcef638e34d3147d3377ce85c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79476122"
---
# <a name="create-and-modify-peering-for-an-expressroute-circuit-using-cli"></a>Criar e modificar o epeering para um circuito ExpressRoute usando o CLI

Este artigo ajuda-o a criar e gerir a configuração/a peering de encaminhamento para um circuito ExpressRoute no modelo de implementação do Gestor de Recursos utilizando o CLI. Também pode verificar o estado, atualizar ou eliminar e desprovisionar os pares para um circuito ExpressRoute. Se pretender utilizar um método diferente para trabalhar com o seu circuito, selecione um artigo da seguinte lista:

> [!div class="op_single_selector"]
> * [Portal Azure](expressroute-howto-routing-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-routing-arm.md)
> * [Azure CLI](howto-routing-cli.md)
> * [O público espreita](about-public-peering.md)
> * [Vídeo - Peering privado](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-private-peering-for-your-expressroute-circuit)
> * [Vídeo - Microsoft peering](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-microsoft-peering-for-your-expressroute-circuit)
> * [PowerShell (clássico)](expressroute-howto-routing-classic.md)
> 

## <a name="configuration-prerequisites"></a>Pré-requisitos da configuração

* Antes de começar, instale a versão mais recente dos comandos da CLI (2.0 ou posterior). Para obter informações sobre como instalar os comandos da CLI, veja [Instalar a CLI do Azure](/cli/azure/install-azure-cli).
* Certifique-se de que reviu os [pré-requisitos,](expressroute-prerequisites.md)os requisitos de [encaminhamento](expressroute-routing.md)e as páginas de [fluxo de trabalho](expressroute-workflows.md) antes de iniciar a configuração.
* Deve ter um circuito ExpressRoute ativo. Siga as instruções para [Criar um circuito ExpressRoute](howto-circuit-cli.md) e solicite ao seu fornecedor de conectividade para ativar o circuito antes de continuar. O circuito ExpressRoute deve estar num estado aprovisionado e habilitado para que possa executar os comandos neste artigo.

Estas instruções aplicam-se apenas aos circuitos criados com fornecedores de serviços que fornecem serviços de conectividade de Camada 2. Se estiver a utilizar um prestador de serviços que oferece serviços geridos pela Camada 3 (normalmente um IPVPN, como o MPLS), o seu fornecedor de conectividade irá configurar e gerir o encaminhamento para si.

Pode configurar o peering privado e a Microsoft a espreitar por um circuito ExpressRoute (o público azure é depreciado para novos circuitos). Os pares podem ser configurados em qualquer ordem que escolha. No entanto, tem de confirmar que conclui a configuração de cada peering, um de cada vez. Para obter mais informações sobre domínios de encaminhamento e observações, consulte os domínios de [encaminhamento expressRoute](expressroute-circuit-peerings.md). Para obter informações sobre o público, consulte o [público expressRoute.](about-public-peering.md)

## <a name="microsoft-peering"></a><a name="msft"></a>Peering da Microsoft

Esta secção ajuda-o a criar, obter, atualizar e eliminar a configuração de peering da Microsoft para um circuito ExpressRoute.

> [!IMPORTANT]
> O peering da Microsoft dos circuitos ExpressRoute que foram configurados antes de 1 de agosto de 2017 terá todos os prefixos de serviço anunciados através do peering da Microsoft, mesmo que os filtros de rota não estejam definidos. O peering da Microsoft dos circuitos ExpressRoute que estejam configurados em ou depois de 1 de agosto de 2017 não terá quaisquer prefixos anunciados até que um filtro de rota seja ligado ao circuito. Para mais informações, consulte [Configure um filtro de rota para o peering da Microsoft](how-to-routefilter-powershell.md).
> 
> 

### <a name="to-create-microsoft-peering"></a>Para criar peering da Microsoft

1. Instale a versão mais recente do Azure CLI. Utilize a versão mais recente da Interface de Linha de Comando Azure (CLI). Reveja os [pré-requisitos](expressroute-prerequisites.md) e [os fluxos de trabalho](expressroute-workflows.md) antes de iniciar a configuração.

   ```azurecli
   az login
   ```

   Selecione a subscrição para a qual pretende criar o circuito ExpressRoute.

   ```azurecli
   az account set --subscription "<subscription ID>"
   ```
2. Crie um circuito ExpressRoute. Siga as instruções para criar um [circuito ExpressRoute](howto-circuit-cli.md) e solicite ao fornecedor de conectividade que o aprovisione. Se o seu fornecedor de conectividade oferecer serviços geridos pela Camada 3, pode pedir ao seu fornecedor de conectividade que permita à Microsoft espreitar por si. Nesse caso, não necessita de seguir as instruções indicadas nas secções seguintes. No entanto, se o seu fornecedor de conectividade não conseguir encaminhamento para si, depois de criar o seu circuito, continue a sua configuração utilizando os próximos passos. 

3. Verifique o circuito ExpressRoute para se certificar de que está provisionado e também ativado. Utilize o seguinte exemplo:

   ```azurecli
   az network express-route list
   ```

   A resposta é semelhante ao seguinte exemplo:

   ```output
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
   * Prefixos anunciados: tem de fornecer uma lista de todos os prefixos que planeia anunciar durante a sessão de BGP. São aceites apenas prefixos de endereços IP públicos. Se planeia enviar um conjunto de prefixos, pode enviar uma lista separada de vírpostas. Estes prefixos têm de ser registados para si num RIR/TIR.
   * **Opcional -** Cliente ASN: Se estiver a publicitar prefixos que não estejam registados no número AS de peering, pode especificar o número AS a que estão registados.
   * Nome do registo de encaminhamento: pode especificar o RIR/TIR de acordo com o número AS e os prefixos aos quais estão registados.
   * **Opcional -** Um haxixe MD5 se optar por usar um.

   Execute o seguinte exemplo para configurar o olho da Microsoft para o seu circuito:

   ```azurecli
   az network express-route peering create --circuit-name MyCircuit --peer-asn 100 --primary-peer-subnet 123.0.0.0/30 -g ExpressRouteResourceGroup --secondary-peer-subnet 123.0.0.4/30 --vlan-id 300 --peering-type MicrosoftPeering --advertised-public-prefixes 123.1.0.0/24
   ```

### <a name="to-view-microsoft-peering-details"></a><a name="getmsft"></a>Para ver os detalhes do peering da Microsoft

Pode obter detalhes de configuração utilizando o seguinte exemplo:

```azurecli
az network express-route peering show -g ExpressRouteResourceGroup --circuit-name MyCircuit --name AzureMicrosoftPeering
```
> [!IMPORTANT]
> A Microsoft verifica se os "prefixos públicos anunciados" e "Peer ASN" (ou 'Customer ASN') são-lhe atribuídos no Registo de Encaminhamento de Internet. Se estiver a receber os prefixos públicos de outra entidade e se a atribuição não for registada com o registo de encaminhamento, a validação automática não estará concluída e exigirá validação manual. Se a validação automática falhar, verá 'PublicprefixesState' como 'Validação necessária' na saída do comando acima. 
> 
> Se vir a mensagem 'Validação necessária', recolha os documentos que mostrem que os prefixos públicos são atribuídos à sua organização pela entidade que está listada como proprietária dos prefixos no registo de encaminhamento e submeta estes documentos para validação manual por abertura de um bilhete de apoio como mostrado abaixo. 
> 
>

O resultado é semelhante ao seguinte exemplo:

```output
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

### <a name="to-update-microsoft-peering-configuration"></a><a name="updatemsft"></a>Para atualizar a configuração do peering da Microsoft

Pode atualizar qualquer parte da configuração. Os prefixos anunciados do circuito estão a ser atualizados de 123.1.0.0/24 para 124.1.0.0.0/24 no seguinte exemplo:

```azurecli
az network express-route peering update --circuit-name MyCircuit -g ExpressRouteResourceGroup --peering-type MicrosoftPeering --advertised-public-prefixes 124.1.0.0/24
```

### <a name="to-add-ipv6-microsoft-peering-settings-to-an-existing-ipv4-configuration"></a><a name="addIPv6msft"></a>Para adicionar as definições de peering iPv6 microsoft a uma configuração IPv4 existente

```azurecli
az network express-route peering update -g ExpressRouteResourceGroup --circuit-name MyCircuit --peering-type MicrosoftPeering --ip-version ipv6 --primary-peer-subnet 2002:db00::/126 --secondary-peer-subnet 2003:db00::/126 --advertised-public-prefixes 2002:db00::/126
```

### <a name="to-delete-microsoft-peering"></a><a name="deletemsft"></a>Para eliminar o peering da Microsoft

Pode remover a sua configuração de observação executando o seguinte exemplo:

```azurecli
az network express-route peering delete -g ExpressRouteResourceGroup --circuit-name MyCircuit --name MicrosoftPeering
```

## <a name="azure-private-peering"></a><a name="private"></a>Peering privado do Azure

Esta secção ajuda-o a criar, obter, atualizar e eliminar a configuração de peering privado Azure para um circuito ExpressRoute.

### <a name="to-create-azure-private-peering"></a>Para criar um peering privado do Azure

1. Instale a versão mais recente do Azure CLI. Deve utilizar a versão mais recente da Interface da linha de comando Azure (CLI).* Reveja os [pré-requisitos](expressroute-prerequisites.md) e [fluxos](expressroute-workflows.md) de trabalho antes de iniciar a configuração.

   ```azurecli
   az login
   ```

   Selecione a subscrição com a qual pretende criar o circuito ExpressRoute

   ```azurecli
   az account set --subscription "<subscription ID>"
   ```
2. Crie um circuito ExpressRoute. Siga as instruções para criar um [circuito ExpressRoute](howto-circuit-cli.md) e solicite ao fornecedor de conectividade que o aprovisione. Se o seu fornecedor de conectividade oferecer serviços geridos pela Camada 3, pode pedir ao seu fornecedor de conectividade que permita ao Azure espreitar por si. Nesse caso, não necessita de seguir as instruções indicadas nas secções seguintes. No entanto, se o seu fornecedor de conectividade não conseguir encaminhamento para si, depois de criar o seu circuito, continue a sua configuração utilizando os próximos passos.

3. Verifique o circuito ExpressRoute para se certificar de que está provisionado e também ativado. Utilize o seguinte exemplo:

   ```azurecli
   az network express-route show --resource-group ExpressRouteResourceGroup --name MyCircuit
   ```

   A resposta é semelhante ao seguinte exemplo:

   ```output
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

   * Uma sub-rede /30 para a ligação primária. A sub-rede não deve fazer parte de qualquer espaço de endereço reservado para redes virtuais.
   * Uma sub-rede /30 para a ligação secundária. A sub-rede não deve fazer parte de qualquer espaço de endereço reservado para redes virtuais.
   * Um ID de VLAN válido para estabelecer este peering. Assegure que nenhum peering no circuito utiliza o mesmo ID de VLAN.
   * Número AS para peering. Pode utilizar números AS de 2 e 4 bytes. Pode utilizar um número AS privado para este peering. Assegure que não está a utilizar 65515.
   * **Opcional -** Um haxixe MD5 se optar por usar um.

   Utilize o seguinte exemplo para configurar o pino privado azure para o seu circuito:

   ```azurecli
   az network express-route peering create --circuit-name MyCircuit --peer-asn 100 --primary-peer-subnet 10.0.0.0/30 -g ExpressRouteResourceGroup --secondary-peer-subnet 10.0.0.4/30 --vlan-id 200 --peering-type AzurePrivatePeering
   ```

   Se optar por utilizar um haxixe MD5, utilize o seguinte exemplo:

   ```azurecli
   az network express-route peering create --circuit-name MyCircuit --peer-asn 100 --primary-peer-subnet 10.0.0.0/30 -g ExpressRouteResourceGroup --secondary-peer-subnet 10.0.0.4/30 --vlan-id 200 --peering-type AzurePrivatePeering --SharedKey "A1B2C3D4"
   ```

   > [!IMPORTANT]
   > Assegure que especifica o seu número AS como ASN de peering, não cliente ASN.
   > 
   > 

### <a name="to-view-azure-private-peering-details"></a><a name="getprivate"></a>Para ver os detalhes do peering privado do Azure

Pode obter detalhes de configuração utilizando o seguinte exemplo:

```azurecli
az network express-route peering show -g ExpressRouteResourceGroup --circuit-name MyCircuit --name AzurePrivatePeering
```

O resultado é semelhante ao seguinte exemplo:

```output
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

### <a name="to-update-azure-private-peering-configuration"></a><a name="updateprivate"></a>Para atualizar a configuração do peering privado do Azure

Pode atualizar qualquer parte da configuração utilizando o seguinte exemplo. Neste exemplo, o ID VLAN do circuito está a ser atualizado de 100 a 500.

```azurecli
az network express-route peering update --vlan-id 500 -g ExpressRouteResourceGroup --circuit-name MyCircuit --name AzurePrivatePeering
```

### <a name="to-delete-azure-private-peering"></a><a name="deleteprivate"></a>Para eliminar um peering privado do Azure

Pode remover a sua configuração de observação executando o seguinte exemplo:

> [!WARNING]
> Deve garantir que todas as redes virtuais e ligações ExpressRoute Global Reach sejam removidas antes de executar este exemplo. 
> 
> 

```azurecli
az network express-route peering delete -g ExpressRouteResourceGroup --circuit-name MyCircuit --name AzurePrivatePeering
```


## <a name="next-steps"></a>Passos seguintes

Passo seguinte, [Ligar uma VNet a um circuito ExpressRoute](howto-linkvnet-cli.md).

* Para obter mais informações sobre o fluxo de trabalho do ExpressRoute, veja [Fluxos de trabalho do ExpressRoute](expressroute-workflows.md).
* Para obter mais informações sobre peering do circuito, veja [Circuitos ExpressRoute e domínios de encaminhamento](expressroute-circuit-peerings.md).
* Para obter mais informações sobre como trabalhar com redes virtuais, veja [Descrição geral da rede virtual](../virtual-network/virtual-networks-overview.md).
