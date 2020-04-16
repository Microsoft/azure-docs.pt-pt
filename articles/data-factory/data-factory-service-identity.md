---
title: Identidade gerida do Data Factory
description: Saiba mais sobre a identidade gerida para a Azure Data Factory.
services: data-factory
author: linda33wj
manager: shwang
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/16/2020
ms.author: jingwang
ms.openlocfilehash: d47450f3252074d3bae8df97766bf8858fca5972
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416585"
---
# <a name="managed-identity-for-data-factory"></a>Identidade gerida do Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Este artigo ajuda-o a compreender o que é gerido identidade para data factory (anteriormente conhecido como Identidade de Serviço Gerido/MSI) e como funciona.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>Descrição geral

Ao criar uma fábrica de dados, uma identidade gerida pode ser criada juntamente com a criação de fábrica. A identidade gerida é uma aplicação gerida registada no Azure Ative Directory, e representa esta fábrica de dados específica.

A identidade gerida para data factory beneficia as seguintes funcionalidades:

- [Armazenar credencial em Azure Key Vault,](store-credentials-in-key-vault.md)caso em que a identidade gerida pela fábrica de dados é usada para autenticação Azure Key Vault.
- Conectores incluindo [armazenamento Azure Blob,](connector-azure-blob-storage.md) [Azure Data Lake Storage Gen1,](connector-azure-data-lake-store.md) [Azure Data Lake Storage Gen2,](connector-azure-data-lake-storage.md) [Azure SQL Database](connector-azure-sql-database.md)e [Azure SQL Data Warehouse](connector-azure-sql-data-warehouse.md).
- [Atividade web.](control-flow-web-activity.md)

## <a name="generate-managed-identity"></a>Gerar identidade gerida

A identidade gerida para data factory é gerada da seguinte forma:

- Ao criar uma fábrica de dados através do **portal Azure ou powerShell,** a identidade gerida será sempre criada automaticamente.
- Ao criar uma fábrica de dados através **do SDK,** a identidade gerida só será criada se especificar "Identidade = nova Identidade de Fábrica". Consulte o exemplo em [.NET quickstart - crie fábrica de dados](quickstart-create-data-factory-dot-net.md#create-a-data-factory).
- Ao criar uma fábrica de dados através **da REST API,** a identidade gerida só será criada se especificar a secção "identidade" no organismo de pedido. Consulte o exemplo no [REST quickstart - crie fábrica de dados](quickstart-create-data-factory-rest-api.md#create-a-data-factory).

Se encontrar a sua fábrica de dados não tem uma identidade gerida associada após [a recuperação da](#retrieve-managed-identity) instrução de identidade gerida, pode explicitamente gerar uma atualizando a fábrica de dados com o iniciador de identidade programáticamente:

- [Gerar identidade gerida usando powerShell](#generate-managed-identity-using-powershell)
- [Gerar identidade gerida usando a API REST](#generate-managed-identity-using-rest-api)
- [Gerar identidade gerida usando um modelo de Gestor de Recursos Azure](#generate-managed-identity-using-an-azure-resource-manager-template)
- [Gerar identidade gerida usando SDK](#generate-managed-identity-using-sdk)

>[!NOTE]
>- A identidade gerida não pode ser modificada. Atualizar uma fábrica de dados que já tem uma identidade gerida não terá qualquer impacto, a identidade gerida mantém-se inalterada.
>- Se atualizar uma fábrica de dados que já tenha uma identidade gerida sem especificar o parâmetro de "identidade" no objeto de fábrica ou sem especificar a secção de "identidade" no organismo de pedido rest, terá um erro.
>- Ao eliminar uma fábrica de dados, a identidade gerida associada será eliminada.

### <a name="generate-managed-identity-using-powershell"></a>Gerar identidade gerida usando powerShell

Ligue novamente para o comando **Set-AzDataFactoryV2** e verá os campos de "Identidade" a serem recentemente gerados:

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

### <a name="generate-managed-identity-using-rest-api"></a>Gerar identidade gerida usando a API REST

Ligue abaixo da API com secção "identidade" no organismo de pedido:

```
PATCH https://management.azure.com/subscriptions/<subsID>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/<data factory name>?api-version=2018-06-01
```

**Órgão de pedido**: adicione "identidade": { "type": "SystemAssigned" }.

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

**Resposta**: a identidade gerida é criada automaticamente e a secção "identidade" é povoada em conformidade.

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

### <a name="generate-managed-identity-using-an-azure-resource-manager-template"></a>Gerar identidade gerida usando um modelo de Gestor de Recursos Azure

**Modelo**: adicionar "identidade": { "type": "SystemAssigned" }.

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

Ligue para a fábrica de dados create_or_update função com Identidade=nova Identidade de Fábrica(). Código da amostra utilizando .NET:

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
> Se não vir a identidade gerida, [gere identidade gerida](#generate-managed-identity) atualizando a sua fábrica.

### <a name="retrieve-managed-identity-using-azure-portal"></a>Recuperar identidade gerida usando portal Azure

Pode encontrar as informações de identidade geridas do portal Azure - > a sua fábrica de dados -> Propriedades.

- ID de objeto de identidade gerido
- Inquilino de Identidade Gerido
- ID de aplicação de identidade gerida

As informações de identidade geridas também aparecerão quando criar um serviço ligado que suporta a autenticação de identidade gerida, como O Azure Blob, Armazenamento de Lago de Dados Azure, Cofre chave Azure, etc.

Ao conceder permissão, utilize o id do objeto ou o nome da fábrica de dados (como nome de identidade gerido) para encontrar esta identidade.

### <a name="retrieve-managed-identity-using-powershell"></a>Recuperar identidade gerida usando powerShell

O ID principal de identidade gerido e id do inquilino será devolvido quando você obter uma fábrica de dados específica da seguinte forma. Utilize o **Principado id** para conceder acesso:

```powershell
PS C:\WINDOWS\system32> (Get-AzDataFactoryV2 -ResourceGroupName <resourceGroupName> -Name <dataFactoryName>).Identity

PrincipalId                          TenantId
-----------                          --------
765ad4ab-XXXX-XXXX-XXXX-51ed985819dc 72f988bf-XXXX-XXXX-XXXX-2d7cd011db47
```

Pode obter o ID da aplicação copiando acima do ID principal, em seguida, executando abaixo o comando do Diretório Ativo Azure com o ID principal como parâmetro.

```powershell
PS C:\WINDOWS\system32> Get-AzADServicePrincipal -ObjectId 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc

ServicePrincipalNames : {76f668b3-XXXX-XXXX-XXXX-1b3348c75e02, https://identity.azure.net/P86P8g6nt1QxfPJx22om8MOooMf/Ag0Qf/nnREppHkU=}
ApplicationId         : 76f668b3-XXXX-XXXX-XXXX-1b3348c75e02
DisplayName           : ADFV2DemoFactory
Id                    : 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc
Type                  : ServicePrincipal
```

## <a name="next-steps"></a>Passos seguintes
Consulte os seguintes tópicos que introduzem quando e como utilizar a identidade gerida pela fábrica de dados:

- [Credencial de loja em Cofre chave Azure](store-credentials-in-key-vault.md)
- [Copiar dados de/para a Azure Data Lake Store utilizando identidades geridas para autenticação de recursos Azure](connector-azure-data-lake-store.md)

Consulte [identidades geridas para](/azure/active-directory/managed-identities-azure-resources/overview) a visão geral dos recursos azure para obter mais conhecimento sobre identidades geridas para recursos Azure, em que a identidade gerida pela fábrica de dados se baseia. 
