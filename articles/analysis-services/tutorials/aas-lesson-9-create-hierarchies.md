---
title: 'O Azure Analysis Services lição 9 do tutorial: Criar hierarquias | Documentos da Microsoft'
description: Descreve como criar hierarquias num modelo de tabela.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 5eb80051052138924cdb30655609215974435839
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/23/2019
ms.locfileid: "56729921"
---
# <a name="create-hierarchies"></a>Criar hierarquias

Nesta lição, irá criar hierarquias. Hierarquias são grupos de colunas organizados em níveis. Por exemplo, uma hierarquia de Geografia pode ter subníveis para país, estado, município e cidade. As hierarquias podem aparecer separadas de outras colunas numa lista de campos de aplicações cliente relatório, tornando mais fácil para os utilizadores clientes navegarem e incluírem num relatório. Para obter mais informações, consulte [Hierarquias](https://docs.microsoft.com/sql/analysis-services/tabular-models/hierarchies-ssas-tabular)
  
Para criar hierarquias, use o designer de modelo da *vista de diagrama*. A criação e a gestão de hierarquias não é suportada na vista de dados.  
  
Tempo estimado para concluir esta lição: **20 minutos**  
  
## <a name="prerequisites"></a>Pré-requisitos  
Este tópico faz parte de um tutorial de modelação em tabela que deve ser concluído por ordem. Antes de executar as tarefas nesta lição, deverá ter concluído a lição anterior: [Lição 8: Criar perspetivas](../tutorials/aas-lesson-8-create-perspectives.md).  
  
## <a name="create-hierarchies"></a>Criar hierarquias  
  
#### <a name="to-create-a-category-hierarchy-in-the-dimproduct-table"></a>Para criar uma hierarquia de categoria na tabela DimProduct  
  
1.  No designer de modelo (vista de diagrama), clique com botão direito do rato na tabela **DimProduct** > **Criar hierarquia**. Uma nova hierarquia aparece na parte inferior da janela da tabela. Mude o nome da hierarquia **Categoria**.  
  
2.  Clique e arraste a coluna **ProductCategoryName** para a nova hierarquia **Categoria**.  
  
3.  Na hierarquia **Categoria**, clique com o botão direito do rato em **ProductCategoryName** > **Mudar nome** e, em seguida, digite **Categoria**.  
  
    > [!NOTE]  
    > Mudar o nome de uma coluna numa hierarquia não muda o nome dessa coluna na tabela. Uma coluna numa hierarquia é apenas uma representação da coluna na tabela.  
  
4.  Clique e arraste a coluna **ProductCategoryName** para a nova hierarquia **Categoria**. Mude o nome para **Subcategoria**. 
  
5.  Clique com botão direito do rato na coluna **ModelName** > **Adicionar à hierarquia** e, em seguida, selecione **Categoria**. Mude o nome para **Modelo**.

6.  Finalmente, adicione **EnglishProductName** à hierarquia Categoria. Mude o nome para **Produto**.  

    ![aas-lesson9-category](../tutorials/media/aas-lesson9-category.png)
  
#### <a name="to-create-hierarchies-in-the-dimdate-table"></a>Para criar hierarquias na tabela DimDate  
  
1.  Na tabela **DimDate**, crie uma hierarquia com o nome **Calendário**.  
  
3.  Adicione as seguintes colunas pela ordem:

    *  CalendarYear
    *  CalendarSemester
    *  CalendarQuarter
    *  MonthCalendar
    *  DayNumberOfMonth
    
4.  Na tabela **DimDate**, crie uma hierarquia com o nome **Fiscal**. Inclua as seguintes colunas pela ordem:  
  
    *  FiscalYear
    *  FiscalSemester
    *  FiscalQuarter
    *  MonthCalendar
    *  DayNumberOfMonth
  
5.  Por fim, na tabela **DimDate**, crie uma hierarquia **ProductionCalendar**. Inclua as seguintes colunas pela ordem:  
    *  CalendarYear
    *  WeekNumberOfYear
    *  DayNumberOfWeek
  
## <a name="whats-next"></a>Passos seguintes?
[Lição 10: Criar partições](../tutorials/aas-lesson-10-create-partitions.md). 
  
  
