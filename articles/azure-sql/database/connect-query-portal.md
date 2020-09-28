---
title: Consulta uma Base de Dados SQL utilizando o editor de consulta no portal Azure (pré-visualização)
description: Saiba como utilizar o Editor de Consulta para executar consultas Transact-SQL (T-SQL) contra uma base de dados na Base de Dados Azure SQL.
titleSuffix: Azure SQL Database
keywords: conectar à base de dados sql,base de dados de sql de consulta, portal azul, portal, editor de consulta
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: quickstart
author: Ninarn
ms.author: ninarn
ms.reviewer: sstein
ms.date: 05/29/2020
ms.openlocfilehash: 4eb02c65a8ce486ea152863d8b6c6d0600280893
ms.sourcegitcommit: b48e8a62a63a6ea99812e0a2279b83102e082b61
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/28/2020
ms.locfileid: "91409596"
---
# <a name="quickstart-use-the-azure-portals-query-editor-preview-to-query-an-azure-sql-database"></a>Quickstart: Use o editor de consulta do portal Azure (pré-visualização) para consultar uma Base de Dados Azure SQL
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

O editor de consulta é uma ferramenta no portal Azure para executar consultas SQL contra a sua base de dados na Base de Dados Azure SQL ou armazém de dados em Azure Synapse Analytics. 

Neste quickstart, você usará o editor de consulta para executar consultas Transact-SQL (T-SQL) com uma base de dados.

## <a name="prerequisites"></a>Pré-requisitos

Completar este arranque rápido requer a base de dados de amostras AdventureWorksLT. Se não tiver uma cópia de funcionamento da base de dados de amostras AdventureWorksLT na Base de Dados SQL, o seguinte quickstart cria rapidamente uma:

- [Quickstart: Criar uma base de dados na Base de Dados Azure SQL utilizando o portal Azure, PowerShell ou Azure CLI](single-database-create-quickstart.md) 

### <a name="configure-network-settings"></a>Configurar as definições de rede

Se tiver um dos seguintes erros no editor de consulta: *As definições de rede locais podem estar a impedir o Editor de Consulta de emitir consultas. Clique aqui para obter instruções sobre como configurar as definições da sua rede*ou *não foi possível estabelecer uma ligação ao servidor. Isto pode indicar um problema com a configuração de firewall local ou as definições de procuração de rede,* as seguintes informações importantes devem ajudar a resolver:

> [!IMPORTANT]
> O editor de consulta utiliza as portas 443 e 1443 para comunicar. Certifique-se de que ativou o tráfego HTTPS de saída nestas portas. Também precisa de [adicionar o seu endereço IP de saída às regras de firewall permitidas pelo servidor](firewall-create-server-level-portal-quickstart.md) para aceder às suas bases de dados e armazéns de dados.


## <a name="open-the-sql-database-query-editor"></a>Abra o SqL Database Query Editor

