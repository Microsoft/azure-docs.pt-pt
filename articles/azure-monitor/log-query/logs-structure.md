---
title: Estrutura dos Registos do Monitor Azure  Microsoft Docs
description: É necessário uma consulta de registo para recuperar dados de registo do Monitor Azure.  Este artigo descreve como novas consultas de registo são usadas no Monitor Azure e fornece conceitos que precisa entender antes de criar um.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/22/2019
ms.openlocfilehash: 1d647ba7e8d4f0e29252dfff95099e39bab87895
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2020
ms.locfileid: "77662081"
---
# <a name="structure-of-azure-monitor-logs"></a>Estrutura dos registos do Monitor Azure
A capacidade de obter rapidamente informações sobre os seus dados usando uma consulta de [log](log-query-overview.md) é uma característica poderosa do Monitor Azure. Para criar consultas eficientes e úteis, deve compreender alguns conceitos básicos, como onde os dados que deseja estão localizados e como são estruturados. Este artigo fornece os conceitos básicos que precisa para começar.

## <a name="overview"></a>Descrição geral
Os dados em Registos do Monitor Do Azure são armazenados num espaço de trabalho de Log Analytics ou numa aplicação De insights de aplicação. Ambos são alimentados pelo [Azure Data Explorer,](/azure/data-explorer/) o que significa que alavancam o seu potente motor de dados e linguagem de consulta.

Os dados em ambos os espaços de trabalho e aplicações são organizados em tabelas, cada uma das quais armazena diferentes tipos de dados e tem o seu próprio conjunto único de propriedades. A maioria das [fontes](../platform/data-sources.md) de dados escreverá para as suas próprias tabelas num espaço de trabalho de Log Analytics, enquanto os Insights de Aplicação escreverão para um conjunto predefinido de tabelas numa aplicação Application Insights. As consultas de registo são muito flexíveis, permitindo combinar facilmente dados de várias tabelas e até mesmo usar uma consulta de recursos cruzados para combinar dados de tabelas em vários espaços de trabalho ou para escrever consultas que combinam espaço de trabalho e dados de aplicações.

A imagem que se segue mostra exemplos de fontes de dados que escrevem para diferentes tabelas que são usadas em consultas de amostra.

![Tabelas](media/logs-structure/queries-tables.png)

## <a name="log-analytics-workspace"></a>Área de trabalho do Log Analytics
Todos os dados recolhidos pelo Azure Monitor Logs, com exceção dos Insights de Aplicação, são armazenados num espaço de [trabalho de Log Analytics](../platform/manage-access.md). Pode criar um ou mais espaços de trabalho dependendo dos seus requisitos específicos. [Fontes](../platform/data-sources.md) de dados como registos de atividade e registos de recursos a partir de recursos Do Azure, agentes em máquinas virtuais, e dados de insights e soluções de monitorização escreverão dados para um ou mais espaços de trabalho que configuracomo parte do seu embarque. Outros serviços, como [o Azure Security Center](/azure/security-center/) e o [Azure Sentinel,](/azure/sentinel/) também utilizam um espaço de trabalho de Log Analytics para armazenar os seus dados para que possam ser analisados através de consultas de registo, juntamente com a monitorização de dados de outras fontes.

Diferentes tipos de dados são armazenados em diferentes tabelas no espaço de trabalho, e cada tabela tem um conjunto único de propriedades. Um conjunto padrão de tabelas são adicionados a um espaço de trabalho quando é criado, e novas tabelas são adicionadas para diferentes fontes de dados, soluções e serviços à medida que estão a bordo. Também pode criar tabelas personalizadas utilizando a [API](../platform/data-collector-api.md)de Recolha de Dados .

Pode navegar nas tabelas num espaço de trabalho e o seu esquema no separador **Schema** no Log Analytics para o espaço de trabalho.

![Esquema do espaço de trabalho](media/scope/workspace-schema.png)

