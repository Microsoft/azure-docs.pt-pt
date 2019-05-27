---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 05/07/2019
ms.openlocfilehash: 327654ca06a3997855d904414fa4258491ee6c88
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66124347"
---
### <a name="run-multiple-containers-on-the-same-host"></a>Executar vários contentores no mesmo anfitrião

Se pretende executar vários contentores com portas expostas, certifique-se executar cada contentor com uma porta diferente de expostos. Por exemplo, execute o primeiro contentor na porta 5000 e o segundo contentor na porta 5001.

Pode ter este contentor e um contentor de serviços cognitivos do Azure diferente em execução no anfitrião em conjunto. Também pode ter vários contentores do mesmo contentor de serviços cognitivos em execução.
