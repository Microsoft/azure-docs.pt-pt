---
title: SQL Server em Máquinas Virtuais do Windows nas perguntas frequentes do Azure | Microsoft Docs
description: Este artigo fornece respostas para perguntas frequentes sobre a execução de SQL Server em VMs do Azure.
services: virtual-machines-windows
documentationcenter: ''
author: v-shysun
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
ms.openlocfilehash: 4b50b4acf6ea655c40821e7c49824af11aeeb9ab
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68816301"
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

1. **Posso criar uma imagem VHD de uma VM SQL Server?**

   Sim, mas há algumas considerações. Se você implantar esse VHD em uma nova VM no Azure, você não obterá a seção de configuração SQL Server no Portal. Em seguida, você deve gerenciar as opções de configuração de SQL Server por meio do PowerShell. Além disso, você será cobrado pela taxa da VM do SQL na qual sua imagem foi originalmente baseada. Isso é verdadeiro mesmo se você remover SQL Server do VHD antes da implantação. 

1. **É possível configurar as configurações não mostradas na Galeria de máquinas virtuais (por exemplo, Windows 2008 R2 + SQL Server 2012)?**

   Não. Para imagens da Galeria de máquinas virtuais que incluem SQL Server, você deve selecionar uma das imagens fornecidas por meio do portal do Azure ou por meio do [PowerShell](virtual-machines-windows-ps-sql-create.md). 


## <a name="creation"></a>Criação

