---
title: Visualizar dados com power BI Microsoft Azure
description: Visualizar dados do SQL Data Warehouse com o Power BI
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/17/2018
ms.author: martinle
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 7e764eeab6f681d90e1a602f02cdb03330d6fd3d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80350953"
---
# <a name="visualize-data-with-power-bi"></a>Visualizar dados com o Power BI
Este tutorial mostra como utilizar o Power BI para ligar ao SQL Data Warehouse e criar algumas visualizações básicas.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Data-Warehouse-Sample-Data-and-PowerBI/player]
> 
> 

## <a name="prerequisites"></a>Pré-requisitos
Para seguir este tutorial, é necessário:

* Um SQL Data Warehouse pré-carregado com a base de dados da AdventureWorksDW. Para fornecer um armazém de dados, consulte Criar um Armazém de [Dados SQL](create-data-warehouse-portal.md) e optar por carregar os dados da amostra. Se já tem um armazém de dados mas não tem dados de amostra, pode [carregar o WideWorldImportersDW](load-data-wideworldimportersdw.md).

## <a name="1-connect-to-your-database"></a>1. Ligue-se à sua base de dados
Para abrir o Power BI e ligar à base de dados AdventureWorksDW:

1. Assine no [portal Azure.](https://portal.azure.com/)
2. Clique em **Bases de dados SQL** e selecione a sua base de dados AdventureWorks do SQL Data Warehouse.
   
    ![Localizar a base de dados](./media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-find-database.png)
3. Clique no botão “Abrir no Power BI”.
   
    ![Botão do Power BI](./media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-button.png)
4. Deverá agora ver a página de ligação do SQL Data Warehouse, apresentando o seu endereço Web da base de dados. Clique em Seguinte.
   
    ![Ligação ao Power BI](./media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-connect-to-azure.png)
5. Introduza o nome de utilizador e senha do seu servidor Azure SQL.
   
    ![Power BI sign-in](./media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-sign-in.png)
6. Para abrir a base de dados, clique no conjunto de dados AdventureWorksDW na lâmina esquerda.
   
    ![No Power BI, abrir AdventureWorksDW](./media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-open-adventureworks.png)

## <a name="2-create-a-report"></a>2. Criar um relatório
Está agora pronto para utilizar o Power BI para analisar os dados de exemplo da AdventureWorksDW. Para efetuar a análise, a AdventureWorksDW tem uma vista denominada AggregateSales. Esta vista contém algumas das métricas principais para analisar as vendas da empresa.

1. Para criar um mapa do montante de vendas, de acordo com o código postal, no painel direito de campos, clique na vista AggregateSales para expandi-la. Clique nas colunas PostalCode e SalesAmount para as selecionar.
   
    ![Power BI seleciona AgregadoSales](./media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-aggregatesales.png)
   
    O Power BI reconheceu automaticamente os dados geográficos e colocou-os num mapa para si.
   
    ![Mapa do Power BI](./media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-map.png)

2. Este passo cria um gráfico de barras que mostra a quantidade de vendas por receitas de cliente. Para criar o gráfico de barras, vá à vista Agregada Sales expandida. Clique no campo SalesAmount. Arraste o campo Receitas de Cliente para a esquerda e solte-o no Eixo.
   
    ![Power BI seleciona eixo](./media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-chooseaxis.png)
   
    O mapa do bar sobre a esquerda.
   
    ![Barra do Power BI](./media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-bar.png)
3. Este passo cria um gráfico de linhas que mostra o montante de vendas por data de encomenda. Para criar o gráfico de linha, vá à vista Agregada Sales expandida. Clique em SalesAmount e OrderDate. Na coluna Visualizações, clique no ícone do Line Chart, que é o primeiro ícone da segunda linha sob visualizações.
   
    ![Power BI seleciona gráfico de linha](./media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-prepare-line.png)
   
    Tem agora um relatório que mostra as três visualizações diferentes dos dados.
   
    ![Linha do Power BI](./media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-line.png)

Pode guardar o seu progresso em qualquer altura, clicando em **Ficheiro** e selecionando **Guardar**.

## <a name="using-direct-connect"></a>Usando a Ligação Direta
Tal como acontece com a Base de Dados Azure SQL, o SQL Data Warehouse Direct Connect permite uma redução lógica ao lado das capacidades analíticas do Power BI. Com o Direct Connect, as consultas são enviadas de volta para o seu Armazém de Dados Azure SQL em tempo real enquanto explora os dados.  Esta funcionalidade, combinada com a escala do SQL Data Warehouse, permite-lhe criar relatórios dinâmicos em minutos contra terabytes de dados. Além disso, a introdução do botão Open in Power BI permite que os utilizadores liguem diretamente o Power BI ao seu Armazém de Dados SQL sem recolher informações de outras partes do Azure.

Ao utilizar a Ligação Direta:

* Especifique o nome do servidor totalmente qualificado ao ligar.
* Certifique-se de que as regras de firewall para a base de dados estão configuradas para permitir o acesso aos serviços Azure.
* Todas as ações, tais como selecionar uma coluna, ou adicionar um filtro, questiona diretamente o armazém de dados.
* Os azulejos são renovados automaticamente e aproximadamente a cada 15 minutos.
* Q&A não está disponível para conjuntos de dados de Ligação Direta.
* As alterações de esquema são incorporadas automaticamente.
* Todas as consultas de Ligação Direta terão tempo de paragem após 2 minutos.

Estas restrições e notas podem mudar à medida que as experiências melhoram.

## <a name="next-steps"></a>Passos seguintes
Agora que lhe demos algum tempo para aquecer com os dados da amostra, ver como [desenvolver](sql-data-warehouse-overview-develop.md) ou [carregar.](design-elt-data-loading.md) Em alternativa, consulte o [site do Power BI](https://www.powerbi.com/).
