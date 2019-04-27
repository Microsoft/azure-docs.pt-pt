---
title: Descrição geral do dimensionamento automático com conjuntos de dimensionamento de máquina virtual do Azure | Documentos da Microsoft
description: Saiba mais sobre as diferentes formas que pode dimensionar automaticamente um conjunto com base no desempenho ou numa agenda fixa de dimensionamento de máquina virtual do Azure
services: virtual-machine-scale-sets
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: d29a3385-179e-4331-a315-daa7ea5701df
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/29/2018
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 610f3073594f73f04a68865593be6bfb4188d4f1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60883675"
---
# <a name="overview-of-autoscale-with-azure-virtual-machine-scale-sets"></a>Define a descrição geral do dimensionamento automático com o dimensionamento de máquina virtual do Azure
Um conjunto de dimensionamento de máquina virtual do Azure automaticamente pode aumentar ou diminuir o número de instâncias VM que execute a sua aplicação. Este comportamento automatizado e elástico reduz os custos de gestão para monitorizar e otimizar o desempenho da sua aplicação. Criar regras que definem o desempenho aceitável para uma experiência de cliente positivo. Quando esses limites definidos são cumpridos, regras de dimensionamento automático tomar medidas para ajustar a capacidade do seu conjunto de dimensionamento. Também pode agendar eventos automaticamente aumentar ou diminuir a capacidade do seu conjunto de dimensionamento em fixo de vezes. Este artigo fornece uma descrição geral do desempenho de que as métricas estão disponíveis e pode efetuar o dimensionamento automático de ações.


## <a name="benefits-of-autoscale"></a>Benefícios de dimensionamento automático
Se a exigência da aplicação aumentar, a carga sobre as instâncias de VM no conjunto de dimensionamento também aumenta. Se este aumento de carga for consistente, em vez de ser apenas uma breve exigência, pode configurar regras de dimensionamento automático para aumentar o número de instâncias de VM no conjunto de dimensionamento.

Quando estas instâncias de VM forem criadas e as aplicações forem implementadas, o conjunto de dimensionamento começa a distribuir o tráfego pelas mesmas através do balanceador de carga. Controlar que métricas para monitorizar, como CPU ou memória, o tempo que a carga de aplicação tem de cumprir um determinado limite e quantas instâncias VM para adicionar à escala definida.

À noite ou ao fim de semana, a exigência da aplicação pode diminuir. Se esta diminuição de carga for consistente durante um certo período de tempo, pode configurar regras de dimensionamento automático para diminuir o número de instâncias de VM no conjunto de dimensionamento. Esta ação de dimensionamento para reduzir horizontalmente reduz o custo de execução do conjunto de dimensionamento, uma vez que apenas executa o número de instâncias necessário para satisfazer a exigência atual.


## <a name="use-host-based-metrics"></a>Utilize as métricas baseadas no anfitrião
Pode criar regras de dimensionamento automático que métricas de anfitrião incorporadas disponíveis das instâncias de VM. Métricas de anfitrião dão-lhe visibilidade para o desempenho das instâncias de VM num conjunto sem a necessidade de instalar ou configurar agentes adicionais e coleções de dados de dimensionamento. Regras de dimensionamento automático que utilizam estas métricas podem aumentar horizontalmente ou no número de instâncias de VM em resposta a utilização da CPU, exigência de memória ou acesso ao disco.

Podem ser criadas regras de dimensionamento automático que utilizem métricas baseadas no anfitrião com uma das seguintes ferramentas:

- [Portal do Azure](virtual-machine-scale-sets-autoscale-portal.md)
- [Azure PowerShell](tutorial-autoscale-powershell.md)
- [CLI do Azure](tutorial-autoscale-cli.md)
- [Modelo do Azure](tutorial-autoscale-template.md)

