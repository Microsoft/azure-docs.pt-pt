---
title: Imagens multi-arquitetura no seu registo
description: Use o seu registo de contentores Azure para construir, importar, armazenar e implementar imagens multi-arquitetura (multi-arco)
ms.topic: article
ms.date: 02/07/2021
ms.custom: ''
ms.openlocfilehash: 520518a275e0a9d191ae770e560a0e6e8278eae2
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104802459"
---
# <a name="multi-architecture-images-in-your-azure-container-registry"></a>Imagens multi-arquitetura no seu registo de contentores Azure

Este artigo introduz imagens *multi-arquitetura* *(multi-arcos)* e como pode usar funcionalidades do Registo de Contentores Azure para ajudar a criar, armazenar e usá-las. 

Uma imagem multi-arco é um tipo de imagem de recipiente que pode combinar variantes para diferentes arquiteturas, e às vezes para diferentes sistemas operativos. Ao executar uma imagem com suporte multi-arquitetura, os clientes do contentor selecionarão automaticamente uma variante de imagem que corresponda ao seu SISTEMA e arquitetura.

## <a name="manifests-and-manifest-lists"></a>Manifestos e listas manifestas

As imagens multi-arco são baseadas em manifestos de imagem e listas manifestas.

### <a name="manifest"></a>Manifesto

