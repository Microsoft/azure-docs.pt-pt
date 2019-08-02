---
title: Aplicativos multilocatários com RLS e ferramentas de banco de dados elástico | Microsoft Docs
description: Use as ferramentas de banco de dados elástico com segurança em nível de linha para criar um aplicativo com uma camada altamente escalonável.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: VanMSFT
ms.author: vanto
ms.reviewer: sstein
ms.date: 12/18/2018
ms.openlocfilehash: 996d4e2ba62c06992b0433fd255800ba8cea0af3
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68570180"
---
# <a name="multi-tenant-applications-with-elastic-database-tools-and-row-level-security"></a>Aplicativos multilocatários com ferramentas de banco de dados elástico e segurança em nível de linha

As [ferramentas de banco](sql-database-elastic-scale-get-started.md) de dados elástico e a [RLS (segurança em nível de linha)][rls] cooperam para habilitar o dimensionamento da camada de dado de um aplicativo multilocatário com o banco de dados SQL do Azure. Juntas, essas tecnologias ajudam a criar um aplicativo que tem uma camada de dados altamente escalonável. A camada de dados dá suporte a fragmentos de vários locatários e usa **ADO.Net SqlClient** ou **Entity Framework**. Para obter mais informações, consulte [padrões de design para aplicativos SaaS multilocatário com o banco de dados SQL do Azure](saas-tenancy-app-design-patterns.md).

- As **ferramentas de banco** de dados elástico permitem que os desenvolvedores escalem horizontalmente a camada de dados com as práticas de fragmentação padrão, usando bibliotecas .net e modelos de serviço do Azure. O gerenciamento de fragmentos usando a [biblioteca de cliente do banco de dados elástico][s-d-elastic-database-client-library] ajuda a automatizar e simplificar muitas das tarefas infraestrutura normalmente associadas à fragmentação.
- A **segurança em nível de linha** permite que os desenvolvedores armazenem dados com segurança para vários locatários no mesmo banco de dados. As políticas de segurança RLS filtram as linhas que não pertencem ao locatário que está executando uma consulta. Centralizar a lógica de filtro dentro do banco de dados simplifica a manutenção e reduz o risco de um erro de segurança. A alternativa de depender de todo o código do cliente para impor a segurança é arriscada.

Ao usar esses recursos juntos, um aplicativo pode armazenar dados para vários locatários no mesmo banco de dados de fragmento. Custa menos por locatário quando os locatários compartilham um banco de dados. Ainda assim, o mesmo aplicativo também pode oferecer aos seus locatários Premium a opção de pagar por seu próprio fragmento dedicado de locatário único. Um dos benefícios do isolamento de locatário único é a garantia de desempenho do confirmador. Em um banco de dados de locatário único, não há nenhum outro locatário competindo por recursos.

O objetivo é usar as APIs de [Roteamento dependentes de dados](sql-database-elastic-scale-data-dependent-routing.md) da biblioteca de cliente do banco do dados elástico para conectar automaticamente cada locatário específico ao banco de dados de fragmento correto. Somente um fragmento contém um valor de Tenantid específico para o locatário fornecido. A Tenantid é a *chave*de fragmentação. Depois que a conexão é estabelecida, uma política de segurança RLS dentro do banco de dados garante que o locatário fornecido possa acessar somente as linhas de dados que contêm sua Tenantid.

> [!NOTE]
> O identificador do locatário pode consistir em mais de uma coluna. Para sua conveniência, vamos supor que, informalmente, uma Tenantid de coluna única.

![Arquitetura de aplicativo de blog][1]

## <a name="download-the-sample-project"></a>Baixar o projeto de exemplo

### <a name="prerequisites"></a>Pré-requisitos

