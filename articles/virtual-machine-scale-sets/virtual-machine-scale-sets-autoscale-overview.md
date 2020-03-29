---
title: Descrição geral do dimensionamento automático com o conjunto de dimensionamento de máquinas virtuais do Azure
description: Saiba mais sobre as diferentes formas de escalar automaticamente um conjunto de escala de máquina virtual Azure baseado no desempenho ou num horário fixo
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76278147"
---
# <a name="overview-of-autoscale-with-azure-virtual-machine-scale-sets"></a>Descrição geral do dimensionamento automático com o conjunto de dimensionamento de máquinas virtuais do Azure
Um conjunto de escala de máquina virtual Azure pode aumentar ou diminuir automaticamente o número de casos vm que executam a sua aplicação. Este comportamento automatizado e elástico reduz a sobrecarga de gestão para monitorizar e otimizar o desempenho da sua aplicação. Cria regras que definem o desempenho aceitável para uma experiência positiva do cliente. Quando esses limiares definidos são cumpridos, as regras de escala automática tomam medidas para ajustar a capacidade do seu conjunto de escala. Também pode agendar eventos para aumentar ou diminuir automaticamente a capacidade da sua escala definida em horários fixos. Este artigo fornece uma visão geral das métricas de desempenho disponíveis e quais as ações que a escala automática pode realizar.


## <a name="benefits-of-autoscale"></a>Benefícios da escala automática
Se a exigência da aplicação aumentar, a carga sobre as instâncias de VM no conjunto de dimensionamento também aumenta. Se este aumento de carga for consistente, em vez de ser apenas uma breve exigência, pode configurar regras de dimensionamento automático para aumentar o número de instâncias de VM no conjunto de dimensionamento.

Quando estas instâncias de VM forem criadas e as aplicações forem implementadas, o conjunto de dimensionamento começa a distribuir o tráfego pelas mesmas através do balanceador de carga. Você controla quais as métricas a monitorizar, como CPU ou memória, quanto tempo a carga de aplicação deve cumprir um determinado limiar, e quantas instâncias de VM adicionar ao conjunto de escala.

À noite ou ao fim de semana, a exigência da aplicação pode diminuir. Se esta diminuição de carga for consistente durante um certo período de tempo, pode configurar regras de dimensionamento automático para diminuir o número de instâncias de VM no conjunto de dimensionamento. Esta ação de dimensionamento para reduzir horizontalmente reduz o custo de execução do conjunto de dimensionamento, uma vez que apenas executa o número de instâncias necessário para satisfazer a exigência atual.


## <a name="use-host-based-metrics"></a>Utilize métricas baseadas em hospedeiros
Pode criar regras de escala automática que as métricas de hospedagem incorporadas estão disponíveis a partir dos seus casos vm. As métricas do hospedeiro dão-lhe visibilidade no desempenho das instâncias VM num conjunto de escala sem a necessidade de instalar ou configurar agentes adicionais e recolhas de dados. As regras de escala automática que utilizam estas métricas podem escalar ou no número de casos de VM em resposta ao uso do CPU, à procura de memória ou ao acesso ao disco.

Podem ser criadas regras de dimensionamento automático que utilizem métricas baseadas no anfitrião com uma das seguintes ferramentas:

- [Portal Azure](virtual-machine-scale-sets-autoscale-portal.md)
- [Azure PowerShell](tutorial-autoscale-powershell.md)
- [Azure CLI](tutorial-autoscale-cli.md)
- [Modelo do Azure](tutorial-autoscale-template.md)

