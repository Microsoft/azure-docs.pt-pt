---
title: Descrição geral do dimensionamento automático com o conjunto de dimensionamento de máquinas virtuais do Azure
description: Saiba mais sobre as diferentes formas de escalar automaticamente uma balança de máquina virtual Azure definida com base no desempenho ou num horário fixo
author: avirishuv
ms.author: avverma
ms.topic: overview
ms.service: virtual-machine-scale-sets
ms.subservice: autoscale
ms.date: 06/30/2020
ms.reviewer: jushiman
ms.custom: avverma
ms.openlocfilehash: dbd2d6dc3034ff64ef6b5d4d36d836ce8462b154
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100591394"
---
# <a name="overview-of-autoscale-with-azure-virtual-machine-scale-sets"></a>Descrição geral do dimensionamento automático com o conjunto de dimensionamento de máquinas virtuais do Azure
Um conjunto de escala de máquina virtual Azure pode aumentar ou diminuir automaticamente o número de instâncias VM que executam a sua aplicação. Este comportamento automatizado e elástico reduz a sobrecarga de gestão para monitorizar e otimizar o desempenho da sua aplicação. Cria regras que definem o desempenho aceitável para uma experiência positiva do cliente. Quando esses limiares definidos são cumpridos, as regras de autoescala tomam medidas para ajustar a capacidade do seu conjunto de escala. Também pode agendar eventos para aumentar ou diminuir automaticamente a capacidade da sua escala definida em horários fixos. Este artigo fornece uma visão geral das métricas de desempenho disponíveis e quais as ações que a autoescala pode realizar.


## <a name="benefits-of-autoscale"></a>Benefícios da autoescala
Se a exigência da aplicação aumentar, a carga sobre as instâncias de VM no conjunto de dimensionamento também aumenta. Se este aumento de carga for consistente, em vez de ser apenas uma breve exigência, pode configurar regras de dimensionamento automático para aumentar o número de instâncias de VM no conjunto de dimensionamento.

> [!NOTE]
> Ao utilizar reparações automáticas para o seu conjunto de escala, o número máximo de casos na balança definida pode ser de 200. Saiba mais sobre [reparações de instâncias automáticas.](./virtual-machine-scale-sets-automatic-instance-repairs.md)

Quando estas instâncias de VM forem criadas e as aplicações forem implementadas, o conjunto de dimensionamento começa a distribuir o tráfego pelas mesmas através do balanceador de carga. Você controla que métricas monitorizar, como CPU ou memória, quanto tempo a carga da aplicação deve cumprir um determinado limiar, e quantas instâncias VM para adicionar ao conjunto de escala.

À noite ou ao fim de semana, a exigência da aplicação pode diminuir. Se esta diminuição de carga for consistente durante um certo período de tempo, pode configurar regras de dimensionamento automático para diminuir o número de instâncias de VM no conjunto de dimensionamento. Esta ação de dimensionamento para reduzir horizontalmente reduz o custo de execução do conjunto de dimensionamento, uma vez que apenas executa o número de instâncias necessário para satisfazer a exigência atual.


## <a name="use-host-based-metrics"></a>Use métricas baseadas em hospedeiros
Pode criar regras de autoescala que as métricas de anfitrião incorporadas estão disponíveis nas suas instâncias VM. As métricas do anfitrião dão-lhe visibilidade para o desempenho das instâncias VM num conjunto de escala sem a necessidade de instalar ou configurar agentes adicionais e recolhas de dados. As regras de autoescalação que utilizam estas métricas podem escalar ou no número de instâncias VM em resposta ao uso do CPU, procura de memória ou acesso ao disco.

Podem ser criadas regras de dimensionamento automático que utilizem métricas baseadas no anfitrião com uma das seguintes ferramentas:

- [Portal do Azure](virtual-machine-scale-sets-autoscale-portal.md)
- [Azure PowerShell](tutorial-autoscale-powershell.md)
- [CLI do Azure](tutorial-autoscale-cli.md)
- [Modelo do Azure](tutorial-autoscale-template.md)

