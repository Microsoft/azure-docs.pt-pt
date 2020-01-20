---
title: Visão geral do dimensionamento automático com conjuntos de dimensionamento de máquinas virtuais do Azure
description: Saiba mais sobre as diferentes maneiras que você pode dimensionar automaticamente um conjunto de dimensionamento de máquinas virtuais do Azure com base no desempenho ou em uma agenda fixa
author: cynthn
tags: azure-resource-manager
ms.assetid: d29a3385-179e-4331-a315-daa7ea5701df
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: eb96be187502afcccfd3fb2c88f709facfbc3b59
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/19/2020
ms.locfileid: "76278147"
---
# <a name="overview-of-autoscale-with-azure-virtual-machine-scale-sets"></a>Visão geral do dimensionamento automático com conjuntos de dimensionamento de máquinas virtuais do Azure
Um conjunto de dimensionamento de máquinas virtuais do Azure pode aumentar ou diminuir automaticamente o número de instâncias de VM que executam seu aplicativo. Esse comportamento automatizado e elástico reduz a sobrecarga de gerenciamento para monitorar e otimizar o desempenho do seu aplicativo. Você cria regras que definem o desempenho aceitável para uma experiência de cliente positiva. Quando esses limites definidos são atendidos, as regras de dimensionamento automático tomam medidas para ajustar a capacidade do seu conjunto de dimensionamento. Você também pode agendar eventos para aumentar ou diminuir automaticamente a capacidade de seu conjunto de dimensionamento em horários fixos. Este artigo fornece uma visão geral de quais métricas de desempenho estão disponíveis e quais ações o dimensionamento automático pode executar.


## <a name="benefits-of-autoscale"></a>Benefícios do dimensionamento automático
Se a exigência da aplicação aumentar, a carga sobre as instâncias de VM no conjunto de dimensionamento também aumenta. Se este aumento de carga for consistente, em vez de ser apenas uma breve exigência, pode configurar regras de dimensionamento automático para aumentar o número de instâncias de VM no conjunto de dimensionamento.

Quando estas instâncias de VM forem criadas e as aplicações forem implementadas, o conjunto de dimensionamento começa a distribuir o tráfego pelas mesmas através do balanceador de carga. Você controla quais métricas monitorar, como CPU ou memória, por quanto tempo a carga do aplicativo deve atender a um determinado limite e quantas instâncias de VM devem ser adicionadas ao conjunto de dimensionamento.

À noite ou ao fim de semana, a exigência da aplicação pode diminuir. Se esta diminuição de carga for consistente durante um certo período de tempo, pode configurar regras de dimensionamento automático para diminuir o número de instâncias de VM no conjunto de dimensionamento. Esta ação de dimensionamento para reduzir horizontalmente reduz o custo de execução do conjunto de dimensionamento, uma vez que apenas executa o número de instâncias necessário para satisfazer a exigência atual.


## <a name="use-host-based-metrics"></a>Usar métricas baseadas em host
Você pode criar regras de dimensionamento automático que têm métricas de host internas disponíveis em suas instâncias de VM. As métricas do host oferecem visibilidade do desempenho das instâncias de VM em um conjunto de dimensionamento sem a necessidade de instalar ou configurar agentes adicionais e coleções de dados. As regras de dimensionamento automático que usam essas métricas podem escalar horizontalmente ou no número de instâncias de VM em resposta ao uso da CPU, à demanda de memória ou ao acesso ao disco.

Podem ser criadas regras de dimensionamento automático que utilizem métricas baseadas no anfitrião com uma das seguintes ferramentas:

- [Portal do Azure](virtual-machine-scale-sets-autoscale-portal.md)
- [O Azure PowerShell](tutorial-autoscale-powershell.md)
- [CLI do Azure](tutorial-autoscale-cli.md)
- [Modelo do Azure](tutorial-autoscale-template.md)

