---
title: Recolher e analisar o log de atividade do Azure no Monitor Azure
description: Recolher o Registo de Atividades do Azure em Registos de MonitorEs Azure e utilizar a solução de monitorização para analisar e pesquisar o registo de atividade do Azure em todas as suas subscrições do Azure.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 04/14/2020
ms.openlocfilehash: 098aeaa06a26c57744402722aa3eacc51ea85fb7
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81382859"
---
# <a name="collect-and-analyze-azure-activity-log-in-azure-monitor"></a>Recolher e analisar o log da Atividade Azure no Monitor Azure
O [registo da Atividade Azure](platform-logs-overview.md) é um registo de [plataforma](platform-logs-overview.md) que fornece informações sobre eventos de nível de subscrição que ocorreram no Azure. Enquanto pode ver o registo de atividade no portal Azure, deve configurá-lo para enviar para um espaço de trabalho log Analytics para permitir funcionalidades adicionais do Monitor Azure. Este artigo descreve como executar esta configuração e como enviar o registo de atividade para centros de armazenamento e evento sinuosos do Azure.

Recolher o Registo de Atividades num espaço de trabalho de Log Analytics proporciona as seguintes vantagens:

- Nenhuma taxa de ingestão de dados ou de retenção de dados para os dados de registo de atividade armazenados num espaço de trabalho de Log Analytics.
- Correlacionar dados de registo de atividade com outros dados de monitorização recolhidos pelo Azure Monitor.
- Utilize consultas de registo para realizar análises complexas e obter informações profundas sobre as entradas de Registo de Atividade.
- Utilize alertas de registo com entradas de Atividade que permitam uma lógica de alerta mais complexa.
- Registo de registo de atividade de loja por mais de 90 dias.
- Consolidar entradas de registo de várias subscrições do Azure e inquilinos num único local para análise em conjunto.

> [!IMPORTANT]
> Recolher registos entre inquilinos requer [o Farol Azure.](/azure/lighthouse)

## <a name="collecting-activity-log"></a>Registo de Atividades de Recolha
O registo de Atividade é recolhido automaticamente para [visualização no portal Azure](activity-log-view.md). Para o recolher num espaço de trabalho do Log Analytics ou para o enviar centros de armazenamento ou eventos Azure, crie uma [definição de diagnóstico](diagnostic-settings.md). Este é o mesmo método utilizado pelos registos de recursos tornando-o consistente para todos os [registos da plataforma](platform-logs-overview.md).  

