---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: 09aa5affefc576606984ea7116b3d9bda4ba83d5
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67704321"
---
### <a name="run-multiple-containers-on-the-same-host"></a>Executar vários contentores no mesmo anfitrião

Se pretende executar vários contentores com portas expostas, certifique-se executar cada contentor com uma porta diferente de expostos. Por exemplo, execute o primeiro contentor na porta 5000 e o segundo contentor na porta 5001.

Pode ter este contentor e um contentor de serviços cognitivos do Azure diferente em execução no anfitrião em conjunto. Também pode ter vários contentores do mesmo contentor de serviços cognitivos em execução.
