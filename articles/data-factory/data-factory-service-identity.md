---
title: Identidade gerida do Data Factory
description: Saiba mais sobre identidade gerenciada para Azure Data Factory.
services: data-factory
author: linda33wj
manager: shwang
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: jingwang
ms.openlocfilehash: ede1e56384c75c64765962b7db196973a30c605b
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74928453"
---
# <a name="managed-identity-for-data-factory"></a>Identidade gerida do Data Factory

Este artigo ajuda você a entender o que é a identidade gerenciada para Data Factory (anteriormente conhecido como Identidade de Serviço Gerenciada/MSI) e como ela funciona.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>Visão geral

Ao criar um data factory, uma identidade gerenciada pode ser criada junto com a criação de fábrica. A identidade gerenciada é um aplicativo gerenciado registrado para Azure Active Directory e representa esse data factory específico.

A identidade gerenciada para Data Factory beneficia os seguintes recursos:

- [Armazene a credencial em Azure Key Vault](store-credentials-in-key-vault.md), nesse caso data Factory identidade gerenciada é usada para Azure Key Vault autenticação.
- Conectores, incluindo o [armazenamento de BLOBs do Azure](connector-azure-blob-storage.md), [Azure data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure data Lake Storage Gen2](connector-azure-data-lake-storage.md), [banco de dados SQL do Azure](connector-azure-sql-database.md)e [Azure SQL data warehouse](connector-azure-sql-data-warehouse.md).
- [Atividade da Web](control-flow-web-activity.md).

## <a name="generate-managed-identity"></a>Gerar identidade gerenciada

A identidade gerenciada para Data Factory é gerada da seguinte maneira:

