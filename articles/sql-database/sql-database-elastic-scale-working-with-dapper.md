---
title: Usando a biblioteca de cliente do banco de dados elástico com Dapper | Microsoft Docs
description: Usando a biblioteca de cliente do banco de dados elástico com Dapper.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/04/2018
ms.openlocfilehash: 1eafb123014effad9daca89dc1b852367d9cbbf1
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68568268"
---
# <a name="using-elastic-database-client-library-with-dapper"></a>Usando a biblioteca de cliente do banco de dados elástico com Dapper
Este documento destina-se a desenvolvedores que dependem de Dapper para criar aplicativos, mas também querem adotar [ferramentas de banco](sql-database-elastic-scale-introduction.md) de dados elástico para criar aplicativos que implementam a fragmentação para escalar horizontalmente sua camada.  Este documento ilustra as alterações em aplicativos baseados em Dapper que são necessárias para integrar o com ferramentas de banco de dados elástico. Nosso foco é compor o gerenciamento de fragmentos de banco de dados elástico e o roteamento dependente de dado com Dapper. 

**Código de exemplo**: [Ferramentas de banco de dados elástico para a integração do banco de dados SQL do Azure-Dapper](https://code.msdn.microsoft.com/Elastic-Scale-with-Azure-e19fc77f).

É fácil integrar **Dapper** e **DapperExtensions** com a biblioteca de cliente do banco de dados elástico para o banco de dados SQL do Azure. Seus aplicativos podem usar o roteamento dependente de dados alterando a criação e a abertura de novos objetos [SqlConnection](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection.aspx) para usar a chamada [OpenConnectionForKey](https://msdn.microsoft.com/library/azure/dn807226.aspx) da [biblioteca de cliente](https://msdn.microsoft.com/library/azure/dn765902.aspx). Isso limita as alterações no aplicativo apenas onde novas conexões são criadas e abertas. 

## <a name="dapper-overview"></a>Visão geral do Dapper
**Dapper** é um mapeador relacional de objeto. Ele mapeia objetos .NET do seu aplicativo para um banco de dados relacional (e vice-versa). A primeira parte do código de exemplo ilustra como você pode integrar a biblioteca de cliente do banco de dados elástico com aplicativos baseados em Dapper. A segunda parte do código de exemplo ilustra como integrar o ao usar Dapper e DapperExtensions.  

A funcionalidade do mapeador no Dapper fornece métodos de extensão em conexões de banco de dados que simplificam o envio de instruções T-SQL para execução ou consulta do banco de dados. Por exemplo, Dapper facilita o mapeamento entre seus objetos .NET e os parâmetros de instruções SQL para **executar** chamadas, ou para consumir os resultados de suas consultas SQL em objetos .NET usando chamadas de **consulta** de Dapper. 

Ao usar o DapperExtensions, você não precisa mais fornecer as instruções SQL. Métodos de extensões como **GetList** ou **Insert** sobre a conexão de banco de dados criam as instruções SQL em segundo plano.

Outro benefício de Dapper e também DapperExtensions é que o aplicativo controla a criação da conexão de banco de dados. Isso ajuda a interagir com a biblioteca de cliente do banco de dados elástico que agentes conexões de banco de dados com base no mapeamento de shardlets para bancos de dados.

Para obter os assemblies do Dapper, consulte [Dapper dot net](https://www.nuget.org/packages/Dapper/). Para as extensões Dapper, consulte [DapperExtensions](https://www.nuget.org/packages/DapperExtensions).

## <a name="a-quick-look-at-the-elastic-database-client-library"></a>Uma visão rápida da biblioteca de cliente do banco de dados elástico
Com a biblioteca de cliente do banco de dados elástico, você define as partições dos dados do aplicativo, chamadas *shardlets*, os mapeia para os bancos e os identifica por *chaves*de fragmentação. Você pode ter tantos bancos de dados quantos precisar e distribuir seu shardlets entre esses bancos de dados. O mapeamento de valores de chave de fragmentação para os bancos de dados é armazenado por um mapa de fragmentos fornecido pelas APIs da biblioteca. Esse recurso é chamado de **Gerenciamento de mapa de fragmentos**. O mapa de fragmentos também serve como o agente de conexões de banco de dados para solicitações que carregam uma chave de fragmentação. Esse recurso é conhecido como **Roteamento Dependente de dados**.

![Mapas de fragmentos e roteamento dependente de dados][1]

O Gerenciador de mapa de fragmentos protege os usuários de exibições inconsistentes em dados do shardlet que podem ocorrer quando operações simultâneas de gerenciamento de shardlet estão ocorrendo nos bancos de dado. Para fazer isso, o fragmento mapeia o agente de conexões de banco de dados para um aplicativo criado com a biblioteca. Quando as operações de gerenciamento de fragmentos podem afetar o shardlet, isso permite que a funcionalidade de mapa de fragmentos elimine automaticamente uma conexão de banco de dados. 

Em vez de usar a maneira tradicional de criar conexões para Dapper, você precisa usar o [método OpenConnectionForKey](https://msdn.microsoft.com/library/azure/dn824099.aspx). Isso garante que toda a validação ocorra e que as conexões sejam gerenciadas corretamente quando qualquer dado se mover entre fragmentos.

### <a name="requirements-for-dapper-integration"></a>Requisitos para a integração do Dapper
Ao trabalhar com a biblioteca de cliente do banco de dados elástico e as APIs do Dapper, você deseja manter as seguintes propriedades:

* **Escalar horizontalmente**: Queremos adicionar ou Remover bancos de dados da camada de dado do aplicativo fragmentado, conforme necessário, para as demandas de capacidade do aplicativo. 
* **Consistência**: Como o aplicativo é escalado horizontalmente usando a fragmentação, você precisa executar o roteamento dependente de dados. Queremos usar os recursos de roteamento dependentes de dados da biblioteca para fazer isso. Em particular, você deseja manter as garantias de validação e consistência fornecidas por conexões que são orientadas por meio do Gerenciador de mapa de fragmentos para evitar corrupção ou resultados de consulta incorretos. Isso garante que as conexões com um determinado shardlet sejam rejeitadas ou interrompidas se (por exemplo) o shardlet estiver atualmente movido para um fragmento diferente usando APIs de divisão/mesclagem.
* **Mapeamento de objeto**: Queremos manter a conveniência dos mapeamentos fornecidos pelo Dapper para converter entre classes no aplicativo e as estruturas de banco de dados subjacentes. 

A seção a seguir fornece diretrizes para esses requisitos para aplicativos baseados em **Dapper** e **DapperExtensions**.

## <a name="technical-guidance"></a>Diretrizes técnicas
### <a name="data-dependent-routing-with-dapper"></a>Roteamento dependente de dados com Dapper
Com o Dapper, o aplicativo é normalmente responsável por criar e abrir as conexões com o banco de dados subjacente. Dado um tipo T pelo aplicativo, Dapper retorna os resultados da consulta como coleções .NET do tipo T. Dapper executa o mapeamento das linhas de resultado T-SQL para os objetos do tipo T. Da mesma forma, Dapper mapeia objetos .NET em valores SQL ou parâmetros para instruções DML (linguagem de manipulação de dados). O Dapper oferece essa funcionalidade por meio de métodos de extensão no objeto [SqlConnection](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection.aspx) regular das bibliotecas de cliente SQL do ADO .net. A conexão SQL retornada pelas APIs de escala elástica para DDR também são objetos [SqlConnection](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection.aspx) regulares. Isso nos permite usar diretamente as extensões de Dapper sobre o tipo retornado pela API de DDR da biblioteca de cliente, pois ela também é uma conexão de cliente SQL simples.

Essas observações tornam simples o uso de conexões orientadas pela biblioteca de cliente do banco de dados elástico para Dapper.

Este exemplo de código (do exemplo a seguir) ilustra a abordagem em que a chave de fragmentação é fornecida pelo aplicativo para a biblioteca para o agente da conexão com o fragmento correto.   

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

A chamada para a API [OpenConnectionForKey](https://msdn.microsoft.com/library/azure/dn807226.aspx) substitui a criação padrão e a abertura de uma conexão de cliente SQL. A chamada [OpenConnectionForKey](https://msdn.microsoft.com/library/azure/dn807226.aspx) usa os argumentos que são necessários para o roteamento dependente de dados: 

* O mapa de fragmentos para acessar as interfaces de roteamento dependentes de dados
* A chave de fragmentação para identificar o shardlet
* As credenciais (nome de usuário e senha) para se conectar ao fragmento

O objeto de mapa de fragmentos cria uma conexão com o fragmento que mantém o shardlet para a chave de fragmentação especificada. As APIs de cliente do banco de dados elástico também marcam a conexão para implementar suas garantias de consistência. Como a chamada para [OpenConnectionForKey](https://msdn.microsoft.com/library/azure/dn807226.aspx) retorna um objeto de conexão de cliente SQL regular, a chamada subsequente para o método de extensão **Execute** de Dapper segue a prática Dapper padrão.

As consultas funcionam praticamente da mesma forma – você primeiro abre a conexão usando [OpenConnectionForKey](https://msdn.microsoft.com/library/azure/dn807226.aspx) da API do cliente. Em seguida, use os métodos de extensão Dapper regulares para mapear os resultados da consulta SQL em objetos .NET:

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

Observe que o bloco **using** com os escopos de conexão DDR todas as operações de banco de dados dentro do bloco para um fragmento em que tenantId1 é mantido. A consulta retorna apenas os Blogs armazenados no fragmento atual, mas não aqueles armazenados em outros fragmentos. 

## <a name="data-dependent-routing-with-dapper-and-dapperextensions"></a>Roteamento dependente de dados com Dapper e DapperExtensions
O Dapper vem com um ecossistema de extensões adicionais que podem fornecer mais conveniência e abstração do banco de dados ao desenvolver aplicativos de banco de dados. DapperExtensions é um exemplo. 

Usar DapperExtensions em seu aplicativo não altera como as conexões de banco de dados são criadas e gerenciadas. Ainda é responsabilidade do aplicativo abrir conexões e os objetos de conexão de cliente SQL regulares são esperados pelos métodos de extensão. Podemos contar com o [OpenConnectionForKey](https://msdn.microsoft.com/library/azure/dn807226.aspx) conforme descrito acima. Como os exemplos de código a seguir mostram, a única alteração é que você não precisa mais escrever as instruções T-SQL:

    using (SqlConnection sqlconn = shardingLayer.ShardMap.OpenConnectionForKey(
                    key: tenantId2,
                    connectionString: connStrBldr.ConnectionString,
                    options: ConnectionOptions.Validate))
    {
           var blog = new Blog { Name = name2 };
           sqlconn.Insert(blog);
    }

E aqui está o exemplo de código para a consulta: 

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

### <a name="handling-transient-faults"></a>Tratando falhas transitórias
A equipe de práticas de & de padrões da Microsoft publicou o [bloco de aplicativos para tratamento de falhas transitórias](https://msdn.microsoft.com/library/hh680934.aspx) para ajudar os desenvolvedores de aplicativos a atenuar condições de falhas transitórias comuns encontradas durante a execução na nuvem. Para obter mais informações, [consulte perseverity, Secret de todos os triunfos: Usando o bloco](https://msdn.microsoft.com/library/dn440719.aspx)de aplicativo de tratamento de falhas transitórias.

O exemplo de código se baseia na biblioteca de falhas transitórias para proteger contra falhas transitórias. 

    SqlDatabaseUtils.SqlRetryPolicy.ExecuteAction(() =>
    {
       using (SqlConnection sqlconn =
          shardingLayer.ShardMap.OpenConnectionForKey(tenantId2, connStrBldr.ConnectionString, ConnectionOptions.Validate))
          {
              var blog = new Blog { Name = name2 };
              sqlconn.Insert(blog);
          }
    });

**SqlDatabaseUtils. SqlRetryPolicy** no código acima é definido como um **SqlDatabaseTransientErrorDetectionStrategy** com uma contagem de repetições de 10 e 5 segundos de tempo de espera entre repetições. Se você estiver usando transações, verifique se o seu escopo de repetição retorna ao início da transação no caso de uma falha transitória.

## <a name="limitations"></a>Limitações
As abordagens descritas neste documento envolvem algumas limitações:

* O código de exemplo deste documento não demonstra como gerenciar o esquema entre fragmentos.
* Devido a uma solicitação, presumimos que todo o processamento do banco de dados está contido em um único fragmento, conforme identificado pela chave de fragmentação fornecida pela solicitação. No entanto, essa suposição nem sempre é segura, por exemplo, quando não é possível disponibilizar uma chave de fragmentação. Para resolver isso, a biblioteca de cliente do banco de dados elástico inclui a [classe MultiShardQuery](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.multishardexception.aspx). A classe implementa uma abstração de conexão para consultar vários fragmentos. Usar MultiShardQuery em combinação com Dapper está além do escopo deste documento.

## <a name="conclusion"></a>Conclusão
Os aplicativos que usam Dapper e DapperExtensions podem facilmente se beneficiar das ferramentas de banco de dados elástico para o banco de dados SQL do Azure. Por meio das etapas descritas neste documento, esses aplicativos podem usar a capacidade da ferramenta para roteamento dependente de dados, alterando a criação e a abertura de novos objetos [SqlConnection](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection.aspx) para usar a chamada [OpenConnectionForKey](https://msdn.microsoft.com/library/azure/dn807226.aspx) do elástico biblioteca de cliente de banco de dados. Isso limita as alterações de aplicativo necessárias para os locais em que novas conexões são criadas e abertas. 

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-working-with-dapper/dapperimage1.png
