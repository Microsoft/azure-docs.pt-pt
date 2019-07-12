---
title: Sobre os repositórios e imagens no Azure Container Registry
description: Introdução aos conceitos chave dos registos de contentores do Azure, os repositórios e imagens de contentor.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 07/01/2019
ms.author: danlep
ms.openlocfilehash: a14f0a2a86c5e4922fcddf3c92d48c6dfb1497a3
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67800331"
---
# <a name="about-registries-repositories-and-images"></a>Sobre os registos, repositórios e imagens

Este artigo apresenta os conceitos chave dos registos de contentores, repositórios e imagens de contentor e artefactos relacionados. 

## <a name="registry"></a>Registo

Um contentor *Registro* é um serviço que armazena e distribui imagens de contentor. Hub do docker é um registo de contentor público que oferece suporte a Comunidade de código-fonte aberto e serve como um catálogo geral de imagens. O Azure Container Registry fornece aos usuários controlar diretamente das suas imagens, com a autenticação integrada, [georreplicação](container-registry-geo-replication.md) suportar a distribuição global e fiabilidade para implementações de perto da rede, [ configuração de rede e de firewall virtual](container-registry-vnet.md), [marca bloqueio](container-registry-image-lock.md), e a muitas outras funcionalidades. 

Para além das imagens de contentor do Docker, relacionados com o Azure Container Registry suporta [artefactos de conteúdo](container-registry-image-formats.md) incluindo formatos de imagem da iniciativa de contentor aberto (OCI).

## <a name="content-addressable-elements-of-an-artifact"></a>Elementos de endereçáveis conteúdo de um artefacto

O endereço de um artefato num registo de contentor do Azure inclui os seguintes elementos. 

```
[loginUrl]/[namespace]/[artifact:][tag]
```

* **loginUrl** -o nome completamente qualificado do anfitrião de registo. O anfitrião de registro num Azure container registry está no formato *myregistry*. azurecr.io (em minúsculas). Tem de especificar o loginUrl ao utilizar o Docker ou outras ferramentas de cliente para os artefatos de extração ou push para um Azure container registry. 
* **espaço de nomes** - barra delimitada por agrupamento lógico de imagens relacionadas ou artefactos - por exemplo, para um grupo de trabalho ou aplicação
* **artefacto** -o nome de um repositório para uma imagem específica ou um artefacto
* **etiqueta** -uma versão específica de uma imagem ou armazenados num repositório de artefactos


Por exemplo, o nome completo de uma imagem num registo de contentor do Azure pode ter o seguinte aspeto:

```
myregistry.azurecr.io/marketing/campaign10-18/email-sender:v2
```

Veja as secções seguintes para obter detalhes sobre estes elementos.

## <a name="repository-name"></a>Nome do repositório

Gerir registos de contentores *repositórios*, coleções de imagens de contentor ou outros artefactos com o mesmo nome, mas etiquetas diferentes. Por exemplo, as seguintes três imagens são no repositório "acr-helloworld":

```
acr-helloworld:latest
acr-helloworld:v1
acr-helloworld:v2
```

