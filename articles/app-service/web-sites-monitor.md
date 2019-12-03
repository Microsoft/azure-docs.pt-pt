---
title: Monitorizar aplicações
description: Saiba como monitorar aplicativos no serviço de Azure App usando o portal do Azure. Entenda as cotas e as métricas que são relatadas.
author: btardif
ms.assetid: d273da4e-07de-48e0-b99d-4020d84a425e
ms.topic: article
ms.date: 01/11/2019
ms.author: byvinyal
ms.custom: seodec18
ms.openlocfilehash: 4c0d3822b5000611d1b5229924cb44d055795468
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/02/2019
ms.locfileid: "74688283"
---
# <a name="monitor-apps-in-azure-app-service"></a>Monitorar aplicativos no serviço Azure App
O [serviço de Azure app](https://go.microsoft.com/fwlink/?LinkId=529714) fornece funcionalidade de monitoramento interna para aplicativos Web, back-ends móveis e aplicativos de API no [portal do Azure](https://portal.azure.com).

No portal do Azure, você pode examinar *cotas* e *métricas* para um aplicativo e um plano do serviço de aplicativo, bem como configurar *alertas* e *dimensionamento automático* que são métricas baseadas.

## <a name="understand-quotas"></a>Entender as cotas

Os aplicativos hospedados no serviço de aplicativo estão sujeitos a determinados limites nos recursos que eles podem usar. Os limites são definidos pelo plano do serviço de aplicativo associado ao aplicativo.

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

Se o aplicativo estiver hospedado em um plano *gratuito* ou *compartilhado* , os limites dos recursos que o aplicativo pode usar serão definidos por cotas.

Se o aplicativo estiver hospedado em um plano *básico*, *Standard*ou *Premium* , os limites dos recursos que eles podem usar serão definidos pelo *tamanho* (pequeno, médio, grande) e pela contagem de *instâncias* (1, 2, 3 e assim por diante) do plano do serviço de aplicativo.

As cotas para aplicativos livres ou compartilhados são:

| Quota | Descrição |
| --- | --- |
| **CPU (curta)** | A quantidade de CPU permitida para esse aplicativo em um intervalo de 5 minutos. Essa cota é redefinida a cada cinco minutos. |
| **CPU (dia)** | A quantidade total de CPU permitida para esse aplicativo em um dia. Essa cota é redefinida a cada 24 horas à meia-noite UTC. |
| **Memória** | A quantidade total de memória permitida para este aplicativo. |
| **Larga** | A quantidade total de largura de banda de saída permitida para esse aplicativo em um dia. Essa cota é redefinida a cada 24 horas à meia-noite UTC. |
| **WPD** | A quantidade total de armazenamento permitido. |

A única cota aplicável a aplicativos hospedados em *Basic*, *Standard*e *Premium* é FileSystem.

Para obter mais informações sobre cotas, limites e recursos específicos disponíveis para as várias SKUs do serviço de aplicativo, consulte [limites de serviço de assinatura do Azure](../azure-subscription-service-limits.md#app-service-limits).

### <a name="quota-enforcement"></a>Imposição de cota

Se um aplicativo exceder a cota de *CPU (curto)* , *CPU (dia)* ou *largura de banda* , o aplicativo será interrompido até que a cota seja redefinida. Durante esse tempo, todas as solicitações de entrada resultarão em um erro HTTP 403.

![mensagem de erro 403][http403]

Se a cota de memória do aplicativo for excedida, o aplicativo será reiniciado.

Se a cota do sistema de arquivos for excedida, qualquer operação de gravação falhará. As falhas de operação de gravação incluem qualquer gravação nos logs.

Você pode aumentar ou remover cotas do seu aplicativo atualizando seu plano do serviço de aplicativo.

## <a name="understand-metrics"></a>Entender as métricas

> [!NOTE]
> O **uso do sistema de arquivos** é uma nova métrica sendo distribuída globalmente, nenhum dado é esperado, a menos que você tenha sido na lista de permissões para visualização privada.
> 

As métricas fornecem informações sobre o aplicativo ou o comportamento do plano do serviço de aplicativo.

Para um aplicativo, as métricas disponíveis são:

| Métrica | Descrição |
| --- | --- |
| **Tempo médio de resposta** | O tempo médio necessário para o aplicativo atender solicitações, em segundos. |
| **Conjunto de trabalho de memória média** | A quantidade média de memória usada pelo aplicativo, em megabytes (MiB). |
| **Ligações** | O número de soquetes vinculados existentes na área restrita (w3wp. exe e seus processos filho).  Um soquete associado é criado chamando-se as APIs BIND ()/Connect () e permanece até que o soquete seja fechado com CloseHandle ()/closesocket (). |
| **Tempo de CPU** | A quantidade de CPU consumida pelo aplicativo, em segundos. Para obter mais informações sobre essa métrica, consulte [CPU time vs CPU Percentage](#cpu-time-vs-cpu-percentage). |
| **Assemblies atuais** | O número atual de assemblies carregados em todos os AppDomains neste aplicativo. |
| **Dados em** | A quantidade de largura de banda de entrada consumida pelo aplicativo, na MiB. |
| **Saída de dados** | A quantidade de largura de banda de saída consumida pelo aplicativo, na MiB. |
| **Uso do sistema de arquivos** | Porcentagem da cota do sistema de arquivos consumida pelo aplicativo. |
| **Coletas de lixo de Gen 0** | O número de vezes que os objetos de geração 0 são coletados como lixo desde o início do processo do aplicativo. Os GCs de geração mais alta incluem todos os GCs de geração mais baixa.|
| **Coletas de lixo da Gen 1** | O número de vezes que os objetos da geração 1 são coletados como lixo desde o início do processo do aplicativo. Os GCs de geração mais alta incluem todos os GCs de geração mais baixa.|
| **Coletas de lixo da Gen 2** | O número de vezes que os objetos de geração 2 são coletados como lixo desde o início do processo do aplicativo.|
| **Contagem de identificadores** | O número total de identificadores abertos no momento pelo processo do aplicativo.|
| **2xx http** | A contagem de solicitações que resultam em um código de status HTTP ≥ 200, mas < 300. |
| **3xx http** | A contagem de solicitações que resultam em um código de status HTTP ≥ 300, mas < 400. |
| **Http 401** | A contagem de solicitações que resultam no código de status HTTP 401. |
| **Http 403** | A contagem de solicitações que resultam no código de status HTTP 403. |
| **Http 404** | A contagem de solicitações que resultam no código de status HTTP 404. |
| **Http 406** | A contagem de solicitações que resultam no código de status HTTP 406. |
| **4xx http** | A contagem de solicitações que resultam em um código de status HTTP ≥ 400, mas < 500. |
| **Erros do servidor http** | A contagem de solicitações que resultam em um código de status HTTP ≥ 500, mas < 600. |
| **E/s outros bytes por segundo** | A taxa na qual o processo do aplicativo está emitindo bytes para operações de e/s que não envolvem dados, como operações de controle.|
| **E/s outras operações por segundo** | A taxa na qual o processo do aplicativo está emitindo operações de e/s que não são operações de leitura ou de gravação.|
| **Bytes de leitura de e/s por segundo** | A taxa na qual o processo do aplicativo está lendo bytes de operações de e/s.|
| **Operações de leitura de e/s por segundo** | A taxa na qual o processo do aplicativo está emitindo operações de e/s de leitura.|
| **Bytes de gravação de e/s por segundo** | A taxa na qual o processo do aplicativo está gravando bytes em operações de e/s.|
| **Operações de gravação de e/s por segundo** | A taxa na qual o processo do aplicativo está emitindo operações de e/s de gravação.|
| **Conjunto de trabalho de memória** | A quantidade atual de memória usada pelo aplicativo, na MiB. |
| **Bytes particulares** | Bytes privados é o tamanho atual, em bytes, da memória alocada pelo processo do aplicativo que não pode ser compartilhada com outros processos.|
| **Pedido** | O número total de solicitações, independentemente do código de status HTTP resultante. |
| **Solicitações na fila de aplicativos** | O número de solicitações na fila de solicitações do aplicativo.|
| **Contagem de threads** | O número de threads atualmente ativos no processo do aplicativo.|
| **Total de domínios de aplicativo** | O número atual de AppDomains carregados neste aplicativo.|
| **Total de domínios de aplicativo descarregados** | O número total de AppDomains descarregados desde o início do aplicativo.|


Para um plano do serviço de aplicativo, as métricas disponíveis são:

> [!NOTE]
> As métricas do plano do serviço de aplicativo estão disponíveis apenas para planos nas camadas *básica*, *Standard*e *Premium* .
> 

| Métrica | Descrição |
| --- | --- |
| **Percentual de CPU** | A média de CPU usada em todas as instâncias do plano. |
| **Porcentagem de memória** | A média de memória usada em todas as instâncias do plano. |
| **Dados em** | A largura de banda de entrada média usada em todas as instâncias do plano. |
| **Saída de dados** | A largura de banda de saída média usada em todas as instâncias do plano. |
| **Comprimento da fila de disco** | O número médio de solicitações de leitura e gravação que foram enfileiradas no armazenamento. Um comprimento de fila de disco alto é uma indicação de um aplicativo que pode estar causando lentidão devido a uma e/s excessiva de disco. |
| **Comprimento da fila http** | O número médio de solicitações HTTP que tinham que ficar na fila antes de serem atendidas. Um comprimento de fila HTTP alto ou crescente é um sintoma de um plano sob carga pesada. |

### <a name="cpu-time-vs-cpu-percentage"></a>Tempo de CPU versus percentual de CPU
<!-- To do: Fix Anchor (#CPU-time-vs.-CPU-percentage) -->

Há duas métricas que refletem o uso da CPU:

**Tempo de CPU**: útil para aplicativos hospedados em planos gratuitos ou compartilhados, pois uma de suas cotas é definida em minutos de CPU usados pelo aplicativo.

**Percentual de CPU**: útil para aplicativos hospedados nos planos básico, Standard e Premium, pois eles podem ser escalados horizontalmente. A porcentagem de CPU é uma boa indicação do uso geral em todas as instâncias.

## <a name="metrics-granularity-and-retention-policy"></a>Granularidade de métricas e política de retenção
As métricas para um aplicativo e um plano do serviço de aplicativo são registradas e agregadas pelo serviço, com as seguintes granularidades e políticas de retenção:

* As métricas de granularidade de **minuto** são mantidas por 30 horas.
* As métricas de granularidade de **hora** são retidas por 30 dias.
* As métricas de granularidade de **dia** são retidas por 30 dias.

## <a name="monitoring-quotas-and-metrics-in-the-azure-portal"></a>Monitoramento de cotas e métricas no portal do Azure
Para examinar o status das várias cotas e métricas que afetam um aplicativo, acesse o [portal do Azure](https://portal.azure.com).

![Gráfico de cotas no portal do Azure][quotas]

Para localizar cotas, selecione **configurações** > **cotas**. No gráfico, você pode examinar: 
1. O nome da cota.
1. Seu intervalo de redefinição.
1. Seu limite atual.
1. Seu valor atual.

![gráfico de métricas no portal do Azure][metrics] você pode acessar as métricas diretamente da página de **recursos** . Para personalizar o gráfico: 
1. Selecione o gráfico.
1. Selecione **Editar gráfico**.
1. Edite o **intervalo de tempo**.
1. Edite o **tipo de gráfico**.
1. Edite as métricas que você deseja exibir.  

Para saber mais sobre métricas, consulte [monitorar métricas de serviço](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md).

## <a name="alerts-and-autoscale"></a>Alertas e dimensionamento automático
As métricas para um aplicativo ou um plano do serviço de aplicativo podem ser conectadas a alertas. Para obter mais informações, consulte [Receber notificações de alerta](../monitoring-and-diagnostics/insights-alerts-portal.md).

Os aplicativos do serviço de aplicativo hospedados nos planos do serviço de aplicativo básico, Standard ou Premium oferecem suporte a autoescala. Com o dimensionamento automático, você pode configurar regras que monitoram as métricas do plano do serviço de aplicativo. As regras podem aumentar ou diminuir a contagem de instâncias, o que pode fornecer recursos adicionais, conforme necessário. As regras também podem ajudá-lo a economizar dinheiro quando o aplicativo estiver excessivamente provisionado.

Para obter mais informações sobre o dimensionamento automático, consulte [como dimensionar](../monitoring-and-diagnostics/insights-how-to-scale.md) e [as práticas recomendadas para Azure monitor autoescala](../azure-monitor/platform/autoscale-best-practices.md).

[fzilla]:https://go.microsoft.com/fwlink/?LinkId=247914
[vmsizes]:https://go.microsoft.com/fwlink/?LinkID=309169

<!-- Images. -->
[http403]: ./media/web-sites-monitor/http403.png
[quotas]: ./media/web-sites-monitor/quotas.png
[metrics]: ./media/web-sites-monitor/metrics.png