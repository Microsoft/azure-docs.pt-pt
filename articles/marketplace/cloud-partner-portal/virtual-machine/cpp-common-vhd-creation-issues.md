---
title: Problemas comuns durante a criação do VHD (FAQ) para o Azure Marketplace
description: Perguntas mais frequentes sobre a criação do VHD e os problemas associados.
services: Azure Marketplace
author: HannibalSII
ms.service: marketplace
ms.topic: article
ms.date: 10/02/2018
ms.author: hascipio; v-divte; v-miclar
ms.openlocfilehash: d8b532793282db18d6182237bb921bc118ea717b
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/30/2019
ms.locfileid: "64938438"
---
# <a name="common-issues-during-vhd-creation-faq"></a>Problemas comuns durante a criação do VHD (FAQ)

As seguintes perguntas mais frequentes (FAQ) capa de problemas comuns encontrados durante o disco rígido virtual (VHD) e a máquina virtual criação (VM) para as ofertas VM. 

## <a name="how-do-you-create-a-vm-from-the-azure-portal-using-the-vhd-that-is-uploaded-to-premium-storage"></a>Como criar uma VM a partir do portal do Azure com o VHD que é carregado para o armazenamento premium?

O Azure Marketplace não suporta atualmente a criação de VM de ofertas de imagens que residem no armazenamento gerido ou de armazenamento Premium do Azure.  Para obter mais informações sobre estas opções de armazenamento, consulte [descrição geral de discos geridos do Azure](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview).


## <a name="can-you-use-generation-2-vms-for-offers"></a>Pode usar as VMs de geração 2 para as ofertas?

Não, apenas geração são suportadas 1 VHDs.  No entanto, estamos atualmente a trabalhar com a equipe da plataforma Microsoft Azure para investigar o suporte para VMs de geração 2.  Para obter mais informações sobre as diferenças, veja [deve criar máquinas virtuais de geração 1 ou 2 no Hyper-V?](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v)


## <a name="how-do-you-change-the-name-of-the-host"></a>Como alterar o nome do anfitrião?

Não é possível.  Assim que a VM é criada, os usuários (inclusive proprietários) não é possível atualizar o nome do anfitrião.


## <a name="how-do-you-reset-the-remote-desktop-service-or-its-sign-in-password"></a>Como repor o serviço de ambiente de trabalho remoto ou a palavra-passe início de sessão?

Os artigos seguintes explicam como efetuar reposições de RDS para VMs baseadas no Windows e Linux:   

- [Como repor o serviço Ambiente de Trabalho Remoto ou a respetiva palavra-passe de início de sessão numa VM do Windows](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-reset-rdp/)
- [Como repor uma palavra-passe de VM do Linux ou uma chave SSH, corrija a configuração de SSH e verificar a consistência de disco com a extensão VMAccess](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-classic-reset-access/)


## <a name="how-do-you-generate-new-ssh-certificates"></a>Como pode gerar novos certificados SSH?

Geração de certificados é explicada no artigo [URI de assinatura de acesso partilhado Get para a imagem de VM](./cpp-get-sas-uri.md) na secção subsequente [crie recursos técnicos para uma oferta VM](./cpp-create-technical-assets.md).


## <a name="how-do-you-configure-a-virtual-private-network-vpn-to-work-with-my-vms"></a>Como configurar uma rede privada virtual (VPN) para trabalhar com as minhas VMs?

