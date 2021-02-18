---
title: Visão geral da definição de vista
description: Descreve o conceito de criação de definição de visualização para aplicações geridas azure.
ms.topic: conceptual
ms.author: lazinnat
author: lazinnat
ms.date: 06/12/2019
ms.openlocfilehash: 55263d3c742d18cf03303f96f08fb9aa370c7af8
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100592072"
---
# <a name="view-definition-artifact-in-azure-managed-applications"></a>Ver artefacto de definição em Aplicações Geridas Azure

Ver definição é um artefacto opcional em Aplicações Geridas Azure. Permite personalizar a página geral e adicionar mais visualizações, tais como Métricas e recursos personalizados.

Este artigo fornece uma visão geral do artefacto de definição de visão e suas capacidades.

## <a name="view-definition-artifact"></a>Artefacto de definição de vista

O artefacto de definição de ponto deve ser nomeado **viewDefinition.js** e colocado ao mesmo nível **quecreateUiDefinition.js** e **mainTemplate.jsno** pacote de .zip que cria uma definição de aplicação gerida. Para aprender a criar o pacote .zip e publicar uma definição de aplicação gerida, consulte [publicar uma definição de aplicação gerida Azure](publish-service-catalog-app.md)

## <a name="view-definition-schema"></a>Ver esquema de definição

