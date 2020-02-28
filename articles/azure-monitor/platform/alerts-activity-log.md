---
title: Criar, visualizar e gerir alertas de registo de atividade no Monitor Azure
description: Crie alertas de registo de atividade utilizando o portal Azure, um modelo de Gestor de Recursos Azure e Azure PowerShell.
ms.topic: conceptual
ms.subservice: alerts
ms.date: 06/25/2019
ms.openlocfilehash: 9791ebaadeb1ee724692a9e1a0d61aff5cbae6a3
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2020
ms.locfileid: "77668490"
---
# <a name="create-view-and-manage-activity-log-alerts-by-using-azure-monitor"></a>Criar, visualizar e gerir alertas de registo de atividade utilizando o Monitor Azure  

## <a name="overview"></a>Descrição geral

Os alertas de registo de atividade são os alertas que são ativados quando ocorre um novo evento de registo de atividade que corresponde às condições especificadas no alerta.

Estes alertas são para recursos Azure e podem ser criados usando um modelo de Gestor de Recursos Azure. Também podem ser criados, atualizados ou eliminados no portal Azure. Normalmente, cria alertas de registo de atividade para receber notificações quando ocorrem alterações específicas aos recursos da sua subscrição Azure. Os alertas são frequentemente examinados para determinados grupos ou recursos. Por exemplo, é melhor que seja notificado quando for eliminada qualquer máquina virtual do grupo de recursos de amostra **myProductionResourceGroup.** Ou, pode querer ser notificado se forem atribuídas novas funções a um utilizador na sua subscrição.

> [!IMPORTANT]
> Os alertas sobre a notificação de saúde de serviço não podem ser criados através da interface para a criação de alerta de registo de atividade. Para saber mais sobre como criar e utilizar notificações de saúde de serviço, consulte Receber alertas de registo de [atividade sonantes em notificações](alerts-activity-log-service-notifications.md)de saúde de serviço .

Quando criar regras de alerta, certifique-se do seguinte:

- A subscrição no âmbito não é diferente da subscrição onde o alerta é criado.
- Os critérios devem ser o nível, estado, chamador, grupo de recursos, id de recursos ou categoria de evento de tipo de recurso em que o alerta está configurado.
- Não há nenhuma condição "qualquer Of" ou condições aninhadas na configuração de alerta JSON. Basicamente, apenas uma condição "allOf" é permitida sem mais condições "allOf" ou "anyOf".
- Quando a categoria for "administrativa", deve especificar pelo menos um dos critérios anteriores no seu alerta. Não pode criar um alerta que ativa sempre que um evento é criado nos registos de atividade.

## <a name="azure-portal"></a>Portal do Azure

Pode utilizar o portal Azure para criar e modificar as regras de alerta de registo de atividade. A experiência está integrada com um registo de atividade do Azure para garantir a criação de alerta sem emenda para eventos específicos de interesse.

### <a name="create-with-the-azure-portal"></a>Criar com o portal Azure

Utilize o seguinte procedimento.

1. No portal Azure, selecione **Monitor > ** **Alertas**.
2. Selecione **Nova regra** de alerta no canto superior esquerdo da janela **Alertas.**

     ![Nova regra de alerta](media/alerts-activity-log/AlertsPreviewOption.png)

     A janela de **regras Create** aparece.

      ![Novas opções de regra de alerta](media/alerts-activity-log/create-new-alert-rule-options.png)