Para criar regras de dimensionamento automático que usam métricas de desempenho mais detalhadas, você pode [instalar e configurar a extensão de diagnóstico do Azure](#in-guest-vm-metrics-with-the-azure-diagnostics-extension) em instâncias de VM ou [configurar seu aplicativo usar o app insights](#application-level-metrics-with-app-insights).

As regras de dimensionamento automático que usam métricas baseadas em host, métricas de VM no convidado com a extensão de diagnóstico do Azure e o app insights podem usar as seguintes definições de configuração.

### <a name="metric-sources"></a>Fontes métricas
As regras de dimensionamento automático podem usar métricas de uma das seguintes fontes:

| Origem métrica        | Caso de utilização                                                                                                                     |
|----------------------|------------------------------------------------------------------------------------------------------------------------------|
| Conjunto de dimensionamento atual    | Para métricas baseadas em host que não exigem agentes adicionais para serem instalados ou configurados.                                  |
| Conta de armazenamento      | A extensão de diagnóstico do Azure grava métricas de desempenho no armazenamento do Azure que é consumido para disparar regras de dimensionamento automático. |
| Fila do Service Bus    | Seu aplicativo ou outros componentes podem transmitir mensagens em uma fila do barramento de serviço do Azure para disparar regras.                   |
| Estatísticas das Aplicações | Um pacote de instrumentação instalado em seu aplicativo que transmite as métricas diretamente do aplicativo.                         |


### <a name="autoscale-rule-criteria"></a>Critérios de regra de dimensionamento automático
As seguintes métricas baseadas em host estão disponíveis para uso quando você cria regras de dimensionamento automático. Se você usar a extensão de diagnóstico do Azure ou o app insights, defina quais métricas monitorar e usar com regras de dimensionamento automático.

| Nome da métrica               |
|---------------------------|
| Percentagem da CPU            |
| Entrada na Rede                |
| Saída da Rede               |
| Bytes de leitura de disco           |
| Bytes de gravação no disco          |
| Operações de leitura de disco/s  |
| Operações de gravação de disco/s |
| Créditos de CPU restantes     |
| Créditos de CPU consumidos      |

Quando você cria regras de dimensionamento automático para monitorar uma determinada métrica, as regras examinam uma das seguintes ações de agregação de métricas:

| Tipo de agregação |
|------------------|
| Média          |
| Mínimo          |
| Máximo          |
| Total            |
| Último             |
| Contagem            |

As regras de dimensionamento automático são disparadas quando as métricas são comparadas com o limite definido com um dos seguintes operadores:

| Operador                 |
|--------------------------|
| Mais do que             |
| Maior que ou igual a |
| Menos do que                |
| Menor que ou igual a    |
| Igual a                 |
| Não é igual a             |


### <a name="actions-when-rules-trigger"></a>Ações quando as regras são disparadas
Quando uma regra de dimensionamento automático é disparada, seu conjunto de dimensionamento pode ser dimensionado automaticamente de uma das seguintes maneiras:

| Operação de dimensionamento     | Caso de utilização                                                                                                                               |
|---------------------|----------------------------------------------------------------------------------------------------------------------------------------|
| Aumentar a contagem em   | Um número fixo de instâncias de VM a serem criadas. Útil em conjuntos de dimensionamento com um número menor de VMs.                                           |
| Aumentar porcentagem por | Um aumento baseado em percentual de instâncias de VM. Bom para conjuntos de dimensionamento maiores em que um aumento fixo pode não melhorar visivelmente o desempenho. |
| Aumentar contagem para   | Crie quantas instâncias de VM forem necessárias para alcançar um valor máximo desejado.                                                            |
| Diminuir a contagem em   | Um número fixo de instâncias de VM a serem removidas. Útil em conjuntos de dimensionamento com um número menor de VMs.                                           |
| Diminuir porcentagem por | Uma diminuição baseada em percentual de instâncias de VM. Bom para conjuntos de dimensionamento maiores em que um aumento fixo pode não reduzir visivelmente o consumo de recursos e os custos. |
| Diminuir contagem para   | Remova quantas instâncias de VM forem necessárias para alcançar um valor mínimo desejado.                                                            |


## <a name="in-guest-vm-metrics-with-the-azure-diagnostics-extension"></a>Métricas de VM no convidado com a extensão de diagnóstico do Azure
A extensão de diagnóstico do Azure é um agente que é executado dentro de uma instância de VM. O agente monitora e salva as métricas de desempenho no armazenamento do Azure. Essas métricas de desempenho contêm informações mais detalhadas sobre o status da VM, como *AverageReadTime* para discos ou *PercentIdleTime* para CPU. Você pode criar regras de dimensionamento automático com base em uma conscientização mais detalhada do desempenho da VM, não apenas na porcentagem de uso da CPU ou do consumo de memória.

Para usar a extensão de diagnóstico do Azure, você deve criar contas de armazenamento do Azure para suas instâncias de VM, instalar o agente de diagnóstico do Azure e configurar as VMs para transmitir contadores de desempenho específicos para a conta de armazenamento.

Para obter mais informações, veja os artigos para saber como ativar a extensão de diagnóstico do Azure numa [VM do Linux](../virtual-machines/extensions/diagnostics-linux.md) ou numa [VM do Windows](../virtual-machines/extensions/diagnostics-windows.md).


## <a name="application-level-metrics-with-app-insights"></a>Métricas de nível de aplicativo com o app insights
Para obter mais visibilidade no desempenho de seus aplicativos, você pode usar Application Insights. Você instala um pacote de instrumentação pequeno em seu aplicativo que monitora o aplicativo e envia a telemetria para o Azure. Você pode monitorar as métricas, como os tempos de resposta do aplicativo, o desempenho de carregamento de página e as contagens de sessão. Essas métricas de aplicativo podem ser usadas para criar regras de dimensionamento automático em um nível granular e incorporado à medida que você dispara regras com base em informações acionáveis que podem afetar a experiência do cliente.

Para obter mais informações sobre o App Insights, veja [O que é o Application Insights](../azure-monitor/app/app-insights-overview.md).


## <a name="scheduled-autoscale"></a>Dimensionamento automático agendado
Você também pode criar regras de dimensionamento automático com base em agendas. Essas regras baseadas em agendamento permitem que você dimensione automaticamente o número de instâncias de VM em horários fixos. Com as regras baseadas em desempenho, pode haver um impacto no desempenho do aplicativo antes que as regras de dimensionamento automático sejam disparadas e que as novas instâncias de VM sejam provisionadas. Se você puder prever essa demanda, as instâncias de VM adicionais serão provisionadas e estarão prontas para uso adicional do cliente e demanda do aplicativo.

Os exemplos a seguir são cenários que podem beneficiar o uso de regras de dimensionamento automático com base em agendamento:

- Dimensione automaticamente o número de instâncias de VM no início do dia útil quando a demanda do cliente aumenta. No final do dia de trabalho, dimensione automaticamente o número de instâncias de VM para minimizar os custos de recursos durante a noite quando o uso do aplicativo for baixo.
- Se um departamento usa um aplicativo intensamente em determinadas partes do mês ou ciclo fiscal, dimensione automaticamente o número de instâncias de VM para acomodar suas demandas adicionais.
- Quando há um evento de marketing, uma promoção ou uma venda de feriado, você pode dimensionar automaticamente o número de instâncias de VM à frente da demanda prevista do cliente. 


## <a name="next-steps"></a>Passos seguintes
Você pode criar regras de dimensionamento automático que usam métricas baseadas em host com uma das seguintes ferramentas:

- [O Azure PowerShell](tutorial-autoscale-powershell.md)
- [CLI do Azure](tutorial-autoscale-cli.md)
- [Modelo do Azure](tutorial-autoscale-template.md)

Esta visão geral detalha como usar regras de dimensionamento automático para dimensionar horizontalmente e aumentar ou diminuir o *número* de instâncias de VM em seu conjunto de dimensionamento. Você também pode dimensionar verticalmente para aumentar ou diminuir o *tamanho*da instância da VM. Para obter mais informações, consulte [autoescala vertical com conjuntos de dimensionamento de máquinas virtuais](virtual-machine-scale-sets-vertical-scale-reprovision.md).

Para obter informações sobre como gerenciar suas instâncias de VM, consulte [gerenciar conjuntos de dimensionamento de máquinas virtuais com Azure PowerShell](virtual-machine-scale-sets-windows-manage.md).

Para saber como gerar alertas quando suas regras de dimensionamento automático forem disparadas, consulte [usar ações de dimensionamento automático para enviar notificações de alerta por email e webhook no Azure monitor](../azure-monitor/platform/autoscale-webhook-email.md). Você também pode [usar logs de auditoria para enviar notificações de alerta por email e webhook no Azure monitor](../monitoring-and-diagnostics/insights-auditlog-to-webhook-email.md).
