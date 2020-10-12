---
title: Use o visualizador de erro de desenho de Azure Maps
description: Neste artigo, você vai aprender sobre como visualizar avisos e erros devolvidos pela API de Conversão do Criador.
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/12/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: b3f9451a5ffd13c67232107d8db1e2da4a3891ec
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86524748"
---
# <a name="using-the-azure-maps-drawing-error-visualizer"></a>Utilizando o visualizador de erro de desenho de mapas Azure

O Visualizador de Erro de Desenho é uma aplicação web autónoma que exibe [avisos e erros de pacote de desenho detetados](drawing-conversion-error-codes.md) durante o processo de conversão. A aplicação web Error Visualizer consiste numa página estática que pode utilizar sem se ligar à internet.  Pode utilizar o Error Visualizer para corrigir erros e avisos de acordo com os [requisitos do pacote de desenho](drawing-requirements.md). A [AZure Maps Conversion API](https://docs.microsoft.com/rest/api/maps/conversion) apenas devolve uma resposta com uma ligação ao Error Visualizer apenas quando um erro é detetado.

## <a name="prerequisites"></a>Pré-requisitos

Antes de conseguir descarregar o Visualizador de Erro de Desenho, terá de o fazer:

1. [Criar uma conta do Azure Maps](quick-demo-map-app.md#create-an-azure-maps-account)
2. [Obtenha uma chave de subscrição primária,](quick-demo-map-app.md#get-the-primary-key-for-your-account)também conhecida como a chave primária ou a chave de subscrição.
3. [Criar um recurso Criador](how-to-manage-creator.md)

Este tutorial usa a aplicação [Do Carteiro,](https://www.postman.com/) mas você pode escolher um ambiente de desenvolvimento de API diferente.

## <a name="download"></a>Download

1. Faça o upload do seu pacote Drawing para o serviço Azure Maps Creator para obter um `udid` para o pacote carregado. Para obter etapas sobre como carregar um pacote, consulte [o Upload de um pacote de desenho](tutorial-creator-indoor-maps.md#upload-a-drawing-package).

2. Agora que o pacote de desenho está carregado, usaremos `udid` para o pacote carregado para converter o pacote em dados de mapa. Para obter etapas sobre como converter um pacote, consulte [converter um pacote de desenho](tutorial-creator-indoor-maps.md#convert-a-drawing-package).

    >[!NOTE]
    >Se o seu processo de conversão for bem sucedido, não receberá um link para a ferramenta Error Visualizer.

3. Sob o **separador headers de** resposta na aplicação Do Carteiro, procure o `diagnosticPackageLocation` imóvel, devolvido pela API de Conversão. A resposta deve aparecer como o seguinte JSON:

    ```json
    {
        "operationId": "77dc9262-d3b8-4e32-b65d-74d785b53504",
        "created": "2020-04-22T19:39:54.9518496+00:00",
        "status": "Failed",
        "resourceLocation": "https://atlas.microsoft.com/conversion/{conversionId}?api-version=1.0",
        "properties": {
            "diagnosticPackageLocation": "https://atlas.microsoft.com/mapData/ce61c3c1-faa8-75b7-349f-d863f6523748?api-version=1.0"
        }
    }
    ```

4. Descarregue o Visualizador de Erro de Pacote de Desenho fazendo um `HTTP-GET` pedido no `diagnosticPackageLocation` URL.

## <a name="setup"></a>Configuração

Dentro do pacote fechado descarregado a partir do `diagnosticPackageLocation` link, você encontrará dois ficheiros.

* _VisualizationTool.zip_: Contém o código de origem, os meios de comunicação e a página web do Visualizador de Erros de Desenho.
* _ConversionWarningsAndErrors.jsem_: Contém uma lista formatada de advertências, erros e detalhes adicionais que são utilizados pelo Visualizador de Erros de Desenho.

Desaperte a pasta _VisualizationTool.zip._ Contém os seguintes itens:

* pasta _de ativos:_ contém imagens e ficheiros de mídia
* pasta _estática:_ código fonte
* index.htmficheiro _l:_ a aplicação web.

Abra o ficheiro _index.html_ utilizando qualquer um dos navegadores abaixo, com o número da respetiva versão. Pode utilizar uma versão diferente, se a versão oferecer um comportamento igualmente compatível como a versão listada.

* Microsoft Edge 80
* Safari 13
* Cromo 80
* Firefox 74

## <a name="using-the-drawing-error-visualizer-tool"></a>Utilizando a ferramenta 'Visualizador de erros de desenho'

Depois de lançar a ferramenta Desenho Error Visualizer, será apresentada com a página de upload. A página de upload contém uma caixa de arrasto e gota. O arrastar & caixa de gota também funciona como botão que lança um diálogo do Explorador de Ficheiros.

:::image type="content" source="./media/drawing-errors-visualizer/start-page.png" alt-text="App de visualizador de erro de desenho - página de início":::

O  _ConversionWarningsAndErrors.jsno_ ficheiro foi colocado na raiz do diretório descarregado. Para carregar o _ConversionWarningsAndErrors.jspode_ arrastar & deixar cair o ficheiro na caixa ou clicar na caixa, encontrar o ficheiro no diálogo Do Explorador de Ficheiros e, em seguida, carregar o ficheiro.

:::image type="content" source="./media/drawing-errors-visualizer/loading-data.gif" alt-text="App de visualizador de erro de desenho - página de início":::

Assim que o _ConversionWarningsAndErrors.jsem_ cargas de ficheiros, verá uma lista dos erros e avisos do seu pacote de desenho. Cada erro ou aviso é especificado pela camada, nível e uma mensagem detalhada. Para ver informações detalhadas sobre um erro ou aviso, clique no link **Detalhes.** Uma secção intratável aparecerá então abaixo da lista. Pode agora navegar para cada erro para saber mais detalhes sobre como resolver o erro.

:::image type="content" source="./media/drawing-errors-visualizer/errors.png" alt-text="App de visualizador de erro de desenho - página de início":::

## <a name="next-steps"></a>Passos seguintes

Uma vez que o seu [pacote De desenho satisfaça os requisitos,](drawing-requirements.md)pode utilizar o [serviço Azure Maps Dataset](https://docs.microsoft.com/rest/api/maps/conversion) para converter o pacote Drawing num conjunto de dados. Em seguida, pode utilizar o módulo web Indoor Maps para desenvolver a sua aplicação. Saiba mais lendo os seguintes artigos:

> [!div class="nextstepaction"]
> [Códigos de erro de conversão de desenho](drawing-conversion-error-codes.md)

> [!div class="nextstepaction"]
> [Criador para mapas interiores](creator-indoor-maps.md)

> [!div class="nextstepaction"]
> [Utilize o módulo Mapas Interiores](how-to-use-indoor-module.md)

> [!div class="nextstepaction"]
> [Implementar estilo dinâmico de mapa interior](indoor-map-dynamic-styling.md)