---
title: Utilização de biblioteca de clientes de base de dados elástica com Enquadramento de Entidade
description: Utilizar biblioteca de clientes elásticos e quadro de entidades para codificar bases de dados
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/04/2019
ms.openlocfilehash: 8eafd99f07c64c20565a954216341f3dea9541b0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91442655"
---
# <a name="elastic-database-client-library-with-entity-framework"></a>Biblioteca de clientes de base de dados elástica com enquadramento de entidade
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Este documento mostra as alterações de uma aplicação Quadro de Entidades que são necessárias para integrar com as [ferramentas Elastic Database](elastic-scale-introduction.md). O foco está em compor a [gestão de mapas de fragmentos](elastic-scale-shard-map-management.md) e [encaminhamento dependente de dados](elastic-scale-data-dependent-routing.md) com a abordagem Entity Framework **Code First.** O código primeiro - novo tutorial [de base de dados](https://msdn.microsoft.com/data/jj193542.aspx) para a EF serve de exemplo de execução ao longo deste documento. O código de amostra que acompanha este documento faz parte do conjunto de amostras de ferramentas de base de dados elásticas nas amostras do Código do Estúdio Visual.

## <a name="downloading-and-running-the-sample-code"></a>Descarregar e executar o Código da Amostra

Para descarregar o código para este artigo:

* O Visual Studio 2012 ou mais tarde é necessário.
* Descarregue as [Ferramentas Elásticas DB para Azure SQL - Amostra de Integração De Quadros de Entidades.](https://github.com/Azure/elastic-db-tools/) Desaperte a amostra para um local à sua escolha.
* Inicie o Visual Studio.
* No Estúdio Visual, selecione File -> Open Project/Solution.
* No diálogo **Open Project,** navegue para a amostra que descarregou e selecione **EntityFrameworkCodeFirst.sln** para abrir a amostra.

Para executar a amostra, é necessário criar três bases de dados vazias na Base de Dados Azure SQL:

* Base de dados do Gestor de Mapas de Fragmentos
* Base de dados Shard 1
* Base de dados Shard 2

Assim que tiver criado estas bases de dados, preencha os titulares de lugares em **Program.cs** com o nome do seu servidor, os nomes da base de dados e as suas credenciais para se ligar às bases de dados. Construa a solução no Visual Studio. O Visual Studio descarrega os pacotes NuGet necessários para a biblioteca de clientes de base de dados elástica, o Quadro de Entidades e o tratamento de Falhas Transitórias como parte do processo de construção. Certifique-se de que a restauração dos pacotes NuGet está ativada para a sua solução. Pode ativar esta definição clicando corretamente no ficheiro de solução no Visual Studio Solution Explorer.

## <a name="entity-framework-workflows"></a>Fluxos de trabalho do Quadro de Entidades

Os desenvolvedores do Quadro de Entidades contam com um dos quatro fluxos de trabalho seguintes para construir aplicações e garantir a persistência de objetos de aplicação:

* **Código Primeiro (Nova Base de Dados)**: O desenvolvedor EF cria o modelo no código de aplicação e, em seguida, a EF gera a base de dados a partir dele.
* **Código Primeiro (Base de Dados Existente)**: O desenvolvedor permite que a EF gere o código de aplicação do modelo a partir de uma base de dados existente.
* **Modelo Primeiro**: O desenvolvedor cria o modelo no designer EF e, em seguida, a EF cria a base de dados a partir do modelo.
* **Base de dados Primeiro**: O desenvolvedor utiliza ferramentas EF para inferir o modelo a partir de uma base de dados existente.

Todas estas abordagens dependem da classe DbContext para gerir de forma transparente as ligações de base de dados e o esquema de base de dados para uma aplicação. Diferentes construtores na classe base DbContext permitem diferentes níveis de controlo sobre a criação de ligação, a colocação de botas de base de dados e a criação de esquemas. Os desafios surgem principalmente do facto de a gestão da ligação de base de dados fornecida pela EF se cruzar com as capacidades de gestão de ligação das interfaces de encaminhamento dependentes de dados fornecidas pela biblioteca de clientes de base de dados elástica.

## <a name="elastic-database-tools-assumptions"></a>Pressupostos de ferramentas de base de dados elásticas

Para definições de termo, consulte [o glossário de ferramentas elastic database](elastic-scale-glossary.md).

Com biblioteca de clientes de base de dados elástica, define as divisórias dos dados da sua aplicação chamadas fragmentos. Os fragmentos são identificados por uma chave de fragmentos e são mapeados para bases de dados específicas. Uma aplicação pode ter o número de bases de dados necessárias e distribuir os fragmentos para fornecer capacidade ou desempenho suficientes dadas as necessidades atuais do negócio. O mapeamento dos valores-chave de fragmentos nas bases de dados é armazenado por um mapa de fragmentos fornecido pelas APIs do cliente da base de dados elástica. Esta capacidade chama-se **Shard Map Management,** ou SMM para abreviar. O mapa de fragmentos também serve como corretor de ligações de base de dados para pedidos que carregam uma chave de fragmentos. Esta capacidade é conhecida como **encaminhamento dependente de dados**.

O gestor de mapas de fragmentos protege os utilizadores de visões inconsistentes em dados de shardlet que podem ocorrer quando estão a acontecer operações de gestão de fragmentos simultâneas (como a relocalização de dados de um fragmento para outro). Para tal, os mapas de fragmentos geridos pelo corretor da biblioteca do cliente são as ligações de base de dados para uma aplicação. Isto permite que a funcionalidade do mapa de fragmentos mate automaticamente uma ligação de base de dados quando as operações de gestão de fragmentos podem ter impacto no fragmento para o que a ligação foi criada. Esta abordagem tem de ser integrado com algumas das funcionalidades da EF, tais como a criação de novas ligações a partir de uma existente para verificar a existência de bases de dados. Em geral, a nossa observação tem sido a de que os construtores padrão DbContext apenas funcionam de forma fiável para ligações de base de dados fechadas que podem ser clonadas com segurança para trabalhos de EF. Em vez disso, o princípio de conceção da base de dados elástica é apenas ligar a corretora aberta. Poder-se-ia pensar que fechar uma ligação intermediada pela biblioteca do cliente antes de a entregar ao EF DbContext pode resolver este problema. No entanto, ao fechar a ligação e contar com a EF para a reabrir, renuncia-se à validação e à verificação de consistência realizadas pela biblioteca. No entanto, a funcionalidade de migração na EF utiliza estas ligações para gerir o esquema de base de dados subjacente de uma forma transparente à aplicação. Idealmente, irá reter e combinar todas estas capacidades tanto da biblioteca de clientes de base de dados elástica como da EF na mesma aplicação. A secção seguinte discute mais detalhadamente estas propriedades e requisitos.

## <a name="requirements"></a>Requisitos

Ao trabalhar com a biblioteca de clientes de base de dados elástica e as APIs do Quadro de Entidades, pretende reter as seguintes propriedades:

* **Escala:** Para adicionar ou remover bases de dados do nível de dados da aplicação emperrada, conforme necessário para as exigências de capacidade da aplicação. Isto significa o controlo sobre a criação e eliminação de bases de dados e a utilização da base de dados elástica de apIs do gestor de mapas para gerir bases de dados e mapeamentos de shardlets.
* **Consistência**: A aplicação emprega fragmentos e utiliza as capacidades de encaminhamento dependentes de dados da biblioteca do cliente. Para evitar corrupção ou resultados de consultas erradas, as ligações são intermediadas através do gestor de mapas de fragmentos. Isto também mantém validação e consistência.
* **Código Primeiro**: Manter a conveniência do primeiro paradigma do código da EF. No Código Primeiro, as classes da aplicação são mapeadas de forma transparente para as estruturas de base de dados subjacentes. O código de aplicação interage com DbSets que mascaram a maioria dos aspetos envolvidos no processamento subjacente da base de dados.
* **Schema**: O Quadro de Entidades trata da criação inicial de esquemas de base de dados e da evolução subsequente do esquema através de migrações. Ao reter estas capacidades, a adaptação da sua app é fácil à medida que os dados evoluem.

A seguinte orientação instrui como satisfazer estes requisitos para aplicações code first usando ferramentas de base de dados elásticas.

## <a name="data-dependent-routing-using-ef-dbcontext"></a>Encaminhamento dependente de dados utilizando EF DbContext

As ligações de base de dados com o Quadro de Entidades são normalmente geridas através de subclasses de **DbContext**. Crie estas subclasses derivando da **DbContext**. É aqui que define os seus **DbSets** que implementam as coleções apoiadas na base de dados de objetos CLR para a sua aplicação. No contexto do encaminhamento dependente de dados, pode identificar várias propriedades úteis que não são necessariamente guardando para outros cenários de primeira aplicação do código EF:

* A base de dados já existe e foi registada no mapa de fragmentos da base de dados elástica.
* O esquema da aplicação já foi implementado na base de dados (explicado abaixo).
* As ligações de encaminhamento dependentes de dados para a base de dados são intermediadas pelo mapa de fragmentos.

Para integrar **os textos DbContexts** com encaminhamento dependente de dados para a escala:

1. Criar ligações físicas de base de dados através das interfaces elásticas do cliente da base de dados elástica do gestor de mapas de fragmentos.
2. Embrulhe a ligação com a subclasse **DbContext**
3. Passe a ligação para baixo para as classes base **DbContext** para garantir que todo o processamento do lado EF também acontece.

O seguinte exemplo de código ilustra esta abordagem. (Este código também está no projeto do Estúdio Visual que acompanha)

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

* Um novo construtor substitui o construtor predefinido na subclasse DbContext
* O novo construtor assume os argumentos necessários para o encaminhamento dependente de dados através da biblioteca de clientes de base de dados elástica:
  
  * o mapa de fragmentos para aceder às interfaces de encaminhamento dependentes de dados,
  * a chave de fragmentos para identificar o fragmento,
  * uma cadeia de ligação com as credenciais para a ligação de encaminhamento dependente de dados ao fragmento.
* A chamada para o construtor de classe base leva um desvio para um método estático que executa todos os passos necessários para o encaminhamento dependente de dados.
  
  * Utiliza a chamada OpenConnectionForKey das interfaces elásticas do cliente da base de dados no mapa de fragmentos para estabelecer uma ligação aberta.
  * O mapa de fragmentos cria a ligação aberta ao fragmento que segura o fragmento para a chave de fragmento dada.
  * Esta ligação aberta é repercutido no construtor de classe base da DbContext para indicar que esta ligação deve ser utilizada pela EF em vez de permitir que a EF crie uma nova ligação automaticamente. Desta forma, a ligação foi marcada pela API do cliente de base de dados elástica para que possa garantir a consistência em operações de gestão de mapas de fragmentos.

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

O novo construtor abre a ligação ao fragmento que detém os dados para o fragmento identificado pelo valor do **inquilino 1**. O código do bloco **de utilização** permanece inalterado para aceder ao **DbSet** para blogs que utilizem EF no shard para **inquilinos1**. Isto altera a semântica para o código no bloco de utilização de modo a que todas as operações de base de dados sejam agora traçadas para o fragmento onde o **tenantid1** é mantido. Por exemplo, uma consulta linq sobre os blogs **DbSet** só devolveria blogs armazenados no fragmento atual, mas não os armazenados em outros fragmentos.  

### <a name="transient-faults-handling"></a>Tratamento de falhas transitórias

A equipa de práticas de & microsoft Patterns publicou o [Bloco de Aplicações de Tratamento de Falhas Transitórias](https://msdn.microsoft.com/library/dn440719.aspx). A biblioteca é utilizada com biblioteca de clientes em escala elástica em combinação com a EF. No entanto, certifique-se de que qualquer exceção transitória regressa a um local onde possa garantir que o novo construtor está a ser utilizado após uma falha transitória, de modo a que qualquer nova tentativa de ligação seja feita utilizando os construtores que alterou. Caso contrário, não é garantida uma ligação ao fragmento correto e não existem garantias de que a ligação seja mantida à medida que ocorrem alterações ao mapa de fragmentos.

A seguinte amostra de código ilustra como uma política de relíndição SQL pode ser usada em torno dos novos construtores de subclasse **DbContext:**

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

**SqlDatabaseUtils.SqlRetryPolicy** no código acima é definido como um **SqlDataTransientErrorDetectionStrategy** com uma contagem de 10 e 5 segundos de espera entre retréis. Esta abordagem é semelhante à orientação para as transações de EF e iniciadas pelo utilizador (ver [Limitações com Estratégias de Execução DeStripadas (EF6 em diante)](https://msdn.microsoft.com/data/dn307226). Ambas as situações exigem que o programa de aplicação controle o âmbito a que a exceção transitória retorna: ou reabrir a transação, ou (como mostrado) recriar o contexto do construtor adequado que utiliza a biblioteca de clientes de base de dados elástica.

A necessidade de controlar onde as exceções transitórias nos levam de volta ao seu âmbito também impede a utilização da **sqlAzureExecutionStrategy** que vem com a EF. **SqlAzureExecutionStrategy** reabriria uma ligação, mas não utilizaria **o OpenConnectionForKey** e, portanto, contornaria toda a validação que é realizada como parte da chamada **OpenConnectionForKey.** Em vez disso, a amostra de código utiliza a **versão defaultExecutionStrategy** incorporada que também vem com EF. Ao contrário do **SqlAzureExecutionStrategy,** funciona corretamente em combinação com a política de reagem a partir do Tratamento de Falhas Transitórias. A política de execução está definida na classe **ElasticScaleDbConfiguration.** Note que decidimos não usar **a DefaultSqlExecutionStrategy** uma vez que sugere a utilização **de SqlAzureExecutionStrategy** se ocorrerem exceções transitórias - o que levaria a um comportamento errado como discutido. Para obter mais informações sobre as diferentes políticas de relícuro e EF, consulte [a Connection Resiliency in EF](https://msdn.microsoft.com/data/dn456835.aspx).

#### <a name="constructor-rewrites"></a>Construtor reescreve

Os exemplos de código acima ilustram as reescaminações de construtores predefinidas necessárias para a sua aplicação, a fim de utilizar o encaminhamento dependente de dados com o Quadro de Entidades. A tabela a seguir generaliza esta abordagem a outros construtores.

| Construtor atual | Construtor reescrito para dados | Construtor de base | Notas |
| --- | --- | --- | --- |
| MyContext() |ElasticScaleContext (ShardMap, TKey) |DbContext (DbConnection, bool) |A ligação tem de ser uma função do mapa de fragmentos e da chave de encaminhamento dependente de dados. Você precisa de passar a criação automática de ligação por EF e, em vez disso, usar o mapa de fragmentos para intermediar a ligação. |
| MyContext(cadeia) |ElasticScaleContext (ShardMap, TKey) |DbContext (DbConnection, bool) |A ligação é uma função do mapa de fragmentos e da chave de encaminhamento dependente de dados. Um nome de base de dados fixo ou uma cadeia de ligação não funciona como a validação por by-pass pelo mapa de fragmentos. |
| MyContext (DbCompiledModel) |ElasticScaleContext (ShardMap, TKey, DbCompiledModel) |DbContext (DbConnection, DbCompiledModel, bool) |A ligação é criada para o mapa de fragmentos dado e chave de fragmentos com o modelo fornecido. O modelo compilado é passado para a base c'tor. |
| MyContext (DbConnection, bool) |ElasticScaleContext (ShardMap, TKey, bool) |DbContext (DbConnection, bool) |A ligação tem de ser deduzida do mapa de fragmentos e da chave. Não pode ser fornecido como uma entrada (a menos que essa entrada já estivesse usando o mapa de fragmentos e a chave). O Boolean é passado. |
| MyContext (string, DbCompiledModel) |ElasticScaleContext (ShardMap, TKey, DbCompiledModel) |DbContext (DbConnection, DbCompiledModel, bool) |A ligação tem de ser deduzida do mapa de fragmentos e da chave. Não pode ser fornecida como uma entrada (a menos que essa entrada tenha usado o mapa de fragmentos e a chave). O modelo compilado é transmitido. |
| MyContext (ObjectContext, bool) |ElasticScaleContext (ShardMap, TKey, ObjectContext, bool) |DbContext (ObjectContext, bool) |O novo construtor deve garantir que qualquer ligação no ObjectContext passou como entrada é redirecionada para uma ligação gerida por Escala Elástica. Uma discussão detalhada sobre os textos objectcontexts está fora do âmbito deste documento. |
| MyContext (DbConnection, DbCompiledModel, bool) |ElasticScaleContext (ShardMap, TKey, DbCompiledModel, bool) |DbContext (DbConnection, DbCompiledModel, bool); |A ligação tem de ser deduzida do mapa de fragmentos e da chave. A ligação não pode ser fornecida como uma entrada (a menos que essa entrada já estivesse usando o mapa de fragmentos e a chave). Modelo e Boolean são passados para o construtor de classe base. |

## <a name="shard-schema-deployment-through-ef-migrations"></a>Implantação de esquemas de fragmentos através de migrações ef

A gestão automática de esquemas é uma conveniência fornecida pelo Quadro de Entidades. No contexto das aplicações que utilizam ferramentas de base de dados elásticas, pretende reter esta capacidade de provisão automática do esquema a fragmentos recém-criados quando as bases de dados são adicionadas à aplicação de fragmentos. O caso de utilização primária consiste em aumentar a capacidade no nível de dados para aplicações com cacos utilizando a EF. Confiar nas capacidades da EF para a gestão de esquemas reduz o esforço de administração da base de dados com uma aplicação de sharded construída sobre a EF.

A implantação de esquemas através das migrações EF funciona melhor em **ligações não abertas**. Isto contrasta com o cenário de encaminhamento dependente de dados que se baseia na ligação aberta fornecida pela API do cliente de base de dados elástica. Outra diferença é a exigência de coerência: Embora desejável para garantir a coerência de todas as ligações de encaminhamento dependentes de dados para proteger contra manipulação simultânea de mapas de fragmentos, não é uma preocupação com a implantação inicial do esquema para uma nova base de dados que ainda não foi registada no mapa de fragmentos, e ainda não foi atribuída para segurar as shardlets. Pode, portanto, contar com ligações regulares de base de dados para este cenário, em oposição ao encaminhamento dependente de dados.  

Isto leva a uma abordagem em que a implantação de esquemas através das migrações EF está estreitamente associada ao registo da nova base de dados como fragmento no mapa de fragmentos da aplicação. Isto baseia-se nos seguintes pré-requisitos:

* A base de dados já foi criada.
* A base de dados está vazia - não contém nenhum esquema do utilizador e nenhum dado do utilizador.
* A base de dados ainda não pode ser acedida através das APIs do cliente da base de dados elástica para encaminhamento dependente de dados.

Com estes pré-requisitos no lugar, você pode criar um **SqlConnection** não aberto regularmente para iniciar migrações EF para implantação de esquemas. A seguinte amostra de código ilustra esta abordagem.

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

Esta amostra mostra o método **RegisterNewShard** que regista o fragmento no mapa de fragmentos, implanta o esquema através das migrações EF e armazena um mapeamento de uma chave de fragmentos para o fragmento. Baseia-se num construtor da subclasse **DbContext** **(ElasticScaleContext** na amostra) que toma como entrada uma corda de ligação SQL. O código deste construtor é simples, como mostra o seguinte exemplo:

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

Pode-se ter usado a versão do construtor herdado da classe base. Mas o código precisa de garantir que o inicializador predefinido para EF é utilizado na ligação. Daí o pequeno desvio para o método estático antes de chamar para o construtor da classe base com a cadeia de ligação. Note que o registo de fragmentos deve ser executado em um domínio ou processo de aplicação diferente para garantir que as definições do inicializador para EF não entram em conflito.

## <a name="limitations"></a>Limitações

As abordagens descritas neste documento implicam algumas limitações:

* As aplicações EF que utilizam **o LocalDb** precisam primeiro de migrar para uma base de dados regular do SQL Server antes de utilizar a biblioteca de clientes de base de dados elástica. Escalonar uma aplicação através de fragmentos com escala elástica não é possível com **o LocalDb**. Note que o desenvolvimento ainda pode usar **o LocalDb.**
* Quaisquer alterações à aplicação que impliquem alterações no esquema de bases de dados devem passar por migrações de EF em todos os fragmentos. O código de amostra deste documento não demonstra como fazê-lo. Considere utilizar Update-Database com um parâmetro ConnectionString para iterar sobre todos os fragmentos; ou extrair o script T-SQL para a migração pendente usando Update-Database com a opção -Script e aplicar o script T-SQL nos seus fragmentos.  
* Dado um pedido, presume-se que todo o seu processamento de base de dados está contido num único fragmento identificado pela chave de fragmentos fornecida pelo pedido. No entanto, esta suposição nem sempre é verdadeira. Por exemplo, quando não é possível disponibilizar uma chave de cacos. Para resolver este problema, a biblioteca do cliente fornece a classe **MultiShardQuery** que implementa uma abstração de conexão para consulta sobre vários fragmentos. Aprender a usar o **MultiShardQuery** em combinação com a EF está fora do âmbito deste documento

## <a name="conclusion"></a>Conclusão

Através das etapas descritas neste documento, as aplicações EF podem utilizar a capacidade da biblioteca de clientes de base de dados elástica para o encaminhamento dependente de dados, refactorizando os construtores das subclasses **DbContext** utilizadas na aplicação EF. Isto limita as alterações necessárias aos locais onde já existem aulas **de DbContext.** Além disso, as aplicações da EF podem continuar a beneficiar da implantação automática de esquemas, combinando as etapas que invocam as necessárias migrações de EF com o registo de novos fragmentos e mapeamentos no mapa de fragmentos.

[!INCLUDE [elastic-scale-include](../../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-use-entity-framework-applications-visual-studio/sample.png
