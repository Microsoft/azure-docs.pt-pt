---
title: Resolução de problemas implementando problemas de máquina virtual do Windows no Azure Microsoft Docs
description: Resolução de problemas implementando problemas de máquina virtual do Windows no modelo de implementação do Gestor de Recursos Azure.
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
ms.openlocfilehash: 8ed567ee20835cfe076c9b06234f2cba23f3a8bd
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96002575"
---
# <a name="troubleshoot-deploying-windows-virtual-machine-issues-in-azure"></a>Resolver problemas de implementação de máquinas virtuais do Windows no Azure

Para resolver problemas de implementação de máquinas virtuais (VM) em Azure, reveja as [principais questões](#top-issues) para falhas e resoluções comuns.

Se precisar de mais ajuda em qualquer ponto deste artigo, pode contactar os especialistas da Azure nos [fóruns msdn Azure e Stack Overflow](https://azure.microsoft.com/support/forums/). Em alternativa, pode apresentar um incidente de suporte Azure. Vá ao [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione Obter **Apoio**.

## <a name="top-issues"></a>Principais questões
[!INCLUDE [virtual-machines-windows-troubleshoot-deploy-vm-top](../../../includes/virtual-machines-windows-troubleshoot-deploy-vm-top.md)]

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

## <a name="how-can-i-use-and-deploy-a-windows-client-image-into-azure"></a>Como posso usar e colocar uma imagem do cliente do Windows no Azure?

Pode utilizar o Windows 7, Windows 8 ou Windows 10 em Azure para cenários dev/teste se tiver uma subscrição adequada do Visual Studio (anteriormente MSDN). Este [artigo](../windows/client-images.md) descreve os requisitos de elegibilidade para executar o cliente do Windows em Azure e utiliza as imagens da Galeria Azure.

## <a name="how-can-i-deploy-a-virtual-machine-using-the-hybrid-use-benefit-hub"></a>Como posso implantar uma máquina virtual utilizando o Hybrid Use Benefit (HUB)?

Existem algumas formas diferentes de implementar máquinas virtuais do Windows com o Azure Hybrid Use Benefit.

Para uma subscrição do Contrato de Empresa:

• Implementar VMs a partir de imagens específicas do Marketplace que estejam pré-configuradas com benefício de utilização híbrida Azure.

Para o acordo de empresa:

• Carreque um VM personalizado e implemente utilizando um modelo de Gestor de Recursos ou Azure PowerShell.

Para obter mais informações, veja os seguintes recursos:

 - [Visão geral do benefício do uso híbrido Azure](https://azure.microsoft.com/pricing/hybrid-use-benefit/)

 - [FAQ transferível](https://download.microsoft.com/download/4/2/1/4211AC94-D607-4A45-B472-4B30EDF437DE/Windows_Server_Azure_Hybrid_Use_FAQ_EN_US.pdf)

 - Benefício de [utilização híbrida Azure para servidor do Windows e cliente Windows](../windows/hybrid-use-benefit-licensing.md).

 - [Como posso usar o Benefício de Uso Híbrido em Azure](/archive/blogs/azureedu/how-can-i-use-the-hybrid-use-benefit-in-azure)

## <a name="how-do-i-activate-my-monthly-credit-for-visual-studio-enterprise-bizspark"></a>Como ativar o meu crédito mensal para o estúdio Visual Enterprise (BizSpark)

Para ativar o seu crédito mensal, consulte este [artigo.](https://azure.microsoft.com/offers/ms-azr-0064p/)

## <a name="how-to-add-enterprise-devtest-to-my-enterprise-agreement-ea-to-get-access-to-window-client-images"></a>Como adicionar Enterprise Dev/Test ao meu Acordo Empresarial (EA) para ter acesso às imagens do cliente da Janela?

A capacidade de criar subscrições com base na oferta Enterprise Dev/Test está restrita aos Proprietários de Contas que tenham sido autorizados a fazê-lo por um Administrador da Empresa. O Proprietário de Contas cria subscrições através do Portal da Conta Azure e, em seguida, deve adicionar subscritores ativos do Visual Studio como coadministradores. Para que possam gerir e utilizar os recursos necessários para o desenvolvimento e teste. Para obter mais informações, veja [Enterprise Dev/Test](https://azure.microsoft.com/offers/ms-azr-0148p/).

## <a name="my-drivers-are-missing-for-my-windows-n-series-vm"></a>Os meus motoristas estão desaparecidos para o meu VM do Windows N

As instruções para instalar controladores para VMs baseados no Windows estão localizadas [aqui.](../sizes-gpu.md#supported-operating-systems-and-drivers)

## <a name="i-cant-find-a-gpu-instance-within-my-n-series-vm"></a>Não consigo encontrar uma instância gpu dentro do meu VM n-series

Para tirar partido das capacidades da GPU dos VMs da série Azure N, tem de instalar controladores gráficos em cada VM após a implementação. As informações de configuração do condutor estão disponíveis [aqui.](../sizes-gpu.md#supported-operating-systems-and-drivers)

## <a name="are-n-series-vms-available-in-my-region"></a>Os VMs da Série N estão disponíveis na minha região?

Pode consultar a disponibilidade dos [Produtos disponíveis por mesa da região,](https://azure.microsoft.com/regions/services)e os preços [aqui.](https://azure.microsoft.com/pricing/details/virtual-machines/series/#n-series)

## <a name="what-client-images-can-i-use-and-deploy-in-azure-and-how-to-i-get-them"></a>Que imagens de clientes posso usar e implementar no Azure, e como as obter?

Pode utilizar o Windows 7, Windows 8 ou Windows 10 em Azure para cenários dev/teste, desde que tenha uma subscrição adequada do Visual Studio (anteriormente MSDN). 

- As imagens do Windows 10 estão disponíveis na Galeria Azure dentro [de ofertas elegíveis dev/teste](../windows/client-images.md#eligible-offers). 
- Os subscritores do Visual Studio dentro de qualquer tipo de oferta também podem [preparar e criar adequadamente](../windows/prepare-for-upload-vhd-image.md) uma imagem de 64 bits do Windows 7, Windows 8 ou Windows 10 e, em seguida, [fazer o upload para o Azure](../windows/upload-generalized-managed.md). A utilização permanece limitada a dev/teste por subscritores ativos do Visual Studio.

Este [artigo](../windows/client-images.md) descreve os requisitos de elegibilidade para executar o cliente do Windows em Azure e a utilização das imagens da Galeria Azure.

## <a name="i-am-not-able-to-see-vm-size-family-that-i-want-when-resizing-my-vm"></a>Não consigo ver a família VM Size que quero quando redimensionar o meu VM.

Quando um VM está em funcionamento, é implantado num servidor físico. Os servidores físicos nas regiões de Azure estão agrupados em aglomerados de hardware físico comum. Redimensionar um VM que requer que o VM seja movido para diferentes clusters de hardware é diferente dependendo do modelo de implementação usado para implantar o VM.

- VMs implantados no modelo de implantação Clássico, a implementação do serviço de nuvem deve ser removida e redistribuída para alterar os VMs para um tamanho em outra família de tamanho.

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

- VMs implantados no modelo de implementação do Gestor de Recursos, deve parar todos os VMs no conjunto de disponibilidade antes de alterar o tamanho de qualquer VM no conjunto de disponibilidade.

## <a name="the-listed-vm-size-is-not-supported-while-deploying-in-availability-set"></a>O tamanho VM listado não é suportado durante a implementação no Conjunto de Disponibilidade.

Escolha um tamanho suportado no cluster do conjunto de disponibilidade. Recomenda-se ao criar um conjunto de disponibilidade para escolher o maior tamanho de VM que você acha que precisa, e ter que ser a sua primeira implementação para o conjunto Disponibilidade.

## <a name="can-i-add-an-existing-classic-vm-to-an-availability-set"></a>Posso adicionar um VM clássico existente a um conjunto de disponibilidade?

Sim. Pode adicionar um VM clássico existente a um conjunto de disponibilidade novo ou existente. Para obter mais [informações, adicione uma máquina virtual existente a um conjunto de disponibilidade.](/previous-versions/azure/virtual-machines/windows/classic/configure-availability-classic#addmachine)


## <a name="next-steps"></a>Passos seguintes
Se precisar de mais ajuda em qualquer ponto deste artigo, pode contactar os especialistas da Azure nos [fóruns msdn Azure e Stack Overflow](https://azure.microsoft.com/support/forums/).

Em alternativa, pode apresentar um incidente de suporte Azure. Vá ao [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione Obter **Apoio**.