Para criar uma definição de diagnóstico para o registo de atividade, selecione **definições** de diagnóstico do menu de **registo de atividade** no Monitor Azure. Consulte [a definição de diagnóstico Create para recolher registos e métricas da plataforma em Azure](diagnostic-settings.md) para obter detalhes sobre a criação da configuração. Consulte [as categorias no registo de atividade](activity-log-view.md#categories-in-the-activity-log) para obter uma descrição das categorias que pode filtrar. Se tiver alguma definição de legado, certifique-se de que as desativa antes de criar uma definição de diagnóstico. Ter ambos ativados pode resultar em dados duplicados.

![Definições de diagnóstico](media/diagnostic-settings-subscription/diagnostic-settings.png)


> [!NOTE]
> Atualmente, só é possível criar uma definição de diagnóstico de nível de subscrição utilizando o portal Azure e um modelo de Gestor de Recursos. 


## <a name="legacy-settings"></a>Configurações do legado 
Embora as configurações de diagnóstico sejam o método preferido para enviar o registo de Atividade para diferentes destinos, os métodos legados continuarão a funcionar se não optar por substituir por uma definição de diagnóstico. As definições de diagnóstico têm as seguintes vantagens sobre métodos legados, e recomenda-se que atualize a sua configuração:

- Método consistente para recolher todos os registos da plataforma.
- Colete registo de Atividades em várias subscrições e inquilinos.
- Filtrar a recolha para recolher apenas registos para determinadas categorias.
- Colete todas as categorias de registo de Atividade. Algumas categorias não são recolhidas usando o método legado.
- Latência mais rápida para ingestão de loging. O método anterior tem cerca de 15 minutos de latência, enquanto as definições de diagnóstico adicionam apenas cerca de 1 minuto.



### <a name="log-profiles"></a>Perfis de registo
Os perfis de registo são o método legado para o envio do registo de atividade saque a centros de armazenamento ou eventos Do Azure. Utilize o seguinte procedimento para continuar a trabalhar com um perfil de registo ou para desativá-lo em preparação para migrar para uma definição de diagnóstico.

1. A partir do menu **Azure Monitor** no portal Azure, selecione **registo de atividade**.
3. Clique em **Definições de diagnóstico**.

   ![Definições de diagnóstico](media/diagnostic-settings-subscription/diagnostic-settings.png)

4. Clique no banner roxo para a experiência do legado.

    ![Experiência legado](media/diagnostic-settings-subscription/legacy-experience.png)

### <a name="log-analytics-workspace"></a>Área de trabalho do Log Analytics
O método legado para recolher o log de Atividade num espaço de trabalho do Log Analytics está a ligar o registo na configuração do espaço de trabalho. 

1. A partir do menu de espaços de **trabalho Log Analytics** no portal Azure, selecione o espaço de trabalho para recolher o Registo de Atividades.
1. Na secção **Fontes** de Dados do Espaço de Trabalho do menu do espaço de trabalho, selecione **o registo da Atividade Do Azure**.
1. Clique na subscrição que pretende ligar.

    ![Áreas de Trabalho](media/activity-log-collect/workspaces.png)

1. Clique em Ligar o **Connect** para ligar o registo de atividade na subscrição ao espaço de trabalho selecionado. Se a subscrição já estiver ligada a outro espaço de trabalho, clique em **Desligar** primeiro para desconectá-la.

    ![Conectar espaços de trabalho](media/activity-log-collect/connect-workspace.png)


Para desativar a definição, execute o mesmo procedimento e clique em **Desligar** para remover a subscrição do espaço de trabalho.


## <a name="analyze-activity-log-in-log-analytics-workspace"></a>Analisar o log de atividade no espaço de trabalho do Log Analytics
Quando ligar um Registo de Atividade a um espaço de trabalho de Log Analytics, as entradas serão escritas para o espaço de trabalho numa tabela chamada *AzureActivity* que pode recuperar com uma consulta de [log](../log-query/log-query-overview.md). A estrutura deste quadro varia consoante a [categoria da entrada de registo](activity-log-view.md#categories-in-the-activity-log). Consulte o esquema do evento De Log de Atividade do [Azure](activity-log-schema.md) para obter uma descrição de cada categoria.


### <a name="data-structure-changes"></a>Alterações na estrutura de dados
As definições de diagnóstico recolhem os mesmos dados que o método legado utilizado para recolher o registo de Atividade com algumas alterações na estrutura da tabela *AzureActivity.*

As colunas da tabela seguinte foram depreciadas no esquema atualizado. Ainda existem no *AzureActivity,* mas não terão dados. A substituição destas colunas não é nova, mas contém os mesmos dados que a coluna depreciada. Estão num formato diferente, pelo que poderá ser necessário modificar as consultas de registo que as utilizam. 

| Coluna depreciada | Coluna de substituição |
|:---|:---|
| Estatuto de Atividade    | ActivityStatusValue    |
| Subestatuto de atividade | ActivitySubstatusValue |
| OperationName     | OperationNameValue     |
| ResourceProvider  | Valor do Fornecedor de Recursos  |

> [!IMPORTANT]
> Em alguns casos, os valores nestas colunas podem estar em todas as maiúsculas. Se tiver uma consulta que inclua estas colunas, deve utilizar o [operador =~](https://docs.microsoft.com/azure/kusto/query/datatypes-string-operators) para fazer uma comparação insensível.

A seguinte coluna foi adicionada à *AzureActivity* no esquema atualizado:

- Authorization_d
- Claims_d
- Properties_d


## <a name="activity-logs-analytics-monitoring-solution"></a>Solução de monitorização de Logs de Atividade Analytics
A solução de monitorização Azure Log Analytics será depreciada em breve e substituída por um livro utilizando o esquema atualizado no espaço de trabalho log Analytics. Ainda pode utilizar a solução se já a tiver ativada, mas só poderá ser utilizada se estiver a recolher o registo de Atividade utilizando definições antigas. 



### <a name="use-the-solution"></a>Use a solução
As soluções de monitorização são acedidas a partir do menu **Monitor** no portal Azure. Selecione **Mais** na secção **Insights** para abrir a página **'Overview'** com os azulejos da solução. O azulejo **Azure Activity Logs** apresenta uma contagem do número de registos **azureActivity** no seu espaço de trabalho.

![Azulejos de logs de atividade azure](media/collect-activity-logs/azure-activity-logs-tile.png)


Clique no azulejo **Azure Activity Logs** para abrir a vista Registos de Atividade sintetizações **do Azure.** A vista inclui as peças de visualização na tabela a seguir. Cada peça lista até 10 itens que correspondam aos critérios dessas peças para o intervalo de tempo especificado. Pode executar uma consulta de registo que devolve todos os registos correspondentes clicando **Ver tudo** na parte inferior da peça.

![Painel de logs de atividade azure](media/collect-activity-logs/activity-log-dash.png)


### <a name="enable-the-solution-for-new-subscriptions"></a>Ativar a solução para novas subscrições
Em breve deixará de poder adicionar a solução Activity Logs Analytics à sua subscrição através do portal Azure. Pode adicioná-lo utilizando o seguinte procedimento com um modelo de gestor de recursos. 

1. Copie o seguinte json num ficheiro chamado *ActivityLogTemplate*.json.

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

2. Desdobrar o modelo utilizando os seguintes comandos PowerShell:

    ```PowerShell
    Connect-AzAccount
    Select-AzSubscription <SubscriptionName>
    New-AzResourceGroupDeployment -Name activitysolution -ResourceGroupName <ResourceGroup> -TemplateFile <Path to template file>
    ```


## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre o [Registo de Atividades](platform-logs-overview.md).
- Saiba mais sobre a plataforma de [dados Azure Monitor.](data-platform.md)
- Utilize consultas de [registo](../log-query/log-query-overview.md) para visualizar informações detalhadas do seu Registo de Atividades.
