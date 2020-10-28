---
title: Dados de fluxo usando a integração Azure Stream Analytics (pré-visualização)
description: Utilize a integração Azure Stream Analytics para transmitir dados para a Base de Dados Azure SQL.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: ajetasin
ms.author: ajetasi
ms.reviewer: sstein
ms.date: 11/04/2019
ms.openlocfilehash: b796d6689db143cf59ae4ca0a180c2c7c317b7bd
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92789442"
---
# <a name="stream-data-into-azure-sql-database-using-azure-stream-analytics-integration-preview"></a>Transmitir dados para a Base de Dados Azure SQL utilizando a integração Azure Stream Analytics (pré-visualização)

Os utilizadores podem agora ingerir, processar, visualizar e analisar dados de streaming em tempo real numa tabela diretamente a partir de uma base de dados na Base de Dados Azure SQL. Fazem-no no portal Azure utilizando o [Azure Stream Analytics](../../stream-analytics/stream-analytics-introduction.md). Esta experiência permite uma grande variedade de cenários, tais como carro conectado, monitorização remota, deteção de fraudes, e muito mais. No portal Azure, pode selecionar uma fonte de eventos (Event Hub/IoT Hub), ver eventos em tempo real e selecionar uma tabela para armazenar eventos. Também pode escrever consultas de linguagem Azure Stream Analytics no portal para transformar eventos de entrada e armazená-los na tabela selecionada. Este novo ponto de entrada é além das experiências de criação e configuração que já existem no Stream Analytics. Esta experiência parte do contexto da sua base de dados, permitindo-lhe configurar rapidamente um trabalho stream Analytics e navegar perfeitamente entre a base de dados em Azure SQL Database e stream Analytics experiências.

![Fluxo de streaming analytics](./media/stream-data-stream-analytics-integration/stream-analytics-flow.png)

## <a name="key-benefits"></a>Principais vantagens

- Comutação de contexto mínimo: Pode partir de uma base de dados na Base de Dados Azure SQL no portal e começar a ingerir dados em tempo real numa tabela sem mudar para qualquer outro serviço.
- Número reduzido de etapas: O contexto da sua base de dados e tabela é utilizado para configurar um trabalho stream Analytics.
- Facilidade adicional de utilização com dados de pré-visualização: Pré-visualizar dados de entrada a partir da fonte de eventos (Event Hub/IoT Hub) no contexto da tabela selecionada

> [!IMPORTANT]
> Um trabalho do Azure Stream Analytics pode ser transmitido para Azure SQL Database, Azure SQL Managed Instance, ou Azure Synapse Analytics (anteriormente SQL Data Warehouse). Para mais informações, consulte [outputs](../../stream-analytics/stream-analytics-define-outputs.md).

## <a name="prerequisites"></a>Pré-requisitos

Para concluir os passos neste artigo, precisa dos seguintes recursos:

- Uma subscrição do Azure. Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/).
- Uma base de dados na Base de Dados Azure SQL. Para mais informações, consulte [criar uma única base de dados na Base de Dados Azure SQL](single-database-create-quickstart.md).
- Uma regra de firewall que permite ao computador ligar-se ao servidor. Para obter mais informações, consulte [Criar uma regra de firewall ao nível do servidor](firewall-create-server-level-portal-quickstart.md).

## <a name="configure-stream-analytics-integration"></a>Integração de análise de Configure Stream

1. Inicie sessão no portal do Azure.
2. Navegue para a base de dados onde pretende ingerir os seus dados de streaming. Selecione **stream analytics (pré-visualização)** .

    ![Stream Analytics](./media/stream-data-stream-analytics-integration/stream-analytics.png)

3. Para começar a ingerir os seus dados de streaming nesta base de dados, selecione **Criar** e dar um nome ao seu trabalho de streaming e, em seguida, selecione **Seguinte: Entrada** .

    ![configurar stream Analytics básicos](./media/stream-data-stream-analytics-integration/create-job.png)

