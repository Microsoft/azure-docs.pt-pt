---
title: Resolução de problemas à escala automática azure
description: Rastrear problemas com a autoscalcificação Azure usado em Tecido de Serviço, Máquinas Virtuais, Web Apps e serviços na nuvem.
ms.topic: conceptual
ms.date: 11/4/2019
ms.subservice: autoscale
ms.openlocfilehash: 9780cf88070110c4efc13c477d65307aa3985fe5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75751331"
---
# <a name="troubleshooting-azure-autoscale"></a>Resolução de problemas à escala automática azure
 
A escala automática Do Azure Monitor ajuda-o a ter a quantidade certa de recursos a funcionar para lidar com a carga na sua aplicação. Permite-lhe adicionar recursos para lidar com aumentos de carga e também economizar dinheiro removendo recursos que estão parados. Pode escalar com base numa data de agenda, data fixa ou métrica de recursos que escolher. Para mais informações, consulte a [Visão Geral da Escala Automática](autoscale-overview.md).

O serviço de escala automática fornece métricas e registos para entender que ações de escala ocorreram e a avaliação das condições que levaram a essas ações. Pode encontrar respostas a questões como:

- Por que o meu serviço fez escala para fora ou dentro?
- Por que o meu serviço não escalou?
- Porque é que uma ação à escala automática falhou?
- Porque é que uma ação à escala automática está a demorar algum tempo?
  
## <a name="autoscale-metrics"></a>Métricas de escala automática

