---
title: Use o feed de alteração DB do Azure Cosmos para visualizar a análise de dados em tempo real
description: Este artigo descreve como o feed de mudança pode ser usado por uma empresa de retalho para entender os padrões dos utilizadores, realizar análises de dados em tempo real e visualização
author: SnehaGunda
ms.service: cosmos-db
ms.devlang: java
ms.topic: how-to
ms.date: 05/28/2019
ms.author: sngun
ms.custom: devx-track-java
ms.openlocfilehash: 84a39ade902bd22d67e9b3a7d40b392bfd83dfd3
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92475920"
---
# <a name="use-azure-cosmos-db-change-feed-to-visualize-real-time-data-analytics"></a>Use o feed de alteração DB do Azure Cosmos para visualizar a análise de dados em tempo real

O feed de mudança de DB Azure Cosmos é um mecanismo para obter um feed contínuo e incremental de registos de um contentor Azure Cosmos à medida que esses registos estão sendo criados ou modificados. Mude os trabalhos de suporte de alimentação ouvindo o recipiente para quaisquer alterações. Em seguida, disponibiliza a lista ordenada dos documentos que foram alterados, pela ordem pela qual foram modificados. Para saber mais sobre a mudança de alimentação, consulte [trabalhar com artigo change feed.](change-feed.md) 

Este artigo descreve como o feed de mudança pode ser usado por uma empresa de e-commerce para entender os padrões dos utilizadores, realizar análises de dados em tempo real e visualização. Irá analisar eventos como visualizar um item por utilizador, adicionar um item ao seu carrinho ou comprar um artigo. Quando um destes eventos ocorre, um novo registo é criado, e o feed de alteração regista esse registo. A alteração do feed desencadeia uma série de passos que resultam na visualização de métricas que analisam o desempenho e a atividade da empresa. As métricas de amostra que você pode visualizar incluem receitas, visitantes exclusivos do site, itens mais populares, e preço médio dos itens que são vistos versus adicionados a um carrinho versus comprado. Estas métricas de amostra podem ajudar uma empresa de e-commerce a avaliar a popularidade do seu site, desenvolver as suas estratégias de publicidade e preços e tomar decisões sobre o inventário para investir.

Interessado em ver um vídeo sobre a solução antes de começar, veja o seguinte vídeo:

