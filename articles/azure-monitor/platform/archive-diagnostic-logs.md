---
title: Arquivar registos de diagnóstico do Azure
description: Aprenda a arquivar os registos de diagnóstico do Azure para retenção de longa duração numa conta de armazenamento.
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 07/18/2018
ms.author: johnkem
ms.subservice: logs
ms.openlocfilehash: 0d23509d4efb0385770811e004bb2599c3866847
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2019
ms.locfileid: "57313349"
---
# <a name="archive-azure-diagnostic-logs"></a>Arquivar registos de diagnóstico do Azure

Neste artigo, vamos mostrar como pode usar o portal do Azure, Cmdlets do PowerShell, CLI ou REST API para arquivar sua [registos de diagnóstico do Azure](../../azure-monitor/platform/diagnostic-logs-overview.md) numa conta de armazenamento. Esta opção é útil se gostaria de manter os seus registos de diagnóstico com uma política de retenção opcional para auditoria, análise estática ou cópia de segurança. A conta de armazenamento não tem de estar na mesma subscrição que o recurso emite os registos, desde que o utilizador que configura a definição possui acesso RBAC adequado para ambas as subscrições.

> [!WARNING]
> O formato dos dados de registo na conta de armazenamento vai ser alterado para Linhas de JSON a 1 de novembro de 2018. [Leia este artigo para obter uma descrição do impacto e saber como atualizar a sua ferramenta para trabalhar com o novo formato.](./../../azure-monitor/platform/diagnostic-logs-append-blobs.md) 
>
> 

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, precisa [criar uma conta de armazenamento](../../storage/common/storage-quickstart-create-account.md) ao qual pode arquivar os seus registos de diagnóstico. É altamente recomendável que não use uma conta de armazenamento existente que tenha outros, não monitorizar dados armazenados na mesma, para que pode controlar melhor acesso a dados de monitorização. No entanto, se também são arquivar o registo de atividades e métricas de diagnóstico para uma conta de armazenamento, talvez faça sentido usar essa conta de armazenamento para os seus registos de diagnóstico para manter todos os dados de monitorização numa localização central.

> [!NOTE]
>  Atualmente não pode arquivar dados a um armazenamento de conta que, por trás de uma rede virtual protegida.

## <a name="diagnostic-settings"></a>Definições de diagnóstico