Utilize a seguinte consulta para listar as tabelas no espaço de trabalho e o número de registos recolhidos em cada um durante o dia anterior. 

```Kusto
union withsource = table * 
| where TimeGenerated > ago(1d)
| summarize count() by table
| sort by table asc
```
Consulte a documentação de cada fonte de dados para obter detalhes das tabelas que criam. Exemplos incluem artigos para fontes de [dados de agentes,](../platform/agent-data-sources.md) [registos](../platform/diagnostic-logs-schema.md)de recursos e soluções de [monitorização.](../insights/solutions-inventory.md)

### <a name="workspace-permissions"></a>Permissões no espaço de trabalho
Consulte a conceção de uma implementação de [Registos do Monitor Azure](../platform/design-logs-deployment.md) para compreender a estratégia de controlo de acesso e recomendações para fornecer acesso aos dados num espaço de trabalho. Além de conceder acesso ao próprio espaço de trabalho, pode limitar o acesso a tabelas individuais utilizando o [RBAC de nível de mesa](../platform/manage-access.md#table-level-rbac).

## <a name="application-insights-application"></a>Aplicação de Insights de Aplicação
Quando cria uma aplicação no Application Insights, uma aplicação correspondente é automaticamente criada em Registos do Monitor Azure. Não é necessária qualquer configuração para recolher dados, e a aplicação irá automaticamente escrever dados de monitorização, tais como visualizações de páginas, pedidos e exceções.

Ao contrário de um espaço de trabalho log analytics, uma aplicação Application Insights tem um conjunto fixo de tabelas. Não é possível configurar outras fontes de dados para escrever na aplicação para que não possam ser criadas tabelas adicionais. 

| Tabela | Descrição | 
|:---|:---|
| availabilityResults | Resumo dos dados dos testes de disponibilidade. |
| browserTimings      | Dados sobre o desempenho do cliente, como o tempo que se demorou a processar os dados que chegam. |
| eventos personalizados        | Eventos personalizados criados pela sua aplicação. |
| métricas personalizadas       | Métricas personalizadas criadas pela sua aplicação. |
| dependências        | Chamadas da aplicação para componentes externos. |
| exceções          | Exceções lançadas pelo tempo de execução da aplicação. |
| pageViews           | Dados sobre cada visualização do site com informações do navegador. |
| performanceCounters | Medições de desempenho dos recursos computacionais que suportam a aplicação. |
| pedidos            | Detalhes de cada pedido de candidatura.  |
| Vestígios              | Resultados do rastreio distribuído. |

Pode ver o esquema para cada tabela no separador **Schema** no Log Analytics para a aplicação.

![Esquema de aplicação](media/scope/application-schema.png)

## <a name="standard-properties"></a>Propriedades padrão
Enquanto cada mesa em Registos de Monitores Azure tem o seu próprio esquema, existem propriedades padrão partilhadas por todas as tabelas. Consulte [as propriedades Standard em Registos do Monitor Azure](../platform/log-standard-properties.md) para obter detalhes de cada um.

| Área de trabalho do Log Analytics | Aplicação de Insights de Aplicação | Descrição |
|:---|:---|:---|
| TimeGenerated | carimbo de data/hora  | Data e hora em que o disco foi criado. |
| Tipo          | ItemType   | Nome da mesa de onde o disco foi recuperado. |
| _ResourceId   |            | Identificador único para o recurso com o que o registo está associado. |
| _IsBillable   |            | Especifica se os dados ingeridos são faturados. |
| _BilledSize   |            | Especifica o tamanho em bytes de dados que serão cobrados. |

## <a name="next-steps"></a>Passos seguintes
- Saiba usar o Log Analytics para criar e editar pesquisas de [registo.](../log-query/portals.md)
- Confira um [tutorial sobre escrever consultas](../log-query/get-started-queries.md) usando a nova linguagem de consulta.
