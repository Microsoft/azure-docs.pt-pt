---
title: Dimensionamento automático avançado com máquinas virtuais do Azure
description: Utiliza o Gestor de recursos e conjuntos de dimensionamento de VM com várias regras e perfis que enviem e-mail e chamam os URLs de webhook com ações de dimensionamento.
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 02/22/2016
ms.author: ancav
ms.subservice: autoscale
ms.openlocfilehash: 6da653bc94c8b549282ab9124dba23b08771c5f1
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58080782"
---
# <a name="advanced-autoscale-configuration-using-resource-manager-templates-for-vm-scale-sets"></a>Configuração do dimensionamento automático avançado com modelos do Resource Manager para conjuntos de dimensionamento de VM
Pode reduzir e aumentar horizontalmente em conjuntos de dimensionamento de Máquina Virtual com base nos limiares de métricas de desempenho, por uma agenda periódica ou por uma data específica. Também pode configurar notificações de e-mail e webhook para ações de dimensionamento. Estas instruções mostram um exemplo de configuração de todos esses objetos usando um modelo do Resource Manager num conjunto de dimensionamento de VM.

> [!NOTE]
> Embora estas instruções explica os passos para conjuntos de dimensionamento de VM, as mesmas informações aplica-se para dimensionamento automático [serviços Cloud](https://azure.microsoft.com/services/cloud-services/), [serviço de aplicações - aplicações Web](https://azure.microsoft.com/services/app-service/web/), e [deserviçosdegestãodeAPI](https://docs.microsoft.com/azure/api-management/api-management-key-concepts) Para um dimensionamento simples de entrada/saída de definição de um conjunto de dimensionamento de VM com base numa métrica de desempenho simples, tais como CPU, consulte a [Linux](../../virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-cli.md) e [Windows](../../virtual-machine-scale-sets/tutorial-autoscale-powershell.md) documentos
>
>

## <a name="walkthrough"></a>Instruções
Nestas instruções, utilizamos [Explorador de recursos do Azure](https://resources.azure.com/) para configurar e atualizar a definição de dimensionamento automático para um conjunto de dimensionamento. Explorador de recursos do Azure é uma forma fácil de gerir recursos do Azure através de modelos do Resource Manager. Se estiver familiarizado com a ferramenta do Explorador de recursos do Azure, leia [esta introdução](https://azure.microsoft.com/blog/azure-resource-explorer-a-new-tool-to-discover-the-azure-api/).

1. Implemente um novo conjunto de dimensionamento com uma definição de dimensionamento automático básicas. Este artigo utiliza um da Galeria de início rápido do Azure, que tem um Windows conjunto de dimensionamento com um modelo de dimensionamento automático básicas. Conjuntos de dimensionamento do Linux funcionam da mesma maneira.
2. Depois do conjunto de dimensionamento é criado, navegue para o recurso de conjunto de dimensionamento do Azure Resource Explorer. Consulte o seguinte no nó de Microsoft. insights.

    ![Explorador do Azure](media/autoscale-virtual-machine-scale-sets/azure_explorer_navigate.png)

    A execução do modelo tem criado uma definição de dimensionamento automático de padrão com o nome **'autoscalewad'**. No lado direito, pode ver a definição completa desta definição de dimensionamento automático. Neste caso, a predefinição de dimensionamento automático é fornecido com uma regra de aumento horizontal e a redução horizontal da % com base de CPU.  

3. Agora pode adicionar mais regras com base na agenda ou requisitos específicos e perfis. Vamos criar uma definição de dimensionamento automático com três perfis. Para compreender os perfis e regras de dimensionamento automático, reveja [melhores práticas de dimensionamento automático](autoscale-best-practices.md).  

    | R & egras de perfis | Descrição |
    |--- | --- |
    | **Perfil** |**/ Métrica de desempenho com base em** |
    | Regra |Contagem de mensagens da fila de barramento de serviço > x |
    | Regra |Contagem de mensagens da fila de barramento de serviço < y |
    | Regra |% De CPU > n |
    | Regra |CPU% < p |
    | **Perfil** |**Dia da semana horas da manhã (não há regras)** |
    | **Perfil** |**Dia de lançamento do produto (não há regras)** |

4. Eis um cenário hipotético de dimensionamento que utilizamos para este passo a passo.

   * **Carga com base** -gostaria aumente horizontalmente ou com base na carga no meu aplicativo hospedado em meu set.* de dimensionamento
   * **O tamanho da fila de mensagens** -posso utilizar uma fila do Service Bus para as mensagens recebidas para meu aplicativo. Uso de contagem de mensagens da fila e a % de CPU e configurar um perfil predefinido para acionar uma ação de dimensionamento se qualquer uma das contagem de mensagens ou CPU atinge o limiar.\*
   * **Hora do dia e semana** -desejo ter um perfil de "Hora do dia", com base periódica semanal denominado "Horas da manhã de dia da semana". Com base nos dados históricos, eu sei que é melhor ter determinado número de instâncias de VM para processar a carga de meu aplicativo durante este período.\*
   * **Datas especiais** -, Adicionei um perfil de 'Dia de lançamento de produto'. Eu planeie com antecedência para datas específicas, para que meu aplicativo está pronto para processar a carga devido anúncios de marketing e quando o colocamos um novo produto na aplicação.\*
   * *Os dois últimos perfis também podem ter outras métricas baseada em regras de desempenho dentro dos mesmos. Neste caso, optei por não ter um e em vez disso, contar com a métrica de desempenho predefinido com base em regras. As regras são opcionais para os perfis de recorrentes e baseado na data.*

     Priorização do mecanismo de dimensionamento automático dos perfis e as regras também é capturada no [melhores práticas de dimensionamento automático](autoscale-best-practices.md) artigo.
     Para obter uma lista de métricas comuns do dimensionamento automático, consulte [métricas comuns do dimensionamento automático](autoscale-common-metrics.md)

5. Certifique-se estiver a utilizar o **leitura/escrita** modo no Explorador de recursos

    ![Autoscalewad, definição de dimensionamento automático predefinidos](media/autoscale-virtual-machine-scale-sets/autoscalewad.png)

6. Clique em Editar. **Substitua** o elemento "perfis" na definição de dimensionamento automático com a seguinte configuração:

    ![Perfis](media/autoscale-virtual-machine-scale-sets/profiles.png)

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
    Para campos suportados e os respetivos valores, consulte [documentação da API de REST de dimensionamento automático](https://msdn.microsoft.com/library/azure/dn931928.aspx). Agora sua definição de dimensionamento automático contém três perfis explicados anteriormente.

7. Por fim, examinaremos a dimensionamento automático **notificação** secção. Notificações de dimensionamento automático permitem-lhe fazer três coisas quando um Escalamento horizontal ou em ação com êxito é acionado.
   - Notificar o administrador e coadministradores da sua subscrição
   - Um conjunto de utilizadores de e-mail
   - Acione uma chamada webhook. Quando acionado, este webhook envia os metadados sobre a condição de dimensionamento automático e o conjunto de dimensionamento recursos. Para saber mais sobre o payload do webhook de dimensionamento automático, veja [configurar o Webhook e notificações de E-Mail do dimensionamento automático](autoscale-webhook-email.md).

   Adicione o seguinte ao substituir de definição de dimensionamento automático sua **notificação** elemento cujo valor é nulo

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

   Pressionar **colocar** botão no Explorador de recursos para atualizar a definição de dimensionamento automático.

Atualizar uma definição de dimensionamento automático num conjunto para incluir vários perfis de dimensionamento e dimensionar as notificações de dimensionamento de VM.

## <a name="next-steps"></a>Próximos Passos
Utilize estas ligações para saber mais sobre dimensionamento automático.

[Resolver problemas de dimensionamento automático com conjuntos de dimensionamento de máquinas virtuais](../../virtual-machine-scale-sets/virtual-machine-scale-sets-troubleshoot.md)

[Métricas comuns do dimensionamento automático](autoscale-common-metrics.md)

[Melhores práticas do dimensionamento automático do Azure](autoscale-best-practices.md)

[Gerir o dimensionamento automático com o PowerShell](../../azure-monitor/platform/powershell-quickstart-samples.md#create-and-manage-autoscale-settings)

[Gerir o dimensionamento automático com a CLI](cli-samples.md#autoscale)

[Configurar o Webhook e notificações de E-Mail do dimensionamento automático](autoscale-webhook-email.md)

[Insights/autoscalesettings](/azure/templates/microsoft.insights/autoscalesettings) referência de modelo
