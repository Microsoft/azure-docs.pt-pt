---
title: Preparar para alterar o formato para Azure Monitor logs de diagnóstico
description: Descreve o impacto e como atualizar suas ferramentas para lidar com os novos logs de diagnóstico do Azure que foram alterados para usar blobs de acréscimo em 1º de novembro de 2018.
author: johnkemnetz
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 07/06/2018
ms.author: johnkem
ms.subservice: logs
ms.openlocfilehash: 5e71f4c590e4eafea5a2c6ad52b8df8c7dcf3814
ms.sourcegitcommit: 8a2949267c913b0e332ff8675bcdfc049029b64b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74307052"
---
# <a name="prepare-for-format-change-to-azure-monitor-diagnostic-logs-archived-to-a-storage-account"></a>Preparar para alterar o formato para Azure Monitor logs de diagnóstico arquivados em uma conta de armazenamento

> [!WARNING]
> Se você estiver enviando [logs ou métricas de diagnóstico de recursos do Azure para uma conta de armazenamento usando configurações de diagnóstico de recurso](./../../azure-monitor/platform/archive-diagnostic-logs.md) ou [logs de atividade para uma conta de armazenamento usando perfis de log](./../../azure-monitor/platform/archive-activity-log.md), o formato dos dados na conta de armazenamento será alterado para linhas JSON em Nov. 1, 2018. As instruções a seguir descrevem o impacto e como atualizar suas ferramentas para lidar com o novo formato. 
>
> 

## <a name="what-is-changing"></a>O que está sendo alterado

O Azure Monitor oferece uma funcionalidade que permite enviar dados de diagnóstico de recursos e dados de log de atividades para uma conta de armazenamento do Azure, namespace de hubs de eventos ou em um espaço de trabalho Log Analytics no Azure Monitor. Para resolver um problema de desempenho do sistema, em **1º de novembro de 2018 às 12:00, meia-noite UTC** o formato dos dados de log enviados para o armazenamento de BLOBs será alterado. Se você tiver ferramentas que estão lendo dados fora do armazenamento de BLOBs, precisará atualizar suas ferramentas para entender o novo formato de dados.

