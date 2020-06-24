---
title: Registos de recursos Azure
description: Saiba como transmitir registos de recursos do Azure para um espaço de trabalho log analytics no Azure Monitor.
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: 492aae69895d62c784d15cd77405d0c52ec13e3e
ms.sourcegitcommit: 51977b63624dfd3b4f22fb9fe68761d26eed6824
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/17/2020
ms.locfileid: "84947102"
---
# <a name="azure-resource-logs"></a>Registos de recursos Azure
Os registos de recursos Azure são [registos de plataformas](platform-logs-overview.md) que fornecem informações sobre operações que foram realizadas dentro de um recurso Azure. O conteúdo dos registos de recursos varia consouros e de tipo de recurso. Os registos de recursos não são recolhidos por predefinição. Tem de criar uma definição de diagnóstico para cada recurso Azure para enviar os seus registos de recursos para um espaço de trabalho do Log Analytics para utilizar com [registos do Monitor Azure,](data-platform-logs.md)Azure Event Hubs para encaminhar para fora do Azure, ou para o Azure Storage para arquivar.

Consulte [Criar definições de diagnóstico para enviar registos e métricas da plataforma para diferentes destinos](diagnostic-settings.md) para obter detalhes sobre a criação de uma definição de diagnóstico e [implementar o Monitor Azure em escala utilizando a Política Azure](deploy-scale.md) para obter detalhes sobre a utilização da Política Azure para criar automaticamente uma definição de diagnóstico para cada recurso Azure que criar.

## <a name="send-to-log-analytics-workspace"></a>Enviar para a área de trabalho do Log Analytics
 Envie registos de recursos para um espaço de trabalho do Log Analytics para permitir as funcionalidades dos Registos do [Monitor Azure,](data-platform-logs.md) que inclui os seguintes:

- Correlacionar os dados de registo de recursos com outros dados de monitorização recolhidos pelo Azure Monitor.
- Consolidar entradas de registo de múltiplos recursos Azure, subscrições e inquilinos em um local para análise em conjunto.
- Use consultas de registo para realizar análises complexas e obtenha informações profundas sobre dados de registo.
- Utilize alertas de registo com lógica de alerta complexa.

[Crie uma definição de diagnóstico](diagnostic-settings.md) para enviar registos de recursos para um espaço de trabalho Log Analytics. Estes dados são armazenados em tabelas conforme descrito na [Estrutura de Registos monitores Azure](../log-query/logs-structure.md). As tabelas utilizadas pelos registos de recursos dependem do tipo de recolha que o recurso está a utilizar:

- Diagnósticos Azure - Todos os dados escritos são para a tabela _AzureDiagnostics._
- Específico de recursos - Os dados são escritos para tabela individual para cada categoria do recurso.

### <a name="azure-diagnostics-mode"></a>Modo de diagnóstico Azure 
Neste modo, todos os dados de qualquer definição de diagnóstico serão recolhidos na tabela _AzureDiagnostics._ Este é o método legado usado hoje pela maioria dos serviços da Azure. Uma vez que vários tipos de recursos enviam dados para a mesma tabela, o seu esquema é o superconjunto dos esquemas de todos os diferentes tipos de dados que estão a ser recolhidos.

Considere o seguinte exemplo onde as definições de diagnóstico estão a ser recolhidas no mesmo espaço de trabalho para os seguintes tipos de dados:

- Registos de auditoria do serviço 1 (com um esquema constituído por colunas A, B e C)  
- Registos de erro de serviço 1 (com um esquema constituído por colunas D, E e F)  
- Registos de auditoria do serviço 2 (com um esquema constituído por colunas G, H e I)  

A tabela AzureDiagnostics será a seguinte:  

