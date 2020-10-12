---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: b9d0d2b97472eb3264f5e4600fddbfc7d3250918
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "67704221"
---
## <a name="use-the-docker-cli-to-authenticate-the-private-container-registry"></a>Utilize o CLI do Docker para autenticar o registo privado de contentores

Pode autenticar-se com o registo privado de contentores para recipientes de serviços cognitivos de qualquer forma, mas o método recomendado pela linha de comando é utilizar o [Docker CLI](https://docs.docker.com/engine/reference/commandline/cli/).

Utilize o [ `docker login` comando](https://docs.docker.com/engine/reference/commandline/login/), como mostra o exemplo seguinte, para iniciar sessão `containerpreview.azurecr.io` no registo privado de contentores para recipientes de serviços cognitivos. Substitua *\<username\>* pelo nome de utilizador e pela *\<password\>* palavra-passe fornecida nas credenciais que recebeu da equipa dos Serviços Cognitivos da Azure.

```
docker login containerpreview.azurecr.io -u <username> -p <password>
```

Se tiver assegurado as suas credenciais num ficheiro de texto, pode concatenar o conteúdo desse ficheiro de texto, utilizando o `cat` comando, para o `docker login` comando, como mostra o exemplo seguinte. *\<passwordFile\>* Substitua-o pelo caminho e nome do ficheiro de texto que contém a palavra-passe e *\<username\>* pelo nome de utilizador fornecido nas suas credenciais.

```
cat <passwordFile> | docker login containerpreview.azurecr.io -u <username> --password-stdin
```
