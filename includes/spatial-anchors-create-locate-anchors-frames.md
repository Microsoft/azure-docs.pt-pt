---
ms.openlocfilehash: 10eb16dc1757255c77ed8967dc132f42660e070f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60232347"
---
## <a name="provide-frames-to-the-session"></a>Fornecer quadros para a sessão

A sessão de âncora geográficos funciona, mapeando o espaço à volta do utilizador. Isso ajuda a determinar onde estão localizadas as âncoras. Plataformas móveis (iOS e Android) requerem uma chamada nativa para o feed para obter quadros da biblioteca de AR de sua plataforma da câmara. Por outro lado, HoloLens constantemente está a analisar o ambiente, portanto, não há nenhuma necessidade de uma chamada específica, como com o Mobile.