1. Faça o súps para entrar no [portal Azure](https://portal.azure.com/) e selecione a base de dados que pretende consultar.

2. No menu de **base de dados SQL,** selecione **Editor de Consulta (pré-visualização)**.

    ![encontrar editor de consultas](./media/connect-query-portal/find-query-editor.PNG)


## <a name="establish-a-connection-to-the-database"></a>Estabelecer uma ligação à base de dados

Apesar de ter assinado no portal, ainda precisa de fornecer credenciais para aceder à base de dados. Pode ligar-se através da autenticação SQL ou do Azure Ative Directory para se ligar à sua base de dados.

### <a name="connect-using-sql-authentication"></a>Ligar com a Autenticação SQL

1. Na página **de Login,** na **autenticação do servidor SQL,** introduza um **Login** e **uma Palavra-passe** para um utilizador que tenha acesso à base de dados. Se não tiver a certeza, utilize o login e a palavra-passe para o administrador do Servidor do servidor da base de dados.

    ![iniciar sessão](./media/connect-query-portal/login-menu.png)

2. Selecione **OK**.


### <a name="connect-using-azure-active-directory"></a>Conecte-se usando diretório ativo Azure

Configurar um administrador do Azure Ative Directory (Azure AD) permite-lhe utilizar uma única identidade para iniciar seduções no portal Azure e na sua base de dados. Para ligar à sua base de dados utilizando o Azure AD, siga os passos abaixo para configurar um administrador AD Azure para a sua instância do SQL Server.

> [!NOTE]
> * As contas de e-mail (por exemplo, outlook.com, gmail.com, yahoo.com, e assim por diante) ainda não são suportadas como administradores da AD Azure. Certifique-se de escolher um utilizador criado nativamente no AD Azure ou federado no AD Azure.
> * O sinal de administração da Admin Azure não funciona com contas que tenham a autenticação de 2 fatores ativada.

#### <a name="set-an-active-directory-admin-for-the-server"></a>Desação de um diretório ativo para o servidor

1. No portal Azure, selecione a sua instância SQL Server.

2. No menu do **servidor SQL,** selecione **Ative Directory admin**.

3. Na barra de ferramentas de página de página de **administração do SqL Server Ative Directory,** selecione **Definir administração** e escolha o utilizador ou grupo como administrador AD Azure.

    ![selecionar o active directory](./media/connect-query-portal/select-active-directory.png)

4. Na página **de administração Adicionar,** na caixa de pesquisa, insira um utilizador ou grupo para encontrar, selecione-o como administrador e, em seguida, escolha o botão **Seleção.**

5. De volta à barra de ferramentas de página de página de página de **administração do sql** Server Ative, selecione **Save**.

### <a name="connect-to-the-database"></a>Ligue-se à base de dados

6. No menu do **servidor SQL,** selecione **bases de dados SQL**e, em seguida, selecione a sua base de dados.

7. No menu de **base de dados SQL,** selecione **Editor de Consulta (pré-visualização)**. Na página **de Login,** sob a etiqueta **de autenticação ative Directory,** aparece uma mensagem a dizer que foi assinado se é um administrador AD Ad Azure. Em seguida, selecione o Botão **Continuar** *\<your user or group ID>* como botão. Se a página indicar que não fez login com sucesso, poderá ter de atualizar a página.

## <a name="query-a-database-in-sql-database"></a>Consulta de uma base de dados na Base de Dados SQL

As seguintes consultas de exemplo devem ser executadas com sucesso contra a base de dados de amostras AdventureWorksLT.

### <a name="run-a-select-query"></a>Executar uma consulta SELECT

1. Cole a seguinte consulta no editor de consulta:

   ```sql
    SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName
    FROM SalesLT.ProductCategory pc
    JOIN SalesLT.Product p
    ON pc.productcategoryid = p.productcategoryid;
   ```

2. Selecione **Executar** e, em seguida, reveja a saída no painel **de resultados.**

   ![Resultados do editor de consultas](./media/connect-query-portal/query-editor-results.png)

3. Opcionalmente, pode guardar a consulta como ficheiro .sql ou exportar os dados devolvidos como ficheiro .json, .csv ou .xml.

### <a name="run-an-insert-query"></a>Executar uma consulta INSERT

Executar a seguinte declaração [DE INSERÇÃO](/sql/t-sql/statements/insert-transact-sql/) T-SQL para adicionar um novo produto na `SalesLT.Product` tabela.

1. Substitua a consulta anterior por esta.

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


2. **Selecione Executar** para inserir uma nova linha na `Product` tabela. O painel **de mensagens** mostra **que a consulta conseguiu: Linhas afetadas: 1**.


### <a name="run-an-update-query"></a>Executar uma consulta UPDATE

Execute a seguinte declaração T-SQL de [ATUALIZAÇÃO](/sql/t-sql/queries/update-transact-sql/) para modificar o seu novo produto.

1. Substitua a consulta anterior por esta.

   ```sql
   UPDATE [SalesLT].[Product]
   SET [ListPrice] = 125
   WHERE Name = 'myNewProduct';
   ```

2. Selecione **Executar** para atualizar a linha especificada na `Product` tabela. O painel **de mensagens** mostra **que a consulta conseguiu: Linhas afetadas: 1**.

### <a name="run-a-delete-query"></a>Executar uma consulta DELETE

Executar a seguinte declaração [DELETE](/sql/t-sql/statements/delete-transact-sql/) T-SQL para remover o seu novo produto.

1. Substitua a consulta anterior por esta:

   ```sql
   DELETE FROM [SalesLT].[Product]
   WHERE Name = 'myNewProduct';
   ```

2. Selecione **Executar** para eliminar a linha especificada na `Product` tabela. O painel **de mensagens** mostra **que a consulta conseguiu: Linhas afetadas: 1**.


## <a name="query-editor-considerations"></a>Considerações de editor de consulta

Há algumas coisas a saber quando se trabalha com o editor de consultas.

* O editor de consulta utiliza as portas 443 e 1443 para comunicar. Certifique-se de que ativou o tráfego HTTPS de saída nestas portas. Também terá de adicionar o seu endereço IP de saída às regras de firewall permitidas pelo servidor para aceder às suas bases de dados e armazéns de dados.

* Se tiver uma ligação de Ligação Privada, o Editor de Consulta funciona sem necessidade de adicionar o endereço Ip do Cliente na firewall sql Database.

* A pressão **F5** atualiza a página do editor de consulta e qualquer consulta que esteja a ser trabalhada perde-se.

* O editor de consulta não suporta a ligação à `master` base de dados.

* Há um prazo de 5 minutos para a execução de consultas.

* O editor de consulta apenas suporta projeção cilíndrica para tipos de dados de geografia.

* Não há suporte para o IntelliSense para tabelas e visualizações de bases de dados, mas o editor suporta automaticamente nomes que já foram dactilografado.




## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre o Transact-SQL (T-SQL) suportado na Base de Dados Azure SQL, consulte [a Resolução das diferenças transact-SQL durante a migração para a Base de Dados SQL.](transact-sql-tsql-differences-sql-server.md)
