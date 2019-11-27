---
title: Atualizar grupo de contêineres
description: Saiba como atualizar contêineres em execução em seus grupos de contêiner de instâncias de contêiner do Azure.
ms.topic: article
ms.date: 09/03/2019
ms.openlocfilehash: f57ebcf050b5563b45f10af57c1721338df88ff9
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/26/2019
ms.locfileid: "74533308"
---
# <a name="update-containers-in-azure-container-instances"></a>Atualizar contêineres em instâncias de contêiner do Azure

Durante a operação normal das instâncias de contêiner, talvez você ache necessário atualizar os contêineres em execução em um [grupo de contêineres](container-instances-container-groups.md). Por exemplo, você pode desejar atualizar a versão da imagem, alterar um nome DNS, atualizar variáveis de ambiente ou atualizar o estado de um contêiner cujo aplicativo falhou.

> [!NOTE]
> Grupos de contêineres finalizados ou excluídos não podem ser atualizados. Depois que um grupo de contêineres termina (está em um estado de êxito ou de falha) ou foi excluído, o grupo deve ser implantado como novo.

## <a name="update-a-container-group"></a>Atualizar um grupo de contêineres

Atualize os contêineres em um grupo de contêineres em execução Reimplantando um grupo existente com pelo menos uma propriedade modificada. Quando você atualiza um grupo de contêineres, todos os contêineres em execução no grupo são reiniciados no local, geralmente no mesmo host de contêiner subjacente.

Reimplante um grupo de contêineres existente emitindo o comando Create (ou use o portal do Azure) e especifique o nome de um grupo existente. Modifique pelo menos uma propriedade válida do grupo ao emitir o comando Create para disparar a reimplantação e deixe as propriedades restantes inalteradas (ou continue a usar valores padrão). Nem todas as propriedades do grupo de contêineres são válidas para reimplantação. Consulte [Propriedades que exigem exclusão](#properties-that-require-container-delete) para obter uma lista de propriedades sem suporte.

O exemplo a seguir CLI do Azure atualiza um grupo de contêineres com um novo rótulo de nome DNS. Como a propriedade rótulo do nome DNS do grupo é aquela que pode ser atualizada, o grupo de contêineres é reimplantado e seus contêineres são reiniciados.

Implantação inicial com rótulo de nome DNS *MyApplication-preparo*:

```azurecli-interactive
# Create container group
az container create --resource-group myResourceGroup --name mycontainer \
    --image nginx:alpine --dns-name-label myapplication-staging
```

Atualize o grupo de contêineres com um novo rótulo de nome DNS, *MyApplication*e deixe as propriedades restantes inalteradas:

```azurecli-interactive
# Update DNS name label (restarts container), leave other properties unchanged
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

Grupos de contêineres finalizados ou excluídos não podem ser atualizados. Depois que um grupo de contêineres for interrompido (está no estado *encerrado* ) ou tiver sido excluído, o grupo será implantado como novo.

## <a name="properties-that-require-container-delete"></a>Propriedades que exigem exclusão de contêiner

Como mencionado anteriormente, nem todas as propriedades do grupo de contêineres podem ser atualizadas. Por exemplo, para alterar as portas ou reiniciar a política de um contêiner, primeiro você deve excluir o grupo de contêineres e, em seguida, criá-lo novamente.

Essas propriedades exigem a exclusão do grupo de contêineres antes da reimplantação:

* Tipo de so
* CPU
* Memória
* Reinicializar política
* Portas

Quando você exclui um grupo de contêineres e o recria, ele não é "reimplantado", mas é criado novo. Todas as camadas de imagem são retiradas do registro, não das que foram armazenadas em cache por uma implantação anterior. O endereço IP do contêiner também pode ser alterado porque está sendo implantado em um host subjacente diferente.

## <a name="next-steps"></a>Passos seguintes

Mencionado várias vezes neste artigo está o **grupo de contêineres**. Cada contêiner nas instâncias de contêiner do Azure é implantado em um grupo de contêineres e os grupos de contêineres podem conter mais de um contêiner.

[Grupos de contentores no Azure Container Instances](container-instances-container-groups.md)

[Implantar um grupo de vários contêineres](container-instances-multi-container-group.md)

[Parar ou iniciar os contêineres manualmente nas instâncias de contêiner do Azure](container-instances-stop-start.md)

<!-- LINKS - External -->

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container?view=azure-cli-latest#az-container-create
[azure-cli-install]: /cli/azure/install-azure-cli
