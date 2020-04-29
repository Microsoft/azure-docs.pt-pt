---
ms.openlocfilehash: 505670e719e86086dbf7721b4298ec913220f928
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76694444"
---
## <a name="provide-frames-to-the-session"></a>Fornecer quadros para a sessão

A sessão de âncora espacial funciona mapeando o espaço em torno do utilizador. Fazê-lo ajuda a determinar onde estão as âncoras. As plataformas móveis (iOS & Android) exigem uma chamada nativa para o feed da câmara para obter quadros da biblioteca AR da sua plataforma. Em contraste, os HoloLens estão constantemente a digitalizar o ambiente, por isso não há necessidade de uma chamada específica como no Mobile.