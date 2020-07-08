---
title: Empurre e puxe o artefacto do OCI
description: Empurre e puxe artefactos da Iniciativa de Contentores Abertos (OCI) utilizando um registo privado de contentores em Azure
author: SteveLasker
manager: gwallace
ms.topic: article
ms.date: 03/11/2020
ms.author: stevelas
ms.openlocfilehash: 2c6b66b635a2513ccc19e0352414d18d8389fef1
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "79371057"
---
# <a name="push-and-pull-an-oci-artifact-using-an-azure-container-registry"></a>Empurre e puxe um artefacto OCI usando um registo de contentores Azure

Você pode usar um registo de contentores Azure para armazenar e gerir [artefactos open container Initiative (OCI),](container-registry-image-formats.md#oci-artifacts) bem como imagens de recipientes compatíveis com Docker e Docker.

Para demonstrar esta capacidade, este artigo mostra como usar a ferramenta [de Registo OCI como Armazenamento (ORAS)](https://github.com/deislabs/oras) para empurrar um artefacto de amostra - um ficheiro de texto - para um registo de contentores Azure. Em seguida, retire o artefacto do registo. Você pode gerir uma variedade de artefactos OCI em um registro de contentores Azure usando diferentes ferramentas de linha de comando apropriadas a cada artefacto.

## <a name="prerequisites"></a>Pré-requisitos

* **Registo de contentores do Azure** - crie um registos de contentores na sua subscrição do Azure. Por exemplo, utilize o [portal Azure](container-registry-get-started-portal.md) ou o [Azure CLI](container-registry-get-started-azure-cli.md).
* **Ferramenta ORAS** - Descarregue e instale um desbloqueio ORAS atual para o seu sistema operativo a partir do [repo GitHub](https://github.com/deislabs/oras/releases). A ferramenta é libertada como uma bola de tarball comprimido `.tar.gz` (ficheiro). Extrair e instalar o ficheiro utilizando procedimentos padrão para o seu sistema operativo.
* **Azure Ative Directory service principal (opcional)** - Para autenticar diretamente com o ORAS, crie um [principal de serviço](container-registry-auth-service-principal.md) para aceder ao seu registo. Certifique-se de que o diretor de serviço é atribuído a um papel como acrPush para que tenha permissões para empurrar e puxar artefactos.
* **Azure CLI (opcional)** - Para utilizar uma identidade individual, necessita de uma instalação local do Azure CLI. Recomenda-se a versão 2.0.71 ou posterior. Corre `az --version ` para encontrar a versão. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)](/cli/azure/install-azure-cli).
* **Docker (opcional)** - Para utilizar uma identidade individual, também deve ter Docker instalado localmente, para autenticar com o registo. O Docker disponibiliza pacotes que o configuram facilmente em qualquer sistema [macOS][docker-mac], [Windows][docker-windows] ou [Linux][docker-linux].


## <a name="sign-in-to-a-registry"></a>Inscreva-se num registo

Esta secção mostra dois fluxos de trabalho sugeridos para assinar no registo, dependendo da identidade utilizada. Escolha o método apropriado para o seu ambiente.

### <a name="sign-in-with-oras"></a>Inscreva-se com ORAS

Utilizando um [principal de serviço](container-registry-auth-service-principal.md) com direitos de pressão, executar o comando para iniciar `oras login` sôm no registo usando o ID principal da aplicação de serviço e senha. Especificar o nome de registo totalmente qualificado (todos os minúsculos), neste caso *myregistry.azurecr.io*. O ID principal de aplicação de serviço é passado na variável `$SP_APP_ID` ambiente, e a palavra-passe na variável `$SP_PASSWD` .

```bash
oras login myregistry.azurecr.io --username $SP_APP_ID --password $SP_PASSWD
```

Para ler a palavra-passe de Stdin, use `--password-stdin` .

### <a name="sign-in-with-azure-cli"></a>Iniciar sessão com a CLI do Azure

[Inscreva-se no](/cli/azure/authenticate-azure-cli) CLI Azure com a sua identidade para empurrar e retirar artefactos do registo do contentor.

Em seguida, utilize o login de comando Azure CLI [az acr](/cli/azure/acr?view=azure-cli-latest#az-acr-login) para aceder ao registo. Por exemplo, autenticar a um registo denominado *miogressiário:*

```azurecli
az login
az acr login --name myregistry
```

> [!NOTE]
> `az acr login`usa o cliente Docker para definir um token Azure Ative Directory no `docker.config` ficheiro. O cliente Docker deve ser instalado e em execução para completar o fluxo de autenticação individual.

## <a name="push-an-artifact"></a>Empurre um artefacto

Crie um ficheiro de texto num diretório de trabalho local com algum texto de amostra. Por exemplo, numa casca de pancada:

```bash
echo "Here is an artifact!" > artifact.txt
```

Utilize o `oras push` comando para empurrar este ficheiro de texto para o seu registo. O exemplo a seguir empurra o ficheiro de texto da amostra para o `samples/artifact` repo. O registo é identificado com o nome de registo totalmente qualificado *myregistry.azurecr.io* (todos os minúsculos). O artefacto está `1.0` marcado. O artefacto tem um tipo indefinido, por padrão, identificado pela cadeia *tipo mídia* que segue o nome de ficheiro `artifact.txt` . Consulte [os artefactos OCI](https://github.com/opencontainers/artifacts) para obter tipos adicionais. 

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

Para gerir artefactos no seu registo, se estiver a utilizar o CLI Azure, executar comandos padrão `az acr` para gerir imagens. Por exemplo, obtenha os atributos do artefacto usando o comando [de exibição de repositório az acr:][az-acr-repository-show]

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

Executar o `oras pull` comando para retirar o artefacto do seu registo.

Primeiro, remova o ficheiro de texto do seu diretório de trabalho local:

```bash
rm artifact.txt
```

Corra `oras pull` para puxar o artefacto e especifique o tipo de mídia usado para empurrar o artefacto:

```bash
oras pull myregistry.azurecr.io/samples/artifact:1.0 \
    --media-type application/vnd.unknown.layer.v1+txt
```

Verifique se a atração foi bem sucedida:

```bash
$ cat artifact.txt
Here is an artifact!
```

## <a name="remove-the-artifact-optional"></a>Remova o artefacto (opcional)

Para remover o artefacto do seu registo de contentores Azure, utilize o comando [de eliminação do repositório az acr.][az-acr-repository-delete] O exemplo a seguir remove o artefacto que aí armazenaste:

```azurecli
az acr repository delete \
    --name myregistry \
    --image samples/artifact:1.0
```

## <a name="next-steps"></a>Próximos passos

* Saiba mais sobre [a Biblioteca ORAS,](https://github.com/deislabs/oras/tree/master/docs)incluindo como configurar um manifesto para um artefacto
* Visite o [repo de artefactos OCI](https://github.com/opencontainers/artifacts) para obter informações de referência sobre novos tipos de artefactos



<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - internal -->
[az-acr-repository-show]: /cli/azure/acr/repository?#az-acr-repository-show
[az-acr-repository-delete]: /cli/azure/acr/repository#az-acr-repository-delete
