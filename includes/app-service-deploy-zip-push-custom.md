---
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 11/03/2016
ms.author: cephalin
ms.openlocfilehash: 0019e50615f3e66778709ad8cb28f92967c66e2e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96018452"
---
## <a name="deployment-customization"></a>Personalização de implementação

O processo de implementação pressupõe que o ficheiro .zip que empurra contém uma aplicação pronta a executar. Por padrão, não são executadas personalizações. Para ativar os mesmos processos de construção que obtém com integração contínua, adicione o seguinte às definições da sua aplicação:

`SCM_DO_BUILD_DURING_DEPLOYMENT=true`

Quando utiliza .zip colocação de impulsos, esta definição é **falsa** por defeito. O padrão é **verdadeiro** para implementações de integração contínua. Quando definido como **verdadeiro,** as suas definições relacionadas com a implementação são utilizadas durante a implementação. Pode configurar estas definições como definições de aplicações ou num ficheiro de configuração de .implementação que esteja localizado na raiz do seu ficheiro .zip. Para obter mais informações, consulte [as definições relacionadas com o Repositório e](https://github.com/projectkudu/kudu/wiki/Configurable-settings#repository-and-deployment-related-settings) a implementação na referência de implantação.