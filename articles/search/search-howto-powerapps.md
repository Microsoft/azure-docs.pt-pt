---
title: 'Tutorial: Consulta de Power Apps'
titleSuffix: Azure Cognitive Search
description: Orientação passo a passo sobre como construir uma App de Energia que se conecta a um índice de Pesquisa Cognitiva Azure, envia consultas e dá resultados.
author: luiscabrer
manager: eladz
ms.author: luisca
ms.service: cognitive-search
ms.devlang: rest-api
ms.topic: tutorial
ms.date: 04/25/2020
ms.openlocfilehash: eafd0ced64567ec7b51ff0f8aac19668343867ea
ms.sourcegitcommit: edccc241bc40b8b08f009baf29a5580bf53e220c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/24/2020
ms.locfileid: "82129551"
---
# <a name="tutorial-query-a-cognitive-search-index-from-power-apps"></a>Tutorial: Consulta a índice de pesquisa cognitiva de aplicações de energia

Aproveite o ambiente rápido de desenvolvimento de aplicações das Power Apps para criar uma aplicação personalizada para o seu conteúdo pesquisável em Pesquisa Cognitiva Azure.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Ligue-se à Pesquisa Cognitiva Azure
> * Configurar um pedido de consulta
> * Visualizar resultados numa aplicação de tela

Se não tiver uma subscrição Azure, abra uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

