---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: 9911b1c92bdca6c0cdf064ea484cfb603e659467
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67712494"
---
Se precisar de configurar um proxy HTTP para fazer pedidos de saída, utilize estes dois argumentos:

| Nome | Tipo de dados | Descrição |
|--|--|--|
|HTTP_PROXY|Cadeia de caracteres|o proxy a utilizar, por exemplo, `http://proxy:8888`<br><proxy-url>|
|HTTP_PROXY_CREDS|Cadeia de caracteres|quaisquer credenciais necessárias para autenticar o proxy, por exemplo, username:password.|
|`<proxy-user>`|cadeia|O utilizador para o proxy.|
|`proxy-password`|Cadeia de caracteres|A palavra-passe associada `<proxy-user>` para o proxy.|
||||


```bash
docker run --rm -it -p 5000:5000 \
--memory 2g --cpus 1 \
--mount type=bind,src=/home/azureuser/output,target=/output \
<registry-location>/<image-name> \
Eula=accept \
Billing=<billing-endpoint> \
ApiKey=<api-key> \
HTTP_PROXY=<proxy-url> \
HTTP_PROXY_CREDS=<proxy-user>:<proxy-password> \
```