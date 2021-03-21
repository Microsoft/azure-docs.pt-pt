---
title: Visualizações do mapa do Azure Monitor
description: Saiba mais sobre visualizações de mapas de livros do Azure Monitor.
services: azure-monitor
author: lgayhardt
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 11/25/2020
ms.author: lagayhar
ms.openlocfilehash: dd9bafab982923785898b7f135e5c0408aa19a14
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100615991"
---
# <a name="map-visualization"></a>Visualização de mapa

O mapa ajuda a visualizar questões de ponta de pin em regiões específicas e apresentando visões agregadas de alto nível dos dados de monitorização, fornecendo capacidade para agregar todos os dados mapeados a cada local/país/região.

A imagem abaixo mostra o total de transações e a latência de ponta a ponta para diferentes contas de armazenamento. Aqui o tamanho é determinado pelo número total de transações e as métricas de cor abaixo do mapa mostram a latência de ponta a ponta. Após a primeira observação, o número de transações na região **oeste dos EUA** é pequeno em comparação com a região leste dos **EUA,** mas a latência de ponta a ponta para a região **oeste dos EUA** é maior do que a região leste dos **EUA.** Isto fornece uma ideia inicial de que algo está mal para os **EUA Ocidentais.**

![Screenshot do mapa de localização de Azure](./media/workbooks-map-visualizations/map-performance-example.png)

## <a name="adding-a-map"></a>Adicionar um mapa

O mapa pode ser visualizado se os dados/métricas subjacentes mentem informações sobre Latitude/Longitude, informações sobre recursos Azure, informações de localização Azure ou país/região, nome ou código país/região.

### <a name="using-azure-location"></a>Usando a localização Azure

1. Mude o livro para editar o modo selecionando no item da barra de ferramentas **Editar.**
2. **Selecione Adicionar** e adicione *consulta*.
3. Altere a *Fonte de Dados* `Azure Resource Graph` para, em seguida, escolher qualquer subscrição que tenha conta de armazenamento.
4. Introduza a consulta abaixo para a sua análise e a consulta de **execução** selecionada .

    ```kusto
    where  type =~ 'microsoft.storage/storageaccounts'
    | summarize count() by location
    ```

5. Definir *tamanho* para `Large` .
6. Desa estade a *Visualização* para `Map` .
7. Todas as definições serão autopovoadas. Para configurações personalizadas, selecione o botão **Definições** do Mapa para abrir o painel de definições.
8. Abaixo está uma imagem da visualização do mapa que mostra contas de armazenamento de cada região de Azure para a subscrição selecionada.

![Screenshot do mapa de localização de Azure com a consulta acima](./media/workbooks-map-visualizations/map-azure-location-example.png)

### <a name="using-azure-resource"></a>Usando o recurso Azure

1. Mude o livro para editar o modo selecionando no item da barra de ferramentas **Editar.**
2. **Selecione Adicionar** e adicione *métrica*.
3. Utilize uma subscrição que tenha contas de armazenamento.
4. Alterar *o Tipo de Recurso* de e para o `storage account` *recurso* selecione várias contas de armazenamento.
5. Selecione **Add Metric** e adicione uma métrica de transação.
    1. Espaço de nomes: `Account`
    2. Métrica: `Transactions`
    3. Agregação: `Sum`
    
    ![Screenshot da métrica de transação](./media/workbooks-map-visualizations/map-transaction-metric.png)
1. Selecione **Add Metric** e adicione a métrica de latência E2E de sucesso.
    1. Espaço de nomes: `Account`
    1. Métrica: `Success E2E Latency`
    1. Agregação: `Average`
    
    ![Screenshot do sucesso métrica de latência de ponta a ponta](./media/workbooks-map-visualizations/map-e2e-latency-metric.png)
