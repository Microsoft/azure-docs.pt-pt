---
title: Dados de fluxo usando a integração de Stream Analytics do Azure SQL Database (versão prévia)
description: Use Azure Stream Analytics para transmitir dados para um banco de dado SQL do Azure.
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
ms.openlocfilehash: fb889f14c3370e1297f98110903c64e93e09c946
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73687074"
---
# <a name="stream-data-by-using-azure-sql-database-stream-analytics-integration-preview"></a>Dados de fluxo usando a integração de Stream Analytics do Azure SQL Database (versão prévia)

Agora, os usuários podem ingerir, processar, exibir e analisar dados de streaming em tempo real em uma tabela diretamente de um SQL Database no portal do Azure usando [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md). Essa experiência permite uma ampla variedade de cenários, como carro conectado, monitoramento remoto, detecção de fraudes e muito mais. No portal do Azure, você pode selecionar uma origem de eventos (Hub de eventos/Hub IoT), exibir eventos de entrada em tempo real e selecionar uma tabela para armazenar eventos. Você também pode escrever Stream Analytics consultas de linguagem de consulta no portal para transformar os eventos de entrada e armazená-los na tabela selecionada. Esse novo ponto de entrada é além das experiências de criação e configuração que já existem no Stream Analytics. Essa experiência começa no contexto do banco de dados, permitindo que você configure rapidamente um trabalho Stream Analytics e navegue diretamente entre o banco de dados SQL do Azure e as experiências de Stream Analytics.

![Fluxo de Stream Analytics](media/sql-database-stream-analytics/stream-analytics-flow.png)

## <a name="key-benefits"></a>Principais vantagens

- Alternância de contexto mínima: você pode iniciar a partir de um banco de dados SQL no portal e começar a ingerir os dados em tempo real em uma tabela sem alternar para nenhum outro serviço. 
- Número reduzido de etapas: o contexto do banco de dados e da tabela é usado para pré-configurar um trabalho de Stream Analytics.
- Facilidade de uso adicional com dados de visualização: Visualizar dados de entrada da origem de eventos (Hub de eventos/Hub IoT) no contexto da tabela selecionada 


## <a name="prerequisites"></a>Pré-requisitos

Para concluir as etapas neste artigo, você precisa dos seguintes recursos:

- Uma subscrição do Azure. Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/). 
- Um banco de dados SQL. Para obter detalhes, consulte [criar um banco de dados individual no banco de dados SQL do Azure](sql-database-single-database-get-started.md).
- Uma regra de firewall que permite que o computador se conecte ao SQL Server do Azure. Para obter detalhes, consulte [criar uma regra de firewall no nível de servidor](sql-database-server-level-firewall-rule.md).


## <a name="configure-stream-analytics-integration"></a>Configurar a integração do Stream Analytics

1. Inicie sessão no Portal do Azure. 
2. Navegue até o banco de dados SQL no qual você deseja ingerir seus fluxos de transmissão. Selecione **Stream Analytics (versão prévia)** . 

    ![Stream Analytics](media/sql-database-stream-analytics/stream-analytics.png)

3. Para começar a ingerir seus dados de streaming para este banco de dado SQL, selecione **criar** e dê um nome ao seu trabalho de streaming e, em seguida, selecione **Avançar: entrada**. 

    ![criar Stream Analytics trabalho](media/sql-database-stream-analytics/create-job.png)

4. Insira os detalhes da origem dos eventos e, em seguida, selecione **Avançar: saída**.

   - **Tipo de entrada**: Hub de eventos/Hub IOT
   - **Alias de entrada**: Insira um nome para identificar a origem dos eventos 
   - **Assinatura**: igual à assinatura do banco de dados SQL 
   - **Namespace do hub de eventos**: nome do namespace 
   - **Nome do hub de eventos**: nome do hub de eventos dentro do namespace selecionado 
   - **Nome da política do hub de eventos** (padrão para criar novo): forneça um nome de política 
   - **Grupo de consumidores do hub de eventos** (padrão para criar novo): forneça um nome de grupo de consumidores  
     - Recomendamos que você crie um grupo de consumidores e uma política para cada novo trabalho de Azure Stream Analytics que você criar aqui. Os grupos de consumidores permitem apenas cinco leitores simultâneos, portanto, fornecer um grupo de consumidores dedicado para cada trabalho evitará erros que possam surgir de exceder esse limite. Uma política dedicada permite que você gire sua chave ou REVOKE permissões sem afetar outros recursos.

    ![criar Stream Analytics trabalho](media/sql-database-stream-analytics/create-job-output.png)

5. Selecione em qual tabela você deseja ingerir seus dados de streaming. Depois de terminar, selecione **criar**.
   - **Nome de usuário**, **senha**: Insira suas credenciais para autenticação do SQL Server. Selecione **Validar**.
   - **Tabela**: selecione **criar novo** ou **usar existente**. Nesse fluxo, vamos selecionar **criar**. Isso criará uma nova tabela quando você iniciar o trabalho do Stream Analytics.

    ![criar Stream Analytics trabalho](media/sql-database-stream-analytics/create.png)

