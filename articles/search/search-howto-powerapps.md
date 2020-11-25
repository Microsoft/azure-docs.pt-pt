---
title: 'Tutorial: Consulta de Power Apps'
titleSuffix: Azure Cognitive Search
description: Orientação passo a passo sobre como construir uma App de Energia que se conecta a um índice de Pesquisa Cognitiva Azure, envia consultas e produz resultados.
author: luiscabrer
manager: eladz
ms.author: luisca
ms.service: cognitive-search
ms.devlang: rest-api
ms.topic: tutorial
ms.date: 11/17/2020
ms.openlocfilehash: e8c16f02cf6b77fa54d2a19abac48e9914aa99bd
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96008570"
---
# <a name="tutorial-query-a-cognitive-search-index-from-power-apps"></a>Tutorial: Consulta de um índice de pesquisa cognitiva de Power Apps

Aproveite o ambiente de desenvolvimento rápido de aplicações de Power Apps para criar uma aplicação personalizada para o seu conteúdo pesmável em Azure Cognitive Search.

Neste tutorial, vai aprender a:

> [!div class="checklist"]
> * Ligue-se à Pesquisa Cognitiva Azure
> * Configurar um pedido de consulta
> * Visualizar resultados numa aplicação de tela

Se não tiver uma subscrição do Azure, abra uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

