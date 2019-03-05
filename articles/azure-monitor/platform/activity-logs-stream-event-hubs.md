---
title: O registo de atividades do Azure para os Hubs de eventos do Stream
description: Aprenda a transmitir o registo de atividade do Azure para os Hubs de eventos.
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 07/25/2018
ms.author: johnkem
ms.subservice: logs
ms.openlocfilehash: 3d4c1029315e754410f31b13042d1d6acb105da1
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2019
ms.locfileid: "57309694"
---
# <a name="stream-the-azure-activity-log-to-event-hubs"></a>O registo de atividades do Azure para os Hubs de eventos do Stream
Pode transmitir em fluxo a [registo de atividades do Azure](../../azure-monitor/platform/activity-logs-overview.md) em tempo real para qualquer aplicativo, por qualquer um:

* Usando o incorporado **exportar** opção no portal
* Ativar o ID de regra de Azure Service Bus num perfil de registo através de cmdlets do Azure PowerShell ou CLI do Azure

## <a name="what-you-can-do-with-the-activity-log-and-event-hubs"></a>O que pode fazer com o registo de atividades e os Hubs de eventos
Seguem-se pode utilizar a capacidade de transmissão em fluxo para o registo de atividades de duas formas:

* **Stream aos sistemas de Registro em log e telemetria de terceiros**: Ao longo do tempo, Hubs de eventos de transmissão em fluxo se tornará o mecanismo para o registo de atividades de pipes no SIEMs de terceiros e soluções de análise de registo.
* **Criar uma plataforma de Registro em log e telemetria personalizada**: Se já tiver uma plataforma de telemetria personalizados internos ou estão pensando em criar uma, altamente dimensionável de publicação-subscrição natureza dos Hubs de eventos permite-lhe flexibilidade ingerir o registo de atividades. Para obter mais informações, consulte [vídeo de Dan Rosanova sobre como utilizar os Hubs de eventos numa plataforma de telemetria de escala global](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/).

## <a name="enable-streaming-of-the-activity-log"></a>Ativar a transmissão em fluxo do registo de atividades
Pode ativar a transmissão em fluxo do registo de atividades por meio de programação ou através do portal. De qualquer forma, escolha um espaço de nomes de Hubs de eventos e uma política de acesso partilhado para esse espaço de nomes. Um hub de eventos com o nome insights-logs-operationallogs é criado nesse espaço de nomes quando ocorre o primeiro evento novo do registo de atividades. 

Se não tiver um espaço de nomes de Hubs de eventos, tem primeiro de criar uma. Se anteriormente transmitidos em fluxo eventos de registo de atividades para este espaço de nomes de Hubs de eventos, o hub de eventos que foi criado anteriormente será reutilizado. 

A política de acesso partilhado define as permissões que tem o mecanismo de transmissão em fluxo. Hoje em dia, transmissão em fluxo aos Hubs de eventos exige **Manage**, **enviar**, e **escutar** permissões. Pode criar ou modificar as políticas de acesso partilhado para o espaço de nomes de Hubs de eventos no portal do Azure sob o **configurar** separador para o espaço de nomes de Hubs de eventos. 

Para atualizar o perfil de registo do registo de atividades para incluir a transmissão em fluxo, o utilizador que está a tornar a alteração tem de ter a permissão de ListKey nessa regra de autorização dos Hubs de eventos. O espaço de nomes de Hubs de eventos não tem de estar na mesma subscrição que a subscrição que é emite os registos, desde que o utilizador que configura a definição tenha RBAC adequada de acesso para ambas as subscrições e ambas as subscrições estão no mesmo inquilino do AAD.

### <a name="via-the-azure-portal"></a>Através do portal do Azure
1. Navegue para o **registo de atividades** secção utilizando o **todos os serviços** pesquisa no lado esquerdo do portal.
   
   ![Selecionar o registo de atividades na lista de serviços no portal](./media/activity-logs-stream-event-hubs/activity-logs-portal-navigate-v2.png)
2. Selecione o **exportar para o Hub de eventos** botão na parte superior do registo.
   
   ![Botão de exportação no portal](./media/activity-logs-stream-event-hubs/activity-logs-portal-export-v2.png)

   Tenha em atenção que as definições de filtro que aplicou ao visualizar o registo de atividades no vista anterior não tem nenhum impacto nas suas definições de exportação. Esses são apenas para filtragem, o que vê ao navegar pelo seu registo de atividades no portal.
3. Na secção que aparece, selecione **todas as regiões**. Não selecione certas regiões.
   
   ![Secção de exportação](./media/activity-logs-stream-event-hubs/export-audit.png)

   > [!WARNING]  
   > Se selecionar qualquer coisa que **todas as regiões**, perderá os principais eventos que pretende receber. O registo de atividades é um log global de (, independente de região), para que a maioria dos eventos não têm uma região associada a eles. 
   >

4. Clique nas **os Hubs de eventos do Azure** opção e selecione um espaço de nomes de hubs de eventos para os registos que deve ser enviado, em seguida, clique em **OK**.
5. Selecione **guardar** para salvar essas configurações. As definições são imediatamente aplicadas à sua subscrição.
6. Se tiver várias subscrições, repita esta ação e enviar todos os dados para o hub de eventos do mesmo.

### <a name="via-powershell-cmdlets"></a>Cmdlets do PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Se já existir um perfil de registo, tem primeiro de remover o perfil de registo existentes e, em seguida, crie um novo perfil de registo.

1. Utilize `Get-AzLogProfile` para identificar se existe um perfil de registo.  Se existir um perfil de registo, localize a *nome* propriedade.
2. Uso `Remove-AzLogProfile` para remover o perfil de registo com o valor do *nome* propriedade.

    ```powershell
    # For example, if the log profile name is 'default'
    Remove-AzLogProfile -Name "default"
    ```
3. Utilize `Add-AzLogProfile` para criar um novo perfil de registo:

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

   Add-AzLogProfile -Name $logProfileName -Location $locations -ServiceBusRuleId $serviceBusRuleId
   ```

### <a name="via-azure-cli"></a>Através da CLI do Azure
Se já existir um perfil de registo, tem primeiro de remover o perfil de registo existentes e, em seguida, crie um novo perfil de registo.

1. Utilize `az monitor log-profiles list` para identificar se existe um perfil de registo.
2. Uso `az monitor log-profiles delete --name "<log profile name>` para remover o perfil de registo com o valor do *nome* propriedade.
3. Utilize `az monitor log-profiles create` para criar um novo perfil de registo:

   ```azurecli-interactive
   az monitor log-profiles create --name "default" --location null --locations "global" "eastus" "westus" --categories "Delete" "Write" "Action"  --enabled false --days 0 --service-bus-rule-id "/subscriptions/<YOUR SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventHub/namespaces/<EVENT HUB NAME SPACE>/authorizationrules/RootManageSharedAccessKey"
   ```

## <a name="consume-the-log-data-from-event-hubs"></a>Consumir os dados de registo dos Hubs de eventos
O esquema para o registo de atividades está disponível no [monitorizar a atividade de subscrição com o registo de atividades do Azure](../../azure-monitor/platform/activity-logs-overview.md). Cada evento é uma matriz de blobs JSON chamado *registos*.

## <a name="next-steps"></a>Passos Seguintes
* [Arquive o registo de atividades para uma conta de armazenamento](../../azure-monitor/platform/archive-activity-log.md)
* [Leia a visão geral do registo de atividades do Azure](../../azure-monitor/platform/activity-logs-overview.md)
* [Configurar um alerta com base num evento do registo de atividade](../../azure-monitor/platform/alerts-log-webhook.md)


