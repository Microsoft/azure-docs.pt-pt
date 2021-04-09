---
title: 'Tutorial: Configure peering para o circuito Expressroute - Azure CLI'
description: Este tutorial mostra-lhe como criar e providenciar o olhar privado, público e Microsoft de um circuito ExpressRoute. Este artigo também mostra como verificar o estado, atualizar ou eliminar peerings no seu circuito.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: tutorial
ms.date: 10/09/2020
ms.author: duau
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: 2c56e847e3b112d50285cd2c116c8f22efbc507f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101715535"
---
# <a name="tutorial-create-and-modify-peering-for-an-expressroute-circuit-using-cli"></a>Tutorial: Criar e modificar o espreitamento para um circuito ExpressRoute usando OCli

Este tutorial mostra-lhe como criar e gerir a configuração/observação de encaminhamento para um circuito ExpressRoute no modelo de implementação do Gestor de Recursos utilizando o CLI. Também pode verificar o estado, a atualização ou eliminação e desprovisionamento de um circuito ExpressRoute. Se pretender utilizar um método diferente para trabalhar com o seu circuito, selecione um artigo da seguinte lista:

> [!div class="op_single_selector"]
> * [Portal do Azure](expressroute-howto-routing-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-routing-arm.md)
> * [CLI do Azure](howto-routing-cli.md)
> * [Peering público](about-public-peering.md)
> * [Vídeo - Peering privado](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-private-peering-for-your-expressroute-circuit)
> * [Vídeo - Microsoft a espreitar](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-microsoft-peering-for-your-expressroute-circuit)
> * [PowerShell (clássico)](expressroute-howto-routing-classic.md)
> 

Neste tutorial, ficará a saber como:
> [!div class="checklist"]
> - Configurar, atualizar e eliminar a Microsoft a espreitar por um circuito
> - Configurar, atualizar e apagar Azure private espreitar por um circuito

## <a name="prerequisites"></a>Pré-requisitos

* Antes de começar, instale a versão mais recente dos comandos da CLI (2.0 ou posterior). Para obter informações sobre como instalar os comandos da CLI, veja [Instalar a CLI do Azure](/cli/azure/install-azure-cli).
* Certifique-se de que reviu os [pré-requisitos, requisitos](expressroute-prerequisites.md) [de encaminhamento](expressroute-routing.md)e páginas [de fluxo de trabalho](expressroute-workflows.md) antes de iniciar a configuração.
* Deve ter um circuito ExpressRoute ativo. Siga as instruções para [criar um circuito ExpressRoute](howto-circuit-cli.md) e tenha o circuito ativado pelo seu fornecedor de conectividade antes de continuar. O circuito ExpressRoute deve estar num estado provisionado e habilitado para que você execute os comandos neste artigo.

Estas instruções aplicam-se apenas aos circuitos criados com fornecedores de serviços que fornecem serviços de conectividade de Camada 2. Se estiver a utilizar um prestador de serviços que oferece serviços geridos da Camada 3 (normalmente um IPVPN, como o MPLS), o seu fornecedor de conectividade configurará e gerirá o encaminhamento para si.

Pode configurar o seu olhar privado e a Microsoft a espreitar por um circuito ExpressRoute. Os seus pares podem ser configurados em qualquer ordem que escolherem. No entanto, tem de confirmar que conclui a configuração de cada peering, um de cada vez. Para obter mais informações sobre domínios de encaminhamento e observação, consulte [os domínios de encaminhamento ExpressRoute](expressroute-circuit-peerings.md).

## <a name="microsoft-peering"></a><a name="msft"></a>Microsoft a espreitar

Esta secção ajuda-o a criar, obter, atualizar e eliminar a configuração de espreitar a Microsoft para um circuito ExpressRoute.

> [!IMPORTANT]
> O perspitamento da Microsoft nos circuitos ExpressRoute que foram configurados antes de 1 de agosto de 2017 terá todos os prefixos de serviço anunciados através do olho da Microsoft, mesmo que os filtros de rota não estejam definidos. O perspitamento da Microsoft dos circuitos ExpressRoute que estejam configurados em ou depois de 1 de agosto de 2017 não terá quaisquer prefixos anunciados até que um filtro de rota seja ligado ao circuito. Para obter mais informações, consulte [configurar um filtro de rota para o microsoft espreitar.](how-to-routefilter-powershell.md)
> 


