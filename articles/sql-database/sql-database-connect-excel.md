---
title: Conectar o Excel a um banco de dados individual
description: Saiba como conectar o Microsoft Excel a um banco de dados individual no banco de dados SQL do Azure. Importe dados para o Excel para criação de relatórios e exploração de dados.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: joseidz
ms.author: craigg
ms.reviewer: ''
ms.date: 02/12/2019
ms.openlocfilehash: de5f23bf1e8acd8a5fcd0cf8e1526f88667800c1
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73827122"
---
# <a name="connect-excel-to-a-single-database-in-azure-sql-database-and-create-a-report"></a>Conectar o Excel a um banco de dados individual no banco de dados SQL do Azure e criar um relatório

Conecte o Excel a um único banco de dados no banco de dado SQL do Azure e importe os dados e crie tabelas e gráficos com base em valores no banco de dados. Neste tutorial, irá configurar a ligação entre o Excel e uma tabela de base de dados, guardar o ficheiro que armazena os dados e as informações de ligação para o Excel e, em seguida, criar um gráfico dinâmico a partir dos valores da base de dados.

Você precisará de um banco de dados individual antes de começar. Se você não tiver uma, confira [criar um banco](sql-database-single-database-get-started.md) de dados individual e [criar um firewall de IP no nível de servidor](sql-database-server-level-firewall-rule.md) para obter um único banco de dado com um exemplo de data em funcionamento em alguns minutos.

Neste artigo, você importará dados de exemplo para o Excel a partir desse artigo, mas poderá seguir etapas semelhantes com seus próprios dados.

