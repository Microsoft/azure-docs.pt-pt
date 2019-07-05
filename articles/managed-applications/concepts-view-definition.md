---
title: Descrição geral da definição de vista em aplicações geridas do Azure | Documentos da Microsoft
description: Descreve o conceito de criação da definição de vista para aplicações geridas do Azure.
services: managed-applications
ms.service: managed-applications
ms.topic: conceptual
ms.author: lazinnat
author: lazinnat
ms.date: 06/12/2019
ms.openlocfilehash: 6735787f9b43f98ab611584f3c7191c9f927dbc2
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/29/2019
ms.locfileid: "67478748"
---
# <a name="view-definition-artifact-in-azure-managed-applications"></a>Artefactos de definição de vista em aplicações geridas do Azure

Definição de vista é um artefato opcional em aplicações geridas do Azure. Permite personalizar a página de descrição geral e adicionar vistas mais como recursos de métricas e personalizado.

Este artigo fornece uma visão geral do artefacto de definição de vista e as respetivas capacidades.

## <a name="view-definition-artifact"></a>Artefacto de definição de vista

O artefacto de definição de vista deve ser nomeado **viewDefinition.json** e colocados no mesmo nível que **Createuidefinition** e **maintemplate. JSON** na. zip pacote que cria uma definição da aplicação gerida. Para saber como criar o pacote. zip e publicar uma definição da aplicação gerida, veja [publicar uma definição de aplicação gerida do Azure](publish-managed-app-definition-quickstart.md)

## <a name="view-definition-schema"></a>Esquema de definição de vista

