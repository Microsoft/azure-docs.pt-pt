---
title: Importar dados do Azure Log Analytics para o Power BI | Microsoft Docs
description: Power BI é um serviço de análise de negócios baseado em nuvem da Microsoft que fornece visualizações e relatórios avançados para análise de diferentes conjuntos de dados.  Este artigo descreve como configurar e importar Log Analytics dados no Power BI e configurá-los para serem atualizados automaticamente.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/01/2019
ms.openlocfilehash: 62a010480dc83561a11c6ee99c76f35b29e808c1
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/25/2019
ms.locfileid: "72932120"
---
# <a name="import-azure-monitor-log-data-into-power-bi"></a>Importar Azure Monitor dados de log para Power BI


[Power bi](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/) é um serviço de análise de negócios baseado em nuvem da Microsoft que fornece visualizações e relatórios avançados para análise de diferentes conjuntos de dados.  Você pode importar os resultados de uma Azure Monitor consulta de log em um conjunto de dados Power BI para que você possa aproveitar seus recursos, como combinar dados de fontes diferentes e compartilhar relatórios na Web e em dispositivos móveis.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="overview"></a>Visão geral
Para importar dados de um [espaço de trabalho log Analytics](manage-access.md) no Azure Monitor para Power bi, você cria um conjunto de dados no Power bi com base em uma [consulta de log](../log-query/log-query-overview.md) no Azure monitor.  A consulta é executada cada vez que o conjunto de um é atualizado.  Em seguida, você pode criar Power BI relatórios que usam dados do DataSet.  Para criar o conjunto de Power BI, você exporta sua consulta de Log Analytics para o [idioma Power Query (M)](https://docs.microsoft.com/powerquery-m/power-query-m-language-specification).  Em seguida, você usa isso para criar uma consulta no Power BI Desktop e, em seguida, publicá-la em Power BI como um conjunto de uma.  Os detalhes desse processo são descritos abaixo.

![Log Analytics Power BI](media/powerbi/overview.png)

## <a name="export-query"></a>Exportar consulta
Comece criando uma [consulta de log](../log-query/log-query-overview.md) que retorne os dados que você deseja que preencham o Power bi DataSet.  Em seguida, exporte essa consulta para [Power Query (M) linguagem](https://docs.microsoft.com/powerquery-m/power-query-m-language-specification) que pode ser usada pelo Power bi desktop.

1. [Crie a consulta de log no log Analytics](../log-query/get-started-portal.md) para extrair os dados para seu conjunto.
2. Selecione **exportar** > **Power bi consulta (M)** .  Isso exporta a consulta para um arquivo de texto chamado **PowerBIQuery. txt**. 

    ![Exportar pesquisa de logs](media/powerbi/export-analytics.png)

3. Abra o arquivo de texto e copie seu conteúdo.

## <a name="import-query-into-power-bi-desktop"></a>Importar consulta para Power BI Desktop
Power BI Desktop é um aplicativo de área de trabalho que permite que você crie conjuntos de e relatórios que podem ser publicados em Power BI.  Você também pode usá-lo para criar uma consulta usando o idioma Power Query exportado do Azure Monitor. 

1. Instale [Power bi desktop](https://powerbi.microsoft.com/desktop/) se você ainda não o tiver e, em seguida, abra o aplicativo.
2. Selecione **obter dados** > **consulta em branco** para abrir uma nova consulta.  Em seguida, selecione **Editor avançado** e cole o conteúdo do arquivo exportado na consulta. Clique em **Concluído**.

    ![Consulta de Power BI Desktop](media/powerbi/desktop-new-query.png)

5. A consulta é executada e seus resultados são exibidos.  Você pode ser solicitado a fornecer credenciais para se conectar ao Azure.  
6. Digite um nome descritivo para a consulta.  O padrão é **query1**. Clique em **fechar e aplicar** para adicionar o conjunto de um ao relatório.

    ![Nome do Power BI Desktop](media/powerbi/desktop-results.png)



## <a name="publish-to-power-bi"></a>Publicar no Power BI
Quando você publicar no Power BI, um conjunto de um DataSet e um relatório serão criados.  Se você criar um relatório no Power BI Desktop, isso será publicado com seus dados.  Caso contrário, um relatório em branco será criado.  Você pode modificar o relatório em Power BI ou criar um novo com base no conjunto de um.

1. Crie um relatório com base em seus dados.  Use a [documentação Power bi desktop](https://docs.microsoft.com/power-bi/desktop-report-view) se você não estiver familiarizado com ela.  
1. Quando estiver pronto para enviá-lo para Power BI, clique em **publicar**.  
1. Quando solicitado, selecione um destino em sua conta de Power BI.  A menos que você tenha um destino específico em mente, use **meu espaço de trabalho**.

    ![Power BI Desktop publicar](media/powerbi/desktop-publish.png)

1. Quando a publicação for concluída, clique em **abrir no Power bi** para abrir Power bi com seu novo conjunto de novos.


### <a name="configure-scheduled-refresh"></a>Configurar a atualização agendada
O conjunto de dados criado no Power BI terá os mesmos dados que você viu anteriormente em Power BI Desktop.  Você precisa atualizar o conjunto de dados periodicamente para executar a consulta novamente e preenchê-la com os dados mais recentes de Azure Monitor.  

1. Clique no espaço de trabalho onde você carregou o relatório e selecione o menu **conjuntos** de itens. 
1. Selecione o menu de contexto ao lado do novo conjunto de e selecione **configurações**. 
1. Em **credenciais da fonte de dados** , você deve ter uma mensagem informando que as credenciais são inválidas.  Isso ocorre porque você ainda não forneceu credenciais para o conjunto de dados a ser usado quando ele atualiza os seus dados.  
1. Clique em **Editar credenciais** e especifique as credenciais com acesso ao espaço de trabalho Log Analytics no Azure monitor. Se você precisar de autenticação de dois fatores, selecione **OAuth2** para que o **método de autenticação** seja solicitado a fazer logon com suas credenciais.

    ![Agenda de Power BI](media/powerbi/powerbi-schedule.png)

5. Em **atualização agendada** , ative a opção para **manter seus dados atualizados**.  Opcionalmente, você pode alterar a **frequência de atualização** e uma ou mais horas específicas para executar a atualização.

    ![Power BI atualizar](media/powerbi/powerbi-schedule-refresh.png)



## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre [pesquisas de log](../log-query/log-query-overview.md) para criar consultas que podem ser exportadas para Power bi.
* Saiba mais sobre [Power bi](https://powerbi.microsoft.com) criar visualizações com base em exportações de log Azure monitor.