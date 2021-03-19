---
title: Consulta uma Base de Dados SQL utilizando o editor de consulta no portal Azure (pré-visualização)
description: Saiba como utilizar o Editor de Consulta para executar consultas Transact-SQL (T-SQL) contra uma base de dados na Base de Dados Azure SQL.
titleSuffix: Azure SQL Database
keywords: conectar à base de dados sql,base de dados de sql de consulta, portal azul, portal, editor de consulta
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: sqldbrb=1, contperf-fy21q3-portal
ms.devlang: ''
ms.topic: quickstart
author: Ninarn
ms.author: ninarn
ms.reviewer: sstein
ms.date: 03/01/2021
ms.openlocfilehash: a6f13e27a5aa2684a16565c616d781e3d5c3a750
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104594194"
---
# <a name="quickstart-use-the-azure-portals-query-editor-preview-to-query-an-azure-sql-database"></a>Quickstart: Use o editor de consulta do portal Azure (pré-visualização) para consultar uma Base de Dados Azure SQL
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

O editor de consulta é uma ferramenta no portal Azure para executar consultas SQL contra a sua base de dados na Base de Dados Azure SQL ou armazém de dados em Azure Synapse Analytics.

Neste quickstart, você usará o editor de consulta para executar consultas Transact-SQL (T-SQL) com uma base de dados.

## <a name="prerequisites"></a>Pré-requisitos

### <a name="create-a-database-with-sample-data"></a>Criar uma base de dados com dados de exemplo

Completar este arranque rápido requer a base de dados de amostras AdventureWorksLT. Se não tiver uma cópia de funcionamento da base de dados de amostras AdventureWorksLT na Base de Dados SQL, o seguinte quickstart ajuda-o a criar rapidamente uma:

[Quickstart: Criar uma base de dados na Base de Dados Azure SQL utilizando o portal Azure, PowerShell ou Azure CLI](single-database-create-quickstart.md)

### <a name="set-an-azure-active-directory-admin-for-the-server-optional"></a>Desconfie de um administrador do Azure Ative Directory para o servidor (opcional)

Configurar um administrador do Azure Ative Directory (Azure AD) permite-lhe utilizar uma única identidade para iniciar seduções no portal Azure e na sua base de dados. Se quiser utilizar o Azure AD para ligar ao editor de consulta, siga os passos abaixo.

Este processo é opcional, pode em vez disso utilizar a autenticação SQL para ligar ao editor de consulta.

> [!NOTE]
> * As contas de e-mail (por exemplo, outlook.com, gmail.com, yahoo.com, e assim por diante) ainda não são suportadas como administradores da AD Azure. Certifique-se de escolher um utilizador criado nativamente no AD Azure ou federado no AD Azure.
> * O Azure AD admin assina em obras com contas que têm autenticação de 2 fatores ativada, mas o editor de consulta não suporta a autenticação de 2 fatores.

1. No portal Azure, navegue para o seu servidor de base de dados SQL.

2. No menu do **servidor SQL,** selecione **Ative Directory admin**.

3. Na barra de ferramentas de página de página de **administração do diretório ativo do** sql Server, selecione **Definir administração**.

    ![selecionar o active directory](./media/connect-query-portal/select-active-directory.png)

4. Na página **de administração Adicionar,** na caixa de pesquisa, insira um utilizador ou grupo para encontrar, selecione-o como administrador e, em seguida, escolha o botão **Seleção.**

5. De volta à barra de ferramentas de página de **administração do sql** Server Ative, selecione **Save**.

## <a name="using-sql-query-editor"></a>Usando o SQL Query Editor

