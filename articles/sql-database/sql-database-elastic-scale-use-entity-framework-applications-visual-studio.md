---
title: Com a biblioteca de cliente de base de dados elástica com Entity Framework | Documentos da Microsoft
description: Utilizar a biblioteca de clientes de bases de dados elásticas e o Entity Framework para bases de dados de codificação
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
ms.date: 01/04/2019
ms.openlocfilehash: 54890aef8dabfa019a5181c155b6668b1c07cf2c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60331935"
---
# <a name="elastic-database-client-library-with-entity-framework"></a>Biblioteca de clientes de base de dados elástica com Entity Framework

Este documento mostra as alterações de aplicação do Entity Framework que são necessários para integrar com o [ferramentas de bases de dados elásticas](sql-database-elastic-scale-introduction.md). O foco está na composição [gestão de mapas de partições horizontais](sql-database-elastic-scale-shard-map-management.md) e [encaminhamento dependente de dados](sql-database-elastic-scale-data-dependent-routing.md) com o Entity Framework **Code First** abordagem. O [Code-First - nova base de dados](https://msdn.microsoft.com/data/jj193542.aspx) tutorial para o EF funciona como o exemplo em execução em todo este documento. O código de exemplo que acompanha este documento é parte das ferramentas de bases de dados elásticas conjunto de exemplos nos exemplos de código do Visual Studio.

## <a name="downloading-and-running-the-sample-code"></a>Baixar e executar o código de exemplo

Para transferir o código deste artigo:

* É necessário o Visual Studio 2012 ou posterior. 
* Transfira o [ferramentas elásticas de DB para SQL do Azure - exemplo de integração de Entity Framework](https://code.msdn.microsoft.com/windowsapps/Elastic-Scale-with-Azure-bae904ba) do MSDN. Deszipe o exemplo para um local de sua preferência.
* Inicie o Visual Studio. 
* No Visual Studio, selecione Arquivo -> Abrir projeto/solução. 
* Na **Open Project** caixa de diálogo, navegue para o exemplo que transferiu e selecione **EntityFrameworkCodeFirst.sln** para abrir o exemplo. 

Para executar o exemplo, terá de criar três bases de dados vazias na base de dados do Azure SQL:

* Base de dados de Gestor de mapas de partições horizontais
* Base de dados de partição horizontal 1
* Base de dados de partição horizontal 2

Depois de criar esses bancos de dados, preencha os espaços reservados no **Program.cs** com o nome do servidor de BD SQL do Azure, os nomes de base de dados e as suas credenciais para ligar às bases de dados. Compile a solução no Visual Studio. Downloads do Visual Studio os NuGet pacotes necessários para a biblioteca de cliente da base de dados elástica, Entity Framework e o processamento como parte do processo de compilação de falhas transitórias. Certifique-se de que o restauro de pacotes NuGet está ativado para a sua solução. Pode ativar esta definição ao clicar com o botão direito no arquivo de solução no Explorador de soluções do Visual Studio. 

## <a name="entity-framework-workflows"></a>Fluxos de trabalho do Entity Framework

Os desenvolvedores do Entity Framework se baseiam em um dos seguintes quatro fluxos de trabalho para criar aplicativos e para garantir a persistência para objectos da aplicação:

* **Code First (nova base de dados)**: O desenvolvedor do EF cria o modelo no código da aplicação e, em seguida, o EF gera a base de dados do mesmo. 
* **Code First (base de dados existente)**: O desenvolvedor permite que o EF gerar o código do aplicativo para o modelo a partir de uma base de dados existente.
* **Modelar primeiro**: O desenvolvedor cria o modelo no EF designer e, em seguida, o EF cria a base de dados do modelo.
* **Base de dados primeiro**: O desenvolvedor usa o EF de ferramentas para inferir o modelo de uma base de dados existente. 

Todas essas abordagens contam com a classe DbContext para gerir de forma transparente as ligações de base de dados e esquema de banco de dados para uma aplicação. Permitem construtores diferentes na classe base DbContext para diferentes níveis de controlo sobre a criação da ligação, base de dados de inicialização e criação de esquema. Desafios surgem principalmente do fato de que a gestão de ligação de base de dados fornecida pelo EF intersetar-com as capacidades de gestão de ligação de interfaces de roteamento dependente de dados fornecidas pela biblioteca de cliente da base de dados elásticas. 

## <a name="elastic-database-tools-assumptions"></a>Suposições de ferramentas de bases de dados elásticas

Para obter definições de termo, consulte [Glossário de ferramentas de bases de dados elásticas](sql-database-elastic-scale-glossary.md).

Biblioteca de clientes de bases de dados elásticas, definir as partições de dados da sua aplicação chamados shardlets. Shardlets são identificadas por uma chave de fragmentação e são mapeados para bases de dados específicas. Uma aplicação poderá ter tantas bases de dados conforme necessário e distribuir os shardlets fornecer suficiente capacidade ou o desempenho, tendo em conta os requisitos de negócios atuais. O mapeamento de valores de chave de fragmentação às bases de dados é armazenado por um mapa de partições horizontais fornecido pelo cliente de base de dados elástica APIs. Esse recurso é chamado **gestão de mapas de partições horizontais**, ou SMM abreviada. O mapa de partições horizontais também serve como o Mediador de ligações de base de dados para pedidos que vão ser uma chave de fragmentação. Esta capacidade é conhecida como **encaminhamento dependente de dados**. 

O Gestor de mapas de partições horizontais protege os usuários de vistas inconsistentes sobre os dados de shardlet que podem ocorrer quando ocorrem operações de gestão de shardlet em simultâneo (por exemplo, alteração da localização dados de uma partição para outra). Para fazer isso, os mapas de partições horizontais gerida, pelo Mediador de biblioteca de cliente, as ligações de base de dados para uma aplicação. Isso permite que a funcionalidade de mapa de partições horizontais eliminar automaticamente uma ligação de base de dados quando operações de gestão de partições horizontais podem afetar o shardlet que foi criada para a ligação. Essa abordagem tem de integrar com algumas das funcionalidades do EF, como a criação de novas ligações de um já existente para verificar a existência de base de dados. Em geral, nossa observação foi que os construtores de DbContext padrão funcionam apenas com fiabilidade para ligações de base de dados fechada que podem ser Clonadas em segurança para o EF funcionam. Em vez disso, é o princípio de design de base de dados elástica mediar apenas ligações abertas. Se pensa que fechar uma conexão mediada pela biblioteca cliente antes de entregar para o EF DbContext poderá resolver este problema. No entanto, ao fechar a ligação e depender do EF para abri-lo novamente, uma foregoes as verificações de validação e consistência realizadas pela biblioteca. A funcionalidade de migrações do EF, no entanto, utiliza estas ligações para gerir o esquema de banco de dados subjacentes de forma transparente para o aplicativo. O ideal é que irá reter e combinar todos esses recursos da biblioteca de clientes de bases de dados elásticas e EF no mesmo aplicativo. A seção a seguir aborda essas propriedades e os requisitos mais detalhadamente. 

## <a name="requirements"></a>Requisitos

Ao trabalhar com a biblioteca de cliente de bases de dados elásticas e a APIs do Entity Framework, em que pretenda manter as seguintes propriedades: 

* **Escalamento horizontal**: Para adicionar ou remover bases de dados a partir da camada de dados da aplicação em partição horizontal, conforme necessário para as necessidades de capacidade do aplicativo. Isso significa que o controle sobre a criação e eliminação de bases de dados e utilizar o Gestor de mapas de partições horizontais de bases de dados elásticas APIs para gerir bases de dados e mapeamentos de shardlets. 
* **Consistência**: A aplicação utiliza a fragmentação e utiliza as capacidades de encaminhamento dependente de dados da biblioteca de clientes. Para evitar corrupção ou os resultados da consulta errado, as ligações são mediadas através do Gestor de mapas de partições horizontais. Isso também mantém validação e consistência.
* **Code First**: Para manter a conveniência do paradigma de primeiro de código do EF. No Code First, classes do aplicativo são mapeados forma transparente para as estruturas de base de dados subjacente. O código do aplicativo interage com DbSets mascarar a maioria dos aspectos envolvidos no processamento da base de dados subjacente.
* **Esquema**: Entity Framework processa a criação de esquema de base de dados inicial e a evolução de esquema subsequentes através de migrações. Ao manter estas capacidades, adaptando-se a sua aplicação é fácil à medida que os dados evolui. 

As seguintes orientações instrui como satisfazer os requisitos seguintes para o Code First aplicativos usando ferramentas de bases de dados elásticas. 

## <a name="data-dependent-routing-using-ef-dbcontext"></a>Encaminhamento com o EF DbContext dependente de dados

Ligações de base de dados com o Entity Framework, normalmente, são geridas através de subclasses de **DbContext**. Criar estes subclasses derivando de **DbContext**. É onde definimos seu **DbSets** que implementem as coleções de base de dados de objetos CLR para a sua aplicação. No contexto de encaminhamento dependente de dados, pode identificar as diversas propriedades úteis que não contêm necessariamente para outros cenários de aplicações do primeiro código do EF: 

* A base de dados já existe e tenha sido registrado no mapa de partições horizontais de bases de dados elásticas. 
* O esquema da aplicação já tiver sido implementado no banco de dados (explicado abaixo). 
* Ligações de encaminhamento dependente de dados na base de dados são mediadas no mapa de partições horizontais. 

Para integrar **DbContexts** com encaminhamento dependente de dados de escalamento horizontal:

1. Criar ligações de base de dados física por meio de interfaces de cliente de base de dados elástica do Gestor de mapas de partições horizontais, 
2. Encapsular a conexão com o **DbContext** subclasse
3. Passar a ligação para baixo para o **DbContext** classes para garantir que todo o processamento no lado do EF também acontece base. 

O exemplo de código seguinte ilustra essa abordagem. (Esse código também está no projeto do Visual Studio que acompanha este artigo)

```csharp
public class ElasticScaleContext<T> : DbContext
{
public DbSet<Blog> Blogs { get; set; }
...

    // C'tor for data-dependent routing. This call opens a validated connection 
    // routed to the proper shard by the shard map manager. 
    // Note that the base class c'tor call fails for an open connection
    // if migrations need to be done and SQL credentials are used. This is the reason for the 
    // separation of c'tors into the data-dependent routing case (this c'tor) and the internal c'tor for new shards.
    public ElasticScaleContext(ShardMap shardMap, T shardingKey, string connectionStr)
        : base(CreateDDRConnection(shardMap, shardingKey, connectionStr), 
        true /* contextOwnsConnection */)
    {
    }

    // Only static methods are allowed in calls into base class c'tors.
    private static DbConnection CreateDDRConnection(
    ShardMap shardMap, 
    T shardingKey, 
    string connectionStr)
    {
        // No initialization
        Database.SetInitializer<ElasticScaleContext<T>>(null);

        // Ask shard map to broker a validated connection for the given key
        SqlConnection conn = shardMap.OpenConnectionForKey<T>
                            (shardingKey, connectionStr, ConnectionOptions.Validate);
        return conn;
    }
```

## <a name="main-points"></a>Pontos principais

* Um novo construtor substitui o construtor padrão na subclasse de DbContext 
* O construtor novo utiliza os argumentos que são necessários para encaminhamento dependente de dados por meio da biblioteca de clientes de bases de dados elásticas:
  
  * o mapa de partições horizontais para acessar as interfaces de encaminhamento dependente de dados,
  * a chave de fragmentação para identificar o shardlet,
  * uma cadeia de ligação com as credenciais para a ligação de encaminhamento dependente de dados para a partição horizontal. 
* A chamada para o construtor de classe base leva um desvio num método estático que executa todos os passos necessários para encaminhamento dependente de dados. 
  
  * Utiliza a chamada de OpenConnectionForKey das interfaces de cliente de base de dados elástica no mapa de partições horizontais para estabelecer uma conexão aberta.
  * O mapa de partições horizontais cria a ligação aberta para a partição horizontal que contém o shardlet para a chave de fragmentação especificado.
  * Essa conexão aberta é passado de volta para o construtor de classe base do DbContext para indicar que esta ligação está a ser utilizado pelo EF em vez de permitir que o EF criar automaticamente uma nova ligação. Desta forma, a ligação foi marcada pelo cliente de base de dados elástica API para que ele pode garantir a consistência em operações de gestão do mapa de partições horizontais.

Utilize o novo construtor para sua subclasse de DbContext, em vez do construtor padrão em seu código. Segue-se um exemplo: 

```csharp
// Create and save a new blog.

Console.Write("Enter a name for a new blog: "); 
var name = Console.ReadLine(); 

using (var db = new ElasticScaleContext<int>( 
                        sharding.ShardMap,  
                        tenantId1,  
                        connStrBldr.ConnectionString)) 
{ 
    var blog = new Blog { Name = name }; 
    db.Blogs.Add(blog); 
    db.SaveChanges(); 

    // Display all Blogs for tenant 1 
    var query = from b in db.Blogs 
                orderby b.Name 
                select b; 
    … 
}
```

O novo construtor abre a ligação para a partição horizontal que contém os dados para o shardlet identificado pelo valor de **tenantid1**. O código na **usando** bloco permanece inalterado para acesso a **DbSet** para blogs usando o EF na partição horizontal para **tenantid1**. Isto altera a semântica para bloquear o código com, todas as operações de base de dados estão agora confinadas numa partição onde **tenantid1** é mantida. Por exemplo, uma consulta LINQ sobre os blogs **DbSet** apenas retornaria blogs armazenados na partição horizontal atual, mas não os arquivos armazenados em outras partições horizontais.  

#### <a name="transient-faults-handling"></a>Processamento de falhas transitórias

A Microsoft Patterns & Practices do azurecat publicou a [o Transient Fault Handling Application Block](https://msdn.microsoft.com/library/dn440719.aspx). A biblioteca é utilizada com a biblioteca de cliente de escala elástica em combinação com o EF. No entanto, certifique-se de que qualquer exceção transitória retorna um lugar onde pode garantir que está a ser utilizado o novo construtor após uma falha transitória, para que qualquer nova tentativa de conexão é feita usando os construtores que é ajustado. Caso contrário, uma ligação para a partição horizontal correta não é garantida, e não há garantias da que ligação é mantida à medida que ocorrem alterações para o mapa de partições horizontais. 

O exemplo de código a seguir ilustra como uma política de repetição SQL pode ser usada em todo o novo **DbContext** construtores de subclasse: 

```csharp
SqlDatabaseUtils.SqlRetryPolicy.ExecuteAction(() => 
{ 
    using (var db = new ElasticScaleContext<int>( 
                            sharding.ShardMap,  
                            tenantId1,  
                            connStrBldr.ConnectionString)) 
        { 
                var blog = new Blog { Name = name }; 
                db.Blogs.Add(blog); 
                db.SaveChanges(); 
        … 
        } 
    }); 
```

**SqlDatabaseUtils.SqlRetryPolicy** no código acima é definido como um **SqlDatabaseTransientErrorDetectionStrategy** com uma contagem de repetições de 10 e 5 segundos de tempo entre tentativas de espera. Esta abordagem é semelhante para as orientações para o EF e transações iniciadas pelo usuário (consulte [limitações com estratégias de execução de repetir a operação (EF6 onwards)](https://msdn.microsoft.com/data/dn307226). Ambas as situações requerem que o programa de aplicativo controla o âmbito para o qual a exceção transitória devolve: para reabrir a transação ou (conforme mostrado) recriar o contexto do construtor apropriado que utiliza a biblioteca de clientes de bases de dados elásticas.

A necessidade de controlar em que exceções transitórias demorar-na volta no âmbito também impede a utilização de incorporada **SqlAzureExecutionStrategy** que é fornecido com o EF. **SqlAzureExecutionStrategy** seria reabrir uma ligação, mas utiliza **OpenConnectionForKey** e, portanto, ignorar a validação que é executada como parte do **OpenConnectionForKey**chamar. Em vez disso, o código de exemplo utiliza o incorporado **DefaultExecutionStrategy** que também vem com o EF. Em vez de **SqlAzureExecutionStrategy**, funciona corretamente em combinação com a política de repetição de processamento de erros. A política de execução está definida **ElasticScaleDbConfiguration** classe. Tenha em atenção que decidimos não usar **DefaultSqlExecutionStrategy** uma vez que ele sugere o uso **SqlAzureExecutionStrategy** se ocorrerem a exceções transitórias - que poderia levar a comportamento incorreto conforme discutido. Para obter mais informações sobre as políticas de repetição diferentes e o EF, consulte [resiliência de ligação no EF](https://msdn.microsoft.com/data/dn456835.aspx).     

#### <a name="constructor-rewrites"></a>Construtor reescritas

Os exemplos de código acima ilustram o padrão construtor reescreverá necessários para a sua aplicação para usar o encaminhamento dependente de dados com o Entity Framework. A tabela seguinte generaliza essa abordagem para outros construtores. 

| Construtor atual | Construtor reescrito para dados | Construtor base | Notas |
| --- | --- | --- | --- |
| MyContext() |ElasticScaleContext(ShardMap, TKey) |DbContext(DbConnection, bool) |A ligação tem de ser uma função de mapa de partições horizontais e a chave de encaminhamento dependente de dados. Precisa para criação de ligação automática de ignorar pelo EF e vez disso, o mapa de partições horizontais para mediar a ligação. |
| MyContext(string) |ElasticScaleContext(ShardMap, TKey) |DbContext(DbConnection, bool) |A ligação é uma função de mapa de partições horizontais e a chave de encaminhamento dependente de dados. Uma cadeia de ligação ou nome de base de dados fixa não funciona como eles validação de ignorar no mapa de partições horizontais. |
| MyContext(DbCompiledModel) |ElasticScaleContext(ShardMap, TKey, DbCompiledModel) |DbContext(DbConnection, DbCompiledModel, bool) |A ligação é criada para a chave de partição horizontal determinado mapa e fragmentação com o modelo fornecido. O modelo compilado é passado para o c'tor base. |
| MyContext(DbConnection, bool) |ElasticScaleContext(ShardMap, TKey, bool) |DbContext(DbConnection, bool) |A conexão precisa ser inferido a partir do mapa de partições horizontais e a chave. Não pode ser fornecido como entrada (a menos que essa entrada já estava a utilizar o mapa de partições horizontais e a chave). O valor é passado. |
| MyContext(string, DbCompiledModel) |ElasticScaleContext(ShardMap, TKey, DbCompiledModel) |DbContext(DbConnection, DbCompiledModel, bool) |A conexão precisa ser inferido a partir do mapa de partições horizontais e a chave. Não pode ser fornecido como entrada (a menos que essa entrada estava a utilizar o mapa de partições horizontais e a chave). O modelo compilado é passado. |
| MyContext(ObjectContext, bool) |ElasticScaleContext(ShardMap, TKey, ObjectContext, bool) |DbContext(ObjectContext, bool) |O novo construtor tem de garantir que todas as ligações no ObjectContext passado como entrada é reencaminhadas para uma ligação gerenciada pelo dimensionamento flexível. Uma discussão detalhada sobre ObjectContexts está além do escopo deste documento. |
| MyContext(DbConnection, DbCompiledModel, bool) |ElasticScaleContext(ShardMap, TKey, DbCompiledModel, bool) |DbContext(DbConnection, DbCompiledModel, bool); |A conexão precisa ser inferido a partir do mapa de partições horizontais e a chave. A ligação não pode ser fornecida como entrada (a menos que essa entrada já estava a utilizar o mapa de partições horizontais e a chave). Modelo e booleano serão transmitidos para o construtor de classe base. |

## <a name="shard-schema-deployment-through-ef-migrations"></a>Implementação do esquema de partição horizontal através de migrações do EF

Gestão de esquemas automática é uma conveniência fornecida pelo Entity Framework. No contexto de aplicativos usando ferramentas de bases de dados elásticas, que pretenda manter esta capacidade para aprovisionar automaticamente o esquema de partições horizontais recentemente criados quando as bases de dados são adicionados à aplicação em partição horizontal. É o principal motivo aumentar a capacidade em camada de dados para aplicações em partição horizontal com o EF. Contar com capacidades do EF para gestão de esquemas reduz o esforço de administração de banco de dados com uma aplicação em partição horizontal baseada no EF. 

Implementação do esquema por meio de migrações do EF funciona melhor em **por abrir ligações**. Isso contrasta com o cenário para encaminhamento dependente de dados que se baseia na ligação aberta fornecida pela API do cliente de base de dados elástica. Outra diferença é o requisito de consistência: Embora seja desejável para garantir a consistência para todas as ligações de encaminhamento dependente de dados para proteção contra manipulação de mapa de partições horizontais em simultâneo, não é uma preocupação com a implementação do esquema inicial para uma nova base de dados que tenha ainda não foi registado no mapa de partições horizontais e ainda não foi alocado para conter os shardlets. Portanto, pode contar com ligações de base de dados regular para este cenário, em vez de encaminhamento dependente de dados.  

Isso nos leva a uma abordagem em que a implementação do esquema por meio de migrações do EF está intimamente ligada com o registo da nova base de dados como uma partição horizontal no mapa de partições horizontais da aplicação. Isso depende dos seguintes pré-requisitos: 

* A base de dados já foi criado. 
* A base de dados está vazia - realiza nenhum esquema de utilizador e não existem dados de utilizador.
* A base de dados ainda não pode ser acedido através de APIs de cliente da base de dados elásticas para encaminhamento dependente de dados. 

Com a configuração destes pré-requisitos, pode criar um normais não aberta **SqlConnection** iniciar migrações do EF para implementação do esquema. O exemplo de código a seguir ilustra essa abordagem. 

```csharp
// Enter a new shard - i.e. an empty database - to the shard map, allocate a first tenant to it  
// and kick off EF initialization of the database to deploy schema 

public void RegisterNewShard(string server, string database, string connStr, int key) 
{ 

    Shard shard = this.ShardMap.CreateShard(new ShardLocation(server, database)); 

    SqlConnectionStringBuilder connStrBldr = new SqlConnectionStringBuilder(connStr); 
    connStrBldr.DataSource = server; 
    connStrBldr.InitialCatalog = database; 

    // Go into a DbContext to trigger migrations and schema deployment for the new shard. 
    // This requires an un-opened connection. 
    using (var db = new ElasticScaleContext<int>(connStrBldr.ConnectionString)) 
    { 
        // Run a query to engage EF migrations 
        (from b in db.Blogs 
            select b).Count(); 
    } 

    // Register the mapping of the tenant to the shard in the shard map. 
    // After this step, data-dependent routing on the shard map can be used 

    this.ShardMap.CreatePointMapping(key, shard); 
} 
```

Este exemplo mostra o método **RegisterNewShard** que registra a partição horizontal no mapa de partições horizontais, implementa o esquema através de migrações do EF e armazena um mapeamento de uma chave de fragmentação para a partição horizontal. Ele conta com um construtor do **DbContext** subclasse (**ElasticScaleContext** no exemplo) que precisa de uma cadeia de ligação de SQL como entrada. O código deste construtor é direta, como mostrado no exemplo seguinte: 

```csharp
// C'tor to deploy schema and migrations to a new shard 
protected internal ElasticScaleContext(string connectionString) 
    : base(SetInitializerForConnection(connectionString)) 
{ 
} 

// Only static methods are allowed in calls into base class c'tors 
private static string SetInitializerForConnection(string connectionString) 
{ 
    // You want existence checks so that the schema can get deployed 
    Database.SetInitializer<ElasticScaleContext<T>>( 
new CreateDatabaseIfNotExists<ElasticScaleContext<T>>()); 

    return connectionString; 
} 
```

Um pode ter usado a versão do construtor herdada da classe base. Mas o código precisa para se certificar de que o inicializador padrão do EF é utilizado ao estabelecer ligação. Curto, por conseguinte, desvio para o método estático antes de chamar o construtor de classe base com a cadeia de ligação. Tenha em atenção que o registo de partições horizontais deve ser executados num domínio aplicacional diferente ou o processo para garantir que as definições do inicializador para o EF não entram em conflito. 

## <a name="limitations"></a>Limitações

As abordagens descritas neste documento envolve algumas limitações: 

* Aplicativos do EF que usam **LocalDb** primeiro tem de migrar para uma base de dados regular do SQL Server antes de utilizar a biblioteca de clientes de bases de dados elásticas. Não é possível com aumentar horizontalmente uma aplicação através de fragmentação com a escala elástica **LocalDb**. Tenha em atenção que o desenvolvimento ainda pode usar **LocalDb**. 
* Quaisquer alterações à aplicação, o que implicam alterações de esquema de base de dados tem de aceder através de migrações do EF em todas as partições horizontais. O código de exemplo para este documento não demonstrar como fazer isso. Considere a utilização de atualização do banco de dados com um parâmetro de ConnectionString para iterar sobre todas as partições horizontais; ou extrair o script de T-SQL para a migração pendente com o Update-Database com o opção - Script opção e aplicar o script T-SQL para as partições horizontais.  
* Devido um pedido, é assumido que tudo parte do processamento de base de dados se encontra numa única partição horizontal conforme identificado pela chave de fragmentação fornecida pelo pedido. No entanto, essa suposição não ser sempre verdadeiras. Por exemplo, quando não é possível disponibilizar uma chave de fragmentação. Para resolver isso, a biblioteca de cliente fornece a **MultiShardQuery** classe que implementa uma abstração de ligação para consultar o ao longo de vários shards. Aprender a utilizar o **MultiShardQuery** em combinação com o EF está além do escopo deste documento

## <a name="conclusion"></a>Conclusão

Os passos descritos neste documento, aplicativos do EF podem utilizar a capacidade da biblioteca de clientes de bases de dados elásticas para encaminhamento dependente de dados, redefinindo construtores do **DbContext** subclasses usadas no aplicativo EF. Isso limita as alterações necessárias para esses locais onde **DbContext** classes já existem. Além disso, os aplicativos do EF podem continuar a beneficiar da implementação do esquema automática ao combinar os passos que invocam as migrações do EF necessárias com o registo de novas partições horizontais e os mapeamentos do mapa de partições horizontais. 

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-use-entity-framework-applications-visual-studio/sample.png
