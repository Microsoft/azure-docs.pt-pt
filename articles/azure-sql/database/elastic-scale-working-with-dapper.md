---
title: Usando a biblioteca de clientes de base de dados elástica com Dapper
description: Usando a biblioteca de clientes de base de dados elástica com Dapper.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/04/2018
ms.openlocfilehash: d660e62ea293bd3cc377b95612cfaf41a9f1cd6a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92793369"
---
# <a name="using-the-elastic-database-client-library-with-dapper"></a>Usando a biblioteca de clientes de base de dados elástica com Dapper
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Este documento destina-se a programadores que dependem da Dapper para construir aplicações, mas também querem abraçar [ferramentas de base de dados elásticas](elastic-scale-introduction.md) para criar aplicações que implementem o sharding para escalar o seu nível de dados.  Este documento ilustra as alterações nas aplicações baseadas em Dapper que são necessárias para integrar com ferramentas de base de dados elásticas. O nosso foco é compor a gestão elástica de fragmentos de base de dados e o encaminhamento dependente de dados com o Dapper. 

**Código de amostra**: [Ferramentas elásticas de base de dados para Azure SQL Database - Integração da Dapper](https://code.msdn.microsoft.com/Elastic-Scale-with-Azure-e19fc77f).

A integração **da Dapper** e **da dapperExtensions** com a biblioteca de clientes de base de dados elástica para a Base de Dados Azure SQL é fácil. As suas aplicações podem utilizar o encaminhamento dependente de dados alterando a criação e abertura de novos objetos [SqlConnection](/dotnet/api/system.data.sqlclient.sqlconnection) para utilizar a chamada [OpenConnectionForKey](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1) da biblioteca do [cliente](/previous-versions/azure/dn765902(v=azure.100)). Isto limita as alterações na sua aplicação apenas onde novas ligações são criadas e abertas. 

## <a name="dapper-overview"></a>Visão geral dapper
**Dapper** é um mapper relacional de objetos. Mapeia objetos .NET da sua aplicação para uma base de dados relacional (e vice-versa). A primeira parte do código de amostra ilustra como pode integrar a biblioteca de clientes de base de dados elástica com aplicações baseadas em Dapper. A segunda parte do código de amostra ilustra como integrar-se quando se utiliza astensões Dapper e DapperExtensions.  

A funcionalidade mapper em Dapper fornece métodos de extensão em ligações de base de dados que simplificam a apresentação de declarações T-SQL para execução ou consulta da base de dados. Por exemplo, o Dapper facilita o mapa entre os seus objetos .NET e os parâmetros das declarações SQL para chamadas **Executadas,** ou para consumir os resultados das suas consultas SQL em objetos .NET utilizando chamadas **de consulta** da Dapper. 

Ao utilizar as detensões DapperExtensions, já não precisa de fornecer as declarações SQL. Métodos de extensões como **GetList** ou **Insert** over the database connection criam as declarações SQL nos bastidores.

Outro benefício da Dapper e também da DapperExtensions é que a aplicação controla a criação da ligação de base de dados. Isto ajuda a interagir com a biblioteca de clientes de base de dados elástica que intermedia ligações de base de dados com base no mapeamento de shardlets para bases de dados.

Para obter as assembléias Dapper, consulte [a rede da dapper dot.](https://www.nuget.org/packages/Dapper/) Para as extensões Dapper, consulte [DapperExtensions](https://www.nuget.org/packages/DapperExtensions).

## <a name="a-quick-look-at-the-elastic-database-client-library"></a>Uma olhada rápida na biblioteca de clientes de base de dados elástica
Com a biblioteca de clientes de base de dados elástica, define as divisórias dos dados da sua aplicação chamadas *shardlets,* mapeia-as para bases de dados e identifica-as através *de chaves de caco.* Pode ter todas as bases de dados que precisar e distribuir os seus fragmentos por estas bases de dados. O mapeamento dos valores-chave de fragmentos nas bases de dados é armazenado por um mapa de fragmentos fornecido pelas APIs da biblioteca. Esta capacidade chama-se **gestão de mapas de fragmentos.** O mapa de fragmentos também serve como corretor de ligações de base de dados para pedidos que carregam uma chave de fragmentos. Esta capacidade é referida como **encaminhamento dependente de dados**.

![Mapas de fragmentos e encaminhamento dependente de dados][1]

O gestor de mapas de fragmentos protege os utilizadores de visões inconsistentes em dados de shardlet que podem ocorrer quando operações de gestão de fragmentos simultâneas estão acontecendo nas bases de dados. Para tal, o corretor de mapas de fragmentos as ligações de base de dados para uma aplicação construída com a biblioteca. Quando as operações de gestão de fragmentos podem ter impacto no shardlet, isto permite que a funcionalidade do mapa de fragmentos mate automaticamente uma ligação de base de dados. 

Em vez de utilizar a forma tradicional de criar ligações para o Dapper, é necessário utilizar o [método OpenConnectionForKey](/previous-versions/azure/dn824099(v=azure.100)). Isto garante que toda a validação ocorre e as ligações são geridas corretamente quando qualquer dado se move entre fragmentos.

### <a name="requirements-for-dapper-integration"></a>Requisitos para a integração da Dapper
Ao trabalhar com a biblioteca de clientes de base de dados elástica e as APIs da Dapper, pretende reter as seguintes propriedades:

* **Escala**: Queremos adicionar ou remover bases de dados do nível de dados da aplicação de fragmentos, conforme necessário para as exigências de capacidade da aplicação. 
* **Consistência**: Uma vez que a aplicação é dimensionada utilizando o fragmento, é necessário efetuar o encaminhamento dependente de dados. Queremos usar as capacidades de encaminhamento dependentes de dados da biblioteca para o fazer. Em particular, pretende manter as garantias de validação e consistência fornecidas por ligações que são intermediadas através do gestor de mapas de fragmentos, de forma a evitar corrupção ou resultados de consulta erradas. Isto garante que as ligações a um determinado fragmento são rejeitadas ou paradas se (por exemplo) o fragmento for atualmente movido para um fragmento diferente utilizando APIs split/merge.
* **Apeamento de objetos**: Queremos manter a conveniência dos mapeamentos fornecidos pela Dapper para traduzir entre classes na aplicação e as estruturas de base de dados subjacentes. 

A secção seguinte fornece orientações para estes requisitos para aplicações baseadas em **Dapper** e **DapperExtensions**.

## <a name="technical-guidance"></a>Orientação técnica
### <a name="data-dependent-routing-with-dapper"></a>Encaminhamento dependente de dados com Dapper
Com a Dapper, a aplicação é normalmente responsável pela criação e abertura das ligações à base de dados subjacente. Dado um tipo T pela aplicação, dapper devolve resultados de consulta como .NET coleções do tipo T. Dapper executa o mapeamento das linhas de resultado T-SQL para os objetos do tipo T. Da mesma forma, dapper mapeia .NET objetos em valores SQL ou parâmetros para declarações de linguagem de manipulação de dados (DML). A Dapper oferece esta funcionalidade através de métodos de extensão no objeto [SqlConnection](/dotnet/api/system.data.sqlclient.sqlconnection) regular das bibliotecas do Cliente ADO .NET SQL. A ligação SQL devolvida pelas APIs de Escala Elástica para DDR também são objetos [sqlConnection](/dotnet/api/system.data.sqlclient.sqlconnection) regulares. Isto permite-nos utilizar diretamente extensões Dapper sobre o tipo devolvido pela DDR API da biblioteca cliente, uma vez que é também uma simples ligação ao Cliente SQL.

Estas observações tornam simples a utilização de ligações intermediadas pela biblioteca de clientes de base de dados elástica para a Dapper.

Este exemplo de código (da amostra de acompanhamento) ilustra a abordagem em que a chave de fragmentos é fornecida pela aplicação à biblioteca para intermediar a ligação ao fragmento certo.   

```csharp
    using (SqlConnection sqlconn = shardingLayer.ShardMap.OpenConnectionForKey(
                     key: tenantId1,
                     connectionString: connStrBldr.ConnectionString,
                     options: ConnectionOptions.Validate))
    {
        var blog = new Blog { Name = name };
        sqlconn.Execute(@"
                      INSERT INTO
                            Blog (Name)
                            VALUES (@name)", new { name = blog.Name }
                        );
    }
```

A chamada para a [API OpenConnectionForKey](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1) substitui a criação e abertura padrão de uma ligação ao Cliente SQL. A chamada [OpenConnectionForKey](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1) aceita os argumentos necessários para o encaminhamento dependente de dados: 

* O mapa de fragmentos para aceder às interfaces de encaminhamento dependentes de dados
* A chave de fragmentos para identificar o fragmento
* As credenciais (nome de utilizador e palavra-passe) para ligar ao fragmento

O objeto do mapa de fragmentos cria uma ligação ao fragmento que segura o fragmento para a chave de fragmentos dada. As APIs do cliente de base de dados elástica também marcam a ligação para implementar as suas garantias de consistência. Uma vez que a chamada para [OpenConnectionForKey](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1) devolve um objeto de conexão regular do Cliente SQL, a chamada subsequente para o método de extensão **Executar** da Dapper segue a prática padrão da Dapper.

As consultas funcionam da mesma forma - primeiro abre a ligação utilizando o [OpenConnectionForKey](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1) da API do cliente. Em seguida, utilize os métodos de extensão Dapper regulares para mapear os resultados da sua consulta SQL em objetos .NET:

```csharp
    using (SqlConnection sqlconn = shardingLayer.ShardMap.OpenConnectionForKey(
                    key: tenantId1,
                    connectionString: connStrBldr.ConnectionString,
                    options: ConnectionOptions.Validate ))
    {
           // Display all Blogs for tenant 1
           IEnumerable<Blog> result = sqlconn.Query<Blog>(@"
                                SELECT *
                                FROM Blog
                                ORDER BY Name");

           Console.WriteLine("All blogs for tenant id {0}:", tenantId1);
           foreach (var item in result)
           {
                Console.WriteLine(item.Name);
            }
    }
```

Note que o bloco **de utilização** com a ligação DDR scope todas as operações de base de dados dentro do bloco para o fragmento onde o inquilinoId1 é mantido. A consulta apenas retorna blogs armazenados no fragmento atual, mas não os armazenados em quaisquer outros fragmentos. 

## <a name="data-dependent-routing-with-dapper-and-dapperextensions"></a>Encaminhamento dependente de dados com Dapper e DapperExtensions
Dapper vem com um ecossistema de extensões adicionais que podem fornecer mais comodidade e abstração da base de dados ao desenvolver aplicações de base de dados. DapperExtensions é um exemplo. 

A utilização de dapperExtensions na sua aplicação não altera a forma como as ligações de base de dados são criadas e geridas. É ainda da responsabilidade da aplicação abrir ligações, e os objetos regulares de ligação do Cliente SQL são esperados pelos métodos de extensão. Podemos confiar no [OpenConnectionForKey](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1) como descrito acima. Como mostram as seguintes amostras de código, a única alteração é que já não tem de escrever as declarações T-SQL:

```csharp
    using (SqlConnection sqlconn = shardingLayer.ShardMap.OpenConnectionForKey(
                    key: tenantId2,
                    connectionString: connStrBldr.ConnectionString,
                    options: ConnectionOptions.Validate))
    {
           var blog = new Blog { Name = name2 };
           sqlconn.Insert(blog);
    }
```

E aqui está a amostra de código para a consulta: 

```csharp
    using (SqlConnection sqlconn = shardingLayer.ShardMap.OpenConnectionForKey(
                    key: tenantId2,
                    connectionString: connStrBldr.ConnectionString,
                    options: ConnectionOptions.Validate))
    {
           // Display all Blogs for tenant 2
           IEnumerable<Blog> result = sqlconn.GetList<Blog>();
           Console.WriteLine("All blogs for tenant id {0}:", tenantId2);
           foreach (var item in result)
           {
               Console.WriteLine(item.Name);
           }
    }
```

### <a name="handling-transient-faults"></a>Tratamento de falhas transitórias
A equipa de Práticas & de Padrões microsoft publicou o [Bloco de Aplicações de Tratamento de Falhas Transitórias](/previous-versions/msp-n-p/hh680934(v=pandp.50)) para ajudar os desenvolvedores de aplicações a mitigar as condições de falha transitórias comuns encontradas ao correr na nuvem. Para obter mais informações, consulte [Perseverance, Secret of All Triumphs: Using the Transient Fault Handling Application Block](/previous-versions/msp-n-p/dn440719(v=pandp.60)).

A amostra de código baseia-se na biblioteca de avarias transitórias para proteger contra falhas transitórias. 

```csharp
    SqlDatabaseUtils.SqlRetryPolicy.ExecuteAction(() =>
    {
       using (SqlConnection sqlconn =
          shardingLayer.ShardMap.OpenConnectionForKey(tenantId2, connStrBldr.ConnectionString, ConnectionOptions.Validate))
          {
              var blog = new Blog { Name = name2 };
              sqlconn.Insert(blog);
          }
    });
```

**SqlDatabaseUtils.SqlRetryPolicy** no código acima é definido como um **SqlDataTransientErrorDetectionStrategy** com uma contagem de 10 e 5 segundos de espera entre retréis. Se estiver a utilizar transações, certifique-se de que o seu âmbito de retíria remonta ao início da transação em caso de falha transitória.

## <a name="limitations"></a>Limitações
As abordagens descritas neste documento implicam algumas limitações:

* O código de amostra deste documento não demonstra como gerir o esquema através de fragmentos.
* Dado um pedido, assumimos que todo o seu processamento de base de dados está contido num único fragmento identificado pela chave de fragmentos fornecida pelo pedido. No entanto, esta suposição nem sempre se mantém, por exemplo, quando não é possível disponibilizar uma chave de fragmentos. Para resolver este problema, a biblioteca de clientes de base de dados elástica inclui a [classe MultiShardQuery.](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.query.multishardexception) A classe implementa uma abstração de conexão para consulta sobre vários fragmentos. A utilização do MultiShardQuery em combinação com o Dapper está fora do âmbito deste documento.

## <a name="conclusion"></a>Conclusão
As aplicações que utilizam as dapper e as dapperExtensions podem facilmente beneficiar de ferramentas elásticas de base de dados para a Base de Dados Azure SQL. Através dos passos descritos neste documento, estas aplicações podem utilizar a capacidade da ferramenta para encaminhamento dependente de dados, alterando a criação e abertura de novos objetos [SqlConnection](/dotnet/api/system.data.sqlclient.sqlconnection) para utilizar a chamada [OpenConnectionForKey](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1) da biblioteca de clientes de base de dados elástica. Isto limita as alterações de aplicação necessárias aos locais onde são criadas e abertas novas ligações. 

[!INCLUDE [elastic-scale-include](../../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/elastic-scale-working-with-dapper/dapperimage1.png