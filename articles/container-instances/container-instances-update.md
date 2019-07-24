---
title: Atualizar contêineres em instâncias de contêiner do Azure
description: Saiba como atualizar contêineres em execução em seus grupos de contêiner de instâncias de contêiner do Azure.
services: container-instances
author: dlepow
manager: gwallace
ms.service: container-instances
ms.topic: article
ms.date: 08/01/2018
ms.author: danlep
ms.openlocfilehash: d555ba6b8c2b32fc6ec56d6c51dda9626b6f0cb0
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/18/2019
ms.locfileid: "68325535"
---
# <a name="update-containers-in-azure-container-instances"></a>Atualizar contêineres em instâncias de contêiner do Azure

Durante a operação normal das instâncias de contêiner, talvez você ache necessário atualizar os contêineres em um grupo de contêineres. Por exemplo, você pode desejar atualizar a versão da imagem, alterar um nome DNS, atualizar variáveis de ambiente ou atualizar o estado de um contêiner cujo aplicativo falhou.

## <a name="update-a-container-group"></a>Atualizar um grupo de contêineres

Atualize os contêineres em um grupo de contêineres Reimplantando um grupo existente com pelo menos uma propriedade modificada. Quando você atualiza um grupo de contêineres, todos os contêineres em execução no grupo são reiniciados no local.

Reimplante um grupo de contêineres existente emitindo o comando Create (ou use o portal do Azure) e especifique o nome de um grupo existente. Modifique pelo menos uma propriedade válida do grupo ao emitir o comando Create para disparar a reimplantação. Nem todas as propriedades do grupo de contêineres são válidas para reimplantação. Consulte [Propriedades que exigem exclusão](#properties-that-require-container-delete) para obter uma lista de propriedades sem suporte.

O exemplo a seguir CLI do Azure atualiza um grupo de contêineres com um novo rótulo de nome DNS. Como a propriedade rótulo do nome DNS do grupo é modificada, o grupo de contêineres é reimplantado e seus contêineres são reiniciados.

Implantação inicial com rótulo de nome DNS *MyApplication-preparo*:

```azurecli-interactive
# Create container group
az container create --resource-group myResourceGroup --name mycontainer \
    --image nginx:alpine --dns-name-label myapplication-staging
```

Atualize o grupo de contêineres com um novo rótulo de nome DNS, *MyApplication*:

```azurecli-interactive
# Update container group (restarts container)
az container create --resource-group myResourceGroup --name mycontainer \
    --image nginx:alpine --dns-name-label myapplication
```

## <a name="update-benefits"></a>Benefícios da atualização

O principal benefício de atualizar um grupo de contêineres existente é a implantação mais rápida. Quando você reimplanta um grupo de contêineres existente, suas camadas de imagem de contêiner são extraídas daquelas armazenadas em cache pela implantação anterior. Em vez de extrair todas as camadas de imagem do registro como é feito com novas implantações, somente as camadas modificadas (se houver) são puxadas.

Aplicativos baseados em imagens de contêiner maiores, como o Windows Server Core, podem ver uma melhoria significativa na velocidade de implantação quando você atualiza em vez de excluir e implantar novo.

## <a name="limitations"></a>Limitações

Nem todas as propriedades de um grupo de contêineres dão suporte a atualizações. Para alterar algumas propriedades de um grupo de contêineres, você deve primeiro excluir e reimplantar o grupo. Para obter detalhes, consulte [Propriedades que exigem exclusão de contêiner](#properties-that-require-container-delete).

Todos os contêineres em um grupo de contêineres são reiniciados quando você atualiza o grupo de contêineres. Você não pode executar uma atualização ou reinicialização in-loco de um contêiner específico em um grupo de vários contêineres.

O endereço IP de um contêiner normalmente não é alterado entre as atualizações, mas não é garantido que permaneça o mesmo. Desde que o grupo de contêineres seja implantado no mesmo host subjacente, o grupo de contêineres manterá seu endereço IP. Embora seja raro, e embora as instâncias de contêiner do Azure façam cada esforço para reimplantar no mesmo host, há alguns eventos internos do Azure que podem causar a reimplantação em um host diferente. Para atenuar esse problema, use sempre um rótulo de nome DNS para suas instâncias de contêiner.

Grupos de contêineres finalizados ou excluídos não podem ser atualizados. Depois que um grupo de contêineres for interrompido (  está no estado encerrado) ou tiver sido excluído, o grupo será implantado como novo.

## <a name="properties-that-require-container-delete"></a>Propriedades que exigem exclusão de contêiner

Como mencionado anteriormente, nem todas as propriedades do grupo de contêineres podem ser atualizadas. Por exemplo, para alterar as portas ou reiniciar a política de um contêiner, primeiro você deve excluir o grupo de contêineres e, em seguida, criá-lo novamente.

Essas propriedades exigem a exclusão do grupo de contêineres antes da reimplantação:

* Tipo de so
* CPU
* Memória
* Política de reinício
* Portas

Quando você exclui um grupo de contêineres e o recria, ele não é "reimplantado", mas é criado novo. Todas as camadas de imagem são retiradas do registro, não das que foram armazenadas em cache por uma implantação anterior. O endereço IP do contêiner também pode ser alterado porque está sendo implantado em um host subjacente diferente.

## <a name="next-steps"></a>Passos Seguintes

Mencionado várias vezes neste artigo está o **grupo**de contêineres. Cada contêiner nas instâncias de contêiner do Azure é implantado em um grupo de contêineres e os grupos de contêineres podem conter mais de um contêiner.

[Grupos de contentores no Azure Container Instances](container-instances-container-groups.md)

[Implantar um grupo de vários contêineres](container-instances-multi-container-group.md)

<!-- LINKS - External -->

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container?view=azure-cli-latest#az-container-create
[az-container-logs]: /cli/azure/container?view=azure-cli-latest#az-container-logs
[az-container-show]: /cli/azure/container?view=azure-cli-latest#az-container-show
[azure-cli-install]: /cli/azure/install-azure-cli
