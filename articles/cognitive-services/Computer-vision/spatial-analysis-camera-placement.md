---
title: Colocação da câmara de análise espacial
titleSuffix: Azure Cognitive Services
description: Saiba como configurar uma câmara para uso com Análise Espacial
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 01/12/2021
ms.author: aahi
ms.openlocfilehash: 849afe54125588cb7664ee221e4f229e83be8f96
ms.sourcegitcommit: 0aec60c088f1dcb0f89eaad5faf5f2c815e53bf8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/14/2021
ms.locfileid: "98185483"
---
# <a name="camera-placement-guide"></a>Guia de colocação de câmera

Este artigo fornece recomendações de colocação de câmaras para análise espacial (visualização pública). Inclui orientações gerais, bem como recomendações específicas para a altura, ângulo e distância entre câmara e ponto focal para todas as operações incluídas. 

> [!NOTE]
> Este guia foi concebido para a câmara eixo M3045-V. Esta câmara utilizará a resolução 1920x1080, campo de visão horizontal de 106 graus, campo de vista vertical de 59 graus e um comprimento focal fixo de 2,8 mm. Os princípios abaixo aplicar-se-ão a todas as câmaras, mas orientações específicas em torno da altura da câmara e da distância entre câmara e ponto focal terão de ser ajustadas para utilização com outras câmaras. 

## <a name="general-guidelines"></a>Orientações gerais

Considere as seguintes orientações gerais ao posicionar as câmaras para análise espacial:

* **Altura de iluminação.** Coloque as câmaras por baixo dos dispositivos de iluminação para que os aparelhos não bloqueiem as câmaras.
* **As obstruções.** Para evitar obstrução das vistas da câmara, tome nota de obstruções tais como postes, sinalização, prateleiras, paredes e câmaras LP existentes.
* **Iluminação ambiental.** A iluminação exterior afeta a qualidade da imagem da câmara. Para evitar condições de retroiluminação severas, evite direcionar as câmaras para janelas exteriores e portas de vidro.
* **Regras e regulamentos de privacidade locais.** Os regulamentos locais podem restringir o que as câmaras podem capturar. Certifique-se de que compreende as regras e regulamentos locais antes de colocar as câmaras.
* **Estrutura de construção.** AVAC, aspersores e fios existentes podem limitar a montagem dura das câmaras.
* **Gestão de cabos.** Certifique-se de que pode encaminhar um cabo ethernet de locais de montagem de câmaras planeadas para o interruptor Power Over Internet (PoE).

## <a name="height-focal-point-distance-and-angle"></a>Altura, distância focal e ângulo

Tem de considerar três coisas ao decidir como instalar uma câmara para análise espacial:
- Altura da câmera
- Distância entre câmara e ponto focal
- O ângulo da câmara em relação ao plano do chão

Também é importante saber o sentido que a maioria das pessoas anda (direção de caminhada) em relação ao campo de visão da câmara, se possível. Esta direção é importante para o desempenho do sistema.

![Imagem de uma pessoa andando em uma direção](./media/spatial-analysis/person-walking-direction.png)

A ilustração que se segue mostra a vista de elevação para a direção de caminhada da pessoa.

![Elevação e vista de plano](./media/spatial-analysis/person-walking-direction-diagram.png)

## <a name="camera-height"></a>Altura da câmera

Geralmente, as câmaras devem ser montadas a 12-14 pés do chão. Para a deteção da máscara facial, recomendamos que as câmaras sejam montadas a 8-12 pés do chão. Ao planear a montagem da câmara neste intervalo, considere obstruções (por exemplo: prateleiras, luzes penduradas, sinalização de suspensão e visores) que possam afetar a vista da câmara e, em seguida, ajustar a altura conforme necessário.

## <a name="camera-to-focal-point-distance"></a>Distância entre câmara e ponto focal

_A distância entre a câmara e_ o ponto focal é a distância linear do ponto focal (ou centro da imagem da câmara) para a câmara medida no chão.

![Distância entre câmara e ponto focal](./media/spatial-analysis/camera-focal-point.png)

Esta distância é medida no plano do chão.

![Como a distância entre câmara e focal é medida a partir do chão](./media/spatial-analysis/camera-focal-point-floor-plane.png)

De cima, parece que é assim:

![Como a distância entre câmara e focal é medida a partir de cima](./media/spatial-analysis/camera-focal-point-above.png)

Utilize a tabela abaixo para determinar a distância da câmara do ponto focal com base em alturas de montagem específicas. Estas distâncias são para uma colocação ideal. Note que a tabela fornece orientações abaixo da recomendação de 12'-14', uma vez que alguns limites máximos podem limitar a altura. Para a deteção da máscara facial, a distância recomendada entre a câmara e o ponto focal (min/máx) é de 4'-10' para a altura da câmara entre 8' e 12'.

| Altura da câmera | Distância entre câmara e ponto focal (min/máx) |  
| ------------- | ---------------------------------------- |  
| 8'            | 10'-13'                                  |  
| 10'           | 7'-13'                                   |  
| 12'           | 10'-17'                                  |  
| 14'           | 11'-18'                                  |  
| 16'           | 12'-22'                                  |  
| 20'           | 15'-30'                                  |  

A ilustração que se segue simula as vistas da câmara das distâncias mais próximas e mais distantes da câmara para o ponto focal.

| Mais próximo                                      | Mais longe                                      |  
| -------------------------------------------- | --------------------------------------------- |  
| ![Distância mais próxima da câmara ao ponto focal](./media/spatial-analysis/focal-point-closest.png) | ![Distância mais distante da câmara para o ponto focal](./media/spatial-analysis/focal-point-farthest.png) |  

## <a name="camera-angle-mounting-ranges"></a>Gamas de montagem de ângulo de câmara

