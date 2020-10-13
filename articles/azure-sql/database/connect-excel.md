---
title: Ligar com o Excel
description: Saiba como ligar o Microsoft Excel a uma base de dados na Base de Dados Azure SQL ou na Azure SQL Managed Instance. Importe dados para o Excel para criação de relatórios e exploração de dados.
titleSuffix: Azure SQL Database & SQL Managed Instance
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 05/29/2020
ms.openlocfilehash: 304ad51bc40c8165c2dc11bb70287491e3d6dfb9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91444032"
---
# <a name="connect-excel-to-a-database-in-azure-sql-database-or-azure-sql-managed-instance-and-create-a-report"></a>Ligue o Excel a uma base de dados na Base de Dados Azure SQL ou na Azure SQL Gerenciada Instância, e crie um relatório
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Pode ligar o Excel a uma base de dados e depois importar dados e criar tabelas e gráficos com base em valores na base de dados. Neste tutorial, irá configurar a ligação entre o Excel e uma tabela de base de dados, guardar o ficheiro que armazena os dados e as informações de ligação para o Excel e, em seguida, criar um gráfico dinâmico a partir dos valores da base de dados.

Tens de criar uma base de dados antes de começares. Se não tiver uma, consulte [criar uma base de dados na Base de Dados Azure SQL](single-database-create-quickstart.md) e criar firewall IP ao [nível do servidor](firewall-create-server-level-portal-quickstart.md) para obter uma base de dados com dados de amostra em funcionamento em poucos minutos.

Neste artigo, irá importar dados de amostras para o Excel a partir desse artigo, mas pode seguir passos semelhantes com os seus próprios dados.

