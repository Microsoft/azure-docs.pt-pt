---
title: Descrição geral dos registos de diagnóstico do Azure
description: Saiba mais sobre os registos de diagnóstico do Azure no Azure Monitor e como pode usá-los para compreender os eventos que ocorrem dentro de um recurso do Azure.
author: nkiest
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: nikiest
ms.subservice: logs
ms.openlocfilehash: 8902e29baa5802e3416bcda97ca59a5576d41829
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66244866"
---
# <a name="overview-of-azure-diagnostic-logs"></a>Descrição geral dos registos de diagnóstico do Azure

**Os registos de diagnóstico** fornecer dados avançados e frequentes sobre o funcionamento de um recurso do Azure. O Azure Monitor disponibiliza dois tipos de registos de diagnóstico:

* **Registos de inquilino** -estes registos provenientes de serviços de inquilinos existentes fora de uma subscrição do Azure, como registos do Azure Active Directory.
* **Registos de recursos** -estes registos provenientes de serviços do Azure que implementar recursos dentro de uma subscrição do Azure, tais como grupos de segurança de rede ou contas de armazenamento.

    ![Registos do diagnóstico de recursos vs outros tipos de registos](media/diagnostic-logs-overview/Diagnostics_Logs_vs_other_logs_v5.png)

O conteúdo estes registos varia consoante o tipo de serviço e de recursos do Azure. Por exemplo, contadores de regras do grupo de segurança de rede e de auditorias do Cofre de chaves são dois tipos de registos de diagnóstico.

Estes registos são diferentes do [registo de atividades](activity-logs-overview.md). O registo de atividades fornece informações sobre as operações executadas nos recursos na sua subscrição com o Resource Manager, por exemplo, criar uma máquina virtual ou eliminar uma aplicação lógica. O registo de atividades é um registo ao nível da subscrição. Os registos de diagnóstico ao nível de recursos fornecem informações aprofundadas de operações executadas dentro desse recurso em si, por exemplo, obter um segredo a partir de um cofre de chave.

Estes registos também diferem dos registos de diagnóstico de nível de sistema operacional convidado. Os registos de diagnóstico do SO convidado são esses recolhidos por um agente em execução numa máquina virtual ou outros suportado o tipo de recurso. Os registos de diagnóstico ao nível de recursos exigem sem agente e captura dados de recursos específicos da plataforma do Azure em si, enquanto os registos de diagnóstico de nível de sistema operacional convidado capturar os dados do sistema operativo e aplicações em execução numa máquina virtual.

Nem todos os serviços suportam os registos de diagnóstico descritos aqui. [Este artigo contém uma lista de secção quais os serviços que suportam registos de diagnóstico](./../../azure-monitor/platform/diagnostic-logs-schema.md).

## <a name="what-you-can-do-with-diagnostic-logs"></a>O que pode fazer com os registos de diagnóstico
Aqui estão algumas das coisas que pode fazer com os registos de diagnóstico:

![Colocação lógica dos registos de diagnóstico](./media/diagnostic-logs-overview/Diagnostics_Logs_Actions.png)

* Guarde-as para um [ **conta de armazenamento** ](../../azure-monitor/platform/archive-diagnostic-logs.md) para inspeção de auditoria ou manual. Pode especificar o tempo (em dias) de retenção usando **definições de diagnóstico de recursos**.
* [Stream-lhes **os Hubs de eventos** ](diagnostic-logs-stream-event-hubs.md) para ingestão por um serviço de terceiros ou de uma solução de análise personalizada como o Power BI.
* Analisá-los com [do Azure Monitor](../../azure-monitor/platform/collect-azure-metrics-logs.md), onde os dados são gravados imediatamente para o Azure Monitor sem a necessidade de primeiro escrever os dados para o armazenamento.  

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

Pode utilizar uma conta de armazenamento ou o espaço de nomes de Hubs de eventos que não esteja na mesma subscrição que emite os registos. O utilizador que configura a definição tem de ter o acesso RBAC adequado para ambas as subscrições.

> [!NOTE]
>  Atualmente não é possível arquivar os registos de fluxo de rede para uma conta de armazenamento que está atrás de uma rede virtual protegida.

## <a name="diagnostic-settings"></a>Definições de diagnóstico

Registos de diagnóstico de recursos são configurados com definições de diagnóstico de recursos. Os registos de diagnóstico do inquilino estão configurados ao utilizar uma definição de diagnóstico do inquilino. **Definições de diagnóstico** para um controle de serviço:

* Onde os registos de diagnóstico e métricas são enviadas (conta de armazenamento, os Hubs de eventos, e/ou do Azure Monitor).
* As categorias de registo são enviadas e se também são enviados dados métricos.
* O tempo em que cada categoria de registo deve ser mantida numa conta de armazenamento.
    - A retenção de zero dias significa que os registos são mantidos para sempre. Caso contrário, o valor pode ser qualquer número de dias entre 1 e 365.
    - Se as políticas de retenção são definidas, mas armazenamento de registos numa conta de armazenamento está desativado (por exemplo, se apenas as opções de Hubs de eventos ou o Log Analytics estão selecionadas), as políticas de retenção não têm efeito.
    - Políticas de retenção são aplicado por dia, portanto, no final do dia (UTC), registos a partir do dia em que está, agora, além de retenção de política são eliminadas. Por exemplo, se tivesse uma política de retenção de um dia, no início do dia hoje os registos de ontem de before dia serão eliminados. O processo de eliminação começa a meia-noite UTC, mas tenha em atenção que pode demorar até 24 horas para os registos para ser eliminado da sua conta de armazenamento.

Estas definições são configuradas a partir das definições de diagnóstico no portal, com os comandos do PowerShell do Azure e a CLI, ou utilizando o [API de REST do Azure Monitor](https://docs.microsoft.com/rest/api/monitor/).

> [!NOTE]
> Atualmente, o envio de métricas multidimensionais através das definições de diagnóstico não é suportado. As métricas com dimensões são exportadas como métricas dimensionais simples e agregadas em valores de dimensões.
>
> *Por exemplo*: A métrica "Mensagens recebidas" num Hub de eventos pode ser explorada e representada um por nível de fila. No entanto, se for exportada através das definições de diagnóstico, a métrica será representada como todas as mensagens recebidas em todas as filas do hub do Hub de Eventos.
>
>

## <a name="supported-services-categories-and-schemas-for-diagnostic-logs"></a>Serviços suportados, categorias e esquemas para os registos de diagnóstico

[Veja este artigo](../../azure-monitor/platform/diagnostic-logs-schema.md) para uma lista completa dos serviços suportados e as categorias de registo e os esquemas usados por esses serviços.

## <a name="next-steps"></a>Passos Seguintes

* [Stream registos de diagnóstico de recursos ao **dos Hubs de eventos**](diagnostic-logs-stream-event-hubs.md)
* [Alterar definições de diagnóstico de recursos com a API de REST do Azure Monitor](https://docs.microsoft.com/rest/api/monitor/)
* [Analisar registos do armazenamento do Azure com o Azure Monitor](collect-azure-metrics-logs.md)
