---
title: SQL Server em Máquinas Virtuais do Windows nas perguntas frequentes do Azure | Microsoft Docs
description: Este artigo fornece respostas para perguntas frequentes sobre a execução de SQL Server em VMs do Azure.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: felixwu
editor: ''
tags: azure-service-management
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/05/2019
ms.author: mathoma
ms.openlocfilehash: 80c90ceb0e2edac47d67b99e7fb7f03c1ab82fb1
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/09/2019
ms.locfileid: "68882362"
---
# <a name="frequently-asked-questions-for-sql-server-running-on-windows-virtual-machines-in-azure"></a>Perguntas frequentes sobre SQL Server em execução em máquinas virtuais do Windows no Azure

> [!div class="op_single_selector"]
> * [Windows](virtual-machines-windows-sql-server-iaas-faq.md)
> * [Linux](../../linux/sql/sql-server-linux-faq.md)

Este artigo fornece respostas para algumas das perguntas mais comuns sobre a execução [de SQL Server em máquinas virtuais do Windows no Azure](https://azure.microsoft.com/services/virtual-machines/sql-server/).

> [!NOTE]
> Este artigo se concentra em problemas específicos para SQL Server em VMs do Windows. Se você estiver executando SQL Server em Linux VMs, consulte as [perguntas frequentes sobre o Linux](../../linux/sql/sql-server-linux-faq.md).

[!INCLUDE [support-disclaimer](../../../../includes/support-disclaimer.md)]

## <a id="images"></a>Imagens

1. **Quais SQL Server imagens da Galeria de máquinas virtuais estão disponíveis?** 

   O Azure mantém imagens de máquina virtual para todas as versões principais com suporte do SQL Server em todas as edições para Windows e Linux. Para obter mais informações, consulte a lista completa de imagens de [VM do Windows](virtual-machines-windows-sql-server-iaas-overview.md#payasyougo) e imagens de [VM do Linux](../../linux/sql/sql-server-linux-virtual-machines-overview.md#create).

1. **As imagens da Galeria de máquinas virtuais SQL Server existentes são atualizadas?**

   A cada dois meses, SQL Server imagens na Galeria de máquinas virtuais são atualizadas com as atualizações mais recentes do Windows e do Linux. Para imagens do Windows, isso inclui todas as atualizações marcadas como importantes no Windows Update, incluindo atualizações de segurança SQL Server importantes e Service Packs. Para imagens do Linux, isso inclui as atualizações mais recentes do sistema. SQL Server atualizações cumulativas são tratadas de forma diferente para Linux e Windows. Para o Linux, SQL Server atualizações cumulativas também são incluídas na atualização. Mas, no momento, as VMs do Windows não são atualizadas com SQL Server ou atualizações cumulativas do Windows Server.

1. **É possível SQL Server imagens de máquina virtual sejam removidas da Galeria?**

   Sim. O Azure mantém apenas uma imagem por versão principal e edição. Por exemplo, quando um novo service pack de SQL Server é liberado, o Azure adiciona uma nova imagem à galeria para essa service pack. A imagem de SQL Server para a service pack anterior é removida imediatamente da portal do Azure. No entanto, ele ainda está disponível para provisionamento do PowerShell nos próximos três meses. Após três meses, a imagem de service pack anterior não estará mais disponível. Essa política de remoção também se aplicará se uma versão de SQL Server não for suportada quando atingir o final de seu ciclo de vida.


1. **É possível implantar uma imagem mais antiga do SQL Server que não esteja visível na portal do Azure?**

   Sim, usando o PowerShell. Para obter mais informações sobre como implantar VMs SQL Server usando o PowerShell, consulte [como provisionar SQL Server máquinas virtuais com o Azure PowerShell](virtual-machines-windows-ps-sql-create.md).

1. **Posso criar uma imagem generalizada do Azure SQL Server Marketplace da minha VM SQL Server e usá-la para implantar VMs?**

   Sim, mas você deve [registrar cada vm SQL Server com o provedor de recursos de vm SQL Server](virtual-machines-windows-sql-register-with-resource-provider.md) para gerenciar sua vm de SQL Server no portal, bem como utilizar recursos como aplicação de patch automatizada e backups automáticos. Ao registrar com o provedor de recursos, também será necessário especificar o tipo de licença para cada VM SQL Server. 

1. **Posso usar meu próprio VHD para implantar uma VM SQL Server?**

   Sim, mas você deve [registrar cada vm SQL Server com o provedor de recursos de vm SQL Server](virtual-machines-windows-sql-register-with-resource-provider.md) para gerenciar sua vm de SQL Server no portal, bem como utilizar recursos como aplicação de patch automatizada e backups automáticos.

1. **É possível configurar as configurações não mostradas na Galeria de máquinas virtuais (por exemplo, Windows 2008 R2 + SQL Server 2012)?**

   Não. Para imagens da Galeria de máquinas virtuais que incluem SQL Server, você deve selecionar uma das imagens fornecidas por meio do portal do Azure ou por meio do [PowerShell](virtual-machines-windows-ps-sql-create.md). No entanto, você pode implantar uma VM do Windows e a autoinstalação SQL Server a ela. Em seguida, você deve [registrar sua vm SQL Server com o provedor de recursos de vm SQL Server](virtual-machines-windows-sql-register-with-resource-provider.md) para gerenciar sua VM SQL Server no portal, bem como utilizar recursos como aplicação de patch automatizada e backups automáticos. 


## <a name="creation"></a>Criação

1. **Como fazer criar uma máquina virtual do Azure com SQL Server?**

   O método mais fácil é criar uma máquina virtual que inclua SQL Server. Para obter um tutorial sobre como se inscrever no Azure e criar uma VM SQL Server no portal, consulte provisionar [uma máquina virtual SQL Server no portal do Azure](virtual-machines-windows-portal-sql-server-provision.md). Você pode selecionar uma imagem de máquina virtual que usa licenciamento de SQL Server de pagamento por segundo ou pode usar uma imagem que permite que você traga sua própria licença de SQL Server. Você também tem a opção de instalar manualmente o SQL Server em uma VM com uma edição gratuita licenciada (Developer ou Express) ou reutilizando uma licença local. Certifique-se de [registrar sua VM de SQL Server com o provedor de recursos de vm SQL Server](virtual-machines-windows-sql-register-with-resource-provider.md) para gerenciar sua vm de SQL Server no portal, bem como utilizar recursos como aplicação de patch automatizada e backups automáticos. Se você traga sua própria licença, deve ter [mobilidade de licenças via Software Assurance no Azure](https://azure.microsoft.com/pricing/license-mobility/). Para obter mais informações, consulte [Pricing guidance for SQL Server Azure VMs (Documentação de orientação sobre preços de VMs do Azure do SQL Server)](virtual-machines-windows-sql-server-pricing-guidance.md).

1. **Como posso migrar meu banco de dados local SQL Server para a nuvem?**

   Primeiro, crie uma máquina virtual do Azure com uma instância de SQL Server. Em seguida, migre seus bancos de dados locais para essa instância. Para estratégias de migração de dados, consulte [migrar um SQL Server banco de dado para SQL Server em uma VM do Azure](virtual-machines-windows-migrate-sql.md).

## <a name="licensing"></a>Licenciamento

1. **Como posso instalar a minha cópia licenciada do SQL Server numa VM do Azure?**

   Há três maneiras de fazer isso. Se você for um cliente do EA (Enterprise Agreement), poderá provisionar uma das [imagens de máquina virtual que dá suporte a licenças](virtual-machines-windows-sql-server-iaas-overview.md#BYOL), que também é conhecida como BYOL (traga sua própria licença). Se você tiver o [Software Assurance](https://www.microsoft.com/en-us/licensing/licensing-programs/software-assurance-default), poderá habilitar o [benefício híbrido do Azure](virtual-machines-windows-sql-ahb.md) em uma imagem PAYG (pré-pago) existente. Ou você pode copiar a mídia de instalação do SQL Server para uma VM do Windows Server e, em seguida, instalar SQL Server na VM. Certifique-se de registrar sua VM SQL Server com o [provedor de recursos](virtual-machines-windows-sql-register-with-resource-provider.md) para recursos como gerenciamento de portal, backup automatizado e aplicação de patch automatizada. 

1. **Tenho de pagar para licenciar o SQL Server numa VM do Azure se só estiver a ser utilizado para reserva/ativação pós-falha?**

   Para ter uma licença passiva gratuita para um grupo de disponibilidade secundário em espera ou instância clusterizada de failover, você deve atender a todos os critérios a seguir, conforme descrito no [Guia de licenciamento PDF](https://download.microsoft.com/download/7/8/C/78CDF005-97C1-4129-926B-CE4A6FE92CF5/SQL_Server_2017_Licensing_guide.pdf):

   1. Você tem o [License Mobility](https://www.microsoft.com/licensing/licensing-programs/software-assurance-license-mobility?activetab=software-assurance-license-mobility-pivot:primaryr2) por meio do [Software Assurance](https://www.microsoft.comlicensing/licensing-programs/software-assurance-default?activetab=software-assurance-default-pivot%3aprimaryr3). 
   1. A instância de SQL Server passiva não atende SQL Server dados aos clientes ou executa cargas de trabalho do Active SQL Server. Ele é usado apenas para sincronizar com o servidor primário e, caso contrário, manter o banco de dados passivo em um estado de espera passiva. Se ele estiver servindo dados, como relatórios para clientes que executam cargas de trabalho do Active SQL Server, ou se executar qualquer "operação", como backups adicionais do servidor secundário, ele deverá ser uma instância paga de SQL Server licenciada. 
   1. A licença do Active SQL Server é coberta pelo Software Assurance e permite **uma** instância de SQL Server secundário passiva, com até a mesma quantidade de computação que o servidor ativo licenciado, apenas. 
   1. A VM de SQL Server secundária utiliza o [modelo de licença](virtual-machines-windows-sql-ahb.md)BYOL (traga sua própria licença) ou benefício híbrido do Azure (AHB). 

1. **Posso alterar uma VM para utilizar a minha própria licença do SQL Server caso tenha sido criada com uma das imagens da galeria pay as you go?**

   Sim. Você pode alternar facilmente uma imagem de galeria pré-paga (PAYG) para trazer sua própria licença (BYOL) habilitando o [benefício híbrido do Azure](https://azure.microsoft.com/pricing/hybrid-benefit/faq/)...  Para obter mais informações, consulte [como alterar o modelo de licenciamento para uma VM SQL Server](virtual-machines-windows-sql-ahb.md). Atualmente, essa instalação está disponível apenas para clientes de nuvem pública.

1. **A mudança do modelo de licenciamento requer algum período de inatividade do SQL Server?**

   Não. A [alteração do modelo de licenciamento](virtual-machines-windows-sql-ahb.md) não exige nenhum tempo de inatividade para SQL Server, pois a alteração entra em vigor imediatamente e não requer uma reinicialização da VM. No entanto, para registrar sua VM SQL Server com o provedor de recursos de VM SQL Server, a [extensão de IaaS do SQL](virtual-machines-windows-sql-server-agent-extension.md) é um pré-requisito e a instalação da extensão SQL IaaS no modo _completo_ reinicia o serviço SQL Server. Assim, se a extensão de IaaS do SQL precisar ser instalada, instale-a no modo _leve_ para funcionalidade limitada ou instale-a em modo _completo_ durante uma janela de manutenção. A extensão IaaS do SQL instalada no modo _leve_ pode ser atualizada para o modo _completo_ a qualquer momento, mas requer uma reinicialização do serviço SQL Server. 

1. **Posso usar o portal do Azure para gerenciar várias instâncias na mesma VM?**

   Não. O gerenciamento de portal é um recurso fornecido pelo provedor de recursos de VM SQL Server, que se baseia na extensão do agente IaaS SQL Server. Assim, as mesmas limitações se aplicam ao provedor de recursos para a extensão. O portal pode gerenciar apenas uma instância padrão ou uma instância nomeada, desde que tenha sido configurada corretamente. Para obter mais informações sobre essas limitações, consulte [SQL Server extensão do agente IaaS](virtual-machines-windows-sql-server-agent-extension.md). 

1. **As assinaturas do CSP podem ativar o Benefício Híbrido do Azure?**

   Sim, o Benefício Híbrido do Azure está disponível para assinaturas do CSP. Os clientes do CSP devem primeiro implantar uma imagem paga conforme o uso e, em seguida, [alterar o modelo](virtual-machines-windows-sql-ahb.md) de licenciamento para traga sua própria licença.

1. **O registro da minha VM com o novo SQL Server provedor de recursos da VM trará custos adicionais?**

   Não. O provedor de recursos de VM SQL Server apenas habilita a capacidade de gerenciamento adicional para SQL Server na VM do Azure sem encargos adicionais. 

1. **O provedor de recursos de VM SQL Server está disponível para todos os clientes?**
 
   Sim, contanto que a VM SQL Server tenha sido implantada na nuvem pública usando o modelo do Resource Manager e não o modelo clássico. Todos os outros clientes podem se registrar no novo SQL Server provedor de recursos de VM. No entanto, somente os clientes com o benefício do [Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default?activetab=software-assurance-default-pivot%3aprimaryr3) podem usar sua própria licença ativando o [benefício híbrido do Azure (AHB)](https://azure.microsoft.com/pricing/hybrid-benefit/) em uma VM SQL Server. 

1. **O que acontecerá com o recurso de provedor de recursos (_Microsoft. SqlVirtualMachine_) se o recurso da VM for movido ou descartado?** 

   Quando o recurso Microsoft. Compute/VirtualMachine é Descartado ou movido, o recurso Microsoft. SqlVirtualMachine associado é notificado para replicar a operação de forma assíncrona.

1. **O que acontecerá com a VM se o recurso do provedor de recursos (_Microsoft. SqlVirtualMachine_) for descartado?**

    O recurso Microsoft. Compute/VirtualMachine não é afetado quando o recurso Microsoft. SqlVirtualMachine é Descartado. No entanto, as alterações de licenciamento retornarão por padrão à origem da imagem original. 

1. **É possível registrar VMs de SQL Server implantadas automaticamente com o provedor de recursos de VM SQL Server?**

    Sim. Se você implantou o SQL Server de sua própria mídia e instalou a extensão SQL IaaS, você pode registrar sua VM SQL Server com o provedor de recursos para obter os benefícios de gerenciamento fornecidos pela extensão SQL IaaS. No entanto, você não pode converter uma VM de SQL Server implantada para o pré-pago.

1. **É possível alternar o modelo de licenciamento em uma VM SQL Server implantada usando o modelo clássico?**

   Não. Não há suporte para a alteração do modelo de licenciamento em uma VM clássica. Você pode migrar sua VM para o modelo de Azure Resource Manager e registrar com o provedor de recursos de VM SQL Server. Depois que a VM estiver registrada com o provedor de recursos de VM SQL Server, as alterações no modelo de licenciamento estarão disponíveis na VM. 
   


## <a name="administration"></a>Administração

1. **Posso instalar uma segunda instância do SQL Server na mesma VM? Posso alterar as funcionalidades instaladas da instância predefinida?**

   Sim. A mídia de instalação do SQL Server está localizada em uma pasta na unidade **C** . Execute **Setup. exe** desse local para adicionar novas instâncias de SQL Server ou para alterar outros recursos instalados do SQL Server no computador. Observe que alguns recursos, como o backup automatizado, a aplicação de patch automatizada e a integração de Azure Key Vault, operam apenas na instância padrão ou uma instância nomeada que foi configurada corretamente (consulte a pergunta 3). 

1. **Posso desinstalar a instância predefinida do SQL Server?**

   Sim, mas há considerações a ter em conta. Primeiro, a cobrança SQL Server associada pode continuar a ocorrer dependendo do modelo de licença da VM. Em segundo lugar, conforme mencionado na resposta anterior, há recursos que dependem da [extensão do agente IaaS SQL Server](virtual-machines-windows-sql-server-agent-extension.md). Se você desinstalar a instância padrão sem remover a extensão IaaS também, a extensão continuará a procurar a instância padrão e poderá gerar erros de log de eventos. Esses erros são provenientes das duas origens abaixo: **Microsoft SQL Server o gerenciamento de credenciais** e **Microsoft SQL Server agente IaaS**. Um dos erros poderá ser semelhante ao seguinte:

      Ocorreu um erro relacionado com a rede ou específico da instância ao estabelecer uma ligação ao SQL Server. O servidor não foi encontrado ou não está acessível.

   Se você decidir desinstalar a instância padrão, também desinstale o [SQL Server extensão do agente IaaS](virtual-machines-windows-sql-server-agent-extension.md) . 

1. **Posso usar uma instância nomeada do SQL Server com a extensão IaaS**?
   
   Sim, se a instância nomeada for a única na SQL Server e se a instância padrão original tiver sido desinstalada [corretamente](virtual-machines-windows-sql-server-agent-extension.md#install-on-a-vm-with-a-single-named-sql-server-instance). Se não houver nenhuma instância padrão e houver várias instâncias nomeadas em uma única VM SQL Server, a extensão do agente IaaS SQL Server não será instalada. 

1. **Posso remover completamente SQL Server de uma VM SQL Server?**

   Sim, mas você continuará a ser cobrado por sua VM SQL Server, conforme descrito em [diretrizes de preços para SQL Server VMs do Azure](virtual-machines-windows-sql-server-pricing-guidance.md). Se já não precisar do SQL Server, pode implementar uma máquina virtual nova e migrar os dados e as aplicações para a mesma. Depois, pode remover a máquina virtual do SQL Server.
   
## <a name="updating-and-patching"></a>Atualização e aplicação de patch

1. **Como mudar para uma versão/edição diferente do SQL Server numa VM do Azure?**

   Os clientes podem alterar a sua versão/edição do SQL Server com o suporte de dados de instalação que contém a versão ou a edição pretendida do SQL Server. Uma vez a edição alterada, utilize o portal do Azure para alterar a propriedade de edição da VM para refletir com precisão a faturação da VM. Para obter mais informações, consulte [Change Edition of a VM SQL Server](virtual-machines-windows-sql-change-edition.md). Não há nenhuma diferença de cobrança para versões diferentes do SQL Server, portanto, depois que a versão do SQL Server tiver sido alterada, nenhuma ação adicional será necessária.

1. **Onde posso obter a mídia de instalação para alterar a edição ou a versão do SQL Server?**

  Os clientes que têm o [Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default) podem obter a mídia de instalação do centro de licenciamento por [volume](https://www.microsoft.com/Licensing/servicecenter/default.aspx). Os clientes que não têm o Software Assurance podem usar a mídia de instalação de um Marketplace SQL Server imagem de VM que tenha sua edição desejada.

1. **Como as atualizações e os service packs são aplicados em uma VM SQL Server?**

   As máquinas virtuais dão-lhe controlo sobre o computador anfitrião, incluindo o momento e a forma como aplica atualizações. Para o sistema operacional, você pode aplicar manualmente as atualizações do Windows ou pode habilitar um serviço de agendamento chamado [aplicação de patch automatizada](virtual-machines-windows-sql-automated-patching.md). A Aplicação de Patches Automatizada instala todas as atualizações marcadas como importantes, incluindo atualizações do SQL Server nessa categoria. As outras atualizações opcionais do SQL Server têm de ser instaladas manualmente.

1. **Posso atualizar minha instância do SQL Server 2008/2008 R2 depois de registrá-la com o provedor de recursos de VM SQL Server?**

   Sim. Você pode usar qualquer mídia de instalação para atualizar a versão e a edição do SQL Server e, em seguida, pode atualizar o [modo de extensão de IaaS do SQL](virtual-machines-windows-sql-server-agent-extension.md#change-management-modes) de _nenhum agente_ para _completo_. Isso dará acesso a todos os benefícios da extensão de IaaS do SQL, como gerenciamento de portal, backups automatizados e aplicação de patch automatizada. 

## <a name="general"></a>Geral

1. **Há SQL Server FCI (instâncias de cluster de failover) com suporte em VMs do Azure?**

   Sim. Você pode [criar um cluster de failover do Windows no Windows Server 2016](virtual-machines-windows-portal-sql-create-failover-cluster.md) e usar espaços de armazenamento diretos (S2D) para o armazenamento de cluster. Como alternativa, você pode usar soluções de armazenamento ou cluster de terceiros, conforme descrito em [alta disponibilidade e recuperação de desastres para SQL Server em máquinas virtuais do Azure](virtual-machines-windows-sql-high-availability-dr.md#azure-only-high-availability-solutions).

   > [!IMPORTANT]
   > Neste momento, não há suporte para a extensão _completa_ do [agente IaaS SQL Server](virtual-machines-windows-sql-server-agent-extension.md) para SQL Server FCI no Azure. Recomendamos que você desinstale a extensão _completa_ de VMs que participam do FCI e instale a extensão no modo _leve_ . Essa extensão oferece suporte a recursos, como backup automatizado e aplicação de patches e alguns recursos do portal para SQL Server. Esses recursos não funcionarão para VMs SQL Server depois que o agente _completo_ for desinstalado.

1. **Qual é a diferença entre SQL Server VMs e o serviço de banco de dados SQL?**

   Conceitualmente, a execução de SQL Server em uma máquina virtual do Azure não é tão diferente de executar SQL Server em um datacenter remoto. Por outro lado, o [banco de dados SQL](../../../sql-database/sql-database-technical-overview.md) oferece o banco de dados como serviço. Com o banco de dados SQL, você não tem acesso às máquinas que hospedam seus bancos de dados. Para obter uma comparação completa, [consulte escolher uma nuvem SQL Server opção: Banco de dados SQL do Azure (PaaS) ou SQL Server em VMs do](../../../sql-database/sql-database-paas-vs-sql-server-iaas.md)Azure (IaaS).

1. **Como fazer instalar o SQL Data Tools na minha VM do Azure?**

    Baixe e instale as ferramentas de dados do SQL em [Microsoft SQL Server Data Tools-Business Intelligence para Visual Studio 2013](https://www.microsoft.com/download/details.aspx?id=42313).

1. **As transações distribuídas com o MSDTC têm suporte em VMs SQL Server?**
   
    Sim. O DTC local tem suporte para o SQL Server 2016 SP2 e superior. No entanto, os aplicativos devem ser testados ao utilizar grupos de disponibilidade Always On, pois as transações em andamento durante um failover falharão e deverão ser repetidas. O DTC clusterizado está disponível a partir do Windows Server 2019. 

## <a name="resources"></a>Recursos

**VMs do Windows**:

* [Visão geral de SQL Server em uma VM do Windows](virtual-machines-windows-sql-server-iaas-overview.md).
* [Provisionar uma VM SQL Server Windows](virtual-machines-windows-portal-sql-server-provision.md)
* [Migrando um banco de dados para SQL Server em uma VM do Azure](virtual-machines-windows-migrate-sql.md)
* [Alta disponibilidade e recuperação de desastre para SQL Server em máquinas virtuais do Azure](virtual-machines-windows-sql-high-availability-dr.md)
* [Melhores práticas de desempenho do SQL Server nas Máquinas Virtuais do Azure](virtual-machines-windows-sql-performance.md)
* [Padrões de Aplicação e Estratégias de Desenvolvimento para o SQL Server em Máquinas Virtuais do Azure](virtual-machines-windows-sql-server-app-patterns-dev-strategies.md)

**VMs do Linux**:

* [Visão geral de SQL Server em uma VM Linux](../../linux/sql/sql-server-linux-virtual-machines-overview.md)
* [Provisionar um SQL Server VM do Linux](../../linux/sql/provision-sql-server-linux-virtual-machine.md)
* [Perguntas frequentes (Linux)](../../linux/sql/sql-server-linux-faq.md)
* [Documentação do SQL Server no Linux](https://docs.microsoft.com/sql/linux/sql-server-linux-overview)
