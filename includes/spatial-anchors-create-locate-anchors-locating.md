---
ms.openlocfilehash: 430302d8b1b2a01febbbe2f11057bb331ec80c28
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57907815"
---
## <a name="locate-a-cloud-spatial-anchor"></a>Localize uma âncora de geográficos de cloud

Para localizar as âncoras da cloud espaciais, terá de saber os seus identificadores. Os IDs de âncora podem ser armazenado no serviço de back-end da sua aplicação e acessível a todos os dispositivos que podem autenticar corretamente ao mesmo. Para obter um exemplo de uma veja [Tutorial: Partilhar as âncoras geográficos em todos os dispositivos](/azure/spatial-anchors/tutorials/tutorial-share-anchors-across-devices/).

Criar uma instância de um objeto de AnchorLocateCriteria, defina os identificadores que está à procura e invoca o método de CreateWatcher na sessão, fornecendo seu AnchorLocateCriteria.
