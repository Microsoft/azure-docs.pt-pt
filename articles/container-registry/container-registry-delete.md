---
title: Eliminar recursos de imagem no Azure Container Registry
description: Obter detalhes sobre como gerir eficazmente o tamanho do registo ao eliminar dados de imagem de contentor.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 01/04/2019
ms.author: danlep
ms.openlocfilehash: f3206da25a3c0727e3f9fe12190580a6c28c81a3
ms.sourcegitcommit: 1afd2e835dd507259cf7bb798b1b130adbb21840
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2019
ms.locfileid: "56983256"
---
# <a name="delete-container-images-in-azure-container-registry"></a>Eliminar imagens de contentor no Azure Container Registry

Para manter o tamanho do seu registo de contentor do Azure, deve eliminar periodicamente dados de imagem obsoletos. Embora algumas imagens de contentor implementadas em produção podem exigir armazenamento a longo prazo, outras pessoas podem ser eliminadas, mais rapidamente. Por exemplo, num cenário de teste e compilação automatizada, seu registo rapidamente pode preencher com imagens que nunca podem ser implementadas e podem ser removidas pouco tempo depois de concluir o passo de compilação e teste.

Uma vez que pode eliminar os dados de imagem de várias maneiras diferentes, é importante compreender como cada operação de eliminação afeta a utilização do armazenamento. Este artigo apresenta os componentes de imagens de registo e um contentor de Docker pela primeira vez, então abrange vários métodos para a eliminação de dados de imagem.

## <a name="registry"></a>Registo

Um contentor *Registro* é um serviço que armazena e distribui imagens de contentor. Hub do docker é um registo de contentor de Docker público, enquanto o Azure Container Registry fornece Docker privado registos de contentores no Azure.

## <a name="repository"></a>Repositório

Gerir registos de contentores *repositórios*, coleções de imagens de contentor com o mesmo nome, mas etiquetas diferentes. Por exemplo, as seguintes três imagens são no repositório "acr-helloworld":

```
acr-helloworld:latest
acr-helloworld:v1
acr-helloworld:v2
```