O **viewDefinition.jsem** arquivo tem apenas uma propriedade de nível `views` superior, que é uma variedade de pontos de vista. Cada vista é mostrada na interface de utilizador de aplicação gerida como um item de menu separado na tabela de conteúdos. Cada vista tem uma `kind` propriedade que define o tipo de vista. Deve ser definido para um dos seguintes valores: [Visão geral,](#overview) [Métricas,](#metrics) [CustomResources,](#custom-resources) [Associações.](#associations) Para obter mais informações, consulte [o esquema atual da JSON para viewDefinition.js.](https://schema.management.azure.com/schemas/viewdefinition/0.0.1-preview/ViewDefinition.json#)

Amostra JSON para definição de vista:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/viewdefinition/0.0.1-preview/ViewDefinition.json#",
    "contentVersion": "0.0.0.1",
    "views": [
        {
            "kind": "Overview",
            "properties": {
                "header": "Welcome to your Azure Managed Application",
                "description": "This managed application is for demo purposes only.",
                "commands": [
                    {
                        "displayName": "Test Action",
                        "path": "testAction"
                    }
                ]
            }
        },
        {
            "kind": "Metrics",
            "properties": {
                "displayName": "This is my metrics view",
                "version": "1.0.0",
                "charts": [
                    {
                        "displayName": "Sample chart",
                        "chartType": "Bar",
                        "metrics": [
                            {
                                "name": "Availability",
                                "aggregationType": "avg",
                                "resourceTagFilter": [ "tag1" ],
                                "resourceType": "Microsoft.Storage/storageAccounts",
                                "namespace": "Microsoft.Storage/storageAccounts"
                            }
                        ]
                    }
                ]
            }
        },
        {
            "kind": "CustomResources",
            "properties": {
                "displayName": "Test custom resource type",
                "version": "1.0.0",
                "resourceType": "testCustomResource",
                "createUIDefinition": { },
                "commands": [
                    {
                        "displayName": "Custom Context Action",
                        "path": "testCustomResource/testContextAction",
                        "icon": "Stop",
                        "createUIDefinition": { }
                    }
                ],
                "columns": [
                    {"key": "name", "displayName": "Name"},
                    {"key": "properties.myProperty1", "displayName": "Property 1"},
                    {"key": "properties.myProperty2", "displayName": "Property 2", "optional": true}
                ]
            }
        },
        {
            "kind": "Associations",
            "properties": {
                "displayName": "Test association resource type",
                "version": "1.0.0",
                "targetResourceType": "Microsoft.Compute/virtualMachines",
                "createUIDefinition": { }
            }
        }
    ]
}
```

## <a name="overview"></a>Descrição Geral

`"kind": "Overview"`

Quando fornece esta vista em **viewDefinition.js,** substitui a página de Visão Geral predefinido na sua aplicação gerida.

```json
{
    "kind": "Overview",
    "properties": {
        "header": "Welcome to your Azure Managed Application",
        "description": "This managed application is for demo purposes only.",
        "commands": [
            {
                "displayName": "Test Action",
                "path": "testAction"
            }
        ]
    }
}
```

|Propriedade|Necessário|Descrição|
|---------|---------|---------|
|cabeçalho|No|O cabeçalho da página geral.|
|descrição|No|A descrição da sua aplicação gerida.|
|comandos|No|A matriz de botões adicionais da barra de ferramentas da página geral, ver [comandos](#commands).|

![O Screenshot mostra a visão geral de uma aplicação gerida com um controlo de Ação de Teste para executar uma aplicação de demonstração.](./media/view-definition/overview.png)

## <a name="metrics"></a>Métricas

`"kind": "Metrics"`

A visão métrica permite-lhe recolher e agregar dados dos recursos geridos da aplicação em [Azure Monitor Metrics](../../azure-monitor/essentials/data-platform-metrics.md).

```json
{
    "kind": "Metrics",
    "properties": {
        "displayName": "This is my metrics view",
        "version": "1.0.0",
        "charts": [
            {
                "displayName": "Sample chart",
                "chartType": "Bar",
                "metrics": [
                    {
                        "name": "Availability",
                        "aggregationType": "avg",
                        "resourceTagFilter": [ "tag1" ],
                        "resourceType": "Microsoft.Storage/storageAccounts",
                        "namespace": "Microsoft.Storage/storageAccounts"
                    }
                ]
            }
        ]
    }
}
```

|Propriedade|Necessário|Descrição|
|---------|---------|---------|
|displayName|No|O título exibido da vista.|
|versão|No|A versão da plataforma usada para tornar a vista.|
|charts|Yes|A matriz de gráficos da página de métricas.|

### <a name="chart"></a>Gráfico

|Propriedade|Necessário|Descrição|
|---------|---------|---------|
|displayName|Yes|O título exibido da tabela.|
|chartType|No|A visualização a ser usada para este gráfico. Por padrão, usa um gráfico de linha. Tipos de gráficos suportados: `Bar, Line, Area, Scatter` .|
|metrics|Yes|A variedade de métricas para traçar neste gráfico. Para saber mais sobre as métricas suportadas no portal Azure, consulte [métricas suportadas com o Azure Monitor](../../azure-monitor/essentials/metrics-supported.md)|

### <a name="metric"></a>Metric

|Propriedade|Necessário|Descrição|
|---------|---------|---------|
|name|Yes|O nome da métrica.|
|agregaçãoType|Yes|O tipo de agregação a utilizar para esta métrica. Tipos de agregação suportados: `none, sum, min, max, avg, unique, percentile, count`|
|espaço de nomes|No|Informações adicionais a utilizar ao determinar o fornecedor de métricas correto.|
|recursoRFiltro|No|A matriz de etiquetas de recursos (será separada com `or` a palavra) para a qual as métricas seriam apresentadas. Aplica-se em cima do filtro do tipo de recurso.|
|resourceType|Yes|O tipo de recurso para o qual as métricas seriam apresentadas.|

![A screenshot mostra uma página de monitorização chamada This is my metrics view for a managed application.](./media/view-definition/metrics.png)

## <a name="custom-resources"></a>Recursos personalizados

`"kind": "CustomResources"`

Pode definir várias visões deste tipo. Cada vista representa um tipo de recurso personalizado **único** do fornecedor personalizado que definiu em **mainTemplate.jsem**. Para uma introdução a fornecedores personalizados, consulte [a visão geral do Pré-visualização dos fornecedores personalizados Azure.](../custom-providers/overview.md)

Nesta vista pode efetuar operações GET, PUT, DELETE e POST para o seu tipo de recurso personalizado. As operações POST podem ser ações personalizadas globais ou ações personalizadas num contexto do seu tipo de recurso personalizado.

```json
{
    "kind": "CustomResources",
    "properties": {
        "displayName": "Test custom resource type",
        "version": "1.0.0",
        "resourceType": "testCustomResource",
        "icon": "Polychromatic.ResourceList",
        "createUIDefinition": { },
        "commands": [
            {
                "displayName": "Custom Context Action",
                "path": "testCustomResource/testContextAction",
                "icon": "Stop",
                "createUIDefinition": { },
            }
        ],
        "columns": [
            {"key": "name", "displayName": "Name"},
            {"key": "properties.myProperty1", "displayName": "Property 1"},
            {"key": "properties.myProperty2", "displayName": "Property 2", "optional": true}
        ]
    }
}
```

|Propriedade|Necessário|Descrição|
|---------|---------|---------|
|displayName|Yes|O título exibido da vista. O título deve ser **exclusivo** de cada vista CustomResources no seu **viewDefinition.jsem**.|
|versão|No|A versão da plataforma usada para tornar a vista.|
|resourceType|Yes|O tipo de recurso personalizado. Deve ser um tipo de recurso personalizado **único** do seu fornecedor personalizado.|
|ícone|No|O ícone da vista. A lista de ícones de exemplo é definida no [Esquema JSON.](https://schema.management.azure.com/schemas/viewdefinition/0.0.1-preview/ViewDefinition.json#)|
|createUIDefinition|No|Crie esquema de definição de UI para criar um comando de recursos personalizado. Para uma introdução à criação de definições de UI, consulte [Começar com CreateUiDefinition](create-uidefinition-overview.md)|
|comandos|No|A matriz de botões adicionais da barra de ferramentas da vista CustomResources, consulte [os comandos](#commands).|
|colunas|No|A matriz de colunas do recurso personalizado. Se não estiver definida, a `name` coluna será mostrada por defeito. A coluna deve ter `"key"` `"displayName"` e. . Para a chave, forneça a chave da propriedade para exibir numa vista. Se aninhado, use o ponto como delimiter, por exemplo, `"key": "name"` ou `"key": "properties.property1"` . Para o nome do visor, forneça o nome de exibição da propriedade para visualizar numa vista. Você também pode fornecer um `"optional"` imóvel. Quando definida como verdadeira, a coluna é escondida numa vista por defeito.|

![A screenshot mostra uma página de Recursos chamada Test custom resource type e o control custom Context Action.](./media/view-definition/customresources.png)

## <a name="commands"></a>Comandos

Os comandos são uma série de botões adicionais da barra de ferramentas que são apresentados na página. Cada comando representa uma ação POST do seu Fornecedor Personalizado Azure definida em **mainTemplate.js.** Para uma introdução a fornecedores personalizados, consulte [a visão geral dos Fornecedores Personalizados Azure.](../custom-providers/overview.md)

```json
{
    "commands": [
        {
            "displayName": "Start Test Action",
            "path": "testAction",
            "icon": "Start",
            "createUIDefinition": { }
        },
    ]
}
```

|Propriedade|Necessário|Descrição|
|---------|---------|---------|
|displayName|Yes|O nome apresentado do botão de comando.|
|caminho|Yes|O nome de ação do fornecedor personalizado. A ação deve ser definida em **mainTemplate.jsem**.|
|ícone|No|O ícone do botão de comando. A lista de ícones de exemplo é definida no [Esquema JSON.](https://schema.management.azure.com/schemas/viewdefinition/0.0.1-preview/ViewDefinition.json#)|
|createUIDefinition|No|Crie esquema de definição de UI para comando. Para uma introdução à criação de definições de UI, consulte [Começar com CreateUiDefinition](create-uidefinition-overview.md).|

## <a name="associations"></a>Associações

`"kind": "Associations"`

Pode definir várias visões deste tipo. Esta vista permite-lhe ligar os recursos existentes à aplicação gerida através do fornecedor personalizado que definiu em **mainTemplate.jsem**. Para uma introdução a fornecedores personalizados, consulte [a visão geral do Pré-visualização dos fornecedores personalizados Azure.](../custom-providers/overview.md)

Nesta perspetiva, pode alargar os recursos Azure existentes com base no `targetResourceType` . Quando um recurso é selecionado, criará um pedido de embarque ao fornecedor **público** personalizado, que pode aplicar um efeito colateral ao recurso. 

```json
{
    "kind": "Associations",
    "properties": {
        "displayName": "Test association resource type",
        "version": "1.0.0",
        "targetResourceType": "Microsoft.Compute/virtualMachines",
        "createUIDefinition": { }
    }
}
```

|Propriedade|Necessário|Descrição|
|---------|---------|---------|
|displayName|Yes|O título exibido da vista. O título deve ser **único** para cada visão de associações na sua **viewDefinition.jsem**.|
|versão|No|A versão da plataforma usada para tornar a vista.|
|targetResourceType|Yes|O tipo de recurso alvo. Este é o tipo de recurso que será apresentado para a utilização de recursos a bordo.|
|createUIDefinition|No|Crie esquema de definição de UI para criar o comando de recursos de associação. Para uma introdução à criação de definições de UI, consulte [Começar com CreateUiDefinition](create-uidefinition-overview.md)|

## <a name="looking-for-help"></a>À procura de ajuda

Se tiver dúvidas sobre aplicações geridas azure, tente perguntar no [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-managedapps). Uma pergunta semelhante pode já ter sido feita e respondida, por isso verifique primeiro antes de publicar. Adicione a etiqueta `azure-managedapps` para obter uma resposta rápida!

## <a name="next-steps"></a>Passos seguintes

- Para obter uma introdução às aplicações geridas, veja [Descrição geral da Aplicação Gerida do Azure](overview.md).
- Para uma introdução a fornecedores personalizados, consulte [a visão geral dos Fornecedores Personalizados Azure.](../custom-providers/overview.md)
- Para criar uma Aplicação Gerida Azure com Fornecedores Personalizados Azure, consulte [Tutorial: Criar aplicação gerida com ações personalizadas do fornecedor e tipos de recursos](tutorial-create-managed-app-with-custom-provider.md)
