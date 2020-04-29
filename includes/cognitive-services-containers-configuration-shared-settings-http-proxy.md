---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: 84cd8ed79281b005407b5a857398b5669635c072
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "68320509"
---
Se precisar de configurar um proxy HTTP para fazer pedidos de saída, utilize estes dois argumentos:

| Nome | Tipo de dados | Descrição |
|--|--|--|
|HTTP_PROXY|string|O representante a usar, por exemplo,`http://proxy:8888`<br>`<proxy-url>`|
|HTTP_PROXY_CREDS|string|Quaisquer credenciais necessárias para autenticar contra o representante, por exemplo, nome de utilizador:palavra-passe.|
|`<proxy-user>`|string|O utilizador do representante.|
|`<proxy-password>`|string|A senha associada `<proxy-user>` ao representante.|
||||


```bash
docker run --rm -it -p 5000:5000 \
--memory 2g --cpus 1 \
--mount type=bind,src=/home/azureuser/output,target=/output \
<registry-location>/<image-name> \
Eula=accept \
Billing=<endpoint> \
ApiKey=<api-key> \
HTTP_PROXY=<proxy-url> \
HTTP_PROXY_CREDS=<proxy-user>:<proxy-password> \
```
