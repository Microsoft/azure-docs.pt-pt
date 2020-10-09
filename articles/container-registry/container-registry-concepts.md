---
title: Sobre repositórios & imagens
description: Introdução a conceitos-chave de registos de contentores Azure, repositórios e imagens de contentores.
ms.topic: article
ms.date: 06/16/2020
ms.openlocfilehash: f3a3e2a00b4fb35f9e9dd1415d5c197aef0d39b0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85390453"
---
# <a name="about-registries-repositories-and-images"></a>Sobre registos, repositórios e imagens

Este artigo introduz os conceitos-chave de registos de contentores, repositórios e imagens de contentores e artefactos relacionados. 

## <a name="registry"></a>Registo

Um *registo de* contentores é um serviço que armazena e distribui imagens de contentores. Docker Hub é um registo público de contentores que suporta a comunidade de código aberto e serve como um catálogo geral de imagens. O Registo de Contentores Azure fornece aos utilizadores o controlo direto das suas imagens, com autenticação integrada, [geo-replicação](container-registry-geo-replication.md) suportando a distribuição global e fiabilidade para implementações de proximidade de rede, [configuração de rede virtual e firewall,](container-registry-vnet.md) [bloqueio de etiquetas,](container-registry-image-lock.md)e muitas outras funcionalidades melhoradas. 

Além das imagens do contentor Docker, o Registo de Contentores Azure suporta artefactos de [conteúdo relacionados,](container-registry-image-formats.md) incluindo formatos de imagem open container initiative (OCI).

## <a name="content-addressable-elements-of-an-artifact"></a>Elementos endereçantes de conteúdo de um artefacto

O endereço de um artefacto num registo de contentores Azure inclui os seguintes elementos. 

`[loginUrl]/[repository:][tag]`

* **loginUrl** - O nome totalmente qualificado do anfitrião do registo. O hospedeiro de registo num registo de contentores Azure encontra-se no formato *de miogitária*.azurecr.io (todas minúsculas). Tem de especificar o loginUrl ao utilizar o Docker ou outras ferramentas do cliente para puxar ou empurrar artefactos para um registo de contentores Azure. 
* **repositório** - Nome de um agrupamento lógico de uma ou mais imagens ou artefactos relacionados - por exemplo, as imagens para uma aplicação ou um sistema operativo base. Pode incluir o caminho *do espaço de nome.* 
* **tag** - Identificador de uma versão específica de uma imagem ou artefacto armazenado num repositório.

Por exemplo, o nome completo de uma imagem num registo de contentores Azure pode parecer:

*myregistry.azurecr.io/marketing/campaign10-18/email-sender:v2*

Consulte as seguintes secções para obter mais informações sobre estes elementos.

## <a name="repository-name"></a>Nome do repositório

Um *repositório* é uma coleção de imagens de contentores ou outros artefactos com o mesmo nome, mas etiquetas diferentes. Por exemplo, as três imagens que se seguem estão no repositório "acr-helloworld":


- *acr-helloworld:mais recente*
- *acr-helloworld:v1*
- *acr-helloworld:v2*

