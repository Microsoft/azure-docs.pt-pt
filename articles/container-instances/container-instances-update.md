---
title: Atualizar grupo de contentores
description: Saiba como atualizar os contentores em funcionamento nos grupos de contentores de contentores Do seu Contentor Azure.
ms.topic: article
ms.date: 09/03/2019
ms.openlocfilehash: f57ebcf050b5563b45f10af57c1721338df88ff9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74533308"
---
# <a name="update-containers-in-azure-container-instances"></a>Atualizar contentores no Azure Container Instances

Durante o funcionamento normal das instâncias do seu contentor, poderá ser necessário atualizar os contentores de funcionamento num grupo de [contentores](container-instances-container-groups.md). Por exemplo, pode desejar atualizar a versão de imagem, alterar um nome DNS, atualizar variáveis ambientais ou refrescar o estado de um recipiente cuja aplicação se despenhou.

> [!NOTE]
> Os grupos de contentores rescindidos ou eliminados não podem ser atualizados. Uma vez que um grupo de contentores tenha terminado (está em estado bem sucedido ou falhado) ou foi eliminado, o grupo deve ser implantado como novo.

## <a name="update-a-container-group"></a>Atualizar um grupo de contentores

Atualize os contentores num grupo de contentores em execução, reimplantando um grupo existente com pelo menos uma propriedade modificada. Ao atualizar um grupo de contentores, todos os contentores em funcionamento do grupo são reiniciados no local, normalmente no mesmo hospedeiro subjacente.

Reutilizar um grupo de contentores existente, emitindo o comando de criação (ou utilizar o portal Azure) e especificar o nome de um grupo existente. Modifique pelo menos uma propriedade válida do grupo quando emitir o comando de criação para desencadear a reimplantação e deixe as propriedades restantes inalteradas (ou continue a utilizar valores predefinidos). Nem todas as propriedades do grupo de contentores são válidas para redistribuição. Consulte [propriedades que exijam a eliminação](#properties-that-require-container-delete) de uma lista de propriedades não suportadas.

O exemplo do Azure CLI atualiza um grupo de contentores com uma nova etiqueta de nome DNS. Como a propriedade de etiqueta de nome DNS do grupo é uma que pode ser atualizada, o grupo de contentores é reimplantado, e os seus contentores reiniciados.

Implantação inicial com o nome DNS rótulo *myapplication-staging:*

```azurecli-interactive
# Create container group
az container create --resource-group myResourceGroup --name mycontainer \
    --image nginx:alpine --dns-name-label myapplication-staging
```

Atualize o grupo de contentores com uma nova etiqueta de nome DNS, *myapplication,* e deixe as restantes propriedades inalteradas:

```azurecli-interactive
# Update DNS name label (restarts container), leave other properties unchanged
az container create --resource-group myResourceGroup --name mycontainer \
    --image nginx:alpine --dns-name-label myapplication
```

## <a name="update-benefits"></a>Atualizar benefícios

O principal benefício da atualização de um grupo de contentores existente é a implantação mais rápida. Quando reimplanta um grupo de contentores existente, as suas camadas de imagem de contentor são retiradas das colocadas pela implantação anterior. Em vez de retirar todas as camadas de imagem frescas do registo, como é feito com novas implementações, apenas as camadas modificadas (se houver) são puxadas.

Aplicações baseadas em imagens de contentores maiores como o Windows Server Core podem ver melhorias significativas na velocidade de implementação quando atualizas em vez de eliminar e implementar novas.

## <a name="limitations"></a>Limitações

Nem todas as propriedades de um grupo de contentores suportam atualizações. Para alterar algumas propriedades de um grupo de contentores, tem primeiro de eliminar e, em seguida, recolocar o grupo. Para mais detalhes, consulte [propriedades que exijam a eliminação do recipiente](#properties-that-require-container-delete).

Todos os contentores de um grupo de contentores são reiniciados quando atualizar o grupo de contentores. Não é possível efetuar uma atualização ou reiniciar um recipiente específico num grupo multi-contentores.

O endereço IP de um contentor normalmente não muda entre atualizações, mas não é garantido que permaneça o mesmo. Enquanto o grupo de contentores for implantado para o mesmo hospedeiro subjacente, o grupo de contentores mantém o seu endereço IP. Embora raros, e enquanto as Instâncias de Contentores Azure fazem todos os esforços para se recolocarem no mesmo hospedeiro, existem alguns eventos internos do Azure que podem causar a redistribuição a um hospedeiro diferente. Para atenuar este problema, utilize sempre uma etiqueta de nome DNS para os casos do seu contentor.

Os grupos de contentores rescindidos ou eliminados não podem ser atualizados. Uma vez que um grupo de contentores tenha parado (está no estado *de terminada)* ou tenha sido eliminado, o grupo é implantado como novo.

## <a name="properties-that-require-container-delete"></a>Propriedades que requerem eliminação do contentor

Como mencionado anteriormente, nem todas as propriedades do grupo de contentores podem ser atualizadas. Por exemplo, para alterar as portas ou reiniciar a política de um contentor, tem primeiro de eliminar o grupo de contentores e, em seguida, criá-lo novamente.

Estas propriedades requerem a eliminação do grupo de contentores antes da redistribuição:

* Tipo OS
* CPU
* Memória
* Política de reinício
* Portas

Quando se apaga um grupo de contentores e o recria, não é "reimplantado", mas criou-se um novo. Todas as camadas de imagem são retiradas frescas do registo, não das que foram colocadas por uma implantação anterior. O endereço IP do recipiente também pode ser alterado devido à sua implantação para um hospedeiro subjacente diferente.

## <a name="next-steps"></a>Passos seguintes

Mencionado várias vezes neste artigo é o grupo de **contentores.** Todos os contentores em Conjunto de Contentores de Azure são implantados num grupo de contentores, e os grupos de contentores podem conter mais do que um contentor.

[Grupos de contentores no Azure Container Instances](container-instances-container-groups.md)

[Implementar um grupo com vários contentores](container-instances-multi-container-group.md)

[Paragem manual ou start de contentores em instâncias de contentores de Azure](container-instances-stop-start.md)

<!-- LINKS - External -->

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container?view=azure-cli-latest#az-container-create
[azure-cli-install]: /cli/azure/install-azure-cli
