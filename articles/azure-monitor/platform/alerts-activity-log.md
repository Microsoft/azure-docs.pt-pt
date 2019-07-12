---
title: Criar, ver e gerir alertas de registo de atividade no Azure Monitor
description: Crie alertas do registo de atividade com o portal do Azure, um modelo Azure Resource Manager e o Azure PowerShell.
author: msvijayn
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 06/25/2019
ms.author: vinagara
ms.openlocfilehash: 71e61228fcdbd52abedbc1f1205baa60b1aea923
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67705283"
---
# <a name="create-view-and-manage-activity-log-alerts-by-using-azure-monitor"></a>Criar, ver e gerir alertas de registo de atividade com o Azure Monitor  

## <a name="overview"></a>Descrição geral
Alertas de registo de atividade são os alertas que ser ativados quando um novo log de eventos de atividade ocorre que coincida com as condições especificadas no alerta.

Estes alertas são recursos do Azure e podem ser criados com um modelo Azure Resource Manager. Eles também podem ser criados, atualizados ou eliminados no portal do Azure. Normalmente, criar atividade de alertas de registo para receber notificações quando ocorrem alterações específicas recursos na sua subscrição do Azure. Alertas, muitas vezes, estão confinados a recursos ou grupos de recursos específico. Por exemplo, pode querer ser notificado quando qualquer máquina virtual no grupo de recursos de exemplo **myProductionResourceGroup** é eliminado. Em alternativa, pode querer ser notificado se quaisquer novas funções são atribuídas a um utilizador na sua subscrição.

> [!IMPORTANT]
> Não não possível criar alertas nas notificações de estado de funcionamento do serviço através da interface para a criação de alerta de registo de atividade. Para saber mais sobre como criar e utilizar notificações de estado de funcionamento do serviço, veja [receber alertas de registo de atividade nas notificações de estado de funcionamento do serviço](alerts-activity-log-service-notifications.md).

Ao criar regras de alerta, certifique-se ao seguinte:

- A subscrição no âmbito não é diferente da subscrição em que o alerta é criado.
- Os critérios tem de ser o nível, estado, autor da chamada, grupo de recursos, ID de recurso ou categoria de eventos de tipo de recurso no qual o alerta está configurado.
- Não existe nenhuma condição "anyOf" ou condições aninhadas na configuração do alerta JSON. Basicamente, apenas uma condição de "todos" é permitida sem mais "todos" ou "anyOf" condições.
- Quando a categoria é "administrativa", tem de especificar, pelo menos, um dos critérios anteriores no seu alerta. Não pode criar um alerta que é ativado sempre que for criado um evento nos registos de atividade.


## <a name="azure-portal"></a>Portal do Azure

Pode utilizar o portal do Azure para criar e modificar regras de alerta de registo de atividade. A experiência está integrada com um registo de atividades do Azure para garantir que a criação do alerta totalmente integrada para eventos específicos de interesse.

### <a name="create-with-the-azure-portal"></a>Criar com o portal do Azure

Utilize o procedimento seguinte.

1. No portal do Azure, selecione **Monitor** > **alertas**.
2. Selecione **nova regra de alerta** no canto superior esquerdo do **alertas** janela.

     ![Nova regra de alerta](media/alerts-activity-log/AlertsPreviewOption.png)

     O **criar regra** é apresentada a janela.

      ![novas opções de regra de alerta](media/alerts-activity-log/create-new-alert-rule-options.png)

3. Sob **definir a condição de alerta**, forneça as seguintes informações e selecione **feito**:

   - **Destino do alerta:** Para ver e selecionar o destino para o novo alerta, utilize **filtrar por subscrição** / **filtrar por tipo de recurso**. Selecione o recurso ou grupo de recursos na lista apresentada.

     > [!NOTE]
     > 
     > Pode selecionar um recurso, grupo de recursos ou uma subscrição completa para um sinal de registo de atividade.

     **Vista de destino do alerta de exemplo**

     ![Selecionar o destino](media/alerts-activity-log/select-target.png)

   - Sob **critérios de destino**, selecione **adicionar critérios**. São apresentados todos os sinais disponíveis para o destino, que incluem aqueles de várias categorias de **registo de atividades**. O nome da categoria é acrescentado para o **monitorizar serviço** nome.

   - Selecione o sinal na lista apresentada de diversas operações possíveis para o tipo **registo de atividades**.

     Pode selecionar a linha de tempo do histórico de registos e a lógica de alerta correspondente para este sinal de destino:

     **Adicionar ecrã de critérios**

     ![adicionar critérios](media/alerts-activity-log/add-criteria.png)

     - **Tempo do histórico**: Podem ser desenhados eventos disponíveis para a operação selecionada ao longo das últimas 6, 12 ou 24 horas ou durante a semana passada.

     - **Lógica de alerta**:

       - **Nível do evento**: O nível de gravidade do evento: _Verboso_, _informativa_, _aviso_, _erro_, ou _críticos_.
       - **Estado**: O estado do evento: _Iniciado_, _falha_, ou _foi concluída com êxito_.
       - **Evento iniciado por**: Também conhecido como o autor da chamada. O endereço de e-mail ou o identificador do Azure Active Directory do utilizador que executou a operação.

       Este gráfico de sinal de exemplo tem a lógica de alerta aplicada:

       ![critérios selecionados](media/alerts-activity-log/criteria-selected.png)

