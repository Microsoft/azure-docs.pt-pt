---
title: Monitorizar aplicações
description: Saiba como monitorizar as aplicações no Azure App Service utilizando o portal Azure. Compreenda as quotas e métricas que são reportadas.
author: btardif
ms.assetid: d273da4e-07de-48e0-b99d-4020d84a425e
ms.topic: article
ms.date: 04/23/2020
ms.author: byvinyal
ms.custom: seodec18
ms.openlocfilehash: bf230032afe80680dc392c2a74da2a5aef381983
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "100586101"
---
# <a name="monitor-apps-in-azure-app-service"></a>Monitorize aplicativos no Azure App Service
[O Azure App Service](./overview.md) fornece funcionalidades de monitorização incorporadas para aplicações web, móveis e API no [portal Azure.](https://portal.azure.com)

No portal Azure, pode rever *quotas* e *métricas* para um plano de app e App Service, e configurar *alertas* e *autoscaling regras* baseadas em métricas.

## <a name="understand-quotas"></a>Compreender quotas

As aplicações que estão hospedadas no Serviço de Aplicações estão sujeitas a certos limites dos recursos que podem utilizar. Os limites são definidos pelo plano de Serviço de Aplicações que está associado à app.

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

Se a aplicação estiver hospedada num plano *Gratuito* ou *Partilhado,* os limites dos recursos que a app pode usar são definidos por quotas.

Se a aplicação estiver hospedada num plano *Básico,* *Standard* ou *Premium,* os limites dos recursos que podem utilizar são definidos pelo *tamanho* (Pequeno, Médio, Grande) e contagem de *instâncias* (1, 2, 3, e assim por diante) do plano de Serviço de Aplicações.

As quotas para aplicações gratuitas ou partilhadas são:

| Quota | Description |
| --- | --- |
| **CPU (Curto)** | A quantidade de CPU permitida para esta aplicação num intervalo de 5 minutos. Esta quota repõe a cada cinco minutos. |
| **CPU (Dia)** | A quantidade total de CPU permitida para esta aplicação em um dia. Esta quota repõe-se a cada 24 horas à meia-noite utc. |
| **Memória** | A quantidade total de memória permitida para esta aplicação. |
| **Largura de banda** | A quantidade total de largura de banda de saída permitida para esta aplicação em um dia. Esta quota repõe-se a cada 24 horas à meia-noite utc. |
| **Filesystem** | A quantidade total de armazenamento permitida. |

A única quota aplicável às aplicações que estão hospedadas em *Basic,* *Standard* e *Premium* é o Filesystem.

Para obter mais informações sobre as quotas, limites e funcionalidades específicas disponíveis para os vários SKUs do Serviço de Aplicações, consulte [os limites do serviço de subscrição Azure](../azure-resource-manager/management/azure-subscription-service-limits.md#app-service-limits).

### <a name="quota-enforcement"></a>Execução de quotas

Se uma aplicação exceder o *CPU (curto)*, *CPU (Dia)* ou quota de largura de *banda,* a aplicação é interrompida até que a quota seja reposta. Durante este tempo, todos os pedidos de entrada resultam num erro HTTP 403.

![403 mensagem de erro][http403]

Se a quota de memória da aplicação for excedida, a aplicação é interrompida temporariamente.

Se a quota do Sistema de Ficheiros for excedida, qualquer operação de escrita falha. As falhas de operação de escrita incluem quaisquer escritos em registos.

Pode aumentar ou remover quotas da sua aplicação através da atualização do seu plano de Serviço de Aplicações.

## <a name="understand-metrics"></a>Compreender métricas

> [!NOTE]
> **O Uso do Sistema de Ficheiros** é uma nova métrica que está a ser lançada globalmente, não são esperados dados a menos que a sua aplicação esteja hospedada num Ambiente de Serviço de Aplicações.
> 

> [!IMPORTANT]
> **O tempo médio de resposta** será depreciado para evitar confusões com agregações métricas. Utilize o **Tempo de Resposta** como substituto.

> [!NOTE]
> As métricas para uma aplicação incluem os pedidos para o site SCM da aplicação (Kudu).  Isto inclui pedidos para visualizar o logstream do site usando Kudu.  Os pedidos de logstream podem abranger vários minutos, o que afetará as métricas do Tempo de Pedido.  Os utilizadores devem estar cientes desta relação ao utilizar estas métricas com lógica de autoescala.
> 

As métricas fornecem informações sobre a app ou o comportamento do plano de Serviço de Aplicações.

Para uma aplicação, as métricas disponíveis são:

| Metric | Descrição |
| --- | --- |
| **Tempo de resposta** | O tempo que a app demorou a servir pedidos, em segundos. |
| **Tempo médio de resposta (precotado)** | O tempo médio de tempo necessário para a app servir pedidos, em segundos. |
| **Conjunto de trabalho de memória média** | A quantidade média de memória utilizada pela app, em megabytes (MiB). |
| **Ligações** | O número de tomadas encadernadas existentes na caixa de areia (w3wp.exe e os seus processos infantis).  Uma tomada de encaixe é criada chamando APIs()/connect() APIs e permanece até que a referida tomada seja fechada com CloseHandle()/closesocket(). |
| **Hora do CPU** | A quantidade de CPU consumida pela app, em segundos. Para obter mais informações sobre esta métrica, consulte [a percentagem de tempo cpu vs CPU](#cpu-time-vs-cpu-percentage). |
| **Atuais Assembleias** | O número atual de Assembléias carregadas em todos os AppDomains nesta aplicação. |
| **Dados em** | A quantidade de largura de banda recebida consumida pela app, no MiB. |
| **Data out** | A quantidade de largura de banda de saída consumida pela app, no MiB. |
| **Utilização do sistema de ficheiros** | A quantidade de utilização em bytes por partilha de armazenamento. |
| **Coleções de lixo gen 0** | O número de vezes que a geração 0 objetos são lixo recolhido desde o início do processo da aplicação. Os GCs de maior geração incluem todos os GCs de menor geração.|
| **Coleções de lixo da Gen 1** | O número de vezes que os objetos de geração 1 são lixo recolhidos desde o início do processo de aplicação. Os GCs de maior geração incluem todos os GCs de menor geração.|
| **Coleções de lixo gen 2** | O número de vezes que a geração 2 objetos são lixo recolhidos desde o início do processo de aplicação.|
| **N.º de Identificadores** | O número total de pegas atualmente abertas pelo processo da aplicação.|
| **Estado do Controlo de Saúde** | O estado de saúde médio em todos os casos da aplicação no Plano de Serviço de Aplicações.|
| **Http 2xx** | A contagem de pedidos que resultam num código de estado HTTP ≥ 200, mas < 300. |
| **Http 3xx** | A contagem de pedidos que resultam num código de estado HTTP ≥ 300 mas < 400. |
| **Http 401** | A contagem de pedidos que resultam em código de estado HTTP 401. |
| **Http 403** | A contagem de pedidos que resultam em código de estado HTTP 403. |
| **Http 404** | A contagem de pedidos que resultam em código de estado HTTP 404. |
| **Http 406** | A contagem de pedidos que resultam em código de estado HTTP 406. |
| **Http 4xx** | A contagem de pedidos que resultam num código de estado HTTP ≥ 400 mas < 500. |
| **http Erros do servidor** | A contagem de pedidos que resultam num código de estado HTTP ≥ 500 mas < 600. |
| **IO Outros Bytes por Segundo** | A taxa a que o processo de aplicação está a emitir bytes para operações de I/S que não envolvem dados, como operações de controlo.|
| **IO Outras Operações por Segundo** | A taxa a que o processo de aplicação está a emitir operações de E/S que não são lidas ou escrevem operações.|
| **IO Ler Bytes por segundo** | A taxa a que o processo de aplicação está a ler bytes das operações de E/S.|
| **IO Ler Operações por segundo** | A taxa a que o processo de aplicação está a emitir operações de I/O.|
| **IO Escrever Bytes por segundo** | A taxa a que o processo de aplicação está a escrever bytes para operações de I/S.|
| **IO Escrever Operações por segundo** | A taxa a que o processo de aplicação está a emitir operações de I/O de escrita.|
| **Conjunto de trabalho de memória** | A quantidade atual de memória utilizada pela aplicação, em MiB. |
| **Bytes privados** | Private Bytes é o tamanho atual, em bytes, de memória que o processo de aplicação alocou que não pode ser partilhado com outros processos.|
| **Pedidos** | O número total de pedidos, independentemente do código de estado HTTP resultante. |
| **Pedidos na fila de aplicações** | O número de pedidos na fila de pedidos.|
| **Contagem de fios** | O número de fios atualmente ativos no processo de aplicação.|
| **Domínios totais de aplicativos** | O número atual de AppDomains carregado nesta aplicação.|
| **Total de domínios de aplicações descarregados** | O número total de AppDomains descarregados desde o início da aplicação.|


Para um plano de Serviço de Aplicações, as métricas disponíveis são:

> [!NOTE]
> As métricas do plano de aplicação estão disponíveis apenas para planos nos níveis *Básico,* *Standard* e *Premium.*
> 

| Metric | Descrição |
| --- | --- |
| **Percentagem de CPU** | O CPU médio usado em todas as instâncias do plano. |
| **Percentagem de Memória** | A memória média usada em todos os casos do plano. |
| **Dados em** | A largura de banda média usada em todos os casos do plano. |
| **Data out** | A largura de banda de saída média usada em todos os casos do plano. |
| **Comprimento da fila do disco** | O número médio de pedidos de leitura e de escrita que estavam na fila no armazenamento. Um comprimento de fila de disco elevado é uma indicação de uma aplicação que pode estar a abrandar devido à i/O excessiva do disco. |
| **Comprimento da fila http** | O número médio de pedidos HTTP que tiveram de sentar-se na fila antes de serem cumpridos. Um comprimento de fila HTTP elevado ou crescente é um sintoma de um plano sob carga pesada. |

### <a name="cpu-time-vs-cpu-percentage"></a>Percentagem de tempo de CPU vs CPU
<!-- To do: Fix Anchor (#CPU-time-vs.-CPU-percentage) -->

Existem duas métricas que refletem o uso do CPU:

**TEMPO CPU**: Útil para aplicações hospedadas em planos gratuitos ou partilhados, porque uma das suas quotas é definida em minutos de CPU utilizados pela app.

**Percentagem de CPU**: Útil para aplicações hospedadas em planos Básicos, Standard e Premium, porque podem ser dimensionadas. A percentagem de CPU é uma boa indicação da utilização global em todos os casos.

## <a name="metrics-granularity-and-retention-policy"></a>Política de granularidade e retenção de métricas
As métricas para um plano de serviço de aplicações e aplicações são registadas e agregadas pelo serviço e [mantidas de acordo com estas regras.](../azure-monitor/essentials/data-platform-metrics.md#retention-of-metrics)

## <a name="monitoring-quotas-and-metrics-in-the-azure-portal"></a>Quotas e métricas de monitorização no portal Azure
Para rever o estado das várias quotas e métricas que afetam uma app, vá ao [portal Azure](https://portal.azure.com).

![Tabela de quotas no portal Azure][quotas]

Para encontrar quotas, selecione **Definições**  >  **Quotas**. Na tabela, pode rever: 
1. O nome da quota.
1. O seu intervalo de reset.
1. O seu limite atual.
1. O seu valor atual.

![Gráfico métrico no portal Azure ][metrics] Pode aceder às métricas diretamente a partir da página **de Visão Geral** do recurso. Aqui você verá gráficos representando algumas das métricas de aplicações.

Clicar em qualquer um desses gráficos irá levá-lo à vista de métricas onde pode criar gráficos personalizados, consultar métricas diferentes e muito mais. 

Para saber mais sobre as métricas, consulte [as métricas de serviço do Monitor.](../azure-monitor/data-platform.md)

## <a name="alerts-and-autoscale"></a>Alertas e autoescala
As métricas para uma aplicação ou um plano de Serviço de Aplicações podem ser ligadas a alertas. Para obter mais informações, consulte [Receber notificações de alerta](../azure-monitor/alerts/alerts-classic-portal.md).

As aplicações do Serviço de Aplicações hospedadas em planos básicos ou superiores do Serviço de Aplicações suportam a autoescala. Com a autoescala, pode configurar regras que monitorizam as métricas do plano do Serviço de Aplicações. As regras podem aumentar ou diminuir a contagem de casos, que pode fornecer recursos adicionais, se necessário. As regras também podem ajudá-lo a economizar dinheiro quando a aplicação está sobre-aprovisionada.

Para obter mais informações sobre a autoescala, consulte [Como escalar](../azure-monitor/autoscale/autoscale-get-started.md) e [As Melhores Práticas para autoscaling do Azure Monitor](../azure-monitor/autoscale/autoscale-best-practices.md).

[fzilla]:https://go.microsoft.com/fwlink/?LinkId=247914
[vmsizes]:https://go.microsoft.com/fwlink/?LinkID=309169

<!-- Images. -->
[http403]: ./media/web-sites-monitor/http403.png
[quotas]: ./media/web-sites-monitor/quotas.png
[metrics]: ./media/web-sites-monitor/metrics.png
