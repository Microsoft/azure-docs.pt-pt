---
title: Estrutura de Registos monitores Azure / Microsoft Docs
description: Necessita de uma consulta de registo para obter dados de registo do Azure Monitor.  Este artigo descreve como novas consultas de log são usadas no Azure Monitor e fornece conceitos que você precisa entender antes de criar um.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/09/2020
ms.openlocfilehash: b4882ec9eb8b81ae27a1e8eed2e5b4349fbeac3f
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/20/2020
ms.locfileid: "86516197"
---
# <a name="structure-of-azure-monitor-logs"></a>Estrutura de Registos monitores Azure
A capacidade de obter rapidamente informações sobre os seus dados usando uma consulta de [registo](log-query-overview.md) é uma característica poderosa do Azure Monitor. Para criar consultas eficientes e úteis, deve entender alguns conceitos básicos, como o local onde os dados que pretende e como está estruturado. Este artigo fornece os conceitos básicos que precisa para começar.

## <a name="overview"></a>Descrição Geral
Os dados em Registos monitores Azure são armazenados num espaço de trabalho do Log Analytics ou numa aplicação Application Insights. Ambos são alimentados pelo [Azure Data Explorer,](/azure/data-explorer/) o que significa que eles alavancam o seu poderoso motor de dados e linguagem de consulta.

> [!IMPORTANT]
> Se estiver a utilizar um [recurso de Informação de Aplicação baseado no espaço de trabalho,](../app/create-workspace-resource.md)a telemetria é armazenada num espaço de trabalho do Log Analytics com todos os outros dados de registo. As tabelas foram renomeadas e reestruturadas, mas têm as mesmas informações que as tabelas da aplicação Application Insights.

Os dados em espaços de trabalho e aplicações são organizados em tabelas, cada uma das quais armazena diferentes tipos de dados e tem o seu próprio conjunto de propriedades únicas. A maioria das fontes de dados escreverá para as suas [próprias tabelas](../platform/data-sources.md) num espaço de trabalho do Log Analytics, enquanto o Application Insights escreverá para um conjunto de tabelas predefinido numa aplicação de Insights de Aplicação. As consultas de registo são muito flexíveis permitindo combinar facilmente dados de várias tabelas e até mesmo usar uma consulta de recursos cruzados para combinar dados de tabelas em vários espaços de trabalho ou para escrever consultas que combinem espaço de trabalho e dados de aplicação.

A imagem a seguir mostra exemplos de fontes de dados que escrevem para diferentes tabelas que são usadas em consultas de amostragem.

![Tabelas](media/logs-structure/queries-tables.png)

## <a name="log-analytics-workspace"></a>Área de trabalho do Log Analytics
Todos os dados recolhidos pelos Registos do Monitor Azure, com exceção dos Insights de Aplicação, são armazenados num [espaço de trabalho do Log Analytics.](../platform/manage-access.md) Pode criar um ou mais espaços de trabalho dependendo dos seus requisitos específicos. [Fontes de dados,](../platform/data-sources.md) tais como registos de atividades e registos de recursos a partir de recursos Azure, agentes em máquinas virtuais e dados de insights e soluções de monitorização escreverão dados para um ou mais espaços de trabalho que configura como parte do seu embarque. Outros serviços, como [o Azure Security Center](../../security-center/index.yml) e o [Azure Sentinel,](../../sentinel/index.yml) também utilizam um espaço de trabalho log Analytics para armazenar os seus dados para que possam ser analisados utilizando consultas de registo, juntamente com dados de monitorização de outras fontes.

Diferentes tipos de dados são armazenados em diferentes tabelas no espaço de trabalho, e cada mesa tem um conjunto único de propriedades. Um conjunto padrão de tabelas são adicionadas a um espaço de trabalho quando é criado, e novas tabelas são adicionadas para diferentes fontes de dados, soluções e serviços à medida que estão a bordo. Também pode criar tabelas personalizadas utilizando a [API do Colecionador de Dados.](../platform/data-collector-api.md)

Pode navegar nas tabelas num espaço de trabalho e no seu esquema no **separador Schema** em Log Analytics para o espaço de trabalho.

![Esquema de espaço de trabalho](media/scope/workspace-schema.png)

Utilize a seguinte consulta para listar as tabelas no espaço de trabalho e o número de registos recolhidos em cada um no dia anterior. 