Esta secção descreve gamas aceitáveis de montagem de ângulos de câmara. Estas gamas de montagem mostram o alcance aceitável para uma colocação ótima.

### <a name="line-configuration"></a>Configuração de linha

A tabela a seguir mostra recomendações para câmaras configuradas para a operação **cognitiveservices.vision.spatialanalysis-personcrossline.** Para a deteção da máscara facial, +/-30 graus é o ângulo ideal de montagem da câmara para a altura da câmara entre 8' e 12'.

| Altura da câmera | Distância entre câmara e ponto focal | Ângulo ideal de montagem da câmara (min/máx) |  
| ------------- | ------------------------------ | ------------------------------------------ |  
| 8'            | 9'                             | +/-40°                                     |  
| 10'           | 10'                            | +/-30°                                     |  
| 12'           | 13'                            | +/-20°                                     |  
| 16'           | 18'                            | +/-10°                                     |  
| 20'           | 22'                            | +/-10°                                     |  

A ilustração que se segue simula as vistas da câmara utilizando as recomendações de ângulo de montagem mais à esquerda (+) e mais à direita para a utilização de **cognitiveservices.vision.espacialanalysis-personcrossingline** para fazer a contagem de entradas de uma forma de porta.

| Vista mais à esquerda                | Vista mais à direita                |  
| ---------------------------- | ----------------------------- |  
| ![Ângulo da câmera mais à esquerda](./media/spatial-analysis/camera-angle-left.png) | ![Ângulo de câmera mais à direita](./media/spatial-analysis/camera-angle-right.png) |  

A ilustração seguinte mostra a colocação da câmara e os ângulos de montagem de uma vista de olhos de pássaros.

![Vista superior](./media/spatial-analysis/camera-angle-top.png)

### <a name="zone-configuration"></a>Configuração de zona

Recomendamos que coloque câmaras a 3 metros ou mais acima do solo para garantir que a área coberta é suficientemente grande. 

Quando a zona estiver ao lado de um obstáculo como uma parede ou prateleira, monte as câmaras na distância especificada do alvo dentro da gama de ângulo aceitável de 120 graus, como mostra a seguinte ilustração.

![Ângulo de câmera aceitável](./media/spatial-analysis/camera-angle-acceptable.png)

A seguinte ilustração fornece simulações para as vistas da câmara esquerda e direita de uma área ao lado de uma prateleira.

| Vista esquerda        | Vista direita        |  
| ---------------- | ----------------- |  
| ![Vista esquerda](./media/spatial-analysis/end-cap-left.png) | ![Vista direita](./media/spatial-analysis/end-cap-right.png) |  

#### <a name="queues"></a>Filas

Os **serviços cognitivos.vision.espacialanalysis-personcount**, **cognitiveservices.vision.espacialanalysis-persondistance**, e **cognitiveservices.vision.spatialanalysis-personcrossingpolygon** podem ser usados para monitorizar as filas. Para uma ótima qualidade de dados de fila, as barreiras retrácteis do cinto são preferíveis para minimizar a oclusão das pessoas na fila e garantir que a localização das filas é consistente com o tempo.

![Fila de cinto retráctil retráctil](./media/spatial-analysis/retractable-belt-queue.png)

Este tipo de barreira é preferido sobre barreiras opacas para a formação de filas para maximizar a precisão dos insights do sistema.

Existem dois tipos de filas: linear e zig-zag.

A seguinte ilustração mostra recomendações para filas lineares:

![Recomendação linear de fila](./media/spatial-analysis/camera-angle-linear-queue.png)

A seguinte ilustração fornece simulações para as vistas da câmara esquerda e direita das filas lineares. Note que pode montar a câmara no lado oposto da fila.

| Vista esquerda                          | Vista direita                          |  
| ---------------------------------- | ----------------------------------- |  
| ![Ângulo esquerdo para a fila linear](./media/spatial-analysis/camera-angle-linear-left.png) | ![Ângulo reto para a fila linear](./media/spatial-analysis/camera-angle-linear-right.png) |  

Para as filas zig-zag, é melhor evitar colocar a câmara diretamente virada para a direção da linha da fila, como mostra a seguinte ilustração. Note que cada uma das posições da câmara de quatro exemplos na ilustração proporciona a vista ideal com um desvio aceitável de +/- 15 graus em cada direção.

As seguintes ilustrações simulam a vista de uma câmara colocada nos locais ideais para uma fila zig-zag.

| Ver 1        | Vista 2        |  
| ------------- | ------------- |  
| ![Ver 1](./media/spatial-analysis/camera-angle-ideal-location-right.png) | ![Vista 2](./media/spatial-analysis/camera-angle-ideal-location-left.png) |  

| Ver 3 |  Ver 4 |  
| ---- | ----  |
| ![Ver 3](./media/spatial-analysis/camera-angle-ideal-location-back.png) |  ![Ver 4](./media/spatial-analysis/camera-angle-ideal-location-back-left.png) | 

##### <a name="organic-queues"></a>Filas orgânicas

As filas orgânicas formam-se organicamente. Este estilo de fila é aceitável se as filas não se formam além de 2-3 pessoas e a linha se forma dentro da definição de zona. Se o comprimento da fila for normalmente superior a 2-3 pessoas, recomendamos a utilização de uma barreira de cinto retrátil para ajudar a orientar a direção da fila e garantir que a linha se forma dentro da definição de zona.

## <a name="next-steps"></a>Passos seguintes

* [Implementar uma aplicação web People Counting](spatial-analysis-web-app.md)
* [Configure operações de análise espacial](./spatial-analysis-operations.md)
* [Registo e resolução de problemas](spatial-analysis-logging.md)
* [Guia de colocação de zona e linha](spatial-analysis-zone-line-placement.md)
