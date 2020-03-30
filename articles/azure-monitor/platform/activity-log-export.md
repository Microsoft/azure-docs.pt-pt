---
title: Exportar o Registo de Atividades Do Azure
description: Export e Azure Activity log to storage for arquivamento ou Azure Event Hubs para exportação fora de Azure.
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: edaa585ffb3448a80b021aa924a9d654ac829931
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79096290"
---
# <a name="export-azure-activity-log-to-storage-or-azure-event-hubs"></a>Export Azure Activity log to storage or Azure Event Hubs

> [!IMPORTANT]
> O método para enviar o registo da Atividade Azure para o Azure Storage e os Centros de Eventos Azure mudou para [configurações](diagnostic-settings.md)de diagnóstico . Este artigo descreve o método legado que está em processo de ser depreciado. Consulte a recolha e exportação de registos da [Atividade Azure](diagnostic-settings-legacy.md) para uma comparação.


O [Registo de Atividades do Azure](platform-logs-overview.md) fornece informações sobre eventos de nível de subscrição que ocorreram na sua subscrição Azure. Além de visualizar o registo de atividade no portal Azure ou copiá-lo para um espaço de trabalho de Log Analytics onde pode ser analisado com outros dados recolhidos pelo Azure Monitor, pode criar um perfil de registo para arquivar o registo de atividade sacana para uma conta de armazenamento Azure ou transmiti-lo para um Centro de Eventos.

## <a name="archive-activity-log"></a>Registo de Atividades de Arquivo
Arquivar o Registo de Atividade saqueado numa conta de armazenamento é útil se quiser reter os seus dados de registo por mais de 90 dias (com controlo total sobre a política de retenção) para auditoria, análise estática ou backup. Se necessitar apenas de reter os seus eventos durante 90 dias ou menos, não precisa de configurar o arquivo para uma conta de armazenamento, uma vez que os eventos de Registo de Atividade saem retidos na plataforma Azure durante 90 dias.

## <a name="stream-activity-log-to-event-hub"></a>Registo de atividade de fluxo para centro de eventos
[O Azure Event Hubs](/azure/event-hubs/) é uma plataforma de streaming de dados e um serviço de ingestão de eventos que pode receber e processar milhões de eventos por segundo. Os dados enviados para um hub de eventos podem ser transformados e armazenados em qualquer fornecedor de análise em tempo real ou adaptadores de armazenamento/criação de batches. Duas formas de utilizar a capacidade de streaming para o Registo de Atividades são:
* **Stream para sistemas de registo e telemetria de terceiros**: Com o tempo, o streaming de Hubs de Eventos Azure tornar-se-á o mecanismo para canalizar o seu Registo de Atividade em SIEMs de terceiros e soluções de análise de registo.
* **Construa uma plataforma personalizada de telemetria e exploração madeireira**: Se já tem uma plataforma de telemetria personalizada ou está a pensar em construir uma, a natureza altamente escalável de subscrição de registos de eventos permite-lhe ingerir de forma flexível o registo de atividade.

## <a name="prerequisites"></a>Pré-requisitos

### <a name="storage-account"></a>Conta de armazenamento
Se estiver a arquivar o seu Registo de Atividades, precisa [de criar uma conta](../../storage/common/storage-account-create.md) de armazenamento se ainda não tiver uma. Não deve utilizar uma conta de armazenamento existente que tenha outros dados não monitorizadores armazenados na si para que possa controlar melhor o acesso aos dados de monitorização. Se também estiver a arquivar registos e métricas para uma conta de armazenamento, pode optar por utilizar essa mesma conta de armazenamento para manter todos os dados de monitorização num local central.

A conta de armazenamento não tem de estar na mesma subscrição que os registos emissores de subscrição, desde que o utilizador que configura a definição tenha acesso rBAC adequado a ambas as subscrições. 

> [!TIP]
> Consulte [as firewalls de Armazenamento Configure Azure e redes virtuais](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions) para fornecer acesso a uma conta de armazenamento por trás de uma rede virtual segura.

### <a name="event-hubs"></a>Event Hubs
Se está a enviar o seu Registo de Atividades para um centro de eventos, então precisa de criar um centro de [eventos](../../event-hubs/event-hubs-create.md) se ainda não tiver um. Se já transmitiu eventos de Registo de Atividade para este espaço de nome sinuoso do Event Hubs, então esse centro de eventos será reutilizado.

