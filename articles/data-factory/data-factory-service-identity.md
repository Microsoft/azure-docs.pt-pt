---
title: Identidade gerida do Data Factory
description: Conheça a identidade gerida para a Azure Data Factory.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 07/06/2020
ms.author: jingwang
ms.openlocfilehash: e0d3b551265a480a700f374ddfcf89dd4d93333f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100389166"
---
# <a name="managed-identity-for-data-factory"></a>Identidade gerida do Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Este artigo ajuda-o a compreender o que é a identidade gerida para a Data Factory (anteriormente conhecida como Identidade de Serviço Gerido/MSI) e como funciona.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>Descrição geral

Ao criar uma fábrica de dados, pode ser criada uma identidade gerida juntamente com a criação de fábrica. A identidade gerida é uma aplicação gerida registada no Azure Ative Directory, e representa esta fábrica de dados específica.

A identidade gerida para data factory beneficia as seguintes características:

- [Armazenar credencial em Azure Key Vault,](store-credentials-in-key-vault.md)caso em que a identidade gerida pela fábrica de dados é usada para a autenticação do Cofre da Chave Azure.
- Conectores incluindo [armazenamento Azure Blob](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure SQL Database](connector-azure-sql-database.md), e [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md).
- [Atividade web](control-flow-web-activity.md).

## <a name="generate-managed-identity"></a>Gerar identidade gerida

A identidade gerida para a Data Factory é gerada da seguinte forma:

