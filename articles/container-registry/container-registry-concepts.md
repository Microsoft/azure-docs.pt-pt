---
title: Sobre repositórios e imagens no registro de contêiner do Azure
description: Introdução aos principais conceitos de registros de contêiner do Azure, repositórios e imagens de contêiner.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 09/10/2019
ms.author: danlep
ms.openlocfilehash: 9a3d4a7d9c3fd4a0465d4e780024559a71372d9d
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/26/2019
ms.locfileid: "71300207"
---
# <a name="about-registries-repositories-and-images"></a>Sobre registros, repositórios e imagens

Este artigo apresenta os principais conceitos de registros de contêiner, repositórios e imagens de contêiner e artefatos relacionados. 

## <a name="registry"></a>Registo

Um *registro* de contêiner é um serviço que armazena e distribui imagens de contêiner. O Hub do Docker é um registro de contêiner público que dá suporte à comunidade de software livre e serve como um catálogo geral de imagens. O registro de contêiner do Azure fornece aos usuários o controle direto de suas imagens, com autenticação integrada, [replicação geográfica](container-registry-geo-replication.md) com suporte para distribuição global e confiabilidade para implantações de fechamento de rede, [rede virtual e firewall configuração](container-registry-vnet.md), [bloqueio de marca](container-registry-image-lock.md)e muitos outros recursos avançados. 

Além das imagens de contêiner do Docker, o registro de contêiner do Azure dá suporte a [artefatos de conteúdo](container-registry-image-formats.md) relacionados, incluindo formatos de imagem de OCI (Open container Initiative).

## <a name="content-addressable-elements-of-an-artifact"></a>Elementos endereçáveis de conteúdo de um artefato

O endereço de um artefato em um registro de contêiner do Azure inclui os elementos a seguir. 

```
[loginUrl]/[namespace]/[artifact:][tag]
```

* **LoginUrl** -o nome totalmente qualificado do host do registro. O host do registro em um registro de contêiner do Azure está no formato *myregistry*. azurecr.IO (todas as letras minúsculas). Você deve especificar o loginUrl ao usar o Docker ou outras ferramentas de cliente para efetuar pull ou enviar artefatos por push para um registro de contêiner do Azure. 
* **namespace** – agrupamento lógico delimitado por barra de imagens ou artefatos relacionados – por exemplo, para um grupo de trabalho ou aplicativo
* **artefato** -o nome de um repositório para uma imagem ou um artefato específico
* **marca** -uma versão específica de uma imagem ou um artefato armazenado em um repositório


Por exemplo, o nome completo de uma imagem em um registro de contêiner do Azure pode parecer com o seguinte:

```
myregistry.azurecr.io/marketing/campaign10-18/email-sender:v2
```

Consulte as seções a seguir para obter detalhes sobre esses elementos.

## <a name="repository-name"></a>Nome do repositório

Os registros de contêiner gerenciam *repositórios*, coleções de imagens de contêiner ou outros artefatos com o mesmo nome, mas marcas diferentes. Por exemplo, as três imagens a seguir estão no repositório "ACR-HelloWorld":

```
acr-helloworld:latest
acr-helloworld:v1
acr-helloworld:v2
```