A escala automática fornece-lhe [quatro métricas](metrics-supported.md#microsoftinsightsautoscalesettings) para compreender o seu funcionamento. 

- **Valor Métrico Observado** - O valor da métrica que escolheu para tomar a ação de escala, como visto ou calculado pelo motor de escala automática. Como uma única definição de escala automática pode ter múltiplas regras e, portanto, múltiplas fontes métricas, pode filtrar usando a "fonte métrica" como dimensão.
- **Limite métrico** - O limiar que definiu para tomar a medida de escala. Como uma única definição de escala automática pode ter múltiplas regras e, portanto, múltiplas fontes métricas, pode filtrar usando a "regra métrica" como dimensão.
- **Capacidade Observada** - O número ativo de instâncias do recurso-alvo vista pelo motor autoescala.
- **Ações de Dimensionamento Iniciadas** – o número de ações de redução horizontal e ampliação iniciadas pelo mecanismo de dimensionamento automático. Pode filtrar por escala vs. escala em ações.

Pode utilizar o [Metrics Explorer](metrics-getting-started.md) para traçar as métricas acima num só local. O gráfico deve mostrar:

  - a métrica real
  - a métrica como visto/computado por motor de escala automática
  - o limiar para uma ação de escala
  - a mudança de capacidade 

## <a name="example-1---analyzing-a-simple-autoscale-rule"></a>Exemplo 1 - Analisar uma regra simples de escala automática 

Temos uma configuração de escala automática simples para um conjunto de escala de máquina virtual que:

- escalas para fora quando a percentagem média de CPU de um conjunto é superior a 70% durante 10 minutos 
- escalas quando a percentagem de CPU do conjunto é inferior a 5% por mais de 10 minutos. 

Vamos rever as métricas do serviço de escala automática.
 
![Exemplo de CPU de escala de máquina virtual](media/autoscale-troubleshoot/autoscale-vmss-CPU-ex-full-1.png)

![Exemplo de CPU de escala de máquina virtual](media/autoscale-troubleshoot/autoscale-vmss-CPU-ex-full-2.png)

***Figura 1a - Métrica percentual do CPU para conjunto de escala de máquina virtual e a métrica de valor métrico observado para a definição de escala automática***

![Limiar Métrico e Capacidade Observada](media/autoscale-troubleshoot/autoscale-metric-threshold-capacity-ex-full.png)

***Figura 1b - Limiar Métrico e Capacidade Observada***

Na figura 1b, o **Limiar Métrico** (linha azul clara) para a regra de saída é de 70.  A **Capacidade Observada** (linha azul escura) mostra o número de instâncias ativas, que é atualmente 3. 

> [!NOTE]
> Terá de filtrar o **Limiar Métrico** pela escala métrica da escala de dimensão do gatilho (aumento) para ver o limiar de escala para fora e pela escala em regra (diminuição). 

## <a name="example-2---advanced-autoscaling-for-a-virtual-machine-scale-set"></a>Exemplo 2 - Autoscalcificação avançada para um conjunto de escala de máquina virtual

Temos uma definição de escala automática que permite que um recurso conjunto de escala de máquina virtual se esforce com base na sua própria métrica **Fluxos de Saída**. Note que a opção de contagem de **divisões por exemplo** para o limiar métrico é verificada. 

A regra de ação em escala é: 

Se o valor do Fluxo de **Saída por instância** for superior a 10, então o serviço de escala automática deve ser dimensionado em 1 instância. 

Neste caso, o valor métrico observado pelo motor de escala automática é calculado como o valor métrico real dividido pelo número de instâncias. Se o valor métrico observado for inferior ao limiar, não é iniciada qualquer ação de escala para fora. 
 
![Exemplo de métricas de escala automática de escala de máquina virtual](media/autoscale-troubleshoot/autoscale-vmss-metric-chart-ex-1.png)

![Exemplo de métricas de escala automática de escala de máquina virtual](media/autoscale-troubleshoot/autoscale-vmss-metric-chart-ex-2.png)

***Figura 2 - Escala de máquina virtual definida métricas de escala automática exemplo***

Na figura 2, pode-se ver dois gráficos métricos. 

O gráfico em cima mostra o valor real da métrica de **Fluxos de Saída.** O valor real é 6. 

O gráfico na parte inferior mostra alguns valores. 
 - O **valor métrico observado** (azul claro) é 3 porque existem 2 instâncias ativas e 6 divididos por 2 é 3. 
 - A **Capacidade Observada** (roxo) mostra a contagem de exemplos vista pelo motor de escala automática. 
 - O **Limiar Métrico** (verde claro) está definido para 10. 

Se existirem regras de ação em escala múltipla, pode utilizar a divisão ou a opção **de filtro de adição** no gráfico do explorador de Métricas para olhar a métrica por uma fonte ou regra específica. Para obter mais informações sobre a divisão de um gráfico métrico, consulte [características avançadas de gráficos métricos - divisão](metrics-charts.md#apply-splitting-to-a-chart)

## <a name="example-3---understanding-autoscale-events"></a>Exemplo 3 - Compreender eventos de escala automática

No ecrã de definição de escala automática, vá ao separador **histórico Run** para ver as ações de escala mais recentes. O separador também mostra a alteração da **Capacidade Observada** ao longo do tempo. Para obter mais detalhes sobre todas as ações de escala automática, incluindo operações como atualizar/eliminar configurações de escala automática, ver o registo de atividade e o filtro por operações de escala automática.

![Configurações de escala automática executar histórico](media/autoscale-troubleshoot/autoscale-setting-run-history-smaller.png)

## <a name="autoscale-resource-logs"></a>Registos de recursos de escala automática

Tal como qualquer outro recurso Azure, o serviço de escala automática fornece registos de [recursos.](platform-logs-overview.md) Há duas categorias de registos.

- **Avaliações de escala automática** - O motor de escala automática regista entradas de registo para cada avaliação de condição cada vez que faz uma verificação.  A entrada inclui detalhes sobre os valores observados das métricas, as regras avaliadas, e se a avaliação resultou ou não numa ação de escala.

- **Ações** de escala automática - Os registos de escala do motor eventos de ação iniciados por serviço de escala automática e os resultados dessas ações de escala (sucesso, falha e quanta escala ocorreu como visto pelo serviço de escala automática).

Tal como acontece com qualquer serviço suportado pelo Monitor Azure, pode utilizar [as Definições](diagnostic-settings.md) de Diagnóstico para encaminhar estes registos:

- ao seu espaço de trabalho Log Analytics para análise detalhada
- para Centros de Eventos e, em seguida, para ferramentas não-Azure
- para a sua conta de armazenamento Azure para arquivo  

![Definições de diagnóstico de escala automática](media/autoscale-troubleshoot/diagnostic-settings.png)

A imagem anterior mostra as definições de diagnóstico de escala automática do portal Azure. Aí pode selecionar o separador Registos de Diagnóstico/Recursos e ativar a recolha e encaminhamento de registos. Também pode executar a mesma ação utilizando modelos REST API, CLI, PowerShell, Resource Manager para Definições de Diagnóstico, escolhendo o tipo de recurso como *Microsoft.Insights/AutoscaleSettings*. 

## <a name="troubleshooting-using-autoscale-logs"></a>Resolução de problemas utilizando registos de escala automática 

Para uma melhor experiência de resolução de problemas, recomendamos que envie os seus registos para o Azure Monitor Logs (Log Analytics) através de um espaço de trabalho quando criar a definição de escala automática. Este processo é mostrado na imagem na secção anterior. Pode validar melhor as avaliações e as ações de escala utilizando o Log Analytics.

Depois de configurar os seus registos de escala automática a enviar para o espaço de trabalho do Log Analytics, pode executar as seguintes consultas para verificar os registos. 

Para começar, experimente esta consulta para ver os mais recentes registos de avaliação de escala automática:

```Kusto
AutoscaleEvaluationsLog
| limit 50
```

Ou experimente a seguinte consulta para ver os registos de ação em escala mais recentes:

```Kusto
AutoscaleScaleActionsLog
| limit 50
```

Utilize as seguintes secções nestas questões. 

## <a name="a-scale-action-occurred-that-i-didnt-expect"></a>Ocorreu uma ação à escala que eu não esperava

Primeiro execute a consulta para ação em escala para encontrar a ação de escala que lhe interessa. Se for a última ação em escala, utilize a seguinte consulta:

```Kusto
AutoscaleScaleActionsLog
| take 1
```

Selecione o campo CorrelationId a partir do registo de ações de escala. Utilize o CorrelationId para encontrar o registo de avaliação certo. A execução da consulta abaixo apresentará todas as regras e condições avaliadas que conduzam a essa ação de escala.

```Kusto
AutoscaleEvaluationsLog
| where CorrelationId = "<correliationId>"
```

## <a name="what-profile-caused-a-scale-action"></a>Que perfil causou uma ação à escala?

Ocorreu uma ação em escala, mas tem regras e perfis sobrepostos e é preciso localizar o que causou a ação. 

Encontre a correlação Id da ação de escala (como explicado no exemplo 1) e, em seguida, execute a consulta nos registos de avaliação para saber mais sobre o perfil.

```Kusto
AutoscaleEvaluationsLog
| where CorrelationId = "<correliationId_Guid>"
| where ProfileSelected == true
| project ProfileEvaluationTime, Profile, ProfileSelected, EvaluationResult
```

Toda a avaliação do perfil também pode ser melhor entendida usando a seguinte consulta

```Kusto
AutoscaleEvaluationsLog
| where TimeGenerated > ago(2h)
| where OperationName contains == "profileEvaluation"
| project OperationName, Profile, ProfileEvaluationTime, ProfileSelected, EvaluationResult
```

## <a name="a-scale-action-did-not-occur"></a>Uma ação de escala não ocorreu

Esperava uma ação em escala e não aconteceu. Pode não haver eventos de ação em escala ou registos.

Reveja as métricas de escala automática se estiver a utilizar uma regra de escala métrica. É possível que o **valor métrico observado** ou a capacidade **observada** não sejam o que se esperava que fossem e, portanto, a regra da escala não disparou. Ainda veria avaliações, mas não uma regra de escala. Também é possível que o tempo de arrefecimento impedisse que uma ação de escala ocorresse. 

 Reveja os registos de avaliação de escala automática durante o período de tempo que esperava que a ação de escala ocorresse. Reveja todas as avaliações que fez e por que decidiu não desencadear uma ação em escala.


```Kusto
AutoscaleEvaluationsLog
| where TimeGenerated > ago(2h)
| where OperationName == "MetricEvaluation" or OperationName == "ScaleRuleEvaluation"
| project OperationName, MetricData, ObservedValue, Threshold, EstimateScaleResult
```

## <a name="scale-action-failed"></a>A ação de escala falhou

Pode haver um caso em que o serviço de escala automática tomou a medida de escala, mas o sistema decidiu não escalar ou não completar a ação de escala. Use esta consulta para encontrar as ações de escala falhadas.

```Kusto
AutoscaleScaleActionsLog
| where ResultType == "Failed"
| project ResultDescription
```

Crie regras de alerta para ser notificado de ações ou falhas de escala automática. Também pode criar regras de alerta para ser notificado em eventos de escala automática.

## <a name="schema-of-autoscale-resource-logs"></a>Esquema de registos de recursos de escala automática

Para mais informações, consulte [registos](autoscale-resource-log-schema.md) de recursos de escala automática

## <a name="next-steps"></a>Passos seguintes
Leia informações sobre [as melhores práticas de escala automática.](autoscale-best-practices.md) 
