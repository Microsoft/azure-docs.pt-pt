---
title: Empurre e puxe artefacto oCI
description: Empurre e puxe artefactos da Open Container Initiative (OCI) utilizando um registo privado de contentores em Azure
author: SteveLasker
manager: gwallace
ms.topic: article
ms.date: 03/11/2020
ms.author: stevelas
ms.openlocfilehash: 2c6b66b635a2513ccc19e0352414d18d8389fef1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79371057"
---
# <a name="push-and-pull-an-oci-artifact-using-an-azure-container-registry"></a>Empurre e puxe um artefacto OCI usando um registo de contentores Azure

Pode utilizar um registo de contentores Azure para armazenar e gerir [artefactos da Open Container Initiative (OCI),](container-registry-image-formats.md#oci-artifacts) bem como imagens de contentores compatíveis com Docker e Docker.

Para demonstrar esta capacidade, este artigo mostra como usar a ferramenta [OCI Registry as Storage (ORAS)](https://github.com/deislabs/oras) para empurrar um artefacto de amostra - um ficheiro de texto - para um registo de contentores Azure. Então, retire o artefacto do registo. Você pode gerir uma variedade de artefactos OCI em um registo de contentores Azure usando diferentes ferramentas de linha de comando adequadas a cada artefacto.

## <a name="prerequisites"></a>Pré-requisitos

* **Registo de contentores do Azure** - crie um registos de contentores na sua subscrição do Azure. Por exemplo, utilize o [portal Azure](container-registry-get-started-portal.md) ou o [Azure CLI](container-registry-get-started-azure-cli.md).
* **Ferramenta ORAS** - Descarregue e instale uma versão ORAS atual para o seu sistema operativo a partir do [repo GitHub](https://github.com/deislabs/oras/releases). A ferramenta é libertada como uma`.tar.gz` bola de tarball comprimido (ficheiro). Extrair e instalar o ficheiro utilizando procedimentos padrão para o seu sistema operativo.
* **Diretoria Ativa azure (opcional)** - Para autenticar diretamente com oras, crie um [serviço principal](container-registry-auth-service-principal.md) para aceder ao seu registo. Certifique-se de que o diretor de serviço é atribuído a uma função como a AcrPush para que tenha permissões para empurrar e puxar artefactos.
* **Azure CLI (opcional)** - Para utilizar uma identidade individual, necessita de uma instalação local do Azure CLI. A versão 2.0.71 ou posterior é recomendada. Corra `az --version `para encontrar a versão. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)](/cli/azure/install-azure-cli).
* **Docker (opcional)** - Para utilizar uma identidade individual, também deve ter o Docker instalado localmente, para autenticar com o registo. O Docker disponibiliza pacotes que o configuram facilmente em qualquer sistema [macOS][docker-mac], [Windows][docker-windows] ou [Linux][docker-linux].


## <a name="sign-in-to-a-registry"></a>Inscreva-se num registo

Esta secção mostra dois fluxos de trabalho sugeridos para assinar no registo, dependendo da identidade utilizada. Escolha o método adequado para o seu ambiente.

### <a name="sign-in-with-oras"></a>Inscreva-se na ORAS

Utilizando um diretor de [serviço](container-registry-auth-service-principal.md) `oras login` com direitos de impulso, execute o comando para iniciar sessão no registo utilizando o ID de aplicação principal do serviço e a palavra-passe. Especifique o nome do registo totalmente qualificado (todas as minúsculas), neste caso *myregistry.azurecr.io*. O ID de aplicação principal `$SP_APP_ID`do serviço é passado `$SP_PASSWD`na variável ambiente, e a palavra-passe na variável .

```bash
oras login myregistry.azurecr.io --username $SP_APP_ID --password $SP_PASSWD
```

Para ler a palavra-passe de `--password-stdin`Stdin, use .

### <a name="sign-in-with-azure-cli"></a>Iniciar sessão com a CLI do Azure

[Inscreva-se no](/cli/azure/authenticate-azure-cli) Azure CLI com a sua identidade para empurrar e retirar artefactos do registo do contentor.

Em seguida, utilize o comando Azure CLI [az acr login](/cli/azure/acr?view=azure-cli-latest#az-acr-login) para aceder ao registo. Por exemplo, autenticar um registo chamado *myregistry:*

```azurecli
az login
az acr login --name myregistry
```

> [!NOTE]
> `az acr login`usa o cliente Docker para definir um token `docker.config` de Diretório Ativo Azure no ficheiro. O cliente Docker deve ser instalado e em execução para completar o fluxo de autenticação individual.

## <a name="push-an-artifact"></a>Empurre um artefacto

Crie um ficheiro de texto num diretório de trabalho local com algum texto de amostra. Por exemplo, numa concha de bash:

```bash
echo "Here is an artifact!" > artifact.txt
```

Utilize `oras push` o comando para empurrar este ficheiro de texto para o seu registo. O exemplo seguinte empurra o ficheiro `samples/artifact` de texto da amostra para o repo. O registo é identificado com o nome de registo totalmente qualificado *myregistry.azurecr.io* (todas as minúsculas). O artefacto está `1.0`marcado. O artefacto tem um tipo indefinido, por defeito, identificado `artifact.txt`pela cadeia do tipo de *mídia* seguindo o nome de ficheiro . Consulte [artefactos OCI](https://github.com/opencontainers/artifacts) para obter tipos adicionais. 

**Linux**

```bash
oras push myregistry.azurecr.io/samples/artifact:1.0 \
    --manifest-config /dev/null:application/vnd.unknown.config.v1+json \
    ./artifact.txt:application/vnd.unknown.layer.v1+txt
```

**Windows**

```cmd
.\oras.exe push myregistry.azurecr.io/samples/artifact:1.0 ^
    --manifest-config NUL:application/vnd.unknown.config.v1+json ^
    .\artifact.txt:application/vnd.unknown.layer.v1+txt
```

A saída para um impulso bem sucedido é semelhante à seguinte:

```console
Uploading 33998889555f artifact.txt
Pushed myregistry.azurecr.io/samples/artifact:1.0
Digest: sha256:xxxxxxbc912ef63e69136f05f1078dbf8d00960a79ee73c210eb2a5f65xxxxxx
```

Para gerir artefactos no seu registo, se estiver a utilizar `az acr` o Azure CLI, execute comandos padrão para gerir imagens. Por exemplo, obtenha os atributos do artefacto usando o comando de show de [repositório az acr:][az-acr-repository-show]

```azurecli
az acr repository show \
    --name myregistry \
    --image samples/artifact:1.0
```

O resultado é semelhante ao seguinte:

```json
{
  "changeableAttributes": {
    "deleteEnabled": true,
    "listEnabled": true,
    "readEnabled": true,
    "writeEnabled": true
  },
  "createdTime": "2019-08-28T20:43:31.0001687Z",
  "digest": "sha256:xxxxxxbc912ef63e69136f05f1078dbf8d00960a79ee73c210eb2a5f65xxxxxx",
  "lastUpdateTime": "2019-08-28T20:43:31.0001687Z",
  "name": "1.0",
  "signed": false
}
```

## <a name="pull-an-artifact"></a>Puxe um artefacto

Mande `oras pull` o comando para retirar o artefacto do seu registo.

Primeiro remova o ficheiro de texto do seu diretório de trabalho local:

```bash
rm artifact.txt
```

Corra `oras pull` para puxar o artefacto e especifique o tipo de mídia utilizado para empurrar o artefacto:

```bash
oras pull myregistry.azurecr.io/samples/artifact:1.0 \
    --media-type application/vnd.unknown.layer.v1+txt
```

Verifique se a atração foi bem sucedida:

```bash
$ cat artifact.txt
Here is an artifact!
```

## <a name="remove-the-artifact-optional"></a>Retire o artefacto (opcional)

Para remover o artefacto do registo do seu contentor Azure, utilize o comando de eliminação do [repositório az acr.][az-acr-repository-delete] O exemplo seguinte remove o artefacto que aí guardou:

```azurecli
az acr repository delete \
    --name myregistry \
    --image samples/artifact:1.0
```

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [a Biblioteca ORAS,](https://github.com/deislabs/oras/tree/master/docs)incluindo como configurar um manifesto para um artefacto
* Visite o Repo de Artefactos oci para obter informações de referência sobre novos tipos de [artefactos](https://github.com/opencontainers/artifacts)



<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - internal -->
[az-acr-repository-show]: /cli/azure/acr/repository?#az-acr-repository-show
[az-acr-repository-delete]: /cli/azure/acr/repository#az-acr-repository-delete
