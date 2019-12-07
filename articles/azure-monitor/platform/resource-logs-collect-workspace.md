---
title: Coletar logs de recursos do Azure no espaço de trabalho Log Analytics
description: Saiba como transmitir logs de recursos do Azure para um espaço de trabalho Log Analytics no Azure Monitor.
author: bwren
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/20/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: 83b91be52694076373d950e0ad785ef22671ef4f
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/06/2019
ms.locfileid: "74894520"
---
# <a name="collect-azure-resource-logs-in-log-analytics-workspace-in-azure-monitor"></a>Coletar logs de recursos do Azure no espaço de trabalho Log Analytics no Azure Monitor
[Os logs de recursos](resource-logs-overview.md) no Azure fornecem dados avançados e frequentes sobre a operação interna de um recurso do Azure. Este artigo descreve a coleta de logs de recursos em um espaço de trabalho Log Analytics que permite analisá-lo com outros dados de monitoramento coletados em logs de Azure Monitor usando consultas de log poderosas e também para aproveitar outros recursos de Azure Monitor, como alertas e visualizações. 


## <a name="what-you-can-do-with-resource-logs-in-a-workspace"></a>O que você pode fazer com os logs de recursos em um espaço de trabalho
Coletar logs de recursos em um espaço de trabalho Log Analytics permite que você analise os logs de todos os seus recursos do Azure juntos e aproveite todos os recursos disponíveis para [Azure monitor logs](data-platform-logs.md) que incluem o seguinte:

* **Consultas de log** – crie [consultas de log](../log-query/log-query-overview.md) usando uma linguagem de consulta avançada para analisar rapidamente e obter informações sobre seus dados de diagnóstico e analisá-los com os dados coletados de outras fontes no Azure monitor.
* **Alertas** -obtenha notificações proativas de condições críticas e padrões identificados em seus logs de recursos usando [alertas de log no Azure monitor](alerts-log.md).
* **Visualizações** – fixe os resultados de uma consulta de log em um painel do Azure ou inclua-os em uma pasta de trabalho como parte de um relatório interativo.

## <a name="prerequisites"></a>Pré-requisitos
Você precisará [criar um novo espaço de trabalho](../learn/quick-create-workspace.md) se ainda não tiver um. O espaço de trabalho não precisa estar na mesma assinatura que o recurso que envia logs, contanto que o usuário que define a configuração tenha acesso RBAC apropriado a ambas as assinaturas.

## <a name="create-a-diagnostic-setting"></a>Criar uma configuração de diagnóstico
Os logs de recursos não são coletados por padrão. Colete-os em um espaço de trabalho Log Analytics e outros destinos criando uma configuração de diagnóstico para um recurso do Azure. Consulte [criar configuração de diagnóstico para coletar logs e métricas no Azure](diagnostic-settings.md) para obter detalhes.

## <a name="collection-mode"></a>Modo de coleta
Os dados coletados em um espaço de trabalho Log Analytics são armazenados em tabelas, conforme descrito em [estrutura de logs de Azure monitor](../log-query/logs-structure.md). As tabelas usadas pelos logs de recursos dependem do tipo de coleção que o recurso está usando:

- Diagnóstico do Azure-todos os dados gravados estão na tabela _AzureDiagnostics_ .
- Dados específicos do recurso são gravados em uma tabela individual para cada categoria do recurso.

### <a name="azure-diagnostics-mode"></a>Modo de Diagnóstico do Azure 
Nesse modo, todos os dados de qualquer [configuração de diagnóstico](diagnostic-settings.md) serão coletados na tabela _AzureDiagnostics_ . Esse é o método herdado usado hoje pela maioria dos serviços do Azure.

Como vários tipos de recursos enviam dados para a mesma tabela, seu esquema é o superconjunto dos esquemas de todos os tipos de dados diferentes que estão sendo coletados.

Considere o exemplo a seguir em que as configurações de diagnóstico estão sendo coletadas no mesmo espaço de trabalho para os seguintes tipos de dados:

- Logs de auditoria do serviço 1 (tendo um esquema que consiste nas colunas A, B e C)  
- Logs de erros do serviço 1 (tendo um esquema que consiste em colunas D, E e F)  
- Logs de auditoria do serviço 2 (tendo um esquema que consiste em colunas G, H e I)  

A tabela AzureDiagnostics terá a seguinte aparência:  

| ResourceProvider    | Categoria     | A  | B  | C  | D  | E  | F  | G  | H  | I  |
| -- | -- | -- | -- | -- | -- | -- | -- | -- | -- | -- |
| Microsoft. Service1 | AuditLogs    | x1 | Y1 | z1 |    |    |    |    |    |    |
| Microsoft. Service1 | ErrorLogs    |    |    |    | q1 | W1 | E1 |    |    |    |
| Microsoft. Service2 | AuditLogs    |    |    |    |    |    |    | j1 | K1 | l1 |
| Microsoft. Service1 | ErrorLogs    |    |    |    | q2 | W2 | E2 |    |    |    |
| Microsoft. Service2 | AuditLogs    |    |    |    |    |    |    | j3 | k3 | l3 |
| Microsoft. Service1 | AuditLogs    | x5 | y5 | z5 |    |    |    |    |    |    |
| ... |