1. Definir *tamanho* para `Large` .
1. Desa ajuste o tamanho *da visualização* para `Map` .
1. Nas **Definições de Mapas** definem as seguintes definições:
    1. Informações de localização utilizando: `Azure Resource`
    1. Campo de recursos Azure: `Name`
    1. Tamanho por: `microsoft.storage/storageaccounts-Transaction-Transactions`
    1. Agregação para a localização: `Sum of values`
    1. Tipo de coloração: `Heatmap`
    1. Cor por: `microsoft.storage/storageaccounts-Transaction-SuccessE2ELatency`
    1. Agregação para a cor: `Sum of values`
    1. Paleta de cores: `Green to Red`
    1. Valor mínimo: `0`
    1. Valor métrico: `microsoft.storage/storageaccounts-Transaction-SuccessE2ELatency`
    1. Agregar outras métricas por: `Sum of values`
    1. Selecione a caixa **de formatação personalizada**
    1. Unidade: `Milliseconds`
    1. Estilo: `Decimal`
    1. Dígitos fracionais máximos: `2`

### <a name="using-countryregion"></a>Utilização de país/região

1. Mude o livro para editar o modo selecionando no item da barra de ferramentas **Editar.**
2. Selecione **Adicionar,* em seguida, *adicionar consulta*.
3. Altere a *fonte de dados* para `Log` .
4. Selecione *o tipo de recurso* como , em `Application Insights` seguida, escolha qualquer recurso application Insights que tenha dados pageViews.
5. Utilize o editor de consulta para introduzir o KQL para a sua análise e selecione **'**

    ```kusto
    pageViews
    | project duration, itemCount, client_CountryOrRegion
    | limit 20
    ```

6. Desa ajuste os valores de tamanho para `Large` .
7. Desa estale a visualização para `Map` .
8. Todas as definições serão autopovoadas. Para configurações personalizadas, selecione **Definições de mapas**.

### <a name="using-latitudelocation"></a>Utilização de latitude/localização

1. Mude o livro para editar o modo selecionando no item da barra de ferramentas **Editar.**
2. Selecione **Adicionar,* em seguida, *adicionar consulta*.
3. Altere a *fonte de dados* para `JSON` .
1. Introduza os dados JSON abaixo no editor de consulta e selecione **'Run'.**
1. Desa ajuste os valores *de tamanho* para `Large` .
1. Desa estade a *Visualização* para `Map` .
1. Nas **definições do mapa** em "Definições métricas", defina o Rótulo *métrico* para `displayName` selecionar Guardar e **Fechar**.

A visualização do mapa abaixo mostra os utilizadores para cada localização de latitude e longitude com a etiqueta selecionada para métricas.

![Screenshot de uma visualização de mapa que mostra os utilizadores para cada localização de latitude e longitude com a etiqueta selecionada para métricas](./media/workbooks-map-visualizations/map-latitude-longitude-example.png)

## <a name="map-settings"></a>Definições do mapa

### <a name="layout-settings"></a>Definições de layout

