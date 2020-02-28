---
title: Parâmetros de recursos dos livros Azure Monitor
description: Simplificar relatórios complexos com livros pré-construídos e parametrizados personalizados
services: azure-monitor
author: mrbullwinkle
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: cc2cde7932f783f63ee2783f0589ce4f88f248a2
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2020
ms.locfileid: "77658107"
---
# <a name="workbook-resource-parameters"></a>Parâmetros de recursos do livro

Os parâmetros de recursos permitem a recolha de recursos em livros de trabalho. Isto é útil para definir o âmbito a partir do qual obter os dados. Um exemplo é permitir que os utilizadores selecionem o conjunto de VMs, que os gráficos mais tarde utilizarão ao apresentar os dados.

Os valores dos apanhadores de recursos podem vir do contexto do livro, da lista estática ou das consultas do Azure Resource Graph.

## <a name="creating-a-resource-parameter-workbook-resources"></a>Criação de um parâmetro de recursos (recursos do livro)
1. Comece com um livro vazio no modo de edição.
2. Escolha _Adicionar parâmetros_ dos links dentro do livro.
3. Clique no botão azul _Adicionar Parâmetro._
4. Na nova vidraça de parâmetroque aparece:
    1. Nome do parâmetro: `Applications`
    2. Tipo de parâmetro: `Resource picker`
    3. Obrigatório: `checked`
    4. Permitir várias seleções: `checked`
5. Obtenha dados de: `Workbook Resources`
6. Incluir apenas tipos de recursos: `Application Insights`
7. Escolha 'Guardar' da barra de ferramentas para criar o parâmetro.

![Imagem mostrando a criação de um parâmetro de recursos usando recursos do livro](./media/workbooks-resources/resource-create.png)

## <a name="creating-a-resource-parameter-azure-resource-graph"></a>Criação de um parâmetro de recursos (Gráfico de Recursos Azure)
1. Comece com um livro vazio no modo de edição.
2. Escolha _Adicionar parâmetros_ dos links dentro do livro.
3. Clique no botão azul _Adicionar Parâmetro._
4. Na nova vidraça de parâmetroque aparece:
    1. Nome do parâmetro: `Applications`
    2. Tipo de parâmetro: `Resource picker`
    3. Obrigatório: `checked`
    4. Permitir várias seleções: `checked`
5. Obtenha dados de: `Query`
    1. Tipo de consulta: `Azure Resource Graph`
    2. Assinaturas: `Use default subscriptions`
    3. No controlo de consulta, adicione este corte
    ```kusto
    where type == 'microsoft.insights/components'
    | project value = id, label = name, selected = false, group = resourceGroup
    ```
7. Escolha 'Guardar' da barra de ferramentas para criar o parâmetro.

![Imagem mostrando a criação de um parâmetro de recursos usando o Gráfico de Recursos Azure](./media/workbooks-resources/resource-query.png)

> [!NOTE]
> O Azure Resource Graph ainda não está disponível em todas as nuvens. Certifique-se de que é suportado na sua nuvem-alvo se escolher esta abordagem.

[Documentação do Gráfico de Recursos Azure](https://docs.microsoft.com/azure/governance/resource-graph/overview)

## <a name="creating-a-resource-parameter--json-list"></a>Criação de um parâmetro de recurso (lista JSON)
1. Comece com um livro vazio no modo de edição.
2. Escolha _Adicionar parâmetros_ dos links dentro do livro.
3. Clique no botão azul _Adicionar Parâmetro._
4. Na nova vidraça de parâmetroque aparece:
    1. Nome do parâmetro: `Applications`
    2. Tipo de parâmetro: `Resource picker`
    3. Obrigatório: `checked`
    4. Permitir várias seleções: `checked`
5. Obtenha dados de: `JSON`
    1. No controlo de conteúdos, adicione este snippet json
    ```json
    [
        { "value":"/subscriptions/<sub-id>/resourceGroups/<resource-group>/providers/<resource-type>/acmeauthentication", "label": "acmeauthentication", "selected":true, "group":"Acme Backend" },
        { "value":"/subscriptions/<sub-id>/resourceGroups/<resource-group>/providers/<resource-type>/acmeweb", "label": "acmeweb", "selected":false, "group":"Acme Frontend" }
    ]
    ```
    2. Acerte no botão de _atualização_ azul.
6. Definir opcionalmente o `Include only resource types` para insights de _aplicação_
7. Escolha 'Guardar' da barra de ferramentas para criar o parâmetro.

## <a name="referencing-a-resource-parameter"></a>Referenciando um parâmetro de recursos
1. Adicione um controlo de consulta ao livro e selecione um recurso Application Insights.
2. Utilize a queda dos Insights de _Aplicação_ para ligar o parâmetro ao comando. Ao fazê-lo define o âmbito da consulta aos recursos devolvidos pelo parâmetro em tempo de execução.
4. No controlo KQL, adicione este corte
    ```kusto
    requests
    | summarize Requests = count() by appName, name
    | order by Requests desc
    ```
5. Corra a consulta para ver os resultados. 

![Imagem mostrando um parâmetro de recurso referenciado em um controlo de consulta](./media/workbooks-resources/resource-reference.png)

> Esta abordagem pode ser utilizada para ligar recursos a outros controlos, como as métricas.

## <a name="resource-parameter-options"></a>Opções de parâmetros de recursos
| Parâmetro | Explicação | Exemplo |
| ------------- |:-------------|:-------------|
| `{Applications}` | O ID de recurso selecionado | _/subscrições/<sub-id>/resourceGroups/<resource-group>/providers/<resource-type>/acmeauthentication_ |
| `{Applications:label}` | O rótulo do recurso selecionado | `acmefrontend` |
| `{Applications:value}` | O valor do recurso selecionado | _'/subscrições/<sub-id>/resourceGroups/<resource-group>/providers/<resource-type>/acmeauthentication'_ |
| `{Applications:name}` | O nome do recurso selecionado | `acmefrontend` |
| `{Applications:resourceGroup}` | O grupo de recursos do recurso selecionado | `acmegroup` |
| `{Applications:resourceType}` | O tipo de recurso selecionado | _microsoft.insights/componentes_ |
| `{Applications:subscription}` | A subscrição do recurso selecionado |  |
| `{Applications:grid}` | Uma grade mostrando as propriedades dos recursos. Útil para renderizar em um bloco de texto enquanto depura  |  |

## <a name="next-steps"></a>Passos seguintes

* [Começar a](workbooks-visualizations.md) aprender mais sobre livros de trabalho muitas opções de visualizações ricas.
* [Controle](workbooks-access-control.md) e partilhe o acesso aos recursos do seu livro.
