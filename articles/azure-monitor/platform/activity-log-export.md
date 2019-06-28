---
title: Exportar o registo de atividades do Azure
description: Exporte registo de atividades do Azure para o armazenamento para os Hubs de eventos de arquivamento ou do Azure para exportar fora do Azure.
author: bwren
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: acf2526e79519e610614dc5217efbfe5e327b90f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66248148"
---
# <a name="export-azure-activity-log-to-storage-or-azure-event-hubs"></a>Exportar o registo de atividades do Azure para armazenamento ou Hubs de eventos do Azure
O [registo de atividades do Azure](activity-logs-overview.md) fornece informações sobre os eventos de nível de assinatura que ocorreram na sua subscrição do Azure. Além de visualizar o registo de atividades no portal do Azure ou copiá-la para uma área de trabalho do Log Analytics onde podem ser analisados com outros dados recolhidos pelo Monitor do Azure, pode criar um perfil de registo para arquivar o registo de atividades para uma conta de armazenamento do Azure ou transmiti-lo para um  Hub de eventos.

## <a name="archive-activity-log"></a>Arquivar registo de atividades
Arquivar o registo de atividades para uma conta de armazenamento é útil se gostaria de manter os seus dados de registo mais de 90 dias (com controlo total sobre a política de retenção) para cópia de segurança, auditoria ou análise estática. Se só precisa de manter seus eventos durante 90 dias ou menos não é necessário configurar o arquivo para uma conta de armazenamento, uma vez que os eventos de registo de Atividades são mantidos na plataforma do Azure durante 90 dias.

## <a name="stream-activity-log-to-event-hub"></a>Registo de atividades do Stream para o Hub de eventos
[Os Hubs de eventos do Azure](/azure/event-hubs/) é uma plataforma e o evento de serviço de ingestão que pode receber transmissão em fluxo de dados e processar milhões de eventos por segundo. Os dados enviados para um hub de eventos podem ser transformados e armazenados em qualquer fornecedor de análise em tempo real ou adaptadores de armazenamento/criação de batches. Pode utilizar a capacidade de transmissão em fluxo para o registo de atividades de duas formas são:
* **Stream aos sistemas de Registro em log e telemetria de terceiros**: Ao longo do tempo, Hubs de eventos de transmissão em fluxo se tornará o mecanismo para o registo de atividades de pipes no SIEMs de terceiros e soluções de análise de registo.
* **Criar uma plataforma de Registro em log e telemetria personalizada**: Se já tiver uma plataforma de telemetria personalizados internos ou estão pensando em criar uma, altamente dimensionável de publicação-subscrição natureza dos Hubs de eventos permite-lhe flexibilidade ingerir o registo de atividades. 

## <a name="prerequisites"></a>Pré-requisitos

### <a name="storage-account"></a>Conta de armazenamento
Se estiver a arquivar o registo de atividades, precisará [criar uma conta de armazenamento](../../storage/common/storage-quickstart-create-account.md) se ainda não tiver uma. Não deve utilizar uma conta de armazenamento existente que tenha outros, não monitorizar dados armazenados na mesma, para que pode controlar melhor acesso a dados de monitorização. Se também são arquivar os registos de diagnóstico e métricas para uma conta de armazenamento no entanto, pode optar por utilizar essa mesma conta de armazenamento para manter todos os dados de monitorização numa localização central.

A conta de armazenamento não tem de estar na mesma subscrição que a subscrição que emite os registos, desde que o utilizador que configura a definição possui acesso RBAC adequado para ambas as subscrições.
> [!NOTE]
>  Atualmente não é possível arquivar dados para uma conta de armazenamento que está atrás de uma rede virtual protegida.

### <a name="event-hubs"></a>Hubs de Eventos
Se está a enviar o registo de atividades para um hub de eventos, então precisa [criar um hub de eventos](../../event-hubs/event-hubs-create.md) se ainda não tiver uma. Se anteriormente transmitidos em fluxo eventos de registo de atividades para este espaço de nomes de Hubs de eventos, em seguida, esse hub de eventos será reutilizado.

A política de acesso partilhado define as permissões que tem o mecanismo de transmissão em fluxo. Transmissão em fluxo aos Hubs de eventos exige permissões de gerir, envio e escutar. Pode criar ou modificar as políticas de acesso partilhado para o espaço de nomes de Hubs de eventos no portal do Azure, no separador Configurar para o espaço de nomes de Hubs de eventos.

Para atualizar o perfil de registo do registo de atividades para incluir a transmissão em fluxo, tem de ter a permissão de ListKey essa regra de autorização dos Hubs de eventos. O espaço de nomes de Hubs de eventos não tem de estar na mesma subscrição que a subscrição que é emite os registos, desde que o utilizador que configura a definição tenha RBAC adequada de acesso para ambas as subscrições e ambas as subscrições estão no mesmo inquilino do AAD.

