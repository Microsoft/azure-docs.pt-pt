---
title: Métricas de compreensão para Azure Spring Cloud
description: Saiba como rever métricas em Azure Spring Cloud
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 12/06/2019
ms.author: brendm
ms.openlocfilehash: ed8a46683d79211559b4af7972c6d1bb784e1f02
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84434170"
---
# <a name="understand-metrics-for-azure-spring-cloud"></a>Compreenda as métricas para Azure Spring Cloud

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

A vista padrão inclui todas as métricas da aplicação do serviço Azure Spring Cloud em conjunto. As métricas de uma aplicação ou instância podem ser filtradas no visor.  Clique **em Adicionar filtro,** desaponte a propriedade para **App**e selecione a aplicação-alvo que pretende monitorizar na caixa de texto **Values.** 

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
>| Erro Global tomcat (precotado)<br><br>(a ser removido após 1 de julho de 2020)  | tomcat.global.erro | Contagem | Número de erros ocorridos em pedidos processados |
>| tomcat.global.erro | tomcat.global.erro | Contagem | Número de erros ocorridos em pedidos processados |

### <a name="performance"></a>Desempenho
>[!div class="mx-tdCol2BreakAll"]
>| Name | Nome métrico do actuador de primavera | Unidade | Detalhes |
>|----|----|----|------------|
>| Percentagem de Utilização do CPU do sistema (depreciada)<br><br>(a ser removido após 1 de julho de 2020) | system.cpu.usage | Percentagem | Uso recente do CPU para todo o sistema. Este valor é um duplo no intervalo [0.0,1.0]. Um valor de 0,0 significa que todos os CPUs estiveram inativos durante o período de tempo observado recentemente, enquanto um valor de 1.0 significa que todas as CPUs estavam a funcionar ativamente a 100% do tempo durante o período recente observado.|
>| system.cpu.usage | system.cpu.usage | Percentagem | Uso recente do CPU para todo o sistema. Este valor é um duplo no intervalo [0.0,1.0]. Um valor de 0,0 significa que todos os CPUs estiveram inativos durante o período de tempo observado recentemente, enquanto um valor de 1.0 significa que todas as CPUs estavam a funcionar ativamente a 100% do tempo durante o período recente observado.|
>| Percentagem de Utilização cpu de aplicação (precotado)<br><br>(removido após 1 de julho de 2020) | Percentagem de Utilização cpu de aplicação | Percentagem | Utilização recente do CPU para o processo Java Virtual Machine. Este valor é um duplo no intervalo [0.0,1.0]. Um valor de 0.0 significa que nenhum dos CPUs estava a executar fios do processo JVM durante o recente período de tempo observado, enquanto um valor de 1.0 significa que todos os CPUs estavam a executar ativamente fios a partir do JVM 100% do tempo durante o período recente observado. Os fios do JVM incluem os fios de aplicação, bem como os fios internos JVM.|
>| process.cpu.usage | Percentagem de Utilização cpu de aplicação | Percentagem | Utilização recente do CPU para o processo Java Virtual Machine. Este valor é um duplo no intervalo [0.0,1.0]. Um valor de 0.0 significa que nenhum dos CPUs estava a executar fios do processo JVM durante o recente período de tempo observado, enquanto um valor de 1.0 significa que todos os CPUs estavam a executar ativamente fios a partir do JVM 100% do tempo durante o período recente observado. Os fios do JVM incluem os fios de aplicação, bem como os fios internos JVM.|
>| Memória de aplicação Atribuída (precotado)<br><br>(a ser removido após 1 de julho de 2020) | jvm.memory.committed | Bytes | Representa a quantidade de memória que é garantida para ser usada pelo JVM. O JVM pode libertar memória para o sistema e a memória comprometida pode ser inferior à memória inicial. A memória comprometida será sempre maior ou igual à memória utilizada. |
>| jvm.memory.committed | jvm.memory.committed | Bytes | Representa a quantidade de memória que é garantida para ser usada pelo JVM. O JVM pode libertar memória para o sistema e comprometido pode ser menor do que o init. comprometido será sempre maior ou igual a usado. |
>| Memória de aplicativo utilizada (depreciada)<br><br>(a ser removido após 1 de julho de 2020) | jvm.memory.usado | Bytes | Representa a quantidade de memória atualmente utilizada nos bytes. |
>| jvm.memory.usado | jvm.memory.usado | Bytes | Representa a quantidade de memória atualmente utilizada nos bytes. |
>| App Memory Max (precotado)<br><br>(a ser removido após 1 de julho de 2020) | jvm.memory.max | Bytes | Representa a quantidade máxima de memória que pode ser usada para a gestão da memória. A quantidade de memória usada e comprometida será sempre inferior ou igual ao máximo se o máximo for definido. Uma atribuição de memória pode falhar se tentar aumentar a memória usada de tal forma que usou > cometida mesmo que usada <= max ainda seria verdade (por exemplo, quando o sistema é baixo na memória virtual). |
>| jvm.memory.max | jvm.memory.max | Bytes | Representa a quantidade máxima de memória que pode ser usada para a gestão da memória. A quantidade de memória usada e comprometida será sempre inferior ou igual ao máximo se o máximo for definido. Uma atribuição de memória pode falhar se tentar aumentar a memória usada de tal forma que usou > cometida mesmo que usada <= max ainda seria verdade (por exemplo, quando o sistema é baixo na memória virtual). |
>| Tamanho máximo de dados de geração antiga disponível (precedido)<br><br>(a ser removido após 1 de julho de 2020) | jvm.gc.max.data.size | Bytes | O pico da memória do conjunto de memórias da velha geração desde que a máquina virtual java foi iniciada. |
>| jvm.gc.max.data.size | jvm.gc.max.data.size | Bytes | O pico da memória do conjunto de memórias da velha geração desde que a máquina virtual java foi iniciada. |
>| Tamanho dos dados de geração antiga (precotado)<br><br>(a ser removido após 1 de julho de 2020) | jvm.gc.live.data.size | Bytes | Tamanho da memória de geração velha depois de um GC completo. |
>| jvm.gc.live.data.size | jvm.gc.live.data.size | Bytes | Tamanho da memória de geração velha depois de um GC completo. |
>| Tamanho dos dados de geração antiga (precotado)<br><br>(a ser removido após 1 de julho de 2020) | jvm.gc.memory.promovido | Bytes | Contagem de aumentos positivos no tamanho do pool de memória de geração antiga antes de GC para depois de GC. |
>| jvm.gc.memory.promovido | jvm.gc.memory.promovido | Bytes | Contagem de aumentos positivos no tamanho do pool de memória de geração antiga antes de GC para depois de GC. |
>| Promover para o tamanho dos dados de geração jovem (precotado)<br><br>(a ser removido após 1 de julho de 2020) | jvm.gc.memory.alocado | Bytes | Incrementado para um aumento no tamanho do pool de memória de geração jovem após um GC para antes do próximo. |
>| jvm.gc.memory.alocado | jvm.gc.memory.alocado | Bytes | Incrementado para um aumento no tamanho do pool de memória de geração jovem após um GC para antes do próximo. |
>| Contagem de pausas GC (depreciada)<br><br>(a ser removido após 1 de julho de 2020) | jvm.gc.pausa (contagem total) | Contagem | Contagem total de GC após o início deste JMV, incluindo Young and Old GC. |
>| jvm.gc.pause.total.count | jvm.gc.pausa (contagem total) | Contagem | Contagem total de GC após o início deste JMV, incluindo Young and Old GC. |
>| GC Pausa Tempo Total (precotado)<br><br>(a ser removido após 1 de julho de 2020) | jvm.gc.pausa (tempo total) | Milissegundos | Total de tempo de GC consumido após o início deste JMV, incluindo Young and Old GC. |
>| jvm.gc.pause.total.time | jvm.gc.pausa (tempo total) | Milissegundos | Total de tempo de GC consumido após o início deste JMV, incluindo Young and Old GC. |

