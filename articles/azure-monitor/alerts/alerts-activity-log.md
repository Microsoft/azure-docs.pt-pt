---
title: Criar, visualizar e gerir alertas de registo de atividade no Azure Monitor
description: Crie alertas de registo de atividade utilizando o portal Azure, um modelo de Gestor de Recursos Azure e Azure PowerShell.
ms.topic: conceptual
ms.subservice: alerts
ms.date: 06/25/2019
ms.openlocfilehash: 83023cca6b034ee0e9acddfa081f09eb47b9fb1e
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100621022"
---
# <a name="create-view-and-manage-activity-log-alerts-by-using-azure-monitor"></a>Criar, visualizar e gerir alertas de registo de atividades utilizando o Azure Monitor  

## <a name="overview"></a>Descrição Geral

Os alertas de registo de atividade são os alertas que são ativados quando ocorre um novo evento de registo de atividade que corresponde às condições especificadas no alerta.

Estes alertas são para recursos Azure e podem ser criados usando um modelo de Gestor de Recursos Azure. Também podem ser criados, atualizados ou eliminados no portal Azure. Normalmente, cria alertas de registo de atividade para receber notificações quando ocorrem alterações específicas aos recursos na sua subscrição do Azure. Os alertas são muitas vezes alargados a determinados grupos de recursos ou recursos. Por exemplo, é melhor ser notificado quando qualquer máquina virtual do grupo de recursos de amostra **myProductionResourceGroup** for eliminada. Ou, pode querer ser notificado se alguma nova função for atribuída a um utilizador na sua subscrição.

> [!IMPORTANT]
> Os alertas sobre a notificação de saúde do serviço não podem ser criados através da interface para a criação de alerta de registo de atividade. Para saber mais sobre como criar e utilizar notificações de saúde do serviço, consulte [receber alertas de registo de atividades nas notificações de saúde do serviço.](../../service-health/alerts-activity-log-service-notifications-portal.md)

Quando criar regras de alerta, certifique-se de que:

- A subscrição no âmbito não é diferente da subscrição onde o alerta é criado.
- Os critérios devem ser o nível, estado, chamador, grupo de recursos, ID de recurso ou categoria de evento tipo de recurso em que o alerta está configurado.
- Não existe condição "qualquer Of" ou condições aninhadas na configuração de alerta JSON. Basicamente, apenas uma condição "allOf" é permitida sem mais condições "allOf" ou "anyOf".
- Quando a categoria for "administrativa", deve especificar pelo menos um dos critérios anteriores no seu alerta. Não pode criar um alerta que seja ativado sempre que um evento é criado nos registos de atividade.
- Não podem ser criados alertas para eventos na categoria de alerta de registo de atividade.

## <a name="azure-portal"></a>Portal do Azure

Pode utilizar o portal Azure para criar e modificar as regras de alerta de registo de atividade. A experiência é integrada com um registo de atividades Azure para garantir a criação de alerta sem emenda para eventos específicos de interesse.

### <a name="create-with-the-azure-portal"></a>Criar com o portal Azure

Utilize o seguinte procedimento.

1. No portal Azure, selecione  >  **Alertas de** Monitor .
2. Selecione **Nova regra** de alerta no canto superior esquerdo da janela **Alertas.**

     ![Nova regra de alerta](media/alerts-activity-log/AlertsPreviewOption.png)

     Aparece a janela **de regras Create.**

      ![Novas opções de regra de alerta](media/alerts-activity-log/create-new-alert-rule-options.png)

