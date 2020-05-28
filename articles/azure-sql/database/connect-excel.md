---
title: Ligar com o Excel
description: Saiba como ligar o Microsoft Excel à Base de Dados SQL Azure. Importe dados para o Excel para criação de relatórios e exploração de dados.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: joseidz
ms.author: craigg
ms.reviewer: ''
ms.date: 02/12/2019
ms.openlocfilehash: 08bf78e6cc548ed717d6bc57946067a16ec13e2b
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84043451"
---
# <a name="connect-excel-to-azure-sql-database-and-create-a-report"></a>Ligue excel à Base de Dados SQL Azure e crie um relatório
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Ligue o Excel à Base de Dados SQL Azure e importe dados e crie tabelas e gráficos com base em valores na base de dados. Neste tutorial, irá configurar a ligação entre o Excel e uma tabela de base de dados, guardar o ficheiro que armazena os dados e as informações de ligação para o Excel e, em seguida, criar um gráfico dinâmico a partir dos valores da base de dados.

Vai precisar de uma base de dados SQL antes de começar. Se não tiver uma, consulte Criar uma Base de [Dados SQL Azure](single-database-create-quickstart.md) e criar uma [firewall IP ao nível do servidor](firewall-create-server-level-portal-quickstart.md) para obter uma Base de Dados SQL com dados de amostra saindo e funcionando em poucos minutos.

Neste artigo, você vai importar dados de amostra para Excel a partir desse artigo, mas você pode seguir passos semelhantes com os seus próprios dados.