Para criar regras de escala automática que utilizem métricas de desempenho mais detalhadas, pode [instalar e configurar a extensão](#in-guest-vm-metrics-with-the-azure-diagnostics-extension) de diagnóstico do Azure em instâncias VM, ou configurar a [sua aplicação usar App Insights](#application-level-metrics-with-app-insights).

Regras de escala automática que utilizam métricas baseadas em hospedeiros, métricas VM no hóspede com a extensão de diagnóstico Azure, e App Insights podem usar as seguintes configurações de configuração.

### <a name="metric-sources"></a>Fontes métricas
As regras de escala automática podem utilizar métricas de uma das seguintes fontes:

| Origem métrica        | Caso de utilização                                                                                                                     |
|----------------------|------------------------------------------------------------------------------------------------------------------------------|
| Conjunto de escala atual    | Para métricas baseadas em hospedeiros que não exijam que agentes adicionais sejam instalados ou configurados.                                  |
| Conta de armazenamento      | A extensão de diagnóstico Azure escreve métricas de desempenho para o armazenamento Azure que é então consumido para desencadear regras de escala automática. |
| Fila do Service Bus    | A sua aplicação ou outros componentes podem transmitir mensagens numa fila do Azure Service Bus para desencadear regras.                   |
| Application Insights | Um pacote de instrumentação instalado na sua aplicação que transmite métricas diretamente da aplicação.                         |


### <a name="autoscale-rule-criteria"></a>Critérios de regra de escala automática
As seguintes métricas baseadas no hospedeiro estão disponíveis para utilização quando cria regras de escala automática. Se utilizar a extensão de diagnóstico Azure ou app Insights, define quais as métricas a monitorizar e a utilizar com regras de escala automática.

| Nome da métrica               |
|---------------------------|
| Percentagem da CPU            |
| Entrada na Rede                |
| Saída da Rede               |
| Discos Ler Bytes           |
| Bytes de Escrita de Disco          |
| Operações de leitura do disco/sec  |
| Operações de escrita de disco/sec |
| Créditos cpu restantes     |
| Créditos da CPU consumidos      |

Quando se criam regras de escala automática para monitorizar uma determinada métrica, as regras analisam uma das seguintes ações de agregação de métricas:

| Tipo de agregação |
|------------------|
| Média          |
| Mínimo          |
| Máximo          |
| Total            |
| Último             |
| Contagem            |

As regras de escala automática são então ativadas quando as métricas são comparadas com o seu limiar definido com um dos seguintes operadores:

| Operador                 |
|--------------------------|
| Maior que             |
| Maior que ou igual a |
| Menor do que                |
| Menor ou igual a    |
| Igual a                 |
| Diferente de             |


### <a name="actions-when-rules-trigger"></a>Ações quando as regras desencadeiam
Quando uma regra de escala automática dispara, o seu conjunto de escala pode automaticamente escalar de uma das seguintes formas:

| Operação à escala     | Caso de utilização                                                                                                                               |
|---------------------|----------------------------------------------------------------------------------------------------------------------------------------|
| Aumentar a contagem em   | Um número fixo de casos vm para criar. Útil em conjuntos de escala com um número menor de VMs.                                           |
| Aumentar por cento por | Um aumento percentual dos casos vm. Bom para conjuntos de maior escala onde um aumento fixo pode não melhorar visivelmente o desempenho. |
| Aumentar a contagem para   | Crie tantas instâncias vm são necessárias para atingir uma quantidade máxima desejada.                                                            |
| Diminuir a contagem em   | Um número fixo de casos vm para remover. Útil em conjuntos de escala com um número menor de VMs.                                           |
| Diminuir por cento por | Uma diminuição percentual dos casos de VM. Bom para conjuntos de maior escala em que um aumento fixo não pode reduzir visivelmente o consumo de recursos e os custos. |
| Diminuir a contagem para   | Remova o número de instâncias vm necessárias para atingir um valor mínimo desejado.                                                            |


## <a name="in-guest-vm-metrics-with-the-azure-diagnostics-extension"></a>Métricas VM convidadas com a extensão de diagnóstico azure
A extensão de diagnóstico azure é um agente que corre dentro de uma instância VM. O agente monitoriza e guarda métricas de desempenho para o armazenamento azure. Estas métricas de desempenho contêm informações mais detalhadas sobre o estado do VM, tais como *AverageReadTime* para discos ou *PercentIdleTime* para CPU. Pode criar regras de escala automática com base numa consciência mais detalhada do desempenho do VM, e não apenas na percentagem de utilização de CPU ou consumo de memória.

Para utilizar a extensão de diagnóstico Azure, deve criar contas de armazenamento Azure para as suas instâncias VM, instalar o agente de diagnóstico Azure e, em seguida, configurar os VMs para transmitir contadores de desempenho específicos para a conta de armazenamento.

Para obter mais informações, veja os artigos para saber como ativar a extensão de diagnóstico do Azure numa [VM do Linux](../virtual-machines/extensions/diagnostics-linux.md) ou numa [VM do Windows](../virtual-machines/extensions/diagnostics-windows.md).


## <a name="application-level-metrics-with-app-insights"></a>Métricas de nível de aplicação com Insights de Aplicações
Para obter mais visibilidade no desempenho das suas aplicações, pode utilizar o Application Insights. Instala um pequeno pacote de instrumentação na sua aplicação que monitoriza a aplicação e envia telemetria para o Azure. Pode monitorizar métricas como os tempos de resposta da sua aplicação, o desempenho da carga da página e a contagem da sessão. Estas métricas de aplicação podem ser usadas para criar regras de escala automática a um nível granular e incorporado à medida que desencadeia regras baseadas em insights acionáveis que possam afetar a experiência do cliente.

Para obter mais informações sobre o App Insights, veja [O que é o Application Insights](../azure-monitor/app/app-insights-overview.md).


## <a name="scheduled-autoscale"></a>Escala automática programada
Também pode criar regras de escala automática com base em horários. Estas regras baseadas em horários permitem-lhe escalar automaticamente o número de instâncias vm em horários fixos. Com regras baseadas no desempenho, pode haver um impacto de desempenho na aplicação antes que as regras de escala automática disparem e as novas instâncias vm são aprovisionadas. Se puder antecipar tal procura, as instâncias vm adicionais são aprovisionadas e prontas para o uso adicional do cliente e procura de aplicação.

Os seguintes exemplos são cenários que podem beneficiar a utilização de regras de escala automática baseadas em horários:

- Escalda automaticamente o número de casos de VM no início do dia de trabalho quando a procura do cliente aumenta. No final do dia de trabalho, escala automaticamente no número de instâncias vm para minimizar os custos de recursos durante a noite quando a utilização da aplicação é baixa.
- Se um departamento utilizar uma aplicação fortemente em certas partes do mês ou ciclo fiscal, escala automaticamente o número de instâncias vm para acomodar as suas exigências adicionais.
- Quando há um evento de marketing, promoção ou venda de férias, você pode automaticamente escalar o número de casos vm antes da procura antecipada do cliente. 


## <a name="next-steps"></a>Passos seguintes
Pode criar regras de escala automática que utilizam métricas baseadas em hospedeiros com uma das seguintes ferramentas:

- [Azure PowerShell](tutorial-autoscale-powershell.md)
- [Azure CLI](tutorial-autoscale-cli.md)
- [Modelo do Azure](tutorial-autoscale-template.md)

Esta visão geral detalha como utilizar regras de escala automática para escalar horizontalmente e aumentar ou diminuir o *número* de instâncias vm no seu conjunto de escala. Também pode escalar verticalmente para aumentar ou diminuir o *tamanho*da instância VM . Para mais informações, consulte a [escala automática vertical com conjuntos de escala](virtual-machine-scale-sets-vertical-scale-reprovision.md)de máquina virtual .

Para obter informações sobre como gerir os seus casos vm, consulte Gerir conjuntos de [escala de máquinas virtuais com o Azure PowerShell](virtual-machine-scale-sets-windows-manage.md).

Para aprender a gerar alertas quando as suas regras de escala automática disparam, consulte Utilizar ações de escala automática para enviar notificações de alerta de [e-mail e webhook no Monitor Azure](../azure-monitor/platform/autoscale-webhook-email.md). Também pode utilizar registos de auditoria para enviar notificações de alerta de [e-mail e webhook no Monitor Azure](../monitoring-and-diagnostics/insights-auditlog-to-webhook-email.md).
