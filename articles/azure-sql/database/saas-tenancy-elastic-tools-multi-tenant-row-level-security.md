---
title: Aplicativos multi-inquilinos com RLS e ferramentas de base de dados elásticas
description: Utilize ferramentas de base de dados elásticas com segurança ao nível da linha para construir uma aplicação com um nível de dados altamente escalável.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: VanMSFT
ms.author: vanto
ms.reviewer: sstein
ms.date: 12/18/2018
ms.openlocfilehash: 6d753a90f2a4cb19c9f3933d007fb3d378af6d81
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92793216"
---
# <a name="multi-tenant-applications-with-elastic-database-tools-and-row-level-security"></a>Aplicações multi-arrendatários com ferramentas de base de dados elásticas e segurança ao nível da linha
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

[Ferramentas de base de dados elásticas](elastic-scale-get-started.md) e [segurança ao nível da linha (RLS)][rls] cooperam para permitir a escala do nível de dados de uma aplicação multi-inquilino com Azure SQL Database. Juntas estas tecnologias ajudam-no a construir uma aplicação que tem um nível de dados altamente escalável. O nível de dados suporta fragmentos de vários inquilinos, e utiliza **ADO.NET SqlClient** ou **Entity Framework** . Para obter mais informações, consulte [Padrões de Design para Aplicações SaaS multi-arrendantes com Base de Dados Azure SQL](./saas-tenancy-app-design-patterns.md).

- **As ferramentas de base de dados elásticas** permitem aos desenvolvedores escalar o nível de dados com práticas padrão de fragmentos, utilizando bibliotecas .NET e modelos de serviço Azure. Gerir fragmentos utilizando a Biblioteca de [Clientes de Base de Dados Elástica][s-d-elastic-database-client-library] ajuda a automatizar e agilizar muitas das tarefas infraestruturais tipicamente associadas ao fragmento.
- **A segurança ao nível da linha** permite que os desenvolvedores armazenem com segurança dados para vários inquilinos na mesma base de dados. As políticas de segurança RLS filtram linhas que não pertencem ao inquilino que executa uma consulta. Centralizar a lógica do filtro dentro da base de dados simplifica a manutenção e reduz o risco de um erro de segurança. A alternativa de confiar em todo o código do cliente para impor a segurança é arriscada.

Ao utilizar estas funcionalidades em conjunto, uma aplicação pode armazenar dados para vários inquilinos na mesma base de dados de fragmentos. Custa menos por inquilino quando os inquilinos partilham uma base de dados. No entanto, a mesma aplicação também pode oferecer aos seus inquilinos premium a opção de pagar pelo seu próprio fragmento dedicado de inquilino único. Um dos benefícios do isolamento de um único inquilino é garantias de desempenho mais firmes. Numa base de dados de inquilinos individuais, não há outro inquilino a competir por recursos.

O objetivo é utilizar as APIs [de encaminhamento de dados dependentes de dados da](elastic-scale-data-dependent-routing.md) biblioteca de clientes de base de dados elásticas para ligar automaticamente cada um dos inquilinos à base de dados de fragmentos correta. Apenas um fragmento contém um valor particular de TenantId para o inquilino dado. O TenantId é *a chave de caco.* Após a ligação ser estabelecida, uma política de segurança RLS dentro da base de dados garante que o inquilino dado só pode aceder às linhas de dados que contêm o seu TenantId.

> [!NOTE]
> O identificador de inquilinos pode ser composto por mais de uma coluna. Por conveniência é esta discussão, assumimos informalmente uma única coluna TenantId.

![Arquitetura de aplicativos de blogging][1]

## <a name="download-the-sample-project"></a>Descarregue o projeto de amostra

### <a name="prerequisites"></a>Pré-requisitos

