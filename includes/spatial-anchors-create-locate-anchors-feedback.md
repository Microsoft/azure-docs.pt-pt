---
ms.openlocfilehash: 7b9954930c668e338cc289e948a5591c09ec2654
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/25/2020
ms.locfileid: "76694551"
---
## <a name="provide-feedback-to-the-user"></a>Fornecer feedback ao utilizador

Pode escrever código para lidar com o evento atualizado da sessão. Este evento dispara sempre que a sessão melhora a sua compreensão do que o rodeia. Ao fazê-lo, permite-lhe:

- Utilize a `UserFeedback` classe para fornecer feedback ao utilizador à medida que o dispositivo se move e a sessão atualiza a sua compreensão ambiental. Para tal,
- Determina em que ponto há dados espaciais suficientes para criar âncoras espaciais. Determina-se isto com um `ReadyForCreateProgress` ou `RecommendedForCreateProgress` outro. Uma vez `ReadyForCreateProgress` acima de 1, temos dados suficientes para salvar uma âncora espacial em nuvem, embora recomendemos que espere até `RecommendedForCreateProgress` que seja acima de 1 para fazê-lo.
