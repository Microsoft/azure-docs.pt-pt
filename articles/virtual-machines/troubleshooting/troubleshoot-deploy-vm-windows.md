---
title: Resolver problemas de máquina virtual do Windows implementar no Azure | Documentos da Microsoft
description: Resolver problemas de implantação problemas de máquina virtual do Windows no modelo de implementação do Gestor de recursos de Azurethe.
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
ms.openlocfilehash: 9d98a060033674a53c8ac5390bc40d10b0fbe69b
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56882506"
---
# <a name="troubleshoot-deploying-windows-virtual-machine-issues-in-azure"></a>Resolver problemas de máquina virtual do Windows implementar no Azure

Para resolver problemas de implementação de máquina virtual (VM) no Azure, reveja os [principais problemas](#top-issues) para falhas e resoluções comuns.

Se precisar de mais ajuda a qualquer momento neste artigo, pode contactar os especialistas do Azure no [os fóruns do Azure do MSDN e Stack Overflow](https://azure.microsoft.com/support/forums/). Em alternativa, pode enviar um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione **obter suporte**.

## <a name="top-issues"></a>Principais problemas
[!INCLUDE [virtual-machines-windows-troubleshoot-deploy-vm-top](../../../includes/virtual-machines-windows-troubleshoot-deploy-vm-top.md)]

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

## <a name="how-can-i-use-and-deploy-a-windows-client-image-into-azure"></a>Como pode utilizar e implementa uma imagem de cliente do windows no Azure?

Pode utilizar Windows 7, Windows 8 ou Windows 10 no Azure para cenários de desenvolvimento/teste se tiver uma subscrição do Visual Studio (anteriormente conhecido como MSDN) adequada. Isso [artigo](../windows/client-images.md) descreve os requisitos de elegibilidade para o cliente de Windows em execução no Azure e utiliza as imagens da galeria do Azure.

## <a name="how-can-i-deploy-a-virtual-machine-using-the-hybrid-use-benefit-hub"></a>Como posso implementar uma máquina virtual com o benefício de utilização híbrida (HUB)?

Existem duas formas diferentes de implementar as máquinas de virtuais do Windows com o Azure Hybrid Use Benefit.

Para uma subscrição do Enterprise Agreement:

• Implementar VMs a partir de imagens do Marketplace específicas que estão pré-configuradas com o Azure Hybrid Use Benefit.

Para contrato Enterprise:

• Carregar VMS personalizadas e implemente com um modelo do Resource Manager ou o Azure PowerShell.

Para obter mais informações, consulte os seguintes recursos:

 - [Descrição geral do benefício de utilização híbrida do Azure](https://azure.microsoft.com/pricing/hybrid-use-benefit/)

 - [FAQ para download](https://download.microsoft.com/download/4/2/1/4211AC94-D607-4A45-B472-4B30EDF437DE/Windows_Server_Azure_Hybrid_Use_FAQ_EN_US.pdf)

 - [O benefício de utilização híbrido do Azure para Windows Server e cliente Windows](../windows/hybrid-use-benefit-licensing.md).

 - [Como posso utilizar o benefício de utilização híbrida do Azure](https://blogs.msdn.microsoft.com/azureedu/2016/04/13/how-can-i-use-the-hybrid-use-benefit-in-azure)

## <a name="how-do-i-activate-my-monthly-credit-for-visual-studio-enterprise-bizspark"></a>Como posso ativar o meu crédito mensal para Visual studio Enterprise (BizSpark)

Para ativar o seu crédito mensal, veja este [artigo](https://azure.microsoft.com/offers/ms-azr-0064p/).

## <a name="how-to-add-enterprise-devtest-to-my-enterprise-agreement-ea-to-get-access-to-window-client-images"></a>Como adicionar ao meu Enterprise Agreement (EA) para obter acesso a imagens de cliente da janela de Enterprise programador/teste?

A capacidade de criar subscrições baseadas na oferta Enterprise Dev/Test é restrita a Proprietários da Conta que tenham recebido permissão para tal de um Administrador Empresarial. O proprietário da conta cria subscrições através do Portal de conta do Azure e, em seguida, deve adicionar subscritores ativos do Visual Studio como coadministradores. Para que eles podem gerir e utilizar os recursos necessários para desenvolvimento e teste. Para obter mais informações, consulte [Enterprise programador/teste](https://azure.microsoft.com/offers/ms-azr-0148p/).

## <a name="my-drivers-are-missing-for-my-windows-n-series-vm"></a>Meus controladores estão em falta para a minha VM de série N do Windows

Drivers para VMs baseadas em Windows estão localizados [aqui](../windows/n-series-driver-setup.md).

## <a name="i-cant-find-a-gpu-instance-within-my-n-series-vm"></a>Não consigo encontrar uma instância GPU dentro de minha VM de série N

Para tirar partido das capacidades de GPU de VMs de série N do Azure com o Windows Server 2016 ou Windows Server 2012 R2, tem de instalar drivers de gráficos NVIDIA em cada VM após a implementação. Informações de configuração do controlador estão disponíveis para [VMs do Windows](../windows/n-series-driver-setup.md) e [VMs do Linux](../linux/n-series-driver-setup.md).

## <a name="is-n-series-vms-available-in-my-region"></a>VMs de série N está disponível na minha região?

Pode verificar a disponibilidade a partir do [produtos disponíveis por tabela region](https://azure.microsoft.com/regions/services)e preços [aqui](https://azure.microsoft.com/pricing/details/virtual-machines/series/#n-series).

## <a name="what-client-images-can-i-use-and-deploy-in-azure-and-how-to-i-get-them"></a>As imagens do cliente posso utilizar e implementar no Azure e como para obtê-los?

Pode usar o Windows 7, Windows 8, ou Windows 10 no Azure para cenários de desenvolvimento/teste fornecida que tem uma subscrição do Visual Studio (anteriormente conhecido como MSDN) adequada. 

- Imagens do Windows 10 estão disponíveis a partir da galeria do Azure dentro [ofertas elegível dev/test](../windows/client-images.md#eligible-offers). 
- Subscritores do Visual Studio dentro de qualquer tipo de oferta também podem [adequadamente preparar e crie](../windows/prepare-for-upload-vhd-image.md) uma imagem do Windows 7, Windows 8 ou Windows 10 de 64 bits e, em seguida [carregar para o Azure](../windows/upload-generalized-managed.md). A utilização continua a ser limitada para desenvolvimento/teste por subscritores ativos do Visual Studio.

Isso [artigo](../windows/client-images.md) descreve os requisitos de elegibilidade para o cliente de Windows em execução no Azure e a utilização das imagens da galeria do Azure.

## <a name="i-am-not-able-to-see-vm-size-family-that-i-want-when-resizing-my-vm"></a>Eu não sou capaz de ver a família de tamanho de VM que desejo durante o redimensionamento de minha VM.

Quando uma VM está em execução, ele é implementado um servidor físico. Os servidores físicos em regiões do Azure são agrupados em clusters de hardware físico comum. Redimensionar uma VM que requer que a VM de ser movidos para clusters de hardware diferentes é diferente, dependendo de qual modelo de implementação foi utilizado para implementar a VM.

- As VMs implementadas num modelo de implementação clássica, a implementação de serviço em nuvem tem de ser removidas e implementado novamente para alterar as VMs para um tamanho em outra família de tamanho.

- As VMs implementadas no modelo de implementação do Resource Manager, tem de parar todas as VMs no conjunto antes de alterar o tamanho de qualquer VM no conjunto de disponibilidade de disponibilidade.

## <a name="the-listed-vm-size-is-not-supported-while-deploying-in-availability-set"></a>O tamanho da VM listado não é suportado durante a implementação no conjunto de disponibilidade.

Escolha um tamanho que é suportado num cluster de conjunto de disponibilidade. Recomenda-se quando criar um disponibilidade definida para escolher o maior tamanho da VM acha que precisa e tem que ser sua primeira implementação para o conjunto de disponibilidade.

## <a name="can-i-add-an-existing-classic-vm-to-an-availability-set"></a>Pode adicionar uma VM clássica existente para um conjunto de disponibilidade?

Sim. Pode adicionar uma VM clássica existente para um novo ou existente do conjunto de disponibilidade. Para obter mais informações, consulte [adicionar uma máquina virtual existente a um conjunto de disponibilidade](/previous-versions/azure/virtual-machines/windows/classic/configure-availability-classic#addmachine).


## <a name="next-steps"></a>Passos Seguintes
Se precisar de mais ajuda a qualquer momento neste artigo, pode contactar os especialistas do Azure no [os fóruns do Azure do MSDN e Stack Overflow](https://azure.microsoft.com/support/forums/).

Em alternativa, pode enviar um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione **obter suporte**.
