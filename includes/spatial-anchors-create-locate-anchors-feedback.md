---
ms.openlocfilehash: 7b9954930c668e338cc289e948a5591c09ec2654
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2020
ms.locfileid: "76694551"
---
## <a name="provide-feedback-to-the-user"></a>Fornecer feedback ao utilizador

Pode escrever código para lidar com o evento atualizado da sessão. Este evento dispara sempre que a sessão melhora a sua compreensão do que o rodeia. Ao fazê-lo, permite-lhe:

- Utilize a classe `UserFeedback` para fornecer feedback ao utilizador à medida que o dispositivo se move e a sessão atualiza a sua compreensão ambiental. Para tal,
- Determinar em que ponto há dados espaciais rastreados suficientes para criar âncoras espaciais. Determina-se isto com `ReadyForCreateProgress` ou `RecommendedForCreateProgress`. Uma vez `ReadyForCreateProgress` acima de 1, temos dados suficientes para salvar uma âncora espacial em nuvem, embora recomendemos que espere até `RecommendedForCreateProgress` está acima de 1 para fazê-lo.
