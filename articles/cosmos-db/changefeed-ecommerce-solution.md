---
title: Use o feed de mudança de DB Azure Cosmos para visualizar análise de dados em tempo real
description: Este artigo descreve como o feed de mudança pode ser usado por uma empresa de retalho para entender os padrões de utilizador, realizar análise de dados em tempo real e visualização
author: SnehaGunda
ms.service: cosmos-db
ms.devlang: java
ms.topic: conceptual
ms.date: 05/28/2019
ms.author: sngun
ms.openlocfilehash: c0c1a28dc399d3f176f92e656621fec1bc92dbfc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76513514"
---
# <a name="use-azure-cosmos-db-change-feed-to-visualize-real-time-data-analytics"></a>Use o feed de mudança de DB Azure Cosmos para visualizar análise de dados em tempo real

O feed de mudança de DB Azure Cosmos é um mecanismo para obter um feed contínuo e incremental de registos de um contentor Azure Cosmos, uma vez que esses registos estão a ser criados ou modificados. Alterar o suporte de alimentação funciona ouvindo o recipiente para quaisquer alterações. Em seguida, disponibiliza a lista ordenada dos documentos que foram alterados, pela ordem pela qual foram modificados. Para saber mais sobre o feed de mudança, consulte trabalhar com o artigo change [feed.](change-feed.md) 

Este artigo descreve como o feed de mudança pode ser usado por uma empresa de e-commerce para entender os padrões do utilizador, realizar análise saneada de dados em tempo real e visualização. Analisará eventos como um utilizador que vê um item, adiciona um item ao seu carrinho ou compra de um item. Quando um destes eventos ocorre, é criado um novo registo, e os registos de feed de mudança que registam. A mudança de feed desencadeia então uma série de passos que resultam na visualização de métricas que analisam o desempenho e a atividade da empresa. As métricas de amostra que pode visualizar incluem receitas, visitantes únicos do site, itens mais populares, e preço médio dos itens que são vistos versus adicionados a um carrinho versus comprados. Estas métricas de amostra podem ajudar uma empresa de e-commerce a avaliar a popularidade do seu site, desenvolver as suas estratégias de publicidade e preços, e tomar decisões sobre em que inventário investir.

Interessado em ver um vídeo sobre a solução antes de começar, veja o seguinte vídeo:

> [!VIDEO https://www.youtube.com/embed/AYOiMkvxlzo]
>

## <a name="solution-components"></a>Componentes da solução
O diagrama seguinte representa o fluxo de dados e os componentes envolvidos na solução:

![Projeto visual](./media/changefeed-ecommerce-solution/project-visual.png)
 
1. **Geração de Dados:** O simulador de dados é utilizado para gerar dados de retalho que representam eventos como um utilizador que vê um item, adicionando um item ao seu carrinho e comprando um item. Pode gerar um grande conjunto de dados de amostra utilizando o gerador de dados. Os dados da amostra gerados contêm documentos no seguinte formato:
   
   ```json
   {      
     "CartID": 2486,
     "Action": "Viewed",
     "Item": "Women's Denim Jacket",
     "Price": 31.99
   }
   ```

2. **Cosmos DB:** Os dados gerados são armazenados num contentor Azure Cosmos.  

3. **Alterar feed:** O feed de mudança irá ouvir alterações no recipiente Azure Cosmos. Cada vez que um novo documento é adicionado à coleção (isto é, quando ocorre um evento que ocorre como um utilizador visualizando um item, adicionando um item ao seu carrinho, ou comprando um item), o feed de mudança irá desencadear uma [Função Azure](../azure-functions/functions-overview.md).  

4. **Função Azure:** A Função Azure processa os novos dados e envia-os para um Hub de [Eventos Azure.](../event-hubs/event-hubs-about.md)  

5. **Centro de Eventos:** O Azure Event Hub armazena estes eventos e envia-os para o [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md) para realizar uma análise mais aprofundada.  

6. **Análise de Fluxo Azure:** O Azure Stream Analytics define consultas para processar os eventos e realizar análises de dados em tempo real. Estes dados são então enviados para o [Microsoft Power BI](https://docs.microsoft.com/power-bi/desktop-what-is-desktop).  

7. **Power BI:** O Power BI é utilizado para visualizar os dados enviados pelo Azure Stream Analytics. Pode construir um painel para ver como as métricas mudam em tempo real.  

## <a name="prerequisites"></a>Pré-requisitos

* Microsoft .NET Framework 4.7.1 ou superior

* Microsoft .NET Core 2.1 (ou superior)

* Estúdio Visual com desenvolvimento universal da Plataforma Windows, desenvolvimento de ambiente de trabalho .NET e ASP.NET e trabalhos de trabalho de desenvolvimento web

* Subscrição do Microsoft Azure

* Conta Microsoft Power BI

* Descarregue o laboratório de [feed de mudança de Db Azure Cosmos](https://github.com/Azure-Samples/azure-cosmos-db-change-feed-dotnet-retail-sample) do GitHub. 

## <a name="create-azure-resources"></a>Criar recursos do Azure 

Crie os recursos Azure - Azure Cosmos DB, Conta de Armazenamento, Hub de Eventos, Stream Analytics exigido pela solução. Irá implementar estes recursos através de um modelo de Gestor de Recursos Azure. Utilize os seguintes passos para mobilizar estes recursos: 

1. Desloque a política de execução do Windows PowerShell para **sem restrições**. Para tal, abra o **Windows PowerShell como Administrador** e execute os seguintes comandos:

   ```powershell
   Get-ExecutionPolicy
   Set-ExecutionPolicy Unrestricted 
   ```

2. A partir do repositório GitHub que descarregou no passo anterior, navegue para a pasta Do Gestor de **Recursos Do Azure** e abra o ficheiro chamado **ficheiro parameters.json.**  

3. Forneça valores para cosmosdbaccount_name, eventhubnamespace_name, storageaccount_name, parâmetros indicados no ficheiro **parâmetros.json.** Terá de usar os nomes que dá a cada um dos seus recursos mais tarde.  

4. A partir do **Windows PowerShell,** navegue até à pasta Do Gestor de **Recursos Azure** e execute o seguinte comando:

   ```powershell
   .\deploy.ps1
   ```
5. Quando solicitado, introduza o id **de subscrição**do Azure, altere o **feedlab** para o nome do grupo de recursos e **execute 1** para o nome de implementação. Uma vez que os recursos comecem a ser implantados, pode levar até 10 minutos para que esteja concluído.

## <a name="create-a-database-and-the-collection"></a>Criar uma base de dados e a coleção

Agora vai criar uma coleção para realizar eventos de site de e-commerce. Quando um utilizador visualiza um item, adiciona um item ao seu carrinho, ou compra um item, a coleção receberá um registo que inclua a ação ("visualizado", "adicionado" ou "comprado"), o nome do item em causa, o preço do item em causa e o número de identificação do carrinho de utilizador envolvido.

1. Vá ao [portal Azure](https://portal.azure.com/) e encontre a **Conta DB Azure Cosmos** que é criada pela implementação do modelo.  

2. A partir do painel **do Data Explorer,** selecione **New Collection** e preencha o formulário com os seguintes detalhes:  

   * Para o campo **id base de dados,** selecione **Criar novo**, em seguida, introduza a base de **dados changefeedlab**. Deixe a caixa de entrada da base de **dados Provision** desmarcada.  
   * Para o campo id **da Coleção,** introduza **changefeedlabcollection**.  
   * Para o campo **chave de partição,** **introduza /Item**. Isto é sensível ao caso, por isso certifique-se de introduzir corretamente.  
   * Para o campo **de entrada,** insira **10000**.  
   * Selecione o botão **OK**.  

3. Em seguida, criar outra coleção chamada arrendamentos para processamento de **alimentos** para mudança. A coleção de arrendamentos coordena o processamento da mudança de alimentos entre vários trabalhadores. Uma coleção separada é usada para armazenar os arrendamentos com um arrendamento por partição.  

4. Volte ao painel do **Data Explorer** e selecione **New Collection** e preencha o formulário com os seguintes detalhes:

   * Para o id **da Base de Dados,** selecione Utilize o **uso existente**e introduza a base de **dados changefeedlab**.  
   * Para o campo **id da Coleção,** insira **arrendamentos.**  
   * Para **a capacidade de armazenamento,** selecione **Fixed**.  
   * Deixe o campo **de Entrada** definido ao seu valor predefinido.  
   * Selecione o botão **OK**.

## <a name="get-the-connection-string-and-keys"></a>Obtenha a corda de ligação e as chaves

### <a name="get-the-azure-cosmos-db-connection-string"></a>Obtenha a cadeia de ligação Azure Cosmos DB

1. Vá ao [portal Azure](https://portal.azure.com/) e encontre a **Conta DB Azure Cosmos** que é criada pela implementação do modelo.  

2. Navegue para o painel **de Teclas,** copie o STRING DE LIGAÇÃO PRIMÁRIO e copie-o para um bloco de notas ou outro documento a que terá acesso em todo o laboratório. Deve rotulá-lo **Cosmos DB Connection String**. Terá de copiar o fio para o seu código mais tarde, por isso tome nota e lembre-se onde está a armazená-lo.

### <a name="get-the-storage-account-key-and-connection-string"></a>Obtenha a chave da conta de armazenamento e a cadeia de ligação

As Contas de Armazenamento Azure permitem que os utilizadores armazenem dados. Neste laboratório, utilizará uma conta de armazenamento para armazenar dados que são utilizados pela Função Azure. A Função Azure é ativada quando qualquer modificação é feita na recolha.

1. Volte ao seu grupo de recursos e abra a conta de armazenamento que criou anteriormente  

2. Selecione **teclas** de acesso do menu do lado esquerdo.  

3. Copie os valores da **chave 1** para um bloco de notas ou outro documento a que terá acesso em todo o laboratório. Deve rotular a **chave** como **Chave** de Armazenamento e a cadeia de **ligação** como string de **ligação**de armazenamento . Terá de copiar estas cordas para o seu código mais tarde, por isso tome nota e lembre-se onde as está a armazenar.  

### <a name="get-the-event-hub-namespace-connection-string"></a>Obtenha a cadeia de ligação nome space do centro de eventos

Um Azure Event Hub recebe os dados do evento, lojas, processos e reencaminha os dados. Neste laboratório, o Azure Event Hub receberá um documento sempre que ocorrer um novo evento (ou seja, um item é visto por um utilizador, adicionado ao carrinho de um utilizador, ou comprado por um utilizador) e depois irá encaminhar esse documento para o Azure Stream Analytics.

1. Volte ao seu grupo de recursos e abra o Espaço nome do **Event Hub** que criou e nomeou no pré-laboratório.  

2. Selecione políticas de **acesso partilhado** do menu do lado esquerdo.  

3. Selecione **RootManageSharedAccessKey**. Copie a chave primária de **ligação** para um bloco de notas ou outro documento a que terá acesso em todo o laboratório. Deve rotulá-lo de linha de ligação Event **Hub Namespace.** Terá de copiar o fio para o seu código mais tarde, por isso tome nota e lembre-se onde está a armazená-lo.

## <a name="set-up-azure-function-to-read-the-change-feed"></a>Configurar função Azure para ler o feed de mudança

Quando um novo documento é criado, ou um documento atual é modificado num recipiente Cosmos, o feed de mudança adiciona automaticamente esse documento modificado ao seu histórico de alterações de recolha. Agora vai construir e executar uma Função Azure que processa o feed de mudança. Quando um documento é criado ou modificado na coleção que criou, a Função Azure será desencadeada pelo feed de mudança. Em seguida, a Função Azure enviará o documento modificado para o Centro de Eventos.

1. Volte ao repositório que clonou no seu dispositivo.  

2. Clique no ficheiro chamado **ChangeFeedLabSolution.sln** e selecione **Open With Visual Studio**.  

3. Navegue para **local.settings.json** no Estúdio Visual. Em seguida, use os valores que gravou anteriormente para preencher os espaços em branco.  

4. Navegue para **ChangeFeedProcessor.cs.** Nos parâmetros para a função **Executar,** execute as seguintes ações:  

   * Substitua o texto **O SEU NOME DE COLEÇÃO AQUI** pelo nome da sua coleção. Se seguiu instruções anteriores, o nome da sua coleção é changefeedlabcollection.  
   * Substitua o texto **O NOME DE COLEÇÃO DE ARRENDAMENTOS AQUI** com o nome da sua coleção de arrendamentos. Se seguiu instruções anteriores, o nome da sua coleção de arrendamentos é **arrendamento.**  
   * No topo do Visual Studio, certifique-se de que a caixa do Projeto Startup à esquerda da seta verde diz **ChangeFeedFunction**.  
   * Selecione **Iniciar** no topo da página para executar o programa  
   * Pode confirmar que a função está a decorrer quando a aplicação de consola diz "Job host started".

## <a name="insert-data-into-azure-cosmos-db"></a>Insira os dados no Azure Cosmos DB 

Para ver como os processos de feed de mudança processam novas ações num site de e-commerce, têm de simular dados que representem os utilizadores que vêem itens do catálogo de produtos, adicionando esses itens aos seus carrinhos e comprando os itens nos seus carrinhos. Estes dados são arbitrários e com o propósito de replicar os dados de um site de ecommerce seriam.

1. Navegue de volta ao repositório no File Explorer e clique à direita **ChangeFeedFunction.sln** para abri-lo novamente numa nova janela do Estúdio Visual.  

2. Navegue para o ficheiro **App.config.** Dentro `<appSettings>` do bloco, adicione o ponto final e a **chave primária** única da sua conta Azure Cosmos DB que recuperou anteriormente.  

3. Adicione os nomes da **recolha** e **da base de dados.** (Estes nomes devem ser **changefeedlabcollection** e **changefeedlabdatabase,** a menos que opte por nomear o seu de forma diferente.)

   ![Atualizar cordas de ligação](./media/changefeed-ecommerce-solution/update-connection-string.png)
 
4. Guarde as alterações em todos os ficheiros editados.  

5. No topo do Visual Studio, certifique-se de que a caixa **do Projeto Startup** à esquerda da seta verde diz **DataGenerator**. Em seguida, selecione **Iniciar** na parte superior da página para executar o programa.  
 
6. Espere que o programa corra. As estrelas significam que os dados estão a chegar! Mantenha o programa em funcionamento - é importante que muitos dados sejam recolhidos.  

7. Se navegar para o [portal Azure](https://portal.azure.com/) , então para a conta Cosmos DB dentro do seu grupo de recursos, em seguida, para **o Data Explorer**, verá os dados aleatórios importados na sua recolha de **changefeedlab** .
 
   ![Dados gerados no portal](./media/changefeed-ecommerce-solution/data-generated-in-portal.png)

## <a name="set-up-a-stream-analytics-job"></a>Configurar um trabalho de análise de fluxo

O Azure Stream Analytics é um serviço de cloud totalmente gerido para o processamento em tempo real de dados de streaming. Neste laboratório, você usará análises de fluxo para processar novos eventos a partir do Event Hub (isto é, quando um item é visto, adicionado a um carrinho, ou comprado), incorporar esses eventos em análise de dados em tempo real, e enviá-los para Power BI para visualização.

1. Desde o [portal Azure,](https://portal.azure.com/)navegue até ao seu grupo de recursos e depois até ao **streamjob1** (o trabalho de análise de fluxo que criou no pré-laboratório).  

2. Selecione **Inputs** conforme demonstrado abaixo.  

   ![Criar entrada](./media/changefeed-ecommerce-solution/create-input.png)

3. Selecione + Adicione a **entrada do fluxo**. Em seguida, selecione **Event Hub** a partir do menu suspenso.  

4. Preencha o novo formulário de entrada com os seguintes detalhes:

   * No campo de pseudónimo **sinuoso,** introduza a **entrada**.  
   * Selecione a opção para **Select Event Hub a partir das suas subscrições**.  
   * Detete o campo **de subscrição** para a sua subscrição.  
   * No campo espaço de **nome do Event Hub,** introduza o nome do seu Espaço nome do Event Hub que criou durante o pré-laboratório.  
   * No campo de **nome Sem Evento,** selecione a opção de **Utilização existente** e escolha o **event-hub1** a partir do menu suspenso.  
   * Deixe o campo de nome de **política do Event Hub** definido para o seu valor padrão.  
   * Deixe o formato de **serialização do Evento** como **JSON**.  
   * Deixe o **campo de codificação** definido para **UTF-8**.  
   * Deixe o **campo do tipo de compressão do evento** definido para **Nenhum**.  
   * Selecione o botão **Guardar**.

5. Navegue de volta para a página de trabalho de análise de fluxo e selecione **Saídas**.  

6. Selecione **+ Adicionar**. Em seguida, selecione **Power BI** a partir do menu suspenso.  

7. Para criar uma nova saída power BI para visualizar o preço médio, execute as seguintes ações:

   * No campo **de saída,** introduza **a médiaPriceOutput**.  
   * Deixe o campo **de trabalho do Grupo** definido para autorizar a **ligação aos espaços**de trabalho de carga .  
   * No campo de **nome dataset,** introduza **a médiaPreço**.  
   * No campo de **nome da tabela,** introduza **a médiaPreço**.  
   * Selecione o botão **Autorizar** e, em seguida, siga as instruções para autorizar a ligação ao Power BI.  
   * Selecione o botão **Guardar**.  

8. Em seguida, volte ao **streamjob1** e **selecione Editar consulta**.

   ![Editar consulta](./media/changefeed-ecommerce-solution/edit-query.png)
 
9. Colhe a seguinte consulta na janela de consulta. A consulta PREÇO **MÉDIO** calcula o preço médio de todos os itens que são visualizados pelos utilizadores, o preço médio de todos os itens que são adicionados aos carrinhos dos utilizadores, e o preço médio de todos os itens que são comprados pelos utilizadores. Esta métrica pode ajudar as empresas de e-commerce a decidir em que preços vender artigos e em que inventário investir. Por exemplo, se o preço médio dos itens visualizados for muito superior ao preço médio dos artigos comprados, então uma empresa poderá optar por adicionar itens menos dispendiosos ao seu inventário.

   ```sql
   /*AVERAGE PRICE*/      
   SELECT System.TimeStamp AS Time, Action, AVG(Price)  
    INTO averagePriceOutput  
    FROM input  
    GROUP BY Action, TumblingWindow(second,5) 
   ```
10. Em seguida, selecione **Guardar** no canto superior esquerdo.  

11. Volte agora ao **streamjob1** e selecione o botão **Iniciar** na parte superior da página. O Azure Stream Analytics pode demorar alguns minutos a ser iniciado, mas eventualmente verá supor que passa de "Iniciar" para "Running".

## <a name="connect-to-power-bi"></a>Ligar ao Power BI

O Power BI é um conjunto de ferramentas de análise de negócio para analisar os dados e partilhar informações. É um grande exemplo de como pode visualizar estrategicamente os dados analisados.

1. Inscreva-se no Power BI e navegue para **o Meu Espaço de Trabalho** abrindo o menu no lado esquerdo da página.  

2. Selecione **+ Crie** no canto superior direito e, em seguida, selecione **dashboard** para criar um dashboard.  

3. Selecione **+ Adicione azulejo** no canto superior direito.  

4. Selecione Dados de **Streaming Personalizados**e, em seguida, selecione o botão **Seguinte.**  
 
5. Selecione **a médiaPreço** dos **SEUS DATASETS**e, em seguida, selecione **Next**.  

6. No campo Tipo de **Visualização,** escolha o gráfico de **barras agrupada** do menu suspenso. Sob **eixo,** adicione ação. Skip **Legend** sem adicionar nada. Em seguida, sob a secção seguinte chamada **Valor,** adicione **avg**. Selecione **Seguinte,** em seguida, título da sua tabela e selecione **Aplicar**. Devia ver um novo gráfico no seu painel de instrumentos!  

7. Agora, se quiser visualizar mais métricas, pode voltar ao **streamjob1** e criar mais três saídas com os seguintes campos.

   a. **Pseudónimo de saída:** entradaRevenueOutput, dataset name: incomingRevenue, Nome da tabela: incomingRevenue  
   b. **Pseudónimo de saída:** top5Output, nome dataset: top5, Nome de tabela: top5  
   c. **Pseudónimo de saída:** VisitanteCountOutput único, nome dataset: uniqueVisitorCount, nome de mesa: uniqueVisitorCount

   Em seguida, selecione **Editar consultas** e colar as seguintes consultas **acima** daquela que já escreveu.

   ```sql
    /*TOP 5*/
    WITH Counter AS
    (
    SELECT Item, Price, Action, COUNT(*) AS countEvents
    FROM input
    WHERE Action = 'Purchased'
    GROUP BY Item, Price, Action, TumblingWindow(second,30)
    ), 
    top5 AS
    (
    SELECT DISTINCT
    CollectTop(5)  OVER (ORDER BY countEvents) AS topEvent
    FROM Counter
    GROUP BY TumblingWindow(second,30)
    ), 
    arrayselect AS 
    (
    SELECT arrayElement.ArrayValue
    FROM top5
    CROSS APPLY GetArrayElements(top5.topevent) AS arrayElement
    ) 
    SELECT arrayvalue.value.item, arrayvalue.value.price,   arrayvalue.value.countEvents
    INTO top5Output
    FROM arrayselect

    /*REVENUE*/
    SELECT System.TimeStamp AS Time, SUM(Price)
    INTO incomingRevenueOutput
    FROM input
    WHERE Action = 'Purchased'
    GROUP BY TumblingWindow(hour, 1)

    /*UNIQUE VISITORS*/
    SELECT System.TimeStamp AS Time, COUNT(DISTINCT CartID) as uniqueVisitors
    INTO uniqueVisitorCountOutput
    FROM input
    GROUP BY TumblingWindow(second, 5)
   ```
   
   A consulta TOP 5 calcula os 5 itens mais altos, classificados pelo número de vezes que foram comprados. Esta métrica pode ajudar as empresas de e-commerce a avaliar quais os itens mais populares e podem influenciar as decisões de publicidade, preços e inventário da empresa.

   A consulta de RECEITAs calcula as receitas resumindo os preços de todos os itens comprados a cada minuto. Esta métrica pode ajudar as empresas de e-commerce a avaliar o seu desempenho financeiro e também a compreender quais as horas do dia que contribuem para a maioria das receitas. Isto pode ter impacto na estratégia global da empresa, em particular no marketing.

   A consulta UNIQUE VISITORS calcula quantos visitantes únicos estão no site a cada 5 segundos, detetando ID de carrinho único. Esta métrica pode ajudar as empresas de e-commerce a avaliar a sua atividade no site e a planear como adquirir mais clientes.

8. Agora também pode adicionar azulejos para estes conjuntos de dados.

   * Para o Top 5, faria sentido fazer um gráfico de colunas agrupado com os itens como o eixo e a contagem como o valor.  
   * Para as Receitas, faria sentido fazer um gráfico de linha com o tempo como o eixo e a soma dos preços como valor. O prazo para exibir deve ser o maior possível para fornecer o máximo de informação possível.  
   * Para visitantes únicos, faria sentido fazer uma visualização de cartão com o número de visitantes únicos como o valor.

   É assim que um painel de amostras fica com estes gráficos:

   ![VISUALIZAÇÕES](./media/changefeed-ecommerce-solution/visualizations.png)

## <a name="optional-visualize-with-an-e-commerce-site"></a>Opcional: Visualizar com um site de e-commerce

Irá agora observar como pode usar a sua nova ferramenta de análise de dados para se conectar com um verdadeiro site de e-commerce. Para construir o site de e-commerce, utilize uma base de dados Azure Cosmos para armazenar a lista de categorias de produtos (Women's, Men's, Unisex), o catálogo de produtos e uma lista dos itens mais populares.

1. Navegue de volta ao [portal Azure,](https://portal.azure.com/)depois à sua **conta Cosmos DB,** depois ao **Data Explorer.**  

   Adicione duas coleções em**produtos** e **categorias** **changefeedlab** - com capacidade de armazenamento fixo.

   Adicione outra coleção sob a base de **dados changefeedlab** denominada **topItems** e **/Item** como a chave de partição.

2. Selecione a coleção **topItems** e, em **escala e definições,** detete o **Tempo para Viver** até **30 segundos** para que as atualizações topItems a cada 30 segundos.

   ![Time to live](./media/changefeed-ecommerce-solution/time-to-live.png)

3. Para povoar a coleção **topItems** com os itens mais comprados, navegue de volta ao **streamjob1** e adicione uma nova **Saída**. Selecione **Cosmos DB**.

4. Preencha os campos necessários, como se pode ver abaixo.

   ![Produção cosmos](./media/changefeed-ecommerce-solution/cosmos-output.png)
 
5. Se adicionar a consulta opcional top 5 na parte anterior do laboratório, proceda à parte 5a. Se não, proceda à parte 5b.

   5a. No **streamjob1**, selecione **Editar consulta** e colar a seguinte consulta no seu editor de consulta Azure Stream Analytics abaixo da consulta TOP 5 mas acima do resto das consultas.

   ```sql
   SELECT arrayvalue.value.item AS Item, arrayvalue.value.price, arrayvalue.value.countEvents
   INTO topItems
   FROM arrayselect
   ```
   5b. No **streamjob1**, selecione **Editar consulta** e colar a seguinte consulta no seu editor de consulta Azure Stream Analytics acima de todas as outras consultas.

   ```sql
   /*TOP 5*/
   WITH Counter AS
   (
   SELECT Item, Price, Action, COUNT(*) AS countEvents
   FROM input
   WHERE Action = 'Purchased'
   GROUP BY Item, Price, Action, TumblingWindow(second,30)
   ), 
   top5 AS
   (
   SELECT DISTINCT
   CollectTop(5)  OVER (ORDER BY countEvents) AS topEvent
   FROM Counter
   GROUP BY TumblingWindow(second,30)
   ), 
   arrayselect AS 
   (
   SELECT arrayElement.ArrayValue
   FROM top5
   CROSS APPLY GetArrayElements(top5.topevent) AS arrayElement
   ) 
   SELECT arrayvalue.value.item AS Item, arrayvalue.value.price, arrayvalue.value.countEvents
   INTO topItems
   FROM arrayselect
   ```

6. Abra o **EcommerceWebApp.sln** e navegue para o ficheiro **Web.config** no **Solution Explorer**.  

7. Dentro `<appSettings>` do bloco, adicione a CHAVE **URI** e **PRIMARY** que guardou anteriormente onde diz o **seu URI aqui** e a sua chave principal **aqui**. Em seguida, adicione o nome da sua base de **dados** e o nome da **recolha** conforme indicado. (Estes nomes devem ser **changefeedlab** database e **changefeedlabcollection,** a menos que tenha optado por nomear o seu de forma diferente.)

   Preencha o nome da recolha de **produtos,** o nome da **recolha de categorias**e o nome da recolha de artigos de **topo,** conforme indicado. (Estes nomes devem ser **produtos, categorias e topItems,** a menos que tenha optado por nomear o seu de forma diferente.)  

8. Navegue para e abra a **pasta Checkout** dentro **do EcommerceWebApp.sln.** Em seguida, abra o ficheiro **Web.config** dentro dessa pasta.  

9. Dentro `<appSettings>` do bloco, adicione a CHAVE **URI** e **PRIMARY** que guardou anteriormente onde indicado. Em seguida, adicione o nome da sua base de **dados** e o nome da **recolha** conforme indicado. (Estes nomes devem ser **changefeedlab** database e **changefeedlabcollection,** a menos que tenha optado por nomear o seu de forma diferente.)  

10. Pressione **Comece** no topo da página para executar o programa.  

11. Agora pode brincar no site de e-commerce. Quando vir um item, adicionar um item ao seu carrinho, alterar a quantidade de um item no seu carrinho, ou comprar um item, estes eventos serão transmitidos através do feed de mudança de Db Cosmos para Event Hub, ASA, e depois Power BI. Recomendamos que continue a executar dataGenerator para gerar dados significativos de tráfego web e fornecer um conjunto realista de "Hot Products" no site de e-commerce.

## <a name="delete-the-resources"></a>Eliminar os recursos

Para eliminar os recursos que criou durante este laboratório, navegue para o grupo de recursos no [portal Azure,](https://portal.azure.com/)em seguida, selecione **Eliminar o grupo** de recursos do menu no topo da página e seguir as instruções fornecidas.

## <a name="next-steps"></a>Passos seguintes 
  
* Para saber mais sobre o feed de mudança, veja trabalhar com o suporte de feed de [mudança em Azure Cosmos DB](change-feed.md) 
