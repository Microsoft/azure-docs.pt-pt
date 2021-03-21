---
title: Registo de atividades do Azure
description: Veja o registo de atividades Azure e envie-o para Registos monitores Azure, Hubs de Eventos Azure e Armazenamento Azure.
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 06/12/2020
ms.author: bwren
ms.openlocfilehash: 7583b4037d350b9190d6eae30c28b907b1d41d86
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "104722859"
---
# <a name="azure-activity-log"></a>Registo de atividades do Azure
O Registo de atividades é um [registo de plataformas](./platform-logs-overview.md) no Azure que proporciona informações sobre eventos ao nível da subscrição. Tal inclui informações como quando um recurso é modificado ou quando uma máquina virtual é iniciada. Pode visualizar o registo de Atividade no portal Azure ou recuperar entradas com PowerShell e CLI. Para obter funcionalidades adicionais, deverá criar uma definição de diagnóstico para enviar o registo de Atividade para [Registos do Monitor Azure,](../logs/data-platform-logs.md)para Azure Event Hubs para encaminhar para fora do Azure, ou para o Azure Storage para arquivar. Este artigo fornece detalhes sobre a visualização do registo de atividade e o envio para diferentes destinos.

Consulte [Configurações de diagnóstico para enviar registos e métricas da plataforma para diferentes destinos](./diagnostic-settings.md) para obter detalhes sobre a criação de uma definição de diagnóstico.

> [!NOTE]
> As entradas no Registo de Atividades são geradas pelo sistemas e não podem ser alteradas nem eliminadas.

## <a name="view-the-activity-log"></a>Ver o registo de atividades
Pode aceder ao Registo de atividades a partir da maioria dos menus do portal do Azure. O menu a partir do qual abre o Registo de atividades determina o filtro inicial. Se o abrir a partir do menu **Monitor,** o único filtro estará na subscrição. Se o abrir a partir do menu de um recurso, o filtro será definido para esse recurso. Pode sempre alterar o filtro para visualizar todas as outras entradas. Clique **em Adicionar Filtro** para adicionar propriedades adicionais ao filtro.

![Ver Registo de Atividades](./media/activity-log/view-activity-log.png)