Irá também precisar de uma cópia do Excel. Este artigo utiliza o [Microsoft Excel 2016](https://products.office.com/).

## <a name="connect-excel-to-a-sql-database-and-load-data"></a>Ligue excel a uma base de dados SQL e carregue dados

1. Para ligar o Excel a uma Base de Dados SQL, abra o Excel e, em seguida, crie um novo livro ou abra um livro excel existente.
2. Na barra de menus na parte superior da página, selecione o separador **Dados,** selecione **Obter Dados,** selecione From Azure e, em seguida, selecione **From Azure SQL Database**.

   ![Selecione fonte de dados: Ligue excel à Base de Dados SQL.](./media/connect-excel/excel_data_source.png)

3. Na caixa de diálogo de base de **dados do Servidor SQL,** escreva o **nome servidor** a que pretende ligar no formulário <nome de *servidor* > **.database.windows.net**. Por exemplo, **msftestserver.database.windows.net.** Facultais e insira no nome da sua base de dados. Selecione **OK** para abrir a janela de credenciais.

   ![Ligar à caixa de diálogo do servidor de base de dados](./media/connect-excel/server-name.png)

4. Na caixa de diálogo de base de **dados do Servidor SQL,** selecione **Base de Dados** no lado esquerdo e, em seguida, introduza no seu Nome de **Utilizador** e **Palavra-passe** para o servidor a que pretende ligar. Selecione **Ligar** para abrir o **Navigator**.

   ![Escrever o nome do servidor e as credenciais de início de sessão](./media/connect-excel/connect-to-server.png)

   > [!TIP]
   > Dependendo do ambiente de rede, poderá não conseguir ligar ou perder a ligação se o servidor não permitir o tráfego a partir do endereço IP do seu cliente. Aceda ao [portal do Azure](https://portal.azure.com/), clique em servidores SQL, clique no servidor, clique em firewall em definições e adicione o seu endereço IP de cliente. Consulte [Como configurar as definições da firewall](firewall-configure.md) para detalhes.

5. No **Navegador**, selecione a base de dados com a que pretende trabalhar a partir da lista, selecione as tabelas ou vistas com as quais pretende trabalhar (escolhemos **vGetAllCategories),** e, em seguida, selecione **Load** para mover os dados da sua base de dados para a sua folha de cálculo Excel.

    ![Selecione uma base de dados e a tabela.](./media/connect-excel/select-database-and-table.png)

## <a name="import-the-data-into-excel-and-create-a-pivot-chart"></a>Importar os dados para o Excel e criar um gráfico dinâmico

Agora que estabeleceu a ligação, tem várias opções diferentes sobre como carregar os dados. Por exemplo, os seguintes passos criam um gráfico de pivô com base nos dados encontrados na sua Base de Dados SQL.

1. Siga os passos na secção anterior, mas desta vez, em vez de selecionar **load,** selecione **Load para** a partir da gota de **carga.**
2. Em seguida, selecione como pretende ver estes dados no seu livro. Vamos escolher **PivotChart**. Também pode optar por criar uma **Nova folha de cálculo** ou **Adicionar estes dados ao Modelo de Dados**. Para mais informações sobre Modelos de Dados, consulte [Criar um modelo de dados no Excel](https://support.office.com/article/Create-a-Data-Model-in-Excel-87E7A54C-87DC-488E-9410-5C75DBCB0F7B).

    ![Escolher o formato para dados no Excel](./media/connect-excel/import-data.png)

    A folha de cálculo tem agora um gráfico e uma tabela dinâmicos em branco.
3. Em **Campos PivotTable**, selecione todas as caixas de verificação para os campos que pretende visualizar.

    ![Configure o relatório de base de dados.](./media/connect-excel/power-pivot-results.png)

> [!TIP]
> Se pretender ligar outros livros e folhas de cálculo do Excel à base de dados, selecione o separador **Dados** e selecione **Fontes Recentes** para lançar a caixa de diálogo **Fontes Recentes.** A partir daí, escolha a ligação que criou a partir da lista e, em seguida, clique em **Open**.
> ![Caixa de diálogo fontes recentes](./media/connect-excel/recent-connections.png)

## <a name="create-a-permanent-connection-using-odc-file"></a>Criar uma ligação permanente utilizando ficheiro .odc

Para guardar os dados de ligação permanentemente, pode criar um ficheiro .odc e tornar esta ligação uma opção selecionável dentro da caixa de diálogo **Conexões Existentes.**

1. Na barra de menus na parte superior da página, selecione o separador **Dados** e, em seguida, selecione **Ligações Existentes** para lançar a caixa de diálogo **conexões existentes.**
   1. **Selecione Procurar mais para** abrir a caixa de diálogo Select Data **Source.**
   2. Selecione o ficheiro **+NewSqlServerConnection.odc** e, em seguida, **selecione Abrir** para abrir o **Assistente de Ligação**de Dados .

      ![Nova caixa de diálogo de ligação](./media/connect-excel/new-connection.png)

2. No **Assistente de Ligação**de Dados, escreva o nome do seu servidor e as credenciais de Base de Dados SQL. Selecione **Seguinte**.
   1. Selecione a base de dados que contém os seus dados a partir da queda.
   2. Selecione a tabela ou veja que está interessado. Escolhemos vGetAllCategories.
   3. Selecione **Seguinte**.

      ![Assistente de ligação de dados](./media/connect-excel/data-connection-wizard.png)

3. Selecione a localização do seu ficheiro, o Nome do **Ficheiro**e o **Nome Amigável** no próximo ecrã do Assistente de Ligação de Dados. Também pode optar por guardar a palavra-passe no ficheiro, embora isso possa potencialmente expor os seus dados a acesso indesejado. Selecione **Terminar** quando estiver pronto.

    ![Salvar a ligação de dados](./media/connect-excel/save-data-connection.png)

4. Selecione como pretende importar os seus dados. Escolhemos fazer uma Mesa Pivot. Também pode modificar as propriedades da ligação por **propriedades**selecionadas . Selecione **OK** quando estiver pronto. Se não optou por guardar a palavra-passe com o ficheiro, então será-lhe solicitado que introduza as suas credenciais.

    ![Importar Dados](./media/connect-excel/import-data2.png)

5. Verifique se a sua nova ligação foi guardada expandindo o separador **Dados** e selecionando **as Ligações Existentes**.

    ![Conexão existente](./media/connect-excel/existing-connection.png)

## <a name="next-steps"></a>Próximos passos

* Saiba como [Ligar a Base de Dados SQL com o SQL Server Management Studio](connect-query-ssms.md) para consulta e análise avançadas.
* Saiba mais sobre as vantagens dos [conjuntos elásticos](elastic-pool-overview.md).
* Saiba como [criar uma aplicação Web que estabelece ligação à Base de Dados SQL no back-end](../../app-service/app-service-web-tutorial-dotnet-sqldatabase.md).