* [Conta Power Apps](https://make.powerapps.com)

* [Índice de amostra de hotéis](search-get-started-portal.md) alojado no seu serviço de pesquisa

* [Chave API de consulta](search-security-api-keys.md#find-existing-keys)

## <a name="1---create-a-custom-connector"></a>1 - Criar um conector personalizado

Um conector em Power Apps é uma ligação de fonte de dados. Neste passo, irá criar um conector personalizado para ligar a um índice de pesquisa na nuvem.

1. [Inscreva-se nas](https://make.powerapps.com) Power Apps.

1. À esquerda, **Data** expanda os  >  **Conectores Personalizados de Dados**.
 
    :::image type="content" source="./media/search-howto-powerapps/1-2-custom-connector.png" alt-text="Menu de conector personalizado" border="true":::

1. Selecione  **+ Novo conector personalizado** e, em seguida, selecione Criar a partir de **branco**.

    :::image type="content" source="./media/search-howto-powerapps/1-3-create-blank.png" alt-text="Criar a partir do menu em branco" border="true":::

1. Dê ao seu conector personalizado um nome (por exemplo, *AzureSearchQuery),* e, em seguida, clique em **Continuar**.

1. Insira informações na Página Geral:

   * Cor de fundo do ícone (por exemplo, #007ee5)
   * Descrição (por exemplo, "Um conector para a pesquisa cognitiva de Azure")
   * No Anfitrião, terá de introduzir o URL do seu serviço de pesquisa `<yourservicename>.search.windows.net` (como)
   * Para URL base, basta introduzir "/"

    :::image type="content" source="./media/search-howto-powerapps/1-5-general-info.png" alt-text="Diálogo geral de informação" border="true":::

1. Na Página de Segurança, desateie *a chave API* como **tipo de autenticação,** desa esta medida, definida tanto a etiqueta do parâmetro como o nome do parâmetro para *a tecla API*. Para **a localização do parâmetro**, selecione *Cabeçalho* como mostrado abaixo.

    :::image type="content" source="./media/search-howto-powerapps/1-6-authentication-type.png" alt-text="Opção tipo de autenticação" border="true":::

1. Na Página Definições, selecione **+ Nova Ação** para criar uma ação que questione o índice. Introduza o valor "Consulta" para o resumo e o nome do ID de funcionamento. Introduza uma descrição como *"Consultas o índice de pesquisa".*

    :::image type="content" source="./media/search-howto-powerapps/1-7-new-action.png" alt-text="Novas opções de ação" border="true":::

1. Rola para baixo. Em Pedidos, selecione **+ Importar do** botão de amostra para configurar um pedido de consulta para o seu serviço de pesquisa:

   * Selecione o verbo `GET`

   * Para o URL introduza uma consulta de amostra para o seu índice de pesquisa `search=*` (devolve todos os `$select=` documentos, permite-lhe escolher campos). A versão API é necessária. Totalmente especificado, um URL pode parecer assim: `https://mydemo.search.windows.net/indexes/hotels-sample-index/docs?search=*&$select=HotelName,Description,Address/City&api-version=2020-06-30`

   * Para cabeçalhos, escreva `Content-Type` . Vai definir o valor num `application/json` passo posterior.

     **As Aplicações de Energia** utilizam a sintaxe no URL para extrair parâmetros da consulta: os parâmetros de pesquisa, seleção e versão api tornam-se configuráveis à medida que progride através do assistente.

       :::image type="content" source="./media/search-howto-powerapps/1-8-1-import-from-sample.png" alt-text="Importar a partir da amostra" border="true":::

1. Clique **em Importar** para preencher automaticamente o Pedido. Complete a definição dos metadados do parâmetro clicando no **símbolo ...** ao lado de cada um dos parâmetros. Clique **de volta** para voltar à página 'Pedido' após cada atualização de parâmetros.

   :::image type="content" source="./media/search-howto-powerapps/1-8-2-import-from-sample.png" alt-text="Importação do diálogo de amostras" border="true":::

1. Para *pesquisa*: Desafina `*` como o valor **predefinido,** definir **a visibilidade necessária** como *Falso* e definir **visibilidade** a *nenhum*. 

    :::image type="content" source="./media/search-howto-powerapps/1-10-1-parameter-metadata-search.png" alt-text="Metadados de parâmetros de pesquisa" border="true":::

1. Para *selecionar*: Desafina `HotelName,Description,Address/City` como o valor **predefinido,** definido **como** *Falso*, e desatado **visibilidade** a *nenhum*.  

    :::image type="content" source="./media/search-howto-powerapps/1-10-4-parameter-metadata-select.png" alt-text="Selecione metadados de parâmetros" border="true":::

1. Para *a versão api*: Desafina `2020-06-30` como o valor **predefinido**, definido **para** *Verdadeiro*, e desate a **visibilidade** como *interna*.  

    :::image type="content" source="./media/search-howto-powerapps/1-10-2-parameter-metadata-version.png" alt-text="Metadados de parâmetros de versão" border="true":::

1. Para *o tipo de conteúdo*: Definir para `application/json` .

1. Depois de fazer estas alterações, altere a visão do **Swagger Editor.** Na secção de parâmetros deve ver a seguinte configuração:

    ```JSON
    parameters:
      - {name: search, in: query, required: false, type: string, default: '*'}
      - {name: $select, in: query, required: false, type: string, default: 'HotelName,Description,Address/City'}
      - {name: api-version, in: query, required: true, type: string, default: '2020-06-30',
        x-ms-visibility: internal}
      - {name: Content-Type, in: header, required: false, type: string}
    ```

1. Volte para o assistente e volte ao **3. Pedir** passo. Desloque-se até à secção 'Resposta'. Clique **em "Adicionar resposta padrão".** Isto é fundamental porque ajudará a Power Apps a entender o esquema da resposta. 

1. Cole uma resposta de amostra. Uma forma fácil de capturar uma resposta de amostra é através do Search Explorer no portal Azure. No Search Explorer, deverá introduzir a mesma consulta que fez para o pedido, mas adicione **$top=2** para limitar os resultados a apenas dois documentos: `search=*&$select=HotelName,Description,Address/City&$top=2` . . . 

   As Power Apps só precisam de alguns resultados para detetar o esquema. Pode copiar a seguinte resposta para o assistente agora, assumindo que está a usar o índice de amostras de hotéis.

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
    > Existe um limite de caracteres para a resposta JSON que pode introduzir, por isso pode querer simplificar o JSON antes de o colar. O esquema e o formato da resposta são mais importantes do que os próprios valores. Por exemplo, o campo Descrição poderia ser simplificado apenas para a primeira frase.

1. Clique **em Importar** para adicionar a resposta padrão.

1. Clique em **Criar conector** no topo direito para guardar a definição.

1. Clique **perto** para fechar o conector.

## <a name="2---test-the-connection"></a>2 - Testar a ligação

Quando o conector for criado pela primeira vez, é necessário reabri-lo a partir da lista de Conectores Personalizados para o testar. Mais tarde, se es fazer atualizações adicionais, pode testar a partir do assistente.

Você precisará de uma [chave API de consulta](search-security-api-keys.md#find-existing-keys) para esta tarefa. Cada vez que uma ligação é criada, seja para um teste ou inclusão numa aplicação, o conector precisa da chave API de consulta utilizada para a ligação à Pesquisa Cognitiva Azure.

1. No extremo esquerdo, clique em **Conectores Personalizados.**

1. Encontre o seu conector na lista (neste tutorial, é "AzureSearchQuery").

1. Selecione o conector, expanda a lista de ações e selecione **Ver Propriedades**.

    :::image type="content" source="./media/search-howto-powerapps/1-11-1-test-connector.png" alt-text="Ver Propriedades" border="true":::

1. **Selecione Editar** no topo direito.

1. Selecione **4. Teste** para abrir a página de teste.

1. Na Operação de Teste, clique **+ Nova Ligação**.

1. Introduza uma chave API de consulta. Esta é uma consulta de Pesquisa Cognitiva Azure para acesso apenas de leitura a um índice. Pode [encontrar a chave](search-security-api-keys.md#find-existing-keys) no portal Azure. 

1. Em Operações, clique no botão **de funcionamento do Teste.** Se tiver sucesso, deverá ver um estado de 200 e no corpo da resposta deverá ver JSON que descreve os resultados da pesquisa.

    :::image type="content" source="./media/search-howto-powerapps/1-11-2-test-connector.png" alt-text="Resposta JSON" border="true":::

## <a name="3---visualize-results"></a>3 - Visualizar resultados

Neste passo, crie uma App de Alimentação com uma caixa de pesquisa, um botão de pesquisa e uma área de exibição para os resultados. A Power App irá ligar-se ao conector personalizado recentemente criado para obter os dados do Azure Search.

1. À esquerda, **expanda** apps  >  **+ nova aplicação**  >  **Canvas**.

    :::image type="content" source="./media/search-howto-powerapps/2-1-create-canvas.png" alt-text="Criar aplicativo de tela" border="true":::

1. Selecione o tipo de aplicação. Para este tutorial, crie uma **App Em Branco** com o Layout do **Telefone.** O **Estúdio Power Apps** vai aparecer.

1. Uma vez no estúdio, selecione o separador **Fontes de Dados** e clique no novo Conector que acaba de criar. No nosso caso, chama-se *AzureSearchQuery.* Clique **Em Adicionar uma ligação**.

   Introduza a tecla API de consulta.

    :::image type="content" source="./media/search-howto-powerapps/2-3-connect-connector.png" alt-text="conector de ligação" border="true":::

    Agora, *o AzureSearchQuery* é uma fonte de dados que está disponível para ser usada a partir da sua aplicação.

1. No **separador Inserir,** adicione alguns controlos à tela.

    :::image type="content" source="./media/search-howto-powerapps/2-4-add-controls.png" alt-text="Inserir controlos" border="true":::

1. Insira os seguintes elementos:

   * Um Rótulo de Texto com o valor "Consulta:"
   * Um elemento de entrada de texto (chame-lhe *txtQuery,* valor predefinido: "*")
   * Um botão com o texto "Procurar" 
   * Uma Galeria Vertical chamada (chame-lhe *galeriaResults)*

    A tela deve ser algo assim:

    :::image type="content" source="./media/search-howto-powerapps/2-5-controls-layout.png" alt-text="Controla o layout" border="true":::

1. Para fazer com que o **botão 'Pesquisar'** emita uma consulta, cole a seguinte ação no **OnSelect:**

    ```
    If(!IsBlank(txtQuery.Text),
        ClearCollect(azResult, AzureSearchQuery.Query({search: txtQuery.Text}).value))
    ```

   A imagem que se segue mostra a barra de fórmula para a ação **OnSelect.**

    :::image type="content" source="./media/search-howto-powerapps/2-6-search-button-event.png" alt-text="Botão OnSelect" border="true":::

   Esta ação fará com que o botão atualize uma nova coleção chamada *azResult* com o resultado da consulta de pesquisa, utilizando o texto na caixa de texto *txtQuery* como termo de consulta.

   > [!NOTE]
   > Experimente isto se obtém um erro de sintaxe de fórmula "A função 'ClearCollect' tem algumas funções inválidas":
   > 
   > * Em primeiro lugar, certifique-se de que a referência do conector está correta. Limpe o nome do conector e comece a escrever o nome do seu conector. O Intellisense deve sugerir o conector e o verbo certos.
   > 
   > * Se o erro persistir, elimine e recrie o conector. Se houver várias instâncias de um conector, a aplicação pode estar a usar a errada.
   > 

1. Ligue o controlo da Galeria Vertical à coleção *azResult* que foi criada quando completou o passo anterior. 

   Selecione o controlo da galeria e execute as seguintes ações no painel de propriedades.

   * Definir **DataSource** para *azResult*.
   * Selecione um **Layout** que funcione para si com base no tipo de dados no seu índice. Neste caso, usamos o *título, legenda e* layout do corpo.
   * **Editar Campos** e selecionar os campos que gostaria de visualizar.

    Uma vez que fornecemos um resultado de amostra quando definimos o conector, a aplicação está ciente dos campos disponíveis no seu índice.
    
    :::image type="content" source="./media/search-howto-powerapps/2-7-gallery-select-fields.png" alt-text="Campos da galeria" border="true":::   
 
1. Prima **F5** para pré-visualizar a aplicação.  

    :::image type="content" source="./media/search-howto-powerapps/2-8-3-final.png" alt-text="Aplicação final" border="true":::    

<!--     Remember that the fields can be set to calculated values.

    For the example, setting using the *"Image, Title and Subtitle"* layout and specifying the *Image* function as the concatenation of the root path for the data and the file name (for instance, `"https://mystore.blob.core.windows.net/multilang/" & ThisItem.metadata_storage_name`) will produce the result below.

    :::image type="content" source="./media/search-howto-powerapps/2-8-2-final.png" alt-text="Final app" border="true":::         -->

## <a name="clean-up-resources"></a>Limpar os recursos

Ao trabalhar na sua própria subscrição, recomendamos que verifique, depois de concluir um projeto, se irá precisar dos recursos que criou. Os recursos que deixar em execução podem custar-lhe dinheiro. Pode eliminar recursos individualmente ou eliminar o grupo de recursos para eliminar todo o conjunto de recursos.

Pode encontrar e gerir recursos no portal, utilizando a ligação **de todos os recursos** ou **grupos** de recursos no painel de navegação à esquerda.

Se estiver a utilizar um serviço gratuito, lembre-se que está limitado a três índices, indexadores e fontes de dados. Pode eliminar itens individuais no portal para ficar abaixo do limite.

## <a name="next-steps"></a>Passos seguintes

As Power Apps permitem o rápido desenvolvimento de aplicações personalizadas. Agora que sabe como se conectar a um índice de pesquisa, saiba mais sobre a criação de uma experiência de visualização rica numa App de Energia personalizada.

> [!div class="nextstepaction"]
> [Catálogo de Aprendizagem de Aplicativos de Energia](/powerapps/learning-catalog/get-started)