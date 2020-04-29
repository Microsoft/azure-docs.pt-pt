---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: b9d0d2b97472eb3264f5e4600fddbfc7d3250918
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "67704221"
---
## <a name="use-the-docker-cli-to-authenticate-the-private-container-registry"></a>Utilize o Docker CLI para autenticar o registo de contentores privados

Pode autenticar com o registo de contentores privados para recipientes de serviços cognitivos de várias maneiras, mas o método recomendado da linha de comando é utilizar o [Docker CLI](https://docs.docker.com/engine/reference/commandline/cli/).

Utilize [ `docker login` ](https://docs.docker.com/engine/reference/commandline/login/)o comando , como se pode ver `containerpreview.azurecr.io`no seguinte exemplo, para iniciar sessão no registo de contentores privados para recipientes de serviços cognitivos. Substitua * \<\> * o nome de utilizador pelo nome de utilizador e * \<palavra-passe\> * pela palavra-passe fornecida nas credenciais que recebeu da equipa dos Serviços Cognitivos do Azure.

```
docker login containerpreview.azurecr.io -u <username> -p <password>
```

Se tiver assegurado as suas credenciais num ficheiro de texto, pode concatenar o `cat` conteúdo desse `docker login` ficheiro de texto, utilizando o comando, para o comando, como se pode ver no seguinte exemplo. Substitua * \<\> o passwordFile* pelo caminho e nome do ficheiro de texto que contém a palavra-passe e * \<\> * o nome de utilizador com o nome de utilizador que está fornecido nas suas credenciais.

```
cat <passwordFile> | docker login containerpreview.azurecr.io -u <username> --password-stdin
```
