---
title: Visão geral do Log Analytics no Azure Monitor
description: Descreve o Log Analytics, que é uma ferramenta no portal Azure utilizado para editar e executar consultas de registo para analisar dados em Registos do Monitor Azure.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/04/2020
ms.openlocfilehash: d83cc4045f09ed0f704a0d61c6b303406835eb44
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102052301"
---
# <a name="overview-of-log-analytics-in-azure-monitor"></a>Visão geral do Log Analytics no Azure Monitor
Log Analytics é uma ferramenta no portal Azure usado para editar e executar consultas de registo com dados em Registos monitores Azure. Pode escrever uma consulta simples que devolve um conjunto de registos e, em seguida, use funcionalidades de Log Analytics para ordenar, filtrar e analisá-los. Ou pode escrever uma consulta mais avançada para realizar análises estatísticas e visualizar os resultados num gráfico para identificar uma determinada tendência. Quer trabalhe com os resultados das suas consultas interativamente ou utilize-as com outras funcionalidades do Azure Monitor, como alertas de consulta de registo ou livros de trabalho, o Log Analytics é a ferramenta que vai utilizar para os escrever e testar. 


> [!TIP]
> Este artigo fornece uma descrição do Log Analytics e de cada uma das suas características. Se quiser entrar num tutorial, consulte o [tutorial do Log Analytics.](./log-analytics-tutorial.md)



## <a name="starting-log-analytics"></a>Início do Registo Analytics
Inicie o Registo de **Análises** a partir de Registos no menu **Azure Monitor** no portal Azure. Você também verá esta opção no menu para a maioria dos recursos Azure. Independentemente de onde o iniciar, será a mesma ferramenta Log Analytics. O menu que utiliza para iniciar o Log Analytics determina os dados que estarão disponíveis. Se o iniciar a partir do menu **Azure Monitor** ou do menu **de espaços de trabalho Log Analytics,** terá acesso a todos os registos num espaço de trabalho. Se selecionar **Registos** de outro tipo de recurso, os seus dados serão limitados a registar dados para esse recurso. Consulte [o âmbito de consulta de registo e o intervalo de tempo no Azure Monitor Log Analytics](./scope.md) para obter mais detalhes.

