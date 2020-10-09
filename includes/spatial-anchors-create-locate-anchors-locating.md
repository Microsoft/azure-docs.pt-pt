---
ms.openlocfilehash: 4df49b4e68769a907423c4edef9f8820df4d20b7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "83006482"
---
## <a name="locate-a-cloud-spatial-anchor"></a>Localize uma âncora espacial em nuvem

Ser capaz de localizar uma âncora espacial de nuvem previamente salva é uma das principais razões para a utilização de Âncoras Espaciais Azure. Há várias maneiras diferentes de localizar uma âncora espacial em nuvem. Podes usar uma estratégia num observador de cada vez.
- Localize as âncoras por identificador.
- Localizar âncoras ligadas a uma âncora previamente localizada. Pode aprender sobre relações de âncora [aqui.](/azure/spatial-anchors/concepts/anchor-relationships-way-finding/)
- Localizar âncora [utilizando a relocalização](/azure/spatial-anchors/concepts/coarse-reloc/)grossa .

> [!NOTE]
> Cada vez que localizar uma âncora, as Âncoras Espaciais Azure tentarão utilizar os dados ambientais recolhidos para aumentar as informações visuais na âncora. Se tiver dificuldade em localizar uma âncora, pode ser útil criar uma âncora e, em seguida, localizá-la várias vezes de diferentes ângulos e condições de iluminação.

Se estiver a localizar âncoras espaciais em nuvem por identificador, irá querer armazenar o identificador de âncora espacial em nuvem no serviço back-end da sua aplicação e torná-lo acessível a todos os dispositivos que possam autenticar adequadamente o mesmo. Para um exemplo disso, consulte [Tutorial: Partilhar Âncoras Espaciais através de dispositivos](/azure/spatial-anchors/tutorials/tutorial-share-anchors-across-devices/).

Instantaneamente um `AnchorLocateCriteria` objeto, desemote os identificadores que procura, e invoque o `CreateWatcher` método na sessão fornecendo o seu `AnchorLocateCriteria` .