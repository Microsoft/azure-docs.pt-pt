---
title: Problemas de resolução de problemas implementam problemas de máquinas virtuais do Windows em Azure Microsoft Docs
description: Problemas implementando problemas de máquinas virtuais do Windows no modelo de implementação do Gestor de Recursos Azure.
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
ms.openlocfilehash: cdbaeb5a97beba342bc471e75d1b07be0d0141ae
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2020
ms.locfileid: "77921416"
---
# <a name="troubleshoot-deploying-windows-virtual-machine-issues-in-azure"></a>Problemas implementam problemas de máquinas virtuais do Windows em Azure

Para resolver problemas de implantação de máquinas virtuais (VM) em Azure, reveja as [principais questões](#top-issues) para falhas e resoluções comuns.

Se precisar de mais ajuda em qualquer ponto deste artigo, pode contactar os especialistas do Azure nos [fóruns MSDN Azure e Stack Overflow](https://azure.microsoft.com/support/forums/). Em alternativa, pode enviar um incidente de suporte do Azure. Vá ao site de [suporte azure](https://azure.microsoft.com/support/options/) e selecione **Obter Suporte**.

## <a name="top-issues"></a>Principais questões
[!INCLUDE [virtual-machines-windows-troubleshoot-deploy-vm-top](../../../includes/virtual-machines-windows-troubleshoot-deploy-vm-top.md)]

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

## <a name="how-can-i-use-and-deploy-a-windows-client-image-into-azure"></a>Como posso usar e colocar uma imagem de cliente de janelas no Azure?

Pode utilizar o Windows 7, Windows 8 ou Windows 10 em Azure para cenários de dev/teste se tiver uma subscrição de Estúdio Visual apropriado (anteriormente MSDN). Este [artigo](../windows/client-images.md) descreve os requisitos de elegibilidade para executar o cliente Windows em Azure e utiliza as imagens da Galeria Azure.

## <a name="how-can-i-deploy-a-virtual-machine-using-the-hybrid-use-benefit-hub"></a>Como posso implantar uma máquina virtual utilizando o Hybrid Use Benefit (HUB)?

Existem algumas formas diferentes de implementar máquinas virtuais windows com o Azure Hybrid Use Benefit.

Para uma subscrição do Acordo Empresarial:

• Implemente VMs a partir de imagens específicas do Marketplace pré-configuradas com O Benefício de Utilização Híbrido Azure.

Para o acordo da Empresa:

• Faça o upload de um VM personalizado e implemente utilizando um modelo de Gestor de Recursos ou Azure PowerShell.

Para obter mais informações, consulte os seguintes recursos:

 - [Visão geral do benefício de uso híbrido azure](https://azure.microsoft.com/pricing/hybrid-use-benefit/)

 - [FAQ descartável](https://download.microsoft.com/download/4/2/1/4211AC94-D607-4A45-B472-4B30EDF437DE/Windows_Server_Azure_Hybrid_Use_FAQ_EN_US.pdf)

 - [Benefício de utilização híbrido Azure para windows server e cliente windows](../windows/hybrid-use-benefit-licensing.md).

 - [Como posso usar o Hybrid Use Benefit em Azure](https://blogs.msdn.microsoft.com/azureedu/2016/04/13/how-can-i-use-the-hybrid-use-benefit-in-azure)

## <a name="how-do-i-activate-my-monthly-credit-for-visual-studio-enterprise-bizspark"></a>Como ativo o meu crédito mensal para o estúdio visual Enterprise (BizSpark)

Para ativar o seu crédito mensal, consulte este [artigo.](https://azure.microsoft.com/offers/ms-azr-0064p/)

## <a name="how-to-add-enterprise-devtest-to-my-enterprise-agreement-ea-to-get-access-to-window-client-images"></a>Como adicionar Enterprise Dev/Test ao meu Acordo de Empresa (EA) para ter acesso às imagens do cliente Window?

A capacidade de criar subscrições com base na oferta Enterprise Dev/Test é restrita aos Proprietários de Conta que tenham sido autorizados a fazê-lo por um Administrador da Empresa. O Proprietário da Conta cria subscrições através do Portal da Conta Azure, devendo depois adicionar subscritores ativos do Estúdio Visual como coadministradores. Para que possam gerir e utilizar os recursos necessários para o desenvolvimento e testes. Para obter mais informações, veja [Enterprise Dev/Test](https://azure.microsoft.com/offers/ms-azr-0148p/).

## <a name="my-drivers-are-missing-for-my-windows-n-series-vm"></a>Os meus motoristas estão desaparecidos para o meu VM da Série N do Windows

Os controladores para VMs baseados no Windows estão localizados [aqui](../windows/n-series-driver-setup.md).

## <a name="i-cant-find-a-gpu-instance-within-my-n-series-vm"></a>Não encontro uma instância de GPU dentro do meu VM série N

Para tirar partido das capacidades de GPU dos VMs da série N Azure que executam o Windows Server 2016 ou o Windows Server 2012 R2, tem de instalar controladores gráficos NVIDIA em cada VM após a implementação. As informações de configuração do controlador estão disponíveis para [VMs windows](../windows/n-series-driver-setup.md) e [VMs Linux](../linux/n-series-driver-setup.md).

## <a name="is-n-series-vms-available-in-my-region"></a>Os VMs da Série N estão disponíveis na minha região?

Pode consultar a disponibilidade dos Produtos disponíveis por mesa da [região](https://azure.microsoft.com/regions/services)e os preços [aqui.](https://azure.microsoft.com/pricing/details/virtual-machines/series/#n-series)

## <a name="what-client-images-can-i-use-and-deploy-in-azure-and-how-to-i-get-them"></a>Que imagens de clientes posso usar e implantar em Azure, e como as conseguir?

Pode utilizar o Windows 7, Windows 8 ou Windows 10 em Azure para cenários de dev/teste, desde que tenha uma subscrição de Estúdio Visual apropriado (anteriormente MSDN). 

- As imagens do Windows 10 estão disponíveis na Galeria Azure dentro de [ofertas elegíveis de v/teste.](../windows/client-images.md#eligible-offers) 
- Os subscritores do Visual Studio dentro de qualquer tipo de oferta também podem [preparar e criar](../windows/prepare-for-upload-vhd-image.md) uma imagem do Windows 7, Windows 8 ou Windows 10 de 64 bits e depois fazer o upload para o [Azure.](../windows/upload-generalized-managed.md) A utilização continua limitada a dev/teste por subscritores ativos do Visual Studio.

Este [artigo](../windows/client-images.md) descreve os requisitos de elegibilidade para executar o cliente Windows em Azure e a utilização das imagens da Galeria Azure.

## <a name="i-am-not-able-to-see-vm-size-family-that-i-want-when-resizing-my-vm"></a>Não consigo ver a família VM Size que quero ao redimensionar o meu VM.

Quando um VM está em execução, é implantado num servidor físico. Os servidores físicos nas regiões de Azure estão agrupados em aglomerados de hardware físico comum. Redimensionar um VM que exige que o VM seja transferido para diferentes clusters de hardware é diferente dependendo do modelo de implementação utilizado para implementar o VM.

- VMs implantados no modelo de implementação Clássico, a implementação do serviço em nuvem deve ser removida e reimplantada para mudar os VMs para um tamanho em outra família de tamanho.

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

- VMs implantados no modelo de implementação do Gestor de Recursos, você deve parar todos os VMs no conjunto de disponibilidade antes de alterar o tamanho de qualquer VM no conjunto de disponibilidade.

## <a name="the-listed-vm-size-is-not-supported-while-deploying-in-availability-set"></a>O tamanho vm listado não é suportado durante a implementação no Conjunto de Disponibilidade.

Escolha um tamanho suportado no cluster do conjunto de disponibilidade. Recomenda-se que ao criar um conjunto de disponibilidades escolha o maior tamanho VM que você acha que precisa, e ter que ser a sua primeira implementação para o conjunto de Disponibilidade.

## <a name="can-i-add-an-existing-classic-vm-to-an-availability-set"></a>Posso adicionar um VM Clássico existente a um conjunto de disponibilidade?

Sim. Pode adicionar um VM clássico existente a um novo conjunto de disponibilidade ou já existente. Para mais informações consulte [Adicionar uma máquina virtual existente a um conjunto de disponibilidade](/previous-versions/azure/virtual-machines/windows/classic/configure-availability-classic#addmachine).


## <a name="next-steps"></a>Passos seguintes
Se precisar de mais ajuda em qualquer ponto deste artigo, pode contactar os especialistas do Azure nos [fóruns MSDN Azure e Stack Overflow](https://azure.microsoft.com/support/forums/).

Em alternativa, pode enviar um incidente de suporte do Azure. Vá ao site de [suporte azure](https://azure.microsoft.com/support/options/) e selecione **Obter Suporte**.
