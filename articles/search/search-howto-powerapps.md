---
title: Como consultar a Pesquisa Cognitiva azure de Aplicações de Energia
titleSuffix: Azure Cognitive Search
description: Orientação passo a passo sobre como criar conector personalizado à Pesquisa Cognitiva e como visualizá-lo a partir de uma App de Energia
author: luiscabrer
manager: eladz
ms.author: luisca
ms.service: cognitive-search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 03/25/2020
ms.openlocfilehash: c246f8652227a5ad2c0798880e530d6039cdeea8
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2020
ms.locfileid: "80385117"
---
# <a name="how-to-query-a-cognitive-search-index-from-power-apps"></a>Como consultar um índice de pesquisa cognitiva de Aplicações de Energia

Este documento mostra como criar um conector personalizado power Apps para que possa recuperar os resultados da pesquisa a partir de um índice de pesquisa. Também mostra como emitir uma consulta de pesquisa e visualizar os resultados de uma App power. 

## <a name="prerequisites"></a>Pré-requisitos
*    O acesso à conta das Aplicações de Alimentação com a capacidade de criar conectores personalizados.
*    Assumimos que já criou um Índice de Pesquisa Azure.

## <a name="create-a-custom-connector-to-query-azure-search"></a>Crie um conector personalizado para consultar a Pesquisa Azure

