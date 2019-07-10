---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: 00cc63f53388ab7bea05a0b55784247f63477684
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67704191"
---
## <a name="validate-that-a-container-is-running"></a>Validar que um contentor está em execução 

Existem várias formas para validar que o contentor está em execução. 

|Pedir|Objetivo|
|--|--|
|`http://localhost:5000/`|O contêiner fornece uma página inicial.|
|`http://localhost:5000/status`|Pedido com GET, para validar que o contentor está em execução sem causar uma consulta de ponto final. Este pedido pode ser utilizado para Kubernetes [sondas de liveness e preparação](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/).|
|`http://localhost:5000/swagger`|O contentor fornece um conjunto completo de documentação para os pontos finais e um `Try it now` funcionalidade. Com esta funcionalidade, pode introduzir as definições num formulário HTML baseada na web e fazer a consulta sem ter de escrever qualquer código. Depois da consulta devolve, um comando CURL de exemplo é fornecido demonstrar que os cabeçalhos HTTP e corpo Formatar que seja necessário. |

![Home page do contentor](./media/cognitive-services-containers-api-documentation/container-webpage.png)
