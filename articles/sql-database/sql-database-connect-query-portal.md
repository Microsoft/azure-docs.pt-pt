---
title: Consulta de uma Base de Dados SQL usando o editor de consulta no portal Azure
description: Aprenda a usar o Editor de Consulta para executar consultas Transact-SQL (T-SQL) contra uma Base de Dados Azure SQL.
keywords: ligar à base de dados sql,consulta sql base de dados, portal azure, portal, editor de consulta
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: Ninarn
ms.author: ninarn
ms.reviewer: carlrab
ms.date: 03/12/2020
ms.openlocfilehash: 5847ef3033d257faef4831785b8abd864d54e835
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79209585"
---
# <a name="quickstart-use-the-azure-portals-query-editor-to-query-a-sql-database"></a>Quickstart: Use o editor de consulta do portal Azure para consultar uma base de dados SQL

O editor de consulta é uma ferramenta no portal Azure para executar consultas SQL contra a sua base de dados Azure SQL ou Azure SQL Data Warehouse. 

Neste arranque rápido, você usará o editor de consulta para executar perguntas Transact-SQL (T-SQL) contra uma base de dados Azure SQL.


## <a name="prerequisites"></a>Pré-requisitos

Completar este quickstart requer a base de dados da amostra AdventureWorksLT. Se não tiver uma cópia de trabalho da base de dados AdventureWorksLT SQL, o seguinte arranque rápido rapidamente cria um:

- [Quickstart: Criar uma única base de dados Azure SQL utilizando o portal Azure, PowerShell ou Azure CLI](sql-database-single-database-get-started.md) 

### <a name="configure-network-settings"></a>Configurar as definições de rede

Se tiver um dos seguintes erros no editor de consulta: As definições de *rede locais podem estar a impedir o Editor de Consulta de emitir consultas. Clique aqui para obter instruções sobre como configurar as definições*da sua rede , ou *não foi possível estabelecer uma ligação ao servidor. Isto pode indicar um problema com a configuração*de firewall local ou as definições de procuração de rede, as seguintes informações importantes devem ajudar a resolver:

> [!IMPORTANT]
> O editor de consulta usa as portas 443 e 1443 para comunicar. Certifique-se de que ativou o tráfego HTTPS de saída nestes portos. Também precisa de adicionar o seu endereço IP de [saída às regras de firewall permitidas pelo servidor](sql-database-server-level-firewall-rule.md) para aceder às suas bases de dados e armazéns de dados.


## <a name="open-the-sql-database-query-editor"></a>Abra o Editor de Consulta de Base de Dados SQL

