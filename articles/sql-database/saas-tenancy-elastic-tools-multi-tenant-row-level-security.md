---
title: Aplicativos multi-inquilinos com RLS e ferramentas de base de dados elásticas
description: Utilize ferramentas de base de dados elásticas com segurança ao nível da linha para construir uma aplicação com um nível de dados altamente escalável.
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
ms.openlocfilehash: a5fe5d6d4076c5d82d33737d05bb95ede0a89c00
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73822022"
---
# <a name="multi-tenant-applications-with-elastic-database-tools-and-row-level-security"></a>Aplicações multi-inquilinos com ferramentas de base de dados elásticas e segurança ao nível da linha

[As ferramentas](sql-database-elastic-scale-get-started.md) de base de dados elásticas e a segurança ao [nível da linha (RLS)][rls] cooperam para permitir a escala do nível de dados de uma aplicação multi-arrendatária com base de dados Azure SQL. Juntas, estas tecnologias ajudam-no a construir uma aplicação que tem um nível de dados altamente escalável. O nível de dados suporta fragmentos de multi-inquilinos e utiliza **ADO.NET SqlClient** ou **Estrutura de Entidades.** Para mais informações, consulte [Padrões de Design para Aplicações SaaS multi-arrendatárias com base de dados Azure SQL](saas-tenancy-app-design-patterns.md).

- **As ferramentas elásticas de base** de dados permitem aos desenvolvedores escalar o nível de dados com práticas padrão de sharding, utilizando bibliotecas .NET e modelos de serviço Azure. Gerir fragmentos utilizando a Biblioteca de Clientes de Base de [Dados Elástica][s-d-elastic-database-client-library] ajuda a automatizar e a agilizar muitas das tarefas infraestruturais tipicamente associadas à sharding.
- **A segurança ao nível da linha** permite que os desenvolvedores armazenem dados com segurança para vários inquilinos na mesma base de dados. As políticas de segurança do RLS filtram linhas que não pertencem ao inquilino executando uma consulta. Centralizar a lógica do filtro dentro da base de dados simplifica a manutenção e reduz o risco de um erro de segurança. A alternativa de confiar em todo o código do cliente para impor a segurança é arriscada.

Ao utilizar estas funcionalidades em conjunto, uma aplicação pode armazenar dados para vários inquilinos na mesma base de dados. Custa menos por inquilino quando os inquilinos partilham uma base de dados. No entanto, a mesma aplicação também pode oferecer aos seus inquilinos premium a opção de pagar o seu próprio fragmento dedicado de inquilino único. Um dos benefícios do isolamento de um único inquilino são garantias de desempenho mais firmes. Numa base de dados de um único inquilino, não há outro inquilino a competir por recursos.

O objetivo é utilizar as APIs [de encaminhamento dependentes](sql-database-elastic-scale-data-dependent-routing.md) de dados da biblioteca de dados da biblioteca de dados da biblioteca de dados elásticos para ligar automaticamente cada inquilino à base de dados de fragmentos correta. Apenas um fragmento contém um valor especial do TenantId para o inquilino dado. O TenantId é a *chave da sharding.* Após a criação da ligação, uma política de segurança RLS dentro da base de dados garante que o inquilino dado só pode aceder às linhas de dados que contêm o seu TenantId.

> [!NOTE]
> O identificador inquilino pode ser composto por mais de uma coluna. Por conveniência é esta discussão, assumimos informalmente um TenantId de uma coluna única.

![Arquitetura de aplicativos de blogging][1]

## <a name="download-the-sample-project"></a>Descarregue o projeto da amostra

### <a name="prerequisites"></a>Pré-requisitos