Também podem incluir os nomes de repositório [espaços de nomes](container-registry-best-practices.md#repository-namespaces). Espaços de nomes permitem para imagens de grupo usando nomes de encaminhamento de repositório delimitada por barra, por exemplo:

```
marketing/campaign10-18/web:v2
marketing/campaign10-18/api:v3
marketing/campaign10-18/email-sender:v2
product-returns/web-submission:20180604
product-returns/legacy-integrator:20180715
```

## <a name="image"></a>Image

Uma imagem de contentor ou outros artefactos dentro de um registo estão associado uma ou mais etiquetas, tem um ou mais camadas e é identificado por um manifesto. Compreender como esses componentes se relacionam entre si pode ajudá-lo a gerir o seu registo de forma eficaz.

### <a name="tag"></a>Etiqueta

O *marca* para uma imagem ou outros artefactos Especifica a versão. Um artefato único dentro de um repositório pode ser atribuído uma ou várias etiquetas e também pode ser "". Ou seja, pode eliminar todas as etiquetas de uma imagem, enquanto os dados da imagem (seu camadas) permanecem no Registro.

O repositório (ou repositório e o espaço de nomes) e uma marca define o nome de uma imagem. Pode colocar e retirar uma imagem especificando seu nome na operação de push ou pull.

Como identificar o imagens de contentor é orientado por seus cenários para desenvolver ou implementá-las. Por exemplo, tags estáveis são recomendadas para manter suas imagens base e etiquetas exclusivas para a implementação de imagens. Para obter mais informações, consulte [recomendações para etiquetagem e controlo de versões de imagens de contentor](container-registry-image-tag-version.md).

### <a name="layer"></a>Camada

Imagens de contentor são constituídas por um ou mais *camadas*, cada um correspondendo a uma linha no Dockerfile que define a imagem. Imagens num registo compartilham camadas comuns, aumentando a eficiência de armazenamento. Por exemplo, várias imagens em repositórios diferentes podem compartilhar a mesma camada de base de Alpine Linux, mas apenas uma cópia dessa camada é armazenada no Registro.

Também a partilha de camada otimiza a distribuição de camada para nós com várias imagens comuns camadas de partilha. Por exemplo, se uma imagem já num nó de incluir a camada de Alpine Linux como base, a solicitação subsequente de uma imagem diferente que referencia a mesma camada não transfere a camada para o nó. Em vez disso, ele faz referência a camada já existentes no nó.

Para fornecer isolamento seguro e a proteção contra potenciais manipulação de camada, as camadas não são partilhadas entre registos.

### <a name="manifest"></a>Manifesto

Cada imagem de contentor ou o artefacto enviadas para um registo de contentor está associado um *manifesto*. O manifesto, gerados pelo registo quando a imagem é enviada por push, exclusivamente identifica a imagem e especifica seus camadas. Pode listar os manifestos para um repositório com o comando da CLI do Azure [az acr repositório show-manifestos][az-acr-repository-show-manifests]:

```azurecli
az acr repository show-manifests --name <acrName> --repository <repositoryName>
```

Por exemplo, o manifesto de lista digests para o repositório "acr-helloworld":

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

Os manifestos são identificados por um valor de hash SHA-256 exclusivo, ou *digest manifesto*. Cada imagem ou um artefacto – se etiquetados ou não – é identificado pelo respetivo digest. O valor de texto implícita é exclusivo, mesmo que os dados de camada da imagem são idênticos ao que de outra imagem. Esse mecanismo é o que permite que envie repetidamente imagens de forma idêntica marcadas para um registo. Por exemplo, pode enviar repetidamente `myimage:latest` para o seu registo sem erro pois cada imagem é identificada pelo respetivo digest exclusivo.

Pode solicitar uma imagem a partir de um registo, especificando o resumo da operação de solicitação. Alguns sistemas podem ser configurados para extrair por digest, porque esta ação garante a versão da imagem sendo obtida, mesmo que uma imagem de forma idêntica marcada, em seguida, é enviada para o registo.

Por exemplo, solicitar uma imagem do repositório "acr-helloworld" pelo resumo do manifesto:

```console
$ docker pull myregistry.azurecr.io/acr-helloworld@sha256:0a2e01852872580b2c2fea9380ff8d7b637d3928783c55beb3f21a6e58d5d108
```

> [!IMPORTANT]
> Se emitir repetidamente imagens modificadas com etiquetas idênticas, poderá criar imagens do órfãos imagens que estão não marcada, mas continuam a consumir espaço no seu registo. Não marcadas imagens não são apresentadas na CLI do Azure ou no portal do Azure ao listar ou ver imagens por etiqueta. No entanto, suas camadas ainda existem e consumam espaço no seu registo. Para obter informações sobre a liberar espaço utilizado por imagens não marcadas, consulte [eliminar imagens de contentor no Azure Container Registry](container-registry-delete.md).


## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre [armazenamento de imagens](container-registry-storage.md) e [formatos de conteúdo suportados](container-registry-image-formats.md) no Azure Container Registry.

<!-- LINKS - Internal -->
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az-acr-repository-show-manifests


