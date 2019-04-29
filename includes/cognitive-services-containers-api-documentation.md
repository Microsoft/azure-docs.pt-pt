---
author: diberry
ms.author: v-junlch
ms.service: cognitive-services
ms.topic: include
origin.date: 03/25/2019
ms.date: 04/23/2019
ms.openlocfilehash: 94e95864d8bac2d6dc0ff690a2a8f53bd2db5a40
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60598897"
---
## <a name="validate-container-is-running"></a>Validar o contentor está em execução 

Existem várias formas para validar o contentor está em execução: 

|Pedir|Objetivo|
|--|--|
|`http://localhost:5000/`|O contêiner fornece uma home page.|
|`http://localhost:5000/status`|Pedido com GET validar o contentor está em execução sem causar uma consulta de ponto final. Isso pode ser usado para Kubernetes [sondas de liveness e preparação](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/).|
|`http://localhost:5000/swagger`|O contentor fornece um conjunto completo de documentação para os pontos finais, bem como um `Try it now` funcionalidade. Esta funcionalidade permite-lhe introduzir as definições num formulário HTML baseada na web e fazer a consulta sem ter de escrever qualquer código. Depois da consulta de volta, um exemplo de comando CURL é fornecida para demonstrar os cabeçalhos HTTP e corpo formato necessário. |

![Home page do contentor](./media/cognitive-services-containers-api-documentation/container-webpage.png)

