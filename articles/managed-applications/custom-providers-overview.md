---
title: Descrição geral dos fornecedores de recursos personalizado do Azure
description: Saiba mais sobre os fornecedores de recursos do Azure personalizadas e como estender o plano de API do Azure de acordo com seus fluxos de trabalho.
author: jjbfour
ms.service: managed-applications
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: jobreen
ms.openlocfilehash: d25ef00adc307bae57da2c04d4472874f8d67bcd
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67796088"
---
# <a name="azure-custom-resource-providers-overview"></a>Visão geral sobre provedores de recurso personalizado do Azure

Fornecedores de recursos personalizado do Azure é uma plataforma de extensibilidade para o Azure. Ele permite que define a APIs personalizadas que podem ser utilizadas para enriquecer a predefinição experiência no Azure. Esta documentação descreve:

- Como criar e implementar um fornecedor de recursos personalizado do Azure.
- Como utilizar fornecedores de recursos do Azure personalizadas para expandir os fluxos de trabalho existentes.
- Onde encontrar exemplos de código e guias para começar a utilizar.

![Descrição geral do fornecedor personalizado](./media/custom-providers-overview/overview.png)

> [!IMPORTANT]
> Fornecedores personalizados está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.
> Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="what-can-custom-resource-providers-do"></a>O que podem fornecedores de recursos personalizado?

Aqui estão alguns exemplos de como pode conseguir com fornecedores de recursos do Azure personalizado:

- Expanda a API de REST do Azure Resource Manager para incluir serviços internos e externos.
- Ative cenários personalizados sobre fluxos de trabalho do Azure existentes.
- Personalize o controle de modelos do Azure Resource Manager e o efeito.

## <a name="what-is-a-custom-resource-provider"></a>O que é um provedor de recursos personalizado

Fornecedores de recursos personalizado do Azure são feitas através da criação de um contrato entre o Azure e um ponto de extremidade. Este contrato define uma lista de novos recursos e ações por meio de um novo recurso **Microsoft.CustomProviders/resourceProviders**. O fornecedor de recursos personalizado, em seguida, irá expor essas novas APIs no Azure. Fornecedores de recursos personalizado do Azure são compostas por três partes: fornecedor de recursos personalizado **pontos de extremidade**e recursos personalizados.

## <a name="how-to-build-custom-resource-providers"></a>Como criar provedores de recursos personalizados

Fornecedores de recursos personalizados são uma lista de contratos entre o Azure e pontos de extremidade. Este contrato descreve como o Azure deve interagir com um ponto final. Os atos de fornecedor de recursos, como um proxy e irá reencaminhar solicitações e respostas de e para a especificado **ponto final**. Um fornecedor de recursos, pode especificar dois tipos de contratos: [ **os resourceTypes** ](./custom-providers-resources-endpoint-how-to.md) e [ **ações**](./custom-providers-action-endpoint-how-to.md). Estas estão ativadas por meio de definições de ponto final. Uma definição de ponto final é composta por três campos: **name**, **routingType**, e **endpoint**.

Ponto final de exemplo:

```JSON
{
  "name": "{endpointDefinitionName}",
  "routingType": "Proxy",
  "endpoint": "https://{endpointURL}/"
}
```

Propriedade | Necessário | Descrição
---|---|---
name | *Sim* | O nome da definição do ponto final. Azure irá expor este nome por meio de sua API em ' /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/<br>resourceProviders/{resourceProviderName}/{endpointDefinitionName}'
routingType | *no* | Determina o tipo de contrato com o **ponto final**. Se não for especificado, será predefinido para "Proxy".
endpoint | *Sim* | O ponto final para encaminhar os pedidos para. Isto irá processar a resposta, bem como quaisquer efeitos colaterais do pedido.

### <a name="building-custom-resources"></a>Criar recursos personalizados

**Os ResourceTypes** descrevem novos recursos personalizados que são adicionados ao Azure. Expõem esses métodos de RESTful CRUD básicos. Consulte [mais sobre como criar recursos personalizados](./custom-providers-resources-endpoint-how-to.md)

Fornecedor de recursos personalizado com de exemplo **os resourceTypes**:

```JSON
{
  "properties": {
    "resourceTypes": [
      {
        "name": "myCustomResources",
        "routingType": "Proxy",
        "endpoint": "https://{endpointURL}/"
      }
    ]
  },
  "location": "eastus"
}
```

APIs adicionados para o Azure para o exemplo acima:

HttpMethod | Exemplo de URI | Descrição
---|---|---
PUT | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomResources/{customResourceName}?api-version=2018-09-01-preview | A chamada de API REST do Azure para criar um novo recurso.
DELETE | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomResources/{customResourceName}?api-version=2018-09-01-preview | A chamada de API REST do Azure para eliminar um recurso existente.
GET | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomResources/{customResourceName}?api-version=2018-09-01-preview | A chamada de API REST do Azure para recuperar um recurso existente.
GET | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomResources?api-version=2018-09-01-preview | A chamada de API REST do Azure para obter a lista de recursos existentes.

### <a name="building-custom-actions"></a>Criação de ações personalizadas

**Ações** descrevem novas ações que são adicionadas ao Azure. Estes podem ser expostos sobre o fornecedor de recursos ou aninhar uma **resourceType**. Consulte [mais sobre a criação de ações personalizadas](./custom-providers-action-endpoint-how-to.md)

Fornecedor de recursos personalizado com de exemplo **ações**:

```JSON
{
  "properties": {
    "actions": [
      {
        "name": "myCustomAction",
        "routingType": "Proxy",
        "endpoint": "https://{endpointURL}/"
      }
    ]
  },
  "location": "eastus"
}
```

APIs adicionados para o Azure para o exemplo acima:

HttpMethod | Exemplo de URI | Descrição
---|---|---
POST | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomAction?api-version=2018-09-01-preview | A chamada de API REST do Azure para ativar a ação.

## <a name="looking-for-help"></a>Está à procura de ajuda

Se tiver perguntas para o desenvolvimento de fornecedor de recursos do Azure personalizadas, experimente perguntar [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-custom-providers). Uma pergunta semelhante pode ter já foi feita e respondida, portanto, consulte primeiro antes de lançamentos. Adicionar a marca ```azure-custom-providers``` para obter uma resposta rápida!

## <a name="next-steps"></a>Passos Seguintes

Neste artigo, aprendeu sobre fornecedores personalizados. Vá para o artigo seguinte para criar um provedor personalizado.

- [Quickstart: Criar o fornecedor de recursos personalizado do Azure e implementar recursos personalizados](./create-custom-provider.md)
- [Tutorial: Criar ações personalizadas e os recursos no Azure](./tutorial-custom-providers-101.md)
- [How To: Adicionar ações personalizadas a API REST do Azure](./custom-providers-action-endpoint-how-to.md)
- [How To: Adicionar recursos personalizados à API REST do Azure](./custom-providers-resources-endpoint-how-to.md)
