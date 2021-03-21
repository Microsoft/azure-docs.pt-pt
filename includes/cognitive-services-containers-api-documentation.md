---
author: IEvangelist
ms.author: dapine
ms.date: 08/22/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: 8b3d856d255968b4a6736db908ce3999cbd56193
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "95996467"
---
## <a name="validate-that-a-container-is-running"></a>Validar que um contentor está a funcionar 

Há várias formas de validar que o contentor está a funcionar. Localize o endereço *IP externo* e a porta exposta do recipiente em questão e abra o seu navegador web favorito. Utilize os urls de vários pedidos abaixo para validar o recipiente em funcionamento. O exemplo de pedido URLs listados abaixo são `http://localhost:5000` , mas o seu recipiente específico pode variar. Tenha em mente que deve confiar no endereço *IP externo* do seu contentor e na porta exposta.

| URL do Pedido | Objetivo |
|--|--|
| `http://localhost:5000/` | O contentor fornece uma home page. |
| `http://localhost:5000/ready` | Solicitado com GET, isto fornece uma verificação de que o recipiente está pronto para aceitar uma consulta contra o modelo.  Este pedido pode ser utilizado para sondas kubernetes [de prontidão e prontidão.](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) |
| `http://localhost:5000/status` | Também solicitado com GET, isto verifica se a chave api utilizada para iniciar o contentor é válida sem causar uma consulta de ponto final. Este pedido pode ser utilizado para sondas kubernetes [de prontidão e prontidão.](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) |
| `http://localhost:5000/swagger` | O contentor fornece um conjunto completo de documentação para os pontos finais e uma funcionalidade **Experimentar**. Com esta funcionalidade, pode introduzir as suas definições num formulário HTML baseado na Web e fazer a consulta sem ter de escrever qualquer código. Após o retorno da consulta, é fornecido um comando CURL de exemplo para demonstrar os cabeçalhos HTTP e o formato corporal que é necessário. |

![Página inicial do contentor](./media/cognitive-services-containers-api-documentation/container-webpage.png)
