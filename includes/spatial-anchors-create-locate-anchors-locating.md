---
ms.openlocfilehash: c2b1e1d87600e83733eb8bdbf5bc6e2e63376cf4
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "95993098"
---
## <a name="locate-a-cloud-spatial-anchor"></a>Localize uma âncora espacial em nuvem

Ser capaz de localizar uma âncora espacial de nuvem previamente salva é uma das principais razões para a utilização de Âncoras Espaciais Azure. Há várias maneiras diferentes de localizar uma âncora espacial em nuvem. Podes usar uma estratégia num observador de cada vez.
- Localize as âncoras por identificador.
- Localizar âncoras ligadas a uma âncora previamente localizada. Pode aprender sobre relações de âncora [aqui.](../articles/spatial-anchors/concepts/anchor-relationships-way-finding.md)
- Localizar âncora [utilizando a relocalização](../articles/spatial-anchors/concepts/coarse-reloc.md)grossa .

> [!NOTE]
> Cada vez que localizar uma âncora, as Âncoras Espaciais Azure tentarão utilizar os dados ambientais recolhidos para aumentar as informações visuais na âncora. Se tiver dificuldade em localizar uma âncora, pode ser útil criar uma âncora e, em seguida, localizá-la várias vezes de diferentes ângulos e condições de iluminação.

Se estiver a localizar âncoras espaciais em nuvem por identificador, irá querer armazenar o identificador de âncora espacial em nuvem no serviço back-end da sua aplicação e torná-lo acessível a todos os dispositivos que possam autenticar adequadamente o mesmo. Para um exemplo disso, consulte [Tutorial: Partilhar Âncoras Espaciais através de dispositivos](../articles/spatial-anchors/tutorials/tutorial-share-anchors-across-devices.md).

Instantaneamente um `AnchorLocateCriteria` objeto, desemote os identificadores que procura, e invoque o `CreateWatcher` método na sessão fornecendo o seu `AnchorLocateCriteria` .