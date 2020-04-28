---
title: Tutorial power BI para conector Azure Cosmos DB
description: Utilize este tutorial power BI para importar a JSON, criar relatórios perspicazes e visualizar dados utilizando o conector Azure Cosmos DB e Power BI.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: sngun
ms.openlocfilehash: 1dbdd428a54ebf38c7b880bb9530935c0f748226
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "69616813"
---
# <a name="visualize-azure-cosmos-db-data-by-using-the-power-bi-connector"></a>Visualizar dados do Azure Cosmos DB com o conector do Power BI

[Power BI](https://powerbi.microsoft.com/) é um serviço online onde pode criar e partilhar dashboards e relatórios. O Power BI Desktop é uma ferramenta de criação de relatórios que lhe permite obter dados de várias origens de dados. O Azure Cosmos DB é uma das origens de dados que pode utilizar com o Power BI Desktop. Pode ligar o Power BI Desktop à conta do Azure Cosmos DB com o conector do Azure Cosmos DB para Power BI.  Depois de importar os dados do Azure Cosmos DB para o Power BI, pode transformá-los, criar relatórios e publicar os relatórios no Power BI.   

Este artigo descreve os passos necessários para se ligar à conta do Azure Cosmos DB para o Power BI Desktop. Após a ligação, navegue para uma coleção, extraia os dados, transforme os dados JSON num formato tabular e publique um relatório no Power BI.

> [!NOTE]
> O conector Power BI para Azure Cosmos DB liga-se ao Power BI Desktop. Os relatórios criados no Power BI Desktop podem ser publicados para PowerBI.com. A extração direta de dados da Azure Cosmos DB não pode ser efetuada a partir de PowerBI.com. 

> [!NOTE]
> A ligação ao Azure Cosmos DB com o conector Power BI é atualmente suportada apenas para contas API De SQL E API da Azure Cosmos.

## <a name="prerequisites"></a>Pré-requisitos
Antes de seguir as instruções neste tutorial power bi, certifique-se de que tem acesso aos seguintes recursos:

* [Descarregue a versão mais recente do Power BI Desktop](https://powerbi.microsoft.com/desktop).

* Descarregue os dados do [vulcão](https://github.com/Azure-Samples/azure-cosmos-db-sample-data/blob/master/SampleData/VolcanoData.json) da amostra do GitHub.

* Crie uma conta de base de [dados Azure Cosmos](https://azure.microsoft.com/documentation/articles/create-account/) e importe os dados do vulcão utilizando a ferramenta de migração de [dados Azure Cosmos DB.](import-data.md) Ao importar dados, considere as seguintes definições para a fonte e destinos na ferramenta de migração de dados:

   * **Parâmetros de origem** 

       * **Importação de:** Arquivos JSON(s)

   * **Parâmetros-alvo** 

      * **Cadeia de ligação:**`AccountEndpoint=<Your_account_endpoint>;AccountKey=<Your_primary_or_secondary_key>;Database= <Your_database_name>` 

      * **Chave de partição:** /País 

      * **Recolha:** 1000 

Para partilhar os seus relatórios em PowerBI.com, deve ter uma conta em PowerBI.com.  Para saber mais sobre Power BI [https://powerbi.microsoft.com/pricing](https://powerbi.microsoft.com/pricing)e Power BI Pro, consulte .

## <a name="lets-get-started"></a>Vamos começar.
Neste tutorial, imaginemos que é um geólogo a estudar vulcões em todo o mundo. Os dados do vulcão são armazenados numa conta Azure Cosmos DB e o formato de documento JSON é o seguinte:

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

Você vai recuperar os dados do vulcão da conta Azure Cosmos DB e visualizar dados num relatório interativo do Power BI.

1. Executar power bi desktop.

2. Pode **obter Dados,** ver **Fontes Recentes**ou **Abrir Outros Relatórios** diretamente a partir do ecrã de boas-vindas. Selecione o "X" no canto superior direito para fechar o ecrã. A visualização do **Relatório** do Power BI Desktop é apresentada.
   
   ![Power BI Desktop Report View - Conector Power BI](./media/powerbi-visualize/power_bi_connector_pbireportview.png)

3. Selecione a fita **Home** e, em seguida, clique em **Obter Dados**.  A janela **Get Data** deve aparecer.

4. Clique em **Azure,** selecione **Azure Cosmos DB (Beta)** e, em seguida, clique em **Connect**. 

    ![Power BI Desktop Obter Dados - Conector Power BI](./media/powerbi-visualize/power_bi_connector_pbigetdata.png)   

5. Na página **'Preview Connector',** clique em **Continuar**. A janela **Azure Cosmos DB** aparece.

6. Especifique o URL final da conta Azure Cosmos DB que deseja recuperar os dados como mostrado abaixo e, em seguida, clique em **OK**. Para utilizar a sua própria conta, pode recuperar o URL da caixa URI na **lâmina** keys do portal Azure. Opcionalmente, pode fornecer o nome da base de dados, o nome da recolha ou utilizar o navegador para selecionar a base de dados e a recolha para identificar de onde vêm os dados.
   
7. Se estiver a ligar-se a este ponto final pela primeira vez, é solicitado a chave da conta. Para sua própria conta, recupere a chave da caixa **chave primária** na lâmina de **teclas de leitura do** portal Azure. Introduza a tecla apropriada e, em seguida, clique em **Ligar**.
   
   Recomendamos que utilize a tecla de leitura apenas quando for em relatórios de construção. Isto evita uma exposição desnecessária da chave principal para potenciais riscos de segurança. A tecla apenas para leitura está disponível a partir da lâmina **Keys** do portal Azure. 
    
8. Quando a conta está ligada com sucesso, aparece o painel **do Navegador.** O **Navegador** mostra uma lista de bases de dados na conta.

9. Clique e expanda na base de dados de onde vêm os dados do relatório, **selecione volcanodb** (o nome da base de dados pode ser diferente).   

10. Agora, selecione uma coleção que contenha os dados para recuperar, **selecione volcano1** (o seu nome de recolha pode ser diferente).
    
    O painel de pré-visualização mostra uma lista de itens **Record.**  Um Documento é representado como um tipo **de Record** em Power BI. Da mesma forma, um bloco JSON aninhado dentro de um documento é também um **Record**.
    
    ![Tutorial power BI para ligação Azure Cosmos DB Power BI - janela navigator](./media/powerbi-visualize/power_bi_connector_pbinavigator.png)
12. Clique em **Editar** para lançar o Editor de Consulta numa nova janela para transformar os dados.

## <a name="flattening-and-transforming-json-documents"></a>Achatamento e transformação de documentos JSON
1. Mude para a janela Power BI Consulta Editor Editor, onde a coluna **Document** no painel central.
   ![Editor de Consultas do Power BI Desktop](./media/powerbi-visualize/power_bi_connector_pbiqueryeditor.png)
2. Clique no expansor no lado direito do cabeçalho da coluna **Document.**  O menu de contexto com uma lista de campos aparecerá.  Selecione os campos que necessita para o seu relatório, por exemplo, Nome vulcão, país, região, localização, elevação, tipo, estado e última erupção. Desfaça o nome original da coluna Use como caixa de **pré-fixação** e, em seguida, clique em **OK**.
   
    ![Power BI tutorial para o conector Azure Cosmos DB Power BI - Expandir documentos](./media/powerbi-visualize/power_bi_connector_pbiqueryeditorexpander.png)
3. O painel central apresenta uma pré-visualização do resultado com os campos selecionados.
   
    ![Power BI tutorial para conector Azure Cosmos DB Power BI - Resultados flatten](./media/powerbi-visualize/power_bi_connector_pbiresultflatten.png)
4. No nosso exemplo, a propriedade Localização é um bloco GeoJSON em um documento.  Como pode ver, a localização está representada como um tipo **de Record** no Power BI Desktop.  
5. Clique no expansor no lado direito do cabeçalho da coluna Document.Localização.  O menu de contexto com tipos e campos de coordenadas aparecem.  Vamos selecionar o campo de coordenadas, garantir que utilize o **nome da coluna original como prefixo** não é selecionado e clique EM **OK**.
   
    ![Power BI tutorial para ligação Azure Cosmos DB Power BI - Registo de localização](./media/powerbi-visualize/power_bi_connector_pbilocationrecord.png)
6. O painel central mostra agora uma coluna coordenada do tipo **Lista.**  Como mostrado no início do tutorial, os dados da GeoJSON neste tutorial são do tipo Point com valores de Latitude e Longitude registados na matriz de coordenadas.
   
    O elemento coordenada[0] representa longitude enquanto as coordenadas[1] representam a Latitude.
    ![Power BI tutorial para conectador DB Power BI - Lista de coordenadas](./media/powerbi-visualize/power_bi_connector_pbiresultflattenlist.png)
7. Para aplainar a matriz de coordenadas, crie uma **Coluna Personalizada** chamada LatLong.  Selecione a fita **'Adicionar Coluna'** e clique na **Coluna Personalizada**.  A janela da **Coluna Personalizada** aparece.
8. Forneça um nome para a nova coluna, por exemplo, LatLong.
9. Em seguida, especifique a fórmula personalizada para a nova coluna.  Por exemplo, vamos concatenar os valores de Latitude e Longitude separados por uma `Text.From([coordinates]{1})&","&Text.From([coordinates]{0})`vírvia, como mostrado abaixo usando a seguinte fórmula: . Clique em **OK**.
   
    Para obter mais informações sobre expressões de análise de dados (DAX) incluindo funções DAX, visite [o DAX Basics no Power BI Desktop](https://docs.microsoft.com/power-bi/desktop-quickstart-learn-dax-basics).
   
    ![Power BI tutorial para conectador De Potência DB De SMB - Adicionar Coluna Personalizada](./media/powerbi-visualize/power_bi_connector_pbicustomlatlong.png)

10. Agora, o painel central mostra as novas colunas LatLong povoadas com os valores.
    
    ![Power BI tutorial para conectador Db Power BI Azure Cosmos - coluna LatLong personalizada](./media/powerbi-visualize/power_bi_connector_pbicolumnlatlong.png)
    
    Se receber um Erro na nova coluna, certifique-se de que os passos aplicados em definições de consulta correspondem à seguinte figura:
    
    ![Os passos aplicados devem ser Fonte, Navegação, Documento Expandido, Documento Expandido.Localização, Personalizado Adicionado](./media/powerbi-visualize/power-bi-applied-steps.png)
    
    Se os seus passos forem diferentes, apague os passos extras e tente adicionar novamente a coluna personalizada. 

11. Clique em **Fechar e Aplicar** para salvar o modelo de dados.
    
    ![Power BI tutorial para conector Azure Cosmos DB Power BI - Close & Apply](./media/powerbi-visualize/power_bi_connector_pbicloseapply.png)

<a id="build-the-reports"></a>
## <a name="build-the-reports"></a>Construa os relatórios
A visão do Relatório do Ambiente de Trabalho do Power BI é onde pode começar a criar relatórios para visualizar dados.  Pode criar relatórios arrastando e largando campos na tela do **Relatório.**

![Power BI Desktop Report View - Conector Power BI](./media/powerbi-visualize/power_bi_connector_pbireportview2.png)

Na opinião do Relatório, deve encontrar:

1. O painel **Fields,** é aqui que pode ver uma lista de modelos de dados com campos que pode usar para os seus relatórios.
2. O painel **de Visualizações.** Um relatório pode conter uma única ou múltipla visualização.  Escolha os tipos visuais que se adaptam às suas necessidades no painel de **Visualizações.**
3. A tela do **Relatório,** é aqui que se constroem os visuais para o seu relatório.
4. A página do **Relatório.** Pode adicionar várias páginas de relatório no Power BI Desktop.

O seguinte mostra os passos básicos da criação de um simples relatório de visão de mapa interativo.

1. Para o nosso exemplo, vamos criar uma vista de mapa mostrando a localização de cada vulcão.  No painel **de Visualizações,** clique no tipo visual do Mapa como realçado na imagem acima.  Devia ver o tipo visual do Mapa pintado na tela do **Relatório.**  O painel de **visualização** também deve apresentar um conjunto de propriedades relacionadas com o tipo visual do Mapa.
2. Agora, arraste e deixe cair o campo LatLong do painel **Fields** para a propriedade **localização** em **Visualizations.**
3. Em seguida, arraste e deixe cair o campo de nome do vulcão para a propriedade **legend.**  
4. Em seguida, arraste e deixe cair o campo elevação para a propriedade **Size.**  
5. Agora deve ver o mapa visual mostrando um conjunto de bolhas indicando a localização de cada vulcão com o tamanho da bolha correlacionado com a elevação do vulcão.
6. Criou agora um relatório básico.  Pode personalizar ainda mais o relatório adicionando mais visualizações.  No nosso caso, adicionámos um cortador tipo vulcão para tornar o relatório interativo.  
   
7. No menu 'Ficheiro', clique em **Guardar** e guardar o ficheiro como PowerBITutorial.pbix.

## <a name="publish-and-share-your-report"></a>Publique e partilhe o seu relatório
Para partilhar o seu relatório, deve ter uma conta em PowerBI.com.

1. No Ambiente de Trabalho Power BI, clique na fita **Home.**
2. Clique em **Publicar**.  É-lhe solicitado que introduza o nome de utilizador e a palavra-passe para a sua conta PowerBI.com.
3. Uma vez autenticada a credencial, o relatório é publicado no seu destino selecionado.
4. **Clique em Open 'PowerBITutorial.pbix' no Power BI** para ver e partilhar o seu relatório sobre PowerBI.com.
   
    ![Publicação para Power BI Success! Tutorial aberto no Power BI](./media/powerbi-visualize/power_bi_connector_open_in_powerbi.png)

## <a name="create-a-dashboard-in-powerbicom"></a>Crie um dashboard no PowerBI.com
Agora que tem um relatório, vamos partilhá-lo PowerBI.com

Quando publica o seu relatório do Power BI Desktop para PowerBI.com, gera um **Relatório** e um Conjunto de **Dados** no seu PowerBI.com inquilino. Por exemplo, depois de publicar um relatório chamado **PowerBITutorial** para PowerBI.com, verá o PowerBITutorial nas secções **de Relatórios** e **Conjuntos** de Dados em PowerBI.com.

   ![Screenshot do novo Relatório e Dataset em PowerBI.com](./media/powerbi-visualize/powerbi-reports-datasets.png)

Para criar um dashboard sharable, clique no botão **Pin Live Page** no seu relatório PowerBI.com.

   ![Screenshot do novo Relatório e Dataset em PowerBI.com](./media/powerbi-visualize/power-bi-pin-live-tile.png)

Em seguida, siga as instruções em [Pin um azulejo de um relatório](https://powerbi.microsoft.com/documentation/powerbi-service-pin-a-tile-to-a-dashboard-from-a-report/#pin-a-tile-from-a-report) para criar um novo painel de instrumentos. 

Também pode fazer modificações ad hoc para reportar antes de criar um dashboard. No entanto, recomenda-se que utilize o Power BI Desktop para efetuar as modificações e republique o relatório para PowerBI.com.

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
## <a name="next-steps"></a>Passos seguintes
* Para saber mais sobre o Power BI, consulte [Começar com Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/).
* Para saber mais sobre o Azure Cosmos DB, consulte a página de aterragem de [documentação Azure Cosmos DB.](https://azure.microsoft.com/documentation/services/cosmos-db/)

