---
title: Noções básicas sobre definições de dimensionamento automático no Azure Monitor
description: Uma divisão detalhada das definições de dimensionamento automático e como elas funcionam. Aplica-se a máquinas virtuais, serviços Cloud, Web Apps
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 12/18/2017
ms.author: ancav
ms.subservice: autoscale
ms.openlocfilehash: 02840b8a909f46c37130bdb7162674c694a0ff96
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60787500"
---
# <a name="understand-autoscale-settings"></a>Compreender as definições de Dimensionamento Automático
Definições de dimensionamento automático ajudam a garantir que tenha a quantidade certa de recursos em execução para processar a carga flutuantes da sua aplicação. Pode configurar definições de dimensionamento automático para ser acionado com base nas métricas que indicam a carga e de desempenho ou acionadas numa data e hora agendadas. Este artigo analisa detalhadamente a anatomia de uma definição de dimensionamento automático. O artigo começa com o esquema e propriedades de uma configuração e, em seguida, percorre os tipos de perfis diferentes que podem ser configurados. Por fim, o artigo discute como a funcionalidade de dimensionamento automático no Azure avalia que perfil de executar um determinado momento.

## <a name="autoscale-setting-schema"></a>Esquema de definição de dimensionamento automático
Para ilustrar o esquema de definição de dimensionamento automático, é utilizada a seguinte definição de dimensionamento automático. É importante observar que tem esta definição de dimensionamento automático:
- Um perfil. 
- Duas regras de métrica neste perfil: um para o Escalamento horizontal e outro para reduzir horizontalmente.
  - A regra de escalamento horizontal é acionada quando a métrica de CPU de percentagem média do conjunto de dimensionamento de máquina virtual for superior a 85 por cento durante os últimos 10 minutos.
  - A regra de redução horizontal é acionada quando a média do conjunto de dimensionamento de máquina virtual é inferior a 60 por cento para os últimos minutos.