Há dois passos principais para ter um PowerApp que mostra os resultados da Pesquisa Cognitiva Azure. Primeiro, vamos criar um conector que possa consultar o índice de pesquisa. Na [próxima secção](#visualize-results-from-the-custom-connector) atualizaremos a aplicação power apps para visualizar os resultados devolvidos pelo conector.

1. Vá ao [make.powerapps.com](http://make.powerapps.com) e **assine.**

1. Pesquisar por**Conectores Personalizados** de **Dados** > 
 
    :::image type="content" source="./media/search-howto-powerapps/1-2-custom-connector.png" alt-text="Menu conector personalizado" border="true":::

1. Clique **+ Novo conector personalizado** e, em seguida, selecione Criar a partir de **branco**.

    :::image type="content" source="./media/search-howto-powerapps/1-3-create-blank.png" alt-text="Criar a partir do menu em branco" border="true":::

1. Dê um nome ao seu conector personalizado. (isto é, *AzureSearchQuery),* e depois clique em **Continuar**. Isto criará um assistente para criar o seu novo conector.

1. Insira informação na Página Geral.

    - Cor de fundo do ícone (por exemplo, #007ee5)
    - Descrição (por exemplo, "Um conector à Pesquisa Cognitiva Azure")
    - No Anfitrião, terá de introduzir o URL do `<yourservicename>.search.windows.net`seu serviço de pesquisa (por exemplo, )
    - Para URL base, basta introduzir "/"
    
    :::image type="content" source="./media/search-howto-powerapps/1-5-general-info.png" alt-text="Diálogo geral da informação" border="true":::

1. Na página de segurança, coloque a *tecla API* como o Tipo de **Autenticação,** coloque a etiqueta do parâmetro e os campos de nome do parâmetro como *chave api*. Para a localização do **parâmetro,** selecione *Header* como mostrado abaixo.
 
    :::image type="content" source="./media/search-howto-powerapps/1-6-authentication-type.png" alt-text="Opção do tipo de autenticação" border="true":::

1. Na Página Definições, selecione **+ Nova Ação** para criar uma ação que irá consultar o índice. Introduza o valor "Consulta" para o resumo e o nome do ID de operação. Introduza uma descrição como "Consultas ao índice de *pesquisa".*
 
    :::image type="content" source="./media/search-howto-powerapps/1-7-new-action.png" alt-text="Novas opções de ação" border="true":::


1. Prima o **+ Importar do** botão de amostra para definir os parâmetros e os cabeçalhos. Em seguida, definirá o pedido de consulta.  

    * Selecione o verbo`GET`
    * Para o URL introduza uma consulta de amostra para o seu índice de pesquisa, por exemplo:
       
    >https://yoursearchservicename.search.windows.net/indexes/yourindexname/docs?search=*&api-versão=2019-05-06-Preview
    

    **As Aplicações** de Alimentação usarão a sintaxe para extrair parâmetros da consulta. Note que definimos explicitamente o campo de pesquisa. 

    :::image type="content" source="./media/search-howto-powerapps/1-8-1-import-from-sample.png" alt-text="Importar a partir da amostra" border="false":::

1.  Clique **em Importar** para pré-preencher automaticamente o diálogo Do Pedido.

    :::image type="content" source="./media/search-howto-powerapps/1-8-2-import-from-sample.png" alt-text="Importação do diálogo da amostra" border="false":::


1. Complete a definição dos metadados do parâmetro clicando no **...** símbolo ao lado de cada um dos parâmetros.

    - Para *pesquisa* `*` : Definir como o **valor predefinido**, definido como *falso* e definir a **visibilidade** para *nenhum*. **required** 

    :::image type="content" source="./media/search-howto-powerapps/1-10-1-parameter-metadata-search.png" alt-text="Pesquisar metadados de parâmetros" border="true":::

    - Para a *versão api*: Definir `2019-05-06-Preview` como o **valor predefinido,** desloque a **visibilidade** como interna e definida **necessária** para *True*.  

    :::image type="content" source="./media/search-howto-powerapps/1-10-2-parameter-metadata-version.png" alt-text="Metadados de parâmetros de versão" border="true":::

    - Da mesma forma, para a *tecla api, coloque-a*conforme **necessário,** com **visibilidade** *interna* . Introduza a chave API do seu serviço de pesquisa como o **valor predefinido**.
    
    Depois de fazer estas alterações, alterna para a vista do **Editor Swagger.** Na secção de parâmetros deve ver a seguinte configuração:    

    ```
          parameters:
          - {name: search, in: query, required: false, type: string, default: '*'}
          - {name: api-version, in: query, required: true, type: string, default: 2019-05-06-Preview,
            x-ms-visibility: internal}
          - {name: api-key, in: header, required: true, type: string, default: YOURKEYGOESHERE,
            x-ms-visibility: internal}
    ```

1. Na secção Resposta, clique em **"Adicionar resposta por defeito".** Isto é fundamental porque ajudará as **Power Apps** a entender o esquema da resposta. Pasta uma resposta de amostra.

    > [!TIP] 
    > Existe um limite de caracteres para a resposta JSON que pode introduzir, por isso pode querer simplificar o JSON para que antes de colá-lo. O importante aspeto do esquema/formato da resposta. Os valores reais na resposta da amostra são menos importantes e podem ser simplificados para reduzir a contagem de caracteres.
    

1.    Clique no botão de **conector Criar** na parte superior direita do ecrã antes de o testar.

1.  Na página de teste, clique na **+ Nova Ligação**e introduza a chave de consulta do serviço de pesquisa como o valor para a *api-key*.

    Este passo pode levá-lo para fora do assistente e para a página Ligações. Você pode querer voltar ao editor de Conexões Personalizadas para realmente testar a ligação. Vá ao **Conector Personalizado** > Selecione o conector recém-criado > *...* > **Ver Propriedades** > **Editar** > **4. Teste** para voltar à página de teste.

1.    Clique agora na **operação de teste** para se certificar de que está a obter resultados do seu índice. Se tiver sucesso, deve ver um estado de 200, e no corpo da resposta deve ver a JSON que descreve os seus resultados de pesquisa.




## <a name="visualize-results-from-the-custom-connector"></a>Visualizar os resultados do conector personalizado
O objetivo deste tutorial não é mostrar-lhe como criar experiências de utilizador chiques com aplicações de energia, por isso o layout ui será minimalista. Vamos criar um PowerApp com uma caixa de pesquisa, um botão de pesquisa e mostrar os resultados num controlo de galeria.  O PowerApp irá ligar-se ao nosso conector personalizado recentemente criado para obter os dados do Azure Search.

1. Criar uma nova App Power. Vá à secção **Apps,** clique em **+ Nova aplicação,** e selecione **Canvas**.

    :::image type="content" source="./media/search-howto-powerapps/2-1-create-canvas.png" alt-text="Criar app de lona" border="true":::

1. Selecione o tipo de aplicação que deseja. Para este tutorial crie uma **app em branco** com o Layout do **Telefone**. O **Power Apps Studio** vai aparecer.

1. Uma vez no estúdio, selecione o separador **Fontes** de Dados e clique no novo Connector que acaba de criar. No nosso caso, chama-se *AzureSearchQuery.* Clique **em adicionar uma ligação**.

    :::image type="content" source="./media/search-howto-powerapps/2-3-connect-connector.png" alt-text="ligar o conector" border="true":::

    Agora, *o AzureSearchQuery* é uma fonte de dados que está disponível para ser utilizada a partir da sua aplicação.
    
1. Navegue para o **separador Inserir,** para que possamos adicionar alguns controlos à nossa forma.

    :::image type="content" source="./media/search-howto-powerapps/2-4-add-controls.png" alt-text="Inserir controlos" border="true":::

1.  Insira os seguintes elementos:
    -   Uma etiqueta de texto com o valor "Consulta:"
    -   Um elemento de entrada de texto (chame-o *de txtQuery*, valor predefinido: "*")
    -   Um botão com o texto "Search" 
    -   Uma Galeria Vertical chamada (chame-lhe *galeriaResultados)*
    
    A sua forma deve ser parecida com esta:

    :::image type="content" source="./media/search-howto-powerapps/2-5-controls-layout.png" alt-text="Controla o layout" border="true":::

1. Para fazer com que o **botão 'Procurar'** emita uma consulta, selecione o botão e colhe a seguinte ação para assumir **o OnSelect:**

    ```
    If(!IsBlank(txtQuery.Text),
        ClearCollect(azResult, AzureSearchQuery.Get({search: txtQuery.Text}).value))
    ```

    :::image type="content" source="./media/search-howto-powerapps/2-6-search-button-event.png" alt-text="Botão OnSelect" border="true":::
 
    Esta ação fará com que o botão atualize uma nova coleção chamada *azResult* com o resultado da consulta de pesquisa, utilizando o texto na caixa de texto *txtQuery* como o termo de consulta.
    
1.  Como próximo passo, ligaremos a galeria vertical que criamos à coleção *AzResult.* Selecione o controlo da galeria e execute as seguintes ações no painel de propriedades.

    -  Definir **DataSource** para *azResult*.
    
    -  Selecione um **Layout** que funcione para si com base no tipo de dados do seu índice. Neste caso, usamos o *Título, legenda e* layout corporal.
    
    -  **Editar Campos**e selecionar os campos que gostaria de visualizar.

    Uma vez que fornecemos um resultado de amostra quando definimos o conector, a aplicação está ciente dos campos disponíveis no seu índice.
    
    :::image type="content" source="./media/search-howto-powerapps/2-7-gallery-select-fields.png" alt-text="Campos da galeria" border="true":::   
 
1.  Pressione **F5** para pré-visualizar a aplicação.  

    Lembre-se que os campos podem ser definidos para valores calculados.      
    Por exemplo, a definição utilizando o layout *"Imagem, Título e Legenda"* e especificando a função *Imagem* como a `"https://mystore.blob.core.windows.net/multilang/" & ThisItem.metadata_storage_name`concatenação do caminho-raiz para os dados e o nome do ficheiro (por exemplo, ) produzirá o resultado abaixo.

    :::image type="content" source="./media/search-howto-powerapps/2-8-2-final.png" alt-text="Aplicação final" border="true":::        

## <a name="next-steps"></a>Passos seguintes

Para mais informações e formação online, consulte [Power Apps Learning Catalog](https://docs.microsoft.com/powerapps/learning-catalog/get-started).