- Ao criar a fábrica de dados através do **portal Azure ou powerShell,** a identidade gerida será sempre criada automaticamente.
- Ao criar uma fábrica de dados através **de SDK,** a identidade gerida só será criada se especificar "Identidade = nova FactoryIdentity",)" no objeto de fábrica para criação. Veja o exemplo em [.NET quickstart - crie fábrica de dados](quickstart-create-data-factory-dot-net.md#create-a-data-factory).
- Ao criar a fábrica de dados através **da REST API,** a identidade gerida só será criada se especificar a secção "identidade" no organismo de pedido. Veja o exemplo no [arranque rápido REST - crie fábrica de dados](quickstart-create-data-factory-rest-api.md#create-a-data-factory).

Se encontrar que a sua fábrica de dados não tem uma identidade gerida associada após [a recuperação](#retrieve-managed-identity) de instruções de identidade geridas, pode gerar explicitamente uma, atualizando a fábrica de dados com o iniciador de identidade programáticamente:

- [Gerar identidade gerida usando PowerShell](#generate-managed-identity-using-powershell)
- [Gerar identidade gerida usando REST API](#generate-managed-identity-using-rest-api)
- [Gere uma identidade gerida utilizando um modelo de Gestor de Recursos Azure](#generate-managed-identity-using-an-azure-resource-manager-template)
- [Gerar identidade gerida usando SDK](#generate-managed-identity-using-sdk)

>[!NOTE]
>- A identidade gerida não pode ser modificada. Atualizar uma fábrica de dados que já tenha uma identidade gerida não terá qualquer impacto, a identidade gerida é mantida inalterada.
>- Se atualizar uma fábrica de dados que já tenha uma identidade gerida sem especificar o parâmetro de "identidade" no objeto de fábrica ou sem especificar a secção "identidade" no corpo de pedidos REST, terá um erro.
>- Quando eliminar uma fábrica de dados, a identidade gerida associada será eliminada.

### <a name="generate-managed-identity-using-powershell"></a>Gerar identidade gerida usando PowerShell

Ligue para o comando **Set-AzDataFactoryV2** e, em seguida, vê os campos "Identidade" a serem recentemente gerados:

```powershell
PS C:\WINDOWS\system32> Set-AzDataFactoryV2 -ResourceGroupName <resourceGroupName> -Name <dataFactoryName> -Location <region>

DataFactoryName   : ADFV2DemoFactory
DataFactoryId     : /subscriptions/<subsID>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/ADFV2DemoFactory
ResourceGroupName : <resourceGroupName>
Location          : East US
Tags              : {}
Identity          : Microsoft.Azure.Management.DataFactory.Models.FactoryIdentity
ProvisioningState : Succeeded
```

### <a name="generate-managed-identity-using-rest-api"></a>Gerar identidade gerida usando REST API

Ligue abaixo da API com secção "identidade" no organismo de pedido:

```
PATCH https://management.azure.com/subscriptions/<subsID>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/<data factory name>?api-version=2018-06-01
```

**Órgão de pedido**: adicione "identidade": { "tipo": "SystemAssigned" }.

```json
{
    "name": "<dataFactoryName>",
    "location": "<region>",
    "properties": {},
    "identity": {
        "type": "SystemAssigned"
    }
}
```

**Resposta:** a identidade gerida é criada automaticamente e a secção "identidade" é povoada em conformidade.

```json
{
    "name": "<dataFactoryName>",
    "tags": {},
    "properties": {
        "provisioningState": "Succeeded",
        "loggingStorageAccountKey": "**********",
        "createTime": "2017-09-26T04:10:01.1135678Z",
        "version": "2018-06-01"
    },
    "identity": {
        "type": "SystemAssigned",
        "principalId": "765ad4ab-XXXX-XXXX-XXXX-51ed985819dc",
        "tenantId": "72f988bf-XXXX-XXXX-XXXX-2d7cd011db47"
    },
    "id": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/ADFV2DemoFactory",
    "type": "Microsoft.DataFactory/factories",
    "location": "<region>"
}
```

### <a name="generate-managed-identity-using-an-azure-resource-manager-template"></a>Gere uma identidade gerida utilizando um modelo de Gestor de Recursos Azure

**Modelo:** adicionar "identidade": { "tipo": "SystemAssigned" }.

```json
{
    "contentVersion": "1.0.0.0",
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "resources": [{
        "name": "<dataFactoryName>",
        "apiVersion": "2018-06-01",
        "type": "Microsoft.DataFactory/factories",
        "location": "<region>",
        "identity": {
            "type": "SystemAssigned"
        }
    }]
}
```

### <a name="generate-managed-identity-using-sdk"></a>Gerar identidade gerida usando SDK

Ligue para a fábrica de dados create_or_update função com Identidade=nova FactoryIdentity(). Código de amostra utilizando .NET:

```csharp
Factory dataFactory = new Factory
{
    Location = <region>,
    Identity = new FactoryIdentity()
};
client.Factories.CreateOrUpdate(resourceGroup, dataFactoryName, dataFactory);
```

## <a name="retrieve-managed-identity"></a>Recuperar identidade gerida

Pode recuperar a identidade gerida do portal Azure ou programáticamente. As seguintes secções mostram algumas amostras.

>[!TIP]
> Se não vir a identidade gerida, [gere a identidade gerida](#generate-managed-identity) atualizando a sua fábrica.

### <a name="retrieve-managed-identity-using-azure-portal"></a>Recupere a identidade gerida usando o portal Azure

Pode encontrar as informações de identidade geridas a partir do portal Azure -> sua fábrica de dados -> Properties.

- ID de objeto de identidade gerido
- Inquilino de Identidade Gerida
- ID de aplicação de identidade gerida

As informações de identidade geridas também aparecerão quando criar um serviço ligado, que suporta a autenticação de identidade gerida, como Azure Blob, Azure Data Lake Storage, Azure Key Vault, etc.

Ao conceder permissão, utilize o ID do objeto ou o nome da fábrica de dados (como nome de identidade gerido) para encontrar esta identidade.

### <a name="retrieve-managed-identity-using-powershell"></a>Recupere a identidade gerida usando o PowerShell

A identificação do id principal de identidade gerida e o ID do inquilino serão devolvidos quando você receber uma fábrica de dados específica da seguinte forma. Utilize o **PrincipalId** para conceder acesso:

```powershell
PS C:\WINDOWS\system32> (Get-AzDataFactoryV2 -ResourceGroupName <resourceGroupName> -Name <dataFactoryName>).Identity

PrincipalId                          TenantId
-----------                          --------
765ad4ab-XXXX-XXXX-XXXX-51ed985819dc 72f988bf-XXXX-XXXX-XXXX-2d7cd011db47
```

Pode obter o ID da aplicação copiando acima do ID principal, e depois correndo abaixo do comando do Diretório Ativo Azure com o ID principal como parâmetro.

```powershell
PS C:\WINDOWS\system32> Get-AzADServicePrincipal -ObjectId 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc

ServicePrincipalNames : {76f668b3-XXXX-XXXX-XXXX-1b3348c75e02, https://identity.azure.net/P86P8g6nt1QxfPJx22om8MOooMf/Ag0Qf/nnREppHkU=}
ApplicationId         : 76f668b3-XXXX-XXXX-XXXX-1b3348c75e02
DisplayName           : ADFV2DemoFactory
Id                    : 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc
Type                  : ServicePrincipal
```

### <a name="retrieve-managed-identity-using-rest-api"></a>Recupere a identidade gerida usando a REST API

A identificação do id principal de identidade gerida e o ID do inquilino serão devolvidos quando você receber uma fábrica de dados específica da seguinte forma.

Ligue abaixo da API no pedido:

```
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataFactory/factories/{factoryName}?api-version=2018-06-01
```

**Resposta**: Obterá resposta como mostrada no exemplo abaixo. A secção "identidade" é povoada em conformidade.

```json
{
    "name":"<dataFactoryName>",
    "identity":{
        "type":"SystemAssigned",
        "principalId":"554cff9e-XXXX-XXXX-XXXX-90c7d9ff2ead",
        "tenantId":"72f988bf-XXXX-XXXX-XXXX-2d7cd011db47"
    },
    "id":"/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/<dataFactoryName>",
    "type":"Microsoft.DataFactory/factories",
    "properties":{
        "provisioningState":"Succeeded",
        "createTime":"2020-02-12T02:22:50.2384387Z",
        "version":"2018-06-01",
        "factoryStatistics":{
            "totalResourceCount":0,
            "maxAllowedResourceCount":0,
            "factorySizeInGbUnits":0,
            "maxAllowedFactorySizeInGbUnits":0
        }
    },
    "eTag":"\"03006b40-XXXX-XXXX-XXXX-5e43617a0000\"",
    "location":"<region>",
    "tags":{

    }
}
```

> [!TIP] 
> Para recuperar a identidade gerida a partir de um modelo ARM, adicione uma secção **de saídas** no ARM JSON:

```json
{
    "outputs":{
        "managedIdentityObjectId":{
            "type":"string",
            "value":"[reference(resourceId('Microsoft.DataFactory/factories', parameters('<dataFactoryName>')), '2018-06-01', 'Full').identity.principalId]"
        }
    }
}
```

## <a name="next-steps"></a>Passos seguintes
Veja os seguintes tópicos que introduzem quando e como utilizar a identidade gerida pela fábrica de dados:

- [Credencial de loja em Azure Key Vault](store-credentials-in-key-vault.md)
- [Copiar dados de/para Azure Data Lake Store utilizando identidades geridas para autenticação de recursos Azure](connector-azure-data-lake-store.md)

Consulte [identidades geridas para a Visão Geral dos Recursos Azure](../active-directory/managed-identities-azure-resources/overview.md) para obter mais informações sobre identidades geridas para recursos Azure, em que a identidade gerida pela fábrica de dados é baseada.