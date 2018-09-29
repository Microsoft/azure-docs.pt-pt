---
title: Tutorial de BI de energia para o conector do Azure Cosmos DB | Documentos da Microsoft
description: Utilize este tutorial de Power BI para importar o JSON, criar relatórios criteriosos e visualizar dados utilizando o conector do Azure Cosmos DB e o Power BI.
keywords: Power bi tutorial, visualize os dados, o conector do power bi
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 08/17/2018
ms.author: sngun
ms.openlocfilehash: b5b3a96991a2150e553af01e3fda73e04116d7fb
ms.sourcegitcommit: f31bfb398430ed7d66a85c7ca1f1cc9943656678
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/28/2018
ms.locfileid: "47452416"
---
# <a name="power-bi-tutorial-for-azure-cosmos-db-visualize-data-using-the-power-bi-connector"></a>Tutorial de BI de energia para o Azure Cosmos DB: visualizar dados utilizando o conector do Power BI
[PowerBI.com](https://powerbi.microsoft.com/) é um serviço online onde pode criar e partilhar dashboards e relatórios com dados que são importantes para e sua organização.  O ambiente de trabalho do Power BI é um ferramenta que permite-lhe recuperar dados de várias origens de dados, intercalar e transformar os dados, criar relatórios avançados e visualizações e publicar os relatórios no Power BI de criação de relatórios dedicado.  Com a versão mais recente do Power BI Desktop, pode agora ligar à sua conta do Azure Cosmos DB através do conector do Azure Cosmos DB para o Power BI.   

Neste tutorial do Power BI, vamos percorrer os passos para ligar a uma conta do Azure Cosmos DB no Power BI Desktop, navegue para uma coleção em que queremos para extrair os dados usando o navegador, transforme dados JSON em formato tabular com o Editor de consultas do Power BI Desktop e criar e publicar um relatório em PowerBI.com.

Depois de concluir este tutorial do Power BI, será capaz de responder às seguintes perguntas:  

* Como posso criar relatórios com dados do Azure Cosmos DB com o Power BI Desktop?
* Como ligar a uma conta do Azure Cosmos DB no Power BI Desktop?
* Como posso recuperar dados de uma coleção no Power BI Desktop?
* Como pode transformar os dados JSON aninhados no Power BI Desktop?
* Como publicar e partilhar os meus relatórios no PowerBI.com?

> [!NOTE]
> O conector do Power BI para o Azure Cosmos DB liga-se ao Power BI Desktop para extração e a transformação de dados. Relatórios criados no Power BI Desktop, em seguida, podem ser publicados para o PowerBI.com. Extração direta e a transformação de dados do Azure Cosmos DB não não possível efetuar no PowerBI.com. 

> [!NOTE]
> Ligar ao Azure Cosmos DB com o conector do Power BI é atualmente suportado para a API de SQL do Azure Cosmos DB e apenas para contas de API do Gremlin.

## <a name="prerequisites"></a>Pré-requisitos
Antes de seguir as instruções neste tutorial do Power BI, certifique-se de que tem acesso para os seguintes recursos:

* [A versão mais recente do Power BI Desktop](https://powerbi.microsoft.com/desktop).
* Acesso a nossa conta de demonstração ou dados na sua conta do Azure Cosmos DB.
  * A conta de demonstração é preenchida com os dados de volcano mostrados neste tutorial. Esta conta de demonstração não está vinculada a qualquer SLAs e destina-se apenas a fins de demonstração.  Podemos reservar o direito de fazer modificações para esta conta de demonstração incluindo mas não se limitando a, encerrando a conta, alterar a chave, restringir o acesso, a alteração e eliminar os dados, a qualquer momento sem aviso ou motivo.
    * URL: `https://analytics.documents.azure.com`
    * Chave só de leitura: `MSr6kt7Gn0YRQbjd6RbTnTt7VHc5ohaAFu7osF0HdyQmfR+YhwCH2D2jcczVIR1LNK3nMPNBD31losN7lQ/fkw==`
  * Ou, para criar sua própria conta, veja [criar uma conta de base de dados do Azure Cosmos DB no portal do Azure](https://azure.microsoft.com/documentation/articles/create-account/). Em seguida, para obter volcano exemplo dados que é semelhantes ao que são utilizados neste tutorial (mas não contém os blocos de GeoJSON), consulte a [NOAA site](https://www.ngdc.noaa.gov/nndc/struts/form?t=102557&s=5&d=5) e, em seguida, importar os dados com o [ferramenta de migração de dados do Azure Cosmos DB](import-data.md).

Para partilhar os seus relatórios no PowerBI.com, tem de ter uma conta no PowerBI.com.  Para saber mais sobre o Power BI para gratuito e o Power BI Pro, visite [ https://powerbi.microsoft.com/pricing ](https://powerbi.microsoft.com/pricing).

## <a name="lets-get-started"></a>Vamos começar
Neste tutorial, Vamos imaginar que é um geólogo estudar exterminaram em todo o mundo.  Os dados de volcano são armazenados numa conta do Azure Cosmos DB e os documentos JSON a aparência do documento de exemplo seguinte.

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

Pretende recuperar os dados de volcano da conta do Azure Cosmos DB e visualize dados num relatório do Power BI interativo, como o relatório seguinte.

![Ao concluir este tutorial do Power BI com o conector do Power BI, poderá visualizar dados com o relatório do Power BI Desktop volcano](./media/powerbi-visualize/power_bi_connector_pbireportfinal.png)

Pronto para experimentá-lo? Vamos começar.

1. Execute o ambiente de trabalho do Power BI na sua estação de trabalho.
2. Assim que o Power BI Desktop é iniciado, um *bem-vindo* é apresentado o ecrã.
   
    ![Ecrã de boas-vindas do BI ambiente de trabalho do Power - conector do Power BI](./media/powerbi-visualize/power_bi_connector_welcome.png)
3. Pode **obter dados**, consulte **origens recentes**, ou **abra relatórios de outros** diretamente a partir do *boas-vindas* ecrã.  Clique no X no canto superior direito para fechar o ecrã. O **relatório** é apresentada a vista do Power BI Desktop.
   
    ![Power BI Desktop vista de relatório - conector do Power BI](./media/powerbi-visualize/power_bi_connector_pbireportview.png)
4. Selecione o **home page** Friso, em seguida, clique em **obter dados**.  O **obter dados** deverá aparecer a janela.
5. Clique em **Azure**, selecione **(Beta) do Azure Cosmos DB**e, em seguida, clique em **Connect**. 

    ![O ambiente de trabalho do Power BI obter dados - conector do Power BI](./media/powerbi-visualize/power_bi_connector_pbigetdata.png)   
6. Sobre o **conector de pré-visualização** página, clique em **continuar**. O **do Azure Cosmos DB** é apresentada a janela.
7. Especifique o URL de ponto final de conta do Azure Cosmos DB que pretende recuperar os dados de, conforme mostrado abaixo e, em seguida, clique em **OK**. Para utilizar a sua conta, pode obter o URL na caixa de URI no **[chaves](manage-account.md#keys)** painel do portal do Azure. Para utilizar a conta de demonstração, introduza `https://analytics.documents.azure.com` para o URL. 
   
    Deixe o nome de base de dados, o nome da coleção e a instrução SQL em branco como estes campos são opcionais.  Em vez disso, usamos o navegador para selecionar a base de dados e a coleção para identificar a origem dos dados.
   
    ![Tutorial de BI de energia para o conector do Azure Cosmos DB Power BI - janela de ligação de ambiente de trabalho](./media/powerbi-visualize/power_bi_connector_pbiconnectwindow.png)
8. Se estiver a ligar a este ponto final pela primeira vez, lhe for pedido para a chave de conta. Na sua conta, obter a chave do **chave primária** caixa de **[chaves só de leitura](manage-account.md#keys)** painel do portal do Azure. Para a conta de demonstração, a chave é `MSr6kt7Gn0YRQbjd6RbTnTt7VHc5ohaAFu7osF0HdyQmfR+YhwCH2D2jcczVIR1LNK3nMPNBD31losN7lQ/fkw==`. Introduza a chave apropriada e, em seguida, clique em **Connect**.
   
    Recomendamos que utilize a chave só de leitura durante a criação de relatórios.  Isto impede a exposição desnecessária da chave mestra para potenciais riscos de segurança. A chave só de leitura está disponível a partir da [chaves](manage-account.md#keys) painel do portal do Azure, ou pode utilizar as informações de conta de demonstração fornecidas acima.
   
    ![Tutorial de BI de energia para o conector do Azure Cosmos DB Power BI - chave da conta](./media/powerbi-visualize/power_bi_connector_pbidocumentdbkey.png)
    
    > [!NOTE] 
    > Se obtiver um erro que diz "base de dados especificada não foi encontrado." ver a solução alternativa passos desta [problema do Power BI](https://community.powerbi.com/t5/Issues/Document-DB-Power-BI/idi-p/208200).
    
9. Quando a conta é ligada com êxito, o **navegador** é apresentado o painel.  O **navegador** mostra uma lista de bases de dados sob a conta.
10. Clique e expanda na base de dados onde os dados para o relatório é proveniente, se estiver a utilizar a conta de demonstração, selecione **volcanodb**.   
11. Agora, selecione uma coleção que contém os dados para recuperar. Se estiver a utilizar a conta de demonstração, selecione **volcano1**.
    
    O painel de visualização mostra uma lista dos **registo** itens.  Um documento é representado como um **registo** tipo no Power BI. Da mesma forma, um bloco JSON aninhado dentro de um documento é também um **registo**.
    
    ![Tutorial de BI de energia para o conector do Azure Cosmos DB Power BI - janela do navegador](./media/powerbi-visualize/power_bi_connector_pbinavigator.png)
12. Clique em **editar** para iniciar o Editor de consulta numa nova janela para transformar os dados.

## <a name="flattening-and-transforming-json-documents"></a>Mesclar e transformar documentos JSON
1. Mude para a janela do Editor de consultas do Power BI, onde o **documento** coluna no painel central.
   ![Editor de consultas do Power BI Desktop](./media/powerbi-visualize/power_bi_connector_pbiqueryeditor.png)
2. Clique no expansor no lado direito do **documento** cabeçalho da coluna.  Será apresentado o menu de contexto com uma lista de campos.  Selecione os campos que necessários para o relatório, por exemplo, nome Volcano, país, região, localização, elevação, tipo, estado e última Eruption saber. Desmarque os **utilização nome de coluna original como prefixo** caixa e, em seguida, clique em **OK**.
   
    ![Tutorial de BI para o conector do Azure Cosmos DB Power BI de energia - expanda documentos](./media/powerbi-visualize/power_bi_connector_pbiqueryeditorexpander.png)
3. O painel central apresenta uma pré-visualização do resultado com os campos selecionados.
   
    ![Tutorial de BI para o conector do Azure Cosmos DB Power BI de energia - nivelamento resultados](./media/powerbi-visualize/power_bi_connector_pbiresultflatten.png)
4. No nosso exemplo, a propriedade de localização é um bloco de GeoJSON num documento.  Como pode ver, a localização é representada como um **registo** tipo no Power BI Desktop.  
5. Clique no expansor no lado direito do cabeçalho de coluna Document.Location.  O menu de contexto com campos de coordenadas e tipo são apresentados.  Vamos selecionar o campo de coordenadas, certifique-se **utilização nome de coluna original como prefixo** não está selecionada e clique em **OK**.
   
    ![Tutorial de BI de energia para o conector do Azure Cosmos DB Power BI - registo de localização](./media/powerbi-visualize/power_bi_connector_pbilocationrecord.png)
6. Painel central mostra agora uma coluna de coordenadas de **lista** tipo.  Conforme mostrado no início do tutorial, os dados de GeoJSON neste tutorial são do tipo de ponto com valores de Latitude e Longitude registados na matriz de coordenadas.
   
    O elemento de coordenadas [0] representa Longitude enquanto coordenadas [1] representa Latitude.
    ![Tutorial de BI de energia para o conector do Azure Cosmos DB Power BI - lista de coordenadas](./media/powerbi-visualize/power_bi_connector_pbiresultflattenlist.png)
7. A matriz de coordenadas de aplanar, criar um **coluna personalizada** chamado LatLong.  Selecione o **Adicionar coluna** Friso e clique em **coluna personalizada**.  O **coluna personalizada** é apresentada a janela.
8. Forneça um nome para a nova coluna, por exemplo, LatLong.
9. Em seguida, especifique a fórmula personalizada para a nova coluna.  No nosso exemplo, concatenamos os valores de Latitude e Longitude, separados por vírgulas, como mostrado a seguir usando a seguinte fórmula: `Text.From([coordinates]{1})&","&Text.From([coordinates]{0})`. Clique em **OK**.
   
    Para obter mais informações sobre as expressões DAX (Data Analysis) incluindo funções DAX, visite [básicas de DAX no Power BI Desktop](https://support.powerbi.com/knowledgebase/articles/554619-dax-basics-in-power-bi-desktop).
   
    ![Tutorial de BI de energia para o conector do Azure Cosmos DB Power BI - Adicionar coluna personalizada](./media/powerbi-visualize/power_bi_connector_pbicustomlatlong.png)

10. Agora, o painel central mostra as novas colunas de LatLong preenchidas com os valores.
    
    ![Tutorial de BI de energia para o conector do Azure Cosmos DB Power BI - coluna LatLong personalizado](./media/powerbi-visualize/power_bi_connector_pbicolumnlatlong.png)
    
    Se receber um erro na nova coluna, certifique-se de que os passos aplicados em definições da consulta correspondem a figura a seguir:
    
    ![Passos aplicados devem ser a fonte, navegação, documento expandido, Document.Location expandido, personalizado adicionado](./media/powerbi-visualize/power-bi-applied-steps.png)
    
    Se os passos são diferentes, elimine as etapas adicionais e tente novamente a adicionar a coluna personalizada. 

11. Clique em **fechar e aplicar** para guardar o modelo de dados.
    
    ![Tutorial de BI de energia para o conector do Azure Cosmos DB Power BI - fechar e aplicar](./media/powerbi-visualize/power_bi_connector_pbicloseapply.png)

<a id="build-the-reports"></a>
## <a name="build-the-reports"></a>Criar os relatórios
O modo de exibição do Power BI Desktop relatório é onde pode começar a criar relatórios para visualizar os dados.  Pode criar relatórios ao arrastar e largar os campos para o **relatório** baseadas em telas.

![Power BI Desktop vista de relatório - conector do Power BI](./media/powerbi-visualize/power_bi_connector_pbireportview2.png)

Na vista de relatório, encontrará:

1. O **campos** painel, isso é onde pode ver uma lista de modelos de dados com campos que pode utilizar para os seus relatórios.
2. O **visualizações** painel. Um relatório pode conter uma única ou várias visualizações.  Escolha os tipos de elementos visuais que se ajusta a suas necessidades de desde o **visualizações** painel.
3. O **relatório** baseadas em telas, isso é onde criar os elementos visuais para o relatório.
4. O **relatório** página. Pode adicionar várias páginas de relatório no Power BI Desktop.

O código a seguir mostra as etapas básicas de criação de um relatório simples de exibição de mapa interativo.

1. No nosso exemplo, iremos criar uma vista de mapa que mostra a localização de cada volcano.  Na **visualizações** painel, clique no tipo de elemento visual de mapa como realçado na captura de ecrã acima.  Deverá ver o tipo de elemento visual de mapa pintado na **relatório** baseadas em telas.  O **visualização** painel também deve apresentar um conjunto de propriedades relacionadas com o tipo de elemento visual de mapa.
2. Agora, arraste e largue o campo de LatLong do **campos** painel para o **localização** propriedade na **visualizações** painel.
3. Em seguida, arraste e largue o campo de nome de Volcano para o **legenda** propriedade.  
4. Em seguida, arraste e largue o campo de elevação para o **tamanho** propriedade.  
5. Agora, deverá ver o mapa que mostra um conjunto de bolhas que indica a localização de cada volcano com o tamanho da bolha correlacionar para a elevação do volcano visual.
6. Agora criou um relatório básico.  Pode personalizar ainda mais o relatório ao adicionar mais visualizações.  No nosso caso, adicionamos uma segmentação de dados do tipo de Volcano para tornar o relatório interativo.  
   
    ![Captura de ecrã do relatório do Power BI Desktop final após a conclusão do tutorial do Power BI para o Azure Cosmos DB](./media/powerbi-visualize/power_bi_connector_pbireportfinal.png)
7. No menu ficheiro, clique em **guardar** e salve o arquivo como PowerBITutorial.pbix.

## <a name="publish-and-share-your-report"></a>Publicar e partilhar o relatório
Para partilhar o relatório, tem de ter uma conta no PowerBI.com.

1. No Power BI Desktop, clique nas **home page** faixa de opções.
2. Clique em **Publicar**.  Ser-lhe pedido para introduzir o nome de utilizador e palavra-passe para a sua conta do PowerBI.com.
3. Assim que a credencial foi autenticada, o relatório ser publicado para seu destino que selecionou.
4. Clique em **aberto 'PowerBITutorial.pbix"no Power BI** para ver e partilhar o relatório em PowerBI.com.
   
    ![A publicar no Power BI sucesso! Tutorial aberto no Power BI](./media/powerbi-visualize/power_bi_connector_open_in_powerbi.png)

## <a name="create-a-dashboard-in-powerbicom"></a>Criar um dashboard no PowerBI.com
Agora que tem um relatório, permite a partilhá-lo no PowerBI.com

Ao publicar o relatório do Power BI Desktop para o PowerBI.com, será gerado um **relatório** e uma **conjunto de dados** no seu inquilino de PowerBI.com. Por exemplo, afinal publicado um relatório chamado **PowerBITutorial** no PowerBI.com, verá PowerBITutorial em ambos os **relatórios** e **conjuntos de dados** secções em PowerBI.com.

   ![Captura de ecrã do novo relatório e conjunto de dados no PowerBI.com](./media/powerbi-visualize/powerbi-reports-datasets.png)

Para criar um dashboard partilháveis, clique a **afixar página dinâmica** botão no seu relatório em PowerBI.com.

   ![Captura de ecrã do novo relatório e conjunto de dados no PowerBI.com](./media/powerbi-visualize/power-bi-pin-live-tile.png)

Em seguida, siga as instruções em [afixar um mosaico a partir de um relatório](https://powerbi.microsoft.com/documentation/powerbi-service-pin-a-tile-to-a-dashboard-from-a-report/#pin-a-tile-from-a-report) para criar um novo dashboard. 

Também pode fazer modificações ad hoc ao relatório antes de criar um dashboard. No entanto, é recomendado que utilize o Power BI Desktop para efetuar as modificações e voltar a publicar o relatório em PowerBI.com.

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
* Para saber mais sobre o Power BI, veja [introdução ao Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/).
* Para saber mais sobre o Azure Cosmos DB, veja a [página de destino de documentação do Azure Cosmos DB](https://azure.microsoft.com/documentation/services/cosmos-db/).