Os nomes de repositório também podem incluir [namespaces](container-registry-best-practices.md#repository-namespaces). Os namespaces permitem que você agrupe imagens usando nomes de repositório delimitados por barra, por exemplo:

```
marketing/campaign10-18/web:v2
marketing/campaign10-18/api:v3
marketing/campaign10-18/email-sender:v2
product-returns/web-submission:20180604
product-returns/legacy-integrator:20180715
```

## <a name="image"></a>Image

Uma imagem de contêiner ou outro artefato dentro de um registro é associado a uma ou mais marcas, tem uma ou mais camadas e é identificado por um manifesto. Entender como esses componentes se relacionam entre si pode ajudá-lo a gerenciar seu registro com eficiência.

### <a name="tag"></a>Etiqueta

A *marca* para uma imagem ou outro artefato especifica sua versão. Um único artefato dentro de um repositório pode ser atribuído a uma ou várias marcas e também pode ser "não marcado". Ou seja, você pode excluir todas as marcas de uma imagem, enquanto os dados da imagem (suas camadas) permanecem no registro.

O repositório (ou repositório e namespace) mais uma marca define o nome de uma imagem. Você pode enviar e extrair uma imagem especificando seu nome na operação de envio por Push ou pull.

A maneira como você marca as imagens de contêiner é guiada por seus cenários para desenvolver ou implantá-las. Por exemplo, marcas estáveis são recomendadas para manter suas imagens base e marcas exclusivas para a implantação de imagens. Para obter mais informações, consulte [recomendações para marcação e controle de versão de imagens de contêiner](container-registry-image-tag-version.md).

### <a name="layer"></a>Camada

As imagens de contêiner são constituídas de uma ou mais *camadas*, cada uma correspondendo a uma linha no Dockerfile que define a imagem. As imagens em um registro compartilham camadas comuns, aumentando a eficiência do armazenamento. Por exemplo, várias imagens em repositórios diferentes podem compartilhar a mesma camada base do Alpine Linux, mas apenas uma cópia dessa camada é armazenada no registro.

O compartilhamento de camada também otimiza a distribuição de camadas para nós com várias imagens compartilhando camadas comuns. Por exemplo, se uma imagem que já está em um nó incluir a camada Alpine Linux como base, a extração subsequente de uma imagem diferente que faz referência à mesma camada não transferirá a camada para o nó. Em vez disso, ele faz referência à camada já existente no nó.

Para fornecer isolamento seguro e proteção contra possíveis manipulações de camada, as camadas não são compartilhadas entre registros.

### <a name="manifest"></a>Manifesto

Cada imagem de contêiner ou artefato enviado por push para um registro de contêiner é associado a um *manifesto*. O manifesto, gerado pelo registro quando a imagem é enviada por push, identifica exclusivamente a imagem e especifica suas camadas. Você pode listar os manifestos para um repositório com o comando de CLI do Azure [AZ ACR Repository show-manifestas][az-acr-repository-show-manifests]:

```azurecli
az acr repository show-manifests --name <acrName> --repository <repositoryName>
```

Por exemplo, liste os manifestos para o repositório "ACR-HelloWorld":

```console
$ az acr repository show-manifests --name myregistry --repository acr-helloworld
[
  {
    "digest": "sha256:0a2e01852872580b2c2fea9380ff8d7b637d3928783c55beb3f21a6e58d5d108",
    "tags": [
      "latest",
      "v3"
    ],
    "timestamp": "2018-07-12T15:52:00.2075864Z"
  },
  {
    "digest": "sha256:3168a21b98836dda7eb7a846b3d735286e09a32b0aa2401773da518e7eba3b57",
    "tags": [
      "v2"
    ],
    "timestamp": "2018-07-12T15:50:53.5372468Z"
  },
  {
    "digest": "sha256:7ca0e0ae50c95155dbb0e380f37d7471e98d2232ed9e31eece9f9fb9078f2728",
    "tags": [
      "v1"
    ],
    "timestamp": "2018-07-11T21:38:35.9170967Z"
  }
]
```

### <a name="manifest-digest"></a>Resumo do manifesto

Os manifestos são identificados por um hash SHA-256 exclusivo ou um *Resumo de manifesto*. Cada imagem ou artefato – se marcado ou não, é identificado por seu resumo. O valor de Digest é exclusivo mesmo que os dados da camada da imagem sejam idênticos aos de outra imagem. Esse mecanismo é o que permite que você envie por push imagens marcadas de forma idêntica a um registro. Por exemplo, você pode enviar por `myimage:latest` Push repetidamente para o registro sem erro porque cada imagem é identificada por seu resumo exclusivo.

Você pode extrair uma imagem de um registro especificando seu resumo na operação de pull. Alguns sistemas podem ser configurados para efetuar pull por Resumo porque garantem a versão da imagem que está sendo puxada, mesmo se uma imagem com marca idêntica for subseqüentemente enviada ao registro.

Por exemplo, receba uma imagem do repositório "ACR-HelloWorld" pelo resumo do manifesto:

```console
$ docker pull myregistry.azurecr.io/acr-helloworld@sha256:0a2e01852872580b2c2fea9380ff8d7b637d3928783c55beb3f21a6e58d5d108
```

> [!IMPORTANT]
> Se você enviar por push imagens modificadas com marcas idênticas, poderá criar imagens órfãs – imagens não marcadas, mas ainda consumir espaço no registro. As imagens não marcadas não são mostradas no CLI do Azure ou no portal do Azure quando você lista ou exibe imagens por marca. No entanto, suas camadas ainda existem e consomem espaço no registro. A exclusão de uma imagem não marcada libera o espaço do registro quando o manifesto é o único, ou o último, apontando para uma camada específica. Para obter informações sobre como liberar espaço usado por imagens não marcadas, consulte [Excluir imagens de contêiner no registro de contêiner do Azure](container-registry-delete.md).

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre o [armazenamento de imagem](container-registry-storage.md) e os formatos de [conteúdo com suporte](container-registry-image-formats.md) no registro de contêiner do Azure.

<!-- LINKS - Internal -->
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az-acr-repository-show-manifests


