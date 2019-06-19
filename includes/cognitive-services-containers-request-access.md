---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 01/24/2019
ms.openlocfilehash: 4cdcec850f32d7e94f33eb28e5bf7839e511f347
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67184582"
---
Preencha e submeta a [formulário de pedido de contentores de imagem digitalizada dos serviços cognitivos](https://aka.ms/VisionContainersPreview) para pedir acesso ao contentor. O formulário solicita as informações sobre si, sua empresa e o cenário de utilizador que vai utilizar o contentor. Depois de submeter o formulário, a equipe de serviços cognitivos do Azure revê-lo para se certificar de que cumpre os critérios para acesso ao registo de contentor privado.

> [!IMPORTANT]
> Tem de utilizar um endereço de e-mail associado com uma conta Microsoft (MSA) ou uma conta do Azure Active Directory (Azure AD) no formulário.

Se o pedido for aprovado, receberá um e-mail com instruções que descrevem como obter as suas credenciais e aceder ao registo de contentor privado.

## <a name="log-in-to-the-private-container-registry"></a>Inicie sessão particular registo de contentor

Existem várias formas de autenticar com o registo de contentor privado para contentores de serviços cognitivos. Recomendamos que utilize o método da linha de comandos utilizando a [CLI do Docker](https://docs.docker.com/engine/reference/commandline/cli/).

Utilize o [início de sessão do docker](https://docs.docker.com/engine/reference/commandline/login/) comando, conforme mostrado no exemplo a seguir, para iniciar sessão no `containerpreview.azurecr.io`, que é o registo de contentor privado para contentores de serviços cognitivos. Substitua *\<nome de utilizador\>* com o nome de utilizador e *\<palavra-passe\>* com a palavra-passe que forneceu as credenciais que recebeu do Azure Equipa de serviços cognitiva.

```
docker login containerpreview.azurecr.io -u <username> -p <password>
```

Se proteger as suas credenciais de um arquivo de texto, poderá concatenar o conteúdo desse ficheiro de texto para o `docker login` comando. Utilize o `cat` de comando, conforme mostrado no exemplo a seguir. Substitua *\<passwordFile\>* com o caminho e nome do ficheiro de texto que contém a palavra-passe. Substitua *\<nome de utilizador\>* com o nome de utilizador fornecido nas suas credenciais.

```
cat <passwordFile> | docker login containerpreview.azurecr.io -u <username> --password-stdin
```

