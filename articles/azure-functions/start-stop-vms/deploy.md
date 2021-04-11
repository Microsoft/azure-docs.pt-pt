---
title: Implementar VMs de início/paragem v2 (pré-visualização)
description: Este artigo diz como implementar a função Iniciar/Parar VMs v2 (pré-visualização) para os seus VMs Azure na sua subscrição Azure.
services: azure-functions
ms.subservice: ''
ms.date: 03/29/2021
ms.topic: conceptual
ms.openlocfilehash: 9ca808fffbd26c8837ad9a43447f60e99f89d922
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/01/2021
ms.locfileid: "106112208"
---
# <a name="deploy-startstop-vms-v2-preview"></a>Implementar VMs de início/paragem v2 (pré-visualização)

Execute os passos neste tópico em sequência para instalar a função Iniciar/Parar VMs v2 (pré-visualização). Depois de concluir o processo de configuração, configuure os horários para personalizá-lo às suas necessidades.

## <a name="deploy-feature"></a>Implementar recurso

A implantação é iniciada a partir da organização Start/Stop VMs v2 GitHub [aqui.](https://github.com/microsoft/startstopv2-deployments/blob/main/README.md) Embora esta funcionalidade se destine a gerir todos os seus VMs na sua subscrição em todos os grupos de recursos a partir de uma única implementação dentro da subscrição, pode instalar outra instância da funcionalidade com base no modelo de operações ou requisitos da sua organização. Também pode ser configurado para gerir centralmente VMs em várias subscrições.

Para simplificar a gestão e remoção, recomendamos que implemente VMs de início/paragem v2 (pré-visualização) para um grupo de recursos dedicado.

> [!NOTE]
> Atualmente, esta pré-visualização não suporta especificar uma conta de Armazenamento ou recurso de Insights de Aplicação existente.

1. Abra o seu navegador e navegue para a organização Start/Stop VMs v2 [GitHub](https://github.com/microsoft/startstopv2-deployments/blob/main/README.md).
1. Selecione a opção de implementação com base no ambiente de nuvem Azure em que os VMs Azure são criados. Isto abrirá a página de implementação personalizada do Gestor de Recursos Azure no portal Azure.
1. Se solicitado, inscreva-se no [portal Azure](https://portal.azure.com).
1. Introduza os seguintes valores:

    |Name |Valor |
    |-----|------|
    |Region |Selecione uma região perto de si para obter novos recursos.|
    |Nome do Grupo de Recursos |Especifique o nome do grupo de recursos que conterá os recursos individuais para VMs de início/paragem. |
    |Região do Grupo de Recursos |Especificar a região para o grupo de recursos. Por exemplo, **E.U.A. Central**.|
    |Nome da aplicação de função Azure |Digite um nome que seja válido num caminho URL. O nome que digita é validado para se certificar de que é único em Funções Azure. |
    |Nome de insights de aplicação |Especifique o nome da sua instância Desaparatos de Aplicação que irá conter a análise de VMs de início/paragem. |
    |Região de Insights de Aplicações |Especifique a região para o caso de Insights de Aplicação.|
    |Nome da conta de armazenamento |Especifique o nome da conta de Armazenamento Azure para armazenar telemetria de execução start/stop VMs. |
    |Endereço de E-mail |Especifique um ou mais endereços de e-mail para receber notificações de estado, separados por uma vírgula (,).|

    :::image type="content" source="media/deploy/deployment-template-details.png" alt-text="Configuração de implementação do modelo iniciar/parar VMs":::

1. Selecione **'Rever + criar'** na parte inferior da página.
1. Selecione **Criar** para iniciar a implementação.
1. Selecione o ícone de campainha (notificações) na parte superior do ecrã para ver o estado de implementação. Verá a **implantação em curso.** Aguarde até que a implementação esteja concluída.
1. Selecione **Ir para o grupo de recursos** no painel de notificação. Deverá ver um ecrã semelhante a:

    :::image type="content" source="media/deploy/deployment-results-resource-list.png" alt-text="Lista de recursos de implementação de modelos de início/paragem de VMs":::

## <a name="enable-multiple-subscriptions"></a>Ativar várias subscrições

Após a conclusão da implementação Iniciar/Paragem, execute os seguintes passos para permitir que os VMs de início/paragem v2 (pré-visualização) tomem medidas através de várias subscrições.

1. Copie o valor para o Nome da Aplicação da Função Azure que especificou durante a implementação.

1. No portal, navegue para a sua subscrição secundária. Selecione a subscrição e, em seguida, selecione **Access Control (IAM)**

1. **Selecione Adicionar** e, em seguida, selecione **Adicionar a atribuição de função**.

1. Selecione a **função Contribuinte** da lista de down down **Role.**

1. Introduza o Nome de Aplicação da Função Azure no campo **Selecione.** Selecione o nome da função nos resultados.

1. **Selecione Guardar** para cometer as suas alterações.

## <a name="configure-schedules-overview"></a>Configurar horários visão geral

Para gerir o método de automatização para controlar o início e paragem dos seus VMs, configura uma ou mais das aplicações lógicas incluídas com base nos seus requisitos.

- Agendado - As ações de início e paragem baseiam-se num horário que especifica contra o Azure Resource Manager e os VMs **clássicos. ststv2_vms_Scheduled_start** e **ststv2_vms_Scheduled_stop** configurar o início e paragem programados.

- Sequenciais - As ações de arranque e paragem baseiam-se num cronojo de direcionamento de VMs com etiquetas de sequenciação pré-definidas. Apenas duas tags nomeadas são suportadas - **início de sequência** e **sequências .** **ststv2_vms_Sequenced_start** e **ststv2_vms_Sequenced_stop** configuram o início e a paragem sequenciais.

    > [!NOTE]
    > Este cenário suporta apenas VMs Azure Resource Manager.

- AutoStop - Esta funcionalidade é utilizada apenas para realizar uma ação de stop contra o Azure Resource Manager e vMs clássicos com base na sua utilização de CPU. Também pode ser uma *ação* de tomada programada , que cria alertas em VMs e com base na condição, o alerta é desencadeado para executar a ação de stop.**ststv2_vms_AutoStop** configura a funcionalidade de paragem automática.

Se precisar de horários adicionais, pode duplicar uma das Aplicações Lógicas fornecidas utilizando a opção **Clone** no portal Azure.

:::image type="content" source="media/deploy/logic-apps-clone-option.png" alt-text="Selecione a opção Clone para duplicar uma aplicação lógica":::

## <a name="scheduled-start-and-stop-scenario"></a>Cenário de início e paragem programado

Execute os seguintes passos para configurar a ação programada de início e paragem para O Gestor de Recursos Azure e VMs clássicos. Por exemplo, pode configurar o horário **de ststv2_vms_Scheduled_start** para começar de manhã quando estiver no escritório, e parar todos os VMs através de uma subscrição quando sair do trabalho à noite com base no **horário ststv2_vms_Scheduled_stop.**

Configurar a aplicação lógica para apenas iniciar os VMs é suportado.

Para cada cenário, pode direcionar a ação contra uma ou mais subscrições, grupos de recursos individuais ou múltiplos, e especificar um ou mais VMs numa lista de inclusão ou exclusão. Não é possível especificá-los juntos na mesma aplicação lógica.

1. Inscreva-se no [portal Azure](https://portal.azure.com) e, em seguida, navegue para **aplicações Lógica**.

1. Da lista de aplicações Logic, até configurar o início programado, selecione **ststv2_vms_Scheduled_start**. Para configurar a paragem programada, selecione **ststv2_vms_Scheduled_stop**.

1. Selecione o designer de **aplicativos Logic** a partir do painel da esquerda.

1. Depois de o Designer de Aplicações Lógica aparecer, no painel de designers, selecione **Recorrência** para configurar o calendário da aplicação lógica. Para saber mais sobre as opções específicas de recorrência, consulte [a tarefa recorrente do Agenda](../../connectors/connectors-native-recurrence.md#add-the-recurrence-trigger).

    :::image type="content" source="media/deploy/schedule-recurrence-property.png" alt-text="Configure a frequência de recorrência para a aplicação lógica":::

1. No painel de design, selecione **Função-Tente** configurar as definições do alvo. No organismo de pedido, se pretender gerir VMs em todos os grupos de recursos da subscrição, modifique o organismo de pedidos como mostrado no exemplo seguinte.

    ```json
    {
      "Action": "start",
      "EnableClassic": false,
      "RequestScopes": {
        "ExcludedVMLists": [],
        "Subscriptions": [
          "/subscriptions/12345678-1234-5678-1234-123456781234/"
        ]
     }
    }
    ```

    Especifique várias subscrições na `subscriptions` matriz com cada valor separado por uma vírgula como no exemplo seguinte.

    ```json
    "Subscriptions": [
          "/subscriptions/12345678-1234-5678-1234-123456781234/",
          "/subscriptions/11111111-0000-1111-2222-444444444444/"
        ]
    ```

    No organismo de pedido, se pretender gerir VMs para grupos de recursos específicos, modifique o corpo de pedidos como mostrado no exemplo seguinte. Cada caminho de recurso especificado deve ser separado por uma vírgula. Pode especificar um grupo de recursos ou mais, se necessário.

    Este exemplo também demonstra a exclusão de uma máquina virtual. Pode excluir o VM especificando o caminho do recurso VMs ou por wildcard.

    ```json
    {
      "Action": "start",
      "EnableClassic": false,
      "RequestScopes": {
        "ResourceGroups": [
          "/subscriptions/12345678-1234-5678-1234-123456781234/resourceGroups/rg1/",
          "/subscriptions/11111111-0000-1111-2222-444444444444/resourceGroups/rg2/"
        ],
        "ExcludedVMLists": [
         "/subscriptions/12345678-1111-2222-3333-1234567891234/resourceGroups/vmrg1/providers/Microsoft.Compute/virtualMachines/vm1"
        ]
      }
    }
    ```

    Aqui a ação será realizada em todos os VMs, exceto no nome VM começa com Az e Bz em ambas as subscrições.

    ```json
    {
      "Action": "start",
      "EnableClassic": false,
      "RequestScopes": {
        "ExcludedVMLists": [“Az*”,“Bz*”],
       "Subscriptions": [
          "/subscriptions/12345678-1234-5678-1234-123456781234/",
          "/subscriptions/11111111-0000-1111-2222-444444444444/"
    
        ]
      }
    }
    ```

    No organismo de pedido, se pretender gerir um conjunto específico de VMs dentro da subscrição, modifique o organismo de pedido como mostrado no exemplo seguinte. Cada caminho de recurso especificado deve ser separado por uma vírgula. Pode especificar um VM se necessário.

    ```json
    {
      "Action": "start",
      "EnableClassic": true,
      "RequestScopes": {
        "ExcludedVMLists": [],
        "VMLists": [
          "/subscriptions/12345678-1234-5678-1234-123456781234/resourceGroups/rg1/providers/Microsoft.Compute/virtualMachines/vm1",
          "/subscriptions/12345678-1234-5678-1234-123456781234/resourceGroups/rg3/providers/Microsoft.Compute/virtualMachines/vm2",
          "/subscriptions/11111111-0000-1111-2222-444444444444/resourceGroups/rg2/providers/Microsoft.ClassicCompute/virtualMachines/vm30"
          
        ]
    }
    ```

## <a name="sequenced-start-and-stop-scenario"></a>Cenário de início e paragem sequenciado

Num ambiente que inclui dois ou mais componentes em VMs múltiplos do Azure Resource Manager numa arquitetura de aplicação distribuída, apoiar a sequência em que os componentes são iniciados e parados em ordem é importante.

1. Da lista de aplicações Lógica, até configurar o início sequencial, selecione **ststv2_vms_Sequenced_start**. Para configurar a paragem sequencial, selecione **ststv2_vms_Sequenced_stop**.

1. Selecione o designer de **aplicativos Logic** a partir do painel da esquerda.

1. Depois de o Designer de Aplicações Lógica aparecer, no painel de designers, selecione **Recorrência** para configurar o calendário da aplicação lógica. Para saber mais sobre as opções específicas de recorrência, consulte [a tarefa recorrente do Agenda](../../connectors/connectors-native-recurrence.md#add-the-recurrence-trigger).

    :::image type="content" source="media/deploy/schedule-recurrence-property.png" alt-text="Configure a frequência de recorrência para a aplicação lógica":::

1. No painel de design, selecione **Função-Tente** configurar as definições do alvo. No organismo de pedido, se pretender gerir VMs em todos os grupos de recursos da subscrição, modifique o organismo de pedidos como mostrado no exemplo seguinte.

    ```json
    {
      "Action": "start",
      "EnableClassic": false,
      "RequestScopes": {
        "ExcludedVMLists": [],
        "Subscriptions": [
          "/subscriptions/12345678-1234-5678-1234-123456781234/"
        ]
     },
       "Sequenced": true
    }
    ```

    Especifique várias subscrições na `subscriptions` matriz com cada valor separado por uma vírgula como no exemplo seguinte.

    ```json
    "Subscriptions": [
          "/subscriptions/12345678-1234-5678-1234-123456781234/",
          "/subscriptions/11111111-0000-1111-2222-444444444444/"
        ]
    ```

    No organismo de pedido, se pretender gerir VMs para grupos de recursos específicos, modifique o corpo de pedidos como mostrado no exemplo seguinte. Cada caminho de recurso especificado deve ser separado por uma vírgula. Pode especificar um grupo de recursos se necessário.

    Este exemplo também demonstra a exclusão de uma máquina virtual pelo seu caminho de recursos em comparação com o exemplo para o início/paragem programado, que usou wildcards.

    ```json
    {
      "Action": "start",
      "EnableClassic": false,
      "RequestScopes": {
        "ResourceGroups": [
          "/subscriptions/12345678-1234-5678-1234-123456781234/resourceGroups/rg1/",
          "/subscriptions/11111111-0000-1111-2222-444444444444/resourceGroups/rg2/"
        ],
        "ExcludedVMLists": [
         "/subscriptions/12345678-1111-2222-3333-1234567891234/resourceGroups/vmrg1/providers/Microsoft.Compute/virtualMachines/vm1"
        ]
      },
       "Sequenced": true
    }
    ```

    No organismo de pedido, se pretender gerir um conjunto específico de VMs dentro de uma subscrição, modifique o organismo de pedido como mostrado no exemplo seguinte. Cada caminho de recurso especificado deve ser separado por uma vírgula. Pode especificar um VM se necessário.

    ```json
    {
      "Action": "start",
      "EnableClassic": true,
      "RequestScopes": {
        "ExcludedVMLists": [],
        "VMLists": [
          "/subscriptions/12345678-1234-5678-1234-123456781234/resourceGroups/rg1/providers/Microsoft.Compute/virtualMachines/vm1",
          "/subscriptions/12345678-1234-5678-1234-123456781234/resourceGroups/rg2/providers/Microsoft.ClassicCompute/virtualMachines/vm2",
          "/subscriptions/11111111-0000-1111-2222-444444444444/resourceGroups/rg2/providers/Microsoft.ClassicCompute/virtualMachines/vm30"
        ]
      },
       "Sequenced": true
    }
    ```

## <a name="auto-stop-scenario"></a>Cenário de paragem automática

Iniciar/Parar VMs v2 (pré-visualização) pode ajudar a gerir o custo de execução do Azure Resource Manager e VMs clássicos na sua subscrição, avaliando máquinas que não são usadas durante períodos não-pico, como horas de expediente, e desligá-las automaticamente se a utilização do processador for inferior a uma percentagem especificada.

As seguintes propriedades de alerta métrico na personalização do suporte do corpo do pedido:

- AutoStop_MetricName
- AutoStop_Condition
- AutoStop_Threshold
- AutoStop_Description
- AutoStop_Frequency
- AutoStop_Severity
- AutoStop_Threshold
- AutoStop_TimeAggregationOperator
- AutoStop_TimeWindow

Para saber mais sobre como funcionam os alertas métricos do Azure Monitor e como os configurar ver [alertas métricos no Azure Monitor](../../azure-monitor/alerts/alerts-metric-overview.md).

1. Da lista de aplicações Logic, para configurar a paragem automática, selecione **ststv2_vms_AutoStop**.

1. Selecione o designer de **aplicativos Logic** a partir do painel da esquerda.

1. Depois de o Designer de Aplicações Lógica aparecer, no painel de designers, selecione **Recorrência** para configurar o calendário da aplicação lógica. Para saber mais sobre as opções específicas de recorrência, consulte [a tarefa recorrente do Agenda](../../connectors/connectors-native-recurrence.md#add-the-recurrence-trigger).

    :::image type="content" source="media/deploy/schedule-recurrence-property.png" alt-text="Configure a frequência de recorrência para a aplicação lógica":::

1. No painel de design, selecione **Função-Tente** configurar as definições do alvo. No organismo de pedido, se pretender gerir VMs em todos os grupos de recursos da subscrição, modifique o organismo de pedidos como mostrado no exemplo seguinte.

    ```json
    {
      "Action": "stop",
      "EnableClassic": false,    
      "AutoStop_MetricName": "Percentage CPU",
      "AutoStop_Condition": "LessThan",
      "AutoStop_Description": "Alert to stop the VM if the CPU % exceed the threshold",
      "AutoStop_Frequency": "00:05:00",
      "AutoStop_Severity": "2",
      "AutoStop_Threshold": "5",
      "AutoStop_TimeAggregationOperator": "Average",
      "AutoStop_TimeWindow": "06:00:00",
      "RequestScopes":{        
        "Subscriptions":[
            "/subscriptions/12345678-1111-2222-3333-1234567891234/",
            "/subscriptions/12345678-2222-4444-5555-1234567891234/"
        ],
        "ExcludedVMLists":[]
      }        
    }
    ```

    No organismo de pedido, se pretender gerir VMs para grupos de recursos específicos, modifique o corpo de pedidos como mostrado no exemplo seguinte. Cada caminho de recurso especificado deve ser separado por uma vírgula. Pode especificar um grupo de recursos se necessário.

    ```json
    {
      "Action": "stop",
      "AutoStop_Condition": "LessThan",
      "AutoStop_Description": "Alert to stop the VM if the CPU % exceed the threshold",
      "AutoStop_Frequency": "00:05:00",
      "AutoStop_MetricName": "Percentage CPU",
      "AutoStop_Severity": "2",
      "AutoStop_Threshold": "5",
      "AutoStop_TimeAggregationOperator": "Average",
      "AutoStop_TimeWindow": "06:00:00",
      "EnableClassic": true,
      "RequestScopes": {
        "ExcludedVMLists": [],
        "ResourceGroups": [
          "/subscriptions/12345678-1111-2222-3333-1234567891234/resourceGroups/vmrg1/",
          "/subscriptions/12345678-1111-2222-3333-1234567891234/resourceGroupsvmrg2/",
          "/subscriptions/12345678-2222-4444-5555-1234567891234/resourceGroups/VMHostingRG/"
          ]
      }
    }
    ```

    No organismo de pedido, se pretender gerir um conjunto específico de VMs dentro da subscrição, modifique o organismo de pedido como mostrado no exemplo seguinte. Cada caminho de recurso especificado deve ser separado por uma vírgula. Pode especificar um VM se necessário.

    ```json
    {
      "Action": "stop",
      "AutoStop_Condition": "LessThan",
      "AutoStop_Description": "Alert to stop the VM if the CPU % exceed the threshold",
      "AutoStop_Frequency": "00:05:00",
      "AutoStop_MetricName": "Percentage CPU",
      "AutoStop_Severity": "2",
      "AutoStop_Threshold": "5",
      "AutoStop_TimeAggregationOperator": "Average",
      "AutoStop_TimeWindow": "06:00:00",
      "EnableClassic": true,
      "RequestScopes": {
        "ExcludedVMLists": [],
        "VMLists": [
          "/subscriptions/12345678-1111-2222-3333-1234567891234/resourceGroups/rg3/providers/Microsoft.ClassicCompute/virtualMachines/Clasyvm11",
          "/subscriptions/12345678-1111-2222-3333-1234567891234/resourceGroups/vmrg1/providers/Microsoft.Compute/virtualMachines/vm1"
        ]
      }
    }
    ```

## <a name="next-steps"></a>Passos seguintes

Para aprender a monitorizar o estado dos seus VMs Azure geridos pela função Iniciar/Parar VMs v2 (pré-visualização) e executar outras tarefas de gestão, consulte o artigo ['Iniciar/Parar VMs'.](manage.md)