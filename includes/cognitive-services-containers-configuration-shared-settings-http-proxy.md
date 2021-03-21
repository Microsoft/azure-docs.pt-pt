---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: ce4cc68826b39b5707549afc799d2d214e8876c6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96001201"
---
Se precisar de configurar um representante HTTP para fazer pedidos de saída, utilize estes dois argumentos:

| Name | Tipo de dados | Descrição |
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