4. Sob **definir detalhes do alerta**, indique a seguinte informação:

    - **Nome da regra de alerta**: O nome para a nova regra de alerta.
    - **Descrição**: A descrição para a nova regra de alerta.
    - **Guardar o alerta para o grupo de recursos**: Selecione o grupo de recursos onde pretende guardar esta regra de novo.

5. Sob **grupo de ação**, no menu pendente, especifique o grupo de ação que pretende atribuir a esta nova regra de alerta. Ou, [criar um novo grupo de ação](../../azure-monitor/platform/action-groups.md) e atribuí-lo para a nova regra. Para criar um novo grupo, selecione **+ novo grupo**.

6. Para ativar as regras depois de criá-los, selecione **Sim** para o **ativar regra após a criação** opção.
7. Selecione **criar regra de alerta**.

    A nova regra de alerta para o registo de atividades é criada e é apresentada uma mensagem de confirmação no canto superior direito da janela.

    Pode ativar, desativar, editar ou eliminar uma regra. Saiba mais sobre como gerir regras de registo de atividade.


Uma analogia simple para condições de noções básicas sobre em que as regras de alerta podem ser criadas num registo de atividade é explorar ou filtrar eventos através da [registo de atividades no portal do Azure](activity-log-view.md#azure-portal). Na **do Azure Monitor - registo de atividades** ecrã, pode filtrar ou encontre o evento necessário e, em seguida, criar um alerta com o **Adicionar alerta de registo de atividade** botão. Em seguida, siga os passos 4 a 7 como anteriormente mostrados.
    
 ![Adicionar alerta do registo de atividades](media/alerts-activity-log/add-activity-log.png)
    

### <a name="view-and-manage-in-the-azure-portal"></a>Ver e gerir no portal do Azure

1. No portal do Azure, selecione **Monitor** > **alertas**. Selecione **gerir regras de alerta** no canto superior esquerdo da janela.

    ![Gerir regras de alerta](media/alerts-activity-log/manage-alert-rules.png)

    É apresentada a lista de regras disponíveis.

2. Procure a regra de registo de atividade modificar.

    ![Procurar regras de alerta de registo de atividade](media/alerts-activity-log/searth-activity-log-rule-to-edit.png)

    Pode utilizar os filtros disponíveis, _subscrição_, _grupo de recursos_, _recurso_, _sinalizar tipo_, ou _Estado_ , para encontrar a regra de atividade que pretende editar.

   > [!NOTE]
   > 
   > Pode editar apenas **Descrição**, **critérios de destino**, e **grupos de ação**.

3. Selecione a regra e clique duas vezes para editar as opções de regra. Faça as alterações necessárias e, em seguida, selecione **guardar**.

   ![Gerir regras de alerta](media/alerts-activity-log/activity-log-rule-edit-page.png)

4. Pode ativar, desativar ou eliminar uma regra. Selecione a opção adequada na parte superior da janela depois de selecionar a regra conforme descrito no passo 2.


## <a name="azure-resource-manager-template"></a>Modelo Azure Resource Manager
Para criar um alerta de registo de atividades com um modelo Azure Resource Manager, crie um recurso do tipo `microsoft.insights/activityLogAlerts`. Em seguida, preencher todas as propriedades relacionadas. Este é um modelo que cria um alerta de registo de atividade:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "activityLogAlertName": {
      "type": "string",
      "metadata": {
        "description": "Unique name (within the Resource Group) for the Activity log alert."
      }
    },
    "activityLogAlertEnabled": {
      "type": "bool",
      "defaultValue": true,
      "metadata": {
        "description": "Indicates whether or not the alert is enabled."
      }
    },
    "actionGroupResourceId": {
      "type": "string",
      "metadata": {
        "description": "Resource Id for the Action group."
      }
    }
  },
  "resources": [   
    {
      "type": "Microsoft.Insights/activityLogAlerts",
      "apiVersion": "2017-04-01",
      "name": "[parameters('activityLogAlertName')]",      
      "location": "Global",
      "properties": {
        "enabled": "[parameters('activityLogAlertEnabled')]",
        "scopes": [
            "[subscription().id]"
        ],        
        "condition": {
          "allOf": [
            {
              "field": "category",
              "equals": "Administrative"
            },
            {
              "field": "operationName",
              "equals": "Microsoft.Resources/deployments/write"
            },
            {
              "field": "resourceType",
              "equals": "Microsoft.Resources/deployments"
            }
          ]
        },
        "actions": {
          "actionGroups":
          [
            {
              "actionGroupId": "[parameters('actionGroupResourceId')]"
            }
          ]
        }
      }
    }
  ]
}
```
O exemplo anterior JSON pode ser salvas como, por exemplo, sampleActivityLogAlert.json com o objetivo deste passo a passo e pode ser implantado usando [do Azure Resource Manager no portal do Azure](../../azure-resource-manager/resource-group-template-deploy-portal.md).

> [!NOTE]
> Poderá demorar até 5 minutos para a nova atividade log alert rule fique ativa.

## <a name="rest-api"></a>API REST 
O [API de alertas do Log de atividade do Azure Monitor](https://docs.microsoft.com/rest/api/monitor/activitylogalerts) é uma API REST. Ele é totalmente compatível com a API de REST do Azure Resource Manager. Pode ser utilizada através do PowerShell, utilizando o cmdlet do Resource Manager ou a CLI do Azure.

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

### <a name="deploy-the-resource-manager-template-with-powershell"></a>Implementar o modelo do Resource Manager com o PowerShell
Utilizar o PowerShell para implementar o modelo do Resource Manager de exemplo mostrado no anterior [modelo Azure Resource Manager](#azure-resource-manager-template) secção, utilize o seguinte comando:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "myRG" -TemplateFile sampleActivityLogAlert.json -TemplateParameterFile sampleActivityLogAlert.parameters.json
```

