---
title: Fazer pedidos com o Postman
titleSuffix: Azure Digital Twins
description: Aprenda a configurar e use o Carteiro para testar as APIs das Gémeas Digitais Azure.
ms.author: baanders
author: baanders
ms.service: digital-twins
services: digital-twins
ms.topic: how-to
ms.date: 11/10/2020
ms.openlocfilehash: a528e224511fda363afb80a7749a018e07b5fa26
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/17/2021
ms.locfileid: "107588008"
---
# <a name="how-to-use-postman-to-send-requests-to-the-azure-digital-twins-apis"></a>Como usar o Carteiro para enviar pedidos para as APIs das Gémeas Digitais do Azure

[O Carteiro](https://www.getpostman.com/) é uma ferramenta de teste REST que fornece funcionalidades de solicitação HTTP chave num AMBIENTE e GUI baseado em plugin. Pode usá-lo para elaborar pedidos HTTP e submetê-los às [APIs de REST de Gémeos Digitais Azure.](how-to-use-apis-sdks.md)

Este artigo descreve como configurar o [cliente Postman REST](https://www.getpostman.com/) para interagir com as APIs das Gémeas Digitais Azure, através dos seguintes passos:

1. Use o CLI Azure para [**obter um sinal de portador**](#get-bearer-token) que você usará para fazer pedidos de API no Carteiro.
1. Crie uma [**coleção de Carteiro**](#about-postman-collections) e configuure o cliente Postman REST para usar o seu símbolo ao portador para autenticar. Ao configurar a coleção, pode escolher qualquer uma destas opções:
    1. [**Importe**](#import-collection-of-azure-digital-twins-apis) uma coleção pré-construída de pedidos de API de Gémeos Digitais Azure.
    1. [**Crie**](#create-your-own-collection) a sua própria coleção do zero.
1. [**Adicione pedidos**](#add-an-individual-request) à sua coleção configurada e envie-os para as APIs das Gémeas Digitais Azure.

A Azure Digital Twins tem dois conjuntos de APIs com os que pode trabalhar: **plano de dados** e **plano de controlo.** Para saber mais sobre a diferença entre estes conjuntos de API, consulte [*Como-a-fazer: Use as APIs e SDKs de Gémeos Digitais Azure*](how-to-use-apis-sdks.md). Este artigo contém informações para ambos os conjuntos de API.

## <a name="prerequisites"></a>Pré-requisitos

Para prosseguir com a utilização do Carteiro para aceder às APIs das Gémeas Digitais Azure, é necessário configurar uma instância Azure Digital Twins e descarregar o Carteiro. O resto desta secção leva-o por estes degraus.

### <a name="set-up-azure-digital-twins-instance"></a>Configurar a instância Azure Digital Twins

[!INCLUDE [digital-twins-prereq-instance.md](../../includes/digital-twins-prereq-instance.md)]

### <a name="download-postman"></a>Baixar Carteiro

Em seguida, descarregue a versão para desktop do cliente do Carteiro. Navegue para [*www.getpostman.com/apps*](https://www.getpostman.com/apps) e siga as instruções para descarregar a app.

## <a name="get-bearer-token"></a>Obter ficha de portador

Agora que configuraste o Carteiro e a tua instância Azure Digital Twins, terás de obter um sinal ao portador que os pedidos do Carteiro podem usar para autorizar contra as APIs das Gémeas Digitais Azure.

Há várias maneiras possíveis de obter este símbolo. Este artigo utiliza o [CLI Azure](/cli/azure/install-azure-cli) para assinar na sua conta Azure e obter um token dessa forma.

Se tiver um Azure CLI [instalado localmente,](/cli/azure/install-azure-cli)pode iniciar um pedido de comando na sua máquina para executar os seguintes comandos.
Caso contrário, pode abrir uma janela [Azure Cloud Shell](https://shell.azure.com) no seu navegador e executar os comandos lá.

1. Primeiro, certifique-se de que está registado em Azure com as credenciais apropriadas, executando este comando:

    ```azurecli-interactive
    az login
    ```

2. Em seguida, use o comando [get-token da conta az](/cli/azure/account#az_account_get_access_token) para obter um token portador com acesso ao serviço Azure Digital Twins. Neste comando, você passará no ID de recurso para o ponto final de serviço Azure Digital Twins, de forma a obter um token de acesso que pode aceder aos recursos da Azure Digital Twins. 

    O contexto necessário para o token depende do conjunto de APIs que está a usar, por isso use os separadores abaixo para selecionar entre apis [plano de dados](how-to-use-apis-sdks.md#overview-data-plane-apis) e [plano de controlo.](how-to-use-apis-sdks.md#overview-control-plane-apis)

    # <a name="data-plane"></a>[Plano de dados](#tab/data-plane)
    
    Para obter um símbolo para utilizar com as APIs do **plano de dados,** utilize o seguinte valor estático para o contexto simbólico: `0b07f429-9f4b-4714-9392-cc5e8e80c8b0` . Este é o ID de recursos para o ponto final do serviço Azure Digital Twins.
    
    ```azurecli-interactive
    az account get-access-token --resource 0b07f429-9f4b-4714-9392-cc5e8e80c8b0
    ```
    
    # <a name="control-plane"></a>[Plano de controlo](#tab/control-plane)
    
    Para obter um símbolo para utilizar com as APIs do **plano de controlo,** utilize o seguinte valor para o contexto simbólico: `https://management.azure.com/` .
    
    ```azurecli-interactive
    az account get-access-token --resource https://management.azure.com/
    ```
    ---

    >[!NOTE]
    > Se precisar de aceder à sua instância Azure Digital Twins utilizando um principal serviço ou conta de utilizador que pertença a um inquilino diferente do Azure Ative Directory do caso, terá de solicitar um **token** do inquilino "home" da Azure Digital Twins. Para obter mais informações sobre este processo, consulte [*Como escrever o código de autenticação da aplicação*](how-to-authenticate-client.md#authenticate-across-tenants).

3. Copie o valor do `accessToken` resultado e guarde-o para o utilizar na secção seguinte. Este é o seu **valor simbólico** que irá fornecer ao Carteiro para autorizar os seus pedidos.

    :::image type="content" source="media/how-to-use-postman/console-access-token.png" alt-text="Screenshot da consola mostrando o resultado do comando get-token da conta az. Destaca-se o campo AccessToken e o seu valor de amostra.":::

>[!TIP]
>Este token é válido por pelo menos cinco minutos e um máximo de 60 minutos. Se esgotar o tempo atribuído ao token atual, pode repetir os passos nesta secção para obter um novo.

Em seguida, você vai configurar o Carteiro para usar este token para fazer pedidos de API para Azure Digital Twins.

## <a name="about-postman-collections"></a>Sobre coleções de Carteiro

Os pedidos no Carteiro são guardados em **coleções** (grupos de pedidos). Quando cria uma coleção para agrupar os seus pedidos, pode aplicar configurações comuns a muitos pedidos ao mesmo tempo. Isto pode simplificar consideravelmente a autorização se pretender criar mais do que um pedido contra as APIs das Gémeas Digitais Azure, uma vez que só tem de configurar estes detalhes uma vez para toda a coleção.

Ao trabalhar com a Azure Digital Twins, pode começar importando uma [coleção pré-construída de todos os pedidos da Azure Digital Twins.](#import-collection-of-azure-digital-twins-apis) Você pode querer fazer isso se você estiver explorando as APIs e quer configurar rapidamente um projeto com exemplos de pedido.

Em alternativa, também pode optar por começar do zero, [criando](#create-your-own-collection) a sua própria coleção vazia e povoando-a com pedidos individuais que apenas liguem para as APIs de que necessita. 

As seguintes secções descrevem ambos estes processos. O resto do artigo ocorre na sua aplicação local do Carteiro, por isso abra já a aplicação do Carteiro no seu computador.

## <a name="import-collection-of-azure-digital-twins-apis"></a>Coleção de importação de APIs de Gémeos Digitais Azure

Uma forma rápida de começar com a Azure Digital Twins em Carteiro é importar uma coleção pré-construída de pedidos para as APIs das Gémeas Digitais Azure.

### <a name="download-the-collection-file"></a>Descarregue o ficheiro de recolha

O primeiro passo na importação do conjunto API é o download de uma coleção. Escolha o separador abaixo para a sua escolha de plano de dados ou plano de controlo para ver as opções de recolha pré-construídas.

# <a name="data-plane"></a>[Plano de dados](#tab/data-plane)

Existem atualmente duas coleções de planos de dados Azure Digital Twins disponíveis para escolher:
* [**Azure Digital Twins Postman Collection**](https://github.com/microsoft/azure-digital-twins-postman-samples): Esta coleção proporciona uma experiência simples de começar para a Azure Digital Twins em Carteiro. Os pedidos incluem dados de amostra, para que possa executá-los com edições mínimas necessárias. Escolha esta coleção se quiser um conjunto digerível de pedidos de API chave contendo informações de amostra.
    - Para encontrar a coleção, navegue até ao link de repo e abra o ficheiro nomeado *postman_collection.jsem*.
* **[Plano de dados Azure Digital Twins Swagger](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/digitaltwins/data-plane/Microsoft.DigitalTwins)**: Este repo contém o ficheiro Swagger completo para o conjunto Azure Digital Twins API, que pode ser descarregado e importado para o Carteiro como uma coleção. Isto fornecerá um conjunto abrangente de todos os pedidos de API, mas com corpos de dados vazios em vez de dados de amostragem. Escolha esta coleção se quiser ter acesso a todas as chamadas da API e preencha todos os dados por si mesmo.
    - Para encontrar a coleção, navegue até ao link de repo e escolha a pasta para a versão especifica mais recente. A partir daqui, abra o ficheiro chamado *digitaltwins.js.*

# <a name="control-plane"></a>[Plano de controlo](#tab/control-plane)

A coleção atualmente disponível para o avião de controlo é o [**avião de controlo Azure Digital Twins Swagger.**](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/digitaltwins/data-plane/Microsoft.DigitalTwins) Este repo contém o ficheiro Swagger completo para o conjunto Azure Digital Twins API, que pode ser descarregado e importado para o Carteiro como uma coleção. Isto fornecerá um conjunto abrangente de todos os pedidos da API.

Para encontrar a coleção, navegue até ao link de repo e escolha a pasta para a versão especifica mais recente. A partir daqui, abra o ficheiro chamado *digitaltwins.js.*

---

Aqui está como baixar a sua coleção escolhida para a sua máquina para que possa importá-la para o Carteiro.
1. Utilize os links acima para abrir o ficheiro de recolha no GitHub no seu browser.
1. Selecione o botão **Raw** para abrir o texto em bruto do ficheiro.
    :::image type="content" source="media/how-to-use-postman/swagger-raw.png" alt-text="A imagem do avião de dados digitaltwins.jsno ficheiro no GitHub. Há um destaque em torno do botão Raw." lightbox="media/how-to-use-postman/swagger-raw.png":::
1. Copie o texto da janela e cole-o num novo ficheiro na sua máquina.
1. Guarde o ficheiro com uma extensão *.json* (o nome do ficheiro pode ser o que quiser, desde que se lembre dele para encontrar o ficheiro mais tarde).

### <a name="import-the-collection"></a>Importar a coleção

Em seguida, importe a coleção para o Carteiro.

1. A partir da janela principal do Carteiro, selecione o botão **Importar.**
    :::image type="content" source="media/how-to-use-postman/postman-import-collection.png" alt-text="Imagem de uma janela recém-aberta do Carteiro. Destaca-se o botão 'Importar'." lightbox="media/how-to-use-postman/postman-import-collection.png":::

1. Na janela Import que se segue, selecione **Upload Files** e navegue para o ficheiro de recolha da sua máquina que criou anteriormente. Selecione Abrir.
1. Selecione o botão **Importar** para confirmar.

    :::image type="content" source="media/how-to-use-postman/postman-import-collection-2.png" alt-text="Screenshot da janela 'Import' do Carteiro, mostrando o ficheiro a importar como uma coleção e o botão Import.":::

A coleção recentemente importada pode agora ser vista a partir da sua visão principal do Carteiro, no separador Coleções.

:::image type="content" source="media/how-to-use-postman/postman-post-collection-imported.png" alt-text="Imagem da janela principal do Carteiro. A recolha recentemente importada está em destaque no separador 'Coleções'." lightbox="media/how-to-use-postman/postman-post-collection-imported.png":::

Em seguida, continue na secção seguinte para adicionar um token ao portador da coleção para autorização e conectá-lo ao seu exemplo de gémeos Azure Digital.

### <a name="configure-authorization"></a>Autorização deconfigure

Em seguida, edite a coleção que criou para configurar alguns detalhes de acesso. Realce a coleção que criou e selecione o ícone **'Ver mais ações'** para puxar um menu. Selecione **Editar**.

:::image type="content" source="media/how-to-use-postman/postman-edit-collection.png" alt-text="Imagem do Carteiro. Destaca-se o ícone 'Ver mais ações' para a coleção importada e 'Editar' é destacado nas opções." lightbox="media/how-to-use-postman/postman-edit-collection.png":::

Siga estes passos para adicionar um sinal ao portador da recolha para autorização. É aqui que você vai usar o **valor simbólico** que recolheu na secção [Get bearer token](#get-bearer-token) para usá-lo para todos os pedidos da API na sua coleção.

1. No diálogo de edição para a sua coleção, certifique-se de que está no **separador Autorização.** 

    :::image type="content" source="media/how-to-use-postman/postman-authorization-imported.png" alt-text="Screenshot do diálogo de edição da coleção importada no Carteiro, mostrando o separador 'Autorização'." lightbox="media/how-to-use-postman/postman-authorization-imported.png":::

1. Desacorde o Tipo para **OAuth 2.0,** cole o seu token de acesso na caixa Access Token e selecione **Guardar**.

    :::image type="content" source="media/how-to-use-postman/postman-paste-token-imported.png" alt-text="Screenshot do diálogo de edição do Carteiro para a coleção importada, no separador 'Autorização'. O tipo é 'OAuth 2.0', e a caixa Access Token é destacada." lightbox="media/how-to-use-postman/postman-paste-token-imported.png":::

### <a name="additional-configuration"></a>Configuração adicional

# <a name="data-plane"></a>[Plano de dados](#tab/data-plane)

Se estiver a fazer uma recolha de planos de [dados,](how-to-use-apis-sdks.md#overview-data-plane-apis) ajude a recolha a conectar-se facilmente aos seus recursos Azure Digital Twins, definindo **algumas variáveis fornecidas** com as coleções. Quando muitos pedidos numa coleção requerem o mesmo valor (como o nome de anfitrião da sua instância Azure Digital Twins), pode armazenar o valor numa variável que se aplica a cada pedido na coleção. Ambas as coleções descarregadas para Azure Digital Twins vêm com variáveis pré-criadas que você pode definir ao nível da coleção.

1. Ainda no diálogo de edição para a sua coleção, mude para o **separador Variáveis.**

1. Utilize o nome de **anfitrião** do seu caso a partir da secção [*Pré-Requisitos*](#prerequisites) para definir o campo VALOR CORRENTE da variável relevante. Selecione **Guardar**.

    :::image type="content" source="media/how-to-use-postman/postman-variables-imported.png" alt-text="Screenshot do diálogo de edição da coleção importada no Carteiro, mostrando o separador 'Variáveis'. Destaca-se o campo 'VALOR CORRENTE'." lightbox="media/how-to-use-postman/postman-variables-imported.png":::

1. Se a sua coleção tiver variáveis adicionais, preencha e guarde esses valores também.

Quando terminar os passos acima, está farto de configurar a coleção. Pode fechar o separador de edição da coleção, se quiser.

# <a name="control-plane"></a>[Plano de controlo](#tab/control-plane)

Se estás a fazer uma coleção de aviões de [controlo,](how-to-use-apis-sdks.md#overview-control-plane-apis) fizeste tudo o que precisavas para configurar a coleção. Pode fechar o separador de edição da coleção, se quiser, e proceder à secção seguinte.

--- 

### <a name="explore-requests"></a>Explorar pedidos

Em seguida, explore os pedidos dentro da coleção Azure Digital Twins API. Pode expandir a coleção para visualizar os pedidos pré-criados (classificados por categoria de operação). 

Pedidos diferentes requerem informações diferentes sobre o seu caso e os seus dados. Para ver todas as informações necessárias para elaborar um pedido específico, consulte os detalhes do pedido na documentação de referência da [API dos Gémeos Digitais Azure.](/rest/api/azure-digitaltwins/)

Pode editar os detalhes de um pedido na coleção do Carteiro utilizando estes passos:

1. Selecione-o na lista para obter os seus detalhes editáveis. 

1. Preencha os valores para as variáveis listadas no **separador Params** em **Variáveis De Caminho**.

    :::image type="content" source="media/how-to-use-postman/postman-request-details-imported.png" alt-text="Imagem do Carteiro. A coleção é expandida para mostrar um pedido. A secção 'Variáveis de Caminho' é destacada nos detalhes do pedido." lightbox="media/how-to-use-postman/postman-request-details-imported.png":::

1. Forneça os **seguintes detalhes** do Headers ou **Body** necessários nos respetivos separadores.

Uma vez fornecidos todos os dados necessários, pode executar o pedido com o botão **Enviar.**

Também pode adicionar os seus próprios pedidos à coleção, utilizando o processo descrito na secção [*adicionar um pedido individual*](#add-an-individual-request) abaixo.

## <a name="create-your-own-collection"></a>Crie a sua própria coleção

Em vez de importar a coleção existente de todas as APIs de Gémeos Digitais Azure, também pode criar a sua própria coleção de raiz. Em seguida, pode preenocupá-lo com pedidos individuais usando a [documentação de referência da API dos Gémeos Digitais Azure.](/rest/api/azure-digitaltwins/)

### <a name="create-a-postman-collection"></a>Criar uma coleção do Postman

1. Para criar uma coleção, selecione o botão **Novo** na janela principal do carteiro.

    :::image type="content" source="media/how-to-use-postman/postman-new.png" alt-text="Imagem da janela principal do Carteiro. O botão 'Novo' é realçado." lightbox="media/how-to-use-postman/postman-new.png":::

    Escolha um tipo de **Coleção.**

    :::image type="content" source="media/how-to-use-postman/postman-new-collection-2.png" alt-text="Screenshot do diálogo &quot;Create New&quot; no Carteiro. A opção 'Coleção' está em destaque.":::

1. Isto abrirá um separador para preencher os detalhes da nova coleção. Selecione o ícone Editar ao lado do nome predefinido da coleção **(Nova Coleção)** para substituí-lo pela sua própria escolha de nome. 

    :::image type="content" source="media/how-to-use-postman/postman-new-collection-3.png" alt-text="Screenshot do diálogo de edição da nova coleção no Carteiro. Destaca-se o ícone Editar ao lado do nome 'Nova Coleção'." lightbox="media/how-to-use-postman/postman-new-collection-3.png":::

Em seguida, continue para a secção seguinte para adicionar um sinal ao portador da coleção para autorização.

### <a name="configure-authorization"></a>Autorização deconfigure

Siga estes passos para adicionar um sinal ao portador da recolha para autorização. É aqui que você vai usar o **valor simbólico** que recolheu na secção [Get bearer token](#get-bearer-token) para usá-lo para todos os pedidos da API na sua coleção.

1. Ainda no diálogo de edição para a sua nova coleção, mude para o **separador Autorização.**

    :::image type="content" source="media/how-to-use-postman/postman-authorization-custom.png" alt-text="Screenshot do diálogo de edição da nova coleção no Carteiro, mostrando o separador 'Autorização'." lightbox="media/how-to-use-postman/postman-authorization-custom.png":::

1. Desacorde o Tipo para **OAuth 2.0,** cole o seu token de acesso na caixa Access Token e selecione **Guardar**.

    :::image type="content" source="media/how-to-use-postman/postman-paste-token-custom.png" alt-text="Screenshot do diálogo de edição do Carteiro para a nova coleção, no separador 'Autorização'. O tipo é 'OAuth 2.0', e a caixa Access Token é destacada." lightbox="media/how-to-use-postman/postman-paste-token-custom.png":::

Quando terminar os passos acima, está farto de configurar a coleção. Pode fechar o separador de edição da nova coleção, se quiser.

A nova coleção pode ser vista a partir da sua visão principal do Carteiro, no separador Coleções.

:::image type="content" source="media/how-to-use-postman/postman-post-collection-custom.png" alt-text="Imagem da janela principal do Carteiro. A coleção personalizada recém-criada está em destaque no separador 'Coleções'." lightbox="media/how-to-use-postman/postman-post-collection-custom.png":::

## <a name="add-an-individual-request"></a>Adicionar um pedido individual

Agora que a sua coleção está configurada, pode adicionar os seus próprios pedidos às APIs Azure Digital Twin.

1. Para criar um pedido, volte a utilizar o botão **Novo.**

    :::image type="content" source="media/how-to-use-postman/postman-new.png" alt-text="Imagem da janela principal do Carteiro. O botão 'Novo' é realçado." lightbox="media/how-to-use-postman/postman-new.png":::

    Escolha um tipo de **Pedido.**

    :::image type="content" source="media/how-to-use-postman/postman-new-request-2.png" alt-text="Screenshot do diálogo &quot;Create New&quot; no Carteiro. A opção &quot;Pedido&quot; é realçada.":::

1. Esta ação abre a janela SAVE REQUEST, onde pode introduzir um nome para o seu pedido, dar-lhe uma descrição opcional e escolher a coleção da qual faz parte. Preencha os detalhes e guarde o pedido para a coleção que criou anteriormente.

    :::row:::
        :::column:::
            :::image type="content" source="media/how-to-use-postman/postman-save-request.png" alt-text="Screenshot da janela &quot;Save request&quot; no Carteiro mostrando os campos descritos. Destaca-se o botão 'Save to Azure Digital Twins'.":::
        :::column-end:::
        :::column:::
        :::column-end:::
    :::row-end:::

Agora pode ver o seu pedido sob a coleção e selecioná-lo para obter os seus detalhes editáveis.

:::image type="content" source="media/how-to-use-postman/postman-request-details-custom.png" alt-text="Imagem do Carteiro. A coleção Azure Digital Twins é expandida para mostrar os detalhes do pedido." lightbox="media/how-to-use-postman/postman-request-details-custom.png":::

### <a name="set-request-details"></a>Definir detalhes do pedido

Para fazer um pedido de Carteiro a uma das APIs das Gémeas Digitais Azure, você precisará do URL da API e informações sobre os detalhes que ele requer. Pode encontrar esta informação na documentação de referência da [Azure Digital Twins REST API](/rest/api/azure-digitaltwins/).

Para proceder a uma consulta de exemplo, este artigo utilizará a API de Consulta (e a sua [documentação de referência)](/rest/api/digital-twins/dataplane/query/querytwins)para consultar todos os gémeos digitais em um caso.

1. Obtenha o URL de pedido e escreva a partir da documentação de referência. Para a API de Consulta, este é atualmente *POST. `https://digitaltwins-hostname/query?api-version=2020-10-31`*
1. No Carteiro, desabrocha o tipo de pedido e introduza o URL de pedido, preenchendo os espaços reservados no URL conforme necessário. É aqui que utilizará o nome de **anfitrião** do seu caso na secção [*Pré-Requisitos.*](#prerequisites)
    
   :::image type="content" source="media/how-to-use-postman/postman-request-url.png" alt-text="Screenshot dos detalhes do novo pedido no Carteiro. O URL de consulta da documentação de referência foi preenchido na caixa URL de pedido." lightbox="media/how-to-use-postman/postman-request-url.png":::
    
1. Verifique se os parâmetros indicados para o pedido no **separador Params coincidem** com os descritos na documentação de referência. Para este pedido no Carteiro, o `api-version` parâmetro foi preenchido automaticamente quando o URL de pedido foi introduzido no passo anterior. Para a API de Consulta, este é o único parâmetro necessário, pelo que este passo está feito.
1. No separador **Autorização,** desateia o tipo **de herdar auth do progenitor.** Isto indica que este pedido utilizará a autorização que estabeleceu anteriormente para toda a coleção.
1. Verifique se os cabeçalhos indicados para o pedido no **separador Cabeçalhos** correspondem aos descritos na documentação de referência. Para este pedido, vários cabeçalhos foram preenchidos automaticamente. Para a API de consulta, nenhuma das opções do cabeçalho é necessária, pelo que este passo está feito.
1. Verifique se o corpo indicado para o pedido no separador **Corpo** corresponde às necessidades descritas na documentação de referência. Para a API de consulta, é necessário um organismo JSON para fornecer o texto de consulta. Aqui está um órgão de exemplo para este pedido que consulta para todos os gémeos digitais no caso:

   :::image type="content" source="media/how-to-use-postman/postman-request-body.png" alt-text="Screenshot dos detalhes do novo pedido no Carteiro, no separador Corpo. Contém um corpo JSON cru com uma consulta de 'SELECT * FROM DIGITALTWINS'." lightbox="media/how-to-use-postman/postman-request-body.png":::

   Para obter mais informações sobre a criação de consultas Azure Digital Twins, consulte [*Como fazer: Consultar o gráfico gémeo*](how-to-query-graph.md).

1. Verifique a documentação de referência de quaisquer outros campos que possam ser necessários para o seu tipo de pedido. Para a API de Consulta, todos os requisitos foram agora cumpridos no pedido do Carteiro, pelo que este passo está feito.
1. Utilize o botão **Enviar** para enviar o seu pedido preenchido.
   :::image type="content" source="media/how-to-use-postman/postman-request-send.png" alt-text="Screenshot do Carteiro mostrando os detalhes do novo pedido. O botão Enviar está realçado." lightbox="media/how-to-use-postman/postman-request-send.png":::

Após o envio do pedido, os detalhes da resposta aparecerão na janela do Carteiro abaixo do pedido. Pode ver o código de estado da resposta e qualquer texto corporal.

:::image type="content" source="media/how-to-use-postman/postman-request-response.png" alt-text="Screenshot do pedido enviado no Carteiro. Abaixo dos detalhes do pedido, a resposta é mostrada. O estado é de 200 OK e o corpo mostra os resultados da consulta." lightbox="media/how-to-use-postman/postman-request-response.png":::

Também pode comparar a resposta com os dados de resposta esperados na documentação de referência, para verificar o resultado ou saber mais sobre quaisquer erros que surjam.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre as APIs das Gémeas Digitais, leia [*Como fazer: Use as APIs e SDKs de gémeos digitais Azure,*](how-to-use-apis-sdks.md)ou consulte a [documentação de referência para as APIs REST](/rest/api/azure-digitaltwins/).