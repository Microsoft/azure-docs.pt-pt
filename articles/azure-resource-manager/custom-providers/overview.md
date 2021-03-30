---
title: Visão geral dos fornecedores personalizados
description: Saiba mais sobre os Fornecedores de Recursos Personalizados Azure e como estender o plano API Azure para se adaptar aos seus fluxos de trabalho.
author: jjbfour
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: jobreen
ms.openlocfilehash: 68b8bd187d58cd71778b8a922684cc3817a0715d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "80398464"
---
# <a name="azure-custom-resource-providers-overview"></a>Visão geral dos fornecedores de recursos personalizados Azure

A Azure Custom Resource Providers é uma plataforma de extensibilidade para a Azure. Permite-lhe definir APIs personalizados que podem ser usados para enriquecer a experiência padrão do Azure. Esta documentação descreve:

- Como construir e implementar um Fornecedor de Recursos Personalizados Azure.
- Como utilizar os Fornecedores de Recursos Personalizados Azure para alargar os fluxos de trabalho existentes.
- Onde encontrar guias e amostras de código para começar.

![Visão geral do fornecedor personalizado](./media/overview/overview.png)

> [!IMPORTANT]
> Os Fornecedores Personalizados estão atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.
> Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="what-can-custom-resource-providers-do"></a>O que podem os fornecedores de recursos personalizados fazer

Aqui estão alguns exemplos do que pode alcançar com fornecedores de recursos personalizados Azure:

- Alargar a Azure Resource Manager REST API para incluir serviços internos e externos.
- Ativar cenários personalizados para além dos fluxos de trabalho Azure existentes.
- Personalize o controlo e o efeito do gestor de recursos Azure.

## <a name="what-is-a-custom-resource-provider"></a>O que é um fornecedor de recursos personalizado

Os Fornecedores de Recursos Personalizados Azure são feitos através da criação de um contrato entre a Azure e um ponto final. Este contrato define uma lista de novos recursos e ações através de um novo recurso, **Microsoft.CustomProviders/resourceProviders**. O fornecedor de recursos personalizados irá então expor estas novas APIs em Azure. Os Fornecedores de Recursos Personalizados Azure são compostos por três partes: fornecedor de recursos **personalizados, pontos finais** e recursos personalizados.

## <a name="how-to-build-custom-resource-providers"></a>Como construir fornecedores de recursos personalizados

Os fornecedores de recursos personalizados são uma lista de contratos entre a Azure e os pontos finais. Este contrato descreve como a Azure deve interagir com um ponto final. O fornecedor de recursos age como um representante e encaminhará pedidos e respostas para e a partir do **ponto final** especificado. Um fornecedor de recursos pode especificar dois tipos de contratos: [**recursosTypes**](./custom-providers-resources-endpoint-how-to.md) e [**ações**](./custom-providers-action-endpoint-how-to.md). Estes são ativados através de definições de ponto final. Uma definição de ponto final é composta por três campos: **nome,** **roteamentoType** e **ponto final**.

Ponto final da amostra:

```JSON
{
  "name": "{endpointDefinitionName}",
  "routingType": "Proxy",
  "endpoint": "https://{endpointURL}/"
}
```

Propriedade | Necessário | Descrição
---|---|---
name | *Sim* | O nome da definição de ponto final. O Azure exporá este nome através da sua API em '/subscrições/{subscriçãoId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/<br>recursosProviders/{resourceProviderName}/{endpointDefinitionName}'
roteamentoType | *Não* | Determina o tipo de contrato com o **ponto final.** Se não for especificado, será por defeito para "Proxy".
endpoint | *Sim* | O ponto final para encaminhar os pedidos para. Isto irá lidar com a resposta, bem como quaisquer efeitos secundários do pedido.

### <a name="building-custom-resources"></a>Construção de recursos personalizados

Os Recursos Descrevem novos recursos **personalizados** que são adicionados ao Azure. Estes expõem métodos básicos RESTful CRUD. Ver [mais sobre a criação de recursos personalizados](./custom-providers-resources-endpoint-how-to.md)

Fornecedor de recursos personalizados de amostra com **recursosTypes**:

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

APIs adicionadas ao Azure para a amostra acima:

HttpMethod | Amostra URI | Description
---|---|---
PUT | /subscrições/{subscriçãoId}/resourceGroups/{resourceGroupName}/<br>fornecedores/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomResources/{customResourceName}?api-version=2018-09-01-preview | A Azure REST API chama para criar um novo recurso.
DELETE | /subscrições/{subscriçãoId}/resourceGroups/{resourceGroupName}/<br>fornecedores/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomResources/{customResourceName}?api-version=2018-09-01-preview | A Azure REST API chama para eliminar um recurso existente.
GET | /subscrições/{subscriçãoId}/resourceGroups/{resourceGroupName}/<br>fornecedores/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomResources/{customResourceName}?api-version=2018-09-01-preview | A Azure REST API chama para recuperar um recurso existente.
GET | /subscrições/{subscriçãoId}/resourceGroups/{resourceGroupName}/<br>fornecedores/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomResources?api-version=2018-09-01-preview | A Azure REST API chama para recuperar a lista de recursos existentes.

### <a name="building-custom-actions"></a>Construindo ações personalizadas

**As ações** descrevem novas ações que são adicionadas ao Azure. Estes podem ser expostos em cima do fornecedor de recursos ou aninhados sob **umtip de recursoType**. Ver [mais sobre a criação de ações personalizadas](./custom-providers-action-endpoint-how-to.md)

Fornecedor de Recursos Personalizados da Amostra com **ações:**

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

APIs adicionadas ao Azure para a amostra acima:

HttpMethod | Amostra URI | Description
---|---|---
POST | /subscrições/{subscriçãoId}/resourceGroups/{resourceGroupName}/<br>fornecedores/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomAction?api-version=2018-09-01-preview | A Azure REST API chama para ativar a ação.

## <a name="looking-for-help"></a>À procura de ajuda

Se tiver dúvidas para o desenvolvimento do Fornecedor de Recursos Personalizados Azure, tente perguntar no [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-custom-providers). Uma pergunta semelhante pode já ter sido feita e respondida, por isso verifique primeiro antes de publicar. Adicione a etiqueta ```azure-custom-providers``` para obter uma resposta rápida!

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu sobre fornecedores personalizados. Vá ao próximo artigo para criar um fornecedor personalizado.

- [Quickstart: Criar Fornecedor de Recursos Personalizados Azure e implementar recursos personalizados](./create-custom-provider.md)
- [Tutorial: Criar ações e recursos personalizados em Azure](./tutorial-get-started-with-custom-providers.md)
- [Como: Adicionar ações personalizadas à Azure REST API](./custom-providers-action-endpoint-how-to.md)
- [Como: Adicionar recursos personalizados à Azure REST API](./custom-providers-resources-endpoint-how-to.md)