Irá também precisar de uma cópia do Excel. Este artigo utiliza o [Microsoft Excel 2016](https://products.office.com/).

## <a name="connect-excel-and-load-data"></a>Ligue o Excel e carregue os dados

1. Para ligar o Excel a uma base de dados na Base de Dados SQL, abra o Excel e, em seguida, crie um novo livro de trabalho ou abra um livro de trabalho excel existente.
2. Na barra de menus no topo da página, selecione o separador **Dados,** selecione **Obter Dados**, selecione From Azure e, em seguida, selecione **Azure SQL Database**.

   ![Selecione fonte de dados: Ligue o Excel à Base de Dados SQL.](./media/connect-excel/excel_data_source.png)

3. Na caixa de diálogo **de base de dados SQL Server,** digite o **nome do Servidor** a que pretende ligar no formulário <nome *de servidor* > **.database.windows.net**. Por exemplo, **msftestserver.database.windows.net.** Opcionalmente, insira o nome da sua base de dados. Selecione **OK** para abrir a janela de credenciais.

   ![Ligue-se à caixa de diálogo do servidor de bases de dados](./media/connect-excel/server-name.png)

4. Na caixa de diálogo **de base de dados SQL Server,** selecione **Base de Dados** no lado esquerdo e, em seguida, introduza o **seu Nome de Utilizador** e **Palavra-passe** para o servidor a que pretende ligar. Selecione **Connect** para abrir o **Navegador**.

   ![Escrever o nome do servidor e as credenciais de início de sessão](./media/connect-excel/connect-to-server.png)

   > [!TIP]
   > Dependendo do ambiente da rede, poderá não conseguir ligar-se ou poderá perder a ligação se o servidor não permitir o tráfego do endereço IP do seu cliente. Aceda ao [portal do Azure](https://portal.azure.com/), clique em servidores SQL, clique no servidor, clique em firewall em definições e adicione o seu endereço IP de cliente. Consulte [Como configurar as definições da firewall](firewall-configure.md) para detalhes.

5. No **Navigator**, selecione a base de dados com a quais pretende trabalhar a partir da lista, selecione as tabelas ou vistas com as quais pretende trabalhar **(escolhemos vGetAllCategories),** e, em seguida, selecione **Load** para mover os dados da sua base de dados para a sua folha de cálculo do Excel.

    ![Selecione uma base de dados e a tabela.](./media/connect-excel/select-database-and-table.png)

## <a name="import-the-data-into-excel-and-create-a-pivot-chart"></a>Importar os dados para o Excel e criar um gráfico dinâmico

Agora que estabeleceu a ligação, tem várias opções diferentes sobre como carregar os dados. Por exemplo, os seguintes passos criam um gráfico de rotação com base nos dados encontrados na sua base de dados na Base de Dados SQL.

1. Siga os passos na secção anterior, mas desta vez, em vez de selecionar **Load**, selecione **Load to** from the **Load** drop-down.
2. Em seguida, selecione como pretende ver estes dados no seu livro. Vamos escolher **PivotChart**. Também pode optar por criar uma **Nova folha de cálculo** ou **Adicionar estes dados ao Modelo de Dados**. Para mais informações sobre Modelos de Dados, consulte [Criar um modelo de dados no Excel](https://support.office.com/article/Create-a-Data-Model-in-Excel-87E7A54C-87DC-488E-9410-5C75DBCB0F7B).

    ![Escolher o formato para dados no Excel](./media/connect-excel/import-data.png)

    A folha de cálculo tem agora um gráfico e uma tabela dinâmicos em branco.
3. Em **Campos PivotTable**, selecione todas as caixas de verificação para os campos que pretende visualizar.

    ![Configure o relatório de base de dados.](./media/connect-excel/power-pivot-results.png)

> [!TIP]
> Se pretender ligar outros livros e folhas de cálculo do Excel à base de dados, selecione o separador **Dados** e selecione **Fontes Recentes** para lançar a caixa de diálogo **Fontes Recentes.** A partir daí, escolha a ligação que criou a partir da lista e, em seguida, clique em **Abrir**.
> ![Caixa de diálogo sources recentes](./media/connect-excel/recent-connections.png)

## <a name="create-a-permanent-connection-using-odc-file"></a>Criar uma ligação permanente utilizando o ficheiro .odc

Para guardar permanentemente os dados de ligação, pode criar um ficheiro .odc e tornar esta ligação uma opção selecionável dentro da caixa de diálogo **de ligações existente.**

1. Na barra de menus no topo da página, selecione o separador **Dados** e, em seguida, selecione **As Ligações Existentes** para lançar a caixa de diálogo **de ligações existentes.**
   1. **Selecione Procurar mais para** abrir a caixa de diálogo **'Origem de Dados' Select.**
   2. Selecione o ficheiro **+NewSqlServerConnection.odc** e, em seguida, selecione **Abrir** para abrir o **Assistente de Ligação**de Dados .

      ![Caixa de diálogo de nova ligação](./media/connect-excel/new-connection.png)

2. No Assistente de **Ligação de Dados,** digite o nome do servidor e as suas credenciais de Base de Dados SQL. Selecione **Seguinte**.
   1. Selecione a base de dados que contém os seus dados a partir do drop-down.
   2. Selecione a mesa ou vista em que está interessado. Escolhemos vGetAllCategories.
   3. Selecione **Seguinte**.

      ![Assistente de Ligação de Dados](./media/connect-excel/data-connection-wizard.png)

3. Selecione a localização do seu ficheiro, o **Nome do Ficheiro**e o Nome **Amigável** no ecrã seguinte do Assistente de Ligação de Dados. Também pode optar por guardar a palavra-passe no ficheiro, embora isso possa potencialmente expor os seus dados a um acesso indesejado. **Selecione Terminar** quando estiver pronto.

    ![Guardar a ligação de dados](./media/connect-excel/save-data-connection.png)

4. Selecione como pretende importar os seus dados. Escolhemos fazer uma Mesa Desagradar. Também pode modificar as propriedades da ligação por **propriedades selecionadas.** Selecione **OK** quando estiver pronto. Se não optou por guardar a palavra-passe com o ficheiro, será solicitado que introduza as suas credenciais.

    ![Importar Dados](./media/connect-excel/import-data2.png)

5. Verifique se a sua nova ligação foi guardada expandindo o separador **Dados** e selecionando **as Ligações Existentes**.

    ![Conexão existente](./media/connect-excel/existing-connection.png)

## <a name="next-steps"></a>Passos seguintes

* Saiba como [conectar e consultar o SQL Server Management Studio](connect-query-ssms.md) para consultas e análises avançadas.
* Saiba mais sobre as vantagens dos [conjuntos elásticos](elastic-pool-overview.md).
* Saiba como [criar uma aplicação web que se conecta à Base de Dados Azure SQL na parte de trás](../../app-service/app-service-web-tutorial-dotnet-sqldatabase.md).
