---
ms.openlocfilehash: 7b9954930c668e338cc289e948a5591c09ec2654
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76694551"
---
## <a name="provide-feedback-to-the-user"></a>Fornecer feedback ao utilizador

Pode escrever código para lidar com o evento atualizado da sessão. Este evento dispara sempre que a sessão melhora a sua compreensão do que o rodeia. Ao fazê-lo, permite-lhe:

- Utilize `UserFeedback` a classe para fornecer feedback ao utilizador à medida que o dispositivo se move e a sessão atualiza a sua compreensão ambiental. Para tal,
- Determinar em que ponto há dados espaciais rastreados suficientes para criar âncoras espaciais. Determina isto `ReadyForCreateProgress` com `RecommendedForCreateProgress`qualquer um ou. Uma `ReadyForCreateProgress` vez acima de 1, temos dados suficientes para guardar `RecommendedForCreateProgress` uma âncora espacial em nuvem, embora recomendemos que espere até estar acima de 1 para fazê-lo.