Para arquivar os registos de diagnósticos usando qualquer um dos métodos abaixo, define um **definição de diagnóstico** para um recurso em particular. Uma definição de diagnóstico para um recurso define as categorias de registos e métricos dados enviados para um destino (conta de armazenamento, espaço de nomes de Hubs de eventos ou do Log Analytics). Também define a política de retenção (número de dias a manter) para eventos de cada categoria de registo e dados métricos armazenados numa conta de armazenamento. Se uma política de retenção é definida como zero, eventos dessa categoria de registo são armazenados indefinidamente (ou seja dizer que, para sempre). Uma política de retenção caso contrário, pode ser qualquer número de dias entre 1 e 2147483647. [Pode ler mais sobre as definições de diagnóstico aqui](../../azure-monitor/platform/diagnostic-logs-overview.md#diagnostic-settings). Políticas de retenção são aplicado por dia, portanto, no final do dia (UTC), registos a partir do dia em que está, agora, além do período de retenção política será eliminada. Por exemplo, se tivesse uma política de retenção de um dia, no início do dia hoje os registos de ontem de before dia serão eliminados. O processo de eliminação começa a meia-noite UTC, mas tenha em atenção que pode demorar até 24 horas para os registos para ser eliminado da sua conta de armazenamento. 

> [!NOTE]
> Atualmente, o envio de métricas multidimensionais através das definições de diagnóstico não é suportado. As métricas com dimensões são exportadas como métricas dimensionais simples e agregadas em valores de dimensões.
>
> *Por exemplo*: A métrica "Mensagens recebidas" num Hub de eventos pode ser explorada e representada um por nível de fila. No entanto, se for exportada através das definições de diagnóstico, a métrica será representada como todas as mensagens recebidas em todas as filas do hub do Hub de Eventos.
>
>

## <a name="archive-diagnostic-logs-using-the-portal"></a>Arquivar os registos de diagnóstico com o portal

1. No portal, navegue para o Azure Monitor e clique em **definições de diagnóstico**

    ![Secção de monitorização do Azure Monitor](media/archive-diagnostic-logs/diagnostic-settings-blade.png)

2. Opcionalmente, filtrar a lista por grupo de recursos ou tipo de recurso, em seguida, clique no recurso para o qual pretende configurar uma definição de diagnóstico.

3. Se não existem definições existem no recurso que selecionou, lhe for pedido para criar uma definição. Clique em "Ativar diagnósticos."

   ![Adicionar definição de diagnóstico - sem definições existentes](media/archive-diagnostic-logs/diagnostic-settings-none.png)

   Se existirem definições existentes no recurso, verá uma lista das definições já configurada neste recurso. Clique em "Adicionar definição de diagnóstico".

   ![Adicionar definição de diagnóstico - existente definições](media/archive-diagnostic-logs/diagnostic-settings-multiple.png)

3. Dê a sua definição de um nome e marque a caixa **exportar para a conta de armazenamento**, em seguida, selecione uma conta de armazenamento. Opcionalmente, defina um número de dias para reter estes registos ao utilizar o **retenção (dias)** controlos de deslize. A retenção de zero dias armazena os logs de indefinidamente.

   ![Adicionar definição de diagnóstico - existente definições](media/archive-diagnostic-logs/diagnostic-settings-configure.png)

4. Clique em **Guardar**.

Após alguns instantes, a nova definição é apresentada na sua lista de definições para este recurso e os registos de diagnóstico são arquivados para essa conta de armazenamento quando novos dados de evento são gerados.

## <a name="archive-diagnostic-logs-via-azure-powershell"></a>Arquivar os registos de diagnóstico através do Azure PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

```
Set-AzDiagnosticSetting -ResourceId /subscriptions/s1id1234-5679-0123-4567-890123456789/resourceGroups/testresourcegroup/providers/Microsoft.Network/networkSecurityGroups/testnsg -StorageAccountId /subscriptions/s1id1234-5679-0123-4567-890123456789/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -Categories networksecuritygroupevent,networksecuritygrouprulecounter -Enabled $true -RetentionEnabled $true -RetentionInDays 90
```

| Propriedade | Necessário | Descrição |
| --- | --- | --- |
| ResourceId |Sim |ID de recurso do recurso no qual pretende definir uma definição de diagnóstico. |
| StorageAccountId |Não |ID de recurso da conta do Storage para o qual os registos de diagnóstico deverá ser guardados. |
| Categorias |Não |Lista separada por vírgulas de categorias de registo para ativar. |
| Ativado |Sim |Booleano indicando se o diagnóstico está ativado ou desativado neste recurso. |
| RetentionEnabled |Não |Booleano indicando se uma política de retenção estão ativadas neste recurso. |
| RetentionInDays |Não |Número de dias para as quais eventos devem ser mantidos entre 1 e 2147483647. Um valor de zero armazena os logs de indefinidamente. |

## <a name="archive-diagnostic-logs-via-the-azure-cli"></a>Arquivar os registos de diagnóstico através da CLI do Azure

```azurecli
az monitor diagnostic-settings create --name <diagnostic name> \
    --storage-account <name or ID of storage account> \
    --resource <target resource object ID> \
    --resource-group <storage account resource group> \
    --logs '[
    {
        "category": <category name>,
        "enabled": true,
        "retentionPolicy": {
            "days": <# days to retain>,
            "enabled": true
        }
    }]'
```

Pode adicionar categorias adicionais no registo de diagnóstico através da adição de dicionários para a matriz JSON passada como o `--logs` parâmetro.

O `--resource-group` argumento só é necessária se `--storage-account` não é um ID de objeto. Para obter a documentação completa para arquivar os registos de diagnóstico para o armazenamento, consulte a [referência de comando da CLI](/cli/azure/monitor/diagnostic-settings#az-monitor-diagnostic-settings-create).

## <a name="archive-diagnostic-logs-via-the-rest-api"></a>Arquivar os registos de diagnóstico através da API REST

[Veja este documento](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings) para obter informações sobre como pode configurar uma definição de diagnóstico utilizando a API de REST do Azure Monitor.

## <a name="schema-of-diagnostic-logs-in-the-storage-account"></a>Esquema de registos de diagnóstico na conta de armazenamento

Assim que tiver configurado a arquivamento, é criado um contentor de armazenamento na conta de armazenamento, assim que um evento ocorre em uma das categorias de registo que tiver ativado. Os blobs no contentor seguem a mesma Convenção de nomenclatura em registos de atividades e os registos de diagnóstico, conforme ilustrado aqui:

```
insights-logs-{log category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/RESOURCEGROUPS/{resource group name}/PROVIDERS/{resource provider name}/{resource type}/{resource name}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

Por exemplo, pode ser um nome de blob:

```
insights-logs-networksecuritygrouprulecounter/resourceId=/SUBSCRIPTIONS/s1id1234-5679-0123-4567-890123456789/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUP/TESTNSG/y=2016/m=08/d=22/h=18/m=00/PT1H.json
```

Cada blob PT1H.json contém um blob JSON de eventos que ocorreram dentro da hora especificada no URL do blob (por exemplo, h=12). Durante a hora presente, os eventos são acrescentados ao ficheiro PT1H.json à medida que ocorrem. O valor de minuto (m = 00) é sempre 00, uma vez que os eventos de registo de diagnóstico são divididos em blobs individuais por hora.

No ficheiro PT1H.json cada evento é armazenado na matriz "registos", seguindo este formato:

``` JSON
{
    "records": [
        {
            "time": "2016-07-01T00:00:37.2040000Z",
            "systemId": "46cdbb41-cb9c-4f3d-a5b4-1d458d827ff1",
            "category": "NetworkSecurityGroupRuleCounter",
            "resourceId": "/SUBSCRIPTIONS/s1id1234-5679-0123-4567-890123456789/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/TESTNSG",
            "operationName": "NetworkSecurityGroupCounters",
            "properties": {
                "vnetResourceGuid": "{12345678-9012-3456-7890-123456789012}",
                "subnetPrefix": "10.3.0.0/24",
                "macAddress": "000123456789",
                "ruleName": "/subscriptions/ s1id1234-5679-0123-4567-890123456789/resourceGroups/testresourcegroup/providers/Microsoft.Network/networkSecurityGroups/testnsg/securityRules/default-allow-rdp",
                "direction": "In",
                "type": "allow",
                "matchedConnections": 1988
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
| categoria |Categoria de registo do evento. |
| propriedades |Conjunto de `<Key, Value>` pares (ou seja, dicionário), que descreve os detalhes do evento. |

> [!NOTE]
> As propriedades e o uso dessas propriedades podem variar dependendo do recurso.

## <a name="next-steps"></a>Passos Seguintes

* [Transfira blobs para análise](../../storage/blobs/storage-quickstart-blobs-dotnet.md)
* [Registos de diagnóstico do Stream para um espaço de nomes de Hubs de eventos](../../azure-monitor/platform/diagnostic-logs-stream-event-hubs.md)
* [Arquivar registos do Azure Active Directory com o Azure Monitor](../../active-directory/reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account.md)
* [Leia mais sobre os registos de diagnóstico](../../azure-monitor/platform/diagnostic-logs-overview.md)

