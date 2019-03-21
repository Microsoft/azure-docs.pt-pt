---
title: Visualizar dados do SQL Data Warehouse com o Power BI Microsoft Azure
description: Visualizar dados do SQL Data Warehouse com o Power BI
services: sql-data-warehouse
author: KavithaJonnakuti
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: consume
ms.date: 04/17/2018
ms.author: kavithaj
ms.reviewer: igorstan
ms.openlocfilehash: b2377466ba830eed9fed0e9a4033b3eca3d351c4
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57834630"
---
# <a name="visualize-data-with-power-bi"></a>Visualizar dados com o Power BI
Este tutorial mostra como utilizar o Power BI para ligar ao SQL Data Warehouse e criar algumas visualizações básicas.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Data-Warehouse-Sample-Data-and-PowerBI/player]
> 
> 

## <a name="prerequisites"></a>Pré-requisitos
Para seguir este tutorial, é necessário:

* Um SQL Data Warehouse pré-carregado com a base de dados da AdventureWorksDW. Para aprovisionar um armazém de dados, consulte [criar um SQL Data Warehouse](create-data-warehouse-portal.md) e opte por carregar os dados de exemplo. Se já tiver um armazém de dados, mas não tem dados de exemplo, pode [carregar WideWorldImportersDW](load-data-wideworldimportersdw.md).

## <a name="1-connect-to-your-database"></a>1. Ligar à base de dados
Para abrir o Power BI e ligar à base de dados AdventureWorksDW:

1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).
2. Clique em **Bases de dados SQL** e selecione a sua base de dados AdventureWorks do SQL Data Warehouse.
   
    ![Localizar a base de dados](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-find-database.png)
3. Clique no botão “Abrir no Power BI”.
   
    ![Botão do Power BI](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-button.png)
4. Deverá agora ver a página de ligação do SQL Data Warehouse, apresentando o seu endereço Web da base de dados. Clique em Seguinte.
   
    ![Ligação ao Power BI](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-connect-to-azure.png)
5. Introduza o nome de utilizador de servidor de SQL do Azure e a palavra-passe.
   
    ![Power BI início de sessão](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-sign-in.png)
6. Para abrir a base de dados, clique o conjunto de dados AdventureWorksDW no painel esquerdo.
   
    ![No Power BI, abrir AdventureWorksDW](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-open-adventureworks.png)

## <a name="2-create-a-report"></a>2. Criar um relatório
Está agora pronto para utilizar o Power BI para analisar os dados de exemplo da AdventureWorksDW. Para efetuar a análise, a AdventureWorksDW tem uma vista denominada AggregateSales. Esta vista contém algumas das métricas principais para analisar as vendas da empresa.

1. Para criar um mapa do montante de vendas, de acordo com o código postal, no painel direito de campos, clique na vista AggregateSales para expandi-la. Clique nas colunas PostalCode e SalesAmount para as selecionar.
   
    ![O Power BI seleciona AggregateSales](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-aggregatesales.png)
   
    Power BI é automaticamente reconhecida dados geográficos e colocá-lo num mapa para si.
   
    ![Mapa do Power BI](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-map.png)

2. Este passo cria um gráfico de barras que mostra a quantidade de vendas por receitas de cliente. Para criar o gráfico de barras, vá para a vista AggregateSales expandida. Clique no campo SalesAmount. Arraste o campo Receitas de Cliente para a esquerda e solte-o no Eixo.
   
    ![Power BI seleciona o eixo](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-chooseaxis.png)
   
    O gráfico de barras ao longo do lado esquerdo.
   
    ![Barra do Power BI](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-bar.png)
3. Este passo cria um gráfico de linhas que mostra o montante de vendas por data de encomenda. Para criar o gráfico de linhas, vá para a vista AggregateSales expandida. Clique em SalesAmount e OrderDate. Na coluna visualizações, clique no ícone de gráfico de linhas, o que é o primeiro ícone na segunda linha em visualizações.
   
    ![Power BI seleciona o gráfico de linhas](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-prepare-line.png)
   
    Tem agora um relatório que mostra as três visualizações diferentes dos dados.
   
    ![Linha do Power BI](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-line.png)

Pode guardar o seu progresso em qualquer altura, clicando em **Ficheiro** e selecionando **Guardar**.

## <a name="using-direct-connect"></a>Utilizar o Direct Connect
Como com a base de dados SQL do Azure, SQL Data Warehouse direto ligar permite lógica propagação juntamente com as capacidades de análises do Power BI. Com ligar em direto, consultas são enviadas para o Azure SQL Data Warehouse em tempo real à medida que explora os dados.  Esta funcionalidade, combinada com a escala do SQL Data Warehouse, permite-lhe criar relatórios dinâmicos em minutos referentes a terabytes de dados. Além disso, a introdução do abrir no botão do Power BI permite que os utilizadores liguem diretamente o Power BI ao seu SQL Data Warehouse sem a recolher informações de outras partes do Azure.

Ao utilizar a ligação direta:

* Especifique o nome de servidor completamente qualificado quando ligar.
* Certifique-se de que as regras de firewall da base de dados estão configuradas para permitir acesso aos serviços do Azure.
* Cada ação, como selecionar uma coluna ou adicionar um filtro, consulta diretamente o armazém de dados.
* Os mosaicos são atualizados automaticamente e aproximadamente a cada 15 minutos.
* As perguntas e respostas não estão disponível para estabelecer a ligação direta a conjuntos de dados.
* Alterações de esquema são incorporadas automaticamente.
* Todas as consultas de ligação direta expiram depois de 2 minutos.

Estas restrições e notas podem ser alterados como melhorar as experiências.

## <a name="next-steps"></a>Passos Seguintes
Agora que lhe demos algum tempo para entender os dados de exemplo, consulte como [programar](sql-data-warehouse-overview-develop.md), [carregar](design-elt-data-loading.md), ou [migrar](sql-data-warehouse-overview-migrate.md). Em alternativa, consulte o [site do Power BI](https://www.powerbi.com/).
