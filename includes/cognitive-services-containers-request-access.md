---
author: IEvangelist
ms.author: dapine
ms.service: cognitive-services
ms.topic: include
ms.date: 03/04/2020
ms.openlocfilehash: 88d83676de1e7fa18c4c1dcbf347da8d685ba2fa
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83593341"
---
Preencha e submeta os recipientes dos [Serviços Cognitivos a solicitar](https://aka.ms/cognitivegate) o formulário de pedido para solicitar o acesso ao recipiente. O formulário solicita informações sobre si, a sua empresa e o cenário de utilizador para o qual utilizará o recipiente. Depois de submeter o formulário, a equipa de Serviços Cognitivos Azure revê-o para se certificar de que satisfaz os critérios de acesso ao registo de contentores privados.

> [!IMPORTANT]
> Deve utilizar um endereço de e-mail associado a uma Conta Microsoft (MSA) ou a uma conta Azure Ative Directory (Azure AD) no formulário. Para obter mais informações sobre os critérios de aceitação, consulte [Serviços Cognitivos - Processo de Gating](../articles/cognitive-services/cognitive-services-gating-process.md).

Se o seu pedido for aprovado, receberá um e-mail com instruções que descrevem como obter as suas credenciais e aceder ao registo de contentores privados.

## <a name="log-in-to-the-private-container-registry"></a>Faça login no registo de contentores privados

Existem várias formas de autenticar com o registo de contentores privados para os contentores dos Serviços Cognitivos. Recomendamos que utilize o método da linha de comando utilizando o [Docker CLI](https://docs.docker.com/engine/reference/commandline/cli/).

Utilize o comando de [login do estivador,](https://docs.docker.com/engine/reference/commandline/login/) como se pode ver no seguinte exemplo, para iniciar sessão em `containerpreview.azurecr.io` , que é o registo de contentores privados para recipientes de Serviços Cognitivos. Substitua o * \< nome \> * de utilizador pelo nome de utilizador e * \< palavra-passe \> * pela palavra-passe fornecida nas credenciais recebidas da equipa de Serviços Cognitivos Do Azure.

```
docker login containerpreview.azurecr.io -u <username> -p <password>
```

Se tiver as suas credenciais num ficheiro de texto, pode concatenar o conteúdo desse ficheiro de texto ao `docker login` comando. Utilize o `cat` comando, como se pode ver no seguinte exemplo. Substitua o * \< passwordFile \> * pelo caminho e nome do ficheiro de texto que contém a palavra-passe. Substitua o * \< nome \> * de utilizador pelo nome de utilizador fornecido nas suas credenciais.

```
cat <passwordFile> | docker login containerpreview.azurecr.io -u <username> --password-stdin
```