### <a name="request"></a>Pedir
>[!div class="mx-tdCol2BreakAll"]
>| Name | Nome métrico do actuador de primavera | Unidade | Detalhes |
>|----|----|----|------------|
>| Tomcat Total Enviado Bytes (precotado)<br><br>(removido após 1 de julho de 2020) | tomcat.global.enviado | Bytes | Quantidade de dados que o servidor web tomcat enviado |
>| tomcat.global.enviado | tomcat.global.enviado | Bytes | Quantidade de dados que o servidor web tomcat enviado |
>| Tomcat Total Recebido Bytes (precotado)<br><br>(removido após 1 de julho de 2020) | tomcat.global.recebeu | Bytes | Quantidade de dados que o servidor web Tomcat recebeu |
>| tomcat.global.recebeu | tomcat.global.recebeu | Bytes | Quantidade de dados que o servidor web Tomcat recebeu |
>| Pedido Tomcat Tempo Total (precotado)<br><br>(removido após 1 de julho de 2020) | tomcat.global.request (tempo total) | Milissegundos | Tempo total do servidor web Tomcat para processar os pedidos |
>| Tomcat Request Total Count (precotado)<br><br>(removido após 1 de julho de 2020) | tomcat.global.request (contagem total) | Contagem | Contagem total de pedidos processados do servidor web Tomcat |
>| tomcat.global.request.total.count | tomcat.global.request (contagem total) | Contagem | Contagem total de pedidos processados do servidor web Tomcat |
>| Tomcat Request Max Time (precotado)<br><br>(removido após 1 de julho de 2020) | tomcat.global.request.max | Milissegundos | Tempo máximo do servidor web Tomcat para processar um pedido |
>| tomcat.global.request.max | tomcat.global.request.max | Milissegundos | Tempo máximo do servidor web Tomcat para processar um pedido |

