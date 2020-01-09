---
title: Noções básicas sobre métricas para o Azure Spring Cloud
description: Saiba como examinar as métricas no Azure Spring Cloud
author: jpconnock
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 12/06/2019
ms.author: jeconnoc
ms.openlocfilehash: 682cc8383db5704b28b9f4d6b8c22c4bbfc3f1ab
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/28/2019
ms.locfileid: "75528203"
---
# <a name="understand-metrics-for-azure-spring-cloud"></a>Entender as métricas para o Azure Spring Cloud

O Azure Metrics Explorer é um componente do portal do Microsoft Azure que permite plotar gráficos, correlacionar visualmente tendências e investigar picos e quedas em métricas. Use o Metrics Explorer para investigar a integridade e a utilização de seus recursos. 

No Azure Spring Cloud, há dois pontos de exibição para métricas.
* Gráficos em cada página de visão geral do aplicativo
* Página métricas comuns

 ![Gráficos de métricas](media/metrics/metrics-1.png)

Os gráficos na **visão geral** do aplicativo fornecem verificações rápidas de status para cada aplicativo. A página **métricas** comuns contém todas as métricas disponíveis para referência. Você pode criar seus próprios gráficos na página métricas comuns e fixá-los no painel.

## <a name="application-overview-page"></a>Página Visão geral do aplicativo
Selecione um aplicativo no **Gerenciamento de aplicativos** para localizar gráficos na página Visão geral.  

 ![Gerenciamento de métricas do aplicativo](media/metrics/metrics-2.png)

A página **visão geral do aplicativo** de cada aplicativo apresenta um gráfico de métricas que permite que você execute uma verificação rápida de status do seu aplicativo.  

 ![Visão geral das métricas do aplicativo](media/metrics/metrics-3.png)

O Azure Spring Cloud fornece esses cinco gráficos com métricas que são atualizadas a cada minuto:

* **Erros do servidor http**: contagem de erros para solicitações HTTP para seu aplicativo
* **Dados em**: bytes recebidos por seu aplicativo
* **Saída de dados**: bytes enviados pelo seu aplicativo
* **Solicitações**: solicitações recebidas por seu aplicativo
* **Tempo médio de resposta**: tempo médio de resposta do seu aplicativo

Para o gráfico, você pode selecionar um intervalo de tempo de uma hora para sete dias.

## <a name="common-metrics-page"></a>Página métricas comuns

As **métricas** no painel de navegação à esquerda são vinculadas à página métricas comuns.

Primeiro, selecione as métricas a serem exibidas:

![Selecionar exibição de métrica](media/metrics/metrics-4.png)

Em seguida, selecione tipo de agregação para cada métrica:

![Agregação de métrica](media/metrics/metrics-5.png)

O tipo de agregação indica como agregar tempo. Há um ponto de métrica a cada minuto.
* Total: soma todas as métricas como saída de destino.
* Média: Use o valor médio no período como saída de destino.
* Max/min: Use o valor max/min no período como saída de destino.

O intervalo de tempo a ser mostrado também pode ser modificado.  O intervalo de tempo pode ser escolhido nos últimos 30 minutos até os últimos 30 dias ou um intervalo de tempo personalizado.

![Modificação de métrica](media/metrics/metrics-6.png)

A exibição padrão inclui todas as métricas de application's do serviço de nuvem do Spring do Azure juntas. As métricas de um aplicativo ou instância podem ser filtradas na exibição.  Clique em **Adicionar filtro**, defina a propriedade como **aplicativo**e selecione o aplicativo de destino que você deseja monitorar na caixa de texto **valores** . 

Você pode usar dois tipos de filtros (Propriedades):
* Aplicativo: filtrar por nome do aplicativo
* Instância: filtrar por instância de aplicativo

![Filtros de métrica](media/metrics/metrics-7.png)

Você também pode usar a opção **aplicar divisão** , que irá desenhar várias linhas para um aplicativo:

![Divisão de métrica](media/metrics/metrics-8.png)

>[!TIP]
> Você pode criar seus próprios gráficos na página de métricas e fixá-los em seu **painel**. Comece nomeando seu gráfico.  Em seguida, selecione **fixar no painel no canto superior direito**. Agora você pode verificar seu aplicativo no **painel**do Portal.

## <a name="user-portal-metrics-options"></a>Opções de métricas do portal do usuário

