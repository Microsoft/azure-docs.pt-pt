---
title: Visualizar dados usando o conector de Data Explorer do Azure para o Microsoft Excel
description: Neste artigo, você aprenderá a usar o conector do Excel para o Azure Data Explorer.
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 08/30/2019
ms.openlocfilehash: 18c6499afe450a0387b0d9f0f13ee4378e0a21a9
ms.sourcegitcommit: ee61ec9b09c8c87e7dfc72ef47175d934e6019cc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/30/2019
ms.locfileid: "70173836"
---
# <a name="visualize-data-using-the-azure-data-explorer-connector-for-excel"></a>Visualizar dados usando o conector do Data Explorer do Azure para Excel

O Azure Data Explorer fornece duas opções para se conectar a dados no Excel: Use o conector nativo ou importe uma consulta do Data Explorer do Azure. Este artigo mostra como usar o conector nativo no Excel e conectar-se ao cluster de Data Explorer do Azure para obter e Visualizar dados.

O conector nativo do Azure Data Explorer Excel oferece a capacidade de exportar os resultados da consulta para o Excel. Você também pode adicionar uma consulta KQL como uma fonte de dados do Excel para cálculos ou visualizações adicionais.

## <a name="define-kusto-query-as-an-excel-data-source-and-load-the-data-to-excel"></a>Definir a consulta Kusto como uma fonte de dados do Excel e carregar os dados para o Excel

1. Abra **o Microsoft Excel**.
1. Na guia **dados** , selecione **obter dados** > **do Azure** > **do Azure data Explorer**.

    ![Obter dados do Azure Data Explorer](media/excel-connector/get-data-from-adx.png)

1. Na janela **Data Explorer do Azure (Kusto)** , preencha os campos a seguir e selecione **OK**.

    ![Janela do Data Explorer do Azure (Kusto)](media/excel-connector/adx-connection-window.png)
    
    |Campo   |Descrição |
    |---------|---------|
    |**Em**   |   Nome do cluster (obrigatório)      |    
    |**Base de Dados**     |    Nome da base de dados      |    
    |**Nome da tabela ou consulta do Azure Data Explorer**    |     Nome da tabela ou consulta de Data Explorer do Azure    | 
    
    **Opções avançadas:**

     |Campo   |Descrição |
    |---------|---------|
    |**Limitar o número de registros do resultado da consulta**     |     Limitar o número de registros carregados no Excel  |    
    |**Limitar o tamanho dos dados de resultado da consulta (bytes)**    |    Limitar o tamanho dos dados      |   
    |**Desabilitar truncamento do conjunto de resultados**    |         |      
    |**Instruções SET adicionais (separadas por ponto e vírgula)**    |    Adicionar `set` instruções para aplicar à fonte de dados     |   

1.  No painel **navegador** , navegue até a tabela correta. No painel visualização de tabela, selecione **transformar dados** para fazer alterações em seus dados ou selecione **carregar** para carregá-los no Excel.

![Janela de visualização de tabela](media/excel-connector/navigate-table-preview-window.png)

   > [!TIP]
   > Se o **banco de dados** e/ou o nome da **tabela ou a consulta do Azure data Explorer** já estiverem especificados, o painel de visualização de tabela correto será aberto automaticamente. 

## <a name="analyze-and-visualize-data-in-excel"></a>Analisar e Visualizar dados no Excel

Depois que os dados forem carregados no Excel e estiverem disponíveis na planilha do Excel, você poderá analisar, resumir e visualizar os dados criando relações e visuais. 

1.  Na guia **design da tabela** , selecione **resumir com tabela dinâmica**. Na janela **criar tabela dinâmica** , selecione a tabela relevante e selecione **OK**.

    ![Criar tabela dinâmica](media/excel-connector/create-pivot-table.png)

1. No painel **campos de tabela dinâmica** , selecione as colunas de tabela relevantes para criar tabelas de resumo. No exemplo abaixo, **EventID** e **estado** são selecionados.
    
    ![Selecionar campos de tabela dinâmica](media/excel-connector/pivot-table-pick-fields.png)

1. Na guia **analisar da tabela dinâmica** , selecione **gráfico dinâmico** para criar visuais com base na tabela. 

    ![Gráfico dinâmico](media/excel-connector/pivot-table-analyze-pivotchart.png)

1. No exemplo a seguir, use **ID de evento**, **StartTime**e **EventType** para exibir informações adicionais sobre os eventos meteorológicos.

    ![Visualizar os dados](media/excel-connector/visualize-excel-data.png)

1. Crie painéis completos para monitorar seus dados.

