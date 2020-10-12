---
author: Juliako
ms.service: media-services
ms.topic: include
ms.date: 11/09/2018
ms.author: juliako
ms.openlocfilehash: 065cb4daa9501ee658d364dad43b9e03798e4083
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "67184673"
---
O trabalho produz um ficheiro de saída JSON que contém metadados sobre rostos detetados e rastreados. Os metadados incluem coordenadas que indicam a localização das faces, bem como um número de identificação facial que indica o rastreio desse indivíduo. Os números de identificação facial são propensos a ser reiniciados em circunstâncias em que a face frontal é perdida ou sobreposta no quadro, resultando em alguns indivíduos que recebem vários IDs atribuídos.

A saída JSON inclui os seguintes elementos:

### <a name="root-json-elements"></a>Elementos raiz JSON

| Elemento | Descrição |
| --- | --- |
| versão |Isto refere-se à versão da API de vídeo. |
| escala de tempo |"Carrapatos" por segundo do vídeo. |
| offset |Este é o tempo compensado para os tempos. Na versão 1.0 das APIs de vídeo, este será sempre 0. Em cenários futuros que apoiamos, este valor pode mudar. |
| largura, altura |A largura e o alto da moldura de vídeo de saída, em pixels.|
| framerate |Fotogramas por segundo do vídeo. |
| [fragmentos](#fragments-json-elements) |Os metadados são divididos em diferentes segmentos chamados fragmentos. Cada fragmento contém um início, duração, número de intervalos e evento(s). |

### <a name="fragments-json-elements"></a>Fragmentos elementos JSON

|Elemento|Descrição|
|---|---|
| iniciar |A hora de início do primeiro evento em "tiques". |
| duration |O comprimento do fragmento, em "carrapatos". |
| índice | (Aplica-se apenas ao Azure Media Redator) define o índice de quadro do evento atual. |
| interval |O intervalo de cada entrada de evento dentro do fragmento, em "tiques". |
| eventos |Cada evento contém os rostos detetados e rastreados dentro dessa duração. É uma variedade de eventos. A matriz externa representa um intervalo de tempo. A matriz interna é constituída por 0 ou mais eventos que ocorreram nesse ponto no tempo. Um suporte vazio [] significa que não foram detetados rostos. |
| ID |A identificação do rosto que está a ser seguido. Este número pode mudar inadvertidamente se um rosto não for detetado. Um dado indivíduo deve ter o mesmo ID ao longo do vídeo geral, mas isso não pode ser garantido devido a limitações no algoritmo de deteção (oclusão, etc.). |
| x, y |As coordenadas superiores esquerdas X e Y da caixa de delimitação facial numa escala normalizada de 0,0 a 1.0. <br/>As coordenadas X e Y são sempre relativas à paisagem, por isso, se tiver um vídeo de retrato (ou de cabeça para baixo, no caso do iOS), terá de transpor as coordenadas em conformidade. |
| largura, altura |A largura e a altura da caixa de delimitação facial numa escala normalizada de 0,0 a 1,0. |
| rostos Detetado |Isto é encontrado no final dos resultados do JSON e resume o número de rostos que o algoritmo detetou durante o vídeo. Como os IDs podem ser reiniciados inadvertidamente se um rosto não for detetado (por exemplo, a face sai do ecrã, olha para o lado), este número pode nem sempre ser igual ao número real de rostos no vídeo. |
