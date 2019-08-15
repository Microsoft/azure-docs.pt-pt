---
title: Azure Cosmos DB API de Tabela recursos do SDK do .NET &
description: Saiba tudo sobre a API tabela do Azure Cosmos DB incluindo as datas de lançamento, datas de extinção e as alterações feitas entre cada versão.
author: wmengmsft
ms.author: wmeng
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: dotnet
ms.topic: reference
ms.date: 08/17/2018
ms.openlocfilehash: aa9752cdc2148036b3edff8a6002dc81ebff6312
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/15/2019
ms.locfileid: "69036309"
---
# <a name="azure-cosmos-db-table-net-api-download-and-release-notes"></a>API .NET da tabela Azure Cosmos DB: Download e notas de versão

> [!div class="op_single_selector"]
> * [.NET](table-sdk-dotnet.md)
> * [.NET Standard](table-sdk-dotnet-standard.md)
> * [Java](table-sdk-java.md)
> * [Node.js](table-sdk-nodejs.md)
> * [Python](table-sdk-python.md)

|   |   |
|---|---|
|**Transferência de SDK**|[NuGet](https://aka.ms/acdbtablenuget)|
|**Início rápido**|[Azure Cosmos DB: Crie um aplicativo com o .NET e o API de Tabela](create-table-dotnet.md)|
|**Tutorial**|[Azure Cosmos DB: Desenvolva com o API de Tabela no .NET](tutorial-develop-table-dotnet.md)|
|**Estrutura de suporte atual**|[Microsoft .NET Framework 4.5.1](https://www.microsoft.com/en-us/download/details.aspx?id=40779)|

> [!IMPORTANT]
> O SDK do .NET Framework [Microsoft. Azure. CosmosDB. Table](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table) está no modo de manutenção e será preterido em breve. Atualize para a nova biblioteca de .NET Standard [Microsoft. Azure. Cosmos. Table](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) para continuar a obter os recursos mais recentes com suporte no API de tabela.

> Se tiver criado uma conta de API de Tabela durante a pré-visualização, crie uma [nova conta de API de Tabela](create-table-dotnet.md#create-a-database-account) para trabalhar com os SDKs de API de Tabela disponíveis geralmente.
>

## <a name="release-notes"></a>Notas de versão

### <a name="a-name210210"></a><a name="2.1.0"/>2.1.0

* Correções de erros

### <a name="a-name200200"></a><a name="2.0.0"/>2.0.0

* Adição de suporte de gravação de várias regiões
* Correção das dependências do pacote NuGet no Microsoft. Azure. DocumentDB, Microsoft. OData. Core, Microsoft. OData. EDM, Microsoft. Spatial

### <a name="a-name113113"></a><a name="1.1.3"/>1.1.3

* Correção das dependências do pacote NuGet em Microsoft. Azure. Storage. Common e Microsoft. Azure. DocumentDB.
* Correções de bugs na serialização de tabela quando JsonConvert. DefaultSettings são configuradas.

### <a name="a-name111111"></a><a name="1.1.1"/>1.1.1

* Validação adicionada para ETAGs malformados no modo direto.
* Bug de consulta LINQ corrigido no modo de gateway.
* As APIs síncronas agora são executadas no pool de threads com SynchronizationContext.

### <a name="a-name110110"></a><a name="1.1.0"/>1.1.0

* Adicionar TableQueryMaxItemCount, TableQueryEnableScan, TableQueryMaxDegreeOfParallelism e TableQueryContinuationTokenLimitInKb a TableRequestOptions
* Correções de bugs

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0

* Versão de disponibilidade geral

### <a name="a-name010-preview090-preview"></a><a name="0.1.0-preview"/>0.9.0-preview

* Versão de pré-visualização inicial

## <a name="release-and-retirement-dates"></a>Datas de lançamento e desativação

A Microsoft fornece notificações com pelo menos **12 meses** de antecedência para desativar um SDK a fim de suavizar a transição para uma versão mais recente/com suporte.

Atualmente `Microsoft.Azure.CosmosDB.Table` , a biblioteca está disponível somente para .NET Framework e está no modo de manutenção e será preterida em breve. Novos recursos e funcionalidades e otimizações são adicionados somente à biblioteca de .NET Standard [Microsoft. Azure. Cosmos. Table](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table), por isso é recomendável que você atualize para [Microsoft. Azure. Cosmos. Table](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table).

O pacote de visualização [WindowsAzure. Storage-premiumtable](https://www.nuget.org/packages/WindowsAzure.Storage-PremiumTable/0.1.0-preview) foi preterido. O SDK WindowsAzure. Storage-premiumtable será desativado em 15 de novembro de 2018, em que as solicitações de tempo para o SDK desativado não serão permitidas. 

Todas as solicitações para Azure Cosmos DB usando um SDK desativado são rejeitadas pelo serviço.
<br/>

| Version | Data de lançamento | Data de retirada |
| --- | --- | --- |
| [2.1.0](#2.1.0) |22 de janeiro de 2019|01 de abril de 2020 |
| [2.0.0](#2.0.0) |26 de setembro de 2018|1º de março de 2020 |
| [1.1.3](#1.1.3) |17 de julho de 2018|1º de dezembro de 2019 |
| [1.1.1](#1.1.1) |26 de março de 2018|1º de dezembro de 2019 |
| [1.1.0](#1.1.0) |21 de fevereiro de 2018|1º de dezembro de 2019 |
| [1.0.0](#1.0.0) |15 de Novembro de 2017|15 de novembro de 2019 |
| 0.9.0-visualização |11 de novembro de 2017 |11 de novembro de 2019 |

## <a name="troubleshooting"></a>Resolução de problemas

Se você receber o erro 

```
Unable to resolve dependency 'Microsoft.Azure.Storage.Common'. Source(s) used: 'nuget.org', 
'CliFallbackFolder', 'Microsoft Visual Studio Offline Packages', 'Microsoft Azure Service Fabric SDK'`
```

ao tentar usar o pacote NuGet Microsoft. Azure. CosmosDB. Table, você tem duas opções para corrigir o problema:

* Use o console gerenciar pacote para instalar o pacote Microsoft. Azure. CosmosDB. Table e suas dependências. Para fazer isso, digite o seguinte no console do Gerenciador de pacotes para sua solução. 

    ```powershell
    Install-Package Microsoft.Azure.CosmosDB.Table -IncludePrerelease
    ```

    
* Usando sua ferramenta de gerenciamento de pacotes do NuGet preferida, instale o pacote NuGet Microsoft. Azure. Storage. Common antes de instalar Microsoft. Azure. CosmosDB. Table.

## <a name="faq"></a>FAQ

[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Consulte também

Para saber mais sobre o API de Tabela de Azure Cosmos DB, consulte [introdução ao Azure Cosmos DB API de tabela](table-introduction.md). 