3. Em **condições de alerta,** forneça as seguintes informações e selecione **Feito**:

   - **Alvo de alerta:** Para visualizar e selecionar o alvo para o novo alerta, utilize **filter por subscrição**  /  **Filter por tipo de recurso**. Selecione o grupo de recursos ou recursos da lista apresentada.

     > [!NOTE]
     > 
     > Pode selecionar apenas [o Azure Resource Manager](../../azure-resource-manager/management/overview.md) rastreado recurso, grupo de recursos ou uma subscrição completa para um sinal de registo de atividade. 

     **Vista de amostra de alvo de alerta**

     ![Selecionar o destino](media/alerts-activity-log/select-target.png)

   - De acordo com **os critérios-alvo**, selecione **os critérios de adicionar**. Todos os sinais disponíveis para o alvo são apresentados, que inclui os de várias categorias de Registo de **Atividades.** O nome da categoria é anexado ao nome **de Serviço monitor.**

   - Selecione o sinal da lista apresentada de várias operações possíveis para o tipo **de Registo de Atividade**.

     Pode selecionar a linha de tempo do histórico de registos e a lógica de alerta correspondente para este sinal de destino:

     **Adicionar ecrã de critérios**

     ![Adicionar critérios](media/alerts-activity-log/add-criteria.png)
     
     > [!NOTE]
     > 
     >  Para termos regras de elevada qualidade e efetivas, pedimos que acrescentem pelo menos mais uma condição às regras com o sinal "Todas as Administrações". 
     > Como parte da definição do alerta, deve preencher uma das descidas: "Nível de evento", "Estado" ou "Iniciado por" e por isso a regra será mais específica.

     - **Tempo de história**: Os eventos disponíveis para a operação selecionada podem ser traçados ao longo das últimas 6, 12 ou 24 horas ou durante a última semana.

     - **Lógica de alerta:**

       - **Nível de evento**: O nível de gravidade do evento: _Verbose_, _Informational,_ _Warning,_ _Error_, or _Critical_.
       - **Estado**: O estado do evento: _Iniciado,_ _Falhado,_ ou _Bem Sucedido_.
       - **Evento iniciado por:** Também conhecido como o chamador. O endereço de e-mail ou identificador do Diretório Ativo Azure do utilizador que realizou a operação.

       Este gráfico de sinal de amostra tem a lógica de alerta aplicada:

       ![Critérios selecionados](media/alerts-activity-log/criteria-selected.png)

4. Em **Definição de detalhes de alerta,** forneça os seguintes detalhes:

    - **Nome da regra de alerta**: O nome da nova regra de alerta.
    - **Descrição**: A descrição da nova regra de alerta.
    - **Guardar alerta para o grupo de recursos**: Selecione o grupo de recursos onde pretende guardar esta nova regra.

5. No **grupo Ação**, a partir do menu suspenso, especifique o grupo de ação que pretende atribuir a esta nova regra de alerta. Ou [criar um novo grupo de ação](../platform/action-groups.md) e atribuí-lo à nova regra. Para criar um novo grupo, selecione **+ Novo grupo.**

6. Para ativar as regras após a sua criação, selecione **Sim** para a regra Enable após a opção **de criação.**
7. Selecione **Criar regra de alerta**.

    A nova regra de alerta para o registo de atividade é criada e uma mensagem de confirmação aparece no canto superior direito da janela.

    Pode ativar, desativar, editar ou eliminar uma regra. Saiba mais sobre como gerir as regras de registo de atividades.


