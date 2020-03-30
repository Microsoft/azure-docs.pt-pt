---
title: Visão geral da definição de vista
description: Descreve o conceito de criação de definição de vista para aplicações geridas pelo Azure.
ms.topic: conceptual
ms.author: lazinnat
author: lazinnat
ms.date: 06/12/2019
ms.openlocfilehash: d0c60f5738bf634f9d43d6d4f0d78c1239b7ff3c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75650698"
---
# <a name="view-definition-artifact-in-azure-managed-applications"></a>Ver artefacto de definição em Aplicações Geridas de Azure

A definição de visualização é um artefacto opcional em Aplicações Geridas azure. Permite personalizar a página de visão geral e adicionar mais visualizações como Métricas e recursos Personalizados.

Este artigo fornece uma visão geral do artefacto de definição de visão e suas capacidades.

## <a name="view-definition-artifact"></a>Artefacto de definição de vista

O artefacto de definição de vista deve ser nomeado **viewDefinition.json** e colocado ao mesmo nível que **createUiDefinition.json** e **mainTemplate.json** no pacote .zip que cria uma definição de aplicação gerida. Para aprender a criar o pacote .zip e publicar uma definição de aplicação gerida, consulte Publicar uma definição de [Aplicação Gerida azure](publish-managed-app-definition-quickstart.md)

## <a name="view-definition-schema"></a>Ver esquema de definição

