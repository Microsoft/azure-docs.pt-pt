---
title: SDK de .NET API e recursos de tabelas do Azure Cosmos DB | Documentos da Microsoft
description: Saiba tudo sobre a API tabela do Azure Cosmos DB incluindo as datas de lançamento, datas de extinção e as alterações feitas entre cada versão.
services: cosmos-db
author: rnagpal
manager: kfile
ms.service: cosmos-db
ms.component: cosmosdb-table
ms.devlang: dotnet
ms.topic: reference
ms.date: 08/17/2018
ms.author: rnagpal
ms.openlocfilehash: d0bd7dba5d50445cb681c16d9575b1bd69167e2f
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/20/2018
ms.locfileid: "42056308"
---
# <a name="azure-cosmos-db-table-net-api-download-and-release-notes"></a>API do Azure Cosmos DB tabela .NET: Transferir e notas de versão
> [!div class="op_single_selector"]
> * [.NET](table-sdk-dotnet.md)
> * [Java](table-sdk-java.md)
> * [Node.js](table-sdk-nodejs.md)
> * [Python](table-sdk-python.md)

|   |   |
|---|---|
|**Transferência de SDK**|[NuGet](https://aka.ms/acdbtablenuget)|
|**Documentação da API**|[Documentação de referência da .NET API](https://aka.ms/acdbtableapiref)|
|**Início rápido**|[Azure Cosmos DB: Criar uma aplicação com o .NET e a API de tabelas](create-table-dotnet.md)|
|**Tutorial**|[Azure Cosmos DB: Desenvolver com a API de tabela no .NET](tutorial-develop-table-dotnet.md)|
|**Estrutura de suporte atual**|[Microsoft .NET Framework 4.5.1](https://www.microsoft.com/en-us/download/details.aspx?id=40779)|

> [!IMPORTANT]
> Se tiver criado uma conta de API de Tabela durante a pré-visualização, crie uma [nova conta de API de Tabela](create-table-dotnet.md#create-a-database-account) para trabalhar com os SDKs de API de Tabela disponíveis geralmente.
>

## <a name="release-notes"></a>Notas de versão

### <a name="a-name113113"></a><a name="1.1.3"/>1.1.3
* Dependências do pacote de NuGet fixas no Microsoft.Azure.Storage.Common e Microsoft.Azure.DocumentDB.
* Correções de erros na serialização de tabela quando JsonConvert.DefaultSettings são configurados.

### <a name="a-name111111"></a><a name="1.1.1"/>1.1.1
* Acréscimo da validação para um formato incorreto ETAGs no modo direto.
* Foi corrigido o erro do consulta LINQ no modo de Gateway.
* Agora executam APIs síncronas no pool de threads com o SynchronizationContext.

### <a name="a-name110110"></a><a name="1.1.0"/>1.1.0
* Adicionar TableQueryMaxItemCount, TableQueryEnableScan, TableQueryMaxDegreeOfParallelism e TableQueryContinuationTokenLimitInKb a TableRequestOptions
* Correções de erros

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0
* Versão de disponibilidade geral

### <a name="a-name010-preview090-preview"></a><a name="0.1.0-preview"/>0.9.0-Preview
* Versão de pré-visualização inicial

## <a name="release-and-retirement-dates"></a>Datas de lançamento e de extinção
A Microsoft fornece notificação, pelo menos, **12 meses** antecedência extinguir um SDK para facilitar a transição para uma versão mais recente/suportadas.

O [windowsazure. Storage-PremiumTable](https://www.nuget.org/packages/WindowsAzure.Storage-PremiumTable/0.1.0-preview) foi preterido e substituído por pacote de pré-visualização do [cosmosdb](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table) pacote. O SDK de windowsazure. Storage-PremiumTable será retirado a 15 de Novembro de 2018, na qual tempo pedidos para o SDK extinto não será permitido. O `Microsoft.Azure.CosmosDB.Table` biblioteca está atualmente disponível para o .NET Standard apenas, ainda não está disponível para .NET Core.

Novos recursos e funcionalidade e otimizações, apenas são adicionadas ao SDK atual, como tal, é recomendável que sempre atualiza para a versão mais recente SDK mais cedo possível. 

Todos os pedidos para o Azure Cosmos DB com um SDK extinto são rejeitados pelo serviço.
<br/>

| Versão | Data de lançamento | Data de retirada |
| --- | --- | --- |
| [1.1.3](#1.1.3) |17 de Julho de 2018|--- |
| [1.1.1](#1.1.1) |26 de Março de 2018|--- |
| [1.1.0](#1.1.0) |21 de fevereiro de 2018|--- |
| [1.0.0](#1.0.0) |15 de Novembro de 2017|--- |
| [0.9.0-preview](#0.9.0-preview) |11 de Novembro de 2017 |--- |

## <a name="troubleshooting"></a>Resolução de problemas

Se obtiver o erro 

```
Unable to resolve dependency 'Microsoft.Azure.Storage.Common'. Source(s) used: 'nuget.org', 
'CliFallbackFolder', 'Microsoft Visual Studio Offline Packages', 'Microsoft Azure Service Fabric SDK'`
```

ao tentar utilizar o pacote NuGet do cosmosdb, tem duas opções para corrigir o problema:

* Utilize a consola de gerir pacotes para instalar o pacote do cosmosdb e as respetivas dependências. Para tal, escreva o seguinte na consola do Gestor de pacotes para a sua solução. 
    ```
    Install-Package Microsoft.Azure.CosmosDB.Table -IncludePrerelease
    ```
    
* Utilizar a sua ferramenta de gestão de pacotes de NuGet preferencial, instale o pacote de Microsoft.Azure.Storage.Common NuGet antes de instalar o cosmosdb.

## <a name="faq"></a>FAQ

[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Consulte também
Para saber mais sobre a API de tabela do Azure Cosmos DB, veja [introdução à API de tabela do Azure Cosmos DB](table-introduction.md). 
