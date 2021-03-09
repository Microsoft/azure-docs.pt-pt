---
title: Criar um grupo de colocação por proximidade com o portal
description: Saiba como criar um grupo de colocação de proximidade utilizando o portal Azure.
author: cynthn
ms.service: virtual-machines
ms.subservice: proximity-placement-groups
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 3/8/2021
ms.author: cynthn
ms.openlocfilehash: daf844870670c14db5208f45fbd9c9adf46be985
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/09/2021
ms.locfileid: "102504631"
---
# <a name="create-a-proximity-placement-group-using-the-azure-portal"></a>Criar um grupo de colocação de proximidade utilizando o portal Azure

Para obter VMs o mais próximo possível, alcançando a menor latência possível, deve implantá-los dentro de um [grupo de colocação de proximidade](../co-location.md#proximity-placement-groups).

Um grupo de colocação de proximidade é um agrupamento lógico usado para garantir que os recursos de computação Azure estão fisicamente localizados perto uns dos outros. Os grupos de colocação de proximidade são úteis para cargas de trabalho onde a baixa latência é um requisito.

> [!NOTE]
> Os grupos de colocação de proximidade não podem ser utilizados com anfitriões dedicados.
>
> Se quiser utilizar zonas de disponibilidade juntamente com grupos de colocação, tem de se certificar de que os VMs do grupo de colocação também estão todos na mesma zona de disponibilidade.
>

## <a name="create-the-proximity-placement-group"></a>Criar o grupo de colocação de proximidade

1. Tipo **grupo de colocação** de proximidade na pesquisa.
1. Em **Serviços** nos resultados da pesquisa, selecione **grupos de colocação de proximidade**.
1. Na página **grupos de colocação de proximidade,** selecione **Adicionar**.
1. No **separador Basics,** nos **detalhes do Projeto,** certifique-se de que a subscrição correta é selecionada.
1. No **grupo de recursos** ou selecione Criar **novo** para criar um novo grupo ou selecionar um grupo de recursos vazio que já exista, a partir do drop-down. 
1. Na **Região** selecione o local onde deseja que o grupo de colocação de proximidade seja criado.
1. Em **Closey placement group name** type a name and then select Review + **create**.
1. Após os passes de validação, **selecione Criar** para criar o grupo de colocação de proximidade.

    ![Screenshot de criar um grupo de colocação de proximidade](./media/ppg/ppg.png)


## <a name="create-a-vm"></a>Criar uma VM

1. Ao criar um VM no portal, vá ao separador **Avançado.** 
1. Na seleção do **grupo de colocação de proximidade,** selecione o grupo de colocação correto. 

    ![Screenshot da secção do grupo de colocação de proximidade ao criar um novo VM no portal](./media/ppg/vm-ppg.png)

1. Quando terminar de fazer todas as outras seleções necessárias, selecione **Review + create**.
1. Depois de passar na validação, **selecione Criar** para implementar o VM no grupo de colocação.


## <a name="add-vms-in-an-availability-set-to-a-proximity-placement-group"></a>Adicione VMs em um conjunto de disponibilidade para um grupo de colocação de proximidade

Se o VM fizer parte do conjunto Availability, é necessário adicionar o conjunto de disponibilidade definida no grupo de colocação, antes de adicionar os VMs.

1. No [portal](https://portal.azure.com) procure *conjuntos de Disponibilidade* e selecione o seu conjunto de disponibilidade a partir dos resultados.
1. Stop\deallocate cada VM na disponibilidade definida selecionando o VM, em seguida, selecionando **Stop** na página para o VM e, em seguida, selecione **OK** para parar o VM.
1. Na página para o seu conjunto de disponibilidade, certifique-se de que todos os VMs têm o **Estado** listado como **Stop (deallocated)**.
1. No menu esquerdo, selecione **Configuração**.
1. No **grupo de colocação de proximidade,** selecione um grupo de colocação a partir do drop-down e, em seguida, selecione **Save**.
1. Selecione **visão geral** do menu esquerdo para ver novamente a lista de VMs. 
1. Selecione cada VM no conjunto de disponibilidade e, em seguida, **selecione Iniciar** na página para cada VM. 


## <a name="add-existing-vm-to-placement-group"></a>Adicione vM existente ao grupo de colocação 


1. Na página para o VM, **selecione Stop**.
1. Uma vez que o estado do VM esteja listado como **Stop (deallocated)**, selecione **Configuração** no menu esquerdo.
1. No **grupo de colocação de proximidade,** selecione um grupo de colocação a partir do drop-down e, em seguida, selecione **Save**.
1. Selecione **a visão geral** do menu esquerdo e, em seguida, selecione **Iniciar** para reiniciar o VM.

 

## <a name="next-steps"></a>Passos seguintes

Também pode utilizar o [Azure PowerShell](proximity-placement-groups.md) para criar grupos de colocação de proximidade.