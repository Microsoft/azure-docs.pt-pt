---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 05/07/2019
ms.openlocfilehash: 27c6b59a458fb79b86d7064d710a01593a1745dc
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66125139"
---
## <a name="use-docker-cli-to-authenticate-private-container-registry"></a>Utilizar a CLI do Docker para autenticar o registo de contentor privado

Existem várias formas de autenticar com o registo de contentor privado para contentores de serviços cognitivos, mas o método recomendado na linha de comando é utilizando o [CLI do Docker](https://docs.docker.com/engine/reference/commandline/cli/).

Utilize o [início de sessão do docker](https://docs.docker.com/engine/reference/commandline/login/) comando, conforme mostrado no exemplo a seguir, para iniciar sessão no `containerpreview.azurecr.io`, o registo de contentor privado para contentores de serviços cognitivos. Substitua *\<nome de utilizador\>* com o nome de utilizador e *\<palavra-passe\>* com a palavra-passe que forneceu as credenciais que recebeu do Azure Equipa de serviços cognitiva.

```
docker login containerpreview.azurecr.io -u <username> -p <password>
```

Se proteger as suas credenciais de um arquivo de texto, poderá concatenar os conteúdos de texto de ficheiros, com o `cat` comando, para o `docker login` comando conforme mostrado no exemplo a seguir. Substitua *\<passwordFile\>* com o caminho e nome do ficheiro de texto que contém a palavra-passe e *\<username\>* com o nome de utilizador fornecido as credenciais.

```
cat <passwordFile> | docker login containerpreview.azurecr.io -u <username> --password-stdin
```
