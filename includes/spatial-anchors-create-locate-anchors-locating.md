---
ms.openlocfilehash: 52dfbfca5f79a7f92848ea39eddc00aa10f05ff1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60232363"
---
## <a name="locate-a-cloud-spatial-anchor"></a>Localize uma âncora de geográficos de cloud

A capacidade de localizar uma âncora de geográficos anteriormente carregada cloud é uma das razões principais para utilizar a biblioteca de âncoras espaciais do Azure. Para localizar as âncoras da cloud espaciais, terá de saber os seus identificadores. Os IDs de âncora podem ser armazenados no serviço de back-end da sua aplicação e acessível a todos os dispositivos que podem autenticar corretamente ao mesmo. Para obter um exemplo de uma veja [Tutorial: Partilhar as âncoras geográficos em todos os dispositivos](/azure/spatial-anchors/tutorials/tutorial-share-anchors-across-devices/).

Criar uma instância de um `AnchorLocateCriteria` de objeto, defina os identificadores que está à procura e invoca o `CreateWatcher` método na sessão, fornecendo seu `AnchorLocateCriteria`.