Cada imagem de recipiente é representada por um [manifesto.](container-registry-concepts.md#manifest) Um manifesto é um ficheiro JSON que identifica exclusivamente a imagem, referindo as suas camadas e os seus tamanhos correspondentes. 

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
    
Pode ver um manifesto no Registo do Contentor de Azure utilizando o portal Azure ou ferramentas como o comando [az acr repositório de manifestos](/cli/azure/acr/repository#az_acr_repository_show_manifests) no CLI Azure.

### <a name="manifest-list"></a>Lista de manifestos

Uma *lista manifesta* para uma imagem multi-arco (conhecida mais genericamente como um índice de [imagem](https://github.com/opencontainers/image-spec/blob/master/image-index.md) para imagens OCI) é uma coleção (índice) de imagens, e cria-se uma especificando um ou mais nomes de imagem. Inclui detalhes sobre cada uma das imagens, como o so e arquitetura, tamanho e digestão manifesto. A lista de manifestos pode ser usada da mesma forma que um nome de imagem `docker pull` e `docker run` comandos. 

O `docker` CLI gere manifestos e listas manifestas usando o comando [manifesto do estivador.](https://docs.docker.com/engine/reference/commandline/manifest/)

> [!NOTE]
> Atualmente, o `docker manifest` comando e as subcomandantes são experimentais. Consulte a documentação do Docker para obter mais detalhes sobre a utilização de comandos experimentais.

Pode ver uma lista de manifestos usando o `docker manifest inspect` comando. Segue-se a saída para a imagem multi-arco, `mcr.microsoft.com/mcr/hello-world:latest` que tem três manifestos: dois para as arquiteturas Linux OS e um para uma arquitetura Windows.
```json
{
  "schemaVersion": 2,
  "mediaType": "application/vnd.docker.distribution.manifest.list.v2+json",
  "manifests": [
    {
      "mediaType": "application/vnd.docker.distribution.manifest.v2+json",
      "size": 524,
      "digest": "sha256:83c7f9c92844bbbb5d0a101b22f7c2a7949e40f8ea90c8b3bc396879d95e899a",
      "platform": {
        "architecture": "amd64",
        "os": "linux"
      }
    },
    {
      "mediaType": "application/vnd.docker.distribution.manifest.v2+json",
      "size": 525,
      "digest": "sha256:873612c5503f3f1674f315c67089dee577d8cc6afc18565e0b4183ae355fb343",
      "platform": {
        "architecture": "arm64",
        "os": "linux"
      }
    },
    {
      "mediaType": "application/vnd.docker.distribution.manifest.v2+json",
      "size": 1124,
      "digest": "sha256:b791ad98d505abb8c9618868fc43c74aa94d08f1d7afe37d19647c0030905cae",
      "platform": {
        "architecture": "amd64",
        "os": "windows",
        "os.version": "10.0.17763.1697"
      }
    }
  ]
}
```

Quando uma lista de manifestos multi-arcos é armazenada no Registo do Contentor de Azure, também pode ver a lista de manifestos utilizando o portal Azure ou com ferramentas como o comando [de manifestos de demonstração do repositório az acr.](/cli/azure/acr/repository#az_acr_repository_how_manifests)

## <a name="import-a-multi-arch-image"></a>Importe uma imagem multi-arco 

Uma imagem multi-arco existente pode ser importada para um registo de contentores Azure utilizando o comando [de importação az acr.](/cli/azure/acr#az_acr_import) A sintaxe de importação de imagem é a mesma que com uma imagem de arquitetura única. Como importar uma imagem de arquitetura única, importar uma imagem multi-arco não usa comandos Docker. 

Para mais informações, consulte [as imagens do contentor de importação para um registo de contentores.](container-registry-import-images.md)

## <a name="push-a-multi-arch-image"></a>Empurre uma imagem multi-arco

Quando tiver construído fluxos de trabalho para criar imagens de recipientes para diferentes arquiteturas, siga estes passos para empurrar uma imagem multi-arco para o seu registo de contentores Azure.

1. Marque e empurre cada imagem específica da arquitetura para o seu registo de contentores. O exemplo a seguir pressupõe duas arquiteturas Linux: arm64 e amd64. 

   ```console
   docker tag myimage:arm64 \
     myregistry.azurecr.io/multi-arch-samples/myimage:arm64

   docker push myregistry.azurecr.io/multi-arch-samples/myimage:arm64
 
   docker tag myimage:amd64 \
     myregistry.azurecr.io/multi-arch-samples/myimage:amd64

   docker push myregistry.azurecr.io/multi-arch-samples/myimage:amd64
   ``` 

1. Corra `docker manifest create` para criar uma lista manifesta para combinar as imagens anteriores numa imagem multi-arco.

   ```console
   docker manifest create myregistry.azurecr.io/multi-arch-samples/myimage:multi \
    myregistry.azurecr.io/multi-arch-samples/myimage:arm64 \
    myregistry.azurecr.io/multi-arch-samples/myimage:amd64
   ```

1. Empurre o manifesto para o registo do seu contentor `docker manifest push` utilizando:

   ```console
   docker manifest push myregistry.azurecr.io/multi-arch-samples/myimage:multi
   ```

1. Use o `docker manifest inspect` comando para ver a lista de manifestos. Um exemplo de saída de comando é mostrado numa secção anterior.

Depois de empurrar o manifesto multi-arco para o seu registo, trabalhe com a imagem multi-arco da mesma forma que faz com uma imagem de arquitetura única. Por exemplo, puxe a imagem usando `docker pull` , e use [comandos de repositório az acr](/cli/azure/acr/repository#az_acr_repository) para visualizar tags, manifestos e outras propriedades da imagem.

## <a name="build-and-push-a-multi-arch-image"></a>Construa e empurre uma imagem multi-arco

Utilizando funcionalidades de [Tarefas ACR,](container-registry-tasks-overview.md)pode construir e empurrar uma imagem multi-arco para o seu registo de contentores Azure. Por exemplo, defina uma tarefa em [várias etapas](container-registry-tasks-multi-step.md) num ficheiro YAML que constrói uma imagem de vários arcos Linux.

O exemplo a seguir pressupõe que tem Dockerfiles separados para duas arquiteturas, arm64 e amd64. Constrói e empurra as imagens específicas da arquitetura, depois cria e empurra um manifesto multi-arco que tem a `latest` etiqueta:

```yml
version: v1.1.0

steps:
- build: -t {{.Run.Registry}}/multi-arch-samples/myimage:{{.Run.ID}}-amd64 -f dockerfile.arm64 . 
- build: -t {{.Run.Registry}}/multi-arch-samples/myyimage:{{.Run.ID}}-arm64 -f dockerfile.amd64 . 
- push: 
    - {{.Run.Registry}}/multi-arch-samples/myimage:{{.Run.ID}}-arm64
    - {{.Run.Registry}}/multi-arch-samples/myimage:{{.Run.ID}}-amd64
- cmd: >
    docker manifest create
    {{.Run.Registry}}/multi-arch-samples/myimage:latest
    {{.Run.Registry}}/multi-arch-samples/myimage:{{.Run.ID}}-arm64
    {{.Run.Registry}}/multi-arch-samples/myimage:{{.Run.ID}}-amd64
- cmd: docker manifest push --purge {{.Run.Registry}}/multi-arch-samples/myimage:latest
- cmd: docker manifest inspect {{.Run.Registry}}/multi-arch-samples/myimage:latest
```

## <a name="next-steps"></a>Passos seguintes

* Use [gasodutos Azure](/azure/devops/pipelines/get-started/what-is-azure-pipelines) para construir imagens de contentores para diferentes arquiteturas.
* Saiba construir imagens multiplataformas utilizando o experimental Docker [buildx](https://docs.docker.com/buildx/working-with-buildx/) plug-in.

<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-windows]: https://docs.docker.com/docker-for-windows/
