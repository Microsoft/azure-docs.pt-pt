---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 02/21/2019
ms.openlocfilehash: e80feac7dbf16652cc2e2a6176ed8b2c8c48e35b
ms.sourcegitcommit: e88188bc015525d5bead239ed562067d3fae9822
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/24/2019
ms.locfileid: "56753754"
---
### <a name="running-multiple-containers-on-the-same-host"></a>A execução de vários contentores no mesmo anfitrião

Se pretende executar vários contentores com portas expostas, certifique-se executar cada contentor com uma porta diferente. Por exemplo, execute o primeiro contentor na porta 5000 e o segundo contentor na porta 5001.

Substitua a `<container-registry>` e `<container-name>` com os valores dos contentores que utilizar. Estes não têm de ser o mesmo contentor. Pode ter o contentor de rostos e o contentor de LUIS em execução no anfitrião em conjunto, ou pode ter vários contentores de rostos em execução. 

Execute o primeiro contentor na porta 5000. 

```bash 
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
<container-registry>/microsoft/<container-name> \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY}
```

Execute o contentor de segundo na porta 5001.


```bash 
docker run --rm -it -p 5001:5000 --memory 4g --cpus 1 \
<container-registry>/microsoft/<container-name> \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY}
```

Cada contentor subseqüente deve ser uma porta diferente. 
