---
ms.openlocfilehash: 849777ac3ec11915c602861fea0c42a49fb08bcb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "83006480"
---
## <a name="provide-frames-to-the-session"></a>Fornecer quadros para a sessão

A sessão de âncora espacial funciona mapeando o espaço em torno do utilizador. Ao fazê-lo, ajuda a determinar onde estão as âncoras. As plataformas móveis (iOS & Android) requerem uma chamada nativa para o feed da câmara para obter quadros da biblioteca AR da sua plataforma. Em contraste, o HoloLens está constantemente a digitalizar o ambiente, por isso não há necessidade de uma chamada específica como nas plataformas móveis.