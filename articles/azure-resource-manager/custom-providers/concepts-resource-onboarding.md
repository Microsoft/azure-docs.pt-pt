---
title: Integração de recursos
description: Aprenda a executar recursos no embarque utilizando fornecedores personalizados azure para aplicar gestão ou configuração a outros tipos de recursos Azure.
author: jjbfour
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: jobreen
ms.openlocfilehash: 1846b036f12fe7e691021ec0248782cad946d9b7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75650412"
---
# <a name="azure-custom-providers-resource-onboarding-overview"></a>Azure Custom Providers recursos sobre a visão geral do embarque

O recurso Azure Custom Providers no embarque é um modelo de extensibility para tipos de recursos Azure. Permite-lhe aplicar operações ou gestão em todos os recursos azure existentes em escala. Para mais informações, consulte como os [fornecedores personalizados azure podem estender o Azure](overview.md). Este artigo descreve:

- Que recurso pode fazer.
- Recursos sobre o básico de embarque e como usá-lo.
- Onde encontrar guias e amostras de código para começar.

> [!IMPORTANT]
> Os Fornecedores Personalizados estão atualmente em pré-visualização pública.
> Esta versão de pré-visualização é fornecida sem um acordo de nível de serviço, e não a recomendamos para cargas de trabalho de produção. Certas funcionalidades podem não ser suportadas ou ter capacidades limitadas.
> Para mais informações, consulte [os Termos Suplementares de Utilização para pré-visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)do Microsoft Azure .

## <a name="what-can-resource-onboarding-do"></a>O que pode o recurso no embarque fazer?

Semelhante aos recursos personalizados da [Azure Custom Providers,](./custom-providers-resources-endpoint-how-to.md)o recurso onboarding define um contrato que irá proxy "onboarding" pedidos para um ponto final. Ao contrário dos recursos personalizados, o embarque de recursos não cria um novo tipo de recursos. Em vez disso, permite a extensão dos tipos de recursos existentes. E o recurso funciona com a Política Azure, para que a gestão e a configuração de recursos possam ser feitas em escala. Alguns exemplos de fluxos de trabalho de bordo de recursos:

- Instale e gerencie as extensões da máquina virtual.
- Faça upload e configure os incumprimentos nas contas de armazenamento do Azure.
- Ativar as definições de diagnóstico de base em escala.

## <a name="resource-onboarding-basics"></a>Fundamentos de embarque de recursos

Configura o embarque de recursos através de Fornecedores Personalizados Azure utilizando os tipos de recursos microsoft.CustomProviders/resourceProviders e Microsoft.CustomProviders/associations. Para ativar o embarque de recursos para um fornecedor personalizado, durante o processo de configuração, criar um **recursoType** chamado "associações" com um **routtype** Type que inclua "Extensão". O Microsoft.CustomProviders/associations e Microsoft.CustomProviders/resourceProviders não precisam de pertencer ao mesmo grupo de recursos.

Aqui está uma amostra do fornecedor personalizado Azure:

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

Propriedade | Necessário? | Descrição
---|---|---
nome | Sim | O nome da definição de ponto final. Para o recurso onboarding, o nome deve ser "associações".
routingType | Sim | Determina o tipo de contrato com o ponto final. Para o embarque de recursos, os tipos de **encaminhamento** válidos são "Proxy,Cache, Extension" e "Webhook,Cache,Extension".
endpoint | Sim | O ponto final para encaminhar os pedidos para. Isto tratará da resposta e dos efeitos secundários do pedido.

Depois de criar o fornecedor personalizado com o tipo de recursos das associações, pode direcionar-se para o uso da Microsoft.CustomProviders/associações. Microsoft.CustomProviders/associações é um recurso de extensão que pode estender qualquer outro recurso Azure. Quando for criada uma instância da Microsoft.CustomProviders/associações, será preciso um target de propriedade **ResourceId**, que deve ser um Microsoft.CustomProviders/resourceProviders ou Microsoft.Solutions/applications resource ID. Nestes casos, o pedido será encaminhado para o tipo de recursos das associações na instância Microsoft.CustomProviders/resourceProviders que criou.

> [!NOTE]
> Se um ID de recursos Microsoft.Solutions/applications for fornecido como o **targetResourceId,** deve haver um Microsoft.CustomProviders/resourceProviders implantados no grupo de recursos geridos com o nome "público".

Associação de Fornecedores Personalizados Da Amostra Azure:

```JSON
{
  "properties": {
    "targetResourceId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}",
    ...
  }
}
```

Propriedade | Necessário? | Descrição
---|---|---
targetResourceId | Sim | O ID de recursos do Microsoft.CustomProviders/resourceProviders ou Microsoft.Solutions/applications.

## <a name="how-to-use-resource-onboarding"></a>Como utilizar recursos no embarque

O recurso no embarque funciona alargando outros recursos com o recurso de extensão Microsoft.CustomProviders/associações. Na amostra seguinte, o pedido é feito para uma máquina virtual, mas qualquer recurso pode ser estendido.

Em primeiro lugar, é necessário criar um recurso personalizado para o fornecedor com um tipo de recurso de associações. Isto irá declarar o URL de callback que será usado quando for criado um recurso correspondente da Microsoft.CustomProviders/associações, que visa o fornecedor personalizado.

Sample Microsoft.CustomProviders/resourceProviders criam pedido:

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

Depois de criar o fornecedor personalizado, pode direcionar outros recursos e aplicar os efeitos colaterais do fornecedor personalizado.

Sample Microsoft.CustomProviders/associações criam pedido:

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

Este pedido será então encaminhado para o ponto final especificado no fornecedor personalizado que criou, que é referenciado pelo **targetResourceId** neste formulário:

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

O ponto final deve responder com `Content-Type` uma aplicação/json e um corpo de resposta JSON válido. Os campos que forem devolvidos sob o objeto de **propriedades** da JSON serão adicionados à resposta de retorno da associação.

## <a name="getting-help"></a>Obter ajuda

Se tiver dúvidas sobre o desenvolvimento de Fornecedores de Recursos Personalizados Azure, tente perguntar-lhes sobre [o Stack Overflow](https://stackoverflow.com/questions/tagged/azure-custom-providers). Uma pergunta semelhante pode já ter sido respondida, por isso verifique primeiro antes de publicar. Adicione a ```azure-custom-providers``` etiqueta para obter uma resposta rápida!

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu sobre fornecedores personalizados. Veja estes artigos para saber mais:

- [Tutorial: Recurso a bordo com fornecedores personalizados](./tutorial-resource-onboarding.md)
- [Tutorial: Criar ações e recursos personalizados em Azure](./tutorial-get-started-with-custom-providers.md)
- [Quickstart: Criar um fornecedor de recursos personalizados e implementar recursos personalizados](./create-custom-provider.md)
- [Como: Adicionar ações personalizadas a uma API Azure REST](./custom-providers-action-endpoint-how-to.md)
- [Como: Adicionar recursos personalizados a uma API Azure REST](./custom-providers-resources-endpoint-how-to.md)
