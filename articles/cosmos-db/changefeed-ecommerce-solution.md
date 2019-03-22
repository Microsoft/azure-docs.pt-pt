---
title: Utilize o Azure Cosmos DB alterar feed para visualizar a análise de dados em tempo real
description: Este artigo descreve como feed de alterações pode ser utilizado por uma empresa de revenda para compreender os padrões de utilizador, efetuar a análise de dados em tempo real e visualização.
author: SnehaGunda
ms.service: cosmos-db
ms.devlang: java
ms.topic: conceptual
ms.date: 08/12/2018
ms.author: sngun
ms.openlocfilehash: 50ae4de8c9c1d10b0b17a9744ffa2340059fc298
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58120521"
---
# <a name="use-azure-cosmos-db-change-feed-to-visualize-real-time-data-analytics"></a>Utilize o Azure Cosmos DB alterar feed para visualizar a análise de dados em tempo real

O feed de alterações do Azure Cosmos DB é um mecanismo para obter um feed de incremental e contínuo de registos de um contentor do Azure Cosmos DB, como os registos estão a ser criado ou modificado. Feed de alterações funciona de suporte através da escuta de contentor para efetuar quaisquer alterações. Em seguida, gera a saída de lista classificada de documentos que foram alteradas pela ordem em que foram modificadas. Para saber mais sobre o feed de alterações, veja [trabalhar com o feed de alterações](change-feed.md) artigo. 

Este artigo descreve como feed de alterações pode ser utilizado por uma empresa de comércio eletrônico para compreender os padrões de utilizador, efetuar a análise de dados em tempo real e visualização. Irá analisar eventos como um utilizador visualizar um item, adicionando um item ao seu carrinho ou compra de um item. Quando um dos seguintes eventos ocorre, é criado um novo registo e registos que registam de feed da alteração. Feed de alterações, em seguida, aciona uma série de passos, resultando na visualização de métricas que analisar o desempenho da empresa e a atividade. As métricas de exemplo que pode ver incluem receita, visitantes de site exclusivo, itens mais populares, e preço médio dos itens que são visualizados adicionado ao carrinho versus adquiridas. Estas métricas de exemplo podem ajudar a avaliar sua popularidade do site, desenvolver seu publicidade e estratégias de preços e tomar decisões sobre o inventário de investir numa empresa de comércio eletrônico.

Interessado em ver um vídeo sobre a solução antes de começar, veja o vídeo seguinte:

> [!VIDEO https://www.youtube.com/embed/AYOiMkvxlzo]
>

## <a name="solution-components"></a>Componentes da solução
O diagrama abaixo representa o fluxo de dados e componentes envolvidos na solução:

![Projeto visual](./media/changefeed-ecommerce-solution/project-visual.png)
 
1. **Geração de dados:** Simulador de dados é utilizado para gerar dados de revenda que representa os eventos como um utilizador de um item de exibição, adicionando um item ao seu carrinho e compra de um item. Pode gerar grande conjunto de dados de exemplo usando o gerador de dados. Os dados de exemplo gerado contém documentos no seguinte formato:
   
   ```json
   {      
     "CartID": 2486,
     "Action": "Viewed",
     "Item": "Women's Denim Jacket",
     "Price": 31.99
   }
   ```

2. **Cosmos DB:** Os dados gerados são lojas numa coleção do Azure Cosmos DB.  

3. **Feed de alterações:** O feed de alterações ouvirá as alterações à coleção do Azure Cosmos DB. Sempre que um novo documento é adicionado à coleção (que é quando um evento ocorre como um utilizador visualizar um item, adicionando um item ao seu carrinho, ou de um item), a alteração feed irá acionar um [função do Azure](../azure-functions/functions-overview.md).  

4. **Função do Azure:** A função do Azure processa os novos dados e envia-o para uma [Hub de eventos do Azure](../event-hubs/event-hubs-about.md).  

5. **Hub de eventos:** O Hub de eventos do Azure armazena estes eventos e envia-lhes [do Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md) para efetuar a análise.  

6. **O Azure Stream Analytics:** O Azure Stream Analytics define consultas para processar os eventos e realizar análises de dados em tempo real. Estes dados, em seguida, são enviados para [Microsoft Power BI](https://docs.microsoft.com/power-bi/desktop-what-is-desktop).  

7. **Power BI:** Power BI é utilizado para visualizar os dados enviados pelo Azure Stream Analytics. Pode criar um dashboard para ver como as métricas de alteram em tempo real.  

## <a name="prerequisites"></a>Pré-requisitos

* Microsoft .NET Framework 4.7.1 ou superior

* Microsoft .NET Core 2.1 (ou posterior)

* Visual Studio com o desenvolvimento de plataforma Universal do Windows, desenvolvimento de área de trabalho do .NET e cargas de trabalho de desenvolvimento do ASP.NET e web

* Subscrição do Microsoft Azure

* Conta do Microsoft Power BI

* Transfira o [laboratório de feed de alterações do Azure Cosmos DB](https://github.com/Azure-Samples/azure-cosmos-db-change-feed-dotnet-retail-sample) do GitHub. 

## <a name="create-azure-resources"></a>Criar recursos do Azure 

Crie recursos do Azure - Azure Cosmos DB, conta de armazenamento, Hub de eventos, necessária para a solução de análise de Stream. Irá implementar estes recursos por meio de um modelo Azure Resource Manager. Utilize os seguintes passos para implementar estes recursos: 

1. Defina a política de execução do Windows PowerShell para **irrestrito**. Para tal, abra **Windows PowerShell como administrador** e execute os seguintes comandos:

   ```powershell
   Get-ExecutionPolicy
   Set-ExecutionPolicy Unrestricted 
   ```

2. O repositório do GitHub que transferiu no passo anterior, navegue para o **do Azure Resource Manager** pasta e, abra o arquivo chamado **Parameters. JSON** ficheiro.  

3. Fornecer valores para cosmosdbaccount_name, eventhubnamespace_name, storageaccount_name, parâmetros, como indicado na **Parameters. JSON** ficheiro. Terá de utilizar os nomes que dar a cada um dos seus recursos mais tarde.  

4. Partir **Windows PowerShell**, navegue para o **do Azure Resource Manager** pasta e execute o seguinte comando:

   ```powershell
   .\deploy.ps1
   ```
5. Quando lhe for pedido, introduza o seu do Azure **ID de subscrição**, **changefeedlab** para o nome do grupo de recursos, e **run1** para o nome da implementação. Assim que os recursos de começam a implementar, poderá demorar até 10 minutos para que este seja concluído.

## <a name="create-a-database-and-the-collection"></a>Criar uma base de dados e coleção

Agora, vai criar uma coleção para manter os eventos de site de comércio eletrônico. Quando um utilizador vê um item, adiciona um item ao seu carrinho ou compra de um item, a coleção irá receber um registo que inclui a ação ("exibidas", "adicionadas" ou "comprados"), o nome do item de envolvidos, o preço do item envolvidos e o número de ID de ao carrinho de utilizador i nvolved.

1. Aceda a [Portal do Azure](https://portal.azure.com/) e localize a **conta do Azure Cosmos DB** que são criadas pela implementação do modelo.  

2. Partir do **Data Explorer** painel, selecione **nova coleção** e preencha o formulário com os seguintes detalhes:  

   * Para o **id de base de dados** campo, selecione **criar nova**, em seguida, introduza **changefeedlabdatabase**. Deixe o **débito de base de dados de aprovisionamento** caixa desmarcada.  
   * Para o **recolha** id, insira **changefeedlabcollection**.  
   * Para o **chave de partição** , insira **/Item**. O que diferencia maiúsculas de minúsculas, pelo que certifique-se de que inseri-lo corretamente.  
   * Para o **débito** , insira **10000**.  
   * Selecione o botão **OK**.  

3. Em seguida, crie outra coleção com o nome **concessões** para processamento de feed de alterações. A coleção de concessões coordena o feed de alterações em várias funções de trabalho de processamento. Uma coleção separada é utilizada para armazenar as concessões com uma concessão por partição.  

4. Retorno para o **Data Explorer** painel e selecione **nova coleção** e preencha o formulário com os seguintes detalhes:

   * Para o **id de base de dados** campo, selecione **utilizar existente**, em seguida, introduza **changefeedlabdatabase**.  
   * Para o **id da coleção** , insira **concessões**.  
   * Para **capacidade de armazenamento**, selecione **Fixed**.  
   * Deixe o **débito** campo definido como o valor predefinido.  
   * Selecione o botão **OK**.

## <a name="get-the-connection-string-and-keys"></a>Obter as chaves e a cadeia de ligação

### <a name="get-the-azure-cosmos-db-connection-string"></a>Obter a cadeia de ligação do Azure Cosmos DB

1. Aceda a [Portal do Azure](https://portal.azure.com/) e localize a **conta do Azure Cosmos DB** que são criadas pela implementação do modelo.  

2. Navegue para o **chaves** painel, copie a cadeia de ligação primária e copie-o para um bloco de notas ou outro documento que terá acesso em todo o laboratório. Deve etiquetá-lo **cadeia de ligação do Cosmos DB**. Terá de copiar a cadeia de caracteres em seu código mais tarde, por isso, tome nota e lembre-se de onde são armazená-los.

### <a name="get-the-storage-account-key-and-connection-string"></a>Obter a cadeia de ligação e a chave da conta de armazenamento

Contas de armazenamento do Azure permitem aos utilizadores armazenar dados. Neste laboratório, irá utilizar uma conta de armazenamento para armazenar dados que são utilizados pela função do Azure. A função do Azure é acionada quando qualquer modificação é efetuada à coleção.

1. Regresse ao seu grupo de recursos e abra a conta de armazenamento que criou anteriormente  

2. Selecione **chaves de acesso** no menu no lado esquerdo.  

3. Copie os valores em **chave 1** para um bloco de notas ou outro documento que terá acesso em todo o laboratório. Deve etiquetar os **chave** como **chave de armazenamento** e o **cadeia de ligação** como **cadeia de ligação de armazenamento**. Terá de copiar essas cadeias de caracteres em seu código mais tarde, por isso, tome nota e lembre-se de onde são armazená-los.  

### <a name="get-the-event-hub-namespace-connection-string"></a>Obter a cadeia de ligação de espaço de nomes de hub de eventos

Um Hub de eventos do Azure recebe os dados de eventos, arquivos, processos e encaminha os dados. Neste laboratório, o Hub de eventos do Azure irá receber um documento sempre que ocorre um novo evento (ou seja, um item é visualizado por um utilizador, adicionado ao carrinho de um utilizador ou comprado através de um utilizador) e, em seguida, irá reencaminhar esse documento para o Azure Stream Analytics.

1. Regresse ao seu grupo de recursos e abra o **espaço de nomes de Hub de eventos** que criou e um nome com o prelab.  

2. Selecione **políticas de acesso partilhado** no menu no lado esquerdo.  

3. Selecione **RootManageSharedAccessKey**. Copiar o **ligação chave primária da cadeia de caracteres** para um bloco de notas ou outro documento que terá acesso em todo o laboratório. Deve etiquetá-lo **espaço de nomes de Hub de eventos** cadeia de ligação. Terá de copiar a cadeia de caracteres em seu código mais tarde, por isso, tome nota e lembre-se de onde são armazená-los.

## <a name="set-up-azure-function-to-read-the-change-feed"></a>Configurar a função do Azure para ler o feed de alterações

Quando é criado um novo documento, ou um documento atual é modificado numa coleção do Cosmos DB, a alteração feed automaticamente adiciona esse documento modificado para o respetivo histórico de alterações de coleções. Agora, irá criar e executar uma função do Azure que processa o feed de alterações. Quando um documento é criado ou modificado na coleção que criou, a função do Azure será acionada pelo feed de alterações. Em seguida, a função do Azure irá enviar o documento modificado para o Hub de eventos.

1. Retornar para o repositório que clonou no seu dispositivo.  

2. Com o botão direito no arquivo chamado **ChangeFeedLabSolution.sln** e selecione **aberta com o Visual Studio**.  

3. Navegue para **Settings** no Visual Studio. Em seguida, utilize os valores que registou anteriormente para preencher os espaços em branco.  

4. Navegue para **ChangeFeedProcessor.cs**. Os parâmetros para o **executar** de função, execute as seguintes ações:  

   * Substitua o texto **sua COLEÇÃO nome aqui** com o nome da sua coleção. Se seguiu as instruções anteriores, o nome da sua coleção é changefeedlabcollection.  
   * Substitua o texto **YOUR concessões COLEÇÃO nome aqui** com o nome da sua coleção de concessões. Se seguiu as instruções anteriores, o nome da sua coleção de concessões é **concessões**.  
   * Na parte superior do Visual Studio, certifique-se de que a caixa de projeto de arranque à esquerda na seta verde diz **ChangeFeedFunction**.  
   * Selecione **iniciar** na parte superior da página para executar o programa  
   * Pode confirmar que a função está em execução quando a aplicação de consola diz "anfitrião de trabalho iniciado".

## <a name="insert-data-into-azure-cosmos-db"></a>Inserir dados na Azure Cosmos DB 

Para ver como feed de alterações processa novas ações num site de comércio eletrônico, tem de simular os dados que representa os utilizadores visualizar os itens a partir do catálogo de produto, adicionando os itens para seus carrinhos e os itens em seus carrinhos de compra. Estes dados são arbitrários e com o objetivo de replicar os dados num comércio eletrónico site teria o seguinte aspeto.

1. Navegue de volta para o repositório no Explorador de ficheiros e, com o botão direito **ChangeFeedFunction.sln** para abri-lo novamente numa nova janela do Visual Studio.  

2. Navegue para o **App. config** ficheiro. Dentro de <appSettings> bloco, adicionar o ponto final e exclusivo **chave primária** da sua conta do Azure Cosmos DB que obteve anteriormente.  

3. Adicione a **coleção** e **base de dados** nomes. (Esses nomes devem ser **changefeedlabcollection** e **changefeedlabdatabase** , a menos que escolher para um nome diferente.)

   ![Atualizar cadeias de ligação](./media/changefeed-ecommerce-solution/update-connection-string.png)
 
4. Guarde as alterações em todos os ficheiros editados.  

5. Na parte superior do Visual Studio, certifique-se de que o **projeto de arranque** caixa à esquerda da seta verde para indica **DataGenerator**. Em seguida, selecione **iniciar** na parte superior da página para executar o programa.  
 
6. Aguarde que o programa seja executado. As estrelas significam que dados vem! Manter o programa em execução – é importante que muitos dados são recolhidos.  

7. Se navegar até [Portal do Azure](https://portal.azure.com/) , em seguida, para o Cosmos DB de contas no seu grupo de recursos, em seguida, para **Data Explorer**, verá os dados aleatório importados para sua  **changefeedlabcollection** .
 
   ![Dados gerados no portal](./media/changefeed-ecommerce-solution/data-generated-in-portal.png)

## <a name="set-up-a-stream-analytics-job"></a>Configurar uma tarefa do stream analytics

O Azure Stream Analytics é um serviço totalmente gerido na cloud para processamento em tempo real de dados de transmissão em fluxo. Neste laboratório, irá utilizar o stream analytics para processar novos eventos do Hub de eventos (ou seja, quando um item é visualizado, adicionado a um carrinho ou adquirido), incorporar esses eventos em análise de dados em tempo real e enviá-los para o Power BI para visualização.

1. Do [Portal do Azure](https://portal.azure.com/), navegue para o grupo de recursos, em seguida, para **streamjob1** (a tarefa do stream analytics que criou no prelab).  

2. Selecione **entradas** como demonstrado a seguir.  

   ![Criar entrada](./media/changefeed-ecommerce-solution/create-input.png)

3. Selecione **+ Adicionar entrada de fluxo**. Em seguida, selecione **Hub de eventos** no menu pendente.  

4. Preencha o formulário de entrada novo com os seguintes detalhes:

   * Na **entrada** alias, insira **entrada**.  
   * Selecione a opção para **selecionar Hub de eventos a partir das subscrições**.  
   * Definir o **subscrição** campo à sua subscrição.  
   * Na **espaço de nomes do Hub de eventos** , insira o nome do seu espaço de nomes de Hub de eventos que criou durante o prelab.  
   * Na **nome do Hub de eventos** campo, selecione a opção para **utilizar existente** e escolha **eventos hub1** no menu pendente.  
   * Deixe **política do Hub de eventos** conjunto de campos de nome para o valor predefinido.  
   * Deixe **formato de serialização de eventos** como **JSON**.  
   * Deixe **campo Encoding** definida como **UTF-8**.  
   * Deixe **tipo de compressão de evento** campo definido como **nenhum**.  
   * Selecione o botão **Guardar**.

5. Navegue de volta para a página da tarefa do stream analytics e selecione **saídas**.  

6. Selecione **+ Adicionar**. Em seguida, selecione **Power BI** no menu pendente.  

7. Para criar uma nova saída do Power BI para visualizar o preço médio, execute as seguintes ações:

   * Na **alias de saída** , insira **averagePriceOutput**.  
   * Deixe o **área de trabalho de grupo** campo definido como **autorizar a ligação a carregar áreas de trabalho**.  
   * Na **nome do conjunto de dados** , insira **averagePrice**.  
   * Na **nome da tabela** , insira **averagePrice**.  
   * Selecione o **autorizar** botão, em seguida, siga as instruções para autorizar a ligação para o Power BI.  
   * Selecione o botão **Guardar**.  

8. Em seguida, volte ao **streamjob1** e selecione **Editar consulta**.

   ![Editar consulta](./media/changefeed-ecommerce-solution/edit-query.png)
 
9. Cole a consulta seguinte na janela da consulta. O **preço médio** consulta calcula o preço médio de todos os itens que são exibidas pelos usuários, o preço médio de todos os itens que são adicionados à carrinhos dos utilizadores e o preço médio de todos os itens são comprados pelos usuários. Esta métrica pode ajudar a decidir quais os preços para vender itens em e o que fazer o inventário de investir em empresas de comércio eletrônico. Por exemplo, se o preço médio de itens visualizados é muito maior do que o preço médio de itens comprados, em seguida, uma empresa pode optar por adicionar itens menos dispendiosos para seu inventário.

   ```sql
   /*AVERAGE PRICE*/      
   SELECT System.TimeStamp AS Time, Action, AVG(Price)  
    INTO averagePriceOutput  
    FROM input  
    GROUP BY Action, TumblingWindow(second,5) 
   ```
10. Em seguida, selecione **guardar** no canto superior esquerdo.  

11. Agora volte ao **streamjob1** e selecione o **iniciar** botão na parte superior da página. O Azure Stream Analytics pode demorar alguns minutos a iniciar a cópia de segurança, mas, eventualmente, verá que ele altere de "Iniciar" para "Em execução".

## <a name="connect-to-power-bi"></a>Ligar ao Power BI

O Power BI é um conjunto de ferramentas de análise de negócio para analisar dados e partilhar informações. É um ótimo exemplo de como estrategicamente, pode visualizar os dados analisados.

1. Inicie sessão no Power BI e navegue para **minha área de trabalho** ao abrir o menu no lado esquerdo da página.  

2. Selecione **+ criar** no canto superior direito e, em seguida, selecione **Dashboard** para criar um dashboard.  

3. Selecione **+ adicionar mosaico** no canto superior direito.  

4. Selecione **dados de transmissão em fluxo personalizados**, em seguida, selecione a **próxima** botão.  
 
5. Selecione **averagePrice** partir **conjuntos de dados YOUR**, em seguida, selecione **seguinte**.  

6. Na **tipo de visualização** campo, escolha **colocar em cluster gráfico de barras** no menu pendente. Sob **eixo**, adicione a ação. Ignorar **legenda** sem adicionar nada. Em seguida, na secção seguinte chamado **valor**, adicione **média**. Selecione **próxima**, em seguida, o gráfico do título e selecione **aplicar**. Deverá ver um novo gráfico no seu dashboard!  

7. Agora, se quiser visualizar mais métricas, pode voltar para **streamjob1** e criar três saídas mais com os seguintes campos.

   a. **Alias de saída:** incomingRevenueOutput, nome do conjunto de dados: incomingRevenue, nome da tabela: incomingRevenue  
   b. **Alias de saída:** top5Output, nome do conjunto de dados: top5, nome da tabela: top5  
   c. **Alias de saída:** uniqueVisitorCountOutput, nome do conjunto de dados: uniqueVisitorCount, nome da tabela: uniqueVisitorCount

   Em seguida, selecione **consulta de edição** e cole as seguintes consultas **acima** aquele que já escreveu.

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
   
   A consulta de TOP 5 calcula os principais 5 itens, classificados pelo número de vezes que foram compradas. Esta métrica pode ajudar as empresas de comércio eletrônico avaliar, quais os itens são mais populares e podem influenciar a publicidade da empresa, preços e decisões de inventário.

   A consulta de receita calcula receita ao somar os preços de todos os itens comprados a cada minuto. Esta métrica pode ajudar as empresas de comércio eletrônico avaliar seu desempenho financeiro e também compreender o que horas do dia que contribuem para a maior parte da receita. Isto pode afetar a estratégia geral da empresa, em particular de marketing.

   A consulta de VISITANTES exclusivos calcula quantos visitantes exclusivos são no site de IDs de 5 em 5 segundos por detectando carrinho exclusivo Esta métrica pode ajudar as empresas de comércio eletrônico avaliar suas atividades de site e delinear como adquirir mais clientes.

8. Agora pode adicionar mosaicos para estes conjuntos de dados também.

   * Para a parte superior 5, faria sentido fazer um gráfico de colunas em cluster com os itens como o eixo e a contagem de como o valor.  
   * Para a receita, faria sentido fazer um gráfico de linhas com o tempo que o eixo e a soma dos preços, como o valor. A janela de tempo para apresentar deve ser o maior possível para fornecer o máximo possível de informações.  
   * Para os visitantes exclusivos, faria sentido fazer uma visualização de cartão com o número de visitantes exclusivos, como o valor.

   Este é o aspeto de um dashboard de exemplo com estes gráficos:

   ![visualizações](./media/changefeed-ecommerce-solution/visualizations.png)

## <a name="optional-visualize-with-an-e-commerce-site"></a>Opcional: Visualizar com um site de comércio eletrónico

Agora irá observar como pode utilizar a nova ferramenta de análise de dados para estabelecer ligação com um site de comércio eletrônico real. Para criar o site de comércio eletrónico, utilize uma base de dados do Azure Cosmos DB para armazenar a lista de categorias de produtos (feminino, masculino, Unisex), o catálogo de produtos e uma lista dos itens mais populares.

1. Navegue de volta para o [Portal do Azure](https://portal.azure.com/), em seguida, para sua **conta do Cosmos DB**, em seguida, para **Data Explorer**.  

   Adicionar duas coleções sob **changefeedlabdatabase** - **produtos** e **categorias** com capacidade de armazenamento fixa.

   Adicionar outra coleção sob **changefeedlabdatabase** com o nome **topItems** e **/Item** como a chave de partição.

2. Selecione o **topItems** coleção e, em **dimensionamento e as definições** definir o **TTL** ser **30 segundos** , de modo que topItems atualiza cada 30 segundos.

   ![Time to live](./media/changefeed-ecommerce-solution/time-to-live.png)

3. Para preencher os **topItems** coleção com comprados com mais freqüência itens, navegue de volta para **streamjob1** e adicionar um novo **saída**. Selecione **Cosmos DB**.

4. Preencha os campos obrigatórios como pictured abaixo.

   ![Saída do cosmos](./media/changefeed-ecommerce-solution/cosmos-output.png)
 
5. Se tiver adicionado a consulta de TOP 5 opcional na parte anterior do laboratório, avance para 5a de parte. Caso contrário, avance para a parte 5b.

   5a. Na **streamjob1**, selecione **Editar consulta** e cole a seguinte consulta no seu editor de consulta do Azure Stream Analytics abaixo a consulta de TOP 5 mas acima o restante das consultas.

   ```sql
   SELECT arrayvalue.value.item AS Item, arrayvalue.value.price, arrayvalue.value.countEvents
   INTO topItems
   FROM arrayselect
   ```
   5b. Na **streamjob1**, selecione **Editar consulta** e cole a seguinte consulta no seu editor de consulta do Azure Stream Analytics acima de tudo outras consultas.

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

6. Open **EcommerceWebApp.sln** e navegue para o **Web. config** de ficheiros a **Explorador de soluções**.  

7. Dentro do `<appSettings>` bloquear, adicione o **URI** e **chave primária** que guardou anteriormente em que se lê **o URI aqui** e **a chave primária aqui**. Em seguida, adicione no seu **nome da base de dados** e **nome da coleção** conforme indicado. (Esses nomes devem ser **changefeedlabdatabase** e **changefeedlabcollection** , a menos que escolheu para um nome diferente.)

   Preencha suas **nome da coleção de produtos**, **nome da coleção de categorias**, e **nome da coleção de itens principais** conforme indicado. (Esses nomes devem ser **produtos, categorias e topItems** , a menos que escolheu para um nome diferente.)  

8. Navegue para e abra o **pasta de Check-out** dentro **EcommerceWebApp.sln.** Em seguida, abra a **Web. config** ficheiro dessa pasta.  

9. Dentro de `<appSettings>` bloquear, adicione o **URI** e **chave primária** que guardou anteriormente onde seja indicado. Em seguida, adicione no seu **nome da base de dados** e **nome da coleção** conforme indicado. (Esses nomes devem ser **changefeedlabdatabase** e **changefeedlabcollection** , a menos que escolheu para um nome diferente.)  

10. Prima **iniciar** na parte superior da página para executar o programa.  

11. Agora pode se divertir no site de comércio eletrônico. Ao ver um item, adicionar um item ao carrinho, alterar a quantidade de um item no carrinho ou comprar um item, esses eventos são passados para o Cosmos DB alterar feed no Hub de eventos, ASA e, em seguida, o Power BI. Recomendamos continuar a executar DataGenerator para gerar dados de tráfego da web significativas e fornecer um conjunto realista de "Produtos de acesso frequente" no site de comércio eletrônico.

## <a name="delete-the-resources"></a>Eliminar os recursos

Para eliminar os recursos que criou durante este laboratório, navegar para o grupo de recursos na [Portal do Azure](https://portal.azure.com/), em seguida, selecione **eliminar grupo de recursos** no menu na parte superior da página e siga as instruções fornecido.

## <a name="next-steps"></a>Passos Seguintes 
  
* Para saber mais sobre o feed de alterações, veja [suporte de feed de trabalhar com a alteração no Azure Cosmos DB](change-feed.md) 
* [Solução de notificação de feed de alterações](change-feed-hl7-fhir-logic-apps.md) para a organização de cuidados de saúde com o Azure Cosmos DB.
