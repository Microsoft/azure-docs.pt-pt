---
title: Criar aplicações móveis com o Xamarin e o Azure Cosmos DB | Microsoft Docs
description: Um tutorial para criar uma aplicação Xamarin iOS, Android ou Formulários com o Azure Cosmos DB. O Azure Cosmos DB é uma base de dados na cloud rápida e à escala mundial para aplicações móveis.
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 11/15/2017
ms.author: sngun
ms.openlocfilehash: 6311316ab5aeab4685ca820ef5dbec4832896dcd
ms.sourcegitcommit: ebf2f2fab4441c3065559201faf8b0a81d575743
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2018
ms.locfileid: "52164511"
---
# <a name="build-mobile-applications-with-xamarin-and-azure-cosmos-db"></a>Criar aplicações móveis com o Xamarin e o Azure Cosmos DB

> [!div class="op_single_selector"]
> * [.NET](sql-api-dotnet-application.md)
> * [Java](sql-api-java-application.md)
> * [Node.js](sql-api-nodejs-application.md)
> * [Python](sql-api-python-application.md)
> * [Xamarin](mobile-apps-with-xamarin.md)
> 

A maioria das aplicações móveis precisa de armazenar dados na cloud e o Azure Cosmos DB é uma base de dados na cloud para aplicações móveis. Tem tudo aquilo de que um programador de aplicações móveis necessita. É uma base de dados como serviço totalmente gerida e dimensionada a pedido. Pode transportar os seus dados para a aplicação de forma transparente, independentemente de onde os seus utilizadores estejam no mundo. Através do [SDK de .NET Core do Azure Cosmos DB](sql-api-sdk-dotnet-core.md), pode permitir que as aplicações móveis Xamarin interajam diretamente com o Azure Cosmos DB sem uma camada média.

Este artigo disponibiliza um tutorial para criar aplicações móveis com o Xamarin e o Azure Cosmos DB. O código fonte completo do tutorial encontra-se no [Xamarin e no Azure Cosmos DB no GitHub](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/xamarin), incluindo como pode gerir utilizadores e permissões.

## <a name="azure-cosmos-db-capabilities-for-mobile-apps"></a>Funcionalidades do Azure Cosmos DB para aplicações móveis
O Azure Cosmos DB oferece as seguintes funcionalidades principais para os programadores de aplicações móveis:

![Funcionalidades do Azure Cosmos DB para aplicações móveis](media/mobile-apps-with-xamarin/documentdb-for-mobile.png)

