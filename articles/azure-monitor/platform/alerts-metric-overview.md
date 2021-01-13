---
title: Entenda como os alertas métricos funcionam no Azure Monitor.
description: Obtenha uma visão geral do que pode fazer com alertas métricos e como funcionam no Azure Monitor.
ms.date: 01/13/2021
ms.topic: conceptual
ms.subservice: alerts
ms.openlocfilehash: d8e0621f88455a68ad0ee1b236f6b423c9d54b52
ms.sourcegitcommit: c136985b3733640892fee4d7c557d40665a660af
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/13/2021
ms.locfileid: "98179899"
---
# <a name="understand-how-metric-alerts-work-in-azure-monitor"></a>Compreender a forma como os alertas de métrica funcionam no Azure Monitor

Os alertas métricos no Azure Monitor funcionam em cima de métricas multidimensionais. Estas métricas podem ser [métricas de plataforma,](alerts-metric-near-real-time.md#metrics-and-dimensions-supported) [métricas personalizadas,](./metrics-custom-overview.md) [registos populares do Azure Monitor convertidos em métricas](./alerts-metric-logs.md) e métricas de Insights de Aplicação. Os alertas métricos avaliam em intervalos regulares para verificar se as condições de uma ou mais séries de tempo métricas são verdadeiras e notificam-no quando as avaliações forem satisfeitas. Os alertas métricos são imponentes, ou seja, só enviam notificações quando o Estado muda.

## <a name="how-do-metric-alerts-work"></a>Como funcionam os alertas métricos?

Pode definir uma regra de alerta métrico especificando um recurso-alvo a monitorizar, nome métrico, tipo de condição (estático ou dinâmico), e a condição (um operador e um limiar/sensibilidade) e um grupo de ação a ser acionado quando a regra de alerta dispara. Os tipos de condição afetam a forma como os limiares são determinados. [Saiba mais sobre o tipo de condições e opções de sensibilidade dos Limiares Dinâmicos](alerts-dynamic-thresholds.md).

### <a name="alert-rule-with-static-condition-type"></a>Regra de alerta com tipo de condição estática

Digamos que criou uma regra de alerta métrica de limiar estático da seguinte forma:

- Recurso-alvo (o recurso Azure que pretende monitorizar): myVM
- Métrica: Percentagem CPU
- Tipo de condição: Estático
- Agregação do tempo (Estatística que é atropelada por valores métricos brutos. [Agregações de tempo suportadas](metrics-charts.md#changing-aggregation) são Min, Max, Avg, Total, Contagem): Média
- Período (A janela de olhar para trás sobre os valores métricos são verificados): Ao longo dos últimos 5 minutos
- Frequência (A frequência com que o alerta métrico verifica se as condições são satisfeitas): 1 min
- Operador: Maior do que
- Limiar: 70

A partir do momento em que a regra de alerta é criada, o monitor corre a cada 1 minuto e olha para os valores métricos dos últimos 5 minutos e verifica se a média desses valores excede 70. Se a condição for cumprida, ou seja, a percentagem média de CPU nos últimos 5 minutos ultrapassa os 70, a regra de alerta dispara uma notificação ativada. Se tiver configurado um e-mail ou uma ação de web hook no grupo de ação associado à regra de alerta, receberá uma notificação ativada em ambos.

Quando se usa múltiplas condições numa regra, a regra "e" as condições em conjunto. Ou seja, um alerta dispara quando todas as condições da regra de alerta avaliam como verdadeiras e resolvem quando uma das condições já não é verdadeira. Um exemplo para este tipo de regra de alerta seria monitorizar uma máquina virtual Azure e alertar quando ambos "Percentagem CPU é superior a 90%" e "O comprimento da fila é superior a 300 itens".

### <a name="alert-rule-with-dynamic-condition-type"></a>Regra de alerta com tipo de condição dinâmica

Digamos que criou uma regra simples de alerta de limiares dinâmicos da seguinte forma:

- Recurso-alvo (o recurso Azure que pretende monitorizar): myVM
- Métrica: Percentagem CPU
- Tipo de condição: Dinâmico
- Agregação do tempo (Estatística que é atropelada por valores métricos brutos. [Agregações de tempo suportadas](metrics-charts.md#changing-aggregation) são Min, Max, Avg, Total, Contagem): Média
- Período (A janela de olhar para trás sobre os valores métricos são verificados): Ao longo dos últimos 5 minutos
- Frequência (A frequência com que o alerta métrico verifica se as condições são satisfeitas): 1 min
- Operador: Maior do que
- Sensibilidade: Médio
- Olhar para trás Períodos: 4
- Número de Violações: 4

Uma vez criada a regra de alerta, o algoritmo de aprendizagem automática Dynamic Thresholds adquirirá dados históricos disponíveis, calculará o limiar que melhor se adequa ao padrão de comportamento da série métrica e aprenderá continuamente com base em novos dados para tornar o limiar mais preciso.

A partir do momento em que a regra de alerta é criada, o monitor corre a cada 1 minuto e olha para os valores métricos nos últimos 20 minutos agrupados em períodos de 5 minutos e verifica se a média dos valores do período em cada um dos 4 períodos excede o limiar esperado. Se a condição for cumprida, ou seja, a percentagem média de CPU nos últimos 20 minutos (quatro períodos de 5 minutos) desviou-se do comportamento esperado quatro vezes, a regra de alerta dispara uma notificação ativada. Se tiver configurado um e-mail ou uma ação de web hook no grupo de ação associado à regra de alerta, receberá uma notificação ativada em ambos.

### <a name="view-and-resolution-of-fired-alerts"></a>Ver e resolução de alertas disparados

Os exemplos acima de alerta de disparo também podem ser vistos no portal Azure na lâmina **All Alerts.**

Digamos que o uso em "myVM" continua acima do limiar em verificações subsequentes, a regra de alerta não voltará a disparar até que as condições sejam resolvidas.

Após algum tempo, a utilização em "myVM" volta ao normal (vai abaixo do limiar). A regra de alerta monitoriza a condição por mais duas vezes, para enviar uma notificação resolvida. A regra de alerta envia uma mensagem resolvida/desativada quando a condição de alerta não é cumprida durante três períodos consecutivos para reduzir o ruído em caso de condições de abanão.

À medida que a notificação resolvida é enviada através de ganchos web ou e-mail, o estado da instância de alerta (chamado estado de monitor) no portal Azure também está definido para ser resolvido.

> [!NOTE]
>
> Quando uma regra de alerta monitoriza várias condições, um alerta de disparo será resolvido se pelo menos uma das condições deixar de ser satisfeita por três períodos consecutivos.

### <a name="using-dimensions"></a>Usando dimensões

Os alertas métricos no Azure Monitor também suportam a monitorização de combinações de valor de múltiplas dimensões com uma regra. Vamos entender por que pode usar combinações de múltiplas dimensões com a ajuda de um exemplo.

Diga que tem um plano de Serviço de Aplicações para o seu website. Pretende monitorizar a utilização do CPU em várias instâncias que executam o seu site/app. Pode fazê-lo usando uma regra de alerta métrico da seguinte forma:

- Recurso-alvo: myAppServicePlan
- Métrica: Percentagem CPU
- Tipo de condição: Estático
- Dimensões
  - Instância = InstanceName1, InstanceName2
- Agregação de tempo: Média
- Período: Nos últimos 5 minutos
- Frequência: 1 min
- Operador: GreaterThan
- Limiar: 70

Como antes, esta regra monitoriza se o uso médio de CPU nos últimos 5 minutos exceder 70%. No entanto, com a mesma regra pode monitorizar duas instâncias que executam o seu website. Cada instância será monitorizada individualmente e receberá notificações individualmente.

Digamos que tem uma aplicação web que está a ver uma procura massiva e que terá de adicionar mais instâncias. A regra acima ainda monitoriza apenas duas instâncias. No entanto, pode criar uma regra da seguinte forma:

- Recurso-alvo: myAppServicePlan
- Métrica: Percentagem CPU
- Tipo de condição: Estático
- Dimensões
  - Instância = *
- Agregação de tempo: Média
- Período: Nos últimos 5 minutos
- Frequência: 1 min
- Operador: GreaterThan
- Limiar: 70

Esta regra irá monitorizar automaticamente todos os valores, por exemplo, i.e pode monitorizar as suas instâncias à medida que surgem sem precisar de modificar novamente a sua regra de alerta métrico.

Ao monitorizar várias dimensões, a regra dos alertas Dynamic Thresholds pode criar limiares personalizados para centenas de séries métricas de cada vez. Os Limiares Dinâmicos resultam em menos regras de alerta para gerir e poupar tempo significativo na gestão e criação de regras de alerta.

Digamos que tem uma aplicação web com muitos casos e não sabe qual é o limiar mais adequado. As regras acima referidas utilizarão sempre o limiar de 70%. No entanto, pode criar uma regra da seguinte forma:

- Recurso-alvo: myAppServicePlan
- Métrica: Percentagem CPU
- Tipo de condição: Dinâmico
- Dimensões
  - Instância = *
- Agregação de tempo: Média
- Período: Nos últimos 5 minutos
- Frequência: 1 min
- Operador: GreaterThan
- Sensibilidade: Médio
- Olhar para trás Períodos: 1
- Número de Violações: 1

Esta regra monitoriza se a utilização média do CPU nos últimos 5 minutos exceder o comportamento esperado para cada instância. A mesma regra é que pode monitorizar as instâncias à medida que surgem sem precisar de modificar novamente a sua regra de alerta métrico. Cada instância terá um limiar que se adequa ao padrão de comportamento da série métrica e mudará continuamente com base em novos dados para tornar o limiar mais preciso. Como antes, cada instância será monitorizada individualmente e receberá notificações individualmente.

O aumento dos períodos de retrocesso e o número de violações também podem permitir que os alertas de filtragem apenas alertem para a sua definição de um desvio significativo. [Saiba mais sobre as opções avançadas dynamic Thresholds](alerts-dynamic-thresholds.md#what-do-the-advanced-settings-in-dynamic-thresholds-mean).

> [!NOTE]
>
> Recomendamos a escolha de uma *granularidade agregada (Período)* maior do que a *Frequência de avaliação,* para reduzir a probabilidade de faltar à primeira avaliação de séries temporais adicionadas nos seguintes casos:
> - Regra de alerta métrico que monitoriza múltiplas dimensões – Quando uma nova combinação de valor de dimensão é adicionada
> - Regra de alerta métrico que monitoriza múltiplos recursos – Quando um novo recurso é adicionado ao âmbito
> - Regra de alerta métrico que monitoriza uma métrica que não é emitida continuamente (métrica escassa) – Quando a métrica é emitida após um período superior a 24 horas em que não foi emitida



## <a name="monitoring-at-scale-using-metric-alerts-in-azure-monitor"></a>Monitorização em escala usando alertas métricos no Azure Monitor

Até agora, você viu como um único alerta métrico poderia ser usado para monitorizar uma ou muitas séries de tempo métricas relacionadas com um único recurso Azure. Muitas vezes, pode querer a mesma regra de alerta aplicada a muitos recursos. O Azure Monitor também apoia a monitorização de múltiplos recursos (do mesmo tipo) com uma regra de alerta métrico, para os recursos que existem na mesma região do Azure. 

Esta funcionalidade é atualmente suportada para métricas de plataforma (não métricas personalizadas) para os seguintes serviços nas seguintes nuvens Azure:

| Serviço | Azure Público | Governo | China |
|:--------|:--------|:--------|:--------|
| Máquinas virtuais<sup>1</sup>  | **Sim** | **Sim** | Não |
| Bases de dados de servidores SQL | **Sim** | **Sim** | **Sim** |
| Piscinas elásticas do servidor SQL | **Sim** | **Sim** | **Sim** |
| NetApp arquiva grupos de capacidade | **Sim** | **Sim** | **Sim** |
| Volumes de ficheiros NetApp | **Sim** | **Sim** | **Sim** |
| Cofres de chaves | **Sim** | **Sim** | **Sim** |
| Cache do Azure para Redis | **Sim** | **Sim** | **Sim** |
| Dispositivos de borda de caixa de dados | **Sim** | **Sim** | **Sim** |

<sup>1</sup> Não suportado para métricas de rede de máquinas virtuais (Rede No Total, Rede out Total, Fluxos de Entrada, Fluxos de Saída, Taxa máxima de criação de fluxos de entrada, taxa máxima de criação de fluxos de saída).

Pode especificar o âmbito de monitorização através de uma única regra de alerta métrico de uma de três maneiras. Por exemplo, com máquinas virtuais pode especificar o âmbito como:  

- uma lista de máquinas virtuais (numa região de Azure) dentro de uma subscrição
- todas as máquinas virtuais (numa região de Azure) em um ou mais grupos de recursos numa subscrição
- todas as máquinas virtuais (numa região de Azure) numa subscrição

> [!NOTE]
>
> O âmbito de uma regra de alerta métrico multi-recurso deve conter pelo menos um recurso do tipo de recurso selecionado.

Criar regras de alerta métrico que monitorizem múltiplos recursos é como [criar qualquer outro alerta métrico](alerts-metric.md) que monitorize um único recurso. A única diferença é que escolheria todos os recursos que pretende monitorizar. Também pode criar estas regras através dos [modelos Azure Resource Manager](./alerts-metric-create-templates.md#template-for-a-metric-alert-that-monitors-multiple-resources). Receberá notificações individuais de cada recurso monitorizado.

> [!NOTE]
>
> Numa regra de alerta métrico que monitoriza múltiplos recursos, apenas é permitida uma condição.

## <a name="typical-latency"></a>Latência típica

Normalmente, no caso dos alertas de métricas, recebe uma notificação em menos de 5 minutos se definir a frequência da regra de alerta como 1 minuto. Se os sistemas de notificação estiverem sobrecarregados, a latência poderá ser maior.

## <a name="supported-resource-types-for-metric-alerts"></a>Tipos de recursos suportados para alertas métricos

Pode encontrar a lista completa de tipos de recursos suportados neste [artigo.](./alerts-metric-near-real-time.md#metrics-and-dimensions-supported)


## <a name="next-steps"></a>Passos seguintes

- [Saiba como criar, ver e gerir alertas métricos em Azure](alerts-metric.md)
- [Saiba como criar alertas dentro do Azure Montior Metrics Explorer](./metrics-charts.md#create-alert-rules)
- [Saiba como implementar alertas métricos usando modelos do Gestor de Recursos Azure](./alerts-metric-create-templates.md)
- [Saiba mais sobre grupos de ação](action-groups.md)
- [Saiba mais sobre o tipo de condição de Limiares Dinâmicos](alerts-dynamic-thresholds.md)
- [Saiba mais sobre problemas de resolução de problemas em alertas métricos](alerts-troubleshoot-metric.md)

