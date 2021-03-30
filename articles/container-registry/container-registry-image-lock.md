---
title: Bloquear imagens
description: Descreva os atributos para uma imagem de recipiente ou repositório para que não possa ser eliminado ou substituído num registo de contentores Azure.
ms.topic: article
ms.date: 09/30/2019
ms.openlocfilehash: da84767523bb6d948b71b1c1ad2ddaffb628354a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "77659701"
---
# <a name="lock-a-container-image-in-an-azure-container-registry"></a>Bloqueie uma imagem de contentor num registo de contentores Azure

Num registo de contentores Azure, pode bloquear uma versão de imagem ou um repositório para que não possa ser eliminado ou atualizado. Para bloquear uma imagem ou um repositório, atualize os seus atributos utilizando a [atualização do repositório][az-acr-repository-update]do comando Azure CLI az acr . 

Este artigo requer que execute o CLI Azure em Azure Cloud Shell ou localmente (versão 2.0.55 ou posteriormente recomendado). Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)][azure-cli].

> [!IMPORTANT]
> Este artigo não se aplica ao bloqueio de um registo completo, por exemplo, utilizando **Definições > Fechaduras** no portal Azure, ou `az lock` comandos no CLI Azure. Bloquear um recurso de registo não o impede de criar, atualizar ou eliminar dados em repositórios. O bloqueio de um registo apenas afeta operações de gestão, tais como a adição ou eliminação de replicações, ou a eliminação do próprio registo. Mais informações sobre [os recursos de bloqueio para evitar alterações inesperadas](../azure-resource-manager/management/lock-resources.md).

## <a name="scenarios"></a>Cenários

Por predefinição, uma imagem marcada no Registo do Contentor de Azure é *mutável,* por isso, com permissões apropriadas, pode atualizar repetidamente e empurrar uma imagem com a mesma etiqueta para um registo. As imagens do contentor também podem ser [eliminadas](container-registry-delete.md) conforme necessário. Este comportamento é útil quando desenvolve imagens e precisa de manter um tamanho para o seu registo.

No entanto, quando colocar uma imagem de recipiente para a produção, poderá necessitar de uma imagem de recipiente *imutável.* Uma imagem imutável é aquela que não se pode apagar ou substituir acidentalmente.

Consulte [recomendações para a marcação e versão de imagens de contentores](container-registry-image-tag-version.md) para estratégias para etiquetar e versagens no seu registo.

Utilize o comando [de atualização do repositório az acr][az-acr-repository-update] para definir atributos de repositório para que possa:

* Bloqueie uma versão de imagem, ou um repositório inteiro

* Proteja uma versão de imagem ou repositório da eliminação, mas permita atualizações

* Evite operações de leitura (puxar) numa versão de imagem, ou num repositório inteiro

Consulte as seguintes secções por exemplo. 

## <a name="lock-an-image-or-repository"></a>Bloqueie uma imagem ou repositório 

### <a name="show-the-current-repository-attributes"></a>Mostrar os atributos atuais do repositório
Para ver os atributos atuais de um repositório, executar o seguinte comando [de repositório az acr:][az-acr-repository-show]

```azurecli
az acr repository show \
    --name myregistry --repository myrepo \
    --output jsonc
```

### <a name="show-the-current-image-attributes"></a>Mostrar os atributos de imagem atuais
Para ver os atributos atuais de uma etiqueta, executar o seguinte comando [de repositório az acr:][az-acr-repository-show]

```azurecli
az acr repository show \
    --name myregistry --image image:tag \
    --output jsonc
```

### <a name="lock-an-image-by-tag"></a>Bloqueie uma imagem por etiqueta

Para bloquear a imagem *myrepo/myimage:tag* na *minha liogistria,* executar o seguinte comando [de atualização do repositório az acr:][az-acr-repository-update]

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage:tag \
    --write-enabled false
```

### <a name="lock-an-image-by-manifest-digest"></a>Bloqueie uma imagem por uma leitura manifesta

Para bloquear uma imagem *myrepo/myimage* identificada pelo manifesto digest (hash SHA-256, representada como `sha256:...` ), executar o seguinte comando. (Para encontrar a reerção manifesta associada a uma ou mais etiquetas de imagem, executar o comando [az acr repository show-manifests.)][az-acr-repository-show-manifests]

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage@sha256:123456abcdefg \
    --write-enabled false
```

### <a name="lock-a-repository"></a>Bloqueie um repositório

Para bloquear o repositório *myrepo/myimage* e todas as imagens nele, executar o seguinte comando:

```azurecli
az acr repository update \
    --name myregistry --repository myrepo/myimage \
    --write-enabled false
```

## <a name="protect-an-image-or-repository-from-deletion"></a>Proteger uma imagem ou repositório da eliminação

### <a name="protect-an-image-from-deletion"></a>Proteger uma imagem da eliminação

Para permitir que a imagem *myrepo/myimage:tag* seja atualizada mas não eliminada, execute o seguinte comando:

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage:tag \
    --delete-enabled false --write-enabled true
```

### <a name="protect-a-repository-from-deletion"></a>Proteja um repositório da eliminação

O comando a seguir define o repositório *myrepo/myimage* para que não possa ser apagado. As imagens individuais ainda podem ser atualizadas ou eliminadas.

```azurecli
az acr repository update \
    --name myregistry --repository myrepo/myimage \
    --delete-enabled false --write-enabled true
```

## <a name="prevent-read-operations-on-an-image-or-repository"></a>Impedir a leitura de operações numa imagem ou repositório

Para evitar operações de leitura (puxar) na *imagem myrepo/myimage:tag,* executar o seguinte comando:

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage:tag \
    --read-enabled false
```

Para evitar a leitura de operações em todas as imagens do repositório *myrepo/myimage,* executar o seguinte comando:

```azurecli
az acr repository update \
    --name myregistry --repository myrepo/myimage \
    --read-enabled false
```

## <a name="unlock-an-image-or-repository"></a>Desbloquear uma imagem ou repositório

Para restaurar o comportamento predefinido da imagem *myrepo/myimage:tag* para que possa ser eliminada e atualizada, execute o seguinte comando:

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage:tag \
    --delete-enabled true --write-enabled true
```

Para restaurar o comportamento padrão do repositório *myrepo/myimage* e todas as imagens para que possam ser eliminadas e atualizadas, executar o seguinte comando:

```azurecli
az acr repository update \
    --name myregistry --repository myrepo/myimage \
    --delete-enabled true --write-enabled true
```

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu a usar o comando [de atualização do repositório az acr][az-acr-repository-update] para evitar a eliminação ou atualização de versões de imagem num repositório. Para definir atributos adicionais, consulte a referência de comando de [atualização de acr az acr.][az-acr-repository-update]

Para ver os atributos definidos para uma versão de imagem ou repositório, use o comando [de exibição de repositório az acr.][az-acr-repository-show]

Para obter mais informações sobre as operações de eliminação, consulte [eliminar as imagens dos contentores no Registo do Contentor de Azure][container-registry-delete].

<!-- LINKS - Internal -->
[az-acr-repository-update]: /cli/azure/acr/repository#az-acr-repository-update
[az-acr-repository-show]: /cli/azure/acr/repository#az-acr-repository-show
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az-acr-repository-show-manifests
[azure-cli]: /cli/azure/install-azure-cli
[container-registry-delete]: container-registry-delete.md