3. Sob a condição de **alerta Defina,** forneça as seguintes informações e selecione **Feito:**

   - **Alvo de alerta:** Para visualizar e selecionar o alvo para o novo alerta, utilize **o Filtro por subscrição** / **Filter por tipo de recurso**. Selecione o grupo de recursos ou recursos da lista apresentada.

     > [!NOTE]
     > 
     > Pode selecionar apenas o [Azure Resource Manager,](../../azure-resource-manager/management/overview.md) o grupo de recursos ou uma subscrição inteira para um sinal de registo de atividade. 

     **Visão de amostra de alvo de alerta**

     ![Selecionar o destino](media/alerts-activity-log/select-target.png)

   - Sob **os critérios de destino,** selecione **Critérios de adição**. Todos os sinais disponíveis para o alvo são apresentados, que inclui os de várias categorias de Registo de **Atividades**. O nome da categoria está anexado ao nome **do Monitor Service.**

   - Selecione o sinal da lista apresentada de várias operações possíveis para o log de **atividade**do tipo .

     Pode selecionar a linha temporal do histórico de registos e a lógica de alerta correspondente para este sinal-alvo:

     **Adicionar ecrã de critérios**

     ![Adicionar critérios](media/alerts-activity-log/add-criteria.png)

     - **Tempo de história**: Os eventos disponíveis para a operação selecionada podem ser traçados ao longo das últimas 6, 12 ou 24 horas ou durante a última semana.

     - **Lógica de alerta:**

       - **Nível**de evento : O nível de gravidade do evento: _Verbose,_ _Informacional,_ _Aviso,_ _Erro_ou _Crítico_.
       - **Estado**: O estado do evento: _Iniciado,_ _falhado_ou _bem sucedido_.
       - **Evento iniciado por**: Também conhecido como quem liga. O endereço de e-mail ou identificador de Diretório Ativo Azure do utilizador que realizou a operação.

       Este gráfico de sinal de amostra tem a lógica de alerta aplicada:

       ![Critérios selecionados](media/alerts-activity-log/criteria-selected.png)

4. Em detalhes de **alerta Definidos,** forneça os seguintes detalhes:

    - **Nome**da regra do alerta : O nome da nova regra de alerta.
    - **Descrição**: A descrição da nova regra de alerta.
    - **Guarde o alerta para**o grupo de recursos : Selecione o grupo de recursos onde pretende salvar esta nova regra.

5. No **grupo Action**, a partir do menu suspenso, especifique o grupo de ação que pretende atribuir a esta nova regra de alerta. Ou [criar um novo grupo](../../azure-monitor/platform/action-groups.md) de ação e atribuí-lo à nova regra. Para criar um novo grupo, selecione **+ Novo grupo**.

6. Para ativar as regras após a sua criação, selecione **Sim** para a regra Enable sobre a opção **criação.**
7. Selecione **Criar regra**de alerta .

    A nova regra de alerta para o registo de atividade é criada, e uma mensagem de confirmação aparece no canto superior direito da janela.

    Pode ativar, desativar, editar ou excluir uma regra. Saiba mais sobre como gerir as regras de registo de atividades.


