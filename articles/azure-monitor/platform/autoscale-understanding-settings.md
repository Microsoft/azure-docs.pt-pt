---
title: Noções básicas sobre configurações de dimensionamento automático no Azure Monitor
description: Uma análise detalhada das configurações de dimensionamento automático e como elas funcionam. Aplica-se a máquinas virtuais, serviços de nuvem, aplicativos Web
ms.topic: conceptual
ms.date: 12/18/2017
ms.subservice: autoscale
ms.openlocfilehash: 9a2b94208de7ce490a0e7acfbb71175b4a7c846e
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75364310"
---
# <a name="understand-autoscale-settings"></a>Compreender as definições de Dimensionamento Automático
As configurações de dimensionamento automático ajudam a garantir que você tenha a quantidade certa de recursos em execução para lidar com a carga flutuante do seu aplicativo. Você pode definir as configurações de AutoEscala para serem disparadas com base em métricas que indicam carga ou desempenho, ou disparadas em uma data e hora agendadas. Este artigo faz uma visão detalhada da anatomia de uma configuração de dimensionamento automático. O artigo começa com o esquema e as propriedades de uma configuração e percorre os diferentes tipos de perfil que podem ser configurados. Por fim, o artigo discute como o recurso de dimensionamento automático no Azure avalia qual perfil deve ser executado em um determinado momento.

## <a name="autoscale-setting-schema"></a>Esquema de configuração de dimensionamento automático
Para ilustrar o esquema de configuração de dimensionamento automático, a seguinte configuração de dimensionamento automático é usada. É importante observar que essa configuração de dimensionamento automático tem:
- Um perfil. 
- Duas regras de métrica neste perfil: uma para expansão e outra para reduzir horizontalmente.
  - A regra de expansão é disparada quando a métrica média da CPU do conjunto de dimensionamento de máquinas virtuais é maior que 85% nos últimos 10 minutos.
  - A regra de redução horizontal é disparada quando a média do conjunto de dimensionamento de máquinas virtuais é menor que 60% para o último minuto.

