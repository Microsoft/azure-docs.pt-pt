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
ms.openlocfilehash: c6b9cfab4256c72118a65f7fb0c8e672e2082ffe
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2021
ms.locfileid: "102199667"
---
# <a name="how-to-use-postman-to-send-requests-to-the-azure-digital-twins-apis"></a>Como usar o Carteiro para enviar pedidos para as APIs das Gémeas Digitais do Azure

[O Carteiro](https://www.getpostman.com/) é uma ferramenta de teste REST que fornece funcionalidades de solicitação HTTP chave num AMBIENTE e GUI baseado em plugin. Pode usá-lo para elaborar pedidos HTTP e submetê-los às [APIs de REST de Gémeos Digitais Azure.](how-to-use-apis-sdks.md)

Este artigo descreve como configurar o [cliente Postman REST](https://www.getpostman.com/) para interagir com as APIs das Gémeas Digitais Azure, através dos seguintes passos:

1. Use o [CLI Azure](/cli/azure/install-azure-cli) para obter um sinal de portador que você usará para fazer pedidos de API no Carteiro.
1. Crie uma coleção de Carteiro e configuure o cliente Postman REST para usar o seu símbolo ao portador para autenticar.
1. Utilize o Carteiro configurado para criar e enviar um pedido às APIs das Gémeas Digitais Azure.

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

1. Em seguida, use o comando [get-token da conta az](/cli/azure/account#az_account_get_access_token) para obter um token portador com acesso ao serviço Azure Digital Twins.

    ```azurecli-interactive
    az account get-access-token --resource 0b07f429-9f4b-4714-9392-cc5e8e80c8b0
    ```

1. Copie o valor do `accessToken` resultado e guarde-o para o utilizar na secção seguinte. Este é o seu **valor simbólico** que irá fornecer ao Carteiro para autenticar os seus pedidos.

    :::image type="content" source="media/how-to-use-postman/console-access-token.png" alt-text="Vista de uma janela de consola local mostrando o resultado do comando get-token da conta az. Um dos campos do resultado chama-se accessToken e o seu valor de amostra -- começando com ey -- é destacado.":::

>[!TIP]
>Este token é válido por pelo menos cinco minutos e um máximo de 60 minutos. Se esgotar o tempo atribuído ao token atual, pode repetir os passos nesta secção para obter um novo.

## <a name="set-up-postman-collection-and-authorization"></a>Configurar a recolha e autorização do Carteiro

Em seguida, crie carteiro para fazer pedidos de API.
Estes passos acontecem na sua aplicação local do Carteiro, por isso abra a aplicação do Carteiro no seu computador.

### <a name="create-a-postman-collection"></a>Criar uma coleção do Postman

Os pedidos no Carteiro são guardados em **coleções** (grupos de pedidos). Quando cria uma coleção para agrupar os seus pedidos, pode aplicar configurações comuns a muitos pedidos ao mesmo tempo. Isto pode simplificar consideravelmente a autorização se pretender criar mais do que um pedido contra as APIs das Gémeas Digitais Azure, uma vez que só tem de configurar a autenticação uma vez para toda a coleção.

1. Para criar uma coleção, premir o botão *+ New Collection.*

    :::image type="content" source="media/how-to-use-postman/postman-new-collection.png" alt-text="Vista de uma janela recém-aberta do Carteiro. O botão 'Nova Coleção' está em destaque":::

1. Na *janela CREATE Uma NOVA JANELA DE RECOLHA* que se segue, forneça um **Nome** e **Descrição** opcional para a sua coleção.

Em seguida, continue para a secção seguinte para adicionar um sinal ao portador da coleção para autorização.

### <a name="add-authorization-token-and-finish-collection"></a>Adicione token de autorização e a recolha de acabamentos

1. No *CREATE Um novo* diálogo DE RECOLHA, mova-se para o *separador Autorização.* É aqui que irá colocar o **valor simbólico** que recolheu na secção [Deken do portador,](#get-bearer-token) a fim de usá-lo para todos os pedidos da API na sua coleção.

    :::image type="content" source="media/how-to-use-postman/postman-authorization.png" alt-text="A janela 'CREATE A NEW COLLECTION' Postman, mostrando o separador 'Autorização'.":::

1. Desacorde o *Tipo* para _**OAuth 2.0**_ e cole o seu token de acesso na caixa *Access Token.*

    :::image type="content" source="media/how-to-use-postman/postman-paste-token.png" alt-text="A janela 'CREATE A NEW COLLECTION' Postman, mostrando o separador 'Autorização'. É selecionado um tipo de 'OAuth 2.0' e destaca-se a caixa Access Token onde o valor do token de acesso pode ser colado.":::

1. Depois de colar no seu token ao portador, bata *na Create* para terminar de criar a sua coleção.

A sua nova coleção pode agora ser vista a partir da sua visão principal do Carteiro, em *Coleções.*

:::image type="content" source="media/how-to-use-postman/postman-post-collection.png" alt-text="Vista da janela principal do Carteiro. A coleção recém-criada está em destaque no separador 'Coleções'.":::

## <a name="create-a-request"></a>Criar um pedido

Depois de completar os passos anteriores, pode criar pedidos para as APIs Azure Digital Twin.

1. Para criar um pedido, premir o botão *+ Novo.*

    :::image type="content" source="media/how-to-use-postman/postman-new-request.png" alt-text="Vista da janela principal do Carteiro. O botão 'Novo' está em destaque":::

1. Escolha *o Pedido.*

    :::image type="content" source="media/how-to-use-postman/postman-new-request-2.png" alt-text="Veja as opções que pode selecionar para criar algo novo. A opção 'Pedido' está em destaque":::

1. Esta ação abre a janela *de pedido Save,* onde pode introduzir um nome para o seu pedido, dar-lhe uma descrição opcional e escolher a coleção da qual faz parte. Preencha os detalhes e guarde o pedido para a coleção que criou anteriormente.

    :::row:::
        :::column:::
            :::image type="content" source="media/how-to-use-postman/postman-save-request.png" alt-text="Vista para a janela 'Guardar pedido' onde pode preencher os campos descritos. O botão 'Save to Azure Digital Twins' está em destaque":::
        :::column-end:::
        :::column:::
        :::column-end:::
    :::row-end:::

Agora pode ver o seu pedido sob a coleção e selecioná-lo para obter os seus detalhes editáveis.

:::image type="content" source="media/how-to-use-postman/postman-request-details.png" alt-text="Vista da janela principal do Carteiro. A coleção Azure Digital Twins é expandida, e o pedido de 'Consultas gémeas' é destacado. Os detalhes do pedido são mostrados no centro da página." lightbox="media/how-to-use-postman/postman-request-details.png":::

### <a name="set-request-details"></a>Definir detalhes do pedido

Para fazer um pedido de Carteiro a uma das APIs das Gémeas Digitais Azure, você precisará do URL da API e informações sobre os detalhes que ele requer. Pode encontrar esta informação na documentação de referência da [Azure Digital Twins REST API](/rest/api/azure-digitaltwins/).

Para proceder a uma consulta de exemplo, este artigo utilizará a API de Consulta (e a sua [documentação de referência)](/rest/api/digital-twins/dataplane/query/querytwins)para consultar todos os gémeos digitais em um caso.

1. Obtenha o URL de pedido e escreva a partir da documentação de referência. Para a API de Consulta, este é atualmente *POST. `https://digitaltwins-hostname/query?api-version=2020-10-31`*
1. No Carteiro, desabrocha o tipo de pedido e introduza o URL de pedido, preenchendo os espaços reservados no URL conforme necessário. É aqui que utilizará o nome de **anfitrião** do seu caso na secção [*Pré-Requisitos.*](#prerequisites)
    
   :::image type="content" source="media/how-to-use-postman/postman-request-url.png" alt-text="Nos detalhes do novo pedido, o URL de consulta da documentação de referência foi preenchido na caixa URL do pedido." lightbox="media/how-to-use-postman/postman-request-url.png":::
    
1. Verifique se os parâmetros indicados para o pedido no *separador Params coincidem* com os descritos na documentação de referência. Para este pedido no Carteiro, o `api-version` parâmetro foi preenchido automaticamente quando o URL de pedido foi introduzido no passo anterior. Para a API de Consulta, este é o único parâmetro necessário, pelo que este passo está feito.
1. No separador *Autorização,* desateia o *tipo* *de herdar auth do progenitor.* Isto indica que este pedido utilizará a autenticação que configurar anteriormente para toda a coleção.
1. Verifique se os cabeçalhos indicados para o pedido no *separador Cabeçalhos* correspondem aos descritos na documentação de referência. Para este pedido, vários cabeçalhos foram preenchidos automaticamente. Para a API de consulta, nenhuma das opções do cabeçalho é necessária, pelo que este passo está feito.
1. Verifique se o corpo indicado para o pedido no separador *Corpo* corresponde às necessidades descritas na documentação de referência. Para a API de consulta, é necessário um organismo JSON para fornecer o texto de consulta. Aqui está um órgão de exemplo para este pedido que consulta para todos os gémeos digitais no caso:

   :::image type="content" source="media/how-to-use-postman/postman-request-body.png" alt-text="Nos detalhes do novo pedido, é indicado o separador Corpo. Contém um corpo JSON cru com uma consulta de 'SELECT * FROM DIGITALTWINS'." lightbox="media/how-to-use-postman/postman-request-body.png":::

   Para obter mais informações sobre a criação de consultas Azure Digital Twins, consulte [*Como fazer: Consultar o gráfico gémeo*](how-to-query-graph.md).

1. Verifique a documentação de referência de quaisquer outros campos que possam ser necessários para o seu tipo de pedido. Para a API de Consulta, todos os requisitos foram agora cumpridos no pedido do Carteiro, pelo que este passo está feito.
1. Utilize o botão *Enviar* para enviar o seu pedido preenchido.
   :::image type="content" source="media/how-to-use-postman/postman-request-send.png" alt-text="Perto dos detalhes do novo pedido, destaca-se o botão Enviar." lightbox="media/how-to-use-postman/postman-request-send.png":::

Após o envio do pedido, os detalhes da resposta aparecerão na janela do Carteiro abaixo do pedido. Pode ver o código de estado da resposta e qualquer texto corporal.

:::image type="content" source="media/how-to-use-postman/postman-request-response.png" alt-text="Abaixo dos detalhes do pedido enviado, destacam-se os detalhes da resposta. Há um Status de 200 OK e texto corporal que descreve gémeos digitais que foram devolvidos pela consulta." lightbox="media/how-to-use-postman/postman-request-response.png":::

Também pode comparar a resposta com os dados de resposta esperados na documentação de referência, para verificar o resultado ou saber mais sobre quaisquer erros que surjam.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre as APIs das Gémeas Digitais, leia [*Como fazer: Use as APIs e SDKs de gémeos digitais Azure,*](how-to-use-apis-sdks.md)ou consulte a [documentação de referência para as APIs REST](/rest/api/azure-digitaltwins/).