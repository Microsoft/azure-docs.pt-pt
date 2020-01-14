---
title: Problemas comuns durante a criação do VHD (FAQ) para o Azure Marketplace
description: Perguntas frequentes sobre a criação de VHD e problemas associados.
services: Azure Marketplace
author: MaggiePucciEvans
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 10/02/2018
ms.author: evansma
ms.reviewer: v-divte; v-miclar
ms.openlocfilehash: 152a2f6fda081b2612197cc4848207b336adf8dc
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/14/2020
ms.locfileid: "75934264"
---
# <a name="common-issues-during-vhd-creation-faq"></a>Problemas comuns durante a criação do VHD (FAQ)

As perguntas frequentes a seguir abordam problemas comuns encontrados durante o VHD (disco rígido virtual) e a criação de VM (máquina virtual) para ofertas de VM. 

## <a name="how-do-you-create-a-vm-from-the-azure-portal-using-the-vhd-that-is-uploaded-to-premium-storage"></a>Como criar uma VM do portal do Azure usando o VHD que é carregado no armazenamento Premium?

Atualmente, o Azure Marketplace não dá suporte à criação de ofertas de VM de imagens que residem no armazenamento gerenciado ou no armazenamento Premium do Azure.  Para obter mais informações sobre essas opções de armazenamento, consulte [visão geral do Azure Managed disks](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview).


## <a name="can-you-use-generation-2-vms-for-offers"></a>Você pode usar VMs de geração 2 para ofertas?

Não, somente VHDs de geração 1 têm suporte.  No entanto, estamos trabalhando atualmente com a equipe da plataforma Microsoft Azure para investigar o suporte para VMs de geração 2.  Para obter mais informações sobre as diferenças, consulte [devo criar uma máquina virtual de geração 1 ou 2 no Hyper-V?](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v)


## <a name="how-do-you-change-the-name-of-the-host"></a>Como alterar o nome do host?

Você não pode.  Depois que a VM é criada, os usuários (incluindo os proprietários) não podem atualizar o nome do host.


## <a name="how-do-you-reset-the-remote-desktop-service-or-its-sign-in-password"></a>Como você redefine o serviço de Área de Trabalho Remota ou sua senha de entrada?

Os artigos a seguir explicam como executar redefinições de RDS para VMs baseadas em Windows e Linux:   

- [Como repor o serviço Ambiente de Trabalho Remoto ou a respetiva palavra-passe de início de sessão numa VM do Windows](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-reset-rdp/)
- [Como redefinir uma senha de VM do Linux ou chave SSH, corrigir a configuração de SSH e verificar a consistência do disco usando a extensão VMAccess](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-classic-reset-access/)


## <a name="how-do-you-generate-new-ssh-certificates"></a>Como você gera novos certificados SSH?

A geração de certificados é explicada no artigo [obter o URI de assinatura de acesso compartilhado para sua imagem de VM](./cpp-get-sas-uri.md) na seção subsequente [criar ativos técnicos para uma oferta de VM](./cpp-create-technical-assets.md).


## <a name="how-do-you-configure-a-virtual-private-network-vpn-to-work-with-my-vms"></a>Como configurar uma VPN (rede virtual privada) para trabalhar com minhas VMs?

Se você estiver usando o modelo de implantação Azure Resource Manager, terá três opções comuns de configuração de uma VPN:
- [Criar um gateway de VPN baseado em rota usando o portal do Azure](https://docs.microsoft.com/azure/vpn-gateway/create-routebased-vpn-gateway-portal)
- [Criar um gateway de VPN baseado em rota usando o PowerShell](https://docs.microsoft.com/azure/vpn-gateway/create-routebased-vpn-gateway-powershell)
- [Criar um gateway de VPN baseado em rota usando a CLI](https://docs.microsoft.com/azure/vpn-gateway/create-routebased-vpn-gateway-cli)


## <a name="what-are-microsoft-support-policies-for-running-microsoft-server-software-on-azure-based-vms"></a>Quais são as políticas de suporte da Microsoft para executar o software de servidor da Microsoft em VMs baseadas no Azure?

Essas políticas de suporte são detalhadas no artigo [suporte a software de servidor da Microsoft para Microsoft Azure máquinas virtuais](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines).


## <a name="do-virtual-machines-have-unique-identifiers-associated-with-them"></a>As máquinas virtuais têm identificadores exclusivos associados a elas?

Sim, se hospedado no Azure.  O Azure atribui um identificador exclusivo, denominado ID exclusiva da máquina virtual do Azure, a cada novo recurso da VM criado.  Para obter mais informações, leia a postagem do blog [ID exclusiva da máquina virtual do Azure](https://blogs.msdn.microsoft.com/wasimbloch/2016/10/20/azure-virtual-machine-unique-id/).  Você também pode obter esse identificador programaticamente por meio da [API de lista](https://docs.microsoft.com/rest/api/compute/virtualmachines/list).


## <a name="in-a-vm-how-do-you-manage-the-custom-script-extension-in-the-startup-task"></a>Em uma VM, como você gerencia a extensão de script personalizado na tarefa de inicialização?

O artigo a seguir fornece detalhes sobre como usar a extensão de script personalizado usando o módulo Azure PowerShell, modelos de Azure Resource Manager e detalhes de solução de problemas em sistemas Windows: [extensão de script personalizado para Windows](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-extensions-customscript/)


## <a name="are-32-bit-applications-or-services-supported-in-the-azure-marketplace"></a>Há suporte para aplicativos de 32 bits ou serviços no Azure Marketplace?

Geralmente, não.  Os sistemas operacionais com suporte e os serviços padrão para VMs do Azure são todos de 64 bits.  No entanto, do ponto de vista técnico, a maioria dos sistemas operacionais de 64 bits dá suporte à execução de versões de 32 bits de aplicativos para compatibilidade com versões anteriores.  No entanto, o uso de aplicativos de 32 bits como parte de sua solução de VM não tem suporte e, portanto, é *altamente desencorajado*.  Em vez disso, recompile seu aplicativo como um projeto de 64 bits.

Para obter mais informações, veja os artigos seguintes:
- [Executando aplicativos de 32 bits](https://docs.microsoft.com/windows/desktop/WinProg64/running-32-bit-applications)
- [Suporte para sistemas operacionais de 32 bits em máquinas virtuais do Azure](https://support.microsoft.com/help/4021388/support-for-32-bit-operating-systems-in-azure-virtual-machines)
- [Microsoft server software support for Microsoft Azure virtual machines](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) (Suporte de software de servidor da Microsoft para máquinas virtuais do Microsoft Azure)


## <a name="every-time-i-try-to-create-an-image-from-my-vhds-i-get-the-error-vhd-is-already-registered-with-image-repository-as-the-resource-in-powershell-i-did-not-create-any-image-before-nor-did-i-find-any-image-with-this-name-in-azure-how-do-i-resolve-this-issue"></a>Toda vez que tento criar uma imagem de meus VHDs, obtenho o erro `.VHD is already registered with image repository as the resource` no PowerShell. Eu não criei nenhuma imagem antes nem encontrei nenhuma imagem com esse nome no Azure. Como fazer resolver esse problema?

Esse problema geralmente ocorre se o usuário provisionou uma VM de um VHD que tem um bloqueio.  Verifique se não há nenhuma VM alocada a partir deste VHD e repita a operação.  Se esse problema persistir, abra um tíquete de suporte, conforme explicado em [suporte para Portal do Cloud Partner](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-support-for-cloud-partner-portal). 

