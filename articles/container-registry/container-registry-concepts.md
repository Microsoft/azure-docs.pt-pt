---
title: Sobre registos, repositórios, imagens e artefactos
description: Introdução a conceitos-chave de registos de contentores Azure, repositórios, imagens de contentores e outros artefactos.
ms.topic: article
ms.date: 01/29/2021
ms.openlocfilehash: 991be79b10b6061f2034eb19e4e139af65aef3cf
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100578109"
---
# <a name="about-registries-repositories-and-artifacts"></a>Sobre registos, repositórios e artefactos

Este artigo introduz os conceitos-chave de registos de contentores, repositórios e imagens de contentores e artefactos relacionados. 

:::image type="content" source="media/container-registry-concepts/registry-elements.png" alt-text="Registo, repositórios e artefactos":::

## <a name="registry"></a>Registo

Um *registo de* contentores é um serviço que armazena e distribui imagens de contentores e artefactos relacionados. Docker Hub é um exemplo de um registo público de contentores que serve como um catálogo geral de imagens de contentores Docker. O Registo de Contentores Azure fornece aos utilizadores o controlo direto do seu conteúdo de contentores, com autenticação integrada, [geo-replicação](container-registry-geo-replication.md) suportando a distribuição global e fiabilidade para implementações de proximidade de rede, [configuração de rede virtual com Private Link,](container-registry-private-link.md) [bloqueio de etiquetas,](container-registry-image-lock.md)e muitas outras funcionalidades melhoradas. 

Além das imagens de contentores compatíveis com Docker, o Registo de Contentores Azure suporta uma gama de artefactos de conteúdo, incluindo [gráficos](container-registry-image-formats.md) helm e formatos de imagem Open Container Initiative (OCI).

## <a name="repository"></a>Repositório

Um *repositório* é uma coleção de imagens de contentores ou outros artefactos num registo com o mesmo nome, mas etiquetas diferentes. Por exemplo, as três imagens que se seguem estão no `acr-helloworld` repositório:

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

## <a name="artifact"></a>Artefacto

Uma imagem de recipiente ou outro artefacto dentro de um registo está associado a uma ou mais etiquetas, tem uma ou mais camadas, e é identificado por um manifesto. Compreender como estes componentes se relacionam entre si pode ajudá-lo a gerir eficazmente o seu registo.

### <a name="tag"></a>Etiqueta

A *etiqueta* para uma imagem ou outro artefacto especifica a sua versão. Um único artefacto dentro de um repositório pode ser atribuído uma ou muitas etiquetas, e também pode ser "não pontuado". Ou seja, pode eliminar todas as tags de uma imagem, enquanto os dados da imagem (suas camadas) permanecem no registo.

O repositório (ou repositório e espaço de nome) mais uma etiqueta define o nome de uma imagem. Pode empurrar e puxar uma imagem especificando o seu nome na operação push ou pull. A etiqueta `latest` é utilizada por defeito se não fornecer uma nos comandos do Docker.

A forma como identifica as imagens dos contentores é guiada pelos seus cenários para as desenvolver ou implantar. Por exemplo, são recomendadas etiquetas estáveis para manter as suas imagens base e etiquetas únicas para a implementação de imagens. Para obter mais informações, consulte [recomendações para a marcação e versão das imagens dos recipientes](container-registry-image-tag-version.md).

