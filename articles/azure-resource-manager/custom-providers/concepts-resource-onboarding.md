---
title: Integração de recursos
description: Aprenda a realizar o embarque de recursos utilizando fornecedores personalizados Azure para aplicar gestão ou configuração a outros tipos de recursos Azure.
author: jjbfour
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: jobreen
ms.openlocfilehash: 1846b036f12fe7e691021ec0248782cad946d9b7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "75650412"
---
# <a name="azure-custom-providers-resource-onboarding-overview"></a>Azure Custom Providers recurso onboarding overview

O recurso Azure Custom Providers no embarque é um modelo de extensibilidade para tipos de recursos Azure. Permite aplicar operações ou gestão através dos recursos Azure existentes em escala. Para mais informações, consulte [como os Fornecedores Personalizados Azure podem estender o Azure](overview.md). Este artigo descreve:

- Que recurso a bordo pode fazer.
- Recursos no embarque básico e como usá-lo.
- Onde encontrar guias e amostras de código para começar.

> [!IMPORTANT]
> Os Fornecedores Personalizados estão atualmente em pré-visualização pública.
> Esta versão de pré-visualização é fornecida sem um acordo de nível de serviço, e não recomendamos para cargas de trabalho de produção. Certas funcionalidades podem não ser suportadas ou podem ter capacidades restritas.
> Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="what-can-resource-onboarding-do"></a>O que os recursos de bordo podem fazer?

À semelhança [dos recursos personalizados da Azure Custom Providers,](./custom-providers-resources-endpoint-how-to.md)o recurso onboarding define um contrato que irá recorrer a pedidos de "onboarding" para um ponto final. Ao contrário dos recursos personalizados, o recurso ao bordo não cria um novo tipo de recurso. Em vez disso, permite a extensão dos tipos de recursos existentes. E o embarque de recursos funciona com a Azure Policy, para que a gestão e configuração de recursos possam ser feitos em escala. Alguns exemplos de fluxos de trabalho de embarque de recursos:

- Instale e gere em extensões de máquinas virtuais.
- Carregar e configurar incumprimentos nas contas de armazenamento Azure.
- Ativar as definições de diagnóstico de base à escala.

## <a name="resource-onboarding-basics"></a>Básicos de embarque de recursos

Configura recursos no embarque através de fornecedores personalizados Azure utilizando os tipos de recursos microsoft.CustomProviders/resourceProviders e Microsoft.CustomProviders/associations. Para permitir o embarque de recursos para um fornecedor personalizado, durante o processo de configuração, crie um **recursoType** chamado "associações" com um **RoteamentoType** que inclua "Extensão". O Microsoft.CustomProviders/associations e o Microsoft.CustomProviders/resourceProviders não precisam de pertencer ao mesmo grupo de recursos.

Aqui está uma amostra Azure fornecedor personalizado:

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

Propriedade | Necessário? | Description
---|---|---
name | Yes | O nome da definição de ponto final. Para o recurso a bordo, o nome deve ser "associações".
roteamentoType | Yes | Determina o tipo de contrato com o ponto final. Para o embarque de recursos, os tipos de **encaminhamento válidos** são "Proxy,Cache,Extension" e "Webhook,Cache,Extension".
endpoint | Yes | O ponto final para encaminhar os pedidos para. Isto irá lidar com a resposta e quaisquer efeitos secundários do pedido.

Depois de criar o fornecedor personalizado com o tipo de recurso de associações, pode ser alvo através do Microsoft.CustomProviders/associations. Microsoft.CustomProviders/associations é um recurso de extensão que pode estender qualquer outro recurso Azure. Quando um caso de Microsoft.CustomProviders/associations for criado, tomará um target de **propriedadeResourceId**, que deve ser um Microsoft.CustomProviders/resourceProviders ou Microsoft.Solutions/applications resource ID. Nestes casos, o pedido será encaminhado para o tipo de recurso das associações na instância Microsoft.CustomProviders/resourceProviders que criou.

> [!NOTE]
> Se um ID de recursos microsoft.Solutions/aplicações for fornecido como **o targetResourceId,** deve existir um Microsoft.CustomProviders/resourceProviders implantados no grupo de recursos geridos com o nome "público".

Associação de Fornecedores Personalizados Azure:

```JSON
{
  "properties": {
    "targetResourceId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}",
    ...
  }
}
```

Propriedade | Necessário? | Description
---|---|---
targetResourceId | Yes | O ID de recursos dos Microsoft.CustomProviders/resourceProviders ou Microsoft.Solutions/applications.

## <a name="how-to-use-resource-onboarding"></a>Como utilizar recursos a bordo

O embarque de recursos funciona através da extensão de outros recursos com o recurso de extensão Microsoft.CustomProviders/associations. Na amostra seguinte, o pedido é feito para uma máquina virtual, mas qualquer recurso pode ser estendido.

Em primeiro lugar, é necessário criar um recurso de fornecedor personalizado com um tipo de recurso de associações. Isto irá declarar o URL de retorno que será utilizado quando for criado um recurso Microsoft.CustomProviders/associations, que visa o fornecedor personalizado.

Amostra Microsoft.CustomProviders/resourceProviders criar pedido:

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

Depois de criar o fornecedor personalizado, pode direcionar outros recursos e aplicar-lhes os efeitos secundários do fornecedor personalizado.

Amostra Microsoft.CustomProviders/associations criar pedido:

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

O ponto final deve responder com uma aplicação/json `Content-Type` e um organismo de resposta JSON válido. Os campos que são devolvidos sob o objeto de **propriedades** do JSON serão adicionados à resposta de retorno da associação.

## <a name="getting-help"></a>Obter ajuda

Se tiver dúvidas sobre o desenvolvimento de Fornecedores de Recursos Personalizados Azure, tente fazê-los no [Stack Overflow.](https://stackoverflow.com/questions/tagged/azure-custom-providers) Uma pergunta semelhante pode já ter sido respondida, por isso verifique primeiro antes de publicar. Adicione a etiqueta ```azure-custom-providers``` para obter uma resposta rápida!

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu sobre fornecedores personalizados. Consulte estes artigos para saber mais:

- [Tutorial: Embarque de recursos com fornecedores personalizados](./tutorial-resource-onboarding.md)
- [Tutorial: Criar ações e recursos personalizados em Azure](./tutorial-get-started-with-custom-providers.md)
- [Quickstart: Criar um fornecedor de recursos personalizado e implementar recursos personalizados](./create-custom-provider.md)
- [Como: Adicionar ações personalizadas a uma API Azure REST](./custom-providers-action-endpoint-how-to.md)
- [Como: Adicionar recursos personalizados a uma API Azure REST](./custom-providers-resources-endpoint-how-to.md)
