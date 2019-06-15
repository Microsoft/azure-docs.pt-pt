---
title: Azure Cosmos DB tabela SDK de .NET API de & recursos
description: Saiba tudo sobre a API tabela do Azure Cosmos DB incluindo as datas de lançamento, datas de extinção e as alterações feitas entre cada versão.
author: wmengmsft
ms.author: wmeng
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: dotnet
ms.topic: reference
ms.date: 08/17/2018
ms.openlocfilehash: db7cc556525ab57f14984232bf1797764865fca3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65606253"
---
# <a name="azure-cosmos-db-table-net-api-download-and-release-notes"></a>.NET API da tabela do Azure Cosmos DB: Transferir e notas de versão

> [!div class="op_single_selector"]
> * [.NET](table-sdk-dotnet.md)
> * [.NET Standard](table-sdk-dotnet-standard.md)
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
> SDK do .NET Framework [cosmosdb](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table) está em manutenção modo e vão ser preteridos em breve. Atualize para a nova biblioteca .NET Standard [Microsoft.Azure.Cosmos.Table](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) para continuar a ter as funcionalidades mais recentes suportadas pela API de tabela.

> Se tiver criado uma conta de API de Tabela durante a pré-visualização, crie uma [nova conta de API de Tabela](create-table-dotnet.md#create-a-database-account) para trabalhar com os SDKs de API de Tabela disponíveis geralmente.
>

## <a name="release-notes"></a>Notas de versão

### <a name="a-name210210"></a><a name="2.1.0"/>2.1.0

* Correções de erros

### <a name="a-name200200"></a><a name="2.0.0"/>2.0.0

* Suporte adicionado de várias regiões de escrita
* Dependências de pacote NuGet fixas Microsoft.Azure.DocumentDB, Microsoft.OData.Core, Microsoft.OData.Edm, Microsoft.Spatial

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

### <a name="a-name010-preview090-preview"></a><a name="0.1.0-preview"/>0.9.0-preview

* Versão de pré-visualização inicial

## <a name="release-and-retirement-dates"></a>Datas de lançamento e de extinção

A Microsoft fornece notificação, pelo menos, **12 meses** antecedência extinguir um SDK para facilitar a transição para uma versão mais recente/suportadas.

O `Microsoft.Azure.CosmosDB.Table` biblioteca está atualmente disponível para o .NET Framework apenas e está no modo de manutenção e será preterida em breve. Novos recursos e funcionalidades e otimizações, só são adicionadas à biblioteca .NET Standard [Microsoft.Azure.Cosmos.Table](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table), como tal é recomendável que Atualize para [Microsoft.Azure.Cosmos.Table](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table).

O [windowsazure. Storage-PremiumTable](https://www.nuget.org/packages/WindowsAzure.Storage-PremiumTable/0.1.0-preview) pacote de pré-visualização foi preterido. O SDK de windowsazure. Storage-PremiumTable será retirado a 15 de Novembro de 2018, na qual tempo pedidos para o SDK extinto não será permitido. 

Todos os pedidos para o Azure Cosmos DB com um SDK extinto são rejeitados pelo serviço.
<br/>

| Version | Data de lançamento | Data de retirada |
| --- | --- | --- |
| [2.1.0](#2.1.0) |22 de Janeiro de 2019|01 de Abril de 2020 |
| [2.0.0](#2.0.0) |26 de Setembro de 2018|01 de Março de 2020 |
| [1.1.3](#1.1.3) |17 de Julho de 2018|01 de Dezembro de 2019 |
| [1.1.1](#1.1.1) |26 de Março de 2018|01 de Dezembro de 2019 |
| [1.1.0](#1.1.0) |21 de fevereiro de 2018|01 de Dezembro de 2019 |
| [1.0.0](#1.0.0) |15 de Novembro de 2017|15 de Novembro de 2019 |
| 0.9.0-preview |11 de Novembro de 2017 |11 de Novembro de 2019 |

## <a name="troubleshooting"></a>Resolução de problemas

Se obtiver o erro 

```
Unable to resolve dependency 'Microsoft.Azure.Storage.Common'. Source(s) used: 'nuget.org', 
'CliFallbackFolder', 'Microsoft Visual Studio Offline Packages', 'Microsoft Azure Service Fabric SDK'`
```

ao tentar utilizar o pacote NuGet do cosmosdb, tem duas opções para corrigir o problema:

* Utilize a consola de gerir pacotes para instalar o pacote do cosmosdb e as respetivas dependências. Para tal, escreva o seguinte na consola do Gestor de pacotes para a sua solução. 

    ```powershell
    Install-Package Microsoft.Azure.CosmosDB.Table -IncludePrerelease
    ```

    
* Utilizar a sua ferramenta de gestão de pacotes de NuGet preferencial, instale o pacote de Microsoft.Azure.Storage.Common NuGet antes de instalar o cosmosdb.

## <a name="faq"></a>FAQ

[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Consulte também

Para saber mais sobre a API de tabela do Azure Cosmos DB, veja [introdução à API de tabela do Azure Cosmos DB](table-introduction.md). 