Também podem incluir os nomes de repositório [espaços de nomes](container-registry-best-practices.md#repository-namespaces). Espaços de nomes permitem agrupar imagens com nomes de encaminhamento de repositório delimitada por barra, por exemplo:

```
marketing/campaign10-18/web:v2
marketing/campaign10-18/api:v3
marketing/campaign10-18/email-sender:v2
product-returns/web-submission:20180604
product-returns/legacy-integrator:20180715
```

## <a name="components-of-an-image"></a>Componentes de uma imagem

Uma imagem de contentor dentro de um registo está associada uma ou mais etiquetas, tem um ou mais camadas e é identificada por um manifesto. Compreender como esses componentes se relacionam entre si pode ajudar a determinar o melhor método para libertar espaço no seu registo.

### <a name="tag"></a>Etiqueta

Uma imagem *marca* Especifica a versão. Uma única imagem dentro de um repositório pode ser atribuída uma ou várias etiquetas e também pode ser "". Ou seja, pode eliminar todas as etiquetas de uma imagem, enquanto os dados da imagem (seu camadas) permanecem no Registro.

O repositório (ou repositório e o espaço de nomes) e uma marca define o nome de uma imagem. Pode colocar e retirar uma imagem especificando seu nome na operação de push ou pull.

Um registo privado, como o Azure Container Registry, o nome da imagem também inclui o nome completamente qualificado do anfitrião de registo. O anfitrião de registo para imagens no ACR está no formato *acrname.azurecr.io*. Por exemplo, seria o nome completo da primeira imagem no espaço de nomes 'marketing' na secção anterior:

```
myregistry.azurecr.io/marketing/campaign10-18/web:v2
```

Para uma discussão sobre imagem identificando as práticas recomendadas, consulte o [marcação do Docker: Melhores práticas para etiquetagem e controlo de versões de imagens do docker] [ tagging-best-practices] postagem de blog no MSDN.

### <a name="layer"></a>Camada

Imagens são constituídas por um ou mais *camadas*, cada um correspondendo a uma linha no Dockerfile que define a imagem. Imagens num registo compartilham camadas comuns, aumentando a eficiência de armazenamento. Por exemplo, várias imagens em repositórios diferentes podem compartilhar a mesma camada de base de Alpine Linux, mas apenas uma cópia dessa camada é armazenada no Registro.

Também a partilha de camada otimiza a distribuição de camada para nós com várias imagens comuns camadas de partilha. Por exemplo, se uma imagem já num nó de incluir a camada de Alpine Linux como base, a solicitação subsequente de uma imagem diferente que referencia a mesma camada não transfere a camada para o nó. Em vez disso, ele faz referência a camada já existentes no nó.

### <a name="manifest"></a>Manifesto

Cada imagem de contentor enviada para um registo de contentor está associada um *manifesto*. O manifesto, gerados pelo registo quando a imagem é enviada por push, exclusivamente identifica a imagem e especifica seus camadas. Pode listar os manifestos para um repositório com o comando da CLI do Azure [az acr repositório show-manifestos][az-acr-repository-show-manifests]:

```azurecli
az acr repository show-manifests --name <acrName> --repository <repositoryName>
```

Por exemplo, o manifesto a listagem digests para o repositório "acr-helloworld":

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

O manifesto abordado aqui é diferente do manifesto de imagem, pode ver no portal do Azure ou com [inspecionar o manifesto de docker][docker-manifest-inspect]. Na secção seguinte, "Resumo de manifesto" refere-se para o resumo gerado pela operação de push, não a *config.digest* no manifesto de imagem. Pode obter e eliminar imagens da **digest manifesto**, não config.digest. A imagem seguinte ilustra os dois tipos de resumos de.

![Resumo de manifesto e config.digest no portal do Azure][manifest-digest]

### <a name="manifest-digest"></a>Resumo do manifesto

Os manifestos são identificados por um valor de hash SHA-256 exclusivo, ou *digest manifesto*. Cada imagem – se etiquetados ou não – é identificada pelo respetivo digest. O valor de texto implícita é exclusivo, mesmo que os dados de camada da imagem são idênticos ao que de outra imagem. Esse mecanismo é o que permite que envie repetidamente imagens de forma idêntica marcadas para um registo. Por exemplo, pode enviar repetidamente `myimage:latest` para o seu registo sem erro pois cada imagem é identificada pelo respetivo digest exclusivo.

Pode solicitar uma imagem a partir de um registo, especificando o resumo da operação de solicitação. Alguns sistemas podem ser configurados para extrair por digest, porque esta ação garante a versão da imagem sendo obtida, mesmo que uma imagem de forma idêntica marcada, em seguida, é enviada para o registo.

Por exemplo, extrair uma imagem do repositório "acr-helloworld" pelo resumo do manifesto:

```console
$ docker pull myregistry.azurecr.io/acr-helloworld@sha256:0a2e01852872580b2c2fea9380ff8d7b637d3928783c55beb3f21a6e58d5d108
```

> [!IMPORTANT]
> Se emitir repetidamente imagens modificadas com etiquetas idênticas, poderá criar imagens do órfãos imagens que estão não marcada, mas continuam a consumir espaço no seu registo. Não marcadas imagens não são apresentadas na CLI do Azure ou no portal do Azure ao listar ou ver imagens por etiqueta. No entanto, suas camadas ainda existem e consumam espaço no seu registo. O [eliminar imagens não marcadas](#delete-untagged-images) secção deste artigo descreve a liberar espaço utilizado por imagens não marcadas.

## <a name="delete-image-data"></a>Eliminar dados de imagem

Pode eliminar dados de imagem do seu registo de contentor de várias formas:

* Eliminar um [repositório](#delete-repository): Elimina todas as imagens e todas as camadas exclusivas dentro do repositório.
* Eliminar ao [marca](#delete-by-tag): Elimina uma imagem, a marca, todas as camadas exclusivas referenciadas por imagem e todas as outras marcas associadas à imagem.
* Eliminar ao [digest manifesto](#delete-by-manifest-digest): Elimina uma imagem, todas as camadas exclusivas referenciadas por imagem e todas as etiquetas associadas à imagem.

## <a name="delete-repository"></a>Eliminar repositório

Eliminar um repositório elimina todas as imagens no repositório, incluindo todas as etiquetas, camadas exclusivas e manifestos. Quando elimina um repositório, recuperar o espaço de armazenamento utilizado pelas imagens que se encontravam nesse repositório.

A seguinte da CLI do Azure comando elimina o repositório "acr-helloworld" e todas as etiquetas e manifestos de dentro do repositório. Se camadas referenciadas pelos manifestos eliminados não são referenciadas por quaisquer outras imagens no registo, os seus dados de camada também serão eliminados.

```azurecli
 az acr repository delete --name myregistry --repository acr-helloworld
```

## <a name="delete-by-tag"></a>Eliminar por etiqueta

Pode eliminar imagens individuais a partir de um repositório, especificando o nome do repositório e a etiqueta na operação de eliminação. Quando elimina por etiqueta, recuperar o espaço de armazenamento usado por qualquer camadas exclusivas na imagem (camadas não é partilhadas por quaisquer outras imagens no registo).

Para eliminar por etiqueta, utilize [eliminação de repositório az acr] [ az-acr-repository-delete] e especifique o nome da imagem no `--image` parâmetro. Todas as camadas exclusivas para a imagem e quaisquer outras marcas associadas à imagem são eliminados.

Por exemplo, a eliminar o "acr-helloworld:latest" imagem do registo "myregistry":

```azurecli
$ az acr repository delete --name myregistry --image acr-helloworld:latest
This operation will delete the manifest 'sha256:0a2e01852872580b2c2fea9380ff8d7b637d3928783c55beb3f21a6e58d5d108' and all the following images: 'acr-helloworld:latest', 'acr-helloworld:v3'.
Are you sure you want to continue? (y/n): y
```

> [!TIP]
> A eliminar *por etiqueta* não deve ser confundido com a eliminação de uma etiqueta (untagging). Pode eliminar uma etiqueta com o comando da CLI do Azure [untag de repositório az acr][az-acr-repository-untag]. Não existe espaço é liberado quando untag uma imagem, porque sua [manifesto](#manifest) e camada de dados permanecem no Registro. Apenas a referência de marca em si é eliminada.

## <a name="delete-by-manifest-digest"></a>Eliminar ao resumo do manifesto

R [digest manifesto](#manifest-digest) pode ser associado um, nenhum ou múltiplas etiquetas. Quando, para eliminar digest, referenciadas pelo manifesto de todas as etiquetas são eliminadas, conforme a camada de dados para qualquer camadas exclusivas para a imagem. Partilhado a camada de dados não são eliminados.

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

Em seguida, especifique o resumo do que pretende eliminar no [eliminação de repositório az acr] [ az-acr-repository-delete] comando. O formato do comando é:

```azurecli
az acr repository delete --name <acrName> --image <repositoryName>@<digest>
```

Por exemplo eliminar o manifesto do último listado no resultado anterior (com a etiqueta "v2"):

```console
$ az acr repository delete --name myregistry --image acr-helloworld@sha256:3168a21b98836dda7eb7a846b3d735286e09a32b0aa2401773da518e7eba3b57
This operation will delete the manifest 'sha256:3168a21b98836dda7eb7a846b3d735286e09a32b0aa2401773da518e7eba3b57' and all the following images: 'acr-helloworld:v2'.
Are you sure you want to continue? (y/n): y
```

O "acr-helloworld:v2" imagem será eliminada do Registro, conforme é qualquer camada de dados exclusiva para essa imagem. Se um manifesto é associado a vários tags, todas as marcas associadas também são eliminadas.

## <a name="delete-untagged-images"></a>Eliminar imagens não marcadas

Conforme mencionado na [digest manifesto](#manifest-digest) secção, envio de uma imagem modificada com uma etiqueta existente **untags** imagem anteriormente enviada por push, resultando numa imagem órfão (ou "dangling"). A imagem anteriormente enviada por push de manifesto – e seus dados de camada - permanece no Registro. Considere a seguinte sequência de eventos:

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

### <a name="list-untagged-images"></a>Listar imagens não marcadas

Pode listar todas as imagens não marcadas no seu repositório utilizando o seguinte comando da CLI do Azure. Substitua `<acrName>` e `<repositoryName>` com valores adequados para o seu ambiente.

```azurecli
az acr repository show-manifests --name <acrName> --repository <repositoryName> --query "[?tags[0]==null].digest"
```

### <a name="delete-all-untagged-images"></a>Eliminar todas as imagens não marcadas

Utilize os seguintes scripts de exemplo com cuidado – eliminado dados de imagem são UNRECOVERABLE.

**CLI do Azure no Bash**

O script de Bash seguinte elimina todas as imagens não marcadas a partir de um repositório. Ele requer que a CLI do Azure e **xargs**. Por predefinição, o script não realiza nenhuma exclusão. Alteração da `ENABLE_DELETE` valor a `true` para ativar a eliminação de imagem.

> [!WARNING]
> Se tiver de sistemas que solicitar imagens ao manifesto digest (em vez do nome da imagem), não deve executar este script. A eliminar imagens não marcadas irá impedir que esses sistemas extrair as imagens a partir do registo. Em vez de solicitar ao manifesto, considere adotar uma *marcação exclusivo* esquema, uma [recomendado melhor prática][tagging-best-practices].

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
    echo "No data deleted. Set ENABLE_DELETE=true to enable image deletion."
fi
```

**CLI do Azure no PowerShell**

O seguinte script do PowerShell elimina todas as imagens não marcadas a partir de um repositório. Ele requer o PowerShell e a CLI do Azure. Por predefinição, o script não realiza nenhuma exclusão. Alteração da `$enableDelete` valor a `$TRUE` para ativar a eliminação de imagem.

> [!WARNING]
> Se tiver de sistemas que solicitar imagens ao manifesto digest (em vez do nome da imagem), não deve executar este script. A eliminar imagens não marcadas irá impedir que esses sistemas extrair as imagens a partir do registo. Em vez de solicitar ao manifesto, considere adotar uma *marcação exclusivo* esquema, uma [recomendado melhor prática][tagging-best-practices].

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
    Write-Host "No data deleted. Set `$enableDelete = `$TRUE to enable image deletion."
}
```

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre o armazenamento de imagem no Azure Container Registry, veja [armazenamento de imagens de contentor no Azure Container Registry](container-registry-storage.md).

<!-- IMAGES -->
[manifest-digest]: ./media/container-registry-delete/01-manifest-digest.png

<!-- LINKS - External -->
[docker-manifest-inspect]: https://docs.docker.com/edge/engine/reference/commandline/manifest/#manifest-inspect
[portal]: https://portal.azure.com
[tagging-best-practices]: https://blogs.msdn.microsoft.com/stevelasker/2018/03/01/docker-tagging-best-practices-for-tagging-and-versioning-docker-images/

<!-- LINKS - Internal -->
[az-acr-repository-delete]: /cli/azure/acr/repository#az-acr-repository-delete
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az-acr-repository-show-manifests
[az-acr-repository-untag]: /cli/azure/acr/repository#az-acr-repository-untag