### <a name="resource-specific"></a>Específico do recurso
Nesse modo, as tabelas individuais no espaço de trabalho selecionado são criadas para cada categoria selecionada na configuração de diagnóstico. Esse método é recomendado, pois torna muito mais fácil trabalhar com os dados em consultas de log, fornece melhor capacidade de descoberta de esquemas e sua estrutura, melhora o desempenho em tempos de consulta e latência de ingestão e a capacidade de conceder direitos de RBAC em um tabela específica. Todos os serviços do Azure eventualmente serão migrados para o modo específico do recurso. 

O exemplo acima resultaria em três tabelas sendo criadas:
 
- Tabela *Service1AuditLogs* da seguinte maneira:

    | Provedor de recursos | Categoria | A | B | C |
    | -- | -- | -- | -- | -- |
    | Service1 | AuditLogs | x1 | Y1 | z1 |
    | Service1 | AuditLogs | x5 | y5 | z5 |
    | ... |

- Tabela *Service1ErrorLogs* da seguinte maneira:  

    | Provedor de recursos | Categoria | D | E | F |
    | -- | -- | -- | -- | -- | 
    | Service1 | ErrorLogs |  q1 | W1 | E1 |
    | Service1 | ErrorLogs |  q2 | W2 | E2 |
    | ... |

- Tabela *Service2AuditLogs* da seguinte maneira:  

    | Provedor de recursos | Categoria | G | H | I |
    | -- | -- | -- | -- | -- |
    | Service2 | AuditLogs | j1 | K1 | l1|
    | Service2 | AuditLogs | j3 | k3 | l3|
    | ... |



### <a name="select-the-collection-mode"></a>Selecione o modo de coleta
A maioria dos recursos do Azure gravará dados no espaço de trabalho no modo de **diagnóstico do Azure** ou **específico do recurso,** sem lhe dar uma opção. Consulte a [documentação de cada serviço](diagnostic-logs-schema.md) para obter detalhes sobre qual modo ele usa. Todos os serviços do Azure eventualmente usarão o modo específico do recurso. Como parte dessa transição, alguns recursos permitirão que você selecione um modo na configuração de diagnóstico. Você deve especificar o modo específico do recurso para as novas configurações de diagnóstico, pois isso torna os dados mais fáceis de gerenciar e pode ajudá-lo a evitar migrações complexas em uma data posterior.
  
   ![Seletor de modo de configurações de diagnóstico](media/resource-logs-collect-workspace/diagnostic-settings-mode-selector.png)




> [!NOTE]
> Atualmente, o **diagnóstico do Azure** e o modo **específico do recurso** só podem ser selecionados ao definir a configuração de diagnóstico no portal do Azure. Se você definir a configuração usando a CLI, o PowerShell ou a API REST, o **diagnóstico do Azure**será padronizado.

Você pode modificar uma configuração de diagnóstico existente para o modo específico do recurso. Nesse caso, os dados que já foram coletados permanecerão na tabela _AzureDiagnostics_ até que sejam removidos de acordo com sua configuração de retenção para o espaço de trabalho. Novos dados serão coletados na tabela dedicada. Use o operador [Union](https://docs.microsoft.com/azure/kusto/query/unionoperator) para consultar dados em ambas as tabelas.

Continue a assistir ao blog de [atualizações do Azure](https://azure.microsoft.com/updates/) para obter anúncios sobre os serviços do Azure que dão suporte ao modo específico do recurso.

### <a name="column-limit-in-azurediagnostics"></a>Limite de coluna em AzureDiagnostics
Há um limite de propriedade de 500 para qualquer tabela nos logs de Azure Monitor. Quando esse limite for atingido, todas as linhas contendo dados com qualquer propriedade fora do primeiro 500 serão removidas no momento da ingestão. A tabela *AzureDiagnostics* está em particular suscetível a esse limite, pois inclui propriedades para todos os serviços do Azure que gravam nele.

Se você estiver coletando logs de recursos de vários serviços, o _AzureDiagnostics_ poderá exceder esse limite e os dados serão perdidos. Até que todos os serviços do Azure ofereçam suporte ao modo específico de recurso, você deve configurar recursos para gravar em vários espaços de trabalho para reduzir a possibilidade de atingir o limite de coluna de 500.

### <a name="azure-data-factory"></a>Azure Data Factory
Azure Data Factory, devido a um conjunto muito detalhado de logs, é um serviço que é conhecido por gravar um grande número de colunas e, potencialmente, fazer com que o _AzureDiagnostics_ exceda seu limite. Para qualquer configuração de diagnóstico configurada antes do modo específico do recurso ser habilitado, haverá uma nova coluna criada para cada parâmetro de usuário nomeado exclusivamente em relação a qualquer atividade. Mais colunas serão criadas por causa da natureza detalhada das entradas e saídas da atividade.
 
Você deve migrar seus logs para usar o modo específico do recurso assim que possível. Se você não puder fazer isso imediatamente, uma alternativa provisória é isolar Azure Data Factory logs em seu próprio espaço de trabalho para minimizar a chance desses logs, afetando outros tipos de log que estão sendo coletados em seus espaços de trabalho.


## <a name="next-steps"></a>Passos seguintes

* Para saber mais sobre os logs de recursos do Azure, consulte [visão geral dos logs de recursos do Azure](resource-logs-overview.md).
* Para criar uma configuração de diagnóstico para coletar logs de recursos para um espaço de trabalho Log Analytics, consulte [criar configuração de diagnóstico para coletar logs e métricas no Azure](diagnostic-settings.md).
