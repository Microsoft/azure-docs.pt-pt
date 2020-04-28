---
title: Recolher & analisar registos de recursos
description: Grave e analise eventos de registo de recursos para o Registo de Contentores Azure, tais como autenticação, impulso de imagem e puxão de imagem.
ms.topic: article
ms.date: 01/03/2020
ms.openlocfilehash: 00f9468721126bd166051df47cec1596356e9b54
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79409648"
---
# <a name="azure-container-registry-logs-for-diagnostic-evaluation-and-auditing"></a>Registo de contentores azure para avaliação e auditoria de diagnóstico

Este artigo explica como recolher dados de registo para um registo de contentores Azure utilizando características do [Monitor Azure](../azure-monitor/overview.md). O Azure Monitor recolhe [registos](../azure-monitor/platform/platform-logs-overview.md) de recursos (anteriormente *chamados registos*de diagnóstico) para eventos orientados pelo utilizador no seu registo. Recolher e consumir estes dados para atender a necessidades como:

* Eventos de autenticação de registo de auditoria para garantir segurança e conformidade 

* Forneça um rasto de atividade completo em artefactos de registo, tais como eventos de pull e pull para que possa diagnosticar problemas operacionais com o seu registo 

A recolha de dados de registo de recursos utilizando o Monitor Azure pode incorrer em custos adicionais. Consulte os preços do [Monitor Azure](https://azure.microsoft.com/pricing/details/monitor/). 

## <a name="repository-events"></a>Eventos de repositório

Os seguintes eventos de nível repositório para imagens e outros artefactos estão atualmente registados:

* **Impulsionar eventos**
* **Fazer eventos**
* **Eventos untag**
* **Eliminar eventos** (incluindo o repositório eliminar eventos)

Eventos de nível repositório que não estão atualmente registados: eventos de purga.

## <a name="registry-resource-logs"></a>Registo de registos de recursos

Os registos de recursos contêm informações emitidas pelos recursos do Azure que descrevem o seu funcionamento interno. Para um registo de contentores Azure, os registos contêm autenticação e eventos de nível repositório armazenados nas tabelas seguintes. 

* **ContainerRegistryLoginEvents** - Eventos de autenticação de registo e estado, incluindo a identidade de entrada e endereço IP
* **ContainerRegistryRepositoryEvents** - Operações como empurrar e puxar para imagens e outros artefactos em repositórios de registo
* **Métricas** - de registo de contentores AzureMetrics, tais como empurrões agregados e contagem de puxar.[Container registry metrics](../azure-monitor/platform/metrics-supported.md#microsoftcontainerregistryregistries)

Para operações, os dados de registo incluem:
  * Estado de sucesso ou falha
  * Selos de início e fim

Além dos registos de recursos, o Azure fornece um registo de [atividade,](../azure-monitor/platform/platform-logs-overview.md)um único registo de subscrição de eventos de gestão azure, como a criação ou eliminação de um registo de contentores.

## <a name="enable-collection-of-resource-logs"></a>Ativar a recolha de registos de recursos

A recolha de registos de recursos para um registo de contentores não está ativada por defeito. Ative explicitamente as definições de diagnóstico para cada registo que pretende monitorizar. Para opções que permitam configurações de diagnóstico, consulte [Criar definição de diagnóstico para recolher registos e métricas](../azure-monitor/platform/diagnostic-settings.md)da plataforma em Azure .

Por exemplo, para visualizar registos e métricas para um registo de contentores em tempo real no Monitor Azure, recolher os registos de recursos num espaço de trabalho do Log Analytics. Para ativar esta definição de diagnóstico utilizando o portal Azure:

1. Se ainda não tem um espaço de trabalho, crie um espaço de trabalho utilizando o [portal Azure.](../azure-monitor/learn/quick-create-workspace.md) Para minimizar a latência na recolha de dados, certifique-se de que o espaço de trabalho está na **mesma região** que o seu registo de contentores.
1. No portal, selecione o registo e **selecione monitorizar > Definições de diagnóstico > Adicione a definição de diagnóstico**.
1. Introduza um nome para a definição e selecione **Enviar para Registar Analytics**.
1. Selecione o espaço de trabalho para os registos de diagnóstico do registo.
1. Selecione os dados de registo que pretende recolher e clique em **Guardar**.

A imagem seguinte mostra a criação de uma definição de diagnóstico para um registo utilizando o portal.

![Ativar as definições de diagnóstico](media/container-registry-diagnostics-audit-logs/diagnostic-settings.png)

> [!TIP]
> Recolha apenas os dados de que necessita, equilibrando os custos e as suas necessidades de monitorização. Por exemplo, se necessitar apenas de auditar eventos de autenticação, selecione apenas o registo De Registo de Registos de **Contentores.** 

## <a name="view-data-in-azure-monitor"></a>Ver dados no Monitor Azure

Depois de ativar a recolha de registos de diagnóstico no Log Analytics, pode levar alguns minutos para que os dados apareçam no Monitor Azure. Para visualizar os dados no portal, selecione o registo e **selecione Registos de monitorização >**. Selecione uma das tabelas que contenha dados para o registo. 

Executar consultas para ver os dados. Várias consultas de amostra são fornecidas, ou executam as suas próprias. Por exemplo, a seguinte consulta recupera as 24 horas mais recentes de dados da tabela **ContainerRegistryRepositoryEvents:**

```Kusto
ContainerRegistryRepositoryEvents
| where TimeGenerated > ago(1d) 
```

A imagem seguinte mostra a saída da amostra:

![Consultar dados de registo](media/container-registry-diagnostics-audit-logs/azure-monitor-query.png)

Para um tutorial sobre a utilização do Log Analytics no portal Azure, consulte [Iniciar-se com o Azure Monitor Log Analytics](../azure-monitor/log-query/get-started-portal.md), ou experimente o [ambiente de](https://portal.loganalytics.io/demo)demonstração de Log Analytics . 

Para mais informações sobre consultas de registo, consulte a [visão geral das consultas de registo no Monitor Azure](../azure-monitor/log-query/log-query-overview.md).

### <a name="additional-query-examples"></a>Exemplos de consulta adicionais

#### <a name="100-most-recent-registry-events"></a>100 eventos de registo mais recentes

```Kusto
ContainerRegistryRepositoryEvents
| union ContainerRegistryLoginEvents
| top 100 by TimeGenerated
| project TimeGenerated, LoginServer , OperationName , Identity , Repository , DurationMs , Region , ResultType
```

## <a name="additional-log-destinations"></a>Destinos de registo adicionais

Além de enviar os registos para o Log Analytics, ou como alternativa, um cenário comum é selecionar uma conta de Armazenamento Azure como um destino de registo. Para arquivar registos no Armazenamento Azure, crie uma conta de armazenamento antes de permitir o arquivamento através das definições de diagnóstico.

Também pode transmitir eventos de registo de diagnóstico para um Hub de [Eventos Azure.](../event-hubs/event-hubs-what-is-event-hubs.md) Os Hubs de Eventos podem ingerir milhões de eventos por segundo, que pode então transformar e armazenar usando qualquer fornecedor de análise em tempo real. 

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre a utilização [do Log Analytics](../azure-monitor/log-query/get-started-portal.md) e a criação de consultas de [registo.](../azure-monitor/log-query/get-started-queries.md)
* Consulte a [visão geral dos registos da plataforma Azure](../azure-monitor/platform/platform-logs-overview.md) para saber sobre os registos da plataforma que estão disponíveis em diferentes camadas do Azure.

