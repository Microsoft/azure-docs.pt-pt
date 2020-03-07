---
title: Sobre repositórios e imagens
description: Introdução a conceitos-chave de registos de contentores Azure, repositórios e imagens de contentores.
ms.topic: article
ms.date: 09/10/2019
ms.openlocfilehash: 9de0c344b226a0b13e76c7f02977ba3c91ba2d2a
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78362363"
---
# <a name="about-registries-repositories-and-images"></a>Sobre registos, repositórios e imagens

Este artigo introduz os conceitos-chave de registos de contentores, repositórios e imagens de contentores e artefactos relacionados. 

## <a name="registry"></a>Registo

Um *registo* de contentores é um serviço que armazena e distribui imagens de contentores. Docker Hub é um registo público de contentores que apoia a comunidade de código aberto e serve como um catálogo geral de imagens. O Registo de Contentores Azure fornece aos utilizadores o controlo direto das suas imagens, com autenticação integrada, [geo-replicação](container-registry-geo-replication.md) que suporta a distribuição e fiabilidade globais para implementações de proximidade de rede, configuração de [rede virtual e firewall,](container-registry-vnet.md)bloqueio de [etiquetas,](container-registry-image-lock.md)e muitas outras funcionalidades melhoradas. 

Além das imagens de contentores do Docker, o Registo de Contentores Azure suporta artefactos de [conteúdo relacionados,](container-registry-image-formats.md) incluindo formatos de imagem open container initiative (OCI).

## <a name="content-addressable-elements-of-an-artifact"></a>Elementos endereçáveis de conteúdo de um artefacto

O endereço de um artefacto num registo de contentores Azure inclui os seguintes elementos. 

```
[loginUrl]/[namespace]/[artifact:][tag]
```

* **loginUrl** - O nome totalmente qualificado do anfitrião do registo. O registo num registo de contentores Azure encontra-se no formato *myregistry*.azurecr.io (todos minúsculos). Deve especificar o loginUrl ao utilizar o Docker ou outras ferramentas de cliente para puxar ou empurrar artefactos para um registo de contentores Azure. 
* **espaço de nome** - Agrupamento lógico de slash-delimitado de imagens ou artefactos relacionados - por exemplo, para um grupo de trabalho ou app
* **artefacto** - O nome de um repositório para uma determinada imagem ou artefacto
* **etiqueta** - Uma versão específica de uma imagem ou artefacto armazenado num repositório


Por exemplo, o nome completo de uma imagem num registo de contentores Azure pode parecer:

```
myregistry.azurecr.io/marketing/campaign10-18/email-sender:v2
```

Consulte as seguintes secções para obter detalhes sobre estes elementos.

## <a name="repository-name"></a>Nome repositório

Os registos de contentores gerem *repositórios,* coleções de imagens de contentores ou outros artefactos com o mesmo nome, mas etiquetas diferentes. Por exemplo, as três seguintes imagens estão no repositório "acr-helloworld":

```
acr-helloworld:latest
acr-helloworld:v1
acr-helloworld:v2
```