1. **Como fazer criar uma máquina virtual do Azure com SQL Server?**

   A solução mais fácil é criar uma máquina virtual que inclua SQL Server. Para obter um tutorial sobre como se inscrever no Azure e criar uma VM do SQL por meio do portal, confira provisionar [uma máquina virtual SQL Server no portal do Azure](virtual-machines-windows-portal-sql-server-provision.md). Você pode selecionar uma imagem de máquina virtual que usa licenciamento de SQL Server de pagamento por segundo ou pode usar uma imagem que permite que você traga sua própria licença de SQL Server. Você também tem a opção de instalar manualmente o SQL Server em uma VM com uma edição gratuita licenciada (Developer ou Express) ou reutilizando uma licença local. Se você traga sua própria licença, deve ter [mobilidade de licenças via Software Assurance no Azure](https://azure.microsoft.com/pricing/license-mobility/). Para obter mais informações, consulte [Pricing guidance for SQL Server Azure VMs (Documentação de orientação sobre preços de VMs do Azure do SQL Server)](virtual-machines-windows-sql-server-pricing-guidance.md).

1. **Como posso migrar meu banco de dados local SQL Server para a nuvem?**

   Primeiro, crie uma máquina virtual do Azure com uma instância de SQL Server. Em seguida, migre seus bancos de dados locais para essa instância. Para estratégias de migração de dados, consulte [migrar um SQL Server banco de dado para SQL Server em uma VM do Azure](virtual-machines-windows-migrate-sql.md).

## <a name="licensing"></a>Licenciamento

1. **Como posso instalar a minha cópia licenciada do SQL Server numa VM do Azure?**

   Pode fazê-lo de duas formas. Pode aprovisionar uma das [imagens da máquina virtual que suporte as licenças](virtual-machines-windows-sql-server-iaas-overview.md#BYOL), que também é conhecido como “traga a sua própria licença” (“BYOL”). Outra opção é copiar o suporte de dados da instalação do SQL Server para uma VM do Windows Server e, depois, instalar o SQL Server na VM. Contudo, se instalar o SQL Server manualmente, não há integração no portal e a Extensão Agente de IaaS do SQL Server não é suportada, pelo que funcionalidades como a Cópia de Segurança Automática e a Aplicação de Patches Automática não funcionarão neste cenário. Por este motivo, recomendamos utilizar uma das imagens da galeria BYOL. Para usar o BYOL ou sua própria mídia de SQL Server em uma VM do Azure, você deve ter [mobilidade de licenças via Software Assurance no Azure](https://azure.microsoft.com/pricing/license-mobility/). Para obter mais informações, consulte [Pricing guidance for SQL Server Azure VMs (Documentação de orientação sobre preços de VMs do Azure do SQL Server)](virtual-machines-windows-sql-server-pricing-guidance.md).

1. **Tenho de pagar para licenciar o SQL Server numa VM do Azure se só estiver a ser utilizado para reserva/ativação pós-falha?**

   Se você tiver o Software Assurance e usar Mobilidade de Licenças conforme descrito em [perguntas frequentes sobre licenciamento de máquina virtual](https://azure.microsoft.com/pricing/licensing-faq/), não será necessário pagar para licenciar um SQL Server participando como uma réplica secundária passiva em uma implantação de alta disponibilidade. Caso contrário, tem de pagar para licenciar.

1. **Posso alterar uma VM para utilizar a minha própria licença do SQL Server caso tenha sido criada com uma das imagens da galeria pay as you go?**

   Sim. Você pode mover facilmente entre os dois modelos de licenciamento, se você originalmente começou com uma imagem da Galeria pré-paga. No entanto, não poderá mudar a licença para PAYG se tiver começado com uma imagem BYOL. Para obter mais informações, consulte [como alterar o modelo de licenciamento para uma VM SQL Server](virtual-machines-windows-sql-ahb.md).

   > [!Note]
   > Atualmente, essa instalação está disponível apenas para clientes de nuvem pública.

1. **Devo criar a minha VM do SQL nova com imagens BYOL ou o Fornecedor de Recursos de VM do SQL?**

   As imagens traga a sua própria licença (BYOL) só estão disponíveis para clientes EA. Outros clientes que têm o Software Assurance devem usar o provedor de recursos da VM do SQL para criar uma VM do SQL com [benefício híbrido do Azure (AHB)](https://azure.microsoft.com/pricing/licensing-faq/). 

1. **A mudança do modelo de licenciamento requer algum período de inatividade do SQL Server?**

   Não. A [alteração do modelo de licenciamento](virtual-machines-windows-sql-ahb.md) não exige nenhum tempo de inatividade para SQL Server, pois a alteração entra em vigor imediatamente e não requer uma reinicialização da VM. No entanto, para registrar sua VM SQL Server com o provedor de recursos de VM do SQL, a [extensão IaaS do SQL](virtual-machines-windows-sql-server-agent-extension.md) é um pré-requisito e a instalação da extensão SQL IaaS reinicia o serviço SQL Server. Como tal, se for necessário instalar a extensão IaaS do SQL, a instalação deve ser feita durante um período de manutenção. 

1. **As assinaturas do CSP podem ativar o Benefício Híbrido do Azure?**

   Sim, o Benefício Híbrido do Azure está disponível para assinaturas do CSP. Os clientes do CSP devem primeiro implantar uma imagem paga conforme o uso e, em seguida, [alterar o modelo](virtual-machines-windows-sql-ahb.md) de licenciamento para traga sua própria licença.  

1. **O registo da VM no novo fornecedor de recursos de VM do SQL acarretará custos adicionais?**

   Não. O fornecedor de recursos de VM do SQL apenas proporciona capacidade de gestão extra ao SQL Server na VM do Azure sem custos adicionais. 

1. **O fornecedor de recursos de VM do SQL está disponível para todos os clientes?**
 
   Sim. Todos os clientes se podem registar no novo fornecedor de recursos de VM do SQL. No entanto, somente clientes com o benefício do Software Assurance podem ativar o [benefício híbrido do Azure (AHB)](https://azure.microsoft.com/pricing/hybrid-benefit/) (ou BYOL) em uma VM SQL Server. 

1. **O que acontecerá com o recurso _Microsoft. SqlVirtualMachine_ se o recurso da VM for movido ou descartado?** 

   Quando o recurso Microsoft. Compute/VirtualMachine é Descartado ou movido, o recurso Microsoft. SqlVirtualMachine associado é notificado para replicar a operação de forma assíncrona.

1. **O que acontecerá com a VM se o recurso _Microsoft. SqlVirtualMachine_ for descartado?**

    O recurso Microsoft. Compute/VirtualMachine não é afetado quando o recurso Microsoft. SqlVirtualMachine é Descartado. No entanto, as alterações de licenciamento retornarão por padrão à origem da imagem original. 

1. **É possível registar VMs do SQL Server implementadas automaticamente no fornecedor de recursos de VM do SQL?**

    Sim. Se você implantou o SQL Server de sua própria mídia e instalou a extensão SQL IaaS, você pode registrar sua VM SQL Server com o provedor de recursos para obter os benefícios de gerenciamento fornecidos pela extensão SQL IaaS. No entanto, não pode converter uma VM do SQL implementada automaticamente em pay as you go.

1. **É possível alternar o modelo de licenciamento em uma VM SQL Server implantada usando o modelo clássico?**

   Não. Não há suporte para a alteração do modelo de licenciamento em uma VM clássica. Você pode migrar sua VM para o modelo do Resource Manager (ARM) e registrar com o provedor de recursos da VM do SQL. Depois que a VM estiver registrada com o provedor de recursos de VM do SQL, as alterações no modelo de licenciamento estarão disponíveis na VM. 
   

## <a name="administration"></a>Administração

1. **Posso instalar uma segunda instância do SQL Server na mesma VM? Posso alterar as funcionalidades instaladas da instância predefinida?**

   Sim. A mídia de instalação do SQL Server está localizada em uma pasta na unidade **C** . Execute **Setup. exe** desse local para adicionar novas instâncias de SQL Server ou para alterar outros recursos instalados do SQL Server no computador. Observe que alguns recursos, como o backup automatizado, a aplicação de patch automatizada e a integração de Azure Key Vault, operam apenas na instância padrão ou uma instância nomeada que foi configurada corretamente (consulte a pergunta 3). 

1. **Posso desinstalar a instância predefinida do SQL Server?**

   Sim, mas há considerações a ter em conta. Conforme mencionado na resposta anterior, há recursos que dependem da [extensão do agente IaaS SQL Server](virtual-machines-windows-sql-server-agent-extension.md).  Se você desinstalar a instância padrão sem remover a extensão IaaS também, a extensão continuará a procurar e poderá gerar erros de log de eventos. Esses erros são provenientes das duas origens abaixo: **Microsoft SQL Server o gerenciamento de credenciais** e **Microsoft SQL Server agente IaaS**. Um dos erros poderá ser semelhante ao seguinte:

      Ocorreu um erro relacionado com a rede ou específico da instância ao estabelecer uma ligação ao SQL Server. O servidor não foi encontrado ou não está acessível.

   Se você decidir desinstalar a instância padrão, também desinstale o [SQL Server extensão do agente IaaS](virtual-machines-windows-sql-server-agent-extension.md) .

1. **Posso usar uma instância nomeada do SQL Server com a extensão IaaS**?
   
   Sim, se a instância nomeada for a única na SQL Server e se a instância padrão original tiver sido desinstalada [corretamente](../sqlclassic/virtual-machines-windows-classic-sql-server-agent-extension.md#installation). Se não houver nenhuma instância padrão e houver várias instâncias nomeadas em uma única VM SQL Server, a extensão de IaaS não será instalada. 

1. **Posso remover completamente o SQL Server de uma VM do SQL?**

   Sim, mas você continuará a ser cobrado pela sua VM do SQL, conforme descrito em [diretrizes de preços para SQL Server VMs do Azure](virtual-machines-windows-sql-server-pricing-guidance.md). Se já não precisar do SQL Server, pode implementar uma máquina virtual nova e migrar os dados e as aplicações para a mesma. Depois, pode remover a máquina virtual do SQL Server.
   
## <a name="updating-and-patching"></a>Atualização e aplicação de patch

1. **Como mudar para uma versão/edição diferente do SQL Server numa VM do Azure?**

   Os clientes podem alterar a sua versão/edição do SQL Server com o suporte de dados de instalação que contém a versão ou a edição pretendida do SQL Server. Uma vez a edição alterada, utilize o portal do Azure para alterar a propriedade de edição da VM para refletir com precisão a faturação da VM. Para obter mais informações, consulte [Change Edition of a VM SQL Server](virtual-machines-windows-sql-change-edition.md). 


1. **Como as atualizações e os service packs são aplicados em uma VM SQL Server?**

   As máquinas virtuais dão-lhe controlo sobre o computador anfitrião, incluindo o momento e a forma como aplica atualizações. Para o sistema operacional, você pode aplicar manualmente as atualizações do Windows ou pode habilitar um serviço de agendamento chamado [aplicação de patch automatizada](virtual-machines-windows-sql-automated-patching.md). A Aplicação de Patches Automatizada instala todas as atualizações marcadas como importantes, incluindo atualizações do SQL Server nessa categoria. As outras atualizações opcionais do SQL Server têm de ser instaladas manualmente.

## <a name="general"></a>Geral

1. **Há SQL Server FCI (instâncias de cluster de failover) com suporte em VMs do Azure?**

   Sim. Você pode [criar um cluster de failover do Windows no Windows Server 2016](virtual-machines-windows-portal-sql-create-failover-cluster.md) e usar espaços de armazenamento diretos (S2D) para o armazenamento de cluster. Como alternativa, você pode usar soluções de armazenamento ou cluster de terceiros, conforme descrito em [alta disponibilidade e recuperação de desastres para SQL Server em máquinas virtuais do Azure](virtual-machines-windows-sql-high-availability-dr.md#azure-only-high-availability-solutions).

   > [!IMPORTANT]
   > Neste momento, não há suporte para a [extensão do agente IaaS SQL Server](virtual-machines-windows-sql-server-agent-extension.md) para SQL Server FCI no Azure. Recomendamos que você desinstale a extensão de VMs que participam do FCI. Essa extensão oferece suporte a recursos, como backup automatizado e aplicação de patches e alguns recursos do portal para SQL. Esses recursos não funcionarão para VMs do SQL depois que o agente for desinstalado.

1. **Qual é a diferença entre as VMs do SQL e o serviço do banco de dados SQL?**

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
