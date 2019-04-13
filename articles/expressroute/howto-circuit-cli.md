---
title: 'Criar e modificar um circuito do ExpressRoute: CLI do Azure | Documentos da Microsoft'
description: Este artigo mostra como criar, aprovisionar, certifique-se, atualizar, eliminar e desaprovisionar um circuito do ExpressRoute com a CLI.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: anzaman;cherylmc
ms.openlocfilehash: 556589aa7a0a577b9b1a010cf4811922ebc6de52
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/12/2019
ms.locfileid: "59524893"
---
# <a name="create-and-modify-an-expressroute-circuit-using-cli"></a>Criar e modificar um circuito do ExpressRoute com a CLI


Este artigo descreve como criar um circuito do ExpressRoute do Azure com a Interface de linha de comandos (CLI). Este artigo também mostra como verificar o estado, update ou delete e um circuito de desaprovisionamento. Se pretender utilizar um método diferente para trabalhar com os circuitos do ExpressRoute, pode selecionar o artigo da lista seguinte:

> [!div class="op_single_selector"]
> * [Portal do Azure](expressroute-howto-circuit-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-circuit-arm.md)
> * [CLI do Azure](howto-circuit-cli.md)
> * [Vídeo - portal do Azure](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit)
> * [PowerShell (clássico)](expressroute-howto-circuit-classic.md)
> 

## <a name="before-you-begin"></a>Antes de começar

* Antes de começar, instale a versão mais recente dos comandos da CLI (2.0 ou posterior). Para obter informações sobre como instalar os comandos da CLI, veja [Instalar a CLI do Azure](/cli/azure/install-azure-cli) e [Introdução à CLI do Azure](/cli/azure/get-started-with-azure-cli).
* Reveja os [pré-requisitos](expressroute-prerequisites.md) e [fluxos de trabalho](expressroute-workflows.md) antes de iniciar a configuração.

## <a name="create"></a>Criar e aprovisionar um circuito do ExpressRoute

### <a name="1-sign-in-to-your-azure-account-and-select-your-subscription"></a>1. Inicie sessão na sua conta do Azure e selecione a sua subscrição

Para iniciar a configuração, inicie sessão na sua conta do Azure. Se usar o CloudShell "Experimente-", tem sessão iniciada automaticamente. Utilize os exemplos seguintes para ajudar na ligação:

```azurecli
az login
```

Verifique as subscrições da conta.

```azurecli-interactive
az account list
```

Selecione a subscrição para o qual pretende criar um circuito do ExpressRoute.

```azurecli-interactive
az account set --subscription "<subscription ID>"
```

### <a name="2-get-the-list-of-supported-providers-locations-and-bandwidths"></a>2. Obter a lista de fornecedores suportados, locais e larguras de banda

Antes de criar um circuito do ExpressRoute, terá da lista de fornecedores de conectividade suportados, locais e opções de largura de banda. O comando da CLI `az network express-route list-service-providers` retorna essas informações, que irá utilizar em passos posteriores:

```azurecli-interactive
az network express-route list-service-providers
```

A resposta é semelhante ao seguinte exemplo:

```azurecli
[
  {
    "bandwidthsOffered": [
      {
        "offerName": "50Mbps",
        "valueInMbps": 50
      },
      {
        "offerName": "100Mbps",
        "valueInMbps": 100
      },
      {
        "offerName": "200Mbps",
        "valueInMbps": 200
      },
      {
        "offerName": "500Mbps",
        "valueInMbps": 500
      },
      {
        "offerName": "1Gbps",
        "valueInMbps": 1000
      },
      {
        "offerName": "2Gbps",
        "valueInMbps": 2000
      },
      {
        "offerName": "5Gbps",
        "valueInMbps": 5000
      },
      {
        "offerName": "10Gbps",
        "valueInMbps": 10000
      }
    ],
    "id": "/subscriptions//resourceGroups//providers/Microsoft.Network/expressRouteServiceProviders/",
    "location": null,
    "name": "AARNet",
    "peeringLocations": [
      "Melbourne",
      "Sydney"
    ],
    "provisioningState": "Succeeded",
    "resourceGroup": "",
    "tags": null,
    "type": "Microsoft.Network/expressRouteServiceProviders"
  },
```

