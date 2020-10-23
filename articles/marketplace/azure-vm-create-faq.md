---
title: Perguntas comuns sobre VM no Azure Marketplace
description: Questões comuns encontradas ao criar uma máquina virtual no Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: guide
author: iqshahmicrosoft
ms.author: iqshah
ms.date: 10/15/2020
ms.openlocfilehash: 131cd9b1de82213d89ee07f30aed25544f6fcb18
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/20/2020
ms.locfileid: "92284908"
---
# <a name="common-questions-about-vm-in-azure-marketplace"></a>Perguntas comuns sobre VM no Azure Marketplace

Estas perguntas frequentes (FAQ) cobrem questões comuns que poderá encontrar ao criar uma oferta de máquina virtual (VM) no Azure Marketplace.

## <a name="how-do-i-configure-a-virtual-private-network-vpn-to-work-with-my-vms"></a>Como posso configurar uma rede privada virtual (VPN) para trabalhar com os meus VMs?

Se estiver a utilizar o modelo de implementação do Gestor de Recursos Azure, tem três opções:

- [Criar um gateway VPN baseado em rotas utilizando o portal Azure](../vpn-gateway/create-routebased-vpn-gateway-portal.md)
- [Criar um gateway VPN baseado em rotas usando Azure PowerShell](../vpn-gateway/create-routebased-vpn-gateway-powershell.md)
- [Criar um gateway VPN baseado em rotas usando CLI](../vpn-gateway/create-routebased-vpn-gateway-cli.md)

## <a name="what-are-microsoft-support-policies-for-running-microsoft-server-software-on-azure-based-vms"></a>Quais são as políticas de suporte da Microsoft para executar o software do servidor da Microsoft em VMs baseados em Azure?

Pode encontrar detalhes no [suporte ao software do servidor da Microsoft para máquinas virtuais Microsoft Azure](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines).

## <a name="in-a-vm-how-do-i-manage-the-custom-script-extension-in-the-startup-task"></a>Num VM, como posso gerir a extensão de script personalizado na tarefa de arranque?

Para obter detalhes sobre a utilização da extensão de script personalizada utilizando o módulo Azure PowerShell, os modelos do Gestor de Recursos Azure e os passos de resolução de problemas nos sistemas Windows, consulte [a extensão de script personalizada para windows](/azure/virtual-machines/extensions/custom-script-windows).

## <a name="are-32-bit-applications-or-services-supported-in-azure-marketplace"></a>As aplicações ou serviços de 32 bits são suportados no Azure Marketplace?

Não. Os sistemas operativos suportados e os serviços standard para os VMs Azure são todos de 64 bits. Embora a maioria dos sistemas operativos de 64 bits suportem versões de 32 bits de aplicações para retrocompatibilidade, usar aplicações de 32 bits como parte da sua solução VM não é suportado e altamente desencorajado. Recrie a sua aplicação como um projeto de 64 bits.

Para obter mais informações, veja estes artigos:

- [Executando aplicações de 32 bits](https://docs.microsoft.com/windows/desktop/WinProg64/running-32-bit-applications)
- [Suporte para sistemas operativos de 32 bits nas máquinas virtuais do Azure](https://support.microsoft.com/help/4021388/support-for-32-bit-operating-systems-in-azure-virtual-machines)
- [Microsoft server software support for Microsoft Azure virtual machines](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) (Suporte de software de servidor da Microsoft para máquinas virtuais do Microsoft Azure)

## <a name="error-vhd-is-already-registered-with-image-repository-as-the-resource"></a>Erro: VHD já está registado com repositório de imagem como o recurso

Sempre que tento criar uma imagem dos meus VHDs, recebo o erro "VHD já está registado com repositório de imagem como recurso" em Azure PowerShell. Não criei nenhuma imagem antes nem encontrei nenhuma imagem com este nome em Azure. Como posso resolver isto?

Este problema geralmente aparece se você criou um VM a partir de um VHD que tem um bloqueio nele. Confirme que não há VM atribuído a partir deste VHD e, em seguida, re-tentar a operação. Se esta questão continuar, abra um bilhete de apoio. Consulte [o Suporte para o Centro de Parceiros.](support.md)

## <a name="next-steps"></a>Passos seguintes

- [Resolução de problemas de certificação VM](azure-vm-create-certification-faq.md)