- Usar o Visual Studio (2012 ou superior)
- Criar três bancos de dados SQL do Azure
- Baixar projeto de exemplo: [Ferramentas de banco de BD elástico para SQL do Azure-fragmentos de vários locatários](https://go.microsoft.com/?linkid=9888163)
  - Preencha as informações de seus bancos de dados no início do **Program.cs**

Esse projeto estende o descrito em [ferramentas de banco de dados elástico para a integração de Entity Framework SQL do Azure](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md) adicionando suporte para bancos de dados de fragmentos multilocatários. O projeto cria um aplicativo de console simples para criar Blogs e postagens. O projeto inclui quatro locatários, além de dois bancos de dados de fragmentos de vários locatários. Essa configuração é ilustrada no diagrama anterior.

Compile e execute o aplicativo. Essa execução Inicializa o Gerenciador de mapa de fragmentos das ferramentas de banco de dados elástico e executa os seguintes testes:

1. Usando o Entity Framework e o LINQ, crie um novo blog e, em seguida, exiba todos os Blogs para cada locatário
2. Usando o ADO.NET SqlClient, exibir todos os Blogs de um locatário
3. Tente inserir um blog para o locatário incorreto para verificar se um erro foi gerado

Observe que, como a RLS ainda não foi habilitada nos bancos de dados de fragmentos, cada um desses testes revela um problema: os locatários são capazes de ver os Blogs que não pertencem a eles e o aplicativo não é impedido de inserir um blog para o locatário errado. O restante deste artigo descreve como resolver esses problemas impondo o isolamento de locatário com RLS. Há duas etapas:

1. **Camada de aplicativo**: Modifique o código do aplicativo para sempre definir a tenantid atual no contexto\_da sessão depois de abrir uma conexão. O projeto de exemplo já define o Tenantid dessa maneira.
2. **Camada de dados**: Crie uma política de segurança RLS em cada banco de dados de fragmentos para filtrar linhas com base na tenantid armazenada no contexto da sessão\_. Crie uma política para cada um dos seus bancos de dados de fragmentos; caso contrário, as linhas em fragmentos multilocatários não serão filtradas.

## <a name="1-application-tier-set-tenantid-in-the-sessioncontext"></a>1. Camada de aplicativo: Definir tenantid no contexto da\_sessão

Primeiro, você se conecta a um banco de dados de fragmento usando as APIs de roteamento dependentes de dados da biblioteca de cliente do banco de dado elástico. O aplicativo ainda deve informar ao banco de dados qual Locatárioid está usando a conexão. A Tenantid informa à política de segurança RLS quais linhas devem ser filtradas como pertencentes a outros locatários. Armazene a tenantid atual no [contexto\_de sessão](https://docs.microsoft.com/sql/t-sql/functions/session-context-transact-sql) da conexão.

Uma alternativa ao contexto\_de sessão é usar [informações\_de contexto](https://docs.microsoft.com/sql/t-sql/functions/context-info-transact-sql). Mas o\_contexto da sessão é uma opção melhor. O\_contexto de sessão é mais fácil de usar, ele retorna NULL por padrão e dá suporte a pares chave-valor.

### <a name="entity-framework"></a>Entity Framework

Para aplicativos que usam Entity Framework, a abordagem mais fácil é definir o contexto\_da sessão na substituição ElasticScaleContext descrita no [Roteamento Dependente de dados usando o EF DbContext](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md#data-dependent-routing-using-ef-dbcontext). Crie e execute um SqlCommand que defina tenantid no contexto da\_sessão para o shardingKey especificado para a conexão. Em seguida, retorne a conexão orientada por meio do roteamento dependente de dados. Dessa forma, você só precisa escrever código uma vez para definir o contexto\_da sessão.

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

Agora, o\_contexto da sessão é definido automaticamente com a tenantid especificada sempre que ElasticScaleContext é invocado:

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

Para aplicativos que usam ADO.NET SqlClient, crie uma função de wrapper em volta do método ShardMap. OpenConnectionForKey. Faça com que o wrapper defina automaticamente tenantid no\_contexto da sessão para a tenantid atual antes de retornar uma conexão. Para garantir que o\_contexto de sessão seja sempre definido, você só deve abrir conexões usando essa função de wrapper.

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

## <a name="2-data-tier-create-row-level-security-policy"></a>2. Camada de dados: Criar política de segurança em nível de linha

### <a name="create-a-security-policy-to-filter-the-rows-each-tenant-can-access"></a>Criar uma política de segurança para filtrar as linhas que cada locatário pode acessar

Agora que o aplicativo está definindo o\_contexto de sessão com a tenantid atual antes de consultar, uma política de segurança RLS pode filtrar consultas e excluir linhas que têm uma tenantid diferente.

A RLS é implementada no Transact-SQL. Uma função definida pelo usuário define a lógica de acesso e uma política de segurança associa essa função a qualquer número de tabelas. Para este projeto:

1. A função verifica se o aplicativo está conectado ao banco de dados e se a tenantid armazenada no contexto da\_sessão corresponde à tenantid de uma determinada linha.
    - O aplicativo é conectado, em vez de outro usuário do SQL.

2. Um predicado de filtro permite que as linhas que atendem ao filtro Tenantid passem para consultas SELECT, UPDATE e DELETE.
    - Um predicado de bloco impede que as linhas que falham no filtro sejam inseridas ou atualizadas.
    - Se o\_contexto da sessão não tiver sido definido, a função retornará NULL e nenhuma linha será visível ou poderá ser inserida.

Para habilitar a RLS em todos os fragmentos, execute o T-SQL a seguir usando o Visual Studio (SSDT), o SSMS ou o script do PowerShell incluído no projeto. Ou, se você estiver usando [trabalhos de banco de dados elástico](elastic-jobs-overview.md), poderá automatizar a execução desse T-SQL em todos os fragmentos.

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
> Em um projeto complexo, talvez seja necessário adicionar o predicado em centenas de tabelas, o que pode ser entediante. Há um procedimento armazenado auxiliar que gera automaticamente uma política de segurança e adiciona um predicado em todas as tabelas em um esquema. Para obter mais informações, consulte a postagem de blog em [aplicar segurança em nível de linha a todas as tabelas – script auxiliar (blog)](https://blogs.msdn.com/b/sqlsecurity/archive/20../../apply-row-level-security-to-all-tables-helper-script).

Agora, se você executar o aplicativo de exemplo novamente, os locatários verão apenas as linhas que pertencem a eles. Além disso, o aplicativo não pode inserir linhas que pertençam a locatários diferentes daquele atualmente conectado ao banco de dados de fragmentos. Além disso, o aplicativo não pode atualizar a Tenantid em nenhuma linha que possa ver. Se o aplicativo tentar fazer uma, um DbUpdateException será gerado.

Se você adicionar uma nova tabela posteriormente, altere a política de segurança para adicionar o filtro e os predicados de bloqueio na nova tabela.

```sql
ALTER SECURITY POLICY rls.tenantAccessPolicy
    ADD FILTER PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.MyNewTable,
    ADD BLOCK  PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.MyNewTable;
GO
```

### <a name="add-default-constraints-to-automatically-populate-tenantid-for-inserts"></a>Adicionar restrições padrão para preencher automaticamente a Tenantid para inserções

Você pode colocar uma restrição padrão em cada tabela para preencher automaticamente a tenantid com o valor atualmente armazenado no contexto\_da sessão ao inserir linhas. Segue um exemplo.

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

Agora, o aplicativo não precisa especificar uma Tenantid ao inserir linhas:

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
> Se você usar as restrições padrão para um projeto Entity Framework, é recomendável que você *não* inclua a coluna tenantid em seu modelo de dados do EF. Essa recomendação é porque Entity Framework consultas fornecem automaticamente valores padrão que substituem as restrições padrão criadas no T-SQL que usam\_o contexto de sessão.
> Para usar as restrições padrão no projeto de exemplo, por exemplo, você deve remover Tenantid de DataClasses.cs (e executar adicionar-migração no console do Gerenciador de pacotes) e usar o T-SQL para garantir que o campo exista somente nas tabelas do banco de dados. Dessa forma, o EF fornece automaticamente valores padrão incorretos ao inserir dados.

### <a name="optional-enable-a-superuser-to-access-all-rows"></a>Adicional Habilitar um *superusuário* para acessar todas as linhas

Alguns aplicativos podem querer criar um *superusuário* que possa acessar todas as linhas. Um superusuário pode habilitar o relatório em todos os locatários em todos os fragmentos. Ou um superusuário pode executar operações de divisão/mesclagem em fragmentos que envolvem a movimentação de linhas de locatário entre bancos de dados.

Para habilitar o superusuário, crie um novo usuário do`superuser` SQL (neste exemplo) em cada banco de dados de fragmentos. Em seguida, altere a política de segurança com uma nova função de predicado que permita que este usuário acesse todas as linhas. Essa função é fornecida a seguir.

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

- **Adicionando novos fragmentos**: Execute o script T-SQL para habilitar a RLS em novos fragmentos, caso contrário, as consultas nesses fragmentos não serão filtradas.
- **Adicionando novas tabelas**: Adicione um filtro e um predicado de bloco à política de segurança em todos os fragmentos sempre que uma nova tabela for criada. Caso contrário, as consultas na nova tabela não serão filtradas. Essa adição pode ser automatizada usando um gatilho DDL, conforme descrito em [aplicar segurança em nível de linha automaticamente a tabelas recém-criadas (blog)](https://blogs.msdn.com/b/sqlsecurity/archive/20../../apply-row-level-security-automatically-to-newly-created-tables.aspx).

## <a name="summary"></a>Resumo

As ferramentas de banco de dados elástico e a segurança em nível de linha podem ser usadas em conjunto para escalar horizontalmente a camada do aplicativo com suporte para fragmentos multilocatários e de locatário único. Fragmentos multilocatários podem ser usados para armazenar dados com mais eficiência. Essa eficiência é pronunciada onde um grande número de locatários tem apenas algumas linhas de dados. Os fragmentos de locatário único podem dar suporte a locatários Premium que têm requisitos de desempenho e isolamento mais estritos. Para obter mais informações, consulte [referência de segurança em nível de linha][rls].

## <a name="additional-resources"></a>Recursos adicionais

- [O que é um pool elástico do Azure?](sql-database-elastic-pool.md)
- [Aumentar horizontalmente com a Base de Dados SQL do Azure](sql-database-elastic-scale-introduction.md)
- [Padrões de Estrutura para Aplicações de SaaS Multi-inquilinos com a Base de Dados SQL do Azure](saas-tenancy-app-design-patterns.md)
- [Autenticação em aplicações multi-inquilino, com o Azure AD e o OpenID Connect](../guidance/guidance-multitenant-identity-authenticate.md)
- [Aplicação Tailspin Surveys](../guidance/guidance-multitenant-identity-tailspin.md)

## <a name="questions-and-feature-requests"></a>Perguntas e solicitações de recursos

Para perguntas, entre em contato conosco no [Fórum do banco de dados SQL](https://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted). E adicione qualquer solicitação de recurso ao [Fórum de comentários do banco de dados SQL](https://feedback.azure.com/forums/217321-sql-database/).

<!--Image references-->
[1]: ./media/saas-tenancy-elastic-tools-multi-tenant-row-level-security/blogging-app.png
<!--anchors-->
[rls]: https://docs.microsoft.com/sql/relational-databases/security/row-level-security
[s-d-elastic-database-client-library]: sql-database-elastic-database-client-library.md