Os nomes do repositório também podem incluir [espaços de nome.](container-registry-best-practices.md#repository-namespaces) Os espaços de nome permitem identificar repositórios relacionados e propriedade de artefactos na sua organização, utilizando nomes delimitados para a frente. No entanto, o registo gere todos os repositórios de forma independente, não como uma hierarquia. Por exemplo:

- *marketing/campanha10-18/web:v2*
- *marketing/campanha10-18/api:v3*
- *marketing/campanha10-18/email-ender:v2*
- *devolução do produto/submissão da web:20180604*
- *produto-devoluções/legacy-integrador:20180715*

Os nomes do repositório só podem incluir caracteres alfanuméricos minúsculos, períodos, traços, sublinhados e cortes para a frente. 

Para obter regras completas de nomeação do repositório, consulte a [Especificação de Distribuição](https://github.com/docker/distribution/blob/master/docs/spec/api.md#overview)da Iniciativa de Recipiente Aberto .

## <a name="image"></a>Imagem

Uma imagem de recipiente ou outro artefacto dentro de um registo está associado a uma ou mais etiquetas, tem uma ou mais camadas, e é identificado por um manifesto. Compreender como estes componentes se relacionam entre si pode ajudá-lo a gerir eficazmente o seu registo.

### <a name="tag"></a>Etiqueta

A *etiqueta* para uma imagem ou outro artefacto especifica a sua versão. Um único artefacto dentro de um repositório pode ser atribuído uma ou muitas etiquetas, e também pode ser "não pontuado". Ou seja, pode eliminar todas as tags de uma imagem, enquanto os dados da imagem (suas camadas) permanecem no registo.

O repositório (ou repositório e espaço de nome) mais uma etiqueta define o nome de uma imagem. Pode empurrar e puxar uma imagem especificando o seu nome na operação push ou pull. A etiqueta `latest` é utilizada por defeito se não fornecer uma nos comandos do Docker.

A forma como identifica as imagens dos contentores é guiada pelos seus cenários para as desenvolver ou implantar. Por exemplo, são recomendadas etiquetas estáveis para manter as suas imagens base e etiquetas únicas para a implementação de imagens. Para obter mais informações, consulte [recomendações para a marcação e versão das imagens dos recipientes](container-registry-image-tag-version.md).

Para obter regras de identificação de etiquetas, consulte a documentação do [Docker.](https://docs.docker.com/engine/reference/commandline/tag/)

### <a name="layer"></a>Camada

As imagens de contentores são compostas por uma ou mais *camadas,* cada uma correspondente a uma linha no Dockerfile que define a imagem. As imagens num registo partilham camadas comuns, aumentando a eficiência de armazenamento. Por exemplo, várias imagens em diferentes repositórios podem partilhar a mesma camada base alpine Linux, mas apenas uma cópia dessa camada está armazenada no registo.

A partilha de camadas também otimiza a distribuição da camada aos nós com múltiplas imagens que partilham camadas comuns. Por exemplo, se uma imagem já num nó incluir a camada Alpine Linux como base, a atração subsequente de uma imagem diferente que referencia a mesma camada não transfere a camada para o nó. Em vez disso, refere a camada já existente no nó.

Para proporcionar isolamento e proteção seguro contra a manipulação potencial da camada, as camadas não são partilhadas entre registos.

### <a name="manifest"></a>Manifesto

Cada imagem ou artefacto de contentor empurrado para um registo de contentores está associado a um *manifesto*. O manifesto, gerado pelo registo quando a imagem é empurrada, identifica exclusivamente a imagem e especifica as suas camadas. Pode listar os manifestos para um repositório com o comando Azure CLI [repositório de manifestos:][az-acr-repository-show-manifests]

```azurecli
az acr repository show-manifests --name <acrName> --repository <repositoryName>
```

Por exemplo, listar os manifestos para o repositório "acr-helloworld":

```azurecli
az acr repository show-manifests --name myregistry --repository acr-helloworld
```

```output
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

### <a name="manifest-digest"></a>Digestão manifesta

Os manifestos são identificados por um haxixe SHA-256 único, ou *pela digestão manifesto.* Cada imagem ou artefacto- marcado ou não - é identificado pela sua digestão. O valor da digestão é único mesmo que os dados da camada da imagem seja idêntico ao de outra imagem. Este mecanismo é o que lhe permite empurrar repetidamente imagens marcadas de forma idêntica para um registo. Por exemplo, pode empurrar repetidamente `myimage:latest` para o seu registo sem erros porque cada imagem é identificada pela sua digestão única.

Pode extrair uma imagem de um registo especificando a sua digestão na operação de puxar. Alguns sistemas podem ser configurados para puxar por digestão porque garante que a versão de imagem está a ser puxada, mesmo que uma imagem marcada de forma idêntica seja posteriormente empurrada para o registo.

Por exemplo, retire uma imagem do repositório "acr-helloworld" por razão de digestão manifesto:

`docker pull myregistry.azurecr.io/acr-helloworld@sha256:0a2e01852872580b2c2fea9380ff8d7b637d3928783c55beb3f21a6e58d5d108`

> [!IMPORTANT]
> Se premir repetidamente imagens modificadas com etiquetas idênticas, poderá criar imagens órfãs-- imagens que não são gravadas, mas que ainda consomem espaço no seu registo. Imagens não assinaladas não são mostradas no CLI Azure ou no portal Azure quando lista ou vê imagens por marcação. No entanto, as suas camadas ainda existem e consomem espaço no seu registo. Excluir uma imagem não assinala liberta espaço de registo quando o manifesto é o único, ou o último, apontando para uma camada particular. Para obter informações sobre a libertação do espaço utilizado por imagens não gravadas, consulte [apagar imagens de contentores no Registo de Contentores Azure](container-registry-delete.md).

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [armazenamento de imagem](container-registry-storage.md) e [formatos de conteúdo suportado no](container-registry-image-formats.md) Registo de Contentores Azure.

<!-- LINKS - Internal -->
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az-acr-repository-show-manifests