em que o sampleActivityLogAlert.parameters.json contém os valores fornecidos para os parâmetros necessários para a criação de regra de alerta.

### <a name="use-activity-log-powershell-cmdlets"></a>Utilizar cmdlets do PowerShell do registo de atividades

Alertas de registo de atividade tem dedicados cmdlets do PowerShell disponíveis:

- [Set-AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Set-AzActivityLogAlert): Cria um novo alerta de registo de atividades ou atualiza um alerta de registo de atividades existentes.
- [Get-AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Get-AzActivityLogAlert): Obtém a atividade de um ou mais recursos de alerta de registo.
- [Enable-AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Enable-AzActivityLogAlert): Permite que um alerta de registo de atividades existentes e define suas marcas.
- [Disable-AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Disable-AzActivityLogAlert): Desativa um alerta de registo de atividades existentes e define suas marcas.
- [Remove-AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Remove-AzActivityLogAlert): Remove um alerta de registo de atividades.

## <a name="azure-cli"></a>CLI do Azure

Dedicado comandos da CLI do Azure em conjunto [alerta de registo de atividade do monitor de az](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert) estão disponíveis para o gerenciamento de regras de alerta de registo de atividade.

Para criar uma regra de alerta de registo de atividade, utilize os seguintes comandos por esta ordem:

1. [az monitor activity-log alert create](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-create): Crie um novo recurso de regra de alerta de registo de atividade.
1. [âmbito de alerta de registo de atividades do AZ monitor](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert/scope): Adicione âmbito para a regra de alerta de registo de atividade criado.
1. [az monitor activity-log alert action-group](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert/action-group): Adicione um grupo de ação para a regra de alerta de registo de atividade.

Para obter um recurso de regra de alerta de registo de atividade, utilize o comando da CLI do Azure [show de alerta de registo de atividades do az monitor](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-show
). Para ver todos os recursos de regra de alerta de registo de atividade num grupo de recursos, utilize [lista de alerta de registo de atividades do az monitor](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-list).
Recursos de regra de alerta de registo de atividade podem ser removidos através do comando da CLI do Azure [eliminação de alerta de registo de atividades do az monitor](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-delete).

## <a name="next-steps"></a>Passos Seguintes

- Saiba mais sobre [esquema de webhook para registos de atividades](../../azure-monitor/platform/activity-log-alerts-webhook.md).
- Leia uma [descrição geral dos registos de atividades](../../azure-monitor/platform/activity-log-alerts.md).
- Saiba mais sobre [grupos de ação](../../azure-monitor/platform/action-groups.md).  
- Saiba mais sobre [notificações de estado de funcionamento de serviço](../../azure-monitor/platform/service-notifications.md).
