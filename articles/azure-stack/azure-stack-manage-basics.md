---
title: Noções básicas de administração do Azure Stack | Documentos da Microsoft
description: Saiba o que precisa saber para administrar o Azure Stack.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: 856738a7-1510-442a-88a8-d316c67c757c
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/12/2019
ms.author: jeffgilb
ms.lastreviewed: 10/15/2018
ms.openlocfilehash: ba1e7d7f9226b3ca42d4117be33b9c236f0557b0
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56867325"
---
# <a name="azure-stack-administration-basics"></a>Noções básicas de administração do Azure Stack
Há várias coisas que precisa saber se estiver familiarizado com a administração do Azure Stack. Este artigo fornece uma descrição geral da sua função como um operador do Azure Stack, e o que precisa dizer aos utilizadores para os mesmos para se tornar produtivos rapidamente.

## <a name="understand-the-builds"></a>Compreender as compilações

### <a name="integrated-systems"></a>Sistemas integrados

Se estiver a utilizar um sistema integrado do Azure Stack, versões atualizadas do Azure Stack são distribuídas por meio de pacotes de atualização. Pode importar estes pacotes e aplicá-las usando o mosaico de atualizações no portal do administrador. Não é suportada para atualizar instalações ASDK. 
 
### <a name="development-kit"></a>Kit de desenvolvimento

Se estiver usando o Kit de desenvolvimento do Azure Stack, reveja os [o que é o Azure Stack?](./asdk/asdk-what-is.md) artigo para se certificar de que compreende o objetivo do kit de desenvolvimento e suas limitações. Deve usar o kit de desenvolvimento como uma "área de segurança" onde pode avaliar o Azure Stack e desenvolver e testar seus aplicativos num ambiente de não produção. (Para informações de implementação, consulte a [implementação do Development Kit do Azure Stack](./asdk/asdk-install.md) artigo.)

Como o Azure, podemos inovar rapidamente. Vamos lançar regularmente novas compilações. Se estiver a executar o kit de desenvolvimento e pretender mover para a compilação mais recente, deve [Reimplementar o Azure Stack](./asdk/asdk-redeploy.md). Não é possível aplicar os pacotes de atualização. Este processo demora tempo, mas a vantagem é que pode experimentar as funcionalidades mais recentes. A documentação do kit de desenvolvimento no nosso site reflete a compilação de versão mais recente.

## <a name="learn-about-available-services"></a>Conheça os serviços disponíveis

Precisará de uma deteção de quais serviços pode disponibilizar aos seus utilizadores. O Azure Stack suporta um subconjunto de serviços do Azure. A lista de serviços com suporte continuará a evoluir.

**Serviços básicos**

Por predefinição, o Azure Stack inclui os seguintes "fundamentais serviços" ao implementar o Azure Stack:

- Computação
- Armazenamento
- Redes
- Cofre de Chaves

Com estes serviços fundamentais, pode oferecer infraestrutura-como-serviço (IaaS) aos seus utilizadores com configuração mínima.

**Serviços adicionais**

Atualmente, suportamos os seguintes serviços de plataforma-como-serviço (PaaS) adicionais:

- Serviço de Aplicações
- Funções do Azure
- Bases de dados SQL e o MySQL

Estes serviços exigem configuração adicional antes de pode disponibilizá-los aos seus utilizadores. Para obter mais informações, consulte o "tutorial" e as secções "serviços de procedimentos guides\Offer" da nossa documentação de operador do Azure Stack.

**Plano de serviço**

