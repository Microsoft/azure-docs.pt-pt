---
title: Usando a biblioteca de clientes de base de dados elástica com Dapper
description: Usando a biblioteca de clientes de base de dados elástica com dapper.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/04/2018
ms.openlocfilehash: 95723bbcfc5573567bee4a433b9d33908b91f5f0
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84045250"
---
# <a name="using-the-elastic-database-client-library-with-dapper"></a>Usando a biblioteca de clientes de base de dados elástica com Dapper
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Este documento destina-se a programadores que dependem da Dapper para construir aplicações, mas também querem abraçar ferramentas [elásticas](elastic-scale-introduction.md) de base de dados para criar aplicações que implementem sharding para escalar o seu nível de dados.  Este documento ilustra as alterações nas aplicações baseadas em Dapper que são necessárias para integrar com ferramentas de base de dados elásticas. O nosso foco é compor a gestão de bases de dados elásticas e o encaminhamento dependente de dados com o Dapper. 

**Código da amostra**: Ferramentas de base de dados elásticas para base de [dados Azure SQL - Integração Dapper](https://code.msdn.microsoft.com/Elastic-Scale-with-Azure-e19fc77f).

Integrar **dapper** e **dapperExtensions** com a biblioteca de clientes de base de dados elástica para base de dados Azure SQL é fácil. As suas aplicações podem utilizar o encaminhamento dependente de dados alterando a criação e abertura de novos objetos [SqlConnection](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection.aspx) para utilizar a chamada [OpenConnectionForKey](https://msdn.microsoft.com/library/azure/dn807226.aspx) da biblioteca do [cliente](https://msdn.microsoft.com/library/azure/dn765902.aspx). Isto limita as alterações na sua aplicação apenas para onde novas ligações são criadas e abertas. 

## <a name="dapper-overview"></a>Visão geral de Dapper
**Dapper** é um mapeador relacional de objetos. Mapeia objetos .NET da sua aplicação para uma base de dados relacional (e vice-versa). A primeira parte do código da amostra ilustra como pode integrar a biblioteca de clientes de base de dados elástica com aplicações baseadas em Dapper. A segunda parte do código da amostra ilustra como se integrar ao utilizar tanto as Extensões Dapper como dapper.  

A funcionalidade mapper em Dapper fornece métodos de extensão em ligações de base de dados que simplificam a apresentação de declarações T-SQL para execução ou consulta da base de dados. Por exemplo, dapper torna fácil mapear entre os seus objetos .NET e os parâmetros das declarações SQL para **executar** chamadas, ou consumir os resultados das suas consultas SQL em objetos .NET usando chamadas **de Consulta** de Dapper. 

Ao utilizar as Extensões Dapper, já não precisa de fornecer as declarações SQL. Métodos de extensões como **GetList** ou **Insert** sobre a ligação à base de dados criam as declarações SQL nos bastidores.

Outro benefício da Dapper e também da DapperExtensions é que a aplicação controla a criação da ligação à base de dados. Isto ajuda a interagir com a biblioteca de clientes de base de dados elástica que intermedia ligações de base de dados com base no mapeamento de fragmentos para bases de dados.

Para obter os conjuntos Dapper, consulte [dapper dot net](https://www.nuget.org/packages/Dapper/). Para as extensões Dapper, consulte [DapperExtensions](https://www.nuget.org/packages/DapperExtensions).

## <a name="a-quick-look-at-the-elastic-database-client-library"></a>Uma olhada rápida na biblioteca de clientes de base de dados elástica
Com a biblioteca de clientes de base de dados elástica, define divisórias dos dados da sua aplicação chamadas *fragmentos,* mapeie-os para bases de dados e identifique-os por *teclas sharding*. Pode ter as bases de dados necessárias e distribuir os seus fragmentos através destas bases de dados. O mapeamento de valores-chave para as bases de dados é armazenado por um mapa de fragmentos fornecido pelas APIs da biblioteca. Esta capacidade chama-se gestão de **mapas de fragmentos.** O mapa do fragmento também serve como corretor de ligações de base de dados para pedidos que carregam uma chave de sharding. Esta capacidade é referida como **encaminhamento dependente de dados**.

![Mapas de fragmentos e encaminhamento dependente de dados][1]

O gestor de mapas de fragmentos protege os utilizadores de visões inconsistentes em dados de fragmentos que podem ocorrer quando as operações de gestão de fragmentos simultâneos estão a ocorrer nas bases de dados. Para tal, o fragmento mapeia as ligações da base de dados para uma aplicação construída com a biblioteca. Quando as operações de gestão de fragmentos podem ter impacto no fragmento, isto permite que a funcionalidade do mapa do fragmento mate automaticamente uma ligação de base de dados. 

Em vez de utilizar a forma tradicional de criar ligações para dapper, precisa de usar o [método OpenConnectionForKey](https://msdn.microsoft.com/library/azure/dn824099.aspx). Isto garante que toda a validação ocorre e as ligações são geridas corretamente quando qualquer data se move entre fragmentos.

### <a name="requirements-for-dapper-integration"></a>Requisitos para a integração dapper
Ao trabalhar tanto com a biblioteca de clientes de base de dados elástica como com as APIs Dapper, pretende reter as seguintes propriedades:

* **Escala para fora**: Queremos adicionar ou remover bases de dados do nível de dados da aplicação fragmento, conforme necessário para as exigências de capacidade da aplicação. 
* **Consistência**: Uma vez que a aplicação é dimensionada utilizando sharding, é necessário efetuar o encaminhamento dependente de dados. Queremos usar as capacidades de encaminhamento dependente de dados da biblioteca para o fazer. Em particular, pretende manter as garantias de validação e consistência fornecidas pelas ligações que são intermediadas através do gestor de mapas de fragmentos, a fim de evitar corrupção ou resultados de consulta erradas. Isto garante que as ligações a um dado fragmento são rejeitadas ou paradas se (por exemplo) o fragmento for atualmente movido para um fragmento diferente utilizando APIs split/merge.
* **Mapeamento de Objetos**: Queremos manter a conveniência dos mapeamentos fornecidos pela Dapper para traduzir entre classes na aplicação e as estruturas de base de dados subjacentes. 

A secção seguinte fornece orientações para estes requisitos para aplicações baseadas em **Dapper** e **DapperExtensions**.

## <a name="technical-guidance"></a>Orientação técnica
### <a name="data-dependent-routing-with-dapper"></a>Encaminhamento dependente de dados com Dapper
Com a Dapper, a aplicação é tipicamente responsável por criar e abrir as ligações à base de dados subjacente. Dado um tipo T pela aplicação, dapper devolve resultados de consulta como .NET coleções do tipo T. Dapper executa o mapeamento das linhas de resultados T-SQL para os objetos do tipo T. Da mesma forma, dapper mapeia .NET objetos em valores SQL ou parâmetros para declarações de linguagem de manipulação de dados (DML). A Dapper oferece esta funcionalidade através de métodos de extensão no objeto [sqlConnection](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection.aspx) regular das bibliotecas do Cliente ADO .NET SQL. A ligação SQL devolvida pelas APIs de escala elástica para DDR são também objetos [Regulares de Ligação Sql.](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection.aspx) Isto permite-nos utilizar diretamente as extensões Dapper sobre o tipo devolvido pela DDR API da biblioteca do cliente, uma vez que é também uma simples ligação ao Cliente SQL.

Estas observações tornam simples a utilização de ligações intermediadas pela biblioteca de clientes de base de dados elástica para a Dapper.

Este exemplo de código (da amostra que o acompanha) ilustra a abordagem em que a chave de sharding é fornecida pela aplicação à biblioteca para intermediar a ligação ao fragmento direito.   

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

A chamada para o [OpenConnectionForKey](https://msdn.microsoft.com/library/azure/dn807226.aspx) API substitui a criação padrão e a abertura de uma ligação ao Cliente SQL. A chamada [OpenConnectionForKey](https://msdn.microsoft.com/library/azure/dn807226.aspx) leva os argumentos necessários para o encaminhamento dependente de dados: 

* O mapa do fragmento para aceder às interfaces de encaminhamento dependente de dados
* A chave sharding para identificar o fragmento
* As credenciais (nome de utilizador e palavra-passe) para ligar ao fragmento

O objeto do mapa do fragmento cria uma ligação ao fragmento que contém o fragmento para a chave de sharding dada. As APIs do cliente de base de dados elástica também marcam a ligação para implementar as suas garantias de consistência. Uma vez que a chamada para [OpenConnectionForKey](https://msdn.microsoft.com/library/azure/dn807226.aspx) devolve um objeto regular de ligação ao Cliente SQL, a chamada subsequente para o método de extensão **executar** da Dapper segue a prática padrão dapper.

As consultas funcionam da mesma forma - abre-se primeiro a ligação utilizando o [OpenConnectionForKey](https://msdn.microsoft.com/library/azure/dn807226.aspx) da API do cliente. Em seguida, utiliza os métodos regulares de extensão dapper para mapear os resultados da sua consulta SQL em objetos .NET:

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

Note que o bloco de **utilização** com a ligação DDR aplica todas as operações de base de dados dentro do bloco até ao único fragmento onde o inquilino Id1 é mantido. A consulta só devolve blogs armazenados no fragmento atual, mas não os armazenados em quaisquer outros fragmentos. 

## <a name="data-dependent-routing-with-dapper-and-dapperextensions"></a>Encaminhamento dependente de dados com extensões Dapper e Dapper
Dapper vem com um ecossistema de extensões adicionais que podem fornecer mais conveniência e abstração a partir da base de dados ao desenvolver aplicações de base de dados. DapperExtensions é um exemplo. 

Utilizar as Extensões Dapper na sua aplicação não altera a forma como as ligações de base de dados são criadas e geridas. É ainda da responsabilidade da aplicação abrir ligações, e os objetos regulares de ligação ao Cliente SQL são esperados pelos métodos de extensão. Podemos confiar no [OpenConnectionForKey](https://msdn.microsoft.com/library/azure/dn807226.aspx) como descrito acima. Como mostram as seguintes amostras de código, a única alteração é que já não tem de escrever as declarações T-SQL:

    using (SqlConnection sqlconn = shardingLayer.ShardMap.OpenConnectionForKey(
                    key: tenantId2,
                    connectionString: connStrBldr.ConnectionString,
                    options: ConnectionOptions.Validate))
    {
           var blog = new Blog { Name = name2 };
           sqlconn.Insert(blog);
    }

E aqui está a amostra de código para a consulta: 

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

### <a name="handling-transient-faults"></a>Manuseamento de falhas transitórias
A equipa de & Práticas da Microsoft Patterns publicou o Bloco transitório de aplicação de [manuseamento](https://msdn.microsoft.com/library/hh680934.aspx) de falhas para ajudar os desenvolvedores de aplicações a mitigar as condições comuns de falha transitória encontradas quando estão a correr na nuvem. Para mais informações, consulte [Perseverance, Secret of All Triumphs: Using the Transient Fault Handling Application Application Block](https://msdn.microsoft.com/library/dn440719.aspx).

A amostra de código baseia-se na biblioteca de falhas transitórias para proteger contra falhas transitórias. 

    SqlDatabaseUtils.SqlRetryPolicy.ExecuteAction(() =>
    {
       using (SqlConnection sqlconn =
          shardingLayer.ShardMap.OpenConnectionForKey(tenantId2, connStrBldr.ConnectionString, ConnectionOptions.Validate))
          {
              var blog = new Blog { Name = name2 };
              sqlconn.Insert(blog);
          }
    });

**SqlDatabaseUtils.SqlRetryPolicy** no código acima é definido como uma Estratégia de Deteção de **Erros sqlDatabaseTransientErrorCom** com uma contagem de 10 e 5 segundos de tempo de espera entre as tentativas. Se estiver a utilizar transações, certifique-se de que o seu âmbito de retry remonta ao início da transação em caso de falha transitória.

## <a name="limitations"></a>Limitações
As abordagens descritas neste documento implicam algumas limitações:

* O código de amostra para este documento não demonstra como gerir o esquema através de fragmentos.
* Dado um pedido, assumimos que todo o seu processamento de base de dados está contido num único fragmento identificado pela chave sharding fornecida pelo pedido. No entanto, este pressuposto nem sempre se mantém, por exemplo, quando não é possível disponibilizar uma chave de sharding. Para resolver este problema, a biblioteca de clientes de base de dados elástica inclui a [classe MultiShardQuery](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.multishardexception.aspx). A classe implementa uma abstração de conexão para consulta sobre vários fragmentos. Usar multiShardQuery em combinação com dapper está fora do âmbito deste documento.

## <a name="conclusion"></a>Conclusão
Aplicações que utilizam Dapper e DapperExtensions podem facilmente beneficiar de ferramentas de base de dados elásticas para base de dados Azure SQL. Através dos passos descritos neste documento, essas aplicações podem utilizar a capacidade da ferramenta para o encaminhamento dependente de dados, alterando a criação e abertura de novos objetos [SqlConnection](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection.aspx) para utilizar a chamada [OpenConnectionForKey](https://msdn.microsoft.com/library/azure/dn807226.aspx) da biblioteca de clientes de base de dados elástica. Isto limita as alterações de aplicação necessárias aos locais onde são criadas e abertas novas ligações. 

[!INCLUDE [elastic-scale-include](../../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/elastic-scale-working-with-dapper/dapperimage1.png
