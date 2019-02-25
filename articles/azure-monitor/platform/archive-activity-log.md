---
title: Arquivar o registo de atividades do Azure
description: Arquive o registo de atividade do Azure para retenção de longa duração numa conta de armazenamento.
author: nkiest
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 02/22/2019
ms.author: nikiest
ms.subservice: logs
ms.openlocfilehash: f02b17ff4e83c3300973c86f26db76ebff5a8d0a
ms.sourcegitcommit: e88188bc015525d5bead239ed562067d3fae9822
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/24/2019
ms.locfileid: "56750893"
---
# <a name="archive-the-azure-activity-log"></a>Arquivar o registo de atividades do Azure
Neste artigo, vamos mostrar como pode usar o portal do Azure, Cmdlets do PowerShell ou CLI de várias plataformas para arquivar sua [ **registo de atividades do Azure** ](../../azure-monitor/platform/activity-logs-overview.md) numa conta de armazenamento. Esta opção é útil se gostaria de manter o registo de atividades mais de 90 dias (com controlo total sobre a política de retenção) para cópia de segurança, auditoria ou análise estática. Se só precisa de manter seus eventos durante 90 dias ou menos não é necessário configurar o arquivo para uma conta de armazenamento, uma vez que os eventos de registo de Atividades são mantidos na plataforma do Azure durante 90 dias sem ativar arquivamento.

> [!WARNING]
> O formato dos dados de registo na conta de armazenamento vai ser alterado para Linhas de JSON a 1 de novembro de 2018. [Leia este artigo para obter uma descrição do impacto e saber como atualizar a sua ferramenta para trabalhar com o novo formato.](./../../azure-monitor/platform/diagnostic-logs-append-blobs.md) 
>
> 

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar, precisa [criar uma conta de armazenamento](../../storage/common/storage-quickstart-create-account.md) ao qual pode arquivar o registo de atividades. É altamente recomendável que não use uma conta de armazenamento existente que tenha outros, não monitorizar dados armazenados na mesma, para que pode controlar melhor acesso a dados de monitorização. No entanto, se também são arquivar os registos de diagnóstico e métricas para uma conta de armazenamento, talvez faça sentido usar essa conta de armazenamento para o registo de atividades também para manter todos os dados de monitorização numa localização central. A conta de armazenamento não tem de estar na mesma subscrição que a subscrição que emite os registos, desde que o utilizador que configura a definição possui acesso RBAC adequado para ambas as subscrições.

