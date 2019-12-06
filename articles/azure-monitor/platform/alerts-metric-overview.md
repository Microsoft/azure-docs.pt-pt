---
title: Entenda como os alertas de métrica funcionam no Azure Monitor.
description: Obtenha uma visão geral do que você pode fazer com alertas de métrica e como eles funcionam em Azure Monitor.
author: rboucher
ms.author: robb
ms.date: 12/5/2019
ms.topic: conceptual
ms.service: azure-monitor
ms.subservice: alerts
ms.openlocfilehash: 8f84b5641b79514ffed493302f246ecc51a20a87
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74850062"
---
# <a name="understand-how-metric-alerts-work-in-azure-monitor"></a>Entenda como os alertas de métrica funcionam no Azure Monitor

Os alertas de métrica no Azure Monitor funcionam sobre as métricas multidimensionais. Essas métricas podem ser [métricas de plataforma](alerts-metric-near-real-time.md#metrics-and-dimensions-supported), [métricas personalizadas](../../azure-monitor/platform/metrics-custom-overview.md), [logs populares de Azure monitor convertidos em métricas](../../azure-monitor/platform/alerts-metric-logs.md) e Application insights métricas. Os alertas de métricas são avaliados em intervalos regulares para verificar se as condições em uma ou mais séries temporais são verdadeiras e notificá-lo quando as avaliações forem atendidas. Os alertas de métricas são monitorados, ou seja, eles só enviam notificações quando o estado é alterado.

## <a name="how-do-metric-alerts-work"></a>Como funcionam os alertas de métrica?

Você pode definir uma regra de alerta de métrica especificando um recurso de destino a ser monitorado, nome da métrica, tipo de condição (estático ou dinâmico) e a condição (um operador e um limite/sensibilidade) e um grupo de ações a ser disparado quando a regra de alerta for acionada. Os tipos de condição afetam a maneira como os limites são determinados. [Saiba mais sobre o tipo de condição de limites dinâmicos e as opções de sensibilidade](alerts-dynamic-thresholds.md).

### <a name="alert-rule-with-static-condition-type"></a>Regra de alerta com tipo de condição estática

Digamos que você tenha criado uma regra de alerta de métrica de limite estático simples da seguinte maneira:

- Recurso de destino (o recurso do Azure que você deseja monitorar): myVM
- Métrica: percentual de CPU
- Tipo de condição: estático
- Agregação de tempo (estatística que é executada sobre valores de métrica brutos. As agregações de tempo com suporte são mín., máx., média, total, contagem): média
- Período (a janela de pesquisa sobre quais valores de métrica são verificados): nos últimos 5 minutos
- Frequência (a frequência com que o alerta de métrica verifica se as condições são atendidas): 1 min
- Operador: maior que
- Limite: 70

A partir do momento em que a regra de alerta é criada, o monitor é executado a cada 1 minuto e examina os valores de métrica dos últimos 5 minutos e verifica se a média desses valores excede 70. Se a condição for atendida ou não, a CPU percentual média dos últimos 5 minutos excede 70, a regra de alerta acionará uma notificação ativada. Se você tiver configurado um email ou uma ação de gancho da Web no grupo de ações associado à regra de alerta, receberá uma notificação ativada em ambos.

Quando você está usando várias condições em uma regra, a regra "ands" as condições juntas.  Ou seja, o alerta é disparado quando todas as condições no alerta são avaliadas como true e são resolvidas quando uma das condições não é mais verdadeira. E um exemplo desse tipo de alerta seria Alert quando "CPU maior que 90%" e "o comprimento da fila for superior a 300 itens". 

### <a name="alert-rule-with-dynamic-condition-type"></a>Regra de alerta com tipo de condição dinâmica

Digamos que você tenha criado uma regra de alerta de métrica de limites dinâmicos simples da seguinte maneira:

- Recurso de destino (o recurso do Azure que você deseja monitorar): myVM
- Métrica: percentual de CPU
- Tipo de condição: dinâmica
- Agregação de tempo (estatística que é executada sobre valores de métrica brutos. As agregações de tempo com suporte são mín., máx., média, total, contagem): média
- Período (a janela de pesquisa sobre quais valores de métrica são verificados): nos últimos 5 minutos
- Frequência (a frequência com que o alerta de métrica verifica se as condições são atendidas): 1 min
- Operador: maior que
- Sensibilidade: média
- Procurar períodos: 4
- Número de violações: 4

Depois que a regra de alerta for criada, o algoritmo de aprendizado de máquina dos limites dinâmicos obterá dados históricos disponíveis, calculará o limite que melhor se adapta ao padrão de comportamento da série métrica e aprenderá continuamente com base nos novos dados para fazer o limite mais preciso.

A partir do momento em que a regra de alerta é criada, o monitor é executado a cada 1 minuto e examina os valores de métrica nos últimos 20 minutos agrupados em 5 minutos e verifica se a média dos valores de período em cada um dos quatro períodos excede o limite esperado. Se a condição for atendida, ou seja, a porcentagem média de CPU nos últimos 20 minutos (quatro períodos de 5 minutos) desviado do comportamento esperado quatro vezes, a regra de alerta acionará uma notificação ativada. Se você tiver configurado um email ou uma ação de gancho da Web no grupo de ações associado à regra de alerta, receberá uma notificação ativada em ambos.

### <a name="view-and-resolution-of-fired-alerts"></a>Exibição e resolução de alertas acionados

Os exemplos acima de acionamento de regras de alerta também podem ser exibidos no portal do Azure na folha **todos os alertas** .

Digamos que o uso em "myVM" Continue a ultrapassar o limite nas verificações subsequentes, a regra de alerta não será acionada novamente até que as condições sejam resolvidas.

Após algum tempo, o uso em "myVM" volta para o normal (fica abaixo do limite). A regra de alerta monitora a condição mais duas vezes, para enviar uma notificação resolvida. A regra de alerta envia uma mensagem resolvida/desativada quando a condição de alerta não é atendida por três períodos consecutivos para reduzir o ruído no caso de condições de oscilação.

Como a notificação resolvida é enviada por meio de ganchos da Web ou email, o status da instância de alerta (chamada de estado do monitor) em portal do Azure também é definido como resolvido.

### <a name="using-dimensions"></a>Usando dimensões

Os alertas de métrica no Azure Monitor também dão suporte ao monitoramento de combinações de valor de várias dimensões com uma regra. Vamos entender por que você pode usar várias combinações de dimensão com a ajuda de um exemplo.

Digamos que você tenha um plano do serviço de aplicativo para seu site. Você deseja monitorar o uso da CPU em várias instâncias que executam seu site/aplicativo. Você pode fazer isso usando uma regra de alerta de métrica da seguinte maneira:

- Recurso de destino: myAppServicePlan
- Métrica: percentual de CPU
- Tipo de condição: estático
- Dimensões
  - Instância = InstanceName1, InstanceName2
- Agregação de tempo: média
- Período: nos últimos 5 minutos
- Frequência: 1 minuto
- Operador: GreaterThan
- Limite: 70

Assim como antes, essa regra monitora se o uso médio de CPU dos últimos 5 minutos excede 70%. No entanto, com a mesma regra, você pode monitorar duas instâncias executando seu site. Cada instância será monitorada individualmente e você receberá notificações individualmente.

Digamos que você tenha um aplicativo Web que está vendo uma demanda maciça e precisará adicionar mais instâncias. A regra acima ainda monitora apenas duas instâncias. No entanto, você pode criar uma regra da seguinte maneira:

- Recurso de destino: myAppServicePlan
- Métrica: percentual de CPU
- Tipo de condição: estático
- Dimensões
  - Instância = *
- Agregação de tempo: média
- Período: nos últimos 5 minutos
- Frequência: 1 minuto
- Operador: GreaterThan
- Limite: 70

Essa regra monitorará automaticamente todos os valores da instância, ou seja, Você pode monitorar suas instâncias à medida que elas aparecem sem a necessidade de modificar a regra de alerta de métrica novamente.

Ao monitorar várias dimensões, a regra de alertas de limites dinâmicos pode criar limites personalizados para centenas de séries métricas de cada vez. Os limites dinâmicos resultam em menos regras de alerta para gerenciar e salvar o tempo significativo no gerenciamento e na criação de regras de alertas.

Digamos que você tenha um aplicativo Web com muitas instâncias e não saiba qual é o limite mais adequado. As regras acima sempre usarão o limite de 70%. No entanto, você pode criar uma regra da seguinte maneira:

- Recurso de destino: myAppServicePlan
- Métrica: percentual de CPU
- Tipo de condição: dinâmica
- Dimensões
  - Instância = *
- Agregação de tempo: média
- Período: nos últimos 5 minutos
- Frequência: 1 minuto
- Operador: GreaterThan
- Sensibilidade: média
- Procurar períodos: 1
- Número de violações: 1

Essa regra monitora se o uso médio de CPU dos últimos 5 minutos excede o comportamento esperado para cada instância. A mesma regra que você pode monitorar instâncias à medida que elas surgem sem a necessidade de modificar a regra de alerta de métrica novamente. Cada instância receberá um limite que se ajusta ao padrão de comportamento da série métrica e será alterada continuamente com base em novos dados para tornar o limite mais preciso. Assim como antes, cada instância será monitorada individualmente e você receberá notificações individualmente.

O aumento dos períodos de look-back e o número de violações também pode permitir que os alertas de filtragem apenas Alertem a definição de um desvio significativo. [Saiba mais sobre as opções avançadas de limites dinâmicos](alerts-dynamic-thresholds.md#what-do-the-advanced-settings-in-dynamic-thresholds-mean).

## <a name="monitoring-at-scale-using-metric-alerts-in-azure-monitor"></a>Monitoramento em escala usando alertas de métrica no Azure Monitor

Até agora, você viu como um único alerta de métrica pode ser usado para monitorar uma ou várias séries temporais relacionadas a um único recurso do Azure. Muitas vezes, talvez você queira que a mesma regra de alerta seja aplicada a muitos recursos. O Azure Monitor também dá suporte ao monitoramento de vários recursos com uma regra de alerta de métrica. No momento, esse recurso tem suporte apenas em máquinas virtuais, bancos de dados do SQL Server, pools elásticos do SQL Server e dispositivos de borda da caixa do data box. Além disso, um único alerta de métrica pode monitorar recursos em uma região do Azure.

Você pode especificar o escopo do monitoramento por um único alerta de métrica de uma das três maneiras:

- como uma lista de máquinas virtuais em uma região do Azure em uma assinatura
- todas as máquinas virtuais (em uma região do Azure) em um ou mais grupos de recursos em uma assinatura
- todas as máquinas virtuais (em uma região do Azure) em uma assinatura

Criar regras de alerta de métricas que monitoram vários recursos é como [criar qualquer outro alerta de métrica](alerts-metric.md) que monitora um único recurso. Somente a diferença é que você selecionaria todos os recursos que deseja monitorar. Você também pode criar essas regras por meio de [modelos de Azure Resource Manager](../../azure-monitor/platform/alerts-metric-create-templates.md#template-for-a-metric-alert-that-monitors-multiple-resources). Você receberá notificações individuais para cada máquina virtual.

## <a name="typical-latency"></a>Latência típica

Para alertas de métrica, normalmente você será notificado em menos de 5 minutos se definir a frequência da regra de alerta como 1 minuto. Em casos de carga pesada para sistemas de notificação, você poderá ver uma latência mais longa.

## <a name="supported-resource-types-for-metric-alerts"></a>Tipos de recursos com suporte para alertas de métrica

Você pode encontrar a lista completa de tipos de recursos com suporte neste [artigo](../../azure-monitor/platform/alerts-metric-near-real-time.md#metrics-and-dimensions-supported).


## <a name="next-steps"></a>Passos seguintes

- [Saiba como criar, exibir e gerenciar alertas de métrica no Azure](alerts-metric.md)
- [Saiba como implantar alertas de métrica usando modelos de Azure Resource Manager](../../azure-monitor/platform/alerts-metric-create-templates.md)
- [Saiba mais sobre grupos de ação](action-groups.md)
- [Saiba mais sobre o tipo de condição de limites dinâmicos](alerts-dynamic-thresholds.md)
