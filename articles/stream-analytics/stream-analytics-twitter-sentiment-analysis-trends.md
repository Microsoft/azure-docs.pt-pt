---
title: Análise de opiniões do Twitter em tempo real com Azure Stream Analytics
description: Este artigo descreve como usar Stream Analytics para análise de opiniões do Twitter em tempo real. Diretrizes passo a passo da geração de eventos para dados em um painel ao vivo.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 07/09/2019
ms.openlocfilehash: f3ab21d55b7d59bb58760bfba452b4ea2d103496
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75369903"
---
# <a name="real-time-twitter-sentiment-analysis-in-azure-stream-analytics"></a>Análise de opiniões do Twitter em tempo real no Azure Stream Analytics

Saiba como criar uma solução de análise de sentimentos para análise de mídia social, trazendo eventos do Twitter em tempo real para os hubs de eventos do Azure. Em seguida, escreva uma consulta de Azure Stream Analytics para analisar os dados e armazenar os resultados para uso posterior ou criar um painel de [Power bi](https://powerbi.com/) para fornecer informações em tempo real.

As ferramentas de análise de mídia social ajudam as organizações a entender os tópicos de tendências. Os tópicos de tendência são assuntos e atitudes que têm um alto volume de postagens na mídia social. A análise de sentimentos, que também é chamada de *mineração de opinião*, usa ferramentas de análise de mídia social para determinar atitudes em relação a um produto ou uma ideia. 

A análise de tendências do Twitter em tempo real é um ótimo exemplo de uma ferramenta de análise porque o modelo de assinatura de hashtag permite que você ouça palavras-chave específicas (hashtags) e desenvolva a análise de sentimentos do feed.

## <a name="scenario-social-media-sentiment-analysis-in-real-time"></a>Cenário: análise de sentimentos de mídia social em tempo real

Uma empresa que tem um site de mídia de notícias está interessada em obter uma vantagem sobre seus concorrentes, apresentando o conteúdo do site que é imediatamente relevante para seus leitores. A empresa usa a análise de mídia social nos tópicos que são relevantes para os leitores, fazendo uma análise de sentimentos em tempo real dos dados do Twitter.

Para identificar os tópicos de tendência em tempo real no Twitter, a empresa precisa de análise em tempo real sobre o volume de tweets e o sentimentos para tópicos importantes.

## <a name="prerequisites"></a>Pré-requisitos
Neste guia de instruções, você usa um aplicativo cliente que se conecta ao Twitter e procura Tweets que têm determinadas hashtags (que você pode definir). Para executar o aplicativo e analisar os tweets usando o Azure streaming Analytics, você deve ter o seguinte:

* Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/).
* Uma conta do [Twitter](https://twitter.com) .
* O aplicativo TwitterWPFClient, que lê o feed do Twitter. Para obter esse aplicativo, baixe o arquivo [TwitterWPFClient. zip](https://github.com/Azure/azure-stream-analytics/blob/master/Samples/TwitterClient/TwitterWPFClient.zip) do GitHub e descompacte o pacote em uma pasta no computador. Se você quiser ver o código-fonte e executar o aplicativo em um depurador, você pode obter o código-fonte do [GitHub](https://github.com/Azure/azure-stream-analytics/tree/master/Samples/TwitterClient). 

## <a name="create-an-event-hub-for-streaming-analytics-input"></a>Criar um hub de eventos para entrada do streaming Analytics

O aplicativo de exemplo gera eventos e os envia para um hub de eventos do Azure. Os hubs de eventos do Azure são o método preferencial de ingestão de eventos para Stream Analytics. Para obter mais informações, consulte a [documentação dos hubs de eventos do Azure](../event-hubs/event-hubs-what-is-event-hubs.md).

### <a name="create-an-event-hub-namespace-and-event-hub"></a>Criar um namespace do hub de eventos e um hub de eventos
Crie um namespace de Hub de eventos e, em seguida, adicione um hub de eventos a esse namespace. Os namespaces do hub de eventos são usados para agrupar logicamente as instâncias do barramento de eventos relacionadas. 

1. Faça logon no portal do Azure e clique em **criar um recurso** > **Internet das coisas** > **Hub de eventos**. 

2. Na folha **criar namespace** , insira um nome de namespace, como `<yourname>-socialtwitter-eh-ns`. Você pode usar qualquer nome para o namespace, mas o nome deve ser válido para uma URL e deve ser exclusivo no Azure. 
    
3. Selecione uma assinatura e crie ou escolha um grupo de recursos e, em seguida, clique em **criar**. 

    ![Criar um espaço de nomes do hub de eventos](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-create-eventhub-namespace.png)
 
4. Quando o namespace concluir a implantação, localize o namespace do hub de eventos na lista de recursos do Azure. 

5. Clique no novo namespace e, na folha do namespace, clique em **+&nbsp;Hub de eventos**. 

    ![O botão Adicionar Hub de eventos para criar um novo hub de eventos](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-create-eventhub-button.png)    
 
6. Nomeie o novo `socialtwitter-eh`do hub de eventos. Pode utilizar um nome diferente. Se você fizer isso, anote-o, pois você precisará do nome mais tarde. Você não precisa definir nenhuma outra opção para o Hub de eventos.

    ![Folha para criar um novo hub de eventos](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-create-eventhub.png)
 
7. Clique em **Criar**.


### <a name="grant-access-to-the-event-hub"></a>Conceder acesso ao Hub de eventos

Antes que um processo possa enviar dados para um hub de eventos, o Hub de eventos deve ter uma política que permita o acesso apropriado. A política de acesso produz uma cadeia de ligação que inclui as informações de autorização.

1.  Na folha namespace do evento, clique em **hubs de eventos** e, em seguida, clique no nome do novo hub de eventos.

2.  Na folha Hub de eventos, clique em **políticas de acesso compartilhado** e, em seguida, clique em **+&nbsp;adicionar**.

    >[!NOTE]
    >Verifique se você está trabalhando com o Hub de eventos, não o namespace do hub de eventos.

3.  Adicione uma política chamada `socialtwitter-access` e, para **declaração**, selecione **gerenciar**.

    ![Folha para criar uma nova política de acesso do hub de eventos](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-create-shared-access-policy-manage.png)
 
4.  Clique em **Criar**.

5.  Depois que a política tiver sido implantada, clique nela na lista de políticas de acesso compartilhado.

6.  Localize a caixa rotulada **cadeia de conexão-chave primária** e clique no botão copiar ao lado da cadeia de conexão. 
    
    ![Copiando a chave de cadeia de conexão primária da política de acesso](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-shared-access-policy-copy-connection-string.png)
 
7.  Cole a cadeia de ligação num editor de texto. Você precisa dessa cadeia de conexão para a próxima seção, depois de fazer algumas pequenas edições nela.

    A cadeia de conexão tem esta aparência:

        Endpoint=sb://YOURNAME-socialtwitter-eh-ns.servicebus.windows.net/;SharedAccessKeyName=socialtwitter-access;SharedAccessKey=Gw2NFZw6r...FxKbXaC2op6a0ZsPkI=;EntityPath=socialtwitter-eh

    Observe que a cadeia de conexão contém vários pares chave-valor, separados por ponto-e-vírgula: `Endpoint`, `SharedAccessKeyName`, `SharedAccessKey`e `EntityPath`.  

    > [!NOTE]
    > Por segurança, partes da cadeia de conexão no exemplo foram removidas.

8.  No editor de texto, remova o par de `EntityPath` da cadeia de conexão (não se esqueça de remover o ponto e vírgula que o precede). Quando terminar, a cadeia de conexão terá esta aparência:

        Endpoint=sb://YOURNAME-socialtwitter-eh-ns.servicebus.windows.net/;SharedAccessKeyName=socialtwitter-access;SharedAccessKey=Gw2NFZw6r...FxKbXaC2op6a0ZsPkI=


## <a name="configure-and-start-the-twitter-client-application"></a>Configurar e iniciar o aplicativo cliente do Twitter
O aplicativo cliente obtém eventos de Tweet diretamente do Twitter. Para fazer isso, ele precisa de permissão para chamar as APIs de streaming do Twitter. Para configurar essa permissão, você cria um aplicativo no Twitter, que gera credenciais exclusivas (como um token OAuth). Em seguida, você pode configurar o aplicativo cliente para usar essas credenciais quando ele fizer chamadas à API. 

### <a name="create-a-twitter-application"></a>Criar uma aplicação do Twitter
Se você ainda não tiver um aplicativo do Twitter que possa ser usado para este guia de instruções, você poderá criar um. Você já deve ter uma conta do Twitter.

> [!NOTE]
> O processo exato no Twitter para criar um aplicativo e obter as chaves, os segredos e o token podem mudar. Se essas instruções não corresponderem ao que você vê no site do Twitter, consulte a documentação do desenvolvedor do Twitter.

1. Em um navegador da Web, vá para [Twitter para desenvolvedores](https://developer.twitter.com/en/apps)e selecione **criar um aplicativo**. Você pode ver uma mensagem dizendo que precisa ser aplicada a uma conta de desenvolvedor do Twitter. Fique à vontade para fazer isso e, depois que seu aplicativo tiver sido aprovado, você deverá ver um email de confirmação. Pode levar vários dias para ser aprovado para uma conta de desenvolvedor.

   ![Confirmação da conta de desenvolvedor do Twitter](./media/stream-analytics-twitter-sentiment-analysis-trends/twitter-dev-confirmation.png "Confirmação da conta de desenvolvedor do Twitter")

   ![Detalhes do aplicativo do Twitter](./media/stream-analytics-twitter-sentiment-analysis-trends/provide-twitter-app-details.png "Detalhes do aplicativo do Twitter")

2. Na página **Criar uma aplicação**, forneça os detalhes da nova aplicação e, em seguida, selecione **Criar a sua aplicação do Twitter**.

   ![Detalhes do aplicativo do Twitter](./media/stream-analytics-twitter-sentiment-analysis-trends/provide-twitter-app-details-create.png "Detalhes do aplicativo do Twitter")

3. Na página do aplicativo, selecione a guia **chaves e tokens** e copie os valores da **chave de API do consumidor** e da **chave secreta da API do consumidor**. Além disso, selecione **criar** sob **token de acesso e segredo de token de acesso** para gerar os tokens de acesso. Copie os valores de **Token de Acesso** e **Segredo de Token de Acesso**.

    ![Detalhes do aplicativo do Twitter](./media/stream-analytics-twitter-sentiment-analysis-trends/twitter-app-key-secret.png "Detalhes do aplicativo do Twitter")

Guarde os valores que obteve da aplicação do Twitter. Você precisará dos valores posteriormente no instruções.

>[!NOTE]
>As chaves e os segredos para o aplicativo do Twitter fornecem acesso à sua conta do Twitter. Trate essas informações como confidenciais, da mesma forma que você faz com sua senha do Twitter. Por exemplo, não incorpore essas informações em um aplicativo que você dê a outras pessoas. 


### <a name="configure-the-client-application"></a>Configurar o aplicativo cliente
Criamos um aplicativo cliente que se conecta aos dados do Twitter usando as [APIs de streaming do Twitter](https://dev.twitter.com/streaming/overview) para coletar eventos de tweet sobre um conjunto específico de tópicos. O aplicativo usa a ferramenta de código aberto [Sentiment140](http://help.sentiment140.com/) , que atribui o seguinte valor de sentimentos a cada tweet:

* 0 = negativo
* 2 = neutro
* 4 = positivo

Depois que os eventos de tweet tiverem sido atribuídos a um valor de sentimentos, eles serão enviados para o Hub de eventos que você criou anteriormente.

Antes que o aplicativo seja executado, ele requer determinadas informações de você, como as chaves do Twitter e a cadeia de conexão do hub de eventos. Você pode fornecer as informações de configuração das seguintes maneiras:

* Execute o aplicativo e, em seguida, use a interface do usuário do aplicativo para inserir as chaves, os segredos e a cadeia de conexão. Se você fizer isso, as informações de configuração serão usadas para a sessão atual, mas não serão salvas.
* Edite o arquivo. config do aplicativo e defina os valores ali. Essa abordagem persiste as informações de configuração, mas também significa que essas informações potencialmente confidenciais são armazenadas em texto sem formatação no seu computador.

O procedimento a seguir documenta as duas abordagens. 

1. Verifique se você baixou e descompactou o aplicativo [TwitterWPFClient. zip](https://github.com/Azure/azure-stream-analytics/blob/master/Samples/TwitterClient/TwitterWPFClient.zip) , conforme listado nos pré-requisitos.

2. Para definir os valores em tempo de execução (e somente para a sessão atual), execute o aplicativo `TwitterWPFClient.exe`. Quando o aplicativo solicitar, insira os seguintes valores:

    * A chave de consumidor do Twitter (chave de API).
    * O segredo do consumidor do Twitter (segredo da API).
    * O token de acesso do Twitter.
    * O segredo de token de acesso do Twitter.
    * As informações da cadeia de conexão que você salvou anteriormente. Certifique-se de usar a cadeia de conexão que você removeu o `EntityPath` par chave-valor de.
    * As palavras-chave do Twitter para as quais você deseja determinar o sentimentos.

   ![Aplicativo TwitterWpfClient em execução, mostrando configurações obscurecidas](./media/stream-analytics-twitter-sentiment-analysis-trends/wpfclientlines.png)

3. Para definir os valores de forma persistente, use um editor de texto para abrir o arquivo TwitterWpfClient. exe. config. Em seguida, no elemento `<appSettings>`, faça o seguinte:

   * Defina `oauth_consumer_key` para a chave de consumidor do Twitter (chave de API). 
   * Defina `oauth_consumer_secret` para o segredo do consumidor do Twitter (segredo da API).
   * Defina `oauth_token` para o token de acesso do Twitter.
   * Defina `oauth_token_secret` para o segredo de token de acesso do Twitter.

     Posteriormente no elemento `<appSettings>`, faça estas alterações:

   * Defina `EventHubName` para o nome do hub de eventos (ou seja, para o valor do caminho da entidade).
   * Defina `EventHubNameConnectionString` para a cadeia de conexão. Certifique-se de usar a cadeia de conexão que você removeu o `EntityPath` par chave-valor de.

     A seção `<appSettings>` é semelhante ao exemplo a seguir. (Para fins de clareza e segurança, Encapsulamos algumas linhas e removemos alguns caracteres.)

     ![Arquivo de configuração de aplicativo TwitterWpfClient em um editor de texto, mostrando as chaves e os segredos do Twitter e as informações de cadeia de conexão do hub de eventos](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-tiwtter-app-config.png)
 
4. Se você ainda não iniciou o aplicativo, execute TwitterWpfClient. exe agora. 

5. Clique no botão verde Iniciar para coletar opiniões sociais. Você vê eventos de tweet com os valores **CreatedAt**, **topic**e **SentimentScore** que estão sendo enviados ao seu hub de eventos.

    ![Aplicativo TwitterWpfClient em execução, mostrando uma lista de tweets](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-twitter-app-listing.png)

    >[!NOTE]
    >Se você encontrar erros e não vir um fluxo de tweets exibido na parte inferior da janela, verifique as chaves e os segredos. Verifique também a cadeia de conexão (certifique-se de que ela não inclui a chave de `EntityPath` e o valor.)


## <a name="create-a-stream-analytics-job"></a>Criar uma tarefa do Stream Analytics

Agora que os eventos de tweet estão sendo transmitidos em tempo real do Twitter, você pode configurar um trabalho de Stream Analytics para analisar esses eventos em tempo real.

1. Na portal do Azure, clique em **criar um recurso** > **Internet das Coisas** > **trabalho Stream Analytics**.

2. Nomeie o trabalho `socialtwitter-sa-job` e especifique uma assinatura, um grupo de recursos e um local.

    É uma boa ideia posicionar o trabalho e o Hub de eventos na mesma região para obter o melhor desempenho e, assim, você não paga para transferir dados entre regiões.

    ![Criando um novo trabalho de Stream Analytics](./media/stream-analytics-twitter-sentiment-analysis-trends/newjob.png)

3. Clique em **Criar**.

    O trabalho é criado e o portal exibe detalhes do trabalho.


## <a name="specify-the-job-input"></a>Especificar a entrada do trabalho

1. Em seu trabalho de Stream Analytics, em **topologia do trabalho** no meio da folha do trabalho, clique em **entradas**. 

2. Na folha **entradas** , clique em **+&nbsp;adicionar** e, em seguida, preencha a folha com estes valores:

   * **Alias de entrada**: Use o nome `TwitterStream`. Se você usar um nome diferente, anote-o porque você precisará dele mais tarde.
   * **Tipo de origem**: selecione **fluxo de dados**.
   * **Origem**: selecione **Hub de eventos**.
   * **Opção de importação**: selecione **usar o Hub de eventos da assinatura atual**. 
   * **Namespace do barramento de serviço**: selecione o namespace do hub de eventos que você criou anteriormente (`<yourname>-socialtwitter-eh-ns`).
   * **Hub de eventos**: selecione o Hub de eventos que você criou anteriormente (`socialtwitter-eh`).
   * **Nome da política do hub de eventos**: selecione a política de acesso que você criou anteriormente (`socialtwitter-access`).

     ![Criar nova entrada para o trabalho do Stream Analytics](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-twitter-new-input.png)

3. Clique em **Criar**.


## <a name="specify-the-job-query"></a>Especificar a consulta do trabalho

O Stream Analytics dá suporte a um modelo de consulta simples e declarativo que descreve as transformações. Para saber mais sobre a linguagem, consulte a [referência de linguagem de consulta do Azure Stream Analytics](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference).  Este guia de instruções ajuda você a criar e testar várias consultas sobre dados do Twitter.

Para comparar o número de menção entre tópicos, você pode usar uma [janela em cascata](https://docs.microsoft.com/stream-analytics-query/tumbling-window-azure-stream-analytics) para obter a contagem de menção por tópico a cada cinco segundos.

1. Feche a folha de **entradas** se ainda não tiver feito isso.

2. Na folha **visão geral** , clique em **Editar consulta** próximo à parte superior direita da caixa consulta. O Azure lista as entradas e saídas configuradas para o trabalho e permite que você crie uma consulta que permite transformar o fluxo de entrada conforme ele é enviado para a saída.

3. Verifique se o aplicativo TwitterWpfClient está em execução. 

3. Na folha **consulta** , clique nos pontos ao lado da entrada `TwitterStream` e, em seguida, selecione **dados de exemplo da entrada**.

    ![Opções de menu para usar dados de exemplo para a entrada de trabalho do Stream Analytics, com "dados de exemplo da entrada" selecionados](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-create-sample-data-from-input.png)

    Isso abre uma folha que permite especificar a quantidade de dados de exemplo a serem obtidos, definidos em termos de quanto tempo deve-se ler o fluxo de entrada.

4. Defina **minutos** como 3 e clique em **OK**. 
    
    ![Opções para amostragem do fluxo de entrada, com "3 minutos" selecionado.](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-input-create-sample-data.png)

    O Azure amostras de 3 minutos de dados do fluxo de entrada e o notifica quando os dados de exemplo estão prontos. (Isso demora um pouco.) 

    Os dados de exemplo são temporariamente armazenados e estão disponíveis enquanto a janela de consulta estiver aberta. Se você fechar a janela de consulta, os dados de exemplo serão descartados e você precisará criar um novo conjunto de dados de exemplo. 

5. Altere a consulta no editor de código para o seguinte:

    ```
    SELECT System.Timestamp as Time, Topic, COUNT(*)
    FROM TwitterStream TIMESTAMP BY CreatedAt
    GROUP BY TUMBLINGWINDOW(s, 5), Topic
    ```

    Se não tiver usado `TwitterStream` como o alias para a entrada, substitua o alias por `TwitterStream` na consulta.  

    Essa consulta usa a palavra-chave **timestamp by** para especificar um campo de carimbo de data/hora na carga a ser usada na computação temporal. Se esse campo não for especificado, a operação de janela será executada usando a hora em que cada evento chegou ao Hub de eventos. Saiba mais na seção "hora de chegada vs. hora do aplicativo" da [referência de consulta do Stream Analytics](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference).

    Essa consulta também acessa um carimbo de data/hora para o final de cada janela usando a propriedade **System. Timestamp** .

5. Clique em **testar**. A consulta é executada nos dados que você amostra.
    
6. Clique em **Guardar**. Isso salva a consulta como parte do trabalho do Stream Analytics. (Ele não salva os dados de exemplo.)


## <a name="experiment-using-different-fields-from-the-stream"></a>Experimente usar campos diferentes do fluxo 

A tabela a seguir lista os campos que fazem parte dos dados de streaming JSON. Sinta-se à vontade para experimentar no editor de consultas.

|Propriedade JSON | Definição|
|--- | ---|
|CreatedAt | A hora em que o tweet foi criado|
|Tópico | O tópico que corresponde à palavra-chave especificada|
|SentimentScore | A pontuação de sentimentos de Sentiment140|
|Autor | O identificador do Twitter que enviou o tweet|
|Texto | O corpo completo do tweet|


## <a name="create-an-output-sink"></a>Criar um coletor de saída

Agora você definiu um fluxo de eventos, uma entrada de Hub de eventos para ingerir eventos e uma consulta para executar uma transformação sobre o fluxo. A última etapa é definir um coletor de saída para o trabalho.  

Neste guia de instruções, você escreve os eventos de tweet agregados da consulta de trabalho para o armazenamento de BLOBs do Azure.  Você também pode enviar seus resultados para o banco de dados SQL do Azure, armazenamento de tabelas do Azure, hubs de eventos ou Power BI, dependendo das necessidades do seu aplicativo.

## <a name="specify-the-job-output"></a>Especificar a saída do trabalho

1. Na seção **topologia do trabalho** , clique na caixa **saída** . 

2. Na folha **saídas** , clique em **+&nbsp;adicionar** e, em seguida, preencha a folha com estes valores:

   * **Alias de saída**: Use o nome `TwitterStream-Output`. 
   * **Coletor**: selecione **armazenamento de BLOBs**.
   * **Opções de importação**: selecione **usar armazenamento de BLOBs da assinatura atual**.
   * **Conta de armazenamento**. Selecione **criar uma nova conta de armazenamento.**
   * **Conta de armazenamento** (segunda caixa). Insira `YOURNAMEsa`, em que `YOURNAME` é seu nome ou outra cadeia de caracteres exclusiva. O nome pode utilizar apenas letras minúsculas e números e tem de ser exclusivo em todo o Azure. 
   * **Contêiner**. Introduza `socialtwitter`.
     O nome da conta de armazenamento e o nome do contêiner são usados juntos para fornecer um URI para o armazenamento de BLOBs, desta forma: 

     `http://YOURNAMEsa.blob.core.windows.net/socialtwitter/...`
    
     ![Folha "nova saída" para o trabalho de Stream Analytics](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-create-output-blob-storage.png)
    
4. Clique em **Criar**. 

    O Azure cria a conta de armazenamento e gera uma chave automaticamente. 

5. Feche a folha **saídas** . 


## <a name="start-the-job"></a>Iniciar a tarefa

Uma entrada de trabalho, consulta e saída são especificados. Você está pronto para iniciar o trabalho de Stream Analytics.

1. Verifique se o aplicativo TwitterWpfClient está em execução. 

2. Na folha do trabalho, clique em **Iniciar**.

    ![Iniciar o trabalho de Stream Analytics](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-sa-job-start-output.png)

3. Na folha **Iniciar trabalho** , para **hora de início da saída do trabalho**, selecione **agora** e clique em **Iniciar**. 

    ![Folha "Iniciar trabalho" para o trabalho de Stream Analytics](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-sa-job-start-job-blade.png)

    O Azure notifica você quando o trabalho é iniciado e, na folha de trabalho, o status é exibido como **em execução**.

    ![Trabalho em execução](./media/stream-analytics-twitter-sentiment-analysis-trends/jobrunning.png)

## <a name="view-output-for-sentiment-analysis"></a>Exibir saída para análise de sentimentos

Depois que o trabalho tiver começado a ser executado e estiver processando o fluxo do Twitter em tempo real, você poderá exibir a saída para análise de sentimentos.

Você pode usar uma ferramenta como o [Gerenciador de armazenamento do Azure](https://storageexplorer.com/) ou o [Azure Explorer](https://www.cerebrata.com/products/azure-explorer/introduction) para exibir a saída do trabalho em tempo real. A partir daqui, você pode usar [Power bi](https://powerbi.com/) para estender seu aplicativo para incluir um painel personalizado como aquele mostrado na seguinte captura de tela:

![Power BI](./media/stream-analytics-twitter-sentiment-analysis-trends/power-bi.png)


## <a name="create-another-query-to-identify-trending-topics"></a>Criar outra consulta para identificar tópicos de tendências

Outra consulta que você pode usar para entender os sentimentos do Twitter é baseada em uma [janela deslizante](https://docs.microsoft.com/stream-analytics-query/sliding-window-azure-stream-analytics). Para identificar tópicos de tendência, procure tópicos que cruzem um valor de limite para menção em um período de tempo especificado.

Para os fins deste "como", você verifica os tópicos que são mencionados mais de 20 vezes nos últimos 5 segundos.

1. Na folha do trabalho, clique em **parar** para interromper o trabalho. 

2. Na seção **topologia do trabalho** , clique na caixa **consulta** . 

3. Altere a consulta para o seguinte:

    ```    
    SELECT System.Timestamp as Time, Topic, COUNT(*) as Mentions
    FROM TwitterStream TIMESTAMP BY CreatedAt
    GROUP BY SLIDINGWINDOW(s, 5), topic
    HAVING COUNT(*) > 20
    ```

4. Clique em **Guardar**.

5. Verifique se o aplicativo TwitterWpfClient está em execução. 

6. Clique em **Iniciar** para reiniciar o trabalho usando a nova consulta.


## <a name="get-support"></a>Obter suporte
Para obter assistência, tente nosso [fórum do Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Passos seguintes
* [Introdução ao Azure Stream Analytics](stream-analytics-introduction.md)
* [Começar a utilizar o Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Tarefas de escala do Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Referência do idioma de consulta do Azure Stream Analytics](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Referência de API do REST de gestão do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)
