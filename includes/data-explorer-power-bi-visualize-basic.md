---
author: mgblythe
ms.service: data-explorer
ms.topic: include
ms.date: 11/14/2018
ms.author: mblythe
ms.openlocfilehash: 9624856841ec7473543575c31928c6eefd1404c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "67184000"
---
Agora que tem dados no Power BI Desktop, pode criar relatórios com base nesses dados. Vai criar um relatório simples com um gráfico de colunas que mostra danos nas colheitas por estado.

1. No lado esquerdo da janela principal do Power BI, selecione a vista do relatório.

    ![Vista de relatório](media/data-explorer-power-bi-visualize-basic/report-view.png)

1. No painel **VISUALIZAÇÕES**, selecione o gráfico de colunas agrupadas.

    ![Adicionar gráfico de colunas](media/data-explorer-power-bi-visualize-basic/add-column-chart.png)

    É adicionado um gráfico em branco à tela.

    ![Gráfico em branco](media/data-explorer-power-bi-visualize-basic/blank-chart.png)

1. Na lista **FIELDS,** selecione **DamageCrops** e **State**.

    ![Selecionar campos](media/data-explorer-power-bi-visualize-basic/select-fields.png)

    Agora tem um gráfico que mostra os danos nas colheitas para as 1000 melhores filas da tabela.

    ![Danos nas culturas por estado](media/data-explorer-power-bi-visualize-basic/damage-column-chart.png)

1. Guarde o relatório.