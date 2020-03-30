---
title: 'Criar e modificar um circuito ExpressRoute: Azure CLI'
description: Este artigo mostra como criar, fornecer, verificar, atualizar, excluir e desprovisionar um circuito ExpressRoute utilizando o CLI.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 11/13/2019
ms.author: cherylmc
ms.openlocfilehash: b967e1d8751a9c6a5214fef5241d57e954ad9f17
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79476156"
---
# <a name="create-and-modify-an-expressroute-circuit-using-cli"></a>Criar e modificar um circuito do ExpressRoute através da CLI


Este artigo descreve como criar um circuito Azure ExpressRoute utilizando a Interface da Linha de Comando (CLI). Este artigo também mostra como verificar o estado, atualizar ou excluir e desprovisionar um circuito. Se pretender utilizar um método diferente para trabalhar com circuitos ExpressRoute, pode selecionar o artigo a partir da seguinte lista:

> [!div class="op_single_selector"]
> * [Portal Azure](expressroute-howto-circuit-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-circuit-arm.md)
> * [Azure CLI](howto-circuit-cli.md)
> * [Modelo Azure Resource Manager](expressroute-howto-circuit-resource-manager-template.md)
> * [Vídeo - Portal Azure](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit)
> * [PowerShell (clássico)](expressroute-howto-circuit-classic.md)
>

## <a name="before-you-begin"></a>Antes de começar

* Antes de começar, instale a versão mais recente dos comandos da CLI (2.0 ou posterior). Para obter informações sobre como instalar os comandos da CLI, veja [Instalar a CLI do Azure](/cli/azure/install-azure-cli) e [Introdução à CLI do Azure](/cli/azure/get-started-with-azure-cli).
* Reveja os [pré-requisitos](expressroute-prerequisites.md) e [os fluxos de trabalho](expressroute-workflows.md) antes de iniciar a configuração.

## <a name="create-and-provision-an-expressroute-circuit"></a><a name="create"></a>Criar e fornecer um circuito ExpressRoute

### <a name="1-sign-in-to-your-azure-account-and-select-your-subscription"></a>1. Inscreva-se na sua conta Azure e selecione a sua subscrição

Para iniciar a sua configuração, inicie o seu inserido na sua conta Azure. Se utilizar a CloudShell "Try It", está automaticamente inscrito. Utilize os seguintes exemplos para ajudá-lo a ligar:

```azurecli-interactive
az login
```

Verifique as subscrições da conta.

```azurecli-interactive
az account list
```

Selecione a subscrição para a qual pretende criar um circuito ExpressRoute.

```azurecli-interactive
az account set --subscription "<subscription ID>"
```

### <a name="2-get-the-list-of-supported-providers-locations-and-bandwidths"></a>2. Obtenha a lista de fornecedores, locais e larguras de banda suportados

Antes de criar um circuito ExpressRoute, precisa da lista de fornecedores de conectividade suportados, locais e opções de largura de banda. O comando `az network express-route list-service-providers` CLI devolve esta informação, que utilizará em etapas posteriores:

```azurecli-interactive
az network express-route list-service-providers
```

A resposta é semelhante ao seguinte exemplo:

```output
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

Verifique a resposta para ver se o seu fornecedor de conectividade está listado. Tome nota das seguintes informações, que necessitará quando criar um circuito:

* Nome
* PeeringLocations
* Larguras de bandaOferecidas

Está pronto para criar um circuito ExpressRoute.

### <a name="3-create-an-expressroute-circuit"></a>3. Criar um circuito ExpressRoute

> [!IMPORTANT]
> O circuito ExpressRoute é faturado a partir do momento em que uma chave de serviço é emitida. Execute esta operação quando o fornecedor de conectividade estiver pronto para fornecer o circuito.
>
>

Se ainda não tem um grupo de recursos, tem de criar um antes de criar o circuito ExpressRoute. Pode criar um grupo de recursos executando o seguinte comando:

```azurecli-interactive
az group create -n ExpressRouteResourceGroup -l "West US"
```

O exemplo que se segue mostra como criar um circuito ExpressRoute de 200 Mbps através do Equinix em Silicon Valley. Se estiver a utilizar um fornecedor diferente e configurações diferentes, substitua essa informação quando fizer o seu pedido.

Certifique-se de que especifica o nível SKU correto e a família SKU:

* O nível SKU determina se um circuito ExpressRoute é [Local,](expressroute-faqs.md#expressroute-local)Standard ou [Premium.](expressroute-faqs.md#expressroute-premium) Pode especificar *Local,* *Standard* ou *Premium.*
* A família SKU determina o tipo de faturação. Pode especificar *os dados medidos* para um plano de dados medido e *dados ilimitados* para um plano de dados ilimitado. Pode alterar o tipo de faturação de *Dados Medidos* para *Dados Ilimitados,* mas não pode alterar o tipo de Dados *Ilimitados* para *Dados Medidos*. Um circuito *local* é *apenas Ilimitado.*


O circuito ExpressRoute é faturado a partir do momento em que uma chave de serviço é emitida. O exemplo seguinte é um pedido de uma nova chave de serviço:

```azurecli-interactive
az network express-route create --bandwidth 200 -n MyCircuit --peering-location "Silicon Valley" -g ExpressRouteResourceGroup --provider "Equinix" -l "West US" --sku-family MeteredData --sku-tier Standard
```

A resposta contém a chave de serviço.

### <a name="4-list-all-expressroute-circuits"></a>4. Listar todos os circuitos ExpressRoute

Para obter uma lista de todos os circuitos `az network express-route list` ExpressRoute que criou, corra o comando. Pode recuperar esta informação a qualquer momento utilizando este comando. Para listar todos os circuitos, faça a chamada sem parâmetros.

```azurecli-interactive
az network express-route list
```

A sua chave de serviço está listada no campo *ServiceKey* da resposta.

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
"serviceProviderProvisioningState": "NotProvisioned",
"sku": {
  "family": "UnlimitedData",
  "name": "Standard_MeteredData",
  "tier": "Standard"
},
"tags": null,
"type": "Microsoft.Network/expressRouteCircuits]
```

