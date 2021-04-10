---
title: Análise de mídia social com Azure Stream Analytics
description: Este artigo descreve como usar o Stream Analytics para análise de redes sociais usando a API cliente do Twitter. Orientação passo a passo da geração de eventos para dados num dashboard ao vivo.
services: stream-analytics
author: enkrumah
ms.author: ebnkruma
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: how-to
ms.date: 03/16/2021
ms.openlocfilehash: e2369e8f4349a3b4a83c1729c6414d319dd8419e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104589672"
---
# <a name="social-media-analysis-with-azure-stream-analytics"></a>Análise de mídia social com Azure Stream Analytics

Este artigo ensina-lhe como construir uma solução de análise de sentimento nas redes sociais, trazendo eventos do Twitter em tempo real para a Azure Event Hubs. Escreve uma consulta Azure Stream Analytics para analisar os dados e armazenar os resultados para posterior utilização ou criar um dashboard [Power BI](https://powerbi.com/) para fornecer informações em tempo real.

As ferramentas de análise de redes sociais ajudam as organizações a entender os tópicos de tendência. Os tópicos de tendência são temas e atitudes que têm um grande volume de publicações nas redes sociais. A análise de sentimentos, que também é chamada *de mineração de opinião,* utiliza ferramentas de análise de redes sociais para determinar atitudes em relação a um produto ou ideia. 

A análise da tendência do Twitter em tempo real é um grande exemplo de uma ferramenta de análise porque o modelo de subscrição de hashtag permite-lhe ouvir palavras-chave específicas (hashtags) e desenvolver a análise de sentimento do feed.

## <a name="scenario-social-media-sentiment-analysis-in-real-time"></a>Cenário: Análise de sentimento nas redes sociais em tempo real

Uma empresa que tenha um website de media de notícias está interessada em ganhar vantagem sobre os seus concorrentes, apresentando conteúdo sonoro que é imediatamente relevante para os seus leitores. A empresa utiliza a análise das redes sociais sobre tópicos que são relevantes para os leitores, fazendo análises de sentimento em tempo real dos dados do Twitter.

Para identificar tópicos de tendência em tempo real no Twitter, a empresa precisa de análises em tempo real sobre o volume de tweet e o sentimento para tópicos-chave.

## <a name="prerequisites"></a>Pré-requisitos

Neste guia de como fazer, você usa uma aplicação de cliente que se conecta ao Twitter e procura tweets que têm certas hashtags (que você pode definir). Para executar a aplicação e analisar os tweets usando a Azure Streaming Analytics, deve ter o seguinte:

* Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/).

