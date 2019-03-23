---
title: Bloquear uma imagem no Azure Container Registry
description: Definir atributos de uma imagem de contentor ou um repositório para que não pode ser eliminado ou substituído num Azure container registry.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 02/19/2019
ms.author: danlep
ms.openlocfilehash: ebbfaba158e7ddb669111f097eb1adde2373aa6c
ms.sourcegitcommit: 223604d8b6ef20a8c115ff877981ce22ada6155a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2019
ms.locfileid: "58361290"
---
# <a name="lock-a-container-image-in-an-azure-container-registry"></a>Bloquear uma imagem de contentor num registo de contentor do Azure

Num registo de contentor do Azure, pode bloquear uma versão de imagem ou um repositório, para que não pode ser eliminado ou atualizado. Para bloquear uma imagem ou um repositório, atualize seus atributos com o comando da CLI do Azure [atualização do repositório az acr][az-acr-repository-update]. 

Este artigo requer que execute a CLI do Azure no Azure Cloud Shell ou localmente (versão 2.0.55 ou recomendados mais tarde). Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)][azure-cli].

## <a name="scenarios"></a>Cenários

Por predefinição, é uma imagem etiquetada no Azure Container Registry *mutáveis*, por isso, com as permissões apropriadas pode atualizar e enviar uma imagem com a mesma etiqueta a um registo repetidamente. Também podem ser a imagens de contentor [eliminado](container-registry-delete.md) conforme necessário. Este comportamento é útil ao desenvolver imagens e tem de manter um tamanho para o seu registo.

No entanto, quando implementa uma imagem de contentor para a produção, poderá ter uma *imutável* imagem de contentor. Uma imutável imagem é aquele que é possível excluir ou substituir acidentalmente. Utilize o [atualização do repositório az acr] [ az-acr-repository-update] comando para definir atributos de repository para que possa:

* Bloquear uma versão de imagem ou um repositório de inteiro

* Proteger uma versão da imagem ou um repositório de eliminação, mas permitir atualizações

* Impedir operações de leitura (solicitação) numa versão de imagem ou um repositório de inteiro

Veja as secções seguintes para obter exemplos.

## <a name="lock-an-image-or-repository"></a>Uma imagem ou um repositório de bloqueio 

### <a name="show-the-current-repository-attributes"></a>Mostrar os atributos de repositório atual
Para ver os atributos atuais de um repositório, execute o seguinte procedimento [show de repositório az acr] [ az-acr-repository-show] comando:

```azurecli
az acr repository show \
    --name myregistry --repository myrepo
    --output jsonc
```

### <a name="show-the-current-image-attributes"></a>Mostrar os atributos de imagem atual
Para ver os atributos atuais de uma etiqueta, execute o seguinte procedimento [show de repositório az acr] [ az-acr-repository-show] comando:

```azurecli
az acr repository show \
    --name myregistry --image image:tag \
    --output jsonc
```

### <a name="lock-an-image-by-tag"></a>Bloquear uma imagem por etiqueta

Para bloquear o *myrepo / myimage:tag* da imagem no *myregistry*, execute o seguinte [atualização do repositório az acr] [ az-acr-repository-update] comando:

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage:tag \
    --write-enabled false
```

### <a name="lock-an-image-by-manifest-digest"></a>Bloquear uma imagem por digest manifesto

Para o bloqueio de um *myrepo/myimage* imagem identificada pelo resumo de manifesto (hash de SHA-256, representado como `sha256:...`), execute o seguinte comando. (Para localizar o resumo de manifesto associado a uma ou mais etiquetas de imagem, execute o [az acr repositório show-manifestos] [ az-acr-repository-show-manifests] comando.)

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage@sha256:123456abcdefg \
    --write-enabled false
```

### <a name="lock-a-repository"></a>Um repositório de bloqueio

Para bloquear o *myrepo/myimage* repositório e todas as imagens no mesmo, execute o seguinte comando:

```azurecli
az acr repository update \
    --name myregistry --repository myrepo/myimage \
    --write-enabled false
```

## <a name="protect-an-image-or-repository-from-deletion"></a>Proteger uma imagem ou um repositório de eliminação

### <a name="protect-an-image-from-deletion"></a>Proteger uma imagem de eliminação

Para permitir que o *myrepo / myimage:tag* imagem a ser atualizado, mas não eliminar, execute o seguinte comando:

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage:tag \
    --delete-enabled false --write-enabled true
```

### <a name="protect-a-repository-from-deletion"></a>Proteger um repositório de eliminação

O seguinte comando define a *myrepo/myimage* repositório, para que ele não pode ser eliminado. Imagens individuais ainda podem ser atualizadas ou eliminadas.

```azurecli
az acr repository update \
    --name myregistry --repository myrepo/myimage \
    --delete-enabled false --write-enabled true
```

## <a name="prevent-read-operations-on-an-image-or-repository"></a>Impedir operações de leitura numa imagem ou um repositório

Para impedir que as operações de leitura (solicitação) sobre o *myrepo / myimage:tag* imagem, execute o seguinte comando:

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage:tag \
    --read-enabled false
```

Para evitar operações de leitura em todas as imagens no *myrepo/myimage* repositório, execute o seguinte comando:

```azurecli
az acr repository update \
    --name myregistry --repository myrepo/myimage \
    --read-enabled false
```

## <a name="unlock-an-image-or-repository"></a>Desbloquear uma imagem ou um repositório

Para restaurar o comportamento padrão dos *myrepo / myimage:tag* de imagem para que possa ser eliminado e atualizada, execute o seguinte comando:

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage:tag \
    --delete-enabled true --write-enabled true
```

Para restaurar o comportamento padrão dos *myrepo/myimage* repositório e todas as imagens para que possa ser eliminados e atualizadas, execute o seguinte comando:

```azurecli
az acr repository update \
    --name myregistry --repository myrepo/myimage \
    --delete-enabled true --write-enabled true
```

## <a name="next-steps"></a>Passos Seguintes

Neste artigo, aprendeu sobre como utilizar o [atualização do repositório az acr] [ az-acr-repository-update] comando para impedir a eliminação ou atualização de versões de imagem num repositório. Para definir atributos adicionais, consulte a [atualização do repositório az acr] [ az-acr-repository-update] referência do comando.

Para ver os atributos definidos para uma versão da imagem ou um repositório, utilize o [show de repositório az acr] [ az-acr-repository-show] comando.

Para obter detalhes sobre as operações de eliminação, veja [eliminar imagens de contentor no Azure Container Registry][container-registry-delete].

<!-- LINKS - Internal -->
[az-acr-repository-update]: /cli/azure/acr/repository#az-acr-repository-update
[az-acr-repository-show]: /cli/azure/acr/repository#az-acr-repository-show
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az-acr-repository-show-manifests
[azure-cli]: /cli/azure/install-azure-cli
[container-registry-delete]: container-registry-delete.md