* [Conta De Aplicações de Energia](http://make.powerapps.com)

* [Índice de amostras de hotéis](search-get-started-portal.md)

* [Chave API de consulta](search-security-api-keys.md#find-existing-keys)

## <a name="1---create-a-custom-connector"></a>1 - Criar um conector personalizado

Um conector em Power Apps é uma ligação de fonte de dados. Neste passo, você vai criar um conector personalizado para ligar a um índice de pesquisa na nuvem.

1. [Inscreva-se nas](http://make.powerapps.com) Aplicações de Energia.

1. À esquerda, expanda**os Conectores Personalizados**de **Dados** > .
 
    :::image type="content" source="./media/search-howto-powerapps/1-2-custom-connector.png" alt-text="Menu conector personalizado" border="true":::

1. **Selecione + Novo conector personalizado,** e, em seguida, selecione **Criar a partir de branco**.

    :::image type="content" source="./media/search-howto-powerapps/1-3-create-blank.png" alt-text="Criar a partir do menu em branco" border="true":::

1. Dê ao seu conector personalizado um nome (por exemplo, *AzureSearchQuery),* e, em seguida, clique em **Continuar**.

1. Insira informações na Página Geral:

   * Cor de fundo do ícone (por exemplo, #007ee5)
   * Descrição (por exemplo, "Um conector à Pesquisa Cognitiva Azure")
   * No Anfitrião, terá de introduzir o URL `<yourservicename>.search.windows.net`do seu serviço de pesquisa (como)
   * Para URL base, basta introduzir "/"

    :::image type="content" source="./media/search-howto-powerapps/1-5-general-info.png" alt-text="Diálogo geral da informação" border="true":::

1. Na página de segurança, coloque a *tecla API* como tipo de **autenticação,** coloque a etiqueta do parâmetro e o nome do parâmetro para a *tecla api.* Para a localização do **parâmetro,** selecione *Header* como mostrado abaixo.

    :::image type="content" source="./media/search-howto-powerapps/1-6-authentication-type.png" alt-text="Opção do tipo de autenticação" border="true":::

1. Na Página Definições, selecione **+ Nova Ação** para criar uma ação que irá consultar o índice. Introduza o valor "Consulta" para o resumo e o nome do ID de operação. Introduza uma descrição como "Consultas ao índice de *pesquisa".*

    :::image type="content" source="./media/search-howto-powerapps/1-7-new-action.png" alt-text="Novas opções de ação" border="true":::

1. Desça. Em Pedidos, selecione **+ Importe do** botão de amostra para configurar um pedido de consulta ao seu serviço de pesquisa:

   * Selecione o verbo`GET`

   * Para o URL introduza uma consulta`search=*` de amostra `$select=` para o seu índice de pesquisa (devolve todos os documentos, permite-lhe escolher campos). A versão API é necessária. Totalmente especificado, um URL pode parecer assim:`https://mydemo.search.windows.net/indexes/hotels-sample-index/docs?search=*&$select=HotelName,Description,Address/City&api-version=2019-05-06`

   * Para cabeçalhos, escreva `Content-Type`. 

     **As Aplicações** de Alimentação usarão a sintaxe para extrair parâmetros da consulta. Note que definimos explicitamente o campo de pesquisa. 

       :::image type="content" source="./media/search-howto-powerapps/1-8-1-import-from-sample.png" alt-text="Importar a partir da amostra" border="true":::

1. Clique **em Importar** para preencher automaticamente o Pedido. Complete a definição dos metadados do parâmetro clicando no **...** símbolo ao lado de cada um dos parâmetros. Clique em **voltar** à página De pedido após cada atualização do parâmetro.

   :::image type="content" source="./media/search-howto-powerapps/1-8-2-import-from-sample.png" alt-text="Importação do diálogo da amostra" border="true":::

1. Para *pesquisa* `*` : Definir como o **valor predefinido**, definido como *Falso* e definir **visibilidade** para *nenhum*. **required** 

    :::image type="content" source="./media/search-howto-powerapps/1-10-1-parameter-metadata-search.png" alt-text="Pesquisar metadados de parâmetros" border="true":::

1. Para *selecionar* `HotelName,Description,Address/City` : Definir como o **valor predefinido**, definido **para** *Falso*, e definir **visibilidade** para *nenhum*.  

    :::image type="content" source="./media/search-howto-powerapps/1-10-4-parameter-metadata-select.png" alt-text="Metadados de parâmetros de versão" border="true":::

1. Para a *versão api* `2019-05-06` : Definir como o **valor predefinido**, definido **para** *True*, e definir **visibilidade** como *interno*.  

    :::image type="content" source="./media/search-howto-powerapps/1-10-2-parameter-metadata-version.png" alt-text="Metadados de parâmetros de versão" border="true":::

1. Para o tipo de `application/json` *conteúdo*: Definir para .

1. Depois de fazer estas alterações, alterna para a vista **do Editor Swagger.** Na secção de parâmetros deve ver a seguinte configuração:

    ```JSON
    parameters:
      - {name: search, in: query, required: false, type: string, default: '*'}
      - {name: $select, in: query, required: false, type: string, default: 'HotelName,Description,Address/City'}
      - {name: api-version, in: query, required: true, type: string, default: '2019-05-06',
        x-ms-visibility: internal}
      - {name: Content-Type, in: header, required: false, type: string}
    ```

1. Volte para o **3. Peça** passo e desloque-se até à secção resposta. Clique em **"Adicionar resposta por defeito".** Isto é fundamental porque ajudará as Power Apps a entender o esquema da resposta. 

1. Pasta uma resposta de amostra. Uma maneira fácil de capturar uma resposta de amostra é através do Explorador de Pesquisa no portal Azure. No Search Explorer, deve introduzir a mesma consulta que fez para o pedido, mas adicione `search=*&$select=HotelName,Description,Address/City&$top=2` **$top=2** para limitar os resultados a apenas dois documentos: : . 

   As Aplicações de Alimentação só precisam de alguns resultados para detetar o esquema.

    ```JSON
    {
        "@odata.context": "https://mydemo.search.windows.net/indexes('hotels-sample-index')/$metadata#docs(*)",
        "value": [
            {
                "@search.score": 1,
                "HotelName": "Arcadia Resort & Restaurant",
                "Description": "The largest year-round resort in the area offering more of everything for your vacation – at the best value!  What can you enjoy while at the resort, aside from the mile-long sandy beaches of the lake? Check out our activities sure to excite both young and young-at-heart guests. We have it all, including being named “Property of the Year” and a “Top Ten Resort” by top publications.",
                "Address": {
                    "City": "Seattle"
                }
            },
            {
                "@search.score": 1,
                "HotelName": "Travel Resort",
                "Description": "The Best Gaming Resort in the area.  With elegant rooms & suites, pool, cabanas, spa, brewery & world-class gaming.  This is the best place to play, stay & dine.",
                "Address": {
                    "City": "Albuquerque"
                }
            }
        ]
    }
    ```

    > [!TIP] 
    > Existe um limite de caracteres para a resposta JSON que pode introduzir, por isso pode querer simplificar o JSON antes de o colar. O esquema e o formato da resposta são mais importantes do que os próprios valores. Por exemplo, o campo descrição poderia ser simplificado apenas para a primeira frase.

1. Clique em **Criar conector** na parte superior direita.

## <a name="2---test-the-connection"></a>2 - Testar a ligação

Quando o conector é criado pela primeira vez, é necessário reabri-lo da lista de Conectores Personalizados para o testar. Mais tarde, se fizer atualizações adicionais, pode testar a partir de dentro do assistente.

Você precisará de uma [chave API de consulta](search-security-api-keys.md#find-existing-keys) para esta tarefa. Cada vez que uma ligação é criada, seja para um teste ou inclusão numa aplicação, o conector precisa da chave API de consulta usada para ligar à Pesquisa Cognitiva Azure.

1. Na extrema esquerda, clique em **Conectores Personalizados**.

1. Procure o conector pelo nome (neste tutorial, é "AzureSearchQuery").

1. Selecione o conector, expanda a lista de ações e selecione **'Propriedades'.**

    :::image type="content" source="./media/search-howto-powerapps/1-11-1-test-connector.png" alt-text="Ver Propriedades" border="true":::

1. Selecione **Editar** no topo direito.

1. Selecione **4. Teste** para abrir a página de teste.

1. No funcionamento do teste, clique em **+ Nova Ligação**.

1. Introduza uma chave API de consulta. Esta é uma consulta de pesquisa cognitiva Azure para acesso apenas a leitura a um índice. Pode [encontrar a chave](search-security-api-keys.md#find-existing-keys) no portal Azure. 

1. Em Operações, clique no botão de funcionamento do **Teste.** Se tiver sucesso, deve ver um estado de 200, e no corpo da resposta deve ver a JSON que descreve os resultados da pesquisa.

    :::image type="content" source="./media/search-howto-powerapps/1-11-2-test-connector.png" alt-text="Resposta JSON" border="true":::

## <a name="3---visualize-results"></a>3 - Visualizar resultados

Neste passo, crie uma App power com uma caixa de pesquisa, um botão de pesquisa e uma área de exibição para os resultados. A Power App irá ligar-se ao conector personalizado recentemente criado para obter os dados do Azure Search.

1. À esquerda, expanda **apps** > **+ Nova aplicação** > **Canvas**.

    :::image type="content" source="./media/search-howto-powerapps/2-1-create-canvas.png" alt-text="Criar app de lona" border="true":::

1. Selecione o tipo de aplicação. Para este tutorial, crie uma **App em branco** com o Layout do **Telefone**. O **Power Apps Studio** vai aparecer.

1. Uma vez no estúdio, selecione o separador **Fontes** de Dados e clique no novo Connector que acaba de criar. No nosso caso, chama-se *AzureSearchQuery.* Clique **em adicionar uma ligação**.

   Introduza a chave API de consulta.

    :::image type="content" source="./media/search-howto-powerapps/2-3-connect-connector.png" alt-text="ligar o conector" border="true":::

    Agora, *o AzureSearchQuery* é uma fonte de dados que está disponível para ser utilizada a partir da sua aplicação.

1. No **separador Inserir,** adicione alguns controlos à tela.

    :::image type="content" source="./media/search-howto-powerapps/2-4-add-controls.png" alt-text="Inserir controlos" border="true":::

1. Insira os seguintes elementos:

   * Uma etiqueta de texto com o valor "Consulta:"
   * Um elemento de entrada de texto (chame-o *de txtQuery*, valor predefinido: "*")
   * Um botão com o texto "Search" 
   * Uma Galeria Vertical chamada (chame-lhe *galeriaResultados)*

    A tela deve ser parecida com isto:

    :::image type="content" source="./media/search-howto-powerapps/2-5-controls-layout.png" alt-text="Controla o layout" border="true":::

1. Para fazer com que o **botão 'Procurar'** emita uma consulta, colhe a seguinte ação no **OnSelect:**

    ```
    If(!IsBlank(txtQuery.Text),
        ClearCollect(azResult, AzureSearchQuery.Query({search: txtQuery.Text}).value))
    ```

   A imagem a seguir mostra a barra de fórmula para a ação **OnSelect.**

       :::image type="content" source="./media/search-howto-powerapps/2-6-search-button-event.png" alt-text="Button OnSelect" border="true":::

   Esta ação fará com que o botão atualize uma nova coleção chamada *azResult* com o resultado da consulta de pesquisa, utilizando o texto na caixa de texto *txtQuery* como o termo de consulta.

   > [!NOTE]
   > Experimente isto se tiver um erro de sintaxe de fórmula "A função 'ClearCollect' tem algumas funções inválidas":
   > 
   > * Em primeiro lugar, certifique-se de que a referência do conector está correta. Limpe o nome do conector e comece a digitar o nome do conector. O intellisense deve sugerir o conector e o verbo certos.
   > 
   > * Se o erro persistir, elimine e recrie o conector. Se houver vários casos de um conector, a aplicação pode estar a usar a errada.
   > 

1. Ligue o controlo da Galeria Vertical à coleção *azResult* que foi criada quando completou o passo anterior. 

   Selecione o controlo da galeria e execute as seguintes ações no painel de propriedades.

   * Definir **DataSource** para *azResult*.
   * Selecione um **Layout** que funcione para si com base no tipo de dados do seu índice. Neste caso, usamos o *Título, legenda e* layout corporal.
   * **Editar Campos**e selecionar os campos que gostaria de visualizar.

    Uma vez que fornecemos um resultado de amostra quando definimos o conector, a aplicação está ciente dos campos disponíveis no seu índice.
    
    :::image type="content" source="./media/search-howto-powerapps/2-7-gallery-select-fields.png" alt-text="Campos da galeria" border="true":::   
 
1. Pressione **F5** para pré-visualizar a aplicação.  

    :::image type="content" source="./media/search-howto-powerapps/2-8-3-final.png" alt-text="Aplicação final" border="true":::    

<!--     Remember that the fields can be set to calculated values.

    For the example, setting using the *"Image, Title and Subtitle"* layout and specifying the *Image* function as the concatenation of the root path for the data and the file name (for instance, `"https://mystore.blob.core.windows.net/multilang/" & ThisItem.metadata_storage_name`) will produce the result below.

    :::image type="content" source="./media/search-howto-powerapps/2-8-2-final.png" alt-text="Final app" border="true":::         -->

## <a name="clean-up-resources"></a>Limpar recursos

Ao trabalhar na sua própria subscrição, recomendamos que verifique, depois de concluir um projeto, se irá precisar dos recursos que criou. Os recursos que deixar em execução podem custar-lhe dinheiro. Pode eliminar recursos individualmente ou eliminar o grupo de recursos para eliminar todo o conjunto de recursos.

Pode encontrar e gerir recursos no portal, utilizando a ligação **De Todos os recursos** ou **grupos de Recursos** no painel de navegação à esquerda.

Se estiver a utilizar um serviço gratuito, lembre-se de que está limitado a três índices, indexadores e fontes de dados. Pode eliminar itens individuais no portal para se manter abaixo do limite.

## <a name="next-steps"></a>Passos seguintes

As Power Apps permitem o rápido desenvolvimento de aplicações de aplicações personalizadas. Agora que sabe como se conectar a um índice de pesquisa, saiba mais sobre a criação de uma experiência de visualização rica numa Aplicação de Energia personalizada.

> [!div class="nextstepaction"]
> [Catálogo de aprendizagem de aplicações de energia](https://docs.microsoft.com/powerapps/learning-catalog/get-started)

