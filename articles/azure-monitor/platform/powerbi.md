---
title: Importar dados do Azure Log Analytics no Power BI | Documentos da Microsoft
description: Power BI é um serviço de análise de negócios com base na cloud da Microsoft que fornece relatórios e visualizações avançadas para análise de diferentes conjuntos de dados.  Este artigo descreve como configurar e importar dados do Log Analytics no Power BI e configurá-lo para atualizar automaticamente.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: 83edc411-6886-4de1-aadd-33982147b9c3
ms.service: log-analytics
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/19/2018
ms.author: bwren
ms.openlocfilehash: 53e24a6874a1e43b0de07893a6ace3a44b81d373
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58110176"
---
# <a name="import-azure-monitor-log-data-into-power-bi"></a>Importar dados de registo do Azure Monitor para o Power BI


[Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/) é um serviço de análise de negócios com base na cloud da Microsoft que fornece relatórios e visualizações avançadas para análise de diferentes conjuntos de dados.  Pode importar os resultados de uma consulta de registo do Azure Monitor para um conjunto de dados do Power BI, para que possa tirar partido das respetivas funcionalidades, como combinar dados de diferentes origens e partilhar relatórios na web e em dispositivos móveis.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="overview"></a>Descrição geral
Para importar dados a partir de um [área de trabalho do Log Analytics](manage-access.md) do Azure Monitor para o Power BI, vai criar um conjunto de dados no Power BI com base num [consulta de registo](../log-query/log-query-overview.md) no Azure Monitor.  A consulta é executada sempre que o conjunto de dados é atualizado.  Em seguida, pode criar relatórios do Power BI que utilizam dados do conjunto de dados.  Para criar o conjunto de dados no Power BI, exportar a sua consulta do Log Analytics para [linguagem do Power Query (M)](https://msdn.microsoft.com/library/mt807488.aspx).  Em seguida, utilize esta opção para criar uma consulta no Power BI Desktop e, em seguida, publique-o no Power BI como um conjunto de dados.  Os detalhes deste processo são descritos abaixo.

![O log Analytics para o Power BI](media/powerbi/overview.png)

## <a name="export-query"></a>Consulta de exportação
Comece por criar um [consulta de registo](../log-query/log-query-overview.md) que devolve os dados que deseja preencher o conjunto de dados do Power BI.  Em seguida, exportar essa consulta para [linguagem do Power Query (M)](https://msdn.microsoft.com/library/mt807488.aspx) que podem ser utilizadas pelo Power BI Desktop.

1. [Criar a consulta de registo no Log Analytics](../log-query/get-started-portal.md) para extrair os dados para o conjunto de dados.
2. Selecione **exportar** > **do Power BI consulta (M)**.  Isto exporta a consulta para um ficheiro de texto chamado **PowerBIQuery.txt**. 

    ![Pesquisa de registos de exportação](media/powerbi/export-analytics.png)

3. Abra o ficheiro de texto e copie os conteúdos.

## <a name="import-query-into-power-bi-desktop"></a>Importar consulta para o Power BI Desktop
O ambiente de trabalho do Power BI é um aplicativo de desktop permite-lhe criar conjuntos de dados e relatórios que podem ser publicados para o Power BI.  Também pode utilizar para criar uma consulta usando a linguagem do Power Query exportada a partir do Azure Monitor. 

1. Instale [Power BI Desktop](https://powerbi.microsoft.com/desktop/) se não o tiver e, em seguida, abra a aplicação.
2. Selecione **obter dados** > **consulta em branco** para abrir uma nova consulta.  Em seguida, selecione **Editor avançado** e cole o conteúdo do ficheiro exportado para a consulta. Clique em **Concluído**.

    ![Consulta do Power BI Desktop](media/powerbi/desktop-new-query.png)

5. A consulta é executada, e seus resultados são apresentados.  Poderá ser-lhe pedido para as credenciais para ligar ao Azure.  
6. Escreva um nome descritivo para a consulta.  A predefinição é **Consulta1**. Clique em **fechar e aplicar** para adicionar o conjunto de dados ao relatório.

    ![Nome do Power BI Desktop](media/powerbi/desktop-results.png)



## <a name="publish-to-power-bi"></a>Publicar no Power BI
Ao publicar no Power BI, um conjunto de dados e um relatório serão criados.  Se criar um relatório no Power BI Desktop, em seguida, isso será publicado com os seus dados.  Caso contrário, em seguida, será criado um relatório em branco.  Pode modificar o relatório no Power BI ou crie um novo com base no conjunto de dados.

1. Crie um relatório com base nos seus dados.  Uso [documentação do Power BI Desktop](https://docs.microsoft.com/power-bi/desktop-report-view) se não estiver familiarizado com ele.  Quando estiver pronto para enviar para o Power BI, clique em **publicar**.  Quando lhe for pedido, selecione um destino na sua conta do Power BI.  A menos que tenha um destino específico em mente, utilize **minha área de trabalho**.

    ![Publicar do Power BI Desktop](media/powerbi/desktop-publish.png)

1. Quando a publicação estiver concluída, clique em **abrir no Power BI** para abrir o Power BI com o novo conjunto de dados.


### <a name="configure-scheduled-refresh"></a>Configurar a atualização agendada
O conjunto de dados criado no Power BI terão os mesmos dados que viu anteriormente no Power BI Desktop.  Terá de atualizar o conjunto de dados periodicamente para executar novamente a consulta e preenchê-lo com os dados mais recentes do Azure Monitor.  

1. Clique na área de trabalho em que carregou o relatório e selecione o **conjuntos de dados** menu. Selecione o menu de contexto junto ao seu novo conjunto de dados e selecione **definições**. Sob **credenciais da origem de dados** deve ter uma mensagem que as credenciais são inválidas.  Isso é porque ainda não forneceu credenciais ainda para o conjunto de dados a utilizar quando ele atualiza os seus dados.  Clique em **editar credenciais** e especifique as credenciais com acesso à área de trabalho do Log Analytics no Azure Monitor.

    ![Agenda do Power BI](media/powerbi/powerbi-schedule.png)

5. Sob **atualização agendada** ative a opção para **manter os seus dados atualizados**.  Opcionalmente, pode alterar o **frequência de atualização** e uma ou mais vezes específicos para executar a atualização.

    ![Atualização do Power BI](media/powerbi/powerbi-schedule-refresh.png)



## <a name="next-steps"></a>Passos Seguintes
* Saiba mais sobre [pesquisas de registos](../log-query/log-query-overview.md) para criar consultas que podem ser exportadas para o Power BI.
* Saiba mais sobre [Power BI](https://powerbi.microsoft.com) para criar visualizações com base em exportações de registo do Azure Monitor.