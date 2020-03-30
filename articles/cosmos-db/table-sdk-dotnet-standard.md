---
title: Azure Cosmos DB Table API .NET Standard SDK & Resources
description: Saiba tudo sobre o API da Tabela Db Do Azure Cosmos e o .NET Standard SDK, incluindo datas de lançamento, datas de reforma e alterações feitas entre cada versão.
author: sakash279
ms.author: akshanka
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: dotnet
ms.topic: reference
ms.date: 03/18/2019
ms.openlocfilehash: 48fd85e27feb3d0c7f7f722dbbd502be55684385
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76771595"
---
# <a name="azure-cosmos-db-table-net-standard-api-download-and-release-notes"></a>Azure Cosmos DB Table .NET Standard API: Descarregamento e lançamento
> [!div class="op_single_selector"]
> 
> * [.NET](table-sdk-dotnet.md)
> * [.NET Standard](table-sdk-dotnet-standard.md)
> * [Java](table-sdk-java.md)
> * [Nó.js](table-sdk-nodejs.md)
> * [Pitão](table-sdk-python.md)

|   |   |
|---|---|
|**Download sDK**|[Nuget](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table)|
|**Amostra**|[Cosmos DB Table API .NET Sample](https://github.com/Azure-Samples/azure-cosmos-table-dotnet-core-getting-started)|
|**Início rápido**|[Início rápido](create-table-dotnet.md)|
|**Tutorial**|[Tutorial](tutorial-develop-table-dotnet.md)|
|**Quadro apoiado atual**|[Microsoft .NET Standard 2.0](https://www.nuget.org/packages/NETStandard.Library)|
|**Número de relatório**|[Número de relatório](https://github.com/Azure/azure-cosmos-table-dotnet/issues)|

## <a name="release-notes-for-200-series"></a>Notas de lançamento para série2.0.0
A série 2.0.0 assume a dependência da [Microsoft.Azure.Cosmos,](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/)com melhorias de desempenho e consolidação de espaço de nome para cosmos DB endpoint.

### <a name="200-preview"></a><a name="2.0.0-preview"/>2.0.0-pré-visualização
* Pré-visualização inicial de 2.0.0 Table SDK que assume a dependência da [Microsoft.Azure.Cosmos,](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/)com melhorias de desempenho e consolidação do espaço de nome para cosmos DB endpoint. A API pública continua a mesma.

## <a name="release-notes-for-100-series"></a>Notas de lançamento para série 1.0.0
Série 1.0.0 assume a dependência da [Microsoft.Azure.DocumentDB.Core](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core/).

### <a name="105"></a><a name="1.0.5"/>1.0.5
* Introduza uma nova config no tableClientConfiguration para usar o Executor de Repouso para comunicar com a Cosmos DB Table API

### <a name="105-preview"></a><a name="1.0.5-preview"/>1.0.5-pré-visualização
* Correções de erros

### <a name="104"></a><a name="1.0.4"/>1.0.4
* Correções de erros
* Fornecer a opção HttpClientTimeout para RestExecuteorConfiguration.

### <a name="104-preview"></a><a name="1.0.4-preview"/>1.0.4-pré-visualização
* Correções de erros
* Fornecer a opção HttpClientTimeout para RestExecuteorConfiguration.

### <a name="101"></a><a name="1.0.1"/>1.0.1
* Correções de erros

### <a name="100"></a><a name="1.0.0"/>1.0.0
* Libertação geral de disponibilidade

### <a name="0110-preview"></a><a name="0.11.0-preview"/>0.11.0-pré-visualização
* Foram feitas alterações na configuração do CloudTableClient. Agora é preciso um objeto tableClientConfiguration durante a construção. TableClientConfiguration fornece diferentes propriedades para configurar o comportamento do cliente dependendo se o ponto final alvo é Cosmos DB Table API ou Azure Storage Table API.
* Suporte adicionado ao TableQuery para devolver resulta em ordem ordenada numa coluna personalizada. Esta funcionalidade é suportada apenas nos pontos finais da Tabela Cosmos DB.
* Suporte adicional para expor Pedidos Encargos em vários tipos de resultados. Esta funcionalidade é suportada apenas nos pontos finais da Tabela Cosmos DB.

### <a name="0101-preview"></a><a name="0.10.1-preview"/>0.10.1-pré-visualização
* Adicione suporte para token SAS, operações de TablePermissions, ServiceProperties e ServiceStats contra pontos finais da tabela de armazenamento Azure. 
   > [!NOTE]
   > Algumas funcionalidades em SDKs de Mesa de Armazenamento Azure anteriores ainda não são suportadas, como encriptação do lado do cliente.

### <a name="0100-preview"></a><a name="0.10.0-preview"/>0.10.0-pré-visualização
* Adicione suporte para operações crud, lote e consulta de núcleo contra pontos finais da tabela de armazenamento Azure. 
   > [!NOTE]
   > Algumas funcionalidades em SDKs de Mesa de Armazenamento Azure anteriores ainda não são suportadas, como encriptação do lado do cliente.

### <a name="091-preview"></a><a name="0.9.1-preview"/>0.9.1-pré-visualização
* Azure Cosmos DB Table .NET Standard SDK é uma biblioteca cross-platform .NET que proporciona um acesso eficiente ao modelo de dados da Tabela no Cosmos DB. Esta versão inicial suporta o conjunto completo de funcionalidades de Table and Entity CRUD + Query com APIs semelhantes à [Tabela Cosmos DB SDK para .NET Framework](table-sdk-dotnet.md). 
   > [!NOTE]
   >  Os pontos finais da Tabela de Armazenamento Azure ainda não são suportados na versão de pré-visualização de 0.9.1.

## <a name="release-and-retirement-dates"></a>Datas de lançamento e aposentadoria
A Microsoft fornece notificação com pelo menos **12 meses** de antecedência para a reforma de um SDK de forma a suavizar a transição para uma versão mais recente/suportada.

Esta biblioteca padrão .NET .NET [Microsoft.Azure.Cosmos.Table](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) substituirá a biblioteca .NET Framework [Microsoft.Azure.CosmosDB.Table](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table).

### <a name="200-series"></a>Série 2.0.0
| Versão | Data de Lançamento | Data de Aposentadoria |
| --- | --- | --- |
| [2.0.0-pré-visualização](#2.0.0-preview) |Auguest 22, 2019 |--- |

### <a name="100-series"></a>Série 1.0.0
| Versão | Data de Lançamento | Data de Aposentadoria |
| --- | --- | --- |
| [1.0.5](#1.0.5) |13 de setembro de 2019 |--- |
| [1.0.5-pré-visualização](#1.0.5-preview) |Auguest 20, 2019 |--- |
| [1.0.4](#1.0.4) |Auguest 12, 2019 |--- |
| [1.0.4-pré-visualização](#1.0.4-preview) |26 de julho de 2019 |--- |
| 1.0.2-pré-visualização |2 de maio de 2019 |--- |
| [1.0.1](#1.0.1) |19 de abril de 2019 |--- |
| [1.0.0](#1.0.0) |Março 13, 2019 |--- |
| [0.11.0-pré-visualização](#0.11.0-preview) |5 de março de 2019 |--- |
| [0.10.1-pré-visualização](#0.10.1-preview) |22 de janeiro de 2019 |--- |
| [0.10.0-pré-visualização](#0.10.0-preview) |18 de dezembro de 2018 |--- |
| [0.9.1-pré-visualização](#0.9.1-preview) |18 de outubro de 2018 |--- |


## <a name="faq"></a>FAQ

[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Consulte também
Para saber mais sobre a API da Tabela Azure Cosmos DB, consulte introdução à Tabela API da [Tabela Db Do Cosmos Azure.](table-introduction.md)
