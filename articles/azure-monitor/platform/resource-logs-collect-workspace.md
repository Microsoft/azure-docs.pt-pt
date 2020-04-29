---
title: Recolher registos de recursos Azure no espaço de trabalho do Log Analytics
description: Aprenda a transmitir registos de recursos Azure para um espaço de trabalho de Log Analytics no Monitor Azure.
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: 36bd464624118b7671a3879bcc1d34114bba9ce3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79248596"
---
# <a name="collect-azure-platform-logs-in-log-analytics-workspace-in-azure-monitor"></a>Colete registos de plataforma Azure no espaço de trabalho do Log Analytics no Monitor Azure
[Os registos da plataforma](platform-logs-overview.md) no Azure, incluindo registos de registos de atividades do Azure e de recursos, fornecem informações detalhadas de diagnóstico e auditoria para os recursos do Azure e da plataforma Azure de que dependem. Este artigo descreve a recolha de registos de recursos num espaço de trabalho do Log Analytics que permite analisá-lo com outros dados de monitorização recolhidos em Registos de Monitores Do Azure utilizando poderosas consultas de registo e também para alavancar outras funcionalidades do Monitor de Luz, como alertas e visualizações. 


## <a name="what-you-can-do-with-platform-logs-in-a-workspace"></a>O que você pode fazer com logs de plataforma em um espaço de trabalho
Recolher registos de plataformas num espaço de trabalho do Log Analytics permite-lhe analisar os registos de todos os seus recursos Azure em conjunto e aproveitar todas as funcionalidades disponíveis para registos do [Monitor Azure,](data-platform-logs.md) que inclui o seguinte:

* **Consultas** de registo - Criar consultas de [registo](../log-query/log-query-overview.md) utilizando uma linguagem de consulta poderosa para analisar e obter informações sobre os seus dados de diagnóstico e analisá-los com dados recolhidos de outras fontes no Monitor Do Azure.
* **Alerta** - Obtenha uma notificação proactiva das condições e padrões críticos identificados nos seus registos de recursos utilizando alertas de [registo no Monitor Azure](alerts-log.md).
* **Visualizações** - Pin os resultados de uma consulta de log a um painel azure ou incluí-lo em um livro como parte de um relatório interativo.

## <a name="prerequisites"></a>Pré-requisitos
Precisa [criar um novo espaço](../learn/quick-create-workspace.md) de trabalho se ainda não tiver um. O espaço de trabalho não tem de estar na mesma subscrição que os registos de envio de recursos, desde que o utilizador que configura a definição tenha acesso RBAC adequado a ambas as subscrições.

## <a name="create-a-diagnostic-setting"></a>Criar uma definição de diagnóstico
Envie registos de plataformas para um espaço de trabalho de Log Analytics e outros destinos, criando uma definição de diagnóstico para um recurso Azure. Consulte [a definição de diagnóstico Para recolher registos e métricas em Azure](diagnostic-settings.md) para obter mais detalhes.


## <a name="activity-log-collection"></a>Coleção de registo sonantes de atividade
Pode enviar o registo de Atividade sessão a partir de qualquer subscrição única para até cinco espaços de trabalho log Analytics. Os dados de registo de recursos recolhidos num espaço de trabalho do Log Analytics são armazenados na tabela **AzureActivity.** 

## <a name="resource-log-collection-mode"></a>Modo de recolha de registode recursos
Os dados de registo de recursos recolhidos num espaço de trabalho do Log Analytics são armazenados em [tabelas descritas na Estrutura de Registos do Monitor Azure](../log-query/logs-structure.md). As tabelas utilizadas por registos de recursos dependem do tipo de recolha que o recurso está a utilizar:

- Diagnósticos Azure - Todos os dados escritos são para a tabela _AzureDiagnostics._
- Específico de recursos - Os dados são escritos em tabela individual para cada categoria do recurso.

### <a name="azure-diagnostics-mode"></a>Modo De Diagnóstico Azure 
Neste modo, todos os dados de qualquer [definição](diagnostic-settings.md) de diagnóstico serão recolhidos na tabela _AzureDiagnostics._ Este é o método legado usado hoje pela maioria dos serviços Azure.

Uma vez que vários tipos de recursos enviam dados para a mesma tabela, o seu esquema é o superconjunto dos esquemas de todos os diferentes tipos de dados que estão a ser recolhidos.

Considere o seguinte exemplo em que as definições de diagnóstico estão a ser recolhidas no mesmo espaço de trabalho para os seguintes tipos de dados:

- Registos de auditoria do serviço 1 (com um esquema constituído por colunas A, B e C)  
- Registos de erro do serviço 1 (com um esquema constituído por colunas D, E e F)  
- Registos de auditoria do serviço 2 (com um esquema constituído por colunas G, H e I)  

A tabela AzureDiagnostics será a seguinte:  

| ResourceProvider    | Categoria     | A  | B  | C  | D  | E  | F  | G  | H  | I  |
| -- | -- | -- | -- | -- | -- | -- | -- | -- | -- | -- |
| Microsoft.Service1 | Registos de Auditoria    | x1 | y1 | z1 |    |    |    |    |    |    |
| Microsoft.Service1 | ErrorLogs    |    |    |    | q1 | w1 | e1 |    |    |    |
| Microsoft.Service2 | Registos de Auditoria    |    |    |    |    |    |    | j1 | k1 | l1 |
| Microsoft.Service1 | ErrorLogs    |    |    |    | q2 | w2 | e2 |    |    |    |
| Microsoft.Service2 | Registos de Auditoria    |    |    |    |    |    |    | j3 | k3 | l3 |
| Microsoft.Service1 | Registos de Auditoria    | x5 | y5 | z5 |    |    |    |    |    |    |
| ... |

