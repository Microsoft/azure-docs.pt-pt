---
title: Autoescala avançada usando máquinas virtuais Azure
description: Utiliza conjuntos de escala de Gestor de Recursos e VM com múltiplas regras e perfis, que enviam URLs de e-mail e chamadas webhook com ações de escala.
author: mimckitt
ms.author: mimckitt
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.subservice: autoscale
ms.date: 06/25/2020
ms.reviewer: jushiman
ms.custom: mimckitt
ms.openlocfilehash: 26cfdf05847b01d28a8574483acc89cfeced0ffa
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101717745"
---
# <a name="advanced-autoscale-configuration-using-resource-manager-templates-for-vm-scale-sets"></a>Configuração avançada de autoescalação utilizando modelos de gestor de recursos para conjuntos de escala VM
Pode escalar e escalar em conjuntos de escala de máquina virtual com base em limiares de métrica de desempenho, por um horário recorrente ou por uma data específica. Também pode configurar notificações de email e webhook para ações de escala. Este walkthrough mostra um exemplo de configuração de todos estes objetos usando um modelo de Gestor de Recursos num Conjunto de Escala VM.

> [!NOTE]
> Embora este walkthrough explique os passos para conjuntos de escala VM, a mesma informação aplica-se a [serviços](https://azure.microsoft.com/services/cloud-services/)de cloud autoescaling , Serviço de [Aplicações - Web Apps](https://azure.microsoft.com/services/app-service/web/)e [serviços de gestão de API](../../api-management/api-management-key-concepts.md) Para uma definição de escala simples dentro/fora de uma escala VM definida com base numa métrica de desempenho simples, como CPU, consulte os documentos [Linux](../../virtual-machine-scale-sets/tutorial-autoscale-cli.md) e [Windows](../../virtual-machine-scale-sets/tutorial-autoscale-powershell.md)
>
>

## <a name="walkthrough"></a>Instruções
Nesta passagem de walkthrough, utilizamos [o Azure Resource Explorer](https://resources.azure.com/) para configurar e atualizar a definição de escala automática para um conjunto de escala. O Azure Resource Explorer é uma forma fácil de gerir os recursos do Azure através de modelos de Gestor de Recursos. Se é novo na ferramenta Azure Resource Explorer, leia [esta introdução](https://azure.microsoft.com/blog/azure-resource-explorer-a-new-tool-to-discover-the-azure-api/).

1. Desloque um conjunto de escala nova com uma definição de autoescala básica. Este artigo utiliza o da Galeria Azure QuickStart, que tem uma escala windows definida com um modelo básico de escala automática. Os conjuntos de escala linux funcionam da mesma forma.
2. Após a criação do conjunto de escalas, navegue para o recurso definido em escala a partir do Azure Resource Explorer. Vê o seguinte no nó Microsoft.Insights.

    ![Explorador Azure](media/autoscale-virtual-machine-scale-sets/azure_explorer_navigate.png)

    A execução do modelo criou uma definição de autoescala predefinida com o nome **'autoscalewad'**. No lado direito, pode ver a definição completa desta definição de autoescala. Neste caso, a definição de autoescala padrão vem com uma regra de escala e escala de cpu% baseada na escala.  

3. Pode agora adicionar mais perfis e regras com base no horário ou requisitos específicos. Criamos uma definição de autoescala com três perfis. Para compreender perfis e regras em autoescala, reveja [as Melhores Práticas de Autoscale.](autoscale-best-practices.md)  

    | Regras & perfis | Descrição |
    |--- | --- |
    | **Perfil** |**Baseado em desempenho/métrica** |
    | Regra |Contagem de mensagens de ônibus de serviço > x |
    | Regra |Contagem de mensagens de ônibus de serviço < y |
    | Regra |CPU% > n |
    | Regra |CPU% < p |
    | **Perfil** |**Horário da manhã do dia da semana (sem regras)** |
    | **Perfil** |**Dia de lançamento do produto (sem regras)** |

4. Aqui está um cenário hipotético de escala que usamos para este walk-through.

   * **Base de carga** - Eu gostaria de escalar para fora ou em com base na carga na minha aplicação hospedada no meu conjunto de escala.*
   * **Tamanho da fila da mensagem** - Uso uma fila de autocarros de serviço para as mensagens recebidas da minha aplicação. Uso a contagem de mensagens da fila e CPU% e configurar um perfil predefinido para desencadear uma ação de escala se uma contagem de mensagens ou CPU atingir o limiar.\*
   * **Hora da semana e do dia** - Quero um perfil semanal de "hora do dia" chamado "Weekday Morning Hours". Com base em dados históricos, sei que é melhor ter um certo número de instâncias em VM para lidar com a carga da minha aplicação durante este tempo.\*
   * **Datas Especiais** - Adicionei um perfil de "Dia de Lançamento do Produto". Planeio com antecedência datas específicas para que a minha aplicação esteja pronta para lidar com os anúncios de marketing devidos à carga e quando colocarmos um novo produto na aplicação.\*
   * *Os dois últimos perfis também podem ter outras regras baseadas em métricas de desempenho dentro delas. Neste caso, decidi não ter uma e, em vez disso, confiar nas regras baseadas na métrica de desempenho padrão. As regras são opcionais para os perfis recorrentes e baseados em datas.*

     A priorização dos perfis e regras do motor de autoescalação também é captada no artigo de [boas práticas auto-caling.](autoscale-best-practices.md)
     Para obter uma lista de métricas comuns para autoescala, consulte [métricas comuns para autoescala](autoscale-common-metrics.md)

5. Certifique-se de que está no modo **de Ler/Escrever** no Explorador de Recursos

    ![Autoescala, definição de autoescala padrão](media/autoscale-virtual-machine-scale-sets/autoscalewad.png)

6. Clique em Editar. **Substitua** o elemento 'perfis' na definição de autoescala pela seguinte configuração:

    ![A imagem mostra o elemento de perfis.](media/autoscale-virtual-machine-scale-sets/profiles.png)

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
    Para campos suportados e seus valores, consulte [a documentação autoscale REST API](/rest/api/monitor/autoscalesettings). Agora, a sua definição de autoescala contém os três perfis explicados anteriormente.

7. Por último, veja a secção de **notificação** auto-escala. As notificações de escala automática permitem-lhe fazer três coisas quando uma escala ou em ação é desencadeada com sucesso.
   - Notifique os administradores e coadministradores da sua subscrição
   - Envie um e-mail com um conjunto de utilizadores
   - Desencadeie uma chamada webhook. Quando disparado, este webhook envia metadados sobre a condição de autoscalagem e o recurso definido pela escala. Para saber mais sobre a carga útil do webhook de escala automática, consulte [o Configure Webhook & notificações de e-mail para autoescalação](autoscale-webhook-email.md).

   Adicione o seguinte à definição de Autoscale substituindo o seu elemento **de notificação** cujo valor é nulo

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

   Carregar **No** Botão Desembaraciado no Explorador de Recursos para atualizar a definição de escala automática.

Atualizou uma definição de escala automática num conjunto de Escala VM para incluir perfis de várias escalas e notificações de escala.

## <a name="next-steps"></a>Passos Seguintes
Use estes links para saber mais sobre autoscaling.

[Escala automática de resolução de problemas com conjuntos de escala de máquina virtual](../../virtual-machine-scale-sets/virtual-machine-scale-sets-troubleshoot.md)

[Métricas comuns para autoescala](autoscale-common-metrics.md)

[Boas Práticas para Azure Autoscale](autoscale-best-practices.md)

[Gerir autoescala usando PowerShell](../powershell-samples.md#create-and-manage-autoscale-settings)

[Gerir autoescala usando CLI](../cli-samples.md#autoscale)

[Configurar Webhook & notificações de e-mail para autoescala](autoscale-webhook-email.md)

Referência do modelo [Microsoft.Insights/Autoscalesettings](/azure/templates/microsoft.insights/autoscalesettings)