---
title: Atualizar grupo de contentores
description: Saiba como atualizar os recipientes de funcionamento nos grupos de contentores Azure Container.
ms.topic: article
ms.date: 04/17/2020
ms.openlocfilehash: cfc27de8caae98dd1c3065b5ed06433c4baaa5d2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "82928725"
---
# <a name="update-containers-in-azure-container-instances"></a>Atualizar contentores no Azure Container Instances

Durante o funcionamento normal das suas instâncias de contentores, poderá considerar necessário atualizar os recipientes de funcionamento num [grupo de contentores](./container-instances-container-groups.md). Por exemplo, pode desejar atualizar uma propriedade como uma versão de imagem, um nome DNS ou uma variável ambiental, ou refrescar uma propriedade em um recipiente cuja aplicação tenha caído.

Atualize os recipientes num grupo de contentores em funcionamento reposicionando um grupo existente com pelo menos uma propriedade modificada. Quando atualiza um grupo de contentores, todos os recipientes de funcionamento do grupo são reiniciados no local, normalmente no mesmo hospedeiro de contentores subjacente.

> [!NOTE]
> Os grupos de contentores encerrados ou eliminados não podem ser atualizados. Uma vez terminado um grupo de contentores (está num estado bem sucedido ou falhado) ou eliminado, o grupo deve ser implantado como novo. Ver [outras limitações.](#limitations)

## <a name="update-a-container-group"></a>Atualizar um grupo de contentores

Para atualizar um grupo de contentores existente:

* Emita o comando de criar (ou usar o portal Azure) e especifique o nome de um grupo existente 
* Modifique ou adicione pelo menos uma propriedade do grupo que suporta a atualização quando é reimplantação. Certas propriedades [não suportam atualizações.](#properties-that-require-container-delete)
* Desateia outras propriedades com os valores que forneceu anteriormente. Se não definir um valor para um imóvel, ele reverte para o seu valor padrão.

> [!TIP]
> Um [ficheiro YAML](./container-instances-container-groups.md#deployment) ajuda a manter a configuração de implantação de um grupo de contentores e fornece um ponto de partida para implantar um grupo atualizado. Se utilizar um método diferente para criar o grupo, pode exportar a configuração para YAML utilizando [a exportação de recipientes az][az-container-export], 

### <a name="example"></a>Exemplo

O exemplo Azure CLI a seguir atualiza um grupo de contentores com uma nova etiqueta de nome DNS. Como a propriedade da etiqueta de nome DNS do grupo é uma que pode ser atualizada, o grupo de contentores é redistribuído, e os seus contentores reiniciados.

Implantação inicial com o nome DNS *rótulo myapplication-staging*:

```azurecli-interactive
# Create container group
az container create --resource-group myResourceGroup --name mycontainer \
    --image nginx:alpine --dns-name-label myapplication-staging
```

Atualize o grupo de contentores com uma nova etiqueta de nome DNS, *myapplication,* e deite as propriedades restantes com os valores usados anteriormente:

```azurecli-interactive
# Update DNS name label (restarts container), leave other properties unchanged
az container create --resource-group myResourceGroup --name mycontainer \
    --image nginx:alpine --dns-name-label myapplication
```

## <a name="update-benefits"></a>Atualizar benefícios

O principal benefício da atualização de um grupo de contentores existente é a implantação mais rápida. Quando se recoloca um grupo de contentores existente, as suas camadas de imagem de contentor são retiradas das camadas de cache da implantação anterior. Em vez de retirar todas as camadas de imagem frescas do registo, como é feito com novas implementações, apenas as camadas modificadas (se houver) são puxadas.

As aplicações baseadas em imagens de contentores maiores como o Windows Server Core podem ver melhorias significativas na velocidade de implementação quando atualizas em vez de eliminar e implementar novas.

## <a name="limitations"></a>Limitações

* Nem todas as propriedades de um grupo de contentores apoiam atualizações. Para alterar algumas propriedades de um grupo de contentores, tem primeiro de apagar e depois recolocar o grupo. Ver [Propriedades que requerem a eliminação do recipiente.](#properties-that-require-container-delete)
* Todos os recipientes de um grupo de contentores são reiniciados quando atualizar o grupo de contentores. Não é possível efetuar uma atualização ou reinicialização no local de um recipiente específico num grupo de vários contentores.
* O endereço IP de um grupo de contentores é normalmente mantido entre atualizações, mas não é garantido que permaneça o mesmo. Enquanto o grupo de contentores for implantado no mesmo hospedeiro subjacente, o grupo de contentores mantém o seu endereço IP. Embora raros, existem alguns eventos internos do Azure que podem causar a recolocação para um hospedeiro diferente. Para atenuar este problema, recomendamos a utilização de uma etiqueta de nome DNS para as suas instâncias de contentores.
* Os grupos de contentores encerrados ou eliminados não podem ser atualizados. Uma vez parado um grupo de contentores (está no estado *de Terminateed)* ou eliminado, o grupo é implantado como novo.

## <a name="properties-that-require-container-delete"></a>Propriedades que requerem eliminação de contentores

Nem todas as propriedades do grupo de contentores podem ser atualizadas. Por exemplo, para alterar a política de reinício de um recipiente, primeiro deve apagar o grupo de contentores e, em seguida, criá-lo novamente.

As alterações a estas propriedades requerem a eliminação do grupo de contentores antes da reafectação:

* Tipo de SO
* CPU, recursos de memória ou GPU
* Política de reinício
* Perfil de rede

Quando se apaga um grupo de contentores e o recria, não é "redistribuído", mas criou novos. Todas as camadas de imagem são retiradas frescas do registo, não das em cache por uma implantação anterior. O endereço IP do contentor também pode mudar devido à sua implantação num hospedeiro diferente subjacente.

## <a name="next-steps"></a>Passos seguintes

Mencionado várias vezes neste artigo é o **grupo de contentores.** Todos os contentores em Azure Container Instances são implantados num grupo de contentores, e os grupos de contentores podem conter mais de um contentor.

[Grupos de contentores no Azure Container Instances](./container-instances-container-groups.md)

[Implementar um grupo com vários contentores](container-instances-multi-container-group.md)

[Iniciar ou parar manualmente os contentores no Azure Container Instances](container-instances-stop-start.md)

<!-- LINKS - External -->

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container?view=azure-cli-latest#az-container-create
[azure-cli-install]: /cli/azure/install-azure-cli
[az-container-export]: /cli/azure/container#az-container-export
