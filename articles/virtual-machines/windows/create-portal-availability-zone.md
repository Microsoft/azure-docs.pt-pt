---
title: Criar um VM zonado com o portal Azure
description: Criar um VM windows em zona de disponibilidade com o portal Azure
documentationcenter: virtual-machines
author: mimckitt
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 3/8/2021
ms.author: mimckitt
ms.reviewer: cynthn
ms.custom: ''
ms.openlocfilehash: 7c7f135d4033a31f855342c172d73f51478931ab
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/09/2021
ms.locfileid: "102501690"
---
# <a name="create-a-virtual-machine-in-an-availability-zone-using-the-azure-portal"></a>Criar uma máquina virtual numa zona de disponibilidade utilizando o portal Azure

Este artigo passa pela utilização do portal Azure para criar uma máquina virtual numa zona de disponibilidade Azure. Uma [zona de disponibilidade](../../availability-zones/az-overview.md) é uma zona separada fisicamente numa região do Azure. Utilize as zonas de disponibilidade para proteger as aplicações e os dados de uma falha pouco provável ou da perda de um datacenter completo.

Para utilizar uma zona de disponibilidade, crie a máquina virtual numa [região suportada do Azure](../../availability-zones/az-region.md).

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure 

Inicie sessão no portal do Azure em https://portal.azure.com.

## <a name="create-virtual-machine"></a>Criar a máquina virtual

1. Clique em **Criar um recurso** no canto superior esquerdo do portal Azure.

2. Selecione **Computação** e, em seguida, selecione **Windows Server 2016 Datacenter**. 

3. Introduza as informações da máquina virtual. O nome de utilizador e a palavra-passe introduzidos aqui são utilizados para iniciar sessão na máquina virtual. A palavra-passe deve ter pelo menos 12 caracteres de comprimento e satisfazer os [requisitos de complexidade definidos](faq.md#what-are-the-password-requirements-when-creating-a-vm). Escolha uma localização como East US 2 que suporte zonas de disponibilidade. Quando terminar, clique em **OK**.

    ![Introduza as informações básicas sobre a VM no painel do portal](./media/create-portal-availability-zone/create-windows-vm-portal-basic-blade.png)

4. Escolha um tamanho para a VM. Selecione um tamanho recomendado ou filtro com base em funcionalidades. Confirme que o tamanho está disponível na zona que pretende utilizar.

    ![Selecione um tamanho VM](./media/create-portal-availability-zone/create-windows-vm-portal-sizes.png)  

5. Em **Definições**  >  **Alta disponibilidade**, selecione uma das zonas numeradas a partir do dropdown da zona de **disponibilidade,** mantenha os predefinidos restantes e clique em **OK**.

    ![Selecione uma zona de disponibilidade](./media/create-portal-availability-zone/create-windows-vm-portal-availability-zone.png)

6. Na página do resumo, clique em **Criar** para iniciar a implementação da máquina virtual.

7. A VM será afixada ao dashboard do portal do Azure. Depois de concluída a implementação, o resumo da VM abre-se automaticamente.

## <a name="confirm-zone-for-managed-disk-and-ip-address"></a>Confirme zona para o disco gerido e endereço IP

Quando o VM é implantado numa zona de disponibilidade, é criado um disco gerido para o VM na mesma zona de disponibilidade. Por padrão, um endereço IP público também é criado nessa zona.

Pode confirmar as definições de zona para estes recursos no portal.  

1. Clique **em grupos de recursos** e, em seguida, o nome do grupo de recursos para o VM, como o *myResourceGroup*.

2. Clique no nome do recurso Disco. A **página 'Vista Geral'** inclui detalhes sobre a localização e zona de disponibilidade do recurso.

    ![Zona de disponibilidade para disco gerido](./media/create-portal-availability-zone/create-windows-vm-portal-disk.png)

3. Clique no nome do endereço IP público. A **página 'Vista Geral'** inclui detalhes sobre a localização e zona de disponibilidade do recurso.

    ![Zona de disponibilidade para endereço IP](./media/create-portal-availability-zone/create-windows-vm-portal-ip.png)



## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu a criar uma VM numa zona de disponibilidade. Saiba mais sobre [a disponibilidade](../availability.md) para VMs Azure.