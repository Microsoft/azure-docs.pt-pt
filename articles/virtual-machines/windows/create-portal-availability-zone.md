---
title: Criar um VM do Windows com o portal Azure
description: Crie um VM Windows numa zona de disponibilidade com o portal Azure
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 03/27/2018
ms.author: cynthn
ms.custom: ''
ms.openlocfilehash: c6f843693ccfa16f9b31027ba370242e0462b138
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74033883"
---
# <a name="create-a-windows-virtual-machine-in-an-availability-zone-with-the-azure-portal"></a>Criar uma máquina virtual windows em uma zona de disponibilidade com o portal Azure

Este artigo passa pela utilização do portal Azure para criar uma máquina virtual numa zona de disponibilidade Azure. Uma [zona de disponibilidade](../../availability-zones/az-overview.md) é uma zona separada fisicamente numa região do Azure. Utilize as zonas de disponibilidade para proteger as aplicações e os dados de uma falha pouco provável ou da perda de um datacenter completo.

Para utilizar uma zona de disponibilidade, crie a máquina virtual numa [região suportada do Azure](../../availability-zones/az-overview.md#services-support-by-region).

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure 

Inicie sessão no portal do Azure em https://portal.azure.com.

## <a name="create-virtual-machine"></a>Criar a máquina virtual

1. Clique em **Criar um recurso** no canto superior esquerdo do portal Azure.

2. Selecione **Computação** e, em seguida, selecione **Windows Server 2016 Datacenter**. 

3. Introduza as informações da máquina virtual. O nome de utilizador e a palavra-passe introduzidos aqui são utilizados para iniciar sessão na máquina virtual. A palavra-passe tem de ter, pelo menos, 12 carateres e cumprir os [requisitos de complexidade definidos](faq.md#what-are-the-password-requirements-when-creating-a-vm). Escolha um Local como East US 2 que suporte zonas de disponibilidade. Quando terminar, clique em **OK**.

    ![Introduza as informações básicas sobre a VM no painel do portal](./media/create-portal-availability-zone/create-windows-vm-portal-basic-blade.png)

4. Escolha um tamanho para a VM. Selecione um tamanho recomendado ou filtro com base em funcionalidades. Confirme que o tamanho está disponível na zona que pretende utilizar.

    ![Selecione um tamanho VM](./media/create-portal-availability-zone/create-windows-vm-portal-sizes.png)  

5. Em **Definições** > **Alta disponibilidade**, selecione uma das zonas numeradas a partir da desistência da zona de **disponibilidade,** mantenha os restantes predefinições e clique em **OK**.

    ![Selecione uma zona de disponibilidade](./media/create-portal-availability-zone/create-windows-vm-portal-availability-zone.png)

6. Na página de resumo, clique em **Criar** para iniciar a implementação da máquina virtual.

7. A VM será afixada ao dashboard do portal do Azure. Depois de concluída a implementação, o resumo da VM abre-se automaticamente.

## <a name="confirm-zone-for-managed-disk-and-ip-address"></a>Confirme a zona para o disco gerido e endereço IP

Quando o VM é implantado numa zona de disponibilidade, um disco gerido para o VM é criado na mesma zona de disponibilidade. Por padrão, é também criado um endereço IP público nessa zona.

Pode confirmar as definições de zona para estes recursos no portal.  

1. Clique em **grupos de recursos** e, em seguida, o nome do grupo de recursos para o VM, como o *myResourceGroup*.

2. Clique no nome do recurso Disk. A página **'Overview'** inclui detalhes sobre a localização e zona de disponibilidade do recurso.

    ![Zona de disponibilidade para disco gerido](./media/create-portal-availability-zone/create-windows-vm-portal-disk.png)

3. Clique no nome do recurso de endereço IP público. A página **'Overview'** inclui detalhes sobre a localização e zona de disponibilidade do recurso.

    ![Zona de disponibilidade para endereço IP](./media/create-portal-availability-zone/create-windows-vm-portal-ip.png)



## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu a criar uma VM numa zona de disponibilidade. Saiba mais sobre [disponibilidade](availability.md) para VMs Azure.
