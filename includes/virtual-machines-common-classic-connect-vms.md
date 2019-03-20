---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 55e46e058bddca717929df61b2bc766b89e0f885
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58122761"
---
![Máquinas virtuais num serviço cloud autónomo](./media/virtual-machines-common-classic-connect-vms/CloudServiceExample.png)

Se colocar as máquinas virtuais numa rede virtual, pode decidir quantos serviços em nuvem que pretende utilizar para conjuntos de disponibilidade e balanceamento de carga. Além disso, pode organizar as máquinas virtuais em sub-redes da mesma forma como o local de rede e ligar a rede virtual à sua rede no local. Segue-se um exemplo:

![Máquinas virtuais numa rede virtual](./media/virtual-machines-common-classic-connect-vms/VirtualNetworkExample.png)

Redes virtuais são a forma recomendada para ligar a máquinas virtuais no Azure. A melhor prática é configurar cada camada da aplicação num serviço cloud separadas. No entanto, terá de combinar algumas máquinas de virtuais de camadas da aplicação diferentes no mesmo serviço cloud permaneçam num máximo de 200 serviços cloud por subscrição. Para rever este e outros limites, consulte [subscrição do Azure e limites do serviço, Quotas e restrições](../articles/azure-subscription-service-limits.md).

## <a name="connect-vms-in-a-virtual-network"></a>Ligar as VMs numa rede virtual
Para ligar as máquinas virtuais numa rede virtual:

1. Criar a rede virtual no [portal do Azure](../articles/virtual-network/virtual-networks-create-vnet-classic-pportal.md) e especifique "implementação clássica".
2. Crie um conjunto de serviços cloud para a sua implementação refletir o seu design para conjuntos de disponibilidade e balanceamento de carga. No portal do Azure, clique em **criar um recurso > computação > serviço em nuvem** para cada serviço em nuvem.

   Quando preencher os detalhes do serviço de nuvem, selecione a mesma _grupo de recursos_ utilizado com a rede virtual.

3. Para criar cada nova máquina virtual, clique em **criar um recurso > computação**, em seguida, selecione a imagem VM adequada da **aplicações em destaque**.

   Na VM **Noções básicas** painel, selecione a mesma _grupo de recursos_ utilizado com a rede virtual.

   ![Painel Noções básicas de VM ao utilizar uma VNet](./media/virtual-machines-common-classic-connect-vms/CreateVM_Basics_VN.png)

4. Quando preencher a VM **definições**, escolha o correto _serviço Cloud_ ou _rede virtual_ para a VM.

   Azure selecionará o outro item com base na sua seleção.

   ![Painel de definições de VM ao utilizar uma VNet](./media/virtual-machines-common-classic-connect-vms/CreateVM_Settings_VN.png)


## <a name="connect-vms-in-a-standalone-cloud-service"></a>Ligar as VMs num serviço cloud autónomo
Para ligar as máquinas virtuais num serviço cloud autónomo:

1. Criar o serviço em nuvem do [portal do Azure](http://portal.azure.com). Clique em **novo > computação > serviço em nuvem**. Em alternativa, pode criar o serviço em nuvem para a sua implementação ao criar a sua primeira máquina virtual.
2. Quando cria as máquinas virtuais, selecione o mesmo grupo de recursos utilizado com o serviço em nuvem.

   ![Adicionar uma máquina virtual para um serviço cloud existente](./media/virtual-machines-common-classic-connect-vms/CreateVM_Basics_SA.png)

3. Quando preencher os detalhes da VM, escolha o nome do serviço em nuvem criado na primeira etapa.

   ![Selecionar um serviço cloud para uma máquina virtual](./media/virtual-machines-common-classic-connect-vms/CreateVM_Settings_SA.png)
