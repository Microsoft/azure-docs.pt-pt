---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 05/07/2019
ms.openlocfilehash: 02beb6bdce096c35d8948cc8aefab6cc97be907c
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67063950"
---
## <a name="use-the-docker-cli-to-authenticate-the-private-container-registry"></a>Utilizar a CLI do Docker para autenticar o registo de contentor privado

Pode autenticar com o registo de contentor privado para contentores de serviços cognitivos em qualquer uma das várias formas, mas o método recomendado na linha de comando consiste em utilizar o [CLI do Docker](https://docs.docker.com/engine/reference/commandline/cli/).

Utilize o [ `docker login` comando](https://docs.docker.com/engine/reference/commandline/login/), conforme mostrado no exemplo a seguir, para iniciar sessão no `containerpreview.azurecr.io`, o registo de contentor privado para contentores de serviços cognitivos. Substitua *\<nome de utilizador\>* com o nome de utilizador e *\<palavra-passe\>* com a palavra-passe que é fornecidas as credenciais que recebeu das Equipa de serviços cognitivos do Azure.

```
docker login containerpreview.azurecr.io -u <username> -p <password>
```

Se proteger as suas credenciais de um arquivo de texto, poderá concatenar o conteúdo desse ficheiro de texto, utilizando o `cat` comando, para o `docker login` de comando, conforme mostrado no exemplo a seguir. Substitua *\<passwordFile\>* com o caminho e nome do ficheiro de texto que contém a palavra-passe e *\<username\>* com o nome de utilizador que é fornecido as credenciais.

```
cat <passwordFile> | docker login containerpreview.azurecr.io -u <username> --password-stdin
```
