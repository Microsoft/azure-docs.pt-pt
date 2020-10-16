---
title: Criar ambientes de serviço de integração (ISEs) com Apps Lógicas REST API
description: Crie um ambiente de serviço de integração (ISE) utilizando as Aplicações Lógicas REST API para que possa aceder a redes virtuais Azure (VNETs) a partir de Apps Azure Logic
services: logic-apps
ms.suite: integration
ms.reviewer: rarayudu, logicappspm
ms.topic: conceptual
ms.date: 05/29/2020
ms.openlocfilehash: 427b488fe6673bef505fccdaa7185d69437bceaf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89231321"
---
# <a name="create-an-integration-service-environment-ise-by-using-the-logic-apps-rest-api"></a>Criar um ambiente de serviço de integração (ISE) utilizando a API de Apps Lógicas REST

Este artigo mostra como criar um ambiente de serviço de [ *integração* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) através da Logic Apps REST API para cenários onde as suas aplicações lógicas e contas de integração precisam de acesso a uma [rede virtual Azure.](../virtual-network/virtual-networks-overview.md) O ISE é um ambiente dedicado que utiliza o armazenamento dedicado e outros recursos que são mantidos separados do serviço Logic Apps multi-inquilino “global”. Esta separação também reduz qualquer impacto que outros inquilinos do Azure possam ter no desempenho das suas apps. Um ISE também lhe proporciona os seus próprios endereços IP estáticos. Estes endereços IP são separados dos endereços IP estáticos que são partilhados pelas aplicações lógicas no serviço público, multi-inquilino.

Também pode criar um ISE utilizando o modelo de arranque rápido do [Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/201-integration-service-environment) da amostra ou utilizando o [portal Azure](../logic-apps/connect-virtual-network-vnet-isolated-environment.md).

> [!IMPORTANT]
> Aplicações lógicas, gatilhos incorporados, ações incorporadas e conectores que funcionam no seu ISE usam um plano de preços diferente do plano de preços baseado no consumo. Para aprender como os preços e a faturação funcionam para as ISEs, consulte o [modelo de preços de Aplicações Lógicas.](../logic-apps/logic-apps-pricing.md#fixed-pricing) Para taxas de preços, consulte [os preços das Aplicações Lógicas.](../logic-apps/logic-apps-pricing.md)

## <a name="prerequisites"></a>Pré-requisitos

* Os [mesmos pré-requisitos](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#prerequisites) e [requisitos para permitir o acesso ao seu ISE](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#enable-access) como quando cria um ISE no portal Azure

* Uma ferramenta que pode utilizar para criar o seu ISE, chamando a API de Apps Lógicas REST com um pedido HTTPS PUT. Por exemplo, pode usar [o Carteiro,](https://www.getpostman.com/downloads/)ou pode construir uma aplicação lógica que executa esta tarefa.

## <a name="send-the-request"></a>Enviar o pedido

Para criar o seu ISE chamando a API de Apps Lógicas, faça este pedido HTTPS PUT:

`PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Logic/integrationServiceEnvironments/{integrationServiceEnvironmentName}?api-version=2019-05-01`

> [!IMPORTANT]
> A versão Logic Apps REST API 2019-05-01 requer que faça o seu próprio pedido HTTP PUT para conectores ISE.

A implantação geralmente demora dentro de duas horas para terminar. Ocasionalmente, a implantação pode demorar até quatro horas. Para verificar o estado de implementação, no [portal Azure,](https://portal.azure.com)na sua barra de ferramentas Azure, selecione o ícone de notificações, que abre o painel de notificações.

> [!NOTE]
> Se a implementação falhar ou eliminar o ISE, o Azure pode demorar até uma hora antes de lançar as suas sub-redes. Este atraso significa que pode ter de esperar antes de reutilizar as sub-redes noutra ISE.
>
> Se eliminar a sua rede virtual, o Azure geralmente demora até duas horas antes de lançar as suas sub-redes, mas esta operação pode demorar mais tempo. 
> Ao eliminar redes virtuais, certifique-se de que ainda não há recursos ligados. 
> Ver [Eliminar rede virtual](../virtual-network/manage-virtual-network.md#delete-a-virtual-network).

## <a name="request-header"></a>Cabeçalho do pedido

No cabeçalho de pedido, inclua estas propriedades:

* `Content-type`: Desa esta propriedade valor para `application/json` .

* `Authorization`: Desaprova este valor de propriedade ao símbolo do portador para o cliente que tenha acesso à subscrição ou grupo de recursos Azure que pretende utilizar.

<a name="request-body"></a>

## <a name="request-body"></a>Corpo do pedido

Aqui está a sintaxe corporal de pedido, que descreve as propriedades a utilizar quando cria o seu ISE. Para criar um ISE que permita usar um certificado auto-assinado que esteja instalado no `TrustedRoot` local, inclua o `certificates` objeto dentro da secção de definição ise. `properties` Para um ISE existente, pode enviar um pedido PATCH apenas para o `certificates` objeto. Para obter mais informações sobre a utilização de certificados auto-assinados, consulte [acesso seguro e dados - Acesso a chamadas de saída para outros serviços e sistemas](../logic-apps/logic-apps-securing-a-logic-app.md#secure-outbound-requests).

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
      },
      // Include `certificates` object to enable self-signed certificate support
      "certificates": {
         "testCertificate": {
            "publicCertificate": "{base64-encoded-certificate}",
            "kind": "TrustedRoot"
         }
      }
   }
}
```

### <a name="request-body-example"></a>Solicitar exemplo corporal

Este corpo de pedido de exemplo mostra os valores da amostra:

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
      },
      "certificates": {
         "testCertificate": {
            "publicCertificate": "LS0tLS1CRUdJTiBDRV...",
            "kind": "TrustedRoot"
         }
   }
}
```

## <a name="next-steps"></a>Passos seguintes

* [Adicionar recursos a ambientes de serviço de integração](../logic-apps/add-artifacts-integration-service-environment-ise.md)
* [Gerir ambientes do serviço de integração](../logic-apps/ise-manage-integration-service-environment.md#check-network-health)

