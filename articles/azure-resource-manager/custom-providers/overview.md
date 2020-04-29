---
title: Visão geral dos fornecedores personalizados
description: Saiba mais sobre os Fornecedores de Recursos Personalizados Azure e como estender o avião Azure API para se adaptar aos seus fluxos de trabalho.
author: jjbfour
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: jobreen
ms.openlocfilehash: 68b8bd187d58cd71778b8a922684cc3817a0715d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80398464"
---
# <a name="azure-custom-resource-providers-overview"></a>Visão geral dos fornecedores de recursos personalizados do Azure

Azure Custom Resource Providers é uma plataforma de extensibility para o Azure. Permite-lhe definir APIs personalizadas que podem ser usadas para enriquecer a experiência azure padrão. Esta documentação descreve:

- Como construir e implementar um Fornecedor de Recursos Personalizados Azure.
- Como utilizar os Fornecedores de Recursos Personalizados Azure para alargar os fluxos de trabalho existentes.
- Onde encontrar guias e amostras de código para começar.

![Visão geral do fornecedor personalizado](./media/overview/overview.png)

> [!IMPORTANT]
> Os Fornecedores Personalizados estão atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.
> Para mais informações, consulte [os Termos Suplementares de Utilização para pré-visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)do Microsoft Azure .

## <a name="what-can-custom-resource-providers-do"></a>O que os fornecedores de recursos personalizados podem fazer

Aqui estão alguns exemplos do que pode conseguir com os Fornecedores de Recursos Personalizados Azure:

- Alargar a API do Gestor de Recursos Azure para incluir serviços internos e externos.
- Ative cenários personalizados para além dos fluxos de trabalho existentes do Azure.
- Personalize o controlo e efeito do gestor de recursos azure.

## <a name="what-is-a-custom-resource-provider"></a>O que é um fornecedor de recursos personalizados

Os Fornecedores de Recursos Personalizados Azure são feitos através da criação de um contrato entre o Azure e um ponto final. Este contrato define uma lista de novos recursos e ações através de um novo recurso, **Microsoft.CustomProviders/resourceProviders**. O fornecedor de recursos personalizados irá então expor estas novas APIs em Azure. Os Fornecedores de Recursos Personalizados Azure são compostos por três partes: fornecedor de recursos personalizados, **pontos finais**e recursos personalizados.

## <a name="how-to-build-custom-resource-providers"></a>Como construir fornecedores de recursos personalizados

Os fornecedores de recursos personalizados são uma lista de contratos entre o Azure e os pontos finais. Este contrato descreve como o Azure deve interagir com um ponto final. O fornecedor de recursos age como um representante e irá encaminhar pedidos e respostas de e para o **ponto final**especificado. Um fornecedor de recursos pode especificar dois tipos de contratos: [**recursosTipos**](./custom-providers-resources-endpoint-how-to.md) e [**ações.**](./custom-providers-action-endpoint-how-to.md) Estes são ativados através de definições de ponto final. Uma definição de ponto final é composta por três campos: **nome,** **routingType,** e **ponto final**.

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
nome | *Sim,* | O nome da definição de ponto final. O Azure exporá este nome através da sua API em '/subscrições/{subscriçãoId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/<br>recursosFornecedores/{resourceProviderName}/{endpointDefinitionName}'
routingType | *Não* | Determina o tipo de contrato com o **ponto final**. Se não especificado, será indefinido para "Proxy".
endpoint | *Sim,* | O ponto final para encaminhar os pedidos para. Isto tratará da resposta, bem como quaisquer efeitos secundários do pedido.

### <a name="building-custom-resources"></a>Construção de recursos personalizados

**Os ResourceTypes** descrevem novos recursos personalizados que são adicionados ao Azure. Estes expõem métodos básicos de CRUD RESTful. Ver [mais sobre a criação de recursos personalizados](./custom-providers-resources-endpoint-how-to.md)

Provedor de recursos personalizados da amostra com **recursosTipos**:

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

APIs adicionados ao Azure para a amostra acima:

HttpMethod | Amostra URI | Descrição
---|---|---
PUT | /subscrições/{subscriçãoId}/resourceGroups/{resourceGroupName}/<br>fornecedores/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomResources/{customResourceName}?api-version=2018-09-01-preview | A Chamada Azure REST API para criar um novo recurso.
DELETE | /subscrições/{subscriçãoId}/resourceGroups/{resourceGroupName}/<br>fornecedores/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomResources/{customResourceName}?api-version=2018-09-01-preview | A chamada Azure REST API para apagar um recurso existente.
GET | /subscrições/{subscriçãoId}/resourceGroups/{resourceGroupName}/<br>fornecedores/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomResources/{customResourceName}?api-version=2018-09-01-preview | A Chamada Azure REST API para recuperar um recurso existente.
GET | /subscrições/{subscriçãoId}/resourceGroups/{resourceGroupName}/<br>fornecedores/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomResources?api-version=2018-09-01-preview | A API Azure REST para recuperar a lista de recursos existentes.

### <a name="building-custom-actions"></a>Construção de ações personalizadas

**As ações** descrevem novas ações que são adicionadas ao Azure. Estes podem ser expostos em cima do fornecedor de recursos ou aninhados sob um **recursoType**. Ver [mais sobre a criação de ações personalizadas](./custom-providers-action-endpoint-how-to.md)

Provedor de Recursos Personalizados da Amostra com **ações:**

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

APIs adicionados ao Azure para a amostra acima:

HttpMethod | Amostra URI | Descrição
---|---|---
POST | /subscrições/{subscriçãoId}/resourceGroups/{resourceGroupName}/<br>fornecedores/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomAction?api-version=2018-09-01-preview | A chamada Azure REST API para ativar a ação.

## <a name="looking-for-help"></a>À procura de ajuda

Se tiver dúvidas para o desenvolvimento do Fornecedor de Recursos Personalizados Azure, experimente perguntar sobre [o Stack Overflow](https://stackoverflow.com/questions/tagged/azure-custom-providers). Uma pergunta semelhante pode já ter sido feita e respondida, por isso verifique primeiro antes de ser postada. Adicione a ```azure-custom-providers``` etiqueta para obter uma resposta rápida!

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu sobre fornecedores personalizados. Vá ao próximo artigo para criar um fornecedor personalizado.

- [Quickstart: Criar o Fornecedor de Recursos Personalizados Azure e implementar recursos personalizados](./create-custom-provider.md)
- [Tutorial: Criar ações e recursos personalizados em Azure](./tutorial-get-started-with-custom-providers.md)
- [Como: Adicionar ações personalizadas à API Do REST Azure](./custom-providers-action-endpoint-how-to.md)
- [Como: Adicionar recursos personalizados à API Do REST Azure](./custom-providers-resources-endpoint-how-to.md)