1. Inscreva-se no [portal Azure](https://portal.azure.com/) e selecione a base de dados SQL que pretende consultar.

2. No menu de base de **dados SQL,** selecione **Query editor (pré-visualização)** .

    ![encontrar editor de consultas](./media/sql-database-connect-query-portal/find-query-editor.PNG)


## <a name="establish-a-connection-to-the-database"></a>Estabelecer uma ligação à base de dados

Apesar de ter assinado no portal, ainda precisa de fornecer credenciais para aceder à base de dados SQL. Pode ligar-se utilizando a autenticação SQL ou o Diretório Ativo Azure para se ligar à sua base de dados.

### <a name="connect-using-sql-authentication"></a>Ligar com a Autenticação SQL

1. Na página **de Login,** sob a autenticação do **servidor SQL,** introduza um **Login** e **Palavra-passe** para um utilizador que tenha acesso à base de dados. Se não tiver a certeza, utilize o login e a palavra-passe para o servidor do servidor do Servidor.

    ![iniciar sessão](./media/sql-database-connect-query-portal/login-menu.png)

2. Selecione **OK**.


### <a name="connect-using-azure-active-directory"></a>Ligar com o Azure Active Directory

Configurar um administrador azure Ative Directory (Azure AD) permite-lhe utilizar uma única identidade para iniciar sessão no portal Azure e na sua base de dados SQL. Para se ligar à sua base de dados utilizando o Azure AD, siga os passos abaixo para configurar um administrador Azure AD para o seu servidor SQL.

> [!NOTE]
> * As contas de e-mail (por exemplo, outlook.com, gmail.com, yahoo.com, e assim por diante) ainda não são suportadas como administradores da Azure AD. Confirme que escolhe um utilizador criado nativamente no Azure AD, ou federado no Azure AD.
> * Sessão de administrador do Azure AD não funciona com contas que têm a autenticação de 2 fatores ativada.

#### <a name="set-an-active-directory-admin-for-the-database-server"></a>Detete um administrador de Diretório Ativo para o servidor de base de dados

1. No portal Azure, selecione o seu servidor SQL.

2. No menu do **servidor SQL,** selecione **administração ative directory**.

3. Na barra de ferramentas de administração do servidor SQL **Ative Directory,** selecione **Administração set** e escolha o utilizador ou grupo como administrador aD Azure.

    ![selecionar o active directory](./media/sql-database-connect-query-portal/select-active-directory.png)

4. Na página **'Adicionar',** na caixa de pesquisa, introduza um utilizador ou grupo para encontrar, selecione-o como administrador e, em seguida, escolha o botão **Select.**

5. De volta à barra de ferramentas de **administração de diretório ativo** do servidor SQL, selecione **Save**.

### <a name="connect-to-the-database"></a>Ligue-se à base de dados

6. No menu do **servidor SQL,** selecione bases de **dados SQL**e, em seguida, selecione a sua base de dados SQL.

7. No menu de base de **dados SQL,** selecione **Query editor (pré-visualização)** . Na página **de Login,** sob a etiqueta de **autenticação Ative Directy,** aparece uma mensagem a dizer que foi contratada se for administrador ad. Em seguida, selecione o **Botão Continue\<** *seu utilizador ou grupo ID>* botão. Se a página indicar que não conseguiu iniciar sessão, poderá ter de refrescar a página.

## <a name="query-a-sql-database"></a>Consulta de uma base de dados SQL

As seguintes consultas de exemplo devem ser executadas com sucesso contra a base de dados da amostra AdventureWorksLT.

### <a name="run-a-select-query"></a>Executar uma consulta SELECT

1. Colhe a seguinte consulta no editor de consulta:

   ```sql
    SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName
    FROM SalesLT.ProductCategory pc
    JOIN SalesLT.Product p
    ON pc.productcategoryid = p.productcategoryid;
   ```

2. Selecione **Executar** e, em seguida, reveja a saída no painel **resultados.**

   ![Resultados do editor de consultas](./media/sql-database-connect-query-portal/query-editor-results.png)

3. Opcionalmente, pode guardar a consulta como um ficheiro .sql ou exportar os dados devolvidos como um ficheiro .json, .csv ou .xml.

### <a name="run-an-insert-query"></a>Executar uma consulta INSERT

Executar a seguinte declaração [INSERT](/sql/t-sql/statements/insert-transact-sql/) T-SQL para adicionar um novo produto na tabela `SalesLT.Product`.

1. Substitua a consulta anterior este.

    ```sql
    INSERT INTO [SalesLT].[Product]
           ( [Name]
           , [ProductNumber]
           , [Color]
           , [ProductCategoryID]
           , [StandardCost]
           , [ListPrice]
           , [SellStartDate]
           )
    VALUES
           ('myNewProduct'
           ,123456789
           ,'NewColor'
           ,1
           ,100
           ,100
           ,GETDATE() );
   ```


2. Selecione **Executar** para inserir uma nova linha na tabela `Product`. O painel de **mensagens** mostra que a **consulta foi bem sucedida: Linhas afetadas: 1**.


### <a name="run-an-update-query"></a>Executar uma consulta de atualização

Execute a seguinte declaração de [Atualização](/sql/t-sql/queries/update-transact-sql/) T-SQL para modificar o seu novo produto.

1. Substitua a consulta anterior este.

   ```sql
   UPDATE [SalesLT].[Product]
   SET [ListPrice] = 125
   WHERE Name = 'myNewProduct';
   ```

2. Selecione **Executar** para atualizar a linha especificada na tabela `Product`. O painel de **mensagens** mostra que a **consulta foi bem sucedida: Linhas afetadas: 1**.

### <a name="run-a-delete-query"></a>Executar uma consulta DELETE

Executar a seguinte declaração [DELETE](/sql/t-sql/statements/delete-transact-sql/) T-SQL para remover o seu novo produto.

1. Substitua a consulta anterior este:

   ```sql
   DELETE FROM [SalesLT].[Product]
   WHERE Name = 'myNewProduct';
   ```

2. Selecione **Executar** para eliminar a linha especificada na tabela `Product`. O painel de **mensagens** mostra que a **consulta foi bem sucedida: Linhas afetadas: 1**.


## <a name="query-editor-considerations"></a>Considerações de editor de consulta

Existem alguns aspetos a saber ao trabalhar com o editor de consultas.

* O editor de consulta usa as portas 443 e 1443 para comunicar. Certifique-se de que ativou o tráfego HTTPS de saída nestes portos. Também terá de adicionar o seu endereço IP de saída às regras de firewall permitidas pelo servidor para aceder às suas bases de dados e armazéns de dados.

* O Editor de Consulta trabalha com private link sem necessidade de adicionar o endereço Ip do Cliente na firewall sqL Database

* Pressionar **f5** refresca a página do editor de consulta e qualquer consulta em que está a ser trabalhado está perdida.

* O editor de consulta não suporta a ligação à base de dados `master`.

* Há um tempo limite de 5 minutos para execução da consulta.

* O editor de consultas suporta apenas a projeção cilíndrica para tipos de dados de geografia.

* Não há suporte para o IntelliSense para tabelas e vistas de base de dados, mas o editor suporta automaticamente os nomes que já foram dactilografados.




## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre o Transact-SQL (T-SQL) suportado nas bases de dados Azure SQL, consulte a Resolução das [diferenças Transact-SQL durante](sql-database-transact-sql-information.md)a migração para a Base de Dados SQL .
