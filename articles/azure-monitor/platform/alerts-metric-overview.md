---
title: Entenda como os alertas métricos funcionam no Monitor Azure.
description: Obtenha uma visão geral do que pode fazer com alertas métricos e como funcionam no Monitor Azure.
ms.date: 03/17/2020
ms.topic: conceptual
ms.subservice: alerts
ms.openlocfilehash: a6860cad077b597df923274f8971f5652d4ba9e3
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2020
ms.locfileid: "80397978"
---
# <a name="understand-how-metric-alerts-work-in-azure-monitor"></a>Compreender a forma como os alertas de métrica funcionam no Azure Monitor

Alertas métricos no Azure Monitor funcionam em cima de métricas multidimensionais. Estas métricas podem ser métricas de [plataforma,](alerts-metric-near-real-time.md#metrics-and-dimensions-supported) [métricas personalizadas,](../../azure-monitor/platform/metrics-custom-overview.md) [registos populares do Azure Monitor convertidos em métricas](../../azure-monitor/platform/alerts-metric-logs.md) e métricas de Insights de Aplicação. Os alertas métricos avaliam periodicamente para verificar se as condições de uma ou mais séries de tempo métricas são verdadeiras e notificá-lo quando as avaliações são satisfeitas. Os alertas métricos são audais, ou seja, só enviam notificações quando o Estado muda.

## <a name="how-do-metric-alerts-work"></a>Como funcionam os alertas métricos?

Pode definir uma regra de alerta métrico especificando um recurso-alvo a ser monitorizado, nome métrico, tipo de condição (estático ou dinâmico), e a condição (um operador e um limiar/sensibilidade) e um grupo de ação a ser acionado quando a regra de alerta dispara. Os tipos de condição afetam a forma como os limiares são determinados. Saiba mais sobre o tipo de [condição dos Limiares Dinâmicos e opções](alerts-dynamic-thresholds.md)de sensibilidade.

### <a name="alert-rule-with-static-condition-type"></a>Regra de alerta com tipo de condição estática

Digamos que criou uma regra de alerta métrica de limiar estática simples da seguinte forma:

- Recurso-alvo (o recurso Azure que pretende monitorizar): myVM
- Métrica: CPU percentual
- Tipo de condição: Estática
- Agregação do tempo (Estatística que é atropelada por valores métricos brutos. As agregações de tempo suportadas são Min, Max, Avg, Total, Conde): Média
- Período (A janela de olhar para trás sobre quais os valores métricos são verificados): Ao longo dos últimos 5 minutos
- Frequência (A frequência com que o alerta métrico verifica se as condições são satisfeitas): 1 min
- Operador: Maior do que
- Limiar: 70

A partir do momento em que a regra de alerta é criada, o monitor corre a cada 1 min e analisa os valores métricos durante os últimos 5 minutos e verifica se a média desses valores ultrapassa os 70. Se a condição for satisfeita, a percentagem média de CPU nos últimos 5 minutos ultrapassa os 70, a regra de alerta dispara uma notificação ativada. Se tiver configurado um e-mail ou uma ação de web hook no grupo de ação associado à regra de alerta, receberá uma notificação ativada em ambos.

Quando se está a utilizar múltiplas condições numa regra, a regra "es" as condições em conjunto.  Ou seja, o alerta dispara quando todas as condições do alerta avaliam como verdadeiras e resolvem quando uma das condições já não é verdadeira. E exemplo deste tipo de alerta seria alerta quando "CPU superior a 90%" e "o comprimento da fila é superior a 300 itens". 

### <a name="alert-rule-with-dynamic-condition-type"></a>Regra de alerta com tipo de condição dinâmica

Digamos que criou uma regra simples de alerta métrico Dynamic Thresholds da seguinte forma:

- Recurso-alvo (o recurso Azure que pretende monitorizar): myVM
- Métrica: CPU percentual
- Tipo de condição: Dinâmico
- Agregação do tempo (Estatística que é atropelada por valores métricos brutos. As agregações de tempo suportadas são Min, Max, Avg, Total, Conde): Média
- Período (A janela de olhar para trás sobre quais os valores métricos são verificados): Ao longo dos últimos 5 minutos
- Frequência (A frequência com que o alerta métrico verifica se as condições são satisfeitas): 1 min
- Operador: Maior do que
- Sensibilidade: Média
- Olhar para trás períodos: 4
- Número de Violações: 4

Uma vez criada a regra de alerta, o algoritmo de aprendizagem automática Dynamic Thresholds adquirirá dados históricos que estão disponíveis, calculará o limiar que melhor se adequa ao padrão de comportamento da série métrica e aprenderá continuamente com base em novos dados para tornar o limiar mais preciso.

A partir do momento em que a regra de alerta é criada, o monitor executa a cada 1 min e analisa os valores métricos nos últimos 20 minutos agrupados em períodos de 5 minutos e verifica se a média dos valores do período em cada um dos 4 períodos excede o limiar esperado. Se a condição for satisfeita, a percentagem média de CPU nos últimos 20 minutos (quatro períodos de 5 minutos) desviou-se do comportamento esperado quatro vezes, a regra de alerta dispara uma notificação ativada. Se tiver configurado um e-mail ou uma ação de web hook no grupo de ação associado à regra de alerta, receberá uma notificação ativada em ambos.

### <a name="view-and-resolution-of-fired-alerts"></a>Vista e resolução de alertas disparados

Os exemplos acima de disparo de regras de alerta também podem ser vistos no portal Azure na lâmina **All Alerts.**

Digamos que o uso no "myVM" continua acima do limiar em verificações subsequentes, a regra de alerta não voltará a disparar até que as condições sejam resolvidas.

Após algum tempo, o uso em "myVM" volta ao normal (vai abaixo do limiar). A regra de alerta monitoriza a condição por mais duas vezes, para enviar uma notificação resolvida. A regra do alerta envia uma mensagem resolvida/desativada quando a condição de alerta não é satisfeita por três períodos consecutivos para reduzir o ruído em caso de agitação.

À medida que a notificação resolvida é enviada através de web hooks ou e-mail, o estado da instância de alerta (chamado estado de monitor) no portal Azure também está definido para ser resolvido.

### <a name="using-dimensions"></a>Usando dimensões

Os alertas métricos no Monitor Azure também suportam a monitorização de múltiplas dimensões de combinações de valor com uma regra. Vamos entender por que você pode usar múltiplas combinações de dimensão com a ajuda de um exemplo.

Diga que tem um plano de Serviço de Aplicações para o seu website. Deseja monitorizar o uso do CPU em várias instâncias que executam o seu site/app. Pode fazê-lo usando uma regra de alerta métrico da seguinte forma:

- Recurso-alvo: myAppServicePlan
- Métrica: CPU percentual
- Tipo de condição: Estática
- Dimensões
  - Instância = Nome de instância1, nome de exemplo2
- Agregação do tempo: Média
- Período: Ao longo dos últimos 5 minutos
- Frequência: 1 min
- Operador: GreaterThan
- Limiar: 70

Como antes, esta regra monitoriza se o uso médio de CPU nos últimos 5 minutos exceder 70%. No entanto, com a mesma regra pode monitorizar duas instâncias que executam o seu website. Cada instância será monitorizada individualmente e receberá notificações individualmente.

Diga que tem uma aplicação web que está a ver uma procura massiva e que terá de adicionar mais instâncias. A regra acima ainda monitoriza apenas dois casos. No entanto, pode criar uma regra da seguinte forma:

- Recurso-alvo: myAppServicePlan
- Métrica: CPU percentual
- Tipo de condição: Estática
- Dimensões
  - Instância = *
- Agregação do tempo: Média
- Período: Ao longo dos últimos 5 minutos
- Frequência: 1 min
- Operador: GreaterThan
- Limiar: 70

Esta regra monitorizará automaticamente todos os valores por exemplo, ou seja, pode monitorizar as suas situações à medida que surgem sem precisar de modificar novamente a sua regra de alerta métrico.

Ao monitorizar várias dimensões, a regra dos alertas Dynamic Thresholds pode criar limiares personalizados para centenas de séries métricas de cada vez. Os Limiares Dinâmicos resultam em menos regras de alerta para gerir e poupar tempo significativo na gestão e criação de regras de alerta.

Diga que tem uma aplicação web com muitas instâncias e não sabe qual é o limiar mais adequado. As regras acima referidas utilizarão sempre um limiar de 70%. No entanto, pode criar uma regra da seguinte forma:

- Recurso-alvo: myAppServicePlan
- Métrica: CPU percentual
- Tipo de condição: Dinâmico
- Dimensões
  - Instância = *
- Agregação do tempo: Média
- Período: Ao longo dos últimos 5 minutos
- Frequência: 1 min
- Operador: GreaterThan
- Sensibilidade: Média
- Olhar para trás Períodos: 1
- Número de Violações: 1

Esta regra monitoriza se o uso médio do CPU nos últimos 5 minutos exceder o comportamento esperado para cada instância. A mesma regra pode monitorizar as ocorrências à medida que surgem sem precisar de modificar novamente a sua regra de alerta métrico. Cada instância terá um limiar que se adequa ao padrão de comportamento da série métrica e mudará continuamente com base em novos dados para tornar o limiar mais preciso. Como antes, cada instância será monitorizada individualmente e receberá notificações individualmente.

O aumento dos períodos de retrospetiva e o número de violações também podem permitir que os alertas de filtragem alertem apenas para a sua definição de desvio significativo. [Saiba mais sobre as opções avançadas Dynamic Thresholds.](alerts-dynamic-thresholds.md#what-do-the-advanced-settings-in-dynamic-thresholds-mean)

## <a name="monitoring-at-scale-using-metric-alerts-in-azure-monitor"></a>Monitorização em escala utilizando alertas métricos no Monitor Azure

Até agora, viu como um único alerta métrico poderia ser usado para monitorizar uma ou muitas séries métricas relacionadas com um único recurso Azure. Muitas vezes, pode querer que a mesma regra de alerta seja aplicada a muitos recursos. O Azure Monitor também apoia a monitorização de múltiplos recursos (do mesmo tipo) com uma regra de alerta métrico, para os recursos que existem na mesma região do Azure. 

Esta funcionalidade é atualmente suportada para métricas de plataforma (não métricas personalizadas) para os seguintes serviços nas seguintes nuvens Azure:

| Serviço | Azure Público | Governo | China |
|:--------|:--------|:--------|:--------|
| Máquinas virtuais  | **Sim** | Não | Não |
| Bases de dados de servidores SQL | **Sim** | **Sim** | Não |
| Piscinas elásticas de servidor SQL | **Sim** | **Sim** | Não |
| Dispositivos de borda de caixa de dados | **Sim** | **Sim** | Não |

Pode especificar o âmbito de monitorização por uma regra de alerta métrica única de uma de três maneiras. Por exemplo, com máquinas virtuais pode especificar o âmbito como:  

- uma lista de máquinas virtuais numa região do Azure dentro de uma subscrição
- todas as máquinas virtuais (numa região do Azure) em um ou mais grupos de recursos numa subscrição
- todas as máquinas virtuais (numa região do Azure) numa única subscrição

Criar regras de alerta métrico que monitorizem múltiplos recursos é como [criar qualquer outro alerta métrico](alerts-metric.md) que monitorize um único recurso. A única diferença é que escolheria todos os recursos que pretende monitorizar. Também pode criar estas regras através de modelos de [Gestor de Recursos Azure.](../../azure-monitor/platform/alerts-metric-create-templates.md#template-for-a-metric-alert-that-monitors-multiple-resources) Receberá notificações individuais para cada recurso monitorizado.

> [!NOTE]
>
> Numa regra de alerta métrico que monitoriza vários recursos, apenas uma condição é permitida.

## <a name="typical-latency"></a>Latência típica

Para alertas métricos, normalmente será notificado em menos de 5 minutos se definir a frequência da regra de alerta para ser de 1 min. Em casos de carga pesada para sistemas de notificação, poderá ver uma latência mais longa.

## <a name="supported-resource-types-for-metric-alerts"></a>Tipos de recursos suportados para alertas métricos

Pode encontrar a lista completa dos tipos de recursos suportados neste [artigo](../../azure-monitor/platform/alerts-metric-near-real-time.md#metrics-and-dimensions-supported).


## <a name="next-steps"></a>Passos seguintes

- [Saiba como criar, visualizar e gerir alertas métricos em Azure](alerts-metric.md)
- [Saiba como implementar alertas métricos usando modelos do Gestor de Recursos Do Azure](../../azure-monitor/platform/alerts-metric-create-templates.md)
- [Saiba mais sobre grupos de ação](action-groups.md)
- [Saiba mais sobre o tipo de condição dos Limiares Dinâmicos](alerts-dynamic-thresholds.md)