4. Insira os detalhes da origem dos seus eventos e, em seguida, selecione **Seguinte: Saída** .

   - **Tipo de entrada** : Centro de Eventos/Hub IoT
   - **Inserir pseudónimo : Insira** um nome para identificar a fonte de eventos
   - **Assinatura** : Igual à subscrição da Base de Dados Azure SQL
   - **Espaço de nome do Centro de Eventos** : Nome para espaço de nome
   - **Nome do Centro de Eventos** : Nome do centro de eventos dentro do espaço de nome selecionado
   - **Nome da política do Event Hub** (Padrão para criar novo): Dar um nome de política
   - **Grupo de consumidores Event Hub** (Padrão para criar novo): Dê um nome de grupo de consumidores  

      Recomendamos que crie um grupo de consumidores e uma política para cada novo trabalho do Azure Stream Analytics que crie a partir daqui. Os grupos de consumidores permitem apenas cinco leitores simultâneos, pelo que o fornecimento de um grupo de consumidores dedicado para cada trabalho evitará quaisquer erros que possam surgir de exceder esse limite. Uma política dedicada permite-lhe rodar a sua chave ou revogar permissões sem afetar outros recursos.

     ![configurar a produção de trabalho stream Analytics](./media/stream-data-stream-analytics-integration/create-job-output.png)

5. Selecione em que tabela pretende ingerir os seus dados de streaming. Uma vez feito, **selecione Criar** .

   - **Nome de utilizador** , **Palavra-passe** : Introduza as suas credenciais para autenticação do servidor SQL. Selecione **Validar** .
   - **Tabela** : Selecione **Criar novo** ou **utilizar existente** . Neste fluxo, vamos selecionar **Criar.** Isto criará uma nova tabela quando iniciar o trabalho de stream Analytics.

     ![criar trabalho stream analytics](./media/stream-data-stream-analytics-integration/create.png)

6. Uma página de consulta abre com os seguintes detalhes:

   - A **sua Entrada** (fonte de eventos de entrada) a partir da qual irá ingerir dados  
   - A sua **saída** (tabela de saída) que irá armazenar dados transformados
   - Consulta [SAQL de](../../stream-analytics/stream-analytics-stream-analytics-query-patterns.md) amostra com declaração SELECT.
   - **Visualização da entrada** : Mostra a imagem dos dados mais recentes da fonte de eventos de entrada.
     - O tipo de serialização dos seus dados é detetado automaticamente (JSON/CSV). Pode alterá-lo manualmente também para JSON/CSV/AVRO.
     - Pode pré-visualizar dados de entrada no formato Tabela ou formato Raw.
     - Se os seus dados mostrados não estiverem atuais, **selecione Refresh** para ver os eventos mais recentes.
     - **Selecione O intervalo de tempo selecione** para testar a sua consulta contra um intervalo de tempo específico de eventos de entrada.
     - Selecione **a entrada da amostra de upload** para testar a sua consulta carregando um ficheiro JSON/CSV de amostra. Para obter mais informações sobre o teste de uma consulta SAQL, consulte [testar um trabalho Azure Stream Analytics com dados de amostras](../../stream-analytics/stream-analytics-test-query.md).

     ![consulta de teste](./media/stream-data-stream-analytics-integration/test-query.png)

   - **Resultados do teste** : Selecione **a consulta de teste** e pode ver os resultados da sua consulta de streaming

     ![resultados dos testes](./media/stream-data-stream-analytics-integration/test-results.png)

   - **Esquema de resultados do teste** : Mostra o esquema dos resultados da sua consulta de streaming após o teste. Certifique-se de que o esquema de resultados do teste coincide com o seu esquema de saída.

     ![esquema de resultados de teste](./media/stream-data-stream-analytics-integration/test-results-schema.png)

   - **Esquema de saída** : Isto contém esquema da tabela selecionada no passo 5 (novo ou existente).

      - Crie novo: Se selecionou esta opção no passo 5, ainda não verá o esquema até iniciar o trabalho de streaming. Ao criar uma nova tabela, selecione o índice de tabela apropriado. Para obter mais informações sobre a indexação da tabela, consulte [Índices Agrupados e Não Agrupados Descritos](/sql/relational-databases/indexes/clustered-and-nonclustered-indexes-described/).
      - Utilização existente: Se selecionar esta opção no passo 5, verá o esquema da tabela selecionada.

