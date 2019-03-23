---
title: Preparar para alteração de formato para os registos de diagnóstico do Azure Monitor
description: Registos de diagnóstico do Azure serão movidos para utilizar blobs de acréscimo de 1 de Novembro de 2018.
author: johnkemnetz
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 07/06/2018
ms.author: johnkem
ms.subservice: logs
ms.openlocfilehash: ab5fba6bbbf6ade83c7699edec937ba02b222939
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2019
ms.locfileid: "58370062"
---
# <a name="prepare-for-format-change-to-azure-monitor-diagnostic-logs-archived-to-a-storage-account"></a>Preparar para alteração de formato para os registos de diagnóstico do Azure Monitor arquivado para uma conta de armazenamento

> [!WARNING]
> Se enviar [registos de diagnóstico de recursos do Azure ou as métricas para uma conta de armazenamento com definições de diagnóstico de recursos](./../../azure-monitor/platform/archive-diagnostic-logs.md) ou [perfis de registo de registos de atividade para uma conta de armazenamento com](./../../azure-monitor/platform/archive-activity-log.md), o formato dos dados em a conta de armazenamento será alterado para linhas de JSON de 1 de Novembro de 2018. As instruções abaixo descrevem o impacto e como atualizar a sua ferramenta para lidar com o novo formato. 
>
> 

## <a name="what-is-changing"></a>O que está mudando

Monitor do Azure oferece uma funcionalidade que lhe permite enviar dados de diagnóstico de recursos e dados de registo de atividade para uma conta de armazenamento do Azure, o espaço de nomes de Hubs de eventos, ou para uma área de trabalho do Log Analytics no Azure Monitor. Para resolver um problema de desempenho do sistema, no **1 de Novembro de 2018, à meia-noite de 12:00 UTC** irá alterar o formato do registo de envio de dados para o armazenamento de Blobs. Se tiver as ferramentas, ou seja, os dados de leitura fora do armazenamento de BLOBs, terá de atualizar a sua ferramenta para compreender o novo formato de dados.

