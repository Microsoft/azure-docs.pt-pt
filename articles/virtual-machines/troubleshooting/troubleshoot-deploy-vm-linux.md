---
title: Problemas de resolução de problemas com problemas de máquinavirtual Linux em Azure Microsoft Docs
description: Problemas implementando problemas de máquinas virtuais Linux no modelo de implementação do Gestor de Recursos Azure.
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
ms.openlocfilehash: 5a8a58a3935b7cd5efb8565f7e3278ccaae4f4de
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2020
ms.locfileid: "77921441"
---
# <a name="troubleshoot-deploying-linux-virtual-machine-issues-in-azure"></a>Problemas de resolução de problemas implementam problemas de máquinavirtual linux em Azure

Para resolver problemas de implantação de máquinas virtuais (VM) em Azure, reveja as [principais questões](#top-issues) para falhas e resoluções comuns.

Se precisar de mais ajuda em qualquer ponto deste artigo, pode contactar os especialistas do Azure nos [fóruns MSDN Azure e Stack Overflow](https://azure.microsoft.com/support/forums/). Em alternativa, pode enviar um incidente de suporte do Azure. Vá ao site de [suporte azure](https://azure.microsoft.com/support/options/) e selecione **Obter Suporte**.

## <a name="top-issues"></a>Principais questões
[!INCLUDE [virtual-machines-linux-troubleshoot-deploy-vm-top](../../../includes/virtual-machines-linux-troubleshoot-deploy-vm-top.md)]

## <a name="the-cluster-cannot-support-the-requested-vm-size"></a>O cluster não pode suportar o tamanho de VM solicitado
\<propriedades suportamTopicIds="123456789" recursoTags="windows" produtoPesIds="1234, 5678" />
- Tente novamente o pedido utilizando um tamanho VM menor.
- Se o tamanho do VM solicitado não puder ser alterado:
    - Pare todos os VMs no conjunto de disponibilidade. Clique em **Grupos de Recursos** > o seu grupo de recursos > **Recursos** > o seu conjunto de disponibilidade > **Máquinas Virtuais** > a sua máquina virtual > **Stop**.
    - Depois de todos os VMs pararem, crie o VM no tamanho desejado.
    - Inicie primeiro o novo VM e, em seguida, selecione cada um dos VMs parados e clique em Iniciar.


## <a name="the-cluster-does-not-have-free-resources"></a>O cluster não tem recursos livres
\<propriedades suportamTopicIds="123456789" recursoTags="windows" produtoPesIds="1234, 5678" />
- Tente o pedido mais tarde.
- Se o novo VM pode fazer parte de um conjunto de disponibilidade diferente
    - Crie um VM num conjunto de disponibilidade diferente (na mesma região).
    - Adicione o novo VM à mesma rede virtual.

## <a name="how-do-i-activate-my-monthly-credit-for-visual-studio-enterprise-bizspark"></a>Como ativo o meu crédito mensal para o estúdio visual Enterprise (BizSpark)

Para ativar o seu crédito mensal, consulte este [artigo.](https://azure.microsoft.com/offers/ms-azr-0064p/)

## <a name="why-can-i-not-install-the-gpu-driver-for-an-ubuntu-nv-vm"></a>Por que não posso instalar o condutor da GPU para um Ubuntu NV VM?

Atualmente, o suporte da GPU linux só está disponível em VMs Azure NC executando Ubuntu Server 16.04 LTS. Para mais informações, consulte [A configuração de pilotos gpu para VMs série N executando Linux](../linux/n-series-driver-setup.md).

## <a name="my-drivers-are-missing-for-my-linux-n-series-vm"></a>Os meus pilotos estão desaparecidos para o meu Linux N-Series VM

Os condutores de VMs baseados em Linux estão localizados [aqui.](../linux/n-series-driver-setup.md) 

## <a name="i-cant-find-a-gpu-instance-within-my-n-series-vm"></a>Não encontro uma instância de GPU dentro do meu VM série N

Para tirar partido das capacidades de GPU dos VMs da série N Azure que executam o Windows Server 2016 ou o Windows Server 2012 R2, tem de instalar controladores gráficos NVIDIA em cada VM após a implementação. As informações de configuração do controlador estão disponíveis para [VMs windows](../windows/n-series-driver-setup.md) e [VMs Linux](../linux/n-series-driver-setup.md).

## <a name="is-n-series-vms-available-in-my-region"></a>Os VMs da Série N estão disponíveis na minha região?

Pode consultar a disponibilidade dos Produtos disponíveis por mesa da [região](https://azure.microsoft.com/regions/services)e os preços [aqui.](https://azure.microsoft.com/pricing/details/virtual-machines/series/#n-series)

## <a name="i-am-not-able-to-see-vm-size-family-that-i-want-when-resizing-my-vm"></a>Não consigo ver a família VM Size que quero ao redimensionar o meu VM.

Quando um VM está em execução, é implantado num servidor físico. Os servidores físicos nas regiões de Azure estão agrupados em aglomerados de hardware físico comum. Redimensionar um VM que exige que o VM seja transferido para diferentes clusters de hardware é diferente dependendo do modelo de implementação utilizado para implementar o VM.

- VMs implantados no modelo de implementação Clássico, a implementação do serviço em nuvem deve ser removida e reimplantada para mudar os VMs para um tamanho em outra família de tamanho.

- VMs implantados no modelo de implementação do Gestor de Recursos, você deve parar todos os VMs no conjunto de disponibilidade antes de alterar o tamanho de qualquer VM no conjunto de disponibilidade.

## <a name="the-listed-vm-size-is-not-supported-while-deploying-in-availability-set"></a>O tamanho vm listado não é suportado durante a implementação no Conjunto de Disponibilidade.

Escolha um tamanho suportado no cluster do conjunto de disponibilidade. Recomenda-se que ao criar um conjunto de disponibilidades escolha o maior tamanho VM que você acha que precisa, e ter que ser a sua primeira implementação para o conjunto de Disponibilidade.

## <a name="what-linux-distributionsversions-are-supported-on-azure"></a>Que distribuições/versões linux são suportadas no Azure?

Pode encontrar a lista em Linux em Distribuição apoiada pelo [Azure](../linux/endorsed-distros.md).

## <a name="can-i-add-an-existing-classic-vm-to-an-availability-set"></a>Posso adicionar um VM Clássico existente a um conjunto de disponibilidade?

Sim. Pode adicionar um VM clássico existente a um novo conjunto de disponibilidade ou já existente. Para mais informações consulte [Adicionar uma máquina virtual existente a um conjunto de disponibilidade](/previous-versions/azure/virtual-machines/windows/classic/configure-availability-classic#addmachine).

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

## <a name="next-steps"></a>Passos seguintes
Se precisar de mais ajuda em qualquer ponto deste artigo, pode contactar os especialistas do Azure nos [fóruns MSDN Azure e Stack Overflow](https://azure.microsoft.com/support/forums/).

Em alternativa, pode enviar um incidente de suporte do Azure. Vá ao site de [suporte azure](https://azure.microsoft.com/support/options/) e selecione **Obter Suporte**.