### <a name="resource-specific"></a>Específico de recursos
Neste modo, são criadas tabelas individuais no espaço de trabalho selecionado para cada categoria selecionada na definição de diagnóstico. Este método é recomendado uma vez que facilita muito o trabalho com os dados em consultas de registo, proporciona uma melhor descoberta dos schemas e da sua estrutura, melhora o desempenho em tempos de latência e consulta de ingestão, e a capacidade de conceder direitos RBAC numa tabela específica. Todos os serviços Azure acabarão por migrar para o modo Específico de Recursos. 

O exemplo acima resultaria na criação de três tabelas:
 
- Serviço de *Mesa1AuditLogs* da seguinte forma:

    | Fornecedor de Recursos | Categoria | A | B | C |
    | -- | -- | -- | -- | -- |
    | Serviço1 | Registos de Auditoria | x1 | y1 | z1 |
    | Serviço1 | Registos de Auditoria | x5 | y5 | z5 |
    | ... |

- Serviço de *Mesa1ErrorLogs* da seguinte forma:  

    | Fornecedor de Recursos | Categoria | D | E | F |
    | -- | -- | -- | -- | -- | 
    | Serviço1 | ErrorLogs |  q1 | w1 | e1 |
    | Serviço1 | ErrorLogs |  q2 | w2 | e2 |
    | ... |

- Serviço de *Mesa2Registos de Auditoria* da seguinte forma:  

    | Fornecedor de Recursos | Categoria | G | H | I |
    | -- | -- | -- | -- | -- |
    | Serviço2 | Registos de Auditoria | j1 | k1 | l1|
    | Serviço2 | Registos de Auditoria | j3 | k3 | l3|
    | ... |



### <a name="select-the-collection-mode"></a>Selecione o modo de recolha
A maioria dos recursos do Azure escreverá dados para o espaço de trabalho no modo **Azure Diagnostic** ou **Resource-Specific** sem lhe dar uma escolha. Consulte a [documentação de cada serviço para](diagnostic-logs-schema.md) obter detalhes sobre o modo que utiliza. Todos os serviços Azure acabarão por utilizar o modo Específico de Recursos. Como parte desta transição, alguns recursos permitirão selecionar um modo na definição de diagnóstico. Deve especificar o modo específico de recursos para quaisquer novas definições de diagnóstico, uma vez que isso facilita a gestão dos dados e pode ajudá-lo a evitar migrações complexas numa data posterior.
  
   ![Seletor de modo definições de diagnóstico](media/resource-logs-collect-workspace/diagnostic-settings-mode-selector.png)




> [!NOTE]
> Atualmente, os **diagnósticos Azure** e o modo **específico de recurso** só podem ser selecionados quando configurar a definição de diagnóstico no portal Azure. Se configurar a definição utilizando CLI, PowerShell ou Rest API, esta será predefinida para o **diagnóstico Azure**.

Pode modificar uma definição de diagnóstico existente para o modo específico de recursos. Neste caso, os dados já recolhidos permanecerão na tabela _AzureDiagnostics_ até que sejam removidos de acordo com a sua definição de retenção para o espaço de trabalho. Novos dados serão recolhidos na tabela dedicada. Use o operador [sindical](https://docs.microsoft.com/azure/kusto/query/unionoperator) para consultar dados em ambas as tabelas.

Continue a ver o blog [Azure Updates](https://azure.microsoft.com/updates/) para anúncios sobre serviços Azure que suportam o modo Específico de Recursos.

### <a name="column-limit-in-azurediagnostics"></a>Limite de coluna sintetizar em AzureDiagnostics
Existe um limite de 500 propriedades para qualquer mesa em Registos Do Monitor Azure. Uma vez atingido este limite, quaisquer linhas que contenham dados com qualquer propriedade fora dos primeiros 500 serão retiradas no tempo de ingestão. A tabela *AzureDiagnostics* é particularmente suscetível a este limite, uma vez que inclui propriedades para todos os serviços Azure que lhe escrevem.

Se estiver a recolher registos de recursos de vários serviços, o _AzureDiagnostics_ pode exceder este limite e os dados serão perdidos. Até que todos os serviços azure suportem o modo específico de recursos, deve configurar recursos para escrever em vários espaços de trabalho para reduzir a possibilidade de atingir o limite de 500 colunas.

### <a name="azure-data-factory"></a>Azure Data Factory
A Azure Data Factory, devido a um conjunto muito detalhado de registos, é um serviço que é conhecido por escrever um grande número de colunas e potencialmente fazer com que o _AzureDiagnostics_ exceda o seu limite. Para quaisquer configurações de diagnóstico configuradas antes de o modo específico de recursos ter sido ativado, haverá uma nova coluna criada para cada parâmetro de utilizador de nome único contra qualquer atividade. Mais colunas serão criadas devido à natureza verbosa das inputs e saídas de atividade.
 
Deve migrar os seus registos para utilizar o modo específico de recursos o mais rapidamente possível. Se não conseguir fazê-lo imediatamente, uma alternativa provisória é isolar os registos da Azure Data Factory no seu próprio espaço de trabalho para minimizar a probabilidade destes registos terem impacto noutros tipos de registos recolhidos nos seus espaços de trabalho.


## <a name="next-steps"></a>Passos seguintes

* [Leia mais sobre registos](platform-logs-overview.md)de recursos .
* [Crie uma definição de diagnóstico para recolher registos e métricas em Azure](diagnostic-settings.md).
