---
title: Criar definição de diagnóstico para recolher registos e métricas em Azure
description: Crie configurações de diagnóstico para encaminhar os registos da plataforma Azure para os Registos do Monitor Azure, armazenamento Azure ou Hubs de Eventos Azure.
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: fb2f9ff5af68575d9f9d29e9a6aca83d603395b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77672417"
---
# <a name="create-diagnostic-setting-to-collect-platform-logs-and-metrics-in-azure"></a>Criar definição de diagnóstico para recolher registos e métricas da plataforma em Azure
[Os registos da plataforma](platform-logs-overview.md) no Azure, incluindo registos de registos de atividades do Azure e de recursos, fornecem informações detalhadas de diagnóstico e auditoria para os recursos do Azure e da plataforma Azure de que dependem. Este artigo fornece detalhes sobre a criação e configuração de configurações de diagnóstico para enviar registos de plataformas para diferentes destinos.

> [!IMPORTANT]
> Antes de criar uma definição de diagnóstico para recolher o registo de Atividade, deve primeiro desativar qualquer configuração do legado. Consulte o [registo da Atividade Azure com definições de legado](diagnostic-settings-legacy.md) para mais detalhes.

Cada recurso Azure requer a sua própria definição de diagnóstico, que define o seguinte:

- Categorias de registos e dados métricos enviados para os destinos definidos na definição. As categorias disponíveis variarão para diferentes tipos de recursos.
- Um ou mais destinos para enviar os registos. Os destinos atuais incluem log analytics workspace, Event Hubs e Armazenamento Azure.
 
Uma única definição de diagnóstico pode definir não mais do que um dos destinos. Se quiser enviar dados para mais do que um tipo de destino particular (por exemplo, dois espaços de trabalho diferentes do Log Analytics), então crie várias configurações. Cada recurso pode ter até 5 configurações de diagnóstico.


> [!NOTE]
> [As métricas da plataforma](metrics-supported.md) são recolhidas automaticamente para [as Métricas do Monitor Azure](data-platform-metrics.md). As definições de diagnóstico podem ser utilizadas para recolher métricas para determinados serviços Azure em Registos do Monitor Azure para análise com outros dados de monitorização utilizando consultas de [registo](../log-query/log-query-overview.md).

## <a name="destinations"></a>Destinos 
Os registos da plataforma podem ser enviados para os destinos na tabela seguinte. A configuração para cada destino é realizada utilizando o mesmo processo para criar configurações de diagnóstico descritas neste artigo. Siga cada link na tabela seguinte para obter detalhes sobre o envio de dados para esse destino.

| Destino | Descrição |
|:---|:---|
| [Área de trabalho do Log Analytics](resource-logs-collect-workspace.md) | A recolha de registos num espaço de trabalho do Log Analytics permite-lhe analisá-los com outros dados de monitorização recolhidos pelo Azure Monitor utilizando consultas de registo poderosas e também para alavancar outras funcionalidades do Monitor de Acesso, como alertas e visualizações. |
| [Hubs de eventos](resource-logs-stream-event-hubs.md) | O envio de registos para Centros de Eventos permite-lhe transmitir dados para sistemas externos, tais como SIEMs de terceiros e outras soluções de análise de registo. |
| [Conta de armazenamento azure](resource-logs-collect-storage.md) | Arquivar registos numa conta de armazenamento Azure é útil para auditoria, análise estática ou cópia de segurança. |

## <a name="create-diagnostic-settings-in-azure-portal"></a>Criar configurações de diagnóstico no portal Azure
Pode configurar as definições de diagnóstico no portal Azure, quer a partir do menu Do Monitor Azure, quer a partir do menu do recurso.

