---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: 84cd8ed79281b005407b5a857398b5669635c072
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/18/2019
ms.locfileid: "68320509"
---
Se você precisar configurar um proxy HTTP para fazer solicitações de saída, use estes dois argumentos:

| Nome | Tipo de dados | Descrição |
|--|--|--|
|HTTP_PROXY|Cadeia de caracteres|O proxy a ser usado, por exemplo,`http://proxy:8888`<br>`<proxy-url>`|
|HTTP_PROXY_CREDS|Cadeia de caracteres|Todas as credenciais necessárias para a autenticação no proxy, por exemplo, username: password.|
|`<proxy-user>`|cadeia|O usuário para o proxy.|
|`<proxy-password>`|Cadeia de caracteres|A senha associada `<proxy-user>` ao proxy.|
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
