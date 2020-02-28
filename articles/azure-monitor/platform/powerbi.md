---
title: Importar dados do Azure Log Analytics para o Power BI  Microsoft Docs
description: Power BI é um serviço de análise de negócios baseado em nuvem da Microsoft que fornece visualizações e relatórios ricos para análise de diferentes conjuntos de dados.  Este artigo descreve como configurar e importar dados do Log Analytics para o Power BI e configurá-lo para atualizar automaticamente.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/01/2019
ms.openlocfilehash: 8ff24d508eb35c4f2a04c7d024254fa6f1875da8
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2020
ms.locfileid: "77659290"
---
# <a name="import-azure-monitor-log-data-into-power-bi"></a>Import Azure Monitor dados de log no Power BI


[Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/) é um serviço de análise de negócios baseado em nuvem da Microsoft que fornece visualizações e relatórios ricos para análise de diferentes conjuntos de dados.  Pode importar os resultados de uma consulta de log Do Monitor Azure num conjunto de dados power BI para que possa tirar partido das suas funcionalidades, tais como combinar dados de diferentes fontes e partilhar relatórios na web e dispositivos móveis.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="overview"></a>Descrição geral
Para importar dados de um espaço de [trabalho de Log Analytics](manage-access.md) no Monitor Azure para o Power BI, cria um conjunto de dados no Power BI com base numa consulta de [log](../log-query/log-query-overview.md) no Monitor Azure.  A consulta é executada cada vez que o conjunto de dados é atualizado.  Em seguida, pode construir relatórios Power BI que utilizam dados a partir do conjunto de dados.  Para criar o conjunto de dados no Power BI, exporta a sua consulta de Log Analytics para [a linguagem Power Query (M).](https://docs.microsoft.com/powerquery-m/power-query-m-language-specification)  Em seguida, utilize isto para criar uma consulta no Power BI Desktop e, em seguida, publicá-lo no Power BI como um conjunto de dados.  Os detalhes deste processo são descritos abaixo.

![Log Analytics para Power BI](media/powerbi/overview.png)

## <a name="export-query"></a>Consulta de exportação
Comece por criar uma consulta de [registo](../log-query/log-query-overview.md) que derete os dados que pretende povoar o conjunto de dados power BI.  Em seguida, exporta essa consulta para a [linguagem Power Query (M)](https://docs.microsoft.com/powerquery-m/power-query-m-language-specification) que pode ser usada pelo Power BI Desktop.

1. [Crie a consulta](../log-query/get-started-portal.md) de log no Log Analytics para extrair os dados para o seu conjunto de dados.
2. Selecione **Export > ** **Power BI Query (M)** .  Isto exporta a consulta para um ficheiro de texto chamado **PowerBIQuery.txt**. 

    ![Pesquisa de registo de exportação](media/powerbi/export-analytics.png)

3. Abra o ficheiro de texto e copie o seu conteúdo.

## <a name="import-query-into-power-bi-desktop"></a>Consulta de importação para Power BI Desktop
Power BI Desktop é uma aplicação de desktop que permite criar conjuntos de dados e relatórios que podem ser publicados para Power BI.  Também pode usá-lo para criar uma consulta utilizando a linguagem Power Query exportada do Monitor Azure. 

1. Instale o [Power BI Desktop](https://powerbi.microsoft.com/desktop/) se ainda não o tiver e abra a aplicação.
2. Selecione **Obter Dados** > **Consulta em branco** para abrir uma nova consulta.  Em seguida, selecione **Advanced Editor** e colhe o conteúdo do ficheiro exportado para a consulta. Clique em **Concluído**.

    ![Consulta de ambiente de trabalho power BI](media/powerbi/desktop-new-query.png)

5. A consulta é executado, e os seus resultados são apresentados.  Pode ser solicitado que as credenciais se liguem ao Azure.  
6. Digite um nome descritivo para a consulta.  O padrão é **Consulta1**. Clique em **Fechar e Aplicar** para adicionar o conjunto de dados ao relatório.

    ![Nome do ambiente de trabalho power BI](media/powerbi/desktop-results.png)



## <a name="publish-to-power-bi"></a>Publicar para Power BI
Quando publicar no Power BI, será criado um conjunto de dados e um relatório.  Se criar um relatório no Power BI Desktop, então este será publicado com os seus dados.  Caso contrário, será criado um relatório em branco.  Pode modificar o relatório no Power BI ou criar um novo com base no conjunto de dados.

1. Crie um relatório com base nos seus dados.  Utilize [a documentação do Power BI Desktop](https://docs.microsoft.com/power-bi/desktop-report-view) se não estiver familiarizado com ela.  
1. Quando estiver pronto para enviá-lo para Power BI, clique em **Publicar**.  
1. Quando solicitado, selecione um destino na sua conta Power BI.  A menos que tenha um destino específico em mente, use **o meu espaço de trabalho.**

    ![Power BI Desktop publicar](media/powerbi/desktop-publish.png)

1. Quando a publicação estiver concluída, clique **em Open in Power BI** para abrir o Power BI com o seu novo conjunto de dados.


### <a name="configure-scheduled-refresh"></a>Configurar atualização programada
O conjunto de dados criado no Power BI terá os mesmos dados que viu anteriormente no Power BI Desktop.  É necessário refrescar periodicamente o conjunto de dados para executar a consulta novamente e povoá-la com os dados mais recentes do Monitor Azure.  

1. Clique no espaço de trabalho onde fez o upload do seu relatório e selecione o menu **Datasets.** 
1. Selecione o menu de contexto ao lado do seu novo conjunto de dados e selecione **Definições**. 
1. De acordo com **as credenciais** de origem de Dados deve ter uma mensagem de que as credenciais são inválidas.  Isto porque ainda não forneceu credenciais para que o conjunto de dados seja utilizado quando atualiza os seus dados.  
1. Clique em **editar credenciais** e especificar credenciais com acesso ao espaço de trabalho log analytics no Monitor Azure. Se necessitar de autenticação de dois fatores, selecione **OAuth2** para que o método de **Autenticação** seja solicitado para iniciar sessão com as suas credenciais.

    ![Horário do POWER BI](media/powerbi/powerbi-schedule.png)

5. Em **versão de atualização programada,** ligue a opção de **manter os seus dados atualizados**.  Pode alterar opcionalmente a **frequência Refresh** e uma ou mais horas específicas para executar a atualização.

    ![Atualização de Power BI](media/powerbi/powerbi-schedule-refresh.png)



## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre pesquisas de [log](../log-query/log-query-overview.md) para construir consultas que podem ser exportadas para Power BI.
* Saiba mais sobre [o Power BI](https://powerbi.microsoft.com) para construir visualizações com base nas exportações de log do Azure Monitor.
