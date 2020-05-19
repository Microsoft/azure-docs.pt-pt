---
title: Utilize o visualizador de erro de desenho do Azure Maps
description: Neste artigo, você aprenderá sobre como visualizar avisos e erros devolvidos pela API de Conversão de Criadores.
author: anastasia-ms
ms.author: v-stharr
ms.date: 05/18/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: e1b5b16d5522285f2d028303f3295cc1fb740330
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83598816"
---
# <a name="using-the-azure-maps-drawing-error-visualizer"></a>Usando o visualizador de erro de desenho do Azure Maps

O Visualizador de Erro de Desenho é uma aplicação web autónoma que exibe [avisos de pacotes de desenho e erros detetados](drawing-conversion-error-codes.md) durante o processo de conversão. A aplicação web Error Visualizer consiste numa página estática que pode utilizar sem se ligar à internet.  Pode utilizar o Error Visualizer para corrigir erros e avisos de acordo com [os requisitos](drawing-requirements.md)do pacote de desenho . A API de [conversão do Azure Maps](https://docs.microsoft.com/rest/api/maps/data/conversion) só devolve uma resposta com uma ligação ao Error Visualizer apenas quando um erro é detetado.

## <a name="prerequisites"></a>Pré-requisitos

Antes de poder baixar o Visualizador de Erro de Desenho, terá de:

1. [Criar uma conta do Azure Maps](quick-demo-map-app.md#create-an-account-with-azure-maps)
2. [Obtenha uma chave de subscrição primária,](quick-demo-map-app.md#get-the-primary-key-for-your-account)também conhecida como a chave principal ou a chave de subscrição.
3. [Criar um recurso Criador](how-to-manage-creator.md)

Este tutorial utiliza a aplicação [Postman,](https://www.postman.com/) mas pode escolher um ambiente de desenvolvimento diferente da API.

## <a name="download"></a>Download

1. Faça o upload do seu pacote de Desenho para o serviço Azure Maps Creator para obter um `udid` para o pacote carregado. Para obter passos sobre como carregar um pacote, consulte [o upload de um pacote de desenho](tutorial-creator-indoor-maps.md#upload-a-drawing-package).

2. Agora que o pacote de desenho é carregado, usaremos para o pacote enviado para converter o pacote em dados de `udid` mapa. Para obter passos sobre como converter um pacote, consulte [Converter um pacote de desenho](tutorial-creator-indoor-maps.md#convert-a-drawing-package).

    >[!NOTE]
    >Se o seu processo de conversão for bem sucedido, não receberá um link para a ferramenta Error Visualizer.

3. Sob o separador **Cabeçalhos** de resposta na aplicação Carteiro, procure a `diagnosticPackageLocation` propriedade, devolvida pela API de Conversão. A resposta deve aparecer como a seguinte JSON:

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

Dentro do pacote com fecho descarregado a partir do `diagnosticPackageLocation` link, você encontrará dois ficheiros.

* _VisualizaçãoTool.zip_: Contém o código fonte, os meios de comunicação e a página web para o Visualizador de Erro de Desenho.
* _ConversãoAvisosAndErrors.json_: Contém uma lista formatada de avisos, erros e detalhes adicionais que são usados pelo Visualizador de Erros de Desenho.

Desinserte a pasta _VisualizationTool.zip._ Contém os seguintes itens:

* pasta _de ativos:_ contém imagens e ficheiros de mídia
* pasta _estática:_ código fonte
* _ficheiro index.html:_ a aplicação web.

Abra o ficheiro _index.html_ utilizando qualquer um dos navegadores abaixo, com o número da versão respetiva. Pode utilizar uma versão diferente, se a versão oferecer um comportamento igualmente compatível como a versão listada.

* Microsoft Edge 80
* Safari 13
* Chrome 80
* Firefox 74

## <a name="using-the-drawing-error-visualizer-tool"></a>Utilizando a ferramenta de visualização de erros de desenho

Depois de lançar a ferramenta Descarregador de Erro de Desenho, será apresentado com a página de upload. A página de upload contém uma caixa de arrasto e gota. A caixa de lançamento de drag & também funciona como botão que lança um diálogo do Explorador de Ficheiros.

![App de visualização de erro de desenho - página inicial](./media/drawing-errors-visualizer/start-page.png)

O ficheiro _ConversionWarningsAndErrors.json_ foi colocado na raiz do diretório descarregado. Para carregar o _ConversionWarningsAndErrors.json_ pode arrastar & deixar cair o ficheiro na caixa ou clicar na caixa, encontrar o ficheiro no diálogo do File Explorer e, em seguida, carregar o ficheiro.

![Drawing Error Visualizer App - Arrastar e largar para carregar dados](./media/drawing-errors-visualizer/loading-data.gif)

Assim que o ficheiro _ConversionWarningsAndErrors.json_ for carregado, verá uma lista dos erros e avisos do pacote de desenho. Cada erro ou aviso é especificado pela camada, nível e uma mensagem detalhada. Pode agora navegar para cada erro para saber mais detalhes sobre como resolver o erro.  

![App de visualização de erros de desenho - Erros e Avisos](./media/drawing-errors-visualizer/errors.png)

## <a name="next-steps"></a>Passos seguintes

Assim que o seu [pacote de desenho satisfizer os requisitos,](drawing-requirements.md)pode utilizar o [serviço De dataset Do Azure Maps](https://docs.microsoft.com/rest/api/maps/data/conversion) para converter o pacote Desacato num conjunto de dados. Em seguida, pode utilizar o módulo web Indoor Maps para desenvolver a sua aplicação. Saiba mais lendo os seguintes artigos:

> [!div class="nextstepaction"]
> [Códigos de erro de conversão de desenho](drawing-conversion-error-codes.md)

> [!div class="nextstepaction"]
> [Criador de mapas interiores](creator-indoor-maps.md)

> [!div class="nextstepaction"]
> [Utilize o módulo Mapas Interiores](how-to-use-indoor-module.md)

> [!div class="nextstepaction"]
> [Implementar o estilo dinâmico do mapa interior](indoor-map-dynamic-styling.md)