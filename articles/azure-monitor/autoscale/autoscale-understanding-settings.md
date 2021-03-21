---
title: Compreender definições de autoescala no Azure Monitor
description: Uma desagregação detalhada das definições de autoescala e como funcionam. Aplica-se a máquinas virtuais, serviços em nuvem, aplicações web
ms.topic: conceptual
ms.date: 12/18/2017
ms.subservice: autoscale
ms.openlocfilehash: a914f6d71c013acea8dfde0f6578985bc009bb26
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100617591"
---
# <a name="understand-autoscale-settings"></a>Compreender as definições de Dimensionamento Automático
As definições de autoescala ajudam a garantir que tem a quantidade certa de recursos em execução para lidar com a carga flutuante da sua aplicação. Pode configurar as definições de Autoescalação a serem ativadas com base em métricas que indicam carga ou desempenho, ou ativadas numa data e hora programadas. Este artigo dá uma olhada detalhada na anatomia de uma definição de Autoscale. O artigo começa com o esquema e propriedades de uma configuração, e depois anda pelos diferentes tipos de perfil que podem ser configurados. Finalmente, o artigo discute como a funcionalidade Autoscale em Azure avalia qual o perfil a executar a qualquer momento.

## <a name="autoscale-setting-schema"></a>Esquema de definição de autoescala
Para ilustrar o esquema de definição de autoescala, é utilizada a seguinte definição de Autoscale. É importante notar que esta definição de Autoscale tem:
- Um perfil. 
- Duas regras métricas neste perfil: uma para escala para fora, e uma para escala dentro
  - A regra de escala é desencadeada quando a métrica média de CPU da escala de máquina virtual é superior a 85% nos últimos 10 minutos.
  - A regra de dimensionamento é ativada quando a média da escala de máquina virtual é inferior a 60% no minuto anterior.