Os nomes de repositórios também podem incluir espaços de [nome.](container-registry-best-practices.md#repository-namespaces) Os espaços de nome permitem-lhe agrupar imagens utilizando nomes de repositórios delimitados por corte, por exemplo:

```
marketing/campaign10-18/web:v2
marketing/campaign10-18/api:v3
marketing/campaign10-18/email-sender:v2
product-returns/web-submission:20180604
product-returns/legacy-integrator:20180715
```

## <a name="image"></a>Imagem

Uma imagem de recipiente ou outro artefacto dentro de um registo está associado a uma ou mais etiquetas, tem uma ou mais camadas, e é identificado por um manifesto. Compreender como estes componentes se relacionam uns com os outros pode ajudá-lo a gerir eficazmente o seu registo.

### <a name="tag"></a>Etiqueta

A *etiqueta* para uma imagem ou outro artefacto especifica a sua versão. Um único artefacto dentro de um repositório pode ser atribuído uma ou muitas etiquetas, e também pode ser "não marcado". Ou seja, pode eliminar todas as etiquetas de uma imagem, enquanto os dados da imagem (as suas camadas) permanecem no registo.

O repositório (ou repositório e espaço de nome) mais uma etiqueta define o nome de uma imagem. Pode empurrar e puxar uma imagem especificando o seu nome na operação de empurrar ou puxar.

A forma como marca as imagens do contentor é guiada pelos seus cenários para as desenvolver ou implementar. Por exemplo, são recomendadas etiquetas estáveis para manter as suas imagens base e etiquetas únicas para a implementação de imagens. Para mais informações, consulte [recomendações para marcar e verter imagens](container-registry-image-tag-version.md)de contentores .

### <a name="layer"></a>Camada

As imagens do recipiente são compostas por uma ou mais *camadas,* cada uma correspondendo a uma linha no Dockerfile que define a imagem. As imagens num registo partilham camadas comuns, aumentando a eficiência de armazenamento. Por exemplo, várias imagens em diferentes repositórios podem partilhar a mesma camada base alpine Linux, mas apenas uma cópia dessa camada é armazenada no registo.

A partilha de camadas também otimiza a distribuição da camada para os nódosos com múltiplas imagens que partilham camadas comuns. Por exemplo, se uma imagem já em um nó inclui a camada Alpine Linux como base, o puxão subsequente de uma imagem diferente referenciando a mesma camada não transfere a camada para o nó. Em vez disso, refere a camada já existente no nó.

Para proporcionar isolamento seguro e proteção contra a potencial manipulação da camada, as camadas não são partilhadas através dos registos.

### <a name="manifest"></a>Manifesto

Cada imagem ou artefacto do recipiente empurrado para um registo de contentores está associado a um *manifesto*. O manifesto, gerado pelo registo quando a imagem é empurrada, identifica exclusivamente a imagem e especifica as suas camadas. Pode enumerar os manifestos para um repositório com o comando Azure CLI [az repositório show-manifests:][az-acr-repository-show-manifests]

```azurecli
az acr repository show-manifests --name <acrName> --repository <repositoryName>
```

Por exemplo, enumerar os manifestos para o repositório "acr-helloworld":

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

### <a name="manifest-digest"></a>Manifesta digestão

Os manifestos são identificados por um hash SHA-256 único, ou *manifesta digestão.* Cada imagem ou artefacto- quer marcado ou não - é identificado pela sua digestão. O valor de digestão é único mesmo que os dados da camada da imagem sejam idênticos aos de outra imagem. Este mecanismo é o que lhe permite empurrar repetidamente imagens marcadas de forma idêntica para um registo. Por exemplo, pode empurrar repetidamente `myimage:latest` para o seu registo sem erros porque cada imagem é identificada pela sua digestão única.

Pode retirar uma imagem de um registo especificando a sua digestão na operação de puxar. Alguns sistemas podem ser configurados para puxar por digestão porque garante que a versão de imagem está a ser puxada, mesmo que uma imagem com etiqueta idêntica seja posteriormente empurrada para o registo.

Por exemplo, puxe uma imagem do repositório "acr-helloworld" através da digestão manifesta:

```console
$ docker pull myregistry.azurecr.io/acr-helloworld@sha256:0a2e01852872580b2c2fea9380ff8d7b637d3928783c55beb3f21a6e58d5d108
```

> [!IMPORTANT]
> Se empurrar repetidamente imagens modificadas com etiquetas idênticas, poderá criar imagens órfãs-- imagens que não estão marcadas, mas que ainda consomem espaço no seu registo. As imagens não marcadas não são mostradas no Azure CLI ou no portal Azure quando lista ou vê imagens por etiqueta. No entanto, as suas camadas ainda existem e consomem espaço no seu registo. A libertação de uma imagem não marcada liberta espaço de registo quando o manifesto é o único, ou o último, apontando para uma determinada camada. Para obter informações sobre a libertação do espaço utilizado por imagens não marcadas, consulte Apagar imagens de contentores no Registo de [Contentores de Azure](container-registry-delete.md).

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [o armazenamento de imagem](container-registry-storage.md) e [formatos de conteúdo suportado](container-registry-image-formats.md) no Registo de Contentores Azure.

<!-- LINKS - Internal -->
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az-acr-repository-show-manifests


