---
title: Use um Caderno Jupyter para analisar dados no Azure Data Explorer
description: Este tópico mostra-lhe como analisar dados no Azure Data Explorer usando um Caderno Jupyter e a extensão Kqlmagic.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 83902ea5a3e73603311a0c469126ed603d0ebd16
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77064874"
---
# <a name="use-a-jupyter-notebook-and-kqlmagic-extension-to-analyze-data-in-azure-data-explorer"></a>Use um Caderno Jupyter e uma extensão Kqlmagic para analisar dados no Azure Data Explorer

Jupyter Notebook é uma aplicação web de código aberto que permite criar e partilhar documentos contendo código vivo, equações, visualizações e texto narrativo. O uso inclui limpeza e transformação de dados, simulação numérica, modelação estatística, visualização de dados e machine learning.
[Jupyter Notebook](https://jupyter.org/) suporta funções mágicas que estendem as capacidades do núcleo suportando comandos adicionais. A magia KQL é um comando que estende as capacidades do kernel Python em Jupyter Notebook para que você possa executar consultas linguísticas Kusto de forma nativa. Você pode facilmente combinar linguagem de consulta Python e Kusto para consultar `render` e visualizar dados usando biblioteca de Plot.ly rica integrada com comandos. São suportadas fontes de dados para consultas de execução. Estas fontes de dados incluem o Azure Data Explorer, um serviço de exploração rápida e altamente escalável de dados para dados de log e telemetria, bem como registos do Monitor Azure e Insights de Aplicação. A magia KQL também trabalha com cadernos Azure, Jupyter Lab e visual studio code jupyter extensão.

## <a name="prerequisites"></a>Pré-requisitos

- Conta de e-mail organizacional que é membro do Azure Ative Directory (AAD).
- Caderno Jupyter instalado na sua máquina local ou use Cadernos Azure e clone a amostra [Do Portátil Azure](https://kustomagicsamples-manojraheja.notebooks.azure.com/j/notebooks/Getting%20Started%20with%20kqlmagic%20on%20Azure%20Data%20Explorer.ipynb)

## <a name="install-kql-magic-library"></a>Instale biblioteca mágica KQL

1. Instale magia KQL:

    ```python
    !pip install Kqlmagic --no-cache-dir  --upgrade
    ```
    > [!NOTE]
    > Ao utilizar cadernos Azure, este passo não é necessário.

1. Carregar magia KQL:

    ```python
    %reload_ext Kqlmagic
    ```
    > [!NOTE]
    > Mude a versão Kernel para Python 3.6 clicando em Kernel > Change Kernel > Python 3.6
    
## <a name="connect-to-the-azure-data-explorer-help-cluster"></a>Ligue-se ao cluster de ajuda do Explorador de Dados Azure

Utilize o seguinte comando para ligar à base de dados *de amostras* alojada no cluster *Ajuda.* Para utilizadores não Microsoft AAD, `Microsoft.com` substitua o nome do inquilino pelo seu Inquilino AAD.

```python
%kql AzureDataExplorer://tenant="Microsoft.com";code;cluster='help';database='Samples'
```

## <a name="query-and-visualize"></a>Consulta e visualização

Consulta de dados utilizando o operador de [renderização](/azure/kusto/query/renderoperator) e visualizar dados utilizando a biblioteca ploy.ly. Esta consulta e visualização fornece uma experiência integrada que utiliza kQL nativo. Kqlmagic suporta a `timepivot`maioria `pivotchart`das `ladderchart`tabelas exceto, e . Render é suportado com `kind` `ysplit`todos `accumulate`os atributos exceto , e . 

### <a name="query-and-render-piechart"></a>Consulta e render gráfico

```python
%%kql
StormEvents
| summarize statecount=count() by State
| sort by statecount 
| limit 10
| render piechart title="My Pie Chart by State"
```

### <a name="query-and-render-timechart"></a>Consulta e gráfico de tempo de renderização

```python
%%kql
StormEvents
| summarize count() by bin(StartTime,7d)
| render timechart
```

> [!NOTE]
> Estes gráficos são interativos. Selecione um intervalo de tempo para ampliar num momento específico.

### <a name="customize-the-chart-colors"></a>Personalize as cores do gráfico

Se não gostar da paleta de cores padrão, personalize os gráficos utilizando opções de paleta. As paletas disponíveis podem ser encontradas aqui: Escolha a paleta de cores para o resultado do gráfico de [consulta mágica KQL](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FColorYourCharts.ipynb)

1. Para uma lista de paletas:

    ```python
    %kql --palettes -popup_window
    ```

1. Selecione a `cool` paleta de cores e volte a tornar a consulta:

    ```python
    %%kql -palette_name "cool"
    StormEvents
    | summarize statecount=count() by State
    | sort by statecount
    | limit 10
    | render piechart title="My Pie Chart by State"
    ```

## <a name="parameterize-a-query-with-python"></a>Parametrize uma consulta com Python

A magia KQL permite um simples intercâmbio entre a linguagem da consulta de Kusto e python. Para saber mais: [Parametrize a sua consulta mágica KQL com Python](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FParametrizeYourQuery.ipynb)

### <a name="use-a-python-variable-in-your-kql-query"></a>Use uma variável Python na sua consulta KQL

Pode utilizar o valor de uma variável Python na sua consulta para filtrar os dados:

```python
statefilter = ["TEXAS", "KANSAS"]
```

```python
%%kql
let _state = statefilter;
StormEvents 
| where State in (_state) 
| summarize statecount=count() by bin(StartTime,1d), State
| render timechart title = "Trend"
```

### <a name="convert-query-results-to-pandas-dataframe"></a>Converter resultados de consulta para Pandas DataFrame

Pode aceder aos resultados de uma consulta KQL no Pandas DataFrame. Aceda aos resultados da `_kql_raw_result_` última consulta executada por variável e facilmente converta os resultados em Pandas DataFrame da seguinte forma:

```python
df = _kql_raw_result_.to_dataframe()
df.head(10)
```

### <a name="example"></a>Exemplo

Em muitos cenários de análise, você pode querer criar cadernos reutilizáveis que contêm muitas consultas e alimentar os resultados de uma consulta em consultas subsequentes. O exemplo abaixo utiliza `statefilter` a variável Python para filtrar os dados.

1. Execute uma consulta para ver os `DamageProperty`10 estados do top 10 com o máximo:

    ```python
    %%kql
    StormEvents
    | summarize max(DamageProperty) by State
    | order by max_DamageProperty desc
    | limit 10
    ```

1. Execute uma consulta para extrair o estado superior e coloque-o numa variável Python:

    ```python
    df = _kql_raw_result_.to_dataframe()
    statefilter =df.loc[0].State
    statefilter
    ```

1. Execute uma consulta `let` usando a declaração e a variável Python:

    ```python
    %%kql
    let _state = statefilter;
    StormEvents 
    | where State in (_state)
    | summarize statecount=count() by bin(StartTime,1d), State
    | render timechart title = "Trend"
    ```

1. Executar o comando de ajuda:

    ```python
    %kql --help "help"
    ```

> [!TIP]
> Para receber informações sobre `%config Kqlmagic`todas as configurações disponíveis. Para resolver problemas e capturar erros de Kusto, tais como problemas de ligação e consultas incorretas, use`%config Kqlmagic.short_errors=False`

## <a name="next-steps"></a>Passos seguintes

Executar o comando de ajuda para explorar os seguintes cadernos de amostra que contenham todas as funcionalidades suportadas:
- [Começar com magia KQL para Azure Data Explorer](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FQuickStart.ipynb) 
- [Começar com magia KQL para Insights de Aplicação](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FQuickStartAI.ipynb) 
- [Começar com magia KQL para registos do Monitor Azure](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FQuickStartLA.ipynb) 
- [Parametrize a sua consulta mágica KQL com Python](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FParametrizeYourQuery.ipynb) 
- [Escolha a paleta de cores para o resultado do gráfico de consulta mágica KQL](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FColorYourCharts.ipynb)
