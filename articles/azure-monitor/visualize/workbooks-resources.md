---
title: Parâmetros de recursos de livros do Azure Monitor
description: Aprenda a usar parâmetros de recursos para permitir a recolha de recursos em livros de trabalho. Utilize os parâmetros de recurso para definir o âmbito a partir do qual os dados são retirados.
services: azure-monitor
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.openlocfilehash: d4a6d04f88544b3a4f9f9c0c0b290d17f5ef148d
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101725582"
---
# <a name="workbook-resource-parameters"></a>Parâmetros de recursos do livro

Os parâmetros de recursos permitem a recolha de recursos em livros de trabalho. Isto é útil para definir o âmbito de aplicação a partir do qual obter os dados. Um exemplo é permitir que os utilizadores selecionem o conjunto de VMs, que os gráficos mais tarde utilizarão ao apresentar os dados.

Os valores dos apanhadores de recursos podem vir do contexto do livro, da lista estática ou das consultas de Gráfico de Recursos Azure.

## <a name="creating-a-resource-parameter-workbook-resources"></a>Criação de um parâmetro de recursos (recursos de livro)
1. Comece com um livro vazio no modo de edição.
2. Escolha _Adicionar parâmetros_ dos links dentro do livro.
3. Clique no botão azul _adicionar parâmetro._
4. No novo painel de parâmetros que aparece, insira:
    1. Nome do parâmetro: `Applications`
    2. Tipo de parâmetro: `Resource picker`
    3. Necessário: `checked`
    4. Permitir várias seleções: `checked`
5. Obtenha dados de: `Workbook Resources`
6. Incluir apenas tipos de recursos: `Application Insights`
7. Escolha 'Guardar' da barra de ferramentas para criar o parâmetro.

![Imagem mostrando a criação de um parâmetro de recurso usando recursos de livro](./media/workbooks-resources/resource-create.png)

## <a name="creating-a-resource-parameter-azure-resource-graph"></a>Criação de um parâmetro de recurso (Gráfico de Recursos Azure)
1. Comece com um livro vazio no modo de edição.
2. Escolha _Adicionar parâmetros_ dos links dentro do livro.
3. Clique no botão azul _adicionar parâmetro._
4. No novo painel de parâmetros que aparece, insira:
    1. Nome do parâmetro: `Applications`
    2. Tipo de parâmetro: `Resource picker`
    3. Necessário: `checked`
    4. Permitir várias seleções: `checked`
5. Obtenha dados de: `Query`
    1. Tipo de consulta: `Azure Resource Graph`
    2. Assinaturas: `Use default subscriptions`
    3. No controlo de consultas, adicione este corte
    ```kusto
    where type == 'microsoft.insights/components'
    | project value = id, label = name, selected = false, group = resourceGroup
    ```
7. Escolha 'Guardar' da barra de ferramentas para criar o parâmetro.

![Imagem mostrando a criação de um parâmetro de recurso usando O Gráfico de Recursos Azure](./media/workbooks-resources/resource-query.png)

> [!NOTE]
> O Azure Resource Graph ainda não está disponível em todas as nuvens. Certifique-se de que é suportado na sua nuvem-alvo se escolher esta abordagem.

[Documentação do Azure Resource Graph](../../governance/resource-graph/overview.md)

## <a name="creating-a-resource-parameter--json-list"></a>Criação de um parâmetro de recurso (lista JSON)
1. Comece com um livro vazio no modo de edição.
2. Escolha _Adicionar parâmetros_ dos links dentro do livro.
3. Clique no botão azul _adicionar parâmetro._
4. No novo painel de parâmetros que aparece, insira:
    1. Nome do parâmetro: `Applications`
    2. Tipo de parâmetro: `Resource picker`
    3. Necessário: `checked`
    4. Permitir várias seleções: `checked`
5. Obtenha dados de: `JSON`
    1. No controlo de conteúdos, adicione este json snippet
    ```json
    [
        { "value":"/subscriptions/<sub-id>/resourceGroups/<resource-group>/providers/<resource-type>/acmeauthentication", "label": "acmeauthentication", "selected":true, "group":"Acme Backend" },
        { "value":"/subscriptions/<sub-id>/resourceGroups/<resource-group>/providers/<resource-type>/acmeweb", "label": "acmeweb", "selected":false, "group":"Acme Frontend" }
    ]
    ```
    2. Acerte no botão _azul de atualização._
6. Configurar opcionalmente `Include only resource types` o _indesecimento da aplicação_
7. Escolha 'Guardar' da barra de ferramentas para criar o parâmetro.

## <a name="referencing-a-resource-parameter"></a>Referenciando um parâmetro de recurso
1. Adicione um controlo de consulta ao livro de trabalho e selecione um recurso Application Insights.
2. Utilize os _Insights de Aplicação_ para ligar o parâmetro ao controlo. Ao fazê-lo, define-se o âmbito da consulta aos recursos devolvidos pelo parâmetro no tempo de execução.
4. No controlo KQL, adicione este corte
    ```kusto
    requests
    | summarize Requests = count() by appName, name
    | order by Requests desc
    ```
5. Consulte a consulta para ver os resultados. 

![Imagem mostrando um parâmetro de recurso referenciado num controlo de consulta](./media/workbooks-resources/resource-reference.png)

> Esta abordagem pode ser usada para ligar recursos a outros controlos como métricas.

## <a name="resource-parameter-options"></a>Opções de parâmetros de recurso
| Parâmetro | Explicação | Exemplo |
| ------------- |:-------------|:-------------|
| `{Applications}` | O ID de recursos selecionados | _/subscrições/<sub-id>/resourceGroups/<>/fornecedores/<>do tipo de recursos /acmeauthentication_ |
| `{Applications:label}` | O rótulo do recurso selecionado | `acmefrontend` |
| `{Applications:value}` | O valor do recurso selecionado | _«/subscrições/<sub-id>/resourceGroups/<>/fornecedores/<>/acmeauthentication do grupo de recursos_ |
| `{Applications:name}` | O nome do recurso selecionado | `acmefrontend` |
| `{Applications:resourceGroup}` | O grupo de recursos do recurso selecionado | `acmegroup` |
| `{Applications:resourceType}` | O tipo de recurso selecionado | _microsoft.insights/componentes_ |
| `{Applications:subscription}` | A subscrição do recurso selecionado |  |
| `{Applications:grid}` | Uma grelha que mostra as propriedades dos recursos. Útil para renderizar em um bloco de texto enquanto depurando  |  |

## <a name="next-steps"></a>Passos seguintes

* [Começar a](./workbooks-overview.md#visualizations) aprender mais sobre livros de trabalho muitas opções de visualizações ricas.
* [Controle](./workbooks-access-control.md) e partilhe o acesso aos seus recursos do livro.