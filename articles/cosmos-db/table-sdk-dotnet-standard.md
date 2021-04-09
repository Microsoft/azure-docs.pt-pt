---
title: Azure Cosmos DB Tabela API .NET Standard SDK & Recursos
description: Saiba tudo sobre a Azure Cosmos DB Table API e o .NET Standard SDK, incluindo datas de lançamento, datas de reforma e alterações feitas entre cada versão.
author: sakash279
ms.author: akshanka
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: dotnet
ms.topic: reference
ms.date: 03/18/2019
ms.custom: devx-track-dotnet
ms.openlocfilehash: f41a895aef3476386b8b530fc3e265ffaa21e8c5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104597605"
---
# <a name="azure-cosmos-db-table-net-standard-api-download-and-release-notes"></a>Azure Cosmos DB Table .NET Standard API: Baixar e lançar notas
[!INCLUDE[appliesto-table-api](includes/appliesto-table-api.md)]
> [!div class="op_single_selector"]
> 
> * [.NET](table-sdk-dotnet.md)
> * [.NET Standard](table-sdk-dotnet-standard.md)
> * [Java](table-sdk-java.md)
> * [Node.js](table-sdk-nodejs.md)
> * [Python](table-sdk-python.md)

|   | Ligações  |
|---|---|
|**Transferência de SDK**|[NuGet](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table)|
|**Sample**|[Amostra API da tabela cosmos DB .NET](https://github.com/Azure-Samples/azure-cosmos-table-dotnet-core-getting-started)|
|**Início rápido**|[Início rápido](create-table-dotnet.md)|
|**Tutorial**|[Tutorial](tutorial-develop-table-dotnet.md)|
|**Framework suportado atualmente**|[Microsoft .NET Standard 2.0](https://www.nuget.org/packages/NETStandard.Library)|
|**Emissão do Relatório**|[Emissão do Relatório](https://github.com/Azure/azure-cosmos-table-dotnet/issues)|

## <a name="release-notes-for-200-series"></a>Notas de lançamento para série 2.0.0
A série 2.0.0 assume a dependência do [Microsoft.Azure.Cosmos,](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/)com melhorias de desempenho e consolidação de namespace para cosmos DB ponto final.

### <a name="200-preview"></a><a name="2.0.0-preview"></a>2.0.0-pré-visualização
* pré-visualização inicial de 2.0.0 Table SDK que assume a dependência do [Microsoft.Azure.Cosmos,](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/)com melhorias de desempenho e consolidação de espaço de nome para cosmos DB ponto final. A API pública continua a mesma.

## <a name="release-notes-for-100-series"></a>Notas de lançamento para série 1.0.0
Série 1.0.0 assume a dependência [ deMicrosoft.Azure.DocumentDB.Core](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core/).

### <a name="108"></a><a name="1.0.8"></a>1.0.8
* Adicione suporte para definir propriedade TTL se for o ponto final cosmosdb 
* Honra redação política no tempo limite e missão cancelada exceção
* Corrigir a exceção intermitente cancelada vista nas aplicações asp .net
* Corrigir azure armazenamento de mesa recuperar do modo de localização de ponto final secundário
* Versão `Microsoft.Azure.DocumentDB.Core` de atualização da dependência para 2.11.2 que corrige exceção intermitente de referência nulo
* Atualizar `Odata.Core` versão de dependência a 7.6.4 que corrige conflito de compatibilidade com concha azul

### <a name="107"></a><a name="1.0.7"></a>1.0.7
* Melhoria do desempenho ao definir o nível de traço padrão da Tabela SDK para SourceLevels.Off, que pode ser optado através de app.config

### <a name="105"></a><a name="1.0.5"></a>1.0.5
* Introduza novo config no âmbito da TableClientConfiguration para usar o Executor de Repouso para comunicar com a Cosmos DB Table API

### <a name="105-preview"></a><a name="1.0.5-preview"></a>1.0.5-pré-visualização
* Correções de erros

### <a name="104"></a><a name="1.0.4"></a>1.0.4
* Correções de erros
* Forneça a opção HttpClientTimeout para a Configuração do RestExecutor.

### <a name="104-preview"></a><a name="1.0.4-preview"></a>1.0.4 pré-visualização
* Correções de erros
* Forneça a opção HttpClientTimeout para a Configuração do RestExecutor.

### <a name="101"></a><a name="1.0.1"></a>1.0.1
* Correções de erros

### <a name="100"></a><a name="1.0.0"></a>1.0.0
* Libertação geral de disponibilidade

### <a name="0110-preview"></a><a name="0.11.0-preview"></a>0.11.0-pré-visualização
* Foram feitas alterações na forma como o CloudTableClient pode ser configurado. Agora é preciso um objeto de Configuração de TableClient durante a construção. A Configuração de TableClient fornece diferentes propriedades para configurar o comportamento do cliente dependendo se o ponto final alvo é Cosmos DB Table API ou API de Mesa de Armazenamento Azure.
* O suporte adicional ao TableQuery para devolver resulta em ordem ordenada numa coluna personalizada. Esta funcionalidade é suportada apenas nos pontos finais da Tabela Cosmos DB.
* Suporte adicional para expor RequestCharges em vários tipos de resultados. Esta funcionalidade é suportada apenas nos pontos finais da Tabela Cosmos DB.

### <a name="0101-preview"></a><a name="0.10.1-preview"></a>0.10.1 pré-visualização
* Adicione suporte para token SAS, operações de TablePermissions, ServiceProperties e ServiceStats contra pontos finais da Tabela de Armazenamento Azure. 
   > [!NOTE]
   > Algumas funcionalidades em SDKs de tabela de armazenamento Azure anteriores ainda não estão suportadas, como a encriptação do lado do cliente.

### <a name="0100-preview"></a><a name="0.10.0-preview"></a>0.10.0-pré-visualização
* Adicione suporte para operações crud, lote e consulta contra pontos finais da tabela de armazenamento Azure. 
   > [!NOTE]
   > Algumas funcionalidades em SDKs de tabela de armazenamento Azure anteriores ainda não estão suportadas, como a encriptação do lado do cliente.

### <a name="091-preview"></a><a name="0.9.1-preview"></a>0.9.1 pré-visualização
* Azure Cosmos DB Table .NET Standard SDK é uma biblioteca cross-platform .NET que proporciona um acesso eficiente ao modelo de dados da Tabela em Cosmos DB. Esta versão inicial suporta o conjunto completo de funcionalidades de Tabela e Entidade CRUD + Consulta com APIs semelhantes às da [Tabela Cosmos DB SDK para o quadro .NET](table-sdk-dotnet.md). 
   > [!NOTE]
   >  Os pontos finais da tabela de armazenamento Azure ainda não estão suportados na versão de pré-visualização de 0.9.1.

## <a name="release-and-retirement-dates"></a>Datas de lançamento e aposentadoria
A Microsoft fornece a notificação com pelo menos **12 meses** de antecedência para retirar um SDK de forma a suavizar a transição para uma versão mais recente/suportada.

Esta biblioteca padrão .NET [Microsoft.Azure.cosmos.Table](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) substituirá a biblioteca.NET Framework Library [Microsoft.Azure.CosmosDB.Table](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table).

### <a name="200-series"></a>Série 2.0.0
| Versão | Data da versão: | Data de Extinção |
| --- | --- | --- |
| [2.0.0-pré-visualização](#2.0.0-preview) |Auguest 22, 2019 |--- |

### <a name="100-series"></a>Série 1.0.0
| Versão | Data da versão: | Data de Extinção |
| --- | --- | --- |
| [1.0.5](#1.0.5) |13 de setembro de 2019 |--- |
| [1.0.5-pré-visualização](#1.0.5-preview) |Auguest 20, 2019 |--- |
| [1.0.4](#1.0.4) |Auguest 12, 2019 |--- |
| [1.0.4 pré-visualização](#1.0.4-preview) |26 de julho de 2019 |--- |
| 1.0.2 pré-visualização |2 de maio de 2019 |--- |
| [1.0.1](#1.0.1) |19 de abril de 2019 |--- |
| [1.0.0](#1.0.0) |13 de março de 2019 |--- |
| [0.11.0-pré-visualização](#0.11.0-preview) |5 de março de 2019 |--- |
| [0.10.1 pré-visualização](#0.10.1-preview) |22 de janeiro de 2019 |--- |
| [0.10.0-pré-visualização](#0.10.0-preview) |18 de dezembro de 2018 |--- |
| [0.9.1 pré-visualização](#0.9.1-preview) |18 de outubro de 2018 |--- |


## <a name="faq"></a>FAQ

[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Consulte também
Para saber mais sobre a API da Tabela DB AZure Cosmos, consulte [Introdução à Tabela DB API da Azure Cosmos](table-introduction.md).