Se estiver a utilizar o modelo de implementação Azure Resource Manager, em seguida, tem três opções comuns de configuração de uma VPN:
- [Criar um gateway VPN baseado na rota com o portal do Azure](https://docs.microsoft.com/azure/vpn-gateway/create-routebased-vpn-gateway-portal)
- [Criar um gateway VPN baseado na rota com o PowerShell](https://docs.microsoft.com/azure/vpn-gateway/create-routebased-vpn-gateway-powershell)
- [Criar um gateway VPN baseado na rota com a CLI](https://docs.microsoft.com/azure/vpn-gateway/create-routebased-vpn-gateway-cli)


## <a name="what-are-microsoft-support-policies-for-running-microsoft-server-software-on-azure-based-vms"></a>O que são políticas de suporte da Microsoft para executar o software de servidor Microsoft em VMs baseadas no Azure?

Estas políticas são detalhadas no artigo de suporte [suporte de software de servidor Microsoft para máquinas de virtuais do Microsoft Azure](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines).


## <a name="do-virtual-machines-have-unique-identifiers-associated-with-them"></a>Máquinas virtuais têm identificadores exclusivos associados a eles?

Sim, se estiver alojado no Azure.  O Azure atribui um identificador exclusivo, com o nome Máquina Virtual o Azure, o ID exclusivo para cada novo recurso VM é criado.  Para obter mais informações, leia a postagem no blog [ID exclusivo da Máquina Virtual do Azure](https://blogs.msdn.microsoft.com/wasimbloch/2016/10/20/azure-virtual-machine-unique-id/).  Também pode obter este identificador programaticamente através da [API de lista](https://docs.microsoft.com/rest/api/compute/virtualmachines/list).


## <a name="in-a-vm-how-do-you-manage-the-custom-script-extension-in-the-startup-task"></a>Numa VM, como gerir a extensão de script personalizado na tarefa de arranque?

O artigo seguinte fornece detalhes sobre como utilizar a extensão de Script personalizado com o módulo Azure PowerShell, modelos Azure Resource Manager e detalhes de resolução de problemas de etapas em sistemas Windows: [Extensão de Script personalizado para Windows](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-extensions-customscript/)


## <a name="are-32-bit-applications-or-services-supported-in-the-azure-marketplace"></a>São suportados no Azure Marketplace de serviços ou aplicações de 32 bits?

Geralmente, não.  Os sistemas operativos suportados e os serviços padrão para VMs do Azure são todos os de 64 bits.  No entanto, do ponto de vista técnico, mais sistemas de operativos de 64 bits suportam executando versões de 32 bits dos aplicativos para compatibilidade com versões anteriores.  No entanto, usar aplicativos de 32 bits, como parte da sua solução VM não é suportado e é por isso *altamente desencorajado*.  Em vez disso, recompilação do seu aplicativo como um projeto de 64 bits.

Para obter mais informações, veja os artigos seguintes:
- [Aplicativos de 32 bits em execução](https://docs.microsoft.com/windows/desktop/WinProg64/running-32-bit-applications)
- [Suporte para sistemas de operativos de 32 bits em máquinas virtuais do Azure](https://support.microsoft.com/help/4021388/support-for-32-bit-operating-systems-in-azure-virtual-machines)
- [Microsoft server software support for Microsoft Azure virtual machines](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) (Suporte de software de servidor da Microsoft para máquinas virtuais do Microsoft Azure)


## <a name="every-time-i-try-to-create-an-image-from-my-vhds-i-get-the-error-vhd-is-already-registered-with-image-repository-as-the-resource-in-powershell-i-did-not-create-any-image-before-nor-did-i-find-any-image-with-this-name-in-azure-how-do-i-resolve-this-issue"></a>Sempre que tento criar uma imagem de meus VHDs, recebo a mensagem de erro `.VHD is already registered with image repository as the resource` no PowerShell. Eu não criei nenhuma imagem antes nem foi encontrado qualquer imagem com este nome no Azure. Como posso resolver este problema?

Este problema ocorre, normalmente, se o utilizador aprovisionado uma VM a partir de um VHD que tem um bloqueio.  Certifique-se de que não existe nenhuma VM alocada a partir deste VHD e, em seguida, repita a operação.  Se este problema persistir, abra um pedido de suporte, conforme explicado [suporte para a Cloud Partner Portal](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-support-for-cloud-partner-portal). 