> [!NOTE]
> Uma configuração pode ter vários perfis. Para saber mais, consulte a seção de [perfis](#autoscale-profiles) . Um perfil também pode ter várias regras de expansão e regras de redução horizontal definidas. Para ver como eles são avaliados, consulte a seção [avaliação](#autoscale-evaluation) .

```JSON
{
  "id": "/subscriptions/s1/resourceGroups/rg1/providers/microsoft.insights/autoscalesettings/setting1",
  "name": "setting1",
  "type": "Microsoft.Insights/autoscaleSettings",
  "location": "East US",
  "properties": {
    "enabled": true,
    "targetResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.Compute/virtualMachineScaleSets/vmss1",
    "profiles": [
      {
        "name": "mainProfile",
        "capacity": {
          "minimum": "1",
          "maximum": "4",
          "default": "1"
        },
        "rules": [
          {
            "metricTrigger": {
              "metricName": "Percentage CPU",
              "metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.Compute/virtualMachineScaleSets/vmss1",
              "timeGrain": "PT1M",
              "statistic": "Average",
              "timeWindow": "PT10M",
              "timeAggregation": "Average",
              "operator": "GreaterThan",
              "threshold": 85
            },
            "scaleAction": {
              "direction": "Increase",
              "type": "ChangeCount",
              "value": "1",
              "cooldown": "PT5M"
            }
          },
    {
            "metricTrigger": {
              "metricName": "Percentage CPU",
              "metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.Compute/virtualMachineScaleSets/vmss1",
              "timeGrain": "PT1M",
              "statistic": "Average",
              "timeWindow": "PT10M",
              "timeAggregation": "Average",
              "operator": "LessThan",
              "threshold": 60
            },
            "scaleAction": {
              "direction": "Decrease",
              "type": "ChangeCount",
              "value": "1",
              "cooldown": "PT5M"
            }
          }
        ]
      }
    ]
  }
}
```

| Section | Nome do elemento | Descrição |
| --- | --- | --- |
| Definição | ID | A ID de recurso da configuração de dimensionamento automático. As configurações de autoescala são um recurso Azure Resource Manager. |
| Definição | nome | O nome da configuração de dimensionamento automático. |
| Definição | localização | O local da configuração de dimensionamento automático. Esse local pode ser diferente do local do recurso que está sendo dimensionado. |
| propriedades | targetResourceUri | A ID de recurso do recurso que está sendo dimensionado. Você só pode ter uma configuração de dimensionamento automático por recurso. |
| propriedades | profiles | Uma configuração de dimensionamento automático é composta de um ou mais perfis. Cada vez que o mecanismo de dimensionamento automático é executado, ele executa um perfil. |
| profile | nome | O nome do perfil. Você pode escolher qualquer nome que ajude a identificar o perfil. |
| profile | Capacidade. máxima | A capacidade máxima permitida. Ele garante que o dimensionamento automático, ao executar esse perfil, não dimensione o recurso acima desse número. |
| profile | Capacidade. mínimo | A capacidade mínima permitida. Ele garante que o dimensionamento automático, ao executar este perfil, não dimensione o recurso abaixo desse número. |
| profile | Capacity.default | Se houver um problema ao ler a métrica de recurso (nesse caso, a CPU de "vmss1") e a capacidade atual estiver abaixo do padrão, o dimensionamento automático escalará horizontalmente para o padrão. Isso é para garantir a disponibilidade do recurso. Se a capacidade atual já for maior que a capacidade padrão, o dimensionamento automático não será dimensionado horizontalmente. |
| profile | regras | O dimensionamento automático é dimensionado automaticamente entre as capacidades máxima e mínima, usando as regras no perfil. Você pode ter várias regras em um perfil. Normalmente, há duas regras: uma para determinar quando escalar horizontalmente e a outra para determinar quando dimensionar horizontalmente. |
| rule | Metrictrigger incluiria | Define a condição de métrica da regra. |
| Metrictrigger incluiria | MetricName | O nome da métrica. |
| Metrictrigger incluiria |  metricResourceUri | A ID de recurso do recurso que emite a métrica. Na maioria dos casos, é o mesmo que o recurso que está sendo dimensionado. Em alguns casos, ele pode ser diferente. Por exemplo, você pode dimensionar um conjunto de dimensionamento de máquinas virtuais com base no número de mensagens em uma fila de armazenamento. |
| Metrictrigger incluiria | timeGrain | A duração da amostragem da métrica. Por exemplo, **Timegranular = "PT1M"** significa que as métricas devem ser agregadas a cada 1 minuto, usando o método de agregação especificado no elemento Statistic. |
| Metrictrigger incluiria | Estatísticas | O método de agregação dentro do período de intervalo. Por exemplo, **estatística = "Average"** e **timegranular = "PT1M"** significa que as métricas devem ser agregadas a cada 1 minuto, considerando a média. Essa propriedade determina como a métrica é amostrada. |
| Metrictrigger incluiria | timeWindow | A quantidade de tempo para voltar para as métricas. Por exemplo, **TimeWindow = "PT10M"** significa que toda vez que o dimensionamento automático é executado, ele consulta as métricas dos últimos 10 minutos. A janela de tempo permite que suas métricas sejam normalizadas e evite reagir a picos transitórios. |
| Metrictrigger incluiria | timeAggregation | O método de agregação usado para agregar as métricas de amostra. Por exemplo, **timeaggregation = "Average"** deve agregar as métricas de amostra, considerando a média. No caso anterior, siga as dez amostras de 1 minuto e faça a média delas. |
| rule | scaleAction | A ação a ser tomada quando o Metrictrigger incluiria da regra for disparado. |
| scaleAction | direção | "Aumentar" para escalar horizontalmente ou "diminuir" para reduzir horizontalmente.|
| scaleAction | valor | Quanto aumentar ou diminuir a capacidade do recurso. |
| scaleAction | cooldown | A quantidade de tempo de espera após uma operação de dimensionamento antes de escalar novamente. Por exemplo, se **cooldown = "PT10M"** , o dimensionamento automático não tentará dimensionar novamente por mais 10 minutos. O cooldown é permitir que as métricas se Estabilizam após a adição ou remoção de instâncias. |

## <a name="autoscale-profiles"></a>Perfis de dimensionamento automático

Há três tipos de perfis de dimensionamento automático:

- **Perfil regular:** O perfil mais comum. Se você não precisar dimensionar seu recurso com base no dia da semana ou em um dia específico, poderá usar um perfil regular. Esse perfil pode então ser configurado com regras de métrica que determinam quando escalar horizontalmente e quando dimensionar horizontalmente. Você deve ter apenas um perfil regular definido.

    O perfil de exemplo usado anteriormente neste artigo é um exemplo de um perfil regular. Observe que também é possível definir um perfil para ser dimensionado para uma contagem de instâncias estática para seu recurso.

- **Perfil de data fixa:** Esse perfil é para casos especiais. Por exemplo, digamos que você tenha um evento importante chegando em 26 de dezembro de 2017 (PST). Você deseja que as capacidades mínima e máxima do recurso sejam diferentes nesse dia, mas que ainda sejam dimensionadas nas mesmas métricas. Nesse caso, você deve adicionar um perfil de data fixa à lista de perfis de sua configuração. O perfil é configurado para ser executado somente no dia do evento. Para qualquer outro dia, o dimensionamento automático usa o perfil regular.

    ``` JSON
    "profiles": [{
    "name": " regularProfile",
    "capacity": {
    ...
    },
    "rules": [{
    ...
    },
    {
    ...
    }]
    },
    {
    "name": "eventProfile",
    "capacity": {
    ...
    },
    "rules": [{
    ...
    }, {
    ...
    }],
    "fixedDate": {
        "timeZone": "Pacific Standard Time",
               "start": "2017-12-26T00:00:00",
               "end": "2017-12-26T23:59:00"
    }}
    ]
    ```
    
- **Perfil de recorrência:** Esse tipo de perfil permite que você verifique se esse perfil é sempre usado em um determinado dia da semana. Os perfis de recorrência têm apenas uma hora de início. Eles são executados até que o próximo perfil de recorrência ou perfil de data fixa seja definido como iniciar. Uma configuração de dimensionamento automático com apenas um perfil de recorrência executa esse perfil, mesmo se houver um perfil regular definido na mesma configuração. Os dois exemplos a seguir ilustram como esse perfil é usado:

    **Exemplo 1: dias da semana versus finais de semana**
    
    Digamos que, nos finais de semana, você queira que a capacidade máxima seja 4. Em dias da semana, como você espera mais carga, você deseja que a capacidade máxima seja 10. Nesse caso, sua configuração conteria dois perfis de recorrência, um para ser executado nos finais de semana e o outro em dias da semana.
    A configuração é parecida com esta:

    ``` JSON
    "profiles": [
    {
    "name": "weekdayProfile",
    "capacity": {
        ...
    },
    "rules": [{
        ...
    }],
    "recurrence": {
        "frequency": "Week",
        "schedule": {
            "timeZone": "Pacific Standard Time",
            "days": [
                "Monday"
            ],
            "hours": [
                0
            ],
            "minutes": [
                0
            ]
        }
    }}
    },
    {
    "name": "weekendProfile",
    "capacity": {
        ...
    },
    "rules": [{
        ...
    }]
    "recurrence": {
        "frequency": "Week",
        "schedule": {
            "timeZone": "Pacific Standard Time",
            "days": [
                "Saturday"
            ],
            "hours": [
                0
            ],
            "minutes": [
                0
            ]
        }
    }
    }]
    ```

    A configuração anterior mostra que cada perfil de recorrência tem uma agenda. Essa agenda determina quando o perfil começa a ser executado. O perfil é interrompido quando é hora de executar outro perfil.

    Por exemplo, na configuração anterior, "weekdayProfile" é definido para iniciar na segunda-feira às 12:00. Isso significa que esse perfil começa a ser executado na segunda-feira às 12:00. Ele continua até sábado às 12:00, quando "weekendProfile" está agendado para iniciar a execução.

    **Exemplo 2: horário comercial**
    
    Digamos que você deseja ter um limite de métrica durante o horário comercial (9:00 às 5:00 PM) e outro para todos os outros horários. A configuração ficaria assim:
    
    ``` JSON
    "profiles": [
    {
    "name": "businessHoursProfile",
    "capacity": {
        ...
    },
    "rules": [{
        ...
    }],
    "recurrence": {
        "frequency": "Week",
        "schedule": {
            "timeZone": "Pacific Standard Time",
            "days": [
                "Monday", “Tuesday”, “Wednesday”, “Thursday”, “Friday”
            ],
            "hours": [
                9
            ],
            "minutes": [
                0
            ]
        }
    }
    },
    {
    "name": "nonBusinessHoursProfile",
    "capacity": {
        ...
    },
    "rules": [{
        ...
    }]
    "recurrence": {
        "frequency": "Week",
        "schedule": {
            "timeZone": "Pacific Standard Time",
            "days": [
                "Monday", “Tuesday”, “Wednesday”, “Thursday”, “Friday”
            ],
            "hours": [
                17
            ],
            "minutes": [
                0
            ]
        }
    }
    }]
    ```
    
    A configuração anterior mostra que "businessHoursProfile" começa a ser executada na segunda-feira às 9:00 AM e continua a 5:00 PM. É quando "nonBusinessHoursProfile" começa a ser executado. O "nonBusinessHoursProfile" é executado até 9:00 da terça-feira e, em seguida, o "businessHoursProfile" assumirá novamente. Isso se repete até sexta-feira às 5:00 PM. Nesse ponto, "nonBusinessHoursProfile" é executado até segunda-feira às 9:00.
    
> [!Note]
> A interface do usuário de dimensionamento automático no portal do Azure impõe horários de término para perfis de recorrência e começa a executar o perfil padrão da configuração de dimensionamento automático entre perfis de recorrência.
    
## <a name="autoscale-evaluation"></a>Avaliação de dimensionamento automático
Considerando que as configurações de autoescala podem ter vários perfis, e cada perfil pode ter várias regras de métrica, é importante entender como uma configuração de dimensionamento automático é avaliada. Cada vez que o trabalho de dimensionamento automático é executado, ele começa escolhendo o perfil aplicável. Em seguida, o dimensionamento automático avalia os valores mínimo e máximo, bem como as regras de métrica no perfil, e decide se uma ação de escala é necessária.

### <a name="which-profile-will-autoscale-pick"></a>Qual perfil será escolhido pelo dimensionamento automático?

O dimensionamento automático usa a seguinte sequência para escolher o perfil:
1. Primeiro, ele procura qualquer perfil de data fixa que esteja configurado para ser executado agora. Se houver, o dimensionamento automático o executará. Se houver vários perfis de datas fixas que devam ser executados, o dimensionamento automático selecionará o primeiro.
2. Se não houver nenhum perfil de data fixa, o dimensionamento automático examinará os perfis de recorrência. Se um perfil de recorrência for encontrado, ele o executará.
3. Se não houver nenhuma data fixa ou perfis de recorrência, o dimensionamento automático executará o perfil regular.

### <a name="how-does-autoscale-evaluate-multiple-rules"></a>Como o dimensionamento automático avalia várias regras?

Depois que o dimensionamento automático determina qual perfil deve ser executado, ele avalia todas as regras de expansão no perfil (elas são regras com **Direction = "aumentar"** ).

Se uma ou mais regras de escala horizontal forem disparadas, o dimensionamento automático calculará a nova capacidade determinada pela **scaleaction** de cada uma dessas regras. Em seguida, ele é dimensionado para o máximo dessas capacidades, para garantir a disponibilidade do serviço.

Por exemplo, digamos que haja um conjunto de dimensionamento de máquinas virtuais com uma capacidade atual de 10. Há duas regras de expansão: uma que aumenta a capacidade em 10% e outra que aumenta a capacidade em 3 contagens. A primeira regra resultaria em uma nova capacidade de 11, e a segunda regra resultaria em uma capacidade de 13. Para garantir a disponibilidade do serviço, o dimensionamento automático escolhe a ação que resulta na capacidade máxima, portanto, a segunda regra é escolhida.

Se nenhuma regra de expansão for disparada, o dimensionamento automático avaliará todas as regras de redução horizontal (regras com **Direction = "diminuir"** ). O dimensionamento automático só levará uma ação de redução se todas as regras de dimensionamento forem disparadas.

O dimensionamento automático calcula a nova capacidade determinada pela **scaleaction** de cada uma dessas regras. Em seguida, ele escolhe a ação de dimensionamento que resulta no máximo dessas capacidades para garantir a disponibilidade do serviço.

Por exemplo, digamos que haja um conjunto de dimensionamento de máquinas virtuais com uma capacidade atual de 10. Há duas regras de redução horizontal: uma que diminui a capacidade em 50% e outra que diminui a capacidade em 3 contagens. A primeira regra resultaria em uma nova capacidade de 5, e a segunda regra resultaria em uma capacidade de 7. Para garantir a disponibilidade do serviço, o dimensionamento automático escolhe a ação que resulta na capacidade máxima, portanto, a segunda regra é escolhida.

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre o dimensionamento automático fazendo referência ao seguinte:

* [Descrição geral do dimensionamento automático](../../azure-monitor/platform/autoscale-overview.md)
* [Azure Monitor métricas comuns de dimensionamento automático](../../azure-monitor/platform/autoscale-common-metrics.md)
* [Práticas recomendadas para Azure Monitor dimensionamento automático](../../azure-monitor/platform/autoscale-best-practices.md)
* [Usar ações de dimensionamento automático para enviar notificações de alerta por email e webhook](../../azure-monitor/platform/autoscale-webhook-email.md)
* [API REST de dimensionamento automático](https://msdn.microsoft.com/library/dn931953.aspx)