* Na quinta-feira, 1 de Novembro de 2018 à meia-noite de 12:00 UTC, o formato de blob será alterado para ser [linhas de JSON](http://jsonlines.org/). Isso significa que cada registo será delimitado por uma nova linha, com nenhuma das matrizes de registos externa e sem vírgulas entre os registros JSON.
* As alterações de formato de blob para todas as definições de diagnóstico em todas as subscrições de uma só vez. O primeiro ficheiro PT1H.json emitido para 1º de Novembro irá utilizar esse novo formato. Os nomes de BLOBs e de contentores permanecem os mesmos.
* Definir uma definição de diagnóstico entre agora e 1 de Novembro continua emitir dados no formato atual até 1 de Novembro.
* Esta alteração irá ocorrer ao mesmo tempo em todas as regiões de cloud pública. A alteração não irá ocorrer em clouds do Azure na China, Azure Alemanha ou do Azure Government ainda.
* Esta alteração irá afetar os seguintes tipos de dados:
  * [Registos de diagnóstico de recursos do Azure](./../../azure-monitor/platform/archive-diagnostic-logs.md) ([ver a lista de recursos aqui](./../../azure-monitor/platform/diagnostic-logs-schema.md))
  * [Métricas de recurso do Azure que está a ser exportadas por definições de diagnóstico](./../../azure-monitor/platform/diagnostic-logs-overview.md#diagnostic-settings)
  * [Dados de registo de atividade do Azure que está a ser exportados por perfis de registo](./../../azure-monitor/platform/archive-activity-log.md)
* Esta alteração não afeta:
  * Registos de fluxo de rede
  * Registos do Azure service não disponibilizados através do Azure Monitor ainda (por exemplo, registos de diagnóstico do serviço de aplicações do Azure, os registos de análise de armazenamento)
  * Roteamento de registos de diagnóstico do Azure e os registos de atividades a outros destinos (os Hubs de eventos, o Log Analytics)

### <a name="how-to-see-if-you-are-impacted"></a>Como ver se foi afetado

Só foi afetado por esta alteração se:
1. Estão a enviar dados de registo para uma conta de armazenamento do Azure com uma definição de diagnóstico de recursos, e
2. Ter as ferramentas que depende da estrutura JSON estes registos no armazenamento.
 
Para identificar se tiver definições de diagnóstico de recursos que estão a enviar dados para uma conta de armazenamento do Azure, pode navegar para o **Monitor** secção do portal, clique em **das definições de diagnóstico**e a identificar todos os recursos que tenham **estado de diagnóstico** definida como **ativado**:

![Painel de definições de diagnóstico do Monitor do Azure](./media/diagnostic-logs-append-blobs/portal-diag-settings.png)

Se o estado de diagnóstico está definido como ativado, tem uma definição de diagnóstico active esse recurso. Clique no recurso para ver se as definições de diagnóstico estão a enviar dados para uma conta de armazenamento:

![Conta de armazenamento ativada](./media/diagnostic-logs-append-blobs/portal-storage-enabled.png)

Se tiver recursos enviar dados para uma conta de armazenamento com estas definições de diagnóstico de recursos, o formato dos dados nessa conta de armazenamento será afetado por esta alteração. A menos que tenha as ferramentas personalizadas que opera fora essas contas de armazenamento, a alteração de formato não afetarão.

### <a name="details-of-the-format-change"></a>Detalhes da alteração de formato

O formato atual do ficheiro PT1H.json no armazenamento de Blobs do Azure utiliza uma matriz JSON de registos. Agora, eis um exemplo de um ficheiro de registo do Cofre de chaves:

```json
{
    "records": [
        {
            "time": "2016-01-05T01:32:01.2691226Z",
            "resourceId": "/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSOGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT",
            "operationName": "VaultGet",
            "operationVersion": "2015-06-01",
            "category": "AuditEvent",
            "resultType": "Success",
            "resultSignature": "OK",
            "resultDescription": "",
            "durationMs": "78",
            "callerIpAddress": "104.40.82.76",
            "correlationId": "",
            "identity": {
                "claim": {
                    "http://schemas.microsoft.com/identity/claims/objectidentifier": "d9da5048-2737-4770-bd64-XXXXXXXXXXXX",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "live.com#username@outlook.com",
                    "appid": "1950a258-227b-4e31-a9cf-XXXXXXXXXXXX"
                }
            },
            "properties": {
                "clientInfo": "azure-resource-manager/2.0",
                "requestUri": "https://control-prod-wus.vaultcore.azure.net/subscriptions/361da5d4-a47a-4c79-afdd-XXXXXXXXXXXX/resourcegroups/contosoresourcegroup/providers/Microsoft.KeyVault/vaults/contosokeyvault?api-version=2015-06-01",
                "id": "https://contosokeyvault.vault.azure.net/",
                "httpStatusCode": 200
            }
        },
        {
            "time": "2016-01-05T01:33:56.5264523Z",
            "resourceId": "/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSOGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT",
            "operationName": "VaultGet",
            "operationVersion": "2015-06-01",
            "category": "AuditEvent",
            "resultType": "Success",
            "resultSignature": "OK",
            "resultDescription": "",
            "durationMs": "83",
            "callerIpAddress": "104.40.82.76",
            "correlationId": "",
            "identity": {
                "claim": {
                    "http://schemas.microsoft.com/identity/claims/objectidentifier": "d9da5048-2737-4770-bd64-XXXXXXXXXXXX",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "live.com#username@outlook.com",
                    "appid": "1950a258-227b-4e31-a9cf-XXXXXXXXXXXX"
                }
            },
            "properties": {
                "clientInfo": "azure-resource-manager/2.0",
                "requestUri": "https://control-prod-wus.vaultcore.azure.net/subscriptions/361da5d4-a47a-4c79-afdd-XXXXXXXXXXXX/resourcegroups/contosoresourcegroup/providers/Microsoft.KeyVault/vaults/contosokeyvault?api-version=2015-06-01",
                "id": "https://contosokeyvault.vault.azure.net/",
                "httpStatusCode": 200
            }
        }
    ]
}
```

O novo formato usa [linhas JSON](http://jsonlines.org/), onde cada evento é uma linha e o caractere de nova linha indica um novo evento. Eis o que o exemplo acima terá o seguinte aspeto no ficheiro PT1H.json depois da alteração:

```json
{"time": "2016-01-05T01:32:01.2691226Z","resourceId": "/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSOGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT","operationName": "VaultGet","operationVersion": "2015-06-01","category": "AuditEvent","resultType": "Success","resultSignature": "OK","resultDescription": "","durationMs": "78","callerIpAddress": "104.40.82.76","correlationId": "","identity": {"claim": {"http://schemas.microsoft.com/identity/claims/objectidentifier": "d9da5048-2737-4770-bd64-XXXXXXXXXXXX","http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "live.com#username@outlook.com","appid": "1950a258-227b-4e31-a9cf-XXXXXXXXXXXX"}},"properties": {"clientInfo": "azure-resource-manager/2.0","requestUri": "https://control-prod-wus.vaultcore.azure.net/subscriptions/361da5d4-a47a-4c79-afdd-XXXXXXXXXXXX/resourcegroups/contosoresourcegroup/providers/Microsoft.KeyVault/vaults/contosokeyvault?api-version=2015-06-01","id": "https://contosokeyvault.vault.azure.net/","httpStatusCode": 200}}
{"time": "2016-01-05T01:33:56.5264523Z","resourceId": "/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSOGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT","operationName": "VaultGet","operationVersion": "2015-06-01","category": "AuditEvent","resultType": "Success","resultSignature": "OK","resultDescription": "","durationMs": "83","callerIpAddress": "104.40.82.76","correlationId": "","identity": {"claim": {"http://schemas.microsoft.com/identity/claims/objectidentifier": "d9da5048-2737-4770-bd64-XXXXXXXXXXXX","http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "live.com#username@outlook.com","appid": "1950a258-227b-4e31-a9cf-XXXXXXXXXXXX"}},"properties": {"clientInfo": "azure-resource-manager/2.0","requestUri": "https://control-prod-wus.vaultcore.azure.net/subscriptions/361da5d4-a47a-4c79-afdd-XXXXXXXXXXXX/resourcegroups/contosoresourcegroup/providers/Microsoft.KeyVault/vaults/contosokeyvault?api-version=2015-06-01","id": "https://contosokeyvault.vault.azure.net/","httpStatusCode": 200}}
```

Esse novo formato permite que o Azure Monitor para ficheiros de registo de push usando [blobs de acréscimo](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-append-blobs), que são mais eficiente para acrescentar continuamente novos dados de eventos.

## <a name="how-to-update"></a>Como atualizar

Apenas terá de fazer atualizações, se tiver ferramentas personalizadas que ingere estes ficheiros de registo para processamento adicional. Se estiver a efetuar utilizar-se de uma análise de registo externo ou a ferramenta SIEM, recomendamos [com os hubs de eventos para ingerir estes dados em vez disso](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/). Integração de hubs de eventos é mais fácil em termos de processamento de registos a partir de muitos serviços e uso de indicadores de localização num log específico.

Ferramentas personalizadas devem ser atualizadas para lidar com o formato atual e o formato de linhas JSON descrito acima. Isto irá garantir que, quando os dados começaram a aparecer no novo formato, as ferramentas não são interrompidas.

## <a name="next-steps"></a>Passos Seguintes

* Saiba mais sobre [arquivar registos de diagnóstico de recursos para uma conta de armazenamento](./../../azure-monitor/platform/archive-diagnostic-logs.md)
* Saiba mais sobre [arquivamento de dados de registo de atividade para uma conta de armazenamento](./../../azure-monitor/platform/archive-activity-log.md)

