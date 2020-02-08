---
title: Utilização de biblioteca de clientes de base de dados elástica com Quadro de Entidades
description: Utilizar biblioteca eestrutura de clientes elásticos de dados de dados para bases de dados de codificação
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/04/2019
ms.openlocfilehash: 1653a904875964d86864c59c718603a6dacdcbda
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/08/2020
ms.locfileid: "77087182"
---
# <a name="elastic-database-client-library-with-entity-framework"></a>Biblioteca de clientes de base de dados elástica com quadro de entidade

Este documento mostra as alterações numa aplicação 'Quadro de Entidades' que são necessárias para integrar com as ferramentas de Base de [Dados Elásticas.](sql-database-elastic-scale-introduction.md) O foco está na composição da gestão de mapas de [fragmentos](sql-database-elastic-scale-shard-map-management.md) e do [encaminhamento dependente de dados](sql-database-elastic-scale-data-dependent-routing.md) com a abordagem Do **Código-Quadro** da Entidade First. O [tutorial code first - New Database](https://msdn.microsoft.com/data/jj193542.aspx) para EF serve de exemplo em todo este documento. O código de amostra que acompanha este documento faz parte do conjunto de amostras de ferramentas de base de dados elásticas nas Amostras de Código do Estúdio Visual.

## <a name="downloading-and-running-the-sample-code"></a>Descarregar e executar o código da amostra

Para descarregar o código para este artigo:

* É necessário o Visual Studio 2012 ou mais tarde. 
* Descarregue as [ferramentas ELÁSTICAS DB para Azure SQL - Amostra de Integração de Quadros](https://github.com/Azure/elastic-db-tools/)de Entidades . Desaperte a amostra para um local à sua escolha.
* Inicie o Visual Studio. 
* No Estúdio Visual, selecione File &> Open Project/Solution. 
* No diálogo **Open Project,** navegue para a amostra que descarregou e selecione **EntityFrameworkCodeFirst.sln** para abrir a amostra. 

Para executar a amostra, precisa de criar três bases de dados vazias na Base de Dados Azure SQL:

* Base de dados do Gestor de Mapas de Fragmentos
* Base de dados Shard 1
* Base de dados Shard 2

Depois de ter criado estas bases de dados, preencha os suportes de lugares em **Program.cs** com o nome do servidor DoQL DB, os nomes da base de dados e as suas credenciais para se ligar às bases de dados. Construa a solução no Estúdio Visual. O Visual Studio descarrega os pacotes NuGet necessários para a biblioteca de clientes de base de dados elástica, estrutura de entidade e manuseamento de falhas transitórias como parte do processo de construção. Certifique-se de que restaurar os pacotes NuGet está ativado para a sua solução. Pode ativar esta definição clicando corretamente no ficheiro de solução no Visual Studio Solution Explorer. 

## <a name="entity-framework-workflows"></a>Fluxos de trabalho estruturais de entidades

Os desenvolvedores do Quadro de Entidades contam com um dos seguintes quatro fluxos de trabalho para construir aplicações e para garantir a persistência de objetos de aplicação:

* **Código Primeiro (Nova Base de Dados)** : O desenvolvedor ef cria o modelo no código de aplicação e, em seguida, a EF gera a base de dados a partir dele. 
* **Código Primeiro (Base**de Dados Existente) : O desenvolvedor permite que a EF gere o código de aplicação do modelo a partir de uma base de dados existente.
* **Modelo Primeiro**: O desenvolvedor cria o modelo no designer EF e depois a EF cria a base de dados a partir do modelo.
* **Base de Dados Primeiro**: O desenvolvedor utiliza ferramentas EF para inferir o modelo a partir de uma base de dados existente. 

Todas estas abordagens dependem da classe DbContext para gerir de forma transparente as ligações de base de dados e o esquema de base de dados para uma aplicação. Diferentes construtores na classe base DbContext permitem diferentes níveis de controlo sobre a criação de ligação, a colocação de botas de base de dados e a criação de esquemas. Os desafios decorrem principalmente do facto de a gestão da ligação de base de dados fornecida pela EF se cruzar com as capacidades de gestão de ligação das interfaces de encaminhamento dependentes de dados fornecidas pela biblioteca de clientes de base de dados elástica. 

## <a name="elastic-database-tools-assumptions"></a>Pressupostos de ferramentas de base de dados elásticas

Para definições de termo, consulte o glossário das [ferramentas elásticas database](sql-database-elastic-scale-glossary.md).

Com biblioteca de clientes de base de dados elástica, define divisórias dos dados da sua aplicação chamadas fragmentos. Os fragmentos são identificados por uma chave de sharding e são mapeados para bases de dados específicas. Uma aplicação pode ter o maior número de bases de dados necessárias e distribuir os fragmentos para fornecer capacidade ou desempenho suficientes dadas as necessidades comerciais atuais. O mapeamento de valores-chave para as bases de dados é armazenado por um mapa de fragmentos fornecido pela APIs do cliente de base de dados elástica. Esta capacidade chama-se **Shard Map Management**, ou SMM para abreviar. O mapa do fragmento também serve como corretor de ligações de base de dados para pedidos que carregam uma chave de sharding. Esta capacidade é conhecida como **encaminhamento dependente de dados**. 

O gestor de mapas de fragmentos protege os utilizadores de visões inconsistentes em dados de fragmentos que podem ocorrer quando estão a acontecer operações de gestão de shardlets simultâneos (como a deslocalização de dados de um fragmento para outro). Para isso, os mapas de fragmentos geridos pelo corretor da biblioteca cliente as ligações de base de dados para uma aplicação. Isto permite que a funcionalidade do mapa de fragmentos mate automaticamente uma ligação de base de dados quando as operações de gestão do fragmento podem ter impacto no fragmento para o que a ligação foi criada. Esta abordagem tem de se integrar com algumas das funcionalidades da EF, tais como a criação de novas ligações a partir de uma existente para verificar a existência de bases de dados. Em geral, a nossa observação tem sido que os construtores padrão dbContext apenas funcionam de forma fiável para ligações de base de dados fechadas que podem ser clonadas com segurança para trabalhos de EF. O princípio do design da base de dados elástica é, em vez disso, apenas para o corretor abrir ligações. Poder-se-ia pensar que fechar uma ligação intermediada pela biblioteca do cliente antes de entregá-la ao EF DbContext pode resolver este problema. No entanto, ao fechar a ligação e contando com a EF para reabri-la, renuncia-se aos controlos de validação e consistência efetuados pela biblioteca. A funcionalidade de migração na EF, no entanto, utiliza estas ligações para gerir o esquema de base de dados subjacente de uma forma transparente à aplicação. Idealmente, irá reter e combinar todas estas capacidades tanto da biblioteca de clientes de base de dados elástica como da EF na mesma aplicação. A secção seguinte discute mais detalhadamente estas propriedades e requisitos. 

## <a name="requirements"></a>Requisitos

Ao trabalhar com a biblioteca de clientes de base de dados elástica e com as APIs estruturais da entidade, pretende manter as seguintes propriedades: 

* **Scale-out**: Adicionar ou remover bases de dados do nível de dados da aplicação fragmento, conforme necessário para as exigências de capacidade da aplicação. Isto significa controlar a criação e eliminação de bases de dados e utilizar o gestor de mapas de dados elástico APIs para gerir bases de dados e mapeamentos de fragmentos. 
* **Consistência**: A aplicação emprega sharding, e utiliza as capacidades de encaminhamento dependentede dados da biblioteca do cliente. Para evitar corrupção ou resultados de consulta erradas, as ligações são intermediadas através do gestor de mapas de fragmentos. Isto também mantém validação e consistência.
* **Código Primeiro:** Para manter a conveniência do código da EF primeiro paradigma. No Código Primeiro, as classes da aplicação são mapeadas de forma transparente para as estruturas de base de dados subjacentes. O código de aplicação interage com DbSets que mascaram a maioria dos aspetos envolvidos no processamento de bases de dados subjacentes.
* **Schema**: Entity Framework trata da criação inicial de esquemas de base de dados e subsequente evolução do esquema através de migrações. Ao reter estas capacidades, adaptar a sua aplicação é fácil à medida que os dados evoluem. 

A seguinte orientação instrui como satisfazer estes requisitos para aplicações code first utilizando ferramentas de base de dados elásticas. 

## <a name="data-dependent-routing-using-ef-dbcontext"></a>Encaminhamento dependente de dados utilizando EF DbContext

As ligações de base de dados com o Quadro de Entidades são normalmente geridas através de subclasses de **DbContext**. Crie estas subclasses derivando do **DbContext.** É aqui que define os seus **DbSets** que implementam as coleções apoiadas pela base de dados de objetos CLR para a sua aplicação. No contexto do encaminhamento dependente de dados, pode identificar várias propriedades úteis que não detêm necessariamente para outros cenários de primeira aplicação do código EF: 

* A base de dados já existe e foi registada no mapa de bases de dados elástica. 
* O esquema da aplicação já foi implantado na base de dados (explicado abaixo). 
* As ligações de encaminhamento dependentes de dados para a base de dados são intermediadas pelo mapa do fragmento. 

Para integrar o **DbContexts** com o encaminhamento dependente de dados para escala- out:

1. Criar ligações físicas de base de dados através das interfaces elásticas de clientes da base de dados do gestor de mapas de fragmentos, 
2. Embrulhe a ligação com a subclasse **DbContext**
3. Passe a ligação para as classes base **dbContext** para garantir que todo o processamento do lado EF também ocorra. 

O exemplo de código que se segue ilustra esta abordagem. (Este código também está no projeto do Estúdio Visual que acompanha)

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

## <a name="main-points"></a>Principais pontos

* Um novo construtor substitui o construtor padrão na subclasse DbContext 
* O novo construtor pega nos argumentos necessários para o encaminhamento dependente de dados através da biblioteca de clientes de base de dados elástica:
  
  * o mapa do fragmento para aceder às interfaces de encaminhamento dependente de dados,
  * a chave sharding para identificar o fragmento,
  * uma cadeia de ligação com as credenciais para a ligação de encaminhamento dependente de dados ao fragmento. 
* A chamada para o construtor de classes base faz um desvio para um método estático que executa todos os passos necessários para o encaminhamento dependente de dados. 
  
  * Utiliza a chamada OpenConnectionForKey das interfaces de cliente de base de dados elásticas no mapa do fragmento para estabelecer uma ligação aberta.
  * O mapa do fragmento cria a ligação aberta ao fragmento que segura o fragmento para a chave de sharding dada.
  * Esta ligação aberta é transmitida de volta ao construtor de classes base da DbContext para indicar que esta ligação deve ser usada pela EF em vez de deixar a EF criar automaticamente uma nova ligação. Desta forma, a ligação foi marcada pela API do cliente de base de dados elástica para que possa garantir a consistência nas operações de gestão de mapas.

Utilize o novo construtor para a sua subclasse DbContext em vez do construtor predefinido no seu código. Segue-se um exemplo: 

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

O novo construtor abre a ligação ao fragmento que detém os dados do fragmento identificados pelo valor do **arrendatário1**. O código no bloco **de utilização** permanece inalterado para aceder ao **DbSet** para blogs que utilizam EF no fragmento para **inquilinos1**. Isto altera a semântica para o código no bloco de utilização, de modo a que todas as operações de base de dados sejam agora viadas para o único fragmento onde o **arrendatário** 1 é mantido. Por exemplo, uma consulta de LINQ sobre os blogs **DbSet** apenas devolveria blogs armazenados no fragmento atual, mas não os armazenados em outros fragmentos.  

#### <a name="transient-faults-handling"></a>Manipulação de falhas transitórias

A equipa de Padrões e Práticas da Microsoft publicou o Bloco de Aplicações de Manipulação de [Falhas Transitórias](https://msdn.microsoft.com/library/dn440719.aspx). A biblioteca é usada com biblioteca de clientes em escala elástica em combinação com EF. No entanto, certifique-se de que qualquer exceção transitória regressa a um local onde pode garantir que o novo construtor está a ser utilizado após uma falha transitória para que qualquer nova tentativa de ligação seja feita utilizando os construtores que alterou. Caso contrário, não é garantida uma ligação ao fragmento correto, e não existem garantias de que a ligação seja mantida à medida que ocorrem alterações ao mapa do fragmento. 

A seguinte amostra de código ilustra como uma política de retry SQL pode ser usada em torno dos novos construtores de subclasse **DbContext:** 

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

**SqlDatabaseUtils.SqlRetryPolicy** no código acima é definido como uma Estratégia de Deteção de **Erros sqlDatabaseTransientErrorCom** com uma contagem de 10 e 5 segundos de tempo de espera entre as tentativas. Esta abordagem é semelhante à orientação para as transações EF e iniciadas pelo utilizador (ver Limitações com Estratégias de [Execução Retry (EF6 em diante)](https://msdn.microsoft.com/data/dn307226). Ambas as situações exigem que o programa de aplicação controle o âmbito a que a exceção transitória retorna: reabrir a transação ou (como mostrado) recriar o contexto a partir do construtor adequado que utiliza a biblioteca de clientes de base de dados elástica.

A necessidade de controlar onde as exceções transitórias nos levam de volta ao âmbito também impede a utilização da Estratégia de **Execução SqlAzure incorporada** que vem com a EF. **SqlAzureExecutionStrategy** reabriria uma ligação, mas não usaria **OpenConnectionForKey** e, portanto, contornaria toda a validação que é realizada como parte da chamada **OpenConnectionForKey.** Em vez disso, a amostra de código utiliza a Estratégia de **Execução padrão** incorporada que também vem com EF. Ao contrário do **SqlAzureExecutionStrategy,** funciona corretamente em combinação com a política de retry do Tratamento de Falhas Transient. A política de execução é definida na classe **ElasticScaleDbConfiguration.** Note que decidimos não usar **defaultSqlExecutionStrategy uma** vez que sugere a utilização de **SqlAzureExecutionStrategy** se ocorrerem exceções transitórias - o que levaria a comportamentos errados como discutido. Para obter mais informações sobre as diferentes políticas de retry e EF, consulte [a Resiliência da Ligação em EF](https://msdn.microsoft.com/data/dn456835.aspx).     

#### <a name="constructor-rewrites"></a>Construtor reescreve

Os exemplos de código acima ilustram as reescritas de construção predefinidas necessárias para a sua aplicação de forma a utilizar o encaminhamento dependente de dados com o Quadro de Entidades. A tabela que se segue generaliza esta abordagem a outros construtores. 

| Construtor atual | Construtor reescrito para dados | Construtor de Base | Notas |
| --- | --- | --- | --- |
| MyContext() |Contexto de escala elástica (ShardMap, TKey) |DbContext (DbConnection, bool) |A ligação tem de ser uma função do mapa do fragmento e da chave de encaminhamento dependente de dados. Você precisa passar by-by-by criação de conexão automática pela EF e em vez disso usar o mapa de fragmentos para intermediar a ligação. |
| MyContext(string) |Contexto de escala elástica (ShardMap, TKey) |DbContext (DbConnection, bool) |A ligação é uma função do mapa do fragmento e da chave de encaminhamento dependente de dados. Um nome de base de dados fixo ou cadeia de ligação não funciona como validação de by-pass pelo mapa do fragmento. |
| MyContext(DbCompiledModel) |ElasticScaleContext(ShardMap, TKey, DbCompiledModel) |DbContext (DbConnection, DbCompiledModel, bool) |A ligação é criada para o mapa de fragmentos dado e chave de sharding com o modelo fornecido. O modelo compilado é passado para a base c'tor. |
| MyContext (DbConnection, bool) |ElasticScaleContext (ShardMap, TKey, bool) |DbContext (DbConnection, bool) |A ligação tem de ser deduzida do mapa do fragmento e da chave. Não pode ser fornecida como entrada (a menos que essa entrada já estivesse a utilizar o mapa do fragmento e a chave). O Boolean é transmitido. |
| MyContext(string, DbCompiledModel) |ElasticScaleContext(ShardMap, TKey, DbCompiledModel) |DbContext (DbConnection, DbCompiledModel, bool) |A ligação tem de ser deduzida do mapa do fragmento e da chave. Não pode ser fornecida como entrada (a menos que essa entrada estivesse a utilizar o mapa do fragmento e a chave). O modelo compilado é transmitido. |
| MyContext (ObjectContext, bool) |ElasticScaleContext (ShardMap, TKey, ObjectContext, bool) |DbContext (ObjectContext, bool) |O novo construtor precisa de garantir que qualquer ligação no ObjectContext passada como entrada é reencaminhada para uma ligação gerida pela Escala Elástica. Uma discussão detalhada sobre objectContexts está fora do âmbito deste documento. |
| MyContext (DbConnection, DbCompiledModel, bool) |ElasticScaleContext (ShardMap, TKey, DbCompiledModel, bool) |DbContext (DbConnection, DbCompiledModel, bool); |A ligação tem de ser deduzida do mapa do fragmento e da chave. A ligação não pode ser fornecida como entrada (a menos que essa entrada já estivesse a utilizar o mapa do fragmento e a chave). Modelo e Boolean são passados para o construtor de classe base. |

## <a name="shard-schema-deployment-through-ef-migrations"></a>Implantação de esquemas de fragmentos através das migrações do EF

A gestão automática do esquema é uma conveniência proporcionada pelo Quadro de Entidades. No contexto de aplicações que utilizam ferramentas de base de dados elásticas, pretende manter esta capacidade de fornecer automaticamente o esquema a fragmentos recém-criados quando as bases de dados são adicionadas à aplicação esfumada. O principal caso de utilização é aumentar a capacidade no nível de dados para aplicações esfumadas utilizando EF. Confiar nas capacidades da EF para a gestão de esquemas reduz o esforço de administração da base de dados com uma aplicação esfarvidada baseada na EF. 

A implantação de schema através das migrações EF funciona melhor em **ligações não abertas.** Isto contrasta com o cenário de encaminhamento dependente de dados que se baseia na ligação aberta fornecida pela API do cliente de base de dados elástica. Outra diferença é a exigência de coerência: Embora desejável para garantir a coerência de todas as ligações de encaminhamento dependentes de dados para proteger contra manipulação simultânea de mapas de fragmentos, não é uma preocupação com a implantação inicial de esquemas para uma nova base de dados que ainda não foi registado no mapa do fragmento, e ainda não foi alocado para segurar fragmentos. Por isso, pode contar com ligações regulares de base de dados para este cenário, em oposição ao encaminhamento dependente de dados.  

Isto leva a uma abordagem em que a implantação de esquemas através das migrações ef é estreitamente associada ao registo da nova base de dados como um fragmento no mapa de fragmentos da aplicação. Isto baseia-se nos seguintes pré-requisitos: 

* A base de dados já foi criada. 
* A base de dados está vazia - não possui nenhum esquema de utilizador nem dados de utilizador.
* A base de dados ainda não pode ser acedida através da base de dados elástica cliente APIs para encaminhamento dependente de dados. 

Com estes pré-requisitos em vigor, pode criar uma **SqlConnection** regular não aberta para iniciar as migrações EF para implantação de esquemas. A amostra de código que se segue ilustra esta abordagem. 

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

Esta amostra mostra o método **RegisterNewShard** que regista o fragmento no mapa do fragmento, implanta o esquema através das migrações EF, e armazena um mapeamento de uma chave de sharding para o fragmento. Baseia-se num construtor da subclasse **DbContext** **(ElasticScaleContext** na amostra) que toma como entrada uma cadeia de ligação SQL. O código deste construtor é direto, como mostra o seguinte exemplo: 

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

Pode-se ter usado a versão do construtor herdado da classe base. Mas o código precisa de garantir que o inicializador predefinido para EF é utilizado na ligação. Daí o curto desvio para o método estático antes de chamar para o construtor de classe base com a corda de ligação. Note que o registo de fragmentos deve ser executado num domínio ou processo de aplicação diferente para garantir que as definições de inicializador escingência para EF não entram em conflito. 

## <a name="limitations"></a>Limitações

As abordagens descritas neste documento implicam algumas limitações: 

* As aplicações EF que utilizam o **LocalDb** primeiro precisam de migrar para uma base de dados regular do SQL Server antes de utilizarem a biblioteca de clientes de base de dados elástica. A ampliação de uma aplicação através de sharding com escala elástica não é possível com **o LocalDb**. Note que o desenvolvimento ainda pode usar **LocalDb**. 
* Quaisquer alterações na aplicação que impliquem alterações de esquemas de base de dados precisam de passar pelas migrações de EF em todos os fragmentos. O código de amostra para este documento não demonstra como fazê-lo. Considere utilizar a Base de Dados de Atualização com um parâmetro ConnectionString para iterar sobre todos os fragmentos; ou extrair o script T-SQL para a migração pendente utilizando a Actualização-Base de Dados com a opção -Script e aplicar o script T-SQL nos seus fragmentos.  
* Tendo em conta um pedido, presume-se que todo o seu processamento de base de dados está contido num único fragmento identificado pela chave sharding fornecida pelo pedido. No entanto, esta suposição nem sempre é verdadeira. Por exemplo, quando não é possível disponibilizar uma chave de sharding. Para resolver este problema, a biblioteca de clientes fornece a classe **MultiShardQuery** que implementa uma abstração de conexão para consulta sobre vários fragmentos. Aprender a usar o **MultiShardQuery** em combinação com a EF está fora do âmbito deste documento

## <a name="conclusion"></a>Conclusão

Através dos passos descritos neste documento, as aplicações EF podem utilizar a capacidade elástica da biblioteca de clientes da base de dados para o encaminhamento dependente de dados, refactoring de construtores das subclasses **DbContext** utilizadas na aplicação EF. Isto limita as alterações necessárias aos locais onde as aulas **de DbContext** já existem. Além disso, as aplicações EF podem continuar a beneficiar da implantação automática de esquemas, combinando as etapas que invocam as migrações necessárias da EF com o registo de novos fragmentos e mapeamentos no mapa do fragmento. 

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-use-entity-framework-applications-visual-studio/sample.png