Para obter regras de identificação de etiquetas, consulte a documentação do [Docker.](https://docs.docker.com/engine/reference/commandline/tag/)

### <a name="layer"></a>Camada

As imagens e artefactos dos contentores são constituídos por uma ou mais *camadas.* Diferentes tipos de artefactos definem camadas de forma diferente. Por exemplo, numa imagem de recipiente Docker, cada camada corresponde a uma linha no Dockerfile que define a imagem:

:::image type="content" source="media/container-registry-concepts/container-image-layers.png" alt-text="Camadas de uma imagem de recipiente":::

Os artefactos de um registo partilham camadas comuns, aumentando a eficiência de armazenamento. Por exemplo, várias imagens em diferentes repositórios podem ter uma camada base ASP.NET comum, mas apenas uma cópia dessa camada é armazenada no registo. A partilha de camadas também otimiza a distribuição da camada aos nós, com vários artefactos que partilham camadas comuns. Se uma imagem já num nó incluir a camada core ASP.NET como base, a força subsequente de uma imagem diferente que referencia a mesma camada não transfere a camada para o nó. Em vez disso, refere a camada já existente no nó.

Para proporcionar isolamento e proteção seguro contra a manipulação potencial da camada, as camadas não são partilhadas entre registos.

### <a name="manifest"></a>Manifesto

Cada imagem ou artefacto de contentor empurrado para um registo de contentores está associado a um *manifesto*. O manifesto, gerado pelo registo quando o conteúdo é empurrado, identifica exclusivamente os artefactos e especifica as camadas. Pode listar os manifestos para um repositório com o comando Azure CLI [repositório de manifestos][az-acr-repository-show-manifests]. 

Um manifesto básico para uma imagem Linux `hello-world` é semelhante ao seguinte:

  ```json
  {
    "schemaVersion": 2,
    "mediaType": "application/vnd.docker.distribution.manifest.v2+json",
    "config": {
        "mediaType": "application/vnd.docker.container.image.v1+json",
        "size": 1510,
        "digest": "sha256:fbf289e99eb9bca977dae136fbe2a82b6b7d4c372474c9235adc1741675f587e"
      },
    "layers": [
        {
          "mediaType": "application/vnd.docker.image.rootfs.diff.tar.gzip",
          "size": 977,
          "digest": "sha256:2c930d010525941c1d56ec53b97bd057a67ae1865eebf042686d2a2d18271ced"
        }
      ]
  }
  ```

Pode listar os manifestos para um repositório com o comando Azure CLI [repositório de manifestos:][az-acr-repository-show-manifests]

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

Os manifestos são identificados por um haxixe SHA-256 único, ou *pela digestão manifesto.* Cada imagem ou artefacto- marcado ou não - é identificado pela sua digestão. O valor da digestão é único mesmo que os dados da camada do artefacto seja idêntico ao de outro artefacto. Este mecanismo é o que lhe permite empurrar repetidamente imagens marcadas de forma idêntica para um registo. Por exemplo, pode empurrar repetidamente `myimage:latest` para o seu registo sem erros porque cada imagem é identificada pela sua digestão única.

Pode retirar um artefacto de um registo especificando a sua digestão na operação de puxar. Alguns sistemas podem ser configurados para puxar por digestão porque garante que a versão de imagem está a ser puxada, mesmo que uma imagem marcada de forma idêntica seja empurrada mais tarde para o registo.

> [!IMPORTANT]
> Se empurrar repetidamente artefactos modificados com etiquetas idênticas, poderá criar "órfãos"-- artefactos que não são carregados, mas ainda consomem espaço no seu registo. Imagens não assinaladas não são mostradas no CLI Azure ou no portal Azure quando lista ou vê imagens por marcação. No entanto, as suas camadas ainda existem e consomem espaço no seu registo. Excluir uma imagem não assinala liberta espaço de registo quando o manifesto é o único, ou o último, apontando para uma camada particular. Para obter informações sobre a libertação do espaço utilizado por imagens não gravadas, consulte [apagar imagens de contentores no Registo de Contentores Azure](container-registry-delete.md).

## <a name="addressing-an-artifact"></a>Dirigindo-se a um artefacto

Para tratar de um artefacto de registo para operações de push e pull com Docker ou outras ferramentas de cliente, combine o nome de registo totalmente qualificado, o nome do repositório (incluindo o caminho do espaço de nome, se aplicável), e uma etiqueta de artefacto ou uma digestão manifesto. Consulte secções anteriores para obter explicações sobre estes termos.

  **Endereço por etiqueta:**`[loginServerUrl]/[repository][:tag]`
    
  **Endereço por digestão:**`[loginServerUrl]/[repository@sha256][:digest]`  

Quando utilizar o Docker ou outras ferramentas de cliente para puxar ou empurrar artefactos para um registo de contentores Azure, utilize o URL totalmente qualificado do registo, também chamado o nome do *servidor de login.* Na nuvem Azure, o URL totalmente qualificado de um registo de contentores Azure está no formato `myregistry.azurecr.io` (todos os minúsculos).

> [!NOTE]
> * Não é possível especificar um número de porta no URL do servidor de login do registo, tal como `myregistry.azurecr.io:443` . 
> * A etiqueta `latest` é utilizada por defeito se não fornecer uma etiqueta no seu comando.  

   
### <a name="push-by-tag"></a>Empurre por etiqueta

Exemplos: 

   `docker push myregistry.azurecr.io/samples/myimage:20210106`

   `docker push myregistry.azurecr.io/marketing/email-sender`

### <a name="pull-by-tag"></a>Puxe por etiqueta

Exemplo: 

  `docker pull myregistry.azurecr.io/marketing/campaign10-18/email-sender:v2`

### <a name="pull-by-manifest-digest"></a>Puxe por uma digestão manifesta


Exemplo:

  `docker pull myregistry.azurecr.io/acr-helloworld@sha256:0a2e01852872580b2c2fea9380ff8d7b637d3928783c55beb3f21a6e58d5d108`



## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [o armazenamento de registos](container-registry-storage.md) e [formatos de conteúdo suportado no](container-registry-image-formats.md) Registo de Contentores Azure.

Aprenda a [empurrar e a retirar imagens](container-registry-get-started-docker-cli.md) do Registo do Contentor Azure.

<!-- LINKS - Internal -->
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az-acr-repository-show-manifests