- Utilizar o Estúdio Visual (2012 ou superior)
- Criar três bases de dados Azure SQL
- Projeto de amostra de descarregamento: [Ferramentas DB elásticas para Azure SQL - Fragmentos Multi-Inquilinos](https://go.microsoft.com/?linkid=9888163)
  - Preencha a informação para as suas bases de dados no início da **Program.cs**

Este projeto alarga o descrito em [Ferramentas ELÁSTICAs DB para a Integração Estrutura de Entidades Azure SQL - Entidade,](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md) adicionando suporte para bases de dados de fragmentos multi-inquilinos. O projeto constrói uma simples aplicação de consola para criar blogs e posts. O projeto inclui quatro inquilinos, mais duas bases de dados multi-inquilinos. Esta configuração é ilustrada no diagrama anterior.

Compile e execute a aplicação. Esta execução aprisiona o gestor de mapas de dados elástico das ferramentas de base de dados e realiza os seguintes testes:

1. Utilizando o Quadro de Entidades e o LINQ, crie um novo blog e, em seguida, exiba todos os blogs para cada inquilino
2. Usando ADO.NET SqlClient, exiba todos os blogs para um inquilino
3. Tente inserir um blog para o inquilino errado para verificar se um erro é lançado

Note-se que, uma vez que o RLS ainda não foi ativado nas bases de dados, cada um destes testes revela um problema: os inquilinos podem ver blogs que não lhes pertencem, e a aplicação não está impedida de inserir um blog para o inquilino errado. O restante deste artigo descreve como resolver estes problemas, aplicando o isolamento dos inquilinos com o RLS. Há dois passos:

1. **Nível de aplicação:** Modifique o código de\_aplicação para definir sempre o Atual TenantId no CONTEXTO DA SESSÃO após a abertura de uma ligação. O projeto de amostra já define o TenantId desta forma.
2. **Nível de dados**: Criar uma política de segurança RLS em cada base\_de dados de fragmentos para filtrar linhas com base no TenantId armazenado no CONTEXTO DA SESSÃO. Crie uma política para cada uma das suas bases de dados, caso contrário não são filtradas filas em fragmentos de multi-inquilinos.

## <a name="1-application-tier-set-tenantid-in-the-session_context"></a>1. Nível de candidatura:\_Definir TenantId no CONTEXTO DA SESSÃO

Primeiro, liga-se a uma base de dados de fragmentos utilizando as APIs de encaminhamento dependente de dados da biblioteca de clientes de base de dados elásticas. A aplicação deve ainda dizer à base de dados qual o TenantId que está a utilizar a ligação. O TenantId diz à política de segurança do RLS quais as filas que devem ser filtradas como pertencentes a outros inquilinos. Guarde o atual TenantId no CONTEXTO DE [SESSÃO\_](https://docs.microsoft.com/sql/t-sql/functions/session-context-transact-sql) da ligação.

Uma alternativa\_ao CONTEXTO DA SESSÃO é utilizar [informações contextuais.\_](https://docs.microsoft.com/sql/t-sql/functions/context-info-transact-sql) Mas\_o CONTEXTO DA SESSÃO é uma opção melhor. O\_CONTEXTO DA SESSÃO é mais fácil de usar, devolve o NULO por defeito e suporta pares de valor-chave.

### <a name="entity-framework"></a>Entity Framework

Para aplicações que utilizem o Quadro de\_Entidades, a abordagem mais fácil é definir o CONTEXTO DE SESSÃO dentro do overover ElasticScaleContext descrito no [encaminhamento dependente de dados utilizando o EF DbContext](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md#data-dependent-routing-using-ef-dbcontext). Crie e execute um SqlCommand que\_define o TenantId no CONTEXTO DE SESSÃO para o shardingKey especificado para a ligação. Em seguida, devolva a ligação intermediada através de encaminhamento dependente de dados. Desta forma, só precisa de escrever código\_uma vez para definir o CONTEXTO DA SESSÃO.

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

Agora o\_CONTEXTO DA SESSÃO é automaticamente definido com o Inquilina especificado sempre que o ElasticScaleContext é invocado:

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

Para aplicações que utilizem ADO.NET SqlClient, crie uma função de invólucro em torno do método ShardMap.OpenConnectionForKey. Tenha o invólucro automaticamente definido TenantId\_no CONTEXTO DE SESSÃO para o atual TenantId antes de devolver uma ligação. Para garantir\_que o CONTEXTO DA SESSÃO está sempre definido, só deve abrir ligações utilizando esta função de invólucro.

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

## <a name="2-data-tier-create-row-level-security-policy"></a>2. Nível de dados: Criar uma política de segurança ao nível da linha

### <a name="create-a-security-policy-to-filter-the-rows-each-tenant-can-access"></a>Crie uma política de segurança para filtrar as filas a que cada inquilino pode aceder

Agora que a aplicação está a definir o CONTEXTO DE SESSÃO\_com o atual TenantId antes de consultar, uma política de segurança RLS pode filtrar consultas e excluir linhas que tenham um TenantId diferente.

O RLS é implementado na Transact-SQL. Uma função definida pelo utilizador define a lógica de acesso, e uma política de segurança liga esta função a qualquer número de tabelas. Para este projeto:

1. A função verifica que a aplicação está ligada à base de\_dados, e que o TenantId armazenado no CONTEXTO DE SESSÃO corresponde ao TenantId de uma determinada linha.
    - A aplicação está ligada, em vez de qualquer outro utilizador SQL.

2. Um predicado FILTER permite que as linhas que vão ao encontro do filtro TenantId passem para perguntas SELECT, UPDATE e DELETE.
    - Um predicado de BLOCO evita que as linhas que falham o filtro sejam INSERTEd ou UPDATEd.
    - Se\_o CONTEXTO DA SESSÃO não tiver sido definido, a função devolve NULO, e não são visíveis ou capazes de ser inseridas.

Para ativar o RLS em todos os fragmentos, execute o seguinte T-SQL utilizando o Visual Studio (SSDT), o SSMS ou o script PowerShell incluído no projeto. Ou se estiver a usar trabalhos de base de [dados elásticos,](elastic-jobs-overview.md)pode automatizar a execução deste T-SQL em todos os fragmentos.

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
> Num projeto complexo, você pode precisar adicionar o predicado em centenas de mesas, o que pode ser aborrecido. Existe um procedimento armazenado por ajudantes que gera automaticamente uma política de segurança, e adiciona um predicado em todas as mesas num esquema. Para mais informações, consulte a publicação de blog na [Apply Row-Level Security para todas as tabelas - script de ajudante (blog)](https://blogs.msdn.com/b/sqlsecurity/archive/20../../apply-row-level-security-to-all-tables-helper-script).

Agora, se executar o pedido de amostra novamente, os inquilinos vêem apenas fileiras que lhes pertencem. Além disso, a aplicação não pode inserir linhas que pertençam a inquilinos que não os atualmente ligados à base de dados do fragmento. Além disso, a aplicação não pode atualizar o TenantId em quaisquer linhas que possa ver. Se a aplicação tentar fazer qualquer uma delas, é levantada uma DbUpdateException.

Se adicionar uma nova tabela mais tarde, ALTERE a política de segurança para adicionar predicados FILTER e BLOCK na nova tabela.

```sql
ALTER SECURITY POLICY rls.tenantAccessPolicy
    ADD FILTER PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.MyNewTable,
    ADD BLOCK  PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.MyNewTable;
GO
```

### <a name="add-default-constraints-to-automatically-populate-tenantid-for-inserts"></a>Adicione restrições predefinidas para povoar automaticamente o TenantId para INSERTs

Pode colocar uma restrição predefinida em cada tabela para povoar automaticamente\_o TenantId com o valor atualmente armazenado no CONTEXTO DE SESSÃO ao inserir linhas. Segue-se um exemplo.

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

Agora, a aplicação não precisa de especificar um TenantId ao inserir linhas:

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
> Se utilizar restrições predefinidas para um projeto 'Quadro de Entidades', recomenda-se que *não* inclua a coluna TenantId no seu modelo de dados EF. Esta recomendação é porque as consultas do Quadro de Entidades fornecem automaticamente valores predefinidos que sobrepõem as restrições predefinidas criadas no T-SQL que utilizam o CONTEXTO DE SESSÃO.\_
> Para utilizar constrangimentos predefinidos no projeto da amostra, por exemplo, deve remover o TenantId da DataClasses.cs (e executar add-migration na consola do gestor de pacotes) e utilizar o T-SQL para garantir que o campo só existe nas tabelas de bases de dados. Desta forma, a EF fornece automaticamente valores predefinidos incorretos ao inserir dados.

### <a name="optional-enable-a-superuser-to-access-all-rows"></a>(Opcional) Permitir que um *superutilizador* aceda a todas as linhas

Algumas aplicações podem querer criar um *superutilizador* que possa aceder a todas as linhas. Um superutilizador poderia permitir reportar em todos os inquilinos em todos os fragmentos. Ou um superutilizador poderia realizar operações de fusão em fragmentos que envolvem mover filas de inquilinos entre bases de dados.

Para ativar um superutilizador, crie`superuser` um novo utilizador SQL (neste exemplo) em cada base de dados de fragmentos. Em seguida, altere a política de segurança com uma nova função predicada que permite a este utilizador aceder a todas as linhas. Tal função é dada a seguir.

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

- **Adicionar novos fragmentos**: Execute o script T-SQL para ativar o RLS em quaisquer novos fragmentos, caso contrário não são filtradas consultas nestes fragmentos.
- **Adicionar novas tabelas**: Adicione um filtro e bloqueie a política de segurança em todos os fragmentos sempre que for criada uma nova tabela. Caso contrário, as consultas na nova mesa não são filtradas. Esta adição pode ser automatizada utilizando um gatilho DDL, conforme descrito na Aplicação de [Segurança de Nível de Linha automaticamente para mesas recém-criadas (blog)](https://blogs.msdn.com/b/sqlsecurity/archive/20../../apply-row-level-security-automatically-to-newly-created-tables.aspx).

## <a name="summary"></a>Resumo

As ferramentas de base de dados elásticas e a segurança ao nível da linha podem ser utilizadas em conjunto para escalar o nível de dados de uma aplicação com suporte tanto para fragmentos de multi-inquilinos como de um único inquilino. Fragmentos de multi-inquilinos podem ser usados para armazenar dados de forma mais eficiente. Esta eficiência é pronunciada onde um grande número de inquilinos têm apenas algumas filas de dados. Os fragmentos de inquilinos individuais podem apoiar inquilinos premium que tenham requisitos mais rigorosos de desempenho e isolamento. Para mais informações, consulte a [referência de Segurança ao Nível da Linha][rls].

## <a name="additional-resources"></a>Recursos adicionais

- [O que é um conjunto elástico do Azure?](sql-database-elastic-pool.md)
- [Aumentar horizontalmente com a Base de Dados SQL do Azure](sql-database-elastic-scale-introduction.md)
- [Padrões de Estrutura para Aplicações de SaaS Multi-inquilinos com a Base de Dados SQL do Azure](saas-tenancy-app-design-patterns.md)
- [Autenticação em aplicações multi-inquilino, com o Azure AD e o OpenID Connect](../guidance/guidance-multitenant-identity-authenticate.md)
- [Aplicação Tailspin Surveys](../guidance/guidance-multitenant-identity-tailspin.md)

## <a name="questions-and-feature-requests"></a>Perguntas e Pedidos de Recursos

Para perguntas, contacte-nos no [fórum SQL Database](https://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted). E adicione quaisquer pedidos de funcionalidades ao fórum de feedback da Base de [Dados SQL](https://feedback.azure.com/forums/217321-sql-database/).

<!--Image references-->
[1]: ./media/saas-tenancy-elastic-tools-multi-tenant-row-level-security/blogging-app.png
<!--anchors-->
[rls]: https://docs.microsoft.com/sql/relational-databases/security/row-level-security
[s-d-elastic-database-client-library]: sql-database-elastic-database-client-library.md
