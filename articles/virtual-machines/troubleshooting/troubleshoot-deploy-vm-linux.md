---
title: Resolver problemas de implantação problemas de máquina virtual do Linux no Azure | Documentos da Microsoft
description: Resolver problemas de implantação problemas de máquina virtual do Linux no modelo de implementação do Gestor de recursos de Azurethe.
services: virtual-machines-windows
documentationcenter: ''
author: genlin
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 4e383427-4aff-4bf3-a0f4-dbff5c6f0c81
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 11/01/2018
ms.author: genli
ms.openlocfilehash: 45e15fa5b17f75dcc6cfdc7305861d38804f1266
ms.sourcegitcommit: 6678e16c4b273acd3eaf45af310de77090137fa1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/01/2018
ms.locfileid: "50748141"
---
# <a name="troubleshoot-deploying-linux-virtual-machine-issues-in-azure"></a>Resolver problemas de implantação problemas de máquina virtual do Linux no Azure

Para resolver problemas de implementação de máquina virtual (VM) no Azure, reveja os [principais problemas](#top-issues) para falhas e resoluções comuns.

Se precisar de mais ajuda a qualquer momento neste artigo, pode contactar os especialistas do Azure no [os fóruns do Azure do MSDN e Stack Overflow](https://azure.microsoft.com/support/forums/). Em alternativa, pode enviar um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione **obter suporte**.

## <a name="top-issues"></a>Principais problemas
[!INCLUDE [virtual-machines-linux-troubleshoot-deploy-vm-top](../../../includes/virtual-machines-linux-troubleshoot-deploy-vm-top.md)]

## <a name="the-cluster-cannot-support-the-requested-vm-size"></a>O cluster não suporta o tamanho da VM pedida
<properties
supportTopicIds="123456789"
resourceTags="windows"
productPesIds="1234, 5678"
/>
- Repita o pedido com um tamanho VM mais pequeno.
- Se não é possível alterar o tamanho da VM pedida:
    - Pare todas as VMs no conjunto de disponibilidade. Clique em **grupos de recursos** > seu grupo de recursos > **recursos** > seu conjunto de disponibilidade > **máquinas virtuais** > sua máquina virtual >  **Parar**.
    - Depois de parar todas as VMs, crie a VM no tamanho pretendido.
    - Em primeiro lugar, a iniciar a nova VM e, em seguida, selecione cada uma das VMs paradas e clique em Iniciar.


## <a name="the-cluster-does-not-have-free-resources"></a>O cluster não tem recursos gratuitos
<properties
supportTopicIds="123456789"
resourceTags="windows"
productPesIds="1234, 5678"
/>
- Repita o pedido mais tarde.
- Se a nova VM pode fazer parte de um conjunto de disponibilidade diferente
    - Crie uma VM num conjunto (na mesma região) de disponibilidade diferente.
    - Adicione a nova VM a mesma rede virtual.

## <a name="how-do-i-activate-my-monthly-credit-for-visual-studio-enterprise-bizspark"></a>Como posso ativar o meu crédito mensal para Visual studio Enterprise (BizSpark)

Para ativar o seu crédito mensal, veja este [artigo](https://azure.microsoft.com/offers/ms-azr-0064p/).

## <a name="why-can-i-not-install-the-gpu-driver-for-an-ubuntu-nv-vm"></a>Por que eu não conseguir instalar o controlador GPU para uma VM do Ubuntu NV?

Atualmente, o suporte de Linux GPU só está disponível em VMs do NC do Azure a executar o Ubuntu Server 16.04 LTS. Para obter mais informações, consulte [configurar controladores GPU para VMs de série N que executem o Linux](../linux/n-series-driver-setup.md).

## <a name="my-drivers-are-missing-for-my-linux-n-series-vm"></a>Meus controladores estão em falta para a minha VM de série N do Linux

Controladores para VMs baseadas em Linux estão localizadas [aqui](../linux/n-series-driver-setup.md). 

## <a name="i-cant-find-a-gpu-instance-within-my-n-series-vm"></a>Não consigo encontrar uma instância GPU dentro de minha VM de série N

Para tirar partido das capacidades de GPU de VMs de série N do Azure com o Windows Server 2016 ou Windows Server 2012 R2, tem de instalar drivers de gráficos NVIDIA em cada VM após a implementação. Informações de configuração do controlador estão disponíveis para [VMs do Windows](../windows/n-series-driver-setup.md) e [VMs do Linux](../linux/n-series-driver-setup.md).

## <a name="is-n-series-vms-available-in-my-region"></a>VMs de série N está disponível na minha região?

Pode verificar a disponibilidade a partir do [produtos disponíveis por tabela region](https://azure.microsoft.com/regions/services)e preços [aqui](https://azure.microsoft.com/pricing/details/virtual-machines/series/#n-series).

## <a name="i-am-not-able-to-see-vm-size-family-that-i-want-when-resizing-my-vm"></a>Eu não sou capaz de ver a família de tamanho de VM que desejo durante o redimensionamento de minha VM.

Quando uma VM está em execução, ele é implementado um servidor físico. Os servidores físicos em regiões do Azure são agrupados em clusters de hardware físico comum. Redimensionar uma VM que requer que a VM de ser movidos para clusters de hardware diferentes é diferente, dependendo de qual modelo de implementação foi utilizado para implementar a VM.

- As VMs implementadas num modelo de implementação clássica, a implementação de serviço em nuvem tem de ser removidas e implementado novamente para alterar as VMs para um tamanho em outra família de tamanho.

- As VMs implementadas no modelo de implementação do Resource Manager, tem de parar todas as VMs no conjunto antes de alterar o tamanho de qualquer VM no conjunto de disponibilidade de disponibilidade.

## <a name="the-listed-vm-size-is-not-supported-while-deploying-in-availability-set"></a>O tamanho da VM listado não é suportado durante a implementação no conjunto de disponibilidade.

Escolha um tamanho que é suportado num cluster de conjunto de disponibilidade. Recomenda-se quando criar um disponibilidade definida para escolher o maior tamanho da VM acha que precisa e tem que ser sua primeira implementação para o conjunto de disponibilidade.

## <a name="what-linux-distributionsversions-are-supported-on-azure"></a>Quais as distribuições de Linux/versões são suportadas no Azure?

Pode encontrar a lista em Linux no [Azure-Endorsed distribuições](../linux/endorsed-distros.md).

## <a name="can-i-add-an-existing-classic-vm-to-an-availability-set"></a>Pode adicionar uma VM clássica existente para um conjunto de disponibilidade?

Sim. Pode adicionar uma VM clássica existente para um novo ou existente do conjunto de disponibilidade. Para obter mais informações, consulte [adicionar uma máquina virtual existente a um conjunto de disponibilidade](../windows/classic/configure-availability-classic.md#addmachine).


## <a name="next-steps"></a>Passos Seguintes
Se precisar de mais ajuda a qualquer momento neste artigo, pode contactar os especialistas do Azure no [os fóruns do Azure do MSDN e Stack Overflow](https://azure.microsoft.com/support/forums/).

Em alternativa, pode enviar um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione **obter suporte**.
