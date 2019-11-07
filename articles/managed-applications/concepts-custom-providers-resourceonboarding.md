---
title: Integração de recursos de provedores personalizados do Azure
description: Saiba mais sobre a integração de recursos usando os provedores personalizados do Azure para aplicar o gerenciamento ou a configuração a outros tipos de recursos do Azure.
author: jjbfour
ms.service: managed-applications
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: jobreen
ms.openlocfilehash: 6f9dcf4118dd2167f4cef35408d5ead79bf33877
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/05/2019
ms.locfileid: "73609154"
---
# <a name="azure-custom-providers-resource-onboarding-overview"></a>Visão geral da integração de recursos de provedores personalizados do Azure

A integração de recursos de provedores personalizados do Azure é um modelo de extensibilidade para tipos de recursos do Azure. Ele permite que você aplique operações ou gerenciamento entre recursos existentes do Azure em escala. Para obter mais informações, consulte [como os provedores personalizados do Azure podem estender o Azure](./custom-providers-overview.md). Esta documentação descreve:

- O que a integração de recursos pode fazer.
- Noções básicas de integração de recursos e como usá-lo.
- Onde encontrar guias e exemplos de código para começar.

> [!IMPORTANT]
> Os provedores personalizados estão atualmente em visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.
> Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="what-can-resource-onboarding-do"></a>O que a integração de recursos pode fazer

Semelhante aos [recursos personalizados do provedor personalizado do Azure](./custom-providers-resources-endpoint-how-to.md), a integração de recursos define um contrato, que fará o proxy de solicitações de "integração" a um ponto de extremidade. Ao contrário dos recursos personalizados, a integração de recursos não cria um novo tipo de recurso, mas, em vez disso, permite a extensão de tipos de recursos existentes. Além disso, a integração de recursos funciona com o Azure Policy, portanto, o gerenciamento e a configuração de recursos podem ser feitos em escala. Alguns exemplos de fluxos de trabalho de integração de recursos:

- Instale e gerencie em extensões de máquinas virtuais.
- Carregar e configurar padrões em contas de armazenamento do Azure.
- Habilite as configurações de diagnóstico de linha de base em escala.

## <a name="resource-onboarding-basics"></a>Noções básicas de integração de recursos

A integração de recursos é configurada por meio de provedores personalizados do Azure usando os tipos de recursos "Microsoft. CustomProviders/resourceProviders" e "Microsoft. CustomProviders/Associations". Para habilitar a integração de recursos para um provedor personalizado, durante o processo de configuração, crie um **ResourceType** chamado "associações" com um **RoutingType** que inclua "extensão". Além disso, o "Microsoft. CustomProviders/Associations" e o "Microsoft. CustomProviders/resourceProviders" não precisam pertencer ao mesmo grupo de recursos.

Provedor personalizado de exemplo do Azure:

```JSON
{
  "properties": {
    "resourceTypes": [
      {
        "name": "associations",
        "routingType": "Proxy,Cache,Extension",
        "endpoint": "https://microsoft.com/"
      }
    ]
  },
  "location": "eastus"
}
```

Propriedade | Necessário | Descrição
---|---|---
nome | *Ok* | O nome da definição do ponto de extremidade. Para a integração de recursos, o nome deve ser "associações".
roteamentotype | *Ok* | Determina o tipo de contrato com o **ponto de extremidade**. Para integração de recursos, os **routingTypes** válidos são "proxy, cache, extensão" e "webhook, cache, extensão".
endpoint | *Ok* | O ponto de extremidade para o qual rotear as solicitações. Isso tratará a resposta, bem como quaisquer efeitos colaterais da solicitação.

Depois que o provedor personalizado com o tipo de recurso "associações" for criado, você poderá direcionar usando "Microsoft. CustomProviders/Associations". "Microsoft. CustomProviders/Associations" é um recurso de extensão que pode estender qualquer outro recurso do Azure. Quando uma instância de "Microsoft. CustomProviders/Associations" for criada, ela usará uma propriedade **targetResourceId**, que deve ser uma ID de recurso válida de "Microsoft. CustomProviders/resourceProviders" ou "Microsoft. Solutions/Applications". Nesses casos, a solicitação será encaminhada para o tipo de recurso "associações" na instância "Microsoft. CustomProviders/resourceProviders" que criamos.

