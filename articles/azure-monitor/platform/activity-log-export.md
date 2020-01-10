---
title: Exportar o log de atividades do Azure
description: Exportar o log de atividades do Azure para o armazenamento para arquivamento ou hubs de eventos do Azure para exportação fora do Azure.
author: bwren
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: b71f5590f120e15bd4ea027bcf6132795dac3cb6
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/08/2020
ms.locfileid: "75750568"
---
# <a name="export-azure-activity-log-to-storage-or-azure-event-hubs"></a>Exportar o log de atividades do Azure para o armazenamento ou hubs de eventos do Azure

> [!WARNING]
> Agora você pode coletar o log de atividades em um espaço de trabalho Log Analytics usando uma configuração de diagnóstico semelhante a como você coleta logs de recursos. Consulte [coletar e analisar logs de atividades do Azure no espaço de trabalho log Analytics no Azure monitor](diagnostic-settings-subscription.md).

O [log de atividades do Azure](platform-logs-overview.md) fornece informações sobre eventos no nível da assinatura que ocorreram em sua assinatura do Azure. Além de exibir o log de atividades no portal do Azure ou copiá-lo para um espaço de trabalho Log Analytics onde ele pode ser analisado com outros dados coletados pelo Azure Monitor, você pode criar um perfil de log para arquivar o log de atividades em uma conta de armazenamento do Azure ou transmiti-lo para um  Hub de eventos.

## <a name="archive-activity-log"></a>Log de atividades de arquivamento
O arquivamento do log de atividades em uma conta de armazenamento é útil se você quiser manter seus dados de log por mais de 90 dias (com controle total sobre a política de retenção) para auditoria, análise estática ou backup. Se você só precisa reter seus eventos por 90 dias ou menos, não é necessário configurar o arquivamento para uma conta de armazenamento, pois os eventos do log de atividades são mantidos na plataforma do Azure por 90 dias.

## <a name="stream-activity-log-to-event-hub"></a>Transmitir log de atividades para o Hub de eventos
Os [hubs de eventos do Azure](/azure/event-hubs/) são uma plataforma de streaming de dados e um serviço de ingestão de eventos que pode receber e processar milhões de eventos por segundo. Os dados enviados para um hub de eventos podem ser transformados e armazenados em qualquer fornecedor de análise em tempo real ou adaptadores de armazenamento/criação de batches. Duas maneiras de usar o recurso de streaming para o log de atividades são:
* **Transmitir para sistemas de registro em log e telemetria de**terceiros: ao longo do tempo, o streaming de hubs de eventos do Azure se tornará o mecanismo para canalizar seu log de atividades em soluções de Siem e do log Analytics de terceiros.
* **Crie uma plataforma de registro em log e telemetria personalizada**: se você já tiver uma plataforma de telemetria personalizada ou estiver pensando em criar uma, a natureza altamente escalonável de publicação-assinatura dos hubs de eventos permite ingerir o log de atividades com flexibilidade. 

## <a name="prerequisites"></a>Pré-requisitos

### <a name="storage-account"></a>Conta de armazenamento
Se você estiver arquivando o log de atividades, precisará [criar uma conta de armazenamento](../../storage/common/storage-quickstart-create-account.md) se ainda não tiver uma. Você não deve usar uma conta de armazenamento existente que tenha outros dados de não monitoramento armazenados nele para que você possa controlar melhor o acesso aos dados de monitoramento. Se você também estiver arquivando logs e métricas em uma conta de armazenamento, poderá optar por usar essa mesma conta de armazenamento para manter todos os dados de monitoramento em um local central.

A conta de armazenamento não precisa estar na mesma assinatura que a assinatura que emite os logs, contanto que o usuário que define a configuração tenha acesso RBAC apropriado a ambas as assinaturas.
> [!NOTE]
>  Atualmente, não é possível arquivar dados em uma conta de armazenamento que esteja atrás de uma rede virtual protegida.

### <a name="event-hubs"></a>Event Hubs
Se você estiver enviando o log de atividades para um hub de eventos, você precisará [criar um hub de eventos](../../event-hubs/event-hubs-create.md) se ainda não tiver um. Se você tiver transmitido anteriormente eventos do log de atividades para esse namespace de hubs de eventos, esse Hub de eventos será reutilizado.

