---
author: IEvangelist
ms.author: dapine
ms.date: 08/22/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: 081155c8984f39cc9cc8e905eb108c07dee98d2e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "70034434"
---
## <a name="validate-that-a-container-is-running"></a>Validar que um recipiente está funcionando 

Há várias formas de validar que o contentor está a funcionar. Localize o endereço *IP externo* e a porta exposta do contentor em questão e abra o seu navegador web favorito. Utilize os vários URLs de pedido abaixo para validar o funcionamento do recipiente. O pedido de exemplo URLs listados abaixo são, `http://localhost:5000`mas o seu recipiente específico pode variar. Tenha em mente que deve confiar no endereço *IP externo* do seu contentor e na porta exposta.

| URL do Pedido | Objetivo |
|--|--|
| `http://localhost:5000/` | O recipiente fornece uma página inicial. |
| `http://localhost:5000/status` | Solicitado com um HTTP GET, para validar que o recipiente está em funcionamento sem causar uma consulta de ponto final. Este pedido pode ser utilizado para sondas de [vivacidade e prontidão](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/)kubernetes. |
| `http://localhost:5000/swagger` | O recipiente fornece um conjunto completo de documentação para os pontos finais e uma funcionalidade **Experimente-a.** Com esta funcionalidade, pode introduzir as suas definições num formulário HTML baseado na Web e fazer a consulta sem ter de escrever qualquer código. Após a retorna da consulta, é fornecido um comando CURL de exemplo para demonstrar os cabeçalhos HTTP e o formato corporal que é necessário. |

![Página inicial do contentor](./media/cognitive-services-containers-api-documentation/container-webpage.png)
