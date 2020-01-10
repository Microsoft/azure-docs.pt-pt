---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: b9d0d2b97472eb3264f5e4600fddbfc7d3250918
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/28/2019
ms.locfileid: "67704221"
---
## <a name="use-the-docker-cli-to-authenticate-the-private-container-registry"></a>Usar a CLI do Docker para autenticar o registro de contêiner privado

Você pode autenticar com o registro de contêiner privado para contêineres de serviços cognitivas de várias maneiras, mas o método recomendado na linha de comando é usar a [CLI do Docker](https://docs.docker.com/engine/reference/commandline/cli/).

Use o [comando`docker login`](https://docs.docker.com/engine/reference/commandline/login/), conforme mostrado no exemplo a seguir, para fazer logon em `containerpreview.azurecr.io`, o registro de contêiner privado para contêineres de serviços cognitivas. Substitua *\<username\>* pelo nome de usuário e *\<senha\>* pela senha fornecida nas credenciais que você recebeu da equipe de serviços cognitivas do Azure.

```
docker login containerpreview.azurecr.io -u <username> -p <password>
```

Se você protegeu suas credenciais em um arquivo de texto, você pode concatenar o conteúdo desse arquivo de texto, usando o comando `cat`, para o comando `docker login`, conforme mostrado no exemplo a seguir. Substitua *\<passwordfile\>* pelo caminho e nome do arquivo de texto que contém a senha e *\<nome* de usuário\>com o nome que é fornecido em suas credenciais.

```
cat <passwordFile> | docker login containerpreview.azurecr.io -u <username> --password-stdin
```