Para uma descrição das categorias de registo de atividade consulte [o esquema do evento Azure Activity Log](activity-log-schema.md#categories).

## <a name="download-the-activity-log"></a>Descarregue o registo de Atividades
Selecione **Baixar como CSV** para descarregar os eventos na vista atual.

![Baixar registo de atividades](media/activity-log/download-activity-log.png)

### <a name="view-change-history"></a>Ver alterar história

Para alguns eventos, pode ver a história da Mudança, que mostra as mudanças que aconteceram durante esse tempo de evento. Selecione um evento a partir do Registo de Atividades que pretende aprofundar. Selecione o separador Alterar histórico **(Pré-visualização)** para ver quaisquer alterações associadas a esse evento.

![Alterar lista de história para um evento](media/activity-log/change-history-event.png)

Se houver alguma alteração associada ao evento, verá uma lista de alterações que pode selecionar. Isto abre a página **De Histórico de Alteração (Pré-visualização).** Nesta página vê-se as alterações ao recurso. No exemplo seguinte, pode ver não só que o VM mudou de tamanho, mas qual era o tamanho anterior do VM antes da mudança e para o que foi alterado. Para saber mais sobre a história da mudança, consulte [Obter alterações de recursos.](../../governance/resource-graph/how-to/get-resource-changes.md)

![Alterar página de história mostrando diferenças](media/activity-log/change-history-event-details.png)


### <a name="other-methods-to-retrieve-activity-log-events"></a>Outros métodos para recuperar eventos de registo de atividade
Também pode aceder a eventos de registo de atividades utilizando os seguintes métodos.

- Utilize o [cmdlet Get-AzLog](/powershell/module/az.monitor/get-azlog) para recuperar o Registo de Atividade da PowerShell. Consulte [as amostras powerShell do Monitor Azure](../powershell-samples.md#retrieve-activity-log).
- Utilize [log de atividade do monitor az](/cli/azure/monitor/activity-log) para recuperar o Registo de Atividade do CLI.  Consulte [as amostras do CLI do Monitor Azure](../cli-samples.md#view-activity-log).
- Utilize a [API REST Monitor Azure](/rest/api/monitor/) para recuperar o Registo de Atividades de um cliente REST. 


## <a name="send-to-log-analytics-workspace"></a>Enviar para a área de trabalho do Log Analytics
 Envie o registo de Atividade para um espaço de trabalho Log Analytics para ativar as funcionalidades de [Registos do Monitor Azure,](../logs/data-platform-logs.md) que inclui o seguinte:

- Correlacionar os dados de registo de atividade com outros dados de monitorização recolhidos pelo Azure Monitor.
- Consolidar entradas de registo de várias subscrições e inquilinos do Azure num local para análise em conjunto.
- Utilize consultas de registo para realizar análises complexas e obter informações profundas sobre as entradas de Registo de Atividade.
- Utilize alertas de registo com entradas de Atividade permitindo uma lógica de alerta mais complexa.
- Armazenar entradas de registo de atividade por mais de 90 dias.
- Não há taxas de ingestão de dados para dados de registo de atividade armazenados num espaço de trabalho do Log Analytics.
- Não há taxas de retenção de dados até 90 dias para os dados de registo de atividade armazenados num espaço de trabalho do Log Analytics.

[Crie uma definição de diagnóstico](./diagnostic-settings.md) para enviar o registo de Atividade para um espaço de trabalho Log Analytics. Pode enviar o registo de Atividade de qualquer subscrição para até cinco espaços de trabalho. A recolha de registos entre inquilinos requer o [Azure Lighthouse](../../lighthouse/index.yml).

Os dados de registo de atividade num espaço de trabalho do Log Analytics são armazenados numa tabela chamada *AzureActivity* que pode obter com uma [consulta de log](../logs/log-query-overview.md) em Log [Analytics](../logs/log-analytics-tutorial.md). A estrutura deste quadro varia consoante a [categoria da entrada de registo](activity-log-schema.md). Para obter uma descrição das propriedades da tabela, consulte a referência de dados do [Azure Monitor](/azure/azure-monitor/reference/tables/azureactivity).

Por exemplo, para visualizar uma contagem de registos de atividade para cada categoria, utilize a seguinte consulta.

```kusto
AzureActivity
| summarize count() by CategoryValue
```

Para obter todos os registos na categoria administrativa, utilize a seguinte consulta.

```kusto
AzureActivity
| where CategoryValue == "Administrative"
```


## <a name="send-to-azure-event-hubs"></a>Enviar para Azure Event Hubs
Envie o Registo de Atividades para Azure Event Hubs para enviar entradas fora do Azure, por exemplo para um SIEM de terceiros ou outras soluções de análise de registo. Os eventos de registo de atividade dos centros de eventos são consumidos em formato JSON com um `records` elemento que contém os registos em cada carga útil. O esquema depende da categoria e é descrito em [Schema a partir de centros de conta de armazenamento e eventos.](activity-log-schema.md)

Seguem-se os dados de saída da amostra dos Centros de Eventos para um registo de atividade:

``` JSON
{
    "records": [
        {
            "time": "2019-01-21T22:14:26.9792776Z",
            "resourceId": "/subscriptions/s1/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841",
            "operationName": "microsoft.support/supporttickets/write",
            "category": "Write",
            "resultType": "Success",
            "resultSignature": "Succeeded.Created",
            "durationMs": 2826,
            "callerIpAddress": "111.111.111.11",
            "correlationId": "c776f9f4-36e5-4e0e-809b-c9b3c3fb62a8",
            "identity": {
                "authorization": {
                    "scope": "/subscriptions/s1/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841",
                    "action": "microsoft.support/supporttickets/write",
                    "evidence": {
                        "role": "Subscription Admin"
                    }
                },
                "claims": {
                    "aud": "https://management.core.windows.net/",
                    "iss": "https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db47/",
                    "iat": "1421876371",
                    "nbf": "1421876371",
                    "exp": "1421880271",
                    "ver": "1.0",
                    "http://schemas.microsoft.com/identity/claims/tenantid": "00000000-0000-0000-0000-000000000000",
                    "http://schemas.microsoft.com/claims/authnmethodsreferences": "pwd",
                    "http://schemas.microsoft.com/identity/claims/objectidentifier": "2468adf0-8211-44e3-95xq-85137af64708",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "admin@contoso.com",
                    "puid": "20030000801A118C",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "9vckmEGF7zDKk1YzIY8k0t1_EAPaXoeHyPRn6f413zM",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname": "John",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname": "Smith",
                    "name": "John Smith",
                    "groups": "cacfe77c-e058-4712-83qw-f9b08849fd60,7f71d11d-4c41-4b23-99d2-d32ce7aa621c,31522864-0578-4ea0-9gdc-e66cc564d18c",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name": " admin@contoso.com",
                    "appid": "c44b4083-3bq0-49c1-b47d-974e53cbdf3c",
                    "appidacr": "2",
                    "http://schemas.microsoft.com/identity/claims/scope": "user_impersonation",
                    "http://schemas.microsoft.com/claims/authnclassreference": "1"
                }
            },
            "level": "Information",
            "location": "global",
            "properties": {
                "statusCode": "Created",
                "serviceRequestId": "50d5cddb-8ca0-47ad-9b80-6cde2207f97c"
            }
        }
    ]
}
```


## <a name="send-to--azure-storage"></a>Enviar para o armazém da Azure
Envie o Registo de Atividades para uma conta de Armazenamento Azure se pretender reter os seus dados de registo por mais de 90 dias para auditoria, análise estática ou backup. Se apenas precisar de reter os seus eventos durante 90 dias ou menos, não precisa de configurar o arquivo para uma conta de armazenamento, uma vez que os eventos de Registo de Atividade são mantidos na plataforma Azure durante 90 dias.

Quando envia o registo de Atividade para Azure, é criado um recipiente de armazenamento na conta de armazenamento assim que ocorre um evento. As bolhas no recipiente utilizam a seguinte convenção de nomeação:

```
insights-activity-logs/resourceId=/SUBSCRIPTIONS/{subscription ID}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

Por exemplo, uma bolha em particular pode ter um nome semelhante ao seguinte:

```
insights-logs-networksecuritygrouprulecounter/resourceId=/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/y=2020/m=06/d=08/h=18/m=00/PT1H.json
```

Cada blob PT1H.json contém um blob JSON de eventos que ocorreram dentro da hora especificada no URL do blob (por exemplo, h=12). Durante a hora presente, os eventos são acrescentados ao ficheiro PT1H.json à medida que ocorrem. O valor dos minutos (m=00) é sempre de 00, uma vez que os eventos de registo de recursos são divididos em bolhas individuais por hora.

Cada evento é armazenado no PT1H.jsem ficheiro com o seguinte formato que utiliza um esquema de nível superior comum, mas é de outra forma único para cada categoria, conforme descrito no  [esquema de registo de atividade](activity-log-schema.md).

``` JSON
{ "time": "2020-06-12T13:07:46.766Z", "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/MY-RESOURCE-GROUP/PROVIDERS/MICROSOFT.COMPUTE/VIRTUALMACHINES/MV-VM-01", "correlationId": "0f0cb6b4-804b-4129-b893-70aeeb63997e", "operationName": "Microsoft.Resourcehealth/healthevent/Updated/action", "level": "Information", "resultType": "Updated", "category": "ResourceHealth", "properties": {"eventCategory":"ResourceHealth","eventProperties":{"title":"This virtual machine is starting as requested by an authorized user or process. It will be online shortly.","details":"VirtualMachineStartInitiatedByControlPlane","currentHealthStatus":"Unknown","previousHealthStatus":"Unknown","type":"Downtime","cause":"UserInitiated"}}}
```


## <a name="legacy-collection-methods"></a>Métodos de recolha de legados
Esta secção descreve métodos antigos para a recolha do registo de atividade que foram utilizados antes das definições de diagnóstico. Se estiver a utilizar estes métodos, deve considerar a transição para definições de diagnóstico que proporcionem uma melhor funcionalidade e consistência com registos de recursos.

### <a name="log-profiles"></a>Perfis de registo
Os perfis de registo são o método legado para o envio do registo de atividade para o armazenamento de Azure ou centros de eventos. Utilize o seguinte procedimento para continuar a trabalhar com um perfil de registo ou desativá-lo em preparação para migrar para uma definição de diagnóstico.

1. A partir do menu **Azure Monitor** no portal Azure, selecione **Registo de atividades**.
3. Clique em **Definições de diagnóstico**.

   ![Definições de diagnóstico](media/activity-log/diagnostic-settings.png)

4. Clique no banner roxo para a experiência do legado.

    ![Experiência antiga](media/activity-log/legacy-experience.png)



### <a name="configure-log-profile-using-powershell"></a>Configure o perfil de registo usando o PowerShell

Se já existe um perfil de registo, primeiro tem de remover o perfil de registo existente e, em seguida, criar um novo.

1. Utilize `Get-AzLogProfile` para identificar se existe um perfil de registo.  Se existir um perfil de registo, note a propriedade *do nome.*

1. Utilize `Remove-AzLogProfile` para remover o perfil de registo utilizando o valor da propriedade do *nome.*

    ```powershell
    # For example, if the log profile name is 'default'
    Remove-AzLogProfile -Name "default"
    ```

3. Utilize `Add-AzLogProfile` para criar um novo perfil de registo:

    ```powershell
    Add-AzLogProfile -Name my_log_profile -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Location global,westus,eastus -RetentionInDays 90 -Category Write,Delete,Action
    ```

    | Propriedade | Necessário | Descrição |
    | --- | --- | --- |
    | Nome |Yes |O nome do seu perfil de registo. |
    | ArmazenamentoAccountId |No |Identificação de recursos da Conta de Armazenamento onde o Registo de Atividade deve ser guardado. |
    | serviçoBusRuleId |No |Service Bus Rule ID para o espaço de nomes do Service Bus que você gostaria de ter centros de eventos criados. Esta é uma corda com o formato: `{service bus resource ID}/authorizationrules/{key name}` . |
    | Localização |Sim |Lista de regiões separadas por vírgulas para as quais gostaria de recolher eventos de Registo de Atividade. |
    | Retenção Dias |Yes |Número de dias para os quais os eventos devem ser mantidos na conta de armazenamento, entre 1 e 365. Um valor de zero armazena os registos indefinidamente. |
    | Categoria |No |Lista separada por vírgula das categorias de eventos que devem ser recolhidas. Os valores possíveis são _Write,_ _Delete_ e _Action_. |

### <a name="example-script"></a>Script de exemplo
Segue-se uma amostra do script PowerShell para criar um perfil de registo que escreve o Registo de Atividades tanto para uma conta de armazenamento como para um centro de eventos.

   ```powershell
   # Settings needed for the new log profile
   $logProfileName = "default"
   $locations = (Get-AzLocation).Location
   $locations += "global"
   $subscriptionId = "<your Azure subscription Id>"
   $resourceGroupName = "<resource group name your event hub belongs to>"
   $eventHubNamespace = "<event hub namespace>"

   # Build the service bus rule Id from the settings above
   $serviceBusRuleId = "/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.EventHub/namespaces/$eventHubNamespace/authorizationrules/RootManageSharedAccessKey"

   # Build the storage account Id from the settings above
   $storageAccountId = "/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Storage/storageAccounts/$storageAccountName"

   Add-AzLogProfile -Name $logProfileName -Location $locations -StorageAccountId  $storageAccountId -ServiceBusRuleId $serviceBusRuleId
   ```


### <a name="configure-log-profile-using-azure-cli"></a>Configure o perfil de registo usando o CLI Azure

Se já existe um perfil de registo, primeiro tem de remover o perfil de registo existente e, em seguida, criar um novo perfil de registo.

1. Utilize `az monitor log-profiles list` para identificar se existe um perfil de registo.
2. Utilize `az monitor log-profiles delete --name "<log profile name>` para remover o perfil de registo utilizando o valor da propriedade do *nome.*
3. Utilize `az monitor log-profiles create` para criar um novo perfil de registo:

   ```azurecli-interactive
   az monitor log-profiles create --name "default" --location null --locations "global" "eastus" "westus" --categories "Delete" "Write" "Action"  --enabled false --days 0 --service-bus-rule-id "/subscriptions/<YOUR SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventHub/namespaces/<EVENT HUB NAME SPACE>/authorizationrules/RootManageSharedAccessKey"
   ```

    | Propriedade | Necessário | Descrição |
    | --- | --- | --- |
    | name |Yes |O nome do seu perfil de registo. |
    | armazenamento-id conta |Yes |Identificação de recursos da Conta de Armazenamento para a qual devem ser guardados registos de atividade. |
    | Locais |Yes |Lista de regiões separadas pelo espaço para as quais gostaria de recolher eventos de Registo de Atividade. Pode ver uma lista de todas as regiões para a sua subscrição utilizando `az account list-locations --query [].name` . |
    | Dias |Yes |Número de dias para os quais os eventos devem ser mantidos, entre 1 e 365. Um valor de zero armazenará os registos indefinidamente (para sempre).  Se zero, então o parâmetro ativado deve ser definido como falso. |
    |ativado | Yes |Verdadeiro ou Falso.  Usado para ativar ou desativar a política de retenção.  Se for verdade, então o parâmetro dos dias deve ser um valor superior a 0.
    | categories |Yes |Lista separada do espaço das categorias de eventos que devem ser recolhidas. Os valores possíveis são escrever, eliminar e agir. |


### <a name="log-analytics-workspace"></a>Área de trabalho do Log Analytics
O método legado para enviar o login de Atividade para um espaço de trabalho Log Analytics está a ligar o registo na configuração do espaço de trabalho. 

1. A partir do menu **de espaços de trabalho Log Analytics** no portal Azure, selecione o espaço de trabalho para recolher o Registo de Atividades.
1. Na secção **Fontes** de Dados do Espaço de Trabalho do menu do espaço de trabalho, selecione **registo de Atividade Azure**.
1. Clique na subscrição que pretende ligar.

    ![A screenshot mostra log analytics espaço de trabalho com um registo de atividade Azure selecionado.](media/activity-log/workspaces.png)

2. Clique em **Ligar** para ligar o registo de Atividade na subscrição do espaço de trabalho selecionado. Se a subscrição já estiver ligada a outro espaço de trabalho, clique em **Desligar** primeiro para desconectá-la.

    ![Ligar espaços de trabalho](media/activity-log/connect-workspace.png)


Para desativar a definição, execute o mesmo procedimento e clique em **Desligar** para remover a subscrição do espaço de trabalho.

### <a name="data-structure-changes"></a>Alterações na estrutura de dados
As definições de diagnóstico enviam os mesmos dados que o método legado utilizado para enviar o registo de atividade com algumas alterações na estrutura da tabela *AzureActivity.*

As colunas da tabela seguinte foram depreciadas no esquema atualizado. Ainda existem no *AzureActivity,* mas não terão dados. A substituição destas colunas não é nova, mas contém os mesmos dados que a coluna prevada. Estão num formato diferente, por isso poderá ser necessário modificar consultas de registo que as utilizem. 

| Coluna precotado | Coluna de substituição |
|:---|:---|
| Estatísticas de Atividades    | ActivityStatusValue    |
| ActividadeSubstatus | AtividadeSSubstatusValue |
| Categoria          | CategoriaValue          |
| OperationName     | Operação Natalvalue     |
| ResourceProvider  | ResourceProviderValue  |

> [!IMPORTANT]
> Em certos casos, os valores nestas colunas podem estar em maiúsculas. Se tiver uma consulta com estas colunas, deverá utilizar o [operador =~](/azure/kusto/query/datatypes-string-operators) para fazer uma comparação não sensível a maiúsculas e minúsculas.

A seguinte coluna foi adicionada à *AzureActivity* no esquema atualizado:

- Authorization_d
- Claims_d
- Properties_d

## <a name="activity-log-analytics-monitoring-solution"></a>Solução de monitorização do Log Analytics de Atividade
A solução de monitorização do Azure Log Analytics será depreciada em breve e substituída por um livro utilizando o esquema atualizado no espaço de trabalho do Log Analytics. Ainda pode utilizar a solução se já a tiver ativada, mas só pode ser utilizada se estiver a recolher o registo de Atividade utilizando definições antigas. 



### <a name="use-the-solution"></a>Use a solução
As soluções de monitorização são acedidas a partir do menu **Monitor** no portal Azure. Selecione **Mais** na secção **Insights** para abrir a página **'Vista Geral'** com os azulejos da solução. O azulejo **Azure Activity Logs** apresenta uma contagem do número de registos **AzureActivity** no seu espaço de trabalho.

![Azulejo de Registos de Atividade Azure](media/activity-log/azure-activity-logs-tile.png)


Clique no azulejo **Azure Activity Logs** para abrir a vista **Azure Activity Logs.** A vista inclui as partes de visualização na tabela seguinte. Cada peça lista até 10 itens correspondentes aos critérios das peças para o intervalo de tempo especificado. Pode executar uma consulta de registo que devolva todos os registos correspondentes clicando em **Ver tudo** na parte inferior da peça.

![Painel de registos de atividade azure](media/activity-log/activity-log-dash.png)


### <a name="enable-the-solution-for-new-subscriptions"></a>Permitir a solução para novas subscrições
Em breve deixará de ser capaz de adicionar a solução Activity Logs Analytics à sua subscrição utilizando o portal Azure. Pode adicioná-lo utilizando o seguinte procedimento com um modelo de Gestor de Recursos. 

1. Copie o json seguinte num ficheiro chamado *ActivityLogTemplate*.json.

    ```json
    {
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceName": {
            "type": "String",
            "defaultValue": "my-workspace",
            "metadata": {
              "description": "Specifies the name of the workspace."
            }
        },
        "location": {
            "type": "String",
            "allowedValues": [
              "east us",
              "west us",
              "australia central",
              "west europe"
            ],
            "defaultValue": "australia central",
            "metadata": {
              "description": "Specifies the location in which to create the workspace."
            }
        }
      },
        "resources": [
        {
            "type": "Microsoft.OperationalInsights/workspaces",
            "name": "[parameters('workspaceName')]",
            "apiVersion": "2015-11-01-preview",
            "location": "[parameters('location')]",
            "properties": {
                "features": {
                    "searchVersion": 2
                }
            }
        },
        {
            "type": "Microsoft.OperationsManagement/solutions",
            "apiVersion": "2015-11-01-preview",
            "name": "[concat('AzureActivity(', parameters('workspaceName'),')')]",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[resourceId('microsoft.operationalinsights/workspaces', parameters('workspaceName'))]"
            ],
            "plan": {
                "name": "[concat('AzureActivity(', parameters('workspaceName'),')')]",
                "promotionCode": "",
                "product": "OMSGallery/AzureActivity",
                "publisher": "Microsoft"
            },
            "properties": {
                "workspaceResourceId": "[resourceId('microsoft.operationalinsights/workspaces', parameters('workspaceName'))]",
                "containedResources": [
                    "[concat(resourceId('microsoft.operationalinsights/workspaces', parameters('workspaceName')), '/views/AzureActivity(',parameters('workspaceName'))]"
                ]
            }
        },
        {
          "type": "Microsoft.OperationalInsights/workspaces/datasources",
          "kind": "AzureActivityLog",
          "name": "[concat(parameters('workspaceName'), '/', subscription().subscriptionId)]",
          "apiVersion": "2015-11-01-preview",
          "location": "[parameters('location')]",
          "dependsOn": [
              "[parameters('WorkspaceName')]"
          ],
          "properties": {
              "linkedResourceId": "[concat(subscription().Id, '/providers/microsoft.insights/eventTypes/management')]"
          }
        }
      ]
    }    
    ```

2. Implementar o modelo utilizando os seguintes comandos PowerShell:

    ```PowerShell
    Connect-AzAccount
    Select-AzSubscription <SubscriptionName>
    New-AzResourceGroupDeployment -Name activitysolution -ResourceGroupName <ResourceGroup> -TemplateFile <Path to template file>
    ```



## <a name="next-steps"></a>Passos seguintes

* [Leia uma visão geral dos registos da plataforma](./platform-logs-overview.md)
* [Rever esquema de evento de log de atividade](activity-log-schema.md)
* [Criar definição de diagnóstico para enviar registos de atividade para outros destinos](./diagnostic-settings.md)
