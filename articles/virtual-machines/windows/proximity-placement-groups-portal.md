---
title: Criar um grupo de colocação de proximidade usando o portal
description: Aprenda a criar um grupo de colocação de proximidade utilizando o portal Azure.
author: cynthn
ms.service: virtual-machines
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 04/24/2020
ms.author: cynthn
ms.openlocfilehash: 6a14e2bd7385430c3d0fbec06259a876af556e38
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82190406"
---
# <a name="create-a-proximity-placement-group-using-the-portal"></a>Criar um grupo de colocação de proximidade usando o portal

Para obter VMs o mais próximo possível, alcançando a menor latência possível, você deve implantá-los dentro de um grupo de [colocação de proximidade](co-location.md#proximity-placement-groups).

Um grupo de colocação de proximidade é um agrupamento lógico usado para garantir que os recursos da computação Azure estão fisicamente localizados perto uns dos outros. Os grupos de colocação de proximidade são úteis para cargas de trabalho onde a baixa latência é um requisito.

> [!NOTE]
> Os grupos de colocação de proximidade não podem ser utilizados com anfitriões dedicados.
>
> Se você quiser usar zonas de disponibilidade juntamente com grupos de colocação, você precisa ter certeza de que os VMs no grupo de colocação também estão todos na mesma zona de disponibilidade.
>

## <a name="create-the-proximity-placement-group"></a>Criar o grupo de colocação de proximidade

1. Digite o grupo de **colocação** de proximidade na pesquisa.
1. No âmbito **dos Serviços** nos resultados da pesquisa, selecione grupos de **colocação de proximidade**.
1. Na página dos grupos de **colocação de Proximidade,** selecione **Adicionar**.
1. No separador **Basics,** sob os detalhes do **Projeto,** certifique-se de que a subscrição correta é selecionada.
1. No **grupo Recursos,** ou selecione **Criar novo** para criar um novo grupo ou selecionar um grupo de recursos vazios que já existe, a partir da queda. 
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


## <a name="add-vms-in-an-availability-set-to-a-proximity-placement-group"></a>Adicione VMs em um conjunto de disponibilidade para um grupo de colocação de proximidade

Se o VM fizer parte do conjunto de disponibilidade, precisa adicionar a disponibilidade definida no grupo de colocação, antes de adicionar os VMs.

1. No [portal,](https://portal.azure.com) procure *conjuntos* de Disponibilidade e selecione o conjunto de disponibilidade dos resultados.
1. Stop\deallocate cada VM na disponibilidade definida selecionando o VM, selecionando depois **Stop** na página para o VM e, em seguida, selecione **OK** para parar o VM.
1. Na página para o seu conjunto de disponibilidade, certifique-se de que todos os VMs têm o **Status** listado como **Stop (deallocated)**.
1. No menu esquerdo, selecione **Configuração**.
1. No grupo de **colocação de Proximidade,** selecione um grupo de colocação a partir do drop-down e, em seguida, selecione **Save**.
1. Selecione **visão geral** do menu esquerdo para ver novamente a lista de VMs. 
1. Selecione cada VM no conjunto de disponibilidade e, em seguida, selecione **Iniciar** na página para cada VM. 


## <a name="add-existing-vm-to-placement-group"></a>Adicione vM existente ao grupo de colocação 


1. Na página para o VM, selecione **Stop**.
1. Uma vez listado o estado do VM como **Stop (deallocated)**, selecione **Configuração** no menu esquerdo.
1. No grupo de **colocação de Proximidade,** selecione um grupo de colocação a partir do drop-down e, em seguida, selecione **Save**.
1. Selecione **a visão geral** do menu esquerdo e, em seguida, selecione **Iniciar** a reiniciar o VM.

 

## <a name="next-steps"></a>Passos seguintes

Também pode utilizar o [Azure PowerShell](proximity-placement-groups.md) para criar grupos de colocação de proximidade.

