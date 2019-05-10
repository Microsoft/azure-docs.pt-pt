---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 05/07/2019
ms.openlocfilehash: 19726330561abfad08aec3c4908c855616c6ee29
ms.sourcegitcommit: 17411cbf03c3fa3602e624e641099196769d718b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/10/2019
ms.locfileid: "65520900"
---
### <a name="running-multiple-containers-on-the-same-host"></a>A execução de vários contentores no mesmo anfitrião

Se pretende executar vários contentores com portas expostas, certifique-se executar cada contentor com uma porta diferente de expostos. Por exemplo, execute o primeiro contentor na porta 5000 e o segundo contentor na porta 5001.

Pode ter este contentor e um contentor de serviço cognitivo diferente em execução no anfitrião em conjunto, ou pode ter vários contentores do mesmo contentor de serviços cognitivos em execução.
