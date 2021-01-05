---
title: Criar ambientes de serviço de integração (ISEs) com Apps Lógicas REST API
description: Crie um ambiente de serviço de integração (ISE) utilizando as Aplicações Lógicas REST API para que possa aceder a redes virtuais Azure (VNETs) a partir de Apps Azure Logic
services: logic-apps
ms.suite: integration
ms.reviewer: rarayudu, logicappspm
ms.topic: conceptual
ms.date: 12/29/2020
ms.openlocfilehash: 34a5dfb44ee78245b56c1774701f48b3b8a494df
ms.sourcegitcommit: 42922af070f7edf3639a79b1a60565d90bb801c0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/31/2020
ms.locfileid: "97827483"
---
# <a name="create-an-integration-service-environment-ise-by-using-the-logic-apps-rest-api"></a>Criar um ambiente de serviço de integração (ISE) com a API REST do Logic Apps

Para cenários em que as suas aplicações lógicas e contas de integração precisam de acesso a uma [rede virtual Azure,](../virtual-network/virtual-networks-overview.md)pode criar um [ *ambiente de serviço de integração* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) utilizando a API de Apps Lógicas REST. Para saber mais sobre ises, consulte os [recursos da Rede Virtual Azure a partir de Aplicações Azure Logic.](connect-virtual-network-vnet-isolated-environment-overview.md)

Este artigo mostra-lhe como criar um ISE utilizando a API de Apps Lógicas EM Geral. Opcionalmente, também pode ativar uma [identidade gerida atribuída ao sistema ou atribuída ao utilizador](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) no seu ISE, mas apenas utilizando a API de Apps Lógicas REST neste momento. Esta identidade permite que o seu ISE autente o acesso a recursos seguros, como máquinas virtuais e outros sistemas ou serviços, que se encontrem dentro ou ligados a uma rede virtual Azure. Assim, não tens de assinar com as tuas credenciais.

Para obter mais informações sobre outras formas de criar um ISE, consulte estes artigos:

* [Criar um ISE utilizando o portal Azure](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)
* [Crie um ISE utilizando o modelo de arranque rápido do Azure Resource Manager da amostra](https://github.com/Azure/azure-quickstart-templates/tree/master/201-integration-service-environment)
* [Crie um ISE que suporte usando chaves geridas pelo cliente para encriptar dados em repouso](customer-managed-keys-integration-service-environment.md)

## <a name="prerequisites"></a>Pré-requisitos

* Os [mesmos pré-requisitos](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#prerequisites) e [requisitos](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#enable-access) de acesso como quando cria um ISE no portal Azure

* Quaisquer recursos adicionais que queira utilizar com o seu ISE para que possa incluir as suas informações na definição ISE, por exemplo: 

  * Para ativar o suporte de certificado auto-assinado, você precisa incluir informações sobre esse certificado na definição ISE.

  * Para ativar a identidade gerida atribuída pelo utilizador, é necessário criar essa identidade com antecedência e incluir os `objectId` , e propriedades e seus `principalId` `clientId` valores na definição ISE. Para obter mais informações, consulte [Criar uma identidade gerida atribuída ao utilizador no portal Azure](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity).

* Uma ferramenta que pode utilizar para criar o seu ISE, chamando a API de Apps Lógicas REST com um pedido HTTPS PUT. Por exemplo, pode usar [o Carteiro,](https://www.getpostman.com/downloads/)ou pode construir uma aplicação lógica que executa esta tarefa.

## <a name="create-the-ise"></a>Criar o ISE

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

No organismo de pedido, forneça a definição de recurso a utilizar para a criação do seu ISE, incluindo informações para capacidades adicionais que pretende ativar no seu ISE, por exemplo:

* Para criar um ISE que permita a utilização de um certificado e certificado auto-assinado emitido pela Enterprise Certificate Authority que está instalado no `TrustedRoot` local, inclua o `certificates` objeto dentro da secção de definição ise, como este artigo descreve mais `properties` tarde.

* Para criar um ISE que utilize uma identidade gerida atribuída ao sistema ou atribuída ao utilizador, inclua o `identity` objeto com o tipo de identidade gerido e outras informações necessárias na definição ISE, como este artigo descreve mais tarde.

* Para criar um ISE que utilize chaves geridas pelo cliente e Azure Key Vault para encriptar dados em repouso, inclua a [informação que permite](customer-managed-keys-integration-service-environment.md)o suporte de chave gerido pelo cliente . Só pode configurar chaves geridas pelo cliente *na criação,* não depois.

### <a name="request-body-syntax"></a>Solicitar sintaxe corporal

Aqui está a sintaxe corporal de pedido, que descreve as propriedades a utilizar quando cria o seu ISE:

```json
{
   "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Logic/integrationServiceEnvironments/{ISE-name}",
   "name": "{ISE-name}",
   "type": "Microsoft.Logic/integrationServiceEnvironments",
   "location": "{Azure-region}",
   "sku": {
      "name": "Premium",
      "capacity": 1
   },
   // Include the `identity` object to enable the system-assigned identity or user-assigned identity
   "identity": {
      "type": <"SystemAssigned" | "UserAssigned">,
      // When type is "UserAssigned", include the following "userAssignedIdentities" object:
      "userAssignedIdentities": {
         "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{user-assigned-managed-identity-object-ID}": {
            "principalId": "{principal-ID}",
            "clientId": "{client-ID}"
         }
      }
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
      // Include `certificates` object to enable self-signed certiificate and certificate issued by Enterprise Certificate Authority
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
   "identity": {
      "type": "UserAssigned",
      "userAssignedIdentities": {
         "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.ManagedIdentity/userAssignedIdentities/*********************************": {
            "principalId": "*********************************",
            "clientId": "*********************************"
         }
      }
   },
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
}
```
## <a name="add-custom-root-certificates"></a>Adicionar certificados de raiz personalizados

Você usa frequentemente um ISE para ligar a serviços personalizados na sua rede virtual ou no local. Estes serviços personalizados são muitas vezes protegidos por um certificado emitido pela autoridade de certificados de raiz personalizado, como uma Autoridade de Certificados empresariais ou um certificado auto-assinado. Para obter mais informações sobre a utilização de certificados auto-assinados, consulte [acesso seguro e dados - Acesso a chamadas de saída para outros serviços e sistemas](../logic-apps/logic-apps-securing-a-logic-app.md#secure-outbound-requests). Para que o seu ISE se conecte com sucesso a estes serviços através da Transport Layer Security (TLS), o seu ISE precisa de acesso a estes certificados de raiz. Para atualizar o seu ISE com um certificado de raiz de confiança personalizado, faça este `PATCH` pedido HTTPS:

`PATCH https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Logic/integrationServiceEnvironments/{integrationServiceEnvironmentName}?api-version=2019-05-01`

Antes de realizar esta operação, reveja estas considerações:

* Certifique-se de que faz o upload do certificado de raiz *e* de todos os certificados intermédios. O número máximo de certificados é de 20.

* O upload de certificados de raiz é uma operação de substituição onde o último upload substitui os uploads anteriores. Por exemplo, se enviar um pedido que faz o upload de um certificado e, em seguida, enviar outro pedido para enviar outro certificado, o seu ISE utiliza apenas o segundo certificado. Se precisar de utilizar ambos os certificados, adicione-os no mesmo pedido.  

* Carregar certificados de raiz é uma operação assíncronea que pode levar algum tempo. Para verificar o estado ou o resultado, pode enviar um `GET` pedido utilizando o mesmo URI. A mensagem de resposta tem um `provisioningState` campo que devolve o valor quando a `InProgress` operação de upload ainda está a funcionar. Quando `provisioningState` o valor `Succeeded` é, a operação de upload está completa.

#### <a name="request-body-syntax-for-adding-custom-root-certificates"></a>Solicite sintaxe corporal para adicionar certificados de raiz personalizados

Aqui está a sintaxe corporal do pedido, que descreve as propriedades a utilizar quando adiciona certificados de raiz:

```json
{
   "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Logic/integrationServiceEnvironments/{ISE-name}",
   "name": "{ISE-name}",
   "type": "Microsoft.Logic/integrationServiceEnvironments",
   "location": "{Azure-region}",
   "properties": {
      "certificates": {
         "testCertificate1": {
            "publicCertificate": "{base64-encoded-certificate}",
            "kind": "TrustedRoot"
         },
         "testCertificate2": {
            "publicCertificate": "{base64-encoded-certificate}",
            "kind": "TrustedRoot"
         }
      }
   }
}
```

## <a name="next-steps"></a>Passos seguintes

* [Adicionar recursos a ambientes de serviço de integração](../logic-apps/add-artifacts-integration-service-environment-ise.md)
* [Gerir ambientes do serviço de integração](../logic-apps/ise-manage-integration-service-environment.md#check-network-health)
