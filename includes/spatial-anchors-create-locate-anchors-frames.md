---
ms.openlocfilehash: 849777ac3ec11915c602861fea0c42a49fb08bcb
ms.sourcegitcommit: ac4a365a6c6ffa6b6a5fbca1b8f17fde87b4c05e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/10/2020
ms.locfileid: "83006480"
---
## <a name="provide-frames-to-the-session"></a>Fornecer quadros para a sessão

A sessão de âncora espacial funciona mapeando o espaço em torno do utilizador. Fazê-lo ajuda a determinar onde estão as âncoras. As plataformas móveis (iOS & Android) exigem uma chamada nativa para o feed da câmara para obter quadros da biblioteca AR da sua plataforma. Em contraste, os HoloLens estão constantemente a digitalizar o ambiente, por isso não há necessidade de uma chamada específica como nas plataformas móveis.