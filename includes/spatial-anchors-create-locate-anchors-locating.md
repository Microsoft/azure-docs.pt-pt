---
ms.openlocfilehash: ac9fe1d367dba0ebdf4250b3213f191ced758dd3
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2020
ms.locfileid: "76694417"
---
## <a name="locate-a-cloud-spatial-anchor"></a>Localize uma âncora espacial em nuvem

Ser capaz de localizar uma âncora espacial em nuvem previamente guardada é uma das principais razões para usar âncoras espaciais Azure. Há várias maneiras diferentes de localizar uma âncora espacial em nuvem. Podes usar uma estratégia num observador de cada vez.
- Localize as âncoras por identificador.
- Localizar âncoras ligadas a uma âncora previamente localizada. Pode aprender sobre relações de âncora [aqui.](/azure/spatial-anchors/concepts/anchor-relationships-way-finding.md)
- Localizar a âncora utilizando [a relocalização grosseira](/azure/spatial-anchors/concepts/coarse-reloc.md).

Se estiver a localizar âncoras espaciais em nuvem por identificador, irá querer armazenar o identificador de âncora espacial em nuvem no serviço back-end da sua aplicação e torná-lo acessível a todos os dispositivos que possam autenticar adequadamente. Para um exemplo disso, consulte [Tutorial: Partilhe âncoras espaciais através](/azure/spatial-anchors/tutorials/tutorial-share-anchors-across-devices/)de dispositivos .

Instantifique um objeto `AnchorLocateCriteria`, detete os identificadores que procura e invoque o método `CreateWatcher` na sessão, fornecendo o seu `AnchorLocateCriteria`.