Para criar regras de dimensionamento automático que utilizem métricas de desempenho mais detalhadas, pode [instalar e configurar a extensão de diagnóstico do Azure](#in-guest-vm-metrics-with-the-azure-diagnostics-extension) nas instâncias VM, ou [configurar a sua utilização de aplicações do App Insights](#application-level-metrics-with-app-insights).

Regras de dimensionamento automático que utilizem métricas baseadas no anfitrião, métricas da VM convidada com a extensão de diagnóstico do Azure e o App Insights podem utilizar as seguintes definições de configuração.

### <a name="metric-sources"></a>Origens de métrica
Regras de dimensionamento automático podem utilizar as métricas de uma das seguintes origens:

| Origem métrica        | Caso de utilização                                                                                                                     |
|----------------------|------------------------------------------------------------------------------------------------------------------------------|
| Conjunto de dimensionamento atual    | Para as métricas baseadas no anfitrião que não necessitam de agentes adicionais a serem instalados ou configurados.                                  |
| Conta de armazenamento      | A extensão de diagnóstico do Azure escreve métricas de desempenho para o armazenamento do Azure que, em seguida, é utilizado para acionar as regras de dimensionamento automático. |
| Fila do Service Bus    | Seu aplicativo ou outros componentes podem transmitir mensagens numa fila do Service bus do Azure para regras de Acionador.                   |
| Application Insights | Um pacote de instrumentação instalado em seu aplicativo que transmite em fluxo métricas diretamente a partir da aplicação.                         |


### <a name="autoscale-rule-criteria"></a>Critérios de regra de dimensionamento automático
As métricas baseadas no anfitrião seguintes estão disponíveis para utilização quando criar regras de dimensionamento automático. Se utilizar a extensão de diagnóstico do Azure ou o App Insights, define as métricas para monitorizar e utilizar com regras de dimensionamento automático.

| Nome da métrica               |
|---------------------------|
| Percentagem da CPU            |
| Entrada na Rede                |
| Saída da Rede               |
| Bytes de Leitura do Disco           |
| Bytes de Escrita de Disco          |
| Operações/Seg de Leitura do Disco  |
| Operações/Seg de Escrita de Disco |
| Créditos CPU Restantes     |
| Créditos CPU Consumidos      |

Ao criar regras de dimensionamento automático para monitorizar uma determinada métrica, as regras de observar uma das seguintes ações de agregação de métricas:

| Tipo de agregação |
|------------------|
| Média          |
| Mínimo          |
| Máximo          |
| Total            |
| Último             |
| Contagem            |

As regras de dimensionamento automático, em seguida, são acionadas quando as métricas são comparadas contra seu limiar definido com um dos seguintes operadores:

| Operador                 |
|--------------------------|
| Maior que             |
| Maior que ou igual a |
| Menos do que                |
| Menor que ou igual a    |
| Igual a                 |
| Diferente de             |


### <a name="actions-when-rules-trigger"></a>Ações quando acionam as regras
Quando um acionadores de regra de dimensionamento automático, pode dimensionar automaticamente o conjunto de dimensionamento de uma das seguintes formas:

| Operação de dimensionamento     | Caso de utilização                                                                                                                               |
|---------------------|----------------------------------------------------------------------------------------------------------------------------------------|
| Aumentar contagem em   | Um número fixo de instâncias de VM para criar. Útil para conjuntos de dimensionamento com um número menor de VMs.                                           |
| Aumentar percentagem em | Um aumento do número de instâncias de VM baseado em percentagem. Vale a escala maior define onde um aumento fixo pode não visivelmente melhorar o desempenho. |
| Aumentar contagem para   | Crie várias instâncias VM são necessários para atingir um valor máximo pretendido.                                                            |
| Diminuir contagem em   | Um número fixo de instâncias de VM para remover. Útil para conjuntos de dimensionamento com um número menor de VMs.                                           |
| Diminuir percentagem em | Uma diminuição com base na percentagem de instâncias de VM. Vale a escala maior define onde um aumento fixo pode não visivelmente reduzir o consumo de recursos e os custos. |
| Diminuir contagem até   | Remova a muitas instâncias VM são necessárias para chegar a uma quantidade mínima pretendida.                                                            |


## <a name="in-guest-vm-metrics-with-the-azure-diagnostics-extension"></a>Métricas da VM convidada com a extensão de diagnóstico do Azure
A extensão de diagnóstico do Azure é um agente que é executado dentro de uma instância de VM. O agente monitoriza e guarda as métricas de desempenho no armazenamento do Azure. Estas métricas de desempenho contêm informações mais detalhadas sobre o estado da VM, como *AverageReadTime* para discos ou *PercentIdleTime* para a CPU. Pode criar regras de dimensionamento automático com base numa deteção mais detalhada de desempenho da VM, não apenas a percentagem de consumo de memória ou de utilização da CPU.

Para utilizar a extensão de diagnóstico do Azure, tem de criar contas de armazenamento do Azure para as suas instâncias VM, instale o agente de diagnóstico do Azure e configurar as VMs para contadores de desempenho específicos de fluxo para a conta de armazenamento.

Para obter mais informações, veja os artigos para saber como ativar a extensão de diagnóstico do Azure numa [VM do Linux](../virtual-machines/extensions/diagnostics-linux.md) ou numa [VM do Windows](../virtual-machines/extensions/diagnostics-windows.md).


## <a name="application-level-metrics-with-app-insights"></a>Métricas de nível de aplicativo com o App Insights
Para obter mais visibilidade para o desempenho das suas aplicações, pode utilizar o Application Insights. Instale um pequeno pacote de instrumentação na sua aplicação que monitoriza a aplicação e envia a telemetria para o Azure. Pode monitorizar métricas, como os tempos de resposta do seu aplicativo, o desempenho da carga de página, e a sessão de conta. Estas métricas de aplicação podem ser utilizadas para criar regras de dimensionamento automático a um nível granular e incorporado, como acionar as regras com base em informações acionáveis que possam afetar a experiência do cliente.

Para obter mais informações sobre o App Insights, veja [O que é o Application Insights](../azure-monitor/app/app-insights-overview.md).


## <a name="scheduled-autoscale"></a>Dimensionamento automático agendado
Também pode criar regras de dimensionamento automático com base em agendas. Estas regras com base na agenda permitem para dimensionar automaticamente o número de instâncias de VM em fixo de vezes. Com as regras com base em desempenho, pode haver um impacto no desempenho no aplicativo antes do acionador de regras de dimensionamento automático e as novas instâncias VM são aprovisionadas. Se é possível prever esse pedido, as instâncias VM adicionais são provisionado e pronto para a procura de utilização e a aplicação de cliente adicionais.

Os exemplos seguintes são cenários que podem trazer benefícios para o uso de regras de dimensionamento automático com base na agenda:

- Dimensione automaticamente o número de instâncias de VM no início do dia de trabalho quando aumenta a procura dos clientes. No final do dia útil, dimensione automaticamente o número de instâncias de VM para minimizar os custos de recursos durante a noite, quando a utilização de aplicações é baixa.
- Se a um departamento utiliza uma aplicação muito em certas partes do mês ou fiscal ciclo, dimensione automaticamente o número de instâncias VM para acomodar as respetivas necessidades em termos adicionais.
- Quando existe um evento de marketing, promoção ou venda de feriado, pode dimensionar automaticamente o número de instâncias VM à frente da exigência de cliente previstas. 


## <a name="next-steps"></a>Passos Seguintes
Pode criar regras de dimensionamento automático que utilizem métricas baseadas no anfitrião com uma das seguintes ferramentas:

- [Azure PowerShell](tutorial-autoscale-powershell.md)
- [CLI do Azure](tutorial-autoscale-cli.md)
- [Modelo do Azure](tutorial-autoscale-template.md)

Esta descrição geral detalhada de como utilizar regras de dimensionamento automático para aumentar horizontalmente e aumentar ou diminuir a *número* de instâncias VM no seu dimensionamento definido. Também pode aumentar verticalmente para aumentar ou diminuir a instância VM *tamanho*. Para obter mais informações, consulte [dimensionamento automático Vertical com conjuntos de dimensionamento de máquinas virtuais](virtual-machine-scale-sets-vertical-scale-reprovision.md).

Para obter informações sobre como gerir as suas instâncias de VM, consulte [conjuntos de dimensionamento de máquina virtual de gerir com o Azure PowerShell](virtual-machine-scale-sets-windows-manage.md).

Para saber como gerar alertas quando o acionador de regras de seu dimensionamento automático, veja [utilizar ações de dimensionamento automático para enviar e-mail e webhook notificações de alertas no Azure Monitor](../azure-monitor/platform/autoscale-webhook-email.md). Também pode [registos de auditoria de uso para enviar e-mail e webhook notificações de alertas no Azure Monitor](../monitoring-and-diagnostics/insights-auditlog-to-webhook-email.md).