### <a name="to-create-microsoft-peering"></a>Para criar peering da Microsoft

1. Instale a versão mais recente do Azure CLI. Utilize a versão mais recente da Interface de Linha de Comando Azure (CLI).

   ```azurecli
   az login
   ```

   Selecione a subscrição para a qual pretende criar o circuito ExpressRoute.

   ```azurecli
   az account set --subscription "<subscription ID>"
   ```

1. Crie um circuito ExpressRoute. Siga as instruções para criar um [circuito ExpressRoute](howto-circuit-cli.md) e solicite ao fornecedor de conectividade que o aprovisione. Se o seu fornecedor de conectividade oferecer serviços geridos da Camada 3, pode pedir ao seu fornecedor de conectividade para ativar o seu olhar pela Microsoft. Nesse caso, não necessita de seguir as instruções indicadas nas secções seguintes. No entanto, se o seu fornecedor de conectividade não gerir o encaminhamento para si, depois de criar o seu circuito, continue a sua configuração utilizando os próximos passos. 

1. Verifique o circuito ExpressRoute para se certificar de que está a provisionado e também ativado. Utilize o seguinte exemplo:

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

4. Configure o peering da Microsoft para o circuito. Certifique-se de que tem as seguintes informações antes de continuar.

   * Uma sub-rede /30 para a ligação primária. O bloco de endereços deve ser um prefixo IPv4 público válido que lhe pertence e registado num RIR/IRR.
   * Uma sub-rede /30 para a ligação secundária. O bloco de endereços deve ser um prefixo IPv4 público válido que lhe pertence e registado num RIR/IRR.
   * Um ID de VLAN válido para estabelecer este peering. Assegure que nenhum peering no circuito utiliza o mesmo ID de VLAN.
   * Número AS para peering. Pode utilizar números AS de 2 e 4 bytes.
   * Prefixos anunciados: Forneça uma lista de todos os prefixos que pretende anunciar durante a sessão de BGP. São aceites apenas prefixos de endereços IP públicos. Se pretender enviar um conjunto de prefixos, pode enviar uma lista separada por vírgula. Estes prefixos têm de ser registados para si num RIR/TIR.
   * **Opcional -** Cliente ASN: Se estiver a publicitar prefixos que não estejam registados no número AS que está a espreitar, pode especificar o número AS com o qual estão registados.
   * Nome do registo de encaminhamento: pode especificar o RIR/TIR de acordo com o número AS e os prefixos aos quais estão registados.
   * **Opcional -** Um haxixe MD5 se optar por usar um.

   Executar o seguinte exemplo para configurar o olho da Microsoft para o seu circuito:

   ```azurecli
   az network express-route peering create --circuit-name MyCircuit --peer-asn 100 --primary-peer-subnet 123.0.0.0/30 -g ExpressRouteResourceGroup --secondary-peer-subnet 123.0.0.4/30 --vlan-id 300 --peering-type MicrosoftPeering --advertised-public-prefixes 123.1.0.0/24
   ```

### <a name="to-view-microsoft-peering-details"></a><a name="getmsft"></a>Para ver os detalhes do peering da Microsoft

Pode obter detalhes de configuração utilizando o seguinte exemplo:

```azurecli
az network express-route peering show -g ExpressRouteResourceGroup --circuit-name MyCircuit --name AzureMicrosoftPeering
```
> [!IMPORTANT]
> A Microsoft verifica se os prefixos públicos anunciados especificados e 'Peer ASN' (ou 'Cliente ASN') são-lhe atribuídos no Registo de Encaminhamento de Internet. Se estiver a receber os prefixos públicos de outra entidade e se a atribuição não for registada com o registo de encaminhamento, a validação automática não será completa e exigirá validação manual. Se a validação automática falhar, verá 'PublicdPublicPrefixesState' como 'Validação necessária' na saída do comando acima. 
> 
> Se vir a mensagem 'Validação necessária', recolha os documentos(s) que mostram que os prefixos públicos são atribuídos à sua organização pela entidade que está listada como o proprietário dos prefixos no registo de encaminhamento e submeta esses documentos para validação manual através da abertura de um bilhete de apoio. 
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

Pode atualizar qualquer parte da configuração. Os prefixos anunciados do circuito estão a ser atualizados de 123.1.0.0/24 para 124.1.0.0/24 no seguinte exemplo:

```azurecli
az network express-route peering update --circuit-name MyCircuit -g ExpressRouteResourceGroup --peering-type MicrosoftPeering --advertised-public-prefixes 124.1.0.0/24
```

### <a name="to-add-ipv6-microsoft-peering-settings-to-an-existing-ipv4-configuration"></a><a name="addIPv6msft"></a>Para adicionar configurações de peering IPv6 Microsoft a uma configuração IPv4 existente

```azurecli
az network express-route peering update -g ExpressRouteResourceGroup --circuit-name MyCircuit --peering-type MicrosoftPeering --ip-version ipv6 --primary-peer-subnet 2002:db00::/126 --secondary-peer-subnet 2003:db00::/126 --advertised-public-prefixes 2002:db00::/126
```

## <a name="azure-private-peering"></a><a name="private"></a>Peering privado do Azure

Esta secção ajuda-o a criar, obter, atualizar e eliminar a configuração de observação privada Azure para um circuito ExpressRoute.

### <a name="to-create-azure-private-peering"></a>Para criar um peering privado do Azure

1. Instale a versão mais recente do Azure CLI.
1. 
   ```azurecli
   az login
   ```

   Selecione a subscrição com a qual pretende criar o circuito ExpressRoute

   ```azurecli
   az account set --subscription "<subscription ID>"
   ```
1. Crie um circuito ExpressRoute. Siga as instruções para criar um [circuito ExpressRoute](howto-circuit-cli.md) e solicite ao fornecedor de conectividade que o aprovisione. Se o seu fornecedor de conectividade oferecer serviços geridos da Camada 3, pode pedir ao seu fornecedor de conectividade para ativar o seu olhar privado Azure. Nesse caso, não necessita de seguir as instruções indicadas nas secções seguintes. No entanto, se o seu fornecedor de conectividade não gerir o encaminhamento para si, depois de criar o seu circuito, continue a sua configuração utilizando os próximos passos.

1. Verifique o circuito ExpressRoute para se certificar de que está a provisionado e também ativado. Utilize o seguinte exemplo:

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

1. Configure o peering privado do Azure para o circuito. Certifique-se de que tem os seguintes itens antes de continuar com os próximos passos:

   * Um par de sub-redes que não fazem parte de qualquer espaço de endereço reservado para redes virtuais. Uma sub-rede será usada para a ligação primária, enquanto a outra será usada para a ligação secundária. A partir de cada uma destas sub-redes, irá atribuir o primeiro endereço IP utilizável ao seu router, uma vez que a Microsoft utiliza o segundo IP utilizável para o seu router. Tem três opções para este par de sub-redes:
       * IPv4: Duas sub-redes /30.
       * IPv6: Duas sub-redes /126.
       * Ambas: Duas sub-redes /30 e duas sub-redes /126.
   * Um ID de VLAN válido para estabelecer este peering. Assegure que nenhum peering no circuito utiliza o mesmo ID de VLAN.
   * Número AS para peering. Pode utilizar números AS de 2 e 4 bytes. Pode utilizar um número AS privado para este peering. Certifique-se de que não está a usar o 65515.
   * **Opcional -** Um haxixe MD5 se optar por usar um.

   Use o seguinte exemplo para configurar o Azure private espreitar para o seu circuito:

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

## <a name="clean-up-resources"></a>Limpar os recursos

### <a name="to-delete-microsoft-peering"></a><a name="deletemsft"></a>Para eliminar o peering da Microsoft

Pode remover a sua configuração de espreguiçadamento executando o seguinte exemplo:

```azurecli
az network express-route peering delete -g ExpressRouteResourceGroup --circuit-name MyCircuit --name MicrosoftPeering
```

### <a name="to-delete-azure-private-peering"></a><a name="deleteprivate"></a>Para eliminar um peering privado do Azure

Pode remover a sua configuração de espreguiçadamento executando o seguinte exemplo:

> [!WARNING]
> Deve certificar-se de que todas as redes virtuais e as ligações ExpressRoute Global Reach são removidas antes de executar este exemplo. 
> 
> 

```azurecli
az network express-route peering delete -g ExpressRouteResourceGroup --circuit-name MyCircuit --name AzurePrivatePeering
```

## <a name="next-steps"></a>Passos seguintes

Depois de configurar o Azure em privado, pode ligar redes virtuais ao circuito, consulte: 

> [!div class="nextstepaction"]
> [Ligar uma VNet a um circuito do ExpressRoute](howto-linkvnet-cli.md)