- Ao criar data factory por meio do **portal do Azure ou do PowerShell**, a identidade gerenciada será sempre criada automaticamente.
- Ao criar data factory por meio do **SDK**, a identidade gerenciada será criada somente se você especificar "Identity = New FactoryIdentity ()" no objeto de fábrica para criação. Consulte o exemplo em [início rápido do .NET – criar data Factory](quickstart-create-data-factory-dot-net.md#create-a-data-factory).
- Ao criar data factory por meio da **API REST**, a identidade gerenciada será criada somente se você especificar a seção "identidade" no corpo da solicitação. Consulte o exemplo em [REST início rápido-criar data Factory](quickstart-create-data-factory-rest-api.md#create-a-data-factory).

Se você achar que o data factory não tem uma identidade gerenciada associada após a instrução [recuperar identidade gerenciada](#retrieve-managed-identity) , você pode gerar explicitamente uma atualizando a data Factory com o iniciador de identidade programaticamente:

- [Gerar identidade gerenciada usando o PowerShell](#generate-managed-identity-using-powershell)
- [Gerar identidade gerenciada usando a API REST](#generate-managed-identity-using-rest-api)
- [Gerar identidade gerenciada usando um modelo de Azure Resource Manager](#generate-managed-identity-using-an-azure-resource-manager-template)
- [Gerar identidade gerenciada usando o SDK](#generate-managed-identity-using-sdk)

>[!NOTE]
>- A identidade gerenciada não pode ser modificada. A atualização de um data factory que já tem uma identidade gerenciada não terá nenhum impacto, a identidade gerenciada será mantida inalterada.
>- Se você atualizar um data factory que já tem uma identidade gerenciada sem especificar o parâmetro "Identity" no objeto de fábrica ou sem especificar a seção "Identity" no corpo da solicitação REST, receberá um erro.
>- Quando você exclui um data factory, a identidade gerenciada associada será excluída junto com.

### <a name="generate-managed-identity-using-powershell"></a>Gerar identidade gerenciada usando o PowerShell

Chame o comando **set-AzDataFactoryV2** novamente, então você verá os campos de "identidade" que estão sendo gerados recentemente:

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

### <a name="generate-managed-identity-using-rest-api"></a>Gerar identidade gerenciada usando a API REST

Chame a API abaixo com a seção "identidade" no corpo da solicitação:

```
PATCH https://management.azure.com/subscriptions/<subsID>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/<data factory name>?api-version=2018-06-01
```

**Corpo da solicitação**: Adicionar "identidade": {"tipo": "SystemAssigned"}.

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

**Resposta**: a identidade gerenciada é criada automaticamente e a seção "identidade" é preenchida de acordo.

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

### <a name="generate-managed-identity-using-an-azure-resource-manager-template"></a>Gerar identidade gerenciada usando um modelo de Azure Resource Manager

**Modelo**: Adicionar "identidade": {"tipo": "SystemAssigned"}.

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

### <a name="generate-managed-identity-using-sdk"></a>Gerar identidade gerenciada usando o SDK

Chame a função data factory create_or_update com Identity = New FactoryIdentity (). Código de exemplo usando o .NET:

```csharp
Factory dataFactory = new Factory
{
    Location = <region>,
    Identity = new FactoryIdentity()
};
client.Factories.CreateOrUpdate(resourceGroup, dataFactoryName, dataFactory);
```

## <a name="retrieve-managed-identity"></a>Recuperar identidade gerenciada

Você pode recuperar a identidade gerenciada de portal do Azure ou de forma programática. As seções a seguir mostram alguns exemplos.

>[!TIP]
> Se você não vir a identidade gerenciada, [gere a identidade gerenciada](#generate-managed-identity) atualizando sua fábrica.

### <a name="retrieve-managed-identity-using-azure-portal"></a>Recuperar identidade gerenciada usando portal do Azure

Você pode encontrar as informações de identidade gerenciada em portal do Azure-> suas propriedades de data factory >:

- ID de objeto de identidade gerenciada
- Locatário de identidade gerenciada
- **ID do aplicativo de identidade gerenciada** > copiar este valor

![Recuperar identidade gerenciada](media/data-factory-service-identity/retrieve-service-identity-portal.png)

### <a name="retrieve-managed-identity-using-powershell"></a>Recuperar identidade gerenciada usando o PowerShell

A ID da entidade de identidade gerenciada e a ID do locatário serão retornadas quando você obter uma data factory específica da seguinte maneira:

```powershell
PS C:\WINDOWS\system32> (Get-AzDataFactoryV2 -ResourceGroupName <resourceGroupName> -Name <dataFactoryName>).Identity

PrincipalId                          TenantId
-----------                          --------
765ad4ab-XXXX-XXXX-XXXX-51ed985819dc 72f988bf-XXXX-XXXX-XXXX-2d7cd011db47
```

Copie a ID da entidade de segurança e execute abaixo Azure Active Directory comando com ID de entidade de segurança como parâmetro para obter a **ApplicationId**, que você usa para conceder acesso:

```powershell
PS C:\WINDOWS\system32> Get-AzADServicePrincipal -ObjectId 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc

ServicePrincipalNames : {76f668b3-XXXX-XXXX-XXXX-1b3348c75e02, https://identity.azure.net/P86P8g6nt1QxfPJx22om8MOooMf/Ag0Qf/nnREppHkU=}
ApplicationId         : 76f668b3-XXXX-XXXX-XXXX-1b3348c75e02
DisplayName           : ADFV2DemoFactory
Id                    : 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc
Type                  : ServicePrincipal
```

## <a name="next-steps"></a>Passos seguintes
Consulte os tópicos a seguir que introduzem quando e como usar data factory identidade gerenciada:

- [Armazenar credencial em Azure Key Vault](store-credentials-in-key-vault.md)
- [Copiar dados de/para Azure Data Lake Store usando identidades gerenciadas para autenticação de recursos do Azure](connector-azure-data-lake-store.md)

Consulte [visão geral de identidades gerenciadas para recursos do Azure](/azure/active-directory/managed-identities-azure-resources/overview) para obter mais informações sobre identidades gerenciadas para recursos do Azure, que data Factory identidade gerenciada baseia-se no. 
