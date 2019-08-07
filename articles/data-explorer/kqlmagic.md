---
title: Usar um Jupyter Notebook para analisar dados no Azure Data Explorer
description: Este tópico mostra como analisar dados no Azure Data Explorer usando uma Jupyter Notebook e a extensão Kqlmagic.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 312e39ff1b699bb3c7f2baea3c66cbf8999ee44b
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68814513"
---
# <a name="use-a-jupyter-notebook-and-kqlmagic-extension-to-analyze-data-in-azure-data-explorer"></a>Usar uma extensão Jupyter Notebook e Kqlmagic para analisar dados no Azure Data Explorer

Jupyter Notebook é um aplicativo Web de software livre que permite criar e compartilhar documentos que contenham código ao vivo, equações, visualizações e texto de narração. O uso inclui limpeza e transformação de dados, simulação numérica, modelagem estatística, visualização de dados e aprendizado de máquina.
O [Jupyter Notebook](https://jupyter.org/) dá suporte a funções mágicas que estendem os recursos do kernel, oferecendo suporte a comandos adicionais. KQL Magic é um comando que estende os recursos do kernel do Python no Jupyter Notebook para que você possa executar consultas de linguagem Kusto nativamente. Você pode combinar facilmente a linguagem de consulta Python e Kusto para consultar e Visualizar dados usando a biblioteca Plot.ly `render` avançada integrada com comandos. Há suporte para fontes de dados para executar consultas. Essas fontes de dados incluem o Azure Data Explorer, um serviço de exploração de dados rápido e altamente escalonável para dados de log e telemetria, bem como Azure Monitor logs e Application Insights. KQL mágica também funciona com Azure Notebooks, Jupyter Lab e Visual Studio Code extensão Jupyter.

## <a name="prerequisites"></a>Pré-requisitos

- Conta de email organizacional que é membro de Azure Active Directory (AAD).
- Jupyter Notebook instalado no computador local ou usar Azure Notebooks e clonar o [bloco de anotações do Azure](https://kustomagicsamples-manojraheja.notebooks.azure.com/j/notebooks/Getting%20Started%20with%20kqlmagic%20on%20Azure%20Data%20Explorer.ipynb) de exemplo

## <a name="install-kql-magic-library"></a>Instalar a biblioteca mágica do KQL

1. Instale o KQL Magic:

    ```python
    !pip install Kqlmagic --no-cache-dir  --upgrade
    ```
    > [!NOTE]
    > Ao usar Azure Notebooks, essa etapa não é necessária.

1. KQL a mágica do carregamento:

    ```python
    %reload_ext Kqlmagic
    ```

## <a name="connect-to-the-azure-data-explorer-help-cluster"></a>Conectar-se ao cluster de ajuda do Data Explorer do Azure

Use o comando a seguir para se conectar ao banco de dados de *exemplos* hospedado no cluster de *ajuda* . Para usuários não Microsoft AAD, substitua o nome `Microsoft.com` do locatário pelo seu locatário do AAD.

```python
%kql AzureDataExplorer://tenant="Microsoft.com";code;cluster='help';database='Samples'
```

## <a name="query-and-visualize"></a>Consulta e visualização

Consultar dados usando o [operador render](/azure/kusto/query/renderoperator) e Visualizar dados usando a biblioteca Ploy.ly. Essa consulta e visualização fornecem uma experiência integrada que usa o KQL nativo. O Kqlmagic dá suporte à `timepivot`maioria `pivotchart`dos gráficos `ladderchart`, exceto, e. O processamento tem suporte com todos os `kind`atributos `ysplit`, exceto `accumulate`, e. 

### <a name="query-and-render-piechart"></a>Consultar e renderizar piechart

```python
%%kql
StormEvents
| summarize statecount=count() by State
| sort by statecount 
| limit 10
| render piechart title="My Pie Chart by State"
```

### <a name="query-and-render-timechart"></a>Consultar e renderizar o gráfico de

```python
%%kql
StormEvents
| summarize count() by bin(StartTime,7d)
| render timechart
```

> [!NOTE]
> Esses gráficos são interativos. Selecione um intervalo de tempo para aplicar zoom em um horário específico.

### <a name="customize-the-chart-colors"></a>Personalizar as cores do gráfico

Se você não gostar da paleta de cores padrão, personalize os gráficos usando as opções da paleta. As paletas disponíveis podem ser encontradas aqui: [Escolher a paleta de cores para o resultado do gráfico de consulta mágica KQL](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FColorYourCharts.ipynb)

1. Para obter uma lista de paletas:

    ```python
    %kql --palettes -popup_window
    ```

1. Selecione a `cool` paleta de cores e processe a consulta novamente:

    ```python
    %%kql -palette_name "cool"
    StormEvents
    | summarize statecount=count() by State
    | sort by statecount
    | limit 10
    | render piechart title="My Pie Chart by State"
    ```

## <a name="parameterize-a-query-with-python"></a>Parametrizar uma consulta com Python

O KQL Magic permite o intercâmbio simples entre a linguagem de consulta Kusto e o Python. Para saber mais: [Parametrizar sua consulta mágica KQL com Python](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FParametrizeYourQuery.ipynb)

### <a name="use-a-python-variable-in-your-kql-query"></a>Usar uma variável do Python em sua consulta do KQL

Você pode usar o valor de uma variável do Python em sua consulta para filtrar os dados:

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

### <a name="convert-query-results-to-pandas-dataframe"></a>Converter os resultados da consulta no dataframe do pandas

Você pode acessar os resultados de uma consulta KQL no pandas dataframe. Acesse os resultados da última consulta executada `_kql_raw_result_` por variável e converta facilmente os resultados em pandas dataframe da seguinte maneira:

```python
df = _kql_raw_result_.to_dataframe()
df.head(10)
```

### <a name="example"></a>Exemplo

Em muitos cenários de análise, talvez você queira criar blocos de anotações reutilizáveis que contenham muitas consultas e alimentar os resultados de uma consulta em consultas subsequentes. O exemplo a seguir usa a variável `statefilter` do Python para filtrar os dados.

1. Execute uma consulta para exibir os 10 primeiros Estados com máximo `DamageProperty`:

    ```python
    %%kql
    StormEvents
    | summarize max(DamageProperty) by State
    | order by max_DamageProperty desc
    | limit 10
    ```

1. Execute uma consulta para extrair o estado superior e defini-lo em uma variável do Python:

    ```python
    df = _kql_raw_result_.to_dataframe()
    statefilter =df.loc[0].State
    statefilter
    ```

1. Execute uma consulta usando a `let` instrução e a variável do Python:

    ```python
    %%kql
    let _state = statefilter;
    StormEvents 
    | where State in (_state)
    | summarize statecount=count() by bin(StartTime,1d), State
    | render timechart title = "Trend"
    ```

1. Execute o comando de ajuda:

    ```python
    %kql --help "help"
    ```

> [!TIP]
> Para receber informações sobre todas as configurações disponíveis `%config KQLmagic`, use. Para solucionar problemas e capturar erros do Kusto, como problemas de conexão e consultas incorretas, use`%config Kqlmagic.short_errors=False`

## <a name="next-steps"></a>Passos seguintes

Execute o comando de ajuda para explorar os seguintes blocos de anotações de exemplo que contêm todos os recursos com suporte:
- [Introdução ao KQL Magic para Azure Data Explorer](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FQuickStart.ipynb) 
- [Introdução ao KQL Magic para Application Insights](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FQuickStartAI.ipynb) 
- [Introdução ao KQL Magic para logs de Azure Monitor](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FQuickStartLA.ipynb) 
- [Parametrize sua consulta KQL mágica com Python](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FParametrizeYourQuery.ipynb) 
- [Escolher a paleta de cores para o resultado do gráfico de consulta mágica KQL](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FColorYourCharts.ipynb)