Irá também precisar de uma cópia do Excel. Este artigo utiliza o [Microsoft Excel 2016](https://products.office.com/).

## <a name="connect-excel-to-a-sql-database-and-load-data"></a>Conectar o Excel a um banco de dados SQL

1. Para ligar o Excel à base de dados SQL, abra o Excel e, em seguida, crie um novo livro ou abra um livro do Excel existente.
2. Na barra de menus na parte superior da página, selecione a guia **dados** , selecione **obter dados**, selecione do Azure e, em seguida, selecione **do banco de dado SQL do Azure**. 

   ![Selecione a origem de dados: ligar o Excel à base de dados SQL.](./media/sql-database-connect-excel/excel_data_source.png)

   É aberto o Assistente de Ligação de Dados.
3. Na caixa de diálogo **Ligar ao Servidor de Bases de Dados**, escreva o **Nome do servidor** da Base de Dados SQL à qual pretende ligar, no formato <*servername*> **. database.windows.net**. Por exemplo, **msftestserver.Database.Windows.net**. Opcionalmente, insira o nome do seu banco de dados. Selecione **OK** para abrir a janela de credenciais. 

   ![Caixa de diálogo conectar ao servidor de banco de dados](media/sql-database-connect-excel/server-name.png)

4. Na caixa de diálogo **SQL Server banco de dados** , selecione **banco de dados** no lado esquerdo e, em seguida, insira seu **nome de usuário** e **senha** para o servidor de banco de dados SQL ao qual você deseja se conectar. Selecione **conectar** para abrir o **navegador**. 

   ![Escrever o nome do servidor e as credenciais de início de sessão](./media/sql-database-connect-excel/connect-to-server.png)

   > [!TIP]
   > Dependendo do ambiente de rede, poderá não conseguir ligar ou poderá perder a ligação se o servidor da base de dados SQL não permitir tráfego a partir do seu endereço IP de cliente. Aceda ao [portal do Azure](https://portal.azure.com/), clique em servidores SQL, clique no servidor, clique em firewall em definições e adicione o seu endereço IP de cliente. Consulte [Como configurar as definições da firewall](sql-database-configure-firewall-settings.md) para detalhes.

5. No **navegador**, selecione o banco de dados com o qual você deseja trabalhar na lista, selecione as tabelas ou exibições com as quais você deseja trabalhar (escolhemos **vGetAllCategories**) e, em seguida, selecione **carregar** para mover os dados do banco para a planilha do Excel.

    ![Selecione uma base de dados e a tabela.](./media/sql-database-connect-excel/select-database-and-table.png)

## <a name="import-the-data-into-excel-and-create-a-pivot-chart"></a>Importar os dados para o Excel e criar um gráfico dinâmico

Agora que você estabeleceu a conexão, tem várias opções diferentes com a forma de carregar os dados. Por exemplo, as etapas a seguir criam um gráfico dinâmico com base nos dados encontrados no seu SQL Database. 

1. Siga as etapas na seção anterior, mas, desta vez, em vez de selecionar **carregar**, selecione **carregar para** na lista suspensa **carregar** .
2. Em seguida, selecione como você deseja exibir esses dados em sua pasta de trabalho. Vamos escolher **PivotChart**. Também pode optar por criar uma **Nova folha de cálculo** ou **Adicionar estes dados ao Modelo de Dados**. Para mais informações sobre Modelos de Dados, consulte [Criar um modelo de dados no Excel](https://support.office.com/article/Create-a-Data-Model-in-Excel-87E7A54C-87DC-488E-9410-5C75DBCB0F7B). 

    ![Escolher o formato para dados no Excel](./media/sql-database-connect-excel/import-data.png)

    A folha de cálculo tem agora um gráfico e uma tabela dinâmicos em branco.
3. Em **Campos PivotTable**, selecione todas as caixas de verificação para os campos que pretende visualizar.

    ![Configure o relatório de base de dados.](./media/sql-database-connect-excel/power-pivot-results.png)

> [!TIP]
> Se você quiser conectar outras pastas de trabalho e planilhas do Excel ao banco de dados, selecione a guia **Data** e selecione **fontes recentes** para iniciar a caixa de diálogo **fontes recentes** . A partir daí, escolha a conexão que você criou na lista e clique em **abrir**.
> caixa de diálogo ![fontes recentes](media/sql-database-connect-excel/recent-connections.png)

## <a name="create-a-permanent-connection-using-odc-file"></a>Criar uma conexão permanente usando o arquivo. odc

Para salvar os detalhes da conexão permanentemente, você pode criar um arquivo. odc e tornar essa conexão uma opção selecionável dentro da caixa de diálogo **conexões existentes** . 

1. Na barra de menus na parte superior da página, selecione a guia **dados** e, em seguida, selecione **conexões existentes** para iniciar a caixa de diálogo **conexões existentes** . 
   1. Selecione **Procurar mais** para abrir a caixa de diálogo **selecionar fonte de dados** .   
   2. Selecione o arquivo **+ NewSqlServerConnection. odc** e, em seguida, selecione **abrir** para abrir o assistente de **conexão de dados**.

      ![Caixa de diálogo Nova conexão](media/sql-database-connect-excel/new-connection.png)

2. No **Assistente de conexão de dados**, digite o nome do servidor e suas credenciais do banco de dados SQL. Selecione **Seguinte**. 
   1. Selecione o banco de dados que contém os seus dados na lista suspensa. 
   2. Selecione a tabela ou exibição em que você está interessado. Escolhemos vGetAllCategories.
   3. Selecione **Seguinte**. 

      ![Assistente de conexão de dados](media/sql-database-connect-excel/data-connection-wizard.png) 

3. Selecione o local do arquivo, o **nome do arquivo**e o **nome amigável** na próxima tela do assistente de conexão de dados. Você também pode optar por salvar a senha no arquivo, embora isso possa expor potencialmente seus dados para acesso indesejado. Selecione **concluir** quando estiver pronto. 

    ![Salvar conexão de dados](media/sql-database-connect-excel/save-data-connection.png)

4. Selecione como você deseja importar seus dados. Optamos por fazer uma tabela dinâmica. Você também pode modificar as propriedades da conexão selecionando **Propriedades**. Selecione **OK** quando estiver pronto. Se você não optou por salvar a senha com o arquivo, será solicitado que insira suas credenciais. 

    ![Importar Dados](media/sql-database-connect-excel/import-data2.png)

5. Verifique se a nova conexão foi salva expandindo a guia **dados** e selecionando **conexões existentes**. 

    ![Conexão existente](media/sql-database-connect-excel/existing-connection.png)

## <a name="next-steps"></a>Passos seguintes

* Saiba como [Ligar a Base de Dados SQL com o SQL Server Management Studio](sql-database-connect-query-ssms.md) para consulta e análise avançadas.
* Saiba mais sobre as vantagens dos [conjuntos elásticos](sql-database-elastic-pool.md).
* Saiba como [criar uma aplicação Web que estabelece ligação à Base de Dados SQL no back-end](../app-service/app-service-web-tutorial-dotnet-sqldatabase.md).
