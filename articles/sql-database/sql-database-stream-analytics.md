---
title: Dados de streaming utilizando a integração do Stream Analytics (pré-visualização)
description: Utilize o Azure Stream Analytics para transmitir dados numa base de dados Azure SQL.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: ajetasin
ms.author: ajetasi
ms.reviewer: sstein
ms.date: 11/04/2019
ms.openlocfilehash: d233d3c98cc495e4b9e84142781f5eb9faa7eec8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "73820831"
---
# <a name="stream-data-by-using-azure-sql-database-stream-analytics-integration-preview"></a>Transmita dados utilizando a integração do Stream Analytics da Base de Dados De Base de Dados Azure SQL (pré-visualização)

Os utilizadores podem agora ingerir, processar, visualizar e analisar dados de streaming em tempo real numa tabela diretamente a partir de uma base de dados SQL no portal Azure utilizando o [Azure Stream Analytics.](../stream-analytics/stream-analytics-introduction.md) Esta experiência permite uma grande variedade de cenários como carro conectado, monitorização remota, deteção de fraudes, e muito mais. No portal Azure, pode selecionar uma fonte de eventos (Event Hub/IoT Hub), ver eventos em tempo real e selecionar uma tabela para armazenar eventos. Também pode escrever consultas de linguagem de consulta de análise stream no portal para transformar eventos de entrada e armazená-los na tabela selecionada. Este novo ponto de entrada é além das experiências de criação e configuração que já existem no Stream Analytics. Esta experiência começa a partir do contexto da sua base de dados, permitindo-lhe configurar rapidamente um trabalho de Stream Analytics e navegar perfeitamente entre as experiências azure SQL Database e Stream Analytics.

![Fluxo de Análise de Fluxo](media/sql-database-stream-analytics/stream-analytics-flow.png)

## <a name="key-benefits"></a>Principais vantagens

- Comutação mínima de contexto: Pode começar a partir de uma Base de Dados SQL no portal e começar a ingerir dados em tempo real numa tabela sem mudar para qualquer outro serviço. 
- Número reduzido de etapas: O contexto da sua base de dados e tabela é utilizado para pré-configurar um trabalho de Stream Analytics.
- Facilidade adicional de utilização com dados de pré-visualização: Pré-visualização dos dados de entrada da fonte de eventos (Event Hub/IoT Hub) no contexto da tabela selecionada 


## <a name="prerequisites"></a>Pré-requisitos

Para concluir os passos neste artigo, precisa dos seguintes recursos:

