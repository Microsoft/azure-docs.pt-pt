---
title: 'Quickstart: Criar e modificar um circuito ExpressRoute: Azure CLI'
description: Este quickstart mostra como criar, providenciar, verificar, atualizar, eliminar e desprovisionar um circuito ExpressRoute utilizando o Azure CLI.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: quickstart
ms.date: 10/05/2020
ms.author: duau
ms.custom: devx-track-azurecli
ms.openlocfilehash: a1d50c3f8f94fbfd7dbcb9b25e051b7f2951c518
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "91969097"
---
# <a name="quickstart-create-and-modify-an-expressroute-circuit-using-azure-cli"></a>Quickstart: Criar e modificar um circuito ExpressRoute utilizando o Azure CLI

Este quickstart descreve como criar um circuito Azure ExpressRoute utilizando a Interface de Linha de Comando (CLI). Este artigo também mostra como verificar o estado, atualizar ou eliminar e desprovisionar um circuito.

## <a name="prerequisites"></a>Pré-requisitos

* Reveja os [pré-requisitos](expressroute-prerequisites.md) e [fluxos de trabalho](expressroute-workflows.md) antes de iniciar a configuração.
* Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Instale a versão mais recente dos comandos CLI (2.0 ou mais tarde). Para obter informações sobre como instalar os comandos da CLI, veja [Instalar a CLI do Azure](/cli/azure/install-azure-cli) e [Introdução à CLI do Azure](/cli/azure/get-started-with-azure-cli).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-and-provision-an-expressroute-circuit"></a><a name="create"></a>Criar e providenciar um circuito ExpressRoute

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Inscreva-se na sua conta Azure e selecione a sua subscrição

Para iniciar a sua configuração, inicie sação na sua conta Azure. Se utilizar a Cloud Shell "Try It", está assinado automaticamente. Utilize os seguintes exemplos para o ajudar a ligar:

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

### <a name="get-the-list-of-supported-providers-locations-and-bandwidths"></a>Obtenha a lista de fornecedores, locais e larguras de banda apoiadas

Antes de criar um circuito ExpressRoute, precisa da lista de fornecedores de conectividade suportados, locais e opções de largura de banda. O comando CLI `az network express-route list-service-providers` devolve esta informação, que utilizará em etapas posteriores:

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

* Name
* Localizaçãos de pares
* Largura de bandaOffered

Está agora pronto para criar um circuito ExpressRoute.

### <a name="create-an-expressroute-circuit"></a>Criar um circuito do ExpressRoute

> [!IMPORTANT]
> O seu circuito ExpressRoute é faturado a partir do momento em que uma chave de serviço é emitida. Efetuar esta operação quando o fornecedor de conectividade estiver pronto para a provisionar o circuito.
>
>

Se ainda não tem um grupo de recursos, tem de criar um antes de criar o seu circuito ExpressRoute. Pode criar um grupo de recursos executando o seguinte comando:

```azurecli-interactive
az group create -n ExpressRouteResourceGroup -l "West US"
```

O exemplo a seguir mostra como criar um circuito ExpressRoute de 200-Mbps através do Equinix em Silicon Valley. Se estiver a utilizar um fornecedor diferente e diferentes definições, substitua essa informação quando fizer o seu pedido.

Certifique-se de que especifica o nível SKU e a família SKU corretas:

* O nível SKU determina se um circuito ExpressRoute é [Local,](expressroute-faqs.md#expressroute-local)Standard ou [Premium](expressroute-faqs.md#expressroute-premium). Pode especificar *Local,**Standard ou *Premium.* Não pode mudar o SKU de *Standard/Premium* para *Local.*
* A família SKU determina o tipo de faturação. Pode especificar *o MeteredData* para um plano de dados medido e *oData Ilimitado* para um plano de dados ilimitado. Pode alterar o tipo de faturação de *MeteredData* para *UnlimitedData,* mas não pode alterar o tipo de *Data Ilimitado* para *MeteredData*. Um circuito *local* é *apenas oData Ilimitado.*


O seu circuito ExpressRoute é faturado a partir do momento em que uma chave de serviço é emitida. Segue-se um pedido de uma nova chave de serviço:

```azurecli-interactive
az network express-route create --bandwidth 200 -n MyCircuit --peering-location "Silicon Valley" -g ExpressRouteResourceGroup --provider "Equinix" -l "West US" --sku-family MeteredData --sku-tier Standard
```

A resposta contém a chave de serviço.

### <a name="list-all-expressroute-circuits"></a>Listar todos os circuitos ExpressRoute

Para obter uma lista de todos os circuitos ExpressRoute que criou, faça o `az network express-route list` comando. Pode recuperar esta informação a qualquer momento utilizando este comando. Para listar todos os circuitos, faça a chamada sem parâmetros.

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

Pode obter descrições detalhadas de todos os parâmetros executando o comando utilizando o parâmetro 'h'.

```azurecli-interactive
az network express-route list -h
```

### <a name="send-the-service-key-to-your-connectivity-provider-for-provisioning"></a>Envie a chave de serviço para o seu fornecedor de conectividade para provisionamento

O 'ServiceProviderProvisioningState' fornece informações sobre o estado atual do fornecimento do lado do prestador de serviços. O estado fornece o estado do lado da Microsoft. Para mais informações, consulte o [artigo Workflows](expressroute-workflows.md#expressroute-circuit-provisioning-states).

Quando se cria um novo circuito ExpressRoute, o circuito encontra-se no seguinte estado:

```output
"serviceProviderProvisioningState": "NotProvisioned"
"circuitProvisioningState": "Enabled"
```

O circuito muda para o seguinte estado quando o fornecedor de conectividade está atualmente a permitir-lhe:

```output
"serviceProviderProvisioningState": "Provisioning"
"circuitProvisioningState": "Enabled"
```

Para utilizar o circuito ExpressRoute, deve estar no seguinte estado:

```output
"serviceProviderProvisioningState": "Provisioned"
"circuitProvisioningState": "Enabled
```

### <a name="periodically-check-the-status-and-the-state-of-the-circuit-key"></a>Verifique periodicamente o estado e o estado da chave do circuito

A verificação do estado e do estado da chave de serviço informá-lo-á quando o seu fornecedor tiver provisionado o seu circuito. Após a configuração do circuito, o *ServiceProviderProvisioningState* aparece como *Provisionado,* como mostra o seguinte exemplo:

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

### <a name="create-your-routing-configuration"></a>Crie a sua configuração de encaminhamento

Para obter instruções passo a passo, consulte o artigo de configuração do [roteamento do circuito ExpressRoute](howto-routing-cli.md) para criar e modificar os pares de circuitos.

> [!IMPORTANT]
> Estas instruções aplicam-se apenas aos circuitos criados com prestadores de serviços que oferecem serviços de conectividade de camada 2. Se estiver a utilizar um prestador de serviços que oferece serviços geridos de camada 3 (normalmente um IP VPN, como MPLS), o seu fornecedor de conectividade configura e gere o encaminhamento para si.
>
>

### <a name="link-a-virtual-network-to-an-expressroute-circuit"></a>Ligar uma rede virtual a um circuito ExpressRoute

Em seguida, ligue uma rede virtual ao seu circuito ExpressRoute. Utilize as redes virtuais de ligação ao artigo [dos circuitos ExpressRoute.](howto-linkvnet-cli.md)

## <a name="modifying-an-expressroute-circuit"></a><a name="modify"></a>Modificação de um circuito ExpressRoute

Pode modificar certas propriedades de um circuito ExpressRoute sem afetar a conectividade. Pode es fazer as seguintes alterações sem tempo de inatividade:

* Pode ativar ou desativar um addon premium ExpressRoute para o seu circuito ExpressRoute. Mudar o SKU de *Standard/Premium* para *Local* não é suportado.
* Pode aumentar a largura de banda do seu circuito ExpressRoute desde que haja capacidade disponível na porta. No entanto, a redução da largura de banda de um circuito não é suportada.
* Pode alterar o plano de medição de Dados Medidos para Dados Ilimitados. No entanto, a alteração do plano de medição de Dados Ilimitados para Dados Medidos não é suportada.
* Pode ativar e desativar *permitir operações clássicas.*

Para obter mais informações sobre limites e limitações, consulte as [FAQ expressRoute.](expressroute-faqs.md)

### <a name="to-enable-the-expressroute-premium-add-on"></a>Para ativar o complemento premium ExpressRoute

Pode ativar o addon premium ExpressRoute para o circuito existente utilizando o seguinte comando:

```azurecli-interactive
az network express-route update -n MyCircuit -g ExpressRouteResourceGroup --sku-tier Premium
```

O circuito tem agora as funcionalidades adicionais premium ExpressRoute ativadas. Começamos a cobrar-lhe a capacidade de adicionar premium assim que o comando tiver sido executado com sucesso.

### <a name="to-disable-the-expressroute-premium-add-on"></a>Para desativar o complemento premium ExpressRoute

> [!IMPORTANT]
> Esta operação pode falhar se estiver a utilizar recursos superiores ao permitido para o circuito padrão.
>
>

Antes de desativar o complemento premium ExpressRoute, compreenda os seguintes critérios:

* Antes de baixar de prémio para padrão, deve garantir que o número de redes virtuais ligadas ao circuito seja inferior a 10. Se não o fizer, o seu pedido de atualização falha e cobramos-lhe taxas premium.
* Todas as redes virtuais de outras regiões geopolíticas devem ser inicialmente desvinculadas. Se não remover o link, o seu pedido de atualização falhará e continuaremos a cobrar-lhe tarifas premium.
* Sua mesa de rota deve ser menos de 4.000 rotas para espreitar privados. Se o tamanho da tabela de rotas for superior a 4.000 rotas, a sessão de BGP diminuirá. A sessão de BGP não será re habilitada até que o número de prefixos anunciados seja inferior a 4.000.

Pode desativar o complemento premium ExpressRoute para o circuito existente utilizando o seguinte exemplo:

```azurecli-interactive
az network express-route update -n MyCircuit -g ExpressRouteResourceGroup --sku-tier Standard
```

### <a name="to-update-the-expressroute-circuit-bandwidth"></a>Para atualizar a largura de banda do circuito ExpressRoute

Para obter as opções de largura de banda suportadas para o seu fornecedor, consulte as [FAQ ExpressRoute](expressroute-faqs.md). Pode escolher qualquer tamanho maior do que o tamanho do seu circuito existente.

> [!IMPORTANT]
> Se não houver capacidade inadequada na porta existente, poderá ter de recriar o circuito ExpressRoute. Não é possível atualizar o circuito se não houver capacidade adicional disponível nesse local.
>
> Não é possível reduzir a largura de banda de um circuito ExpressRoute sem interrupções. A redução da largura de banda requer que desprovisione o circuito ExpressRoute e, em seguida, reprovisione um novo circuito ExpressRoute.
>

Depois de decidir o tamanho necessário, use o seguinte comando para redimensionar o seu circuito:

```azurecli-interactive
az network express-route update -n MyCircuit -g ExpressRouteResourceGroup --bandwidth 1000
```

O seu circuito será atualizado do lado da Microsoft. Em seguida, deve contactar o seu fornecedor de conectividade para atualizar as configurações do seu lado para corresponder a esta alteração. Depois de fazer esta notificação, começamos a cobrar-lhe a opção de largura de banda atualizada.

### <a name="to-move-the-sku-from-metered-to-unlimited"></a>Para mover o SKU de medido para ilimitado

Pode alterar o SKU de um circuito ExpressRoute utilizando o seguinte exemplo:

```azurecli-interactive
az network express-route update -n MyCircuit -g ExpressRouteResourceGroup --sku-family UnlimitedData
```

### <a name="to-control-access-to-the-classic-and-resource-manager-environments"></a>Para controlar o acesso aos ambientes clássicos e gestores de recursos

Reveja as instruções nos [circuitos Move ExpressRoute do clássico para o modelo de implementação do Gestor de Recursos](expressroute-howto-move-arm.md).

## <a name="deprovisioning-an-expressroute-circuit"></a><a name="delete"></a>Desprovisionamento de um circuito ExpressRoute

Para desprovisionar e eliminar um circuito ExpressRoute, certifique-se de que compreende os seguintes critérios:

* Todas as redes virtuais devem ser desligadas do circuito ExpressRoute. Se esta operação falhar, verifique se alguma rede virtual está ligada ao circuito.
* Se o estado de prestação do serviço de circuito ExpressRoute for **Provisioning** ou **Provisioned,** deve trabalhar com o seu prestador de serviços para desprovisionar o circuito do seu lado. Continuamos a reservar recursos e a cobrar até que o prestador de serviços complete a desprovisionamento do circuito e nos notifique.
* Se o prestador de serviços tiver desprovisionado o circuito, o que significa que o estado de fornecimento do prestador de serviços é definido como **Não provisionado,** pode eliminar o circuito. A faturação do circuito vai parar.

## <a name="clean-up-resources"></a>Limpar os recursos

Pode eliminar o seu circuito ExpressRoute executando o seguinte comando:

```azurecli-interactive
az network express-route delete  -n MyCircuit -g ExpressRouteResourceGroup
```

## <a name="next-steps"></a>Passos seguintes

Depois de criar o seu circuito e foreá-lo com o seu fornecedor, continue até ao passo seguinte para configurar o espreitamento:

> [!div class="nextstepaction"]
> [Crie e modifique o encaminhamento para o seu circuito ExpressRoute](howto-routing-cli.md)