1. Faça o súps para entrar no [portal Azure](https://portal.azure.com/) e selecione a base de dados que pretende consultar.

2. No menu de **base de dados SQL,** selecione **Editor de Consulta (pré-visualização)**.

    ![encontrar editor de consultas](./media/connect-query-portal/find-query-editor.PNG)

### <a name="establish-a-connection-to-the-database"></a>Estabelecer uma ligação à base de dados

Apesar de ter assinado no portal, ainda precisa de fornecer credenciais para aceder à base de dados. Pode ligar-se através da autenticação SQL ou do Azure Ative Directory para se ligar à sua base de dados.

#### <a name="connect-using-sql-authentication"></a>Ligar com a Autenticação SQL

1. Na página **de Login,** na **autenticação do servidor SQL,** introduza um **Login** e **uma Palavra-passe** para um utilizador que tenha acesso à base de dados. Se não tiver a certeza, utilize o login e a palavra-passe para o administrador do Servidor do servidor da base de dados.

    ![iniciar sessão](./media/connect-query-portal/login-menu.png)

2. Selecione **OK**.

#### <a name="connect-using-azure-active-directory"></a>Conecte-se usando diretório ativo Azure

No **editor de consulta (pré-visualização)**, veja a página **de Login** na secção de autenticação do **Ative Directory.** A autenticação acontecerá automaticamente, por isso, se for um administrador AD AD Azure para a base de dados, verá uma mensagem a dizer que foi assinada. Em seguida, selecione o Botão **Continuar** *\<your user or group ID>* como botão. Se a página indicar que não fez login com sucesso, poderá ter de atualizar a página.

### <a name="query-a-database-in-sql-database"></a>Consulta de uma base de dados na Base de Dados SQL

As seguintes consultas de exemplo devem ser executadas com sucesso contra a base de dados de amostras AdventureWorksLT.

#### <a name="run-a-select-query"></a>Executar uma consulta SELECT

1. Cole a seguinte consulta no editor de consulta:

   ```sql
    SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName
    FROM SalesLT.ProductCategory pc
    JOIN SalesLT.Product p
    ON pc.productcategoryid = p.productcategoryid;
   ```

2. Selecione **Executar** e, em seguida, reveja a saída no painel **de resultados.**

   ![Resultados do editor de consultas](./media/connect-query-portal/query-editor-results.png)

3. Opcionalmente, pode guardar a consulta como um ficheiro .sql ou exportar os dados devolvidos como ficheiro .json, .csv ou .xml.

#### <a name="run-an-insert-query"></a>Executar uma consulta INSERT

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


#### <a name="run-an-update-query"></a>Executar uma consulta UPDATE

Execute a seguinte declaração T-SQL de [ATUALIZAÇÃO](/sql/t-sql/queries/update-transact-sql/) para modificar o seu novo produto.

1. Substitua a consulta anterior por esta.

   ```sql
   UPDATE [SalesLT].[Product]
   SET [ListPrice] = 125
   WHERE Name = 'myNewProduct';
   ```

2. Selecione **Executar** para atualizar a linha especificada na `Product` tabela. O painel **de mensagens** mostra **que a consulta conseguiu: Linhas afetadas: 1**.

#### <a name="run-a-delete-query"></a>Executar uma consulta DELETE

Executar a seguinte declaração [DELETE](/sql/t-sql/statements/delete-transact-sql/) T-SQL para remover o seu novo produto.

1. Substitua a consulta anterior por esta:

   ```sql
   DELETE FROM [SalesLT].[Product]
   WHERE Name = 'myNewProduct';
   ```

2. Selecione **Executar** para eliminar a linha especificada na `Product` tabela. O painel **de mensagens** mostra **que a consulta conseguiu: Linhas afetadas: 1**.


## <a name="troubleshooting-and-considerations"></a>Resolução de problemas e considerações

Há algumas coisas a saber quando se trabalha com o editor de consultas.

### <a name="configure-local-network-settings"></a>Configurar as definições de rede locais

Se tiver um dos seguintes erros no editor de consulta:
 - *As definições de rede locais podem estar a impedir o Editor de Consulta de emitir consultas. Clique aqui para obter instruções sobre como configurar as definições da sua rede*
 - *Não foi possível estabelecer uma ligação ao servidor. Isto pode indicar um problema com a configuração de firewall local ou as definições de procuração de rede*

Isto porque o editor de consulta usa a porta 443 e 1443 para comunicar. Terá de garantir que ativou o tráfego HTTPS de saída nestas portas. As instruções abaixo irão acompanhá-lo através de como fazê-lo, dependendo do seu Sistema Operativo. Poderá ter de trabalhar com a sua empresa de TI para conceder aprovação para abrir esta ligação na sua rede local.

#### <a name="steps-for-windows"></a>Passos para Janelas

1. Firewall **open Windows Defender**
2. No menu do lado esquerdo, selecione **Definições Avançadas**
3. No **Windows Defender Firewall com Segurança Avançada,** selecione as **regras de saída** no menu do lado esquerdo.
4. Selecione **Nova Regra...** no menu do lado direito

No **novo assistente de regras de saída** siga estes passos:

1. Selecione **a porta** como o tipo de regra que pretende criar. Selecione **Seguinte**
2. Selecione **TCP**
3. Selecione **portas remotas específicas** e introduza "443, 1443". Em seguida, selecione **Next**
4. Selecione "Permitir a ligação se estiver segura"
5. Selecione **Next** e, em seguida, selecione **Next** novamente
5. Mantenha "Domínio", "Privado" e "Público" todos selecionados
6. Dê à regra um nome, por exemplo , "Access Azure SQL editor de consulta" e opcionalmente uma descrição. Em seguida, **selecione Terminar**

#### <a name="steps-for-mac"></a>Passos para Mac
1. **Preferências do sistema aberto** (menu apple > Preferências do sistema).
2. Clique **em Segurança & Privacidade**.
3. Clique **em Firewall**.
4. Se firewall estiver desligado, **selecione Clique na fechadura para fazer alterações** na parte inferior e selecione Ligue a **Firewall**
4. Clique em **Opções de Firewall**.
5. Na janela **de privacidade & de segurança** selecione esta opção: "Automaticamente permita que o software assinado receba ligações recebidas."

#### <a name="steps-for-linux"></a>Passos para Linux
Executar estes comandos para atualizar iptables
  ```
  sudo iptables -A OUTPUT -p tcp --dport 443 -j ACCEPT
  sudo iptables -A OUTPUT -p tcp --dport 1443 -j ACCEPT
  ```

### <a name="connection-considerations"></a>Considerações de conexão

* Para que as ligações públicas possam ser consultadas pelo editor, é necessário [adicionar o seu endereço IP de saída às regras de firewall permitidas](firewall-create-server-level-portal-quickstart.md) para aceder às suas bases de dados e armazéns de dados.

* Se tiver uma ligação Private Link configurada no servidor e estiver a ligar-se ao editor de consulta a partir de um IP na Rede Virtual privada, o Editor de Consulta funciona sem necessidade de adicionar o endereço IP do Cliente às regras de firewall do servidor de base de dados SQL.

* As permissões RBAC mais básicas necessárias para usar o editor de consulta são ler o acesso ao servidor e base de dados. Qualquer pessoa com este nível de acesso pode aceder à funcionalidade de editor de consulta. Para limitar o acesso a determinados utilizadores, deve impedir que possam entrar no editor de consulta com credenciais de autenticação Azure Ative. Se não puderem atribuir-se como administrador da AAD para o servidor ou aceder/adicionar uma conta de administrador SQL, não devem poder utilizar o editor de consulta.

* O editor de consulta não suporta a ligação à `master` base de dados.

* O editor de consulta não pode ligar-se a uma base de dados de réplicas com `ApplicationIntent=ReadOnly`

* Se viu esta mensagem de erro "O cabeçalho X-CSRF-Signature não pôde ser validado", tome as seguintes medidas para resolver o problema:

    * Certifique-se de que o relógio do computador está definido para o fuso horário e hora certo. Também pode tentar combinar o fuso horário do seu computador com o Azure, procurando o fuso horário para a localização do seu caso, como East US, Pacific, e assim por diante.
    * Se estiver numa rede de procuração, certifique-se de que o cabeçalho de pedido "X-CSRF-Signature" não está a ser modificado ou largado.

### <a name="other-considerations"></a>Outras considerações

* A pressão **F5** atualiza a página do editor de consulta e qualquer consulta que esteja a ser trabalhada perde-se.

* Há um prazo de 5 minutos para a execução de consultas.

* O editor de consulta apenas suporta projeção cilíndrica para tipos de dados de geografia.

* Não há suporte para o IntelliSense para tabelas e visualizações de bases de dados, mas o editor suporta automaticamente nomes que já foram dactilografado.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre o Transact-SQL (T-SQL) suportado na Base de Dados Azure SQL, consulte [a Resolução das diferenças transact-SQL durante a migração para a Base de Dados SQL.](transact-sql-tsql-differences-sql-server.md)
