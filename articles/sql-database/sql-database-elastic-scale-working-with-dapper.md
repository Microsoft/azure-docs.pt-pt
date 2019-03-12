---
title: Utilizar a biblioteca de clientes de bases de dados elásticas com o Dapper | Documentos da Microsoft
description: Utilizar a biblioteca de clientes de bases de dados elásticas com o Dapper.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 12/04/2018
ms.openlocfilehash: 6cc5e3f8f188c60a129f6ad6575b348616bdad9b
ms.sourcegitcommit: dd1a9f38c69954f15ff5c166e456fda37ae1cdf2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57569761"
---
# <a name="using-elastic-database-client-library-with-dapper"></a>Utilizar a biblioteca de clientes de bases de dados elásticas com o Dapper
Este documento é para desenvolvedores que contam com o Dapper a criação de aplicativos, mas também querem adotar [ferramentas de bases de dados elásticas](sql-database-elastic-scale-introduction.md) para criar aplicativos que implementam a fragmentação para aumentar horizontalmente as camadas de dados.  Este documento ilustra as alterações nos aplicativos baseados no Dapper que são necessários para integrar em ferramentas de bases de dados elásticas. É nosso foco irá incidir na composição da gestão de partições horizontais de bases de dados elásticas e encaminhamento dependente de dados com o Dapper. 