- Use o Visual Studio (2012 ou superior)
- Criar três bases de dados na Base de Dados Azure SQL
- Projeto de amostra de descarregamento: [Ferramentas Elásticas DB para Azure SQL - Fragmentos multi-inquilinos](https://go.microsoft.com/?linkid=9888163)
  - Preencha a informação para as suas bases de dados no início de **Program.cs**

Este projeto alarga o descrito em [Ferramentas Elásticas DB para Azure SQL - Integração-Quadro de Entidades](elastic-scale-use-entity-framework-applications-visual-studio.md) adicionando suporte para bases de dados de fragmentos de vários inquilinos. O projeto constrói uma aplicação simples de consola para criar blogs e posts. O projeto inclui quatro inquilinos, mais duas bases de dados de estilhaços multi-inquilinos. Esta configuração é ilustrada no diagrama anterior.

Compile e execute a aplicação. Esta corrida de botas testa o gestor de mapas de ferramentas de base de dados elásticas e realiza os seguintes testes:

1. Utilizando o Entity Framework e o LINQ, crie um novo blog e, em seguida, exiba todos os blogs para cada inquilino
2. Usando ADO.NET SqlClient, exiba todos os blogs para um inquilino
3. Tente inserir um blog para o inquilino errado para verificar se um erro é jogado

Note-se que, como o RLS ainda não foi ativado nas bases de dados de fragmentos, cada um destes testes revela um problema: os inquilinos são capazes de ver blogs que não lhes pertencem, e a aplicação não está impedida de inserir um blog para o inquilino errado. O restante deste artigo descreve como resolver estes problemas, impondo o isolamento dos inquilinos com RLS. Há dois passos:

1. **Nível de aplicação** : Modifique o código de aplicação para definir sempre o atual TenantId no CONTEXTO DE SESSÃO após a \_ abertura de uma ligação. O projeto de amostra já define o TenantId desta forma.
2. **Nível de dados** : Criar uma política de segurança RLS em cada base de dados de fragmentos para filtrar linhas com base no TenantId armazenado em CONTEXTO DE \_ SESSÃO. Crie uma política para cada uma das suas bases de dados de fragmentos, caso contrário as linhas em fragmentos de vários inquilinos não são filtradas.

## <a name="1-application-tier-set-tenantid-in-the-session_context"></a>1. Nível de aplicação: Definir TenantId no contexto de sessão \_

Primeiro, liga-se a uma base de dados de fragmentos utilizando as APIs de encaminhamento dependentes de dados da biblioteca de clientes de base de dados elástica. A aplicação ainda deve dizer à base de dados que o TenantId está a utilizar a ligação. O TenantId diz à política de segurança RLS quais as filas devem ser filtradas como pertencentes a outros inquilinos. Armazenar o atual TenantId no [ \_ contexto de sessão](/sql/t-sql/functions/session-context-transact-sql) da ligação.

Uma alternativa ao \_ contexto de sessão é utilizar info [de contexto. \_ ](/sql/t-sql/functions/context-info-transact-sql) Mas o CONTEXTO DE SESSÃO \_ é uma opção melhor. O CONTEXTO DE SESSÃO \_ é mais fácil de usar, devolve NU POR padrão e suporta pares de valor-chave.

### <a name="entity-framework"></a>Entity Framework

Para aplicações que utilizam o Quadro de Entidades, a abordagem mais fácil é definir o contexto de sessão \_ dentro do sobreposição elasticScaleContext descrito no [encaminhamento dependente de dados utilizando EF DbContext](elastic-scale-use-entity-framework-applications-visual-studio.md#data-dependent-routing-using-ef-dbcontext). Crie e execute um SqlCommand que define o TenantId no contexto de sessão \_ para o shardingKey especificado para a ligação. Em seguida, retornar a ligação intermediada através de encaminhamento dependente de dados. Desta forma, só é necessário escrever código uma vez para definir o contexto de \_ SESSÃO.

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

Agora o CONTEXTO DE SESSÃO \_ é automaticamente definido com o InquilinoId especificado sempre que o ElasticScaleContext é invocado:

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

Para aplicações que utilizem ADO.NET SqlClient, crie uma função de invólucro em torno do método ShardMap.OpenConnectionForKey. Tenha o invólucro automaticamente definido TenantId no contexto de sessão \_ para o inquilino atual antes de devolver uma ligação. Para garantir que o CONTEXTO DE SESSÃO \_ está sempre definido, só deve abrir ligações utilizando esta função de invólucro.

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

## <a name="2-data-tier-create-row-level-security-policy"></a>2. Nível de dados: Criar política de segurança ao nível da linha

### <a name="create-a-security-policy-to-filter-the-rows-each-tenant-can-access"></a>Crie uma política de segurança para filtrar as filas a que cada inquilino pode aceder

Agora que a aplicação está a definir o CONTEXTO DE SESSÃO \_ com o atual TenantId antes de consultar, uma política de segurança RLS pode filtrar consultas e excluir linhas que tenham um TenantId diferente.

O RLS é implementado na Transact-SQL. Uma função definida pelo utilizador define a lógica de acesso, e uma política de segurança liga esta função a qualquer número de tabelas. Para este projeto:

1. A função verifica que o pedido está ligado à base de dados, e que o TenantId armazenado no CONTEXTO DE SESSÃO \_ corresponde ao TenantId de uma determinada linha.
    - A aplicação está conectada, em vez de outro utilizador SQL.

2. Um predicado FILTER permite que as filas que vão ao encontro do filtro TenantId passem para consultas SELECT, UPDATE e DELETE.
    - Um predicado BLOCK impede que as filas que não falham no filtro sejam INSERTed ou UPDATEd.
    - Se o CONTEXTO DE SESSÃO \_ não tiver sido definido, a função volta a ser NU, e não há filas visíveis ou capazes de ser inseridas.

Para ativar o RLS em todos os fragmentos, execute o seguinte T-SQL utilizando o Visual Studio (SSDT), SSMS ou o script PowerShell incluído no projeto. Ou se estiver a utilizar [o Elastic Database Jobs,](./elastic-jobs-overview.md)pode automatizar a execução deste T-SQL em todos os fragmentos.

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
> Num projeto complexo, talvez seja necessário adicionar o predicado em centenas de mesas, o que pode ser aborrecido. Existe um procedimento de armazenação de ajuda que gera automaticamente uma política de segurança, e adiciona um predicado em todas as tabelas num esquema. Para obter mais informações, consulte o post de blog na [Apply Row-Level Security para todas as tabelas - helper script (blog)](https://techcommunity.microsoft.com/t5/sql-server/apply-row-level-security-to-all-tables-helper-script/ba-p/384360).

Agora, se fizer o pedido de amostra de novo, os inquilinos só vêem filas que lhes pertencem. Além disso, a aplicação não pode inserir linhas pertencentes a inquilinos que não o atualmente ligado à base de dados de fragmentos. Além disso, a aplicação não pode atualizar o TenantId em quaisquer linhas que possa ver. Se a aplicação tentar fazer qualquer um dos dois, é levantada uma DbUpdateException.

Se adicionar uma nova tabela mais tarde, ALTERe a política de segurança para adicionar predicados FILTER e BLOCK na nova tabela.

```sql
ALTER SECURITY POLICY rls.tenantAccessPolicy
    ADD FILTER PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.MyNewTable,
    ADD BLOCK  PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.MyNewTable;
GO
```

### <a name="add-default-constraints-to-automatically-populate-tenantid-for-inserts"></a>Adicione restrições predefinidas para povoar automaticamente o TenantId para INSERTs

Pode colocar uma restrição predefinida em cada mesa para povoar automaticamente o TenantId com o valor atualmente armazenado em CONTEXTO DE SESSÃO \_ ao inserir linhas. Segue-se um exemplo.

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

Agora a aplicação não precisa de especificar um TenantId ao inserir linhas:

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
> Se utilizar restrições predefinidas para um projeto Entity Framework, recomenda-se que *não* inclua a coluna TenantId no seu modelo de dados EF. Esta recomendação deve-se ao facto de as consultas do Quadro de Entidades fornecerem automaticamente valores predefinidos que substituem os constrangimentos predefinidos criados em T-SQL que utilizam o CONTEXTO DE \_ SESSÃO.
> Para utilizar constrangimentos predefinidos no projeto da amostra, por exemplo, deve remover o TenantId de DataClasses.cs (e executar Add-Migration na Consola de Gestor de Pacotes) e utilizar o T-SQL para garantir que o campo só existe nas tabelas de bases de dados. Desta forma, a EF fornece automaticamente valores predefinidos incorretos ao inserir dados.

### <a name="optional-enable-a-superuser-to-access-all-rows"></a>(Opcional) Permitir que um *superuser* aceda a todas as linhas

Algumas aplicações podem querer criar um *superuser* que possa aceder a todas as linhas. Um super-acompanhante poderia permitir reportar em todos os inquilinos em todos os fragmentos. Ou um superuser poderia realizar operações de fusão dividida em fragmentos que envolvem mover linhas de inquilinos entre bases de dados.

Para ativar um superuser, crie um novo utilizador SQL `superuser` (neste exemplo) em cada base de dados de fragmentos. Em seguida, altere a política de segurança com uma nova função predicado que permita a este utilizador aceder a todas as linhas. Tal função é dada a seguir.

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

- **Adicionar novos fragmentos** : Execute o script T-SQL para permitir o RLS em quaisquer novos fragmentos, caso contrário as consultas sobre estes fragmentos não são filtradas.
- **Adicionar novas tabelas** : Adicione um filtro e um pré-diabetes à política de segurança em todos os fragmentos sempre que for criada uma nova tabela. Caso contrário, as consultas na nova tabela não são filtradas. Esta adição pode ser automatizada utilizando um gatilho DDL, conforme descrito no [Apply Row-Level Security automaticamente para as tabelas recém-criadas (blog)](https://techcommunity.microsoft.com/t5/SQL-Server/Apply-Row-Level-Security-automatically-to-newly-created-tables/ba-p/384393).

## <a name="summary"></a>Resumo

Ferramentas elásticas de base de dados e segurança ao nível da linha podem ser usadas em conjunto para escalar o nível de dados de uma aplicação com suporte para fragmentos de multi-inquilinos e inquilinos únicos. Fragmentos de vários inquilinos podem ser usados para armazenar dados de forma mais eficiente. Esta eficiência é pronunciada onde um grande número de inquilinos têm apenas algumas filas de dados. Os fragmentos de inquilino único podem apoiar inquilinos premium que tenham requisitos de desempenho e isolamento mais rigorosos. Para obter mais informações, consulte [a referência de Segurança de nível de linha][rls].

## <a name="additional-resources"></a>Recursos adicionais

- [O que é um conjunto elástico do Azure?](elastic-pool-overview.md)
- [Aumentar horizontalmente com a Base de Dados SQL do Azure](elastic-scale-introduction.md)
- [Padrões de Estrutura para Aplicações de SaaS Multi-inquilinos com a Base de Dados SQL do Azure](./saas-tenancy-app-design-patterns.md)
- [Autenticação em aplicações multi-inquilino, com o Azure AD e o OpenID Connect](/azure/architecture/multitenant-identity/authenticate)
- [Aplicação Tailspin Surveys](/azure/architecture/multitenant-identity/tailspin)

## <a name="questions-and-feature-requests"></a>Perguntas e Pedidos de Recursos

Para obter dúvidas, contacte-nos na [página de perguntas do Microsoft Q&A página de perguntas para a Base de Dados SQL](/answers/topics/azure-sql-database.html). E adicione quaisquer pedidos de funcionalidade ao fórum de feedback da [Base de Dados SQL](https://feedback.azure.com/forums/217321-sql-database/).

<!--Image references-->
[1]: ./media/saas-tenancy-elastic-tools-multi-tenant-row-level-security/blogging-app.png
<!--anchors-->
[rls]: /sql/relational-databases/security/row-level-security
[s-d-elastic-database-client-library]:elastic-database-client-library.md