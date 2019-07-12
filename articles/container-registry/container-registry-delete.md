---
title: Eliminar recursos de imagem no Azure Container Registry
description: Obter detalhes sobre como gerir eficazmente o tamanho do registo ao eliminar dados de imagem de contentor.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 06/17/2019
ms.author: danlep
ms.openlocfilehash: c603afa61499a615a0882cef06f14fd3d080a9ef
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67797771"
---
# <a name="delete-container-images-in-azure-container-registry"></a>Eliminar imagens de contentor no Azure Container Registry

Para manter o tamanho do seu registo de contentor do Azure, deve eliminar periodicamente dados de imagem obsoletos. Embora algumas imagens de contentor implementadas em produção podem exigir armazenamento a longo prazo, outras pessoas podem ser eliminadas, mais rapidamente. Por exemplo, num cenário de teste e compilação automatizada, seu registo rapidamente pode preencher com imagens que nunca podem ser implementadas e podem ser removidas pouco tempo depois de concluir o passo de compilação e teste.

Uma vez que pode eliminar os dados de imagem de várias maneiras diferentes, é importante compreender como cada operação de eliminação afeta a utilização do armazenamento. Este artigo abrange vários métodos para a eliminação de dados de imagem:

* Eliminar um [repositório](#delete-repository): Elimina todas as imagens e todas as camadas exclusivas dentro do repositório.
* Eliminar ao [marca](#delete-by-tag): Elimina uma imagem, a marca, todas as camadas exclusivas referenciadas por imagem e todas as outras marcas associadas à imagem.
* Eliminar ao [digest manifesto](#delete-by-manifest-digest): Elimina uma imagem, todas as camadas exclusivas referenciadas por imagem e todas as etiquetas associadas à imagem.

Scripts de exemplo são fornecidos para o ajudar a automatizar as operações de eliminação.

Para obter uma introdução para esses conceitos, veja [sobre os registos, imagens e repositórios](container-registry-concepts.md).

## <a name="delete-repository"></a>Eliminar repositório

Eliminar um repositório elimina todas as imagens no repositório, incluindo todas as etiquetas, camadas exclusivas e manifestos. Quando elimina um repositório, recuperar o espaço de armazenamento utilizado pelas imagens que fazem referência a camadas exclusivas nesse repositório.

A seguinte da CLI do Azure comando elimina o repositório "acr-helloworld" e todas as etiquetas e manifestos de dentro do repositório. Se a camadas referenciadas pelos manifestos eliminados não são referenciadas por quaisquer outras imagens no registo, os dados de camada é também eliminados, recuperar o espaço de armazenamento.

```azurecli
 az acr repository delete --name myregistry --repository acr-helloworld
```

## <a name="delete-by-tag"></a>Eliminar por etiqueta

Pode eliminar imagens individuais a partir de um repositório, especificando o nome do repositório e a etiqueta na operação de eliminação. Quando elimina por etiqueta, recuperar o espaço de armazenamento usado por qualquer camadas exclusivas na imagem (camadas não é partilhadas por quaisquer outras imagens no registo).

Para eliminar por etiqueta, utilize [eliminação de repositório az acr][az-acr-repository-delete] e especifique o nome da imagem no `--image` parâmetro. Todas as camadas exclusivas para a imagem e quaisquer outras marcas associadas à imagem são eliminados.

Por exemplo, a eliminar o "acr-helloworld:latest" imagem do registo "myregistry":

```azurecli
$ az acr repository delete --name myregistry --image acr-helloworld:latest
This operation will delete the manifest 'sha256:0a2e01852872580b2c2fea9380ff8d7b637d3928783c55beb3f21a6e58d5d108' and all the following images: 'acr-helloworld:latest', 'acr-helloworld:v3'.
Are you sure you want to continue? (y/n): y
```

> [!TIP]
> A eliminar *por etiqueta* não deve ser confundido com a eliminação de uma etiqueta (untagging). Pode eliminar uma etiqueta com o comando da CLI do Azure [untag de repositório az acr][az-acr-repository-untag]. Não existe espaço é liberado quando untag uma imagem, porque sua [manifesto](container-registry-concepts.md#manifest) e os dados de camada permanecem no Registro. Apenas a referência de marca em si é eliminada.

## <a name="delete-by-manifest-digest"></a>Eliminar ao resumo do manifesto

R [digest manifesto](container-registry-concepts.md#manifest-digest) pode ser associado um, nenhum ou múltiplas etiquetas. Quando, para eliminar digest, referenciadas pelo manifesto de todas as etiquetas são eliminadas, conforme a camada de dados para qualquer camadas exclusivas para a imagem. Partilhado a camada de dados não são eliminados.

A eliminar pelo resumo, a primeira lista o manifesto digests para o repositório que contém as imagens que pretende eliminar. Por exemplo:

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
  }
]
```

Em seguida, especifique o resumo do que pretende eliminar no [eliminação de repositório az acr][az-acr-repository-delete] comando. O formato do comando é:

```azurecli
az acr repository delete --name <acrName> --image <repositoryName>@<digest>
```

Por exemplo eliminar o manifesto do último listado no resultado anterior (com a etiqueta "v2"):

```console
$ az acr repository delete --name myregistry --image acr-helloworld@sha256:3168a21b98836dda7eb7a846b3d735286e09a32b0aa2401773da518e7eba3b57
This operation will delete the manifest 'sha256:3168a21b98836dda7eb7a846b3d735286e09a32b0aa2401773da518e7eba3b57' and all the following images: 'acr-helloworld:v2'.
Are you sure you want to continue? (y/n): y
```

O `acr-helloworld:v2` imagem será eliminada do Registro, conforme é qualquer camada de dados exclusiva para essa imagem. Se um manifesto é associado a vários tags, todas as marcas associadas também são eliminadas.

## <a name="delete-digests-by-timestamp"></a>Eliminar resumos ao timestamp

Para manter o tamanho de um repositório ou de registo, poderá ter de eliminar periodicamente resumos de manifestos com mais de uma determinada data.

O seguinte comando da CLI do Azure apresenta uma lista de todos os digest manifesto num repositório com mais de um período de tempo especificado por ordem ascendente. Substitua `<acrName>` e `<repositoryName>` com valores adequados para o seu ambiente. O carimbo de hora pode ser uma expressão de data / hora completa ou uma data, tal como neste exemplo.

```azurecli
az acr repository show-manifests --name <acrName> --repository <repositoryName> \
--orderby time_asc -o tsv --query "[?timestamp < '2019-04-05'].[digest, timestamp]"
```

Depois de identificar os resumos de manifestos obsoletos, pode executar o script de Bash seguinte para eliminar os resumos de manifestos com mais de um carimbo especificado. Ele requer que a CLI do Azure e **xargs**. Por predefinição, o script não realiza nenhuma exclusão. Alteração da `ENABLE_DELETE` valor a `true` para ativar a eliminação de imagem.

> [!WARNING]
> Utilize o seguinte script de exemplo com cuidado, dados de imagem eliminada é UNRECOVERABLE. Se tiver de sistemas que solicitar imagens ao manifesto digest (em vez do nome da imagem), não deve executar esses scripts. A eliminar os resumos de manifestos irá impedir que esses sistemas incluem a solicitação as imagens a partir do registo. Em vez de solicitar ao manifesto, considere adotar uma *marcação exclusivo* esquema, uma [recomendado melhor prática][tagging-best-practices]. 

```bash
#!/bin/bash

# WARNING! This script deletes data!
# Run only if you do not have systems
# that pull images via manifest digest.

# Change to 'true' to enable image delete
ENABLE_DELETE=false

# Modify for your environment
# TIMESTAMP can be a date-time string such as 2019-03-15T17:55:00.
REGISTRY=myregistry
REPOSITORY=myrepository
TIMESTAMP=2019-04-05  

# Delete all images older than specified timestamp.

if [ "$ENABLE_DELETE" = true ]
then
    az acr repository show-manifests --name $REGISTRY --repository $REPOSITORY \
    --orderby time_asc --query "[?timestamp < '$TIMESTAMP'].digest" -o tsv \
    | xargs -I% az acr repository delete --name $REGISTRY --image $REPOSITORY@% --yes
else
    echo "No data deleted."
    echo "Set ENABLE_DELETE=true to enable deletion of these images in $REPOSITORY:"
    az acr repository show-manifests --name $REGISTRY --repository $REPOSITORY \
   --orderby time_asc --query "[?timestamp < '$TIMESTAMP'].[digest, timestamp]" -o tsv
fi
```

## <a name="delete-untagged-images"></a>Eliminar imagens não marcadas

Conforme mencionado na [digest manifesto](container-registry-concepts.md#manifest-digest) secção, envio de uma imagem modificada com uma etiqueta existente **untags** imagem anteriormente enviada por push, resultando numa imagem órfão (ou "dangling"). A imagem anteriormente enviada por push de manifesto – e seus dados de camada - permanece no Registro. Considere a seguinte sequência de eventos:

1. Enviar imagem *acr-helloworld* com etiqueta **mais recente**: `docker push myregistry.azurecr.io/acr-helloworld:latest`
1. Verifique os manifestos para o repositório *acr-helloworld*:

   ```console
   $ az acr repository show-manifests --name myregistry --repository acr-helloworld
   [
     {
       "digest": "sha256:d2bdc0c22d78cde155f53b4092111d7e13fe28ebf87a945f94b19c248000ceec",
       "tags": [
         "latest"
       ],
       "timestamp": "2018-07-11T21:32:21.1400513Z"
     }
   ]
   ```

1. Modificar *acr-helloworld* Dockerfile
1. Enviar imagem *acr-helloworld* com etiqueta **mais recente**: `docker push myregistry.azurecr.io/acr-helloworld:latest`
1. Verifique os manifestos para o repositório *acr-helloworld*:

   ```console
   $ az acr repository show-manifests --name myregistry --repository acr-helloworld
   [
     {
       "digest": "sha256:7ca0e0ae50c95155dbb0e380f37d7471e98d2232ed9e31eece9f9fb9078f2728",
       "tags": [
         "latest"
       ],
       "timestamp": "2018-07-11T21:38:35.9170967Z"
     },
     {
       "digest": "sha256:d2bdc0c22d78cde155f53b4092111d7e13fe28ebf87a945f94b19c248000ceec",
       "tags": [],
       "timestamp": "2018-07-11T21:32:21.1400513Z"
     }
   ]
   ```

Como pode ver na saída do último passo na sequência, existe agora uma órfãos manifesto cujos `"tags"` propriedade é uma lista vazia. Esse manifesto ainda existe no Registro, juntamente com quaisquer dados de camada exclusivo por ele referenciados. **Para eliminar tais órfãos imagens e os seus dados de camada, tem de eliminar por digest manifesto**.

## <a name="delete-all-untagged-images"></a>Eliminar todas as imagens não marcadas

Pode listar todas as imagens não marcadas no seu repositório utilizando o seguinte comando da CLI do Azure. Substitua `<acrName>` e `<repositoryName>` com valores adequados para o seu ambiente.

```azurecli
az acr repository show-manifests --name <acrName> --repository <repositoryName> --query "[?tags[0]==null].digest"
```

Utilizar este comando num script, pode eliminar todas as imagens não marcadas num repositório.

> [!WARNING]
> Utilize os seguintes scripts de exemplo com cuidado – eliminado dados de imagem são UNRECOVERABLE. Se tiver de sistemas que solicitar imagens ao manifesto digest (em vez do nome da imagem), não deve executar esses scripts. A eliminar imagens não marcadas irá impedir que esses sistemas extrair as imagens a partir do registo. Em vez de solicitar ao manifesto, considere adotar uma *marcação exclusivo* esquema, uma [recomendado melhor prática][tagging-best-practices].

**CLI do Azure no Bash**

O script de Bash seguinte elimina todas as imagens não marcadas a partir de um repositório. Ele requer que a CLI do Azure e **xargs**. Por predefinição, o script não realiza nenhuma exclusão. Alteração da `ENABLE_DELETE` valor a `true` para ativar a eliminação de imagem.

```bash
#!/bin/bash

# WARNING! This script deletes data!
# Run only if you do not have systems
# that pull images via manifest digest.

# Change to 'true' to enable image delete
ENABLE_DELETE=false

# Modify for your environment
REGISTRY=myregistry
REPOSITORY=myrepository

# Delete all untagged (orphaned) images
if [ "$ENABLE_DELETE" = true ]
then
    az acr repository show-manifests --name $REGISTRY --repository $REPOSITORY  --query "[?tags[0]==null].digest" -o tsv \
    | xargs -I% az acr repository delete --name $REGISTRY --image $REPOSITORY@% --yes
else
    else
    echo "No data deleted."
    echo "Set ENABLE_DELETE=true to enable image deletion of these images in $REPOSITORY:"
    az acr repository show-manifests --name $REGISTRY --repository $REPOSITORY --query "[?tags[0]==null]" -o tsv
fi
```

**CLI do Azure no PowerShell**

O seguinte script do PowerShell elimina todas as imagens não marcadas a partir de um repositório. Ele requer o PowerShell e a CLI do Azure. Por predefinição, o script não realiza nenhuma exclusão. Alteração da `$enableDelete` valor a `$TRUE` para ativar a eliminação de imagem.

```powershell
# WARNING! This script deletes data!
# Run only if you do not have systems
# that pull images via manifest digest.

# Change to '$TRUE' to enable image delete
$enableDelete = $FALSE

# Modify for your environment
$registry = "myregistry"
$repository = "myrepository"

if ($enableDelete) {
    az acr repository show-manifests --name $registry --repository $repository --query "[?tags[0]==null].digest" -o tsv `
    | %{ az acr repository delete --name $registry --image $repository@$_ --yes }
} else {
    Write-Host "No data deleted."
    Write-Host "Set `$enableDelete = `$TRUE to enable image deletion."
    az acr repository show-manifests --name $registry --repository $repository --query "[?tags[0]==null]" -o tsv
}
```

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre o armazenamento de imagem no Azure Container Registry, veja [armazenamento de imagens de contentor no Azure Container Registry](container-registry-storage.md).

<!-- IMAGES -->
[manifest-digest]: ./media/container-registry-delete/01-manifest-digest.png

<!-- LINKS - External -->
[docker-manifest-inspect]: https://docs.docker.com/edge/engine/reference/commandline/manifest/#manifest-inspect
[portal]: https://portal.azure.com
[tagging-best-practices]: https://stevelasker.blog/2018/03/01/docker-tagging-best-practices-for-tagging-and-versioning-docker-images/

<!-- LINKS - Internal -->
[az-acr-repository-delete]: /cli/azure/acr/repository#az-acr-repository-delete
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az-acr-repository-show-manifests
[az-acr-repository-untag]: /cli/azure/acr/repository#az-acr-repository-untag