| Definição | Explicação |
|:-------------|:-------------|
| `Location info using` | Selecione uma maneira de obter a localização dos itens mostrados no mapa. <br> **Latitude/Longitude**: Selecione esta opção se houver colunas com informações de latitude e longitude. Cada linha com dados de latitude e longitude será mostrado como um item distinto no mapa. <br> **Localização azul**: Selecione esta opção se houver uma coluna que tenha informações sobre a localização Azure (leste, westeurope, centralindia, etc.). Especifique esta coluna e irá obter a latitude e longitude correspondentes para cada localização Azure, agrupam as mesmas linhas de localização (com base na agregação especificada) para mostrar as localizações no mapa. <br> **Recurso azul**: Selecione esta opção se houver uma coluna que tenha informações sobre recursos Azure (conta de armazenamento, conta cosmosdb, etc.). Especifique esta coluna e irá obter a latitude e longitude correspondentes para cada recurso Azure, linhas de grupo da mesma localização (localização Azure) juntas (com base na agregação especificada) para mostrar as localizações no mapa. <br> **País/Região**: Selecione esta opção se houver uma coluna que tenha informações sobre o nome/código país/região (EUA, Estados Unidos, IN, Índia, CN, China). Especifique esta coluna e irá obter a latitude e longitude correspondentes para cada País/Região/Código e agrupar as linhas juntamente com o mesmo nome Country-Region Código/Região do País para mostrar as localizações no mapa. O código Country Name and Country não será agrupado como uma única entidade no mapa.
| `Latitude/Longitude` | Estas duas opções serão visíveis se o valor do campo de Localização Info for: Latitude/Longitude. Selecione a coluna que tem latitude no campo de latitude e longitude no campo de longitude, respectivamente. |
| `Azure location field` | Esta opção será visível se o valor do campo de Informação de Localização for: Localização azul. Selecione a coluna que a informação de localização Azure. |
| `Azure resource field` | Esta opção será visível se o valor do campo de Informação de Localização for: Recurso Azure. Selecione a coluna que a informação do recurso Azure. |
| `Country/Region field` | Esta opção será visível se o valor do campo de Informação de Localização for: País ou região. Selecione a coluna que a informação País/Região. |
| `Size by` | Esta opção controla o tamanho dos itens apresentados no mapa. O tamanho depende do valor na coluna especificada pelo utilizador. Atualmente, o raio do círculo é diretamente proporcional à raiz quadrada do valor da coluna. Se 'Nenhum...' é selecionado, todos os círculos mostrarão o tamanho da região padrão.|
| `Aggregation for location` | Este campo especifica como agregar o **tamanho por** colunas que têm a mesma Localização Azul/Azure Resource/Country-Region. |
| `Minimum region size` | Este campo especifica qual é o raio mínimo do item mostrado no mapa. Isto é usado quando há uma diferença significativa entre o tamanho pelos valores da coluna, portanto itens menores dificilmente são visíveis no mapa. |
| `Maximum region size` | Este campo especifica qual é o raio máximo do item mostrado no mapa. Isto é usado quando o tamanho por valores da coluna é extremamente grande e eles estão cobrindo uma enorme área do mapa.|
| `Default region size` | Este campo especifica qual é o raio padrão do item mostrado no mapa. O raio padrão é usado quando o tamanho por coluna é 'Nenhum...' ou o valor é 0.|
| `Minimum value` | O valor mínimo usado para calcular o tamanho da região. Se não for especificado, o valor mínimo será o menor valor após a agregação. |
| `Maximum value` | O valor máximo usado para calcular o tamanho da região. Se não for especificado, o valor máximo será o maior valor após agregação.|
| `Opacity of items on Map` | Este campo especifica como são transparentes os itens apresentados no mapa. Opacidade de 1 significa, sem transparência, onde a opacidade de 0 significa, os itens não serão visíveis no mapa. Se houver muitos itens no mapa, a opacidade pode ser definida de baixo valor para que todos os itens sobrepostos sejam visíveis.|

### <a name="color-settings"></a>Definições de cor

| Tipo de coloração | Explicação |
|:------------- |:-------------|
| `None` | Todos os nós têm a mesma cor. |
| `Thresholds` | Neste tipo, as cores das células são definidas por regras limiares (por exemplo, _CPU > 90% => Vermelho, 60% > CPU > 90% = amarelo>, CPU < 60% => Verde)._ <ul><li> **Cor por**: O valor desta coluna será utilizado pela lógica Thresholds/Heatmap.</li> <li>**Agregação para a cor**: Este campo especifica como agregar a **cor por** colunas que têm a mesma Localização Azul/Recurso Azure/Região país-região. </li> <ul> |
| `Heatmap` | Neste tipo, as células são coloridas com base na paleta de cores e cor por campo. Isto também terá a mesma **Cor por** e **Agregação para** opções de cores como limiares. |

### <a name="metric-settings"></a>Definições métricas
| Definição | Explicação |
|:------------- |:-------------|
| `Metric Label` | Esta opção será visível se o valor do campo de Localização Info for: Latitude/Longitude. Utilizando esta funcionalidade, o utilizador pode escolher a etiqueta para mostrar as métricas apresentadas abaixo do mapa. |
| `Metric Value` | Este campo especifica o valor métrico a ser mostrado abaixo do mapa. |
| `Create 'Others' group after` | Este campo especifica o limite antes da criação de um grupo "Outros". |
| `Aggregate 'Others' metrics by` | Este campo especifica a agregação utilizada para o grupo "Outros", se for mostrada. |
| `Custom formatting` | Utilize este campo para definir unidades, estilo e opções de formatação para valores de números. Isto é o mesmo que [a formatação personalizada da grelha.](../visualize/workbooks-grid-visualizations.md#custom-formatting)|

## <a name="next-steps"></a>Passos seguintes

- Saiba como criar [visualizações de pentes de mel em livros de trabalho.](../visualize/workbooks-honey-comb.md)