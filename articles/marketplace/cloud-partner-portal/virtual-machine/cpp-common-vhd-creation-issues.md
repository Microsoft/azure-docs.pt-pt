---
title: Questões comuns durante a criação de VHD (FAQ) para o Mercado Azure
description: Perguntas frequentes sobre a criação de VHD e questões associadas.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 10/02/2018
ms.author: dsindona
ms.openlocfilehash: 988121effb35949cce442ba9458789105e88feec
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81273448"
---
# <a name="common-issues-during-vhd-creation-faq"></a>Questões comuns durante a criação de VHD (FAQ)

> [!IMPORTANT]
> A partir de 13 de abril de 2020, começaremos a mover a gestão das suas ofertas de Máquina Virtual Azure para partner Center. Após a migração, você vai criar e gerir suas ofertas no Partner Center. Siga as instruções em [Create VHD FAQs](https://aka.ms/VHDcreationIssues) para gerir as suas ofertas migratórias.

As seguintes perguntas frequentes (FAQ) cobrem questões comuns encontradas durante o disco rígido virtual (VHD) e a criação de máquina virtual (VM) para ofertas vm. 

## <a name="how-do-you-create-a-vm-from-the-azure-portal-using-the-vhd-that-is-uploaded-to-premium-storage"></a>Como criar um VM a partir do portal Azure utilizando o VHD que é carregado para armazenamento premium?

Atualmente, o Azure Marketplace não suporta a criação de ofertas VM a partir de imagens que residem no armazenamento gerido ou no Armazenamento Premium Azure.  Para obter mais informações sobre estas opções de armazenamento, consulte a [visão geral dos discos geridos](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview)pelo Azure .


## <a name="can-you-use-generation-2-vms-for-offers"></a>Pode usar vMs de geração 2 para ofertas?

Não, apenas os VHDs da geração 1 são apoiados.  No entanto, estamos neste momento a trabalhar com a Equipa de Plataforma Microsoft Azure para investigar o suporte para os VMs da geração 2.  Para mais informações sobre as diferenças, consulte se devo criar uma máquina virtual de [geração 1 ou 2 em Hyper-V?](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v)


## <a name="how-do-you-change-the-name-of-the-host"></a>Como muda o nome do hospedeiro?

Não pode.  Uma vez que a VM é criada, os utilizadores (incluindo os proprietários) não podem atualizar o nome do anfitrião.


## <a name="how-do-you-reset-the-remote-desktop-service-or-its-sign-in-password"></a>Como redefinir o serviço Remote Desktop ou a sua senha de entrada?

Os seguintes artigos explicam como realizar resets RDS para VMs baseados no Windows e Linux:   

- [Como repor o serviço Ambiente de Trabalho Remoto ou a respetiva palavra-passe de início de sessão numa VM do Windows](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-reset-rdp/)
- [Como redefinir uma palavra-passe Linux VM ou uma chave SSH, corrigir a configuração SSH e verificar a consistência do disco utilizando a extensão VMAccess](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-classic-reset-access/)


## <a name="how-do-you-generate-new-ssh-certificates"></a>Como gera novos certificados SSH?

Geração de certificados é explicada no artigo Obter assinatura de [acesso partilhado URI para a sua imagem VM](./cpp-get-sas-uri.md) na secção subsequente Criar ativos [técnicos para uma oferta VM](./cpp-create-technical-assets.md).


## <a name="how-do-you-configure-a-virtual-private-network-vpn-to-work-with-my-vms"></a>Como configurar uma rede privada virtual (VPN) para trabalhar com os meus VMs?

Se estiver a utilizar o modelo de implementação do Gestor de Recursos Azure, então tem três opções comuns de configurar uma VPN:
- [Criar um gateway VPN baseado em rota usando o portal Azure](https://docs.microsoft.com/azure/vpn-gateway/create-routebased-vpn-gateway-portal)
- [Criar um gateway VPN baseado em rotas usando powerShell](https://docs.microsoft.com/azure/vpn-gateway/create-routebased-vpn-gateway-powershell)
- [Criar um gateway VPN baseado em rota usando cli](https://docs.microsoft.com/azure/vpn-gateway/create-routebased-vpn-gateway-cli)


## <a name="what-are-microsoft-support-policies-for-running-microsoft-server-software-on-azure-based-vms"></a>Quais são as políticas de suporte da Microsoft para executar o software de servidores da Microsoft em VMs baseados no Azure?

Estas políticas de suporte são detalhadas no artigo suporte de software do [servidor Microsoft para máquinas virtuais Microsoft Azure](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines).


## <a name="do-virtual-machines-have-unique-identifiers-associated-with-them"></a>As máquinas virtuais têm identificadores únicos associados a eles?

Sim, se hospedado no Azure.  O Azure atribui um identificador único, denominado Id Exclusivo máquina virtual Azure a cada novo recurso VM que é criado.  Para mais informações, leia o post de blog [Azure Virtual Machine Unique ID](https://blogs.msdn.microsoft.com/wasimbloch/2016/10/20/azure-virtual-machine-unique-id/).  Também pode obter este identificador programáticamente através da [Lista API](https://docs.microsoft.com/rest/api/compute/virtualmachines/list).


## <a name="in-a-vm-how-do-you-manage-the-custom-script-extension-in-the-startup-task"></a>Num VM, como gere a extensão personalizada do script na tarefa de arranque?

O seguinte artigo detalha como usar a extensão do script personalizado utilizando o módulo PowerShell Azure, modelos do Gestor de Recursos Azure e detalha passos de resolução de problemas nos sistemas Windows: [Extensão personalizada do script para windows](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-extensions-customscript/)


## <a name="are-32-bit-applications-or-services-supported-in-the-azure-marketplace"></a>As aplicações ou serviços de 32 bits são suportados no Mercado Azure?

Geralmente, não.  Os sistemas operativos suportados e os serviços standard para Os VMs Azure são todos de 64 bits.  No entanto, do ponto de vista técnico, a maioria dos sistemas operativos de 64 bits suportam versões de aplicações de 32 bits para retrocompatibilidade.  No entanto, a utilização de aplicações de 32 bits como parte da sua solução VM não é suportada e, portanto, é *altamente desencorajada*.  Em vez disso, recompile a sua aplicação como um projeto de 64 bits.

Para obter mais informações, veja os artigos seguintes:
- [Executar aplicações de 32 bits](https://docs.microsoft.com/windows/desktop/WinProg64/running-32-bit-applications)
- [Suporte para sistemas operativos de 32 bits nas máquinas virtuais do Azure](https://support.microsoft.com/help/4021388/support-for-32-bit-operating-systems-in-azure-virtual-machines)
- [Microsoft server software support for Microsoft Azure virtual machines](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) (Suporte de software de servidor da Microsoft para máquinas virtuais do Microsoft Azure)


## <a name="every-time-i-try-to-create-an-image-from-my-vhds-i-get-the-error-vhd-is-already-registered-with-image-repository-as-the-resource-in-powershell-i-did-not-create-any-image-before-nor-did-i-find-any-image-with-this-name-in-azure-how-do-i-resolve-this-issue"></a>Sempre que tento criar uma imagem dos meus VHDs, tenho o erro `.VHD is already registered with image repository as the resource` na PowerShell. Não criei nenhuma imagem antes, nem encontrei qualquer imagem com este nome em Azure. Como posso resolver esta questão?

Este problema ocorre geralmente se o utilizador forprovisionado um VM de um VHD que tem um bloqueio no mesmo.  Verifique se não há VM atribuído a partir deste VHD e, em seguida, tente novamente a operação.  Se esta questão persistir, abra um bilhete de apoio, como explica do [Portal de Suporte para Parceiros cloud.](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-support-for-cloud-partner-portal) 