* Uma conta [no Twitter.](https://twitter.com)

* A aplicação TwitterClientCore, que lê o feed do Twitter. Para obter esta aplicação, baixe [o TwitterClientCore](https://github.com/Azure/azure-stream-analytics/tree/master/DataGenerators/TwitterClientCore).

* Instale a versão [.NET Core CLI](/dotnet/core/tools/?tabs=netcore2x) 2.1.0.

Abaixo está a arquitetura de solução que vai implementar.

   ![Um diagrama que mostra diferentes peças de serviços e aplicações usadas para construir a solução.](./media/stream-analytics-twitter-sentiment-analysis-trends/solution-diagram.png "Diagrama de solução")

## <a name="create-an-event-hub-for-streaming-input"></a>Criar um centro de eventos para entrada de streaming

A aplicação da amostra gera eventos e empurra-os para um centro de eventos Azure. Os Azure Event Hubs são o método preferido de ingestão de eventos para stream analytics. Para mais informações, consulte a documentação do [Azure Event Hubs](../event-hubs/event-hubs-about.md).

### <a name="create-an-event-hub-namespace-and-event-hub"></a>Crie um centro de eventos no espaço de nome e centro de eventos
Nesta secção, você cria um espaço de nome de centro de eventos e adiciona um centro de eventos a esse espaço de nome. Os espaços de nome do centro de eventos são usados para grupos logicamente relacionados casos de ônibus de evento. 

1. Faça login no portal Azure e selecione **Criar um recurso**. E depois. pesquisar por **Centros de Eventos** e selecione **Criar**.

2. Na página **Create Namespace,** insira um nome de espaço de nome. Você pode usar qualquer nome para o espaço de nome, mas o nome deve ser válido para um URL, e deve ser único em Azure. 
    
3. Selecione um nível de preços e subscrição e crie ou escolha um grupo de recursos. Em seguida, escolha um local e **selecione Criar**. 
 
4. Quando o espaço de nome terminar de ser implementado, navegue para o seu grupo de recursos e encontre o espaço de nome do centro de eventos na sua lista de recursos Azure. 

5. A partir do novo espaço de nome, selecione **+ &nbsp; Event Hub**. 

6. Nomeie o novo centro *de eventos socialtwitter-eh*. Pode utilizar um nome diferente. Se o fizer, tome nota, porque precisa do nome mais tarde. Não precisa definir outras opções para o centro de eventos.
 
7. Selecione **Criar**.

### <a name="grant-access-to-the-event-hub"></a>Conceder acesso ao centro de eventos

Antes de um processo poder enviar dados para um centro de eventos, o centro de eventos precisa de uma política que permita o acesso. A política de acesso produz uma cadeia de ligação que inclui as informações de autorização.

1.  Na barra de navegação no lado esquerdo do espaço de nomes dos centros de eventos, selecione **Event Hubs,** que está localizado na secção **Entidades.** Em seguida, selecione o centro de eventos que acabou de criar.

2.  Na barra de navegação do lado esquerdo, selecione **políticas de acesso partilhado** localizadas em **Definições**.

    >[!NOTE]
    >Existe uma opção de políticas de acesso partilhado para o espaço de nome do centro de eventos e para o centro de eventos. Certifique-se de que está a trabalhar no contexto do seu centro de eventos, não no espaço de nomes de centros de eventos.

3.  A partir da página de política de acesso, selecione **+ Adicionar**. Em *seguida, insira o acesso social-witter* para o **nome Da Política** e verifique a caixa de verificação **Manage.**
 
4.  Selecione **Criar**.

5.  Depois de implementada a política, selecione a política da lista de políticas de acesso partilhado.

6.  Encontre a chave primária da **cadeia de ligação** com a etiqueta da caixa e selecione o botão de cópia ao lado da cadeia de ligação.
 
7.  Cole a cadeia de ligação num editor de texto. Precisa desta cadeia de ligação para a próxima secção depois de fazer algumas pequenas edições.

   A cadeia de ligação é assim:
   
   ```
   Endpoint=sb://EVENTHUBS-NAMESPACE.servicebus.windows.net/;SharedAccessKeyName=socialtwitter-access;SharedAccessKey=Gw2NFZw6r...FxKbXaC2op6a0ZsPkI=;EntityPath=socialtwitter-eh
   ```

   Note que a cadeia de ligação contém múltiplos pares de valores-chave, separados com pontos de ligação: `Endpoint` `SharedAccessKeyName` , , e `SharedAccessKey` `EntityPath` .  

   > [!NOTE]
   > Para a segurança, partes do fio de ligação no exemplo foram removidas.

## <a name="configure-and-start-the-twitter-client-application"></a>Configure e inicie a aplicação do cliente do Twitter

A aplicação do cliente obtém eventos de tweet diretamente do Twitter. Para tal, necessita de autorização para ligar para as APIs de streaming do Twitter. Para configurar essa permissão, cria uma aplicação no Twitter, que gera credenciais únicas (como um token OAuth). Em seguida, pode configurar a aplicação do cliente para usar estas credenciais quando então faz chamadas API. 

### <a name="create-a-twitter-application"></a>Criar uma aplicação do Twitter
Se ainda não tiver uma aplicação do Twitter que possa utilizar para este guia, pode criar uma. Já deve ter uma conta no Twitter.

> [!NOTE]
> O processo exato no Twitter para criar uma aplicação e obter as chaves, segredos e fichas pode mudar. Se estas instruções não corresponderem ao que vê no site do Twitter, consulte a documentação do desenvolvedor do Twitter.

1. A partir de um navegador web, vá ao [Twitter For Developers,](https://developer.twitter.com/en/apps)crie uma conta de desenvolvedor e selecione **Criar uma aplicação**. Pode ver uma mensagem a dizer que precisa de se candidatar a uma conta de desenvolvedor do Twitter. Sinta-se livre para fazê-lo, e depois de a sua candidatura ter sido aprovada, deverá ver um e-mail de confirmação. Pode levar vários dias para ser aprovado para uma conta de desenvolvedor.

   ![A screenshot mostra o botão criar uma aplicação.](./media/stream-analytics-twitter-sentiment-analysis-trends/provide-twitter-app-details.png "Detalhes da aplicação do Twitter")

2. Na página **Criar uma aplicação**, forneça os detalhes da nova aplicação e, em seguida, selecione **Criar a sua aplicação do Twitter**.

   ![O screenshot mostra o painel de detalhes da App onde pode introduzir valores para a sua aplicação.](./media/stream-analytics-twitter-sentiment-analysis-trends/provide-twitter-app-details-create.png "Detalhes da aplicação do Twitter")

3. Na página de aplicação, selecione o separador **Chaves e Fichas** e copie os valores para **Chave API do Consumidor** e Chave Secreta **API do consumidor**. Além disso, **selecione Criar** em **Access Token e Access Token Secret** para gerar os tokens de acesso. Copie os valores de **Token de Acesso** e **Segredo de Token de Acesso**.

   Guarde os valores que obteve da aplicação do Twitter. Precisa dos valores mais tarde.

> [!NOTE]
> As chaves e segredos da aplicação do Twitter dão acesso à sua conta de Twitter. Trate esta informação como sensível, tal como faz a sua palavra-passe no Twitter. Por exemplo, não incorpore esta informação numa aplicação que dê a outros. 

### <a name="configure-the-client-application"></a>Configure a aplicação do cliente

Criámos uma aplicação de clientes que se conecta aos dados do Twitter usando [as APIs de streaming do Twitter](https://dev.twitter.com/streaming/overview) para recolher eventos de tweet sobre um conjunto específico de tópicos.

Antes de a aplicação ser executado, requer certas informações suas, como as teclas do Twitter e a cadeia de ligação do hub do evento.

1. Certifique-se de que descarregou a aplicação [TwitterClientCore,](https://github.com/Azure/azure-stream-analytics/tree/master/DataGenerators/TwitterClientCore) conforme listado nos pré-requisitos.

2. Utilize um editor de texto para abrir o ficheiro *App.config.* Esm faça as seguintes alterações ao `<appSettings>` elemento:

   * Definir `oauth_consumer_key` para a Chave de Consumidor do Twitter (chave API). 
   * Definir `oauth_consumer_secret` para o Twitter Consumer Secret (chave secreta da API).
   * Set `oauth_token` to the Twitter Access Token.
   * Set `oauth_token_secret` to the Twitter Access token secret.
   * Coloque `EventHubNameConnectionString` a corda de ligação.
   * Definir `EventHubName` para o nome do hub do evento (que é o valor do caminho da entidade).

3. Abra a linha de comando e navegue para o diretório onde está localizada a sua aplicação TwitterClientCore. Use o comando `dotnet build` para construir o projeto. Em seguida, use o comando `dotnet run` para executar a aplicação. A aplicação envia Tweets para o seu Centro de Eventos.

## <a name="create-a-stream-analytics-job"></a>Criar uma tarefa do Stream Analytics

Agora que os eventos de tweet estão a ser transmitidos em tempo real a partir do Twitter, podes criar um trabalho de Stream Analytics para analisar estes eventos em tempo real.

1. No portal Azure, navegue para o seu grupo de recursos e selecione **+ Adicionar**. Em seguida, procure o **trabalho de Stream Analytics** e selecione **Criar**.

2. Nomeie o trabalho `socialtwitter-sa-job` e especifique uma subscrição, grupo de recursos e localização.

    É uma boa ideia colocar o trabalho e o centro de eventos na mesma região para melhor desempenho e para que você não pague para transferir dados entre regiões.

3. Selecione **Criar**. Em seguida, navegue para o seu trabalho quando o destacamento estiver terminado.

## <a name="specify-the-job-input"></a>Especificar a entrada de trabalho

1. No seu trabalho stream Analytics, selecione **Inputs** do menu esquerdo em **Job Topology**.

2. Selecione Adicionar o Centro **+ &nbsp; de Eventos** de entrada de fluxo  >  . Preencha o **novo formulário de entrada** com as seguintes informações:

   |**Definição**  |**Valor sugerido**  |**Descrição**  |
   |---------|---------|---------|
   |Alias de entrada| *TwitterStream* | Insira um pseudónimo para a entrada. |
   |Subscrição  | \<Your subscription\> |  Selecione a subscrição do Azure que pretende utilizar. |
   |Espaço de nomes do hub de eventos | *asa-twitter-eventhub* |
   |Nome do Hub de Eventos | *socialtwitter-eh* | Escolha *a utilização existente.* Em seguida, selecione o Centro de Eventos que criou.|
   |Tipo de compressão de eventos| GZip | O tipo de compressão de dados.|

   Deixe os valores predefinidos restantes e **selecione Guardar**.

## <a name="specify-the-job-query"></a>Especificar a consulta de trabalho

Stream Analytics suporta um modelo de consulta simples e declarativo que descreve transformações. Para saber mais sobre o idioma, consulte a [Referência de Idioma Azure Stream Analytics.](/stream-analytics-query/stream-analytics-query-language-reference) Este guia de como guiá-lo ajuda-o a autor e a testar várias consultas através dos dados do Twitter.

Para comparar o número de menções entre tópicos, pode usar uma [janela de Tumbling](/stream-analytics-query/tumbling-window-azure-stream-analytics) para obter a contagem de menções por tópico a cada cinco segundos.

1. No seu trabalho **Visão geral**, selecione **Editar consulta** perto da parte superior direita da caixa de consulta. O Azure lista as entradas e saídas configuradas para o trabalho e permite criar uma consulta para transformar o fluxo de entrada à medida que é enviado para a saída.

2. Altere a consulta no editor de consulta para o seguinte:

   ```sql
   SELECT *
   FROM TwitterStream
   ```

3. Os dados do evento a partir das mensagens devem aparecer na janela **de pré-visualização de Entrada** abaixo da sua consulta. Certifique-se de que a **vista** está definida para **JSON**. Se não vir nenhum dado, certifique-se de que o seu gerador de dados está a enviar eventos para o seu centro de eventos e que selecionou o **GZip** como o tipo de compressão para a entrada.

4. Selecione **a consulta do teste** e note os resultados na janela de **resultados** do Teste abaixo da sua consulta.

5. Altere a consulta no editor de código para a seguinte consulta de **teste:**

   ```sql
   SELECT System.Timestamp as Time, text
   FROM TwitterStream
   WHERE text LIKE '%Azure%'
   ```

6. Esta consulta devolve todos os tweets que incluem a palavra-chave *Azure*.

## <a name="create-an-output-sink"></a>Criar um lavatório de saída

Você definiu agora um fluxo de eventos, uma entrada de centro de eventos para ingerir eventos, e uma consulta para realizar uma transformação sobre o fluxo. O último passo é definir um lavatório de saída para o trabalho.  

Neste guia de como guiar, você escreve os eventos de tweet agregados da consulta de trabalho para o armazenamento de Azure Blob.  Também pode empurrar os seus resultados para a Base de Dados Azure SQL, armazenamento de mesa Azure, Centros de Eventos ou Power BI, dependendo das necessidades da sua aplicação.

## <a name="specify-the-job-output"></a>Especificar a saída de trabalho

1. Sob a secção **Job Topology** no menu de navegação à esquerda, selecione **Outputs**. 

2. Na página **Outputs,** clique em **+ &nbsp; Adicionar** e **Blob storage/Data Lake Storage Gen2**:

   * **Pseudónimo de saída**: Use o nome `TwitterStream-Output` . 
   * **Opções de importação**: **Selecione Selecione o armazenamento das suas subscrições.**
   * **Conta de armazenamento**. Selecione a sua conta de armazenamento.
   * **Recipiente**. **Selecione Criar novo** e insira `socialtwitter` .
   
4. Selecione **Guardar**.   

## <a name="start-the-job"></a>Iniciar a tarefa

Uma entrada de trabalho, consulta e saída são especificados. Está pronto para começar o trabalho stream Analytics.

1. Certifique-se de que a aplicação TwitterClientCore está em execução. 

2. Na visão geral do trabalho, selecione **Start**.

3. Na página iniciar o **trabalho,** para **a hora de início da saída de Trabalho**, selecione De **agora** e, em seguida, selecione **Iniciar**.

## <a name="get-support"></a>Obter suporte
Para obter mais assistência, experimente o nosso [Microsoft Q&Uma página de perguntas para a Azure Stream Analytics](/answers/topics/azure-stream-analytics.html).

## <a name="next-steps"></a>Passos seguintes
* [Introdução ao Azure Stream Analytics](stream-analytics-introduction.md)
* [Começar a utilizar o Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Tarefas de escala do Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Referência do idioma de consulta do Azure Stream Analytics](/stream-analytics-query/stream-analytics-query-language-reference)
* [Referência de API do REST de gestão do Azure Stream Analytics](/rest/api/streamanalytics/)
