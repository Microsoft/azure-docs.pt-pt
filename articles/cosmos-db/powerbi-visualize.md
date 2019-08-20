---
title: Tutorial de Power BI para o conector de Azure Cosmos DB
description: Use este Power BI tutorial para importar o JSON, criar relatórios criteriosos e Visualizar dados usando o Azure Cosmos DB e o conector Power BI.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: sngun
ms.openlocfilehash: 1dbdd428a54ebf38c7b880bb9530935c0f748226
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/19/2019
ms.locfileid: "69616813"
---
# <a name="visualize-azure-cosmos-db-data-by-using-the-power-bi-connector"></a>Visualizar dados de Azure Cosmos DB usando o conector de Power BI

[Power bi](https://powerbi.microsoft.com/) é um serviço online no qual você pode criar e compartilhar dashboards e relatórios. O Power BI Desktop é uma ferramenta de criação de relatórios que lhe permite obter dados de várias origens de dados. O Azure Cosmos DB é uma das origens de dados que pode utilizar com o Power BI Desktop. Pode ligar o Power BI Desktop à conta do Azure Cosmos DB com o conector do Azure Cosmos DB para Power BI.  Depois de importar os dados do Azure Cosmos DB para o Power BI, pode transformá-los, criar relatórios e publicar os relatórios no Power BI.   

Este artigo descreve os passos necessários para se ligar à conta do Azure Cosmos DB para o Power BI Desktop. Após a ligação, navegue para uma coleção, extraia os dados, transforme os dados JSON num formato tabular e publique um relatório no Power BI.

> [!NOTE]
> O conector de Power BI para Azure Cosmos DB se conecta ao Power BI Desktop. Os relatórios criados no Power BI Desktop podem ser publicados em PowerBI.com. A extração direta de dados de Azure Cosmos DB não pode ser executada em PowerBI.com. 

> [!NOTE]
> Atualmente, há suporte para a conexão com o Azure Cosmos DB com o conector do Power BI somente para as contas da API do SQL e da API Azure Cosmos DB do Gremlin.

## <a name="prerequisites"></a>Pré-requisitos
Antes de seguir as instruções neste Power BI tutorial, verifique se você tem acesso aos seguintes recursos:

* [Baixe a versão mais recente do Power bi desktop](https://powerbi.microsoft.com/desktop).

* Baixe os [dados de exemplo do vulcão](https://github.com/Azure-Samples/azure-cosmos-db-sample-data/blob/master/SampleData/VolcanoData.json) do github.

* [Crie uma conta de banco de dados Cosmos do Azure](https://azure.microsoft.com/documentation/articles/create-account/) e importe os dados do vulcão usando a [ferramenta de migração de Azure Cosmos DB data](import-data.md). Ao importar dados, considere as seguintes configurações para a origem e os destinos na ferramenta de migração de dados:

   * **Parâmetros de origem** 

       * **Importar de:** Arquivo (s) JSON

   * **Parâmetros de destino** 

      * **Cadeia de conexão:** `AccountEndpoint=<Your_account_endpoint>;AccountKey=<Your_primary_or_secondary_key>;Database= <Your_database_name>` 

      * **Chave de partição:** /Country 

      * **Taxa de transferência da coleção:** 1000 

Para compartilhar seus relatórios no PowerBI.com, você deve ter uma conta no PowerBI.com.  Para saber mais sobre Power BI e Power BI Pro, consulte [https://powerbi.microsoft.com/pricing](https://powerbi.microsoft.com/pricing).

## <a name="lets-get-started"></a>Vamos começar
Neste tutorial, vamos imaginar que você é um Geologist estudar Volcanoes em todo o mundo. Os dados de vulcão são armazenados em uma conta de Azure Cosmos DB e o formato de documento JSON é o seguinte:

    {
        "Volcano Name": "Rainier",
           "Country": "United States",
          "Region": "US-Washington",
          "Location": {
            "type": "Point",
            "coordinates": [
              -121.758,
              46.87
            ]
          },
          "Elevation": 4392,
          "Type": "Stratovolcano",
          "Status": "Dendrochronology",
          "Last Known Eruption": "Last known eruption from 1800-1899, inclusive"
    }

Você recuperará os dados de vulcão da conta de Azure Cosmos DB e visualizará os dados em um relatório de Power BI interativo.

1. Execute Power BI Desktop.

2. Você pode **obter dados**, ver as **fontes recentes**ou **abrir outros relatórios** diretamente na tela de boas-vindas. Selecione o "X" no canto superior direito para fechar a tela. A exibição de **relatório** de Power bi desktop é exibida.
   
   ![Power BI Desktop vista de relatório - conector do Power BI](./media/powerbi-visualize/power_bi_connector_pbireportview.png)

3. Selecione a faixa de opções **página inicial** e clique em **obter dados**.  A janela **obter dados** deve ser exibida.

4. Clique em **Azure**, selecione **Azure Cosmos dB (beta)** e, em seguida, clique em **conectar**. 

    ![Power BI Desktop obter o conector de Power BI de dados](./media/powerbi-visualize/power_bi_connector_pbigetdata.png)   

5. Na página do **conector de visualização** , clique em **continuar**. A janela **Azure Cosmos DB** é exibida.

6. Especifique a URL de ponto de extremidade da conta de Azure Cosmos DB da qual você gostaria de recuperar os dados, conforme mostrado abaixo e clique em **OK**. Para usar sua própria conta, você pode recuperar a URL da caixa URI na folha **chaves** da portal do Azure. Opcionalmente, você pode fornecer o nome do banco de dados, o nome da coleção ou usar o navegador para selecionar o banco de dados e a coleção para identificar de onde vêm o dado.
   
7. Se você estiver se conectando a esse ponto de extremidade pela primeira vez, a chave da conta será solicitada. Para sua própria conta, recupere a chave da caixa **chave primária** na folha **chaves somente leitura** do portal do Azure. Insira a chave apropriada e clique em **conectar**.
   
   Recomendamos que você use a chave somente leitura ao criar relatórios. Isso impede a exposição desnecessária da chave mestra a possíveis riscos de segurança. A chave somente leitura está disponível na folha **chaves** do portal do Azure. 
    
8. Quando a conta for conectada com êxito, o painel **navegador** será exibido. O **navegador** mostra uma lista de bancos de dados na conta.

9. Clique e expanda no banco de dados de onde vem o relatório, selecione **volcanodb** (o nome do banco de dado pode ser diferente).   

10. Agora, selecione uma coleção que contenha os dados a serem recuperados, selecione **volcano1** (o nome da coleção pode ser diferente).
    
    O painel de visualização mostra uma lista de itens de **registro** .  Um documento é representado como um tipo de **registro** em Power bi. Da mesma forma, um bloco JSON aninhado dentro de um documento também é um **registro**.
    
    ![Tutorial de Power BI para a janela conector do Azure Cosmos DB Power BI-navegador](./media/powerbi-visualize/power_bi_connector_pbinavigator.png)
12. Clique em **Editar** para iniciar o editor de consultas em uma nova janela para transformar os dados.

## <a name="flattening-and-transforming-json-documents"></a>Mesclando e transformando documentos JSON
1. Alterne para a janela do editor de consultas Power BI, em que a coluna **documento** no painel central.
   ![Editor de consultas Power BI Desktop](./media/powerbi-visualize/power_bi_connector_pbiqueryeditor.png)
2. Clique no expansor no lado direito do cabeçalho da coluna do **documento** .  O menu de contexto com uma lista de campos será exibido.  Selecione os campos necessários para o relatório, por exemplo, nome do vulcão, país, região, local, elevação, tipo, status e último erupção de conhecimento. Desmarque a caixa **usar nome da coluna original como prefixo** e clique em **OK**.
   
    ![Tutorial de Power BI para Azure Cosmos DB conector de Power BI – expandir documentos](./media/powerbi-visualize/power_bi_connector_pbiqueryeditorexpander.png)
3. O painel central exibe uma visualização do resultado com os campos selecionados.
   
    ![Tutorial de Power BI para Azure Cosmos DB conector de Power BI – nivelar resultados](./media/powerbi-visualize/power_bi_connector_pbiresultflatten.png)
4. Em nosso exemplo, a propriedade Location é um bloco geojson em um documento.  Como você pode ver, Location é representado como um tipo de **registro** em Power bi desktop.  
5. Clique no expansor no lado direito do cabeçalho da coluna documento. local.  O menu de contexto com os campos tipo e coordenadas é exibido.  Vamos selecionar o campo coordenadas, verifique se **usar nome da coluna original como prefixo** não está selecionado e clique em **OK**.
   
    ![Tutorial de Power BI para Azure Cosmos DB conector de Power BI – registro de localização](./media/powerbi-visualize/power_bi_connector_pbilocationrecord.png)
6. O painel central agora mostra uma coluna de coordenadas de tipo de **lista** .  Conforme mostrado no início do tutorial, os dados geojson neste tutorial são de tipo de ponto com valores de latitude e longitude registrados na matriz de coordenadas.
   
    O elemento de coordenadas [0] representa a longitude enquanto as coordenadas [1] representam a latitude.
    ![Tutorial de Power BI para o conector de Power BI de Azure Cosmos DB – lista de coordenadas](./media/powerbi-visualize/power_bi_connector_pbiresultflattenlist.png)
7. Para mesclar a matriz de coordenadas, crie uma **coluna personalizada** chamada latlong.  Selecione a faixa de opções **adicionar coluna** e clique em **coluna personalizada**.  A janela **coluna personalizada** é exibida.
8. Forneça um nome para a nova coluna, por exemplo, LatLong.
9. Em seguida, especifique a fórmula personalizada para a nova coluna.  Para nosso exemplo, concatenaremos os valores de latitude e longitude separados por uma vírgula, conforme mostrado abaixo, usando a seguinte fórmula: `Text.From([coordinates]{1})&","&Text.From([coordinates]{0})`. Clique em **OK**.
   
    Para obter mais informações sobre o DAX (Data Analysis Expressions) incluindo funções DAX, visite [noções básicas do Dax em Power bi desktop](https://docs.microsoft.com/power-bi/desktop-quickstart-learn-dax-basics).
   
    ![Tutorial de Power BI para Azure Cosmos DB Power BI Connector – adicionar coluna personalizada](./media/powerbi-visualize/power_bi_connector_pbicustomlatlong.png)

10. Agora, o painel central mostra as novas colunas LatLong populadas com os valores.
    
    ![Tutorial de Power BI para Azure Cosmos DB conector de Power BI – coluna LatLong personalizada](./media/powerbi-visualize/power_bi_connector_pbicolumnlatlong.png)
    
    Se você receber um erro na nova coluna, certifique-se de que as etapas aplicadas em configurações de consulta correspondam à seguinte figura:
    
    ![As etapas aplicadas devem ser origem, navegação, documento expandido, documento expandido. local, adicionado personalizado](./media/powerbi-visualize/power-bi-applied-steps.png)
    
    Se as etapas forem diferentes, exclua as etapas adicionais e tente adicionar a coluna personalizada novamente. 

11. Clique em **fechar e aplicar** para salvar o modelo de dados.
    
    ![Tutorial de Power BI do conector para Azure Cosmos DB Power BI-fechar & aplicar](./media/powerbi-visualize/power_bi_connector_pbicloseapply.png)

<a id="build-the-reports"></a>
## <a name="build-the-reports"></a>Criar os relatórios
Power BI Desktop exibição de relatório é onde você pode começar a criar relatórios para visualizar dados.  Você pode criar relatórios arrastando e soltando campos na tela de **relatório** .

![Power BI Desktop vista de relatório - conector do Power BI](./media/powerbi-visualize/power_bi_connector_pbireportview2.png)

No modo de exibição de relatório, você deve encontrar:

1. O painel **campos** é onde você pode ver uma lista de modelos de dados com campos que podem ser usados para seus relatórios.
2. O painel **visualizações** . Um relatório pode conter uma única ou várias visualizações.  Escolha os tipos visuais ajustando suas necessidades no painel **visualizações** .
3. A tela **relatório** é onde você cria os elementos visuais para seu relatório.
4. A página do **relatório** . Você pode adicionar várias páginas de relatório no Power BI Desktop.

O seguinte mostra as etapas básicas de criação de um relatório de exibição de mapa interativo simples.

1. Para nosso exemplo, criaremos uma exibição de mapa mostrando o local de cada vulcão.  No painel **visualizações** , clique no tipo de Visual do mapa, conforme realçado na captura de tela acima.  Você deve ver o tipo de Visual do mapa pintado na tela do **relatório** .  O painel **Visualização** também deve exibir um conjunto de propriedades relacionadas ao tipo visual do mapa.
2. Agora, arraste e solte o campo LatLong do painel **campos** para a propriedade **local** no painel **visualizações** .
3. Em seguida, arraste e solte o campo nome do vulcão para a propriedade **legenda** .  
4. Em seguida, arraste e solte o campo elevação para a propriedade **tamanho** .  
5. Agora você deve ver o Visual do mapa mostrando um conjunto de bolhas indicando o local de cada vulcão com o tamanho da bolha correlacionado à elevação do vulcão.
6. Agora você criou um relatório básico.  Você pode personalizar ainda mais o relatório adicionando mais visualizações.  Em nosso caso, adicionamos uma segmentação de tipo vulcão para tornar o relatório interativo.  
   
7. No menu arquivo, clique em **salvar** e salve o arquivo como PowerBITutorial. pbix.

## <a name="publish-and-share-your-report"></a>Publicar e compartilhar seu relatório
Para compartilhar seu relatório, você deve ter uma conta no PowerBI.com.

1. Na Power BI Desktop, clique na faixa de faixas **página inicial** .
2. Clique em **Publicar**.  Você será solicitado a inserir o nome de usuário e a senha para sua conta do PowerBI.com.
3. Depois que a credencial for autenticada, o relatório será publicado no destino selecionado.
4. Clique em **Abrir ' PowerBITutorial. pbix ' em Power bi** para ver e compartilhar seu relatório no PowerBI.com.
   
    ![Publicação em Power BI êxito! Abrir o tutorial no Power BI](./media/powerbi-visualize/power_bi_connector_open_in_powerbi.png)

## <a name="create-a-dashboard-in-powerbicom"></a>Criar um painel no PowerBI.com
Agora que você tem um relatório, vamos compartilhá-lo no PowerBI.com

Quando você publica o relatório de Power BI Desktop para PowerBI.com, ele gera um **relatório** e um **conjunto** de um Dataset em seu locatário PowerBI.com. Por exemplo, depois de publicar um relatório chamado **PowerBITutorial** para PowerBI.com, você verá PowerBITutorial nas seções **relatórios** e conjuntos de **valores** no PowerBI.com.

   ![Captura de tela do novo relatório e conjunto de relatórios no PowerBI.com](./media/powerbi-visualize/powerbi-reports-datasets.png)

Para criar um painel compartilhável, clique no botão **fixar página em tempo real** no relatório do PowerBI.com.

   ![Captura de tela do novo relatório e conjunto de relatórios no PowerBI.com](./media/powerbi-visualize/power-bi-pin-live-tile.png)

Em seguida, siga as instruções em [fixar um bloco de um relatório](https://powerbi.microsoft.com/documentation/powerbi-service-pin-a-tile-to-a-dashboard-from-a-report/#pin-a-tile-from-a-report) para criar um novo painel. 

Você também pode fazer modificações ad hoc para relatar antes de criar um painel. No entanto, é recomendável que você use Power BI Desktop para executar as modificações e republicar o relatório em PowerBI.com.

<!-- ## Refresh data in PowerBI.com
There are two ways to refresh data, ad hoc and scheduled.

For an ad hoc refresh, simply click on the eclipses (…) by the **Dataset**, e.g. PowerBITutorial. You should see a list of actions including **Refresh Now**. Click **Refresh Now** to refresh the data.

![Screenshot of Refresh Now in PowerBI.com](./media/powerbi-visualize/power-bi-refresh-now.png)

For a scheduled refresh, do the following.

1. Click **Schedule Refresh** in the action list. 

    ![Screenshot of the Schedule Refresh in PowerBI.com](./media/powerbi-visualize/power-bi-schedule-refresh.png)
2. In the **Settings** page, expand **Data source credentials**. 
3. Click on **Edit credentials**. 
   
    The Configure popup appears. 
4. Enter the key to connect to the Azure Cosmos DB account for that data set, then click **Sign in**. 
5. Expand **Schedule Refresh** and set up the schedule you want to refresh the dataset. 
6. Click **Apply** and you are done setting up the scheduled refresh.
-->
## <a name="next-steps"></a>Passos Seguintes
* Para saber mais sobre Power BI, consulte Introdução [ao Power bi](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/).
* Para saber mais sobre Azure Cosmos DB, consulte a [página](https://azure.microsoft.com/documentation/services/cosmos-db/)de aterrissagem da documentação do Azure Cosmos DB.