> [!NOTE]
> Uma definição pode ter vários perfis. Para saber mais, consulte a secção [de perfis.](#autoscale-profiles) Um perfil também pode ter várias regras de escala e regras de escala definidas. Para ver como são avaliados, consulte a secção [de avaliação.](#autoscale-evaluation)

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
| Definição | ID | O ID de recursos da definição automática. As definições de autoescala são um recurso Azure Resource Manager. |
| Definição | name | O nome de definição de autoescala. |
| Definição | localização | A localização da definição de autoescala. Esta localização pode ser diferente da localização do recurso a ser dimensionado. |
| propriedades | targetResourceUri | O ID de recursos do recurso a ser dimensionado. Só é possível ter uma definição de Autoscale por recurso. |
| propriedades | perfis | Uma definição de autoescala é composta por um ou mais perfis. Cada vez que o motor de autoescala funciona, executa um perfil. |
| perfil | name | O nome do perfil. Pode escolher qualquer nome que o ajude a identificar o perfil. |
| perfil | Capacidade.máxima | A capacidade máxima permitida. Garante que a Autoscale, ao executar este perfil, não escala o seu recurso acima deste número. |
| perfil | Capacidade.mínimo | A capacidade mínima permitida. Garante que a Autoscale, ao executar este perfil, não escala o seu recurso abaixo deste número. |
| perfil | Capacidade.predefinição | Se houver um problema de leitura da métrica do recurso (neste caso, o CPU de "vmss1"), e a capacidade atual estiver abaixo do padrão, a Autoescala escala para o padrão. Isto é para garantir a disponibilidade do recurso. Se a capacidade atual já for superior à capacidade padrão, a Autoscale não escala. |
| perfil | regras | A escala automática é automaticamente dimensiona entre as capacidades máximas e mínimas, utilizando as regras do perfil. Pode ter várias regras num perfil. Normalmente, existem duas regras: uma para determinar quando escalar, e a outra para determinar quando escalar. |
| regra | metricTrigger | Define a condição métrica da regra. |
| metricTrigger | nome métrico | O nome da métrica. |
| metricTrigger |  metricResourceUri | A identificação de recursos do recurso que emite a métrica. Na maioria dos casos, é o mesmo que o recurso a ser dimensionado. Em alguns casos, pode ser diferente. Por exemplo, pode escalar uma escala de máquina virtual definida com base no número de mensagens numa fila de armazenamento. |
| metricTrigger | timeGrain | A duração da amostragem métrica. Por exemplo, **TimeGrain = "PT1M"** significa que as métricas devem ser agregadas a cada 1 minuto, utilizando o método de agregação especificado no elemento estatístico. |
| metricTrigger | estatística | O método de agregação dentro do período de tempoGrain. Por exemplo, **estatística = "Média"** e **tempoGrain = "PT1M"** significa que as métricas devem ser agregadas a cada 1 minuto, tomando a média. Esta propriedade dita como a métrica é amostrada. |
| metricTrigger | timeWindow | A quantidade de tempo para olhar para trás para as métricas. Por exemplo, **timeWindow = "PT10M"** significa que cada vez que a Autoscale corre, consulta métricas nos últimos 10 minutos. A janela de tempo permite normalizar as suas métricas e evita reagir a picos transitórios. |
| metricTrigger | timeAggregation | O método de agregação utilizado para agregar as métricas amostradas. Por exemplo, **a agregação de tempo = "Média"** deve agregar as métricas amostradas tomando a média. No caso anterior, pegue as 10 amostras de 1 minuto e a média delas. |
| regra | scaleAction | A ação a tomar quando a métricaTrigger da regra é desencadeada. |
| scaleAction | direção | "Aumentar" para escalar, ou "Diminuir" para escalar dentro|
| scaleAction | valor | Quanto para aumentar ou diminuir a capacidade do recurso. |
| scaleAction | tempo de arrefecimento | A quantidade de tempo para esperar depois de uma operação de escala antes de voltar a escalonar. Por exemplo, se **arrefecer = "PT10M",** a Autoescala não tente escalar novamente durante mais 10 minutos. O arrefecimento é para permitir que as métricas estabilizem após a adição ou remoção de instâncias. |

## <a name="autoscale-profiles"></a>Perfis de autoescala

Existem três tipos de perfis de autoescala:

- **Perfil regular:** O perfil mais comum. Se não precisar de escalar o seu recurso com base no dia da semana, ou num determinado dia, pode utilizar um perfil regular. Este perfil pode então ser configurado com regras métricas que ditam quando escalar e quando escalar dentro Só deve ter um perfil regular definido.

    O perfil de exemplo usado anteriormente neste artigo é um exemplo de um perfil regular. Note que também é possível definir um perfil em escala para uma contagem de instância estática para o seu recurso.

- **Perfil de data fixa:** Este perfil é para casos especiais. Por exemplo, digamos que terá um evento importante a 26 de dezembro de 2017 (PST). Quer que as capacidades mínimas e máximas do seu recurso sejam diferentes nesse dia, mas ainda assim escalar nas mesmas métricas. Neste caso, deverá adicionar um perfil de data fixa à lista de perfis da sua definição. O perfil está configurado para ser executado apenas no dia do evento. Para qualquer outro dia, a Autoscale utiliza o perfil regular.

    ```json
    "profiles": [
        {
            "name": " regularProfile",
            "capacity": {
                ...
            },
            "rules": [
                {
                ...
                },
                {
                ...
                }
            ]
        },
        {
            "name": "eventProfile",
            "capacity": {
            ...
            },
            "rules": [
                {
                ...
                }, 
                {
                ...
                }
            ],
            "fixedDate": {
                "timeZone": "Pacific Standard Time",
                "start": "2017-12-26T00:00:00",
                "end": "2017-12-26T23:59:00"
            }
        }
    ]
    ```
    
- **Perfil de recorrência:** Este tipo de perfil permite-lhe garantir que este perfil é sempre utilizado num determinado dia da semana. Os perfis de recorrência só têm uma hora de início. Funcionam até que o próximo perfil de recorrência ou o perfil de data fixa estejam definidos para começar. Uma definição de Autoscale com apenas um perfil de recorrência executa esse perfil, mesmo que haja um perfil regular definido na mesma definição. Os dois exemplos que se seguem ilustram a forma como este perfil é utilizado:

    **Exemplo 1: Dias úteis vs. fins de semana**
    
    Digamos que nos fins de semana, quer que a sua capacidade máxima seja 4. Nos dias úteis, porque espera mais carga, quer que a sua capacidade máxima seja de 10. Neste caso, a sua configuração conteria dois perfis de recorrência, um para correr aos fins de semana e outro nos dias úteis.
    A configuração é assim:

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

    A definição anterior mostra que cada perfil de recorrência tem um calendário. Este horário determina quando o perfil começa a funcionar. O perfil para quando é hora de executar outro perfil.

    Por exemplo, no cenário anterior, o "WeekdayProfile" tem início marcado para segunda-feira, às 00:00. Significa que este perfil começa a ser publicado na segunda-feira às 00:00. Continua até sábado às 00:00, altura em que o "weekendProfile" está agendado para começar a funcionar.

    **Exemplo 2: Horário de trabalho**
    
    Digamos que quer ter um limiar métrico durante o horário comercial (9:00 às 17:00), e um diferente para todas as outras vezes. A definição seria assim:
    
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
    
    A configuração anterior mostra que o "businessHoursProfile" começa a funcionar na segunda-feira às 9:00, e continua até às 17:00. É quando "nonBusinessHoursProfile" começa a funcionar. O "nonBusinessHoursProfile" decorre até às 9:00 de terça-feira, e depois o "businessHoursProfile" assume novamente. Isto repete-se até sexta-feira às 17:00. Nessa altura, o "nonBusinessHoursProfile" decorre até segunda-feira às 9:00 da manhã.
    
> [!Note]
> A interface de utilizador autoescala no portal Azure aplica os tempos finais para perfis de recorrência e começa a executar o perfil padrão da definição de Autoscale entre perfis de recorrência.
    
## <a name="autoscale-evaluation"></a>Avaliação de autoescala
Dado que as definições de Autoscale podem ter vários perfis, e cada perfil pode ter várias regras métricas, é importante entender como uma definição de Autoscale é avaliada. Cada vez que o trabalho de Autoscale funciona, começa por escolher o perfil que é aplicável. Em seguida, a Autoscale avalia os valores mínimos e máximos, e quaisquer regras métricas no perfil, e decide se uma ação de escala é necessária.

### <a name="which-profile-will-autoscale-pick"></a>Qual o perfil que a Autoscale escolherá?

A autoescala utiliza a seguinte sequência para escolher o perfil:
1. Procura primeiro qualquer perfil de data fixa que esteja configurado para ser executado agora. Se houver, a Autoscale executa-o. Se houver vários perfis de data fixa que devem ser executados, o Autoscale seleciona o primeiro.
2. Se não houver perfis de data fixa, a Autoscale analisa os perfis de recorrência. Se for encontrado um perfil de recorrência, executa-o.
3. Se não houver data fixa ou perfis de recorrência, a Autoscale executa o perfil regular.

### <a name="how-does-autoscale-evaluate-multiple-rules"></a>Como avalia a Autoscale várias regras?

Depois de a Autoscale determinar qual o perfil a executar, avalia todas as regras de escala no perfil (estas são regras com **direção = "Aumentar").**

Se uma ou mais regras de escala forem desencadeadas, a Autoscale calcula a nova capacidade determinada pela **escalaAcção** de cada uma dessas regras. Em seguida, escala para o máximo dessas capacidades, para garantir a disponibilidade de serviço.

Por exemplo, digamos que há uma escala de máquina virtual definida com uma capacidade de corrente de 10. Há duas regras de escala: uma que aumenta a capacidade em 10%, e outra que aumenta a capacidade em 3 contagens. A primeira regra resultaria numa nova capacidade de 11, e a segunda regra resultaria numa capacidade de 13. Para garantir a disponibilidade do serviço, a Autoscale escolhe a ação que resulta na capacidade máxima, para que a segunda regra seja escolhida.

Se não forem ativadas regras de escala, a Autoscale avalia todas as regras de escala (regras com **direção = "Diminuição").** A autoescala só toma uma medida de escala se todas as regras de escala forem ativadas.

A autoescala calcula a nova capacidade determinada pela **escalaAcção** de cada uma dessas regras. Em seguida, escolhe a ação de escala que resulta no máximo dessas capacidades para garantir a disponibilidade do serviço.

Por exemplo, digamos que há uma escala de máquina virtual definida com uma capacidade de corrente de 10. Há duas regras de escala: uma que diminui a capacidade em 50%, e outra que diminui a capacidade em 3 contagens. A primeira regra resultaria numa nova capacidade de 5, e a segunda regra resultaria numa capacidade de 7. Para garantir a disponibilidade do serviço, a Autoscale escolhe a ação que resulta na capacidade máxima, para que a segunda regra seja escolhida.

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre a Autoscale referindo-se ao seguinte:

* [Descrição geral do dimensionamento automático](./autoscale-overview.md)
* [Azure Monitor autoescala métricas comuns](./autoscale-common-metrics.md)
* [Melhores práticas para o dimensionamento automático do Azure Monitor](./autoscale-best-practices.md)
* [Utilize ações de escala automática para enviar notificações de alerta de email e webhook](./autoscale-webhook-email.md)
* [API de REPOUSO de Autoescala](/rest/api/monitor/autoscalesettings)

