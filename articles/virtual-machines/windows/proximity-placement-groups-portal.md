---
title: Criar um grupo de colocação de proximidade usando o portal
description: Aprenda a criar um grupo de colocação de proximidade utilizando o portal Azure.
author: cynthn
ms.service: virtual-machines
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 10/30/2019
ms.author: cynthn
ms.openlocfilehash: aaecfbd14289840e795c6323737877e267586e16
ms.sourcegitcommit: 086d7c0cf812de709f6848a645edaf97a7324360
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2020
ms.locfileid: "82098650"
---
# <a name="create-a-proximity-placement-group-using-the-portal"></a>Criar um grupo de colocação de proximidade usando o portal

Para obter VMs o mais próximo possível, alcançando a menor latência possível, você deve implantá-los dentro de um grupo de [colocação de proximidade](co-location.md#proximity-placement-groups).

Um grupo de colocação de proximidade é um agrupamento lógico usado para garantir que os recursos da computação Azure estão fisicamente localizados perto uns dos outros. Os grupos de colocação de proximidade são úteis para cargas de trabalho onde a baixa latência é um requisito.


## <a name="create-the-proximity-placement-group"></a>Criar o grupo de colocação de proximidade

1. Digite o grupo de **colocação** de proximidade na pesquisa.
1. No âmbito **dos Serviços** nos resultados da pesquisa, selecione grupos de **colocação de proximidade**.
1. Na página dos grupos de **colocação de Proximidade,** selecione **Adicionar**.
1. No separador **Basics,** sob os detalhes do **Projeto,** certifique-se de que a subscrição correta é selecionada.
1. No **grupo Recursos,** ou selecione **Criar novos** para criar um novo grupo ou selecione um grupo de recursos existente a partir da queda.
1. Na **Região** selecione o local onde pretende criar o grupo de colocação de proximidade.
1. No nome do grupo de **colocação de Proximidade,** escreva um nome e, em seguida, selecione **Review + criar**.
1. Após os passes de validação, selecione **Criar** para criar o grupo de colocação de proximidade.

    ![Screenshot de criar um grupo de colocação de proximidade](./media/ppg/ppg.png)


## <a name="create-a-vm"></a>Criar uma VM

1. Ao criar um VM no portal, vá ao separador **Advanced.** 
1. Na seleção do grupo de **colocação de Proximidade,** selecione o grupo de colocação correto. 

    ![Screenshot da secção de grupo de colocação de proximidade ao criar um novo VM no portal](./media/ppg/vm-ppg.png)

1. Quando terminar de fazer todas as outras seleções necessárias, selecione **Review + criar**.
1. Depois de passar a validação, selecione **Criar** para implantar o VM no grupo de colocação.




## <a name="next-steps"></a>Passos seguintes

Também pode utilizar o [Azure PowerShell](proximity-placement-groups.md) para criar grupos de colocação de proximidade.