**Código de exemplo**: [Ferramentas de bases de dados elásticas para o Azure SQL Database - integração o Dapper](https://code.msdn.microsoft.com/Elastic-Scale-with-Azure-e19fc77f).

Integrando **o Dapper** e **DapperExtensions** com a base de dados elástica biblioteca de clientes para a base de dados SQL do Azure é fácil. Seus aplicativos podem usar o encaminhamento dependente de dados, alterando a criação e a abertura de novas [SqlConnection](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection.aspx) objetos para utilizar o [OpenConnectionForKey](https://msdn.microsoft.com/library/azure/dn807226.aspx) chamar a partir do [biblioteca de cliente ](https://msdn.microsoft.com/library/azure/dn765902.aspx). Isso limita as alterações na sua aplicação apenas para onde novas ligações são criadas e abertas. 

## <a name="dapper-overview"></a>Visão geral o dapper
**O dapper** é um mapeador relacional de objetos. Ele mapeia os objetos de .NET a partir da sua aplicação para uma base de dados relacional (e vice-versa). A primeira parte do código de exemplo ilustra como integrar a biblioteca de clientes de bases de dados elásticas com o Dapper com base em aplicações. A segunda parte do código de exemplo ilustra como integrar ao utilizar o Dapper e DapperExtensions.  

A funcionalidade de mapeador de pontos no Dapper fornece métodos de extensão em ligações de base de dados que simplificam a submeter instruções T-SQL para execução ou consultar a base de dados. Por exemplo, o Dapper torna mais fácil para mapeamento entre objetos .NET e os parâmetros de instruções SQL para **Execute** chamadas, ou para consumir os resultados das consultas do SQL em objetos .NET usando **consulta** chamadas do Dapper. 

Quando utilizar DapperExtensions, já não terá de fornecer as instruções SQL. Métodos de extensões, como **GetList** ou **inserir** através da ligação de base de dados criar instruções SQL em segundo plano.

Outra vantagem do Dapper e também DapperExtensions é que a aplicação controla a criação da ligação da base de dados. Isto ajuda a interagir com a biblioteca de cliente de base de dados elástica que mediadores de ligações com base no mapeamento de shardlets às bases de dados de base de dados.

Para obter os assemblies o Dapper, consulte [o Dapper ponto net](http://www.nuget.org/packages/Dapper/). Para o Dapper extensões, consulte [DapperExtensions](http://www.nuget.org/packages/DapperExtensions).

## <a name="a-quick-look-at-the-elastic-database-client-library"></a>Uma vista rápida da biblioteca de clientes de bases de dados elásticas
Com a biblioteca de cliente da base de dados elástica, define as partições de dados da sua aplicação chamados *shardlets*mapeá-los para bases de dados e identificá-los por *chaves de fragmentação*. Pode ter tantas bases de dados à medida que precisa e distribuir os shardlets por esses bancos de dados. O mapeamento de valores de chave de fragmentação às bases de dados é armazenado por um mapa de partições horizontais fornecido pelas APIs da biblioteca. Esse recurso é chamado **gestão de mapas de partições horizontais**. O mapa de partições horizontais também serve como o Mediador de ligações de base de dados para pedidos que vão ser uma chave de fragmentação. Esta capacidade é referida como **encaminhamento dependente de dados**.

![Mapas de partições horizontais e encaminhamento dependente de dados][1]

O Gestor de mapas de partições horizontais protege os usuários de vistas inconsistentes sobre os dados de shardlet que podem ocorrer quando as operações de gestão em simultâneo shardlet estão acontecendo nas bases de dados. Para fazer isso, os mapas de partições horizontais mediador as ligações de base de dados para um aplicativo criado com a biblioteca. Quando as operações de gestão de partições horizontais podem afetar o shardlet, isso permite que a funcionalidade de mapa de partições horizontais eliminar automaticamente uma ligação de base de dados. 

Em vez de usar a maneira tradicional de criar ligações para o Dapper, tem de utilizar o [OpenConnectionForKey método](https://msdn.microsoft.com/library/azure/dn824099.aspx). Isto garante que todos os a validação ocorre e ligações são corretamente geridas quando se move todos os dados entre partições horizontais.

### <a name="requirements-for-dapper-integration"></a>Requisitos para integração com o Dapper
Ao trabalhar com a biblioteca de clientes de bases de dados elásticas e as APIs o Dapper, em que pretenda manter as seguintes propriedades:

* **Aumentar horizontalmente**: Queremos adicionar ou remover bases de dados da camada de dados da aplicação em partição horizontal, conforme necessário para as necessidades de capacidade do aplicativo. 
* **Consistência**: Uma vez que a aplicação está aumentada horizontalmente com a fragmentação, terá de efetuar o encaminhamento dependente de dados. Queremos usar recursos de roteamento dependente de dados da biblioteca para fazer isso. Em particular, que pretenda manter a validação e garante a consistência fornecida pelo ligações mediadas por meio do Gestor de mapas de partições horizontais para evitar corrupção ou os resultados da consulta errado. Isto garante que as ligações a um shardlet específico são rejeitadas ou interrompidas se (por exemplo) a shardlet atualmente é movido para das partições horizontais diferentes com APIs de dividir/unir.
* **Mapeamento de objeto**: Queremos manter a conveniência dos mapeamentos fornecida pelo Dapper traduzir entre as classes no aplicativo e as estruturas de base de dados subjacente. 

A secção seguinte fornece orientações para esses requisitos para aplicativos baseados no **o Dapper** e **DapperExtensions**.

## <a name="technical-guidance"></a>Orientações técnicas
### <a name="data-dependent-routing-with-dapper"></a>Encaminhamento dependente de dados com o Dapper
Com o Dapper, o aplicativo é, normalmente, responsável por criar e abrir as ligações à base de dados subjacente. Tendo em conta um tipo T pela aplicação, o Dapper devolve os resultados da consulta como coleções de .NET do tipo o Dapper T. efetua o mapeamento das linhas de resultado de T-SQL para os objetos do tipo T. Da mesma forma, o Dapper mapeia os objetos .NET em valores SQL ou parâmetros para instruções de (DML linguagem) de manipulação de dados. O Dapper oferece essa funcionalidade por meio de métodos de extensão no regular [SqlConnection](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection.aspx) objeto a partir das bibliotecas de cliente de SQL do ADO .NET. A ligação de SQL devolvida pelas APIs de dimensionamento elástico para DDR também são regular [SqlConnection](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection.aspx) objetos. Isto permite-nos utilizar diretamente o Dapper extensões sobre o tipo devolvido pela API de DDR da biblioteca de cliente, como também é uma ligação de cliente de SQL simple.

Essas observações facilitam a utilização de ligações mediadas pela biblioteca de clientes de bases de dados elásticas para o Dapper.

Este exemplo de código (do exemplo que acompanha este artigo) ilustra a abordagem em que a chave de fragmentação é fornecida pela aplicação para a biblioteca como o mediador a ligação para a partição horizontal certa.   

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

A chamada para o [OpenConnectionForKey](https://msdn.microsoft.com/library/azure/dn807226.aspx) API substitui a criação de padrão e a abertura de uma ligação de cliente de SQL. O [OpenConnectionForKey](https://msdn.microsoft.com/library/azure/dn807226.aspx) chamada aceita os argumentos que são necessários para encaminhamento dependente de dados: 

* O mapa de partições horizontais para acessar as interfaces de encaminhamento dependente de dados
* A chave de fragmentação para identificar o shardlet
* As credenciais (nome de utilizador e palavra-passe) para ligar à partição horizontal

O objeto de mapa de partições horizontais cria uma ligação para a partição horizontal que contém o shardlet para a chave de fragmentação especificado. APIs de cliente da base de dados elásticas também marcar a ligação ao implementar suas garantias de consistência. A chamada para [OpenConnectionForKey](https://msdn.microsoft.com/library/azure/dn807226.aspx) retorna um objeto de ligação de cliente SQL regular, a chamada subsequente para o **Execute** método de extensão o Dapper segue a prática do Dapper padrão.

As consultas funcionam muito da mesma forma – a primeira vez que abrir a ligação utilizando [OpenConnectionForKey](https://msdn.microsoft.com/library/azure/dn807226.aspx) do cliente de API. Em seguida, utilize os métodos de extensão o Dapper regulares para mapear os resultados da consulta SQL em objetos .NET:

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

Tenha em atenção que o **usando** bloquear com os âmbitos de ligação de DDR de todas as operações de base de dados dentro do bloco de numa partição onde tenantId1 é mantida. A consulta devolve apenas blogs armazenados na partição horizontal atual, mas não os arquivos armazenados em quaisquer outras partições horizontais. 

## <a name="data-dependent-routing-with-dapper-and-dapperextensions"></a>Encaminhamento dependente de dados com o Dapper e DapperExtensions
O Dapper vem com um ecossistema de extensões adicionais que podem fornecer mais conveniência e a abstração da base de dados ao desenvolver aplicativos de banco de dados. DapperExtensions é um exemplo. 

Usando DapperExtensions em seu aplicativo não muda como ligações de base de dados são criadas e geridas. Ainda é responsabilidade da aplicação para abrir ligações e objetos de conexão do SQL Client regulares esperados pelos métodos de extensão. Podemos contar com o [OpenConnectionForKey](https://msdn.microsoft.com/library/azure/dn807226.aspx) conforme descrito acima. Como mostram os exemplos de código seguinte, a única alteração é que já não tem de escrever as instruções T-SQL:

    using (SqlConnection sqlconn = shardingLayer.ShardMap.OpenConnectionForKey(
                    key: tenantId2,
                    connectionString: connStrBldr.ConnectionString,
                    options: ConnectionOptions.Validate))
    {
           var blog = new Blog { Name = name2 };
           sqlconn.Insert(blog);
    }

E aqui está o código de exemplo para a consulta: 

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

### <a name="handling-transient-faults"></a>Processamento de falhas transitórias
A Microsoft Patterns & Practices do azurecat publicou a [Transient Fault Handling Application Block](https://msdn.microsoft.com/library/hh680934.aspx) para ajudar a atenuar as condições de falhas transitórias comuns encontradas durante a execução na cloud de desenvolvedores de aplicativos. Para obter mais informações, consulte [Perseverance, segredo de todos os Triumphs: Usando o bloco de aplicativo de processamento de falhas transitórias](https://msdn.microsoft.com/library/dn440719.aspx).

O código de exemplo se baseia na biblioteca de falhas transitórias para proteger contra falhas transitórias. 

    SqlDatabaseUtils.SqlRetryPolicy.ExecuteAction(() =>
    {
       using (SqlConnection sqlconn =
          shardingLayer.ShardMap.OpenConnectionForKey(tenantId2, connStrBldr.ConnectionString, ConnectionOptions.Validate))
          {
              var blog = new Blog { Name = name2 };
              sqlconn.Insert(blog);
          }
    });

**SqlDatabaseUtils.SqlRetryPolicy** no código acima é definido como um **SqlDatabaseTransientErrorDetectionStrategy** com uma contagem de repetições de 10 e 5 segundos de tempo entre tentativas de espera. Se estiver a utilizar transações, certifique-se de que o âmbito de repetição tem a ver com o início da transação no caso de falhas transitórias.

## <a name="limitations"></a>Limitações
As abordagens descritas neste documento envolve algumas limitações:

* O código de exemplo para este documento não demonstre como gerir o esquema em partições horizontais.
* Devido um pedido, partimos do princípio de que todos os seu processamento de base de dados se encontra numa única partição horizontal, conforme identificado pela chave de fragmentação fornecida pelo pedido. No entanto, essa suposição não sempre contém, por exemplo, quando não é possível disponibilizar uma chave de fragmentação. Para resolver isso, a biblioteca de cliente de base de dados elástica inclui a [MultiShardQuery classe](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.multishardexception.aspx). A classe implementa uma abstração de ligação para consultar o ao longo de vários shards. Utilizar MultiShardQuery em combinação com o Dapper está além do escopo deste documento.

## <a name="conclusion"></a>Conclusão
Aplicações com o Dapper e DapperExtensions podem se beneficie facilmente a partir das ferramentas de bases de dados elásticas da base de dados do Azure SQL. Os passos descritos neste documento, esses aplicativos podem usar a capacidade da ferramenta para encaminhamento dependente de dados, alterando a criação e a abertura de novas [SqlConnection](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection.aspx) objetos para utilizar o [ OpenConnectionForKey](https://msdn.microsoft.com/library/azure/dn807226.aspx) chamada da biblioteca de clientes de bases de dados elásticas. Isso limita as alterações de aplicação necessárias para esses locais em que as novas ligações são criadas e abertas. 

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-working-with-dapper/dapperimage1.png
