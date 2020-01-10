---
author: IEvangelist
ms.author: dapine
ms.date: 08/22/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: 081155c8984f39cc9cc8e905eb108c07dee98d2e
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/28/2019
ms.locfileid: "70034434"
---
## <a name="validate-that-a-container-is-running"></a>Validar que um contêiner está em execução 

Há várias maneiras de validar que o contêiner está em execução. Localize o endereço *IP externo* e a porta exposta do contêiner em questão e abra seu navegador da Web favorito. Use as várias URLs de solicitação abaixo para validar se o contêiner está em execução. As URLs de solicitação de exemplo listadas abaixo são `http://localhost:5000`, mas seu contêiner específico pode variar. Tenha em mente que você depende do endereço *IP externo* do seu contêiner e da porta exposta.

| URL do Pedido | Finalidade |
|--|--|
| `http://localhost:5000/` | O contêiner fornece um home page. |
| `http://localhost:5000/status` | Solicitado com um HTTP GET, para validar que o contêiner está em execução sem causar uma consulta de ponto de extremidade. Essa solicitação pode ser usada para [investigações de prontidão e vida](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/)kubernetes. |
| `http://localhost:5000/swagger` | O contêiner fornece um conjunto completo de documentação para os pontos de extremidade e um recurso **Experimente** . Com esse recurso, você pode inserir suas configurações em um formulário HTML baseado na Web e fazer a consulta sem precisar escrever nenhum código. Depois que a consulta retorna, um exemplo de comando de ONDULAção é fornecido para demonstrar os cabeçalhos HTTP e o formato de corpo necessários. |

![home page do contêiner](./media/cognitive-services-containers-api-documentation/container-webpage.png)
