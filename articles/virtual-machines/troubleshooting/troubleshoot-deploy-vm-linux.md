---
title: Resolução de problemas implementando problemas de máquina virtual Linux em Azure Microsoft Docs
description: Resolução de problemas implementando problemas de máquina virtual Linux no modelo de implementação do Gestor de Recursos Azure.
services: virtual-machines-windows
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 4e383427-4aff-4bf3-a0f4-dbff5c6f0c81
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.topic: troubleshooting
ms.date: 11/01/2018
ms.author: genli
ms.openlocfilehash: cf8fb383625ec2752264d6e5a70d8625f06689fc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "82628304"
---
# <a name="troubleshoot-deploying-linux-virtual-machine-issues-in-azure"></a>Resolver problemas de implementação de máquinas virtuais do Linux no Azure

Para resolver problemas de implementação de máquinas virtuais (VM) em Azure, reveja as [principais questões](#top-issues) para falhas e resoluções comuns.

Se precisar de mais ajuda em qualquer ponto deste artigo, pode contactar os especialistas da Azure nos [fóruns msdn Azure e Stack Overflow](https://azure.microsoft.com/support/forums/). Em alternativa, pode apresentar um incidente de suporte Azure. Vá ao [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione Obter **Apoio**.

## <a name="top-issues"></a>Principais questões
[!INCLUDE [virtual-machines-linux-troubleshoot-deploy-vm-top](../../../includes/virtual-machines-linux-troubleshoot-deploy-vm-top.md)]

## <a name="the-cluster-cannot-support-the-requested-vm-size"></a>O cluster não pode suportar o tamanho VM solicitado
\<properties
supportTopicIds="123456789"
resourceTags="windows"
productPesIds="1234, 5678"
/>
- Reda o pedido utilizando um tamanho VM menor.
- Se o tamanho do VM solicitado não puder ser alterado:
    - Pare todos os VMs no conjunto de disponibilidade. Clique em **grupos de recursos** > o seu grupo de recursos > **Recursos** > o seu conjunto de disponibilidade > **Máquinas Virtuais** > a sua máquina virtual > **Parar.**
    - Depois de todas as paragens de VMs, crie o VM no tamanho desejado.
    - Inicie primeiro o novo VM e, em seguida, selecione cada um dos VMs parados e clique em Iniciar.


## <a name="the-cluster-does-not-have-free-resources"></a>O cluster não tem recursos livres
\<properties
supportTopicIds="123456789"
resourceTags="windows"
productPesIds="1234, 5678"
/>
- Recandidutar o pedido mais tarde.
- Se o novo VM pode fazer parte de um conjunto de disponibilidade diferente
    - Criar um VM num conjunto de disponibilidade diferente (na mesma região).
    - Adicione o novo VM à mesma rede virtual.

## <a name="how-do-i-activate-my-monthly-credit-for-visual-studio-enterprise-bizspark"></a>Como ativar o meu crédito mensal para o estúdio Visual Enterprise (BizSpark)

Para ativar o seu crédito mensal, consulte este [artigo.](https://azure.microsoft.com/offers/ms-azr-0064p/)

## <a name="why-can-i-not-install-the-gpu-driver-for-an-ubuntu-nv-vm"></a>Por que não posso instalar o controlador gpu para um VM Ubuntu NV?

Atualmente, o suporte ao GPU do Linux só está disponível em VMs Azure NC com o Ubuntu Server 16.04 LTS. Para obter mais informações, consulte [configurar os controladores de GPU para VMs da série N que executam o Linux.](../linux/n-series-driver-setup.md)

## <a name="my-drivers-are-missing-for-my-linux-n-series-vm"></a>Os meus pilotos estão desaparecidos para o meu Linux N-Series VM

As instruções para instalar os controladores para VMs baseados em Linux estão localizadas [aqui.](../sizes-gpu.md#supported-operating-systems-and-drivers)

## <a name="i-cant-find-a-gpu-instance-within-my-n-series-vm"></a>Não consigo encontrar uma instância gpu dentro do meu VM n-series

Para tirar partido das capacidades da GPU dos VMs da série Azure N, tem de instalar controladores gráficos em cada VM após a implementação. As informações de configuração do condutor estão disponíveis [aqui.](../sizes-gpu.md#supported-operating-systems-and-drivers)

## <a name="are-n-series-vms-available-in-my-region"></a>Os VMs da Série N estão disponíveis na minha região?

Pode consultar a disponibilidade dos [Produtos disponíveis por mesa da região,](https://azure.microsoft.com/regions/services)e os preços [aqui.](https://azure.microsoft.com/pricing/details/virtual-machines/series/#n-series)

## <a name="i-am-not-able-to-see-vm-size-family-that-i-want-when-resizing-my-vm"></a>Não consigo ver a família VM Size que quero quando redimensionar o meu VM.

Quando um VM está em funcionamento, é implantado num servidor físico. Os servidores físicos nas regiões de Azure estão agrupados em aglomerados de hardware físico comum. Redimensionar um VM que requer que o VM seja movido para diferentes clusters de hardware é diferente dependendo do modelo de implementação usado para implantar o VM.

- VMs implantados no modelo de implantação Clássico, a implementação do serviço de nuvem deve ser removida e redistribuída para alterar os VMs para um tamanho em outra família de tamanho.

- VMs implantados no modelo de implementação do Gestor de Recursos, deve parar todos os VMs no conjunto de disponibilidade antes de alterar o tamanho de qualquer VM no conjunto de disponibilidade.

## <a name="the-listed-vm-size-is-not-supported-while-deploying-in-availability-set"></a>O tamanho VM listado não é suportado durante a implementação no Conjunto de Disponibilidade.

Escolha um tamanho suportado no cluster do conjunto de disponibilidade. Recomenda-se ao criar um conjunto de disponibilidade para escolher o maior tamanho de VM que você acha que precisa, e ter que ser a sua primeira implementação para o conjunto Disponibilidade.

## <a name="what-linux-distributionsversions-are-supported-on-azure"></a>Quais as distribuições/versões Linux suportadas no Azure?

Pode encontrar a lista no Linux em [Distribuições Apoiadas pela Azure.](../linux/endorsed-distros.md)

## <a name="can-i-add-an-existing-classic-vm-to-an-availability-set"></a>Posso adicionar um VM clássico existente a um conjunto de disponibilidade?

Sim. Pode adicionar um VM clássico existente a um conjunto de disponibilidade novo ou existente. Para obter mais [informações, adicione uma máquina virtual existente a um conjunto de disponibilidade.](/previous-versions/azure/virtual-machines/windows/classic/configure-availability-classic#addmachine)

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

## <a name="next-steps"></a>Passos seguintes
Se precisar de mais ajuda em qualquer ponto deste artigo, pode contactar os especialistas da Azure nos [fóruns msdn Azure e Stack Overflow](https://azure.microsoft.com/support/forums/).

Em alternativa, pode apresentar um incidente de suporte Azure. Vá ao [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione Obter **Apoio**.
