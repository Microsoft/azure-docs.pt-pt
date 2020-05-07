---
title: Atualizar grupo de contentores
description: Saiba como atualizar os contentores em funcionamento nos grupos de contentores de contentores Do seu Contentor Azure.
ms.topic: article
ms.date: 04/17/2020
ms.openlocfilehash: dc10e38aa6be9473f89df6f7fa10a72f63d9ada8
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2020
ms.locfileid: "82854443"
---
# <a name="update-containers-in-azure-container-instances"></a>Atualizar contentores no Azure Container Instances

Durante o funcionamento normal das instâncias do seu contentor, poderá ser necessário atualizar os contentores de funcionamento num grupo de [contentores](container-instances-container-groups.md). Por exemplo, você pode desejar atualizar uma propriedade como uma versão de imagem, um nome DNS, ou uma variável ambiental, ou refrescar uma propriedade em um recipiente cuja aplicação se despenhou.

Atualize os contentores num grupo de contentores em execução, reimplantando um grupo existente com pelo menos uma propriedade modificada. Ao atualizar um grupo de contentores, todos os contentores em funcionamento do grupo são reiniciados no local, normalmente no mesmo hospedeiro subjacente.

> [!NOTE]
> Os grupos de contentores rescindidos ou eliminados não podem ser atualizados. Uma vez que um grupo de contentores tenha terminado (está em estado bem sucedido ou falhado) ou foi eliminado, o grupo deve ser implantado como novo. Ver [outras limitações.](#limitations)

## <a name="update-a-container-group"></a>Atualizar um grupo de contentores

Para atualizar um grupo de contentores existente:

* Emita o comando de criação (ou utilize o portal Azure) e especifique o nome de um grupo existente 
* Modifique ou adicione pelo menos uma propriedade do grupo que suporta a atualização quando reimplanta. Certas propriedades [não suportam atualizações.](#properties-that-require-container-delete)
* Detete outras propriedades com os valores que forneceu anteriormente. Se não definir um valor para uma propriedade, reverte para o seu valor padrão.

> [!TIP]
> Um [ficheiro YAML](container-instances-container-groups.md#deployment) ajuda a manter a configuração de implementação de um grupo de contentores e fornece um ponto de partida para implantar um grupo atualizado. Se usou um método diferente para criar o grupo, pode exportar a configuração para o YAML utilizando a exportação de [contentores az,][az-container-export] 

### <a name="example"></a>Exemplo

O exemplo do Azure CLI atualiza um grupo de contentores com uma nova etiqueta de nome DNS. Como a propriedade de etiqueta de nome DNS do grupo é uma que pode ser atualizada, o grupo de contentores é reimplantado, e os seus contentores reiniciados.

Implantação inicial com o nome DNS rótulo *myapplication-staging:*

```azurecli-interactive
# Create container group
az container create --resource-group myResourceGroup --name mycontainer \
    --image nginx:alpine --dns-name-label myapplication-staging
```

Atualize o grupo de contentores com uma nova etiqueta de nome DNS, *myapplication,* e coloque as restantes propriedades com os valores utilizados anteriormente:

```azurecli-interactive
# Update DNS name label (restarts container), leave other properties unchanged
az container create --resource-group myResourceGroup --name mycontainer \
    --image nginx:alpine --dns-name-label myapplication
```

## <a name="update-benefits"></a>Atualizar benefícios

O principal benefício da atualização de um grupo de contentores existente é a implantação mais rápida. Quando reimplanta um grupo de contentores existente, as suas camadas de imagem de contentor são retiradas das colocadas pela implantação anterior. Em vez de retirar todas as camadas de imagem frescas do registo, como é feito com novas implementações, apenas as camadas modificadas (se houver) são puxadas.

Aplicações baseadas em imagens de contentores maiores como o Windows Server Core podem ver melhorias significativas na velocidade de implementação quando atualizas em vez de eliminar e implementar novas.

## <a name="limitations"></a>Limitações

* Nem todas as propriedades de um grupo de contentores suportam atualizações. Para alterar algumas propriedades de um grupo de contentores, tem primeiro de eliminar e, em seguida, recolocar o grupo. Ver [Propriedades que exijam a eliminação do recipiente](#properties-that-require-container-delete).
* Todos os contentores de um grupo de contentores são reiniciados quando atualizar o grupo de contentores. Não é possível efetuar uma atualização ou reiniciar um recipiente específico num grupo multi-contentores.
* O endereço IP de um grupo de contentores é normalmente retido entre atualizações, mas não é garantido que permaneça o mesmo. Enquanto o grupo de contentores for implantado para o mesmo hospedeiro subjacente, o grupo de contentores mantém o seu endereço IP. Embora raros, existem alguns eventos internos azure que podem causar a redistribuição para um hospedeiro diferente. Para atenuar este problema, recomendamos a utilização de uma etiqueta de nome DNS para os casos do seu contentor.
* Os grupos de contentores rescindidos ou eliminados não podem ser atualizados. Uma vez que um grupo de contentores é parado (está no estado *terminado)* ou eliminado, o grupo é implantado como novo.

## <a name="properties-that-require-container-delete"></a>Propriedades que requerem eliminação do contentor

Nem todas as propriedades do grupo de contentores podem ser atualizadas. Por exemplo, para alterar a política de reinício de um contentor, tem primeiro de eliminar o grupo de contentores e, em seguida, criá-lo novamente.

As alterações a estas propriedades requerem a eliminação do grupo de contentores antes da redistribuição:

* Tipo OS
* Recursos de CPU, memória ou GPU
* Política de reinício
* Perfil de rede

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
[az-container-export]: /cli/azure/container#az-container-export
