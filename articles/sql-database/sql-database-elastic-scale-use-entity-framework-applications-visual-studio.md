---
title: Usando a biblioteca de cliente do banco de dados elástico com Entity Framework
description: Usar biblioteca de cliente do banco de dados elástico e Entity Framework para codificar bancos de dados
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
ms.openlocfilehash: 4198b3a9213ed535c6649c50a20f2ff957d60c94
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73823491"
---
# <a name="elastic-database-client-library-with-entity-framework"></a>Biblioteca de cliente do banco de dados elástico com Entity Framework

Este documento mostra as alterações em um aplicativo Entity Framework que são necessárias para integrar o com as [ferramentas de banco de dados elástico](sql-database-elastic-scale-introduction.md). O foco é na composição do [Gerenciamento de mapa de fragmentos](sql-database-elastic-scale-shard-map-management.md) e do [Roteamento Dependente de dados](sql-database-elastic-scale-data-dependent-routing.md) com a abordagem de **Code First** Entity Framework. O tutorial [Code First novo banco de dados](https://msdn.microsoft.com/data/jj193542.aspx) para o EF serve como o exemplo em execução em todo este documento. O código de exemplo que acompanha este documento faz parte do conjunto de amostras de ferramentas de banco de dados elástico nos exemplos de Visual Studio Code.

## <a name="downloading-and-running-the-sample-code"></a>Baixando e executando o código de exemplo

Para baixar o código deste artigo:

* O Visual Studio 2012 ou posterior é necessário. 
* Baixe as [ferramentas de banco de BD elástico para Azure SQL – exemplo de integração de Entity Framework](https://code.msdn.microsoft.com/windowsapps/Elastic-Scale-with-Azure-bae904ba) do MSDN. Descompacte o exemplo em um local de sua escolha.
* Inicie o Visual Studio. 
* No Visual Studio, selecione Arquivo-> Abrir projeto/solução. 
* Na caixa de diálogo **Abrir projeto** , navegue até o exemplo que você baixou e selecione **EntityFrameworkCodeFirst. sln** para abrir o exemplo. 

Para executar o exemplo, você precisa criar três bancos de dados vazios no banco de dados SQL do Azure:

* Banco de dados do Gerenciador de mapa de fragmentos
* Banco de dados do fragmento 1
* Banco de dados do fragmento 2

Depois de criar esses bancos de dados, preencha os contentores em **Program.cs** com o nome do seu servidor do banco de dados SQL do Azure, os nomes do banco e suas credenciais para se conectar aos bancos de dados. Compile a solução no Visual Studio. O Visual Studio baixa os pacotes NuGet necessários para a biblioteca de cliente do banco de dados elástico, Entity Framework e tratamento de falhas transitórias como parte do processo de compilação. Verifique se a restauração de pacotes NuGet está habilitada para sua solução. Você pode habilitar essa configuração clicando com o botão direito do mouse no arquivo de solução no Gerenciador de Soluções do Visual Studio. 

## <a name="entity-framework-workflows"></a>Fluxos de trabalho Entity Framework

Entity Framework desenvolvedores contam com um dos quatro fluxos de trabalho a seguir para criar aplicativos e garantir a persistência para objetos de aplicativo:

* **Code First (novo banco de dados)** : o desenvolvedor do EF cria o modelo no código do aplicativo e, em seguida, o EF gera o banco de dados a partir dele. 
* **Code First (banco de dados existente)** : o desenvolvedor permite que o EF gere o código do aplicativo para o modelo a partir de um banco de dados existente.
* **Model First**: o desenvolvedor cria o modelo no designer do EF e, em seguida, o EF cria o banco de dados do modelo.
* **Database First**: o desenvolvedor usa as ferramentas do EF para inferir o modelo de um banco de dados existente. 

Todas essas abordagens dependem da classe DbContext para gerenciar de forma transparente as conexões de banco de dados e o esquema de banco de dados para um aplicativo. Construtores diferentes na classe base DbContext permitem diferentes níveis de controle sobre a criação de conexão, inicialização de banco de dados e criação de esquema. Os desafios surgem principalmente do fato de que o gerenciamento de conexão de banco de dados fornecido pelo EF cruza com os recursos de gerenciamento de conexão das interfaces de roteamento dependentes de dados fornecidas pela biblioteca de cliente do banco de dado elástico. 

## <a name="elastic-database-tools-assumptions"></a>Suposições de ferramentas de banco de dados elástico

Para definições de termo, consulte [Glossário de ferramentas de banco de dados elástico](sql-database-elastic-scale-glossary.md).

Com a biblioteca de cliente do banco de dados elástico, você define as partições dos dados do aplicativo, chamadas shardlets. Shardlets são identificados por uma chave de fragmentação e são mapeados para bancos de dados específicos. Um aplicativo pode ter tantos bancos de dados quantos forem necessários e distribuir o shardlets para fornecer capacidade ou desempenho suficiente de acordo com os requisitos de negócios atuais. O mapeamento de valores de chave de fragmentação para os bancos de dados é armazenado por um mapa de fragmentos fornecido pelas APIs de cliente do banco de dados elástico. Esse recurso é chamado de **Gerenciamento de mapa de fragmentos**ou SMM para curto. O mapa de fragmentos também serve como o agente de conexões de banco de dados para solicitações que carregam uma chave de fragmentação. Esse recurso é conhecido como **Roteamento Dependente de dados**. 

O Gerenciador de mapa de fragmentos protege os usuários de exibições inconsistentes em dados do shardlet que podem ocorrer quando operações simultâneas de gerenciamento de shardlet (como realocar dados de um fragmento para outro) estão ocorrendo. Para fazer isso, os mapas de fragmento gerenciados pelo agente de biblioteca de cliente as conexões de banco de dados para um aplicativo. Isso permite que a funcionalidade de mapa de fragmentos elimine automaticamente uma conexão de banco de dados quando as operações de gerenciamento de fragmentos puderem afetar o shardlet para o qual a conexão foi criada. Essa abordagem precisa ser integrada a algumas das funcionalidades do EF, como a criação de novas conexões de um existente para verificar a existência do banco de dados. Em geral, nossa observação foi que os construtores DbContext padrão só funcionam de forma confiável para conexões de banco de dados fechadas que podem ser clonadas com segurança para o trabalho do EF. Em vez disso, o princípio de design do banco de dados elástico é apenas para o agente de conexões abertas. Talvez você ache que fechar uma conexão orientada pela biblioteca do cliente antes de entregá-la ao EF DbContext pode resolver esse problema. No entanto, fechando a conexão e contando com o EF para reabri-la, uma delas é anterior à validação e às verificações de consistência executadas pela biblioteca. No entanto, a funcionalidade de migrações no EF usa essas conexões para gerenciar o esquema de banco de dados subjacente de forma transparente para o aplicativo. Idealmente, você manterá e combinará todos esses recursos da biblioteca de cliente do banco de dados elástico e do EF no mesmo aplicativo. A seção a seguir aborda essas propriedades e requisitos mais detalhadamente. 

## <a name="requirements"></a>Requisitos

Ao trabalhar com a biblioteca de cliente do banco de dados elástico e as APIs de Entity Framework, você deseja manter as seguintes propriedades: 

* **Escala horizontal**: para adicionar ou Remover bancos de dados da camada do aplicativo fragmentado conforme necessário para as demandas de capacidade do aplicativo. Isso significa controle sobre a criação e a exclusão de bancos de dados e o uso de APIs do Gerenciador de mapa de fragmentos de banco de 
* **Consistência**: o aplicativo emprega a fragmentação e usa os recursos de roteamento dependentes de dados da biblioteca do cliente. Para evitar corrupção ou resultados de consulta incorretos, as conexões são orientadas por meio do Gerenciador de mapa de fragmentos. Isso também mantém a validação e a consistência.
* **Code First**: para manter a conveniência do paradigma do Code First do EF. No Code First, as classes no aplicativo são mapeadas de forma transparente para as estruturas de banco de dados subjacentes. O código do aplicativo interage com DbSets que mascaram a maioria dos aspectos envolvidos no processamento de banco de dados subjacente.
* **Esquema**: Entity Framework trata da criação inicial do esquema de banco de dados e da evolução do esquema subsequente por meio de migrações. Ao reter esses recursos, adaptar seu aplicativo é fácil à medida que os dados evoluem. 

As diretrizes a seguir orientam como atender a esses requisitos para Code First aplicativos usando ferramentas de banco de dados elástico. 

## <a name="data-dependent-routing-using-ef-dbcontext"></a>Roteamento dependente de dados usando o EF DbContext

Conexões de banco de dados com Entity Framework normalmente são gerenciadas por meio de subclasses de **DbContext**. Crie essas subclasses derivando de **DbContext**. É aqui que você define seu **DbSets** que implementa as coleções com suporte de banco de dados de objetos CLR para seu aplicativo. No contexto do roteamento dependente de dados, você pode identificar várias propriedades úteis que não necessariamente mantêm outros cenários do aplicativo do código EF: 

* O banco de dados já existe e foi registrado no mapa de fragmentos do banco de dados elástico. 
* O esquema do aplicativo já foi implantado no banco de dados (explicado abaixo). 
* As conexões de roteamento dependentes de dados para o banco de dado são orientadas pelo mapa de fragmentos. 

Para integrar o **DbContexts** ao roteamento dependente de dados para expansão:

1. Criar conexões de banco de dados físico por meio das interfaces de cliente do banco de dados elástico do Gerenciador de mapa de fragmentos, 
2. Encapsular a conexão com a subclasse **DbContext**
3. Passe a conexão para baixo nas classes base **DbContext** para garantir que todo o processamento no lado do EF também aconteça. 

O exemplo de código a seguir ilustra essa abordagem. (Esse código também está no projeto do Visual Studio que o acompanha)

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

* Um novo Construtor substitui o construtor padrão na subclasse DbContext 
* O novo construtor usa os argumentos que são necessários para roteamento dependente de dados por meio da biblioteca de cliente de banco de dados elástico:
  
  * o mapa de fragmentos para acessar as interfaces de roteamento dependentes de dados,
  * a chave de fragmentação para identificar o shardlet,
  * uma cadeia de conexão com as credenciais para a conexão de roteamento dependente de dados para o fragmento. 
* A chamada para o construtor da classe base leva um desvio em um método estático que executa todas as etapas necessárias para o roteamento dependente de dados. 
  
  * Ele usa a chamada OpenConnectionForKey das interfaces de cliente do banco de dados elástico no mapa de fragmentos para estabelecer uma conexão aberta.
  * O mapa de fragmentos cria a conexão aberta com o fragmento que mantém o shardlet para a chave de fragmentação especificada.
  * Essa conexão aberta é passada de volta para o construtor da classe base de DbContext para indicar que essa conexão deve ser usada pelo EF em vez de permitir que o EF crie uma nova conexão automaticamente. Dessa forma, a conexão foi marcada pela API do cliente do banco de dados elástico para que possa garantir a consistência em operações de gerenciamento do mapa de fragmentos.

Use o novo construtor para sua subclasse DbContext em vez do construtor padrão em seu código. Segue-se um exemplo: 

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

O novo Construtor abre a conexão com o fragmento que contém os dados para o shardlet identificado pelo valor de **tenantid1**. O código no bloco **using** permanece inalterado para acessar o **DbSet** para Blogs usando o EF no fragmento para **tenantid1**. Isso altera a semântica do código no bloco Using, de modo que todas as operações de banco de dados agora estejam no escopo de um fragmento em que **tenantid1** é mantida. Por exemplo, uma consulta LINQ sobre os Blogs **DbSet** retornaria apenas Blogs armazenados no fragmento atual, mas não aqueles armazenados em outros fragmentos.  

#### <a name="transient-faults-handling"></a>Tratamento de falhas transitórias

A equipe de práticas de & de padrões da Microsoft publicou o [bloco de aplicativos para tratamento de falhas transitórias](https://msdn.microsoft.com/library/dn440719.aspx). A biblioteca é usada com a biblioteca de cliente de escala elástica em combinação com o EF. No entanto, certifique-se de que qualquer exceção transitória retorne para um local onde você possa garantir que o novo Construtor esteja sendo usado após uma falha transitória para que qualquer nova tentativa de conexão seja feita usando os construtores que você ajuste. Caso contrário, uma conexão com o fragmento correto não será garantida e não haverá garantia de que a conexão será mantida conforme as alterações no mapa de fragmentos ocorrerem. 

O exemplo de código a seguir ilustra como uma política de repetição SQL pode ser usada em relação aos novos construtores de subclasse **DbContext** : 

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

**SqlDatabaseUtils. SqlRetryPolicy** no código acima é definido como um **SqlDatabaseTransientErrorDetectionStrategy** com uma contagem de repetições de 10 e 5 segundos de tempo de espera entre repetições. Essa abordagem é semelhante à orientação para o EF e transações iniciadas pelo usuário (consulte [limitações com a repetição de estratégias de execução (EF6 em diante)](https://msdn.microsoft.com/data/dn307226). Ambas as situações exigem que o programa do aplicativo controle o escopo para o qual a exceção transitória retorna: para reabrir a transação ou (conforme mostrado) recrie o contexto do Construtor adequado que usa a biblioteca de cliente do banco de dados elástico.

A necessidade de controlar onde as exceções transitórias nos leva de volta ao escopo também impede o uso do **SqlAzureExecutionStrategy** interno que vem com o EF. **SqlAzureExecutionStrategy** reabriria uma conexão, mas não usará **OpenConnectionForKey** e, portanto, ignorará toda a validação que é executada como parte da chamada **OpenConnectionForKey** . Em vez disso, o exemplo de código usa o **DefaultExecutionStrategy** interno que também vem com o EF. Em oposição ao **SqlAzureExecutionStrategy**, ele funciona corretamente em combinação com a política de repetição de tratamento de falhas transitórias. A política de execução é definida na classe **ElasticScaleDbConfiguration** . Observe que decidimos não usar **DefaultSqlExecutionStrategy** porque ele sugere o uso de **SqlAzureExecutionStrategy** se ocorrerem exceções transitórias, o que levaria a um comportamento incorreto conforme discutido. Para obter mais informações sobre as diferentes políticas de repetição e o EF, consulte [resiliência de conexão no EF](https://msdn.microsoft.com/data/dn456835.aspx).     

#### <a name="constructor-rewrites"></a>Regravações do Construtor

Os exemplos de código acima ilustram as regravações de construtor padrão necessárias para seu aplicativo a fim de usar o roteamento dependente de dados com o Entity Framework. A tabela a seguir generaliza essa abordagem para outros construtores. 

| Construtor atual | Construtor reescrito para dados | Construtor base | Notas |
| --- | --- | --- | --- |
| MyContext () |ElasticScaleContext (ShardMap, TKey) |DbContext (DbConnection, bool) |A conexão precisa ser uma função do mapa de fragmentos e da chave de roteamento dependente de dados. Você precisa passar a criação de conexão automática pelo EF e, em vez disso, usar o mapa de fragmentos para o agente da conexão. |
| MyContext (cadeia de caracteres) |ElasticScaleContext (ShardMap, TKey) |DbContext (DbConnection, bool) |A conexão é uma função do mapa de fragmentos e da chave de roteamento dependente de dados. Um nome de banco de dados fixo ou uma cadeia de conexão não funciona como aprovado pela validação pelo mapa de fragmentos. |
| MyContext (DbCompiledModel) |ElasticScaleContext (ShardMap, TKey, DbCompiledModel) |DbContext (DbConnection, DbCompiledModel, bool) |A conexão é criada para o mapa de fragmentos e a chave de fragmentação fornecidos com o modelo fornecido. O modelo compilado é passado para o c'tor de base. |
| MyContext (DbConnection, bool) |ElasticScaleContext (ShardMap, TKey, bool) |DbContext (DbConnection, bool) |A conexão precisa ser inferida do mapa de fragmentos e da chave. Ele não pode ser fornecido como uma entrada (a menos que essa entrada já esteja usando o mapa de fragmentos e a chave). O booliano é passado. |
| MyContext (String, DbCompiledModel) |ElasticScaleContext (ShardMap, TKey, DbCompiledModel) |DbContext (DbConnection, DbCompiledModel, bool) |A conexão precisa ser inferida do mapa de fragmentos e da chave. Ele não pode ser fornecido como uma entrada (a menos que essa entrada esteja usando o mapa de fragmentos e a chave). O modelo compilado é passado. |
| MyContext (ObjectContext, bool) |ElasticScaleContext (ShardMap, TKey, ObjectContext, bool) |DbContext (ObjectContext, bool) |O novo Construtor precisa garantir que qualquer conexão no ObjectContext passada como uma entrada seja roteada novamente para uma conexão gerenciada pela escala elástica. Uma discussão detalhada sobre objectcontexts está além do escopo deste documento. |
| MyContext (DbConnection, DbCompiledModel, bool) |ElasticScaleContext (ShardMap, TKey, DbCompiledModel, bool) |DbContext (DbConnection, DbCompiledModel, bool); |A conexão precisa ser inferida do mapa de fragmentos e da chave. A conexão não pode ser fornecida como uma entrada (a menos que essa entrada já esteja usando o mapa de fragmentos e a chave). O modelo e o booliano são passados para o construtor da classe base. |

## <a name="shard-schema-deployment-through-ef-migrations"></a>Implantação de esquema de fragmento por meio de migrações do EF

O gerenciamento automático de esquema é uma conveniência fornecida pelo Entity Framework. No contexto de aplicativos que usam ferramentas de banco de dados elástico, você deseja manter esse recurso para provisionar automaticamente o esquema para fragmentos recém-criados quando os bancos de dados são adicionados ao aplicativo fragmentado. O caso de uso primário é aumentar a capacidade na camada de dados para aplicativos fragmentados usando o EF. Depender de recursos do EF para gerenciamento de esquema reduz o esforço de administração de banco de dados com um aplicativo fragmentado criado no EF. 

A implantação de esquema por meio de migrações do EF funciona melhor em **conexões não abertas**. Isso está em contraste com o cenário do roteamento dependente de dados que depende da conexão aberta fornecida pela API do cliente do Database elástico. Outra diferença é o requisito de consistência: embora seja desejável garantir a consistência de todas as conexões de roteamento dependentes de dados para proteger contra a manipulação simultânea de mapa de fragmentos, isso não é uma preocupação com a implantação de esquema inicial em um novo banco de dados que Ainda não foi registrado no mapa de fragmentos e ainda não foi alocado para conter shardlets. Portanto, você pode contar com conexões de banco de dados regulares para esse cenário, em oposição ao roteamento dependente de dado.  

Isso leva a uma abordagem em que a implantação de esquema por meio de migrações do EF está rigidamente acoplada ao registro do novo banco de dados como um fragmento no mapa de fragmentos do aplicativo. Isso depende dos seguintes pré-requisitos: 

* O banco de dados já foi criado. 
* O banco de dados está vazio-ele não contém nenhum esquema de usuário e nenhum dado de usuário.
* O banco de dados ainda não pode ser acessado por meio de APIs de cliente do banco de dados elástico para roteamento dependente de dado. 

Com esses pré-requisitos em vigor, você pode criar um **SqlConnection** não aberto regular para iniciar migrações do EF para implantação de esquema. O exemplo de código a seguir ilustra essa abordagem. 

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

Este exemplo mostra o método **RegisterNewShard** que registra o fragmento no mapa de fragmentos, implanta o esquema por meio de migrações do EF e armazena um mapeamento de uma chave de fragmentação para o fragmento. Ele se baseia em um construtor da subclasse **DbContext** (**ElasticScaleContext** no exemplo) que usa uma cadeia de conexão SQL como entrada. O código desse construtor é direto, como mostra o exemplo a seguir: 

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

Uma delas pode ter usado a versão do Construtor herdada da classe base. Mas o código precisa garantir que o inicializador padrão para o EF seja usado durante a conexão. Portanto, o desvio curto no método estático antes de chamar o construtor da classe base com a cadeia de conexão. Observe que o registro de fragmentos deve ser executado em um domínio ou processo de aplicativo diferente para garantir que as configurações de inicializador do EF não entrem em conflito. 

## <a name="limitations"></a>Limitações

As abordagens descritas neste documento envolvem algumas limitações: 

* Os aplicativos do EF que usam o **LocalDb** primeiro precisam migrar para um banco de dados SQL Server regular antes de usar a biblioteca de cliente do banco de dados elástico. Não é possível escalar horizontalmente um aplicativo por meio de fragmentação com escala elástica com o **LocalDb**. Observe que o desenvolvimento ainda pode usar o **LocalDb**. 
* Todas as alterações no aplicativo que implicam alterações no esquema de banco de dados precisam passar por migrações do EF em todos os fragmentos. O código de exemplo deste documento não demonstra como fazer isso. Considere o uso de Update-Database com um parâmetro ConnectionString para iterar em todos os fragmentos; ou Extraia o script T-SQL para a migração pendente usando Update-Database com a opção-script e aplique o script T-SQL aos seus fragmentos.  
* Devido a uma solicitação, supõe-se que todo o processamento do banco de dados está contido em um único fragmento, conforme identificado pela chave de fragmentação fornecida pela solicitação. No entanto, essa suposição nem sempre é verdadeira. Por exemplo, quando não é possível disponibilizar uma chave de fragmentação. Para resolver isso, a biblioteca de cliente fornece a classe **MultiShardQuery** que implementa uma abstração de conexão para consultar vários fragmentos. Aprender a usar o **MultiShardQuery** em combinação com o EF está além do escopo deste documento

## <a name="conclusion"></a>Conclusão

Por meio das etapas descritas neste documento, os aplicativos do EF podem usar a funcionalidade da biblioteca de cliente do banco de dados elástico para o roteamento dependente de dado por construtores de refatoração das subclasses **DbContext** usadas no aplicativo EF. Isso limita as alterações necessárias para os locais em que as classes **DbContext** já existem. Além disso, os aplicativos do EF podem continuar a se beneficiar da implantação automática de esquema, combinando as etapas que invocam as migrações necessárias do EF com o registro de novos fragmentos e mapeamentos no mapa de fragmentos. 

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-use-entity-framework-applications-visual-studio/sample.png
