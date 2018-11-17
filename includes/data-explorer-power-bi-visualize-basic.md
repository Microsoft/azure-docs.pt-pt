---
author: mgblythe
ms.service: data-explorer
ms.topic: include
ms.date: 11/14/2018
ms.author: mblythe
ms.openlocfilehash: 9624856841ec7473543575c31928c6eefd1404c1
ms.sourcegitcommit: 7804131dbe9599f7f7afa59cacc2babd19e1e4b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2018
ms.locfileid: "51854756"
---
Agora que tem dados no Power BI Desktop, pode criar relatórios com base nesses dados. Criará um relatório simples com um gráfico de colunas que mostra recortar danos por Estado.

1. No lado esquerdo da janela principal do Power BI, selecione a vista de relatório.

    ![Vista de relatório](media/data-explorer-power-bi-visualize-basic/report-view.png)

1. No painel **VISUALIZAÇÕES**, selecione o gráfico de colunas agrupadas.

    ![Adicionar gráfico de colunas](media/data-explorer-power-bi-visualize-basic/add-column-chart.png)

    É adicionado um gráfico em branco à tela.

    ![Gráfico em branco](media/data-explorer-power-bi-visualize-basic/blank-chart.png)

1. Na **campos** lista, selecione **DamageCrops** e **estado**.

    ![Selecionar campos](media/data-explorer-power-bi-visualize-basic/select-fields.png)

    Tem agora um gráfico que mostra o dano colheitas para as primeiras 1000 linhas na tabela.

    ![Danos de recorte por Estado](media/data-explorer-power-bi-visualize-basic/damage-column-chart.png)

1. Guarde o relatório.