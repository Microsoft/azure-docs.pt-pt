---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: 7f51b7fe95445cbd3a8aff530f89ce55b5abfb1e
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2021
ms.locfileid: "105630106"
---
Se precisar de configurar um representante HTTP para fazer pedidos de saída, utilize estes dois argumentos:

| Name | Tipo de dados | Descrição |
|--|--|--|
|HTTP_PROXY|string|O representante para usar, por exemplo, `http://proxy:8888`<br>`<proxy-url>`|
|HTTP_PROXY_CREDS|string|Quaisquer credenciais necessárias para autenticar contra o representante, por exemplo, `username:password` . Este valor **deve ser em minúsculas.** |
|`<proxy-user>`|string|O utilizador do representante.|
|`<proxy-password>`|string|A palavra-passe associada `<proxy-user>` ao representante.|
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