> [!VIDEO https://www.youtube.com/embed/AYOiMkvxlzo]
>

## <a name="solution-components"></a>Componentes da solução
O diagrama a seguir representa o fluxo de dados e os componentes envolvidos na solução:

:::image type="content" source="./media/changefeed-ecommerce-solution/project-visual.png" alt-text="Projeto visual" border="false":::
 
1. **Geração de Dados:** O simulador de dados é utilizado para gerar dados de retalho que representam eventos como um utilizador que visualiza um item, adiciona um item ao seu carrinho e compra um item. Pode gerar um grande conjunto de dados de amostra utilizando o gerador de dados. Os dados da amostra gerada contêm documentos no seguinte formato:
   
   ```json
   {      
     "CartID": 2486,
     "Action": "Viewed",
     "Item": "Women's Denim Jacket",
     "Price": 31.99
   }
   ```

2. **Cosmos DB:** Os dados gerados são armazenados num contentor Azure Cosmos.  

3. **Alterar o feed:** O feed de mudança ouvirá alterações no recipiente Azure Cosmos. Cada vez que um novo documento é adicionado à coleção (isto é, quando ocorre um evento como um utilizador visualizando um item, adicionando um item ao seu carrinho, ou comprando um item), o feed de alteração irá desencadear uma [Função Azure](../azure-functions/functions-overview.md).  

4. **Função Azure:** A Função Azure processa os novos dados e envia-os para um [Azure Event Hub](../event-hubs/event-hubs-about.md).  

5. **Centro de Eventos:** O Azure Event Hub armazena estes eventos e envia-os para [a Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md) para realizar mais análises.  

6. **Azure Stream Analytics:** Azure Stream Analytics define consultas para processar os eventos e realizar análises de dados em tempo real. Estes dados são então enviados para [o Microsoft Power BI](/power-bi/desktop-what-is-desktop).  

7. **Power BI:** Power BI é utilizado para visualizar os dados enviados pela Azure Stream Analytics. Pode construir um dashboard para ver como as métricas mudam em tempo real.  

## <a name="prerequisites"></a>Pré-requisitos

* Microsoft .NET Framework 4.7.1 ou superior

* Microsoft .NET Core 2.1 (ou superior)

* Estúdio Visual com desenvolvimento universal de plataforma Windows, desenvolvimento de desktop .NET e ASP.NET e trabalhos de desenvolvimento web

* Subscrição do Microsoft Azure

* Conta BI microsoft Power

* Descarregue o [laboratório de feed de mudança de Azure Cosmos dB](https://github.com/Azure-Samples/azure-cosmos-db-change-feed-dotnet-retail-sample) do GitHub. 

## <a name="create-azure-resources"></a>Criar recursos do Azure 

Criar os recursos Azure - Azure Cosmos DB, conta de armazenamento, Event Hub, Stream Analytics exigido pela solução. Irá utilizar estes recursos através de um modelo de Gestor de Recursos Azure. Utilize as seguintes medidas para a implantação destes recursos: 

1. Desaprote a política de execução do Windows PowerShell para **sem restrições**. Para tal, abra **o Windows PowerShell como administrador** e execute os seguintes comandos:

   ```powershell
   Get-ExecutionPolicy
   Set-ExecutionPolicy Unrestricted 
   ```

2. A partir do repositório GitHub que descarregou no passo anterior, navegue para a pasta **Azure Resource Manager** e abra o ficheiro chamado **parameters.jsno** ficheiro.  

3. Fornecer valores para cosmosdbaccount_name, eventhubnamespace_name, storageaccount_name, parâmetros como indicado em **parameters.jsficheiro.** Terá de usar os nomes que dá a cada um dos seus recursos mais tarde.  

4. A partir do **Windows PowerShell,** navegue até à pasta **Azure Resource Manager** e execute o seguinte comando:

   ```powershell
   .\deploy.ps1
   ```
5. Quando solicitado, insira o seu **ID de subscrição**Azure, altere o **feedlab** para o nome do grupo de recursos e **corra 1** para o nome de implementação. Uma vez que os recursos comecem a ser implantados, pode levar até 10 minutos para que esteja concluído.

## <a name="create-a-database-and-the-collection"></a>Criar uma base de dados e a coleção

Irá agora criar uma coleção para realizar eventos do site de e-commerce. Quando um utilizador vê um item, adiciona um item ao seu carrinho, ou compra um item, a coleção receberá um registo que inclui a ação ("visualizada", "adicionada" ou "comprada"), o nome do item envolvido, o preço do artigo envolvido e o número de identificação do carrinho de utilizador envolvido.

1. Vá ao [portal Azure](https://portal.azure.com/) e encontre a **Conta DB Azure Cosmos** que é criada pela implementação do modelo.  

2. A partir do painel **data Explorer,** selecione **New Collection** e preencha o formulário com os seguintes detalhes:  

   * Para o campo **de id de dados de dados,** selecione **Criar novo**e, em seguida, insira a base **de dados changefeedlab**. Deixe a caixa **de produção da base de dados Desmarcada.**  
   * Para o campo de id **Coleção,** insira **a recolha changefeedlab**.  
   * Para o campo **de chaves partição,** **introduza /Item**. Isto é sensível a casos, por isso certifique-se de que o introduz corretamente.  
   * Para o campo **de produção,** insira **10000**.  
   * Selecione o botão **OK**.  

3. Em seguida, crie outra coleção chamada **arrendamentos** para alterar o processamento de feed. A coleção de arrendamentos coordena o processamento da mudança de alimentos em vários trabalhadores. Uma coleção separada é usada para armazenar os arrendamentos com um arrendamento por partição.  

4. Volte ao painel do **Data Explorer** e selecione **Nova Coleção** e preencha o formulário com os seguintes detalhes:

   * Para o campo **de id de base de dados,** selecione **Utilizar a base de**dados do **changefeedlab**.  
   * Para o campo **de id Coleção,** **insira os contratos de arrendamento.**  
   * Para **capacidade de armazenamento**, selecione **Fixo**.  
   * Deixe o campo **de produção** definido para o seu valor predefinido.  
   * Selecione o botão **OK**.

## <a name="get-the-connection-string-and-keys"></a>Obtenha a corda de ligação e as teclas

### <a name="get-the-azure-cosmos-db-connection-string"></a>Obtenha a cadeia de conexão DB Azure Cosmos

1. Vá ao [portal Azure](https://portal.azure.com/) e encontre a **Conta DB Azure Cosmos** que é criada pela implementação do modelo.  

2. Navegue no painel **de chaves,** copie a STRING DE LIGAÇÃO PRIMÁRIA e copie-a para um bloco de notas ou outro documento a que terá acesso em todo o laboratório. Deve rotulá-lo **Cosmos DB Connection String**. Terá de copiar a cadeia para o seu código mais tarde, por isso tome uma nota e lembre-se onde está a guardá-la.

### <a name="get-the-storage-account-key-and-connection-string"></a>Obtenha a chave da conta de armazenamento e a cadeia de conexão

As Contas de Armazenamento Azure permitem aos utilizadores armazenar dados. Neste laboratório, utilizará uma conta de armazenamento para armazenar dados utilizados pela Função Azure. A Função Azure é acionada quando qualquer modificação é feita na coleção.

1. Volte ao seu grupo de recursos e abra a conta de armazenamento que criou anteriormente  

2. Selecione **as teclas** de acesso do menu do lado esquerdo.  

3. Copie os valores na **tecla 1** para um bloco de notas ou outro documento a que terá acesso em todo o laboratório. Deve rotular a **chave** como **chave de armazenamento** e a cadeia de **ligação** como **string de ligação de armazenamento.** Terá de copiar estas cordas para o seu código mais tarde, por isso tome uma nota e lembre-se onde as armazena.  

### <a name="get-the-event-hub-namespace-connection-string"></a>Obtenha a cadeia de conexão de espaço de nome do centro de eventos

Um Azure Event Hub recebe os dados do evento, lojas, processos e reencaminha os dados. Neste laboratório, o Azure Event Hub receberá um documento sempre que um novo evento ocorrer (isto é, um item é visto por um utilizador, adicionado ao carrinho de um utilizador, ou comprado por um utilizador) e, em seguida, irá encaminhar esse documento para a Azure Stream Analytics.

1. Volte ao seu grupo de recursos e abra o **Event Hub Namespace** que criou e nomeou no pré-labão.  

2. Selecione políticas de **acesso compartilhado** do menu do lado esquerdo.  

3. **Selecione RootManageSharedAccessKey**. Copie a **chave primária de ligação** para um bloco de notas ou outro documento a que terá acesso em todo o laboratório. Deve rotulá-lo a cadeia de ligação **Event Hub Namespace.** Terá de copiar a cadeia para o seu código mais tarde, por isso tome uma nota e lembre-se onde está a guardá-la.

## <a name="set-up-azure-function-to-read-the-change-feed"></a>Configurar a Função Azure para ler o feed de alteração

Quando um novo documento é criado, ou um documento atual é modificado num recipiente Cosmos, o feed de alteração adiciona automaticamente esse documento modificado ao seu histórico de mudanças de recolha. Irá agora construir e executar uma Função Azure que processa o feed de alteração. Quando um documento é criado ou modificado na coleção que criou, a Função Azure será desencadeada pelo feed de alteração. Em seguida, a Função Azure enviará o documento modificado para o Centro de Eventos.

1. Volte ao repositório que clonou no seu dispositivo.  

2. Clique com o botão direito no ficheiro chamado **ChangeFeedLabSolution.sln** e **selecione Open With Visual Studio**.  

3. Navegue para **local.settings.jsno** Estúdio Visual. Em seguida, utilize os valores que gravou anteriormente para preencher os espaços em branco.  

4. Navegue até **ChangeFeedProcessor.cs.** Nos parâmetros para a função **Executar,** execute as seguintes ações:  

   * Substitua o texto **O SEU NOME DE COLEÇÃO AQUI** pelo nome da sua coleção. Se seguiu instruções anteriores, o nome da sua coleção é changefeedlabcollection.  
   * Substitua o texto **O NOME DA COLEÇÃO DOS ARRENDAMENTOS AQUI** pelo nome da sua coleção de arrendamentos. Se seguiu instruções anteriores, o nome da sua coleção de arrendamentos é **arrendamentos.**  
   * No topo do Visual Studio, certifique-se de que a caixa do Projeto Startup à esquerda da seta verde diz **ChangeFeedFunction**.  
   * Selecione **Iniciar**  no topo da página para executar o programa  
   * Pode confirmar que a função está a decorrer quando a aplicação da consola diz "O anfitrião de trabalho começou".

## <a name="insert-data-into-azure-cosmos-db"></a>Inserir dados no Azure Cosmos DB 

Para ver como a mudança de feed processa novas ações num site de e-commerce, tem de simular dados que representem os utilizadores a visualizar itens do catálogo de produtos, adicionando esses itens aos seus carrinhos e comprando os itens nos seus carrinhos. Estes dados são arbitrários e com o propósito de replicar quais os dados num site de Ecommerce.

1. Volte ao repositório no File Explorer e clique com o botão direito **ChangeFeedFunction.sln** para abri-lo novamente numa nova janela do Estúdio Visual.  

2. Navegue para o ficheiro **App.config. ** Dentro do `<appSettings>` bloco, adicione o ponto final e **chave PRIMÁRIA** única que da sua conta DB Azure Cosmos que recuperou anteriormente.  

3. Adicione os nomes da **recolha** e **da base de dados.** (Estes nomes devem ser **changefeedlabcollection** e **changefeedlabdatabase,** a menos que escolha nomear o seu de forma diferente.)

   :::image type="content" source="./media/changefeed-ecommerce-solution/update-connection-string.png" alt-text="Projeto visual":::
 
4. Guarde as alterações em todos os ficheiros editados.  

5. No topo do Visual Studio, certifique-se de que a caixa **do Projeto Startup** à esquerda da seta verde diz **DataGenerator**. Em seguida, **selecione Iniciar** no topo da página para executar o programa.  
 
6. Espera que o programa corra. As estrelas significam que os dados estão a chegar! Mantenha o programa em funcionamento - é importante que muitos dados são recolhidos.  

7. Se navegar para o [portal Azure,](https://portal.azure.com/) em seguida, para a conta Cosmos DB dentro do seu grupo de recursos, em seguida, para **Data Explorer**, você verá os dados aleatórios importados na sua recolha **de changefeedlab** .
 
   :::image type="content" source="./media/changefeed-ecommerce-solution/data-generated-in-portal.png" alt-text="Projeto visual":::

## <a name="set-up-a-stream-analytics-job"></a>Crie um trabalho de análise de fluxo

O Azure Stream Analytics é um serviço de nuvem totalmente gerido para o processamento em tempo real de dados de streaming. Neste laboratório, você usará a análise de stream para processar novos eventos a partir do Centro de Eventos (isto é, quando um item é visto, adicionado a um carrinho, ou comprado), incorporar esses eventos em análise de dados em tempo real, e enviá-los para Power BI para visualização.

1. A partir do [portal Azure,](https://portal.azure.com/)navegue até ao seu grupo de recursos e, em seguida, para **streamjob1** (o trabalho de análise de fluxo que criou no pré-labão).  

2. Selecione **Entradas** como demonstrado abaixo.  

   :::image type="content" source="./media/changefeed-ecommerce-solution/create-input.png" alt-text="Projeto visual":::

3. Selecione **+ Adicionar entrada de fluxo**. Em seguida, selecione **Event Hub** no menu suspenso.  

4. Preencha o novo formulário de entrada com os seguintes detalhes:

   * No campo de alias **Entrada,** insira **a entrada**.  
   * Selecione a opção **para Select Event Hub a partir das suas subscrições.**  
   * Desa estação de **Subscrição.**  
   * No campo de **espaço de nomes Do Event Hub,** insira o nome do seu Espaço De Nome do Centro de Eventos que criou durante o pré-evento.  
   * No campo **de nomes do Event Hub,** selecione a opção para utilizar o centro de **eventos existente** e escolha o **evento-hub1** do menu suspenso.  
   * Deixe o campo de nome **de política do Event Hub** definido para o seu valor padrão.  
   * Deixe **o formato de serialização do Evento** como **JSON**.  
   * Deixe **o campo de codificação** definido para **UTF-8**.  
   * Deixe o campo **de compressão do evento** definido para **Nenhum**.  
   * Selecione o botão **Guardar**.

5. Volte para a página de trabalho de streaming de análise e selecione **Outputs**.  

6. Selecione **+ Adicionar**. Em seguida, selecione **Power BI** do menu suspenso.  

7. Para criar uma nova saída power bi para visualizar o preço médio, execute as seguintes ações:

   * No campo **de alias Output,** **introduza o Preço Médio do Preço .**  
   * Deixe o campo **de espaço de trabalho do Grupo** definido para autorizar a **ligação aos espaços de trabalho de carga**.  
   * No campo de **nomes dataset,** insira **o Preço Médio**.  
   * No campo nome de **tabela,** insira **a média de Preço médio**.  
   * Selecione o botão **Authorize** e, em seguida, siga as instruções para autorizar a ligação ao Power BI.  
   * Selecione o botão **Guardar**.  

8. Em seguida, volte ao **streamjob1** e **selecione a consulta de edição**.

   :::image type="content" source="./media/changefeed-ecommerce-solution/edit-query.png" alt-text="Projeto visual" para "Running".

## <a name="connect-to-power-bi"></a>Ligar ao Power BI

O Power BI é um conjunto de ferramentas de análise de negócio para analisar os dados e partilhar informações. É um grande exemplo de como se pode visualizar estrategicamente os dados analisados.

1. Inscreva-se no Power BI e navegue para **o Meu Espaço de Trabalho** abrindo o menu no lado esquerdo da página.  

2. Selecione **+ Crie** no canto superior direito e, em seguida, selecione **Dashboard** para criar um dashboard.  

3. **Selecione + Adicione azulejo** no canto superior direito.  

4. Selecione **dados de streaming personalizados**e, em seguida, selecione o botão **Seguinte.**  
 
5. Selecione **a média Deprice** a partir dos **seus conjuntos de dados**e, em seguida, selecione **Seguinte**.  

6. No campo **Tipo visualização,** escolha o gráfico de **barras agrupados** no menu suspenso. Sob **o Eixo,** adicione ação. Skip **Legend** sem adicionar nada. Em seguida, na secção seguinte chamada **Value,** adicione **avg**. Selecione **Seguinte,** em seguida, titulo a sua tabela e **selecione Aplicar**. Devia ver um novo gráfico no seu painel!  

7. Agora, se quiser visualizar mais métricas, pode voltar ao **streamjob1** e criar mais três saídas com os seguintes campos.

   a. **Pseudónimo de saída:** incomingRevenueOutput, dataset name: incomingRevenue, Table name: incomingRevenue  
   b. **Pseudónimo de saída:** top5Output, nome dataset: top5, nome da tabela: top5  
   c. **Pseudónimo de saída:** uniqueVisitorCountOutput, dataset name: uniqueVisitorCount, Table name: uniqueVisitorCount

   Em seguida, **selecione Editar consulta** e cole as seguintes consultas **acima** da que já escreveu.

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

   A consulta de RECEITA calcula as receitas somando os preços de todos os itens comprados a cada minuto. Esta métrica pode ajudar as empresas de e-commerce a avaliar o seu desempenho financeiro e também compreender que horas do dia contribuem para a maioria das receitas. Isto pode ter impacto na estratégia global da empresa, em particular no marketing.

   A consulta DE VISITANTES ÚNICOS calcula quantos visitantes únicos estão no site a cada 5 segundos, detetando iDs de carrinhos únicos. Esta métrica pode ajudar as empresas de e-commerce a avaliar a sua atividade no site e a planear como adquirir mais clientes.

8. Agora também pode adicionar azulejos para estes conjuntos de dados.

   * Para o Top 5, faria sentido fazer um gráfico de coluna agrupado com os itens como eixo e a contagem como o valor.  
   * Para as receitas, faria sentido fazer um gráfico de linha com o tempo como o eixo e a soma dos preços como o valor. A janela de tempo para visualizar deve ser a maior possível para fornecer o máximo de informação possível.  
   * Para visitantes únicos, faria sentido fazer uma visualização de cartão com o número de visitantes únicos como o valor.

   É assim que um painel de amostras parece com estes gráficos:

   :::image type="content" source="./media/changefeed-ecommerce-solution/visualizations.png" alt-text="Projeto visual":::

## <a name="optional-visualize-with-an-e-commerce-site"></a>Opcional: Visualizar com um site de e-commerce

Irá agora observar como pode utilizar a sua nova ferramenta de análise de dados para se conectar com um site de e-commerce real. Para construir o site de e-commerce, utilize uma base de dados da Azure Cosmos para armazenar a lista de categorias de produtos (Women's, Men's, Unisex), o catálogo de produtos e uma lista dos itens mais populares.

1. Volte ao [portal Azure,](https://portal.azure.com/)depois para a sua **conta Cosmos DB,** depois para o **Data Explorer**.  

   Adicione duas coleções em produtos e categorias **de changefeedfeedlabdatabase**  -  **products** com capacidade de armazenamento fixo. **categories**

   Adicione outra coleção sob **a base de dados changefeedlab** nomeada **topItems** e **/Item** como chave de partição.

2. Selecione a coleção **topItems** e em **Escala e Definições** definir a **hora de viver** para ser de **30 segundos** para que topItems atualiza a cada 30 segundos.

   :::image type="content" source="./media/changefeed-ecommerce-solution/time-to-live.png" alt-text="Projeto visual":::

3. Para preencher a coleção **topItems** com os itens mais comprados, volte ao **streamjob1** e adicione uma nova **Saída**. Selecione **Cosmos DB**.

4. Preencha os campos necessários, conforme ilustrado abaixo.

   :::image type="content" source="./media/changefeed-ecommerce-solution/cosmos-output.png" alt-text="Projeto visual":::
 
5. Se adicionar a consulta opcional TOP 5 na parte anterior do laboratório, proceda à parte 5a. Caso contrário, proceda à parte 5b.

   5a. No **streamjob1**, **selecione Editar consulta** e cole a seguinte consulta no seu editor de consulta Azure Stream Analytics abaixo da consulta TOP 5, mas acima do resto das consultas.

   ```sql
   SELECT arrayvalue.value.item AS Item, arrayvalue.value.price, arrayvalue.value.countEvents
   INTO topItems
   FROM arrayselect
   ```
   5b. No **streamjob1**, **selecione Editar consulta** e cole a seguinte consulta no seu editor de consulta Azure Stream Analytics acima de todas as outras consultas.

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

6. Abra **o EcommerceWebApp.sln** e navegue para o ficheiro **Web.config** no **Solution Explorer**.  

7. Dentro do `<appSettings>` bloco, adicione o **URI** e **a CHAVE PRIMÁRIA** que guardou anteriormente, onde diz o seu **URI aqui** e a sua chave principal **aqui**. Em seguida, adicione o nome da sua **base de dados** e **o nome da recolha** como indicado. (Estes nomes devem ser **changefeedlabdatabase** e **changefeedlabcollection,** a menos que tenha optado por nomear o seu de forma diferente.)

   Preencha o nome da recolha dos seus **produtos,** **nome de coleção de categorias**e **nome de recolha de artigos de topo,** conforme indicado. (Estes nomes devem ser **produtos, categorias e topItems,** a menos que tenha optado por nomear o seu de forma diferente.)  

8. Navegue para abrir e abrir a **pasta Checkout** dentro **do EcommerceWebApp.sln.** Em seguida, abra o ficheiro **Web.config** dentro dessa pasta.  

9. Dentro do `<appSettings>` bloco, adicione o **URI** e **a CHAVE PRIMÁRIA** que guardou anteriormente, onde indicado. Em seguida, adicione o nome da sua **base de dados** e **o nome da recolha** como indicado. (Estes nomes devem ser **changefeedlabdatabase** e **changefeedlabcollection,** a menos que tenha optado por nomear o seu de forma diferente.)  

10. **Pressione Iniciar** no topo da página para executar o programa.  

11. Agora pode brincar no site de e-commerce. Quando visualizar um item, adicione um item ao seu carrinho, altere a quantidade de um item no seu carrinho, ou compre um item, estes eventos serão transmitidos através do Feed de alteração do Cosmos DB para Event Hub, ASA e, em seguida, Power BI. Recomendamos que continue a executar o DataGenerator para gerar dados significativos de tráfego web e fornecer um conjunto realista de "Hot Products" no site de e-commerce.

## <a name="delete-the-resources"></a>Eliminar os recursos

Para eliminar os recursos que criou durante este laboratório, navegue para o grupo de recursos no [portal Azure](https://portal.azure.com/), em seguida, selecione **Delete grupo** de recursos do menu no topo da página e siga as instruções fornecidas.

## <a name="next-steps"></a>Passos seguintes 
  
* Para saber mais sobre a mudança de feed, consulte [trabalhar com o suporte de feed de mudança em Azure Cosmos DB](change-feed.md)