O **viewDefinition.json** ficheiro tem apenas um nível de topo `views` propriedade, que é uma matriz de modos de exibição. Cada vista é mostrada na interface do usuário de aplicativo gerenciado como um item de menu separado na tabela de conteúdos. Cada vista tem um `kind` propriedade que define o tipo da vista. Tem de ser definido para um dos seguintes valores: [Descrição geral](#overview), [métricas](#metrics), [CustomResources](#custom-resources). Para obter mais informações, consulte atual [esquema JSON para viewDefinition.json](https://schema.management.azure.com/schemas/viewdefinition/0.0.1-preview/ViewDefinition.json#).

Exemplo de JSON para a definição de vista:

```json
{
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
                        "displayName": "Custom Test Action",
                        "path": "testAction"
                    },
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
    ]
}

```

## <a name="overview"></a>Descrição geral

`"kind": "Overview"`

Quando fornecer esta vista no **viewDefinition.json**, substitui a página de descrição geral do padrão em seu aplicativo gerenciado.

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
|cabeçalho|Não|O cabeçalho da página de descrição geral.|
|description|Não|A descrição do seu aplicativo gerenciado.|
|Comandos|Não|A matriz dos botões da barra de ferramentas adicionais de página de descrição geral, consulte [comandos](#commands).|

## <a name="metrics"></a>Métricas

`"kind": "Metrics"`

Vista de métrica permite-lhe recolher e agregar os dados dos seus recursos de aplicação gerida no [monitorizar as métricas do Azure](../azure-monitor/platform/data-platform-metrics.md).

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
|displayName|Não|O título apresentado da vista.|
|version|Não|A versão da plataforma utilizada para renderizar a exibição.|
|Gráficos|Sim|A matriz de gráficos da página de métricas.|

### <a name="chart"></a>Gráfico

|Propriedade|Necessário|Descrição|
|---------|---------|---------|
|displayName|Sim|O título apresentado do gráfico.|
|chartType|Não|A visualização a utilizar para este gráfico. Por predefinição, utiliza um gráfico de linhas. Tipos de gráfico suportados: `Bar, Line, Area, Scatter`.|
|metrics|Sim|A matriz de métricas para desenhar neste gráfico. Para saber mais sobre métricas suportadas no portal do Azure, veja [suportado métricas com o Azure Monitor](../azure-monitor/platform/metrics-supported.md)|

### <a name="metric"></a>Métrica

|Propriedade|Necessário|Descrição|
|---------|---------|---------|
|name|Sim|O nome da métrica.|
|aggregationType|Sim|O tipo de agregação a utilizar para esta métrica. Tipos de agregação com suporte: `none, sum, min, max, avg, unique, percentile, count`|
|espaço de nomes|Não|Obter informações adicionais a utilizar ao determinar o fornecedor de métricas correto.|
|resourceTagFilter|Não|Matriz de etiquetas de recurso (ficarão separados com `or` word) para as métricas seria exibido. Aplica-se com base no filtro de tipo de recurso.|
|resourceType|Sim|O tipo de recurso para o qual seriam exibidas métricas.|

## <a name="custom-resources"></a>Recursos personalizados

`"kind": "CustomResources"`

Pode definir várias exibições deste tipo. Cada vista representa um **exclusivo** tipo de recurso personalizado do provedor personalizado que definiu na **maintemplate. JSON**. Para obter uma introdução para fornecedores personalizados, consulte [descrição geral de visualização de provedores personalizados do Azure](custom-providers-overview.md).

Nesta vista, que pode executar GET, PUT, eliminar e POSTAR operações para o seu tipo de recurso personalizado. Operações POST podem ser global ações personalizadas ou ações personalizadas num contexto do seu tipo de recurso personalizado.

```json
{
    "kind": "CustomResources",
    "properties": {
        "displayName": "Test custom resource type",
        "version": "1.0.0",
        "resourceType": "testCustomResource",
        "createUIDefinition": { },
        "commands": [
            {
                "displayName": "Custom Test Action",
                "path": "testAction"
            },
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
|displayName|Sim|O título apresentado da vista. O título deve ser **exclusivo** para cada vista CustomResources no seu **viewDefinition.json**.|
|version|Não|A versão da plataforma utilizada para renderizar a exibição.|
|resourceType|Sim|O tipo de recurso personalizado. Tem de ser um **exclusivo** tipo de recurso personalizado do seu fornecedor personalizado.|
|createUIDefinition|Não|Criar definição de interface do usuário do esquema para criar o comando de recurso personalizado. Para obter uma introdução à criação de definições de interface do Usuário, consulte [introdução ao CreateUiDefinition](create-uidefinition-overview.md)|
|Comandos|Não|A matriz dos botões da barra de ferramentas adicionais da vista CustomResources, consulte [comandos](#commands).|
|Colunas|Não|A matriz de colunas do recurso personalizado. Se não definido o `name` coluna será apresentada por predefinição. A coluna tem de ter `"key"` e `"displayName"`. Para a chave, forneça a chave da propriedade a apresentar numa vista. Se aninhados, utilize ponto como delimitador, por exemplo, `"key": "name"` ou `"key": "properties.property1"`. Para nome a apresentar, forneça o nome a apresentar da propriedade a apresentar numa vista. Também pode fornecer um `"optional"` propriedade. Quando definido como true, a coluna está oculta numa exibição, por predefinição.|

## <a name="commands"></a>Comandos

Comandos é uma matriz dos botões da barra de ferramentas adicionais que são apresentados na página. Cada comando representa uma ação de publicação através do seu fornecedor de personalizado do Azure definidos na **maintemplate. JSON**. Para obter uma introdução para fornecedores personalizados, consulte [visão geral sobre provedores personalizados do Azure](custom-providers-overview.md).

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
|caminho|Sim|O nome da ação de fornecedor personalizado. A ação deve ser definida na **maintemplate. JSON**.|
|Ícone|Não|O ícone do botão de comando. Lista de ícones suportados é definida no [esquema JSON](https://schema.management.azure.com/schemas/viewdefinition/0.0.1-preview/ViewDefinition.json#).|
|createUIDefinition|Não|Crie o esquema de definição de interface do Usuário para o comando. Para obter uma introdução à criação de definições de interface do Usuário, consulte [introdução ao CreateUiDefinition](create-uidefinition-overview.md).|

## <a name="next-steps"></a>Passos Seguintes

- Para obter uma introdução às aplicações geridas, veja [Descrição geral da Aplicação Gerida do Azure](overview.md).
- Para obter uma introdução para fornecedores personalizados, consulte [visão geral sobre provedores personalizados do Azure](custom-providers-overview.md).
