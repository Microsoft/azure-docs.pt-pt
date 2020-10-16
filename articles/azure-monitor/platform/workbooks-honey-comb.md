---
title: Visualizações do pente de mel do Azure Monitor
description: Saiba mais sobre visualizações de pente de mel do Azure Monitor.
services: azure-monitor
author: lgayhardt
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/18/2020
ms.author: lagayhar
ms.openlocfilehash: 859240623c5dd3009890cd2633ffb9cccf230080
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91347431"
---
# <a name="honey-comb-visualizations"></a>Visualizações do pente de mel

Os pentes de mel permitem vistas de alta densidade de métricas ou categorias que podem opcionalmente ser agrupadas como clusters. São úteis na identificação visual de hotspots e perfurações posteriores.

A imagem abaixo mostra a utilização do CPU de máquinas virtuais em duas subscrições. Cada célula representa uma máquina virtual e a cor/rótulo representa a sua utilização média do CPU (os vermelhos são máquinas quentes). As máquinas virtuais estão agrupadas por subscrição.

[![Screenshot mostra a utilização do CPU de máquinas virtuais em duas subscrições](.\media\workbooks-honey-comb\cpu-example.png)](.\media\workbooks-honey-comb\cpu-example.png#lightbox)

## <a name="adding-a-honey-comb"></a>Adicionando um pente de mel

1. Mude o livro para editar o modo clicando no item da barra de ferramentas Editar.
2. Utilizar **Adicione**  na parte inferior e, em *seguida, adicione a consulta* para adicionar um controlo de consulta de registo ao livro.
3. Selecione os "Logs" como fonte de *dados*, "Log Analytics" como tipo *de recurso*, e para *o ponto de recurso* para um espaço de trabalho que tenha registo de desempenho da máquina virtual.
4. Utilize o editor de consulta para introduzir o KQL para a sua análise.

```kusto
    Perf
| where CounterName == 'Available MBytes'
| summarize CounterValue = avg(CounterValue) by Computer, _ResourceId
| extend ResourceGroup = extract(@'/subscriptions/.+/resourcegroups/(.+)/providers/microsoft.compute/virtualmachines/.+', 1, _ResourceId)
| extend ResourceGroup = iff(ResourceGroup == '', 'On-premise computers', ResourceGroup), Id = strcat(_ResourceId, '::', Computer)
```

5. Fazer consulta.
6. Deite a *visualização* em "Gráfico".
7. Selecione **Configurações de gráficos**.
    1. Em *Campos de Layout* na parte inferior, definido:
        1. Tipo de gráfico: **Hive Clusters**.
        2. Nó Id: `Id` .
        3. Grupo por: `None` .
        4. Tamanho do nó: 100.
        5. Margem entre hexágonos: `5` .
        6. Tipo de coloração: **Mapa de calor**.
        7. Campo de cor de nó: `CouterValue` .
        8. Paleta de cores: `Red to Green` .
        9. Valor mínimo: `100` .
        10. Valor máximo: `2000` .
    2. Nas *Definições do formato do nó* na parte superior, ajuste:
        1. Conteúdo superior:
            1. Coluna de utilização: `Computer` .
            2. Renderizador de colunas" `Text` .
        9. Conteúdo do centro:
            1. Coluna de utilização: `CounterValue` .
            2. Renderer coluna: `Big Number` .
            3. Paleta de cores: `None` .
            4. Verifique a caixa *de formatação de números personalizados.*
            5. Unidades: `Megabytes` .
            6. Dígitos fracionais máximos: `1` .
8. **Selecione Guardar e fechar** o botão na parte inferior do painel.

[![Screenshot do controlo de consultas, definições de gráficos e pente de mel com a consulta e configurações acima](.\media\workbooks-honey-comb\available-memory.png)](.\media\workbooks-honey-comb\available-memory.png#lightbox)

## <a name="honey-comb-layout-settings"></a>Definições de layout de pente de mel

| Definição | Explicação |
|:------------- |:-------------|
| `Node Id` | Seleciona uma coluna que fornece o ID único de nós. O valor da coluna pode ser de corda ou de um número. |
| `Group By Field` | Selecione a coluna para agrupar os nós. |
| `Node Size` | Define o tamanho das células hexagonais. Use com a `Margin between hexagons` propriedade para personalizar o aspeto do gráfico de pente de mel. |
| `Margin between hexagons` | Define o espaço entre as células hexagonais. Use com a `Node size` propriedade para personalizar o aspeto do gráfico de pente de mel. |
| `Coloring type` | Seleciona o esquema para usar para colorir o nó. |
| `Node Color Field` | Seleciona uma coluna que fornece a métrica em que as áreas do nó serão baseadas. |

## <a name="node-coloring-types"></a>Tipos de coloração de nó

| Tipo de coloração | Explicação |
|:------------- |:-------------|
| `None` | Todos os nós têm a mesma cor. |
| `Categorical` | Os nós são atribuídos cores com base no valor ou categoria de uma coluna no conjunto de resultados. No exemplo acima, a coloração baseia-se na coluna _Tipo_ do conjunto de resultados. As paletas suportadas são `Default` `Pastel` , e `Cool tone` .  |
| `Heatmap` | Neste tipo, as células são coloridas com base numa coluna métrica e numa paleta de cores. Isto fornece uma forma simples de destacar as métricas espalhadas pelas células. |
| `Thresholds` | Neste tipo, as cores das células são definidas por regras limiares (por exemplo, _CPU > 90% => Vermelho, 60% > CPU > 90% = amarelo>, CPU < 60% => Verde)_ |
| `Field Based` | Neste tipo, uma coluna fornece valores RGB específicos para usar para o nó. Proporciona a maior flexibilidade, mas geralmente requer mais trabalho para permitir.  |
      
## <a name="node-format-settings"></a>Definições de formato de nó

Os autores do pente de mel podem especificar que conteúdo vai para as diferentes partes de um nó: topo, esquerda, centro, direita e fundo. Os autores são livres de usar qualquer um dos suportes de livros de renderização (texto, grande número, linhas de faísca, ícone, etc.).

## <a name="next-steps"></a>Passos seguintes

- Saiba como criar um [renderizador de barras compósitos em livros](workbooks-composite-bar.md)de trabalho.
- Saiba como [importar dados de registo do Azure Monitor para o Power BI](powerbi.md).
