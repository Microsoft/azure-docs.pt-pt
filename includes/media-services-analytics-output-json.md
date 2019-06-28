---
author: Juliako
ms.service: media-services
ms.topic: include
ms.date: 11/09/2018
ms.author: juliako
ms.openlocfilehash: 065cb4daa9501ee658d364dad43b9e03798e4083
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67184673"
---
A tarefa produz um ficheiro de saída do JSON que contém metadados sobre rostos detetados e monitorizados. Os metadados incluem coordenadas que indica a localização de rostos, bem como um número de identificação de face indicando o rastreamento dessa pessoa. Números de ID do rosto são suscetíveis a repor em circunstâncias, o mostrador frontal caso de perda ou overlapped no frame, resultando em alguns indivíduos introdução atribuídos a vários IDs.

A saída JSON inclui os seguintes elementos:

### <a name="root-json-elements"></a>Elementos de JSON de raiz

| Elemento | Descrição |
| --- | --- |
| version |Refere-se para a versão da API de vídeo. |
| escala temporal |"Ticks" por segundo do vídeo. |
| offset |Este é o desvio de tempo para carimbos. Na versão 1.0 de APIs de vídeo, ela sempre será 0. No futuro cenários que suportamos, pode alterar este valor. |
| largura, elevado |A largura e o elevado número do quadro de vídeo de saída, em pixéis.|
| framerate |Fotogramas por segundo do vídeo. |
| [fragments](#fragments-json-elements) |Os metadados é segmentado cópias em diferentes segmentos chamados fragmentos. Cada fragmento contém um início, duração, número de intervalos e evento(s). |

### <a name="fragments-json-elements"></a>Elementos JSON de fragmentos

|Elemento|Descrição|
|---|---|
| start |A hora de início do primeiro evento na "ticks". |
| Duração |O comprimento do fragmento, em "ticks". |
| index | (Se aplica apenas para o Azure Media Redactor) define o índice do quadro do evento atual. |
| interval |O intervalo de cada entrada de evento dentro do fragmento, na "ticks". |
| eventos |Cada evento contém os rostos detetados e monitorizados dentro desse período de tempo. É uma matriz de eventos. A matriz externa representa um intervalo de tempo. A matriz interna é constituída por 0 ou mais eventos que ocorreram nesse ponto no tempo. Um [Reto de fecho vazio] significa que nenhum rostos foram detetados. |
| id |O ID do mostrador da que está a ser controlado. Este número pode alterar inadvertidamente se um rosto ficar detectado. Uma determinada pessoa deve ter o mesmo ID em todo o vídeo geral, mas isso não pode ser garantido devido a limitações no algoritmo de deteção (oclusão, etc.). |
| x, y |A parte superior esquerda X e Y coordenadas do mostrador da caixa num normalizado de dimensionamento de 0,0 a 1,0 delimitadora. <br/>-X e Y coordenadas são relativamente à paisagem sempre, portanto, se tiver um retrato vídeo (ou vantagem pendente, no caso do iOS), terá transpor as coordenadas em conformidade. |
| largura, altura |A largura e altura do mostrador da caixa num normalizado de dimensionamento de 0,0 a 1,0 delimitadora. |
| facesDetected |Isso é encontrado no final dos resultados JSON e resume o número de faces que o algoritmo detectado durante o vídeo. Uma vez que os IDs podem ser repostos inadvertidamente, se um rosto torna-se não detetado (por exemplo, o mostrador apague ecrã, se parece distância), este número não poderá igual sempre o número VERDADEIRO de rostos em vídeo. |
