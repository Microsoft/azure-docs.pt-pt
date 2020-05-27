---
title: Contas de armazenamento do cliente para ingestão de loging
description: Utilize a sua própria conta de armazenamento para a ingestão de dados de registo num espaço de trabalho de Log Analytics no Monitor Azure.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/20/2020
ms.openlocfilehash: 7213cb10936fc1c2117b2c5c3fc32a6bfea02d30
ms.sourcegitcommit: fc0431755effdc4da9a716f908298e34530b1238
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/24/2020
ms.locfileid: "83816781"
---
# <a name="customer-owned-storage-accounts-for-log-ingestion-in-azure-monitor"></a>Contas de armazenamento de propriedade do cliente para ingestão de log ingestão no Monitor Azure

O Azure Monitor utiliza contas de armazenamento no processo de ingestão de alguns tipos de dados, tais como [registos personalizados](data-sources-custom-logs.md) e [alguns registos Do Azure.](azure-storage-iis-table.md) Durante o processo de ingestão, os registos são enviados pela primeira vez para uma conta de armazenamento e posteriormente ingeridos em Log Analytics ou Application Insights. Se pretender controlar os seus dados durante a ingestão, pode utilizar as suas próprias contas de armazenamento em vez do armazenamento gerido pelo serviço. A utilização da sua própria conta de armazenamento dá-lhe controlo sobre o acesso, conteúdo, encriptação e retenção dos registos durante a ingestão. Referimo-nos a isto como Bring Your Own Storage, ou BYOS. 

Um dos cenários que requer o isolamento da rede através de Links Privados. Quando se utiliza um VNet, o isolamento da rede é muitas vezes um requisito, e o acesso à internet pública é limitado. Nesses casos, o acesso ao armazenamento de serviço sintetizado do Azure Monitor para ingestão de logs está completamente bloqueado ou é considerado uma má prática. Em vez disso, os Registos devem ser ingeridos através de uma conta de armazenamento propriedade do cliente dentro do VNet ou facilmente acessíveis a partir dele.

Outro cenário é a encriptação de registos com chaves geridas pelo cliente (CMK). Os clientes podem encriptar dados registados utilizando cmk nos clusters que armazenam os registos. A mesma tecla também pode ser usada para encriptar registos durante o processo de ingestão.

## <a name="data-types-supported"></a>Tipos de dados suportados

Os tipos de dados que são ingeridos a partir de uma conta de armazenamento incluem o seguinte. Consulte [a recolha de dados da extensão de diagnóstico do Azure aos Registos do Monitor Do Azure](azure-storage-iis-table.md) para obter mais informações sobre a ingestão deste tipo.

| Tipo | Informação de mesa |
|:-----|:------------------|
| Registos do IIS | Blob: wad-iis-logfiles|
|Registos de eventos do Windows | Tabela: WADWindowsEventLogsTable |
| Syslog | Tabela: LinuxsyslogVer2v0 |
| Registos Windows ETW | Tabela: WADETWEventTable|
| Service fabric | Tabela: WADServiceFabricSystemEventTable <br/> WADServiceFabricReliableActorEventTable<br/> WADServiceFabricReliableServicEventTable |
| Registos personalizados | n/a |
| Ficheiros de despejo do Azure Security Center Watson | n/a|  

## <a name="storage-account-requirements"></a>Requisitos da conta de armazenamento 
A conta de armazenamento deve satisfazer os seguintes requisitos:

- Acessível a recursos no seu VNet que escrevem registos para o armazenamento.
- Deve estar na mesma região do espaço de trabalho a que está ligado.
- Autorizou explicitamente o Log Analytics a ler registos da conta de armazenamento selecionando *permitir que serviços de EmS fidedignos acedam a esta conta de armazenamento*.

## <a name="process-to-configure-customer-owned-storage"></a>Processo para configurar armazenamento de propriedade do cliente
O processo básico de utilização da sua própria conta de armazenamento para ingestão é o seguinte:

1. Crie uma conta de armazenamento ou selecione uma conta existente.
2. Ligue a conta de armazenamento a um espaço de trabalho de Log Analytics.
3. Gerencie o armazenamento revendo a sua carga e retenção para garantir que está a funcionar como esperado.

O único método disponível para criar e remover ligações é através da API REST. As informações sobre o pedido específico de API necessário para cada processo são fornecidas nas secções abaixo.

## <a name="api-request-values"></a>Valores de pedido da API

#### <a name="datasourcetype"></a>dataSourceType 

- AzureWatson - Utilize este valor para ficheiros de despejo do Azure Security Center Azure Watson.
- CustomLogs – Utilize este valor para os seguintes tipos de dados:
  - Registos personalizados
  - Registos do IIS
  - Eventos (Janelas)
  - Syslog (Linux)
  - Registos ETW
  - Eventos de Tecido de Serviço
  - Dados de avaliação  

#### <a name="storage_account_resource_id"></a>storage_account_resource_id
Este valor utiliza a seguinte estrutura:

```
subscriptions/{subscriptionId}/resourcesGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName1}
```



## <a name="get-current-links"></a>Obtenha links atuais

### <a name="get-linked-storage-accounts-for-a-specific-data-source-type"></a>Obtenha contas de armazenamento ligadas para um tipo específico de fonte de dados

#### <a name="api-request"></a>Pedido da API

```
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/linkedStorageAccounts/{dataSourceType}?api-version=2019-08-01-preview  
```

#### <a name="response"></a>Resposta 

```json
{
    "properties":
    {
        "dataSourceType": "CustomLogs",
        "storageAccountIds  ": 
        [  
            "<storage_account_resource_id_1>",
            "<storage_account_resource_id_2>"
        ],
    },
    "id":"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/microsoft. operationalinsights/workspaces/{resourceName}/linkedStorageAccounts/CustomLogs",
    "name": "CustomLogs",
    "type": "Microsoft.OperationalInsights/workspaces/linkedStorageAccounts"
}
```

### <a name="get-all-linked-storage-accounts"></a>Obtenha todas as contas de armazenamento ligadas

#### <a name="api-request"></a>Pedido da API

```
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/linkedStorageAccounts?api-version=2019-08-01-preview  
```

#### <a name="response"></a>Resposta

```json
{
    [
        {
            "properties":
            {
                "dataSourceType": "CustomLogs",
                "storageAccountIds  ": 
                [  
                    "<storage_account_resource_id_1>",
                    "<storage_account_resource_id_2>"
                ],
            },
            "id":"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/microsoft. operationalinsights/workspaces/{resourceName}/linkedStorageAccounts/CustomLogs",
            "name": "CustomLogs",
            "type": "Microsoft.OperationalInsights/workspaces/linkedStorageAccounts"
        },
        {
            "properties":
            {
                "dataSourceType": " AzureWatson "
                "storageAccountIds  ": 
                [  
                    "<storage_account_resource_id_3>",
                    "<storage_account_resource_id_4>"
                ],
            },
            "id":"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/microsoft. operationalinsights/workspaces/{resourceName}/linkedStorageAccounts/AzureWatson",
            "name": "AzureWatson",
            "type": "Microsoft.OperationalInsights/workspaces/linkedStorageAccounts"
        }
    ]
}
```

## <a name="create-or-modify-a-link"></a>Criar ou modificar uma ligação

Assim que ligar uma conta de armazenamento a um espaço de trabalho, o Log Analytics começará a usá-la em vez da conta de armazenamento detida pelo serviço. Pode registar uma lista de contas de armazenamento ao mesmo tempo, podendo utilizar a mesma conta de armazenamento para vários espaços de trabalho.

Se o seu espaço de trabalho lidar tanto com recursos VNet como recursos fora de um VNet, deve certificar-se de que não está a rejeitar o tráfego proveniente da internet. O seu armazenamento deve ter as mesmas configurações que o seu espaço de trabalho e ser disponibilizado a recursos fora do seu VNet. 

### <a name="api-request"></a>Pedido da API

