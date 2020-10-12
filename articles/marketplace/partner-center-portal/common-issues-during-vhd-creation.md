---
title: Questões comuns durante a criação de VHD (FAQ)
description: Frequentemente, perguntas sobre questões comuns ao criar um disco rígido virtual (VHD).
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: guide
author: iqshahmicrosoft
ms.author: iqshah
ms.date: 04/09/2020
ms.openlocfilehash: f4d30cdb931d6523eba3aac003caeee38a8c024d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88653533"
---
# <a name="common-issues-during-vhd-creation"></a>Questões comuns durante a criação de VHD

Estas perguntas frequentes (FAQ) cobrem questões comuns que poderá encontrar ao criar um disco rígido virtual (VHD) para a sua oferta de Máquina Virtual Azure.

## <a name="how-do-i-create-a-vm-from-the-azure-portal-using-a-vhd-in-premium-storage"></a>Como posso criar um VM a partir do portal Azure utilizando um VHD em armazenamento premium?

A Azure Marketplace não suporta atualmente a criação de ofertas de VM a partir de imagens sobre armazenamento gerido ou a partir de Azure Premium Storage. Para mais detalhes, consulte [a visão geral dos discos geridos Azure](../../virtual-machines/managed-disks-overview.md).

## <a name="can-i-use-generation-2-vms-for-offers"></a>Posso usar VMs de Geração 2 para ofertas?

Não, só os VHDs da Geração 1 são suportados. No entanto, estamos neste momento a trabalhar com a Microsoft Azure Platform Team para investigar o suporte para os VMs da Geração 2. Para mais detalhes, veja [se devo criar uma máquina virtual de geração 1 ou 2 em Hyper-V?](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v)

## <a name="how-do-i-change-the-name-of-the-host"></a>Como mudo o nome do anfitrião?

Não pode fazê-lo. Após a criação de um VM, os utilizadores (incluindo os proprietários) não podem atualizar o nome de anfitrião.

## <a name="how-do-i-reset-the-remote-desktop-service-or-its-sign-in-password"></a>Como posso redefinir o serviço de ambiente de trabalho remoto ou a sua senha de entrada?

Estes artigos explicam como executar resets RDS para VMs baseados no Windows e Linux:

* [Como repor o serviço Ambiente de Trabalho Remoto ou a respetiva palavra-passe de início de sessão numa VM do Windows](/azure/virtual-machines/troubleshooting/reset-rdp)
* [Como redefinir uma palavra-passe Linux VM ou uma chave SSH, corrigir a configuração SSH e verificar a consistência do disco utilizando a extensão VMAccess](/azure/virtual-machines/extensions/vmaccess)

## <a name="how-do-i-generate-new-ssh-certificates"></a>Como gerarei novos certificados SSH?

A geração de certificados é explicada na [certificação de imagem Azure VM](https://aks.ms/CertifyVMimage).

## <a name="how-do-i-configure-a-virtual-private-network-vpn-to-work-with-my-vms"></a>Como posso configurar uma rede privada virtual (VPN) para trabalhar com os meus VMs?

Se estiver a utilizar o modelo de implementação do Gestor de Recursos Azure, tem três opções:

* [Criar um gateway VPN baseado em rotas utilizando o portal Azure](../../vpn-gateway/create-routebased-vpn-gateway-portal.md)
* [Criar um gateway VPN baseado em rotas usando Azure PowerShell](../../vpn-gateway/create-routebased-vpn-gateway-powershell.md)
* [Criar um gateway VPN baseado em rotas usando CLI](../../vpn-gateway/create-routebased-vpn-gateway-cli.md)

## <a name="what-are-microsoft-support-policies-for-running-microsoft-server-software-on-azure-based-vms"></a>Quais são as políticas de suporte da Microsoft para executar o software do servidor da Microsoft em VMs baseados em Azure?

Pode encontrar detalhes no [suporte ao software do servidor da Microsoft para máquinas virtuais Microsoft Azure](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines).

## <a name="do-virtual-machines-have-unique-identifiers-associated-with-them"></a>As máquinas virtuais têm identificadores únicos associados a eles?

Sim, se hospedado no Azure. A Azure atribui um identificador único, chamado [Azure Virtual Machine Unique ID,](https://blogs.msdn.microsoft.com/wasimbloch/2016/10/20/azure-virtual-machine-unique-id/)a cada novo recurso VM criado. Para mais detalhes, consulte o ID Exclusivo da Máquina Virtual Azure. Também pode obter este identificador através da [Lista API.](https://docs.microsoft.com/rest/api/compute/virtualmachines/list)

## <a name="in-a-vm-how-do-i-manage-the-custom-script-extension-in-the-startup-task"></a>Num VM, como posso gerir a extensão de script personalizado na tarefa de arranque?

Para obter detalhes sobre a utilização da extensão de script personalizada utilizando o módulo Azure PowerShell, os modelos do Gestor de Recursos Azure e os passos de resolução de problemas nos sistemas Windows, consulte [a extensão de script personalizada para windows](/azure/virtual-machines/extensions/custom-script-windows).

## <a name="are-32-bit-applications-or-services-supported-in-azure-marketplace"></a>As aplicações ou serviços de 32 bits são suportados no Azure Marketplace?

Geralmente, não. Os sistemas operativos suportados e os serviços standard para os VMs Azure são todos de 64 bits. Embora a maioria dos sistemas operativos de 64 bits suportem versões de 32 bits de aplicações para retrocompatibilidade, usar aplicações de 32 bits como parte da sua solução VM não é suportado e altamente desencorajado. Recrie a sua aplicação como um projeto de 64 bits.

Para obter mais informações, veja estes artigos:

* [Executando aplicações de 32 bits](https://docs.microsoft.com/windows/desktop/WinProg64/running-32-bit-applications)
* [Suporte para sistemas operativos de 32 bits nas máquinas virtuais do Azure](https://support.microsoft.com/help/4021388/support-for-32-bit-operating-systems-in-azure-virtual-machines)
* [Microsoft server software support for Microsoft Azure virtual machines](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) (Suporte de software de servidor da Microsoft para máquinas virtuais do Microsoft Azure)

## <a name="error-vhd-is-already-registered-with-image-repository-as-the-resource"></a>Erro: VHD já está registado com repositório de imagem como o recurso

Sempre que tento criar uma imagem dos meus VHDs, recebo o erro "VHD já está registado com repositório de imagem como recurso" em Azure PowerShell. Não criei nenhuma imagem antes nem encontrei nenhuma imagem com este nome em Azure. Como posso resolver isto?

Este problema geralmente aparece se você criou um VM a partir de um VHD que tem um bloqueio nele. Confirme que não há VM atribuído a partir deste VHD e, em seguida, re-tentar a operação. Se esta questão continuar, abra um bilhete de apoio. Consulte [o Suporte para o Centro de Parceiros.](support.md)