Verifique a resposta para ver se o seu fornecedor de conectividade está listado. Tome nota das informações seguintes, irá necessitar quando criar um circuito:

* Nome
* PeeringLocations
* BandwidthsOffered

Agora, está pronto para criar um circuito do ExpressRoute.

### <a name="3-create-an-expressroute-circuit"></a>3. Criar um circuito do ExpressRoute

> [!IMPORTANT]
> O circuito de ExpressRoute é cobrado a partir do momento numa que chave de serviço é emitida. Efetue esta operação quando o fornecedor de conectividade estiver pronto para aprovisionar o circuito.
> 
> 

Se ainda não tiver um grupo de recursos, tem de criar uma antes de criar o seu circuito do ExpressRoute. Pode criar um grupo de recursos ao executar o seguinte comando:

```azurecli-interactive
az group create -n ExpressRouteResourceGroup -l "West US"
```

O exemplo seguinte mostra como criar um 200 Mbps circuito do ExpressRoute através de Equinix no vale do silício. Se estiver a utilizar um fornecedor diferente e configurações diferentes, substitua essa informação ao fazer o seu pedido. 

Certifique-se de que especifica a camada de SKU correta e a família SKU:

* Escalão de SKU determina se a um padrão de ExpressRoute ou de um suplemento ExpressRoute premium é ativado. Pode especificar "Padrão" para obter o SKU standard ou o "Premium" para o suplemento premium.
* Família SKU determina o tipo de faturação. Pode especificar 'Metereddata' para um plano de dados limitados e 'Unlimiteddata' para um plano de dados ilimitados. Pode alterar o tipo de faturação de 'Metereddata' para 'Unlimiteddata', mas não é possível alterar o tipo de 'Unlimiteddata' para 'Metereddata'.


O circuito de ExpressRoute é cobrado a partir do momento numa que chave de serviço é emitida. O exemplo seguinte é um pedido para uma nova chave de serviço:

```azurecli-interactive
az network express-route create --bandwidth 200 -n MyCircuit --peering-location "Silicon Valley" -g ExpressRouteResourceGroup --provider "Equinix" -l "West US" --sku-family MeteredData --sku-tier Standard
```

A resposta contém a chave de serviço.

### <a name="4-list-all-expressroute-circuits"></a>4. Lista de todos os circuitos do ExpressRoute

Para obter uma lista de todos os circuitos do ExpressRoute que criou, execute o `az network express-route list` comando. Pode recuperar essas informações em qualquer altura, utilizando este comando. Para listar todos os circuitos, fazer a chamada sem parâmetros.

```azurecli-interactive
az network express-route list
```

A chave de serviço está listada na *ServiceKey* campo da resposta.

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
"serviceProviderProvisioningState": "NotProvisioned",
"sku": {
  "family": "UnlimitedData",
  "name": "Standard_MeteredData",
  "tier": "Standard"
},
"tags": null,
"type": "Microsoft.Network/expressRouteCircuits]
```

Pode obter descrições detalhadas de todos os parâmetros ao executar o comando com o "-h' parâmetro.

```azurecli-interactive
az network express-route list -h
```

### <a name="5-send-the-service-key-to-your-connectivity-provider-for-provisioning"></a>5. Enviar a chave de serviço para o seu fornecedor de conectividade para o aprovisionamento

"ServiceProviderProvisioningState" fornece informações sobre o estado atual do aprovisionamento no lado do fornecedor de serviços. O estado fornece o estado do lado da Microsoft. Para obter mais informações, consulte a [artigo de fluxos de trabalho](expressroute-workflows.md#expressroute-circuit-provisioning-states).

Quando cria um novo circuito do ExpressRoute, o circuito está no estado seguinte:

```azurecli-interactive
"serviceProviderProvisioningState": "NotProvisioned"
"circuitProvisioningState": "Enabled"
```

O circuito muda para o seguinte estado quando o fornecedor de conectividade está no processo de ativá-la para:

```azurecli-interactive
"serviceProviderProvisioningState": "Provisioning"
"circuitProvisioningState": "Enabled"
```

Para poder ser capaz de utilizar um circuito do ExpressRoute, tem de estar no seguinte estado:

```azurecli-interactive
"serviceProviderProvisioningState": "Provisioned"
"circuitProvisioningState": "Enabled
```

### <a name="6-periodically-check-the-status-and-the-state-of-the-circuit-key"></a>6. Verificar periodicamente o estado e o estado da chave de circuito

A verificar o estado e o estado da chave de circuito permite-lhe saber quando o fornecedor tiver ativado o seu circuito. Depois do circuito tiver sido configurado, 'ServiceProviderProvisioningState' aparece como "Aprovisionado", conforme mostrado no exemplo a seguir:

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
"serviceProviderProvisioningState": "NotProvisioned",
"sku": {
  "family": "UnlimitedData",
  "name": "Standard_MeteredData",
  "tier": "Standard"
},
"tags": null,
"type": "Microsoft.Network/expressRouteCircuits]
```