1. Quando configurar as definições de diagnóstico no portal Azure depende do recurso.

   - Para um único recurso, clique em **definições de diagnóstico** no **Monitor** no menu do recurso.

        ![Definições de diagnóstico](media/diagnostic-settings/menu-resource.png)

    - Para um ou mais recursos, clique em **definições** de diagnóstico em **Definições** no menu Do Monitor Do Azure e, em seguida, clique no recurso.
    
        ![Definições de diagnóstico](media/diagnostic-settings/menu-monitor.png)

    - Para o registo de atividade, clique em **iniciar sessão de atividade** no menu Do Monitor **Azure** e, em seguida, **definições de diagnóstico**. Certifique-se de que desativa qualquer configuração do legado para o registo de Atividade. Consulte [as definições existentes](diagnostic-settings-legacy.md#disable-existing-settings) para obter mais detalhes.

        ![Definições de diagnóstico](media/diagnostic-settings/menu-activity-log.png)

2. Se não existirem definições no recurso selecionado, é-lhe solicitado que crie uma definição. Clique em **adicionar definição de diagnóstico**.

   ![Adicionar definição de diagnóstico - sem definições existentes](media/diagnostic-settings/add-setting.png)

   Se existirem definições existentes no recurso, verá uma lista de definições já configuradas. Clique em **Adicionar definição de diagnóstico** para adicionar uma nova definição ou editar **definição** para editar uma existente. Cada definição não pode ter mais do que um dos tipos de destino.

   ![Adicionar definição de diagnóstico - definições existentes](media/diagnostic-settings/edit-setting.png)

3. Dê um nome ao seu cenário se já não tiver um.
4. Verifique a caixa para cada destino para enviar os registos. Clique em **Configurar** para especificar as suas definições conforme descrito na tabela seguinte.

    | Definição | Descrição |
    |:---|:---|
    | Área de trabalho do Log Analytics | Nome do espaço de trabalho. |
    | Conta de armazenamento | Nome da conta de armazenamento. |
    | Espaço de nomes do hub de eventos | O espaço de nome onde o hub do evento é criado (se esta for a sua primeira vez em diários de streaming) ou transmitido para (se já existem recursos que estão a transmitir essa categoria de log para este espaço de nome).
    | Nome do hub de eventos | Especificar opcionalmente um nome de centro de eventos para enviar todos os dados na definição. Se não especificar um nome, é criado um centro de eventos para cada categoria de registo. Se estiver a enviar várias categorias, poderá querer especificar um nome para limitar o número de centros de eventos criados. Consulte as quotas e limites de limites do [Azure Event Hubs](../../event-hubs/event-hubs-quotas.md) para detalhes. |
    | Nome da política do centro de eventos | Define as permissões que o mecanismo de streaming tem. |

    ![Adicionar definição de diagnóstico - definições existentes](media/diagnostic-settings/setting-details.png)

5. Verifique a caixa para cada uma das categorias de dados a enviar para os destinos especificados. A lista de categorias variará para cada serviço Azure.

   > [!NOTE]
   > Atualmente, o envio de métricas multidimensionais através das definições de diagnóstico não é suportado. As métricas com dimensões são exportadas como métricas dimensionais simples e agregadas em valores de dimensões.
   >
   > *Por exemplo*: a métrica “Mensagens Recebidas” num hub do Hub de Eventos pode ser explorada e representada ao nível da linha. No entanto, se for exportada através das definições de diagnóstico, a métrica será representada como todas as mensagens recebidas em todas as filas do hub do Hub de Eventos.

6. Clique em **Guardar**.

Após alguns momentos, a nova configuração aparece na sua lista de configurações para este recurso, e os registos são transmitidos para os destinos especificados à medida que novos dados do evento são gerados. Note que pode haver até 15 minutos entre quando um evento é emitido e quando aparece num espaço de [trabalho log Analytics](data-ingestion-time.md).



## <a name="create-diagnostic-settings-using-powershell"></a>Criar definições de diagnóstico usando powerShell
Utilize o cmdlet [Set-AzDiagnosticSettingSetting](https://docs.microsoft.com/powershell/module/az.monitor/set-azdiagnosticsetting) para criar uma definição de diagnóstico com [o Azure PowerShell](powershell-quickstart-samples.md). Consulte a documentação para este cmdlet para obter descrições dos seus parâmetros.

> [!IMPORTANT]
> Não é possível utilizar este método para o registo da Atividade Azure. Em vez disso, utilize a definição de [diagnóstico Create no Monitor Azure utilizando um modelo](diagnostic-settings-template.md) de Gestor de Recursos para criar um modelo de Gestor de Recursos e implementá-lo com powerShell.

Segue-se um exemplo de cmdlet PowerShell para criar uma definição de diagnóstico utilizando os três destinos.


```powershell
Set-AzDiagnosticSetting -Name KeyVault-Diagnostics -ResourceId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault -Category AuditEvent -MetricCategory AllMetrics -Enabled $true -StorageAccountId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount -WorkspaceId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/myworkspace  -EventHubAuthorizationRuleId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhub/authorizationrules/RootManageSharedAccessKey
```


## <a name="create-diagnostic-settings-using-azure-cli"></a>Criar configurações de diagnóstico usando o Azure CLI
Utilize as [definições de diagnóstico](https://docs.microsoft.com/cli/azure/monitor/diagnostic-settings?view=azure-cli-latest#az-monitor-diagnostic-settings-create) do monitor az criar comando para criar uma definição de diagnóstico com o [Azure CLI](https://docs.microsoft.com/cli/azure/monitor?view=azure-cli-latest). Consulte a documentação para este comando para obter descrições dos seus parâmetros.

> [!IMPORTANT]
> Não é possível utilizar este método para o registo da Atividade Azure. Em vez disso, utilize a definição de [diagnóstico Create no Monitor Azure utilizando um modelo](diagnostic-settings-template.md) de Gestor de Recursos para criar um modelo de Gestor de Recursos e implementá-lo com CLI.

Segue-se um exemplo de comando CLI para criar uma definição de diagnóstico utilizando os três destinos.



```azurecli
az monitor diagnostic-settings create  \
--name KeyVault-Diagnostics \
--resource /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault \
--logs    '[{"category": "AuditEvent","enabled": true}]' \
--metrics '[{"category": "AllMetrics","enabled": true}]' \
--storage-account /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount \
--workspace /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/myworkspace \
--event-hub-rule /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhub/authorizationrules/RootManageSharedAccessKey
```

### <a name="configure-diagnostic-settings-using-rest-api"></a>Configure as definições de diagnóstico utilizando a API REST
Consulte [as Definições de Diagnóstico](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings) para criar ou atualizar as definições de diagnóstico utilizando a [API REST DO Monitor Azure](https://docs.microsoft.com/rest/api/monitor/).


### <a name="configure-diagnostic-settings-using-resource-manager-template"></a>Configure as definições de diagnóstico usando o modelo de Gestor de Recursos
Consulte [a definição de diagnóstico no Monitor Azure utilizando um modelo](diagnostic-settings-template.md) de Gestor de Recursos para criar ou atualizar as definições de diagnóstico com um modelo de Gestor de Recursos.

## <a name="next-steps"></a>Passos seguintes

* [Ler mais sobre a plataforma Azure Logs](platform-logs-overview.md)
