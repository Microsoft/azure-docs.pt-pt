---
title: Aplicações multi-inquilino com as ferramentas de bases de dados elásticas e RLS | Documentos da Microsoft
description: Utilize ferramentas de base de dados elástica com segurança ao nível da linha para criar uma aplicação com uma camada de dados altamente dimensionável.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: VanMSFT
ms.author: vanto
ms.reviewer: sstein
manager: craigg
ms.date: 12/18/2018
ms.openlocfilehash: 71d2d542d71977f9d8dfe07370dffd7fe508bc92
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2019
ms.locfileid: "57314964"
---
# <a name="multi-tenant-applications-with-elastic-database-tools-and-row-level-security"></a>Aplicações multi-inquilino com ferramentas de base de dados elástica e segurança ao nível da linha

[Ferramentas de bases de dados elásticas](sql-database-elastic-scale-get-started.md) e [ao nível da linha (RLS) de segurança] [ rls] cooperam para ativar o dimensionamento a camada de dados de uma aplicação multi-inquilino com a base de dados do Azure SQL. Quando combinadas, essas tecnologias ajudam-o a criar uma aplicação que tenha uma camada de dados altamente dimensionável. A camada de dados suporta fragmentos multi-inquilino e usa **ADO.NET SqlClient** ou **Entity Framework**. Para obter mais informações, consulte [padrões de Design para aplicações de SaaS multi-inquilino com a base de dados do Azure SQL](saas-tenancy-app-design-patterns.md).

- **Ferramentas de bases de dados elásticas** permitem que os desenvolvedores a ampliar a camada de dados com as práticas de fragmentação padrão, utilizando bibliotecas .NET e modelos de serviço do Azure. Gerir partições horizontais com o [biblioteca de clientes de base de dados elástica] [ s-d-elastic-database-client-library] ajuda a automatizar e simplificar muitas das tarefas de infraestrutura normalmente associadas a fragmentação.
- **Segurança de nível de linha** permite que os desenvolvedores armazenar com segurança os dados de vários inquilinos na mesma base de dados. As políticas de segurança RLS filtram as linhas que não pertencem ao inquilino de execução de uma consulta. Centralizando a lógica de filtro no banco de dados simplifica a manutenção e reduz o risco de um erro de segurança. A alternativa de depender de todo o código de cliente para impor a segurança é arriscada.

Ao utilizar estas funcionalidades em conjunto, um aplicativo pode armazenar dados de vários inquilinos na mesma base de dados de partição horizontal. Ele tem um custo inferior por inquilino quando os inquilinos partilham uma base de dados. Ainda a mesma aplicação pode também oferecer seus inquilinos premium a opção de pagar pelos seus próprios fragmentos de inquilino único dedicado. Uma vantagem de isolamento de inquilino único é firmer garantias de desempenho. Num banco de dados de inquilino único, não existe nenhum outro inquilino competindo por recursos.

O objetivo é utilizar a biblioteca de cliente da base de dados elástica [encaminhamento dependente de dados](sql-database-elastic-scale-data-dependent-routing.md) APIs para ligar automaticamente a cada inquilino especificado para a base de dados de partição horizontal correto. Apenas uma partição contém o valor do TenantId específico para o inquilino especificado. É o TenantId a *chave de fragmentação*. Depois da ligação é estabelecida, uma política de segurança RLS no banco de dados garante que o inquilino especificado pode aceder apenas às linhas de dados que contêm o TenantId.

> [!NOTE]
> O identificador do inquilino pode consistir em mais de uma coluna. Para conveniência é esta discussão, presumimos informalmente um TenantId de coluna única.

![Arquitetura de aplicações de blogues][1]

## <a name="download-the-sample-project"></a>Transfira o projeto de exemplo

### <a name="prerequisites"></a>Pré-requisitos