Para criar regras de autoescalação que utilizem métricas de desempenho mais detalhadas, pode [instalar e configurar a extensão de diagnóstico Azure](#in-guest-vm-metrics-with-the-azure-diagnostics-extension) em instâncias VM, ou [configurar a sua aplicação utilizar app Insights](#application-level-metrics-with-app-insights).

As regras de autoescalação que utilizam métricas baseadas no hospedeiro, métricas de VM no hóspede com a extensão de diagnóstico Azure, e App Insights podem usar as seguintes definições de configuração.

### <a name="metric-sources"></a>Fontes métricas
As regras de autoescala podem utilizar métricas a partir de uma das seguintes fontes:

| Origem métrica        | Caso de utilização                                                                                                                     |
|----------------------|------------------------------------------------------------------------------------------------------------------------------|
| Conjunto de escala de corrente    | Para métricas baseadas no hospedeiro que não requerem agentes adicionais para serem instalados ou configurados.                                  |
| Conta de armazenamento      | A extensão de diagnóstico Azure escreve métricas de desempenho para o armazenamento Azure que é então consumido para desencadear regras de autoescala. |
| Fila do Service Bus    | A sua aplicação ou outros componentes podem transmitir mensagens numa fila do Azure Service Bus para desencadear regras.                   |
| Application Insights | Um pacote de instrumentação instalado na sua aplicação que transmite métricas diretamente da aplicação.                         |


### <a name="autoscale-rule-criteria"></a>Critérios de regra de autoescala
As seguintes métricas baseadas no hospedeiro estão disponíveis para utilização quando criar regras de autoescala. Se utilizar a extensão de diagnóstico Azure ou app Insights, define quais as métricas a monitorizar e a utilizar com regras de autoescala.

| Nome da métrica               |
|---------------------------|
| Percentagem da CPU            |
| Entrada na Rede                |
| Saída da Rede               |
| Bytes de leitura de disco           |
| Bytes de escrita de disco          |
| Operações de leitura de disco/sec  |
| Operações de escrita de discos/seg |
| Créditos CPU Remanescentes     |
| Créditos CPU Consumidos      |

Quando cria regras de autoescalação para monitorizar uma determinada métrica, as regras analisam uma das seguintes ações de agregação de métricas:

| Tipo de agregação |
|------------------|
| Média          |
| Mínimo          |
| Máximo          |
| Total            |
| Último             |
| de palavras            |

As regras de autoescala são então ativadas quando as métricas são comparadas com o seu limiar definido com um dos seguintes operadores:

| Operador                 |
|--------------------------|
| Maior que             |
| Maior ou igual a |
| Menor que                |
| Menor ou igual a    |
| Igual a                 |
| Diferente de             |


### <a name="actions-when-rules-trigger"></a>Ações quando as regras disparam
Quando uma regra de autoescala dispara, o seu conjunto de escala pode escalar automaticamente de uma das seguintes formas:

| Operação à escala     | Caso de utilização                                                                                                                               |
|---------------------|----------------------------------------------------------------------------------------------------------------------------------------|
| Aumentar a contagem em   | Um número fixo de instâncias VM para criar. Útil em conjuntos de escala com um número menor de VMs.                                           |
| Aumentar por cento em | Um aumento percentual de casos de VM. Bom para conjuntos de escala maior onde um aumento fixo pode não melhorar visivelmente o desempenho. |
| Aumentar a contagem para   | Criar o máximo de instâncias VM necessário para atingir um valor máximo desejado.                                                            |
| Diminuir a contagem em   | Um número fixo de instâncias VM para remover. Útil em conjuntos de escala com um número menor de VMs.                                           |
| Diminuir por cento em | Uma diminuição percentual dos casos de VM. Bom para conjuntos de escala maior onde um aumento fixo pode não reduzir visivelmente o consumo de recursos e os custos. |
| Diminuição da contagem para   | Remova o número de instâncias VM necessárias para atingir um valor mínimo desejado.                                                            |


## <a name="in-guest-vm-metrics-with-the-azure-diagnostics-extension"></a>Métricas VM in-guest com a extensão de diagnóstico Azure
A extensão de diagnóstico Azure é um agente que corre dentro de uma instância VM. O agente monitoriza e guarda as métricas de desempenho para o armazenamento do Azure. Estas métricas de desempenho contêm informações mais detalhadas sobre o estado do VM, tais como *AverageReadTime* para discos ou *PercentIdleTime* para CPU. Pode criar regras de autoescala baseadas numa consciência mais detalhada do desempenho do VM, e não apenas na percentagem de utilização de CPU ou consumo de memória.

Para utilizar a extensão de diagnóstico Azure, tem de criar contas de armazenamento Azure para as suas instâncias VM, instalar o agente de diagnóstico Azure e, em seguida, configurar os VMs para transmitir contadores de desempenho específicos para a conta de armazenamento.

Para obter mais informações, veja os artigos para saber como ativar a extensão de diagnóstico do Azure numa [VM do Linux](../virtual-machines/extensions/diagnostics-linux.md) ou numa [VM do Windows](../virtual-machines/extensions/diagnostics-windows.md).


## <a name="application-level-metrics-with-app-insights"></a>Métricas de nível de aplicação com Insights de Aplicações
Para ganhar mais visibilidade no desempenho das suas aplicações, pode utilizar o Application Insights. Instala um pequeno pacote de instrumentação na sua aplicação que monitoriza a aplicação e envia telemetria para a Azure. Pode monitorizar métricas como os tempos de resposta da sua aplicação, o desempenho da carga da página e a contagem da sessão. Estas métricas de aplicação podem ser usadas para criar regras de autoescala a um nível granular e incorporado à medida que desencadeia regras baseadas em insights acionáveis que podem afetar a experiência do cliente.

Para obter mais informações sobre o App Insights, veja [O que é o Application Insights](../azure-monitor/app/app-insights-overview.md).


## <a name="scheduled-autoscale"></a>Autoescala programada
Também pode criar regras de autoescala com base em horários. Estas regras baseadas em horários permitem-lhe escalar automaticamente o número de casos de VM em horários fixos. Com as regras baseadas no desempenho, pode haver um impacto de desempenho na aplicação antes do desencadeamento das regras de autoescala e das novas instâncias VM. Se puder antecipar tal procura, as instâncias VM adicionais são a provisionadas e prontas para o uso adicional do cliente e procura de aplicação.

Os exemplos a seguir são cenários que podem beneficiar a utilização de regras de autoescala baseadas em horários:

- Aumente automaticamente o número de casos de VM no início do dia de trabalho quando a procura do cliente aumenta. No final do dia de trabalho, dimensione automaticamente o número de instâncias VM para minimizar os custos dos recursos durante a noite quando a utilização da aplicação é baixa.
- Se um departamento utilizar uma aplicação fortemente em certas partes do mês ou ciclo fiscal, dimensione automaticamente o número de instâncias VM para acomodar as suas exigências adicionais.
- Quando há um evento de marketing, promoção ou venda de férias, você pode automaticamente escalar o número de instâncias VM antes da procura antecipada do cliente. 


## <a name="next-steps"></a>Passos seguintes
Pode criar regras de autoescala que utilizem métricas baseadas em hospedeiros com uma das seguintes ferramentas:

- [Azure PowerShell](tutorial-autoscale-powershell.md)
- [CLI do Azure](tutorial-autoscale-cli.md)
- [Modelo do Azure](tutorial-autoscale-template.md)

Esta visão geral detalha como utilizar regras de autoescala para escalar horizontalmente e aumentar ou diminuir o *número* de instâncias VM no seu conjunto de escala. Também pode escalar verticalmente para aumentar ou diminuir o *tamanho* da instância VM . Para obter mais informações, consulte [autoescala vertical com conjuntos de escala de máquina virtual](virtual-machine-scale-sets-vertical-scale-reprovision.md).

Para obter informações sobre como gerir as suas instâncias em VM, consulte [Gerir conjuntos de balanças de máquinas virtuais com Azure PowerShell](./virtual-machine-scale-sets-manage-powershell.md).

Para aprender a gerar alertas quando as suas regras de autoescalação disparam, consulte [utilizar ações de autoescala para enviar notificações de alerta de email e webhook no Azure Monitor](../azure-monitor/autoscale/autoscale-webhook-email.md). Também pode [utilizar registos de auditoria para enviar notificações de alerta de email e webhook no Azure Monitor](../azure-monitor/alerts/alerts-log-webhook.md).
