---
title: Importar dados do Azure Log Analytics para o Power BI Microsoft Docs
description: Power BI é um serviço de análise de negócios baseado na nuvem da Microsoft que fornece visualizações e relatórios ricos para análise de diferentes conjuntos de dados.  Este artigo descreve como configurar e importar dados do Log Analytics no Power BI e configurar-os para atualizar automaticamente.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/01/2019
ms.openlocfilehash: 8b9624313716081a039b12c73e54b9f7afc2a323
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86505317"
---
# <a name="import-azure-monitor-log-data-into-power-bi"></a>Import Azure Monitor regista dados de registo em Power BI


[Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/) é um serviço de análise de negócios baseado na nuvem da Microsoft que fornece visualizações e relatórios ricos para análise de diferentes conjuntos de dados.  Pode importar os resultados de uma consulta de registo do Azure Monitor num conjunto de dados Power BI para que possa tirar partido das suas funcionalidades, tais como combinar dados de diferentes fontes e partilhar relatórios na web e dispositivos móveis.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="overview"></a>Descrição geral
Para importar dados de um espaço de [trabalho Log Analytics](manage-access.md) em Azure Monitor para o Power BI, cria um conjunto de dados no Power BI com base numa consulta de [registo](../log-query/log-query-overview.md) no Azure Monitor.  A consulta é executada cada vez que o conjunto de dados é atualizado.  Em seguida, pode criar relatórios Power BI que utilizam dados a partir do conjunto de dados.  Para criar o conjunto de dados no Power BI, exporta a sua consulta de Log Analytics para [o idioma Power Query (M).](/powerquery-m/power-query-m-language-specification)  Em seguida, utiliza-o para criar uma consulta no Power BI Desktop e depois publicá-lo no Power BI como conjunto de dados.  Os detalhes deste processo são descritos abaixo.

![Log Analytics para Power BI](media/powerbi/overview.png)

## <a name="export-query"></a>Consulta de exportação
Comece por criar uma [consulta de registo](../log-query/log-query-overview.md) que retorne os dados que pretende preencher o conjunto de dados Power BI.  Em seguida, exporta essa consulta para a [linguagem Power Query (M)](/powerquery-m/power-query-m-language-specification) que pode ser usada pelo Power BI Desktop.

1. [Crie a consulta de registo no Log Analytics](../log-query/get-started-portal.md) para extrair os dados para o seu conjunto de dados.
2. Selecione **Export**  >  **Power BI Consulta (M)**.  Isto exporta a consulta para um ficheiro de texto chamado **PowerBIQuery.txt**. 

    ![Pesquisa de registo de exportação](media/powerbi/export-analytics.png)

3. Abra o ficheiro de texto e copie o seu conteúdo.

## <a name="import-query-into-power-bi-desktop"></a>Consulta de importação no Power BI Desktop
Power BI Desktop é uma aplicação de ambiente de trabalho que permite criar conjuntos de dados e relatórios que podem ser publicados no Power BI.  Também pode usá-lo para criar uma consulta utilizando a linguagem Power Query exportada do Azure Monitor. 

1. Instale [o Power BI Desktop](https://powerbi.microsoft.com/desktop/) se ainda não o tiver e, em seguida, abra a aplicação.
2. **Selecione Obter Consulta**Em Branco para abrir uma nova  >  **Blank Query** consulta.  Em seguida, selecione **Advanced Editor** e cole o conteúdo do ficheiro exportado para a consulta. Clique em **Done** (Concluído).

    ![Consulta de ambiente de trabalho Power BI](media/powerbi/desktop-new-query.png)

5. A consulta é e os seus resultados são apresentados.  Pode ser solicitado para que as credenciais se conectem ao Azure.  
6. Digite um nome descritivo para a consulta.  O padrão é **Query1**. Clique **em Fechar e Aplicar** para adicionar o conjunto de dados ao relatório.

    ![Nome do ambiente de trabalho Power BI](media/powerbi/desktop-results.png)



## <a name="publish-to-power-bi"></a>Publicar no Power BI
Quando publicar para o Power BI, será criado um conjunto de dados e um relatório.  Se criar um relatório no Power BI Desktop, então este será publicado com os seus dados.  Caso contrário, será criado um relatório em branco.  Pode modificar o relatório no Power BI ou criar um novo com base no conjunto de dados.

1. Crie um relatório com base nos seus dados.  Utilize [documentação de ambiente de trabalho Power BI](/power-bi/desktop-report-view) se não estiver familiarizado com ela.  
1. Quando estiver pronto para enviá-lo para o Power BI, clique em **Publicar**.  
1. Quando solicitado, selecione um destino na sua conta Power BI.  A menos que tenha um destino específico em mente, use **o meu espaço de trabalho.**

    ![Power BI Desktop publish](media/powerbi/desktop-publish.png)

1. Quando a publicação estiver concluída, clique **em Abrir no Power BI** para abrir o Power BI com o seu novo conjunto de dados.


### <a name="configure-scheduled-refresh"></a>Configurar a atualização agendada
O conjunto de dados criado no Power BI terá os mesmos dados que viu anteriormente no Power BI Desktop.  É necessário atualizar o conjunto de dados periodicamente para executar a consulta novamente e povoá-la com os dados mais recentes do Azure Monitor.  

1. Clique no espaço de trabalho onde fez o upload do seu relatório e selecione o menu **Datasets.** 
1. Selecione o menu de contexto ao lado do seu novo conjunto de dados e selecione **Definições**. 
1. Segundo **as credenciais de fonte de Dados,** deve ter uma mensagem de que as credenciais são inválidas.  Isto porque ainda não forneceu credenciais para que o conjunto de dados seja utilizado quando atualizar os seus dados.  
1. Clique **em Editar credenciais** e especifique credenciais com acesso ao espaço de trabalho Log Analytics no Azure Monitor. Se necessitar de autenticação de dois fatores, selecione **OAuth2** para que o **método de Autenticação** seja solicitado para iniciar sessão com as suas credenciais.

    ![Horário do Bi de potência](media/powerbi/powerbi-schedule.png)

5. Em **Atualização programada** ligue a opção de **manter os seus dados atualizados**.  Pode alterar opcionalmente a **frequência Refresh e** um ou mais tempos específicos para executar a atualização.

    ![Atualização de Power BI](media/powerbi/powerbi-schedule-refresh.png)



## <a name="next-steps"></a>Passos seguintes
* Saiba mais [sobre pesquisas de registo para](../log-query/log-query-overview.md) construir consultas que podem ser exportadas para o Power BI.
* Saiba mais sobre [o Power BI](https://powerbi.microsoft.com) para construir visualizações com base nas exportações de registos do Azure Monitor.
