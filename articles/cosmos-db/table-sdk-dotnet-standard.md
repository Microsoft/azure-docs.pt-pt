---
title: Recursos de & do SDK do Azure Cosmos DB API de Tabela .NET Standard
description: Saiba tudo sobre o Azure Cosmos DB API de Tabela e o SDK do .NET Standard, incluindo datas de lançamento, datas de desativação e alterações feitas entre cada versão.
author: wmengmsft
ms.author: wmeng
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: dotnet
ms.topic: reference
ms.date: 03/18/2019
ms.openlocfilehash: e92c2e58122556f90b25f070fff9e6e6616b0257
ms.sourcegitcommit: 62bd5acd62418518d5991b73a16dca61d7430634
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/13/2019
ms.locfileid: "68976854"
---
# <a name="azure-cosmos-db-table-net-standard-api-download-and-release-notes"></a>API de .NET Standard de tabela de Azure Cosmos DB: Download e notas de versão
> [!div class="op_single_selector"]
> 
> * [.NET](table-sdk-dotnet.md)
> * [.NET Standard](table-sdk-dotnet-standard.md)
> * [Java](table-sdk-java.md)
> * [Node.js](table-sdk-nodejs.md)
> * [Python](table-sdk-python.md)

|   |   |
|---|---|
|**Transferência de SDK**|[NuGet](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table)|
|**Exemplo**|[Exemplo de Cosmos DB API de Tabela .NET](https://github.com/Azure-Samples/azure-cosmos-table-dotnet-core-getting-started)|
|**Início rápido**|[Início rápido](create-table-dotnet.md)|
|**Tutorial**|[Tutorial](tutorial-develop-table-dotnet.md)|
|**Estrutura de suporte atual**|[Microsoft .NET Standard 2,0](https://www.nuget.org/packages/NETStandard.Library)|
|**Relatar problema**|[Relatar problema](https://github.com/Azure/azure-cosmos-table-dotnet/issues)|

## <a name="release-notes"></a>Notas de versão

### <a name="a-name104104"></a><a name="1.0.4"/>1.0.4
* Correções de erros
* Forneça a opção HttpClientTimeout para RestExecutorConfiguration.

### <a name="a-name104-preview104-preview"></a><a name="1.0.4-preview"/>1.0.4-visualização
* Correções de erros
* Forneça a opção HttpClientTimeout para RestExecutorConfiguration.

### <a name="a-name101101"></a><a name="1.0.1"/>1.0.1
* Correções de erros

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0
* Versão de disponibilidade geral

### <a name="a-name0110-preview0110-preview"></a><a name="0.11.0-preview"/>0.11.0-visualização
* Foram feitas alterações em como o CloudTableClient pode ser configurado. Agora, ele pega um objeto TableClientConfiguration durante a construção. O TableClientConfiguration fornece propriedades diferentes para configurar o comportamento do cliente dependendo se o ponto de extremidade de destino é Cosmos DB API de Tabela ou API de Tabela de armazenamento do Azure.
* Adicionado suporte a TableQuery para retornar resultados em uma coluna personalizada. Esse recurso só tem suporte em pontos de extremidade de tabela Cosmos DB.
* Adicionado suporte para expor RequestCharges em vários tipos de resultados. Esse recurso só tem suporte em pontos de extremidade de tabela Cosmos DB.

### <a name="a-name0101-preview0101-preview"></a><a name="0.10.1-preview"/>0.10.1-visualização
* Adicione suporte para token SAS, operações de TablePermissions, Serviceproperties e perstats nos pontos de extremidade da tabela de armazenamento do Azure. 
   > [!NOTE]
   > Algumas funcionalidades em SDKs de tabela de armazenamento do Azure anteriores ainda não têm suporte, como criptografia do lado do cliente.

### <a name="a-name0100-preview0100-preview"></a><a name="0.10.0-preview"/>0.10.0-visualização
* Adicione suporte para operações CRUD, lote e consulta de núcleo em pontos de extremidade de tabela de armazenamento do Azure. 
   > [!NOTE]
   > Algumas funcionalidades em SDKs de tabela de armazenamento do Azure anteriores ainda não têm suporte, como criptografia do lado do cliente.

### <a name="a-name091-preview091-preview"></a><a name="0.9.1-preview"/>0.9.1-preview
* Azure Cosmos DB tabela .NET Standard SDK é uma biblioteca .NET de plataforma cruzada que fornece acesso eficiente ao modelo de dados de tabela no Cosmos DB. Essa versão inicial dá suporte ao conjunto completo de funcionalidades de consulta e CRUD de tabela e entidade com APIs semelhantes às [Cosmos DB SDK de tabela para .NET Framework](table-sdk-dotnet.md). 
   > [!NOTE]
   >  Os pontos de extremidade da tabela de armazenamento do Azure ainda não têm suporte na versão 0.9.1-Preview.

## <a name="release-and-retirement-dates"></a>Datas de lançamento e desativação
A Microsoft fornece notificações com pelo menos **12 meses** de antecedência para desativar um SDK a fim de suavizar a transição para uma versão mais recente/com suporte.

Esta biblioteca de .NET Standard entre plataformas [Microsoft. Azure. Cosmos. Table](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) substituirá a biblioteca de .NET Framework [Microsoft. Azure. CosmosDB. Table](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table).

| Version | Data de lançamento | Data de retirada |
| --- | --- | --- |
| [1.0.4](#1.0.4) |Auguest 12, 2019 |--- |
| [1.0.4-visualização](#1.0.4-preview) |26 de julho de 2019 |--- |
| 1.0.2-preview |2 de maio de 2019 |--- |
| [1.0.1](#1.0.1) |19 de abril de 2019 |--- |
| [1.0.0](#1.0.0) |13 de março de 2019 |--- |
| [0.11.0-visualização](#0.11.0-preview) |5 de março de 2019 |--- |
| [0.10.1-visualização](#0.10.1-preview) |22 de janeiro de 2019 |--- |
| [0.10.0-preview](#0.10.0-preview) |18 de dezembro de 2018 |--- |
| [0.9.1-preview](#0.9.1-preview) |18 de outubro de 2018 |--- |


## <a name="faq"></a>FAQ

[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Consulte também
Para saber mais sobre o API de Tabela de Azure Cosmos DB, consulte [introdução ao Azure Cosmos DB API de tabela](table-introduction.md).