6. Uma página de consulta é aberta com os seguintes detalhes:

   - Sua **entrada** (fonte de eventos de entrada) da qual você irá ingerir dados  
   - Sua **saída** (tabela de saída) que armazenará os dados transformados 
   - Exemplo de [consulta SAQL](../stream-analytics/stream-analytics-stream-analytics-query-patterns.md) com instrução SELECT. 
   - **Visualização de entrada**: mostra o instantâneo dos dados de entrada mais recentes da fonte de eventos de entrada.  
     - O tipo de serialização em seus dados é detectado automaticamente (JSON/CSV). Você pode alterá-lo manualmente também para JSON/CSV/AVRO. 
     - Você pode visualizar os dados de entrada no formato de tabela ou no formato bruto. 
     - Se os dados mostrados não estiverem atuais, selecione **Atualizar** para ver os eventos mais recentes. 
     - Selecione **selecionar intervalo de tempo** para testar sua consulta em um intervalo de tempo específico de eventos de entrada. 
     - Selecione **carregar entrada de exemplo** para testar sua consulta carregando um arquivo JSON/CSV de exemplo. Para obter mais informações sobre como testar uma consulta SAQL, consulte [testar um trabalho de Azure Stream Analytics com dados de exemplo](../stream-analytics/stream-analytics-test-query.md). 

    ![consulta de teste](media/sql-database-stream-analytics/test-query.png)


   - **Resultados de teste**: selecione **testar consulta** e você pode ver os resultados da sua consulta de streaming 

    ![resultados do teste](media/sql-database-stream-analytics/test-results.png)

   - **Esquema de resultados de teste**: mostra o esquema dos resultados de sua consulta de streaming após o teste. Verifique se o esquema de resultados de teste corresponde ao seu esquema de saída. 

    ![esquema de resultados de teste](media/sql-database-stream-analytics/test-results-schema.png)


   - **Esquema de saída**: isso contém o esquema da tabela que você selecionou na etapa 5 (nova ou existente).
     - Criar novo: se você selecionou essa opção na etapa 5, você não verá o esquema ainda até iniciar o trabalho de streaming. Ao criar uma nova tabela, selecione o índice de tabela apropriado. Para obter mais informações sobre a indexação de tabela, consulte [índices clusterizados e não clusterizados descritos](/sql/relational-databases/indexes/clustered-and-nonclustered-indexes-described/).
    - Usar existente: se você selecionou essa opção na etapa 5, verá o esquema da tabela selecionada. 
 
7. Depois de concluir a criação & testando a consulta, selecione **Salvar consulta**. Selecione **iniciar Stream Analytics trabalho** para começar a ingerir dados transformados na tabela SQL. Depois de finalizar os campos a seguir, **inicie** o trabalho. 
   - **Hora de início da saída**: define a hora da primeira saída do trabalho.  
     - Agora: o trabalho será iniciado agora e processará novos dados de entrada.
     - Personalizado: o trabalho será iniciado agora, mas processará dados de um ponto específico no tempo (que pode estar no passado ou no futuro). Para obter mais informações, consulte [como iniciar um trabalho de Azure Stream Analytics](../stream-analytics/start-job.md).
   - **Unidades de streaming**: o Azure Stream Analytics é cobrado pelo número de unidades de streaming necessárias para processar os dados no serviço. Para obter mais informações, consulte [preços de Azure Stream Analytics](https://azure.microsoft.com/pricing/details/stream-analytics/). 
   - **Tratamento de erro de dados de saída**:  
     - Tentar novamente: quando ocorrer um erro, Azure Stream Analytics tentará gravar o evento indefinidamente até que a gravação seja realizada com sucesso. Não há tempo limite para novas tentativas. Eventualmente, todos os eventos subsequentes são impedidos de serem processados pelo evento que está tentando novamente. Essa opção é a política de tratamento de erro de saída padrão. 
     - Drop: Azure Stream Analytics removerá qualquer evento de saída que resulte em um erro de conversão de dados. Os eventos ignorados não podem ser recuperados para reprocessamento mais tarde. Todos os erros transitórios (por exemplo, erros de rede) são repetidos independentemente da configuração de política de tratamento de erro de saída. 
   - **Configurações de saída do banco de dados SQL**: uma opção para herdar o esquema de particionamento da etapa de consulta anterior, para habilitar a topologia totalmente paralela com vários gravadores na tabela. Para obter mais informações, consulte [Azure Stream Analytics saída para o banco de dados SQL do Azure](../stream-analytics/stream-analytics-sql-output-perf.md).
   - **Contagem máxima de lotes**: o limite superior recomendado para o número de registros enviados com cada transação de inserção em massa.  
    Para obter mais informações sobre o tratamento de erros de saída, consulte [políticas de erro de saída no Azure Stream Analytics](../stream-analytics/stream-analytics-output-error-policy.md).  

    ![Iniciar trabalho](media/sql-database-stream-analytics/start-job.png)

8. Depois de iniciar o trabalho, você verá o trabalho em execução na lista e poderá executar as seguintes ações: 
   - **Iniciar/parar o trabalho**: se o trabalho estiver em execução, você poderá interromper o trabalho. Se o trabalho for interrompido, você poderá iniciar o trabalho. 
   - **Editar trabalho**: você pode editar a consulta. Se você quiser fazer mais alterações no trabalho, por exemplo, adicione mais entradas/saídas e, em seguida, abra o trabalho no Stream Analytics. A opção Editar é desabilitada quando o trabalho está em execução. 
   - **Visualizar tabela de saída**: você pode visualizar a tabela no editor de consultas SQL. 
   - **Abrir no Stream Analytics**: Abra o trabalho no serviço Stream Analytics para exibir o monitoramento, os detalhes de depuração do trabalho. 


    ![trabalhos do Stream Analytics](media/sql-database-stream-analytics/jobs.png)






## <a name="next-steps"></a>Passos seguintes

- [Documentação do Azure Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/)
- [Padrões de solução de Azure Stream Analytics](../stream-analytics/stream-analytics-solution-patterns.md)
