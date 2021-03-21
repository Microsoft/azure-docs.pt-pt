---
title: Prepare-se para a mudança de formato para registos de recursos do Azure Monitor
description: Os registos de recursos Azure mudaram-se para usar blobs de apêndice em 1 de novembro de 2018.
author: johnkemnetz
services: monitoring
ms.topic: conceptual
ms.date: 07/06/2018
ms.author: johnkem
ms.openlocfilehash: d4be3983d70a1ca78d849e231b8cc55e2b5895d4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102033202"
---
# <a name="prepare-for-format-change-to-azure-monitor-platform-logs-archived-to-a-storage-account"></a>Prepare-se para a mudança de formato para registos de plataforma do Azure Monitor arquivados numa conta de armazenamento

> [!WARNING]
> Se estiver a enviar [registos ou métricas de recursos Azure para uma conta de armazenamento utilizando configurações](./resource-logs.md#send-to-azure-storage) de diagnóstico ou [registos de atividade para uma conta de armazenamento utilizando perfis](./resource-logs.md#send-to-azure-storage)de registo, o formato dos dados na conta de armazenamento foi alterado para JSON Lines em 1 de novembro de 2018. As instruções abaixo descrevem o impacto e como atualizar a sua ferramenta para lidar com o novo formato.
>

## <a name="what-changed"></a>O que mudou

O Azure Monitor oferece uma capacidade que lhe permite enviar registos de recursos e registos de atividades numa conta de armazenamento Azure, espaço de nomes de Event Hubs ou num espaço de trabalho log Analytics no Azure Monitor. Para resolver um problema de desempenho do sistema, no **dia 1 de novembro de 2018 às 12:00 UTC da meia-noite** o formato de dados de registo enviados para o armazenamento de bolhas mudou. Se tiver ferramentas que estão a ler dados fora do armazenamento de bolhas, precisa de atualizar a sua ferramenta para entender o novo formato de dados.

* Na quinta-feira, 1 de novembro de 2018, às 12:00 UTC, o formato blob mudou para ser [JSON Lines](http://jsonlines.org/). Isto significa que cada registo será delimitado por uma nova linha, sem matriz de registos externos e sem vírgulas entre os registos da JSON.
* O formato blob foi alterado para todas as definições de diagnóstico em todas as subscrições ao mesmo tempo. A primeira PT1H.jsem ficheiro emitido para 1 de novembro utilizou este novo formato. Os nomes da bolha e dos contentores permanecem os mesmos.
* A definição de uma definição de diagnóstico entre antes de 1 de novembro continuou a emitir dados no formato atual até 1 de novembro.
* Esta mudança ocorreu ao mesmo tempo em todas as regiões de nuvem pública. A mudança não ocorrerá no Microsoft Azure Operado pela 21Vianet, Azure Germany ou Azure Government.
* Esta alteração tem impacto nos seguintes tipos de dados:
  * [Registos de recursos Azure](./resource-logs.md#send-to-azure-storage) [(ver lista de recursos aqui)](./resource-logs-schema.md)
  * [Métricas de recursos Azure sendo exportadas por configurações de diagnóstico](../essentials/diagnostic-settings.md)
  * [Dados de registo de atividade azure sendo exportados por perfis de registo](./activity-log.md)
* Esta alteração não tem impacto:
  * Registos de fluxo de rede
  * Os registos de serviço Azure ainda não foram disponibilizados através do Azure Monitor (por exemplo, registos de recursos do Azure App Service, registos de análise de armazenamento)
  * Encaminhamento de registos de recursos Azure e registos de atividades para outros destinos (Event Hubs, Log Analytics)

### <a name="how-to-see-if-you-are-impacted"></a>Como ver se tem impacto

Só é impactado por esta mudança se:
1. Estão enviando dados de registo para uma conta de armazenamento Azure usando uma definição de diagnóstico, e
2. Ter ferramentas que dependem da estrutura JSON destes registos no armazenamento.
 
Para identificar se tem configurações de diagnóstico que estão a enviar dados para uma conta de armazenamento Azure, pode navegar para a secção **Monitor** do portal, clicar em **Definições** de Diagnóstico e identificar quaisquer recursos que tenham o **Estado de Diagnóstico** definido para **Ativado**:

![Lâmina de diagnóstico de monitor de Azure](media/resource-logs-blob-format/portal-diag-settings.png)

Se o Estado de Diagnóstico estiver programado para ativar, tem uma definição de diagnóstico ativa nesse recurso. Clique no recurso para ver se alguma definição de diagnóstico está enviando dados para uma conta de armazenamento:

![Conta de armazenamento ativada](media/resource-logs-blob-format/portal-storage-enabled.png)

Se tiver recursos que enviem dados para uma conta de armazenamento utilizando estas definições de diagnóstico de recursos, o formato dos dados nessa conta de armazenamento será impactado por esta alteração. A menos que tenha ferramentas personalizadas que operam fora destas contas de armazenamento, a alteração de formato não irá impactá-lo.

### <a name="details-of-the-format-change"></a>Detalhes da alteração do formato

O formato atual do PT1H.jsem ficheiro no armazenamento de blob Azure utiliza uma série de registos JSON. Aqui está uma amostra de um ficheiro de registo KeyVault agora:

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

O novo formato utiliza [linhas JSON,](http://jsonlines.org/)onde cada evento é uma linha e o personagem newline indica um novo evento. Aqui está o que a amostra acima será na PT1H.jsem arquivo após a mudança:

```json
{"time": "2016-01-05T01:32:01.2691226Z","resourceId": "/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSOGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT","operationName": "VaultGet","operationVersion": "2015-06-01","category": "AuditEvent","resultType": "Success","resultSignature": "OK","resultDescription": "","durationMs": "78","callerIpAddress": "104.40.82.76","correlationId": "","identity": {"claim": {"http://schemas.microsoft.com/identity/claims/objectidentifier": "d9da5048-2737-4770-bd64-XXXXXXXXXXXX","http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "live.com#username@outlook.com","appid": "1950a258-227b-4e31-a9cf-XXXXXXXXXXXX"}},"properties": {"clientInfo": "azure-resource-manager/2.0","requestUri": "https://control-prod-wus.vaultcore.azure.net/subscriptions/361da5d4-a47a-4c79-afdd-XXXXXXXXXXXX/resourcegroups/contosoresourcegroup/providers/Microsoft.KeyVault/vaults/contosokeyvault?api-version=2015-06-01","id": "https://contosokeyvault.vault.azure.net/","httpStatusCode": 200}}
{"time": "2016-01-05T01:33:56.5264523Z","resourceId": "/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSOGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT","operationName": "VaultGet","operationVersion": "2015-06-01","category": "AuditEvent","resultType": "Success","resultSignature": "OK","resultDescription": "","durationMs": "83","callerIpAddress": "104.40.82.76","correlationId": "","identity": {"claim": {"http://schemas.microsoft.com/identity/claims/objectidentifier": "d9da5048-2737-4770-bd64-XXXXXXXXXXXX","http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "live.com#username@outlook.com","appid": "1950a258-227b-4e31-a9cf-XXXXXXXXXXXX"}},"properties": {"clientInfo": "azure-resource-manager/2.0","requestUri": "https://control-prod-wus.vaultcore.azure.net/subscriptions/361da5d4-a47a-4c79-afdd-XXXXXXXXXXXX/resourcegroups/contosoresourcegroup/providers/Microsoft.KeyVault/vaults/contosokeyvault?api-version=2015-06-01","id": "https://contosokeyvault.vault.azure.net/","httpStatusCode": 200}}
```

Este novo formato permite ao Azure Monitor empurrar ficheiros de registo utilizando [blobs de apêndice ,](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-append-blobs)que são mais eficientes para a anexar continuamente novos dados de eventos.

## <a name="how-to-update"></a>Como atualizar

Só precisa de fazer atualizações se tiver ferramentas personalizadas que ingerirem estes ficheiros de registo para posterior processamento. Se estiver a utilizar uma ferramenta externa de registo ou SIEM, recomendamos a utilização de [centros de eventos para ingerir estes dados.](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/) A integração de centros de eventos é mais fácil em termos de processamento de registos de muitos serviços e localização de marcadores num determinado registo.

As ferramentas personalizadas devem ser atualizadas para lidar tanto com o formato atual como com o formato JSON Lines acima descrito. Isto irá garantir que quando os dados começarem a aparecer no novo formato, as suas ferramentas não se quebram.

## <a name="next-steps"></a>Passos seguintes

* Saiba mais [sobre arquivar registos de recursos de recursos para uma conta de armazenamento](./resource-logs.md#send-to-azure-storage)
* Saiba mais sobre [arquivar dados de registo de atividades para uma conta de armazenamento](./activity-log.md#legacy-collection-methods)
