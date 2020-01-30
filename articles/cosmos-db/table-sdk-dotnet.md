---
title: Azure Cosmos DB Table API .NET SDK & Resources
description: Saiba tudo sobre a API tabela do Azure Cosmos DB incluindo as datas de lançamento, datas de extinção e as alterações feitas entre cada versão.
author: sakash279
ms.author: akshanka
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: dotnet
ms.topic: reference
ms.date: 08/17/2018
ms.openlocfilehash: 5a5305ffd388d2573d250d93131c1fed236008b7
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2020
ms.locfileid: "76771622"
---
# <a name="azure-cosmos-db-table-net-api-download-and-release-notes"></a>Azure Cosmos DB Table .NET API: Descarregue e lance notas

> [!div class="op_single_selector"]
> * [.NET](table-sdk-dotnet.md)
> * [.NET Standard](table-sdk-dotnet-standard.md)
> * [Java](table-sdk-java.md)
> * [Node.js](table-sdk-nodejs.md)
> * [Python](table-sdk-python.md)

|   |   |
|---|---|
|**Transferência de SDK**|[NuGet](https://aka.ms/acdbtablenuget)|
|**Início rápido**|[Azure Cosmos DB: Construa uma app com .NET e a Table API](create-table-dotnet.md)|
|**Tutorial**|[Azure Cosmos DB: Desenvolver com a Tabela API em .NET](tutorial-develop-table-dotnet.md)|
|**Estrutura de suporte atual**|[Microsoft .NET Framework 4.5.1](https://www.microsoft.com/en-us/download/details.aspx?id=40779)|

> [!IMPORTANT]
> O .NET Framework SDK [Microsoft.Azure.CosmosDB.Table](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table) está em modo de manutenção e será depreciado em breve. Por favor, atualize para a nova biblioteca .NET Standard [Microsoft.Azure.Cosmos.Table](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) para continuar a receber as mais recentes funcionalidades suportadas pela API de Mesa.

> Se tiver criado uma conta de API de Tabela durante a pré-visualização, crie uma [nova conta de API de Tabela](create-table-dotnet.md#create-a-database-account) para trabalhar com os SDKs de API de Tabela disponíveis geralmente.
>

## <a name="release-notes"></a>Notas de versão

### <a name="a-name212212"></a><a name="2.1.2"/>2.1.2

* Correções de erros

### <a name="a-name210210"></a><a name="2.1.0"/>2.1.0

* Correções de erros

### <a name="a-name200200"></a><a name="2.0.0"/>2.0.0

* Suporte de escrita multi-região adicionado
* Dependências fixas do pacote NuGet em Microsoft.Azure.DocumentDB, Microsoft.OData.Core, Microsoft.OData.Edm, Microsoft.Spatial

### <a name="a-name113113"></a><a name="1.1.3"/>1.1.3

* Dependências fixas do pacote NuGet em Microsoft.Azure.Storage.Common e Microsoft.Azure.DocumentDB.
* Correções de bugs na serialização da tabela quando JsonConvert.DefaultSettings são configuradas.

### <a name="a-name111111"></a><a name="1.1.1"/>1.1.1

* Validação adicionada para ETAGs mal formados em modo direto.
* Bug de consulta LINQ fixo no modo Gateway.
* APIs sincronizados agora funcionam na piscina de fios com SynchronizationContext.

### <a name="a-name110110"></a><a name="1.1.0"/>1.1.0

* Adicionar QuadroQueryMaxItemCount, TableQueryEnableScan, TableQueryMaxDegreeOfParallelism e TableQueryContinuationTokenLimitInKb a TableRequestOptions
* Correções de bugs

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0

* Versão de disponibilidade geral

### <a name="a-name010-preview090-preview"></a><a name="0.1.0-preview"/>0.9.0-preview

* Versão de pré-visualização inicial

## <a name="release-and-retirement-dates"></a>Datas de lançamento e aposentadoria

A Microsoft fornece notificação com pelo menos **12 meses** de antecedência para a reforma de um SDK de forma a suavizar a transição para uma versão mais recente/suportada.

A biblioteca `Microsoft.Azure.CosmosDB.Table` está atualmente disponível apenas para .NET Framework, e está em modo de manutenção e será depreciada em breve. As novas funcionalidades e funcionalidades e otimizações só são adicionadas à biblioteca .NET Standard [Microsoft.Azure.Cosmos.Table](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table), como tal é recomendado que faça o upgrade para [Microsoft.Azure.Cosmos.Table](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table).

O pacote de [pré-visualização WindowsAzure.Storage-PremiumTable](https://www.nuget.org/packages/WindowsAzure.Storage-PremiumTable/0.1.0-preview) foi amortizado. O WindowsAzure.Storage-PremiumTable SDK será retirado no dia 15 de novembro de 2018, altura em que não serão permitidos pedidos ao SDK reformado. 

Quaisquer pedidos à Azure Cosmos DB utilizando um SDK reformado são rejeitados pelo serviço.
<br/>

| Versão | Data de lançamento | Data de retirada |
| --- | --- | --- |
| [2.1.2](#2.1.2) |16 de setembro de 2019| |
| [2.1.0](#2.1.0) |22 de janeiro de 2019|01 de abril de 2020 |
| [2.0.0](#2.0.0) |26 de setembro de 2018|01 de março de 2020 |
| [1.1.3](#1.1.3) |17 de julho de 2018|01 de dezembro de 2019 |
| [1.1.1](#1.1.1) |26 de março de 2018|01 de dezembro de 2019 |
| [1.1.0](#1.1.0) |21 de fevereiro de 2018|01 de dezembro de 2019 |
| [1.0.0](#1.0.0) |15 de Novembro de 2017|15 de novembro de 2019 |
| 0.9.0-pré-visualização |11 de novembro de 2017 |11 de novembro de 2019 |

## <a name="troubleshooting"></a>Resolução de problemas

Se tiver o erro 

```
Unable to resolve dependency 'Microsoft.Azure.Storage.Common'. Source(s) used: 'nuget.org', 
'CliFallbackFolder', 'Microsoft Visual Studio Offline Packages', 'Microsoft Azure Service Fabric SDK'`
```

ao tentar utilizar o pacote Microsoft.Azure.CosmosDB.Table NuGet, tem duas opções para corrigir o problema:

* Utilize a Consola de Gestão de Pacotes para instalar o pacote Microsoft.Azure.CosmosDB.Table e as suas dependências. Para isso, digite o seguinte na Consola do Gestor de Pacotes para a sua solução. 

    ```powershell
    Install-Package Microsoft.Azure.CosmosDB.Table -IncludePrerelease
    ```

    
* Utilizando a sua ferramenta de gestão de pacotes NuGet preferida, instale o pacote Microsoft.Azure.Storage.Common NuGet antes de instalar o Microsoft.Azure.CosmosDB.Table.

## <a name="faq"></a>FAQ

[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Ver também

Para saber mais sobre a API da Tabela Azure Cosmos DB, consulte introdução à Tabela API da [Tabela Db Do Cosmos Azure.](table-introduction.md) 
