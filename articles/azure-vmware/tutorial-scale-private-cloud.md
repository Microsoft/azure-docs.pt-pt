---
title: Tutorial - Escalar uma nuvem privada
description: Neste tutorial, você usa o portal Azure para escalar uma nuvem privada Azure VMware Solution.
ms.topic: tutorial
ms.date: 03/13/2021
ms.openlocfilehash: 2129a3f5d04311883369b7b708689a13f07ec118
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "103463631"
---
# <a name="tutorial-scale-an-azure-vmware-solution-private-cloud"></a>Tutorial: Escalar uma nuvem privada Azure VMware Solution

Para tirar o máximo partido da sua experiência em nuvem privada Azure VMware Solution, dimensione os clusters e anfitriões para refletir o que precisa para cargas de trabalho planeadas. Pode escalar os clusters e anfitriões numa nuvem privada, conforme necessário para a carga de trabalho da sua aplicação. As limitações de desempenho e disponibilidade de serviços específicos devem ser abordadas caso a caso. Os limites do cluster e do hospedeiro são fornecidos no artigo de [conceito de nuvem privada.](concepts-private-clouds-clusters.md)

Neste tutorial, você usará o portal Azure para:

> [!div class="checklist"]
> * Adicione um cluster a uma nuvem privada existente
> * Adicione anfitriões a um cluster existente

## <a name="prerequisites"></a>Pré-requisitos

Você precisará de uma nuvem privada existente para completar este tutorial. Se não criou uma nuvem privada, use o [tutorial de nuvem privada](tutorial-create-private-cloud.md) para criar uma. 

## <a name="add-a-new-cluster"></a>Adicione um novo cluster

1. Na página geral de uma nuvem privada existente, em **Manage**, selecione **Scale private cloud**. Em seguida, **selecione + Adicione um cluster.**

   :::image type="content" source="./media/tutorial-scale-private-cloud/ss2-select-add-cluster.png" alt-text="selecionar adicionar um cluster" border="true":::

1. Na página **de cluster Adicionar,** utilize o slider para selecionar o número de anfitriões. Selecione **Guardar**.

   :::image type="content" source="./media/tutorial-scale-private-cloud/ss3-configure-new-cluster.png" alt-text="Na página de cluster Adicionar, utilize o slider para selecionar o número de anfitriões. Selecione Save." border="true":::

   A implantação do novo cluster começará.

## <a name="scale-a-cluster"></a>Dimensionar um cluster 

1. Na página geral de uma nuvem privada existente, selecione **Scale private cloud** e selecione o ícone do lápis para editar o cluster.

   :::image type="content" source="./media/tutorial-scale-private-cloud/ss4-select-scale-private-cloud-2.png" alt-text="Selecione a nuvem privada de escala em visão geral" border="true":::

1. Na página **'EditAr Cluster',** utilize o slider para selecionar o número de anfitriões. Selecione **Guardar**.

   :::image type="content" source="./media/tutorial-scale-private-cloud/ss5-scale-cluster.png" alt-text="Na página 'EditAr Cluster', utilize o slider para selecionar o número de anfitriões. Selecione Save." border="true":::

   Começa a adição de anfitriões ao cluster.

## <a name="next-steps"></a>Passos seguintes

Se necessitar de outra nuvem privada Azure VMware Solution, [crie outra nuvem privada,](tutorial-create-private-cloud.md)seguindo os mesmos pré-requisitos de rede, cluster e limites de hospedeiro.

<!-- LINKS - external-->

<!-- LINKS - internal -->