```
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/linkedStorageAccounts/{dataSourceType}?api-version=2019-08-01-preview  
```

### <a name="payload"></a>Carga útil

```json
{
    "properties":
    {
        "storageAccountIds  " : 
        [  
            "<storage_account_resource_id_1>",
            "<storage_account_resource_id_2>"
        ],
    }
}
```

### <a name="response"></a>Resposta

```json
{
    "properties":
    {
        "dataSourceType": "CustomLogs"
        "storageAccountIds  ": 
        [  
            "<storage_account_resource_id_1>",
            "<storage_account_resource_id_2>"
        ],
    },
"id":"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/microsoft. operationalinsights/workspaces/{resourceName}/linkedStorageAccounts/CustomLogs",
"name": "CustomLogs",
"type": "Microsoft.OperationalInsights/workspaces/linkedStorageAccounts"
}
```


## <a name="unlink-a-storage-account"></a>Desvincular uma conta de armazenamento
Se decidir parar de utilizar uma conta de armazenamento para ingestão ou substituir o espaço de trabalho que utiliza, deverá desligar o armazenamento do espaço de trabalho.

Desvincular todas as contas de armazenamento de um espaço de trabalho significa que a ingestão tentará confiar em contas de armazenamento geridas pelo serviço. Se os seus agentes correrem num VNet com acesso limitado à internet, espera-se que a ingestão falhe. O espaço de trabalho deve ter uma conta de armazenamento ligada que seja acessível a partir dos seus recursos monitorizados.

Antes de apagar uma conta de armazenamento, deve certificar-se de que todos os dados que contém foram ingeridos para o espaço de trabalho. Por precaução, mantenha a sua conta de armazenamento disponível depois de ligar um armazenamento alternativo. Apenas apague-o uma vez que todo o seu conteúdo tenha sido ingerido, e pode ver que novos dados são escritos na conta de armazenamento recentemente conectada.


### <a name="api-request"></a>Pedido da API
```
DELETE https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/linkedStorageAccounts/{dataSourceType}?api-version=2019-08-01-preview  
```

## <a name="replace-a-storage-account"></a>Substituir uma conta de armazenamento

Para substituir uma conta de armazenamento utilizada para ingestão, crie primeiro um link para uma nova conta de armazenamento. Os agentes de registo receberão a configuração atualizada e começarão a enviar dados para o novo armazenamento também.

Em seguida, desligue a antiga conta de armazenamento para que os agentes deixem de escrever para a conta removida. O processo de ingestão continua a ler dados desta conta até que tudo seja ingerido. Não apague a conta de armazenamento até ver que todos os registos foram ingeridos.

A configuração do agente será atualizada após alguns minutos, e mudarão para o novo armazenamento.

## <a name="manage-storage-account"></a>Gerir a conta de armazenamento

### <a name="load"></a>Carregar

As contas de armazenamento podem lidar com uma certa carga de leitura e escrever pedidos antes de começarem a estrangular pedidos. O estrangulamento afeta o tempo que leva para ingerir registos e pode resultar em dados perdidos. Se o seu armazenamento estiver sobrecarregado, registe contas de armazenamento adicionais e espalhe a carga entre elas. 

### <a name="related-charges"></a>Encargos relacionados

As contas de armazenamento são cobradas pelo volume de dados armazenados, tipos de armazenamento e tipo de despedimento. Para mais detalhes consulte preços de [bloco blob](https://azure.microsoft.com/pricing/details/storage/blobs/) e [preços de armazenamento de mesa](https://azure.microsoft.com/pricing/details/storage/tables/).

Se a conta de armazenamento registada do seu espaço de trabalho estiver noutra região, será cobrado por saída de acordo com estes Detalhes de Preços da [Largura de Banda](https://azure.microsoft.com/pricing/details/bandwidth/).



## <a name="next-steps"></a>Passos seguintes

- Para obter mais informações sobre a criação de um link privado, consulte [Use Azure Private Link para ligar as redes de forma segura ao Azure Monitor}(ligação privada-security.md)
