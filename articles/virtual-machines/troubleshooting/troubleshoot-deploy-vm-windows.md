---
title: Solucionar problemas de implantação de máquina virtual do Windows no Azure | Microsoft Docs
description: Solucionar problemas de implantação de máquina virtual do Windows no modelo de implantação Azure Resource Manager.
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
ms.openlocfilehash: 2baa82bda1f92fe81bb0db69b84e6865b2709e42
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/17/2019
ms.locfileid: "71058042"
---
# <a name="troubleshoot-deploying-windows-virtual-machine-issues-in-azure"></a>Solucionar problemas de implantação de máquina virtual do Windows no Azure

Para solucionar problemas de implantação de máquina virtual (VM) no Azure, examine os [principais problemas](#top-issues) de falhas e resoluções comuns.

Se precisar de mais ajuda a qualquer momento neste artigo, você poderá entrar em contato com os especialistas do Azure nos [fóruns do Azure e do Stack Overflow do MSDN](https://azure.microsoft.com/support/forums/). Em alternativa, pode enviar um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione **obter suporte**.

## <a name="top-issues"></a>Principais problemas
[!INCLUDE [virtual-machines-windows-troubleshoot-deploy-vm-top](../../../includes/virtual-machines-windows-troubleshoot-deploy-vm-top.md)]

## <a name="the-cluster-cannot-support-the-requested-vm-size"></a>O cluster não dá suporte ao tamanho de VM solicitado
\<Properties supportTopicIds = "123456789" resourceTags = "Windows" productPesIds = "1234, 5678"/>
- Repita a solicitação usando um tamanho de VM menor.
- Se o tamanho da VM solicitada não puder ser alterado:
    - Pare todas as VMs no conjunto de disponibilidade. Clique em **grupos de recursos** > seu grupo de recursos > **recursos** > seu conjunto de disponibilidade > **máquinas virtuais** > sua máquina virtual > **parar**.
    - Depois que todas as VMs forem interrompidas, crie a VM no tamanho desejado.
    - Inicie a nova VM primeiro e, em seguida, selecione cada uma das VMs paradas e clique em Iniciar.


## <a name="the-cluster-does-not-have-free-resources"></a>O cluster não tem recursos gratuitos
\<Properties supportTopicIds = "123456789" resourceTags = "Windows" productPesIds = "1234, 5678"/>
- Repita a solicitação mais tarde.
- Se a nova VM puder fazer parte de um conjunto de disponibilidade diferente
    - Crie uma VM em um conjunto de disponibilidade diferente (na mesma região).
    - Adicione a nova VM à mesma rede virtual.

## <a name="how-can-i-use-and-deploy-a-windows-client-image-into-azure"></a>Como posso usar e implantar uma imagem de cliente do Windows no Azure?

Você pode usar o Windows 7, o Windows 8 ou o Windows 10 no Azure para cenários de desenvolvimento/teste se tiver uma assinatura apropriada do Visual Studio (anteriormente conhecido como MSDN). Este [artigo](../windows/client-images.md) descreve os requisitos de qualificação para executar o cliente do Windows no Azure e usa as imagens da galeria do Azure.

## <a name="how-can-i-deploy-a-virtual-machine-using-the-hybrid-use-benefit-hub"></a>Como posso implantar uma máquina virtual usando o benefício de uso híbrido (HUB)?

Há algumas maneiras diferentes de implantar máquinas virtuais do Windows com o benefício de uso híbrido do Azure.

Para uma assinatura Enterprise Agreement:

• Implante VMs de imagens específicas do Marketplace que são pré-configuradas com o benefício de uso híbrido do Azure.

Para o Enterprise Agreement:

• Carregar uma VM personalizada e implantá-la usando um modelo ou Azure PowerShell do Resource Manager.

Para obter mais informações, consulte os seguintes recursos:

 - [Visão geral do benefício de uso híbrido do Azure](https://azure.microsoft.com/pricing/hybrid-use-benefit/)

 - [Perguntas frequentes baixáveis](https://download.microsoft.com/download/4/2/1/4211AC94-D607-4A45-B472-4B30EDF437DE/Windows_Server_Azure_Hybrid_Use_FAQ_EN_US.pdf)

 - [Benefício de uso híbrido do Azure para Windows Server e Windows Client](../windows/hybrid-use-benefit-licensing.md).

 - [Como posso usar o benefício de uso híbrido no Azure](https://blogs.msdn.microsoft.com/azureedu/2016/04/13/how-can-i-use-the-hybrid-use-benefit-in-azure)

## <a name="how-do-i-activate-my-monthly-credit-for-visual-studio-enterprise-bizspark"></a>Como fazer ativar meu crédito mensal para o Visual Studio Enterprise (BizSpark)

Para ativar seu crédito mensal, consulte este [artigo](https://azure.microsoft.com/offers/ms-azr-0064p/).

## <a name="how-to-add-enterprise-devtest-to-my-enterprise-agreement-ea-to-get-access-to-window-client-images"></a>Como adicionar Desenvolvimento/Teste Enterprise ao meu Enterprise Agreement (EA) para obter acesso às imagens de cliente do Windows?

A capacidade de criar subscrições baseadas na oferta Enterprise Dev/Test é restrita a Proprietários da Conta que tenham recebido permissão para tal de um Administrador Empresarial. O proprietário da conta cria assinaturas por meio do portal de contas do Azure e, em seguida, deve adicionar assinantes ativos do Visual Studio como coadministradores. Para que eles possam gerenciar e usar os recursos necessários para desenvolvimento e teste. Para obter mais informações, consulte [desenvolvimento/teste Enterprise](https://azure.microsoft.com/offers/ms-azr-0148p/).

## <a name="my-drivers-are-missing-for-my-windows-n-series-vm"></a>Meus drivers estão ausentes para minha VM da série N do Windows

Os drivers para VMs baseadas no Windows estão localizados [aqui](../windows/n-series-driver-setup.md).

## <a name="i-cant-find-a-gpu-instance-within-my-n-series-vm"></a>Não consigo encontrar uma instância de GPU em minha VM da série N

Para aproveitar os recursos de GPU das VMs da série N do Azure que executam o Windows Server 2016 ou o Windows Server 2012 R2, você deve instalar os drivers gráficos NVIDIA em cada VM após a implantação. As informações de configuração do driver estão disponíveis para [VMs do Windows e máquinas](../windows/n-series-driver-setup.md) virtuais do [Linux](../linux/n-series-driver-setup.md).

## <a name="is-n-series-vms-available-in-my-region"></a>As VMs da série N estão disponíveis em minha região?

Você pode verificar a disponibilidade da [tabela produtos disponíveis por região](https://azure.microsoft.com/regions/services)e preços [aqui](https://azure.microsoft.com/pricing/details/virtual-machines/series/#n-series).

## <a name="what-client-images-can-i-use-and-deploy-in-azure-and-how-to-i-get-them"></a>Quais imagens de cliente posso usar e implantar no Azure e como posso obtê-las?

Você pode usar o Windows 7, o Windows 8 ou o Windows 10 no Azure para cenários de desenvolvimento/teste, desde que você tenha uma assinatura apropriada do Visual Studio (anteriormente conhecido como MSDN). 

- As imagens do Windows 10 estão disponíveis na galeria do Azure em [ofertas de desenvolvimento/teste qualificadas](../windows/client-images.md#eligible-offers). 
- Os assinantes do Visual Studio em qualquer tipo de oferta também podem [preparar e criar adequadamente](../windows/prepare-for-upload-vhd-image.md) uma imagem de 64 bits do Windows 7, do Windows 8 ou do Windows 10 e, em seguida, [carregar para o Azure](../windows/upload-generalized-managed.md). O uso permanece limitado a desenvolvimento/teste por assinantes ativos do Visual Studio.

Este [artigo](../windows/client-images.md) descreve os requisitos de qualificação para a execução do cliente Windows no Azure e o uso das imagens da galeria do Azure.

## <a name="i-am-not-able-to-see-vm-size-family-that-i-want-when-resizing-my-vm"></a>Não consigo ver a família de tamanhos de VM que desejo ao redimensionar minha VM.

Quando uma VM está em execução, ela é implantada em um servidor físico. Os servidores físicos nas regiões do Azure são agrupados em clusters de hardware físico comum. O redimensionamento de uma VM que exige que a VM seja movida para diferentes clusters de hardware é diferente dependendo de qual modelo de implantação foi usado para implantar a VM.

- VMs implantadas no modelo de implantação clássico, a implantação do serviço de nuvem deve ser removida e reimplantada para alterar as VMs para um tamanho em outra família de tamanho.

- VMs implantadas no modelo de implantação do Gerenciador de recursos, você deve parar todas as VMs no conjunto de disponibilidade antes de alterar o tamanho de qualquer VM no conjunto de disponibilidade.

## <a name="the-listed-vm-size-is-not-supported-while-deploying-in-availability-set"></a>Não há suporte para o tamanho da VM listado durante a implantação no conjunto de disponibilidade.

Escolha um tamanho com suporte no cluster do conjunto de disponibilidade. É recomendável ao criar um conjunto de disponibilidade para escolher o maior tamanho de VM que você considera necessário e ter essa sua primeira implantação para o conjunto de disponibilidade.

## <a name="can-i-add-an-existing-classic-vm-to-an-availability-set"></a>Posso adicionar uma VM clássica existente a um conjunto de disponibilidade?

Sim. Você pode adicionar uma VM clássica existente a um conjunto de disponibilidade novo ou existente. Para obter mais informações, consulte [Adicionar uma máquina virtual existente a um conjunto de disponibilidade](/previous-versions/azure/virtual-machines/windows/classic/configure-availability-classic#addmachine).


## <a name="next-steps"></a>Passos Seguintes
Se precisar de mais ajuda a qualquer momento neste artigo, você poderá entrar em contato com os especialistas do Azure nos [fóruns do Azure e do Stack Overflow do MSDN](https://azure.microsoft.com/support/forums/).

Em alternativa, pode enviar um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione **obter suporte**.
