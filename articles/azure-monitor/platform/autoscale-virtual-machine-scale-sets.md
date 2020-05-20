---
title: Escala automática avançada usando máquinas virtuais Azure
description: Utiliza conjuntos de escala de Gestor de Recursos e VM com múltiplas regras e perfis, que enviam URLs de e-mail e call webhook com ações de escala.
author: mimckitt
ms.author: mimckitt
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.subservice: autoscale
ms.date: 02/22/2016
ms.reviewer: jushiman
ms.custom: mimckitt
ms.openlocfilehash: 65182ce16b8785a3a0e497b3ed8003fa9e7b67bc
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83655424"
---
# <a name="advanced-autoscale-configuration-using-resource-manager-templates-for-vm-scale-sets"></a>Configuração avançada de escala automática usando modelos de Gestor de Recursos para conjuntos de escala VM
Pode escalar e escalar em conjuntos de escala de máquinavirtual com base em limiares métricos de desempenho, por um horário recorrente, ou por uma data específica. Também pode configurar notificações de e-mail e webhook para ações de escala. Este walkthrough mostra um exemplo de configurar todos estes objetos usando um modelo de Gestor de Recursos em um Conjunto de Escala VM.

> [!NOTE]
> Enquanto este walkthrough explica os passos para Conjuntos de Escala VM, a mesma informação aplica-se aos [serviços](https://azure.microsoft.com/services/cloud-services/)de cloud de autodimensionamento, Serviço de [Aplicações - Aplicações Web](https://azure.microsoft.com/services/app-service/web/)e serviços de [Gestão API](https://docs.microsoft.com/azure/api-management/api-management-key-concepts) Para uma simples escala dentro/fora configurando um Conjunto de Escala VM baseado numa métrica de desempenho simples como CPU, consulte os documentos [Linux](../../virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-cli.md) e [Windows](../../virtual-machine-scale-sets/tutorial-autoscale-powershell.md)
>
>

## <a name="walkthrough"></a>Instruções
Neste passeio, utilizamos o [Azure Resource Explorer](https://resources.azure.com/) para configurar e atualizar a definição de escala automática para um conjunto de escala. O Azure Resource Explorer é uma forma fácil de gerir os recursos do Azure através de modelos de Gestor de Recursos. Se é novo na ferramenta Azure Resource Explorer, leia [esta introdução.](https://azure.microsoft.com/blog/azure-resource-explorer-a-new-tool-to-discover-the-azure-api/)

1. Implementar um novo conjunto de escala com uma definição básica de escala automática. Este artigo utiliza o da Galeria QuickStart Azure, que tem um conjunto de escala Windows com um modelo básico de escala automática. Os conjuntos de escala linux funcionam da mesma forma.
2. Depois de criado o conjunto de escala, navegue até ao recurso conjunto de escala do Azure Resource Explorer. Vê o seguinte sob o nó Microsoft.Insights.

    ![Explorador Azure](media/autoscale-virtual-machine-scale-sets/azure_explorer_navigate.png)

    A execução do modelo criou uma definição de escala automática padrão com o nome **'autoscalewad'.** Do lado direito, pode ver a definição completa desta definição de escala automática. Neste caso, a definição de escala automática padrão vem com uma regra de escala baseada em CPU% e regra de escala.  

3. Agora pode adicionar mais perfis e regras com base no horário ou requisitos específicos. Criamos uma definição de escala automática com três perfis. Para compreender perfis e regras em escala automática, reveja as [Melhores Práticas de Escala Automática.](autoscale-best-practices.md)  

    | Regras de & de perfis | Descrição |
    |--- | --- |
    | **Perfil** |**Desempenho/base métrica** |
    | Regra |Contagem de mensagens de fila de ônibus de serviço > x |
    | Regra |Contagem de mensagens de fila de ônibus de serviço < y |
    | Regra |CPU% > n |
    | Regra |CPU% < p |
    | **Perfil** |**Horário matinal da semana (sem regras)** |
    | **Perfil** |**Dia de Lançamento do Produto (sem regras)** |

4. Aqui está um cenário hipotético de escala que usamos para este walk-through.

   * **Baseado em carga** - Eu gostaria de escalar para fora ou em com base na carga na minha aplicação hospedada no meu conjunto de escala.*
   * **Tamanho** da fila da mensagem - Uso uma fila de ônibus de serviço para as mensagens de entrada na minha aplicação. Uso a contagem de mensagens da fila e o CPU% e configuro um perfil predefinido para desencadear uma ação de escala se uma contagem de mensagens ou CPU atingir o limiar.\*
   * **Hora da semana e do dia** - Quero um perfil semanal recorrente baseado em "hora do dia" chamado "Weekday Morning Hours". Com base em dados históricos, sei que é melhor ter um certo número de casos vm para lidar com a carga da minha aplicação durante este tempo.\*
   * **Datas Especiais** - Adicionei um perfil de "Dia de Lançamento de Produto". Planeio com antecedência datas específicas para que a minha aplicação esteja pronta para lidar com os anúncios de marketing devidos e quando colocamos um novo produto na aplicação.\*
   * *Os dois últimos perfis também podem ter outras regras baseadas em métricas de desempenho dentro deles. Neste caso, decidi não ter um e, em vez disso, confiar nas regras baseadas na métrica de desempenho padrão. As regras são opcionais para os perfis recorrentes e baseados em datas.*

     A priorização dos perfis e regras do motor de escala automática também é captada no artigo de [boas práticas autoscalcificadas.](autoscale-best-practices.md)
     Para uma lista de métricas comuns para escala automática, consulte [métricas comuns para autoescala](autoscale-common-metrics.md)

5. Certifique-se de que está no modo **Leitura/Escrita** no Explorador de Recursos

    ![Autoscalewad, definição de escala automática padrão](media/autoscale-virtual-machine-scale-sets/autoscalewad.png)

6. Clique em Editar. **Substitua** o elemento 'perfis' na definição de escala automática pela seguinte configuração:

    ![perfis](media/autoscale-virtual-machine-scale-sets/profiles.png)

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
    Para campos apoiados e seus valores, consulte [a documentação DaPI](https://msdn.microsoft.com/library/azure/dn931928.aspx)REST De Escala Automática . Agora a sua definição de escala automática contém os três perfis explicados anteriormente.

7. Por fim, veja a secção de **notificação** de escala automática. As notificações de escala automática permitem-lhe fazer três coisas quando uma escala ou em ação é desencadeada com sucesso.
   - Notifique os administradores e coadministradores da sua subscrição
   - Envie um email para um conjunto de utilizadores
   - Desencadeie uma chamada de webhook. Quando disparado, este webhook envia metadados sobre a condição de autoscalcificação e o recurso de conjunto de escala. Para saber mais sobre a carga útil do webhook de escala automática, consulte [Configure Webhook & Notificações de e-mail para escala automática](autoscale-webhook-email.md).

   Adicione o seguinte à definição de escala automática substituindo o seu elemento de **notificação** cujo valor é nulo

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

   Carregar no botão **Colocar** no Resource Explorer para atualizar a definição de escala automática.

Atualizou uma definição de escala automática numa escala VM definida para incluir perfis de escala múltipla e notificações de escala.

## <a name="next-steps"></a>Passos Seguintes
Use estes links para saber mais sobre autoscalcificação.

[Escala automática de TroubleShoot com conjuntos de escala de máquina virtual](../../virtual-machine-scale-sets/virtual-machine-scale-sets-troubleshoot.md)

[Métricas Comuns para Autoscale](autoscale-common-metrics.md)

[Boas Práticas para a Escala Automática Azure](autoscale-best-practices.md)

[Gerir a escala automática usando powerShell](../../azure-monitor/platform/powershell-quickstart-samples.md#create-and-manage-autoscale-settings)

[Gerir a escala automática utilizando o CLI](cli-samples.md#autoscale)

[Configure Webhook & Notificações de e-mail para escala automática](autoscale-webhook-email.md)

Referência do modelo [Microsoft.Insights/autoscalesettings](/azure/templates/microsoft.insights/autoscalesettings)