Stream o registo de atividades para um Hub de eventos por [criar um perfil de registo](#create-a-log-profile).

## <a name="create-a-log-profile"></a>Criar um perfil de registo
Definir a forma como o registo de atividades do Azure é exportado usando um **perfil de registo**. Cada subscrição do Azure só pode ter um perfil de registo. Estas definições podem ser configuradas através da **exportar** opção no painel do registo de atividades no portal. Eles também podem ser configurados por meio de programação [utilizando a API de REST do Azure Monitor](https://msdn.microsoft.com/library/azure/dn931927.aspx), cmdlets do PowerShell, ou a CLI.

O perfil de registo define o seguinte.

**Em que o registo de atividades devem ser enviado.** Atualmente, as opções disponíveis são a conta de armazenamento ou Hubs de eventos.

**As categorias de eventos devem ser enviadas.** O significado dos *categoria* no registo de atividades e de perfis do registo de eventos é diferente. No perfil de registo, *categoria* representa o tipo de operação (ação de escrita, eliminação,). Num evento de registo de atividades, o *categoria*"* propriedade representa a origem ou o tipo de evento (por exemplo, administração, ServiceHealth e alerta).

**Que regiões (localizações) devem ser exportados.** Uma vez que muitos eventos no registo de Atividades são eventos globais, deve incluir todas as localizações.

**O tempo que o registo de atividades deve ser mantido numa conta de armazenamento.** A retenção de zero dias significa que os registos são mantidos para sempre. Caso contrário, o valor pode ser qualquer número de dias entre 1 e 2147483647.

Se as políticas de retenção são definidas, mas armazenamento de registos numa conta de armazenamento está desativada, as políticas de retenção não terá qualquer efeito. Políticas de retenção são aplicado por dia, portanto, no final do dia (UTC), registos a partir do dia em que está, agora, além de retenção de política são eliminadas. Por exemplo, se tivesse uma política de retenção de um dia, no início do dia hoje os registos de ontem de before dia serão eliminados. O processo de eliminação começa a meia-noite UTC, mas tenha em atenção que pode demorar até 24 horas para os registos para ser eliminado da sua conta de armazenamento.



> [!WARNING]
> O formato dos dados de registo na conta de armazenamento foi alterado para linhas de JSON no dia 1 de Novembro de 2018. [Leia este artigo para obter uma descrição do impacto e saber como atualizar a sua ferramenta para trabalhar com o novo formato.](diagnostic-logs-append-blobs.md)
>
>

### <a name="create-log-profile-using-the-azure-portal"></a>Criar perfil de registo no portal do Azure

Criar ou editar um perfil de registo com o **exportar para o Hub de eventos** opção no portal do Azure.

1. Partir do **Monitor** menu no portal do Azure, selecione **exportar para o Hub de eventos**.

    ![Botão de exportação no portal](media/activity-log-export/portal-export.png)

3. No painel que aparece, especifique o seguinte:
   * Regiões com os eventos para exportar. Deve selecionar todas as regiões para assegurar que não perder eventos importantes, uma vez que o registo de atividades é um log global de (, independente de região) e para que a maioria dos eventos não têm uma região associada a eles. 
   * Se quiser escrever na conta de armazenamento:
       * A conta de armazenamento para o qual pretende guardar os eventos.
       * o número de dias que pretenda manter esses eventos em armazenamento. Uma definição de 0 dias retém os registos para sempre.
   * Se quiser escrever para o hub de eventos:
       * o Namespace de barramento de serviço no qual gostaria de ter um Hub de eventos a ser criado para esses eventos de transmissão em fluxo.

     ![Painel de registo de atividades de exportação](./media/activity-logs-overview/activity-logs-portal-export-blade.png)


4. Clique em **guardar** para salvar essas configurações. As definições são imediatamente aplicadas à sua subscrição.


### <a name="configure-log-profile-using-powershell"></a>Configurar o perfil de registo com o PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Se já existir um perfil de registo, tem primeiro de remover o perfil de registo existentes e, em seguida, crie um novo.

1. Utilize `Get-AzLogProfile` para identificar se existe um perfil de registo.  Se existir um perfil de registo, tenha em atenção a *nome* propriedade.

1. Uso `Remove-AzLogProfile` para remover o perfil de registo com o valor do *nome* propriedade.

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
    | Name |Sim |Nome do seu perfil de registo. |
    | StorageAccountId |Não |ID de recurso da conta do Storage, onde deverá ser guardado o registo de atividades. |
    | serviceBusRuleId |Não |ID de regra de barramento de serviço para o espaço de nomes do Service Bus que deseja que os hubs de eventos criados no. Esta é uma cadeia com o formato: `{service bus resource ID}/authorizationrules/{key name}`. |
    | Location |Sim |Lista separada por vírgulas de regiões para o qual pretende recolher eventos de registo de atividades. |
    | RetentionInDays |Sim |Número de dias para o qual os eventos devem ser mantidos na conta de armazenamento, entre 1 e 2147483647. Um valor de zero armazena os logs de indefinidamente. |
    | Category |Não |Lista separada por vírgulas das categorias de evento que deve ser recolhidos. Os valores possíveis são _escrever_, _eliminar_, e _ação_. |

### <a name="example-script"></a>Script de exemplo
Segue-se um script do PowerShell de exemplo para criar um perfil de registo que escreve o registo de atividades para ambos os um armazenamento conta e o evento de hub.

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


### <a name="configure-log-profile-using-azure-cli"></a>Configurar o perfil de registo com a CLI do Azure

Se já existir um perfil de registo, tem primeiro de remover o perfil de registo existentes e, em seguida, crie um novo perfil de registo.

1. Utilize `az monitor log-profiles list` para identificar se existe um perfil de registo.
2. Uso `az monitor log-profiles delete --name "<log profile name>` para remover o perfil de registo com o valor do *nome* propriedade.
3. Utilize `az monitor log-profiles create` para criar um novo perfil de registo:

   ```azurecli-interactive
   az monitor log-profiles create --name "default" --location null --locations "global" "eastus" "westus" --categories "Delete" "Write" "Action"  --enabled false --days 0 --service-bus-rule-id "/subscriptions/<YOUR SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventHub/namespaces/<EVENT HUB NAME SPACE>/authorizationrules/RootManageSharedAccessKey"
   ```

    | Propriedade | Necessário | Descrição |
    | --- | --- | --- |
    | name |Sim |Nome do seu perfil de registo. |
    | storage-account-id |Sim |ID de recurso da conta do Storage para o qual os registos de atividades devem ser salvos. |
    | locations |Sim |Lista de regiões para o qual pretende recolher eventos de registo de atividades separadas por espaços. Pode ver uma lista de todas as regiões para a sua subscrição com `az account list-locations --query [].name`. |
    | days |Sim |Número de dias para que eventos devem ser mantidos, entre 1 e 365. Um valor igual a zero irá armazenar os registos indefinidamente (Eterno).  Se zero, em seguida, o parâmetro ativado deve ser definido como true. |
    |enabled | Sim |VERDADEIRO ou FALSO.  Utilizado para ativar ou desativar a política de retenção.  Se for VERDADEIRO, o parâmetro de dias tem de ser um valor maior que 0.
    | categories |Sim |Lista de categorias de evento que deve ser coletado separadas por espaços. Valores possíveis são escrita, eliminação e ação. |



## <a name="activity-log-schema"></a>Esquema de registo de atividade
Se enviado para o armazenamento do Azure ou o Hub de eventos, os dados de registo de atividade serão escritos para JSON com o seguinte formato.

``` JSON
{
    "records": [
        {
            "time": "2015-01-21T22:14:26.9792776Z",
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
                    "http://schemas.microsoft.com/identity/claims/tenantid": "1e8d8218-c5e7-4578-9acc-9abbd5d23315 ",
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
Os elementos neste JSON são descritos na tabela seguinte.

| Nome do elemento | Descrição |
| --- | --- |
| time |Timestamp quando o evento foi gerado pelo processamento do pedido correspondente o evento de serviço do Azure. |
| resourceId |ID de recurso do recurso afetado. |
| operationName |Nome da operação. |
| category |Categoria da ação, por exemplo. Escrita, leitura, a ação. |
| resultType |O tipo de resultado, por exemplo. Início de sucesso, falha, |
| resultSignature |Depende do tipo de recurso. |
| durationMs |Duração da operação em milissegundos |
| callerIpAddress |Endereço IP do utilizador que efetuou a operação, a afirmação UPN ou a afirmação SPN com base na disponibilidade. |
| correlationId |Normalmente, um GUID no formato de cadeia de caracteres. Eventos que partilham uma correlationId pertencem à mesma ação de uber. |
| identity |Blob JSON que descrevem a autorização e afirmações. |
| authorization |Blob de propriedades RBAC do evento. Normalmente, inclui as propriedades de "action", "função" e "escopo". |
| level |Nível do evento. Um dos seguintes valores: _Crítico_, _erro_, _aviso_, _informativa_, e _verboso_ |
| location |Região em que ocorreu a localização (ou global). |
| properties |Conjunto de `<Key, Value>` pares (ou seja, dicionário), que descreve os detalhes do evento. |

> [!NOTE]
> As propriedades e o uso dessas propriedades podem variar dependendo do recurso.



## <a name="next-steps"></a>Passos Seguintes

* [Saiba mais sobre o registo de atividades](../../azure-resource-manager/resource-group-audit.md)
* [Recolher o registo de atividades para registos do Azure Monitor](activity-log-collect.md)