[![Iniciar analítica de registo](media/log-analytics-overview/start-log-analytics.png)](media/log-analytics-overview/start-log-analytics.png#lightbox)

Quando iniciar o Log Analytics, a primeira coisa que verá é uma caixa de diálogo com [consultas de exemplo.](../logs/example-queries.md) Estes são categorizados por solução, e você pode navegar ou procurar consultas que correspondam aos seus requisitos particulares. Você pode ser capaz de encontrar um que faça exatamente o que você precisa, ou carregar um para o editor e modificá-lo conforme necessário. Navegar por exemplo consultas é na verdade uma ótima maneira de aprender a escrever suas próprias consultas. Claro que se quiser começar com um script vazio e escrevê-lo por si mesmo, pode fechar as consultas de exemplo. Basta clicar nas **Consultas** no topo do ecrã se quiser recuperá-las.

## <a name="log-analytics-interface"></a>Interface Log Analytics
A imagem a seguir identifica os diferentes componentes do Log Analytics.

[![Log Analytics](media/log-analytics-overview/log-analytics.png)](media/log-analytics-overview/log-analytics.png#lightbox)

### <a name="1-top-action-bar"></a>1. Barra de ação de topo
Controlos para trabalhar com a consulta na janela de consulta.

| Opção | Descrição |
|:---|:---|
| Âmbito | Especifica o âmbito dos dados utilizados para a consulta. Isto pode ser todos os dados de um espaço de trabalho do Log Analytics ou dados para um determinado recurso em vários espaços de trabalho. Consulte [o âmbito de consulta.](./scope.md) |
| Botão de execução | Clique para executar a consulta selecionada na janela de consulta. Também pode premir shift+in para executar uma consulta. |
| Apanhador de tempo | Selecione o intervalo de tempo para os dados disponíveis para a consulta. Isto é ultrapassado se incluir um filtro de tempo na consulta. Consulte [o âmbito de consulta de registo e o intervalo de tempo no Azure Monitor Log Analytics](./scope.md). |
| Botão Guardar | Guarde a consulta para o Explorador de Consultas para o espaço de trabalho. |
 botão Copiar | Copie um link para a consulta, o texto de consulta ou os resultados da consulta para a área de transferência. |
| Novo botão de regra de alerta | Crie um novo separador com uma consulta vazia. |
| Botão de exportação | Exporte os resultados da consulta para um ficheiro CSV ou a consulta para o formato de Formula Formula Language para utilização com Power Bi. |
| Pino para botão de tablier | Adicione os resultados da consulta a um painel Azure. |
| Botão de consulta de formato | Disponha o texto selecionado para a sua legibilidade. |
| Botão de consultas de exemplo | Abra o exemplo da caixa de diálogo de consultas que é apresentada quando abrir o Log Analytics pela primeira vez. |
| Botão de explorador de consulta | Open **Query Explorer** que dá acesso a consultas guardadas no espaço de trabalho. |


### <a name="2-sidebar"></a>2. Barra lateral
Listas de tabelas no espaço de trabalho, consultas de amostra e opções de filtro para a consulta atual.

| Tecla de Tabulação | Description |
|:---|:---|
| Tables | Lista as tabelas que fazem parte do âmbito selecionado. Selecione **Grupo para** alterar o agrupamento das tabelas. Passe sobre um nome de mesa para exibir uma caixa de diálogo com uma descrição da tabela e opções para ver a sua documentação e para visualizar os seus dados. Expanda uma tabela para ver as suas colunas. Clique duas vezes numa tabela ou nome de coluna para adicioná-lo à consulta. |
| Consultas | Lista de consultas de exemplo que pode abrir na janela de consulta. Esta é a mesma lista que é apresentada quando abre o Log Analytics. Selecione **Grupo para** alterar o agrupamento das consultas. Clique duas vezes numa consulta para adicioná-la à janela de consulta ou pairar sobre ela para outras opções. |
| Filtro | Cria opções de filtro com base nos resultados de uma consulta. Depois de uma consulta, as colunas serão exibidas com valores diferentes dos resultados. Selecione um ou mais valores e, em seguida, clique em **Aplicar & Executar** para adicionar um comando **onde** a consulta e executá-lo novamente. |

### <a name="3-query-window"></a>3. Janela de consulta
A janela de consulta é onde edita a sua consulta. Isto inclui intellisense para comandos KQL e codificação de cores para aumentar a legibilidade. Clique **+** na parte superior da janela para abrir outro separador.

Como uma única janela pode incluir múltiplas consultas. Uma consulta não pode incluir linhas em branco, para que possa separar várias consultas numa janela com uma ou mais linhas em branco. A consulta atual é a que tem o cursor posicionado em qualquer lugar.

Para executar a consulta atual, clique no botão **Executar** ou prima Shift+Enter.

### <a name="4-results-window"></a>4. Janela de resultados
Os resultados da consulta são apresentados na janela de resultados. Por predefinição, os resultados são apresentados como uma tabela. Para exibir como um gráfico, selecione **Gráfico** na janela de resultados ou adicione um comando **de renderização** à sua consulta.

#### <a name="results-view"></a>Vista de resultados
A consulta resulta numa tabela organizada por colunas e linhas. Clique à esquerda de uma linha para expandir os seus valores. Clique no dropdown das **Colunas** para alterar a lista de colunas. Ordenar os resultados clicando num nome de coluna. Filtrar os resultados clicando no funil ao lado de um nome de coluna. Limpe os filtros e reinicie a triagem executando novamente a consulta.

Selecione **colunas de grupo** para visualizar a barra de agrupamento acima dos resultados da consulta. Agrupe os resultados por qualquer coluna arrastando-os para a barra. Crie grupos aninhados nos resultados adicionando colunas adicionais. 

#### <a name="chart-view"></a>Vista de gráfico
Apresenta os resultados como um dos vários tipos de gráficos disponíveis. Pode especificar o tipo de gráfico num comando **de renderização** na sua consulta ou selecioná-lo a partir do dropdown **do Tipo de Visualização.**

| Opção | Descrição |
|:---|:---|
| **Tipo de Visualização** | Tipo de gráfico para exibir. |
| **Eixo X** | Coluna nos resultados a utilizar para o Eixo X 
| **Eixo Y** | Coluna nos resultados a utilizar para o Eixo Y. Esta será tipicamente uma coluna numérica. |
| **Dividido por** | Coluna nos resultados que define a série na tabela. Uma série é criada por cada valor na coluna. |
| **Agregação** | Tipo de agregação para executar sobre os valores numéricos no Eixo Y. |

## <a name="relationship-to-azure-data-explorer"></a>Relação com Azure Data Explorer
Se já está familiarizado com a UI web do Azure Data Explorer, então o Log Analytics deve parecer familiar. Isto porque é construído em cima do Azure Data Explorer e usa a mesma linguagem de consulta kusto (KQL). O Log Analytics adiciona funcionalidades específicas do Azure Monitor, tais como a filtragem por intervalo de tempo e a capacidade de criar uma regra de alerta a partir de uma consulta. Ambas as ferramentas incluíam um explorador que permite digitalizar através da estrutura das tabelas disponíveis, mas o Azure Data Explorer Web UI funciona principalmente com tabelas em bases de dados do Azure Data Explorer enquanto o Log Analytics trabalha com tabelas num espaço de trabalho do Log Analytics. 

## <a name="next-steps"></a>Passos seguintes
- Caminhe através de um [tutorial sobre a utilização do Log Analytics no portal Azure](./log-analytics-tutorial.md).
- Caminhe por um [tutorial sobre consultas de escrita.](./get-started-queries.md)