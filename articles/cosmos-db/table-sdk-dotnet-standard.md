---
title: Azure Cosmos DB tabela API, SDK de .NET Standard & recursos
description: Saiba tudo sobre a API de tabela do Azure Cosmos DB e o .NET SDK padrão, incluindo as datas de lançamento, datas de extinção e as alterações feitas entre cada versão.
author: wmengmsft
ms.author: wmeng
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: dotnet
ms.topic: reference
ms.date: 10/18/2018
ms.openlocfilehash: 38b283ed666b39b4e090bd95051a4454a9b47e62
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57975666"
---
# <a name="azure-cosmos-db-table-net-standard-api-download-and-release-notes"></a>API padrão do .NET da tabela do Azure Cosmos DB: Transferir e notas de versão
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
|**Exemplo**|[Exemplo de .NET API de tabela de cosmos DB](https://github.com/Azure-Samples/azure-cosmos-table-dotnet-core-getting-started)|
|**Início rápido**|[Início rápido](create-table-dotnet.md)|
|**Tutorial**|[Tutorial](tutorial-develop-table-dotnet.md)|
|**Estrutura de suporte atual**|[Microsoft .NET Standard 2.0](https://www.nuget.org/packages/NETStandard.Library)|

## <a name="release-notes"></a>Notas de versão

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0
* Versão de disponibilidade geral

### <a name="a-name0110-preview0110-preview"></a><a name="0.11.0-preview"/>0.11.0-Preview
* As alterações foram feitas como CloudTableClient pode ser configurado. Agora demora um um objeto de TableClientConfiguration durante a construção. TableClientConfiguration fornece propriedades diferentes para configurar o comportamento de cliente, dependendo se o ponto de extremidade de destino é a API de tabela do Cosmos DB ou a API de tabela de armazenamento do Azure.
* Foi adicionado suporte para TableQuery para devolver resultados em ordem classificada numa coluna personalizada. Esta funcionalidade só é suportada em pontos finais de tabela do Cosmos DB.
* Foi adicionado suporte para expor RequestCharges em vários tipos de resultado. Esta funcionalidade só é suportada em pontos finais de tabela do Cosmos DB.

### <a name="a-name0101-preview0101-preview"></a><a name="0.10.1-preview"/>0.10.1-Preview
* Adicione suporte para SAS token, operações de TablePermissions, ServiceProperties e ServiceStats relativamente aos pontos finais de tabelas de armazenamento do Azure. 
   > [!NOTE]
   > Algumas funcionalidades no anterior SDKs de tabela de armazenamento do Azure ainda não são suportadas, como a encriptação do lado do cliente.

### <a name="a-name0100-preview0100-preview"></a><a name="0.10.0-preview"/>0.10.0-preview
* Adicione suporte para núcleo CRUD, batch e operações de consulta relativamente aos pontos finais de tabelas de armazenamento do Azure. 
   > [!NOTE]
   > Algumas funcionalidades no anterior SDKs de tabela de armazenamento do Azure ainda não são suportadas, como a encriptação do lado do cliente.

### <a name="a-name091-preview091-preview"></a><a name="0.9.1-preview"/>0.9.1-preview
* SDK do Azure Cosmos DB tabela .NET Standard é uma biblioteca de .NET de várias plataformas que fornece acesso eficiente ao modelo de dados de tabela no Cosmos DB. Esta versão inicial suporta o conjunto completo de tabela e entidade CRUD + funcionalidades de consulta com APIs similares como o [Cosmos DB tabela SDK para .NET Framework](table-sdk-dotnet.md). 
   > [!NOTE]
   >  Pontos finais de tabela de armazenamento do Azure ainda não são suportados na versão 0.9.1-preview.

## <a name="release-and-retirement-dates"></a>Datas de lançamento e de extinção
A Microsoft fornece notificação, pelo menos, **12 meses** antecedência extinguir um SDK para facilitar a transição para uma versão mais recente/suportadas.

| Versão | Data de lançamento | Data de retirada |
| --- | --- | --- |
| [1.0.0](#1.0.0) |13 de Março de 2019 |--- |
| [0.11.0-preview](#0.11.0-preview) |5 de Março de 2019 |--- |
| [0.10.1-preview](#0.10.1-preview) |22 de Janeiro de 2019 |--- |
| [0.10.0-preview](#0.10.0-preview) |18 de Dezembro de 2018 |--- |
| [0.9.1-preview](#0.9.1-preview) |18 de Outubro de 2018 |--- |


## <a name="faq"></a>FAQ

[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Consulte também
Para saber mais sobre a API de tabela do Azure Cosmos DB, veja [introdução à API de tabela do Azure Cosmos DB](table-introduction.md). 