- Uma subscrição do Azure. Se não tiver uma subscrição Azure, [crie uma conta gratuita.](https://azure.microsoft.com/free/) 
- Uma base de dados SQL. Para mais detalhes, consulte Criar uma única base de dados na Base de [Dados Azure SQL](sql-database-single-database-get-started.md).
- Uma regra de firewall que permite que o seu computador se conectem ao servidor Azure SQL. Para mais detalhes, consulte [Criar uma regra de firewall ao nível do servidor](sql-database-server-level-firewall-rule.md).


## <a name="configure-stream-analytics-integration"></a>Configure integração de análise de stream

1. Inicie sessão no Portal do Azure. 
2. Navegue para a sua base de dados SQL onde pretende ingerir os seus dados de streaming. Selecione **análise de fluxo (pré-visualização)**. 

    ![Stream Analytics](media/sql-database-stream-analytics/stream-analytics.png)

3. Para começar a ingerir os seus dados de streaming nesta base de dados SQL, selecione **Criar** e dar um nome ao seu trabalho de streaming e, em seguida, selecione **Next: Input**. 

    ![criar trabalho de Analytics Stream](media/sql-database-stream-analytics/create-job.png)

4. Introduza os detalhes de origem dos seus eventos e, em seguida, selecione **Seguinte: Saída**.

   - **Tipo de entrada**: Hub de evento/Hub IoT
   - **Pseudónimo de entrada**: Insira um nome para identificar a origem dos seus eventos 
   - **Subscrição**: O mesmo que a subscrição da Base de Dados SQL 
   - **Espaço de nome do Hub de Eventos**: Nome para espaço de nome 
   - **Nome do Hub**do Evento : Nome do centro de eventos dentro do espaço de nome selecionado 
   - **Nome** da política do Event Hub (Padrão para criar novo): Dar um nome de política 
   - **Grupo de consumidores Do Event Hub** (Padrão para criar novo): Dar nome a um grupo de consumidores  
     - Recomendamos que crie um grupo de consumidores e uma política para cada novo trabalho da Azure Stream Analytics que cria a partir daqui. Os grupos de consumidores permitem apenas cinco leitores simultâneos, pelo que a disponibilização de um grupo de consumidores dedicado para cada trabalho evitará quaisquer erros que possam resultar de exceder esse limite. Uma política dedicada permite-lhe rodar a sua chave ou revogar permissões sem afetar outros recursos.

    ![criar trabalho de Analytics Stream](media/sql-database-stream-analytics/create-job-output.png)

5. Selecione em que tabela pretende ingerir os seus dados de streaming. Uma vez feito, selecione **Criar**.
   - **Nome de utilizador**, **Palavra-passe**: Introduza as suas credenciais para autenticação do servidor SQL. Selecione **Validar**.
   - **Tabela**: **Selecione Criar novo** ou utilizar a **existência**. Neste fluxo, vamos selecionar **Criar**. Isto criará uma nova tabela quando iniciar o trabalho de streaming Analytics.

    ![criar trabalho de Analytics Stream](media/sql-database-stream-analytics/create.png)

6. Uma página de consulta abre com os seguintes detalhes:

   - A sua **entrada** (fonte de eventos de entrada) a partir da qual irá ingerir dados  
   - A sua **Saída** (tabela de saída) que armazenará dados transformados 
   - Prove [a consulta SAQL](../stream-analytics/stream-analytics-stream-analytics-query-patterns.md) com a declaração SELECT. 
   - **Pré-visualização de entrada**: Mostra instantâneo dos dados mais recentes da origem dos eventos de entrada.  
     - O tipo de serialização dos seus dados é automaticamente detetado (JSON/CSV). Pode mudá-lo manualmente também para JSON/CSV/AVRO. 
     - Pode visualizar os dados de entrada no formato Tabela ou no formato Raw. 
     - Se os seus dados mostrados não estiverem atuais, selecione **Refresh** para ver os eventos mais recentes. 
     - **Selecione selecione intervalo** de tempo para testar a sua consulta com um intervalo de tempo específico de eventos de entrada. 
     - Selecione **a entrada da amostra de upload** para testar a sua consulta, carregando um ficheiro JSON/CSV da amostra. Para obter mais informações sobre o teste de uma consulta SAQL, consulte Test um trabalho de [Análise de Fluxo De Azure com dados de amostra](../stream-analytics/stream-analytics-test-query.md). 

    ![consulta de teste](media/sql-database-stream-analytics/test-query.png)


   - **Resultados dos testes**: Selecione **a consulta** de teste e poderá ver os resultados da sua consulta de streaming 

    ![resultados dos testes](media/sql-database-stream-analytics/test-results.png)

   - **Esquema de resultados**de teste : Mostra o esquema dos resultados da sua consulta de streaming após os testes. Certifique-se de que os resultados dos testes coincidem com o esquema de saída. 

    ![esquema de resultados de teste](media/sql-database-stream-analytics/test-results-schema.png)


   - **Esquema de saída**: Isto contém esquema da tabela selecionada no passo 5 (novo ou existente).
     - Criar novo: Se selecionar esta opção no passo 5, não verá o esquema ainda até iniciar o trabalho de streaming. Ao criar uma nova tabela, selecione o índice de tabela apropriado. Para obter mais informações sobre a indexação da tabela, consulte [índices agrupados e não agrupados descritos](/sql/relational-databases/indexes/clustered-and-nonclustered-indexes-described/).
    - Utilizar a existente: Se selecionar esta opção no passo 5, verá o esquema da tabela selecionada. 
 
7. Depois de terminar a autoria & testar a consulta, selecione **Guardar consulta**. Selecione **o trabalho start Stream Analytics** para começar a ingerir dados transformados na tabela SQL. Assim que finalizar os seguintes campos, **comece** o trabalho. 
   - **Início**da saída : Isto define o tempo da primeira saída do trabalho.  
     - Agora: O trabalho começará agora e processará novos dados de entrada.
     - Costume: O trabalho começará agora mas processará dados de um determinado ponto no tempo (que pode ser no passado ou no futuro). Para mais informações, consulte Como iniciar um trabalho de [Azure Stream Analytics](../stream-analytics/start-job.md).
   - **Unidades de streaming**: O Azure Stream Analytics tem o preço do número de unidades de streaming necessárias para processar os dados no serviço. Para mais informações, consulte os preços do [Azure Stream Analytics.](https://azure.microsoft.com/pricing/details/stream-analytics/) 
   - **Manipulação**de erros de dados de saída:  
     - Retry: Quando ocorre um erro, o Azure Stream Analytics volta a escrever o evento indefinidamente até que a escrita tenha sucesso. Não há tempo para repetições. Eventualmente, todos os eventos subsequentes são bloqueados do processamento pelo evento que está a ser retentado. Esta opção é a política de manipulação de erros de erro de saída predefinida. 
     - Drop: Azure Stream Analytics deixará cair qualquer evento de saída que resulte num erro de conversão de dados. Os eventos ignorados não podem ser recuperados para reprocessamento mais tarde. Todos os erros transitórios (por exemplo, erros de rede) são novamente experimentados independentemente da configuração da política de manipulação de erros de saída. 
   - Definições de saída de base de **dados SQL**: Uma opção para herdar o esquema de partição do seu passo de consulta anterior, para permitir topologia totalmente paralela com vários escritores à mesa. Para mais informações, consulte a saída do Azure Stream Analytics para a Base de [Dados Azure SQL](../stream-analytics/stream-analytics-sql-output-perf.md).
   - **Contagem máxima do lote**: O limite superior recomendado no número de registos enviados com cada transação de inserção a granel.  
    Para obter mais informações sobre o manuseamento de erros de saída, consulte as políticas de [erro de saída no Azure Stream Analytics](../stream-analytics/stream-analytics-output-error-policy.md).  

    ![começar o trabalho](media/sql-database-stream-analytics/start-job.png)

8. Assim que iniciar o trabalho, verá o trabalho de Corrida na lista, e poderá tomar as seguintes ações: 
   - **Iniciar/parar o trabalho**: Se o trabalho estiver a funcionar, pode parar o trabalho. Se o trabalho for interrompido, podecomeçar o trabalho. 
   - **Trabalho de edição**: Pode editar a consulta. Se quiser fazer mais alterações no ex-trabalho, adicione mais entradas/saídas e abra o trabalho no Stream Analytics. A opção de edição é desativada quando o trabalho está em execução. 
   - Tabela de **saída de pré-visualização:** Pode pré-visualizar a tabela no editor de consulta SQL. 
   - **Open in Stream Analytics**: Abra o trabalho no serviço Stream Analytics para ver monitorização, depurando detalhes do trabalho. 


    ![trabalhos de análise de fluxo](media/sql-database-stream-analytics/jobs.png)






## <a name="next-steps"></a>Passos seguintes

- [Documentação do Azure Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/)
- [Padrões da solução Azure Stream Analytics](../stream-analytics/stream-analytics-solution-patterns.md)
