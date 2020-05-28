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
ms.openlocfilehash: 278f3a5109e638530a55f4b2a77cd6d28aa7ca54
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84047952"
---
# <a name="frequently-asked-questions-for-sql-server-running-on-windows-virtual-machines-in-azure"></a>Frequently asked questions for SQL Server running on Windows virtual machines in Azure (Perguntas frequentes sobre o SQL Server em execução em máquinas virtuais do Windows no Azure)
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

> [!div class="op_single_selector"]
> * [Windows](frequently-asked-questions-faq.md)
> * [Linux](../linux/frequently-asked-questions-faq.md)

Este artigo fornece respostas a algumas das perguntas mais comuns sobre executar [o Servidor SQL em Máquinas Virtuais Windows em Azure](https://azure.microsoft.com/services/virtual-machines/sql-server/).

[!INCLUDE [support-disclaimer](../../../../includes/support-disclaimer.md)]

## <a name="images"></a><a id="images"></a>Imagens

1. **Que imagens de galeria de máquinas virtuais SQL Server estão disponíveis?** 

   O Azure mantém imagens de máquinas virtuais para todas as grandes lançamentos suportadas do SQL Server em todas as edições tanto para Windows como Linux. Para mais informações, consulte a lista completa de [imagens VM do Windows](sql-server-on-azure-vm-iaas-what-is-overview.md#payasyougo) e [imagens VM Linux](../linux/sql-server-on-linux-vm-what-is-iaas-overview.md#create).

1. **As imagens de galeria de máquinas virtuais sQL Server existentes são atualizadas?**

   De dois em dois meses, as imagens do SQL Server na galeria de máquinas virtuais são atualizadas com as mais recentes atualizações do Windows e DoLinux. Para imagens do Windows, isto inclui quaisquer atualizações que sejam marcadas como importantes no Windows Update, incluindo importantes atualizações de segurança do SQL Server e pacotes de serviços. Para as imagens do Linux, isto inclui as mais recentes atualizações do sistema. As atualizações cumulativas do SQL Server são tratadas de forma diferente para o Linux e o Windows. Para o Linux, as atualizações cumulativas do SQL Server também estão incluídas na atualização. Mas, neste momento, os VMs do Windows não são atualizados com as atualizações cumulativas do SQL Server ou do Windows Server.

1. **As imagens de máquinas virtuais do SQL Server podem ser removidas da galeria?**

   Sim. O Azure apenas mantém uma imagem por versão e edição principais. Por exemplo, quando um novo pacote de serviços SQL Server é lançado, o Azure adiciona uma nova imagem à galeria para esse pacote de serviços. A imagem do SQL Server para o pacote de serviço anterior é imediatamente removida do portal Azure. No entanto, ainda está disponível para o fornecimento da PowerShell para os próximos três meses. Após três meses, a imagem anterior do pacote de serviço já não está disponível. Esta política de remoção também se aplicaria se uma versão SQL Server não for suportada quando chegar ao fim do seu ciclo de vida.


1. **É possível implementar uma imagem mais antiga do SQL Server que não seja visível no portal Azure?**

   Sim, usando powerShell. Para obter mais informações sobre a implementação de VMs do Servidor SQL utilizando powerShell, consulte [como fornecer máquinas virtuais SQL Server com O PowerShell Azure](create-sql-vm-powershell.md).
   
1. **É possível criar uma imagem generalizada do Mercado do Servidor Azure SQL do meu VM de servidor SQL e usá-lo para implementar VMs?**

   Sim, mas deve [então registar cada VM do Servidor SQL com o fornecedor de recursos SQL Server VM](sql-vm-resource-provider-register.md) para gerir o seu VM de Servidor SQL no portal, bem como utilizar funcionalidades como patching automatizado e backups automáticos. Ao registar-se com o fornecedor de recursos, também terá de especificar o tipo de licença para cada VM do Servidor SQL.

1. **Como generalizo o SQL Server no Azure VM e uso-o para implementar novos VMs?**

   Pode implementar um VM do Servidor Windows (sem o Servidor SQL instalado no mesmo) e utilizar o processo de [sysprep SQL](/sql/database-engine/install-windows/install-sql-server-using-sysprep?view=sql-server-ver15) para generalizar o Servidor SQL no Azure VM (Windows) com os meios de instalação do Servidor SQL. Os clientes que tiverem o [Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default?rtc=1&activetab=software-assurance-default-pivot%3aprimaryr3), poderão obter o suporte de dados de instalação no [Centro de Licenciamento em Volume](https://www.microsoft.com/Licensing/servicecenter/default.aspx). Os clientes que não possuem garantia de software podem usar os suportes de configuração a partir de uma imagem VM do Marketplace SQL Server que tem a edição desejada.

   Em alternativa, utilize uma das imagens do SQL Server formando o mercado Azure para generalizar o Servidor SQL no Azure VM. Tenha em anote que deve eliminar a seguinte chave de registo na imagem de origem antes de criar a sua própria imagem. Se não o fizer, pode resultar no inchaço da pasta de botas de configuração do Servidor SQL e na extensão SQL IaaS em estado falhado.

   Caminho-chave do registo:  
   `Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Setup\SysPrepExternal\Specialize`

   > [!NOTE]
   > O SQL Server em VMs Azure, incluindo os implantados a partir de imagens generalizadas personalizadas, deve ser registado no fornecedor de [recursos SQL VM](/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-register-with-resource-provider?tabs=azure-cli%2Cbash) para satisfazer os requisitos de conformidade e utilizar funcionalidades opcionais, tais como patching automatizado e backups automáticos. O fornecedor de recursos também permite [especificar o tipo](/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-ahb?tabs=azure-portal) de licença para cada VM do Servidor SQL.

1. **Posso usar o meu próprio VHD para implantar um VM sQL Server?**

   Sim, mas deve [então registar cada VM do Servidor SQL com o fornecedor de recursos SQL Server VM](sql-vm-resource-provider-register.md) para gerir o seu VM de Servidor SQL no portal, bem como utilizar funcionalidades como patching automatizado e backups automáticos.

1. **É possível configurar configurações não mostradas na galeria de máquinas virtuais (por exemplo, Windows 2008 R2 + SQL Server 2012)?**

   Não. Para imagens de galerias de máquinas virtuais que incluam o SQL Server, deve selecionar uma das imagens fornecidas através do portal Azure ou via [PowerShell](create-sql-vm-powershell.md). No entanto, tem a capacidade de implementar um Windows VM e de instalar-se auto-instalado no SQL Server. Em seguida, deve [registar o seu VM do Servidor SQL com o fornecedor de recursos SQL Server VM](sql-vm-resource-provider-register.md) para gerir o seu VM do Servidor SQL no portal, bem como utilizar funcionalidades como patching automatizado e backups automáticos. 


## <a name="creation"></a>Criação

1. **Como posso criar uma máquina virtual Azure com o SQL Server?**

   O método mais fácil é criar uma Máquina Virtual que inclua o Servidor SQL. Para um tutorial sobre a inscrição no Azure e a criação de um VM SQL Server a partir do portal, consulte [a Provision a SQL Server virtual machine no portal Azure](create-sql-vm-portal.md). Pode selecionar uma imagem de máquina virtual que utiliza o licenciamento pay-per-segundo do SQL Server, ou pode utilizar uma imagem que lhe permita trazer a sua própria licença SQL Server. Também tem a opção de instalar manualmente o SQL Server num VM com uma edição livremente licenciada (Developer ou Express) ou reutilizando uma licença no local. Certifique-se de registar o seu VM do [Servidor SQL com o fornecedor de recursos SQL Server VM](sql-vm-resource-provider-register.md) para gerir o seu VM do Servidor SQL no portal, bem como utilizar funcionalidades como patching automatizado e backups automáticos. Se trouxer a sua própria licença, tem de ter [licença de mobilidade através](https://azure.microsoft.com/pricing/license-mobility/)da Garantia de Software no Azure . Para obter mais informações, consulte [Pricing guidance for SQL Server Azure VMs (Documentação de orientação sobre preços de VMs do Azure do SQL Server)](pricing-guidance.md).

1. **Como posso migrar a minha base de dados do SQL Server para a Cloud?**

   Primeiro crie uma máquina virtual Azure com uma instância SQL Server. Em seguida, emigra as bases de dados no local para esse caso. Para estratégias de migração de dados, consulte [Migrate uma base de dados do SQL Server para o SQL Server num VM Azure](migrate-to-vm-from-sql-server.md).

## <a name="licensing"></a>Licenciamento

1. **Como posso instalar a minha cópia licenciada do SQL Server numa VM do Azure?**

   Pode fazê-lo de três formas. Se é cliente de acordo de empresa (EA), pode fornecer uma das [imagens de máquinas virtuais que suporta licenças](sql-server-on-azure-vm-iaas-what-is-overview.md#BYOL), que também é conhecida como bring-your-own-license (BYOL). Se tiver [garantiade software,](https://www.microsoft.com/en-us/licensing/licensing-programs/software-assurance-default)pode ativar o [Benefício Híbrido Azure](licensing-model-azure-hybrid-benefit-ahb-change.md) numa imagem pay-as-you-go (PAYG) existente. Em alternativa, poderá copiar o suporte de dados da instalação do SQL Server para uma VM do Windows Server e, em seguida, instalar o SQL Server na VM. Certifique-se de registar o seu VM de Servidor SQL com o fornecedor de [recursos](sql-vm-resource-provider-register.md) para funcionalidades como gestão de portal, backup automatizado e patching automatizado. 

1. **Posso alterar uma VM para utilizar a minha própria licença do SQL Server caso tenha sido criada com uma das imagens da galeria pay as you go?**

   Sim. Pode facilmente trocar uma imagem de galeria pay-as-you-go (PAYG) para trazer a sua própria licença (BYOL) permitindo o [Benefício Híbrido Azure](https://azure.microsoft.com/pricing/hybrid-benefit/faq/).  Para mais informações, consulte [Como alterar o modelo de licenciamento para um VM do Servidor SQL](licensing-model-azure-hybrid-benefit-ahb-change.md). Atualmente, esta facilidade só está disponível para clientes públicos e do Governo azure.

1. **A mudança do modelo de licenciamento requer algum período de inatividade do SQL Server?**

   Não. [A alteração do modelo](licensing-model-azure-hybrid-benefit-ahb-change.md) de licenciamento não requer qualquer tempo de inatividade para o SQL Server, uma vez que a alteração é eficaz imediatamente e não requer um reinício do VM. No entanto, para registar o seu VM do Servidor SQL com o fornecedor de recursos SQL Server VM, a [extensão SQL IaaS](sql-server-iaas-agent-extension-automate-management.md) é um pré-requisito e a instalação da extensão SQL IaaS em _modo completo_ reinicia o serviço SQL Server. Como tal, se a extensão SQL IaaS precisar de ser instalada, instale-a em modo _leve_ para uma funcionalidade limitada ou instale-a em _modo completo_ durante uma janela de manutenção. A extensão SQL IaaS instalada em modo _leve_ pode ser atualizada para o modo _completo_ a qualquer momento, mas requer um reinício do serviço SQL Server. 
   
1. **É possível mudar para o modelo de licenciamento numa VM do SQL Server implementada com o modelo clássico?**

   Não. Alterar o modelo de licenciamento não é suportado numa VM clássica. Pode migrar a VM para o modelo do Azure Resource Manager e registá-la no fornecedor de recursos da VM do SQL Server. Quando a VM é registada no fornecedor de recursos da VM do SQL Server, as alterações ao modelo de licenciamento estarão disponíveis na VM.

1. **Posso utilizar o portal do Azure para gerir várias instâncias na mesma VM?**

   Não. A gestão do portal é uma funcionalidade proporcionada pelo fornecedor de recursos da VM do SQL Server, que está dependente da extensão do Agente IaaS do SQL Server. Como tal, aplicam-se as mesmas limitações ao fornecedor de recursos que à extensão. O portal pode gerir apenas uma instância predefinida ou uma instância nomeada, desde que tenha sido configurado corretamente. Para obter mais informações sobre estas limitações, consulte a extensão do [agente SQL Server IaaS](sql-server-iaas-agent-extension-automate-management.md). 

1. **As subscrições CSP podem ativar o Benefício Híbrido do Azure?**

   Sim, o Benefício Híbrido do Azure está disponível para as subscrições CSP. Os clientes da CSP devem primeiro implementar uma imagem pay-as-you-go e, em seguida, [alterar o modelo](licensing-model-azure-hybrid-benefit-ahb-change.md) de licenciamento para trazer a sua própria licença.
   
 
1. **Tenho de pagar para licenciar o SQL Server numa VM do Azure se só estiver a ser utilizado para reserva/ativação pós-falha?**

   Para ter uma licença passiva gratuita para um grupo de disponibilidade secundária de prontidão ou instância agrupada, deve cumprir todos os seguintes critérios, tal como descrito sem acordo nos Termos de Licenciamento do [Produto:](https://www.microsoft.com/licensing/product-licensing/products)

   1. Tem [mobilidade de licença](https://www.microsoft.com/licensing/licensing-programs/software-assurance-license-mobility?activetab=software-assurance-license-mobility-pivot:primaryr2) através da Garantia de [Software.](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default?activetab=software-assurance-default-pivot%3aprimaryr3) 
   1. A instância do SQL Server passiva não serve dados do SQL Server aos clientes nem executa cargas de trabalho ativas do SQL Server. É apenas utilizada para sincronizar com o servidor primário e manter a base de dados passiva num estado de reserva semiativa. Se estiver a servir dados, tais como relatórios a clientes que executam cargas de trabalho ativas do SQL Server, ou a realizar qualquer trabalho que não seja o especificado nos termos do produto, deve ser uma instância de Servidor SQL licenciada paga. A seguinte atividade é permitida na instância secundária: verificações da consistência da base de dados ou CheckDB, cópias de segurança completas, cópias de segurança do registo de transações e monitorização dos dados de utilização dos recursos. Também pode executar a instância de recuperação após desastre primária correspondente por breves períodos de teste de recuperação após desastre a cada 90 dias. 
   1. A licença ativa do SQL Server é coberta pela Garantia de Software e permite **uma** instância passiva secundária do SQL Server, com até à mesma quantidade de computação que o servidor ativo licenciado, apenas. 
   1. O VM secundário do Servidor SQL utiliza a licença de recuperação de [desastres](business-continuity-high-availability-disaster-recovery-hadr-overview.md#free-dr-replica-in-azure) no portal Azure.
   
1. **O que é considerado uma instância passiva?**

   A instância do SQL Server passiva não serve dados do SQL Server aos clientes nem executa cargas de trabalho ativas do SQL Server. É apenas utilizada para sincronizar com o servidor primário e manter a base de dados passiva num estado de reserva semiativa. Se estiver a servir dados, como relatórios a clientes que executam cargas de trabalho do SQL Server ativas ou se estiver a realizar qualquer trabalho que não seja o especificado nos termos do produto, deverá utilizar uma instância do SQL Server licenciada paga. A seguinte atividade é permitida na instância secundária: verificações da consistência da base de dados ou CheckDB, cópias de segurança completas, cópias de segurança do registo de transações e monitorização dos dados de utilização dos recursos. Também pode executar a instância de recuperação após desastre primária correspondente por breves períodos de teste de recuperação após desastre a cada 90 dias.
   

1. **Que cenários podem utilizar o benefício de Recuperação após Desastre (DR)?**

   O guia de [licenciamento](https://aka.ms/sql2019licenseguide) fornece cenários em que o Benefício de Recuperação de Desastres pode ser utilizado. Veja os Termos de Produto e fale com os seus contactos de licenciamento ou gestor de conta para obter mais informações.

1. **Que subscrições suportam o benefício de Recuperação após Desastre (DR)?**

   Programas abrangentes que oferecem direitos de subscrição equivalentes ao Software Assurance como um benefício fixo que suporta o benefício DR. Inclui, entre outros, o Open Value (OV), a Subscrição Open Value (OVS), o Contrato Enterprise (EA), Contrato de Subscrição Enterprise (EAS) e a Inscrição Servidor e Cloud (SCE). Consulte os termos do [produto](https://www.microsoft.com/licensing/product-licensing/products) e fale com os seus contactos de licenciamento ou gestor de conta para obter mais informações. 

   
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

    Sim. Se implementou o SQL Server a partir dos seus próprios meios de comunicação e instalou a extensão SQL IaaS, pode registar o seu VM sQL Server com o fornecedor de recursos para obter os benefícios de gestão fornecidos pela extensão SQL IaaS.    


## <a name="administration"></a>Administração

1. **Posso instalar uma segunda instância de SQL Server no mesmo VM? Posso alterar as funcionalidades instaladas da instância predefinida?**

   Sim. O suporte de instalação do SQL Server está localizado numa pasta na unidade **C.** Executar **Configuração.exe** a partir desse local para adicionar novas instâncias do Servidor SQL ou para alterar outras funcionalidades instaladas do Servidor SQL na máquina. Note que algumas funcionalidades, tais como Backup Automatizado, Patching Automatizado e Integração do Cofre de Chaves Azure, operam apenas contra a instância predefinida, ou uma instância nomeada que foi configurada corretamente (Ver Pergunta 3). 

1. **Posso desinstalar a instância predefinida do SQL Server?**

   Sim, mas há considerações a ter em conta. Em primeiro lugar, a faturação associada ao Servidor SQL pode continuar a ocorrer dependendo do modelo de licença para o VM. Em segundo lugar, conforme indicado na resposta anterior, existem funcionalidades que dependem da extensão do [agente SQL Server IaaS](sql-server-iaas-agent-extension-automate-management.md). Se desinstalar a instância predefinida sem remover também a extensão IaaS, a extensão continua a procurar a instância predefinida e pode gerar erros de registo de eventos. Estes erros são das duas seguintes fontes: **Microsoft SQL Server Credential Management** e **Microsoft SQL Server IaaS Agent**. Um dos erros poderá ser semelhante ao seguinte:

      Ocorreu um erro relacionado com a rede ou específico da instância ao estabelecer uma ligação ao SQL Server. O servidor não foi encontrado ou não está acessível.

   Se decidir desinstalar a instância predefinida, também desinstale a extensão do [agente SQL Server IaaS](sql-server-iaas-agent-extension-automate-management.md) também. 

1. **Posso usar uma instância nomeada de SQL Server com a extensão IaaS?**
   
   Sim, se a instância nomeada for a única instância no Servidor SQL, e se a instância padrão original foi [desinstalada corretamente](sql-server-iaas-agent-extension-automate-management.md#install-on-a-vm-with-a-single-named-sql-server-instance). Se não houver uma instância predefinida e existem múltiplas instâncias nomeadas num único VM do Servidor SQL, a extensão do agente SQL Server IaaS não será instalada. 

1. **Posso remover completamente o SQL Server de uma VM do SQL Server?**

   Sim, mas continuará a ser cobrado pelo seu VM de servidor SQL, conforme descrito na orientação de [preços para VMs SQL Server Azure](pricing-guidance.md). Se já não precisar do SQL Server, pode implementar uma máquina virtual nova e migrar os dados e as aplicações para a mesma. Depois, pode remover a máquina virtual do SQL Server.
   
## <a name="updating-and-patching"></a>Atualização e Remendo

1. **Como mudar para uma versão/edição diferente do SQL Server numa VM do Azure?**

   Os clientes podem alterar a sua versão/edição do SQL Server com o suporte de dados de instalação que contém a versão ou a edição pretendida do SQL Server. Uma vez a edição alterada, utilize o portal do Azure para alterar a propriedade de edição da VM para refletir com precisão a faturação da VM. Para mais informações, consulte [a edição de alteração de um VM do Servidor SQL](change-sql-server-edition.md). Não existem diferenças a nível de faturação para versões diferentes do SQL Server, quando a versão do SQL Server for alterada, não é necessária mais nenhuma ação.

1. **Onde posso obter o suporte de dados de configuração para alterar a edição ou a versão do SQL Server?**

   Os clientes que tiverem o [Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default), poderão obter o suporte de dados de instalação no [Centro de Licenciamento em Volume](https://www.microsoft.com/Licensing/servicecenter/default.aspx). Os clientes que não tiverem o Software Assurance, poderão utilizar o suporte de dados de configuração a partir de uma imagem da VM do SQL Server do Marketplace que possua a edição desejada.
   
1. **Como são aplicados os pacotes de serviços e atualizações numa VM do SQL Server?**

   As máquinas virtuais dão-lhe controlo sobre o computador anfitrião, incluindo o momento e a forma como aplica atualizações. No que respeita o sistema operativo, pode aplicar atualizações do Windows manualmente ou pode ativar um serviço de agendamento denominado [Aplicação de Patches Automatizada](automated-patching.md). A Aplicação de Patches Automatizada instala todas as atualizações marcadas como importantes, incluindo atualizações do SQL Server nessa categoria. As outras atualizações opcionais do SQL Server têm de ser instaladas manualmente.

1. **Posso atualizar a minha instância SQL Server 2008 / 2008 R2 depois de o registar no fornecedor de recursos SQL Server VM?**

   Sim. Pode utilizar qualquer suporte de configuração para atualizar a versão e edição do SQL Server e, em seguida, pode atualizar o modo de [extensão SQL IaaS](sql-vm-resource-provider-register.md#management-modes)) de _nenhum agente_ para o _seu completo_. Ao fazê-lo, irá dar-lhe acesso a todos os benefícios da extensão SQL IaaS, tais como a gestão do portal, backups automatizados e patching automatizado. 

1. **Como posso obter atualizações de segurança alargada gratuitas para o fim de suporte das instâncias do SQL Server 2008 e do SQL Server 2008 R2?**

   Pode obter [atualizações de segurança alargada gratuitas](sql-server-2008-extend-end-of-support.md) ao mover o SQL Server tal como está para uma máquina virtual do SQL do Azure. Para obter mais informações, veja [end of support options](/sql/sql-server/end-of-support/sql-server-end-of-life-overview) (opções de fim de suporte). 
  
   

## <a name="general"></a>Geral

1. **Os casos de cluster sql Server (FCI) suportados em VMs Azure?**

   Sim. Pode instalar uma instância de cluster failover utilizando partilhas de [ficheiros premium (PFS)](failover-cluster-instance-premium-file-share-manually-configure.md) ou espaços de [armazenamento diretos (S2D)](failover-cluster-instance-storage-spaces-direct-manually-configure.md) para o subsistema de armazenamento. As ações de ficheiropremium fornecem iOPS e capacidades de entrada que irão atender às necessidades de muitas cargas de trabalho. Para cargas de trabalho intensivas em IO, considere utilizar espaços de armazenamento diretamente com base em premium ou ultra-discos. Alternativamente, pode utilizar soluções de agrupamento ou armazenamento de terceiros, conforme descrito em Alta disponibilidade e recuperação de [desastres para o Servidor SQL em Máquinas Virtuais Azure](business-continuity-high-availability-disaster-recovery-hadr-overview.md#azure-only-high-availability-solutions).

   > [!IMPORTANT]
   > Neste momento, a extensão _completa_ do [agente IaaS do Servidor SQL](sql-server-iaas-agent-extension-automate-management.md) não é suportada para o SQL Server FCI no Azure. Recomendamos que desinstale a extensão _completa_ dos VMs que participam no FCI e instale a extensão em modo _leve._ Esta extensão suporta funcionalidades, tais como Backup Automatizado e Patching e algumas funcionalidades do portal para o Servidor SQL. Estas funcionalidades não funcionarão para VMs do Servidor SQL depois de o agente _completo_ não estar instalado.

1. **Qual é a diferença entre os VMs do Servidor SQL e o serviço de base de dados SQL?**

   Conceptualmente, executar o SQL Server numa máquina virtual Azure não é muito diferente de executar o SQL Server num centro de dados remoto. Em contraste, a Base de [Dados SQL](../../database/sql-database-paas-overview.md) oferece base de dados como serviço. Com a Base de Dados SQL, não tem acesso às máquinas que acolhem as suas bases de dados. Para uma comparação completa, consulte [Escolha uma opção Cloud SQL Server: Azure SQL (PaaS) Database ou SQL Server em VMs Azure (IaaS)](../../azure-sql-iaas-vs-paas-what-is-overview.md).

1. **Como posso instalar ferramentas De Dados SQL no meu VM Azure?**

    Descarregue e instale as ferramentas De Dados SQL a partir de Ferramentas de Dados do [Servidor Microsoft SQL - Business Intelligence para Visual Studio 2013](https://www.microsoft.com/download/details.aspx?id=42313).

1. **As transações distribuídas com mSDTC são suportadas em VMs do Servidor SQL?**
   
    Sim. O DTC local é suportado para SQL Server 2016 SP2 e maior. No entanto, as aplicações devem ser testadas quando utilizam os grupos de disponibilidade Always On, uma vez que as transações em voo durante uma falha falharão e devem ser novamente testadas. O DTC clustered está disponível a partir do Windows Server 2019. 

## <a name="resources"></a>Recursos

**VMs do Windows:**

* [Visão geral do Servidor SQL num VM do Windows](sql-server-on-azure-vm-iaas-what-is-overview.md).
* [Provision a SQL Server Windows VM](create-sql-vm-portal.md)
* [Migrar uma base de dados para o Servidor SQL num VM Azure](migrate-to-vm-from-sql-server.md)
* [Alta disponibilidade e recuperação de desastres para o Servidor SQL em Máquinas Virtuais Azure](business-continuity-high-availability-disaster-recovery-hadr-overview.md)
* [Melhores práticas de desempenho do SQL Server nas Máquinas Virtuais do Azure](performance-guidelines-best-practices.md)
* [Padrões de Aplicação e Estratégias de Desenvolvimento para o SQL Server em Máquinas Virtuais do Azure](application-patterns-development-strategies.md)

**VMs Linux:**

* [Visão geral do Servidor SQL em um VM Linux](../linux/sql-server-on-linux-vm-what-is-iaas-overview.md)
* [Provision a SQL Server Linux VM](../linux/sql-vm-create-portal-quickstart.md)
* [FAQ (Linux)](../linux/frequently-asked-questions-faq.md)
* [Servidor SQL na documentação linux](https://docs.microsoft.com/sql/linux/sql-server-linux-overview)
