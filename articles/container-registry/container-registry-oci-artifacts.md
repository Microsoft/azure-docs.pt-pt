---
title: Enviar artefatos de OCI para o registro de contêiner do Azure privado
description: Artefatos de OCI e pull (Push Open container Initiative) usando um registro de contêiner privado no Azure
services: container-registry
author: SteveLasker
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 08/30/2019
ms.author: stevelas
ms.custom: ''
ms.openlocfilehash: 69423f85aecdc3f8049a7e784888e1f71d0bc702
ms.sourcegitcommit: 7a6d8e841a12052f1ddfe483d1c9b313f21ae9e6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/30/2019
ms.locfileid: "70182703"
---
# <a name="push-and-pull-an-oci-artifact-using-an-azure-container-registry"></a>Enviar por push e efetuar pull de um artefato de OCI usando um registro de contêiner do Azure

Você pode usar um registro de contêiner do Azure para armazenar e gerenciar artefatos de [OCI (iniciativa de contêiner aberto)](container-registry-image-formats.md#oci-artifacts) , bem como imagens de contêiner do Docker e do Docker compatíveis.

Para demonstrar esse recurso, este artigo mostra como usar a ferramenta de [registro de OCI como armazenamento (oras)](https://github.com/deislabs/oras) para enviar por push um artefato de exemplo – um arquivo de texto-para um registro de contêiner do Azure. Em seguida, extraia o artefato do registro. Você pode gerenciar uma variedade de artefatos de OCI em um registro de contêiner do Azure usando diferentes ferramentas de linha de comando apropriadas para cada artefato.

## <a name="prerequisites"></a>Pré-requisitos

* **Registo de contentores do Azure** - crie um registos de contentores na sua subscrição do Azure. Por exemplo, use o [portal do Azure](container-registry-get-started-portal.md) ou o [CLI do Azure](container-registry-get-started-azure-cli.md).
* **Ferramenta oras** – Baixe e instale uma versão atual do oras para o seu sistema operacional do [repositório GitHub](https://github.com/deislabs/oras/releases). A ferramenta é lançada como um tarball compactado`.tar.gz` (arquivo). Extraia e instale o arquivo usando os procedimentos padrão para seu sistema operacional.
* **Azure Active Directory entidade de serviço (opcional)** – para autenticar diretamente com o Oras, crie uma [entidade de serviço](container-registry-auth-service-principal.md) para acessar o registro. Verifique se a entidade de serviço recebe uma função como AcrPush para que ela tenha permissões para enviar e extrair artefatos.
* **CLI do Azure (opcional)** – para usar uma identidade individual, você precisa de uma instalação local do CLI do Azure. A versão 2.0.71 ou posterior é recomendada. Execute `az --version `para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure](/cli/azure/install-azure-cli).
* **Docker (opcional)** – para usar uma identidade individual, você também deve ter o Docker instalado localmente para autenticar com o registro. O Docker fornece pacotes que configuram facilmente o Docker em qualquer sistema [MacOS][docker-mac], [Windows][docker-windows]ou [Linux][docker-linux] .


## <a name="sign-in-to-a-registry"></a>Entrar em um registro

Esta seção mostra dois fluxos de trabalho sugeridos para entrar no registro, dependendo da identidade usada. Escolha o método apropriado para o seu ambiente.

### <a name="sign-in-with-oras"></a>Entrar com ORAS

Usando uma [entidade de serviço](container-registry-auth-service-principal.md) com direitos de push, `oras login` execute o comando para entrar no registro usando a ID do aplicativo da entidade de serviço e a senha. Especifique o nome totalmente qualificado do registro (todas as letras minúsculas), neste caso, *myregistry.azurecr.Io*. A ID do aplicativo da entidade de serviço é passada na `$SP_APP_ID`variável de ambiente e a senha na `$SP_PASSWD`variável.

```bash
oras login myregistry.azurecr.io --username $SP_APP_ID --password $SP_PASSWD
```

Para ler a senha de stdin, use `--password-stdin`.

### <a name="sign-in-with-azure-cli"></a>Iniciar sessão com a CLI do Azure

[Entre](/cli/azure/authenticate-azure-cli) no CLI do Azure com sua identidade para enviar e extrair artefatos do registro de contêiner.

Em seguida, use o comando CLI do Azure [AZ ACR login](/cli/azure/acr?view=azure-cli-latest#az-acr-login) para acessar o registro. Por exemplo, para autenticar em um registrochamado myregistry:

```azurecli
az login
az acr login --name myregistry
```

> [!NOTE]
> `az acr login`usa o cliente do Docker para definir um token Azure Active Directory no `docker.config` arquivo. O cliente do Docker deve estar instalado e em execução para concluir o fluxo de autenticação individual.

## <a name="push-an-artifact"></a>Enviar um artefato por push

Crie um arquivo de texto em um diretório de trabalho de trabalho local com algum texto de exemplo. Por exemplo, em um shell bash:

```bash
echo "Here is an artifact!" > artifact.txt
```

Use o `oras push` comando para enviar esse arquivo de texto por push para o registro. O exemplo a seguir envia por push o arquivo de texto `samples/artifact` de exemplo para o repositório. O registro é identificado com o nome de registro totalmente qualificado *myregistry.azurecr.Io* (todas as letras minúsculas). O artefato está marcado `1.0`. O artefato tem um tipo indefinido, por padrão, identificado pela cadeia de caracteres do *tipo* de mídia `artifact.txt`após o nome do arquivo. Consulte [artefatos de OCI](https://github.com/opencontainers/artifacts) para tipos adicionais. 

```bash
oras push myregistry.azurecr.io/samples/artifact:1.0 \
    --manifest-config /dev/null:application/vnd.unknown.config.v1+json \
    ./artifact.txt:application/vnd.unknown.layer.v1+txt
```

A saída para um push bem-sucedido é semelhante ao seguinte:

```console
Uploading 33998889555f artifact.txt
Pushed myregistry.azurecr.io/samples/artifact:1.0
Digest: sha256:xxxxxxbc912ef63e69136f05f1078dbf8d00960a79ee73c210eb2a5f65xxxxxx
```

Para gerenciar artefatos em seu registro, se você estiver usando o CLI do Azure, execute `az acr` comandos padrão para gerenciar imagens. Por exemplo, obtenha os atributos do artefato usando o comando [AZ ACR Repository show][az-acr-repository-show] :

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

## <a name="pull-an-artifact"></a>Efetuar pull de um artefato

Execute o `oras pull` comando para efetuar pull do artefato do registro.

Primeiro, remova o arquivo de texto do diretório de trabalho local:

```bash
rm artifact.txt
```

Execute `oras pull` para efetuar pull do artefato e especifique o tipo de mídia usado para enviar o artefato por push:

```bash
oras pull myregistry.azurecr.io/samples/artifact:1.0 \
    --media-type application/vnd.unknown.layer.v1+txt
```

Verifique se o pull foi bem-sucedido:

```bash
$ cat artifact.txt
Here is an artifact!
```

## <a name="remove-the-artifact-optional"></a>Remover o artefato (opcional)

Para remover o artefato do registro de contêiner do Azure, use o comando [AZ ACR Repository Delete][az-acr-repository-delete] . O exemplo a seguir remove o artefato que você armazenou lá:

```azurecli
az acr repository delete \
    --name myregistry \
    --image samples/artifact:1.0
```

## <a name="next-steps"></a>Passos Seguintes

* Saiba mais sobre [a biblioteca oras](https://github.com/deislabs/oras/tree/master/docs), incluindo como configurar um manifesto para um artefato
* Visite o repositório de artefatos de [OCI](https://github.com/opencontainers/artifacts) para obter informações de referência sobre novos tipos de artefato



<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - internal -->
[az-acr-repository-show]: /cli/azure/acr/repository?#az-acr-repository-show
[az-acr-repository-delete]: /cli/azure/acr/repository#az-acr-repository-delete
