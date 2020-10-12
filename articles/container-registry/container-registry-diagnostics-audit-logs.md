---
title: Colete & analisar registos de recursos
description: Grave e analise os eventos de registo de recursos para o Registo do Contentor Azure, tais como autenticação, impulso de imagem e puxar imagem.
ms.topic: article
ms.date: 06/01/2020
ms.openlocfilehash: 63ccb944b9c3de9941acf55ca5ea85fda70a7008
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87553381"
---
# <a name="azure-container-registry-logs-for-diagnostic-evaluation-and-auditing"></a>Registo de registo de contentores Azure para avaliação e auditoria de diagnóstico

Este artigo explica como recolher dados de registo de um registo de contentores Azure utilizando características do [Azure Monitor](../azure-monitor/overview.md). O Azure Monitor recolhe [registos de recursos](../azure-monitor/platform/platform-logs-overview.md) (anteriormente *chamados registos*de diagnóstico) para eventos orientados pelo utilizador no seu registo. Recolher e consumir estes dados para atender a necessidades como:

* Eventos de autenticação do registo de auditoria para garantir segurança e conformidade 

* Forneça um rasto completo de atividade em artefactos de registo, tais como pull e pull eventos para que possa diagnosticar problemas operacionais com o seu registo 

A recolha de dados de registo de recursos utilizando o Azure Monitor pode incorrer em custos adicionais. Consulte [os preços do Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/). 

## <a name="repository-events"></a>Eventos de repositório

Os seguintes eventos de nível de repositório para imagens e outros artefactos estão atualmente registados:

* **Push**
* **Puxar**
* **Desmarcação**
* **Excluir** (incluindo eventos de eliminação de repositórios)
* **Purgar tag** e **purgar manifesto**

> [!NOTE]
> Os eventos de purga só são registados se uma política de [retenção](container-registry-retention-policy.md) de registo for configurada.

## <a name="registry-resource-logs"></a>Registos de recursos de registo

Os registos de recursos contêm informações emitidas pelos recursos da Azure que descrevem o seu funcionamento interno. Para um registo de contentores Azure, os registos contêm autenticação e eventos de nível de repositório armazenados nas seguintes tabelas. 

