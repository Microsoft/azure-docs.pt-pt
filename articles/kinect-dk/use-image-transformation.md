---
title: Use transformações de imagem SDK sensor Azure Kinect
description: Aprenda a utilizar as funções de transformação de imagem SDK do sensor Azure Kinect.
author: cdedmonds
ms.author: cedmonds
ms.prod: kinect-dk
ms.topic: conceptual
ms.date: 06/26/2019
keywords: kinect, azul, sensor, sdk, sistema de coordenadas, calibração, projeto, nãoprojecto, transformação, rgb-d, nuvem de ponto
ms.openlocfilehash: df7f2aa13c0e9c0241494e96e720b30f3ff1d8a5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85277459"
---
# <a name="use-azure-kinect-sensor-sdk-image-transformations"></a>Use transformações de imagem SDK sensor Azure Kinect

Siga as funções específicas para utilizar e transformar imagens entre sistemas de câmara coordenados no seu Azure Kinect DK.

## <a name="k4a_transformation-functions"></a>k4a_transformation funções

 Todas as funções pré-fixas com *k4a_transformation* operam em imagens inteiras. Exigem que o cabo de transformação [k4a_transformation_t](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__transformation__t.html) obtido através [de k4a_transformation_create()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga853a1a5b6d521bbdf523a69e890c4f10.html#ga853a1a5b6d521bbdf523a69e890c4f10) e não sejam atribuídos através [de k4a_transformation_destroy()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga7d3ecaae66f26c1a89da9042b1bc6d44.html#ga7d3ecaae66f26c1a89da9042b1bc6d44). Também pode consultar o Exemplo de [Transformação](https://github.com/Microsoft/Azure-Kinect-Sensor-SDK/tree/develop/examples/transformation) SDK que demonstra como usar as três funções neste tópico.

As seguintes funções são abrangidas:

* [`k4a_transformation_depth_image_to_color_camera()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gafacffb5f781a9c2df30d4a16241cd514.html#gafacffb5f781a9c2df30d4a16241cd514)
* [`k4a_transformation_depth_image_to_color_camera_custom()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gac00dd00e7612a86382e3d0a130f276bb.html#gac00dd00e7612a86382e3d0a130f276bb)
* [`k4a_transformation_color_image_to_depth_camera()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gaf3a941f07bb0185cd7a72699a648fc29.html#gaf3a941f07bb0185cd7a72699a648fc29)
* [`k4a_transformation_depth_image_to_point_cloud()`](
https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga7385eb4beb9d8892e8a88cf4feb3be70.html#ga7385eb4beb9d8892e8a88cf4feb3be70)

### <a name="k4a_transformation_depth_image_to_color_camera"></a>k4a_transformation_depth_image_to_color_camera

#### <a name="overview"></a>Descrição geral

 A função [k4a_transformation_depth_image_to_color_camera()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gafacffb5f781a9c2df30d4a16241cd514.html#gafacffb5f781a9c2df30d4a16241cd514) transforma o mapa de profundidade do ponto de vista da câmara de profundidade no ponto de vista da câmara de cores. Esta função foi concebida para produzir as chamadas imagens RGB-D, onde D representa um canal de imagem adicional que regista o valor de profundidade. Como se pode ver na figura abaixo, a imagem a cores e a saída de [k4a_transformation_depth_image_to_color_camera()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gafacffb5f781a9c2df30d4a16241cd514.html#gafacffb5f781a9c2df30d4a16241cd514) parecem ter sido tiradas do mesmo ponto de vista, ou seja, o ponto de vista da câmara a cores.

   ![Transformação de imagem](./media/how-to-guides/image-transformation.png)

#### <a name="implementation"></a>Implementação

 Esta função de transformação é mais complexa do que simplesmente chamar [k4a_calibration_2d_to_2d para](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga3b6bf6dedbfe67468e2f895dcce68ed4.html#ga3b6bf6dedbfe67468e2f895dcce68ed4) cada pixel. Deforma uma malha triangular da geometria da câmara de profundidade para a geometria da câmara de cores. A malha triangular é usada para evitar a criação de furos na imagem de profundidade transformada. Um tampão Z garante que as oclusões são manuseadas corretamente. A aceleração da GPU está ativada para esta função por defeito.

#### <a name="parameters"></a>Parâmetros

 Os parâmetros de entrada são o cabo de transformação e uma imagem de profundidade. A resolução de imagem de profundidade deve corresponder à ```depth_mode``` especificada na criação do cabo de transformação. Por exemplo, se o cabo de transformação foi criado usando o modo 1024x1024, ```K4A_DEPTH_MODE_WFOV_UNBINNED``` a resolução da imagem de profundidade deve ser de 1024x1024 pixels. A saída é uma imagem de profundidade transformada que precisa de ser atribuída pelo utilizador através da chamada [k4a_image_create()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga859554581bb97a620ff8e92a893e71ef.html#ga859554581bb97a620ff8e92a893e71ef). A resolução da imagem de profundidade transformada deve corresponder à ```color_resolution``` especificada na criação do cabo de transformação. Por exemplo, se a resolução de cores foi definida para `K4A_COLOR_RESOLUTION_1080P` , a resolução de imagem de saída deve ser de 1920x1080 pixels. O passo da imagem de saída está definido para , à `width * sizeof(uint16_t)` medida que a imagem armazena valores de profundidade de 16 bits.

### <a name="k4a_transformation_depth_image_to_color_camera_custom"></a>k4a_transformation_depth_image_to_color_camera_custom

#### <a name="overview"></a>Descrição geral

 A função [k4a_transformation_depth_image_to_color_camera_custom transforma](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gac00dd00e7612a86382e3d0a130f276bb.html#gac00dd00e7612a86382e3d0a130f276bb) o mapa de profundidade e uma imagem personalizada do ponto de vista da câmara de profundidade no ponto de vista da câmara de cores. Como extensão de [k4a_transformation_depth_image_to_color_camera()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gafacffb5f781a9c2df30d4a16241cd514.html#gafacffb5f781a9c2df30d4a16241cd514), esta função é projetada para produzir uma imagem personalizada correspondente para a qual cada pixel corresponde às coordenadas de pixel correspondentes da câmara de cores adicional à imagem de profundidade transformada.

#### <a name="implementation"></a>Implementação

 Esta função de transformação produz a imagem de profundidade transformada da mesma forma [que k4a_transformation_depth_image_to_color_camera()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gafacffb5f781a9c2df30d4a16241cd514.html#gafacffb5f781a9c2df30d4a16241cd514). Para transformar a imagem personalizada, esta função fornece opções de utilização da interpolação linear ou da interpolação do vizinho mais próximo. A utilização da interpolação linear pode criar novos valores na imagem personalizada transformada. A utilização da interpolação do vizinho mais próximo impedirá que valores não presentes na imagem original apareçam na imagem de saída, mas resultará numa imagem menos suave. A imagem personalizada deve ser de um único canal de 8 bits ou de 16 bits. A aceleração da GPU está ativada para esta função por defeito.

#### <a name="parameters"></a>Parâmetros

 Os parâmetros de entrada são o cabo de transformação, uma imagem de profundidade, uma imagem personalizada e o tipo de interpolação. A imagem de profundidade e a resolução de imagem personalizada devem corresponder à `depth_mode` especificada na criação do cabo de transformação. Por exemplo, se o cabo de transformação foi criado usando o modo 1024x1024, `K4A_DEPTH_MODE_WFOV_UNBINNED` a resolução da imagem de profundidade e imagem personalizada deve ser de 1024x1024 pixels. O `interpolation_type` deve ser `K4A_TRANSFORMATION_INTERPOLATION_TYPE_LINEAR` `K4A_TRANSFORMATION_INTERPOLATION_TYPE_NEAREST` ou. A saída é uma imagem de profundidade transformada e uma imagem personalizada transformada que precisa de ser atribuída pelo utilizador através da chamada [k4a_image_create()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga859554581bb97a620ff8e92a893e71ef.html#ga859554581bb97a620ff8e92a893e71ef). A resolução da imagem de profundidade transformada e imagem personalizada transformada deve corresponder à `color_resolution` especificada na criação do cabo de transformação. Por exemplo, se a resolução de cores foi definida para `K4A_COLOR_RESOLUTION_1080P` , a resolução de imagem de saída deve ser de 1920x1080 pixels. O passo da imagem de profundidade de saída está definido para , à `width * sizeof(uint16_t)` medida que a imagem armazena valores de profundidade de 16 bits. A imagem personalizada de entrada e a imagem personalizada transformada devem ser de formato `K4A_IMAGE_FORMAT_CUSTOM8` `K4A_IMAGE_FORMAT_CUSTOM16` ou, o passo de imagem correspondente deve ser definido em conformidade. 

### <a name="k4a_transformation_color_image_to_depth_camera"></a>k4a_transformation_color_image_to_depth_camera

#### <a name="overview"></a>Descrição geral

 A função [k4a_transformation_color_image_to_depth_camera()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gaf3a941f07bb0185cd7a72699a648fc29.html#gaf3a941f07bb0185cd7a72699a648fc29) transforma a imagem de cor do ponto de vista da câmara de cor no ponto de vista da câmara de profundidade (ver figura acima). Pode ser usado para gerar imagens RGB-D.

#### <a name="implementation"></a>Implementação

 Para cada pixel do mapa de profundidade, a função utiliza o valor de profundidade do pixel para calcular a coordenada subpixel correspondente na imagem de cor. Em seguida, procuramos o valor da cor nesta coordenada na imagem de cor. A interpolação bilinear é realizada na imagem de cor para obter o valor de cor com precisão subpixel. Um pixel que não tenha uma leitura de profundidade associada é atribuído a um valor BGRA na imagem de `[0,0,0,0]` saída. A aceleração da GPU está ativada para esta função por defeito. Como este método produz buracos na imagem de cor transformada e não lida com oclusões, recomendamos a utilização da função [k4a_transformation_depth_image_to_color_camera()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gafacffb5f781a9c2df30d4a16241cd514.html#gafacffb5f781a9c2df30d4a16241cd514) em vez disso.

#### <a name="parameters"></a>Parâmetros

Os parâmetros de entrada são o cabo de transformação, uma imagem de profundidade e uma imagem de cor. As resoluções de profundidade e imagens de cor devem corresponder ao depth_mode e color_resolution especificados na criação do cabo de transformação. A saída é uma imagem de cor transformada que precisa de ser atribuída pelo utilizador através da chamada [k4a_image_create()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga859554581bb97a620ff8e92a893e71ef.html#ga859554581bb97a620ff8e92a893e71ef). A resolução da imagem de cor transformada deve corresponder ao depth_resolution especificado na criação do cabo de transformação. A imagem de saída armazena quatro valores de 8 bits representando o BGRA para cada pixel. Portanto, o passo da imagem é ```width * 4 * sizeof(uint8_t)``` . A ordem de dados é intercalada por pixels, ou seja, valor azul - pixel 0, valor verde - pixel 0, valor vermelho - pixel 0, valor alfa - pixel 0, valor azul - pixel 1 e assim por diante.

### <a name="k4a_transformation_depth_image_to_point_cloud"></a>k4a_transformation_depth_image_to_point_cloud

#### <a name="overview"></a>Descrição geral

A função [k4a_transformation_depth_image_to_point_cloud()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga7385eb4beb9d8892e8a88cf4feb3be70.html#ga7385eb4beb9d8892e8a88cf4feb3be70) converte um mapa de profundidade 2D tirado por uma câmara numa nuvem de ponto 3D no sistema de coordenadas da mesma câmara. A câmara pode, assim, ser a câmara de profundidade ou de cor.

#### <a name="implementation"></a>Implementação

 A função dá resultados equivalentes a executar [k4a_calibration_2d_to_2d](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga3b6bf6dedbfe67468e2f895dcce68ed4.html#ga3b6bf6dedbfe67468e2f895dcce68ed4) para cada pixel, mas é computacionalmente mais eficiente. Ao chamar [k4a_transformation_create()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga853a1a5b6d521bbdf523a69e890c4f10.html#ga853a1a5b6d521bbdf523a69e890c4f10), precomputamos uma chamada tabela de xy-lookup que armazena fatores de escala x e y para cada pixel de imagem. Ao chamar [k4a_transformation_depth_image_to_point_cloud,,,.](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga7385eb4beb9d8892e8a88cf4feb3be70.html#ga7385eb4beb9d8892e8a88cf4feb3be70) Análogamente, a coordenada 3D Y é calculada por multiplicação com o fator escala y. O exemplo de nuvem de [ponto rápido](https://github.com/Microsoft/Azure-Kinect-Sensor-SDK/tree/develop/examples/fastpointcloud) do SDK demonstra como a tabela xy é calculada. Os utilizadores podem seguir o código de exemplo para implementar a sua própria versão desta função, por exemplo, para acelerar o seu pipeline gpu.

#### <a name="parameters"></a>Parâmetros

 Os parâmetros de entrada são o cabo de transformação, um especificador de câmara e uma imagem de profundidade. Se o especificador da câmara estiver definido para profundidade, a resolução da imagem de profundidade deve corresponder ao depth_mode especificado na criação do cabo de transformação. Caso contrário, se o especificador estiver definido para a câmara de cores, a resolução deve corresponder à resolução do color_resolution escolhido. O parâmetro de saída é uma imagem XYZ que precisa de ser atribuída pelo utilizador através da chamada [k4a_image_create()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga859554581bb97a620ff8e92a893e71ef.html#ga859554581bb97a620ff8e92a893e71ef). A resolução de imagem XYZ deve corresponder à resolução do mapa de profundidade de entrada. Armazenamos três valores de coordenadas assinados de 16 bits em milímetro para cada pixel. O passo da imagem XYZ está, portanto, definido para `width * 3 * sizeof(int16_t)` . A ordem de dados é intercalada por pixels, ou seja, X-coordinate – pixel 0, coordenada Y – pixel 0, coordenada Z – pixel 0, X-coordenada – pixel 1 e assim por diante. Se um pixel não puder ser convertido em 3D, a função atribui os valores `[0,0,0]` ao pixel.

## <a name="samples"></a>Amostras

[Exemplo de transformação](https://github.com/Microsoft/Azure-Kinect-Sensor-SDK/tree/develop/examples/transformation)

## <a name="next-steps"></a>Passos seguintes

Agora você sabe como usar as funções de transformação de imagem do sensor Azure Kinect SDK, você também pode aprender sobre

>[!div class="nextstepaction"]
>[Funções de calibração SDK sensor Azure Kinect](use-calibration-functions.md)

Também pode rever

[Sistemas de coordenadas](coordinate-systems.md)