* Consultas avançadas de dados sem esquemas. O Azure Cosmos DB armazena os dados como documentos JSON sem esquemas em coleções heterogéneas. Proporciona [consultas avançadas e rápidas](how-to-sql-query.md) sem a necessidade de esquemas ou índices.
* Débito rápido. Ler e gravar documentos com o Azure Cosmos DB demora apenas alguns milissegundos. Os programadores podem especificar o débito de que necessitam. Além disso, o Azure Cosmos DB permite fazê-lo com um SLA de 99,99% de disponibilidade para todas as contas de região única e para todas as contas de várias regiões com consistência flexível e 99,999% de disponibilidade de leitura em todas as contas de bases de dados de várias regiões.
* Dimensionamento ilimitado. As suas coleções do Azure Cosmos DB [aumentam conforme a sua aplicação cresce](partition-data.md). Pode começar com dados de pequena dimensão e um débito de centenas de pedidos por segundo. As suas coleções ou bases de dados podem aumentar para petabytes de dados e um débito arbitrariamente grande com centenas de milhões de pedidos por segundo.
* Distribuído globalmente. Os utilizadores de aplicações móveis utilizam-nas em qualquer lugar e, muitas vezes, em todo o mundo. O Azure Cosmos DB é uma [base de dados distribuída globalmente](distribute-data-globally.md). Clique no mapa para disponibilizar dados aos seus utilizadores.
* Autorização avançada incorporada. Com o Azure Cosmos DB, pode implementar padrões populares com facilidade, como [dados por utilizador](https://aka.ms/documentdb-xamarin-todouser) ou dados partilhados multiutilizador sem códigos de autorização personalizados complexos.
* Consultas geoespaciais. Muitas aplicações móveis já proporcionam experiências no contexto de uma área geográfica. Com um suporte de primeira classe para [tipos geoespaciais](geospatial.md), o Azure Cosmos DB faz com que seja fácil criar estas experiências.
* Anexos binários. Normalmente, os dados da sua aplicação incluem blobs binários. O suporte nativo para anexos facilita a utilização do Azure Cosmos DB para centralizar os dados da sua aplicação.

## <a name="azure-cosmos-db-and-xamarin-tutorial"></a>Tutorial do Azure Cosmos DB e do Xamarin
O seguinte tutorial mostra como pode criar uma aplicação móvel com o Xamarin e o Azure Cosmos DB. O código fonte completo do tutorial encontra-se no [Xamarin e no Azure Cosmos DB no GitHub](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/xamarin).

### <a name="get-started"></a>Introdução
Começar a utilizar o Azure Cosmos DB é fácil. Aceda ao portal do Azure e crie uma nova conta do Azure Cosmos DB. Clique no separador **Início rápido**. Transfira o exemplo de lista de tarefas do Xamarin Forms que já está associado à sua conta do Azure Cosmos DB. 

![Início rápido do Azure Cosmos DB para aplicações móveis](media/mobile-apps-with-xamarin/cosmos-db-quickstart.png)

Em alternativa, se tiver uma aplicação Xamarin existente, pode adicionar o [pacote NuGet Azure Cosmos DB](sql-api-sdk-dotnet-core.md). O Azure Cosmos DB suporta o Xamarin.IOS, Xamarin.Android e bibliotecas partilhadas do Xamarin Forms.

### <a name="work-with-data"></a>Trabalhar com dados
Os seus registos de dados são armazenados no Azure Cosmos DB como documentos JSON sem esquemas em coleções heterogéneas. Pode armazenar documentos com diferentes estruturas na mesma coleção:

```cs
    var result = await client.CreateDocumentAsync(collectionLink, todoItem);
```

Nos seus projetos Xamarin, pode utilizar consultas em sintaxe LINQ (Language Integrated Query) em dados sem esquemas:

```cs
    var query = await client.CreateDocumentQuery<ToDoItem>(collectionLink)
                    .Where(todoItem => todoItem.Complete == false)
                    .AsDocumentQuery();

    Items = new List<TodoItem>();
    while (query.HasMoreResults) {
        Items.AddRange(await query.ExecuteNextAsync<TodoItem>());
    }
```
### <a name="add-users"></a>Adicionar utilizadores
Tal como muitos exemplos de introdução, o exemplo do Azure Cosmos DB que transferiu é autenticado no serviço através de uma chave mestra codificada no código da aplicação. Esta predefinição não é uma boa prática para uma aplicação que pretenda executar num lugar que não seja o seu emulador local. Se um utilizador não autorizado obtiver a chave mestra, todos os dados na sua conta do Azure Cosmos DB poderão estar comprometidos. Em vez disso, é recomendável que a sua aplicação aceda apenas aos registos do utilizador com sessão iniciada. O Azure Cosmos DB permite que os programadores concedam permissões de aplicação de leitura ou leitura/escrita a uma coleção, um conjunto de documentos agrupados por uma chave de partição ou um documento específico. 

Siga estes passos para mudar a aplicação de lista de tarefas para uma aplicação de lista de tarefas multiutilizador: 

  1. Adicione um Início de Sessão à sua aplicação através do Facebook, do Active Directory ou de outro fornecedor.

  2. Crie uma coleção UserItems do Azure Cosmos DB com a chave de partição **/userId**. Especificar a chave de partição da sua coleção permite que o Azure Cosmos DB se dimensione indefinidamente à medida que o número de utilizadores da aplicação aumenta e continue a proporcionar consultas rápidas.

  3. Adicione o Mediador de Tokens de Recurso do Azure Cosmos DB. Esta API Web simples efetua a autenticação dos utilizadores e emite tokens de curta duração para utilizadores com sessão iniciada que só têm acesso aos documentos existentes na sua partição. Neste exemplo, o Mediador de Tokens de Recurso está alojado no Serviço de Aplicações.

  4. Modifique a aplicação de forma a que seja autenticada pelo Mediador de Tokens de Recurso com o Facebook e peça os tokens de recurso dos utilizadores com sessão iniciada no Facebook. Em seguida, poderá aceder aos respetivos dados na coleção UserItems.  

Um exemplo de código completo deste padrão encontra-se no [Mediador de Tokens de Recurso no GitHub](https://aka.ms/documentdb-xamarin-todouser). Este diagrama ilustra a solução:

![Mediador de permissões e utilizadores do Azure Cosmos DB](media/mobile-apps-with-xamarin/documentdb-resource-token-broker.png)

Se quiser conceder a dois utilizadores acesso à mesma lista de tarefas, pode incluir permissões adicionais para o token de acesso no Mediador de Tokens de Recurso.

### <a name="scale-on-demand"></a>Dimensionar a pedido
O Azure Cosmos DB é uma base de dados como serviço gerida. À medida que a sua base de utilizadores cresce, não terá de se preocupar em aprovisionar VMs ou aumentar o número de núcleos. Com o Azure Cosmos DB, só precisa de indicar o número de operações por segundo (débito) necessário para a sua aplicação. Pode especificar o débito através do separador **Dimensionar** ao utilizar uma medida de débito denominada Unidades de Pedido (RUs) por segundo. Por exemplo, uma operação de leitura num documento de 1 KB requer 1 RU. Também pode adicionar alertas à métrica **Débito** para monitorizar o aumento de tráfego e alterar programaticamente o débito quando os alertas são acionados.

![Débito de dimensionamento a pedido do Azure Cosmos DB](media/mobile-apps-with-xamarin/cosmos-db-xamarin-scale.png)

### <a name="go-planet-scale"></a>Aumente a sua dimensão à escala global
À medida que a popularidade da sua aplicação cresce, poderá obter utilizadores de todo o mundo. Também poderá querer apenas preparar-se para eventos imprevistos. Aceda ao portal do Azure e abra a sua conta do Azure Cosmos DB. Clique no mapa para que os seus dados sejam replicados continuamente em várias regiões por todo o mundo. Esta funcionalidade torna os dados disponíveis aos seus utilizadores, onde quer que estejam. Também pode adicionar políticas de ativação pós-falha de forma a estar preparado para alguma eventualidade.

![Dimensionamento do Azure Cosmos DB em várias regiões geográficas](media/mobile-apps-with-xamarin/cosmos-db-xamarin-replicate.png)

Parabéns! Concluiu a solução e acabou de criar uma aplicação móvel com o Xamarin e o Azure Cosmos DB. Siga passos semelhantes para criar aplicações Cordova com o JavaScript SDK do Azure Cosmos DB e aplicações nativas iOS/Android com APIs REST do Azure Cosmos DB.

## <a name="next-steps"></a>Passos Seguintes
* Ver o código fonte do [Xamarin e do Azure Cosmos DB no GitHub](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/xamarin).
* Transferir o [SDK de .NET Core do Azure Cosmos DB](sql-api-sdk-dotnet-core.md).
* Encontrar mais exemplos de código para [aplicações .NET](sql-api-dotnet-samples.md).
* Saiba mais sobre as [funcionalidades de consulta avançada do Azure Cosmos DB](how-to-sql-query.md).
* Saiba mais sobre o [suporte geoespacial no Azure Cosmos DB](geospatial.md).



