---
title: Tutorial de Power BI para conector DB Azure Cosmos
description: Utilize este tutorial Power BI para importar JSON, criar relatórios perspicazes e visualizar dados utilizando o conector Azure Cosmos DB e Power BI.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 05/21/2019
ms.author: sngun
ms.openlocfilehash: b27bab9ea3029264143caaacf094f0a799894356
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97359868"
---
# <a name="visualize-azure-cosmos-db-data-by-using-the-power-bi-connector"></a>Visualizar dados do Azure Cosmos DB com o conector do Power BI
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

[Power BI](https://powerbi.microsoft.com/) é um serviço online onde pode criar e partilhar dashboards e relatórios. O Power BI Desktop é uma ferramenta de criação de relatórios que lhe permite obter dados de várias origens de dados. O Azure Cosmos DB é uma das origens de dados que pode utilizar com o Power BI Desktop. Pode ligar o Power BI Desktop à conta do Azure Cosmos DB com o conector do Azure Cosmos DB para Power BI.  Depois de importar os dados do Azure Cosmos DB para o Power BI, pode transformá-los, criar relatórios e publicar os relatórios no Power BI.   

Este artigo descreve os passos necessários para se ligar à conta do Azure Cosmos DB para o Power BI Desktop. Após a ligação, navegue para uma coleção, extraia os dados, transforme os dados JSON num formato tabular e publique um relatório no Power BI.

> [!NOTE]
> O conector Power BI para Azure Cosmos DB liga-se ao Power BI Desktop. Os relatórios criados no Power BI Desktop podem ser publicados para PowerBI.com. A extração direta de dados DB da Azure Cosmos não pode ser realizada a partir de PowerBI.com. 

> [!NOTE]
> A ligação ao Azure Cosmos DB com o conector Power BI é atualmente suportada apenas para as contas API API E API da AZure Cosmos DB EskL e Da API gremlin.

## <a name="prerequisites"></a>Pré-requisitos
Antes de seguir as instruções neste tutorial power BI, certifique-se de que tem acesso aos seguintes recursos:

* [Descarregue a versão mais recente do Power BI Desktop](https://powerbi.microsoft.com/desktop).

* Descarregue os dados do [vulcão](https://github.com/Azure-Samples/azure-cosmos-db-sample-data/blob/main/SampleData/VolcanoData.json) de amostra do GitHub.

* [Crie uma conta de base de dados Azure Cosmos](create-cosmosdb-resources-portal.md#create-an-azure-cosmos-db-account) e importe os dados do vulcão utilizando a [ferramenta de migração de dados Azure Cosmos DB](import-data.md). Ao importar dados, considere as seguintes definições para a fonte e destinos na ferramenta de migração de dados:

   * **Parâmetros de origem** 

       * **Importar a partir de:** Ficheiros JSON

   * **Parâmetros-alvo** 

      * **Cadeia de ligação:**`AccountEndpoint=<Your_account_endpoint>;AccountKey=<Your_primary_or_secondary_key>;Database= <Your_database_name>` 

      * **Chave de partição:** /País 

      * **Produção de Coleção:** 1000 

Para partilhar os seus relatórios em PowerBI.com, deve ter uma conta em PowerBI.com.  Para saber mais sobre Power BI e Power BI Pro, consulte [https://powerbi.microsoft.com/pricing](https://powerbi.microsoft.com/pricing) .

## <a name="lets-get-started"></a>Vamos começar.
Neste tutorial, imaginemos que é um geólogo que estuda vulcões em todo o mundo. Os dados do vulcão são armazenados numa conta DB Azure Cosmos e o formato de documento JSON é o seguinte:

```json
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
```

Você vai recuperar os dados do vulcão da conta DB Azure Cosmos e visualizar dados em um relatório interativo power BI.

1. Executar power bi desktop.

2. Pode **obter dados,** ver **Fontes Recentes,** ou **Abrir Outros Relatórios** diretamente do ecrã de boas-vindas. Selecione o "X" no canto superior direito para fechar o ecrã. É apresentada a visão do **Relatório** do Power BI Desktop.
   
   :::image type="content" source="./media/powerbi-visualize/power_bi_connector_pbireportview.png" alt-text="Power BI Desktop Report View - Conector Power BI":::

3. Selecione a fita **Home** e, em seguida, clique em **Obter Dados**.  A janela **Get Data** deve aparecer.

4. Clique em **Azure,** selecione **Azure Cosmos DB (Beta)** e, em seguida, clique em **Connect**. 

   :::image type="content" source="./media/powerbi-visualize/power_bi_connector_pbigetdata.png" alt-text="Power BI Desktop Obter Dados - Conector BI de potência":::

5. Na página **do Conector de Pré-visualização,** clique **em Continuar**. Aparece a janela **DB de Azure Cosmos.**

6. Especifique o URL de ponto final da conta Azure Cosmos DB que gostaria de recuperar os dados a partir da indicação abaixo e, em seguida, clique **em OK**. Para utilizar a sua própria conta, pode recuperar o URL da caixa URI na lâmina **keys** do portal Azure. Opcionalmente, pode fornecer o nome da base de dados, o nome da recolha ou utilizar o navegador para selecionar a base de dados e a recolha para identificar de onde os dados provêm.
   
7. Se estiver a ligar-se a este ponto final pela primeira vez, é-lhe solicitada a chave da conta. Para a sua própria conta, recupere a chave da caixa **chave primária** na lâmina de **chaves apenas de leitura** do portal Azure. Introduza a tecla apropriada e, em seguida, clique em **Connect**.
   
   Recomendamos que utilize a chave apenas de leitura quando se couem relatórios. Isto evita a exposição desnecessária da chave primária a potenciais riscos de segurança. A chave apenas de leitura está disponível a partir da lâmina **Keys** do portal Azure. 
    
8. Quando a conta está ligada com sucesso, aparece o painel **Navigator.** O **Navigator** mostra uma lista de bases de dados na conta.

9. Clique e expanda na base de dados de onde os dados do relatório provêm, selecione **volcanodb** (o nome da sua base de dados pode ser diferente).   

10. Agora, selecione uma coleção que contenha os dados para recuperar, selecione **vulcão1** (o nome da sua recolha pode ser diferente).
    
    O painel de pré-visualização mostra uma lista de itens **de gravação.**  Um documento é representado como um tipo **de Record** em Power BI. Da mesma forma, um bloco JSON aninhado dentro de um documento também é um **Record**.
    
    :::image type="content" source="./media/powerbi-visualize/power_bi_connector_pbinavigator.png" alt-text="Tutorial de Power BI para conector Azure Cosmos DB Power BI - Janela do Navegador":::

12. Clique em **Editar** para lançar o Editor de Consulta numa nova janela para transformar os dados.

## <a name="flattening-and-transforming-json-documents"></a>Achatamento e transformação de documentos JSON
1. Mude para a janela Power BI Query Editor, onde a coluna **Documento** no painel central.

   :::image type="content" source="./media/powerbi-visualize/power_bi_connector_pbiqueryeditor.png" alt-text="Editor de Consultas do Power BI Desktop":::

1. Clique no expansor no lado direito do cabeçalho da coluna **Document.**  O menu de contexto com uma lista de campos será apresentado.  Selecione os campos de que necessita para o seu relatório, por exemplo, Nome do Vulcão, País, Região, Localização, Elevação, Tipo, Estado e Última Erupção. Desmarque o nome da coluna original como caixa **de prefixo** e, em seguida, clique em **OK**.
   
   :::image type="content" source="./media/powerbi-visualize/power_bi_connector_pbiqueryeditorexpander.png" alt-text="Power BI tutorial para conector Azure Cosmos DB Power BI - Expandir documentos":::

1. O painel central apresenta uma pré-visualização do resultado com os campos selecionados.
   
   :::image type="content" source="./media/powerbi-visualize/power_bi_connector_pbiresultflatten.png" alt-text="Power BI tutorial para conector Azure Cosmos DB Power BI - Resultados achatados":::

1. No nosso exemplo, a propriedade Localização é um bloco GeoJSON em um documento.  Como pode ver, a localização é representada como um tipo **de record** no Power BI Desktop.  

1. Clique no expansor no lado direito do cabeçalho da coluna Document.Location.  O menu de contexto com tipos e coordenadas campos aparecem.  Vamos selecionar o campo de coordenadas, garantir que o nome da coluna original, uma vez que **o prefixo** não está selecionado, e clique **em OK**.
   
   :::image type="content" source="./media/powerbi-visualize/power_bi_connector_pbilocationrecord.png" alt-text="Tutorial de Power BI para conector Azure Cosmos DB Power BI - Registo de localização":::

1. O painel central mostra agora uma coluna coordenada do tipo **Lista.**  Como mostrado no início do tutorial, os dados da GeoJSON neste tutorial são do tipo Ponto com valores de Latitude e Longitude registados na matriz de coordenadas.
   
   O elemento das coordenadas[0] representa a Longitude enquanto as coordenadas[1] representam a Latitude.

   :::image type="content" source="./media/powerbi-visualize/power_bi_connector_pbiresultflattenlist.png" alt-text="Tutorial de Power BI para conector Azure Cosmos DB Power BI - Lista de coordenadas":::

1. Para achatar a matriz de coordenadas, crie uma **Coluna Personalizada** chamada LatLong.  Selecione a fita **'Adicionar Coluna'** e clique na **Coluna Personalizada.**  Aparece a janela **da Coluna Personalizada.**

1. Forneça um nome para a nova coluna, por exemplo, LatLong.

1. Em seguida, especifique a fórmula personalizada para a nova coluna.  Por exemplo, concatenaremos os valores de Latitude e Longitude separados por uma vírgula, como mostrado abaixo, utilizando a seguinte fórmula: `Text.From([coordinates]{1})&","&Text.From([coordinates]{0})` . Clique em **OK**.
   
   Para obter mais informações sobre expressões de análise de dados (DAX) incluindo funções DAX, visite [o DAX Basics no Power BI Desktop](/power-bi/desktop-quickstart-learn-dax-basics).
   
   :::image type="content" source="./media/powerbi-visualize/power_bi_connector_pbicustomlatlong.png" alt-text="Power BI tutorial para conector Azure Cosmos DB Power BI - Adicionar Coluna Personalizada":::

1. Agora, o painel central mostra as novas colunas LatLong povoadas com os valores.
    
    :::image type="content" source="./media/powerbi-visualize/power_bi_connector_pbicolumnlatlong.png" alt-text="Tutorial de Power BI para conector Azure Cosmos DB Power BI - Coluna LatLong personalizada":::
    
    Se receber um Erro na nova coluna, certifique-se de que os passos aplicados em Definições de Consulta correspondem à seguinte figura:
    
    :::image type="content" source="./media/powerbi-visualize/power-bi-applied-steps.png" alt-text="Os passos aplicados devem ser Fonte, Navegação, Documento Expandido, Documento Expandido.Localização, Personalizado Adicionado":::
    
    Se os seus passos forem diferentes, elimine os passos extras e tente adicionar novamente a coluna personalizada. 

1. Clique **em Fechar e Aplicar** para guardar o modelo de dados.

   :::image type="content" source="./media/powerbi-visualize/power_bi_connector_pbicloseapply.png" alt-text="Power BI tutorial para conector Azure Cosmos DB Power BI - Close & Apply":::

<a id="build-the-reports"></a>
## <a name="build-the-reports"></a>Construir os relatórios

Power BI Desktop Report é onde pode começar a criar relatórios para visualizar dados.  Pode criar relatórios arrastando e largando campos na tela do **Relatório.**

:::image type="content" source="./media/powerbi-visualize/power_bi_connector_pbireportview2.png" alt-text="Power BI Desktop Report View - arraste e deixe cair os campos necessários":::

Na visão do Relatório, deve encontrar:

1. O **painel Fields,** é aqui que pode ver uma lista de modelos de dados com campos que pode usar para os seus relatórios.
1. O **painel de visualizações.** Um relatório pode conter uma única ou múltipla visualização.  Escolha os tipos visuais que se ajustem às suas necessidades a partir do **painel visualizações.**
1. O **relatório,** é aqui que se constroem os visuais para o seu relatório.
1. A página **do relatório.** Pode adicionar várias páginas de relatório no Power BI Desktop.

Os seguintes mostram os passos básicos da criação de um simples relatório de visão interativa do Mapa.

1. Por exemplo, criaremos uma vista de mapas que mostre a localização de cada vulcão.  No painel **visualizações,** clique no tipo visual do Mapa como realçado na imagem acima.  Devia ver o tipo visual do Mapa pintado na tela do **Relatório.**  O painel de **visualização** também deve apresentar um conjunto de propriedades relacionadas com o tipo visual do Mapa.
1. Agora, arraste e deixe cair o campo LatLong do painel **Fields** para a propriedade **localização** no painel **de Visualizações.**
1. Em seguida, arraste e largue o campo Nome vulcão para a propriedade **Legend.**  
1. Em seguida, arraste e deixe cair o campo elevação para a propriedade **Size.**  
1. Você deve agora ver o mapa visual mostrando um conjunto de bolhas indicando a localização de cada vulcão com o tamanho da bolha correlacionando com a elevação do vulcão.
1. Criou agora um relatório básico.  Pode personalizar ainda mais o relatório adicionando mais visualizações.  No nosso caso, adicionámos um cortador do tipo vulcão para tornar o relatório interativo.  
   
1. No menu Ficheiro, clique em **Guardar** e guarde o ficheiro como PowerBITutorial.pbix.

## <a name="publish-and-share-your-report"></a>Publique e partilhe o seu relatório
Para partilhar o seu relatório, tem de ter uma conta em PowerBI.com.

1. No Ambiente de Trabalho Power BI, clique na fita **Home.**
1. Clique em **Publish** (Publicar).  É-lhe pedido que introduza o nome de utilizador e a palavra-passe para a sua conta PowerBI.com.
1. Uma vez autenticada a credencial, o relatório é publicado para o seu destino selecionado.
1. Clique **em 'PowerBITutorial.pbix' no Power BI** para ver e partilhar o seu relatório sobre PowerBI.com.
   
   :::image type="content" source="./media/powerbi-visualize/power_bi_connector_open_in_powerbi.png" alt-text="Publicação para Power BI Success! Tutorial aberto no Power BI":::

## <a name="create-a-dashboard-in-powerbicom"></a>Crie um dashboard no PowerBI.com
Agora que tem um relatório, vamos partilhá-lo no PowerBI.com

Ao publicar o seu relatório do Power BI Desktop para PowerBI.com, gera um **Relatório** e um **Conjunto de Dados** no seu PowerBI.com inquilino. Por exemplo, depois de publicar um relatório chamado **PowerBITutorial** para PowerBI.com, verá o PowerBITutorial nas secções **Relatórios** e **Conjuntos de Dados** no PowerBI.com.

   :::image type="content" source="./media/powerbi-visualize/powerbi-reports-datasets.png" alt-text="Screenshot do novo Relatório e Dataset em PowerBI.com":::

Para criar um painel de instrumentos sharable, clique no botão **Pin Live Page** no seu relatório PowerBI.com.

   :::image type="content" source="./media/powerbi-visualize/power-bi-pin-live-tile.png" alt-text="Screenshot de como fixar um relatório a PowerBI.com":::

Em seguida, siga as instruções em [Pin um azulejo de um relatório](https://powerbi.microsoft.com/documentation/powerbi-service-pin-a-tile-to-a-dashboard-from-a-report/#pin-a-tile-from-a-report) para criar um novo painel de instrumentos. 

Também pode fazer modificações ad hoc para reportar antes de criar um dashboard. No entanto, recomenda-se que utilize o Power BI Desktop para executar as modificações e reeditar o relatório para PowerBI.com.

<!-- ## Refresh data in PowerBI.com
There are two ways to refresh data, ad hoc and scheduled.

For an ad hoc refresh, simply click on the eclipses (…) by the **Dataset**, e.g. PowerBITutorial. You should see a list of actions including **Refresh Now**. Click **Refresh Now** to refresh the data.

:::image type="content" source="./media/powerbi-visualize/power-bi-refresh-now.png" alt-text="Screenshot of Refresh Now in PowerBI.com":::

For a scheduled refresh, do the following.

1. Click **Schedule Refresh** in the action list. 

    :::image type="content" source="./media/powerbi-visualize/power-bi-schedule-refresh.png" alt-text="Screenshot of the Schedule Refresh in PowerBI.com":::
2. In the **Settings** page, expand **Data source credentials**. 
3. Click on **Edit credentials**. 
   
    The Configure popup appears. 
4. Enter the key to connect to the Azure Cosmos DB account for that data set, then click **Sign in**. 
5. Expand **Schedule Refresh** and set up the schedule you want to refresh the dataset. 
6. Click **Apply** and you are done setting up the scheduled refresh.
-->
## <a name="next-steps"></a>Passos seguintes
* Para saber mais sobre o Power BI, consulte [Começar com o Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/).
* Para saber mais sobre a Azure Cosmos DB, consulte a página de aterragem da [documentação da Azure Cosmos DB](https://azure.microsoft.com/documentation/services/cosmos-db/).