> [!Note]
> Se uma ID de recurso "Microsoft. Solutions/Applications" for fornecida como o **targetResourceId**, deverá haver um "Microsoft. CustomProviders/resourceProviders" implantado no grupo de recursos gerenciados com o nome "Public".

Exemplo de associação de provedor personalizado do Azure:

```JSON
{
  "properties": {
    "targetResourceId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}",
    ...
  }
}
```

Propriedade | Necessário | Descrição
---|---|---
TargetResourceId | *Ok* | A ID de recurso do "Microsoft. CustomProviders/resourceProviders" ou "Microsoft. Solutions/Applications".

## <a name="how-to-use-resource-onboarding"></a>Como usar a integração de recursos

A integração de recursos funciona estendendo outros recursos com o recurso de extensão "Microsoft. CustomProviders/Associations". No exemplo a seguir, a solicitação será feita para uma máquina virtual, mas qualquer recurso poderá ser estendido.

Primeiro, um recurso de provedor personalizado com um tipo de recurso de "associações" deve ser criado. Isso irá declarar a URL de retorno de chamada que será usada quando um recurso "Microsoft. CustomProviders/Associations" correspondente for criado, o qual se destina ao provedor personalizado.

Solicitação de criação de "Microsoft. CustomProviders/resourceProviders" de exemplo:

``` HTTP
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json

{
  "properties": {
    "resourceTypes": [
      {
        "name": "associations",
        "routingType": "Proxy,Cache,Extension",
        "endpoint": "https://{myCustomEndpoint}/"
      }
    ]
  },
  "location": "{location}"
}
```

Depois que o provedor personalizado é criado, agora podemos direcionar outros recursos e aplicar os efeitos colaterais do provedor personalizado a eles.

Solicitação de criação de "Microsoft. CustomProviders/Associations":

``` HTTP
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{virtualMachineName}/providers/Microsoft.CustomProviders/associations/{associationName}?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json

{
  "properties": {
    "targetResourceId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}",
    "myProperty1": "myPropertyValue1",
    "myProperty2": {
        "myProperty3" : "myPropertyValue3"
    }
  }
}
```

Essa solicitação será então encaminhada para o ponto de extremidade especificado no provedor personalizado criado inicial, que é referenciado pelo "targetResourceId" no formato:

``` HTTP
PUT https://{endpointURL}/?api-version=2018-09-01-preview
Content-Type: application/json
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/associations/{associationName}
X-MS-CustomProviders-ExtensionPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{virtualMachineName}/providers/Microsoft.CustomProviders/associations/{associationName}
X-MS-CustomProviders-ExtendedResource: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{virtualMachineName}

{
  "properties": {
    "myProperty1": "myPropertyValue1",
    "myProperty2": {
        "myProperty3" : "myPropertyValue3"
    }
  }
}
```

O ponto de extremidade deve responder com "Application/JSON" `Content-Type` e um corpo de resposta JSON válido. Os campos que são retornados no objeto "Properties" do JSON serão adicionados à resposta de retorno da associação.

## <a name="looking-for-help"></a>Procurando ajuda

Se você tiver dúvidas sobre o desenvolvimento do provedor de recursos personalizado do Azure, tente solicitar [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-custom-providers). Uma pergunta semelhante pode já ter sido solicitada e respondida, portanto, verifique primeiro antes de lançar. Adicione a marca ```azure-custom-providers``` para obter uma resposta rápida!

## <a name="next-steps"></a>Passos seguintes

Neste artigo, você aprendeu sobre provedores personalizados. Vá para o próximo artigo para criar um provedor personalizado.

- [Tutorial: integração de recursos com provedores personalizados](./tutorial-custom-providers-resource-onboarding.md)
- [Tutorial: criar ações e recursos personalizados no Azure](./tutorial-custom-providers-101.md)
- [Início rápido: criar um provedor de recursos personalizado do Azure e implantar recursos personalizados](./create-custom-provider.md)
- [Como adicionar ações personalizadas à API REST do Azure](./custom-providers-action-endpoint-how-to.md)
- [Como adicionar recursos personalizados à API REST do Azure](./custom-providers-resources-endpoint-how-to.md)