A política de acesso partilhado define as permissões que o mecanismo de streaming tem. O streaming para centros de eventos requer permissões de Gestão, Envio e Escuta. Pode criar ou modificar políticas de acesso partilhado para o espaço de nome sem nome do Event Hubs no portal Azure sob o separador Configure para o seu espaço de nome Sem Evento.

Para atualizar o perfil de registo de registo de atividade para incluir o streaming, deve ter a permissão ListKey nessa regra de autorização do Event Hubs. O espaço de nome sem nome do Event Hubs não tem de estar na mesma subscrição que a subscrição que está a emitir registos, desde que o utilizador que configura a definição tenha acesso rBAC adequado a ambas as subscrições e ambas as subscrições estejam no mesmo inquilino AAD.

Transmita o Registo de Atividades para um Hub de [Eventos criando um Perfil](#create-a-log-profile)de Registo .

## <a name="create-a-log-profile"></a>Criar um perfil de registo
Define como o seu registo de Atividade Azure é exportado através de um perfil de **registo**. Cada subscrição do Azure só pode ter um perfil de registo. Estas definições podem ser configuradas através da opção **Export** na lâmina de registo de atividade no portal. Também podem ser configurados programáticamente [utilizando a API,](https://msdn.microsoft.com/library/azure/dn931927.aspx)PowerShell cmdlets ou CLI do Monitor Azure.

O perfil de registo define o seguinte.

**Para onde deve ser enviado o Registo de Atividades.** Atualmente, as opções disponíveis são Conta de Armazenamento ou Centros de Eventos.

**Que categorias de eventos devem ser enviadas.** O significado da *categoria* em perfis de registo e eventos de Registo de Atividadeé diferente. No perfil de registo, *a categoria* representa o tipo de funcionamento (Escrever, Excluir, Ação). Num evento de Registo de Atividades, a *categoria*"* propriedade representa a fonte ou tipo de evento (por exemplo, Administração, ServiceHealth e Alerta).

**Que regiões (locais) devem ser exportadas.** Você deve incluir todos os locais, uma vez que muitos eventos no Registo de Atividades são eventos globais.

**Quanto tempo o Registo de Atividade deve ser conservado numa Conta de Armazenamento.** Uma retenção de zero dias significa que os registos são mantidos para sempre. Caso contrário, o valor pode ser qualquer número de dias entre 1 e 365.

Se as políticas de retenção forem definidas, mas armazenar registos numa conta de armazenamento é desativada, então as políticas de retenção não têm qualquer efeito. As políticas de retenção são aplicadas por dia, pelo que, ao fim de um dia (UTC), os registos do dia que está agora para além da política de retenção são suprimidos. Por exemplo, se tivesse uma política de retenção de um dia, ao início do dia de hoje, os registos do dia anterior a ontem seriam apagados. O processo de eliminação começa à meia-noite UTC, mas note que pode demorar até 24 horas para que os registos sejam eliminados da sua conta de armazenamento.


> [!IMPORTANT]
> Pode receber um erro ao criar um perfil de registo se o fornecedor de recursos Microsoft.Insights não estiver registado. Consulte [os fornecedores e tipos](../../azure-resource-manager/management/resource-providers-and-types.md) de recursos azure para registar este fornecedor.


### <a name="create-log-profile-using-the-azure-portal"></a>Criar perfil de registo utilizando o portal Azure

Crie ou edite um perfil de registo com a opção **Export to Event Hub** no portal Azure.

1. A partir do menu **Azure Monitor** no portal Azure, selecione **registo de atividade**.
3. Clique em **Definições de diagnóstico**.

   ![Definições de diagnóstico](media/diagnostic-settings-subscription/diagnostic-settings.png)

4. Clique no banner roxo para a experiência do legado.

    ![Experiência legado](media/diagnostic-settings-subscription/legacy-experience.png)

3. Na lâmina que aparece, especifique o seguinte:
   * Regiões com eventos para exportar. Você deve selecionar todas as regiões para garantir que você não perca eventos chave, uma vez que o Registo de Atividades é um registo global (não regional) e, por isso, a maioria dos eventos não tem uma região associada a eles.
   * Se quiser escrever na conta de armazenamento:
       * A Conta de Armazenamento à qual gostaria de guardar eventos.
       * O número de dias que pretende manter estes eventos armazenados. Um ajuste de 0 dias mantém os troncos para sempre.
   * Se quiser escrever para o centro de eventos:
       * O Service Bus Namespace no qual você gostaria que um Hub de eventos fosse criado para transmitir estes eventos.

     ![Lâmina de registo de atividade de exportação](./media/activity-logs-overview/activity-logs-portal-export-blade.png)


4. Clique em **Guardar** para guardar estas definições. As definições são imediatamente aplicadas à sua subscrição.


### <a name="configure-log-profile-using-powershell"></a>Configure o perfil de registo usando powerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Se já existir um perfil de registo, é necessário remover primeiro o perfil de registo existente e, em seguida, criar um novo.

1. Utilize `Get-AzLogProfile` para identificar se existe um perfil de registo.  Se existir um perfil de registo, note a propriedade do *nome.*

1. Utilize `Remove-AzLogProfile` para remover o perfil de registo utilizando o valor da propriedade *do nome.*

    ```powershell
    # For example, if the log profile name is 'default'
    Remove-AzLogProfile -Name "default"
    ```

3. Utilizar `Add-AzLogProfile` para criar um novo perfil de registo:

    ```powershell
    Add-AzLogProfile -Name my_log_profile -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Location global,westus,eastus -RetentionInDays 90 -Category Write,Delete,Action
    ```

    | Propriedade | Necessário | Descrição |
    | --- | --- | --- |
    | Nome |Sim |Nome do seu perfil de registo. |
    | ArmazenamentoAccountid |Não |Identificação de recursos da Conta de Armazenamento onde o Registo de Atividade deve ser guardado. |
    | serviçoBusRuleId |Não |Service Bus Rule ID para o espaço de nome do ônibus de serviço em que você gostaria de ter centros de eventos criados. Esta é uma cadeia `{service bus resource ID}/authorizationrules/{key name}`com o formato: . |
    | Localização |Sim |Lista separada da vírposta de regiões para as quais gostaria de recolher eventos de Registo de Atividades. |
    | Dias de Retenção |Sim |Número de dias para os quais os eventos devem ser mantidos na conta de armazenamento, entre 1 e 365. Um valor de zero armazena os registos indefinidamente. |
    | Categoria |Não |Lista separada da vírposta das categorias de eventos que devem ser recolhidas. Os valores possíveis são _Escrever,_ _Apagar_e _Ação._ |

### <a name="example-script"></a>Script de exemplo
Segue-se um script powerShell de amostra para criar um perfil de registo que escreve o Registo de Atividade sacar tanto para uma conta de armazenamento como para um centro de eventos.

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


### <a name="configure-log-profile-using-azure-cli"></a>Configure o perfil de registo utilizando o Azure CLI

Se já existir um perfil de registo, é necessário remover primeiro o perfil de registo existente e, em seguida, criar um novo perfil de registo.

1. Utilize `az monitor log-profiles list` para identificar se existe um perfil de registo.
2. Utilize `az monitor log-profiles delete --name "<log profile name>` para remover o perfil de registo utilizando o valor da propriedade *do nome.*
3. Utilizar `az monitor log-profiles create` para criar um novo perfil de registo:

   ```azurecli-interactive
   az monitor log-profiles create --name "default" --location null --locations "global" "eastus" "westus" --categories "Delete" "Write" "Action"  --enabled false --days 0 --service-bus-rule-id "/subscriptions/<YOUR SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventHub/namespaces/<EVENT HUB NAME SPACE>/authorizationrules/RootManageSharedAccessKey"
   ```

    | Propriedade | Necessário | Descrição |
    | --- | --- | --- |
    | nome |Sim |Nome do seu perfil de registo. |
    | armazenamento-conta-id |Sim |Identificação de recursos da Conta de Armazenamento para a qual os Registos de Atividade devem ser guardados. |
    | locais |Sim |Lista de regiões separadas pelo espaço para as quais gostaria de recolher eventos de Registo de Atividades. Pode ver uma lista de todas as `az account list-locations --query [].name`regiões para a sua subscrição utilizando . |
    | dias |Sim |Número de dias para os quais os eventos devem ser mantidos, entre 1 e 365. Um valor de zero armazenará os registos indefinidamente (para sempre).  Se zero, então o parâmetro ativado deve ser definido como falso. |
    |ativado | Sim |Verdadeiro ou Falso.  Usado para ativar ou desativar a política de retenção.  Se for verdade, então o parâmetro dos dias deve ser um valor superior a 0.
    | categories |Sim |Lista separada do espaço das categorias de eventos que devem ser recolhidas. Os valores possíveis são Escrever, Apagar e Agir. |



## <a name="next-steps"></a>Passos seguintes

* [Saiba mais sobre o Registo de Atividades](../../azure-resource-manager/management/view-activity-logs.md)
* [Recolher Registo de Atividades em Registos de Monitor Estoque Azure](activity-log-collect.md)
