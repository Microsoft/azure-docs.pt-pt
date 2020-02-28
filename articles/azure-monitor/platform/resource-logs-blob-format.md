---
title: Prepare-se para a alteração do formato para os registos de recursos do Monitor Azure
description: Os registos de recursos azure mudaram-se para usar bolhas de apêndice a 1 de novembro de 2018.
author: johnkemnetz
services: monitoring
ms.topic: conceptual
ms.date: 07/06/2018
ms.author: johnkem
ms.subservice: logs
ms.openlocfilehash: b90b6f8eabbda229fbed4dcef6a5d863ea721faf
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2020
ms.locfileid: "77659140"
---
# <a name="prepare-for-format-change-to-azure-monitor-platform-logs-archived-to-a-storage-account"></a>Prepare-se para a alteração do formato para os registos da plataforma Azure Monitor arquivados numa conta de armazenamento

> [!WARNING]
> Se estiver a enviar [registos ou métricas](resource-logs-collect-storage.md) de recursos do Azure para uma conta de armazenamento utilizando configurações de diagnóstico ou registos de atividade para uma conta de armazenamento utilizando perfis de [registo,](activity-log-export.md)o formato dos dados na conta de armazenamento mudou para Linhas JSON em 1 de novembro de 2018. As instruções abaixo descrevem o impacto e como atualizar a sua ferramenta para lidar com o novo formato.
>

## <a name="what-changed"></a>O que mudou

O Azure Monitor oferece uma capacidade que lhe permite enviar registos de recursos e registos de atividade numa conta de armazenamento Azure, espaço de nome de Event Hubs ou num espaço de trabalho de Log Analytics no Monitor Azure. Para resolver um problema de desempenho do sistema, no dia 1 de novembro de **2018 às 12:00 UTC,** o formato de dados de registo enviados para o armazenamento blob alterado. Se tiver uma ferramenta que esteja a ler dados a partir do armazenamento de blob, precisa de atualizar a sua ferramenta para compreender o novo formato de dados.