### <a name="7-create-your-routing-configuration"></a>7. Criar a configuração do encaminhamento

Para obter instruções passo a passo, consulte a [configuração do encaminhamento do circuito de ExpressRoute](howto-routing-cli.md) artigo para criar e modificar os peerings de circuito.

> [!IMPORTANT]
> Estas instruções aplicam-se apenas aos circuitos criados com fornecedores de serviços que oferecem serviços de conectividade de 2 de camada. Se estiver a utilizar um fornecedor de serviços que oferece geridos de camada 3 serviços (normalmente uma VPN de IP, como MPLS), o seu fornecedor de conectividade configura e gere o encaminhamento por si.
> 
> 

### <a name="8-link-a-virtual-network-to-an-expressroute-circuit"></a>8. Ligar uma rede virtual a um circuito ExpressRoute

Em seguida, ligar uma rede virtual para o seu circuito do ExpressRoute. Utilize o [ligar redes virtuais para circuitos do ExpressRoute](howto-linkvnet-cli.md) artigo.

## <a name="modify"></a>Modificar um circuito do ExpressRoute

Pode modificar algumas propriedades de um circuito do ExpressRoute sem afetar a conectividade. Pode fazer as seguintes alterações sem tempo de inatividade:

* Pode ativar ou desativar um suplemento ExpressRoute premium para o seu circuito do ExpressRoute.
* Pode aumentar a largura de banda do seu circuito do ExpressRoute, desde que haja capacidade na porta. No entanto, a desatualização de largura de banda de um circuito não é suportada. 
* Pode alterar o plano de medição de dados limitados para dados ilimitados. No entanto, a alteração do plano de medição de dados ilimitados para dados limitados não é suportada.
* Pode ativar e desativar *permitir operações clássicas*.

Para obter mais informações sobre limites e limitações, consulte a [FAQ do ExpressRoute](expressroute-faqs.md).

### <a name="to-enable-the-expressroute-premium-add-on"></a>Para ativar o suplemento ExpressRoute premium

Pode ativar o suplemento ExpressRoute premium para o seu circuito existente ao utilizar o seguinte comando:

```azurecli-interactive
az network express-route update -n MyCircuit -g ExpressRouteResourceGroup --sku-tier Premium
```

O circuito tem agora as funcionalidades de suplemento ExpressRoute premium ativadas. Começamos a faturação é para a capacidade de suplemento premium assim que o comando foi executada com êxito.

### <a name="to-disable-the-expressroute-premium-add-on"></a>Para desativar o suplemento ExpressRoute premium

> [!IMPORTANT]
> Esta operação pode falhar se estiver a utilizar os recursos que são maiores que o que é permitido para o circuito standard.
> 
> 

Antes de desativar o suplemento ExpressRoute premium, compreenda os seguintes critérios:

* Antes de para mudar do premium para standard, certifique-se de que tem menos de 10 redes virtuais ligadas ao circuito. Se tiver mais de 10, o seu pedido de atualização falha e é faturado a tarifas premium.
* Deve desassociar a todas as redes virtuais em outras regiões geopolíticas. Se não desassociar todas as suas redes virtuais, o seu pedido de atualização falha e é faturado a tarifas premium.
* A tabela de rotas tem de ser inferior a 4000 rotas para peering privado. Se o tamanho da tabela de rota for superior a 4.000, ignora a sessão de BGP. A sessão não ser reenabled até que o número de prefixos anunciados é inferior a 4000.

