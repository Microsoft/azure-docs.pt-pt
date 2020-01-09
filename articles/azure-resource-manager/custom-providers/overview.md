---
title: Visão geral de provedores personalizados
description: Saiba mais sobre os provedores de recursos personalizados do Azure e como estender o plano de API do Azure para se ajustar aos seus fluxos de trabalho.
author: jjbfour
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: jobreen
ms.openlocfilehash: fd21117219ea3db6946e7a1b889d92702af65b58
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/03/2020
ms.locfileid: "75650490"
---
# <a name="azure-custom-resource-providers-overview"></a>Visão geral dos provedores de recursos personalizados do Azure

Os provedores de recursos personalizados do Azure são uma plataforma de extensibilidade para o Azure. Ele permite que você defina as APIs personalizadas que podem ser usadas para enriquecer a experiência padrão do Azure. Esta documentação descreve:

- Como criar e implantar um provedor de recursos personalizado do Azure.
- Como utilizar os provedores de recursos personalizados do Azure para estender fluxos de trabalho existentes.
- Onde encontrar guias e exemplos de código para começar.

![Visão geral do provedor personalizado](./media/overview/overview.png)

> [!IMPORTANT]
> Os provedores personalizados estão atualmente em visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.
> Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="what-can-custom-resource-providers-do"></a>O que os provedores de recursos personalizados podem fazer

Aqui estão alguns exemplos do que você pode obter com os provedores de recursos personalizados do Azure:

- Estenda Azure Resource Manager API REST para incluir serviços internos e externos.
- Habilite cenários personalizados na parte superior dos fluxos de trabalho do Azure existentes.
- Personalize Azure Resource Manager controle e efeito de modelos.

## <a name="what-is-a-custom-resource-provider"></a>O que é um provedor de recursos personalizado

Os provedores de recursos personalizados do Azure são criados por meio da criação de um contrato entre o Azure e um ponto de extremidade. Este contrato define uma lista de novos recursos e ações por meio de um novo recurso, **Microsoft. CustomProviders/resourceProviders**. O provedor de recursos personalizado irá expor essas novas APIs no Azure. Os provedores de recursos personalizados do Azure são compostos por três partes: provedor de recursos personalizados, **pontos de extremidade**e recursos personalizados.

## <a name="how-to-build-custom-resource-providers"></a>Como criar provedores de recursos personalizados

Os provedores de recursos personalizados são uma lista de contratos entre o Azure e os pontos de extremidade. Este contrato descreve como o Azure deve interagir com um ponto de extremidade. O provedor de recursos atua como um proxy e encaminhará solicitações e respostas de e para o **ponto de extremidade**especificado. Um provedor de recursos pode especificar dois tipos de contratos: [**ResourceTypes**](./custom-providers-resources-endpoint-how-to.md) e [**ações**](./custom-providers-action-endpoint-how-to.md). Elas são habilitadas por meio de definições de ponto de extremidade. Uma definição de ponto de extremidade é composta de três campos: **nome**, **Roteamento**e **ponto de extremidade**.

Ponto de extremidade de exemplo:

```JSON
{
  "name": "{endpointDefinitionName}",
  "routingType": "Proxy",
  "endpoint": "https://{endpointURL}/"
}
```

Propriedade | Obrigatório | Descrição
---|---|---
nome | *Ok* | O nome da definição do ponto de extremidade. O Azure vai expor esse nome por meio de sua API em '/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/<br>resourceProviders/{resourceProviderName}/{endpointDefinitionName}'
roteamentotype | *Não* | Determina o tipo de contrato com o **ponto de extremidade**. Se não for especificado, o padrão será "proxy".
endpoint | *Ok* | O ponto de extremidade para o qual rotear as solicitações. Isso tratará a resposta, bem como quaisquer efeitos colaterais da solicitação.

### <a name="building-custom-resources"></a>Criando recursos personalizados

**ResourceTypes** descrevem novos recursos personalizados que são adicionados ao Azure. Eles expõem os métodos CRUD do RESTful básico. Veja [mais sobre a criação de recursos personalizados](./custom-providers-resources-endpoint-how-to.md)

Provedor de recursos personalizados de exemplo com **ResourceTypes**:

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

APIs adicionadas ao Azure para o exemplo acima:

HttpMethod | URI de exemplo | Descrição
---|---|---
PUT | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomResources/{customResourceName}?api-version=2018-09-01-preview | A chamada à API REST do Azure para criar um novo recurso.
DELETE | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomResources/{customResourceName}?api-version=2018-09-01-preview | A chamada à API REST do Azure para excluir um recurso existente.
GET | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomResources/{customResourceName}?api-version=2018-09-01-preview | A chamada à API REST do Azure para recuperar um recurso existente.
GET | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomResources? API-Version = 2018-09-01 – Preview | A chamada à API REST do Azure para recuperar a lista de recursos existentes.

### <a name="building-custom-actions"></a>Criando ações personalizadas

As **ações** descrevem as novas ações que são adicionadas ao Azure. Eles podem ser expostos na parte superior do provedor de recursos ou aninhados em um **ResourceType**. Veja [mais sobre a criação de ações personalizadas](./custom-providers-action-endpoint-how-to.md)

Provedor de recursos personalizado de exemplo com **ações**:

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

APIs adicionadas ao Azure para o exemplo acima:

HttpMethod | URI de exemplo | Descrição
---|---|---
POST | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>mycustomizable? API-Version = 2018-09-01 – Preview | A chamada à API REST do Azure para ativar a ação.

## <a name="looking-for-help"></a>Procurando ajuda

Se você tiver dúvidas sobre o desenvolvimento do provedor de recursos personalizado do Azure, tente solicitar [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-custom-providers). Uma pergunta semelhante pode já ter sido solicitada e respondida, portanto, verifique primeiro antes de lançar. Adicione a marca ```azure-custom-providers``` para obter uma resposta rápida!

## <a name="next-steps"></a>Passos seguintes

Neste artigo, você aprendeu sobre provedores personalizados. Vá para o próximo artigo para criar um provedor personalizado.

- [Início rápido: criar um provedor de recursos personalizado do Azure e implantar recursos personalizados](./create-custom-provider.md)
- [Tutorial: criar ações e recursos personalizados no Azure](./tutorial-get-started-with-custom-providers.md)
- [Como adicionar ações personalizadas à API REST do Azure](./custom-providers-action-endpoint-how-to.md)
- [Como adicionar recursos personalizados à API REST do Azure](./custom-providers-resources-endpoint-how-to.md)
