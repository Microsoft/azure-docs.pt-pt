---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2019
ms.openlocfilehash: 2d3d7b37721ca1b19f5d73133352cabdbffe6d68
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/18/2019
ms.locfileid: "58886851"
---
Se precisar de configurar um proxy HTTP para fazer pedidos de saída, utilize estes dois argumentos:

| Name | Tipo de dados | Descrição |
|--|--|--|
|HTTP_PROXY|string|o proxy a utilizar, por exemplo, `http://proxy:8888`|
|HTTP_PROXY_CREDS|string|quaisquer credenciais necessárias para autenticar o proxy, por exemplo, username:password.|

```bash
docker run --rm -it -p 5000:5000 \
--memory 2g --cpus 1 \
--mount type=bind,src=/home/azureuser/output,target=/output \
<registry-location>/<image-name> \
Eula=accept \
Billing=<billing-endpoint> \
ApiKey=<api-key> \
HTTP_PROXY=http://190.169.1.6:3128 \
HTTP_PROXY_CREDS=jerry:123456 \
```