---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: 664cea26f910fa5b3354e2879a33de50eb13a7f3
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/16/2019
ms.locfileid: "68286178"
---
Se você precisar configurar um proxy HTTP para fazer solicitações de saída, use estes dois argumentos:

| Nome | Tipo de dados | Descrição |
|--|--|--|
|HTTP_PROXY|Cadeia de caracteres|O proxy a ser usado, por exemplo,`http://proxy:8888`<br>`<proxy-url>`|
|HTTP_PROXY_CREDS|Cadeia de caracteres|Todas as credenciais necessárias para a autenticação no proxy, por exemplo, username: password.|
|`<proxy-user>`|cadeia|O usuário para o proxy.|
|`proxy-password`|cadeia|A senha associada `<proxy-user>` ao proxy.|
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
