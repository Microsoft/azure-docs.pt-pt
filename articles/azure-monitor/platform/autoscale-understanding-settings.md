---
title: Compreender as definições de escala automática no Monitor Azure
description: Uma desagregação detalhada das definições de escala automática e como funcionam. Aplica-se a Máquinas Virtuais, Serviços cloud, Web Apps
ms.topic: conceptual
ms.date: 12/18/2017
ms.subservice: autoscale
ms.openlocfilehash: 9a2b94208de7ce490a0e7acfbb71175b4a7c846e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75364310"
---
# <a name="understand-autoscale-settings"></a>Compreender as definições de Dimensionamento Automático
As definições de escala automática ajudam a garantir que tem a quantidade certa de recursos em execução para lidar com a carga flutuante da sua aplicação. Pode configurar as definições de escala automática a serem ativadas com base em métricas que indiquem carga ou desempenho, ou desencadeadas numa data e hora programadas. Este artigo dá uma olhada detalhada na anatomia de um cenário de escala automática. O artigo começa com o esquema e propriedades de um cenário, e depois percorre os diferentes tipos de perfil que podem ser configurados. Por fim, o artigo discute como a funcionalidade De Escala Automática no Azure avalia qual o perfil a executar a qualquer momento.

## <a name="autoscale-setting-schema"></a>Esquema de definição de escala automática
Para ilustrar o esquema de definição de escala automática, é utilizada a seguinte definição de escala automática. É importante notar que esta definição de escala automática tem:
- Um perfil. 
- Duas regras métricas neste perfil: uma para escala para fora, e outra para escala dentro
  - A regra de escala é desencadeada quando a métrica média de CPU da escala de máquina virtual é superior a 85% nos últimos 10 minutos.
  - A regra da escala é desencadeada quando a média da escala de máquinas virtuais é inferior a 60% no último minuto.

