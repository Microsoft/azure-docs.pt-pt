---
title: Dimensionamento automático avançado usando máquinas virtuais do Azure
description: Usa o Gerenciador de recursos e conjuntos de dimensionamento de VM com várias regras e perfis que enviam email e chamam URLs de webhook com ações de escala.
ms.topic: conceptual
ms.date: 02/22/2016
ms.subservice: autoscale
ms.openlocfilehash: e22806ff94ce2eb830bb6918bfc7f80e5ad3ba0a
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75364225"
---
# <a name="advanced-autoscale-configuration-using-resource-manager-templates-for-vm-scale-sets"></a>Configuração avançada de dimensionamento automático usando modelos do Resource Manager para conjuntos de dimensionamento de VM
Você pode escalar horizontalmente e escalar horizontalmente em conjuntos de dimensionamento de máquinas virtuais com base nos limites de métricas de desempenho, por um agendamento recorrente ou por uma data específica. Você também pode configurar notificações por email e webhook para ações de escala. Este tutorial mostra um exemplo de configuração de todos esses objetos usando um modelo do Resource Manager em um conjunto de dimensionamento de VM.

> [!NOTE]
> Embora este passo a passos explique as etapas para conjuntos de escala de VM, as mesmas informações se aplicam ao dimensionamento automático de [serviços de nuvem](https://azure.microsoft.com/services/cloud-services/), [serviço de aplicativo-aplicativos Web](https://azure.microsoft.com/services/app-service/web/)e [serviços de gerenciamento de API](https://docs.microsoft.com/azure/api-management/api-management-key-concepts) para uma configuração simples de expansão/saída em um conjunto de dimensionamento de VM baseado em uma métrica de desempenho simples, como a CPU, consulte os documentos do [Linux](../../virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-cli.md) e do [Windows](../../virtual-machine-scale-sets/tutorial-autoscale-powershell.md)
>
>

## <a name="walkthrough"></a>Instruções
Neste tutorial, usamos [Azure Resource Explorer](https://resources.azure.com/) para configurar e atualizar a configuração de dimensionamento automático para um conjunto de dimensionamento. Azure Resource Explorer é uma maneira fácil de gerenciar recursos do Azure por meio de modelos do Resource Manager. Se você for novo na ferramenta de Azure Resource Explorer, leia [esta introdução](https://azure.microsoft.com/blog/azure-resource-explorer-a-new-tool-to-discover-the-azure-api/).

1. Implante um novo conjunto de dimensionamento com uma configuração básica de dimensionamento automático. Este artigo usa um da Galeria de início rápido do Azure, que tem um conjunto de dimensionamento do Windows com um modelo básico de dimensionamento automático. Os conjuntos de dimensionamento do Linux funcionam da mesma maneira.
2. Depois que o conjunto de dimensionamento for criado, navegue até o recurso de conjunto de dimensionamento de Azure Resource Explorer. Você verá o seguinte no nó Microsoft. insights.

    ![Gerenciador do Azure](media/autoscale-virtual-machine-scale-sets/azure_explorer_navigate.png)

    A execução do modelo criou uma configuração padrão de dimensionamento automático com o nome **' autoscalewad '** . No lado direito, você pode exibir a definição completa dessa configuração de dimensionamento automático. Nesse caso, a configuração padrão de dimensionamento automático vem com uma regra de expansão e redução horizontal baseada em% de CPU.  

3. Agora você pode adicionar mais perfis e regras com base no agendamento ou em requisitos específicos. Criamos uma configuração de dimensionamento automático com três perfis. Para entender perfis e regras em dimensionamento automático, examine [as práticas recomendadas de dimensionamento automático](autoscale-best-practices.md).  

    | Perfis & regras | Descrição |
    |--- | --- |
    | **Perfil** |**Baseado em desempenho/métrica** |
    | Regra |Contagem de mensagens da fila do barramento de serviço > x |
    | Regra |Contagem de mensagens da fila do barramento de serviço < y |
    | Regra |% > N de CPU |
    | Regra |% De CPU < p |
    | **Perfil** |**Horas da manhã da semana (sem regras)** |
    | **Perfil** |**Dia de lançamento do produto (sem regras)** |

4. Aqui está um cenário de dimensionamento hipotético que usamos para este passo a passo.

   * **Baseado em carga** – eu gostaria de escalar horizontalmente ou com base na carga no meu aplicativo hospedado no meu conjunto de dimensionamento. *
   * **Tamanho da fila de mensagens** -eu uso uma fila do barramento de serviço para as mensagens de entrada para o meu aplicativo. Eu uso a contagem de mensagens da fila e a CPU% e configuro um perfil padrão para disparar uma ação de escala se uma contagem de mensagens ou CPU atingir o limite.\*
   * **Hora da semana e dia** – quero um perfil baseado em ' hora do dia ' recorrente semanal chamado ' dias da manhã da semana '. Com base nos dados históricos, sei que é melhor ter determinado número de instâncias de VM para lidar com a carga do meu aplicativo durante esse tempo.\*
   * **Datas especiais** -adicionei um perfil de "dia de lançamento do produto". Pretendo em frente as datas específicas para que meu aplicativo esteja pronto para lidar com a carga devido aos comunicados de marketing e quando colocamos um novo produto no aplicativo.\*
   * *Os dois últimos perfis também podem ter outras regras baseadas em métrica de desempenho dentro deles. Nesse caso, decidi não ter um e, em vez disso, contar com as regras baseadas em métrica de desempenho padrão. As regras são opcionais para os perfis de recorrência e baseados em data.*

     A priorização do mecanismo de autoescala dos perfis e das regras também é capturada no artigo [práticas recomendadas de dimensionamento](autoscale-best-practices.md) automático.
     Para obter uma lista de métricas comuns para dimensionamento automático, consulte [métricas comuns para dimensionamento automático](autoscale-common-metrics.md)

5. Verifique se você está no modo de **leitura/gravação** no Gerenciador de recursos

    ![Autoscalewad, configuração de dimensionamento automático padrão](media/autoscale-virtual-machine-scale-sets/autoscalewad.png)

6. Clique em Editar. **Substitua** o elemento ' Profiles ' na configuração de dimensionamento automático pela seguinte configuração:

    ![profiles](media/autoscale-virtual-machine-scale-sets/profiles.png)

    ```
    {
            "name": "Perf_Based_Scale",
            "capacity": {
              "minimum": "2",
              "maximum": "12",
              "default": "2"
            },
            "rules": [
              {
                "metricTrigger": {
                  "metricName": "MessageCount",
                  "metricNamespace": "",
                  "metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.ServiceBus/namespaces/mySB/queues/myqueue",
                  "timeGrain": "PT5M",
                  "statistic": "Average",
                  "timeWindow": "PT5M",
                  "timeAggregation": "Average",
                  "operator": "GreaterThan",
                  "threshold": 10
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
                  "metricName": "MessageCount",
                  "metricNamespace": "",
                  "metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.ServiceBus/namespaces/mySB/queues/myqueue",
                  "timeGrain": "PT5M",
                  "statistic": "Average",
                  "timeWindow": "PT5M",
                  "timeAggregation": "Average",
                  "operator": "LessThan",
                  "threshold": 3
                },
                "scaleAction": {
                  "direction": "Decrease",
                  "type": "ChangeCount",
                  "value": "1",
                  "cooldown": "PT5M"
                }
              },
              {
                "metricTrigger": {
                  "metricName": "Percentage CPU",
                  "metricNamespace": "",
                  "metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.Compute/virtualMachineScaleSets/<this_vmss_name>",
                  "timeGrain": "PT5M",
                  "statistic": "Average",
                  "timeWindow": "PT30M",
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
                  "metricNamespace": "",
                  "metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.Compute/virtualMachineScaleSets/<this_vmss_name>",
                  "timeGrain": "PT5M",
                  "statistic": "Average",
                  "timeWindow": "PT30M",
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
          },
          {
            "name": "Weekday_Morning_Hours_Scale",
            "capacity": {
              "minimum": "4",
              "maximum": "12",
              "default": "4"
            },
            "rules": [],
            "recurrence": {
              "frequency": "Week",
              "schedule": {
                "timeZone": "Pacific Standard Time",
                "days": [
                  "Monday",
                  "Tuesday",
                  "Wednesday",
                  "Thursday",
                  "Friday"
                ],
                "hours": [
                  6
                ],
                "minutes": [
                  0
                ]
              }
            }
          },
          {
            "name": "Product_Launch_Day",
            "capacity": {
              "minimum": "6",
              "maximum": "20",
              "default": "6"
            },
            "rules": [],
            "fixedDate": {
              "timeZone": "Pacific Standard Time",
              "start": "2016-06-20T00:06:00Z",
              "end": "2016-06-21T23:59:00Z"
            }
          }
    ```
    Para obter os campos com suporte e seus valores, consulte [documentação da API REST de dimensionamento automático](https://msdn.microsoft.com/library/azure/dn931928.aspx). Agora, a configuração de dimensionamento automático contém os três perfis explicados anteriormente.

7. Por fim, examine a seção **notificação** de dimensionamento automático. As notificações de dimensionamento automático permitem que você faça três coisas quando uma escala horizontal ou em uma ação é disparada com êxito.
   - Notifique o administrador e os coadministradores da sua assinatura
   - Enviar um conjunto de usuários por email
   - Disparar uma chamada de webhook. Quando acionado, este webhook envia metadados sobre a condição de dimensionamento automático e o recurso de conjunto de dimensionamento. Para saber mais sobre a carga de webhook de dimensionamento automático, consulte [Configurar webhook & notificações por email para dimensionamento automático](autoscale-webhook-email.md).

   Adicione o seguinte à configuração de dimensionamento automático substituindo seu elemento de **notificação** cujo valor é nulo

   ```
   "notifications": [
      {
        "operation": "Scale",
        "email": {
          "sendToSubscriptionAdministrator": true,
          "sendToSubscriptionCoAdministrators": false,
          "customEmails": [
              "user1@mycompany.com",
              "user2@mycompany.com"
              ]
        },
        "webhooks": [
          {
            "serviceUri": "https://foo.webhook.example.com?token=abcd1234",
            "properties": {
              "optional_key1": "optional_value1",
              "optional_key2": "optional_value2"
            }
          }
        ]
      }
    ]

   ```

   Pressione o botão **colocar** no Gerenciador de recursos para atualizar a configuração de dimensionamento automático.

Você atualizou uma configuração de dimensionamento automático em um conjunto de dimensionamento de VM para incluir vários perfis de escala e notificações de escala.

## <a name="next-steps"></a>Próximos Passos
Use estes links para saber mais sobre o dimensionamento automático.

[Solucionar problemas de dimensionamento automático com conjuntos de escala de máquina virtual](../../virtual-machine-scale-sets/virtual-machine-scale-sets-troubleshoot.md)

[Métricas comuns para dimensionamento automático](autoscale-common-metrics.md)

[Práticas recomendadas para o dimensionamento automático do Azure](autoscale-best-practices.md)

[Gerenciar o dimensionamento automático usando o PowerShell](../../azure-monitor/platform/powershell-quickstart-samples.md#create-and-manage-autoscale-settings)

[Gerenciar o dimensionamento automático usando a CLI](cli-samples.md#autoscale)

[Configurar webhook & notificações por email para dimensionamento automático](autoscale-webhook-email.md)

Referência de modelo [Microsoft. insights/autoscalesettings](/azure/templates/microsoft.insights/autoscalesettings)
