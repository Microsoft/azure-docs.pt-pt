---
title: Questões comuns durante a criação de VHD (FAQ)
description: Perguntas frequentes sobre questões comuns ao criar um disco rígido virtual (VHD).
author: emuench
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: guide
ms.date: 04/09/2020
ms.openlocfilehash: a47bece4df7263dbc29ec19510c3ea422bb3f4cf
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/13/2020
ms.locfileid: "81266269"
---
# <a name="common-issues-during-vhd-creation"></a>Questões comuns durante a criação de VHD

> [!NOTE]
> Estamos a mover a gestão das suas ofertas Azure VM do Cloud Partner Portal para partner Center. Até que as suas ofertas sejam migradas, por favor continue a seguir as instruções em questões comuns durante a criação de [VHD (FAQ)](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-common-vhd-creation-issues) no Cloud Partner Portal para gerir as suas ofertas.

Estas perguntas frequentemente feitas (FAQ) cobrem questões comuns que você pode encontrar ao criar um disco rígido virtual (VHD) para a sua oferta de Máquina Virtual Azure.

## <a name="how-do-i-create-a-vm-from-the-azure-portal-using-a-vhd-in-premium-storage"></a>Como posso criar um VM do portal Azure usando um VHD em armazenamento premium?

Atualmente, o Azure Marketplace não suporta a criação de ofertas VM a partir de imagens sobre armazenamento gerido ou de Armazenamento Premium Azure. Para mais detalhes, consulte a [visão geral dos discos geridos pelo Azure](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview).

## <a name="can-i-use-generation-2-vms-for-offers"></a>Posso usar VMs da Geração 2 para ofertas?

Não, apenas os VHDs da Geração 1 são apoiados. No entanto, estamos neste momento a trabalhar com a Equipa de Plataforma Microsoft Azure para investigar o suporte para os VMs da Geração 2. Para mais detalhes, consulte Devo criar uma máquina virtual de [geração 1 ou 2 em Hyper-V?](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v)

## <a name="how-do-i-change-the-name-of-the-host"></a>Como mudo o nome do hospedeiro?

Não podes. Após a criação de um VM, os utilizadores (incluindo os proprietários) não podem atualizar o nome do anfitrião.

## <a name="how-do-i-reset-the-remote-desktop-service-or-its-sign-in-password"></a>Como redefinir o serviço Remote Desktop ou a sua senha de entrada?

Estes artigos explicam como realizar resets RDS para VMs baseados em Windows e Linux:

* [Como repor o serviço Ambiente de Trabalho Remoto ou a respetiva palavra-passe de início de sessão numa VM do Windows](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-reset-rdp/)
* [Como redefinir uma palavra-passe Linux VM ou uma chave SSH, corrigir a configuração SSH e verificar a consistência do disco utilizando a extensão VMAccess](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-classic-reset-access/)

## <a name="how-do-i-generate-new-ssh-certificates"></a>Como posso gerar novos certificados SSH?

A geração de certificados é explicada na certificação de [imagem Azure VM.](https://aks.ms/CertifyVMimage)

## <a name="how-do-i-configure-a-virtual-private-network-vpn-to-work-with-my-vms"></a>Como configurar uma rede privada virtual (VPN) para trabalhar com os meus VMs?

Se estiver a utilizar o modelo de implementação do Gestor de Recursos Azure, tem três opções:

* [Criar um gateway VPN baseado em rota usando o portal Azure](https://docs.microsoft.com/azure/vpn-gateway/create-routebased-vpn-gateway-portal)
* [Criar um gateway VPN baseado em rota usando o Azure PowerShell](https://docs.microsoft.com/azure/vpn-gateway/create-routebased-vpn-gateway-powershell)
* [Criar um gateway VPN baseado em rota usando cli](https://docs.microsoft.com/azure/vpn-gateway/create-routebased-vpn-gateway-cli)

## <a name="what-are-microsoft-support-policies-for-running-microsoft-server-software-on-azure-based-vms"></a>Quais são as políticas de suporte da Microsoft para executar o software de servidores da Microsoft em VMs baseados no Azure?

Pode encontrar detalhes no suporte do software do [microsoft para máquinas virtuais Do Microsoft Azure](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines).

## <a name="do-virtual-machines-have-unique-identifiers-associated-with-them"></a>As máquinas virtuais têm identificadores únicos associados a eles?

Sim, se hospedado no Azure. O Azure atribui um identificador único, chamado Id Exclusivo da [Máquina Virtual Azure,](https://blogs.msdn.microsoft.com/wasimbloch/2016/10/20/azure-virtual-machine-unique-id/)a cada novo recurso VM criado. Para mais detalhes, consulte o ID exclusivo da máquina virtual Azure. Também pode obter este identificador através da [Lista API](https://docs.microsoft.com/rest/api/compute/virtualmachines/list).

## <a name="in-a-vm-how-do-i-manage-the-custom-script-extension-in-the-startup-task"></a>Num VM, como posso gerir a extensão personalizada do script na tarefa de arranque?

Para mais detalhes sobre a utilização da extensão do script personalizado utilizando o módulo PowerShell Azure, os modelos do Gestor de Recursos Azure e os passos de resolução de problemas nos sistemas Windows, consulte a [extensão personalizada do script para windows](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-extensions-customscript/).

## <a name="are-32-bit-applications-or-services-supported-in-azure-marketplace"></a>As aplicações ou serviços de 32 bits são suportados no Azure Marketplace?

Geralmente, não. Os sistemas operativos suportados e os serviços standard para Os VMs Azure são todos de 64 bits. Embora a maioria dos sistemas operativos de 64 bits suporte versões de aplicações de 32 bits para retrocompatibilidade, usar aplicações de 32 bits como parte da sua solução VM não é suportado e altamente desencorajado. Recrie a sua aplicação como um projeto de 64 bits.

Para obter mais informações, veja estes artigos:

* [Executar aplicações de 32 bits](https://docs.microsoft.com/windows/desktop/WinProg64/running-32-bit-applications)
* [Suporte para sistemas operativos de 32 bits nas máquinas virtuais do Azure](https://support.microsoft.com/help/4021388/support-for-32-bit-operating-systems-in-azure-virtual-machines)
* [Microsoft server software support for Microsoft Azure virtual machines](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) (Suporte de software de servidor da Microsoft para máquinas virtuais do Microsoft Azure)

## <a name="error-vhd-is-already-registered-with-image-repository-as-the-resource"></a>Erro: VHD já está registado com repositório de imagem como recurso

Sempre que tento criar uma imagem dos meus VHDs, recebo o erro "VHD já está registado com o repositório de imagem como o recurso" na Azure PowerShell. Não criei nenhuma imagem antes, nem encontrei qualquer imagem com este nome em Azure. Como posso resolver isto?

Este problema geralmente aparece se você criou um VM a partir de um VHD que tem um bloqueio nele. Confirme que não existe nenhum VM atribuído a partir deste VHD e, em seguida, retente a operação. Se esta questão continuar, abra um bilhete de apoio. Ver [Suporte para Partner Center](https://docs.microsoft.com/azure/marketplace/partner-center-portal/support).
