---
title: Servidor SQL em Máquinas Virtuais do Windows em FaQ Azure [ Microsoft Docs
description: Este artigo fornece respostas a perguntas frequentes sobre executar o SQL Server em VMs Azure.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: felixwu
editor: ''
tags: azure-service-management
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.service: virtual-machines-sql
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/05/2019
ms.author: mathoma
ms.openlocfilehash: 3b73c329c3db54ba78db15ced8e919af4d4a45d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249740"
---
# <a name="frequently-asked-questions-for-sql-server-running-on-windows-virtual-machines-in-azure"></a>Frequently asked questions for SQL Server running on Windows virtual machines in Azure (Perguntas frequentes sobre o SQL Server em execução em máquinas virtuais do Windows no Azure)

> [!div class="op_single_selector"]
> * [Windows](virtual-machines-windows-sql-server-iaas-faq.md)
> * [Linux](../../linux/sql/sql-server-linux-faq.md)

Este artigo fornece respostas a algumas das perguntas mais comuns sobre executar [o Servidor SQL em Máquinas Virtuais Windows em Azure](https://azure.microsoft.com/services/virtual-machines/sql-server/).

> [!NOTE]
> Este artigo foca-se em questões específicas para o SQL Server nos VMs do Windows. Se estiver a executar o SQL Server em VMs Linux, consulte o [Linux FAQ](../../linux/sql/sql-server-linux-faq.md).

[!INCLUDE [support-disclaimer](../../../../includes/support-disclaimer.md)]

## <a name="images"></a><a id="images"></a>Imagens

1. **Que imagens de galeria de máquinas virtuais SQL Server estão disponíveis?** 

   O Azure mantém imagens de máquinas virtuais para todas as grandes lançamentos suportadas do SQL Server em todas as edições tanto para Windows como Linux. Para mais informações, consulte a lista completa de [imagens VM do Windows](virtual-machines-windows-sql-server-iaas-overview.md#payasyougo) e [imagens VM Linux](../../linux/sql/sql-server-linux-virtual-machines-overview.md#create).

1. **As imagens de galeria de máquinas virtuais sQL Server existentes são atualizadas?**

   De dois em dois meses, as imagens do SQL Server na galeria de máquinas virtuais são atualizadas com as mais recentes atualizações do Windows e DoLinux. Para imagens do Windows, isto inclui quaisquer atualizações que sejam marcadas como importantes no Windows Update, incluindo importantes atualizações de segurança do SQL Server e pacotes de serviços. Para as imagens do Linux, isto inclui as mais recentes atualizações do sistema. As atualizações cumulativas do SQL Server são tratadas de forma diferente para o Linux e o Windows. Para o Linux, as atualizações cumulativas do SQL Server também estão incluídas na atualização. Mas, neste momento, os VMs do Windows não são atualizados com as atualizações cumulativas do SQL Server ou do Windows Server.

1. **As imagens de máquinas virtuais do SQL Server podem ser removidas da galeria?**

   Sim. O Azure apenas mantém uma imagem por versão e edição principais. Por exemplo, quando um novo pacote de serviços SQL Server é lançado, o Azure adiciona uma nova imagem à galeria para esse pacote de serviços. A imagem do SQL Server para o pacote de serviço anterior é imediatamente removida do portal Azure. No entanto, ainda está disponível para o fornecimento da PowerShell para os próximos três meses. Após três meses, a imagem anterior do pacote de serviço já não está disponível. Esta política de remoção também se aplicaria se uma versão SQL Server não for suportada quando chegar ao fim do seu ciclo de vida.


1. **É possível implementar uma imagem mais antiga do SQL Server que não seja visível no portal Azure?**

   Sim, usando powerShell. Para obter mais informações sobre a implementação de VMs do Servidor SQL utilizando powerShell, consulte [como fornecer máquinas virtuais SQL Server com O PowerShell Azure](virtual-machines-windows-ps-sql-create.md).

1. **Posso criar uma imagem generalizada do Mercado do Servidor Azure SQL do meu VM de servidor SQL e usá-lo para implementar VMs?**

   Sim, mas deve [então registar cada VM do Servidor SQL com o fornecedor de recursos SQL Server VM](virtual-machines-windows-sql-register-with-resource-provider.md) para gerir o seu VM de Servidor SQL no portal, bem como utilizar funcionalidades como patching automatizado e backups automáticos. Ao registar-se com o fornecedor de recursos, também terá de especificar o tipo de licença para cada VM do Servidor SQL. 

1. **Posso usar o meu próprio VHD para implantar um VM sQL Server?**

   Sim, mas deve [então registar cada VM do Servidor SQL com o fornecedor de recursos SQL Server VM](virtual-machines-windows-sql-register-with-resource-provider.md) para gerir o seu VM de Servidor SQL no portal, bem como utilizar funcionalidades como patching automatizado e backups automáticos.

1. **É possível configurar configurações não mostradas na galeria de máquinas virtuais (por exemplo, Windows 2008 R2 + SQL Server 2012)?**

   Não. Para imagens de galerias de máquinas virtuais que incluam o SQL Server, deve selecionar uma das imagens fornecidas através do portal Azure ou via [PowerShell](virtual-machines-windows-ps-sql-create.md). No entanto, tem a capacidade de implementar um Windows VM e de instalar-se auto-instalado no SQL Server. Em seguida, deve [registar o seu VM do Servidor SQL com o fornecedor de recursos SQL Server VM](virtual-machines-windows-sql-register-with-resource-provider.md) para gerir o seu VM do Servidor SQL no portal, bem como utilizar funcionalidades como patching automatizado e backups automáticos. 


## <a name="creation"></a>Criação

1. **Como posso criar uma máquina virtual Azure com o SQL Server?**

   O método mais fácil é criar uma Máquina Virtual que inclua o Servidor SQL. Para um tutorial sobre a inscrição no Azure e a criação de um VM SQL Server a partir do portal, consulte [a Provision a SQL Server virtual machine no portal Azure](virtual-machines-windows-portal-sql-server-provision.md). Pode selecionar uma imagem de máquina virtual que utiliza o licenciamento pay-per-segundo do SQL Server, ou pode utilizar uma imagem que lhe permita trazer a sua própria licença SQL Server. Também tem a opção de instalar manualmente o SQL Server num VM com uma edição livremente licenciada (Developer ou Express) ou reutilizando uma licença no local. Certifique-se de registar o seu VM do [Servidor SQL com o fornecedor de recursos SQL Server VM](virtual-machines-windows-sql-register-with-resource-provider.md) para gerir o seu VM do Servidor SQL no portal, bem como utilizar funcionalidades como patching automatizado e backups automáticos. Se trouxer a sua própria licença, tem de ter [licença de mobilidade através](https://azure.microsoft.com/pricing/license-mobility/)da Garantia de Software no Azure . Para obter mais informações, consulte [Pricing guidance for SQL Server Azure VMs (Documentação de orientação sobre preços de VMs do Azure do SQL Server)](virtual-machines-windows-sql-server-pricing-guidance.md).

1. **Como posso migrar a minha base de dados do SQL Server para a Cloud?**

   Primeiro crie uma máquina virtual Azure com uma instância SQL Server. Em seguida, emigra as bases de dados no local para esse caso. Para estratégias de migração de dados, consulte [Migrate uma base de dados do SQL Server para o SQL Server num VM Azure](virtual-machines-windows-migrate-sql.md).

## <a name="licensing"></a>Licenciamento

1. **Como posso instalar a minha cópia licenciada do SQL Server numa VM do Azure?**

   Há três maneiras de fazer isto. Se é cliente de acordo de empresa (EA), pode fornecer uma das [imagens de máquinas virtuais que suporta licenças](virtual-machines-windows-sql-server-iaas-overview.md#BYOL), que também é conhecida como bring-your-own-license (BYOL). Se tiver [garantiade software,](https://www.microsoft.com/en-us/licensing/licensing-programs/software-assurance-default)pode ativar o [Benefício Híbrido Azure](virtual-machines-windows-sql-ahb.md) numa imagem pay-as-you-go (PAYG) existente. Ou pode copiar os meios de instalação do Servidor SQL para um VM do Servidor Windows e, em seguida, instalar o Servidor SQL no VM. Certifique-se de registar o seu VM de Servidor SQL com o fornecedor de [recursos](virtual-machines-windows-sql-register-with-resource-provider.md) para funcionalidades como gestão de portal, backup automatizado e patching automatizado. 

1. **Posso alterar uma VM para utilizar a minha própria licença do SQL Server caso tenha sido criada com uma das imagens da galeria pay as you go?**

   Sim. Pode facilmente trocar uma imagem de galeria pay-as-you-go (PAYG) para trazer a sua própria licença (BYOL) permitindo o [Benefício Híbrido Azure](https://azure.microsoft.com/pricing/hybrid-benefit/faq/).  Para mais informações, consulte [Como alterar o modelo de licenciamento para um VM do Servidor SQL](virtual-machines-windows-sql-ahb.md). Atualmente, esta facilidade está disponível apenas para clientes de Public Cloud.

1. **A mudança do modelo de licenciamento requer algum período de inatividade do SQL Server?**

   Não. [A alteração do modelo](virtual-machines-windows-sql-ahb.md) de licenciamento não requer qualquer tempo de inatividade para o SQL Server, uma vez que a alteração é eficaz imediatamente e não requer um reinício do VM. No entanto, para registar o seu VM do Servidor SQL com o fornecedor de recursos SQL Server VM, a [extensão SQL IaaS](virtual-machines-windows-sql-server-agent-extension.md) é um pré-requisito e a instalação da extensão SQL IaaS em _modo completo_ reinicia o serviço SQL Server. Como tal, se a extensão SQL IaaS precisar de ser instalada, instale-a em modo _leve_ para uma funcionalidade limitada ou instale-a em _modo completo_ durante uma janela de manutenção. A extensão SQL IaaS instalada em modo _leve_ pode ser atualizada para o modo _completo_ a qualquer momento, mas requer um reinício do serviço SQL Server. 
   
1. **É possível mudar o modelo de licenciamento num VM SQL Server implantado usando modelo clássico?**

   Não. A alteração do modelo de licenciamento não é suportada num VM clássico. Pode migrar o seu VM para o modelo Do Gestor de Recursos Azure e registar-se junto do fornecedor de recursos VM do Servidor SQL. Uma vez registado o VM no fornecedor de recursos SQL Server VM, as alterações do modelo de licenciamento estarão disponíveis no VM.

1. **Posso usar o portal Azure para gerir várias instâncias no mesmo VM?**

   Não. A gestão do portal é uma funcionalidade fornecida pelo fornecedor de recursos SQL Server VM, que se baseia na extensão do Agente IaaS do Servidor SQL. Como tal, as mesmas limitações aplicam-se ao fornecedor de recursos quanto à extensão. O portal só pode gerir uma instância predefinida, ou uma instância nomeada, desde que tenha sido configurado corretamente. Para obter mais informações sobre estas limitações, consulte a extensão do [agente SQL Server IaaS](virtual-machines-windows-sql-server-agent-extension.md). 

1. **As subscrições de CSP podem ativar o Benefício Híbrido Azure?**

   Sim, o Azure Hybrid Benefit está disponível para subscrições de CSP. Os clientes da CSP devem primeiro implementar uma imagem pay-as-you-go e, em seguida, [alterar o modelo](virtual-machines-windows-sql-ahb.md) de licenciamento para trazer a sua própria licença.
   
 
1. **Tenho de pagar para licenciar o SQL Server numa VM do Azure se só estiver a ser utilizado para reserva/ativação pós-falha?**

   Para ter uma licença passiva gratuita para um grupo de disponibilidade secundária de prontidão ou instância agrupada, deve cumprir todos os seguintes critérios, tal como descrito sem acordo nos Termos de Licenciamento do [Produto:](https://www.microsoft.com/licensing/product-licensing/products)

   1. Tem [mobilidade de licença](https://www.microsoft.com/licensing/licensing-programs/software-assurance-license-mobility?activetab=software-assurance-license-mobility-pivot:primaryr2) através da Garantia de [Software.](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default?activetab=software-assurance-default-pivot%3aprimaryr3) 
   1. A instância passiva do Servidor SQL não serve dados do SQL Server aos clientes nem executa cargas de trabalho ativas do Servidor SQL. É usado apenas para sincronizar com o servidor primário e, de outra forma, manter a base de dados passiva em um estado de espera quente. Se estiver a servir dados, tais como relatórios a clientes que executam cargas de trabalho ativas do SQL Server, ou a realizar qualquer trabalho que não seja o especificado nos termos do produto, deve ser uma instância de Servidor SQL licenciada paga. A seguinte atividade é permitida na instância secundária: verificações de consistência da base de dados ou CheckDB, cópias de segurança completas, cópias de segurança de registo de transações e monitorização dos dados de utilização dos recursos. Também pode executar a primeira e correspondente instância de recuperação de desastres simultaneamente para breves períodos de testes de recuperação de desastres a cada 90 dias. 
   1. A licença ativa do SQL Server é coberta pela Garantia de Software e permite **uma** instância passiva secundária do SQL Server, com até à mesma quantidade de computação que o servidor ativo licenciado, apenas. 
   1. O VM secundário do Servidor SQL utiliza a licença de recuperação de [desastres](virtual-machines-windows-sql-high-availability-dr.md#free-dr-replica-in-azure) no portal Azure.
   
1. **O que é considerado um caso passivo?**

   A instância passiva do Servidor SQL não serve dados do SQL Server aos clientes nem executa cargas de trabalho ativas do Servidor SQL. É usado apenas para sincronizar com o servidor primário e, de outra forma, manter a base de dados passiva em um estado de espera quente. Se estiver a servir dados, tais como relatórios a clientes que executam cargas de trabalho ativas do SQL Server, ou a realizar qualquer trabalho que não seja o especificado nos termos do produto, deve ser uma instância de Servidor SQL licenciada paga. A seguinte atividade é permitida na instância secundária: verificações de consistência da base de dados ou CheckDB, cópias de segurança completas, cópias de segurança de registo de transações e monitorização dos dados de utilização dos recursos. Também pode executar a primeira e correspondente instância de recuperação de desastres simultaneamente para breves períodos de testes de recuperação de desastres a cada 90 dias.
   

1. **Que cenários podem utilizar o benefício distaster Recovery (DR) ?**

   O guia de [licenciamento](https://aka.ms/sql2019licenseguide) fornece cenários em que o Benefício de Recuperação de Desastres pode ser utilizado. Consulte os termos do produto e fale com os seus contactos de licenciamento ou gestor de conta para obter mais informações.

1. **Quais as subscrições que suportam o benefício de Recuperação de Desastres (DR) ?**

   Programas abrangentes que oferecem direitos de subscrição equivalentes de Garantia de Software como um benefício fixo suportam o benefício DR. Isto inclui. mas não se limita a, o Valor Aberto (OV), subscrição de valor aberto (OVS), Acordo de Empresa (EA), Acordo de Subscrição empresarial (EAS) e o Servidor e Inscrição em Nuvem (SCE). Consulte os termos do [produto](https://www.microsoft.com/licensing/product-licensing/products) e fale com os seus contactos de licenciamento ou com o gestor da acocunt para obter mais informações. 

   
 ## <a name="resource-provider"></a>Fornecedor de recursos

1. **Registar o meu VM com o novo fornecedor de recursos SQL Server VM trará custos adicionais?**

   Não. O fornecedor de recursos SQL Server VM apenas permite uma gestão adicional para o Servidor SQL no Azure VM sem encargos adicionais. 

1. **O fornecedor de recursos SQL Server VM está disponível para todos os clientes?**
 
   Sim, desde que o VM do Servidor SQL tenha sido implantado na nuvem pública usando o modelo De Gestor de Recursos, e não o modelo clássico. Todos os outros clientes podem registar-se com o novo fornecedor de recursos SQL Server VM. No entanto, apenas os clientes com o benefício [de Garantia](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default?activetab=software-assurance-default-pivot%3aprimaryr3) de Software podem usar a sua própria licença ativando o [Azure Hybrid Benefit (AHB)](https://azure.microsoft.com/pricing/hybrid-benefit/) num VM do Servidor SQL. 

1. **O que acontece ao fornecedor de recursos _(Microsoft.SqlVirtualMachine_) se o recurso VM for movido ou abandonado?** 

   Quando o recurso Microsoft.Compute/VirtualMachine é retirado ou movido, então o recurso Microsoft.SqlVirtualMachine associado é notificado para replicar assíncronamente a operação.

1. **O que acontece ao VM se o recurso _(Microsoft.SqlVirtualMachine_) for retirado?**

    O recurso Microsoft.Compute/VirtualMachine não é afetado quando o recurso Microsoft.SqlVirtualMachine é retirado. No entanto, as alterações de licenciamento irão voltar à fonte de imagem original. 

1. **É possível registar VMs de servidor SQL auto-implantados com o fornecedor de recursos VM do Servidor SQL?**

    Sim. Se implementou o SQL Server a partir dos seus próprios meios de comunicação e instalou a extensão SQL IaaS, pode registar o seu VM sQL Server com o fornecedor de recursos para obter os benefícios de gestão fornecidos pela extensão SQL IaaS. No entanto, não é capaz de converter um VM De Servidor SQL auto-implantado para pagar-as-se-go.


   


## <a name="administration"></a>Administração

1. **Posso instalar uma segunda instância de SQL Server no mesmo VM? Posso alterar as funcionalidades instaladas da instância predefinida?**

   Sim. O suporte de instalação do SQL Server está localizado numa pasta na unidade **C.** Executar **Configuração.exe** a partir desse local para adicionar novas instâncias do Servidor SQL ou para alterar outras funcionalidades instaladas do Servidor SQL na máquina. Note que algumas funcionalidades, tais como Backup Automatizado, Patching Automatizado e Integração do Cofre de Chaves Azure, operam apenas contra a instância predefinida, ou uma instância nomeada que foi configurada corretamente (Ver Pergunta 3). 

1. **Posso desinstalar a instância predefinida do SQL Server?**

   Sim, mas há considerações a ter em conta. Em primeiro lugar, a faturação associada ao Servidor SQL pode continuar a ocorrer dependendo do modelo de licença para o VM. Em segundo lugar, conforme indicado na resposta anterior, existem funcionalidades que dependem da extensão do [agente SQL Server IaaS](virtual-machines-windows-sql-server-agent-extension.md). Se desinstalar a instância predefinida sem remover também a extensão IaaS, a extensão continua a procurar a instância predefinida e pode gerar erros de registo de eventos. Estes erros são das duas seguintes fontes: **Microsoft SQL Server Credential Management** e **Microsoft SQL Server IaaS Agent**. Um dos erros poderá ser semelhante ao seguinte:

      Ocorreu um erro relacionado com a rede ou específico da instância ao estabelecer uma ligação ao SQL Server. O servidor não foi encontrado ou não está acessível.

   Se decidir desinstalar a instância predefinida, também desinstale a extensão do [agente SQL Server IaaS](virtual-machines-windows-sql-server-agent-extension.md) também. 

1. **Posso usar uma instância nomeada de SQL Server com a extensão IaaS?**
   
   Sim, se a instância nomeada for a única instância no Servidor SQL, e se a instância padrão original foi [desinstalada corretamente](virtual-machines-windows-sql-server-agent-extension.md#install-on-a-vm-with-a-single-named-sql-server-instance). Se não houver uma instância predefinida e existem múltiplas instâncias nomeadas num único VM do Servidor SQL, a extensão do agente SQL Server IaaS não será instalada. 

1. **Posso remover o Servidor SQL completamente de um VM de servidor SQL?**

   Sim, mas continuará a ser cobrado pelo seu VM de servidor SQL, conforme descrito na orientação de [preços para VMs SQL Server Azure](virtual-machines-windows-sql-server-pricing-guidance.md). Se já não precisar do SQL Server, pode implementar uma máquina virtual nova e migrar os dados e as aplicações para a mesma. Depois, pode remover a máquina virtual do SQL Server.
   
## <a name="updating-and-patching"></a>Atualização e Remendo

1. **Como mudar para uma versão/edição diferente do SQL Server numa VM do Azure?**

   Os clientes podem alterar a sua versão/edição do SQL Server com o suporte de dados de instalação que contém a versão ou a edição pretendida do SQL Server. Uma vez a edição alterada, utilize o portal do Azure para alterar a propriedade de edição da VM para refletir com precisão a faturação da VM. Para mais informações, consulte [a edição de alteração de um VM do Servidor SQL](virtual-machines-windows-sql-change-edition.md). Não existe uma diferença de faturação para diferentes versões do SQL Server, pelo que uma vez alterada a versão do SQL Server, não é necessária mais nenhuma ação.

1. **Onde posso obter os meios de configuração para alterar a edição ou versão do SQL Server?**

   Os clientes que possuam garantia de [software](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default) podem obter os seus suportes de instalação a partir do Centro de Licenciamento de [Volume.](https://www.microsoft.com/Licensing/servicecenter/default.aspx) Os clientes que não possuam garantia de software podem utilizar os suportes de configuração a partir de uma imagem VM do Marketplace SQL Server que tem a sua edição desejada.
   
1. **Como são aplicadas atualizações e pacotes de serviços num VM do Servidor SQL?**

   As máquinas virtuais dão-lhe controlo sobre o computador anfitrião, incluindo o momento e a forma como aplica atualizações. Para o sistema operativo, pode aplicar manualmente atualizações do windows, ou pode ativar um serviço de agendamento chamado [Patching Automatizado](virtual-machines-windows-sql-automated-patching.md). A Aplicação de Patches Automatizada instala todas as atualizações marcadas como importantes, incluindo atualizações do SQL Server nessa categoria. As outras atualizações opcionais do SQL Server têm de ser instaladas manualmente.

1. **Posso atualizar a minha instância SQL Server 2008 / 2008 R2 depois de o registar no fornecedor de recursos SQL Server VM?**

   Sim. Pode utilizar qualquer suporte de configuração para atualizar a versão e edição do SQL Server e, em seguida, pode atualizar o modo de [extensão SQL IaaS](virtual-machines-windows-sql-register-with-resource-provider.md#management-modes)) de _nenhum agente_ para o _seu completo_. Ao fazê-lo, irá dar-lhe acesso a todos os benefícios da extensão SQL IaaS, tais como a gestão do portal, backups automatizados e patching automatizado. 

1. **Como posso obter atualizações de segurança estendidas gratuitas para o meu fim de suporte SQL Server 2008 e SQL Server 2008 Casos R2?**

   Pode obter atualizações de [segurança estendidas gratuitas](virtual-machines-windows-sql-server-2008-eos-extend-support.md) movendo o seu SQL Server como está para uma máquina virtual Azure SQL. Para mais informações, consulte [o fim das opções de suporte](/sql/sql-server/end-of-support/sql-server-end-of-life-overview). 
  
   

## <a name="general"></a>Geral

1. **Os casos de cluster sql Server (FCI) suportados em VMs Azure?**

   Sim. Pode instalar uma instância de cluster failover utilizando partilhas de [ficheiros premium (PFS)](virtual-machines-windows-portal-sql-create-failover-cluster-premium-file-share.md) ou espaços de [armazenamento diretos (S2D)](virtual-machines-windows-portal-sql-create-failover-cluster.md) para o subsistema de armazenamento. As ações de ficheiropremium fornecem iOPS e capacidades de entrada que irão atender às necessidades de muitas cargas de trabalho. Para cargas de trabalho intensivas em IO, considere utilizar espaços de armazenamento diretamente com base em premium ou ultra-discos. Alternativamente, pode utilizar soluções de agrupamento ou armazenamento de terceiros, conforme descrito em Alta disponibilidade e recuperação de [desastres para o Servidor SQL em Máquinas Virtuais Azure](virtual-machines-windows-sql-high-availability-dr.md#azure-only-high-availability-solutions).

   > [!IMPORTANT]
   > Neste momento, a extensão _completa_ do [agente IaaS do Servidor SQL](virtual-machines-windows-sql-server-agent-extension.md) não é suportada para o SQL Server FCI no Azure. Recomendamos que desinstale a extensão _completa_ dos VMs que participam no FCI e instale a extensão em modo _leve._ Esta extensão suporta funcionalidades, tais como Backup Automatizado e Patching e algumas funcionalidades do portal para o Servidor SQL. Estas funcionalidades não funcionarão para VMs do Servidor SQL depois de o agente _completo_ não estar instalado.

1. **Qual é a diferença entre os VMs do Servidor SQL e o serviço de base de dados SQL?**

   Conceptualmente, executar o SQL Server numa máquina virtual Azure não é muito diferente de executar o SQL Server num centro de dados remoto. Em contraste, a Base de [Dados SQL](../../../sql-database/sql-database-technical-overview.md) oferece base de dados como serviço. Com a Base de Dados SQL, não tem acesso às máquinas que acolhem as suas bases de dados. Para uma comparação completa, consulte [Escolha uma opção Cloud SQL Server: Azure SQL (PaaS) Database ou SQL Server em VMs Azure (IaaS)](../../../sql-database/sql-database-paas-vs-sql-server-iaas.md).

1. **Como posso instalar ferramentas De Dados SQL no meu VM Azure?**

    Descarregue e instale as ferramentas De Dados SQL a partir de Ferramentas de Dados do [Servidor Microsoft SQL - Business Intelligence para Visual Studio 2013](https://www.microsoft.com/download/details.aspx?id=42313).

1. **As transações distribuídas com mSDTC são suportadas em VMs do Servidor SQL?**
   
    Sim. O DTC local é suportado para SQL Server 2016 SP2 e maior. No entanto, as aplicações devem ser testadas quando utilizam os grupos de disponibilidade Always On, uma vez que as transações em voo durante uma falha falharão e devem ser novamente testadas. O DTC clustered está disponível a partir do Windows Server 2019. 

## <a name="resources"></a>Recursos

**VMs do Windows:**

* [Visão geral do Servidor SQL num VM do Windows](virtual-machines-windows-sql-server-iaas-overview.md).
* [Provision a SQL Server Windows VM](virtual-machines-windows-portal-sql-server-provision.md)
* [Migrar uma base de dados para o Servidor SQL num VM Azure](virtual-machines-windows-migrate-sql.md)
* [Alta disponibilidade e recuperação de desastres para o Servidor SQL em Máquinas Virtuais Azure](virtual-machines-windows-sql-high-availability-dr.md)
* [Melhores práticas de desempenho do SQL Server nas Máquinas Virtuais do Azure](virtual-machines-windows-sql-performance.md)
* [Padrões de Aplicação e Estratégias de Desenvolvimento para o SQL Server em Máquinas Virtuais do Azure](virtual-machines-windows-sql-server-app-patterns-dev-strategies.md)

**VMs Linux:**

* [Visão geral do Servidor SQL em um VM Linux](../../linux/sql/sql-server-linux-virtual-machines-overview.md)
* [Provision a SQL Server Linux VM](../../linux/sql/provision-sql-server-linux-virtual-machine.md)
* [FAQ (Linux)](../../linux/sql/sql-server-linux-faq.md)
* [Servidor SQL na documentação linux](https://docs.microsoft.com/sql/linux/sql-server-linux-overview)