```Kusto
union withsource = table * 
| where TimeGenerated > ago(1d)
| summarize count() by table
| sort by table asc
```
Consulte a documentação de cada fonte de dados para obter mais informações sobre as tabelas que criam. Exemplos incluem artigos para [fontes de dados de agentes,](../platform/agent-data-sources.md) [registos de recursos](../platform/resource-logs-schema.md)e [soluções de monitorização.](../monitor-reference.md)

### <a name="workspace-permissions"></a>Permissões de espaço de trabalho
Consulte [a implementação de um Azure Monitor Logs](../platform/design-logs-deployment.md) para compreender a estratégia de controlo de acesso e recomendações para fornecer acesso aos dados num espaço de trabalho. Além de conceder acesso ao espaço de trabalho em si, pode limitar o acesso a tabelas individuais usando [o Table Level RBAC.](../platform/manage-access.md#table-level-rbac)

## <a name="application-insights-application"></a>Aplicação de Insights de Aplicação

> [!IMPORTANT]
> Se estiver a utilizar uma telemetria [de recursos de aplicações baseada no espaço de trabalho,](../app/create-workspace-resource.md) é armazenada num espaço de trabalho do Log Analytics com todos os outros dados de registo. As tabelas foram renomeadas e reestruturadas, mas têm a mesma informação que as tabelas de um recurso clássico de Application Insights.

Quando cria uma aplicação no Application Insights, uma aplicação correspondente é criada automaticamente em Registos monitores Azure. Nenhuma configuração é necessária para recolher dados, e a aplicação escreverá automaticamente dados de monitorização, tais como visualizações de páginas, pedidos e exceções.

Ao contrário de um espaço de trabalho Log Analytics, uma aplicação Application Insights tem um conjunto fixo de tabelas. Não é possível configurar outras fontes de dados para escrever para a aplicação para que não possam ser criadas tabelas adicionais. 

| Tabela | Descrição | 
|:---|:---|
| disponibilidadeResults | Dados sumários dos testes de disponibilidade. |
| browsersa      | Dados sobre o desempenho do cliente, como o tempo necessário para processar os dados recebidos. |
| costumesEvents        | Eventos personalizados criados pela sua aplicação. |
| costumesMetricos       | Métricas personalizadas criadas pela sua aplicação. |
| dependências        | Chamadas da aplicação para outros componentes (incluindo componentes externos) gravadas via TrackDependency() – por exemplo, chamadas para API REST, base de dados ou um sistema de ficheiros. |
| exceções          | Exceções lançadas pelo tempo de execução da aplicação, captura as exceções do lado do servidor e do lado do cliente (navegadores).|
| pageVers           | Dados sobre cada visualização do site com informações do navegador. |
| performanceCounters | Medições de desempenho dos recursos computativos que suportam a aplicação, por exemplo, contadores de desempenho do Windows. |
| pedidos            | Pedidos recebidos pelo seu pedido. Por exemplo, é registado um registo de pedidos separado para cada pedido HTTP que a sua aplicação web recebe.  |
| vestígios              | Registos detalhados (vestígios) emitidos através de quadros de código de aplicação/registo registados através do TrackTrace(). |

Pode ver o esquema de cada tabela no **separador Schema** no Log Analytics para a aplicação.

![Esquema de aplicação](media/scope/application-schema.png)

## <a name="standard-properties"></a>Propriedades padrão
Enquanto cada mesa em Azure Monitor Logs tem o seu próprio esquema, existem propriedades padrão partilhadas por todas as tabelas. Consulte [as propriedades Standard em Registos monitores Azure](../platform/log-standard-properties.md) para obter detalhes de cada um.

| Área de trabalho do Log Analytics | Aplicação de Insights de Aplicação | Descrição |
|:---|:---|:---|
| TimeGenerated | carimbo de data/hora  | Data e hora em que o disco foi criado. |
| Tipo          | itemType   | O nome da mesa do registo foi recuperado. |
| _ResourceId   |            | Identificador único para o recurso com o qual o registo está associado. |
| _IsBillable   |            | Especifica se os dados ingeridos são faturados. |
| _BilledSize   |            | Especifica o tamanho dos bytes de dados que serão faturados. |

## <a name="next-steps"></a>Próximos passos
- Saiba como utilizar [o Log Analytics para criar e editar pesquisas de registos.](./log-query-overview.md)
- Confira um [tutorial sobre consultas de escrita](../log-query/get-started-queries.md) usando a nova linguagem de consulta.
