---
title: Implementar anfitriões dedicados azure usando o portal Azure
description: Implemente VMs para anfitriões dedicados usando o portal Azure.
author: cynthn
ms.service: virtual-machines
ms.topic: article
ms.workload: infrastructure
ms.date: 03/10/2020
ms.author: cynthn
ms.openlocfilehash: 195a19ef881f235ad8e42f23b53da9e667ef88d0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79086751"
---
# <a name="deploy-vms-to-dedicated-hosts-using-the-portal"></a>Implementar VMs para anfitriões dedicados usando o portal

Este artigo guia-o através de como criar um [anfitrião dedicado](dedicated-hosts.md) azure para hospedar as suas máquinas virtuais (VMs). 

[!INCLUDE [virtual-machines-common-dedicated-hosts-portal](../../../includes/virtual-machines-common-dedicated-hosts-portal.md)]

## <a name="create-a-vm"></a>Criar uma VM

1. Selecione **Criar um recurso** no canto superior esquerdo do portal do Azure.
1. Na caixa de pesquisa acima da lista de recursos do Azure Marketplace, procure e selecione **Ubuntu Server 16.04 LTS** da Canonical e, em seguida, selecione **Criar**.
1. No separador **Basics,** sob os detalhes do **Projeto,** certifique-se de que a subscrição correta é selecionada e, em seguida, selecione *myDedicatedHostsRG* como o **grupo Recursos**. 
1. Em **Detalhes da instância**, escreva *aminhaVM* para o **Nome da máquina virtual** e selecione *E.U.A. Leste* para a **Localização**.
1. Nas **opções de disponibilidade** selecione Zona de **disponibilidade**, selecione *1* a partir do drop-down.
1. Para o tamanho, selecione **Tamanho de mudança**. Na lista de tamanhos disponíveis, escolha um da série Esv3, como **o Standard E2s v3**. Pode ser necessário limpar o filtro para ver todos os tamanhos disponíveis.
1. Em **Conta de administrador**, selecione **Chave pública SSH**, escreva o seu nome de utilizador e, em seguida, cole a chave pública na caixa de texto. Remova quaisquer espaços em branco à esquerda ou à direita na chave pública.

    ![Conta de administrador](./media/quick-create-portal/administrator-account.png)

1. De acordo com **as regras** > de entrada nas portas de**entrada,** escolha **permitir portas selecionadas** e, em seguida, selecione **SSH (22)** a partir da queda. 
1. No topo da página, selecione o separador **Avançado** e na secção **Anfitrião,** selecione *myHostGroup* para **host group** e *myHost* for the **Host**. 
    ![Selecione grupo anfitrião e anfitrião](./media/dedicated-hosts-portal/advanced.png)
1. Mantenha as restantes predefinições e, em seguida, selecione o botão **Rever + criar** na parte inferior da página.
1. Quando vir a mensagem que a validação passou, selecione **Criar**.

Irá demorar alguns minutos até a VM ser implementada.

## <a name="add-an-existing-vm"></a>Adicione um VM existente 

Você pode adicionar um VM de saída a um anfitrião dedicado, mas o VM deve primeiro ser Stop\Deallocated. Antes de mover um VM para um anfitrião dedicado, certifique-se de que a configuração VM é suportada:

- O tamanho VM deve ser do mesmo tamanho que o hospedeiro dedicado. Por exemplo, se o seu anfitrião dedicado for DSv3, então o tamanho VM pode ser Standard_D4s_v3, mas não poderia ser um Standard_A4_v2. 
- O VM precisa de ser localizado na mesma região que o anfitrião dedicado.
- O VM não pode fazer parte de um grupo de colocação de proximidade. Retire o VM do grupo de colocação de proximidade antes de o transferir para um hospedeiro dedicado. Para mais informações, consulte [Move a VM de um grupo de colocação de proximidade](https://docs.microsoft.com/azure/virtual-machines/windows/proximity-placement-groups#move-an-existing-vm-out-of-a-proximity-placement-group)
- O VM não pode estar num conjunto de disponibilidade.
- Se o VM estiver numa zona de disponibilidade, deve ser a mesma zona de disponibilidade que o grupo anfitrião. As definições da zona de disponibilidade para o VM e o grupo anfitrião devem corresponder.

Mova o VM para um hospedeiro dedicado usando o [portal](https://portal.azure.com).

1. Abra a página para o VM.
1. Selecione **Stop** para parar\deslocar o VM.
1. **Selecione Configuração** a partir do menu esquerdo.
1. Selecione um grupo de anfitriões e um anfitrião dos menus suspensos.
1. Quando terminar, selecione **Guardar** no topo da página.
1. Depois de adicionado o VM ao anfitrião, selecione **a visão geral** do menu esquerdo.
1. No topo da página, selecione **Iniciar** a reiniciar o VM.

## <a name="next-steps"></a>Passos seguintes

- Para mais informações, consulte a visão geral dos [anfitriões dedicados.](dedicated-hosts.md)

- Há um modelo de amostra, encontrado [aqui,](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md)que usa tanto zonas como domínios de falha para a máxima resiliência numa região.

- Também pode implantar um hospedeiro dedicado utilizando o [Azure CLI](dedicated-hosts-cli.md).



