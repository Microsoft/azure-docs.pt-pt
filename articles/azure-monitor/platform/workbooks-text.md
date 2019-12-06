---
title: Parâmetros de texto de Azure Monitor pastas de trabalho
description: Simplifique relatórios complexos com pastas de trabalho parametrizadas predefinidas e personalizadas. Saiba mais sobre os parâmetros de texto da pasta de trabalho.
services: azure-monitor
author: mrbullwinkle
manager: carmonm
ms.service: azure-monitor
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: 3b7affb9c7d7be790d4279c1db8174a5dd3b2c2a
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74872779"
---
# <a name="workbook-text-parameters"></a>Parâmetros de texto da pasta de trabalho

Os parâmetros de caixa de texto fornecem uma maneira simples de coletar entrada de texto de usuários da pasta de trabalho. Eles são usados quando não é prático usar uma lista suspensa para coletar a entrada (por exemplo, um limite arbitrário ou filtros genéricos). As pastas de trabalho permitem que os autores obtenham o valor padrão da caixa de texto de uma consulta. Isso permite cenários interessantes como a definição do limite padrão com base no P95 da métrica.

Um uso comum de TextBoxes é como variáveis internas usadas por outros controles de pasta de trabalho. Isso é feito aproveitando uma consulta de valores padrão e tornando o controle de entrada invisível no modo de leitura. Por exemplo, um usuário pode querer que um limite venha de uma fórmula (não um usuário) e, em seguida, usar o limite em consultas subsequentes.

## <a name="creating-a-text-parameter"></a>Criando um parâmetro de texto
1. Comece com uma pasta de trabalho vazia no modo de edição.
2. Escolha _adicionar parâmetros_ nos links na pasta de trabalho.
3. Clique no botão azul _Adicionar parâmetro_ .
4. No novo painel de parâmetros que aparece, digite:
    1. Nome do parâmetro: `SlowRequestThreshold`
    2. Tipo de parâmetro: `Text`
    3. Necessário: `checked`
    4. Obter valor padrão da consulta: `unchecked`
5. Escolha ' salvar ' na barra de ferramentas para criar o parâmetro.

    ![Imagem mostrando a criação de um parâmetro de texto](./media/workbooks-text/text-create.png)

É assim que a pasta de trabalho será exibida no modo de leitura.

![Imagem mostrando um parâmetro de texto no modo de leitura](./media/workbooks-text/text-readmode.png)

## <a name="referencing-a-text-parameter"></a>Fazendo referência a um parâmetro de texto
1. Adicione um controle de consulta à pasta de trabalho selecionando o link `Add query` azul e selecione um recurso de Application Insights.
2. Na caixa KQL, adicione este trecho:
    ```kusto
    requests
    | summarize AllRequests = count(), SlowRequests = countif(duration >= {SlowRequestThreshold}) by name
    | extend SlowRequestPercent = 100.0 * SlowRequests / AllRequests
    | order by SlowRequests desc
    ```
3. Usando o parâmetro text com um valor de 500 acoplado com o controle de consulta, você efetivamente executa a consulta abaixo:
    ```kusto
    requests
    | summarize AllRequests = count(), SlowRequests = countif(duration >= 500) by name
    | extend SlowRequestPercent = 100.0 * SlowRequests / AllRequests
    | order by SlowRequests desc
    ```
4. Execute a consulta para ver os resultados

    ![Imagem mostrando um parâmetro de texto referenciado em KQL](./media/workbooks-text/text-reference.png)


## <a name="setting-default-values"></a>Definindo valores padrão
1. Comece com uma pasta de trabalho vazia no modo de edição.
2. Escolha _adicionar parâmetros_ nos links na pasta de trabalho.
3. Clique no botão azul _Adicionar parâmetro_ .
4. No novo painel de parâmetros que aparece, digite:
    1. Nome do parâmetro: `SlowRequestThreshold`
    2. Tipo de parâmetro: `Text`
    3. Necessário: `checked`
    4. Obter valor padrão da consulta: `checked`
5. Na caixa KQL, adicione este trecho:
    ```kusto
    requests
    | summarize round(percentile(duration, 95), 2)
    ```
    Essa consulta define o valor padrão da caixa de texto como a duração do 95 º percentil para todas as solicitações no aplicativo.
6. Execute a consulta para ver o resultado
7. Escolha ' salvar ' na barra de ferramentas para criar o parâmetro.

    ![Imagem mostrando um parâmetro de texto com valor padrão de KQL](./media/workbooks-text/text-default-value.png)

> [!NOTE]
> Embora este exemplo consulte Application Insights dados, a abordagem pode ser usada para qualquer fonte de dados baseada em log – Log Analytics, grafo de recursos do Azure, etc.

## <a name="next-steps"></a>Passos seguintes

* [Comece a aprender mais](workbooks-visualizations.md) sobre pastas de trabalho muitas opções de visualizações ricas.
* [Controle](workbooks-access-control.md) e compartilhe o acesso aos recursos da pasta de trabalho.
