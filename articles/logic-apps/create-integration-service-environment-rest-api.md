---
title: Criar ambientes de serviço de integração (ISEs) com Aplicações Lógicas REST API
description: Crie um ambiente de serviço de integração (ISE) utilizando as Aplicações Lógicas REST API para que possa aceder a redes virtuais Azure (VNETs) a partir de Aplicações Lógicas Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 03/11/2020
ms.openlocfilehash: 0670331d2338b4b6419ffbff1452b5fbac91029f
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/01/2020
ms.locfileid: "80478825"
---
# <a name="create-an-integration-service-environment-ise-by-using-the-logic-apps-rest-api"></a>Criar um ambiente de serviço de integração (ISE) utilizando as Aplicações Lógicas REST API

Este artigo mostra como criar um ambiente de serviço de [ *integração* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) através das Aplicações Lógicas REST API para cenários onde as suas aplicações lógicas e contas de integração precisam de acesso a uma [rede virtual Azure.](../virtual-network/virtual-networks-overview.md) Um ISE é um ambiente isolado que utiliza armazenamento dedicado e outros recursos que são mantidos separados do serviço "global" de Aplicações Lógicas Multi-Inquilinos. Esta separação também reduz qualquer impacto que outros inquilinos do Azure possam ter no desempenho das suas apps. Um ISE também lhe fornece os seus próprios endereços IP estáticos. Estes endereços IP são separados dos endereços IP estáticos que são partilhados pelas aplicações lógicas no serviço público, multi-inquilino.

Também pode criar um ISE utilizando o [modelo de quickstart do Gestor](https://github.com/Azure/azure-quickstart-templates/tree/master/201-integration-service-environment) de Recursos Azure ou utilizando o portal [Azure](../logic-apps/connect-virtual-network-vnet-isolated-environment.md).

> [!IMPORTANT]
> Aplicativos lógicos, gatilhos incorporados, ações incorporadas e conectores que funcionam no seu ISE usam um plano de preços diferente do plano de preços baseado no consumo. Para saber como funcionam os preços e a faturação para os ISEs, consulte o modelo de preços das [Aplicações Lógicas.](../logic-apps/logic-apps-pricing.md#fixed-pricing) Para preços, consulte [preços de Apps Lógicas.](../logic-apps/logic-apps-pricing.md)

## <a name="prerequisites"></a>Pré-requisitos

* Os [mesmos pré-requisitos](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#prerequisites) e [requisitos para permitir o acesso ao seu ISE](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#enable-access) como quando cria um ISE no portal Azure

* Uma ferramenta que pode utilizar para criar o seu ISE, chamando as Aplicações Lógicas REST API com um pedido HTTPS PUT. Por exemplo, pode usar [o Carteiro](https://www.getpostman.com/downloads/), ou pode construir uma aplicação lógica que execute esta tarefa.

## <a name="send-the-request"></a>Enviar o pedido

Para criar o seu ISE, chamando as Aplicações Lógicas REST API, faça este pedido HTTPS PUT:

`PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Logic/integrationServiceEnvironments/{integrationServiceEnvironmentName}?api-version=2019-05-01`

> [!IMPORTANT]
> A versão Logic Apps REST API 2019-05-01 requer que faça o seu próprio pedido HTTP PUT para conectores ISE.

O destacamento geralmente leva dentro de duas horas para terminar. Ocasionalmente, o destacamento pode demorar até quatro horas. Para verificar o estado de implantação, no [portal Azure,](https://portal.azure.com)na sua barra de ferramentas Azure, selecione o ícone de notificações, que abre o painel de notificações.

> [!NOTE]
> Se a implementação falhar ou eliminar o ise, o Azure pode demorar até uma hora antes de libertar as suas subredes. Este atraso significa que pode ter de esperar antes de reutilizar as subredes noutro ISE.
>
> Se eliminar a sua rede virtual, o Azure geralmente demora até duas horas antes de libertar as suas subredes, mas esta operação pode demorar mais tempo. 
> Ao apagar redes virtuais, certifique-se de que não há recursos ainda ligados. 
> Ver [Eliminar rede virtual](../virtual-network/manage-virtual-network.md#delete-a-virtual-network).

## <a name="request-header"></a>Cabeçalho do pedido

No cabeçalho de pedido, inclua estas propriedades:

* `Content-type`: Definir este `application/json`valor de propriedade para .

* `Authorization`: Detete este valor de propriedade ao cliente que tenha acesso à subscrição do Azure ou ao grupo de recursos que pretende utilizar.

### <a name="request-body-syntax"></a>Solicitar sintaxe do corpo

Aqui está a sintaxe do corpo de pedidos, que descreve as propriedades a utilizar quando cria o seu ISE:

```json
{
   "id": "/subscriptions/{Azure-subscription-ID/resourceGroups/{Azure-resource-group}/providers/Microsoft.Logic/integrationServiceEnvironments/{ISE-name}",
   "name": "{ISE-name}",
   "type": "Microsoft.Logic/integrationServiceEnvironments",
   "location": "{Azure-region}",
   "sku": {
      "name": "Premium",
      "capacity": 1
   },
   "properties": {
      "networkConfiguration": {
         "accessEndpoint": {
            // Your ISE can use the "External" or "Internal" endpoint. This example uses "External".
            "type": "External"
         },
         "subnets": [
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-1}",
            },
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-2}",
            },
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-3}",
            },
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-4}",
            }
         ]
      }
   }
}
```

### <a name="request-body-example"></a>Solicitar exemplo de corpo

Este organismo de pedido de exemplo mostra os valores da amostra:

```json
{
   "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Logic/integrationServiceEnvironments/Fabrikam-ISE",
   "name": "Fabrikam-ISE",
   "type": "Microsoft.Logic/integrationServiceEnvironments",
   "location": "WestUS2",
   "sku": {
      "name": "Premium",
      "capacity": 1
   },
   "properties": {
      "networkConfiguration": {
         "accessEndpoint": {
            // Your ISE can use the "External" or "Internal" endpoint. This example uses "External".
            "type": "External"
         },
         "subnets": [
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-1",
            },
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-2",
            },
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-3",
            },
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-4",
            }
         ]
      }
   }
}
```

## <a name="next-steps"></a>Passos seguintes

* [Adicionar recursos aos ambientes de serviçode integração](../logic-apps/add-artifacts-integration-service-environment-ise.md)
* [Gerir ambientes do serviço de integração](../logic-apps/ise-manage-integration-service-environment.md#check-network-health)