| ResourceProvider    | Categoria     | A  | B  | C  | D  | E  | F  | G  | H  | I  |
| -- | -- | -- | -- | -- | -- | -- | -- | -- | -- | -- |
| Microsoft.Service1 | Auditorias    | x1 | y1 | z1 |    |    |    |    |    |    |
| Microsoft.Service1 | ErrorLogs    |    |    |    | q1 | w1 | e1 |    |    |    |
| Microsoft.Service2 | Auditorias    |    |    |    |    |    |    | j1 | k1 | L1 |
| Microsoft.Service1 | ErrorLogs    |    |    |    | q2 | w2 | e2 |    |    |    |
| Microsoft.Service2 | Auditorias    |    |    |    |    |    |    | j3 | k3 | I3 |
| Microsoft.Service1 | Auditorias    | x5 | y5 | z5 |    |    |    |    |    |    |
| ... |

### <a name="resource-specific"></a>Específico de recursos
Neste modo, são criadas tabelas individuais no espaço de trabalho selecionado para cada categoria selecionada na definição de diagnóstico. Este método é recomendado, uma vez que torna muito mais fácil trabalhar com os dados em consultas de log, proporciona uma melhor descoberta dos esquemas e da sua estrutura, melhora o desempenho em ambos os tempos de latência e consulta, e a capacidade de conceder direitos de RBAC em uma tabela específica. Todos os serviços Azure irão eventualmente migrar para o modo Específico de Recursos. 

O exemplo acima resultaria na criação de três tabelas:
 
- *Serviço de Mesa1AuditLogs* da seguinte forma:

    | Fornecedor de Recursos | Categoria | A | B | C |
    | -- | -- | -- | -- | -- |
    | Serviço1 | Auditorias | x1 | y1 | z1 |
    | Serviço1 | Auditorias | x5 | y5 | z5 |
    | ... |

- *Serviço de Mesa1ErrorLogs da* seguinte forma:  

    | Fornecedor de Recursos | Categoria | D | E | F |
    | -- | -- | -- | -- | -- | 
    | Serviço1 | ErrorLogs |  q1 | w1 | e1 |
    | Serviço1 | ErrorLogs |  q2 | w2 | e2 |
    | ... |

- *Serviço de Mesa2AuditLogs* da seguinte forma:  

    | Fornecedor de Recursos | Categoria | G | H | I |
    | -- | -- | -- | -- | -- |
    | Serviço2 | Auditorias | j1 | k1 | L1|
    | Serviço2 | Auditorias | j3 | k3 | I3|
    | ... |



### <a name="select-the-collection-mode"></a>Selecione o modo de recolha
A maioria dos recursos Azure escreverá dados para o espaço de trabalho no modo **Azure Diagnostic** ou **Resource-Specific** sem lhe dar uma escolha. Consulte a [documentação de cada serviço](diagnostic-logs-schema.md) para obter mais informações sobre o modo que utiliza. Todos os serviços Azure acabarão por utilizar o modo Específico de Recursos. Como parte desta transição, alguns recursos permitir-lhe-ão selecionar um modo na definição de diagnóstico. Especifique o modo específico de recursos para quaisquer novas definições de diagnóstico, uma vez que isto facilita a gestão dos dados e pode ajudá-lo a evitar migrações complexas numa data posterior.
  
   ![Seletor de modo definições de diagnóstico](media/resource-logs-collect-workspace/diagnostic-settings-mode-selector.png)




> [!NOTE]
> Atualmente, **os diagnósticos Azure** e o modo **específico de Recursos** só podem ser selecionados quando configurar a definição de diagnóstico no portal Azure. Se configurar a definição utilizando CLI, PowerShell ou Rest API, irá por defeito ao **diagnóstico Azure**.