7. Depois de terminar a autoria & testar a consulta, selecione **Save consulta** . Selecione **start stream analytics para** começar a ingerir dados transformados na tabela SQL. Assim que finalizares os seguintes campos, **começa** o trabalho.
   - **Hora de início** da saída : Isto define o tempo da primeira saída do trabalho.  
     - Agora: O trabalho vai começar agora e processar novos dados de entrada.
     - Costume: O trabalho começará agora mas irá processar dados a partir de um ponto específico no tempo (que pode ser no passado ou no futuro). Para mais informações, consulte [Como iniciar um trabalho do Azure Stream Analytics](../../stream-analytics/start-job.md).
   - **Unidades de** streaming : O Azure Stream Analytics tem o preço do número de unidades de streaming necessárias para processar os dados no serviço. Para mais informações, consulte [os preços do Azure Stream Analytics](https://azure.microsoft.com/pricing/details/stream-analytics/).
   - **Tratamento de erros de dados de saída:**  
     - Reagem: Quando ocorre um erro, a Azure Stream Analytics recauchutado a escrever o evento indefinidamente até que a escrita tenha sucesso. Não há tempo para as recauchutagens. Eventualmente, todos os eventos subsequentes são bloqueados do processamento pelo evento que está a voltar a tentar. Esta opção é a política de tratamento de erros de saída por defeito.
     - Gota: O Azure Stream Analytics deixará cair qualquer evento de saída que resulte num erro de conversão de dados. Os eventos não podem ser recuperados para reprocessamento mais tarde. Todos os erros transitórios (por exemplo, erros de rede) são novamente julgados independentemente da configuração da política de manipulação de erros de saída.
   - **Definições de saída da Base de Dados SQL** : Uma opção para herdar o esquema de partição do seu passo de consulta anterior, para permitir uma topologia totalmente paralela com vários escritores à mesa. Para obter mais informações, consulte [a saída Azure Stream Analytics para a Base de Dados Azure SQL](../../stream-analytics/stream-analytics-sql-output-perf.md).
   - **Contagem máxima do lote** : O limite superior recomendado no número de registos enviados com cada transação de inserção a granel.  
    Para obter mais informações sobre o tratamento de erros de saída, consulte [as políticas de erro de saída no Azure Stream Analytics](../../stream-analytics/stream-analytics-output-error-policy.md).  

     ![começar o trabalho](./media/stream-data-stream-analytics-integration/start-job.png)

8. Assim que começar o trabalho, verá o trabalho de Running na lista, e poderá tomar as seguintes ações:
   - **Iniciar/parar o trabalho** : Se o trabalho estiver a funcionar, pode parar o trabalho. Se o trabalho for interrompido, pode começar o trabalho.
   - **Tarefa de edição** : Pode editar a consulta. Se quiser fazer mais alterações ao trabalho ex, adicione mais entradas/saídas e, em seguida, abra o trabalho no Stream Analytics. A opção de edição é desativada quando o trabalho está em execução.
   - **Tabela de saída de pré-visualização** : Pode visualizar a tabela no editor de consulta SQL.
   - **Aberto em Stream Analytics** : Abra o trabalho no Stream Analytics para visualizar a monitorização, depurando detalhes do trabalho.

     ![stream de trabalhos de análise](./media/stream-data-stream-analytics-integration/jobs.png)

## <a name="next-steps"></a>Passos seguintes

- [Documentação do Azure Stream Analytics](../../stream-analytics/index.yml)
- [Padrões da solução Azure Stream Analytics](../../stream-analytics/stream-analytics-solution-patterns.md)
