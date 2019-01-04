---
title: Criar vista e gerir alertas de registo de atividades no Azure Monitor
description: Como criar alertas de registo de atividade a partir do Portal do Azure, o modelo de recurso e o PowerShell.
author: msvijayn
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/15/2018
ms.author: vinagara
ms.openlocfilehash: 6d0c8f62d109d07a9f08e5190a5a2caa0d66a0c1
ms.sourcegitcommit: 7cd706612a2712e4dd11e8ca8d172e81d561e1db
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/18/2018
ms.locfileid: "53579333"
---
# <a name="create-view-and-manage-activity-log-alerts-using-azure-monitor"></a>Criar, ver e gerir alertas de registo de atividade através do Azure Monitor  

## <a name="overview"></a>Descrição geral
Alertas de registo de atividade são os alertas que ser ativados quando um novo log de eventos de atividade ocorre que coincida com as condições especificadas no alerta.

Estes alertas são recursos do Azure, podem ser criadas com um modelo Azure Resource Manager. Eles também podem ser criados, atualizados ou eliminados no portal do Azure. Normalmente, criar atividade de alertas de registo para receber notificações quando ocorrem alterações de específicas nos recursos da sua subscrição do Azure, com freqüência no âmbito de grupos de recursos específico ou recurso. Por exemplo, pode querer ser notificado quando qualquer máquina virtual (grupo de recursos de exemplo) **myProductionResourceGroup** é eliminado, ou pode querer ser notificado se quaisquer novas funções são atribuídas a um utilizador na sua subscrição.

> [!IMPORTANT]
> Não é possível criar alertas nas notificações de estado de funcionamento do serviço através da interface para a criação de alerta de registo de atividade. Para saber mais sobre criar e utilizar notificações de estado de funcionamento do serviço, veja [receber alertas de registo de atividade nas notificações de estado de funcionamento do serviço](alerts-activity-log-service-notifications.md).

## <a name="azure-portal"></a>Portal do Azure

> [!NOTE]

>  Ao criar as regras de alerta, certifique-se ao seguinte:

> - Subscrição no âmbito não é diferente da subscrição em que o alerta é criado.
- Critérios de tem de ser nível/Estado/chamador / grupo de recursos / id de recurso / tipo de recurso / categoria de evento no qual o alerta está configurado.
- Não existe "anyOf" condição ou condições aninhadas na configuração do alerta JSON (basicamente, tudo apenas uma é permitido com não mais tudo/anyOf).
- Quando a categoria é "administrativa". Tem de especificar, pelo menos, um dos critérios anteriores no seu alerta. Não pode criar um alerta que é ativado sempre que for criado um evento nos registos de atividade.

### <a name="create-with-azure-portal"></a>Criar com o portal do Azure

Utilize o seguinte procedimento:

1. A partir do portal do Azure, selecione **Monitor** > **alertas**
2. Clique em **nova regra de alerta** na parte superior a **alertas** janela.

     ![Nova regra de alerta](media/alerts-activity-log/AlertsPreviewOption.png)

     O **criar regra** é apresentada a janela.

      ![novas opções de regra de alerta](media/alerts-activity-log/create-new-alert-rule-options.png)

3. **Na condição de alerta definir,** forneça as seguintes informações e clique em **feito**.

    - **Destino do alerta:** Para ver e selecionar o destino para o novo alerta, utilize **filtrar por subscrição** / **filtrar por tipo de recurso** e selecione o recurso ou grupo de recursos na lista apresentada.

    > [!NOTE]

    > pode selecionar um recurso, grupo de recursos ou uma subscrição completa para o sinal de registo de atividade.

    **Vista de destino de exemplo de alerta** ![selecionar destino](media/alerts-activity-log/select-target.png)

    - Sob **critérios de destino**, clique em **adicionar critérios** e todos os sinais disponíveis para o destino são apresentados, incluindo aqueles de várias categorias de **registo de atividades**; com nome da categoria anexado no **monitorizar serviço** nome.

    - Selecione o sinal na lista apresentada de diversas operações possíveis para o tipo **registo de atividades**.

    Pode selecionar a linha de tempo do histórico de registos e a lógica de alerta correspondente para este sinal de destino:

    **Adicionar ecrã de critérios**

    ![adicionar critérios](media/alerts-activity-log/add-criteria.png)

    **Tempo do histórico**: Eventos disponíveis para a operação selecionada é podem ser plotados durante as últimas 6/12/24 horas (ou) durante a semana passada.

    **Lógica de alerta**:

     - **Nível do evento**-o nível de gravidade do evento. _Verboso_, _informativa_, _aviso_, _erro_, ou _críticos_.
     - **Estado**: O estado do evento. _Iniciado_, _falha_, ou _foi concluída com êxito_.
     - **Evento iniciado por**: Também conhecido como o autor da chamada; O endereço de e-mail ou o identificador do Azure Active Directory do utilizador que executou a operação.

        Gráfico de sinal de exemplo com lógica de alerta aplicada:

        ![ critérios selecionados](media/alerts-activity-log/criteria-selected.png)

4. Sob **definir os detalhes de regras de alerta**, indique a seguinte informação:

    - **Nome da regra de alerta** – nome para a nova regra de alerta
    - **Descrição** – uma descrição para a nova regra de alerta
    - **Guardar o alerta para o grupo de recursos** – selecione o grupo de recursos, onde pretende guardar esta regra de novo.

