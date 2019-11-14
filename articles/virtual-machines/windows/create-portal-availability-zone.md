---
title: Criar uma VM do Windows zoneada com o portal do Azure
description: Criar uma VM do Windows em uma zona de disponibilidade com o portal do Azure
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
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74033883"
---
# <a name="create-a-windows-virtual-machine-in-an-availability-zone-with-the-azure-portal"></a>Criar uma máquina virtual do Windows em uma zona de disponibilidade com o portal do Azure

Este artigo percorre o uso do portal do Azure para criar uma máquina virtual em uma zona de disponibilidade do Azure. Uma [zona de disponibilidade](../../availability-zones/az-overview.md) é uma zona separada fisicamente numa região do Azure. Utilize as zonas de disponibilidade para proteger as aplicações e os dados de uma falha pouco provável ou da perda de um datacenter completo.

Para utilizar uma zona de disponibilidade, crie a máquina virtual numa [região suportada do Azure](../../availability-zones/az-overview.md#services-support-by-region).

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure 

Inicie sessão no portal do Azure em https://portal.azure.com.

## <a name="create-virtual-machine"></a>Criar a máquina virtual

1. Clique em **Criar um recurso**, no canto superior esquerdo do portal do Azure.

2. Selecione **Computação** e, em seguida, selecione **Windows Server 2016 Datacenter**. 

3. Introduza as informações da máquina virtual. O nome de utilizador e a palavra-passe introduzidos aqui são utilizados para iniciar sessão na máquina virtual. A palavra-passe tem de ter, pelo menos, 12 carateres e cumprir os [requisitos de complexidade definidos](faq.md#what-are-the-password-requirements-when-creating-a-vm). Escolha um local como leste dos EUA 2 que ofereça suporte a zonas de disponibilidade. Quando terminar, clique em **OK**.

    ![Introduza as informações básicas sobre a VM no painel do portal](./media/create-portal-availability-zone/create-windows-vm-portal-basic-blade.png)

4. Escolha um tamanho para a VM. Selecione um tamanho recomendado ou filtre com base nos recursos. Confirme se o tamanho está disponível na zona que você deseja usar.

    ![Selecione um tamanho de VM](./media/create-portal-availability-zone/create-windows-vm-portal-sizes.png)  

5. Em **configurações** > **alta disponibilidade**, selecione uma das zonas numeradas na lista suspensa **zona de disponibilidade** , mantenha os padrões restantes e clique em **OK**.

    ![Selecionar uma zona de disponibilidade](./media/create-portal-availability-zone/create-windows-vm-portal-availability-zone.png)

6. Na página Resumo, clique em **criar** para iniciar a implantação da máquina virtual.

7. A VM será afixada ao dashboard do portal do Azure. Depois de concluída a implementação, o resumo da VM abre-se automaticamente.

## <a name="confirm-zone-for-managed-disk-and-ip-address"></a>Confirmar zona para disco gerenciado e endereço IP

Quando a VM é implantada em uma zona de disponibilidade, um disco gerenciado para a VM é criado na mesma zona de disponibilidade. Por padrão, um endereço IP público também é criado nessa zona.

Você pode confirmar as configurações de zona para esses recursos no Portal.  

1. Clique em **grupos de recursos** e no nome do grupo de recursos da VM, como *MyResource*Group.

2. Clique no nome do recurso de disco. A página **visão geral** inclui detalhes sobre o local e a zona de disponibilidade do recurso.

    ![Zona de disponibilidade para o disco gerenciado](./media/create-portal-availability-zone/create-windows-vm-portal-disk.png)

3. Clique no nome do recurso de endereço IP público. A página **visão geral** inclui detalhes sobre o local e a zona de disponibilidade do recurso.

    ![Zona de disponibilidade para endereço IP](./media/create-portal-availability-zone/create-windows-vm-portal-ip.png)



## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu a criar uma VM numa zona de disponibilidade. Saiba mais sobre a [disponibilidade](availability.md) de VMs do Azure.
