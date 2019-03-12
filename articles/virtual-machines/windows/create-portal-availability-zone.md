---
title: Criar uma VM do Windows com zona definida com o portal do Azure | Documentos da Microsoft
description: Criar uma VM do Windows numa zona de disponibilidade com o portal do Azure
services: virtual-machines-windows
documentationcenter: virtual-machines
author: dlepow
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 03/27/2018
ms.author: danlep
ms.custom: ''
ms.openlocfilehash: 23d1e53785ece50943d732db12b4cf460ba6752a
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57767486"
---
# <a name="create-a-windows-virtual-machine-in-an-availability-zone-with-the-azure-portal"></a>Criar uma máquina virtual do Windows numa zona de disponibilidade com o portal do Azure

Este artigo explica através do portal do Azure para criar uma máquina virtual numa zona de disponibilidade do Azure. Uma [zona de disponibilidade](../../availability-zones/az-overview.md) é uma zona separada fisicamente numa região do Azure. Utilize as zonas de disponibilidade para proteger as aplicações e os dados de uma falha pouco provável ou da perda de um datacenter completo.

Para utilizar uma zona de disponibilidade, crie a máquina virtual numa [região suportada do Azure](../../availability-zones/az-overview.md#regions-that-support-availability-zones).

## <a name="log-in-to-azure"></a>Iniciar sessão no Azure 

Inicie sessão no portal do Azure em https://portal.azure.com.

## <a name="create-virtual-machine"></a>Criar máquina virtual

1. Clique em **Criar um recurso**, no canto superior esquerdo do portal do Azure.

2. Selecione **Computação** e, em seguida, selecione **Windows Server 2016 Datacenter**. 

3. Introduza as informações da máquina virtual. O nome de utilizador e palavra-passe introduzidos aqui são utilizados para iniciar sessão na máquina virtual. A palavra-passe tem de ter, pelo menos, 12 carateres e cumprir os [requisitos de complexidade definidos](faq.md#what-are-the-password-requirements-when-creating-a-vm). Escolha uma localização, como os E.U.A. Leste 2, que suporta zonas de disponibilidade. Quando terminar, clique em **OK**.

    ![Introduza as informações básicas sobre a VM no painel do portal](./media/create-portal-availability-zone/create-windows-vm-portal-basic-blade.png)

4. Escolha um tamanho para a VM. Selecione um tamanho recomendado ou filtro com base nos recursos. Certifique-se que o tamanho está disponível na zona de que pretende utilizar.

    ![Selecione um tamanho de VM](./media/create-portal-availability-zone/create-windows-vm-portal-sizes.png)  

5. Sob **configurações** > **elevada disponibilidade**, selecione uma das zonas de numerados do **zona de disponibilidade** lista pendente, mantenha as predefinições restantes, e Clique em **OK**.

    ![Selecione a zona de disponibilidade](./media/create-portal-availability-zone/create-windows-vm-portal-availability-zone.png)

6. Na página Resumo, clique em **criar** para iniciar a implementação da máquina virtual.

7. A VM será afixada ao dashboard do portal do Azure. Depois de concluída a implementação, o resumo da VM abre-se automaticamente.

## <a name="confirm-zone-for-managed-disk-and-ip-address"></a>Confirmar a zona do disco gerido e o endereço IP

Quando a VM é implementada numa zona de disponibilidade, um disco gerido para a VM é criado na mesma zona de disponibilidade. Por predefinição, um endereço IP público também é criado nessa zona.

Pode confirmar as definições de zona para estes recursos no portal.  

1. Clique em **grupos de recursos** e, em seguida, o nome do recurso de grupo para a VM, tal como *myResourceGroup*.

2. Clique no nome do recurso de disco. O **descrição geral** página inclui detalhes sobre a localização e zona de disponibilidade do recurso.

    ![Zona de disponibilidade do disco gerido](./media/create-portal-availability-zone/create-windows-vm-portal-disk.png)

3. Clique no nome do recurso de endereço IP público. O **descrição geral** página inclui detalhes sobre a localização e zona de disponibilidade do recurso.

    ![Zona de disponibilidade para o endereço IP](./media/create-portal-availability-zone/create-windows-vm-portal-ip.png)



## <a name="next-steps"></a>Passos Seguintes

Neste artigo, aprendeu a criar uma VM numa zona de disponibilidade. Saiba mais sobre [regiões e disponibilidade](regions-and-availability.md) para VMs do Azure.
