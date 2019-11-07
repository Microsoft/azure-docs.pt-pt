---
title: Visualizar dados com Power BI Microsoft Azure
description: Visualizar dados do SQL Data Warehouse com o Power BI
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: integration
ms.date: 04/17/2018
ms.author: martinle
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: eea4e3b0b1f0e4ec3eaf3e0aba8952f6693d2921
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73685785"
---
# <a name="visualize-data-with-power-bi"></a>Visualizar dados com o Power BI
Este tutorial mostra como utilizar o Power BI para ligar ao SQL Data Warehouse e criar algumas visualizações básicas.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Data-Warehouse-Sample-Data-and-PowerBI/player]
> 
> 

## <a name="prerequisites"></a>Pré-requisitos
Para seguir este tutorial, é necessário:

* Um SQL Data Warehouse pré-carregado com a base de dados da AdventureWorksDW. Para provisionar um data warehouse, consulte [criar um SQL data warehouse](create-data-warehouse-portal.md) e optar por carregar os dados de exemplo. Se você já tiver um data warehouse mas não tiver dados de exemplo, poderá [carregar WideWorldImportersDW](load-data-wideworldimportersdw.md).

## <a name="1-connect-to-your-database"></a>1. conectar-se ao banco de dados
Para abrir o Power BI e ligar à base de dados AdventureWorksDW:

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
2. Clique em **Bases de dados SQL** e selecione a sua base de dados AdventureWorks do SQL Data Warehouse.
   
    ![Localizar a base de dados](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-find-database.png)
3. Clique no botão “Abrir no Power BI”.
   
    ![Botão do Power BI](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-button.png)
4. Deverá agora ver a página de ligação do SQL Data Warehouse, apresentando o seu endereço Web da base de dados. Clique em Seguinte.
   
    ![Ligação ao Power BI](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-connect-to-azure.png)
5. Insira o nome de usuário e a senha do SQL Server do Azure.
   
    ![Power BI entrar](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-sign-in.png)
6. Clique no conjunto de dados AdventureWorksDW na folha à esquerda para abri-lo.
   
    ![No Power BI, abrir AdventureWorksDW](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-open-adventureworks.png)

## <a name="2-create-a-report"></a>2. criar um relatório
Está agora pronto para utilizar o Power BI para analisar os dados de exemplo da AdventureWorksDW. Para efetuar a análise, a AdventureWorksDW tem uma vista denominada AggregateSales. Esta vista contém algumas das métricas principais para analisar as vendas da empresa.

1. Para criar um mapa do montante de vendas, de acordo com o código postal, no painel direito de campos, clique na vista AggregateSales para expandi-la. Clique nas colunas PostalCode e SalesAmount para as selecionar.
   
    ![Power BI seleciona AggregateSales](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-aggregatesales.png)
   
    Power BI dados geográficos reconhecidos automaticamente e colocá-los em um mapa para você.
   
    ![Mapa do Power BI](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-map.png)

2. Este passo cria um gráfico de barras que mostra a quantidade de vendas por receitas de cliente. Para criar o gráfico de barras, vá para a exibição AggregateSales expandida. Clique no campo SalesAmount. Arraste o campo Receitas de Cliente para a esquerda e solte-o no Eixo.
   
    ![Power BI seleciona eixo](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-chooseaxis.png)
   
    O gráfico de barras à esquerda.
   
    ![Barra do Power BI](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-bar.png)
3. Este passo cria um gráfico de linhas que mostra o montante de vendas por data de encomenda. Para criar o gráfico de linhas, vá para a exibição AggregateSales expandida. Clique em SalesAmount e OrderDate. Na coluna visualizações, clique no ícone de gráfico de linhas, que é o primeiro ícone na segunda linha em visualizações.
   
    ![Power BI seleciona o gráfico de linhas](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-prepare-line.png)
   
    Tem agora um relatório que mostra as três visualizações diferentes dos dados.
   
    ![Linha do Power BI](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-line.png)

Pode guardar o seu progresso em qualquer altura, clicando em **Ficheiro** e selecionando **Guardar**.

## <a name="using-direct-connect"></a>Usando a conexão direta
Assim como acontece com o banco de dados SQL do Azure, SQL Data Warehouse conexão direta permite a aplicação lógica juntamente com os recursos analíticos do Power BI. Com o Direct Connect, as consultas são enviadas de volta para o SQL Data Warehouse do Azure em tempo real à medida que você explora os dados.  Esse recurso, combinado com a escala de SQL Data Warehouse, permite que você crie relatórios dinâmicos em minutos contra terabytes de dados. Além disso, a introdução do botão abrir no Power BI permite que os usuários se conectem diretamente Power BI à sua SQL Data Warehouse sem coletar informações de outras partes do Azure.

Ao usar a conexão direta:

* Especifique o nome do servidor totalmente qualificado ao se conectar.
* Verifique se as regras de firewall do banco de dados estão configuradas para permitir o acesso aos serviços do Azure.
* Cada ação, como selecionar uma coluna ou adicionar um filtro, consulta diretamente o data warehouse.
* Os blocos são atualizados automaticamente e aproximadamente a cada 15 minutos.
* P & A não está disponível para conjuntos de valores de conexão direta.
* As alterações de esquema são incorporadas automaticamente.
* Todas as consultas de conexão direta atingirão o tempo limite após 2 minutos.

Essas restrições e observações podem mudar conforme as experiências melhoram.

## <a name="next-steps"></a>Passos seguintes
Agora que lhe proporcionamos algum tempo para ficarmos com os dados de exemplo, Confira como [desenvolver](sql-data-warehouse-overview-develop.md) ou [carregar](design-elt-data-loading.md). Em alternativa, consulte o [site do Power BI](https://www.powerbi.com/).
