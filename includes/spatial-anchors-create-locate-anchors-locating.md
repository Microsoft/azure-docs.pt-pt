---
ms.openlocfilehash: 4df49b4e68769a907423c4edef9f8820df4d20b7
ms.sourcegitcommit: ac4a365a6c6ffa6b6a5fbca1b8f17fde87b4c05e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/10/2020
ms.locfileid: "83006482"
---
## <a name="locate-a-cloud-spatial-anchor"></a>Localize uma âncora espacial em nuvem

Ser capaz de localizar uma âncora espacial em nuvem previamente guardada é uma das principais razões para usar âncoras espaciais Azure. Há várias maneiras diferentes de localizar uma âncora espacial em nuvem. Podes usar uma estratégia num observador de cada vez.
- Localize as âncoras por identificador.
- Localizar âncoras ligadas a uma âncora previamente localizada. Pode aprender sobre relações de âncora [aqui.](/azure/spatial-anchors/concepts/anchor-relationships-way-finding/)
- Localizar a âncora utilizando [a relocalização grosseira](/azure/spatial-anchors/concepts/coarse-reloc/).

> [!NOTE]
> Cada vez que localizar uma âncora, as Âncoras Espaciais Azure tentarão utilizar os dados ambientais recolhidos para aumentar a informação visual na âncora. Se estiver com dificuldades em localizar uma âncora, pode ser útil criar uma âncora, em seguida, localizá-la várias vezes de diferentes ângulos e condições de iluminação.

Se estiver a localizar âncoras espaciais em nuvem por identificador, irá querer armazenar o identificador de âncora espacial em nuvem no serviço back-end da sua aplicação e torná-lo acessível a todos os dispositivos que possam autenticar adequadamente. Para um exemplo disso, consulte [Tutorial: Partilhe âncoras espaciais através](/azure/spatial-anchors/tutorials/tutorial-share-anchors-across-devices/)de dispositivos .

Instantifique `AnchorLocateCriteria` um objeto, detete os identificadores `CreateWatcher` que procura e invoque o método da sessão fornecendo o seu `AnchorLocateCriteria`.