O ficheiro **viewDefinition.json** tem `views` apenas uma propriedade de nível superior, que é uma variedade de vistas. Cada vista é mostrada na interface de utilizador da aplicação gerida como um item de menu separado na tabela de conteúdos. Cada vista `kind` tem uma propriedade que define o tipo de vista. Deve ser definido para um dos seguintes valores: [Visão geral,](#overview) [Métricas,](#metrics) [Recursos Personalizados,](#custom-resources) [Associações](#associations). Para mais informações, consulte o [atual esquema JSON para verDefini.json](https://schema.management.azure.com/schemas/viewdefinition/0.0.1-preview/ViewDefinition.json#).

Amostra JSON para definição de visualização:

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

## <a name="overview"></a>Descrição geral

`"kind": "Overview"`

Quando fornece esta visão em **viewDefinition.json,** substitui a página de visão geral predefinida na sua aplicação gerida.

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
|cabeçalho|Não|O cabeçalho da página geral.|
|descrição|Não|A descrição da sua aplicação gerida.|
|comandos|Não|A matriz de botões adicionais da barra de ferramentas da página de visão geral, ver [comandos](#commands).|

![Descrição geral](./media/view-definition/overview.png)

## <a name="metrics"></a>Métricas

`"kind": "Metrics"`

A visão das métricas permite-lhe recolher e agregar dados dos seus recursos de aplicação geridos em [Métricas de Monitor De Azure](../../azure-monitor/platform/data-platform-metrics.md).

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
|displayName|Não|O título exibido da vista.|
|versão|Não|A versão da plataforma usada para renderizar a vista.|
|gráficos|Sim|A variedade de gráficos da página das métricas.|

### <a name="chart"></a>Gráfico

|Propriedade|Necessário|Descrição|
|---------|---------|---------|
|displayName|Sim|O título exibido da tabela.|
|chartTypeType|Não|A visualização a utilizar para este gráfico. Por padrão, usa um gráfico de linha. Tipos de gráficos suportados: `Bar, Line, Area, Scatter`.|
|metrics|Sim|A variedade de métricas para traçar neste gráfico. Para saber mais sobre métricas suportadas no portal Azure, consulte [métricas suportadas com o Monitor Azure](../../azure-monitor/platform/metrics-supported.md)|

### <a name="metric"></a>Métrica

|Propriedade|Necessário|Descrição|
|---------|---------|---------|
|nome|Sim|O nome da métrica.|
|agregaçãoType|Sim|O tipo de agregação a utilizar para esta métrica. Tipos de agregação suportados:`none, sum, min, max, avg, unique, percentile, count`|
|espaço de nomes|Não|Informações adicionais a utilizar na determinação do fornecedor de métricas corretas.|
|recursosTagFilter|Não|A matriz de etiquetas de `or` recursos (será separada com palavra) para a qual as métricas seriam apresentadas. Aplica-se em cima do filtro do tipo de recurso.|
|resourceType|Sim|O tipo de recurso para o qual as métricas seriam apresentadas.|

![Métricas](./media/view-definition/metrics.png)

## <a name="custom-resources"></a>Recursos personalizados

`"kind": "CustomResources"`

Pode definir múltiplas vistas deste tipo. Cada vista representa um tipo de recurso personalizado **único** do fornecedor personalizado que definiu no **mainTemplate.json**. Para uma introdução aos fornecedores personalizados, consulte a visão geral da [Pré-visualização dos fornecedores personalizados do Azure](../custom-providers/overview.md).

Nesta perspetiva, pode executar operações GET, PUT, DELETE e POST para o seu tipo de recurso personalizado. As operações post podem ser ações personalizadas globais ou ações personalizadas num contexto do seu tipo de recurso personalizado.

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
|displayName|Sim|O título exibido da vista. O título deve ser **único** para cada vista CustomResources na sua **visãoDefinition.json**.|
|versão|Não|A versão da plataforma usada para renderizar a vista.|
|resourceType|Sim|O tipo de recurso personalizado. Deve ser um tipo de recurso personalizado **único** do seu fornecedor personalizado.|
|ícone|Não|O ícone da vista. A lista de ícones de exemplo é definida em [JSON Schema](https://schema.management.azure.com/schemas/viewdefinition/0.0.1-preview/ViewDefinition.json#).|
|criarUIDefinição|Não|Crie esquema de Definição UI para criar um comando de recursos personalizado. Para uma introdução à criação de definições de UI, consulte [Começar com CreateUiDefinition](create-uidefinition-overview.md)|
|comandos|Não|O conjunto de botões adicionais da barra de ferramentas da vista CustomResources, ver [comandos](#commands).|
|colunas|Não|A variedade de colunas do recurso personalizado. Se não `name` estiver definida, a coluna será mostrada por defeito. A coluna `"key"` deve `"displayName"`ter e. Para a chave, forneça a chave da propriedade para exibir numa vista. Se aninhado, utilize o ponto como `"key": "name"` `"key": "properties.property1"`delimitador, por exemplo, ou . Para o nome do visor, forneça o nome de exibição da propriedade para exibir numa vista. Você também pode `"optional"` fornecer uma propriedade. Quando definida como verdadeira, a coluna é escondida por defeito.|

![Recursos Personalizados](./media/view-definition/customresources.png)

## <a name="commands"></a>Comandos

Os comandos são um conjunto de botões adicionais da barra de ferramentas que são exibidos na página. Cada comando representa uma ação POST do seu Fornecedor Personalizado Azure definida em **mainTemplate.json**. Para uma introdução aos fornecedores personalizados, consulte a visão geral dos [Fornecedores Personalizados do Azure.](../custom-providers/overview.md)

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
|displayName|Sim|O nome apresentado do botão de comando.|
|path|Sim|O nome de ação do fornecedor personalizado. A ação deve ser definida em **mainTemplate.json**.|
|ícone|Não|O ícone do botão de comando. A lista de ícones de exemplo é definida em [JSON Schema](https://schema.management.azure.com/schemas/viewdefinition/0.0.1-preview/ViewDefinition.json#).|
|criarUIDefinição|Não|Crie esquema de definição ui para comando. Para uma introdução à criação de definições de UI, consulte [Começar com CreateUiDefinition](create-uidefinition-overview.md).|

## <a name="associations"></a>Associações

`"kind": "Associations"`

Pode definir múltiplas vistas deste tipo. Esta vista permite-lhe ligar os recursos existentes à aplicação gerida através do fornecedor personalizado que definiu no **mainTemplate.json**. Para uma introdução aos fornecedores personalizados, consulte a visão geral da [Pré-visualização dos fornecedores personalizados do Azure](../custom-providers/overview.md).

Nesta perspetiva, pode alargar os recursos azure existentes com base no `targetResourceType`. Quando um recurso é selecionado, criará um pedido de embarque ao fornecedor **personalizado público,** que pode aplicar um efeito colateral ao recurso. 

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
|displayName|Sim|O título exibido da vista. O título deve ser **único** para cada visão de Associações na sua **visãoDefini.json**.|
|versão|Não|A versão da plataforma usada para renderizar a vista.|
|targetResourceType|Sim|O tipo de recurso alvo. Este é o tipo de recurso que será exibido para o embarque de recursos.|
|criarUIDefinição|Não|Crie esquema de Definição UI para criar o comando de recursos de associação. Para uma introdução à criação de definições de UI, consulte [Começar com CreateUiDefinition](create-uidefinition-overview.md)|

## <a name="looking-for-help"></a>À procura de ajuda

Se tiver dúvidas sobre aplicações geridas pelo Azure, tente perguntar sobre [o Stack Overflow](https://stackoverflow.com/questions/tagged/azure-managedapps). Uma pergunta semelhante pode já ter sido feita e respondida, por isso verifique primeiro antes de ser postada. Adicione a `azure-managedapps` etiqueta para obter uma resposta rápida!

## <a name="next-steps"></a>Passos seguintes

- Para obter uma introdução às aplicações geridas, veja [Descrição geral da Aplicação Gerida do Azure](overview.md).
- Para uma introdução aos fornecedores personalizados, consulte a visão geral dos [Fornecedores Personalizados do Azure.](../custom-providers/overview.md)
- Para criar uma aplicação gerida azure com fornecedores personalizados Azure, consulte [Tutorial: Criar aplicação gerida com ações personalizadas de fornecedor e tipos](tutorial-create-managed-app-with-custom-provider.md) de recursos