- Utilizar o Visual Studio (2012 ou superior)
- Criar três bases de dados SQL do Azure
- Transfira o projeto de exemplo: [Ferramentas de DB elástico de SQL do Azure - partições horizontais de multi-inquilinos](https://go.microsoft.com/?linkid=9888163)
  - Preencha as informações nas bases de dados no início do **Program.cs**

Este projeto estende aquele descrito em [ferramentas elásticas de DB para SQL do Azure - integração de Entity Framework](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md) , adicionando suporte para bases de dados de partição horizontal do multi-inquilino. O projeto baseia-se uma aplicação de consola simples para criar blogues e publicações. O projeto inclui quatro inquilinos e duas bases de dados de partição horizontal do multi-inquilino. Esta configuração é ilustrada no diagrama anterior.

Crie e execute a aplicação. Esta execução arrancar o Gestor de mapas de partições horizontais das ferramentas de bases de dados elásticas e executa os seguintes testes:

1. Usando o Entity Framework e LINQ, criar um novo blogue e, em seguida, apresentar todos os blogs para cada inquilino
2. Usando o ADO.NET SqlClient, apresentar todos os blogs para um inquilino
3. Tentar inserir um blog para o inquilino errado verificar que é gerado um erro

Tenha em atenção que, uma vez que RLS ainda não tiver sido ativada nas bases de dados de partição horizontal, cada um desses testes revela um problema: os inquilinos são capazes de ver blogs que não pertencem aos mesmos e a aplicação não está impedida de inserir um blog para o inquilino errado. O restante deste artigo descreve como resolver estes problemas ao impor o isolamento de inquilino com a RLS. Há duas etapas:

1. **Camada de aplicativos**: Modificar o código do aplicativo sempre definir o TenantId atual na sessão\_contexto depois de abrir uma ligação. O projeto de exemplo define já o TenantId, desta forma.
2. **Camada de dados**: Criar uma política de segurança RLS em cada base de dados de partição horizontal para filtrar linhas com base no TenantId armazenado numa sessão\_contexto. Criar uma política para cada uma das suas bases de dados de partição horizontal, caso contrário, que não são filtradas linhas em partições horizontais de multi-inquilinos.

## <a name="1-application-tier-set-tenantid-in-the-sessioncontext"></a>1. Camada de aplicativos: Definir o TenantId na sessão\_contexto

Ligue primeiro para uma base de dados de partição horizontal com as APIs de encaminhamento dependente de dados da biblioteca de clientes de bases de dados elásticas. O aplicativo ainda tem de pedir ao banco de dados que TenantId está a utilizar a ligação. O TenantId informa sobre a política de segurança RLS que linhas devem ser filtradas como pertencentes de outros inquilinos. Store o TenantId atual no [sessão\_contexto](https://docs.microsoft.com/sql/t-sql/functions/session-context-transact-sql) da ligação.

Uma alternativa à sessão\_contexto é usar [contexto\_informações](https://docs.microsoft.com/sql/t-sql/functions/context-info-transact-sql). Mas sessão\_contexto é uma opção melhor. SESSÃO\_contexto é mais fácil de usar, ela retorna NULL por predefinição e suporta pares chave-valor.

### <a name="entity-framework"></a>Entity Framework

Para aplicativos usando o Entity Framework, a abordagem mais simples é definir a sessão\_contexto em substituição ElasticScaleContext descrita [dependente de dados, encaminhamento com o EF DbContext](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md#data-dependent-routing-using-ef-dbcontext). Criar e executar um SqlCommand que define o TenantId na sessão\_contexto para o shardingKey especificado para a ligação. Em seguida, volte a ligação mediada através do encaminhamento dependente de dados. Dessa forma, só precisa escrever código para definir a sessão\_contexto.

```csharp
// ElasticScaleContext.cs
// Constructor for data-dependent routing.
// This call opens a validated connection that is routed to the
// proper shard by the shard map manager.
// Note that the base class constructor call fails for an open connection
// if migrations need to be done and SQL credentials are used.
// This is the reason for the separation of constructors.
// ...
public ElasticScaleContext(ShardMap shardMap, T shardingKey, string connectionStr)
    : base(
        OpenDDRConnection(shardMap, shardingKey, connectionStr),
        true)  // contextOwnsConnection
{
}

public static SqlConnection OpenDDRConnection(
    ShardMap shardMap,
    T shardingKey,
    string connectionStr)
{
    // No initialization.
    Database.SetInitializer<ElasticScaleContext<T>>(null);

    // Ask shard map to broker a validated connection for the given key.
    SqlConnection conn = null;
    try
    {
        conn = shardMap.OpenConnectionForKey(
            shardingKey,
            connectionStr,
            ConnectionOptions.Validate);

        // Set TenantId in SESSION_CONTEXT to shardingKey
        // to enable Row-Level Security filtering.
        SqlCommand cmd = conn.CreateCommand();
        cmd.CommandText =
            @"exec sp_set_session_context
                @key=N'TenantId', @value=@shardingKey";
        cmd.Parameters.AddWithValue("@shardingKey", shardingKey);
        cmd.ExecuteNonQuery();

        return conn;
    }
    catch (Exception)
    {
        if (conn != null)
        {
            conn.Dispose();
        }
        throw;
    }
}
// ...
```

Agora a sessão\_contexto é automaticamente definido com o TenantId especificado sempre que é invocado ElasticScaleContext:

```csharp
// Program.cs
SqlDatabaseUtils.SqlRetryPolicy.ExecuteAction(() =>
{
    using (var db = new ElasticScaleContext<int>(
        sharding.ShardMap, tenantId, connStrBldr.ConnectionString))
    {
        var query = from b in db.Blogs
                    orderby b.Name
                    select b;

        Console.WriteLine("All blogs for TenantId {0}:", tenantId);
        foreach (var item in query)
        {
            Console.WriteLine(item.Name);
        }
    }
});
```

### <a name="adonet-sqlclient"></a>ADO.NET SqlClient

Para aplicações com o ADO.NET SqlClient, crie uma função de invólucro em torno do método ShardMap.OpenConnectionForKey. Ter o wrapper definir automaticamente o TenantId na sessão\_contexto para o TenantId atual antes de retornar uma ligação. Para garantir que essa sessão\_contexto está sempre definido, apenas deve abrir ligações com esta função de wrapper.

```csharp
// Program.cs
// Wrapper function for ShardMap.OpenConnectionForKey() that
// automatically sets SESSION_CONTEXT with the correct
// tenantId before returning a connection.
// As a best practice, you should only open connections using this method
// to ensure that SESSION_CONTEXT is always set before executing a query.
// ...
public static SqlConnection OpenConnectionForTenant(
    ShardMap shardMap, int tenantId, string connectionStr)
{
    SqlConnection conn = null;
    try
    {
        // Ask shard map to broker a validated connection for the given key.
        conn = shardMap.OpenConnectionForKey(
            tenantId, connectionStr, ConnectionOptions.Validate);

        // Set TenantId in SESSION_CONTEXT to shardingKey
        // to enable Row-Level Security filtering.
        SqlCommand cmd = conn.CreateCommand();
        cmd.CommandText =
            @"exec sp_set_session_context
                @key=N'TenantId', @value=@shardingKey";
        cmd.Parameters.AddWithValue("@shardingKey", tenantId);
        cmd.ExecuteNonQuery();

        return conn;
    }
    catch (Exception)
    {
        if (conn != null)
        {
            conn.Dispose();
        }
        throw;
    }
}

// ...

// Example query via ADO.NET SqlClient.
// If row-level security is enabled, only Tenant 4's blogs are listed.
SqlDatabaseUtils.SqlRetryPolicy.ExecuteAction(() =>
{
    using (SqlConnection conn = OpenConnectionForTenant(
        sharding.ShardMap, tenantId4, connStrBldr.ConnectionString))
    {
        SqlCommand cmd = conn.CreateCommand();
        cmd.CommandText = @"SELECT * FROM Blogs";

        Console.WriteLine(@"--
All blogs for TenantId {0} (using ADO.NET SqlClient):", tenantId4);

        SqlDataReader reader = cmd.ExecuteReader();
        while (reader.Read())
        {
            Console.WriteLine("{0}", reader["Name"]);
        }
    }
});

```

## <a name="2-data-tier-create-row-level-security-policy"></a>2. Camada de dados: Criar política de segurança ao nível da linha

### <a name="create-a-security-policy-to-filter-the-rows-each-tenant-can-access"></a>Criar uma política de segurança para filtrar as linhas que pode aceder a cada inquilino

Agora que o aplicativo é a configuração sessão\_contexto com o TenantId atual antes de consulta, uma política de segurança RLS pode filtrar consultas e excluir linhas que têm um TenantId diferente.

RLS é implementada no Transact-SQL. Uma função definida pelo utilizador define a lógica de acesso e uma política de segurança está vinculado esta função para qualquer número de tabelas. Para este projeto:

1. A função verifica que o aplicativo está ligado à base de dados e que o TenantId armazenado em SESSION\_contexto corresponde ao TenantId de uma determinada linha.
    - O aplicativo está ligado, em vez de outro utilizador SQL.

2. Um predicado de filtro permite que as linhas que cumprem o filtro do TenantId para passar para SELECT, UPDATE e eliminar consultas.
    - Um predicado de bloco impede que as linhas que não cumpram o filtro de ser INSERTed ou atualizada.
    - Se sessão\_contexto não tiver sido definido, a função devolve NULL e não existem linhas são visíveis ou capaz de ser inserido.

Para ativar a RLS em todas as partições horizontais, execute o seguinte T-SQL com o Visual Studio (SSDT), SSMS ou o script do PowerShell incluído no projeto. Ou se estiver a utilizar [tarefas elásticas da base de dados](sql-database-elastic-jobs-overview.md), pode automatizar a execução desse T-SQL em todas as partições horizontais.

```sql
CREATE SCHEMA rls; -- Separate schema to organize RLS objects.
GO

CREATE FUNCTION rls.fn_tenantAccessPredicate(@TenantId int)
    RETURNS TABLE
    WITH SCHEMABINDING
AS
    RETURN SELECT 1 AS fn_accessResult
        -- Use the user in your application’s connection string.
        -- Here we use 'dbo' only for demo purposes!
        WHERE DATABASE_PRINCIPAL_ID() = DATABASE_PRINCIPAL_ID('dbo')
        AND CAST(SESSION_CONTEXT(N'TenantId') AS int) = @TenantId;
GO

CREATE SECURITY POLICY rls.tenantAccessPolicy
    ADD FILTER PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.Blogs,
    ADD BLOCK  PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.Blogs,
    ADD FILTER PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.Posts,
    ADD BLOCK  PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.Posts;
GO
```

> [!TIP]
> Num projeto complexo, que poderá ter de adicionar o predicado em centenas de tabelas, que pode ser entediante. Existe um procedimento armazenado do programa auxiliar automaticamente gera uma política de segurança e adiciona um predicado em todas as tabelas num esquema. Para obter mais informações, consulte o postagem no blog em [aplicam-se a segurança ao nível da linha a todas as tabelas - script de programa auxiliar (blog)](https://blogs.msdn.com/b/sqlsecurity/archive/20../../apply-row-level-security-to-all-tables-helper-script).

Agora se executar novamente a aplicação de exemplo, os inquilinos ver apenas as linhas que pertençam a eles. Além disso, o aplicativo não é possível inserir linhas que pertencem aos inquilinos daquela atualmente ligado à base de dados de partição horizontal. Além disso, a aplicação não é possível atualizar o TenantId em nenhuma linha pode ver. Se a aplicação tenta efetuar um, é gerado um DbUpdateException.

Se adicionar uma nova tabela mais tarde, altera a política de segurança para adicionar predicados de filtro e bloco na tabela nova.

```sql
ALTER SECURITY POLICY rls.tenantAccessPolicy
    ADD FILTER PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.MyNewTable,
    ADD BLOCK  PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.MyNewTable;
GO
```

### <a name="add-default-constraints-to-automatically-populate-tenantid-for-inserts"></a>Adicionar restrições predefinidas para preencher automaticamente o TenantId para inserções

Pode colocar uma restrição default em cada tabela para preencher automaticamente o TenantId com o valor atualmente armazenado na sessão\_contexto ao inserir linhas. Segue um exemplo.

```sql
-- Create default constraints to auto-populate TenantId with the
-- value of SESSION_CONTEXT for inserts.
ALTER TABLE Blogs
    ADD CONSTRAINT df_TenantId_Blogs
    DEFAULT CAST(SESSION_CONTEXT(N'TenantId') AS int) FOR TenantId;
GO

ALTER TABLE Posts
    ADD CONSTRAINT df_TenantId_Posts
    DEFAULT CAST(SESSION_CONTEXT(N'TenantId') AS int) FOR TenantId;
GO
```

Agora o aplicativo não precisa de especificar um TenantId ao inserir linhas:

```csharp
SqlDatabaseUtils.SqlRetryPolicy.ExecuteAction(() =>
{
    using (var db = new ElasticScaleContext<int>(
        sharding.ShardMap, tenantId, connStrBldr.ConnectionString))
    {
        // The default constraint sets TenantId automatically!
        var blog = new Blog { Name = name };
        db.Blogs.Add(blog);
        db.SaveChanges();
    }
});
```

> [!NOTE]
> Se utilizar restrições predefinidas para um projeto do Entity Framework, é recomendável que *não* incluir a coluna do TenantId no seu modelo de dados do EF. Esta recomendação é porque o Entity Framework consulta automaticamente valores de padrão de fornecimento que substituem as restrições de predefinido criadas no T-SQL use sessão\_contexto.
> Para utilizar as restrições predefinidas no projeto de exemplo, por exemplo, deve de remover o TenantId do DataClasses.cs (e execute Add-migração na consola do Gestor de pacotes) e usar o T-SQL para garantir que o campo só existe nas tabelas da base de dados. Dessa forma, o EF automaticamente fornecer valores de padrão incorreto ao inserir dados.

### <a name="optional-enable-a-superuser-to-access-all-rows"></a>(Opcional) Ativar uma *Superutilizador* para aceder a todas as linhas

Alguns aplicativos poderão pretender criar uma *Superutilizador* quem pode aceder a todas as linhas. Um Superutilizador foi possível ativar a geração de relatórios em todos os inquilinos em todas as partições horizontais. Ou um Superutilizador foi possível efetuar operações de divisão / intercalação em partições horizontais que envolvem a movimentação de linhas do inquilino entre bases de dados.

Para ativar um Superutilizador, crie um novo utilizador do SQL (`superuser` neste exemplo) em cada base de dados de partição horizontal. Em seguida, altera a política de segurança com uma nova função de predicado que permite que este utilizador aceder a todas as linhas. Em seguida, recebe uma função.

```sql
-- New predicate function that adds superuser logic.
CREATE FUNCTION rls.fn_tenantAccessPredicateWithSuperUser(@TenantId int)
    RETURNS TABLE
    WITH SCHEMABINDING
AS
    RETURN SELECT 1 AS fn_accessResult
        WHERE
        (
            DATABASE_PRINCIPAL_ID() = DATABASE_PRINCIPAL_ID('dbo') -- Replace 'dbo'.
            AND CAST(SESSION_CONTEXT(N'TenantId') AS int) = @TenantId
        )
        OR
        (
            DATABASE_PRINCIPAL_ID() = DATABASE_PRINCIPAL_ID('superuser')
        );
GO

-- Atomically swap in the new predicate function on each table.
ALTER SECURITY POLICY rls.tenantAccessPolicy
    ALTER FILTER PREDICATE rls.fn_tenantAccessPredicateWithSuperUser(TenantId) ON dbo.Blogs,
    ALTER BLOCK  PREDICATE rls.fn_tenantAccessPredicateWithSuperUser(TenantId) ON dbo.Blogs,
    ALTER FILTER PREDICATE rls.fn_tenantAccessPredicateWithSuperUser(TenantId) ON dbo.Posts,
    ALTER BLOCK  PREDICATE rls.fn_tenantAccessPredicateWithSuperUser(TenantId) ON dbo.Posts;
GO
```


### <a name="maintenance"></a>Manutenção

- **Adicionar novas partições horizontais**: Execute o script T-SQL para ativar a RLS em quaisquer novas partições horizontais, caso contrário, consultas nestas partições horizontais não são filtrados.
- **Adicionar novas tabelas**: Adicione um predicado de filtro e do bloco para a política de segurança em todas as partições horizontais, sempre que é criada uma nova tabela. Caso contrário, as consultas na nova tabela não são filtradas. Esta adição pode ser automatizada com um acionador de DDL, conforme descrito em [aplicam-se a segurança ao nível da linha automaticamente para tabelas recém-criado (blog)](https://blogs.msdn.com/b/sqlsecurity/archive/20../../apply-row-level-security-automatically-to-newly-created-tables.aspx).

## <a name="summary"></a>Resumo

Ferramentas de base de dados elástica e segurança ao nível da linha podem ser utilizadas em conjunto para aumentar horizontalmente a camada de dados de uma aplicação com suporte para ambos os multi-inquilino e de inquilino único as partições horizontais. As partições horizontais de multi-inquilinos podem ser utilizadas para armazenar dados de forma mais eficiente. Essa eficiência é pronuncia-se em que um grande número de inquilinos têm apenas algumas linhas de dados. As partições horizontais de inquilino único podem suportar inquilinos premium que têm requisitos de isolamento e de desempenho mais estritas. Para obter mais informações, consulte [referência de segurança ao nível da linha][rls].

## <a name="additional-resources"></a>Recursos adicionais

- [O que é um conjunto elástico do Azure?](sql-database-elastic-pool.md)
- [Aumentar horizontalmente com a Base de Dados SQL do Azure](sql-database-elastic-scale-introduction.md)
- [Padrões de Estrutura para Aplicações de SaaS Multi-inquilinos com a Base de Dados SQL do Azure](saas-tenancy-app-design-patterns.md)
- [Autenticação em aplicações multi-inquilino, com o Azure AD e o OpenID Connect](../guidance/guidance-multitenant-identity-authenticate.md)
- [Aplicação Tailspin Surveys](../guidance/guidance-multitenant-identity-tailspin.md)

## <a name="questions-and-feature-requests"></a>Perguntas e pedidos de funcionalidades

Para fazer perguntas, contacte-nos sobre o [fórum de base de dados SQL](https://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted). E adicione quaisquer pedidos de funcionalidades para o [fórum de comentários de base de dados SQL](https://feedback.azure.com/forums/217321-sql-database/).

<!--Image references-->
[1]: ./media/saas-tenancy-elastic-tools-multi-tenant-row-level-security/blogging-app.png
<!--anchors-->
[rls]: https://docs.microsoft.com/sql/relational-databases/security/row-level-security
[s-d-elastic-database-client-library]: sql-database-elastic-database-client-library.md