* Na quinta-feira, 1º de novembro de 2018 às 12:00, meia-noite UTC, o formato de blob será alterado para [as linhas JSON](http://jsonlines.org/). Isso significa que cada registro será delimitado por uma nova linha, sem nenhuma matriz de registros externos e sem vírgulas entre os registros JSON.
* O formato de blob é alterado para todas as configurações de diagnóstico em todas as assinaturas de uma só vez. O primeiro arquivo PT1H. JSON emitido para 1º de novembro usará esse novo formato. Os nomes de BLOB e contêiner permanecem os mesmos.
* Definir uma configuração de diagnóstico entre agora e 1 de novembro continuará emitindo dados no formato atual até 1º de novembro.
* Essa alteração ocorrerá de uma só vez em todas as regiões de nuvem pública. A alteração não ocorrerá no Microsoft Azure operado pelas nuvens da 21Vianet, do Azure Alemanha ou do Azure governamental ainda.
* Essa alteração afeta os seguintes tipos de dados:
  * [Logs de diagnóstico de recursos do Azure](archive-diagnostic-logs.md) ([consulte a lista de recursos aqui](diagnostic-logs-schema.md))
  * [Métricas de recursos do Azure sendo exportadas pelas configurações de diagnóstico](diagnostic-settings.md)
  * [Dados do log de atividades do Azure sendo exportados por perfis de log](archive-activity-log.md)
* Essa alteração não afeta:
  * Logs de fluxo de rede
  * Logs de serviço do Azure não disponibilizados por meio do Azure Monitor ainda (por exemplo, Azure App logs de diagnóstico do serviço, logs de análise de armazenamento)
  * Roteamento de logs de diagnóstico e logs de atividades do Azure para outros destinos (hubs de eventos, Log Analytics)

### <a name="how-to-see-if-you-are-impacted"></a>Como ver se você está impactado

Você só será afetado por essa alteração se:
1. Está enviando dados de log para uma conta de armazenamento do Azure usando uma configuração de diagnóstico de recurso e
2. Têm ferramentas que dependem da estrutura JSON desses logs no armazenamento.
 
Para identificar se você tem configurações de diagnóstico de recurso que estão enviando dados para uma conta de armazenamento do Azure, navegue até a seção **Monitor** do portal, clique em **configurações de diagnóstico**e identifique todos os recursos que têm o status de **diagnóstico** definido como **habilitado**:

![Folha configurações de diagnóstico Azure Monitor](./media/diagnostic-logs-append-blobs/portal-diag-settings.png)

Se o status de diagnóstico for definido como habilitado, você terá uma configuração de diagnóstico ativo nesse recurso. Clique no recurso para ver se as configurações de diagnóstico estão enviando dados para uma conta de armazenamento:

![Conta de armazenamento habilitada](./media/diagnostic-logs-append-blobs/portal-storage-enabled.png)

Se você tiver recursos que enviam dados para uma conta de armazenamento usando essas configurações de diagnóstico de recurso, o formato dos dados nessa conta de armazenamento será afetado por essa alteração. A menos que você tenha ferramentas personalizadas que operem dessas contas de armazenamento, a alteração de formato não afetará você.

### <a name="details-of-the-format-change"></a>Detalhes da alteração de formato

O formato atual do arquivo PT1H. JSON no armazenamento de BLOBs do Azure usa uma matriz JSON de registros. Aqui está um exemplo de um arquivo de log do keyvault agora:

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

O novo formato usa [linhas JSON](http://jsonlines.org/), em que cada evento é uma linha e o caractere de linhagem indica um novo evento. Veja a seguir a aparência do exemplo acima no arquivo PT1H. JSON após a alteração:

```json
{"time": "2016-01-05T01:32:01.2691226Z","resourceId": "/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSOGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT","operationName": "VaultGet","operationVersion": "2015-06-01","category": "AuditEvent","resultType": "Success","resultSignature": "OK","resultDescription": "","durationMs": "78","callerIpAddress": "104.40.82.76","correlationId": "","identity": {"claim": {"http://schemas.microsoft.com/identity/claims/objectidentifier": "d9da5048-2737-4770-bd64-XXXXXXXXXXXX","http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "live.com#username@outlook.com","appid": "1950a258-227b-4e31-a9cf-XXXXXXXXXXXX"}},"properties": {"clientInfo": "azure-resource-manager/2.0","requestUri": "https://control-prod-wus.vaultcore.azure.net/subscriptions/361da5d4-a47a-4c79-afdd-XXXXXXXXXXXX/resourcegroups/contosoresourcegroup/providers/Microsoft.KeyVault/vaults/contosokeyvault?api-version=2015-06-01","id": "https://contosokeyvault.vault.azure.net/","httpStatusCode": 200}}
{"time": "2016-01-05T01:33:56.5264523Z","resourceId": "/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSOGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT","operationName": "VaultGet","operationVersion": "2015-06-01","category": "AuditEvent","resultType": "Success","resultSignature": "OK","resultDescription": "","durationMs": "83","callerIpAddress": "104.40.82.76","correlationId": "","identity": {"claim": {"http://schemas.microsoft.com/identity/claims/objectidentifier": "d9da5048-2737-4770-bd64-XXXXXXXXXXXX","http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "live.com#username@outlook.com","appid": "1950a258-227b-4e31-a9cf-XXXXXXXXXXXX"}},"properties": {"clientInfo": "azure-resource-manager/2.0","requestUri": "https://control-prod-wus.vaultcore.azure.net/subscriptions/361da5d4-a47a-4c79-afdd-XXXXXXXXXXXX/resourcegroups/contosoresourcegroup/providers/Microsoft.KeyVault/vaults/contosokeyvault?api-version=2015-06-01","id": "https://contosokeyvault.vault.azure.net/","httpStatusCode": 200}}
```

Esse novo formato permite que Azure Monitor envie arquivos de log usando [blobs de acréscimo](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-append-blobs), que são mais eficientes para anexar continuamente novos dados de evento.

## <a name="how-to-update"></a>Como atualizar

Você só precisa fazer atualizações se tiver ferramentas personalizadas que insomem esses arquivos de log para processamento adicional. Se você estiver usando uma ferramenta de log Analytics ou SIEM externa, é recomendável [usar os hubs de eventos para ingerir esses dados em vez disso](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/). A integração dos hubs de eventos é mais fácil em termos de processamento de logs de vários serviços e do local de indicadores em um log específico.

As ferramentas personalizadas devem ser atualizadas para lidar com o formato atual e o formato das linhas JSON descritas acima. Isso garantirá que quando os dados começarem a aparecer no novo formato, suas ferramentas não serão interrompidas.

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre como [arquivar logs de diagnóstico de recurso em uma conta de armazenamento](./../../azure-monitor/platform/archive-diagnostic-logs.md)
* Saiba mais sobre como [arquivar dados de log de atividades em uma conta de armazenamento](./../../azure-monitor/platform/archive-activity-log.md)