Uma simples analogia para compreender as condições em que as regras de alerta podem ser criadas num registo de atividades é explorar ou filtrar eventos através do registo de [atividades no portal Azure](../essentials/activity-log.md#view-the-activity-log). No **Monitor Azure -** Ecrã de registo de atividade, pode filtrar ou encontrar o evento necessário e, em seguida, criar um alerta utilizando o botão **de alerta de registo de atividade Add.** Em seguida, siga os passos 4 a 7 como anteriormente mostrado.
    
 ![Adicionar alerta do registo de atividade](media/alerts-activity-log/add-activity-log.png)
    

### <a name="view-and-manage-in-the-azure-portal"></a>Ver e gerir no portal Azure

1. No portal Azure, selecione  >  **Alertas de** Monitor . **Selecione Regras** de alerta no canto superior esquerdo da janela.

    ![A screenshot mostra o registo de atividade com a caixa de pesquisa realçada.](media/alerts-activity-log/manage-alert-rules.png)

    A lista de regras disponíveis aparece.

2. Procure a regra do registo de atividade para modificar.

    ![Regras de alerta de registo de atividade de pesquisa](media/alerts-activity-log/searth-activity-log-rule-to-edit.png)

    Pode utilizar os filtros disponíveis, _Subscrição_, _Grupo de Recursos,_  _Recurso,_ _Tipo de Sinal_ ou _Estado,_ para encontrar a regra de atividade que pretende editar.

   > [!NOTE]
   > 
   > Só pode editar **Descrição,** **Critérios-alvo** e **Grupos de Ação**.

3. Selecione a regra e clique duas vezes para editar as opções de regra. Faça as alterações necessárias e, em seguida, **selecione Guardar**.

   ![Gere regras do alerta](media/alerts-activity-log/activity-log-rule-edit-page.png)

4. Pode ativar, desativar ou eliminar uma regra. Selecione a opção adequada na parte superior da janela depois de selecionar a regra conforme descrito no passo 2.


## <a name="azure-resource-manager-template"></a>Modelo Azure Resource Manager
Para criar uma regra de alerta de registo de atividade utilizando um modelo de Gestor de Recursos Azure, cria um recurso do tipo `microsoft.insights/activityLogAlerts` . Depois preenche todas as propriedades relacionadas. Aqui está um modelo que cria uma regra de alerta de registo de atividade:

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
A amostra anterior JSON pode ser guardada como, por exemplo, sampleActivityLogAlert.jspara efeitos deste walk-through e pode ser implementada utilizando [o Azure Resource Manager no portal Azure](../../azure-resource-manager/templates/deploy-portal.md).

  > [!NOTE]
  > 
  > Note que os alertas de registo de atividade de nível mais alto podem ser definidos é a subscrição.
  > O que significa que não há opção para definir alerta em algumas subscrições, por isso a definição deve estar alerta por subscrição.

Os seguintes campos são as opções que pode utilizar no modelo Azure Resource Manager para os campos de condições: Note que "Resource Health", "Advisor" e "Service Health" têm campos de propriedades extra para os seus campos especiais. 
1. resourceId: O ID de recursos do recurso impactado no evento de registo de atividade em que o alerta deve ser gerado.
2. categoria de no evento de registo de atividades. Por exemplo: Administração, Saúde de Serviço, Saúde de Recursos, Autoescala, Segurança, Recomendação, Política.
3. chamador: O endereço de e-mail ou identificador do Azure Ative Directory do utilizador que realizou o funcionamento do evento de registo de atividade.
4. nível: Nível da atividade no evento de registo de atividade em que o alerta deve ser gerado. Por exemplo: Crítico, Erro, Aviso, Informação, Verbose.
5. operaçãoName: O nome da operação no evento de registo de atividade. Por exemplo: Microsoft.Resources/deployments/write
6. grupo de recursos: Nome do grupo de recursos para o recurso impactado no evento de registo de atividade.
7. recursoProvider: [Azure fornecedores de recursos e tipos de explicação](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fazure-resource-manager%2Fmanagement%2Fresource-providers-and-types&data=02%7C01%7CNoga.Lavi%40microsoft.com%7C90b7c2308c0647c0347908d7c9a2918d%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637199572373543634&sdata=4RjpTkO5jsdOgPdt%2F%2FDOlYjIFE2%2B%2BuoHq5%2F7lHpCwQw%3D&reserved=0). Para obter uma lista que mapeia os fornecedores de recursos para os serviços Azure, consulte [os fornecedores de recursos para os serviços Azure](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fazure-resource-manager%2Fmanagement%2Fazure-services-resource-providers&data=02%7C01%7CNoga.Lavi%40microsoft.com%7C90b7c2308c0647c0347908d7c9a2918d%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637199572373553639&sdata=0ZgJPK7BYuJsRifBKFytqphMOxMrkfkEwDqgVH1g8lw%3D&reserved=0).
8. estado: Cadeia descrevendo o estado da operação no evento de atividade. Por exemplo: Iniciado, em progresso, bem sucedido, falhado, ativo, resolvido
9. subStatus: Normalmente o código de estado HTTP da chamada REST correspondente, mas também pode incluir outras cadeias que descrevem um substatus.   Por exemplo: OK (HTTP Status Code: 200), Criado (CÓDIGO DE Estado HTTP: 201), Aceite (CÓDIGO DE Estado HTTP: 202), Sem Conteúdo (Código de Estado HTTP: 204), Mau Pedido (Código de Estado HTTP: 400), Não Encontrado (CÓDIGO DE Estado HTTP: 404), Conflito (CÓDIGO DE Estado HTTP: 409), Erro do Servidor Interno (Código de Estado HTTP: 500), Serviço Indisponível (Código de Estado HTTP: 503), Tempo de Gateway (Código de Estado HTTP: 504).
10. recursoType: O tipo de recurso que foi afetado pelo evento. Por exemplo: Microsoft.Resources/deployments

Por exemplo:

```json
"condition": {
          "allOf": [
            {
              "field": "category",
              "equals": "Administrative"
            },
            {
              "field": "resourceType",
              "equals": "Microsoft.Resources/deployments"
            }
          ]
        }

```
Mais detalhes sobre os campos de registo de atividade que pode encontrar [aqui.](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fazure-monitor%2Fplatform%2Factivity-log-schema&data=02%7C01%7CNoga.Lavi%40microsoft.com%7C90b7c2308c0647c0347908d7c9a2918d%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637199572373563632&sdata=6QXLswwZgUHFXCuF%2FgOSowLzA8iOALVgvL3GMVhkYJY%3D&reserved=0)



> [!NOTE]
> Pode levar até 5 minutos para que a nova regra de alerta de registo de atividade se torne ativa.

## <a name="rest-api"></a>API REST 
O [Azure Monitor Activity Alerts API](/rest/api/monitor/activitylogalerts) é uma API REST. É totalmente compatível com a API do Gestor de Recursos Azure. Pode ser utilizado através do PowerShell utilizando o cmdlet do Gestor de Recursos ou o CLI Azure.

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

### <a name="deploy-the-resource-manager-template-with-powershell"></a>Implemente o modelo de Gestor de Recursos com PowerShell
Para utilizar o PowerShell para implementar o modelo do Gestor de Recursos da amostra mostrado na secção de modelo anterior [do Gestor de Recursos Azure,](#azure-resource-manager-template) utilize o seguinte comando:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "myRG" -TemplateFile sampleActivityLogAlert.json -TemplateParameterFile sampleActivityLogAlert.parameters.json
```

onde o sampleActivityLogAlert.parameters.jscontém os valores previstos para os parâmetros necessários para a criação de regras de alerta.

### <a name="use-activity-log-powershell-cmdlets"></a>Use o log de atividade PowerShell cmdlets

Os alertas de registo de atividade têm cmdlets dedicados powerShell disponíveis:

- [Set-AzActivityLogAlert](/powershell/module/az.monitor/set-azactivitylogalert): Cria um novo alerta de registo de atividade ou atualiza um alerta de registo de atividade existente.
- [Get-AzActivityLogAlert:](/powershell/module/az.monitor/get-azactivitylogalert)Obtém um ou mais recursos de alerta de registo de atividade.
- [Enable-AzActivityLogAlert](/powershell/module/az.monitor/enable-azactivitylogalert): Ativa um alerta de registo de atividade existente e define as suas etiquetas.
- [Desativar O AzActivityLogAlert](/powershell/module/az.monitor/disable-azactivitylogalert): Desativa um alerta de registo de atividade existente e define as suas etiquetas.
- [Remove-AzActivityLogAlert](/powershell/module/az.monitor/remove-azactivitylogalert): Remove um alerta de registo de atividade.

## <a name="azure-cli"></a>CLI do Azure

Os comandos dedicados do CLI Azure ao abrigo do [alerta de registo de atividade do monitor definido az](/cli/azure/monitor/activity-log/alert) estão disponíveis para gerir as regras de alerta de registo de atividade.

Para criar uma nova regra de alerta de registo de atividade, utilize os seguintes comandos nesta ordem:

1. [az monitor de alerta de registo de atividade criar:](/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-create)Criar um novo recurso de regra de alerta de registo de atividade.
1. [az monitor monitor de alerta de registo de atividade](/cli/azure/monitor/activity-log/alert/scope): Adicione o âmbito para a regra de alerta de registo de atividade criada.
1. [az monitorar o grupo de ação de alerta de registo de atividades](/cli/azure/monitor/activity-log/alert/action-group): Adicione um grupo de ação à regra de alerta de registo de atividade.

Para recuperar um recurso de regra de alerta de registo de atividade, utilize o [programa de alerta de registo de atividade do monitor Azure](/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-show
)CLI monitor . Para visualizar todos os recursos de regra de alerta de registo de atividade em um grupo de recursos, utilize [a lista de alerta de registo de atividade do monitor az](/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-list).
Os recursos de regra de alerta de registo de atividade podem ser removidos utilizando o [aviso de registo de atividade do monitor](/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-delete)Azure CLI .

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [o esquema webhook para registos de atividades](./activity-log-alerts-webhook.md).
- Leia uma [visão geral dos registos de atividades.](./activity-log-alerts.md)
- Saiba mais sobre [grupos de ação.](../platform/action-groups.md)  
- Saiba mais sobre [as notificações de saúde do serviço.](../../service-health/service-notifications.md)

