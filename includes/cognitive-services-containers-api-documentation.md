---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 03/25/2019
ms.openlocfilehash: f4925401235aedb341a7e29ca36b079126647f7b
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66124358"
---
## <a name="validate-that-a-container-is-running"></a>Validar que um contentor está em execução 

Existem várias formas para validar que o contentor está em execução. 

|Pedir|Objetivo|
|--|--|
|`http://localhost:5000/`|O contêiner fornece uma página inicial.|
|`http://localhost:5000/status`|Pedido com GET, para validar que o contentor está em execução sem causar uma consulta de ponto final. Este pedido pode ser utilizado para Kubernetes [sondas de liveness e preparação](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/).|
|`http://localhost:5000/swagger`|O contentor fornece um conjunto completo de documentação para os pontos finais e um `Try it now` funcionalidade. Com esta funcionalidade, pode introduzir as definições num formulário HTML baseada na web e fazer a consulta sem ter de escrever qualquer código. Depois da consulta devolve, um comando CURL de exemplo é fornecido demonstrar que os cabeçalhos HTTP e corpo Formatar que seja necessário. |

![Home page do contentor](./media/cognitive-services-containers-api-documentation/container-webpage.png)
