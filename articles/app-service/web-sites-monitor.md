---
title: Monitorizar aplicações
description: Saiba como monitorizar aplicações no Azure App Service utilizando o portal Azure. Compreenda as quotas e métricas que são reportadas.
author: btardif
ms.assetid: d273da4e-07de-48e0-b99d-4020d84a425e
ms.topic: article
ms.date: 01/11/2019
ms.author: byvinyal
ms.custom: seodec18
ms.openlocfilehash: d84340730391abd7dba4d13202503d37941c09b5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79500431"
---
# <a name="monitor-apps-in-azure-app-service"></a>Monitorize aplicações no Serviço de Aplicações Azure
O [Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714) fornece funcionalidades de monitorização incorporadas para aplicações web, aplicações móveis e API no [portal Azure.](https://portal.azure.com)

No portal Azure, pode rever *quotas* e *métricas* para um plano de app e app service, e configurar *alertas* e métricas baseadas em regras de *autoscalcificação.*

## <a name="understand-quotas"></a>Compreender quotas

As aplicações que estão hospedadas no App Service estão sujeitas a certos limites nos recursos que podem utilizar. Os limites são definidos pelo plano de Serviço de Aplicações que está associado à aplicação.

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

Se a aplicação estiver hospedada num plano *Gratuito* ou *Partilhado,* os limites dos recursos que a app pode utilizar são definidos por quotas.

Se a aplicação estiver alojada num plano *Basic*, *Standard*, ou *Premium,* os limites dos recursos que podem utilizar são definidos pelo *tamanho* (Pequeno, Médio, Grande) e contagem de *instâncias* (1, 2, 3, e assim por diante) do plano de Serviço de Aplicações.

Quotas para aplicações gratuitas ou partilhadas são:

| Quota | Descrição |
| --- | --- |
| **CPU (Curto)** | A quantidade de CPU permitida para esta aplicação num intervalo de 5 minutos. Esta quota repõe a cada cinco minutos. |
| **CPU (Dia)** | A quantidade total de CPU permitida para esta aplicação em um dia. Esta quota repõe a cada 24 horas à meia-noite UTC. |
| **Memória** | A quantidade total de memória permitida para esta aplicação. |
| **Largura de banda** | A quantidade total de largura de banda de saída permitida para esta aplicação num dia. Esta quota repõe a cada 24 horas à meia-noite UTC. |
| **Filesystem** | A quantidade total de armazenamento permitida. |

A única quota aplicável às aplicações hospedadas em *Basic*, *Standard*e *Premium* é o Sistema de Ficheiros.

Para obter mais informações sobre as quotas, limites e funcionalidades específicas disponíveis para as várias SKUs do Serviço de Aplicações, consulte os limites do [serviço de subscrição azure.](../azure-resource-manager/management/azure-subscription-service-limits.md#app-service-limits)

### <a name="quota-enforcement"></a>Aplicação de quotas

Se uma aplicação exceder o *CPU (curto)* *CPU (Dia)*, ou quota de largura de *banda,* a aplicação é interrompida até que a quota reset. Durante este período, todos os pedidos de entrada resultam num erro HTTP 403.

![403 mensagem de erro][http403]

Se a quota de memória da aplicação for ultrapassada, a aplicação é reiniciada.

Se a quota do Sistema de Ficheiros for excedida, qualquer operação de escrita falha. As falhas de operação de escrita incluem quaisquer escritos para registos.

Pode aumentar ou remover quotas da sua app atualizando o seu plano de Serviço de Aplicações.

## <a name="understand-metrics"></a>Compreender as métricas

> [!NOTE]
> O Uso do Sistema de **Ficheiros** é uma nova métrica a ser lançada globalmente, não são esperados dados a não ser que tenha sido listado em branco para pré-visualização privada.
> 

> [!IMPORTANT]
> **O tempo médio** de resposta será premeditado para evitar confusões com agregações métricas. Utilize o Tempo de **Resposta** como substituto.

As métricas fornecem informações sobre o comportamento da app ou do plano de serviço de aplicações.

Para uma aplicação, as métricas disponíveis são:

| Métrica | Descrição |
| --- | --- |
| **Tempo de Resposta** | O tempo que a app demorou a servir pedidos, em segundos. |
| **Tempo médio de resposta (depreciado)** | O tempo médio de tempo para a app servir pedidos, em segundos. |
| **Conjunto de trabalho médio da memória** | A quantidade média de memória usada pela app, em megabytes (MiB). |
| **Ligações** | O número de tomadas ligadas existentes na caixa de areia (w3wp.exe e os seus processos infantis).  Uma tomada de atrelado é criada através da ligação ()/ligação() APIs e permanece até que a tomada seja fechada com CloseHandle()/closesocket(). |
| **Tempo cpu** | A quantidade de CPU consumida pela app, em segundos. Para obter mais informações sobre esta métrica, consulte o [tempo de CPU vs cpu percentual](#cpu-time-vs-cpu-percentage). |
| **Assembleias atuais** | O número atual de Assembléias carregados em todos os AppDomains nesta aplicação. |
| **Dados em** | A quantidade de largura de banda consumida pela app, no MiB. |
| **Dados out** | A quantidade de largura de banda de saída consumida pela app, no MiB. |
| **Utilização do sistema de ficheiros** | Percentagem de quota de sistema de ficheiros consumida pela app. |
| **Coleções de lixo gen 0** | O número de vezes que os objetos da geração 0 são recolhidos lixo desde o início do processo da aplicação. GCs de geração superior incluem todos os GCs de geração inferior.|
| **Coleções de lixo da Gen 1** | O número de vezes que os objetos da geração 1 são recolhidos lixo desde o início do processo da aplicação. GCs de geração superior incluem todos os GCs de geração inferior.|
| **Coleções de lixo gen 2** | O número de vezes que os objetos da geração 2 são recolhidos lixo desde o início do processo da aplicação.|
| **N.º de Identificadores** | O número total de pegas atualmente abertas pelo processo da aplicação.|
| **Http 2xx** | A contagem de pedidos que resulta num código de estado HTTP ≥ 200, mas < 300. |
| **Http 3xx** | A contagem de pedidos que resulta num código de estado HTTP ≥ 300, mas < 400. |
| **Http 401** | A contagem de pedidos que resultam no código de estado HTTP 401. |
| **Http 403** | A contagem de pedidos que resultam no código de estado HTTP 403. |
| **Http 404** | A contagem de pedidos que resultam no código de estado HTTP 404. |
| **Http 406** | A contagem de pedidos que resultam no código de estado HTTP 406. |
| **Http 4xx** | A contagem de pedidos que resulta num código de estado HTTP ≥ 400, mas < 500. |
| **Erros do servidor http** | A contagem de pedidos que resulta num código de estado HTTP ≥ 500, mas < 600. |
| **IO Outros Bytes por Segundo** | A taxa a que o processo da aplicação está a emitir bytes para operações de I/O que não envolvem dados, como operações de controlo.|
| **IO outras operações por segundo** | A taxa a que o processo da aplicação está a emitir operações de I/S que não são operações de leitura ou escrita.|
| **IO Ler Bytes por Segundo** | A taxa a que o processo da aplicação está a ler bytes de operações de I/O.|
| **IO Ler Operações por segundo** | A taxa a que o processo da aplicação está a emitir operações de I/O.|
| **IO Escrever Bytes por Segundo** | A taxa a que o processo da aplicação está a escrever bytes para operações de I/O.|
| **IO Write Operations por segundo** | A taxa a que o processo da aplicação está a emitir operações de E/S.|
| **Conjunto de trabalho de memória** | A quantidade atual de memória usada pela app, no MiB. |
| **Bytes Privados** | Private Bytes é o tamanho atual, em bytes, de memória que o processo de aplicação atribuiu que não pode ser partilhado com outros processos.|
| **Pedidos** | O número total de pedidos, independentemente do seu código de estado HTTP resultante. |
| **Pedidos na fila de candidaturas** | O número de pedidos na fila do pedido de pedido de pedido.|
| **Contagem de fios** | O número de fios atualmente ativos no processo da aplicação.|
| **Domínios totais de aplicativos** | O número atual de AppDomains carregados nesta aplicação.|
| **Total de domínios de aplicativos descarregados** | O número total de AppDomains descarregados desde o início da aplicação.|


Para um plano de Serviço de Aplicações, as métricas disponíveis são:

> [!NOTE]
> As métricas do plano de serviço de aplicações estão disponíveis apenas para planos nos níveis *Basic*, *Standard*e *Premium.*
> 

| Métrica | Descrição |
| --- | --- |
| **Percentagem de CPU** | A CPU média usada em todos os casos do plano. |
| **Percentagem de Memória** | A memória média usada em todos os casos do plano. |
| **Dados em** | A largura de banda média de entrada usada em todos os casos do plano. |
| **Dados out** | A largura de banda média de saída usada em todos os casos do plano. |
| **Comprimento da fila do disco** | O número médio de pedidos de leitura e escrita que foram em fila no armazenamento. Um comprimento de fila de disco elevado é uma indicação de uma aplicação que pode estar a abrandar devido ao excesso de I/O do disco. |
| **Comprimento da fila http** | O número médio de pedidos de HTTP que tiveram de se sentar na fila antes de serem cumpridos. Um comprimento de fila http elevado ou crescente é um sintoma de um plano sob carga pesada. |

### <a name="cpu-time-vs-cpu-percentage"></a>CpU tempo vs CPU percentagem
<!-- To do: Fix Anchor (#CPU-time-vs.-CPU-percentage) -->

Existem duas métricas que refletem o uso do CPU:

**Tempo cpu**: Útil para apps hospedadas em planos gratuitos ou partilhados, porque uma das suas quotas é definida em minutos de CPU usados pela app.

**Percentagem de CPU**: Útil para apps hospedadas em planos Básicos, Standard e Premium, porque podem ser dimensionados. A percentagem de CPU é uma boa indicação da utilização global em todos os casos.

## <a name="metrics-granularity-and-retention-policy"></a>Política de granularidade e retenção de métricas
As métricas para um plano de serviço de aplicações e aplicações são registadas e agregadas pelo serviço, com as seguintes granularidades e políticas de retenção:

* As métricas de granularidade **minúsculas** são mantidas durante 30 horas.
* **As** métricas de granularidade da hora são mantidas durante 30 dias.
* **As** métricas de granularidade do dia são mantidas por 30 dias.

## <a name="monitoring-quotas-and-metrics-in-the-azure-portal"></a>Quotas e métricas de monitorização no portal Azure
Para rever o estado das várias quotas e métricas que afetam uma app, vá ao [portal Azure.](https://portal.azure.com)

![Gráfico de quotas no portal Azure][quotas]

Para encontrar quotas, selecione**Quotas**de **Definições** > . Na tabela, pode rever: 
1. O nome da quota.
1. O intervalo de reset.
1. O seu limite atual.
1. O seu valor atual.

![Gráfico métrico no portal][metrics] Azure Pode aceder às métricas diretamente a partir da página **de visão geral** do recurso. Aqui você verá gráficos que representam algumas das métricas das aplicações.

Clicar em qualquer um desses gráficos irá levá-lo à vista das métricas onde você pode criar gráficos personalizados, consultando métricas diferentes e muito mais. 

Para saber mais sobre métricas, consulte [as métricas](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md)de serviço monitor .

## <a name="alerts-and-autoscale"></a>Alertas e escala automática
As métricas de uma aplicação ou de um plano de Serviço de Aplicações podem ser ligadas a alertas. Para obter mais informações, consulte [Receber notificações de alerta](../monitoring-and-diagnostics/insights-alerts-portal.md).

Aplicativos de Serviço de Aplicações hospedados em planos básicos ou superiores do App Service suportam a escala automática. Com escala automática, pode configurar regras que monitorizam as métricas do plano do App Service. As regras podem aumentar ou diminuir a contagem de casos, que pode fornecer recursos adicionais conforme necessário. As regras também podem ajudá-lo a economizar dinheiro quando a aplicação está sobre-provisionada.

Para mais informações sobre escala automática, consulte [Como escalar](../monitoring-and-diagnostics/insights-how-to-scale.md) e [as melhores práticas para autoscalcificação do Monitor Azure](../azure-monitor/platform/autoscale-best-practices.md).

[fzilla]:https://go.microsoft.com/fwlink/?LinkId=247914
[vmsizes]:https://go.microsoft.com/fwlink/?LinkID=309169

<!-- Images. -->
[http403]: ./media/web-sites-monitor/http403.png
[quotas]: ./media/web-sites-monitor/quotas.png
[metrics]: ./media/web-sites-monitor/metrics.png