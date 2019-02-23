---
title: 'O Azure Analysis Services tutorial lição 10: Criar partições | Documentos da Microsoft'
description: Descreve como criar partições no projeto de tutorial do Azure Analysis Services.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 91d34065753ae29b14aad8580bf922b8213a7988
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/23/2019
ms.locfileid: "56738628"
---
# <a name="create-partitions"></a>Criar partições

Nesta lição, irá criar partições para dividir a tabela FactInternetSales em partes lógicas menores que podem ser processadas (atualizadas) independentemente de outras partições. Por predefinição, cada tabela incluída no seu modelo tem uma partição, o que inclui todas as colunas e linhas da tabela. Para a tabela FactInternetSales, queremos dividir os dados por ano; uma partição para cada um dos cinco anos da tabela. Cada partição pode então ser processada independentemente. Para obter mais informações, consulte [Partições](https://docs.microsoft.com/sql/analysis-services/tabular-models/partitions-ssas-tabular). 
  
Tempo estimado para concluir esta lição: **15 minutos**  
  
## <a name="prerequisites"></a>Pré-requisitos  
Este tópico faz parte de um tutorial de modelação em tabela que deve ser concluído por ordem. Antes de executar as tarefas nesta lição, deverá ter concluído a lição anterior: [Lição 9: Criar hierarquias](../tutorials/aas-lesson-9-create-hierarchies.md).  
  
## <a name="create-partitions"></a>Criar partições  
  
#### <a name="to-create-partitions-in-the-factinternetsales-table"></a>Para criar partições na tabela FactInternetSales  
  
1.  No Explorador de modelos tabulares, expanda **Tabelas** e, em seguida, clique com botão direito do rato em **FactInternetSales** > **Partições**.  
  
2.  No Gestor de partições, clique em **Cópia** e, em seguida, altere o nome para **FactInternetSales2010**.
  
    Uma vez que pretende que a partição inclua somente essas linhas dentro de um determinado período, para o ano 2010, deve modificar a expressão de consulta.
  
4.  Clique em **Design** para abrir o editor de consultas e, em seguida, clique na consulta **FactInternetSales2010**.

5.  Na pré-visualização, clique na seta para baixo no cabeçalho de coluna **OrderDate** e clique em **Filtros de data/hora** > **Entre**.

    ![aas-lesson10-query-editor](../tutorials/media/aas-lesson10-query-editor.png)

6.  Na caixa de diálogo Filtrar linhas, no **Mostrar linhas onde: OrderDate**, deixe **é posterior ou igual a**e, em seguida, no campo Data, digite **1/1/2010**. Deixe selecionado o operador **E**, selecione **é antes de** e no campo da data, digite **1/1/2011**e, em seguida, clique em **OK**.

    ![aas-lesson10-filter-rows](../tutorials/media/aas-lesson10-filter-rows.png)
    
    Observe no Editor de consulta, em PASSOS APLICADOS, e verá outro passo com o nome Linhas filtradas. Esse filtro seleciona apenas as datas de pedido de 2010.

8.  Clique em **Importar**.

    No Gestor de partições, observe que a expressão de consulta agora tem uma cláusula adicional sobre linhas filtradas.

    ![aas-lesson10-query](../tutorials/media/aas-lesson10-query.png)
  
    Esta instrução especifica que essa partição deve incluir somente os dados nas linhas onde OrderDate se refere ao ano 2010, conforme especificado na cláusula sobre linhas filtradas.  
  
  
#### <a name="to-create-a-partition-for-the-2011-year"></a>Para criar uma partição para o ano 2011  
  
1.  Na lista de partições, clique na partição **FactInternetSales2010** que criou e clique em **Copiar**.  Altere o nome da partição para **FactInternetSales2011**. 

    Não necessita de utilizar o Editor de consulta para criar uma nova cláusula sobre linhas filtradas. Uma vez que criou uma cópia da consulta para 2010, só tem de efetuar uma pequena alteração na consulta para 2011.
  
2.  Em **Expressão da consulta**, para que esta partição inclua apenas as linhas para o ano 2011, substitua os anos na cláusula sobre linhas filtradas por **2011** e **2012**, respetivamente, como:  
  
    ```  
    let
        Source = #"SQL/localhost;AdventureWorksDW2014",
        dbo_FactInternetSales = Source{[Schema="dbo",Item="FactInternetSales"]}[Data],
        #"Removed Columns" = Table.RemoveColumns(dbo_FactInternetSales,{"OrderDateKey", "DueDateKey", "ShipDateKey"}),
        #"Filtered Rows" = Table.SelectRows(#"Removed Columns", each [OrderDate] >= #datetime(2011, 1, 1, 0, 0, 0) and [OrderDate] < #datetime(2012, 1, 1, 0, 0, 0))
    in
        #"Filtered Rows"
   
    ```  
  
#### <a name="to-create-partitions-for-2012-2013-and-2014"></a>Para criar partições para 2012, 2013 e 2014.  
  
- Siga os passos anteriores ao criar as partições para 2012, 2013 e 2014 e alterar os anos na cláusula de linhas filtradas para incluir apenas as linhas para esse ano. 
  

## <a name="delete-the-factinternetsales-partition"></a>Eliminar a partição FactInternetSales
Agora que definiu as partições para cada ano, pode eliminar a partição FactInternetSales, evitando a sobreposição quando escolher Processar tudo no processamento das partições.

#### <a name="to-delete-the-factinternetsales-partition"></a>Para eliminar a partição FactInternetSales
-  Clique na partição FactInternetSales e, em seguida, clique em **Eliminar**.



## <a name="process-partitions"></a>Processar partições  
No Gestor de partições, observe a coluna **Última processada** para cada uma das novas partições que criou e verá as partições nunca foram processadas. Quando criar partições, deverá executar a operação Processar partições ou Processar tabelas para atualizar os dados nessas partições.  
  
#### <a name="to-process-the-factinternetsales-partitions"></a>Para processar as partições FactInternetSales  
  
1.  Clique em **OK** para fechar o Gestor de partições.  
  
2.  Clique na tabela **FactInternetSales** de tabela e, em seguida, clique no menu **Modelo** > **Processar** > **Processar partições**.  
  
3.  Na caixa de diálogo Processar partições, verifique se o **Modo** é definido para **Processar predefinições**.  
  
4.  Selecione a caixa de seleção na coluna **Processar** para cada uma das cinco partições criadas e depois clique em **OK**.  

    ![aas-lesson10-process-partitions](../tutorials/media/aas-lesson10-process-partitions.png)
  
    Se tiver de fornecer as credenciais de representação, insira o nome de utilizador e palavra-passe do Windows que especificou na lição 2.  
  
    A caixa de diálogo **Processamento de dados** é apresentada e exibe os detalhes do processo para cada partição. Observe que é transferido um número diferente de linhas para cada partição. Cada partição inclui somente as linhas para o ano especificado na cláusula WHERE na instrução SQL. Quando o processamento é concluído, avance e feche a caixa de diálogo Processamento de dados.  
  
    ![aas-lesson10-process-complete](../tutorials/media/aas-lesson10-process-complete.png)
  
## <a name="whats-next"></a>Passos seguintes?
Vá para a próxima lição: [Lição 11: Criar funções](../tutorials/aas-lesson-11-create-roles.md). 