> [!NOTE]
> Uma definição pode ter vários perfis. Para saber mais, consulte a secção [de perfis.](#autoscale-profiles) Um perfil também pode ter várias regras de escala e regras de escala definidas. Para ver como são avaliados, consulte a secção de [avaliação.](#autoscale-evaluation)

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
| Definição | ID | O ID de recurso da definição de autoescala. As definições de escala automática são um recurso do Gestor de Recursos Azure. |
| Definição | nome | O nome de definição de escala automática. |
| Definição | localização | A localização da definição de escala automática. Esta localização pode ser diferente da localização do recurso que está a ser dimensionado. |
| propriedades | targetResourceUri | A identificação do recurso que está a ser dimensionada. Só pode ter uma definição de escala automática por recurso. |
| propriedades | perfis | Uma definição de escala automática é composta por um ou mais perfis. Cada vez que o motor de escala automática funciona, executa um perfil. |
| perfil | nome | O nome do perfil. Pode escolher qualquer nome que o ajude a identificar o perfil. |
| perfil | Capacidade.máxima | A capacidade máxima permitida. Garante que a Escala Automática, ao executar este perfil, não dimensiona o seu recurso acima deste número. |
| perfil | Capacidade.mínima | A capacidade mínima permitida. Garante que a Escala Automática, ao executar este perfil, não dimensiona o seu recurso abaixo deste número. |
| perfil | Capacidade.padrão | Se houver algum problema em ler a métrica do recurso (neste caso, o CPU de "vmss1"), e a capacidade atual for inferior ao padrão, a escala automática escorre para o padrão. Isto é para garantir a disponibilidade do recurso. Se a capacidade atual já for superior à capacidade padrão, a escala automática não escala. |
| perfil | regras | A escala automática escala automaticamente entre as capacidades máximas e mínimas, utilizando as regras do perfil. Pode ter várias regras num perfil. Normalmente existem duas regras: uma para determinar quando escalar, e outra para determinar quando escalar. |
| regra | métricaTrigger | Define a condição métrica da regra. |
| métricaTrigger | nome métrico | O nome da métrica. |
| métricaTrigger |  metricResourceUri | A identificação do recurso que emite a métrica. Na maioria dos casos, é o mesmo que o recurso que está a ser dimensionado. Em alguns casos, pode ser diferente. Por exemplo, pode escalar um conjunto de escala de máquina virtual com base no número de mensagens numa fila de armazenamento. |
| métricaTrigger | timeGrain | A duração da amostragem métrica. Por exemplo, **TimeGrain = "PT1M"** significa que as métricas devem ser agregadas a cada 1 minuto, utilizando o método de agregação especificado no elemento estatístico. |
| métricaTrigger | estatística | O método de agregação dentro do período timeGrain. Por exemplo, **estatística = "Média"** e **tempoGrain = "PT1M"** significa que as métricas devem ser agregadas a cada 1 minuto, tomando a média. Esta propriedade dita como a métrica é amostrada. |
| métricaTrigger | timeWindow | A quantidade de tempo para olhar para trás para as métricas. Por exemplo, **timeWindow = "PT10M"** significa que sempre que a escala automática corre, consulta métricas nos últimos 10 minutos. A janela do tempo permite que as suas métricas sejam normalizadas e evita reagir a picos transitórios. |
| métricaTrigger | timeAggregation | O método de agregação usado para agregar as métricas amostradas. Por exemplo, a **timeAggregation = "Average"** deve agregar as métricas amostradas tomando a média. No caso anterior, pegue as dez amostras de 1 minuto, e faça uma média delas. |
| regra | escalaAction | A ação a tomar quando a métrica Trigger da regra é desencadeada. |
| escalaAction | direção | "Aumentar" para escalar, ou "Diminuir" para escalar.|
| escalaAction | valor | Quanto para aumentar ou diminuir a capacidade do recurso. |
| escalaAction | tempo de arrefecimento | O tempo de espera após uma operação à escala antes de voltar a escalonar. Por exemplo, se **o arrefecimento = "PT10M"**, a escala automática não tentar escalar novamente por mais 10 minutos. O arrefecimento é permitir que as métricas se estabilizem após a adição ou remoção de instâncias. |

## <a name="autoscale-profiles"></a>Perfis de escala automática

Existem três tipos de perfis de escala automática:

- **Perfil regular:** O perfil mais comum. Se não precisar de escalar o seu recurso com base no dia da semana, ou num determinado dia, pode utilizar um perfil regular. Este perfil pode então ser configurado com regras métricas que ditam quando escalar e quando escalar. Só deve ter um perfil regular definido.

    O perfil de exemplo usado anteriormente neste artigo é um exemplo de um perfil regular. Note que também é possível definir um perfil à escala para uma contagem de exemplos estáticos para o seu recurso.

- **Perfil de data fixa:** Este perfil é para casos especiais. Por exemplo, digamos que tem um evento importante a chegar a 26 de dezembro de 2017 (PST). Quer que as capacidades mínimas e máximas do seu recurso sejam diferentes nesse dia, mas ainda assim escalam nas mesmas métricas. Neste caso, deverá adicionar um perfil de data fixa à lista de perfis da sua definição. O perfil está configurado para ser executado apenas no dia do evento. Para qualquer outro dia, a Escala Automática utiliza o perfil regular.

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
    
- Perfil de **recorrência:** Este tipo de perfil permite-lhe garantir que este perfil é sempre utilizado num determinado dia da semana. Os perfis de recorrência só têm uma hora de início. Funcionam até que o próximo perfil de recorrência ou perfil de data fixa esteja definido para começar. Uma definição de escala automática com apenas um perfil de recorrência executa esse perfil, mesmo que exista um perfil regular definido na mesma configuração. Os dois exemplos seguintes ilustram como este perfil é utilizado:

    **Exemplo 1: Dias úteis vs fins de semana**
    
    Digamos que nos fins de semana, quer que a sua capacidade máxima seja de 4. Nos dias úteis, porque espera mais carga, quer que a sua capacidade máxima seja de 10. Neste caso, a sua configuração conteria dois perfis de recorrência, um para correr aos fins de semana e outro nos dias úteis.
    A definição é assim:

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

    A definição anterior mostra que cada perfil de recorrência tem um horário. Este horário determina quando o perfil começa a funcionar. O perfil para quando é hora de fazer outro perfil.

    Por exemplo, na definição anterior, o "WeekdayProfile" tem início na segunda-feira às 00:00. Isso significa que este perfil começa a funcionar na segunda-feira às 00:00. Continua até sábado às 00:00, quando o "weekendProfile" está agendado para começar a funcionar.

    **Exemplo 2: Horário de trabalho**
    
    Digamos que quer ter um limiar métrico durante o horário comercial (das 9:00 às 17:00 horas), e um diferente para todos os outros tempos. A definição seria assim:
    
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
    
    A definição anterior mostra que o "businessHoursProfile" começa a decorrer na segunda-feira às 9:00 da manhã, e continua às 17:00. É quando o "não BusinessHoursProfile" começa a funcionar. O "não BusinessHoursProfile" decorre até às 9:00 de terça-feira e, em seguida, o "businessHoursProfile" retoma. Isto repete-se até sexta-feira às 17h. Nessa altura, o "não BusinessHoursProfile" decorre até segunda-feira às 9:00.
    
> [!Note]
> A interface de utilizador de escala automática no portal Azure aplica os tempos finais para perfis de recorrência e começa a executar o perfil padrão da definição de escala automática entre perfis de recorrência.
    
## <a name="autoscale-evaluation"></a>Avaliação à escala automática
Dado que as configurações de escala automática podem ter múltiplos perfis, e cada perfil pode ter múltiplas regras métricas, é importante entender como uma definição de escala automática é avaliada. Cada vez que o trabalho em Escala Automática corre, começa por escolher o perfil que é aplicável. Em seguida, a Escala Automática avalia os valores mínimos e máximos, e quaisquer regras métricas no perfil, e decide se uma ação de escala é necessária.

### <a name="which-profile-will-autoscale-pick"></a>Que perfil escolherá a Escala Automática?

A escala automática utiliza a seguinte sequência para escolher o perfil:
1. Procura primeiro qualquer perfil de data fixa que esteja configurado para ser executado agora. Se houver, a escala automática executa-o. Se houver vários perfis de data fixa que devem ser executados, a Escala Automática seleciona a primeira.
2. Se não houver perfis de data fixa, a Autoscale analisa os perfis de recorrência. Se for encontrado um perfil de recorrência, executa-o.
3. Se não houver uma data fixa ou perfis de recorrência, a Escala Automática executa o perfil regular.

### <a name="how-does-autoscale-evaluate-multiple-rules"></a>Como é que a Autoscale avalia várias regras?

Depois de a Autoscale determinar qual o perfil a executar, avalia todas as regras de escala no perfil (estas são regras com **direção = "Aumentar").**

Se forem desencadeadas uma ou mais regras de escala, a Escala Automática calcula a nova capacidade determinada pela **escalaAction** de cada uma dessas regras. Em seguida, eleva-se ao máximo dessas capacidades, para garantir a disponibilidade do serviço.

Por exemplo, digamos que há uma escala de máquina virtual com uma capacidade atual de 10. Há duas regras de escala: uma que aumenta a capacidade em 10 por cento, e outra que aumenta a capacidade em 3 contagens. A primeira regra resultaria numa nova capacidade de 11, e a segunda regra resultaria numa capacidade de 13. Para garantir a disponibilidade do serviço, a Escala Automática escolhe a ação que resulta na capacidade máxima, pelo que a segunda regra é escolhida.

Se não forem desencadeadas regras de escala, a Escala Automática avalia todas as regras de escala (regras com **direção = "Diminuição").** A escala automática só toma uma ação de escala se todas as regras de escala forem desencadeadas.

A escala automática calcula a nova capacidade determinada pela **escalaAction** de cada uma dessas regras. Em seguida, escolhe a ação de escala que resulta no máximo dessas capacidades para garantir a disponibilidade do serviço.

Por exemplo, digamos que há uma escala de máquina virtual com uma capacidade atual de 10. Há duas regras de escala: uma que diminui a capacidade em 50 por cento, e outra que diminui a capacidade em 3 contagens. A primeira regra resultaria numa nova capacidade de 5, e a segunda regra resultaria numa capacidade de 7. Para garantir a disponibilidade do serviço, a Escala Automática escolhe a ação que resulta na capacidade máxima, pelo que a segunda regra é escolhida.

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre a Escala Automática referindo-se ao seguinte:

* [Descrição geral do dimensionamento automático](../../azure-monitor/platform/autoscale-overview.md)
* [Métricas comuns de escala automática Azure Monitor](../../azure-monitor/platform/autoscale-common-metrics.md)
* [Melhores práticas para o dimensionamento automático do Azure Monitor](../../azure-monitor/platform/autoscale-best-practices.md)
* [Utilize ações de escala automática para enviar notificações de alerta de e-mail e webhook](../../azure-monitor/platform/autoscale-webhook-email.md)
* [API REST DE Escala Automática](https://msdn.microsoft.com/library/dn931953.aspx)

