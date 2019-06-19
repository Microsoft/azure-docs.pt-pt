---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 05/07/2019
ms.openlocfilehash: 327654ca06a3997855d904414fa4258491ee6c88
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67184534"
---
### <a name="run-multiple-containers-on-the-same-host"></a>Executar vários contentores no mesmo anfitrião

Se pretende executar vários contentores com portas expostas, certifique-se executar cada contentor com uma porta diferente de expostos. Por exemplo, execute o primeiro contentor na porta 5000 e o segundo contentor na porta 5001.

Pode ter este contentor e um contentor de serviços cognitivos do Azure diferente em execução no anfitrião em conjunto. Também pode ter vários contentores do mesmo contentor de serviços cognitivos em execução.
