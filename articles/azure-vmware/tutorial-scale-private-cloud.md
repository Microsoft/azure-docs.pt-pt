---
title: 'Tutorial: Escalar uma nuvem privada'
description: Neste tutorial, utiliza o portal Azure para escalar uma nuvem privada Azure VMware Solution Preview.
ms.topic: tutorial
ms.date: 08/21/2020
ms.openlocfilehash: dddfbddd57f3ad6b541d11c360aeadea4383044a
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/22/2020
ms.locfileid: "88750426"
---
# <a name="tutorial-scale-an-azure-vmware-solution-preview-private-cloud"></a>Tutorial: Escalar uma solução Azure VMware Preview nuvem privada

Para tirar o máximo partido da experiência em nuvem privada Azure VMware Solution, dimensione os clusters e anfitriões para refletir o que precisa para cargas de trabalho planeadas. Uma vez que a Azure VMware Solution não suporta o seu vCenter no local durante a pré-visualização, terá de utilizar o que já criou através do portal Azure.

Pode escalar o número de aglomerados e o número de anfitriões numa nuvem privada, conforme necessário para a carga de trabalho da sua aplicação. As limitações de desempenho e disponibilidade de serviços específicos devem ser abordadas caso a caso dentro do ambiente em nuvem de pré-visualização da Solução Azure VMware. Os limites do cluster e do hospedeiro numa nuvem privada são fornecidos [no artigo de conceito de nuvem privada.](concepts-private-clouds-clusters.md)

Neste tutorial, você usa o portal Azure para:

> [!div class="checklist"]
> * Adicione um cluster a uma nuvem privada existente
> * Adicione anfitriões a um cluster existente

## <a name="prerequisites"></a>Pré-requisitos

Precisa de uma nuvem privada para completar este tutorial. Se ainda não criou uma nuvem privada, utilize o [tutorial de nuvem privada](tutorial-create-private-cloud.md) para criar uma nuvem privada e configurar a rede para a sua nuvem privada VMware em Azure para configurar a rede virtual necessária.

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

   A adição de anfitriões ao cluster começará.

## <a name="next-steps"></a>Passos seguintes

Se necessitar de outra nuvem privada Azure VMware Solution, [crie outra nuvem privada,](tutorial-create-private-cloud.md)seguindo os mesmos pré-requisitos de rede, cluster e limites de hospedeiro.

<!-- LINKS - external-->

<!-- LINKS - internal -->