Uma simples analogia para compreender as condições em que as regras de alerta podem ser criadas num registo de atividade é explorar ou filtrar eventos através do registo de [atividade no portal Azure](activity-log-view.md#azure-portal). No ecrã de **registo Azure Monitor - Atividade,** pode filtrar ou encontrar o evento necessário e, em seguida, criar um alerta utilizando o botão de alerta de registo de **atividade adicionar.** Em seguida, siga os passos 4 a 7, como anteriormente mostrado.
    
 ![Adicionar alerta a partir do registo de atividade](media/alerts-activity-log/add-activity-log.png)
    

### <a name="view-and-manage-in-the-azure-portal"></a>Ver e gerir no portal Azure

1. No portal Azure, selecione **Monitor > ** **Alertas**. **Selecione Gerir as regras** de alerta no canto superior esquerdo da janela.

    ![Gerir as regras de alerta](media/alerts-activity-log/manage-alert-rules.png)

    A lista de regras disponíveis aparece.

2. Procure a regra do registo de atividade para modificar.

    ![Regras de alerta de registo de atividades de pesquisa](media/alerts-activity-log/searth-activity-log-rule-to-edit.png)

    Pode utilizar os filtros disponíveis, _Subscrição,_ _Grupo de Recursos,_ _Recurso,_ Tipo de _Sinal,_ ou _Estado,_ para encontrar a regra de atividade que pretende editar.

   > [!NOTE]
   > 
   > Só pode editar **Descrição,** **critérios-alvo**e grupos de **ação.**

3. Selecione a regra e clique duas vezes para editar as opções de regra. Faça as alterações necessárias e, em seguida, selecione **Guardar**.

   ![Gerir as regras de alerta](media/alerts-activity-log/activity-log-rule-edit-page.png)

4. Pode ativar, desativar ou eliminar uma regra. Selecione a opção adequada na parte superior da janela depois de selecionar a regra conforme descrito no passo 2.


## <a name="azure-resource-manager-template"></a>Modelo Azure Resource Manager
Para criar um alerta de registo de atividade utilizando um modelo de Gestor de Recursos Azure, cria um recurso do tipo `microsoft.insights/activityLogAlerts`. Depois preenchetodas as propriedades relacionadas. Aqui está um modelo que cria um alerta de registo de atividade:

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
A amostra anterior JSON pode ser guardada como, por exemplo, sampleActivityLogAlert.json para efeitos deste walk-through e pode ser implementada utilizando o Gestor de [Recursos Azure no portal Azure](../../azure-resource-manager/templates/deploy-portal.md).

> [!NOTE]
> Pode levar até 5 minutos para que a nova regra de alerta de registo de atividade satifique ativa.

## <a name="rest-api"></a>API REST 
A API de alertas de registo de atividade do [Monitor Azure](https://docs.microsoft.com/rest/api/monitor/activitylogalerts) é uma API REST. É totalmente compatível com a API REST API do Gestor de Recursos Azure. Pode ser utilizado através do PowerShell utilizando o Cmdlet do Gestor de Recursos ou o ClI Azure.

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

### <a name="deploy-the-resource-manager-template-with-powershell"></a>Implementar o modelo de Gestor de Recursos com PowerShell
Para utilizar o PowerShell para implementar o modelo de Gestor de Recursos da amostra mostrado na secção de modelo de gestor de [recursos do Azure](#azure-resource-manager-template) anterior, utilize o seguinte comando:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "myRG" -TemplateFile sampleActivityLogAlert.json -TemplateParameterFile sampleActivityLogAlert.parameters.json
```

onde a amostraActivityLogAlert.parameters.json contém os valores fornecidos para os parâmetros necessários para a criação de regras de alerta.

### <a name="use-activity-log-powershell-cmdlets"></a>Utilize cmdlets powerShell de log de atividade

Os alertas de registo de atividade têm cmdlets powerShell dedicados disponíveis:

- [Set-AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Set-AzActivityLogAlert): Cria um novo alerta de registo de atividade ou atualiza um alerta de registo de atividade existente.
- [Get-AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Get-AzActivityLogAlert): Obtém um ou mais recursos de alerta de registo de atividade.
- [Enable-AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Enable-AzActivityLogAlert): Ativa um alerta de registo de atividade existente e define as suas etiquetas.
- [Desactivar-AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Disable-AzActivityLogAlert): Desativa um alerta de registo de atividade existente e define as suas etiquetas.
- [Remover-AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Remove-AzActivityLogAlert): Remove um alerta de registo de atividade.

## <a name="azure-cli"></a>CLI do Azure

Os comandos Azure CLI dedicados sob o alerta de registo de atividade do [monitor az](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert) estão disponíveis para gerir as regras de alerta de registo de atividade.

Para criar uma nova regra de alerta de registo de atividade, utilize os seguintes comandos nesta ordem:

1. [az monitor de alerta de registo de atividade criar](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-create): Criar um novo recurso de regra de alerta de registo de atividade.
1. [az monitorizar o âmbito de alerta de registo de atividade](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert/scope): Adicione o âmbito para a regra de alerta de registo de atividade criada.
1. [az monitor de](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert/action-group)grupo de ação de alerta de registo de atividade : Adicione um grupo de ação à regra de alerta de registo de atividade.

Para recuperar um recurso de regra de alerta de registo de atividade, utilize o programa de alerta de registo de atividade do comando Azure CLI [az](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-show
). Para ver todos os recursos de regra de alerta de registo de atividade num grupo de recursos, utilize [alista de alerta de registo de atividade az](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-list)monitor .
Os recursos de alerta de registo de atividade podem ser removidos utilizando o alerta de [alerta de registo de atividade](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-delete)do comando Azure CLI .

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [o webhook schema para registos de atividade](../../azure-monitor/platform/activity-log-alerts-webhook.md).
- Leia uma [visão geral dos registos de atividade](../../azure-monitor/platform/activity-log-alerts.md).
- Saiba mais sobre [grupos de ação.](../../azure-monitor/platform/action-groups.md)  
- Conheça as notificações de saúde de [serviço.](../../azure-monitor/platform/service-notifications.md)
