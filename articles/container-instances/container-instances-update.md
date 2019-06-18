---
title: Atualizar os contentores no Azure Container Instances
description: Saiba como atualizar os contentores em execução nos seus grupos de contentor do Azure Container Instances.
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: article
ms.date: 08/01/2018
ms.author: danlep
ms.openlocfilehash: 2df6a2724cbdcd6bbb6c6ca6636256b7e399da8e
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60686896"
---
# <a name="update-containers-in-azure-container-instances"></a>Atualizar os contentores no Azure Container Instances

Durante o funcionamento normal das suas instâncias de contentor, pode ser necessário atualizar os contentores num grupo de contentor. Por exemplo, pode desejar atualizar a versão de imagem, alterar um nome DNS, Atualize as variáveis de ambiente ou Atualize o estado de um contentor cuja aplicação teve uma falha.

## <a name="update-a-container-group"></a>Atualizar um grupo de contentores

Atualize os contentores num grupo de contentor, voltar a implementar um grupo existente pelo menos uma propriedade modificada. Quando atualizar um grupo de contentores, todos os contentores em execução no grupo são reiniciados no local.

Voltar a implementar um grupo de contentor existente emitindo o comando create (ou utilize o portal do Azure) e especifique o nome de um grupo existente. Modificar pelo menos uma propriedade válida do grupo quando emitir o comando create para acionar a reimplementação. Nem todas as propriedades do grupo de contentor são válidas para a nova implementação. Ver [propriedades que necessitam de eliminação](#properties-that-require-container-delete) para obter uma lista de propriedades não suportadas.

O exemplo seguinte da CLI do Azure atualiza um grupo de contentores com uma nova etiqueta de nome DNS. Porque a propriedade de etiqueta de nome DNS do grupo é modificada, o grupo de contentores é reimplementado, sendo reiniciada de seus contêineres.

Inicial de implementação com a etiqueta de nome DNS *myapplication-teste*:

```azurecli-interactive
# Create container group
az container create --resource-group myResourceGroup --name mycontainer \
    --image nginx:alpine --dns-name-label myapplication-staging
```

Atualizar o grupo de contentores com uma nova etiqueta de nome DNS *myapplication*:

```azurecli-interactive
# Update container group (restarts container)
az container create --resource-group myResourceGroup --name mycontainer \
    --image nginx:alpine --dns-name-label myapplication
```

## <a name="update-benefits"></a>Benefícios da atualização

O principal benefício da atualização de um grupo de contentor existente é a implantação mais rápida. Quando voltar a implementar um grupo de contentor existente, respetivas camadas de imagem de contentor são extraídas daqueles armazenados em cache pela implementação anterior. Em vez de extrair todas as camadas de imagem atualizada do Registro como é feito com novas Implantações, são obtidas apenas camadas modificadas (se houver).

Aplicativos com base nas imagens de contentor maiores, como o Windows Server Core pode ver o aperfeiçoamento significativo na velocidade de implantação durante a atualização em vez de eliminam e implementar novas.

## <a name="limitations"></a>Limitações

Nem todas as propriedades de um grupo de contentores suportam atualizações. Para alterar algumas propriedades de um grupo de contentor, primeiro tem de eliminar e voltar a implementar o grupo. Para obter detalhes, consulte [eliminar propriedades que necessitam de contentor](#properties-that-require-container-delete).

Todos os contentores num grupo de contentor são reiniciados quando atualizar o grupo de contentores. Não é possível efetuar uma atualização ou reinício no local de um contentor específico num grupo de vários contentor.

O endereço IP de um contentor, normalmente, não será alterada entre as atualizações, mas não há garantia de permanecem os mesmos. Desde que o grupo de contentor é implementado para o mesmo anfitrião subjacente, o grupo de contentores mantém o respetivo endereço IP. Embora seja raro, e, embora o Azure Container Instances permite todos os esforços para Reimplementar no mesmo anfitrião, existem alguns eventos interno do Azure que podem fazer com que a reimplementação para outro anfitrião. Para atenuar este problema, utilize sempre uma etiqueta de nome DNS para as instâncias de contentor.

Não não possível atualizar os grupos de contentores terminado ou eliminado. Assim que parou de um grupo de contentores (está no *Terminated* Estado) ou tiver sido eliminado, o grupo é implementado como novo.

## <a name="properties-that-require-container-delete"></a>Propriedades que necessitam de eliminação de contentor

Como mencionado anteriormente, nem todas as propriedades do grupo de contentor podem ser atualizadas. Por exemplo, para alterar as portas ou política de um contentor de reinício, deve primeiro eliminar o grupo de contentores, em seguida, criá-la novamente.

Estas propriedades necessitam de eliminação do grupo de contentor antes da nova implementação:

* Tipo de SO
* CPU
* Memória
* Política de reinício
* Portas

Quando eliminar um grupo de contentores e recriá-lo, ele tem não "Reimplementar", mas criado de novo. Todas as camadas de imagem são extraídas atualizadas do Registro, não a partir em cache por uma implementação anterior. O endereço IP do contentor também podem ser alteradas devido a ser implementado para outro anfitrião subjacente.

## <a name="next-steps"></a>Passos Seguintes

Mencionado várias vezes neste artigo é o **grupo de contentores**. Todos os contentores no Azure Container Instances é implementado num grupo de contentor e grupos de contentores podem conter mais de um contêiner.

[Grupos de contentores no Azure Container Instances](container-instances-container-groups.md)

[Implementar um grupo de vários contentor](container-instances-multi-container-group.md)

<!-- LINKS - External -->

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container?view=azure-cli-latest#az-container-create
[az-container-logs]: /cli/azure/container?view=azure-cli-latest#az-container-logs
[az-container-show]: /cli/azure/container?view=azure-cli-latest#az-container-show
[azure-cli-install]: /cli/azure/install-azure-cli
