---
title: Resolução de problemas Azure autoscale
description: Rastrear problemas com autoscalagem Azure usados em Tecido de Serviço, Máquinas Virtuais, Aplicações Web e serviços na nuvem.
ms.topic: conceptual
ms.date: 11/4/2019
ms.subservice: autoscale
ms.openlocfilehash: a0fed6c2d06edcb2c9eb8d715feb0ef6c6ade46f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101711404"
---
# <a name="troubleshooting-azure-autoscale"></a>Resolução de problemas Azure autoscale
 
A autoescala do Azure Monitor ajuda-o a ter a quantidade certa de recursos a funcionar para lidar com a carga na sua aplicação. Permite-lhe adicionar recursos para lidar com aumentos de carga e também economizar dinheiro removendo recursos que estão parados. Pode escalar com base num horário, hora de data fixa ou métrica de recursos que escolher. Para obter mais informações, consulte [a Visão Geral de Autoescala](autoscale-overview.md).

O serviço de autoescala fornece-lhe métricas e registos para entender que ações de escala ocorreram e a avaliação das condições que levaram a essas ações. Pode encontrar respostas a questões como:

- Por que o meu serviço foi para fora ou para dentro?
- Por que o meu serviço não escalou?
- Porque é que uma ação de autoescala falhou?
- Porque é que uma ação de escala automática está a demorar algum tempo à escala?
  
## <a name="autoscale-metrics"></a>Métricas de autoescala