## <a name="log-profile"></a>Perfil de registo
Para arquivar o registo de atividade usando qualquer um dos métodos abaixo, defina o **perfil de registo** para uma subscrição. O perfil de registo define o tipo de eventos que estão armazenados ou transmissão em fluxo e as saídas — hub de conta e/ou eventos de armazenamento. Também define a política de retenção (número de dias a manter) para os eventos armazenados numa conta de armazenamento. Se a política de retenção é definida como zero, os eventos são armazenados indefinidamente. Caso contrário, isso pode ser definido como qualquer valor entre 1 e 365. Políticas de retenção são aplicado por dia, portanto, no final do dia (UTC), registos a partir do dia em que está, agora, além do período de retenção política será eliminada. Por exemplo, se tivesse uma política de retenção de um dia, no início do dia hoje os registos de ontem de before dia serão eliminados. O processo de eliminação começa a meia-noite UTC, mas tenha em atenção que pode demorar até 24 horas para os registos para ser eliminado da sua conta de armazenamento. [Pode ler mais sobre o registo de perfis aqui](../../azure-monitor/platform/activity-logs-overview.md#export-the-activity-log-with-a-log-profile). 

## <a name="archive-the-activity-log-using-the-portal"></a>Arquive o registo de atividades com o portal
1. No portal, clique a **registo de atividades** ligação no painel de navegação esquerda. Se não vir uma ligação para o registo de atividades, clique nas **todos os serviços** ligar pela primeira vez.
   
    ![Navegue para o painel de registo de atividades](media/archive-activity-log/activity-logs-portal-navigate-v2.png)
2. Na parte superior do painel, clique em **exportar para o Hub de eventos**.
   
    ![Clique no botão de exportação](media/archive-activity-log/activity-logs-portal-export-v2.png)
3. No painel que aparece, marque a caixa **exportar para uma conta de armazenamento** e selecione uma conta de armazenamento.
   
    ![Definir uma conta de armazenamento](media/archive-activity-log/act-log-portal-export-blade.png)
4. Utilizar o controlo de deslize ou a caixa de texto, defina um número de dias (0 a 365) para o qual os eventos de registo de atividades devem ser mantidos na sua conta de armazenamento. Se preferir ter os dados mantidos na conta de armazenamento indefinidamente, defina este número para zero. Se precisar de introduzir o número de dias mais do que 365, use os métodos, PowerShell ou a CLI, descritos abaixo.
5. Clique em **Guardar**.

## <a name="archive-the-activity-log-via-powershell"></a>Arquivar o registo de atividade através do PowerShell

   ```powershell
   # Settings needed for the new log profile
   $logProfileName = "default"
   $locations = (Get-AzureRmLocation).Location
   $locations += "global"
   $subscriptionId = "<your Azure subscription Id>"
   $resourceGroupName = "<resource group name your storage account belongs to>"
   $storageAccountName = "<your storage account name>"

   # Build the storage account Id from the settings above
   $storageAccountId = "/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Storage/storageAccounts/$storageAccountName"

   Add-AzureRmLogProfile -Name $logProfileName -Location $locations -StorageAccountId $storageAccountId
   ```

| Propriedade | Necessário | Descrição |
| --- | --- | --- |
| StorageAccountId |Sim |ID de recurso da conta do Storage para o qual os registos de atividades devem ser salvos. |
| Localizações |Sim |Lista separada por vírgulas de regiões para o qual pretende recolher eventos de registo de atividades. Pode ver uma lista de todas as regiões para a sua subscrição com `(Get-AzureRmLocation).Location`. |
| RetentionInDays |Não |Número de dias para que eventos devem ser mantidos, entre 1 e 2147483647. Um valor de zero armazena os logs de indefinidamente (Eterno). |
| Categorias |Não |Lista separada por vírgulas das categorias de evento que deve ser recolhidos. Valores possíveis são escrita, eliminação e ação.  Se não for indicado, em seguida, todos os valores possíveis são pressupõe-se |

## <a name="archive-the-activity-log-via-cli"></a>Arquivar o registo de atividade através da CLI

   ```azurecli-interactive
   az monitor log-profiles create --name "default" --location null --locations "global" "eastus" "westus" --categories "Delete" "Write" "Action"  --enabled false --days 0 --storage-account-id "/subscriptions/<YOUR SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.Storage/storageAccounts/<STORAGE ACCOUNT NAME>"
   ```

| Propriedade | Necessário | Descrição |
| --- | --- | --- |
| nome |Sim |Nome do seu perfil de registo. |
| storage-account-id |Sim |ID de recurso da conta do Storage para o qual os registos de atividades devem ser salvos. |
| localizações |Sim |Lista de regiões para o qual pretende recolher eventos de registo de atividades separadas por espaços. Pode ver uma lista de todas as regiões para a sua subscrição com `az account list-locations --query [].name`. |
| dias |Sim |Número de dias para que eventos devem ser mantidos, entre 1 e 2147483647. Um valor igual a zero irá armazenar os registos indefinidamente (Eterno).  Se zero, em seguida, o parâmetro ativado deve ser definido como true. |
|enabled | Sim |VERDADEIRO ou FALSO.  Utilizado para ativar ou desativar a política de retenção.  Se for VERDADEIRO, o parâmetro de dias tem de ser um valor maior que 0.
| categories |Sim |Lista de categorias de evento que deve ser coletado separadas por espaços. Valores possíveis são escrita, eliminação e ação. |

## <a name="storage-schema-of-the-activity-log"></a>Esquema de armazenamento do registo de atividades
Assim que tiver configurado a arquivamento, um contentor de armazenamento será criado na conta de armazenamento quando ocorre um evento de registo de atividades. Os blobs no contentor seguem a mesma Convenção de nomenclatura em registos de atividades e os registos de diagnóstico, conforme ilustrado aqui:

```
insights-operational-logs/name=default/resourceId=/SUBSCRIPTIONS/{subscription ID}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

Por exemplo, pode ser um nome de blob:

```
insights-operational-logs/name=default/resourceId=/SUBSCRIPTIONS/s1id1234-5679-0123-4567-890123456789/y=2016/m=08/d=22/h=18/m=00/PT1H.json
```

Cada blob PT1H.json contém um blob JSON de eventos que ocorreram dentro da hora especificada no URL do blob (por exemplo, h = 12). Durante a hora presente, os eventos são acrescentados ao ficheiro PT1H.json à medida que ocorrem. O valor de minuto (m = 00) é sempre 00, uma vez que os eventos de registo de Atividades são divididos em blobs individuais por hora.

No ficheiro PT1H.json cada evento é armazenado na matriz "registos", seguindo este formato:

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


| Nome do elemento | Descrição |
| --- | --- |
| hora |Timestamp quando o evento foi gerado pelo processamento do pedido correspondente o evento de serviço do Azure. |
| resourceId |ID de recurso do recurso afetado. |
| operationName |Nome da operação. |
| categoria |Categoria da ação, por exemplo. Escrita, leitura, a ação. |
| resultType |O tipo de resultado, por exemplo. Início de sucesso, falha, |
| resultSignature |Depende do tipo de recurso. |
| durationMs |Duração da operação em milissegundos |
| callerIpAddress |Endereço IP do utilizador que efetuou a operação, a afirmação UPN ou a afirmação SPN com base na disponibilidade. |
| correlationId |Normalmente, um GUID no formato de cadeia de caracteres. Eventos que partilham uma correlationId pertencem à mesma ação de uber. |
| identidade |Blob JSON que descrevem a autorização e afirmações. |
| Autorização |Blob de propriedades RBAC do evento. Normalmente, inclui as propriedades de "action", "função" e "escopo". |
| nível |Nível do evento. Um dos seguintes valores: "Crítico", "Error", "Aviso", "Informativo" e "Verbose" |
| localização |Região em que ocorreu a localização (ou global). |
| propriedades |Conjunto de `<Key, Value>` pares (ou seja, dicionário), que descreve os detalhes do evento. |

> [!NOTE]
> As propriedades e o uso dessas propriedades podem variar dependendo do recurso.
> 
> 

## <a name="next-steps"></a>Passos Seguintes
* [Transfira blobs para análise](../../storage/blobs/storage-quickstart-blobs-dotnet.md)
* [O registo de atividades para os Hubs de eventos do Stream](../../azure-monitor/platform/activity-logs-stream-event-hubs.md)
* [Leia mais sobre o registo de atividades](../../azure-monitor/platform/activity-logs-overview.md)