A tabela a seguir mostra as métricas e os detalhes disponíveis.
>[!div class="mx-tdBreakAll"]
>| Nome | Nome a Apresentar | Nome da métrica do atuador Spring | Unidade | Detalhes |
>|----|----|----|----|------------|
>| SystemCpuUsagePercentage | Porcentagem de uso da CPU do sistema | System. CPU. Usage | Percentagem | Uso recente da CPU para todo o sistema. Esse valor é um duplo no intervalo [0,0, 1,0]. Um valor de 0,0 significa que todas as CPUs estavam ociosas durante o período de tempo recente observado, enquanto um valor de 1,0 significa que todas as CPUs estavam executando ativamente 100% do tempo durante o período recente observado. Todos os valores entre 0,0 e 1,0 são possíveis, dependendo das atividades que estão acontecendo no sistema. Se o uso recente da CPU do sistema não estiver disponível, o método retornará um valor negativo. |
>| AppCpuUsagePercentage | Porcentagem de uso de CPU do aplicativo | Porcentagem de uso de CPU do aplicativo | Percentagem | Uso recente da CPU para o processo de Máquina Virtual Java. Esse valor é um duplo no intervalo [0,0, 1,0]. Um valor de 0,0 significa que nenhuma das CPUs estavam executando threads do processo JVM durante o período de tempo recente observado, enquanto um valor de 1,0 significa que todas as CPUs estavam ativamente executando threads da JVM 100% do tempo durante o período recente observado. Os threads da JVM incluem os threads do aplicativo, bem como os threads internos da JVM. Todos os valores entre 0,0 e 1,0 são possíveis, dependendo das atividades que estão acontecendo no processo de JVM e do sistema inteiro. Se o Máquina Virtual Java uso recente da CPU não estiver disponível, o método retornará um valor negativo. |
>| AppMemoryCommitted | Memória de aplicativo atribuída | JVM. Memory. Committed | Bytes | Representa a quantidade de memória (em bytes) que tem a garantia de estar disponível para uso pela máquina virtual Java. A quantidade de memória confirmada pode mudar ao longo do tempo (aumentar ou diminuir). A máquina virtual Java pode liberar memória para o sistema e confirmada pode ser menor que init. Commit sempre será maior ou igual a usado. |
>| AppMemoryUsed | Memória usada do aplicativo | JVM. Memory. Used | Bytes | Representa a quantidade de memória usada atualmente em bytes. |
>| AppMemoryMax | Memória máxima do aplicativo | JVM. Memory. Max | Bytes | Representa a quantidade máxima de memória (em bytes) que pode ser usada para gerenciamento de memória. Seu valor pode ser indefinido. A quantidade máxima de memória pode mudar ao longo do tempo, se definido. A quantidade de memória usada e confirmada sempre será menor ou igual ao máximo se a opção máximo for definida. Uma alocação de memória pode falhar se tentar aumentar a memória usada, de modo que usada > confirmada mesmo se usado < = Max ainda seria verdadeiro (por exemplo, quando o sistema está com pouca memória virtual). |
>| MaxOldGenMemoryPoolBytes | Tamanho máximo de dados de geração antiga disponíveis | JVM. GC. Max. Data. Size | Bytes | O pico de uso de memória do pool de memória de geração antiga desde que a máquina virtual Java foi iniciada. |
>| OldGenMemoryPoolBytes | Tamanho de dados de geração antiga | JVM. GC. Live. Data. Size | Bytes | Tamanho do pool de memória de geração antiga após um GC completo. |
>| OldGenPromotedBytes | Promover para tamanho de dados de geração antiga | JVM. GC. Memory. promovida | Bytes | Contagem de aumentos positivos no tamanho do pool de memória de geração antiga antes do GC ser após o GC. |
>| YoungGenPromotedBytes | Promover para tamanho de dados de geração jovem | JVM. GC. Memory. allocated | Bytes | Incrementado para um aumento no tamanho do pool de memória de geração jovem após um GC antes do próximo. |
>| GCPauseTotalCount | Contagem de pausar do GC | JVM. GC. PAUSE (total-contagem) | Contagem | Contagem total de GC após esse JMV iniciado, incluindo GC jovem e antigo. |
>| GCPauseTotalTime | Tempo total de pausa do GC | JVM. GC. PAUSE (tempo total) | Milissegundos | Tempo total de GC consumido após esse JMV iniciado, incluindo o GC jovem e antigo. |
>| TomcatSentBytes | Total de bytes enviados do Tomcat | Tomcat. global. sent | Bytes | Quantidade de dados do servidor Web Tomcat enviados, em bytes |
>| TomcatReceivedBytes | Total de bytes recebidos do Tomcat | Tomcat. global. Received | Bytes | Quantidade de dados do servidor Web Tomcat recebidos, em bytes |
>| TomcatRequestTotalTime | Tempo total da solicitação do Tomcat | Tomcat. global. Request (tempo total) | Milissegundos | Tempo total do servidor Web Tomcat para processar as solicitações. |
>| TomcatRequestTotalCount | Contagem total de solicitações do Tomcat | Tomcat. global. Request (total-contagem) | Contagem | Contagem total de solicitações processadas do servidor Web Tomcat |
>| TomcatRequestMaxTime | Tempo máximo de solicitação do Tomcat | Tomcat. global. Request. Max | Milissegundos | Tempo máximo do servidor Web Tomcat para processar uma solicitação |
>| TomcatErrorCount | Erro global do Tomcat | Tomcat. global. Error | Contagem | Número de erros de solicitações processadas |
>| TomcatSessionActiveMaxCount | Contagem ativa máxima da sessão do Tomcat | Tomcat. Sessions. Active. Max | Contagem | Número máximo de sessões que estão ativas ao mesmo tempo |
>| TomcatSessionAliveMaxTime | Tempo de atividade máx de sessão do Tomcat | Tomcat. Sessions. Alive. Max | Milissegundos | Tempo mais longo (em segundos) que uma sessão expirada esteve ativa |
>| TomcatSessionCreatedCount | Contagem de sessões criadas do Tomcat | Tomcat. Sessions. Created | Contagem | Número de sessões que foram criadas |
>| TomcatSessionExpiredCount | Contagem de sessões expiradas do Tomcat | Tomcat. Sessions. Expired | Contagem | Número de sessões que expiraram |
>| TomcatSessionRejectedCount | Contagem rejeitada da sessão Tomcat | Tomcat. Sessions. Rejected | Contagem | Número de sessões que não foram criadas porque o número máximo de sessões ativas foi atingido. |

## <a name="see-also"></a>Ver também
* [Getting started with Azure Metrics Explorer](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-getting-started) (Introdução ao Explorador de Métricas do Azure)

* [Analisar logs e métricas com configurações de diagnóstico](https://docs.microsoft.com/azure/spring-cloud/diagnostic-services)

## <a name="next-steps"></a>Passos seguintes
* [Tutorial: monitorar recursos de nuvem Spring usando alertas e grupos de ações](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-tutorial-alerts-action-groups)

* [Cotas e planos de serviço para o Azure Spring Cloud](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quotas)