A Autoscale fornece-lhe [quatro métricas](../essentials/metrics-supported.md#microsoftinsightsautoscalesettings) para entender o seu funcionamento. 

- **Valor métrico observado** - O valor da métrica que escolheu para tomar a escala em, como visto ou calculado pelo motor de autoescala. Uma vez que uma única definição de autoescala pode ter múltiplas regras e, portanto, múltiplas fontes métricas, pode filtrar usando "fonte métrica" como uma dimensão.
- **Limiar métrico** - O limiar que definiu para tomar a medida de escala. Uma vez que uma única definição de autoescala pode ter múltiplas regras e, portanto, múltiplas fontes métricas, pode filtrar usando a "regra métrica" como uma dimensão.
- **Capacidade observada** - O número ativo de casos do recurso-alvo visto pelo motor Autoscale.
- **Ações de Dimensionamento Iniciadas** – o número de ações de redução horizontal e ampliação iniciadas pelo mecanismo de dimensionamento automático. Pode filtrar por escala vs. escala em ações.

Pode utilizar o [Metrics Explorer](../essentials/metrics-getting-started.md) para traçar as métricas acima, tudo num só local. O gráfico deve mostrar:

  - a métrica real
  - a métrica vista/calculada pelo motor de autoescala
  - o limiar para uma ação de escala
  - a mudança de capacidade 

## <a name="example-1---analyzing-a-simple-autoscale-rule"></a>Exemplo 1 - Analisar uma regra simples de autoescala 

Temos uma definição de autoescala simples para um conjunto de escala de máquina virtual que:

- escala quando a percentagem média de CPU de um conjunto é superior a 70% durante 10 minutos 
- escala quando a percentagem de CPU do conjunto é inferior a 5% por mais de 10 minutos. 

Vamos rever as métricas do serviço de autoescala.
 
![A screenshot mostra um modelo de CPU de percentagem de escala de máquina virtual.](media/autoscale-troubleshoot/autoscale-vmss-CPU-ex-full-1.png)

![Escala de máquina virtual definir percentagem de CPU exemplo](media/autoscale-troubleshoot/autoscale-vmss-CPU-ex-full-2.png)

***Figura 1a - Percentagem métrica do CPU para conjunto de balança de máquina virtual e a métrica de valor métrico observado para definição de autoescala***

![Limiar métrico e capacidade observada](media/autoscale-troubleshoot/autoscale-metric-threshold-capacity-ex-full.png)

***Figura 1b - Limiar métrico e capacidade observada***

Na figura 1b, o **Limiar métrico** (linha azul claro) para a regra de escala é de 70.  A **Capacidade Observada** (linha azul escura) mostra o número de casos ativos, que atualmente é 3. 

> [!NOTE]
> Será necessário filtrar o **Limiar métrico** pela escala de dimensão da regra de regra do gatilho métrico para ver o limiar de escala e pela escala em regra (diminuição). 

## <a name="example-2---advanced-autoscaling-for-a-virtual-machine-scale-set"></a>Exemplo 2 - Autoscalagem avançada para um conjunto de escala de máquina virtual

Dispomos de uma definição de escala automática que permite que um recurso de escala de máquina virtual seja dimensionado com base na sua própria métrica **Outbound Flows**. Note que a opção **de contagem da métrica por exemplo** para o limiar métrico é verificada. 

A regra de ação em escala é: 

Se o valor do Fluxo de **Saída por instância** for superior a 10, então o serviço de autoescala deverá aumentar em 1 instância. 

Neste caso, o valor métrico observado pelo motor de autoescala é calculado como o valor métrico real dividido pelo número de casos. Se o valor métrico observado for inferior ao limiar, não é iniciada qualquer ação de escala. 
 
![A screenshot mostra a página Fluxos de Saída Média com um exemplo de uma escala de máquina virtual definir gráficos de métricas de escala automática.](media/autoscale-troubleshoot/autoscale-vmss-metric-chart-ex-1.png)

![Escala de máquina virtual definir métricas de escala automática exemplo](media/autoscale-troubleshoot/autoscale-vmss-metric-chart-ex-2.png)

***Figura 2 - Escala de máquina virtual definir métricas de escala automática exemplo***

Na figura 2, pode-se ver dois gráficos métricos. 

O gráfico no topo mostra o valor real da métrica **Outbound Flows.** O valor real é 6. 

O gráfico na parte inferior mostra alguns valores. 
 - O **valor métrico observado** (azul claro) é 3 porque existem 2 instâncias ativas e 6 divididos por 2 é 3. 
 - A **Capacidade Observada** (púrpura) mostra a contagem de exemplos vista pelo motor de autoescala. 
 - O **Limiar métrico** (verde claro) está definido para 10. 

Se existirem regras de ação de várias escalas, pode utilizar a divisão ou a opção **de filtro de adicionar** na tabela de exploradores métricas para olhar a métrica por uma fonte ou regra específica. Para obter mais informações sobre a divisão de um gráfico métrico, consulte [funcionalidades avançadas de gráficos métricos - divisão](../essentials/metrics-charts.md#apply-splitting)

## <a name="example-3---understanding-autoscale-events"></a>Exemplo 3 - Compreender eventos de autoescala

No ecrã de definição de escala automática, vá ao separador **história do Run** para ver as ações de escala mais recentes. O separador também mostra a mudança na **Capacidade Observada** ao longo do tempo. Para obter mais detalhes sobre todas as ações de autoescala, incluindo operações como configurações de escala automática/atualização, consulte o registo de atividade e o filtro por operações de autoescala.

![As definições de autoescala executam o histórico](media/autoscale-troubleshoot/autoscale-setting-run-history-smaller.png)

## <a name="autoscale-resource-logs"></a>Registos de recursos de autoescalação

Tal como qualquer outro recurso Azure, o serviço de autoescala fornece [registos de recursos](../essentials/platform-logs-overview.md). Há duas categorias de registos.

- **Avaliações de autoescala** - O motor de escala automática regista as entradas de registo para cada avaliação de cada condição sempre que faz uma verificação.  A rubrica inclui detalhes sobre os valores observados das métricas, as regras avaliadas, e se a avaliação resultou numa ação de escala ou não.

- **Ações de escala automática** - O motor regista eventos de ação de escala iniciados pelo serviço de autoescala e os resultados dessas ações de escala (sucesso, falha e quanta escala ocorreu como visto pelo serviço de autoescala).

Tal como acontece com qualquer serviço suportado pelo Azure Monitor, pode utilizar [Definições de Diagnóstico](../essentials/diagnostic-settings.md) para encaminhar estes registos:

- ao seu espaço de trabalho Log Analytics para análise detalhada
- para Os Centros de Eventos e, em seguida, para ferramentas não-Azure
- para a sua conta de armazenamento Azure para arquivo  

![Definições de diagnóstico de escala automática](media/autoscale-troubleshoot/diagnostic-settings.png)

A imagem anterior mostra as definições de diagnóstico de escala automática do portal Azure. Lá pode selecionar o separador Registos de Diagnóstico/Recursos e ativar a recolha e encaminhamento de registos. Também pode executar a mesma ação utilizando os modelos REST API, CLI, PowerShell, Resource Manager para Definições de Diagnóstico, escolhendo o tipo de recurso como *Microsoft.Insights/AutoscaleSettings*. 

## <a name="troubleshooting-using-autoscale-logs"></a>Resolução de problemas utilizando registos de escala automática 

Para uma melhor experiência de resolução de problemas, recomendamos que os seus registos de encaminhamento para Registos do Monitor Azure (Log Analytics) através de um espaço de trabalho quando criar a definição de escala automática. Este processo é mostrado na imagem na secção anterior. Pode validar melhor as avaliações e dimensionar as ações utilizando o Log Analytics.

Uma vez configurados os seus registos de escala automática para serem enviados para o espaço de trabalho Do Log Analytics, pode executar as seguintes consultas para verificar os registos. 

Para começar, experimente esta consulta para ver os registos de avaliação de autoescala mais recentes:

```Kusto
AutoscaleEvaluationsLog
| limit 50
```

Ou experimente a seguinte consulta para ver os registos de ação de escala mais recentes:

```Kusto
AutoscaleScaleActionsLog
| limit 50
```

Utilize as seguintes secções para estas perguntas. 

## <a name="a-scale-action-occurred-that-i-didnt-expect"></a>Ocorreu uma ação de escala que eu não esperava

Primeiro execute a consulta para uma ação de escala para encontrar a ação de escala que você está interessado. Se for a última ação à escala, utilize a seguinte consulta:

```Kusto
AutoscaleScaleActionsLog
| take 1
```

Selecione o campo CorrelationId a partir do registo de ações de escala. Use o CorrelationId para encontrar o registo de avaliação certo. A execução da consulta abaixo apresentará todas as regras e condições avaliadas que conduzam a essa ação de escala.

```Kusto
AutoscaleEvaluationsLog
| where CorrelationId = "<correliationId>"
```

## <a name="what-profile-caused-a-scale-action"></a>Que perfil causou uma ação em escala?

Ocorreu uma ação de escalada, mas tem regras e perfis sobrepostos e precisa de localizar o que causou a ação. 

Encontre a correlaid da ação de escala (como explicado no exemplo 1) e, em seguida, execute a consulta nos registos de avaliação para saber mais sobre o perfil.

```Kusto
AutoscaleEvaluationsLog
| where CorrelationId = "<correliationId_Guid>"
| where ProfileSelected == true
| project ProfileEvaluationTime, Profile, ProfileSelected, EvaluationResult
```

Toda a avaliação de perfil também pode ser entendida melhor usando a seguinte consulta

```Kusto
AutoscaleEvaluationsLog
| where TimeGenerated > ago(2h)
| where OperationName contains == "profileEvaluation"
| project OperationName, Profile, ProfileEvaluationTime, ProfileSelected, EvaluationResult
```

## <a name="a-scale-action-did-not-occur"></a>Uma ação de escala não ocorreu

Esperava uma ação em escala e não ocorreu. Pode não haver eventos de ação em escala ou registos.

Reveja as métricas de autoescala se estiver a utilizar uma regra de escala baseada em métrica. É possível que o **valor métrico observado** ou **a capacidade observada** não sejam o que se esperava que fossem e, portanto, a regra da escala não disparou. Continuaria a ver avaliações, mas não uma regra de escala. Também é possível que o tempo de arrefecimento impedisse que ocorresse uma ação de escala. 

 Reveja os registos de avaliação de escala automática durante o período de tempo que esperava que a ação de escala ocorresse. Reveja todas as avaliações que fez e por que decidiu não desencadear uma ação de escala.


```Kusto
AutoscaleEvaluationsLog
| where TimeGenerated > ago(2h)
| where OperationName == "MetricEvaluation" or OperationName == "ScaleRuleEvaluation"
| project OperationName, MetricData, ObservedValue, Threshold, EstimateScaleResult
```

## <a name="scale-action-failed"></a>A ação em escala falhou

Pode haver um caso em que o serviço de autoescala tomou a medida da escala, mas o sistema decidiu não escalar ou não ter concluído a ação de escala. Utilize esta consulta para encontrar as ações de escala falhadas.

```Kusto
AutoscaleScaleActionsLog
| where ResultType == "Failed"
| project ResultDescription
```

Crie regras de alerta para ser notificado de ações ou falhas de autoescala. Também pode criar regras de alerta para ser notificado em eventos de autoescala.

## <a name="schema-of-autoscale-resource-logs"></a>Esquema de registos de recursos de autoescalação

Para obter mais informações, consulte [registos de recursos de escala automática](autoscale-resource-log-schema.md)

## <a name="next-steps"></a>Passos seguintes
Leia informações sobre [as melhores práticas de autoescala.](autoscale-best-practices.md)