> [!NOTE]
> Uma configuração pode ter vários perfis. Para obter mais informações, consulte a [perfis](#autoscale-profiles) secção. Um perfil também pode ter várias regras de escalamento horizontal e regras de dimensionamento definidas. Para ver como são avaliadas, consulte a [avaliação](#autoscale-evaluation) secção.

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
| Definição | id | ID de recurso. da definição de dimensionamento automático Definições de dimensionamento automático são um recurso do Azure Resource Manager. |
| Definição | name | O nome da definição de dimensionamento automático. |
| Definição | location | A localização da definição de dimensionamento automático. Esta localização pode ser diferente da localização do recurso que está a ser dimensionado. |
| properties | targetResourceUri | O ID de recurso do recurso que está a ser dimensionado. Só pode ter uma definição de dimensionamento automático por recurso. |
| properties | profiles | Uma definição de dimensionamento automático é composta por um ou mais perfis. Sempre que o mecanismo de dimensionamento automático é executado, ele executa um perfil. |
| profile | name | O nome do perfil. Pode escolher qualquer nome que o ajuda a identificar o perfil. |
| profile | Capacity.Maximum | A capacidade máxima permitida. Ele garante que o dimensionamento automático, ao executar este perfil, não dimensionar seu recurso acima deste número. |
| profile | Capacity.Minimum | A capacidade mínima permitida. Ele garante que o dimensionamento automático, ao executar este perfil, não dimensionar seu recurso abaixo deste número. |
| profile | Capacity.default | Se existir um problema ao ler a métrica de recurso (no caso, a CPU de "vmss1"), e a capacidade atual é inferior a predefinição, o dimensionamento automático aumenta horizontalmente para a predefinição. Isso é para garantir a disponibilidade do recurso. Se a capacidade atual já é maior do que a capacidade predefinida, dimensionamento automático não horizontalmente. |
| profile | rules | Dimensionamento automático pode ser dimensionada automaticamente entre as capacidades de máxima e mínimas, ao utilizar as regras no perfil. Pode ter várias regras num perfil. Normalmente, há duas regras: um para determinar quando deve aumentar horizontalmente e outro para determinar quando deve reduzir horizontalmente. |
| rule | metricTrigger | Define as condições de métricas da regra. |
| metricTrigger | metricName | O nome da métrica. |
| metricTrigger |  metricResourceUri | O ID de recurso do recurso que emite a métrica. Na maioria dos casos, é o mesmo que o recurso a ser dimensionado. Em alguns casos, podem ser diferente. Por exemplo, pode dimensionar um conjunto de dimensionamento de máquinas virtuais com base no número de mensagens numa fila de armazenamento. |
| metricTrigger | timeGrain | A duração de amostragem da métrica. Por exemplo, **TimeGrain = "PT1M"** significa que as métricas devem ser agregadas a cada 1 minuto, ao utilizar o método de agregação especificado no elemento estatística. |
| metricTrigger | estatística | O método de agregação dentro do período de timeGrain. Por exemplo, **estatística = "Average"** e **timeGrain = "PT1M"** significa que as métricas devem ser agregadas cada 1 minuto pela média. Esta propriedade determina a forma como é objeto de amostragem da métrica. |
| metricTrigger | timeWindow | A quantidade de tempo para procurar novamente por métricas. Por exemplo, **timeWindow = "PT10M"** significa que sempre que o dimensionamento automático for executado, ele consulta métricas durante os últimos 10 minutos. A janela de tempo permite às métricas ser normalizados e evita a reação a picos transitórios. |
| metricTrigger | timeAggregation | O método de agregação utilizado para agregar as métricas de amostragem. Por exemplo, **TimeAggregation = "Average"** devem se agregar as métricas de amostragem pela média. No caso anterior, pegar os dez exemplos de 1 minuto e, média-los. |
| rule | scaleAction | A ação a tomar quando o metricTrigger da regra é acionada. |
| scaleAction | direction | "Aumente" para aumentar horizontalmente, ou "Diminuir" para reduzir horizontalmente.|
| scaleAction | value | Quanto para aumentar ou diminuir a capacidade do recurso. |
| scaleAction | arrefecimento | A quantidade de tempo de espera após uma operação de dimensionamento antes de dimensionar novamente. Por exemplo, se **arrefecimento = "PT10M"** , dimensionamento automático não tentar Dimensionar novamente para mais de 10 minutos. O arrefecimento é permitir que as métricas estabilizar após a adição ou remoção de instâncias. |

## <a name="autoscale-profiles"></a>Perfis de dimensionamento automático

Existem três tipos de perfis de dimensionamento automático:

- **Perfil regular:** O perfil mais comuns. Se não precisar de aumentar o seu recurso com base no dia da semana, ou num determinado dia, pode utilizar um perfil regular. Este perfil, em seguida, pode ser configurado com regras de métricas que ditam quando aumentar horizontalmente e quando reduzir horizontalmente. Deverá ter apenas um perfil regular definido.

    O perfil de exemplo usado anteriormente neste artigo é um exemplo de um perfil de regular. Tenha em atenção que também é possível definir um perfil de dimensionamento com uma contagem de instância estática para o seu recurso.

- **Data fixa perfil:** Este perfil é para casos especiais. Por exemplo, digamos que tiver um evento importante em breve de 26 de Dezembro de 2017 (PST). Pretende que o mínimas e máxima as capacidades do seu recurso seja diferente nesse dia, mas ainda dimensionamento sobre as métricas da mesmas. Neste caso, deve adicionar um perfil de data fixa a lista de sua configuração de perfis. O perfil está configurado para ser executado apenas no dia do evento. Para qualquer outro dia, o dimensionamento automático utiliza o perfil regular.

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
    
- **Perfil de periodicidade:** Este tipo de perfil permite-lhe garantir que este perfil é sempre usado num determinado dia da semana. Perfis de periodicidade tem apenas uma hora de início. Eles são executados até o seguinte perfil de periodicidade ou data fixa perfil está definido para iniciar. Uma definição de dimensionamento automático com o perfil de periodicidade apenas um executa esse perfil, mesmo que haja um perfil regular definido na definição da mesma. Os seguintes dois exemplos ilustram como este perfil é utilizado:

    **Exemplo 1: Dias da semana versus fins de semana**
    
    Digamos que no fim de semana, pretende que sua capacidade máxima para ser 4. Nos dias de semana, porque espera mais carga, quer sua capacidade máxima para ser 10. Neste caso, a definição iria conter dois perfis de periodicidade, um para executar nos fins de semana e a outra nos dias de semana.
    A definição tem o seguinte aspeto:

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

    A configuração anterior mostra que cada perfil de periodicidade tem uma agenda. Esta agenda determina quando o perfil é iniciado em execução. O perfil para quando está na altura de outro perfil de execução.

    Por exemplo, na definição da anterior, "weekdayProfile" está definido para iniciar o 12:00 AM na segunda-feira. Isso significa que este perfil começa a ser executado na próxima segunda-feira às 12:00 AM. Ele continua até sábado às 12:00 AM, quando o "weekendProfile" está agendado para começar a executar.

    **Exemplo 2: horário comercial**
    
    Digamos que queira ter um limiar de métrica durante o horário comercial (9:00 para 5:21 horas) e outra para as restantes horas. A definição teria o seguinte aspeto:
    
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
    
    A configuração anterior mostra que "businessHoursProfile" começa a executar na segunda-feira, às 9:00 e continua às 17:00. Que é quando "nonBusinessHoursProfile" entrar em execução. "nonBusinessHoursProfile" é executada até 9:00 Terça-feira e, em seguida, "businessHoursProfile" assume novamente. Isso se repete até Sexta-feira às 17:00: 00. Nesse ponto, "nonBusinessHoursProfile" é executada até à segunda-feira às 9:00.
    
> [!Note]
> A interface do usuário de dimensionamento automático no portal do Azure impõe horas de fim para perfis de periodicidade e começa a executar o perfil predefinido de definição de dimensionamento automático entre perfis de periodicidade.
    
## <a name="autoscale-evaluation"></a>Avaliação de dimensionamento automático
Uma vez que as definições de dimensionamento automático podem ter vários perfis, e cada perfil pode ter várias regras de métricas, é importante compreender como uma definição de dimensionamento automático é avaliada. Sempre que for executada a tarefa de dimensionamento automático, ele começa ao escolher o perfil que se aplicam. Em seguida, o dimensionamento automático avalia o mínimo e máximos valores e quaisquer regras de métrica no perfil e decide se uma ação de dimensionamento é necessária.

### <a name="which-profile-will-autoscale-pick"></a>Que perfil de dimensionamento automático irá escolher?

Dimensionamento automático utiliza a seguinte sequência para escolher o perfil:
1. Procura primeiro qualquer perfil de data fixa que está configurado para ser executado agora. Se existir, o dimensionamento automático executa-o. Se existirem vários perfis de data fixa que devem ser executado, o dimensionamento automático seleciona primeiro.
2. Se não houver nenhum perfil de data fixa, o dimensionamento automático analisa os perfis de periodicidade. Se não for encontrado um perfil de periodicidade, é executada.
3. Se não são data fixa ou perfis de periodicidade, o dimensionamento automático é executado com o perfil regular.

### <a name="how-does-autoscale-evaluate-multiple-rules"></a>Como é que o dimensionamento automático avaliar várias regras?

Depois de dimensionamento automático determina que perfil de execução, ele avalia todas as regras de escalamento horizontal no perfil (estes são regras com **direção = "Aumentar"** ).

Se uma ou mais regras de escalamento horizontal são acionadas, dimensionamento automático calcula a nova capacidade determinada pelos **scaleAction** de cada uma dessas regras. Em seguida, ele aumenta horizontalmente para o número máximo dessas capacidades, para garantir a disponibilidade do serviço.

Por exemplo, digamos que lá está definido de dimensionamento de máquinas virtuais com uma capacidade atual de 10. Há duas regras de escalamento horizontal: um que aumenta a capacidade em 10 por cento e outro que aumenta a capacidade por 3 contagens. A primeira regra resultaria numa nova capacidade de 11, e a segunda regra resultaria numa capacidade de 13. Para garantir a disponibilidade do serviço, o dimensionamento automático escolhe a ação que resulta na capacidade máxima, por isso, a segunda regra é escolhida.

Se não existem regras de escalamento horizontal são acionadas, dimensionamento automático avalia todas as redução horizontal regras (regras com **direção = "Diminuir"** ). Dimensionamento automático permite apenas uma ação de dimensionamento se todas as regras de redução horizontal são acionadas.

Dimensionamento automático calcula a nova capacidade determinada pelos **scaleAction** de cada uma dessas regras. Em seguida, ele escolhe a ação de dimensionamento que resulta num máximo dessas capacidades para garantir a disponibilidade do serviço.

Por exemplo, digamos que lá está definido de dimensionamento de máquinas virtuais com uma capacidade atual de 10. Há duas regras de dimensionamento: um que diminui a capacidade em 50 por cento e outro que diminui a capacidade por 3 contagens. A primeira regra resultaria numa nova capacidade de 5 e a segunda regra resultaria numa capacidade de 7. Para garantir a disponibilidade do serviço, o dimensionamento automático escolhe a ação que resulta na capacidade máxima, por isso, a segunda regra é escolhida.

## <a name="next-steps"></a>Passos Seguintes
Saiba mais sobre dimensionamento automático ao referir-se ao seguinte:

* [Descrição geral do dimensionamento automático](../../azure-monitor/platform/autoscale-overview.md)
* [Métricas comuns do Azure Monitor dimensionamento automático](../../azure-monitor/platform/autoscale-common-metrics.md)
* [Melhores práticas do dimensionamento automático do Azure Monitor](../../azure-monitor/platform/autoscale-best-practices.md)
* [Utilizar ações de dimensionamento automático para enviar o e-mail e webhook notificações de alerta](../../azure-monitor/platform/autoscale-webhook-email.md)
* [API de REST de dimensionamento automático](https://msdn.microsoft.com/library/dn931953.aspx)