Pode modificar uma definição de diagnóstico existente para o modo específico de recursos. Neste caso, os dados já recolhidos permanecerão na tabela _AzureDiagnostics_ até que seja removido de acordo com a sua definição de retenção para o espaço de trabalho. Novos dados serão recolhidos na tabela dedicada. Utilize o operador [sindical](https://docs.microsoft.com/azure/kusto/query/unionoperator) para consultar dados em ambas as tabelas.

Continue a ver o blog [Azure Updates](https://azure.microsoft.com/updates/) para anúncios sobre os serviços Azure que suportam o modo Específico de Recursos.

### <a name="column-limit-in-azurediagnostics"></a>Limite de coluna em AzureDiagnostics
Existe um limite de propriedade de 500 para qualquer tabela em Registos monitores Azure. Uma vez atingido este limite, quaisquer linhas que contenham dados com qualquer propriedade fora dos primeiros 500 serão largadas no momento da ingestão. A tabela *AzureDiagnostics* é, em particular, suscetível a este limite, uma vez que inclui propriedades para todos os serviços Azure que lhe escrevem.

Se estiver a recolher registos de recursos de vários serviços, _o AzureDiagnostics_ poderá exceder este limite e os dados serão perdidos. Até que todos os serviços Azure suportem o modo específico de recursos, deve configurar recursos para escrever em vários espaços de trabalho para reduzir a possibilidade de atingir o limite de coluna de 500.

### <a name="azure-data-factory"></a>Azure Data Factory
Azure Data Factory, devido a um conjunto detalhado de registos, é um serviço que é conhecido por escrever um grande número de colunas e potencialmente fazer com que _o AzureDiagnostics_ exceda o seu limite. Para quaisquer definições de diagnóstico configuradas antes do modo específico de recursos ser ativado, haverá uma nova coluna criada para cada parâmetro de utilizador exclusivamente nomeado contra qualquer atividade. Mais colunas serão criadas devido à natureza verbosa das entradas e saídas de atividade.
 
Deve migrar os seus registos para utilizar o modo específico de recursos o mais rapidamente possível. Se não conseguir fazê-lo imediatamente, uma alternativa provisória é isolar os registos da Azure Data Factory no seu próprio espaço de trabalho para minimizar a probabilidade destes registos terem impacto em outros tipos de registos que estão a ser recolhidos nos seus espaços de trabalho.


## <a name="send-to-azure-event-hubs"></a>Enviar para Azure Event Hubs
Envie registos de recursos para um centro de eventos para enviá-los para fora do Azure, por exemplo para um SIEM de terceiros ou outras soluções de análise de registo. Os registos de recursos dos centros de eventos são consumidos em formato JSON com um `records` elemento que contém os registos em cada carga útil. O esquema depende do tipo de recurso descrito no [esquema comum e específico de serviço para registos de recursos Azure](resource-logs-schema.md).

Seguem-se os dados de saída da amostra dos Centros de Eventos para um registo de recursos:

```json
{
    "records": [
        {
            "time": "2019-07-15T18:00:22.6235064Z",
            "workflowId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA",
            "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA/RUNS/08587330013509921957/ACTIONS/SEND_EMAIL",
            "category": "WorkflowRuntime",
            "level": "Error",
            "operationName": "Microsoft.Logic/workflows/workflowActionCompleted",
            "properties": {
                "$schema": "2016-04-01-preview",
                "startTime": "2016-07-15T17:58:55.048482Z",
                "endTime": "2016-07-15T18:00:22.4109204Z",
                "status": "Failed",
                "code": "BadGateway",
                "resource": {
                    "subscriptionId": "00000000-0000-0000-0000-000000000000",
                    "resourceGroupName": "JohnKemTest",
                    "workflowId": "243aac67fe904cf195d4a28297803785",
                    "workflowName": "JohnKemTestLA",
                    "runId": "08587330013509921957",
                    "location": "westus",
                    "actionName": "Send_email"
                },
                "correlation": {
                    "actionTrackingId": "29a9862f-969b-4c70-90c4-dfbdc814e413",
                    "clientTrackingId": "08587330013509921958"
                }
            }
        },
        {
            "time": "2019-07-15T18:01:15.7532989Z",
            "workflowId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA",
            "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA/RUNS/08587330012106702630/ACTIONS/SEND_EMAIL",
            "category": "WorkflowRuntime",
            "level": "Information",
            "operationName": "Microsoft.Logic/workflows/workflowActionStarted",
            "properties": {
                "$schema": "2016-04-01-preview",
                "startTime": "2016-07-15T18:01:15.5828115Z",
                "status": "Running",
                "resource": {
                    "subscriptionId": "00000000-0000-0000-0000-000000000000",
                    "resourceGroupName": "JohnKemTest",
                    "workflowId": "243aac67fe904cf195d4a28297803785",
                    "workflowName": "JohnKemTestLA",
                    "runId": "08587330012106702630",
                    "location": "westus",
                    "actionName": "Send_email"
                },
                "correlation": {
                    "actionTrackingId": "042fb72c-7bd4-439e-89eb-3cf4409d429e",
                    "clientTrackingId": "08587330012106702632"
                }
            }
        }
    ]
}
```

## <a name="send-to-azure-storage"></a>Enviar para o Armazenamento do Azure
Envie registos de recursos para o armazenamento de Azure para retê-lo para arquivar. Uma vez criado a definição de diagnóstico, um recipiente de armazenamento é criado na conta de armazenamento assim que um evento ocorre numa das categorias de registo ativado. As bolhas dentro do recipiente utilizam a seguinte convenção de nomeação:

```
insights-logs-{log category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/RESOURCEGROUPS/{resource group name}/PROVIDERS/{resource provider name}/{resource type}/{resource name}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

Por exemplo, a bolha de um grupo de segurança de rede pode ter um nome semelhante ao seguinte:

```
insights-logs-networksecuritygrouprulecounter/resourceId=/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUP/TESTNSG/y=2016/m=08/d=22/h=18/m=00/PT1H.json
```

Cada blob PT1H.json contém um blob JSON de eventos que ocorreram dentro da hora especificada no URL do blob (por exemplo, h=12). Durante a hora presente, os eventos são acrescentados ao ficheiro PT1H.json à medida que ocorrem. O valor dos minutos (m=00) é sempre de 00, uma vez que os eventos de registo de recursos são divididos em bolhas individuais por hora.

Dentro do PT1H.jsno ficheiro, cada evento é armazenado com o seguinte formato. Isto utilizará um esquema comum de alto nível, mas será único para cada serviço Azure, conforme descrito no [esquema de registos de recursos](diagnostic-logs-schema.md).

``` JSON
{"time": "2016-07-01T00:00:37.2040000Z","systemId": "46cdbb41-cb9c-4f3d-a5b4-1d458d827ff1","category": "NetworkSecurityGroupRuleCounter","resourceId": "/SUBSCRIPTIONS/s1id1234-5679-0123-4567-890123456789/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/TESTNSG","operationName": "NetworkSecurityGroupCounters","properties": {"vnetResourceGuid": "{12345678-9012-3456-7890-123456789012}","subnetPrefix": "10.3.0.0/24","macAddress": "000123456789","ruleName": "/subscriptions/ s1id1234-5679-0123-4567-890123456789/resourceGroups/testresourcegroup/providers/Microsoft.Network/networkSecurityGroups/testnsg/securityRules/default-allow-rdp","direction": "In","type": "allow","matchedConnections": 1988}}
```

> [!NOTE]
> Os registos da plataforma são escritos para o armazenamento de bolhas usando [linhas JSON](http://jsonlines.org/), onde cada evento é uma linha e o personagem newline indica um novo evento. Este formato foi implementado em novembro de 2018. Antes desta data, os registos foram escritos para o armazenamento de bolhas como um conjunto de registos json como descrito em [Prepare para a alteração de formato para registos de plataforma Azure Monitor arquivados numa conta de armazenamento](resource-logs-blob-format.md).


## <a name="next-steps"></a>Passos seguintes

* [Leia mais sobre registos de recursos.](platform-logs-overview.md)
* [Crie definições de diagnóstico para enviar registos e métricas de plataforma para diferentes destinos](diagnostic-settings.md).
