---
title: Métricas para Azure Spring Cloud
description: Saiba como rever métricas em Azure Spring Cloud
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 09/08/2020
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: 5a8d7d5906a769b778eb8f0ab5abe396a5f2e104
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104878630"
---
# <a name="metrics-for-azure-spring-cloud"></a>Métricas para Azure Spring Cloud

O Azure Metrics explorer é um componente do portal Microsoft Azure que permite traçar gráficos, correlacionar visualmente tendências e investigar picos e mergulhos em métricas. Use o explorador de métricas para investigar a saúde e utilização dos seus recursos. 

Em Azure Spring Cloud, existem dois miradouros para métricas.
* Gráficos em cada página geral de aplicações
* Página de métricas comuns

 ![Gráficos de Métricas](media/metrics/metrics-1.png)

Os gráficos da **visão geral** da aplicação fornecem verificações rápidas de estado para cada aplicação. A página **Métricas comuns** contém todas as métricas disponíveis para referência. Pode construir os seus próprios gráficos na página de métricas comuns e fixá-los no Dashboard.

## <a name="application-overview-page"></a>Página geral da aplicação
Selecione uma aplicação em **Apps** para encontrar gráficos na página geral.  

 ![Gestão de Métricas de Aplicação](media/metrics/metrics-2.png)

A página geral de **cada aplicação** apresenta um gráfico de métricas que lhe permite realizar uma verificação rápida do estado da sua aplicação.  

 ![Visão geral das métricas da aplicação](media/metrics/metrics-3.png)

A Azure Spring Cloud fornece estes cinco gráficos com métricas que são atualizadas a cada minuto:

* **Erros do Servidor http:** Contagem de erros para pedidos HTTP para a sua aplicação
* **Dados Em**: Bytes recebidos pela sua app
* **Data out**: Bytes enviados pela sua app
* **Pedidos**: Pedidos recebidos pela sua app
* **Tempo médio de resposta**: Tempo médio de resposta da sua aplicação

Para a tabela, pode selecionar um intervalo de tempo de uma hora a sete dias.

## <a name="common-metrics-page"></a>Página de métricas comuns

As **Métricas** no painel de navegação esquerdo ligam-se à página métrica comum.

Primeiro, selecione métricas para ver:

![Selecione vista métrica](media/metrics/metrics-4.png)