Pode desativar o suplemento ExpressRoute premium para o circuito existente ao utilizar o exemplo seguinte:

```azurecli-interactive
az network express-route update -n MyCircuit -g ExpressRouteResourceGroup --sku-tier Standard
```

### <a name="to-update-the-expressroute-circuit-bandwidth"></a>Para atualizar a largura de banda do circuito do ExpressRoute

Para as opções de largura de banda suportadas para o seu fornecedor, consulte a [FAQ do ExpressRoute](expressroute-faqs.md). Pode escolher qualquer tamanho superior ao tamanho do seu circuito existente.

> [!IMPORTANT]
> Se houver a capacidade inadequada na porta existente, poderá ter de recriar o circuito do ExpressRoute. Não é possível atualizar o circuito se não existir nenhuma capacidade adicional disponível naquele local.
>
> Não é possível reduzir a largura de banda de um circuito do ExpressRoute sem interrupção. Desatualização de largura de banda exige que desaprovisionar o circuito do ExpressRoute e, em seguida, reaprovisionar um novo circuito do ExpressRoute.
>

Depois de decidir o tamanho que necessário, utilize o seguinte comando para redimensionar o seu circuito:

```azurecli-interactive
az network express-route update -n MyCircuit -g ExpressRouteResourceGroup --bandwidth 1000
```

O circuito é dimensionado de cópia de segurança no lado do Microsoft. Em seguida, tem de contactar o seu fornecedor de conectividade para atualizar as configurações no seu lado para fazer corresponder esta alteração. Depois de efetuar esta notificação, começamos a de faturação para a opção de largura de banda atualizado.

### <a name="to-move-the-sku-from-metered-to-unlimited"></a>Para mover o SKU de limitado para ilimitado

Pode alterar o SKU de um circuito do ExpressRoute, através do exemplo seguinte:

```azurecli-interactive
az network express-route update -n MyCircuit -g ExpressRouteResourceGroup --sku-family UnlimitedData
```

### <a name="to-control-access-to-the-classic-and-resource-manager-environments"></a>Para controlar o acesso a clássica e Resource Manager ambientes

Reveja as instruções em [circuitos do ExpressRoute mover do clássico para o modelo de implementação do Resource Manager](expressroute-howto-move-arm.md).

## <a name="delete"></a>Desaprovisionar e eliminar um circuito do ExpressRoute

Para desaprovisionar e eliminar um circuito do ExpressRoute, certifique-se de que compreende os seguintes critérios:

* Tem de desassociar todas as redes virtuais do circuito do ExpressRoute. Se esta operação falhar, verifique se quaisquer redes virtuais são ligadas ao circuito.
* Se for o fornecedor de serviços de circuito do ExpressRoute estado de aprovisionamento **aprovisionamento** ou **aprovisionado**, deve trabalhar com o fornecedor de serviços para desaprovisionar o circuito no seu lado. Continuamos a reservar recursos e a faturação é que o fornecedor de serviços seja concluída o circuito de desaprovisionamento e notifica-nos.
* É possível eliminar o circuito se o fornecedor de serviços tiver desaprovisionado o circuito. Quando um circuito é desaprovisionado, o fornecedor de serviços de estado de aprovisionamento está definido como **não aprovisionado**. Isto interrompe a faturação do circuito.

Pode eliminar o seu circuito do ExpressRoute, executando o seguinte comando:

```azurecli-interactive
az network express-route delete  -n MyCircuit -g ExpressRouteResourceGroup
```

## <a name="next-steps"></a>Passos Seguintes

Depois de criar o seu circuito, certifique-se de que efetua as seguintes tarefas:

* [Criar e modificar o encaminhamento para o seu circuito do ExpressRoute](howto-routing-cli.md)
* [Ligar a rede virtual para o seu circuito do ExpressRoute](howto-linkvnet-cli.md)