* Na quinta-feira, 1 de novembro de 2018, às 12:00 UTC, o formato blob mudou para [jSON Lines](http://jsonlines.org/). Isto significa que cada disco será delimitado por uma novalinha, sem matriz de registos externos e sem vírgulas entre os registos da JSON.
* O formato blob alterou-se para todas as definições de diagnóstico em todas as subscrições ao mesmo tempo. O primeiro ficheiro PT1H.json emitido para 1 de novembro utilizou este novo formato. Os nomes de blob e contentor permanecem os mesmos.
* A definição de uma definição de diagnóstico entre o dia 1 de novembro continuou a emitir dados no formato atual até 1 de novembro.
* Esta mudança ocorreu de uma só vez em todas as regiões públicas de nuvem. A mudança não ocorrerá ainda na Microsoft Azure Operada pela 21Vianet, Azure Germany ou azure Government.
* Esta alteração tem impacto nos seguintes tipos de dados:
  * [Registos](archive-diagnostic-logs.md) de recursos Azure[(ver lista de recursos aqui)](diagnostic-logs-schema.md)
  * [Métricas de recursos azure exportadas por configurações de diagnóstico](diagnostic-settings.md)
  * [Dados de registo da Atividade Azure que são exportados por perfis de registo](activity-log-collect.md)
* Esta alteração não tem impacto:
  * Registos de fluxo de rede
  * Registos de serviço azure ainda não disponibilizados através do Azure Monitor (por exemplo, registos de recursos do Serviço de Aplicações Azure, registos de análise de armazenamento)
  * Encaminhamento de registos de recursos Azure e registos de atividade para outros destinos (Hubs de Eventos, Log Analytics)

### <a name="how-to-see-if-you-are-impacted"></a>Como ver se é impactado

Só é afetado por esta mudança se:
1. Estão a enviar dados de registo para uma conta de armazenamento Azure usando uma definição de diagnóstico, e
2. Ter ferramentas que dependem da estrutura JSON destes troncos no armazenamento.
 
Para identificar se tem configurações de diagnóstico que estão a enviar dados para uma conta de armazenamento Azure, pode navegar para a secção **Monitor** do portal, clicar em Definições de **Diagnóstico,** e identificar quaisquer recursos que tenham **o Estado** de Diagnóstico definido para **Ativado:**

![Lâmina de definições de diagnóstico do Monitor Azure](media/diagnostic-logs-append-blobs/portal-diag-settings.png)

Se o Estado de Diagnóstico estiver programado para ativar, tem uma definição de diagnóstico ativa nesse recurso. Clique no recurso para ver se alguma definição de diagnóstico está a enviar dados para uma conta de armazenamento:

![Conta de armazenamento ativada](media/diagnostic-logs-append-blobs/portal-storage-enabled.png)

Se tiver recursos para enviar dados para uma conta de armazenamento utilizando estas definições de diagnóstico de recursos, o formato dos dados nessa conta de armazenamento será impactado por esta alteração. A menos que tenha ferramentas personalizadas que operam fora destas contas de armazenamento, a alteração do formato não irá impactá-lo.

### <a name="details-of-the-format-change"></a>Detalhes da mudança de formato

O formato atual do ficheiro PT1H.json no armazenamento de blob Azure utiliza um conjunto de registos JSON. Aqui está uma amostra de um ficheiro de registo KeyVault agora:

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

O novo formato utiliza [linhas JSON,](http://jsonlines.org/)onde cada evento é uma linha e o personagem newline indica um novo evento. Aqui está o que a amostra acima será no ficheiro PT1H.json após a mudança:

```json
{"time": "2016-01-05T01:32:01.2691226Z","resourceId": "/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSOGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT","operationName": "VaultGet","operationVersion": "2015-06-01","category": "AuditEvent","resultType": "Success","resultSignature": "OK","resultDescription": "","durationMs": "78","callerIpAddress": "104.40.82.76","correlationId": "","identity": {"claim": {"http://schemas.microsoft.com/identity/claims/objectidentifier": "d9da5048-2737-4770-bd64-XXXXXXXXXXXX","http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "live.com#username@outlook.com","appid": "1950a258-227b-4e31-a9cf-XXXXXXXXXXXX"}},"properties": {"clientInfo": "azure-resource-manager/2.0","requestUri": "https://control-prod-wus.vaultcore.azure.net/subscriptions/361da5d4-a47a-4c79-afdd-XXXXXXXXXXXX/resourcegroups/contosoresourcegroup/providers/Microsoft.KeyVault/vaults/contosokeyvault?api-version=2015-06-01","id": "https://contosokeyvault.vault.azure.net/","httpStatusCode": 200}}
{"time": "2016-01-05T01:33:56.5264523Z","resourceId": "/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSOGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT","operationName": "VaultGet","operationVersion": "2015-06-01","category": "AuditEvent","resultType": "Success","resultSignature": "OK","resultDescription": "","durationMs": "83","callerIpAddress": "104.40.82.76","correlationId": "","identity": {"claim": {"http://schemas.microsoft.com/identity/claims/objectidentifier": "d9da5048-2737-4770-bd64-XXXXXXXXXXXX","http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "live.com#username@outlook.com","appid": "1950a258-227b-4e31-a9cf-XXXXXXXXXXXX"}},"properties": {"clientInfo": "azure-resource-manager/2.0","requestUri": "https://control-prod-wus.vaultcore.azure.net/subscriptions/361da5d4-a47a-4c79-afdd-XXXXXXXXXXXX/resourcegroups/contosoresourcegroup/providers/Microsoft.KeyVault/vaults/contosokeyvault?api-version=2015-06-01","id": "https://contosokeyvault.vault.azure.net/","httpStatusCode": 200}}
```

Este novo formato permite ao Azure Monitor empurrar ficheiros de registo utilizando bolhas de [apêndice](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-append-blobs), que são mais eficientes para a utilização contínua de novos dados de eventos.

## <a name="how-to-update"></a>Como atualizar

Só precisa de fazer atualizações se tiver ferramentas personalizadas que ingerirestes ficheiros de registo para posterior processamento. Se estiver a utilizar uma ferramenta externa de análise de registo ou SIEM, recomendamos a utilização de centros de [eventos para ingerir estes dados](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/). A integração de centros de eventos é mais fácil em termos de processamento de registos de muitos serviços e localização de marcação num determinado registo.

As ferramentas personalizadas devem ser atualizadas para lidar tanto com o formato atual como com o formato JSON Lines acima descrito. Isto garantirá que quando os dados começarem a aparecer no novo formato, as suas ferramentas não se partem.

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre o arquivamento de [registos](./../../azure-monitor/platform/archive-diagnostic-logs.md) de recursos para uma conta de armazenamento
* Conheça os dados de registo de [atividades](./../../azure-monitor/platform/archive-activity-log.md) de arquivamento numa conta de armazenamento

