---
title: Análise de sentimento no Twitter em tempo real com Azure Stream Analytics
description: Este artigo descreve como usar o Stream Analytics para análise de sentimentos no Twitter em tempo real. Orientação passo a passo da geração de eventos aos dados de um dashboard ao vivo.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 02/10/2020
ms.openlocfilehash: 60fde4ca1d8aaf47367fcdb4b5dc7c73753b7496
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/25/2020
ms.locfileid: "83834769"
---
# <a name="real-time-twitter-sentiment-analysis-in-azure-stream-analytics"></a>Análise de sentimento do Twitter em tempo real no Azure Stream Analytics

Este artigo ensina-o a construir uma solução de análise de sentimentos nas redes sociais, trazendo eventos do Twitter em tempo real para o Azure Event Hubs. Escreve uma consulta do Azure Stream Analytics para analisar os dados e armazenar os resultados para posterior utilização ou criar um dashboard [Power BI](https://powerbi.com/) para fornecer informações em tempo real.

As ferramentas de análise das redes sociais ajudam as organizações a compreender os tópicos de tendência. Temas de tendência são temas e atitudes que têm um grande volume de publicações nas redes sociais. A análise do sentimento, que também é chamada de mineração de *opinião,* utiliza ferramentas de análise de redes sociais para determinar atitudes em relação a um produto ou ideia. 

A análise da tendência do Twitter em tempo real é um grande exemplo de uma ferramenta de análise porque o modelo de subscrição de hashtag permite-lhe ouvir palavras-chave específicas (hashtags) e desenvolver a análise de sentimento do feed.

## <a name="scenario-social-media-sentiment-analysis-in-real-time"></a>Cenário: Análise de sentimento das redes sociais em tempo real

Uma empresa que tem um site de media de notícias está interessada em ganhar vantagem sobre os seus concorrentes, apresentando conteúdo do site que é imediatamente relevante para os seus leitores. A empresa usa a análise das redes sociais sobre temas relevantes para os leitores fazendo análises de sentimentoem em tempo real os dados do Twitter.

Para identificar tópicos de tendência em tempo real no Twitter, a empresa precisa de análises em tempo real sobre o volume de tweet e o sentimento para tópicos-chave.

## <a name="prerequisites"></a>Pré-requisitos

Neste guia, você usa uma aplicação de cliente que se conecta ao Twitter e procura tweets que têm certas hashtags (que você pode definir). Para executar a aplicação e analisar os tweets usando o Azure Streaming Analytics, deve ter o seguinte:

* Se não tiver uma subscrição Azure, crie uma [conta gratuita.](https://azure.microsoft.com/free/)

* Uma conta [no Twitter.](https://twitter.com)

* A aplicação TwitterClientCore, que lê o feed do Twitter. Para obter esta aplicação, baixe [o TwitterClientCore](https://github.com/Azure/azure-stream-analytics/tree/master/DataGenerators/TwitterClientCore).

* Instale a versão [.NET Core CLI](https://docs.microsoft.com/dotnet/core/tools/?tabs=netcore2x) 2.1.0.

## <a name="create-an-event-hub-for-streaming-input"></a>Criar um centro de eventos para streaming de entrada

A aplicação da amostra gera eventos e empurra-os para um centro de eventos Azure. Os Hubs de Eventos Azure são o método preferido de ingestão de eventos para o Stream Analytics. Para mais informações, consulte a documentação do [Azure Event Hubs.](../event-hubs/event-hubs-what-is-event-hubs.md)

### <a name="create-an-event-hub-namespace-and-event-hub"></a>Crie um espaço de nome de centro de eventos e centro de eventos
Nesta secção, você cria um espaço de nome de evento e adiciona um hub de eventos a esse espaço de nome. Os espaços de nome soque do evento são usados para agrupar logicamente casos de ônibus relacionados com eventos. 

1. Inicie sessão no portal Azure e selecione **Criar um recurso**. E depois. procure por Centros de **Eventos** e selecione **Criar**.

2. Na página **Create Namespace,** introduza um nome de espaço de nome. Pode utilizar qualquer nome para o espaço de nome, mas o nome deve ser válido para um URL, e deve ser único em todo o Azure. 
    
3. Selecione um nível de preços e subscrição e crie ou escolha um grupo de recursos. Em seguida, escolha um local e selecione **Criar**. 
 
4. Quando o espaço de nome tiver terminado de ser implantado, navegue para o seu grupo de recursos e encontre o espaço de nome do centro de eventos na sua lista de recursos Azure. 

5. A partir do novo espaço de nome, selecione ** + &nbsp; Event Hub**. 

6. Nomeie o novo centro de *eventos socialtwitter-eh*. Pode utilizar um nome diferente. Se o fizeres, tome nota disso, porque precisas do nome mais tarde. Não precisa definir outras opções para o centro do evento.
 
7. Selecione **Criar**.

### <a name="grant-access-to-the-event-hub"></a>Conceder acesso ao centro de eventos

Antes de um processo poder enviar dados para um centro de eventos, o centro de eventos precisa de uma política que permita o acesso. A política de acesso produz uma cadeia de ligação que inclui as informações de autorização.

1.  Na barra de navegação no lado esquerdo do seu espaço de nome seletiva, selecione **Event Hubs,** que está localizado na secção **Entidades.** Em seguida, selecione o centro de eventos que acabou de criar.

2.  Na barra de navegação do lado esquerdo, selecione as políticas de **acesso partilhado** seletivas situadas em **Definições**.

    >[!NOTE]
    >Existe uma opção de políticas de acesso partilhada para o espaço de nome do centro de eventos e para o centro de eventos. Certifique-se de que está a trabalhar no contexto do seu centro de eventos, e não no espaço de nome sinuoso do evento.

3.  A partir da página de política de acesso, selecione **+ Adicionar**. Em seguida, insira o *acesso social twitter* para o nome **Política** e verifique a caixa de verificação **Gerir.**
 
4.  Selecione **Criar**.

5.  Depois de implementada a política, selecione a política da lista de políticas de acesso partilhado.

6.  Encontre a tecla primária de linha de ligação com a etiqueta de **ligação** e selecione o botão de cópia ao lado da cadeia de ligação.
 
7.  Cole a cadeia de ligação num editor de texto. Precisa desta corda de ligação para a próxima secção depois de fazer algumas pequenas edições.

   A corda de ligação é assim:
   
   ```
   Endpoint=sb://EVENTHUBS-NAMESPACE.servicebus.windows.net/;SharedAccessKeyName=socialtwitter-access;SharedAccessKey=Gw2NFZw6r...FxKbXaC2op6a0ZsPkI=;EntityPath=socialtwitter-eh
   ```

   Note que a cadeia de ligação contém múltiplos pares de valor-chave, separados com pontos evíbis: `Endpoint` `SharedAccessKeyName` , , e `SharedAccessKey` `EntityPath` .  

   > [!NOTE]
   > Para segurança, partes da corda de ligação no exemplo foram removidas.

## <a name="configure-and-start-the-twitter-client-application"></a>Configure e inicie a aplicação do cliente do Twitter

A aplicação do cliente recebe eventos de tweet diretamente do Twitter. Para isso, precisa de autorização para ligar para as APIs de streaming do Twitter. Para configurar essa permissão, cria-se uma aplicação no Twitter, que gera credenciais únicas (como um símbolo oAuth). Em seguida, pode configurar a aplicação do cliente para usar estas credenciais quando faz chamadas API. 

### <a name="create-a-twitter-application"></a>Criar uma aplicação do Twitter
Se ainda não tem uma aplicação no Twitter que pode utilizar para este guia de como orientar, pode criar uma. Já deve ter uma conta no Twitter.

> [!NOTE]
> O processo exato no Twitter para criar uma aplicação e obter as chaves, segredos e fichas pode mudar. Se estas instruções não corresponderem ao que vê no site do Twitter, consulte a documentação do desenvolvedor do Twitter.

1. A partir de um navegador web, vá ao [Twitter Para Desenvolvedores,](https://developer.twitter.com/en/apps)crie uma conta de desenvolvedore e selecione **Criar uma aplicação**. Pode ver uma mensagem a dizer que precisa de se candidatar a uma conta de desenvolvedordo no Twitter. Sinta-se à vontade para o fazer e, depois de aprovado o seu pedido, deverá consultar um e-mail de confirmação. Pode levar vários dias para ser aprovado para uma conta de desenvolvedor.

   ![Detalhes da aplicação do Twitter](./media/stream-analytics-twitter-sentiment-analysis-trends/provide-twitter-app-details.png "Detalhes da aplicação do Twitter")

2. Na página **Criar uma aplicação**, forneça os detalhes da nova aplicação e, em seguida, selecione **Criar a sua aplicação do Twitter**.

   ![Detalhes da aplicação do Twitter](./media/stream-analytics-twitter-sentiment-analysis-trends/provide-twitter-app-details-create.png "Detalhes da aplicação do Twitter")

3. Na página de aplicação, selecione o separador **Keys and Tokens** e copie os valores para **chave api** do consumidor e chave secreta do consumidor **API**Secret . Além disso, selecione **Create** under **Access Token e Access Token Secret** para gerar as fichas de acesso. Copie os valores de **Token de Acesso** e **Segredo de Token de Acesso**.

   Guarde os valores que obteve da aplicação do Twitter. Precisa dos valores mais tarde.

> [!NOTE]
> As chaves e segredos da aplicação do Twitter fornecem acesso à sua conta de Twitter. Trate esta informação como sensível, o mesmo que faz com a sua senha do Twitter. Por exemplo, não insera esta informação numa aplicação que você dá a outros. 

### <a name="configure-the-client-application"></a>Configure a aplicação do cliente

Criámos uma aplicação de clientes que se conecta aos dados do Twitter usando as [APIs de streaming do Twitter](https://dev.twitter.com/streaming/overview) para recolher eventos de tweet sobre um conjunto específico de tópicos.

Antes da aplicação ser executado, requer certas informações suas, como as chaves do Twitter e a cadeia de ligação do centro de eventos.

1. Certifique-se de que descarregou a aplicação [TwitterClientCore,](https://github.com/Azure/azure-stream-analytics/tree/master/DataGenerators/TwitterClientCore) tal como listado nos pré-requisitos.

2. Utilize um editor de texto para abrir o ficheiro *App.config.* Faça as seguintes alterações no `<appSettings>` elemento:

   * Definido `oauth_consumer_key` na Chave do Consumidor do Twitter (tecla API). 
   * Definido `oauth_consumer_secret` para o Twitter Consumer Secret (chave secreta DaPI).
   * Pronto `oauth_token` para o twitter Access token.
   * Definido `oauth_token_secret` para o twitter Access token secret.
   * Coloque `EventHubNameConnectionString` na corda de ligação.
   * Definir `EventHubName` para o nome do centro do evento (que é o valor do caminho da entidade).

3. Abra a linha de comando e navegue para o diretório onde está a sua aplicação TwitterClientCore. Use o comando `dotnet build` para construir o projeto. Em seguida, use o comando `dotnet run` para executar a aplicação. A aplicação envia Tweets para o seu Centro de Eventos.

## <a name="create-a-stream-analytics-job"></a>Criar uma tarefa do Stream Analytics

Agora que os eventos de tweet estão a ser transmitidos em tempo real a partir do Twitter, podes configurar um trabalho de Stream Analytics para analisar estes eventos em tempo real.

1. No portal Azure, navegue para o seu grupo de recursos e selecione **+ Adicionar**. Em seguida, procure o **trabalho do Stream Analytics** e selecione **Criar**.

2. Nomeie o trabalho `socialtwitter-sa-job` e especifique uma subscrição, grupo de recursos e localização.

    É uma boa ideia colocar o trabalho e o centro de eventos na mesma região para melhor desempenho e para que não pague para transferir dados entre regiões.

3. Selecione **Criar**. Em seguida, navegue para o seu trabalho quando o destacamento estiver terminado.

## <a name="specify-the-job-input"></a>Especificar a entrada de trabalho

1. No seu trabalho de Stream Analytics, selecione **Inputs** do menu esquerdo sob **Topologia de Trabalho**.

2. ** + &nbsp; Selecione Adicionar centro de**  >  **eventos**de entrada de fluxo . Preencha o novo formulário de **entrada** com as seguintes informações:

   |**Definição**  |**Valor sugerido**  |**Descrição**  |
   |---------|---------|---------|
   |Alias de entrada| *TwitterStream* | Insira um pseudónimo para a entrada. |
   |Subscrição  | \<A sua subscrição\> |  Selecione a subscrição do Azure que pretende utilizar. |
   |Espaço de nomes do hub de eventos | *asa-twitter-eventhub* |
   |O nome do hub de eventos | *socialtwitter-eh* | Escolha *utilizar o uso existente*. Em seguida, selecione o Centro de Eventos que criou.|
   |Tipo de compressão de eventos| GZip | O tipo de compressão de dados.|

   Deixe os restantes valores predefinidos e selecione **Guardar**.

## <a name="specify-the-job-query"></a>Especifique a consulta de trabalho

Stream Analytics suporta um modelo de consulta simples e declarativa que descreve transformações. Para saber mais sobre o idioma, consulte a Referência da [Linguagem da Consulta do Azure Stream Analytics](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference). Este guia ajuda-o a escrever e a testar várias consultas através de dados do Twitter.

Para comparar o número de menções entre tópicos, pode usar uma [janela Tumbling](https://docs.microsoft.com/stream-analytics-query/tumbling-window-azure-stream-analytics) para obter a contagem de menções por tópico a cada cinco segundos.

1. No seu trabalho **Visão Geral,** selecione **Editar consulta** perto da parte superior direita da caixa De consulta. O Azure lista as inputs e saídas que estão configuradas para o trabalho e permite criar uma consulta para transformar o fluxo de entrada à medida que é enviado para a saída.

2. Altere a consulta no editor de consulta para o seguinte:

   ```sql
   SELECT *
   FROM TwitterStream
   ```

3. Os dados do evento das mensagens devem aparecer na janela de **pré-visualização** de Entrada abaixo da sua consulta. Certifique-se de que o **View** está definido para **JSON**. Se não vir quaisquer dados, certifique-se de que o seu gerador de dados está a enviar eventos para o seu centro de eventos e que selecionou o **GZip** como o tipo de compressão para a entrada.

4. Selecione **a consulta** de teste e note os resultados na janela de **resultados** do Teste abaixo da sua consulta.

5. Altere a consulta no editor de código para o seguinte e selecione a consulta de **teste:**

   ```sql
   SELECT System.Timestamp as Time, text
   FROM TwitterStream
   WHERE text LIKE '%Azure%'
   ```

6. Esta consulta devolve todos os tweets que incluem a palavra-chave *Azure.*

## <a name="create-an-output-sink"></a>Criar um lavatório de saída

Definiu agora um fluxo de eventos, uma entrada de centro de eventos para ingerir eventos, e uma consulta para realizar uma transformação sobre o fluxo. O último passo é definir um lavatório de saída para o trabalho.  

Neste guia de como guiar, você escreve os eventos de tweet agregados da consulta de trabalho para o armazenamento De Blob Azure.  Também pode empurrar os seus resultados para a Base de Dados Azure SQL, armazenamento de mesa azure, Hubs de eventos ou Power BI, dependendo das necessidades da sua aplicação.

## <a name="specify-the-job-output"></a>Especificar a saída de trabalho

1. Na secção **De Trabalho Topologia** no menu de navegação à esquerda, selecione **Saídas**. 

2. Na página **Outputs,** clique em ** + &nbsp; Adicionar** e **Blob armazenamento/Data Lake Storage Gen2:**

   * **Pseudónimo de saída**: Utilize o nome `TwitterStream-Output` . 
   * **Opções de importação**: **Selecione o armazenamento a partir das suas subscrições**.
   * **Conta de armazenamento.** Selecione a sua conta de armazenamento.
   * **Recipiente**. Selecione **Criar novo** e `socialtwitter` entrar.
   
4. Selecione **Guardar**.   

## <a name="start-the-job"></a>Iniciar a tarefa

É especificada uma entrada de trabalho, consulta e saída. Está pronto para iniciar o trabalho de Stream Analytics.

1. Certifique-se de que a aplicação TwitterClientCore está em execução. 

2. Na visão geral do trabalho, selecione **Iniciar**.

3. Na página de início de **trabalho,** para iniciar a saída de **trabalho,** selecione **Agora** e, em seguida, selecione **Iniciar**.

## <a name="get-support"></a>Obter suporte
Para mais assistência, experimente o nosso [Microsoft Q&Uma página de perguntas para o Azure Stream Analytics](https://docs.microsoft.com/answers/topics/azure-stream-analytics.html).

## <a name="next-steps"></a>Passos seguintes
* [Introdução ao Azure Stream Analytics](stream-analytics-introduction.md)
* [Começar a utilizar o Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Tarefas de escala do Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Referência do idioma de consulta do Azure Stream Analytics](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Referência de API do REST de gestão do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)
