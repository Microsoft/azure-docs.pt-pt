---
title: Use o Criador para criar mapas interiores
description: Utilize o Criador do Azure Maps para criar mapas interiores.
author: anastasia-ms
ms.author: v-stharr
ms.date: 05/18/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 5a7f64ead6d2e19242950002feed1cd1491dbacc
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83598767"
---
# <a name="use-creator-to-create-indoor-maps"></a>Use o Criador para criar mapas interiores

Este tutorial mostra-lhe como criar mapas interiores. Neste tutorial, você vai aprender a usar a API para:

> [!div class="checklist"]
> * Faça upload do seu pacote de desenho de mapa interior
> * Converta o seu pacote de desenho em dados de mapas
> * Crie um conjunto de dados a partir dos dados do seu mapa
> * Crie um azulejo a partir dos dados do seu conjunto de dados
> * Consulta do Serviço de Funcionalidadeweb Azure Maps (WFS) API para saber sobre as funcionalidades do seu mapa
> * Crie um estado de funcionalidade utilizando as funcionalidades do seu mapa e os dados no seu conjunto de dados
> * Atualize o seu estado de funcionalidade

## <a name="prerequisites"></a>Pré-requisitos

Para criar mapas interiores:

1. [Faça uma conta Azure Maps](quick-demo-map-app.md#create-an-account-with-azure-maps)
2. [Obtenha uma chave de subscrição primária,](quick-demo-map-app.md#get-the-primary-key-for-your-account)também conhecida como a chave principal ou a chave de subscrição.
3. [Criar um recurso Criador](how-to-manage-creator.md)
4. Descarregue o [pacote de desenho de amostras](https://github.com/Azure-Samples/am-creator-indoor-data-examples).

Este tutorial utiliza a aplicação [Postman,](https://www.postman.com/) mas pode escolher um ambiente de desenvolvimento diferente da API.

## <a name="upload-a-drawing-package"></a>Faça upload de um pacote de desenho

Utilize a [API](https://docs.microsoft.com/rest/api/maps/data/uploadpreview) de upload de dados para fazer o upload do pacote de desenho para os recursos do Azure Maps.

O Data Upload API é uma transação de longo prazo que implementa o padrão definido aqui. Assim que a operação terminar, usaremos o pacote `udid` para aceder ao pacote enviado para convertê-lo. Siga os passos abaixo para obter o `udid` .

1. Abra a aplicação do Carteiro. Perto do topo da aplicação Postman, selecione **New**. Na janela **Criar Nova,** selecione **Coleção**.  Nomeie a coleção e selecione o botão **Criar.**

2. Para criar o pedido, selecione **Nova** novamente. Na janela **Criar Nova,** selecione **Pedido**. Insira um **nome de Pedido** para o pedido. Selecione a coleção que criou no passo anterior e, em seguida, selecione **Guardar**.

3. Selecione o método **POST** HTTP no separador construtor e introduza o seguinte URL para fazer o upload do pacote Descarregamento para o serviço Azure Maps. Para este pedido, e outros pedidos mencionados neste artigo, `<Azure-Maps-Primary-Subscription-key>` substitua-o pela sua chave de subscrição primária.

    ```http
    https://atlas.microsoft.com/mapData/upload?api-version=1.0&dataFormat=zip&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

4. No separador **Cabeçalhos,** especifique um valor para a `Content-Type` tecla. O pacote de desenho é uma pasta com fecho, por isso use o `application/octet-stream` valor. No separador **Corpo,** **selecione binário**. Clique em **Select File** e escolha um pacote de desenho.

     ![gestão de dados](./media/tutorial-creator-indoor-maps/enter-content-type.png)

5. Clique no botão de **enviar** azul e aguarde o pedido para processar. Uma vez que o pedido esteja concluído, vá ao separador **Cabeçalhos** da resposta. Copie o valor da chave **Localização,** que é a `status URL` .

6. Para verificar o estado da chamada API, crie um pedido GET HTTP no `status URL` . Terá de anexar a sua chave de subscrição primária ao URL para autenticação.

    ```http
    https://atlas.microsoft.com/mapData/operations/{operationsId}?api-version=1.0&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

7. Quando **o** pedido GET HTTP estiver concluído com sucesso, pode utilizar o `resourceLocation` URL para recuperar metadados deste recurso no próximo passo.

    ```json
    {
        "operationId": "{operationId}",
        "status": "Succeeded",
        "resourceLocation": "https://atlas.microsoft.com/mapData/metadata/{upload-udid}?api-version=1.0"
    }
    ```

8. Para recuperar metadados de conteúdo, crie um pedido **GET** HTTP no `resourceLocation` URL copiado no passo 7. O corpo de resposta contém um único `udid` para o conteúdo carregado, a localização para aceder/descarregar o conteúdo no futuro, e alguns outros metadados sobre o conteúdo como data criada/atualizada, tamanho, e assim por diante. Um exemplo da resposta global é:

     ```json
    {
        "udid": "{udid}",
        "location": "https://atlas.microsoft.com/mapData/{udid}?api-version=1.0",
        "created": "2020-02-03T02:32:25.0509366+00:00",
        "updated": "2020-02-11T06:12:13.0309351+00:00",
        "sizeInBytes": 766,
        "uploadStatus": "Completed"
    }
    ```

## <a name="convert-a-drawing-package"></a>Converter um pacote de desenho

 Agora que o pacote de desenho é carregado, usaremos para o pacote enviado para converter o pacote em dados de `udid` mapa. A API de conversão utiliza uma transação de longo prazo que implementa o padrão definido [aqui](creator-long-running-operation.md). Assim que a operação terminar, usaremos os `conversionId` para aceder aos dados convertidos. Siga os passos abaixo para obter o `conversionId` .

1. Selecione **New**. Na janela **Criar Nova,** selecione **Pedido**. Insira um **nome De Pedido** e selecione uma coleção. Clique em **Guardar**.

2. Selecione o método **POST** HTTP no separador construtor e introduza o seguinte URL para converter o seu pacote de desenho carregado em dados de mapa. Utilize o `udid` para o pacote carregado.

    ```http
    https://atlas.microsoft.com/conversion/convert?subscription-key={Azure-Maps-Primary-Subscription-key}&api-version=1.0&udid={upload-udid}&inputType=DWG
    ```

3. Clique no botão **Enviar** e aguarde o pedido para processar. Uma vez que o pedido esteja concluído, vá ao separador **Cabeçalhos** da resposta e procure a chave **localização.** Copiar o valor da chave **Localização,** que é o pedido de `status URL` conversão.

4. Inicie um novo método **GET** HTTP no separador construtor. Aprete a chave de subscrição primária do Azure Maps para `status URL` o . Faça **um** pedido GET no `status URL` passo anterior. Se o processo de conversão ainda não tiver concluído, poderá ver algo como a seguinte resposta json:

    ```json
    {
        "operationId": "77dc9262-d3b8-4e32-b65d-74d785b53504",
        "created": "2020-04-22T19:39:54.9518496+00:00",
        "status": "Running"
    }
    ```

5. Assim que o pedido for concluído com sucesso, verá uma mensagem de estado de sucesso no corpo de resposta.  Copie o `conversionId` URL para o pacote `resourceLocation` convertido. A `conversionId` API é utilizada por outras API para aceder aos dados do mapa convertidos.

    ```json
   {
        "operationId": "77dc9262-d3b8-4e32-b65d-74d785b53504",
        "created": "2020-04-22T19:39:54.9518496+00:00",
        "status": "Succeeded",
        "resourceLocation": "https://atlas.microsoft.com/conversion/{conversionId}?api-version=1.0",
        "properties": {}
    }
    ```

>[!NOTE]
>A aplicação Postman não suporta de forma nativa os pedidos de longa duração http long running. Como resultado, poderá notar um longo atraso ao fazer **um** pedido get no URL de estado.  Aguarde cerca de 30 segundos e tente clicar novamente no botão **Enviar** até que a resposta mostre sucesso ou falha.

A embalagem de desenho da amostra deve ser convertida sem erros ou advertências. No entanto, se receber erros ou avisos do seu próprio pacote de desenho, a resposta JSON irá dar-lhe uma ligação ao [visualizador de erro de desenho](drawing-error-visualizer.md). O visualizador de erro de desenho permite-lhe inspecionar os detalhes de erros e advertências. Para receber recomendações sobre como resolver erros de conversão e avisos, consulte os erros e avisos de [conversão do Desenho.](drawing-conversion-error-codes.md)

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

## <a name="create-a-dataset"></a>Criar um conjunto de dados

O conjunto de dados é uma coleção de características do mapa, tais como edifícios, níveis e quartos. Para criar um conjunto de dados, utilize o [Dataset Create API](https://docs.microsoft.com/rest/api/maps/dataset/createpreview). O conjunto de dados Create API leva o `conversionId` pacote de desenho convertido e devolve um dos `datasetId` conjuntos de dados criados. Os passos abaixo mostram-lhe como criar um conjunto de dados.

1. Na aplicação Postman, selecione **New**. Na janela **Criar Nova,** selecione **Pedido**. Insira um **nome De Pedido** e selecione uma coleção. Clique em **Guardar**

2. Faça um pedido **de POST** ao [Dataset Criar API](https://docs.microsoft.com/rest/api/maps/dataset/createpreview) para criar um novo conjunto de dados. Antes de submeter o pedido, apreenda a sua chave de subscrição e a obtida durante o processo de `conversionId` `conversionId` Conversão no passo 5.  O pedido deve parecer o seguinte URL:

    ```http
    https://atlas.microsoft.com/dataset/create?api-version=1.0&conversionID={conversionId}&type=facility&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

3. Obtenha a chave de `statusURL` **localização** dos **cabeçalhos**de resposta .

4. Faça **um** pedido GET no para `statusURL` obter o `datasetId` . Aplique a chave de subscrição primária do Azure Maps para autenticação. O pedido deve parecer o seguinte URL:

    ```http
    https://atlas.microsoft.com/dataset/operations/{operationsId}?api-version=1.0&subscription-key=<Azure-Maps-Primary-Subscription-key>
    ```

5. Quando o pedido **GET** HTTP estiver concluído com sucesso, o cabeçalho de resposta conterá o conjunto de `datasetId` dados criado. Copie o `datasetId` . Você precisa usar o `datasetId` para criar um azulejo.

    ```json
    {
        "operationId": "a93570cb-3e4f-4e45-a2b1-360df174180a",
        "created": "2020-04-22T19:52:38.9352189+00:00",
        "status": "Succeeded",
        "resourceLocation": "https://azure.microsoft.com/dataset/{datasetiId}?api-version=1.0"
     }
    ```

## <a name="create-a-tileset"></a>Criar um azulejo

Um azulejo é um conjunto de azulejos vetoriais que renderizam no mapa. Os tilesets são criados a partir de conjuntos de dados existentes. No entanto, um azulejo é independente do conjunto de dados a partir do qual foi produzido. Se o conjunto de dados for eliminado, o azulejo continuará a existir. Para criar um azulejo, siga os passos abaixo:

1. Na aplicação Postman, selecione **New**. Na janela **Criar Nova,** selecione **Pedido**. Insira um **nome De Pedido** e selecione uma coleção. Clique em **Guardar**

2. Faça um pedido **post** no separador do construtor. O URL de pedido deve parecer o seguinte URL:

    ```http
    https://atlas.microsoft.com/tileset/create/vector?api-version=1.0&datasetID={datasetId}&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

3. Faça **um** pedido get no `statusURL` para o azulejo. Aplique a chave de subscrição primária do Azure Maps para autenticação. O pedido deve parecer o seguinte URL:

   ```http
    https://atlas.microsoft.com/tileset/operations/{operationsId}?api-version=1.0&subscription-key=<Azure-Maps-Primary-Subscription-key>
    ```

4. Quando o pedido **GET** HTTP estiver concluído com sucesso, o cabeçalho de resposta conterá o `tilesetId` para o azulejo criado. Copie o `tilesetId` .

    ```json
    {
        "operationId": "a93570cb-3e4f-4e45-a2b1-360df174180a",
        "createdDateTime": "3/11/2020 8:45:13 PM +00:00",
        "status": "Succeeded",
        "resourceLocation": "https://atlas.microsoft.com/tileset/{tilesetId}?api-version=1.0"
    }
    ```

## <a name="query-datasets-with-wfs-api"></a>Conjuntos de dados de consulta com WFS API

 Os conjuntos de dados podem ser consultados utilizando [a WfS API](https://docs.microsoft.com/rest/api/maps/wfs). Com a API WFS pode consultar coleções de recursos, uma coleção específica ou uma funcionalidade específica com um **ID**de funcionalidade . O **ID** da funcionalidade identifica exclusivamente a funcionalidade dentro do conjunto de dados. É usado, por exemplo, para identificar qual o estado de características que deve ser atualizado num dado estado.

1. Na aplicação Postman, selecione **New**. Na janela **Criar Nova,** selecione **Pedido**. Insira um **nome De Pedido** e selecione uma coleção. Clique em **Guardar**

2. Faça um pedido **GET** para visualizar uma lista das coleções no seu conjunto de dados. `<dataset-id>`Substitua-a com o seu `datasetId` . Utilize a chave primária do Azure Maps em vez do espaço reservado. O pedido deve parecer o seguinte URL:

    ```http
    https://atlas.microsoft.com/wfs/datasets/{datasetId}/collections?subscription-key={Azure-Maps-Primary-Subscription-key}&api-version=1.0
    ```

3. O corpo de resposta será entregue em formato GeoJSON e conterá todas as coleções no conjunto de dados. Para a simplicidade, o exemplo aqui só mostra a `unit` coleção. Para ver um exemplo que contém todas as coleções, consulte [WFS Descrever Coleções API](https://docs.microsoft.com/rest/api/maps/wfs/describecollectionspreview). Para saber mais sobre qualquer coleção, pode clicar em qualquer um dos URLs dentro do `link` elemento.

    ```json
    {
    "collections": [
        {
            "name": "unit",
            "description": "A physical and non-overlapping area which might be occupied and traversed by a navigating agent. Can be a hallway, a room, a courtyard, etc. It is surrounded by physical obstruction (wall), unless the is_open_area attribute is equal to true, and one must add openings where the obstruction shouldn't be there. If is_open_area attribute is equal to true, all the sides are assumed open to the surroundings and walls are to be added where needed. Walls for open areas are represented as a line_element or area_element with is_obstruction equal to true.",
            "links": [
                {
                    "href": "https://atlas.microsoft.com/wfs/datasets/{datasetId}/collections/unit/definition?api-version=1.0",
                    "rel": "describedBy",
                    "title": "Metadata catalogue for unit"
                },
                {
                    "href": "https://atlas.microsoft.com/wfs/datasets/{datasetId}/collections/unit/items?api-version=1.0",
                    "rel": "data",
                    "title": "unit"
                }
                {
                    "href": "https://atlas.microsoft.com/wfs/datasets/{datasetId}/collections/unit?api-version=1.0",
                    "rel": "self",
                    "title": "Metadata catalogue for unit"
                }
            ]
        },
    ```

4. Faça um pedido **GET** para as coleções de `unit` recursos.  `{datasetId}`Substitua-a com o seu `datasetId` . Utilize a chave primária do Azure Maps em vez do espaço reservado. O corpo de resposta conterá todas as características da `unit` coleção. O pedido deve parecer o seguinte URL:

    ```http
    https://atlas.microsoft.com/wfs/datasets/{datasetId}/collections/unit/items?subscription-key={Azure-Maps-Primary-Subscription-key}&api-version=1.0
    ```

5. Copie a funcionalidade para uma funcionalidade de unidade que tenha propriedades de `id` estilo que podem ser modificadas dinamicamente.  Como o estado de ocupação e a temperatura da unidade podem ser atualizados dinamicamente, usaremos esta funcionalidade `id` na secção seguinte. No exemplo seguinte, a funcionalidade `id` é "UNIT26". Vamos referir-nos às propriedades de estilo desta funcionalidade como estados, e usaremos a funcionalidade para fazer um conjunto de estados.

     ```json
    {
        "type": "FeatureCollection",
        "features": [
            {
                "type": "Feature",
                "geometry": {
                    "type": "Polygon",
                    "coordinates": ["..."]
                },
                "properties": {
                    "original_id": "b7410920-8cb0-490b-ab23-b489fd35aed0",
                    "category_id": "CTG8",
                    "is_open_area": true,
                    "navigable_by": [
                        "pedestrian"
                    ],
                    "route_through_behavior": "allowed",
                    "level_id": "LVL14",
                    "occupants": [],
                    "address_id": "DIR1",
                    "name": "157"
                },
                "id": "UNIT26",
                "featureType": ""
            }, {"..."}
        ]
    }
    ```

## <a name="create-a-feature-stateset"></a>Criar um conjunto de estado de recurso

1. Na aplicação Postman, selecione **New**. Na janela **Criar Nova,** selecione **Pedido**. Insira um **nome De Pedido** e selecione uma coleção. Clique em **Guardar**

2. Faça um pedido **de POST** para a [Create Stateset API](https://docs.microsoft.com/rest/api/maps/featurestate/createstatepreview). Utilize o `datasetId` conjunto de dados que contém o estado que pretende modificar. O pedido deve parecer o seguinte URL:

    ```http
    https://atlas.microsoft.com/featureState/stateset?api-version=1.0&datasetId={datasetId}&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

3. Nos **Cabeçalhos** do PEDIDO **POST,** definido `Content-Type` para `application/json` . No **Corpo,** forneça os estilos abaixo para refletir as alterações nos `occupied` e `temperature` *estados.* Quando terminar, clique em **Enviar**.

    ```json
    {
       "styles":[
          {
             "keyname":"occupied",
             "type":"boolean",
             "rules":[
                {
                   "true":"#FF0000",
                   "false":"#00FF00"
                }
             ]
          },
          {
             "keyname":"temperature",
             "type":"number",
             "rules":[
                {
                   "range":{
                      "exclusiveMaximum":66
                   },
                   "color":"#00204e"
                },
                {
                   "range":{
                      "minimum":66,
                      "exclusiveMaximum":70
                   },
                   "color":"#0278da"
                },
                {
                   "range":{
                      "minimum":70,
                      "exclusiveMaximum":74
                   },
                   "color":"#187d1d"
                },
                {
                   "range":{
                      "minimum":74,
                      "exclusiveMaximum":78
                   },
                   "color":"#fef200"
                },
                {
                   "range":{
                      "minimum":78,
                      "exclusiveMaximum":82
                   },
                   "color":"#fe8c01"
                },
                {
                   "range":{
                      "minimum":82
                   },
                   "color":"#e71123"
                }
             ]
          }
       ]
    }
    ```

4. Copie o `statesetId` corpo de resposta.

5. Crie um pedido **POST** para atualizar o estado: Passe o statesetId e funcionalidade com a chave de `ID` subscrição do Azure Maps. O pedido deve parecer o seguinte URL:

    ```http
    https://atlas.microsoft.com/featureState/state?api-version=1.0&statesetID={statesetId}&featureID={featureId}&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

6. Nos **Cabeçalhos** do PEDIDO **POST,** definido `Content-Type` para `application/json` . No **pedido** do **POST,** copie e cole o JSON na amostra abaixo.

    ```json
    {
        "states": [
            {
                "keyName": "occupied",
                "value": true,
                "eventTimestamp": "2019-11-14T17:10:20"
            }
        ]
    }
    ```

    >[!NOTE]
    > A atualização só será guardada se o carimbo de tempo registado for posterior ao carimbo de tempo do pedido anterior. Podemos passar qualquer nome-chave que configuramos anteriormente durante a criação.

7. Após uma atualização bem sucedida, receberá um código de `200 OK` estado HTTP. Se tiver um [estilo dinâmico implementado](indoor-map-dynamic-styling.md) para um mapa interior, a atualização irá apresentar no seu mapa renderizado com a hora especificada.

A [API Feature Get States](https://docs.microsoft.com/rest/api/maps/featurestate/getstatespreview) permite-lhe recuperar o estado de uma funcionalidade utilizando a sua funcionalidade `ID` . Também pode eliminar o conjunto de estados e os seus recursos utilizando a API de [Exclusão](https://docs.microsoft.com/rest/api/maps/featurestate/deletestatesetpreview)do Estado de Recurso .

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Faça upload do seu pacote de desenho de mapa interior
> * Converta o seu pacote de desenho em dados de mapas
> * Crie um conjunto de dados a partir dos dados do seu mapa
> * Crie um azulejo a partir dos dados do seu conjunto de dados
> * Consulta do serviço AZURE Maps WFS para saber sobre as funcionalidades do seu mapa
> * Crie um estado de funcionalidade utilizando as funcionalidades do seu mapa e os dados no seu conjunto de dados
> * Atualize o seu estado de funcionalidade

Está agora equipado com as habilidades necessárias para passar aos próximos guias:

> [!div class="nextstepaction"]
> [Utilize o módulo Mapas Interiores](how-to-use-indoor-module.md)

> [!div class="nextstepaction"]
> [Implementar estilo dinâmico para mapas interiores](indoor-map-dynamic-styling.md)

Saiba mais sobre os diferentes serviços do Azure Maps discutidos neste artigo:

> [!div class="nextstepaction"]
> [Upload de dados](creator-indoor-maps.md#upload-a-drawing-package)

> [!div class="nextstepaction"]
> [Conversão de Dados](creator-indoor-maps.md#convert-a-drawing-package)

> [!div class="nextstepaction"]
> [Conjunto de dados](creator-indoor-maps.md#datasets)

> [!div class="nextstepaction"]
> [Azulejo](creator-indoor-maps.md#tilesets)

> [!div class="nextstepaction"]
> [Conjunto de Estado de Recurso](creator-indoor-maps.md#feature-statesets)

> [!div class="nextstepaction"]
> [Serviço WFS](creator-indoor-maps.md#web-feature-service-api)
