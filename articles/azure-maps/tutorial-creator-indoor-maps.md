---
title: 'Tutorial: Use o Microsoft Azure Maps Creator (Preview) para criar mapas interiores'
description: Tutorial sobre como usar o Microsoft Azure Maps Creator (Preview) para criar mapas interiores
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 9ac53dab29feddd36a95b8b2b041caaf5c3598d5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101720142"
---
# <a name="tutorial-use-creator-preview-to-create-indoor-maps"></a>Tutorial: Use Creator (Preview) para criar mapas interiores

> [!IMPORTANT]
> Os serviços do Azure Maps Creator estão atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Este tutorial mostra-lhe como criar mapas interiores. Neste tutorial, você aprenderá a usar a API para:

> [!div class="checklist"]
> * Faça upload do seu pacote de desenho de mapa interior
> * Converta o seu pacote de desenho em dados de mapas
> * Crie um conjunto de dados a partir dos dados do seu mapa
> * Crie um teesto a partir dos dados do seu conjunto de dados
> * Consultar a API do Azure Maps Web Feature Service (WFS) para conhecer as funcionalidades do seu mapa
> * Crie um estado de funcionalidade utilizando as funcionalidades do seu mapa e os dados no seu conjunto de dados
> * Atualize o seu estado de funcionalidade

## <a name="prerequisites"></a>Pré-requisitos

Para criar mapas interiores:

1. [Faça uma conta Azure Maps](quick-demo-map-app.md#create-an-azure-maps-account)
2. [Obtenha uma chave de subscrição primária,](quick-demo-map-app.md#get-the-primary-key-for-your-account)também conhecida como a chave primária ou a chave de subscrição.
3. [Criar um recurso Criador (Pré-visualização)](how-to-manage-creator.md)
4. Descarregue o [pacote sample drawing](https://github.com/Azure-Samples/am-creator-indoor-data-examples/blob/master/Sample%20-%20Contoso%20Drawing%20Package.zip).

Este tutorial usa a aplicação [Do Carteiro,](https://www.postman.com/) mas você pode escolher um ambiente de desenvolvimento de API diferente.

>[!IMPORTANT]
> Os urls API neste documento podem ter de ser ajustados de acordo com a localização do seu recurso Criador. Para mais detalhes, consulte [Acesso aos Serviços de Criador.](how-to-manage-creator.md#access-to-creator-services)

## <a name="upload-a-drawing-package"></a>Faça upload de um pacote de desenho

Utilize a [API de upload de dados](/rest/api/maps/data/uploadpreview) para carregar o pacote Drawing para os recursos do Azure Maps.

A API de Data Upload é uma transação de longa duração que implementa o padrão definido aqui. Assim que a operação estiver concluída, usaremos o `udid` para aceder ao pacote carregado para convertê-lo. Siga os passos abaixo para obter o `udid` .

1. Abra a aplicação do Carteiro. Perto do topo da aplicação Postman, selecione **New**. Na janela **Criar Nova,** selecione **Coleção**.  Nomeie a coleção e selecione o botão **Criar.**

2. Para criar o pedido, selecione **New** novamente. Na janela **Criar Novo,** selecione **Request**. Insira um **nome de Pedido** para o pedido. Selecione a coleção criada no passo anterior e, em seguida, **selecione Guardar**.

3. Selecione o método **POST** HTTP no separador construtor e introduza o seguinte URL para carregar o pacote Drawing para o serviço Azure Maps. Para este pedido, e outros pedidos mencionados neste artigo, `{Azure-Maps-Primary-Subscription-key}` substitua-o pela sua chave de subscrição primária.

    ```http
    https://atlas.microsoft.com/mapData/upload?api-version=1.0&dataFormat=zip&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

4. No **separador Cabeçalhos,** especifique um valor para a `Content-Type` chave. A embalagem de desenho é uma pasta com fecho, por isso use o `application/octet-stream` valor. No **separador Corpo,** selecione **binário**. Clique em **Select File** e escolha um pacote de desenho.

     ![gestão de dados](./media/tutorial-creator-indoor-maps/enter-content-type-dialog.png)

5. Clique no botão **'Enviar'** azul e aguarde que o pedido seja processado. Assim que o pedido estiver concluído, vá ao **separador Cabeçalhos** da resposta. Copie o valor da chave **localização,** que é a `status URL` .

6. Para verificar o estado da chamada da API, crie um pedido **GET** HTTP sobre o `status URL` . Terá de anexar a chave de subscrição primária do URL para autenticação. O pedido **GET** deve parecer-se com o seguinte URL:

    ```http
    https://atlas.microsoft.com/mapData/operations/<operationId>?api-version=1.0&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

7. Quando o pedido **GET** HTTP estiver concluído com sucesso, retornará um `resourceLocation` . Contém `resourceLocation` o único para o conteúdo `udid` carregado. Opcionalmente, pode utilizar o `resourceLocation` URL para recuperar metadados deste recurso no passo seguinte.

    ```json
    {
        "status": "Succeeded",
        "resourceLocation": "https://atlas.microsoft.com/mapData/metadata/{udid}?api-version=1.0"
    }
    ```

8. Para recuperar metadados de conteúdo, crie um pedido **GET** HTTP no `resourceLocation` URL que foi recuperado no passo 7. Certifique-se de anexar a chave de subscrição primária ao URL para autenticação. O pedido **GET** deve gostar do seguinte URL:

    ```http
   https://atlas.microsoft.com/mapData/metadata/{udid}?api-version=1.0&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

9. Quando o pedido **GET** HTTP estiver concluído com sucesso, o organismo de resposta conterá `udid` o especificado no passo `resourceLocation` 7, a localização para aceder/descarregar o conteúdo no futuro, e outros metadados sobre o conteúdo como a data, tamanho e assim por diante criados/atualizados. Um exemplo da resposta global é:

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

 Agora que o pacote de desenho está carregado, usaremos `udid` para o pacote carregado para converter o pacote em dados de mapa. A API de Conversão utiliza uma transação de longa duração que implementa o padrão definido [aqui.](creator-long-running-operation.md) Assim que a operação estiver concluída, usaremos o `conversionId` para aceder aos dados convertidos. Siga os passos abaixo para obter o `conversionId` .

1. Selecione **Nova**. Na janela **Criar Novo,** selecione **Request**. Insira um **nome De pedido** e selecione uma coleção. Clique em **Guardar**.

2. Selecione o método **POST** HTTP no separador construtor e introduza o seguinte URL para converter o seu pacote de desenho carregado em dados de mapa. Use o `udid` para o pacote carregado.

    ```http
    https://atlas.microsoft.com/conversion/convert?subscription-key={Azure-Maps-Primary-Subscription-key}&api-version=1.0&udid={udid}&inputType=DWG
    ```

    >[!IMPORTANT]
    > Os urls API neste documento podem ter de ser ajustados de acordo com a localização do seu recurso Criador. Para mais detalhes, consulte [os serviços de Acesso ao Criador (Pré-visualização) ](how-to-manage-creator.md#access-to-creator-services).
    > Se receber um erro com `"RequiresCreatorResource"` código, certifique-se de que [aprovisionou um recurso Azure Maps Creator](how-to-manage-creator.md) na sua conta Azure Maps.

3. Clique no botão **Enviar** e aguarde que o pedido processe. Assim que o pedido estiver concluído, vá ao **separador Cabeçalhos** da resposta e procure a chave **localização.** Copie o valor da chave **Localização,** que é o `status URL` pedido de conversão. Vais usar isto no próximo passo.

    :::image type="content" source="./media/tutorial-creator-indoor-maps/copy-location-uri-dialog.png" border="true" alt-text="Copiar o valor da chave de localização":::

4. Inicie um novo método **GET** HTTP no separador construtor. Apecir a chave de subscrição primária do Azure Maps para o `status URL` . Faça um pedido **GET** no `status URL` que copiou no passo 3. O `status URL` parece o seguinte URL:

    ```http
    https://atlas.microsoft.com/conversion/operations/<operationId>?api-version=1.0&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

    Se o processo de conversão ainda não tiver sido concluído, poderá ver algo como a seguinte resposta JSON:

    ```json
    {
        "operationId": "<operationId>",
        "created": "2020-04-22T19:39:54.9518496+00:00",
        "status": "Running"
    }
    ```

5. Assim que o pedido estiver concluído com sucesso, verá uma mensagem de estado de sucesso no corpo de resposta.  Copie `conversionId` o do URL para o pacote `resourceLocation` convertido. O `conversionId` é usado por outra API para aceder aos dados do mapa convertidos.

    ```json
   {
        "operationId": "<operationId>",
        "created": "2020-04-22T19:39:54.9518496+00:00",
        "status": "Succeeded",
        "resourceLocation": "https://atlas.microsoft.com/conversion/{conversionId}?api-version=1.0",
        "properties": {}
    }
    ```

>[!NOTE]
>A aplicação Do Carteiro não suporta de forma nativa pedidos de execução longa http. Como resultado, poderá notar um longo atraso enquanto faz um pedido **GET** no URL de estado.  Aguarde cerca de 30 segundos e tente clicar novamente no botão **Enviar** novamente até que a resposta mostre sucesso ou falha.

A amostra Desenhada deve ser convertida sem erros ou avisos. No entanto, se receber erros ou avisos do seu próprio pacote de desenho, a resposta JSON dar-lhe-á uma ligação ao [visualizador de erros de desenho](drawing-error-visualizer.md). O visualizador de erro de desenho permite-lhe inspecionar os detalhes de erros e avisos. Para receber recomendações sobre como resolver erros e avisos de conversão, consulte os [erros e avisos de conversão de desenho](drawing-conversion-error-codes.md).

```json
{
    "operationId": "<operationId>",
    "created": "2020-04-22T19:39:54.9518496+00:00",
    "status": "Failed",
    "resourceLocation": "https://atlas.microsoft.com/conversion/{conversionId}?api-version=1.0",
    "properties": {
        "diagnosticPackageLocation": "https://atlas.microsoft.com/mapData/ce61c3c1-faa8-75b7-349f-d863f6523748?api-version=1.0"
    }
}
```

## <a name="create-a-dataset"></a>Criar um conjunto de dados

O conjunto de dados é uma coleção de características do mapa, tais como edifícios, níveis e quartos. Para criar um conjunto de dados, utilize o [Conjunto de Dados Criar API](/rest/api/maps/dataset/createpreview). O conjunto de dados Create API leva o `conversionId` pacote de desenho convertido e devolve um dos `datasetId` conjuntos de dados criados. Os passos abaixo mostram-lhe como criar um conjunto de dados.

1. Na aplicação Do Carteiro, selecione **New**. Na janela **Criar Novo,** selecione **Request**. Insira um **nome De pedido** e selecione uma coleção. Clique em **Guardar**

2. Faça um pedido **DE** POST ao [Conjunto de Dados Criar API](/rest/api/maps/dataset/createpreview) para criar um novo conjunto de dados. Antes de submeter o pedido, apedte a sua chave de subscrição e `conversionId` a com a obtida durante o processo de Conversão no passo `conversionId` 5.  O pedido deve parecer-se com o seguinte URL:

    ```http
    https://atlas.microsoft.com/dataset/create?api-version=1.0&conversionID={conversionId}&type=facility&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

3. Obtenha a `statusURL` chave **de localização** dos **cabeçalhos de** resposta .

4. Faça um pedido **GET** no `statusURL` para obter o `datasetId` . Apecir a chave de subscrição primária do Azure Maps para a autenticação. O pedido deve parecer-se com o seguinte URL:

    ```http
    https://atlas.microsoft.com/dataset/operations/<operationId>?api-version=1.0&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

5. Quando o pedido **GET** HTTP estiver concluído com sucesso, o cabeçalho de resposta conterá `datasetId` o conjunto de dados criado. Copiar o `datasetId` . Terá de usar o `datasetId` para criar um azulejo.

    ```json
    {
        "operationId": "<operationId>",
        "created": "2020-04-22T19:52:38.9352189+00:00",
        "status": "Succeeded",
        "resourceLocation": "https://azure.microsoft.com/dataset/{datasetiId}?api-version=1.0"
     }
    ```

## <a name="create-a-tileset"></a>Criar um teesto

Um azulejo é um conjunto de azulejos vetoriais que prestam no mapa. Os tilesets são criados a partir de conjuntos de dados existentes. No entanto, um teesto é independente do conjunto de dados a partir do qual foi obtido. Se o conjunto de dados for eliminado, o teset continuará a existir. Para criar um teesto, siga os passos abaixo:

1. Na aplicação Do Carteiro, selecione **New**. Na janela **Criar Novo,** selecione **Request**. Insira um **nome De pedido** e selecione uma coleção. Clique em **Guardar**

2. Faça um pedido **DEM** na conta do construtor. O URL do pedido deve parecer-se com o seguinte URL:

    ```http
    https://atlas.microsoft.com/tileset/create/vector?api-version=1.0&datasetID={datasetId}&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

3. Faça um pedido **GET** no `statusURL` para o azulejo. Apecir a chave de subscrição primária do Azure Maps para a autenticação. O pedido deve parecer-se com o seguinte URL:

   ```http
    https://atlas.microsoft.com/tileset/operations/<operationId>?api-version=1.0&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

4. Quando o pedido **GET** HTTP estiver concluído com sucesso, o cabeçalho de resposta conterá `tilesetId` o para o teesto criado. Copiar o `tilesetId` .

    ```json
    {
        "operationId": "<operationId>",
        "createdDateTime": "3/11/2020 8:45:13 PM +00:00",
        "status": "Succeeded",
        "resourceLocation": "https://atlas.microsoft.com/tileset/{tilesetId}?api-version=1.0"
    }
    ```

## <a name="query-datasets-with-wfs-api"></a>Conjuntos de dados de consulta com a API do WFS

 Conjuntos de dados podem ser consultados usando  [a API WFS](/rest/api/maps/wfs). Com a API WFS pode consultar-se para coleções de funcionalidades, uma coleção específica ou uma funcionalidade específica com um **ID de** recurso. O **ID de** funcionalidade identifica exclusivamente a funcionalidade dentro do conjunto de dados. É usado, por exemplo, para identificar que estado de recurso deve ser atualizado em um determinado estado.

1. Na aplicação Do Carteiro, selecione **New**. Na janela **Criar Novo,** selecione **Request**. Insira um **nome De pedido** e selecione uma coleção. Clique em **Guardar**

2. Faça um pedido **GET** para visualizar uma lista das coleções no seu conjunto de dados. `<dataset-id>`Substitua-a pela sua `datasetId` . Utilize a sua chave primária Azure Maps em vez do espaço reservado. O pedido deve parecer-se com o seguinte URL:

    ```http
    https://atlas.microsoft.com/wfs/datasets/{datasetId}/collections?subscription-key={Azure-Maps-Primary-Subscription-key}&api-version=1.0
    ```

3. O corpo de resposta será entregue em formato GeoJSON e conterá todas as coleções no conjunto de dados. Para simplificar, o exemplo aqui só mostra a `unit` coleção. Para ver um exemplo que contém todas as coleções, consulte [a WFS Descrever Coleções API.](/rest/api/maps/wfs/collectiondescriptionpreview) Para saber mais sobre qualquer coleção, pode clicar em qualquer um dos URLs dentro do `link` elemento.

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

4. Faça um pedido **GET** para as `unit` coleções de funcionalidades.  `{datasetId}`Substitua-a pela sua `datasetId` . Utilize a sua chave primária Azure Maps em vez do espaço reservado. O corpo de resposta conterá todas as características da `unit` coleção. O pedido deve parecer-se com o seguinte URL:

    ```http
    https://atlas.microsoft.com/wfs/datasets/{datasetId}/collections/unit/items?subscription-key={Azure-Maps-Primary-Subscription-key}&api-version=1.0
    ```

5. Copie a funcionalidade `id` para uma unidade que tenha propriedades de estilo que podem ser modificadas dinamicamente.  Como o estado de ocupação da unidade e a temperatura podem ser atualizados dinamicamente, usaremos esta funcionalidade `id` na secção seguinte. No exemplo seguinte, a função `id` é "UNIT26". vamos referir-nos às propriedades de estilo desta funcionalidade como estados, e usaremos a funcionalidade para fazer um estadista.

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

## <a name="create-a-feature-stateset"></a>Criar um estadoet de recurso

1. Na aplicação Do Carteiro, selecione **New**. Na janela **Criar Novo,** selecione **Request**. Insira um **nome De pedido** e selecione uma coleção. Clique em **Guardar**

2. Faça um pedido **de CORREIO** à [API Create Stateset](/rest/api/maps/featurestate/createstatesetpreview). Utilize o `datasetId` conjunto de dados que contém o estado que pretende modificar. O pedido deve parecer-se com o seguinte URL:

    ```http
    https://atlas.microsoft.com/featureState/stateset?api-version=1.0&datasetId={datasetId}&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

3. Nos **Cabeçalhos** do pedido **DOM,** definido `Content-Type` para `application/json` . No **Corpo**, forneça os estilos de json crus abaixo para refletir alterações nos `occupied` estados e `temperature` *estados.* Quando terminar, clique em **Enviar.**

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

4. Copie o `statesetId` do corpo de resposta.

5. Crie um pedido **DEM** para atualizar o estado: Passe o statesetId e funcionalidade `ID` com a sua chave de subscrição Azure Maps. O pedido deve parecer-se com o seguinte URL:

    ```http
    https://atlas.microsoft.com/featureState/state?api-version=1.0&statesetID={statesetId}&featureID={featureId}&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

6. Nos **Cabeçalhos** do pedido **DOM,** definido `Content-Type` para `application/json` . No **CORPO** do PEDIDO **POST,** copie e cole o JSON na amostra abaixo.

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
    > A atualização só será guardada se o carimbo do tempo registado for após o carimbo de tempo do pedido anterior. Podemos passar qualquer nome chave que já configuramos durante a criação.

7. Após uma atualização bem sucedida, receberá um `200 OK` código de estado HTTP. Se tiver  [um estilo dinâmico implementado](indoor-map-dynamic-styling.md) para um mapa interior, a atualização será exibida no seu mapa renderizado no carimbo de hora especificado.

A [API de Estados-Recursos](/rest/api/maps/featurestate/getstatespreview) permite-lhe recuperar o estado de uma funcionalidade utilizando a sua `ID` funcionalidade. Também pode eliminar o estado e os seus recursos utilizando a [API de eliminação do Estado de funcionalidade.](/rest/api/maps/featurestate/deletestatesetpreview)

Para saber mais sobre os diferentes serviços do Azure Maps Creator (Preview) discutidos neste artigo ver, [Creator Indoor Maps](creator-indoor-maps.md).

## <a name="clean-up-resources"></a>Limpar os recursos

Não há recursos que exijam limpeza.

## <a name="next-steps"></a>Passos seguintes

Para aprender a usar o módulo de mapas interiores, veja

> [!div class="nextstepaction"]
> [Utilize o módulo Mapas Interiores](how-to-use-indoor-module.md)