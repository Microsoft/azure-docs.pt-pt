---
title: Compreender a forma como o trabalho de alertas de métrica no Azure Monitor.
description: Obtenha uma visão geral do que pode fazer com alertas de métricas e como elas funcionam no Azure Monitor.
author: snehithm
ms.author: snmuvva
ms.date: 9/18/2018
ms.topic: conceptual
ms.service: azure-monitor
ms.subservice: alerts
ms.openlocfilehash: ce65d87142df64a9f0c27f3acdb4d6f25e86fb8a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67071633"
---
# <a name="understand-how-metric-alerts-work-in-azure-monitor"></a>Compreender a forma como o trabalho de alertas de métrica no Azure Monitor

Alertas de métricas no Azure Monitor trabalham no topo de métricas multidimensionais. Estas métricas podem ser [métricas de plataforma](alerts-metric-near-real-time.md#metrics-and-dimensions-supported), [métricas personalizadas](../../azure-monitor/platform/metrics-custom-overview.md), [registos populares do Azure Monitor convertido em métricas](../../azure-monitor/platform/alerts-metric-logs.md) e métricas do Application Insights. Alertas de métricas avaliar em intervalos regulares para verificar se a condições de métrica de um ou mais séries de tempo são verdadeiros e notificá-lo quando são cumpridas das avaliações. Alertas de métricas com monitoração de estado, ou seja, eles apenas enviam notificações quando o estado é alterado.

## <a name="how-do-metric-alerts-work"></a>Como funcionam os alertas de métricas?

Pode definir uma regra de alerta de métrica, especificando um recurso de destino a serem monitorados, nome da métrica, o tipo de condição (estático ou dinâmico) e a condição (um operador e uma limiar/sensibilidade) e um grupo de ação para ser acionado quando a regra de alerta é acionado. Tipos de condição afetam a forma como os limiares são determinados. [Saiba mais sobre opções de tipo e a sensibilidade de condição de limiares dinâmicos](alerts-dynamic-thresholds.md).

### <a name="alert-rule-with-static-condition-type"></a>Regra de alerta com o tipo de condição estática

Digamos que tenha criado uma regra de alerta de métrica de limiar estática simples da seguinte forma:

- (O recurso do Azure que pretende monitorizar) do recurso de destino: myVM
- Métrica: Percentagem da CPU
- Tipo de condição: Estático
- Agregação de tempo (estatística que é executada sobre os valores de métrica não processados. Agregações de hora suportados são Min, Máx, média, Total, contagem): Média
- Período (a vista de olhos back-janela ao longo do que métrica valores são verificados): Durante os últimos 5 minutos
- Frequência (a frequência com que o alerta de métrica verifica se as condições são cumpridas): 1 min
- Operador: Maior Que
- Limiar: 70

Desde o momento em que a regra de alerta é criada, o monitor é executado a cada 1 minuto e procura valores de métrica para os últimos 5 minutos e verifica se a média desses valores excede 70. Se a condição for cumprida, ou seja, 70 de excede a percentagem de CPU média nos últimos 5 minutos, a regra de alerta é acionado uma notificação ativada. Se tiver configurado uma mensagem de e-mail ou uma ação de hook de web no grupo de ação associado a regra de alerta, receberá uma notificação ativada em ambos.

Quando estiver a utilizar várias condições numa regra, a regra "ands" as condições em conjunto.  Ou seja, o alerta é acionado quando todas as condições no alerta avaliar como VERDADEIRO e resolver quando já não é uma das condições é verdadeira. E o exemplo deste tipo de alerta seria alertar quando "CPU superior a 90%" e "comprimento da fila é a mais de 300 itens". 

### <a name="alert-rule-with-dynamic-condition-type"></a>Regra de alerta com o tipo de condição dinâmica

Digamos que tenha criado uma regra alerta métrica de limiares dinâmicos simple da seguinte forma:

- (O recurso do Azure que pretende monitorizar) do recurso de destino: myVM
- Métrica: Percentagem da CPU
- Tipo de condição: Dinâmica
- Agregação de tempo (estatística que é executada sobre os valores de métrica não processados. Agregações de hora suportados são Min, Máx, média, Total, contagem): Média
- Período (a vista de olhos back-janela ao longo do que métrica valores são verificados): Durante os últimos 5 minutos
- Frequência (a frequência com que o alerta de métrica verifica se as condições são cumpridas): 1 min
- Operador: Maior Que
- Sensibilidade: Médio
- Períodos de back-vista de olhos: 4
- Número de violações: 4

Depois de criar a regra de alerta, os limiares dinâmicos algoritmo de machine learning irá adquirir dados históricos que estão disponíveis, calcular o limiar que melhor se adequa o padrão de comportamento da métrica de série e será continuamente Saiba com base nos dados de novo para tornar o limiar mais precisa.

Desde o momento em que a regra de alerta é criada, o monitor é executado a cada 1 minuto e analisa a valores de métrica nos últimos 20 minutos agrupados em períodos de 5 minutos e verifica se a média dos valores em cada um dos períodos de 4 excede o limiar do esperado. Se a condição for cumprida, ou seja, a média de percentagem de CPU nos últimos 20 minutos (quatro períodos de 5 minutos) desviaram da comportamento esperado quatro vezes, a regra de alerta é acionado uma notificação ativada. Se tiver configurado uma mensagem de e-mail ou uma ação de hook de web no grupo de ação associado a regra de alerta, receberá uma notificação ativada em ambos.

### <a name="view-and-resolution-of-fired-alerts"></a>Modo de exibição e a resolução de alertas acionados

Os exemplos acima de regras de alerta disparando também podem ser visualizados no portal do Azure no **todos os alertas** painel.

Digamos que a utilização no "myVM" continua a estar acima do limiar em verificações subsequentes, a regra de alerta não será acionado novamente até que as condições são resolvidas.

Após algum tempo, se a utilização no "myVM" volta e para baixo para normal ou seja, fica abaixo do limiar. A regra de alerta monitoriza a condição para mais duas vezes, para enviar uma notificação resolvido. A regra de alerta envia uma mensagem resolvido/desativada quando a condição do alerta não for cumprida para três pontos finais consecutivos reduzir o ruído em caso de em oscilação condições.

Como a notificação resolvida é enviada através de webhooks ou e-mail, o estado da instância do alerta (chamado de estado do monitor) no portal do Azure é também definido como resolvido.

### <a name="using-dimensions"></a>Usando dimensões

Alertas de métricas no Azure Monitor também suportam várias combinações de valor de dimensões com uma regra de monitorização. Vamos entender por que pode usar várias combinações de dimensão com a ajuda de um exemplo.

Suponhamos que tem um plano do serviço de aplicações para o seu site. Pretende monitorizar a utilização da CPU em várias instâncias a executar a sua aplicação/site da web. Pode fazer isso usando uma regra de alerta de métrica da seguinte forma:

- Recurso de destino: myAppServicePlan
- Métrica: Percentagem da CPU
- Tipo de condição: Estático
- Dimensões
  - Instance = InstanceName1, InstanceName2
- Agregação de tempo: Média
- Período de: Durante os últimos 5 minutos
- Frequência de: 1 min
- Operador: GreaterThan
- Limiar: 70

Como antes, esta regra monitoriza se a utilização média da CPU nos últimos 5 minutos exceder 70%. No entanto, com a mesma regra, pode monitorizar duas instâncias a executar o seu Web site. Cada instância irá obter monitorizada individualmente e recebe notificações individualmente.

Digamos que tenha uma aplicação web que está a ver a pedido em massa e terá de adicionar mais instâncias. A regra acima ainda monitoriza apenas duas instâncias. No entanto, pode criar uma regra da seguinte forma:

- Recurso de destino: myAppServicePlan
- Métrica: Percentagem da CPU
- Tipo de condição: Estático
- Dimensões
  - Instância = *
- Agregação de tempo: Média
- Período de: Durante os últimos 5 minutos
- Frequência de: 1 min
- Operador: GreaterThan
- Limiar: 70

Esta regra irá monitorizar automaticamente todos os valores para a instância como Pode monitorizar instâncias de conforme eles forem aparecendo sem a necessidade de modificar a regra de alerta de métrica novamente.

Ao monitorizar várias dimensões, limiares dinâmicos, pode criar a regra de alertas adaptadas limiares para centenas de série de métrica a cada vez. Limiares dinâmicos resulta em menos regras de alerta para gerir e a guardar no gerenciamento e a criação de regras de alertas de um tempo significativo.

Digamos que tenha uma aplicação web com muitas instâncias, e não sabe o que é o limiar mais adequado. As regras acima sempre usará o limiar de 70%. No entanto, pode criar uma regra da seguinte forma:

- Recurso de destino: myAppServicePlan
- Métrica: Percentagem da CPU
- Tipo de condição: Dinâmica
- Dimensões
  - Instância = *
- Agregação de tempo: Média
- Período de: Durante os últimos 5 minutos
- Frequência de: 1 min
- Operador: GreaterThan
- Sensibilidade: Médio
- Períodos de back-vista de olhos: 1
- Número de violações: 1

Esta regra monitoriza se a utilização média da CPU nos últimos 5 minutos exceder o comportamento esperado para cada instância. A mesma regra que pode monitorizar instâncias conforme eles forem aparecendo sem a necessidade de modificar a regra de alerta de métrica novamente. Cada instância irá obter um limiar mais adequada para o padrão de comportamento da métrica de série e será continuamente mudam com base nos novos dados para tornar o limiar mais precisas. Como antes, cada instância de será monitorizada individualmente e recebe notificações individualmente.

Aumentar a períodos de repetição de aparência e o número de violações de também pode permitir a filtragem de alertas para apenas o alerta em sua definição de um desvio significativo. [Saiba mais sobre opções avançadas de limiares dinâmicos](alerts-dynamic-thresholds.md#what-do-the-advanced-settings-in-dynamic-thresholds-mean).

## <a name="monitoring-at-scale-using-metric-alerts-in-azure-monitor"></a>Monitorização em escala com alertas de métricas no Azure Monitor

Até agora, viu como um alerta de métrica único poderia ser usado para monitorar a métrica de uma ou mais séries de tempo relacionado com um único recurso do Azure. Muitas vezes, é aconselhável a mesma regra de alerta aplicada a muitos recursos. Monitor do Azure também suporta a monitorização de vários recursos com uma regra de alerta de métrica. Esta funcionalidade é atualmente suportada apenas em máquinas virtuais. Além disso, um alerta de métrica único pode monitorizar os recursos numa região do Azure.

Pode especificar o âmbito da monitorização, um alerta de métrica único de uma das três formas:

- como uma lista de máquinas virtuais numa única região do Azure dentro de uma subscrição
- todas as máquinas virtuais (numa região do Azure) num ou mais grupos de recursos numa subscrição
- todas as máquinas virtuais (numa região do Azure) numa subscrição

A criação de regras de alerta de métrica que monitorizam os vários recursos é como [criar qualquer alerta de métrica](alerts-metric.md) que monitoriza um único recurso. Apenas a diferença é que deve selecionar todos os recursos que pretende monitorizar. Também pode criar estas regras através de [modelos Azure Resource Manager](../../azure-monitor/platform/alerts-metric-create-templates.md#template-for-metric-alert-that-monitors-multiple-resources). Receberá notificações individuais para cada máquina virtual.

## <a name="typical-latency"></a>Latência típica

Para alertas de métricas, normalmente, será notificado em menos de 5 minutos se definir a frequência de regra de alerta para ser 1 min. Em casos de sobrecarga para sistemas de notificação, poderá ver uma latência de mais tempo.

## <a name="supported-resource-types-for-metric-alerts"></a>Tipos de recurso suportados para alertas de métricas

Pode encontrar a lista completa dos tipos de recurso suportados desta [artigo](../../azure-monitor/platform/alerts-metric-near-real-time.md#metrics-and-dimensions-supported).

Se estiver a utilizam atualmente os alertas de métricas clássicas e estiver à procura para ver se os alertas de métricas suportam os todos os tipos de recurso que está a utilizar, a tabela seguinte mostra os recursos, tipos suportados através de alertas de métrica clássicas e se são suportados por alertas de métricas hoje ou não.

|Tipo de recurso suportado através de alertas de métrica clássicas | Suportado por alertas de métricas |
|-------------------------------------------------|----------------------------|
| Microsoft.ApiManagement/service | Sim |
| Microsoft.Batch/batchAccounts| Sim|
|Microsoft.Cache/redis| Sim |
|Microsoft.ClassicCompute/virtualMachines | Não |
|Microsoft.ClassicCompute/domainNames/slots/roles | Não|
|Microsoft.CognitiveServices/accounts | Não |
|Microsoft.Compute/virtualMachines | Sim|
|Microsoft.Compute/virtualMachineScaleSets| Sim|
|Microsoft.ClassicStorage/storageAccounts| Não |
|Microsoft.DataFactory/datafactories | Sim|
|Microsoft.DBforMySQL/servers| Sim|
|Microsoft.DBforPostgreSQL/servers| Sim|
|Microsoft.Devices/IotHubs | Não|
|Microsoft.DocumentDB/databaseAccounts| Sim|
|Microsoft.EventHub/namespaces | Sim|
|Microsoft.Logic/workflows | Sim|
|Microsoft.Network/loadBalancers |Sim|
|Microsoft.Network/publicIPAddresses| Sim|
|Microsoft.Network/applicationGateways| Sim|
|Microsoft.Network/expressRouteCircuits| Sim|
|Microsoft.Network/trafficManagerProfiles | Sim|
|Microsoft.Search/searchServices | Sim|
|Microsoft.ServiceBus/namespaces| Sim |
|Microsoft.Storage/storageAccounts | Sim|
|Microsoft.StreamAnalytics/streamingjobs| Sim|
|Microsoft.TimeSeriesInsights/environments | Sim|
|Microsoft. Web/serverfarms | Sim |
|Microsoft. / Sites da Web (excluindo as funções) | Sim|
|Microsoft. Web/hostingEnvironments/multiRolePools | Não|
|Microsoft. Web/hostingEnvironments/workerPools| Não |
|Microsoft.SQL/Servers | Não |

## <a name="next-steps"></a>Passos Seguintes

- [Saiba como criar, ver e gerir alertas de métricas no Azure](alerts-metric.md)
- [Saiba como implementar utilizando modelos Azure Resource Manager de alertas de métricas](../../azure-monitor/platform/alerts-metric-create-templates.md)
- [Saiba mais sobre os grupos de ação](action-groups.md)
- [Saiba mais sobre o tipo de condição de limiares dinâmicos](alerts-dynamic-thresholds.md)
