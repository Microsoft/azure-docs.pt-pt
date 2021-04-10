---
title: Azure Cosmos DB Tabela API .NET SDK & Recursos
description: Saiba tudo sobre a AZure Cosmos DB Table API para .NET, incluindo datas de lançamento, datas de aposentadoria e alterações feitas entre cada versão.
author: sakash279
ms.author: akshanka
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: dotnet
ms.topic: reference
ms.date: 08/17/2018
ms.custom: devx-track-dotnet
ms.openlocfilehash: 5af77f34f8e82edf187c1a68b61995ec5c82a8d9
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105566834"
---
# <a name="azure-cosmos-db-table-net-api-download-and-release-notes"></a>Azure Cosmos DB Table .NET API: Baixar e lançar notas
[!INCLUDE[appliesto-table-api](includes/appliesto-table-api.md)]

> [!div class="op_single_selector"]
> * [.NET](table-sdk-dotnet.md)
> * [.NET Standard](table-sdk-dotnet-standard.md)
> * [Java](table-sdk-java.md)
> * [Node.js](table-sdk-nodejs.md)
> * [Python](table-sdk-python.md)

|   | Ligações|
|---|---|
|**Transferência de SDK**|[NuGet](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table)|
|**Início rápido**|[Azure Cosmos DB: Construa uma app com .NET e a Tabela API](create-table-dotnet.md)|
|**Tutorial**|[Azure Cosmos DB: Desenvolver com a API de Tabelas em .NET](tutorial-develop-table-dotnet.md)|
|**Framework suportado atualmente**|[Quadro Microsoft .NET 4.5.1](https://www.microsoft.com/en-us/download/details.aspx?id=40779)|

> [!IMPORTANT]
> O .NET Framework SDK [Microsoft.Azure.CosmosDB.Table](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table) está em modo de manutenção e será deprecado em breve. Por favor, atualize para a nova biblioteca .NET Standard [Microsoft.Azure.Cosmos.Table](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) para continuar a obter as funcionalidades mais recentes suportadas pela Tabela API.

> Se tiver criado uma conta de API de Tabela durante a pré-visualização, crie uma [nova conta de API de Tabela](create-table-dotnet.md#create-a-database-account) para trabalhar com os SDKs de API de Tabela disponíveis geralmente.
>

## <a name="release-notes"></a>Notas de versão

### <a name="212"></a><a name="2.1.2"></a>2.1.2

* Correções de erros

### <a name="210"></a><a name="2.1.0"></a>2.1.0

* Correções de erros

### <a name="200"></a><a name="2.0.0"></a>2.0.0

* Apoio de escrita multi-região adicionado
* As dependências fixas do pacote NuGet em Microsoft.Azure.DocumentDB, Microsoft.OData.Core, Microsoft.OData.Edm, Microsoft.Spatial

### <a name="113"></a><a name="1.1.3"></a>1.1.3

* As dependências fixas do pacote NuGet em Microsoft.Azure.Storage.Common e Microsoft.Azure.DocumentDB.
* Correções de erro na serialização da tabela quando jsonConvert.DefaultSettings são configurados.

### <a name="111"></a><a name="1.1.1"></a>1.1.1

* Validação adicionada para ETAGs mal formados em modo direto.
* Bug de consulta LINQ fixo no modo Gateway.
* ApIs sincronizados agora funcionam na piscina de linha com SynchronizationContext.

### <a name="110"></a><a name="1.1.0"></a>1.1.0

* Adicione TableQueryMaxItemCount, TableQueryEnableScan, TableQueryMaxDegreeOfParallelism e TableQueryContinuationTokenLimitInKb a TableRequestOptions
* Correções de Erros

### <a name="100"></a><a name="1.0.0"></a>1.0.0

* Libertação geral de disponibilidade

### <a name="090-preview"></a><a name="0.1.0-preview"></a>0.9.0-pré-visualização

* Versão de pré-visualização inicial

## <a name="release-and-retirement-dates"></a>Datas de lançamento e aposentadoria

A Microsoft fornece a notificação com pelo menos **12 meses** de antecedência para retirar um SDK de forma a suavizar a transição para uma versão mais recente/suportada.

A `Microsoft.Azure.CosmosDB.Table` biblioteca está atualmente disponível apenas para .NET Framework, e está em modo de manutenção e será depreciada em breve. Novas funcionalidades e funcionalidades e otimizações só são adicionadas à biblioteca .NET Standard [Microsoft.Azure.Cosmos.Table,](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table)como tal é recomendado que faça upgrade para [Microsoft.Azure.Cosmos.Table](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table).

O pacote [de pré-visualização WindowsAzure.Storage-PremiumTable](https://www.nuget.org/packages/WindowsAzure.Storage-PremiumTable/0.1.0-preview) foi depreciado. O WindowsAzure.Storage-PremiumTable SDK será retirado no dia 15 de novembro de 2018, altura em que os pedidos para o SDK reformado não serão permitidos.

| Versão | Data da versão: | Data de Extinção |
| --- | --- | --- |
| [2.1.2](#2.1.2) |16 de setembro de 2019| |
| [2.1.0](#2.1.0) |22 de janeiro de 2019|01 de abril de 2020 |
| [2.0.0](#2.0.0) |26 de setembro de 2018|01 de março de 2020 |
| [1.1.3](#1.1.3) |17 de julho de 2018|01 de dezembro de 2019 |
| [1.1.1](#1.1.1) |26 de março de 2018|01 de dezembro de 2019 |
| [1.1.0](#1.1.0) |21 de fevereiro de 2018|01 de dezembro de 2019 |
| [1.0.0](#1.0.0) |15 de novembro de 2017|15 de novembro de 2019 |
| 0.9.0-pré-visualização |11 de novembro de 2017 |11 de novembro de 2019 |

## <a name="troubleshooting"></a>Resolução de problemas

Se tiver o erro 

```
Unable to resolve dependency 'Microsoft.Azure.Storage.Common'. Source(s) used: 'nuget.org', 
'CliFallbackFolder', 'Microsoft Visual Studio Offline Packages', 'Microsoft Azure Service Fabric SDK'`
```

ao tentar utilizar o pacote Microsoft.Azure.CosmosDB.Table NuGet, tem duas opções para corrigir o problema:

* Utilize a consola de gestão de pacotes para instalar o pacote Microsoft.Azure.CosmosDB.Table e as suas dependências. Para isso, digite o seguinte na Consola Package Manager para a sua solução. 

    ```powershell
    Install-Package Microsoft.Azure.CosmosDB.Table -IncludePrerelease
    ```

    
* Utilizando a sua ferramenta de gestão de pacotes NuGet preferida, instale o pacote Microsoft.Azure.Storage.Common NuGet antes de instalar microsoft.Azure.cosmosDB.Table.

## <a name="faq"></a>FAQ

[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Consulte também

Para saber mais sobre a API da Tabela DB AZure Cosmos, consulte [Introdução à Tabela DB API da Azure Cosmos](table-introduction.md). 