* **ContainerRegistryLoginS**  - Eventos e estado de autenticação do registo, incluindo a identidade de entrada e endereço IP
* **ContainerRegistryRepositoryEvents** - Operações como empurrar e puxar para imagens e outros artefactos em repositórios de registo
* **AzureMetrics**  -  [Métricas de registo de](../azure-monitor/platform/metrics-supported.md#microsoftcontainerregistryregistries) contentores, tais como as contagens agregadas de impulsos e puxar.

Para operações, os dados de registo incluem:
  * Estado de sucesso ou insucesso
  * Selos de tempo de início e fim

Além dos registos de recursos, o Azure fornece um registo de [atividade,](../azure-monitor/platform/platform-logs-overview.md)um registo de nível de subscrição único de eventos de gestão Azure, tais como a criação ou eliminação de um registo de contentores.

## <a name="enable-collection-of-resource-logs"></a>Ativar a recolha de registos de recursos

A recolha de registos de recursos para um registo de contentores não é ativada por defeito. Ativar explicitamente as definições de diagnóstico de cada registo que pretende monitorizar. Para obter opções para permitir configurações de diagnóstico, consulte [Criar definição de diagnóstico para recolher registos e métricas da plataforma em Azure](../azure-monitor/platform/diagnostic-settings.md).

Por exemplo, para visualizar registos e métricas de um registo de contentores em quase tempo real no Azure Monitor, colete os registos de recursos num espaço de trabalho do Log Analytics. Para ativar esta definição de diagnóstico utilizando o portal Azure:

1. Se ainda não tiver um espaço de trabalho, crie um espaço de trabalho utilizando o [portal Azure.](../azure-monitor/learn/quick-create-workspace.md) Para minimizar a latência na recolha de dados, certifique-se de que o espaço de trabalho se encontra na **mesma região** que o registo do seu contentor.
1. No portal, selecione o registo e selecione **As definições de Monitorização > Diagnóstico > Adicionar a definição de diagnóstico**.
1. Introduza um nome para a definição e selecione **Enviar para Registar Análise**.
1. Selecione o espaço de trabalho para os registos de diagnóstico.
1. Selecione os dados de registo que pretende recolher e clique em **Guardar**.

A imagem a seguir mostra a criação de uma definição de diagnóstico para um registo utilizando o portal.

![Ativar as definições de diagnóstico](media/container-registry-diagnostics-audit-logs/diagnostic-settings.png)

> [!TIP]
> Recolha apenas os dados de que necessita, equilibrando o custo e as suas necessidades de monitorização. Por exemplo, se necessitar apenas de auditar eventos de autenticação, selecione apenas o registo **ContainerRegistryLoginEvents.** 

## <a name="view-data-in-azure-monitor"></a>Ver dados no Azure Monitor

Depois de ativar a recolha de registos de diagnóstico no Log Analytics, pode levar alguns minutos para que os dados apareçam no Azure Monitor. Para visualizar os dados no portal, selecione o registo e selecione **Monitoring > Logs**. Selecione uma das tabelas que contém dados para o registo. 

Executar consultas para ver os dados. Várias consultas de amostra são fornecidas, ou executar as suas próprias. Por exemplo, a seguinte consulta recupera as 24 horas mais recentes de dados da tabela **ContainerRegistryRepositoryEvents:**

```Kusto
ContainerRegistryRepositoryEvents
| where TimeGenerated > ago(1d) 
```

A imagem a seguir mostra a saída da amostra:

![Consultar dados de registo](media/container-registry-diagnostics-audit-logs/azure-monitor-query.png)

Para obter um tutorial sobre a utilização do Log Analytics no portal Azure, consulte [Começar com o Azure Monitor Log Analytics,](../azure-monitor/log-query/get-started-portal.md)ou experimentar o [ambiente de demonstração do](https://portal.loganalytics.io/demo)Log Analytics . 

Para obter mais informações sobre consultas de registo, consulte [a visão geral das consultas de registo no Azure Monitor](../azure-monitor/log-query/log-query-overview.md).

## <a name="query-examples"></a>Exemplos de consultas

### <a name="error-events-from-the-last-hour"></a>Erros da última hora

```Kusto
union Event, Syslog // Event table stores Windows event records, Syslog stores Linux records
| where TimeGenerated > ago(1h)
| where EventLevelName == "Error" // EventLevelName is used in the Event (Windows) records
    or SeverityLevel== "err" // SeverityLevel is used in Syslog (Linux) records
```

### <a name="100-most-recent-registry-events"></a>100 eventos de registo mais recentes

```Kusto
ContainerRegistryRepositoryEvents
| union ContainerRegistryLoginEvents
| top 100 by TimeGenerated
| project TimeGenerated, LoginServer, OperationName, Identity, Repository, DurationMs, Region , ResultType
```

### <a name="identity-of-user-or-object-that-deleted-repository"></a>Identidade do utilizador ou objeto que apagou o repositório

```Kusto
ContainerRegistryRepositoryEvents
| where OperationName contains "Delete"
| project LoginServer, OperationName, Repository, Identity, CallerIpAddress
```

### <a name="identity-of-user-or-object-that-deleted-tag"></a>Identidade do utilizador ou objeto que eliminada tag

```Kusto
ContainerRegistryRepositoryEvents
| where OperationName contains "Untag"
| project LoginServer, OperationName, Repository, Tag, Identity, CallerIpAddress
```

### <a name="repository-level-operation-failures"></a>Falhas de funcionamento ao nível do repositório

```kusto
ContainerRegistryRepositoryEvents 
| where ResultDescription contains "40"
| project TimeGenerated, OperationName, Repository, Tag, ResultDescription
```

### <a name="registry-authentication-failures"></a>Falhas de autenticação do registo

```kusto
ContainerRegistryLoginEvents 
| where ResultDescription != "200"
| project TimeGenerated, Identity, CallerIpAddress, ResultDescription
```


## <a name="additional-log-destinations"></a>Destinos adicionais de log

Além de enviar os registos para Log Analytics, ou como alternativa, um cenário comum é selecionar uma conta de Armazenamento Azure como destino de registo. Para arquivar registos no Azure Storage, crie uma conta de armazenamento antes de permitir o arquivamento através das definições de diagnóstico.

Também pode transmitir eventos de registo de diagnóstico para um [Azure Event Hub](../event-hubs/event-hubs-about.md). Os Centros de Eventos podem ingerir milhões de eventos por segundo, que podes transformar e armazenar usando qualquer fornecedor de análise em tempo real. 

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre a utilização [do Log Analytics](../azure-monitor/log-query/get-started-portal.md) e a criação de consultas de [registo.](../azure-monitor/log-query/get-started-queries.md)
* Consulte [a visão geral dos registos da plataforma Azure](../azure-monitor/platform/platform-logs-overview.md) para saber mais sobre os registos da plataforma que estão disponíveis em diferentes camadas do Azure.
