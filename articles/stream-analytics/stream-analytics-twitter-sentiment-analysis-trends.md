---
title: Análise de sentimentos do Twitter em tempo real com o Azure Stream Analytics
description: Este artigo descreve como utilizar o Stream Analytics para análise de sentimentos do Twitter em tempo real. Orientações passo a passo da geração de eventos aos dados num dashboard em direto.
services: stream-analytics
author: jseb225
ms.author: jeanb
ms.reviewer: jasonh
manager: kfile
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/29/2017
ms.openlocfilehash: c34b3fa1597866e4f722fccee84f83f4caab6bfb
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56876301"
---
# <a name="real-time-twitter-sentiment-analysis-in-azure-stream-analytics"></a>Análise de sentimentos do Twitter em tempo real no Azure Stream Analytics

Saiba como criar uma solução de análise de sentimento de análise de redes sociais, trazendo eventos em tempo real do Twitter para os Hubs de eventos do Azure. Pode, em seguida, utilize o escrita uma consulta do Azure Stream Analytics para analisar os dados e a armazenar os resultados para mais tarde ou utilize um dashboard e [Power BI](https://powerbi.com/) para fornecer informações em tempo real.

Ferramentas de análise de redes sociais ajudam as organizações a compreender os tópicos em destaque. Tópicos mais populares são assuntos e atitudes que têm um grande volume de mensagens em mídia social. Análise de sentimentos, que também é designado *extração de opinião*, utiliza as ferramentas de análise de redes sociais para determinar atitudes na direção de um produto, idéia e assim por diante. 

Análise de tendências do Twitter em tempo real é um ótimo exemplo de uma ferramenta de análise, porque o modelo de assinatura de hashtag permite que ouça as palavras-chave específicas (hashtags) e desenvolver a análise de sentimentos do feed.

## <a name="scenario-social-media-sentiment-analysis-in-real-time"></a>Cenário: Análise de sentimentos de meios de comunicação em tempo real

Uma empresa que tem um Web site de multimédia para notícias está interessada em obter uma vantagem sobre seus concorrentes, apresentando conteúdo de site que é imediatamente relevante para os seus leitores. A empresa utiliza a análise de redes sociais sobre tópicos que são relevantes para os leitores, fazendo a análise de sentimentos em tempo real de dados do Twitter.

Para identificar tópicos em destaque em tempo real no Twitter, a empresa precisa de análise em tempo real sobre o volume de tweet e o sentimento para principais tópicos. Em outras palavras, a necessidade é um motor de análise de análise de sentimentos baseada nesse feed de mídia social.

## <a name="prerequisites"></a>Pré-requisitos
Neste tutorial, vai utilizar uma aplicação cliente que se liga ao Twitter e procura tweets com determinados hashtags (que pode configurar a). Para executar o aplicativo e analisar os tweets com o Azure Stream Analytics, tem de ter o seguinte:

* Uma subscrição do Azure
* Uma conta do Twitter 
* Um aplicativo do Twitter e o [token de acesso de OAuth](https://dev.twitter.com/oauth/overview/application-owner-access-tokens) para essa aplicação. Fornecemos instruções de alto nível para saber como criar um aplicativo do Twitter mais tarde.
* O aplicativo TwitterWPFClient, que lê o feed do Twitter. Para obter esta aplicação, transfira o [TwitterWPFClient.zip](https://github.com/Azure/azure-stream-analytics/blob/master/Samples/TwitterClient/TwitterWPFClient.zip) ficheiro a partir do GitHub e, em seguida, deszipe o pacote para uma pasta no seu computador. Se quiser ver a origem de código e executar o aplicativo num depurador, pode obter o código-fonte [GitHub](https://github.com/Azure/azure-stream-analytics/tree/master/Samples/TwitterClient). 

## <a name="create-an-event-hub-for-streaming-analytics-input"></a>Criar um hub de eventos de entrada de análise de transmissão em fluxo

O exemplo de aplicativo gera eventos e envia-as para um hub de eventos do Azure. Os hubs de eventos do Azure são o método preferencial de ingestão de eventos para o Stream Analytics. Para obter mais informações, consulte a [documentação dos Hubs de eventos do Azure](../event-hubs/event-hubs-what-is-event-hubs.md).


### <a name="create-an-event-hub-namespace-and-event-hub"></a>Criar um espaço de nomes de hub de eventos e um hub de eventos
Neste procedimento, primeiro tem de criar um espaço de nomes do hub de eventos e, em seguida, adicione um hub de eventos para esse espaço de nomes. Espaços de nomes de hub de eventos são utilizados para agrupar logicamente as instâncias de barramento de eventos relacionados. 

1. Inicie sessão no portal do Azure e clique em **criar um recurso** > **Internet das coisas** > **Hub de eventos**. 

2. Na **criar espaço de nomes** painel, introduza um nome de espaço de nomes como `<yourname>-socialtwitter-eh-ns`. Pode utilizar qualquer nome para o espaço de nomes, mas o nome tem de ser válido para um URL e tem de ser exclusivo em todo o Azure. 
    
3. Selecione uma subscrição e criar ou escolha um grupo de recursos e clique em **criar**. 

    ![Criar um espaço de nomes do hub de eventos](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-create-eventhub-namespace.png)
 
4. Quando o espaço de nomes tiver concluído a implementação, encontre o espaço de nomes do hub de eventos na sua lista de recursos do Azure. 

5. Clique em novo espaço de nomes e, no painel do espaço de nomes, clique em  **+ &nbsp;Hub de eventos**. 

    ![O botão Adicionar Hub de eventos para a criação de um novo hub de eventos](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-create-eventhub-button.png)    
 
6. Nomeie o novo hub de eventos `socialtwitter-eh`. Pode utilizar um nome diferente. Se o fizer, tome nota do mesmo, porque é necessário o nome mais tarde. Não precisa definir quaisquer outras opções para o hub de eventos.

    ![Painel para criar um novo hub de eventos](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-create-eventhub.png)
 
7. Clique em **Criar**.


### <a name="grant-access-to-the-event-hub"></a>Conceder acesso ao hub de eventos

Antes de um processo pode enviar dados para um hub de eventos, o hub de eventos tem de ter uma política que permita o acesso adequado. A política de acesso produz uma cadeia de ligação que inclui as informações de autorização.

1.  No painel de espaço de nomes do evento, clique em **os Hubs de eventos** e, em seguida, clique no nome do seu novo hub de eventos.

2.  No painel do hub de eventos, clique em **políticas de acesso partilhado** e, em seguida, clique em  **+ &nbsp;Add**.

    >[!NOTE]
    >Certifique-se de que está a trabalhar com o hub de eventos, não o namespace do hub de eventos.

3.  Adicionar uma política com o nome `socialtwitter-access` e para **afirmação**, selecione **gerir**.

    ![Painel para criar uma nova política de acesso do hub de eventos](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-create-shared-access-policy-manage.png)
 
4.  Clique em **Criar**.

5.  Depois de implementar a política, clique no mesmo na lista de políticas de acesso partilhado.

6.  Localize a caixa rotulada **ligação chave primária da cadeia de caracteres** e clique no botão Copiar junto a cadeia de ligação. 
    
    ![Copiar a chave de cadeia de ligação primária da política de acesso](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-shared-access-policy-copy-connection-string.png)
 
7.  Cole a cadeia de ligação num editor de texto. Precisa desta cadeia de ligação para a secção seguinte, depois de fazer algumas pequenas edições ao mesmo.

    A cadeia de ligação tem o seguinte aspeto:

        Endpoint=sb://YOURNAME-socialtwitter-eh-ns.servicebus.windows.net/;SharedAccessKeyName=socialtwitter-access;SharedAccessKey=Gw2NFZw6r...FxKbXaC2op6a0ZsPkI=;EntityPath=socialtwitter-eh

    Tenha em atenção que a cadeia de ligação contém vários pares de chave-valor, separados por ponto e vírgula: `Endpoint`, `SharedAccessKeyName`, `SharedAccessKey`, e `EntityPath`.  

    > [!NOTE]
    > Para segurança, foram removidos partes da cadeia de ligação no exemplo.

8.  No editor de texto, remova o `EntityPath` par da cadeia de ligação (não se esqueça de remover o ponto e vírgula que o precede). Quando tiver terminado, a cadeia de ligação é semelhante a isto:

        Endpoint=sb://YOURNAME-socialtwitter-eh-ns.servicebus.windows.net/;SharedAccessKeyName=socialtwitter-access;SharedAccessKey=Gw2NFZw6r...FxKbXaC2op6a0ZsPkI=


## <a name="configure-and-start-the-twitter-client-application"></a>Configurar e iniciar a aplicação de cliente do Twitter
A aplicação cliente obtém os eventos de tweet diretamente a partir do Twitter. Para fazê-lo, ele precisa de permissão para chamar as APIs de transmissão em fluxo do Twitter. Para configurar essa permissão, crie uma aplicação no Twitter, que gera exclusivas credenciais (por exemplo, um token de OAuth). Em seguida, pode configurar a aplicação de cliente para utilizar estas credenciais quando faz chamadas de API. 

### <a name="create-a-twitter-application"></a>Criar uma aplicação do Twitter
Se ainda não tiver um aplicativo do Twitter que pode utilizar para este tutorial, pode criar um. Já tem de ter uma conta do Twitter.

> [!NOTE]
> O processo exato do twitter para criar uma aplicação e obter as chaves, segredos e token podem ser alteradas. Se estas instruções não correspondem o que vê no site do Twitter, consulte a documentação de programador do Twitter.

1. Vá para o [página de gerenciamento de aplicativo do Twitter](https://apps.twitter.com/). 

2. Crie uma nova aplicação. 

    * Para o URL do Web site, especifique um URL válido. Não tem de ser um site em direto. (Não é possível especificar apenas `localhost`.)
    * Deixe o campo de retorno de chamada em branco. A aplicação de cliente que utilizar para este tutorial não requer retornos de chamada.

    ![Criar uma aplicação no Twitter](./media/stream-analytics-twitter-sentiment-analysis-trends/create-twitter-application.png)

3. Opcionalmente, altere as permissões da aplicação para só de leitura.

4. Quando a aplicação é criada, vá para o **chaves e Tokens de acesso** página.

5. Clique no botão para gerar um segredo de token acesso token e acesso.

Manter essas informações úteis, porque irá precisar no próximo procedimento.

>[!NOTE]
>As chaves e segredos da aplicação do Twitter fornecem acesso à sua conta do Twitter. Trate estas informações como confidencial, igual ao que fazer a sua palavra-passe do Twitter. Por exemplo, não incorpore essas informações num aplicativo que irá fornecer a outras pessoas. 


### <a name="configure-the-client-application"></a>Configurar a aplicação de cliente
Criámos uma aplicação cliente que liga-se à utilização de dados do Twitter [APIs de transmissão em fluxo do Twitter](https://dev.twitter.com/streaming/overview) para recolher eventos de tweet sobre um conjunto específico de tópicos. A aplicação utiliza a [Sentiment140](http://help.sentiment140.com/) ferramenta de código-fonte aberto, o que atribui o seguinte valor de sentimento a cada tweet:

* 0 = negativo
* 2 = neutra
* 4 = positivo

Depois dos eventos de tweet foram atribuídos um valor de sentimento, são enviados por push ao hub de eventos que criou anteriormente.

Antes da aplicação é executada, ela exige determinadas informações pessoais, como as chaves do Twitter e a cadeia de ligação do hub de eventos. Pode fornecer as informações de configuração nas seguintes formas:

* Execute a aplicação e, em seguida, utilize a interface do usuário do aplicativo para introduzir as chaves, segredos e cadeia de ligação. Se fizer isso, as informações de configuração são utilizadas para a sua sessão atual, mas não é guardada.
* Editar o arquivo. config do aplicativo e defina os valores aqui. Essa abordagem persistir as informações de configuração, mas isso também significa que essas informações potencialmente confidenciais são armazenadas em texto simples no seu computador.

O procedimento seguinte documenta as duas abordagens. 

1. Certifique-se de ter baixado e a descompactei a [TwitterWPFClient.zip](https://github.com/Azure/azure-stream-analytics/blob/master/Samples/TwitterClient/TwitterWPFClient.zip) aplicativo, como listado nos pré-requisitos.

2. Para definir a execução de valores em tempo de execução (e apenas para a sessão atual), o `TwitterWPFClient.exe` aplicação. Quando o aplicativo solicita, introduza os seguintes valores:

    * A chave de consumidor do Twitter (chave de API).
    * O segredo de consumidor do Twitter (segredo de API).
    * O Token de acesso do Twitter.
    * Segredo Token de acesso do Twitter.
    * As informações da cadeia de ligação que guardou anteriormente. Certifique-se de que utiliza a cadeia de ligação que removeu o `EntityPath` par chave-valor de.
    * As palavras-chave de Twitter que queira determinar a sensibilidade pelos.

   ![TwitterWpfClient aplicação em execução, mostrando o ocultou definições](./media/stream-analytics-twitter-sentiment-analysis-trends/wpfclientlines.png)

3. Para definir os valores de forma permanente, utilize um editor de texto para abrir o ficheiro de TwitterWpfClient.exe.config. Em seguida, no `<appSettings>` elemento, fazer isso:

    * Definir `oauth_consumer_key` para a chave de consumidor do Twitter (chave de API). 
    * Definir `oauth_consumer_secret` para o segredo de consumidor do Twitter (segredo de API).
    * Definir `oauth_token` para o Token de acesso do Twitter.
    * Definir `oauth_token_secret` para o segredo de Token de acesso de Twitter.

    Mais adiante a `<appSettings>` elemento, efetuar estas alterações:

    * Definir `EventHubName` para o nome do hub de eventos (ou seja, para o valor do caminho de entidade).
    * Definir `EventHubNameConnectionString` para a cadeia de ligação. Certifique-se de que utiliza a cadeia de ligação que removeu o `EntityPath` par chave-valor de.

    O `<appSettings>` secção que se parece com o exemplo seguinte. (Para efeitos de clareza e segurança, podemos encapsulada algumas linhas e removido alguns caracteres).

    ![Arquivo de configuração de aplicativo TwitterWpfClient num editor de texto, que mostra as chaves do Twitter e segredos e as informações de cadeia de ligação de hub de eventos](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-tiwtter-app-config.png)
 
4. Se já não iniciou o aplicativo, execute TwitterWpfClient.exe agora. 

5. Clique no botão verde para iniciar a recolher sentimentos de redes sociais. Pode ver eventos de Tweet com o **CreatedAt**, **tópico**, e **SentimentScore** valores a ser enviados para o hub de eventos.

    ![TwitterWpfClient aplicação em execução, mostrando uma listagem de tweets](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-twitter-app-listing.png)

    >[!NOTE]
    >Se vir erros e, se não vir um fluxo de tweets apresentada na parte inferior da janela, verifique novamente as chaves e segredos. Verifique também a cadeia de ligação (Certifique-se de que não inclui o `EntityPath` chave e valor.)


## <a name="create-a-stream-analytics-job"></a>Criar uma tarefa do Stream Analytics

Agora que os eventos de tweet são de transmissão em fluxo em tempo real do Twitter, pode configurar uma tarefa do Stream Analytics para analisar estes eventos em tempo real.

1. No portal do Azure, clique em **criar um recurso** > **Internet das coisas** > **tarefa do Stream Analytics**.

2. Nome da tarefa `socialtwitter-sa-job` e especificar uma subscrição, grupo de recursos e localização.

    É uma boa idéia colocar a tarefa e o hub de eventos na mesma região para um melhor desempenho e para que não pague a transferência de dados entre regiões.

    ![Criar uma nova tarefa de Stream Analytics](./media/stream-analytics-twitter-sentiment-analysis-trends/newjob.png)

3. Clique em **Criar**.

    A tarefa é criada e o portal apresenta os detalhes da tarefa.


## <a name="specify-the-job-input"></a>Especifique a entrada da tarefa

1. Na sua tarefa do Stream Analytics, sob **topologia da tarefa** no meio do painel de tarefas, clique em **entradas**. 

2. Na **entradas** painel, clique em  **+ &nbsp;Add** e, em seguida, preencha o painel com estes valores:

    * **O alias de entrada**: Utilize o nome `TwitterStream`. Se utilizar um nome diferente, tome nota do mesmo, porque irá precisar dele mais tarde.
    * **Tipo de origem**: Selecione **fluxo de dados**.
    * **origem**: Selecione **hub de eventos**.
    * **Opção de importar**: Selecione **hub de eventos de utilização da subscrição atual**. 
    * **O espaço de nomes do Service bus**: Selecione o espaço de nomes de hub de eventos que criou anteriormente (`<yourname>-socialtwitter-eh-ns`).
    * **Hub de eventos**: Selecione o hub de eventos que criou anteriormente (`socialtwitter-eh`).
    * **Nome de política do hub de eventos**: Selecione a política de acesso que criou anteriormente (`socialtwitter-access`).

    ![Criar nova entrada para a tarefa do Stream Analytics](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-twitter-new-input.png)

3. Clique em **Criar**.


## <a name="specify-the-job-query"></a>Especifique a consulta de tarefa

Stream Analytics suporta um modelo de consulta simples e declarativo que descreve as transformações. Para saber mais sobre a linguagem, veja a [referência de linguagem de consulta do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx).  Este tutorial ajuda-o a criar e testar várias consultas em dados do Twitter.

Para comparar o número de menções entre tópicos, pode utilizar um [janela em cascata](https://msdn.microsoft.com/library/azure/dn835055.aspx) para obter a contagem de menções por tópico a cada cinco segundos.

1. Fechar o **entradas** painel se ainda não o fez.

2. Na **descrição geral** painel, clique em **Editar consulta** junto à parte superior direita da caixa de consulta. O Azure apresenta uma lista as entradas e saídas que estão configuradas para a tarefa e permite-lhe criar uma consulta que lhe permite transformar o fluxo de entrada, como ele é enviado para a saída.

3. Certifique-se de que o aplicativo TwitterWpfClient está em execução. 

3. No **consulta** painel, clique nos pontos junto a `TwitterStream` de entrada e, em seguida, selecione **dados de exemplo da entrada**.

    ![Opções de menu para utilizar dados de exemplo para a entrada de tarefa do Stream Analytics, com "Dados de exemplo da entrada" selecionados](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-create-sample-data-from-input.png)

    Esta ação abre um painel que permite que especifique quantos dados de exemplo para obter, definido em termos de tempo para ler o fluxo de entrada.

4. Definir **minutos** 3 e, em seguida, clique em **OK**. 
    
    ![Opções para o fluxo de entrada, de amostragem com "3 minutos" selecionados.](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-input-create-sample-data.png)

    O Azure amostras de valor de 3 minutos de dados do fluxo de entrada e notifica-o quando os dados de exemplo estão prontos. (Isto demora pouco tempo.) 

    Os dados de exemplo são temporariamente armazenados e estão disponíveis enquanto a janela de consulta estiver aberta. Se fechar a janela de consulta, os dados de exemplo são descartados, e tem de criar um novo conjunto de dados de exemplo. 

5. Altere a consulta no editor de código para o seguinte:

    ```
    SELECT System.Timestamp as Time, Topic, COUNT(*)
    FROM TwitterStream TIMESTAMP BY CreatedAt
    GROUP BY TUMBLINGWINDOW(s, 5), Topic
    ```

    Se não usou `TwitterStream` como o alias de entrada, substitua o seu alias para `TwitterStream` na consulta.  

    Esta consulta utiliza a **TIMESTAMP BY** palavra-chave para especificar um campo timestamp no payload a ser utilizada na computação temporal. Se este campo não for especificado, a operação de windowing é efetuada utilizando a hora que cada evento chegou ao hub de eventos. Saiba mais na seção "Vs de tempo de chegada a hora de aplicação" [referência de consulta do Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx).

    Essa consulta acessa também um carimbo para o final de cada janela utilizando o **Timestamp** propriedade.

5. Clique em **teste**. A consulta é executada relativamente aos dados que objeto de amostragem.
    
6. Clique em **Guardar**. Isso poupa a consulta como parte da tarefa do Stream Analytics. (Ele não salva os dados de exemplo.)


## <a name="experiment-using-different-fields-from-the-stream"></a>Experimentação com diferentes campos do fluxo 

A tabela seguinte lista os campos que fazem parte do JSON, dados de transmissão em fluxo. Fique à vontade experimentar no editor de consultas.

|Propriedade JSON | Definição|
|--- | ---|
|CreatedAt | A hora em que o tweet foi criado|
|Tópico | O tópico que corresponda a palavra-chave especificada|
|SentimentScore | A classificação de sentimento de Sentiment140|
|Autor | O identificador do Twitter que enviou o tweet|
|Texto | O corpo completo do tweet|


## <a name="create-an-output-sink"></a>Criar um sink de saída

Já definiu um fluxo de eventos, um hub de eventos para ingerir eventos e uma consulta para realizar uma transformação durante o fluxo de entrada. A última etapa é definir um sink de saída para a tarefa.  

Neste tutorial, gravar os eventos de tweet agregados da consulta de trabalho para o armazenamento de Blobs do Azure.  Também pode enviar os resultados para a SQL Database do Azure, armazenamento de tabelas do Azure, os Hubs de eventos, ou o Power BI, dependendo do seu aplicativo precisa.

## <a name="specify-the-job-output"></a>Especificar o resultado da tarefa

1. Na **topologia da tarefa** secção, clique nas **saída** caixa. 

2. Na **saídas** painel, clique em  **+ &nbsp;Add** e, em seguida, preencha o painel com estes valores:

    * **Alias de saída**: Utilize o nome `TwitterStream-Output`. 
    * **Sink**: Selecione **Armazenamento de blobs**.
    * **Opções de importação**: Selecione **utilizar o armazenamento de BLOBs da subscrição atual**.
    * **Conta de armazenamento**. Selecione **criar uma nova conta de armazenamento.**
    * **Conta de armazenamento** (segunda caixa). Introduza `YOURNAMEsa`, onde `YOURNAME` é o nome ou de outra cadeia de caracteres exclusiva. O nome pode utilizar apenas letras minúsculas e números e tem de ser exclusivo em todo o Azure. 
    * **Contentor**. Introduza `socialtwitter`.
    O nome da conta de armazenamento e o nome de contentor são utilizados em conjunto para fornecer um URI para o armazenamento de BLOBs, assim: 

    `http://YOURNAMEsa.blob.core.windows.net/socialtwitter/...`
    
    ![Painel "Nova saída" tarefa do Stream Analytics](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-create-output-blob-storage.png)
    
4. Clique em **Criar**. 

    O Azure cria a conta de armazenamento e gera uma chave automaticamente. 

5. Fechar o **saídas** painel. 


## <a name="start-the-job"></a>Iniciar a tarefa

Uma entrada da tarefa, consulta e saída estão especificados. Está pronto para iniciar a tarefa de Stream Analytics.

1. Certifique-se de que o aplicativo TwitterWpfClient está em execução. 

2. No painel de tarefas, clique em **iniciar**.

    ![Iniciar a tarefa de Stream Analytics](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-sa-job-start-output.png)

3. Na **tarefa de início** painel, para **hora de início da saída da tarefa**, selecione **agora** e, em seguida, clique em **iniciar**. 

    ![Painel "Iniciar a tarefa" para a tarefa do Stream Analytics](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-sa-job-start-job-blade.png)

    Azure notifica-o quando a tarefa foi iniciada e, no painel da tarefa, o estado é apresentado como **em execução**.

    ![Tarefa em execução](./media/stream-analytics-twitter-sentiment-analysis-trends/jobrunning.png)

## <a name="view-output-for-sentiment-analysis"></a>Saída do modo de exibição para análise de sentimentos

Depois da tarefa foi iniciada em execução e está a processar o fluxo do Twitter em tempo real, pode ver a saída para análise de sentimentos.

Pode usar uma ferramenta como o [Explorador de armazenamento do Azure](https://storageexplorer.com/) ou [Explorador do Azure](http://www.cerebrata.com/products/azure-explorer/introduction) para ver o resultado da tarefa em tempo real. A partir daqui, pode utilizar [Power BI](https://powerbi.com/) para expandir a sua aplicação para incluir um dashboard personalizado, como o mostrado na captura de ecrã seguinte:

![Power BI](./media/stream-analytics-twitter-sentiment-analysis-trends/power-bi.png)


## <a name="create-another-query-to-identify-trending-topics"></a>Criar outra consulta para identificar tópicos em destaque

Outra consulta, pode utilizar para compreender o sentimento do Twitter se baseia numa [janela deslizante](https://msdn.microsoft.com/library/azure/dn835051.aspx). Para identificar tópicos em destaque, procure tópicos que cruzam um valor de limiar para menções num período de tempo especificado.

Para efeitos deste tutorial, procurar tópicos que são mencionados a mais de 20 vezes nos últimos 5 segundos.

1. No painel de tarefas, clique em **parar** para parar a tarefa. 

2. Na **topologia da tarefa** secção, clique nas **consulta** caixa. 

3. Altere a consulta para o seguinte:

    ```    
    SELECT System.Timestamp as Time, Topic, COUNT(*) as Mentions
    FROM TwitterStream TIMESTAMP BY CreatedAt
    GROUP BY SLIDINGWINDOW(s, 5), topic
    HAVING COUNT(*) > 20
    ```

4. Clique em **Guardar**.

5. Certifique-se de que o aplicativo TwitterWpfClient está em execução. 

6. Clique em **iniciar** para reiniciar a tarefa usando a nova consulta.


## <a name="get-support"></a>Obter suporte
Para obter assistência, tente nosso [fórum do Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Passos Seguintes
* [Introdução ao Azure Stream Analytics](stream-analytics-introduction.md)
* [Começar a utilizar o Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Tarefas de escala do Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Referência do idioma de consulta do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referência de API do REST de gestão do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)
