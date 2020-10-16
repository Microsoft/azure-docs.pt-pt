---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: ce4cc68826b39b5707549afc799d2d214e8876c6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88639074"
---
Se precisar de configurar um representante HTTP para fazer pedidos de saída, utilize estes dois argumentos:

| Nome | Tipo de dados | Descrição |
|--|--|--|
|HTTPS_PROXY|string|O representante para usar, por exemplo, `https://proxy:8888`<br>`<proxy-url>`|
|HTTPS_PROXY_CREDS|string|Quaisquer credenciais necessárias para autenticar contra o proxy, por exemplo, nome de utilizador:password.|
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
HTTPS_PROXY=<proxy-url> \
HTTPS_PROXY_CREDS=<proxy-user>:<proxy-password> \
```