O Azure Stack vai continuar a adicionar suporte para serviços do Azure. Para o plano da prevista, consulte o [Azure Stack: Uma extensão do Azure](https://go.microsoft.com/fwlink/?LinkId=842846&clcid=0x409) White Paper. Também pode monitorizar o [mensagens de blogue do Azure Stack](https://azure.microsoft.com/blog/tag/azure-stack-technical-preview) para novos anúncios.

## <a name="what-account-should-i-use"></a>Que conta devo utilizar?
Existem algumas considerações de conta que deve estar atento ao gerir o Azure Stack. Especialmente em Implantações usando o Windows Server Active Directory Federation Services (AD FS), como o fornecedor de identidade em vez do Azure Active Directory (Azure AD). As seguintes considerações de conta aplicam-se a sistemas integrados do Azure Stack e implementações de ASDK:


|Conta|Azure AD|AD FS|
|-----|-----|-----|
|Administrador local (. \Administrator)|Administrador de anfitrião ASDK|Administrador de anfitrião ASDK|
|AzureStack\AzureStackAdmin|Administrador de anfitrião ASDK<br><br>Pode ser utilizado para iniciar sessão no portal de administração do Azure Stack<br><br>Acesso para ver e administrar anéis do Service Fabric|Administrador de anfitrião ASDK<br><br>Sem acesso ao portal de administração do Azure Stack<br><br>Acesso para ver e administrar anéis do Service Fabric<br><br>Já não é proprietário da subscrição de fornecedor predefinido (DPS)|
|AzureStack\CloudAdmin|Pode aceder e executar comandos permitidos no ponto de extremidade com privilégios|Pode aceder e executar comandos permitidos no ponto de extremidade com privilégios<br><br>Não pode iniciar sessão para o anfitrião ASDK<br><br>Proprietário da subscrição do fornecedor padrão (DPS)|
|Administrador Global do Azure AD|Usados durante a instalação<br><br>Proprietário da subscrição do fornecedor padrão (DPS)|Não aplicável|
|

## <a name="what-tools-do-i-use-to-manage"></a>Que ferramentas posso utilizar para gerir?
 
Pode utilizar o [portal de administrador](azure-stack-manage-portals.md) ou o PowerShell para gerir o Azure Stack. A forma mais fácil de aprender os conceitos básicos é através do portal. Se pretender utilizar o PowerShell, existem passos de preparação. Deve [instale](azure-stack-powershell-install.md) PowerShell, [transferir](azure-stack-powershell-download.md) módulos adicionais, e [configurar](azure-stack-powershell-configure-admin.md) PowerShell.

O Azure Stack utiliza o Azure Resource Manager como seu mecanismo de implantação, gerenciamento e organização subjacente. Se for para gerir o Azure Stack e ajudar a dar suporte aos usuários, deve aprender sobre o Resource Manager. Consulte a [introdução ao Azure Resource Manager](https://download.microsoft.com/download/E/A/4/EA4017B5-F2ED-449A-897E-BD92E42479CE/Getting_Started_With_Azure_Resource_Manager_white_paper_EN_US.pdf) White Paper.

## <a name="your-typical-responsibilities"></a>Suas responsabilidades típicas

Os seus utilizadores pretendem utilizar os serviços. Da sua perspectiva, sua função principal é disponibilizar esses serviços a eles. Tem de decidir quais os serviços para oferecer e tornar esses serviços disponíveis através da criação de planos, ofertas e quotas. Para obter mais informações, consulte [descrição geral da oferta de serviços no Azure Stack](azure-stack-offer-services-overview.md). 

Também terá de adicionar itens a serem [marketplace](azure-stack-marketplace.md), como imagens de máquina virtual. A maneira mais fácil é [transferir itens do marketplace do Azure para o Azure Stack](azure-stack-download-azure-marketplace-item.md).

> [!NOTE]
> Se pretender testar seus planos, ofertas e serviços, deve utilizar o [portal de utilizador](azure-stack-manage-portals.md); não o portal de administrador.

Além de fornecer serviços, tem de efetuar todas as tarefas regulares de um operador para manter a pilha do Azure em execução. Estas tarefas incluem o seguinte:

- Adicionar contas de utilizador (para [do Azure Active Directory](azure-stack-add-new-user-aad.md) implementação ou para [serviços de Federação do Active Directory](azure-stack-add-users-adfs.md) implementação)
- [Atribuir funções (RBAC) do controlo de acesso baseado em funções](azure-stack-manage-permissions.md) (isso não é restrito aos administradores.)
- [Infraestrutura de monitor do Estado de funcionamento](azure-stack-monitor-health.md)
- Gerir [rede](azure-stack-viewing-public-ip-address-consumption.md) e [armazenamento](azure-stack-manage-storage-accounts.md) recursos
- Substituir o hardware defeituoso, por exemplo [substituir um disco com falha](azure-stack-replace-disk.md).

## <a name="what-to-tell-your-users"></a>O que dizer aos utilizadores

Terá de permitir que os utilizadores saber como trabalhar com os serviços no Azure Stack, como se pode ligar para o ambiente e como subscrever ofertas. Além de qualquer da documentação que talvez queira fornecer aos utilizadores, pode direcionar os utilizadores para o site de documentação de utilizadores do Azure Stack.

**Compreender como trabalhar com os serviços no Azure Stack**

Há informações que os utilizadores tem de compreender antes de poderem utilizam os serviços e crie aplicações no Azure Stack. Por exemplo, existem requisitos específicos de PowerShell e a API da versão. Além disso, existem algumas diferenças de funcionalidade entre um serviço no Azure e o serviço equivalente no Azure Stack. Certifique-se de que os utilizadores, reveja os artigos seguintes:

- [Considerações-chave: Utilizar os serviços ou criar aplicativos para o Azure Stack](user/azure-stack-considerations.md)
- [Considerações para máquinas virtuais no Azure Stack](user/azure-stack-vm-considerations.md)
- [Armazenamento: diferenças e considerações](user/azure-stack-acs-differences.md)

As informações nestes artigos resume as diferenças entre um serviço no Azure e o Azure Stack. Complementa as informações de que estão disponíveis para um serviço do Azure na documentação do Azure global.

**Ligar ao Azure Stack como um utilizador**

Num ambiente do kit de desenvolvimento, se um utilizador não tem acesso de ambiente de trabalho remoto para o anfitrião do kit de desenvolvimento, eles tem de configurar uma ligação de rede privada virtual (VPN) antes de poderem aceder ao Azure Stack. Ver [ligar ao Azure Stack](azure-stack-connect-azure-stack.md). 

Os utilizadores vão querer saber como [aceder ao portal de utilizador](user/azure-stack-use-portal.md) ou como se pode ligar através do PowerShell. Num ambiente de sistemas integrados, o endereço de portal de utilizador varia por implementação. Terá de fornecer aos utilizadores com o URL correto.

Se utilizar o PowerShell, os utilizadores podem ter que registar os fornecedores de recursos antes de poderem utilizar os serviços. (Um fornecedor de recursos gere um serviço. Por exemplo, o fornecedor de recursos de rede gere recursos, tais como redes virtuais, interfaces de rede e Balanceadores de carga.) Eles devem [instale](user/azure-stack-powershell-install.md) PowerShell, [transferir](user/azure-stack-powershell-download.md) módulos adicionais, e [configurar](user/azure-stack-powershell-configure-user.md) PowerShell (que inclui o registo do fornecedor de recursos).

**Subscrever uma oferta**

Antes de um utilizador pode aceder a serviços, eles devem [subscrever uma oferta](azure-stack-subscribe-plan-provision-vm.md) que criou como um operador.

## <a name="where-to-get-support"></a>Onde obter suporte

### <a name="integrated-systems"></a>Sistemas integrados

Para um sistema integrado, existe uma coordenada de escalamento e o processo de resolução entre a Microsoft e os nossos parceiros de hardware do fabricante de equipamento original (OEM).

Se houver um problema de serviços cloud, o suporte é oferecido por meio da Microsoft dos clientes suportar (CSS). Se clicar no ícone de ajuda e suporte (ponto de interrogação) no canto superior direito do portal do administrador e, em seguida, clique em **novo pedido de suporte**, esta ação abre um site onde diretamente pode abrir um pedido de suporte.

Se houver um problema com a implementação, patch e atualização de hardware (incluindo unidades de substituível em campo) e qualquer software com a marca de hardware, como o software em execução no anfitrião de ciclo de vida de hardware, contacte o fornecedor do hardware de OEM primeiro.

Para mais nada, contacte o CSS da Microsoft.

### <a name="development-kit"></a>Kit de desenvolvimento

Para o kit de desenvolvimento, pode fazer perguntas relacionadas ao suporte a [fóruns da Microsoft](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack). Se clicar no ícone de ajuda e suporte (ponto de interrogação) no canto superior direito do portal do administrador e, em seguida, clique em **novo pedido de suporte**, esta ação abre o site dos fóruns diretamente. Esses fóruns são monitorados regularmente. Como o kit de desenvolvimento é um ambiente de avaliação, não há nenhum oficial de suporte oferecido por meio da CSS do Microsoft.

## <a name="next-steps"></a>Passos Seguintes

[Gestão da região no Azure Stack](azure-stack-region-management.md)