Os detalhes de todas as opções de métricas podem ser encontrados na [secção](#user-metrics-options) abaixo.

Em seguida, selecione o tipo de agregação para cada métrica:

![Agregação métrica](media/metrics/metrics-5.png)

O tipo de agregação indica como agregar pontos métricos na tabela pelo tempo. Há um ponto métrico bruto a cada minuto, e o tipo de pré-agregação dentro de um minuto é pré-definido por tipo de métricas.
* Soma: Soma todos os valores como saída-alvo.
* Média: Utilize o valor médio do período como saída-alvo.
* Max/Min: Utilize o valor Max/Min no período como saída-alvo.

O intervalo de tempo também pode ser ajustado dos últimos 30 minutos para os 30 dias ou um intervalo de tempo personalizado.

![Modificação métrica](media/metrics/metrics-6.png)

A vista padrão inclui todas as métricas da aplicação do serviço Azure Spring Cloud em conjunto. As métricas de uma aplicação ou instância podem ser filtradas no visor.  Clique **em Adicionar filtro,** desaponte a propriedade para **App** e selecione a aplicação-alvo que pretende monitorizar na caixa de texto **Values.** 

Pode utilizar dois tipos de filtros (propriedades):
* App: filtro por nome de aplicação
* Exemplo: filtrar por instância de aplicação

![Filtros métricos](media/metrics/metrics-7.png)

Também pode utilizar a opção **de divisão De Aplicação,** que irá desenhar várias linhas para uma aplicação:

![Divisão métrica](media/metrics/metrics-8.png)

>[!TIP]
> Pode construir os seus próprios gráficos na página de métricas e fixá-los no seu Painel de **Instrumentos.** Comece por nomear a sua ficha.  Em seguida, selecione **Pin para o painel no canto superior direito**. Já pode verificar a sua aplicação no seu Portal **Dashboard**.

## <a name="user-metrics-options"></a>Opções de métricas do utilizador

As tabelas que se seguem mostram as métricas e detalhes disponíveis.

### <a name="error"></a>Erro
>[!div class="mx-tdCol2BreakAll"]
>| Name | Nome métrico do actuador de primavera | Unidade | Detalhes |
>|----|----|----|------------|
>| tomcat.global.erro | tomcat.global.erro | de palavras | Número de erros ocorridos em pedidos processados |

### <a name="performance"></a>Desempenho
>[!div class="mx-tdCol2BreakAll"]
>| Name | Nome métrico do actuador de primavera | Unidade | Detalhes |
>|----|----|----|------------|
>| system.cpu.usage | system.cpu.usage | Percentagem | Utilização recente do CPU para todo o sistema (Obsoleto e não sugiro a sua utilização). Este valor é um duplo no intervalo [0.0,1.0]. Um valor de 0,0 significa que todos os CPUs estiveram inativos durante o período de tempo observado recentemente, enquanto um valor de 1.0 significa que todas as CPUs estavam a funcionar ativamente a 100% do tempo durante o período recente observado.|
>| process.cpu.usage | Percentagem de Utilização cpu de aplicação | Percentagem | Utilização recente do CPU para o processo java Virtual Machine (Obsoleto e não sugere a sua utilização). Este valor é um duplo no intervalo [0.0,1.0]. Um valor de 0.0 significa que nenhum dos CPUs estava a executar fios do processo JVM durante o recente período de tempo observado, enquanto um valor de 1.0 significa que todos os CPUs estavam a executar ativamente fios a partir do JVM 100% do tempo durante o período recente observado. Os fios do JVM incluem os fios de aplicação, bem como os fios internos JVM.|
>| AppCpuUsage | Utilização da CPU da Aplicação | Percentagem | Utilização recente do CPU do processo JVM contra o CPU atribuído a esta app, valor duplo do tipo entre [0.0,1.0]. Um valor de 0.0 significa que nenhum dos CPUs estava a executar fios do processo JVM durante o recente período de tempo observado, enquanto um valor de 1.0 significa que todos os CPUs estavam a executar ativamente fios a partir do JVM 100% do tempo durante o período recente observado. Os fios do JVM incluem os fios de aplicação, bem como os fios internos JVM.|
>| jvm.memory.committed | jvm.memory.committed | Bytes | Representa a quantidade de memória que é garantida para ser usada pelo JVM. O JVM pode libertar memória para o sistema e comprometido pode ser menor do que o init. comprometido será sempre maior ou igual a usado. |
>| jvm.memory.usado | jvm.memory.usado | Bytes | Representa a quantidade de memória atualmente utilizada nos bytes. |
>| jvm.memória.max | jvm.memória.max | Bytes | Representa a quantidade máxima de memória que pode ser usada para a gestão da memória. A quantidade de memória usada e comprometida será sempre inferior ou igual ao máximo se o máximo for definido. Uma atribuição de memória pode falhar se tentar aumentar a memória usada de tal forma que usou > cometida mesmo que usada <= max ainda seria verdade (por exemplo, quando o sistema é baixo na memória virtual). |
>| jvm.gc.max.data.size | jvm.gc.max.data.size | Bytes | O pico da memória do conjunto de memórias da velha geração desde que a máquina virtual java foi iniciada. |
>| jvm.gc.live.data.size | jvm.gc.live.data.size | Bytes | Tamanho da memória de geração velha depois de um GC completo. |
>| jvm.gc.memory.promovido | jvm.gc.memory.promovido | Bytes | Contagem de aumentos positivos no tamanho do pool de memória de geração antiga antes de GC para depois de GC. |
>| jvm.gc.memory.alocado | jvm.gc.memory.alocado | Bytes | Incrementado para um aumento no tamanho do pool de memória de geração jovem após um GC para antes do próximo. |
>| jvm.gc.pause.total.count | jvm.gc.pausa (contagem total) | de palavras | Contagem total de GC após o início deste JMV, incluindo Young and Old GC. |
>| jvm.gc.pause.total.time | jvm.gc.pausa (tempo total) | Milissegundos | Total de tempo de GC consumido após o início deste JMV, incluindo Young and Old GC. |

### <a name="performance-net"></a>Desempenho (.NET)

>[!div class="mx-tdCol2BreakAll"]
>| Name | Nome métrico do actuador de primavera | Unidade | Detalhes |
>|------|-----------------------------|------|---------|
>| Utilização da CPU       | cpu-uso      | Percentagem      | A percentagem da utilização do CPU do processo em relação a todos os recursos do SISTEMA CPU [0-100]. |
>| Conjunto de trabalho     | conjunto de trabalho    | Megabytes    | Quantidade de conjunto de trabalho utilizado pelo processo. |
>| Tamanho da pilha de GC    | gc-tamanho-heap   | Megabytes    | Tamanho total do monte relatado pelo colecionador de lixo. |
>| Contagem de Gen 0 GC  | gen-0-gc-contagem | de palavras        | Número de recolhas de lixo da Geração 0 por segundo. |
>| Contagem de Gen 1 GC  | gen-1-gc-contagem | de palavras        | Número de recolhas de lixo da Geração 1 por segundo. |
>| Contagem de Gen 2 GC  | gen-2-gc-contagem | de palavras        | Número de recolhas de lixo da Geração 2 por segundo. |
>| Tempo em GC      | timein-gc      | Percentagem      | A percentagem de tempo na recolha de lixo desde a última recolha de lixo. |
>| Tamanho da pilha gen 0 | género-0 tamanho     | Bytes        | Geração 0 tamanho de pilha. |
>| Tamanho da pilha gen 1 | tamanho gen-1     | Bytes        | Tamanho de pilha de geração 1. |
>| Tamanho da pilha Gen 2 | tamanho gen-2     | Bytes        | Tamanho do monte geração 2. |
>| Tamanho da pilha LOH   | loh tamanho       | Bytes        | Tamanho de pilha de objeto grande. |
>| Taxa de atribuição | taxa alloc     | Bytes        | Número de bytes atribuídos por segundo. |
>| Contagem de montagem  | montagem contagem | de palavras        | Número de conjuntos carregados. |
>| Contagem de exceções | exceção-contagem | de palavras       | Número de exceções por segundo. |
>| Contagem de fios de fio de linha de piscina      | threadpool-thread-contagem              | de palavras | Número de fios de piscina de rosca. |
>| Contagem de contenção de bloqueio de monitor | monitor-lock-contention-count        | de palavras | O número de vezes por segundo houve contenção quando se tentava tirar a fechadura de um monitor. |
>| Comprimento da fila da piscina de fio      | threadpool-fila-comprimento              | de palavras | Thread pool trabalho itens de fila comprimento. |
>| Contagem de itens completos da piscina de fio | threadpool-complete-items-contagem | de palavras | Contagem de artigos de trabalho concluídos na piscina de rosca. |
>| Contagem de temporizadores ativos               | contagem de temporizador ativo               | de palavras | O número de temporizadores que estão atualmente ativos. Um temporizador ativo é aquele que está registado para marcar em algum momento no futuro, e ainda não foi cancelado. |

Para mais informações, consulte [os contadores dotnet.](/dotnet/core/diagnostics/dotnet-counters)

### <a name="request"></a>Pedir
>[!div class="mx-tdCol2BreakAll"]
>| Name | Nome métrico do actuador de primavera | Unidade | Detalhes |
>|----|----|----|------------|
>| tomcat.global.enviado | tomcat.global.enviado | Bytes | Quantidade de dados que o servidor web tomcat enviado |
>| tomcat.global.recebeu | tomcat.global.recebeu | Bytes | Quantidade de dados que o servidor web Tomcat recebeu |
>| tomcat.global.request.total.count | tomcat.global.request (contagem total) | de palavras | Contagem total de pedidos processados do servidor web Tomcat |
>| tomcat.global.request.max | tomcat.global.request.max | Milissegundos | Tempo máximo do servidor web Tomcat para processar um pedido |

### <a name="request-net"></a>Pedido (.NET)

>[!div class="mx-tdCol2BreakAll"]
>| Name | Nome métrico do actuador de primavera | Unidade | Detalhes |
>|------|-----------------------------|------|---------|
>| Pedidos por segundo | pedidos por segundo | de palavras | Taxa de pedido. |
>| Total de pedidos | pedidos totais | de palavras | Número total de pedidos. |
>| Pedidos atuais | pedidos correntes | de palavras | Número de pedidos atuais. |
>| Pedidos com falhas | pedidos falhados | de palavras | Número de pedidos falhados. |

Para mais informações, consulte [os contadores dotnet.](/dotnet/core/diagnostics/dotnet-counters)

### <a name="session"></a>Sessão
>[!div class="mx-tdCol2BreakAll"]
>| Name | Nome métrico do actuador de primavera | Unidade | Detalhes |
>|----|----|----|------------|
>| tomcat.sessions.ative.max | tomcat.sessions.ative.max | de palavras | Número máximo de sessões que estiveram ativas ao mesmo tempo |
>| tomcat.sessions.alive.max | tomcat.sessions.alive.max | Milissegundos | Mais tempo (em segundos) que uma sessão expirada estava viva |
>| tomcat.sessions.created | tomcat.sessions.created | de palavras | Número de sessões que foram criadas |
>| tomcat.sessions.expirou | tomcat.sessions.expirou | de palavras | Número de sessões que expiraram |
>| tomcat.sessions.rejeitado | tomcat.sessions.rejeitado | de palavras | Número de sessões que não foram criadas porque o número máximo de sessões ativas atingiu. |
>| tomcat.sessions.ative.current | tomcat.sessions.ative.current | de palavras | Contagem ativa da sessão de Tomcat |

## <a name="see-also"></a>Ver também

* [Quickstart: Monitorização de aplicativos Azure Spring Cloud com registos, métricas e rastreio](spring-cloud-quickstart-logs-metrics-tracing.md)

* [Getting started with Azure Metrics Explorer](../azure-monitor/essentials/metrics-getting-started.md) (Introdução ao Explorador de Métricas do Azure)

* [Analisar registos e métricas com definições de diagnóstico](./diagnostic-services.md)

## <a name="next-steps"></a>Passos seguintes

* [Tutorial: Monitorar recursos da Nuvem de primavera usando alertas e grupos de ação](./spring-cloud-tutorial-alerts-action-groups.md)

* [Quotas e Planos de Serviço para Azure Spring Cloud](./spring-cloud-quotas.md)