5. Sob **grupo de ação**, no menu pendente, especifique o grupo de ação que pretende atribuir a esta nova regra de alerta. Em alternativa, [criar um novo grupo de ação](../../azure-monitor/platform/action-groups.md) e atribuir a nova regra. Para criar um novo grupo, clique em **+ novo grupo**.

6. Para ativar as regras depois de a criar, clique em **Sim** para **ativar regra após a criação** opção.
7. Clique em **criar regra de alerta**.

    A nova regra de alerta para o registo de atividades é criada e é apresentada uma mensagem de confirmação na parte superior direita da janela.

    Pode ativar, desativar, editar ou eliminar uma regra. [Saiba mais](#view-and-manage-activity-log-alert-rules-in-azure-portal) sobre a gestão de regras de registo de atividade.


Em alternativa, uma analogia simple para condições de noções básicas sobre em que as regras de alerta podem ser criadas no registo de atividades, é explorar ou filtrar eventos via [registo de atividades no portal do Azure](../../azure-monitor/platform/activity-logs-overview.md#query-the-activity-log-in-the-azure-portal). No Azure Monitor - registo de atividades, um pode filtrar ou localizar evento necessários e, em seguida, criar um alerta com o **Adicionar alerta de registo de atividade** botão; em seguida, siga passos 4 e posteriores conforme indicado acima do tutorial.
    
 ![ Adicionar alerta do registo de atividades](media/alerts-activity-log/add-activity-log.png)
    

### <a name="view-and-manage-in-azure-portal"></a>Ver e gerir no portal do Azure

1. A partir do portal do Azure, clique em **Monitor** > **alertas** e clique em **gerir regras** na parte superior esquerda da janela.

    ![ Gerir regras de alerta](media/alerts-activity-log/manage-alert-rules.png)

    É apresentada a lista de regras disponíveis.

2. Procure a regra de registo de atividade para modificar.

    ![ Procurar regras de alerta de registo de atividade](media/alerts-activity-log/searth-activity-log-rule-to-edit.png)

    Pode utilizar os filtros disponíveis - _subscrição_, _grupo de recursos_, _recurso_, _tipo de sinal_, ou _Estado_  para encontrar a regra de atividade que pretende editar.

    > [!NOTE]

    > Pode editar apenas **Descrição** , **critérios de destino** e **grupos de ação**.

3.  Selecione a regra e faça duplo clique para editar as opções de regra. Faça as alterações necessárias e, em seguida, clique em **guardar**.

    ![ Gerir regras de alerta](media/alerts-activity-log/activity-log-rule-edit-page.png)

4.  Pode desativar, ativar ou eliminar uma regra. Selecione a opção adequada na parte superior da janela, depois de selecionar a regra conforme detalhado no passo 2.


## <a name="azure-resource-template"></a>Modelo de recursos do Azure
Para criar um alerta de registo de atividades com um modelo do Resource Manager, crie um recurso do tipo `microsoft.insights/activityLogAlerts`. Em seguida, preencher todas as propriedades relacionadas. Aqui está um modelo que cria um alerta de registo de atividades.

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
O json de exemplo acima podem ser salvas como (Digamos) sampleActivityLogAlert.json para efeitos destas instruções e podem ser implementado com [Gestor de recursos do Azure no portal do Azure](../../azure-resource-manager/resource-group-template-deploy-portal.md).

> [!NOTE]
> Poderá demorar até 5 minutos a uma nova atividade log regra de alerta fique ativo

## <a name="rest-api"></a>API REST 
[O Azure Monitor - API de alertas do Log de atividade](https://docs.microsoft.com/rest/api/monitor/activitylogalerts) é uma REST API e totalmente compatível com a API de REST do Azure Resource Manager. Por conseguinte, pode ser utilizada através do Powershell com o cmdlet do Gestor de recursos, bem como a CLI do Azure.

## <a name="powershell"></a>PowerShell
Ilustrado abaixo utilização através do cmdlet do PowerShell do Azure Resource Manager para o exemplo de modelo do Resource mostrado anteriormente (sampleActivityLogAlert.json) na [secção do modelo do Resource](#manage-alert-rules-for-activity-log-using-azure-resource-template) :
```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName "myRG" -TemplateFile sampleActivityLogAlert.json -TemplateParameterFile sampleActivityLogAlert.parameters.json
```
Em que o sampleActivityLogAlert.parameters.json tem valores fornecidos para os parâmetros necessários para a criação de regra de alerta.

## <a name="cli"></a>CLI
Ilustrado abaixo utilização através do comando do Azure Resource Manager na CLI do Azure para o exemplo de modelo do Resource mostrado anteriormente (sampleActivityLogAlert.json) na [secção do modelo do Resource](#manage-alert-rules-for-activity-log-using-azure-resource-template) :

```azurecli
az group deployment create --resource-group myRG --template-file sampleActivityLogAlert.json --parameters @sampleActivityLogAlert.parameters.json
```
O *sampleActivityLogAlert.parameters.json* ficheiro tem valores fornecidos para os parâmetros necessários para a criação de regra de alerta.


## <a name="next-steps"></a>Passos Seguintes

- [Esquema de Webhook para registos de atividades](../../azure-monitor/platform/activity-log-alerts-webhook.md)
- [Descrição geral dos registos de atividades](../../azure-monitor/platform/activity-log-alerts.md) 
- Saiba mais sobre [grupos de ação](../../azure-monitor/platform/action-groups.md).  
- Saiba mais sobre [notificações de estado de funcionamento de serviço](../../azure-monitor/platform/service-notifications.md).
