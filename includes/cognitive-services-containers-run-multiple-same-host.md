---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: 09aa5affefc576606984ea7116b3d9bda4ba83d5
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "67704321"
---
### <a name="run-multiple-containers-on-the-same-host"></a>Executar vários recipientes no mesmo hospedeiro

Se pretender executar vários contentores com portas expostas, certifique-se de que executa cada recipiente com uma porta exposta diferente. Por exemplo, executar o primeiro recipiente no porto 5000 e o segundo contentor no porto 5001.

Pode ter este recipiente e um recipiente de Serviços Cognitivos Azure diferentes a funcionar em conjunto no HOST. Também pode ter vários recipientes do mesmo contentor dos Serviços Cognitivos em funcionamento.
