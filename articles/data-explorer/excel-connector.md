---
title: Visualizar dados utilizando o conector Azure Data Explorer para o Microsoft Excel
description: Neste artigo, aprende-se a utilizar o conector Excel para o Azure Data Explorer.
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 08/30/2019
ms.openlocfilehash: 42f52581d8f2f80deb5d6250ed54ab64fc1ba4d3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74849059"
---
# <a name="visualize-data-using-the-azure-data-explorer-connector-for-excel"></a>Visualizar dados utilizando o conector Azure Data Explorer para excel

O Azure Data Explorer fornece duas opções para a ligação aos dados do Excel: utilize o conector nativo ou importe uma consulta do Azure Data Explorer. Este artigo mostra-lhe como usar o conector nativo no Excel e ligar-se ao cluster Azure Data Explorer para obter e visualizar dados.

O conector nativo do Azure Data Explorer Excel oferece a capacidade de exportar resultados de consulta para o Excel. Também pode adicionar uma consulta KQL como fonte de dados do Excel para cálculos ou visualizações adicionais.

## <a name="define-kusto-query-as-an-excel-data-source-and-load-the-data-to-excel"></a>Defina a consulta de Kusto como uma fonte de dados do Excel e carregue os dados para o Excel

1. Abra o **Microsoft Excel.**
1. No separador **Dados,** selecione **Obter dados** > **do Azure** > **From Azure Data Explorer**.

    ![Obtenha dados do Azure Data Explorer](media/excel-connector/get-data-from-adx.png)

1. Na janela **Azure Data Explorer (Kusto),** complete os seguintes campos e selecione **OK**.

    ![Janela Azure Data Explorer (Kusto)](media/excel-connector/adx-connection-window.png)
    
    |Campo   |Descrição |
    |---------|---------|
    |**Aglomerado**   |   Nome do cluster (obrigatório)      |    
    |**Base de Dados**     |    Nome da base de dados      |    
    |**Nome de mesa ou consulta do Explorador de Dados Azure**    |     Nome da tabela ou consulta do Explorador de Dados Azure    | 
    
    **Opções Avançadas:**

     |Campo   |Descrição |
    |---------|---------|
    |**Limitar o número recorde de resultados da consulta**     |     Limite o número de registos carregados em excel  |    
    |**Limitar o tamanho dos dados dos resultados da consulta (bytes)**    |    Limite o tamanho dos dados      |   
    |**Desativar a truncação do conjunto de resultados**    |         |      
    |**Declarações adicionais de conjunto (separadas por pontos evípis)**    |    Adicionar `set` declarações a aplicar à fonte de dados     |   

1.  No painel **do Navegador,** navegue para a mesa correta. No painel de pré-visualização da tabela, selecione **Transform Data** para fazer alterações nos seus dados ou selecione **Carregar** para carregá-lo para Excel.

![Janela de pré-visualização de mesa](media/excel-connector/navigate-table-preview-window.png)

   > [!TIP]
   > Se a base de **dados** e/ou o nome da tabela ou a consulta do Explorador de **Dados Azure** já estiverem especificadas, o painel de pré-visualização da tabela correta abrir-se-á automaticamente. 

## <a name="analyze-and-visualize-data-in-excel"></a>Analisar e visualizar dados no Excel

Uma vez que os dados se destaquem e estão disponíveis na sua folha excel, pode analisar, resumir e visualizar os dados criando relacionamentos e visuais. 

1.  No separador Design de **Tabela,** **selecione Resumo com Tabela Dinâmica**. Na janela **Create PivotTable,** selecione a tabela relevante e selecione **OK**.

    ![Criar mesa de pivô](media/excel-connector/create-pivot-table.png)

1. No painel **De Campos PivotTable,** selecione as colunas de mesa relevantes para criar tabelas sumárias. No exemplo abaixo, **o EventId** e o **Estado** são selecionados.
    
    ![Selecione campos de mesa-de-eixo](media/excel-connector/pivot-table-pick-fields.png)

1. No separador **PivotTable Analyze,** selecione **PivotChart** para criar visuais baseados na tabela. 

    ![Gráfico de pivô](media/excel-connector/pivot-table-analyze-pivotchart.png)

1. No exemplo abaixo, utilize **o Event Id**, **StartTime**e **EventType** para ver informações adicionais sobre os eventos meteorológicos.

    ![Visualizar os dados](media/excel-connector/visualize-excel-data.png)

1. Crie dashboards completos para monitorizar os seus dados.

## <a name="next-steps"></a>Passos seguintes

[Visualizar dados utilizando uma consulta do Azure Data Explorer Kusto importada para o Microsoft Excel](excel-blank-query.md)