A política de acesso compartilhado define as permissões que o mecanismo de streaming tem. O streaming para hubs de eventos requer permissões de gerenciar, enviar e escutar. Você pode criar ou modificar políticas de acesso compartilhado para o namespace de hubs de eventos na portal do Azure na guia Configurar para seu namespace de hubs de eventos.

Para atualizar o perfil de log do log de atividades para incluir streaming, você deve ter a permissão ListKey nessa regra de autorização de hubs de eventos. O namespace de hubs de eventos não precisa estar na mesma assinatura que a assinatura que está emitindo logs, contanto que o usuário que define a configuração tenha acesso RBAC apropriado a ambas as assinaturas e ambas as assinaturas estejam no mesmo locatário do AAD.

Transmita o log de atividades para um hub de eventos [criando um perfil de log](#create-a-log-profile).

## <a name="create-a-log-profile"></a>Criar um perfil de log
Você define como o log de atividades do Azure é exportado usando um **perfil de log**. Cada assinatura do Azure pode ter apenas um perfil de log. Essas configurações podem ser definidas por meio da opção **Exportar** na folha log de atividades no Portal. Eles também podem ser configurados programaticamente [usando a API REST do Azure monitor, os](https://msdn.microsoft.com/library/azure/dn931927.aspx)cmdlets do PowerShell ou a CLI.

O perfil de log define o seguinte.

**Onde o log de atividades deve ser enviado.** Atualmente, as opções disponíveis são conta de armazenamento ou hubs de eventos.

**Quais categorias de evento devem ser enviadas.** O significado da *categoria* nos perfis de log e nos eventos do log de atividades é diferente. No perfil de log, *categoria* representa o tipo de operação (gravação, exclusão, ação). Em um evento do log de atividades, a propriedade *categoria*"* representa a origem ou o tipo de evento (por exemplo, administração, integridade e alerta).

**Quais regiões (locais) devem ser exportadas.** Você deve incluir todos os locais, pois muitos eventos no log de atividades são eventos globais.

**Por quanto tempo o log de atividades deve ser retido em uma conta de armazenamento.** A retenção de zero dias significa que os registos são mantidos para sempre. Caso contrário, o valor pode ser qualquer número de dias entre 1 e 365.

Se as políticas de retenção forem definidas, mas o armazenamento de logs em uma conta de armazenamento estiver desabilitado, as políticas de retenção não terão nenhum efeito. Políticas de retenção são aplicado por dia, portanto, no final do dia (UTC), registos a partir do dia em que está, agora, além de retenção de política são eliminadas. Por exemplo, se tivesse uma política de retenção de um dia, no início do dia hoje os registos de ontem de before dia serão eliminados. O processo de eliminação começa a meia-noite UTC, mas tenha em atenção que pode demorar até 24 horas para os registos para ser eliminado da sua conta de armazenamento.


> [!IMPORTANT]
> Você pode receber um erro ao criar um perfil de log se o provedor de recursos Microsoft. insights não estiver registrado. Consulte [provedores de recursos do Azure e tipos](../../azure-resource-manager/resource-manager-supported-services.md) para registrar este provedor.


### <a name="create-log-profile-using-the-azure-portal"></a>Criar perfil de log usando o portal do Azure

Crie ou edite um perfil de log com a opção **exportar para o Hub de eventos** no portal do Azure.

1. No menu **monitorar** na portal do Azure, selecione **exportar para o Hub de eventos**.

    ![Botão Exportar no portal](media/activity-log-export/portal-export.png)

3. Na folha que aparece, especifique o seguinte:
   * Regiões com os eventos a serem exportados. Você deve selecionar todas as regiões para garantir que não perca eventos de chave, pois o log de atividades é um log global (não regional) e, portanto, a maioria dos eventos não tem uma região associada a eles. 
   * Se você quiser gravar na conta de armazenamento:
       * A conta de armazenamento na qual você deseja salvar eventos.
       * O número de dias que você deseja manter esses eventos no armazenamento. Uma configuração de 0 dias mantém os logs para sempre.
   * Se você quiser gravar no Hub de eventos:
       * O namespace do barramento de serviço no qual você deseja que um hub de eventos seja criado para transmitir esses eventos.

     ![Folha exportar log de atividades](./media/activity-logs-overview/activity-logs-portal-export-blade.png)


4. Clique em **salvar** para salvar essas configurações. As definições são imediatamente aplicadas à sua subscrição.


### <a name="configure-log-profile-using-powershell"></a>Configurar o perfil de log usando o PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Se um perfil de log já existir, primeiro você precisará remover o perfil de log existente e, em seguida, criar um novo.

1. Use `Get-AzLogProfile` para identificar se um perfil de log existe.  Se houver um perfil de log, observe a propriedade *Name* .

1. Use `Remove-AzLogProfile` para remover o perfil de log usando o valor da propriedade *Name* .

    ```powershell
    # For example, if the log profile name is 'default'
    Remove-AzLogProfile -Name "default"
    ```

3. Use `Add-AzLogProfile` para criar um novo perfil de log:

    ```powershell
    Add-AzLogProfile -Name my_log_profile -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Location global,westus,eastus -RetentionInDays 90 -Category Write,Delete,Action
    ```

    | Propriedade | Obrigatório | Descrição |
    | --- | --- | --- |
    | Nome |Sim |Nome do seu perfil de log. |
    | StorageAccountId |Não |ID de recurso da conta de armazenamento em que o log de atividades deve ser salvo. |
    | serviceBusRuleId |Não |ID da regra do barramento de serviço para o namespace do barramento de serviço no qual você gostaria que os hubs de eventos fossem criados. Esta é uma cadeia de caracteres com o formato: `{service bus resource ID}/authorizationrules/{key name}`. |
    | Localização |Sim |Lista separada por vírgulas de regiões para as quais você gostaria de coletar eventos do log de atividades. |
    | RetentionInDays |Sim |Número de dias pelos quais os eventos devem ser retidos na conta de armazenamento, entre 1 e 365. Um valor de zero armazena os logs indefinidamente. |
    | Categoria |Não |Lista separada por vírgulas de categorias de eventos que devem ser coletadas. Os valores possíveis são _gravação_, _exclusão_e _ação_. |

### <a name="example-script"></a>Script de exemplo
Veja a seguir um exemplo de script do PowerShell para criar um perfil de log que grava o log de atividades em uma conta de armazenamento e no Hub de eventos.

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

   Add-AzLogProfile -Name $logProfileName -Location $locations -ServiceBusRuleId $serviceBusRuleId
   ```


### <a name="configure-log-profile-using-azure-cli"></a>Configurar o perfil de log usando CLI do Azure

Se um perfil de log já existir, primeiro você precisará remover o perfil de log existente e, em seguida, criar um novo perfil de log.

1. Use `az monitor log-profiles list` para identificar se um perfil de log existe.
2. Use `az monitor log-profiles delete --name "<log profile name>` para remover o perfil de log usando o valor da propriedade *Name* .
3. Use `az monitor log-profiles create` para criar um novo perfil de log:

   ```azurecli-interactive
   az monitor log-profiles create --name "default" --location null --locations "global" "eastus" "westus" --categories "Delete" "Write" "Action"  --enabled false --days 0 --service-bus-rule-id "/subscriptions/<YOUR SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventHub/namespaces/<EVENT HUB NAME SPACE>/authorizationrules/RootManageSharedAccessKey"
   ```

    | Propriedade | Obrigatório | Descrição |
    | --- | --- | --- |
    | nome |Sim |Nome do seu perfil de log. |
    | storage-account-id |Sim |ID de recurso da conta de armazenamento na qual os logs de atividade devem ser salvos. |
    | locations |Sim |Lista separada por espaços de regiões para as quais você gostaria de coletar eventos do log de atividades. Você pode exibir uma lista de todas as regiões para sua assinatura usando `az account list-locations --query [].name`. |
    | days |Sim |Número de dias pelos quais os eventos devem ser retidos, entre 1 e 365. Um valor de zero armazenará os logs indefinidamente (para sempre).  Se for zero, o parâmetro Enabled deverá ser definido como false. |
    |enabled | Sim |VERDADEIRO ou FALSO.  Usado para habilitar ou desabilitar a política de retenção.  Se for true, o parâmetro Days deverá ser um valor maior que 0.
    | categories |Sim |Lista separada por espaços de categorias de eventos que devem ser coletadas. Os valores possíveis são gravação, exclusão e ação. |



## <a name="next-steps"></a>Passos seguintes

* [Saiba mais sobre o log de atividades](../../azure-resource-manager/resource-group-audit.md)
* [Coletar log de atividades em logs de Azure Monitor](activity-log-collect.md)
