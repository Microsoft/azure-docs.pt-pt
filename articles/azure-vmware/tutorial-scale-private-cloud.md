---
title: 'Tutorial: Escala uma nuvem privada'
description: Neste tutorial, utiliza o portal Azure para escalar uma Solução Azure VMware Solution (AVS) Pré-visualização da nuvem privada.
ms.topic: tutorial
ms.date: 05/04/2020
ms.openlocfilehash: 70d8c1c555badd6102f4b2547492bdea54e55783
ms.sourcegitcommit: d9cd51c3a7ac46f256db575c1dfe1303b6460d04
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/04/2020
ms.locfileid: "82740291"
---
# <a name="tutorial-scale-an-azure-vmware-solution-avs-preview-private-cloud"></a>Tutorial: Escala uma solução Azure VMware (AVS) Pré-visualizar nuvem privada

Para tirar o máximo partido da sua experiência em nuvem privada AVS Preview, dimensione os clusters e anfitriões para refletir o que precisa para cargas de trabalho planeadas. Uma vez que o AVS não suporta o seu vCenter no local durante a pré-visualização, terá de utilizar o que já criou através do portal Azure.

Pode escalar o número de clusters e o número de anfitriões numa nuvem privada, conforme necessário para a sua carga de trabalho de aplicação. As limitações de desempenho e disponibilidade para serviços específicos devem ser abordadas caso a caso dentro do ambiente de nuvem de pré-visualização AVS. Os limites do cluster e do hospedeiro numa nuvem privada são fornecidos [no artigo conceito de nuvem privada.](concepts-private-clouds-clusters.md)

Neste tutorial, você usa o portal Azure para:

> [!div class="checklist"]
> * Adicione um cluster a uma nuvem privada existente
> * Adicione anfitriões a um aglomerado existente

## <a name="prerequisites"></a>Pré-requisitos

Precisa de uma nuvem privada para completar este tutorial. Se ainda não criou uma nuvem privada, use a [criação](tutorial-create-private-cloud.md) de um tutorial de nuvem privada para criar uma nuvem privada e configurar a rede para a sua nuvem privada VMware em Azure para configurar a rede virtual necessária.

## <a name="add-a-new-cluster"></a>Adicione um novo cluster

Na página geral de uma nuvem privada existente, sob **Manage**, selecione **Scale nuvem privada**. Em seguida, selecione **+ Adicione um cluster**.

:::image type="content" source="./media/tutorial-scale-private-cloud/ss2-select-add-cluster.png" alt-text="selecionar adicionar um cluster" border="true":::

Na página **'Adicionar',** utilize o slider para selecionar o número de anfitriões. Selecione **Guardar**.

:::image type="content" source="./media/tutorial-scale-private-cloud/ss3-configure-new-cluster.png" alt-text="Configure um novo aglomerado de nuvens privadas" border="true":::

A implantação do novo cluster começará.

## <a name="scale-a-cluster"></a>Dimensionar um cluster 

Na página geral de uma nuvem privada existente, selecione **Scale nuvem privada** e selecione o ícone do lápis para editar o cluster.

:::image type="content" source="./media/tutorial-scale-private-cloud/ss4-select-scale-private-cloud-2.png" alt-text="Selecione Nuvem privada de escala em visão geral" border="true":::

Na página **'Editar Cluster',** utilize o slider para selecionar o número de anfitriões. Selecione **Guardar**.

:::image type="content" source="./media/tutorial-scale-private-cloud/ss5-scale-cluster.png" alt-text="Configure um novo aglomerado de nuvens privadas" border="true":::

A adição de anfitriões ao aglomerado começará.

## <a name="next-steps"></a>Passos seguintes

Se necessitar de outra nuvem privada AVS, [crie outra nuvem privada,](tutorial-create-private-cloud.md)seguindo os mesmos pré-requisitos de networking, cluster e limites de hospedar...

<!-- LINKS - external-->

<!-- LINKS - internal -->