Pode obter descrições detalhadas de todos os parâmetros executando o comando utilizando o parâmetro '-h'.

```azurecli-interactive
az network express-route list -h
```

### <a name="5-send-the-service-key-to-your-connectivity-provider-for-provisioning"></a>5. Envie a chave de serviço ao seu fornecedor de conectividade para o fornecimento

'ServiceProviderProvisioningState' fornece informações sobre o estado atual de prestação de provisionamento do lado do prestador de serviços. O estado fornece o estado do lado da Microsoft. Para mais informações, consulte o [artigo Workflows](expressroute-workflows.md#expressroute-circuit-provisioning-states).

Quando cria um novo circuito ExpressRoute, o circuito encontra-se no seguinte estado:

```output
"serviceProviderProvisioningState": "NotProvisioned"
"circuitProvisioningState": "Enabled"
```

O circuito muda para o seguinte estado quando o fornecedor de conectividade está em processo de habilitação para si:

```output
"serviceProviderProvisioningState": "Provisioning"
"circuitProvisioningState": "Enabled"
```

Para poder utilizar um circuito ExpressRoute, deve estar no seguinte estado:

```output
"serviceProviderProvisioningState": "Provisioned"
"circuitProvisioningState": "Enabled
```

### <a name="6-periodically-check-the-status-and-the-state-of-the-circuit-key"></a>6. Verifique periodicamente o estado e o estado da chave do circuito

Verificar o estado e o estado da chave do circuito permite-lhe saber quando o seu fornecedor ativou o seu circuito. Depois de configurado o circuito, o 'ServiceProviderProvisioningState' aparece como 'Provisioned', como mostra o seguinte exemplo:

```azurecli-interactive
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
"serviceProviderProvisioningState": "NotProvisioned",
"sku": {
  "family": "UnlimitedData",
  "name": "Standard_MeteredData",
  "tier": "Standard"
},
"tags": null,
"type": "Microsoft.Network/expressRouteCircuits]
```

### <a name="7-create-your-routing-configuration"></a>7. Crie a sua configuração de encaminhamento

Para obter instruções passo a passo, consulte o artigo de configuração de [encaminhamento](howto-routing-cli.md) do circuito ExpressRoute para criar e modificar os pares de circuitos.

> [!IMPORTANT]
> Estas instruções aplicam-se apenas a circuitos que são criados com prestadores de serviços que oferecem serviços de conectividade camada 2. Se estiver a utilizar um prestador de serviços que oferece serviços geridos de camada 3 (tipicamente um VPN IP, como o MPLS), o seu fornecedor de conectividade configura e gere o encaminhamento para si.
>
>

### <a name="8-link-a-virtual-network-to-an-expressroute-circuit"></a>8. Ligue uma rede virtual a um circuito ExpressRoute

Em seguida, ligue uma rede virtual ao circuito ExpressRoute. Utilize as redes virtuais de ligação ao artigo dos [circuitos ExpressRoute.](howto-linkvnet-cli.md)

## <a name="modifying-an-expressroute-circuit"></a><a name="modify"></a>Modificar um circuito ExpressRoute

Pode modificar certas propriedades de um circuito ExpressRoute sem afetar a conectividade. Pode efazer as seguintes alterações sem tempo de inatividade:

* Pode ativar ou desativar um complemento premium ExpressRoute para o seu circuito ExpressRoute.
* Pode aumentar a largura de banda do seu circuito ExpressRoute desde que haja capacidade disponível na porta. No entanto, a degradação da largura de banda de um circuito não é suportada.
* Pode alterar o plano de medição de Dados Medidos para Dados Ilimitados. No entanto, a alteração do plano de medição de Dados Ilimitados para Dados Medidos não é suportada.
* Pode ativar e desativar *permitir operações clássicas.*

Para obter mais informações sobre limites e limitações, consulte o [ExpressRoute FAQ](expressroute-faqs.md).

### <a name="to-enable-the-expressroute-premium-add-on"></a>Para ativar o add-on premium ExpressRoute

Pode ativar o complemento premium ExpressRoute para o seu circuito existente utilizando o seguinte comando:

```azurecli-interactive
az network express-route update -n MyCircuit -g ExpressRouteResourceGroup --sku-tier Premium
```

O circuito tem agora as funcionalidades de complemento premium ExpressRoute ativadas. Começamos a cobrar-lhe a capacidade de addon premium assim que o comando tiver executado com sucesso.

### <a name="to-disable-the-expressroute-premium-add-on"></a>Para desativar o add-on premium ExpressRoute

> [!IMPORTANT]
> Esta operação pode falhar se estiver a utilizar recursos superiores aos permitidos para o circuito padrão.
>
>

Antes de desativar o complemento premium ExpressRoute, compreenda os seguintes critérios:

* Antes de desvalorizar de prémio para standard, deve certificar-se de que tem menos de 10 redes virtuais ligadas ao circuito. Se tiver mais de 10, o seu pedido de atualização falha, e nós o contamos a preços premium.
* É preciso desligar todas as redes virtuais de outras regiões geopolíticas. Se não desligatodas as suas redes virtuais falhar e cobrar-lhe tarifas premium.
* A sua mesa de rotas deve ser inferior a 4.000 rotas para o peering privado. Se o tamanho da tabela de rotas for superior a 4.000 rotas, a sessão do BGP cai. A sessão não será reativada até que o número de prefixos anunciados seja inferior a 4.000.

Pode desativar o complemento premium ExpressRoute para o circuito existente utilizando o seguinte exemplo:

```azurecli-interactive
az network express-route update -n MyCircuit -g ExpressRouteResourceGroup --sku-tier Standard
```

### <a name="to-update-the-expressroute-circuit-bandwidth"></a>Para atualizar a largura de banda do circuito ExpressRoute

Para obter as opções de largura de banda suportadas para o seu fornecedor, verifique o [ExpressRoute FAQ](expressroute-faqs.md). Pode escolher qualquer tamanho maior do que o tamanho do seu circuito existente.

> [!IMPORTANT]
> Se houver uma capacidade inadequada na porta existente, poderá ter de recriar o circuito ExpressRoute. Não é possível atualizar o circuito se não houver capacidade adicional disponível nesse local.
>
> Não é possível reduzir a largura de banda de um circuito ExpressRoute sem interrupções. A degradação da largura de banda requer que você desprovisione o circuito ExpressRoute e, em seguida, reabastecer um novo circuito ExpressRoute.
>

Depois de decidir o tamanho de que necessita, utilize o seguinte comando para redimensionar o seu circuito:

```azurecli-interactive
az network express-route update -n MyCircuit -g ExpressRouteResourceGroup --bandwidth 1000
```

O seu circuito está dimensionado no lado da Microsoft. Em seguida, deve contactar o seu fornecedor de conectividade para atualizar as configurações do seu lado para corresponder a esta alteração. Depois de fazer esta notificação, começamos a cobrar-lhe a opção de largura de banda atualizada.

### <a name="to-move-the-sku-from-metered-to-unlimited"></a>Para mover o SKU de medido para ilimitado

Pode alterar o SKU de um circuito ExpressRoute utilizando o seguinte exemplo:

```azurecli-interactive
az network express-route update -n MyCircuit -g ExpressRouteResourceGroup --sku-family UnlimitedData
```

### <a name="to-control-access-to-the-classic-and-resource-manager-environments"></a>Para controlar o acesso aos ambientes clássicos e gestores de recursos

Reveja as instruções nos [circuitos Move ExpressRoute do clássico para o modelo](expressroute-howto-move-arm.md)de implementação do Gestor de Recursos .

## <a name="deprovisioning-and-deleting-an-expressroute-circuit"></a><a name="delete"></a>Deprovisioning and deleting an ExpressRoute circuit (Desaprovisionar e eliminar um circuito do ExpressRoute)

Para desprovisionar e eliminar um circuito ExpressRoute, certifique-se de que compreende os seguintes critérios:

* Tem de desassociar todas as redes virtuais do circuito do ExpressRoute. Se esta operação falhar, verifique se alguma rede virtual está ligada ao circuito.
* Se o estado de provisionamento do prestador de serviços do circuito ExpressRoute for **Provisioning** ou **Provisioned,** deve trabalhar com o seu prestador de serviços para desprovisionar o circuito do seu lado. Continuamos a reservar recursos e a cobrar até que o prestador de serviços complete a desprovisionamento do circuito e nos notifique.
* Pode eliminar o circuito se o prestador de serviços tiver desprovisionado o circuito. Quando um circuito é desprovisionado, o estado de provisionamento do prestador de serviços é definido para **não provisionado**. Isto interrompe a faturação do circuito.

Pode eliminar o circuito ExpressRoute executando o seguinte comando:

```azurecli-interactive
az network express-route delete  -n MyCircuit -g ExpressRouteResourceGroup
```

## <a name="next-steps"></a>Passos seguintes

Depois de criar o seu circuito, certifique-se de que faz as seguintes tarefas:

* [Crie e modifique o encaminhamento para o seu circuito ExpressRoute](howto-routing-cli.md)
* [Ligue a sua rede virtual ao circuito ExpressRoute](howto-linkvnet-cli.md)