### <a name="session"></a>Sessão
>[!div class="mx-tdCol2BreakAll"]
>| Name | Nome métrico do actuador de primavera | Unidade | Detalhes |
>|----|----|----|------------|
>| Tomcat Session Max Ative Count (precotado)<br><br>(removido após 1 de julho de 2020) | tomcat.sessions.ative.max | Contagem | Número máximo de sessões que estiveram ativas ao mesmo tempo |
>| tomcat.sessions.ative.max | tomcat.sessions.ative.max | Contagem | Número máximo de sessões que estiveram ativas ao mesmo tempo |
>| Tomcat Session Max Alive Tempo (precedido)<br><br>(removido após 1 de julho de 2020) | tomcat.sessions.alive.max | Milissegundos | Mais tempo (em segundos) que uma sessão expirada estava viva |
>| tomcat.sessions.alive.max | tomcat.sessions.alive.max | Milissegundos | Mais tempo (em segundos) que uma sessão expirada estava viva |
>| Tomcat Session Criou Conde (precotado)<br><br>(removido após 1 de julho de 2020) | tomcat.sessions.created | Contagem | Número de sessões que foram criadas |
>| tomcat.sessions.created | tomcat.sessions.created | Contagem | Número de sessões que foram criadas |
>| Contagem expirada da sessão de Tomcat (prevada)<br><br>(removido após 1 de julho de 2020) | tomcat.sessions.expirou | Contagem | Número de sessões que expiraram |
>| tomcat.sessions.expirou | tomcat.sessions.expirou | Contagem | Número de sessões que expiraram |
>| Tomcat Session Contagem Rejeitada (precotado)<br><br>(removido após 1 de julho de 2020) | tomcat.sessions.rejeitado | Contagem | Número de sessões que não foram criadas porque o número máximo de sessões ativas atingiu. |
>| tomcat.sessions.rejeitado | tomcat.sessions.rejeitado | Contagem | Número de sessões que não foram criadas porque o número máximo de sessões ativas atingiu. |
>| tomcat.sessions.ative.current | tomcat.sessions.ative.current | Contagem | Contagem ativa da sessão de Tomcat |

## <a name="see-also"></a>Veja também
* [Getting started with Azure Metrics Explorer](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-getting-started) (Introdução ao Explorador de Métricas do Azure)

* [Analisar registos e métricas com definições de diagnóstico](https://docs.microsoft.com/azure/spring-cloud/diagnostic-services)

## <a name="next-steps"></a>Próximos passos
* [Tutorial: Monitorar recursos da Nuvem de primavera usando alertas e grupos de ação](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-tutorial-alerts-action-groups)

* [Quotas e Planos de Serviço para Azure Spring Cloud](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quotas)

