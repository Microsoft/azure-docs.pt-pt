---
title: SQL Server em máquinas de virtuais do Windows no FAQ do Azure | Documentos da Microsoft
description: Este artigo fornece respostas às perguntas mais frequentes sobre a execução do SQL Server em VMs do Azure.
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
ms.date: 07/12/2018
ms.author: v-shysun
ms.openlocfilehash: 6f064bb875786fc50073ab4216bc1c52ace294bf
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58113270"
---
# <a name="frequently-asked-questions-for-sql-server-running-on-windows-virtual-machines-in-azure"></a>Perguntas mais frequentes sobre o SQL Server em execução em máquinas virtuais do Windows no Azure

> [!div class="op_single_selector"]
> * [Windows](virtual-machines-windows-sql-server-iaas-faq.md)
> * [Linux](../../linux/sql/sql-server-linux-faq.md)

Este artigo fornece respostas para algumas das perguntas mais comuns sobre a execução [SQL Server em Windows máquinas virtuais no Azure](https://azure.microsoft.com/services/virtual-machines/sql-server/).

> [!NOTE]
> Este artigo se concentra em problemas específicos ao SQL Server em VMs do Windows. Se estiver a executar o SQL Server em VMs do Linux, veja a [FAQ de Linux](../../linux/sql/sql-server-linux-faq.md).

[!INCLUDE [support-disclaimer](../../../../includes/support-disclaimer.md)]

## <a id="images"></a> Imagens

1. **As imagens de Galeria de máquinas virtuais do SQL Server estão disponíveis?**

   O Azure mantém as imagens de máquinas virtuais para todas as versões principais suportadas do SQL Server em todas as edições para Windows e Linux. Para obter mais informações, consulte a lista completa de [imagens de VM do Windows](virtual-machines-windows-sql-server-iaas-overview.md#payasyougo) e [imagens de VM do Linux](../../linux/sql/sql-server-linux-virtual-machines-overview.md#create).

1. **Imagens de Galeria de máquinas virtuais do SQL Server existentes são atualizadas?**

   A cada dois meses, imagens do SQL Server na galeria da máquina virtual são atualizadas com o Windows mais recente e atualizações do Linux. Para imagens do Windows, isso inclui todas as atualizações marcadas como importantes no Windows Update, incluindo atualizações de segurança importantes do SQL Server e service packs. Para imagens do Linux, isso inclui as atualizações mais recentes do sistema. As atualizações cumulativas do SQL Server são tratadas de forma diferente para Linux e Windows. Para o Linux, atualizações cumulativas do SQL Server também inclui a atualização. Mas, neste momento, as VMs do Windows não são atualizadas com atualizações cumulativas do SQL Server ou Windows Server.

1. **Imagens de máquina virtual do SQL Server podem ser removidas da Galeria?**

   Sim. O Azure mantém apenas uma imagem por versão principal e de edição. Por exemplo, quando um novo service pack do SQL Server for lançado, o Azure adiciona uma nova imagem na Galeria para esse pacote de serviço. A imagem do SQL Server para o service pack anterior é imediatamente removida do portal do Azure. No entanto, ele ainda está disponível para aprovisionamento a partir do PowerShell dos três próximos meses. Depois de três meses, a imagem do service pack anterior já não está disponível. Esta política de remoção também seria aplicada caso uma versão do SQL Server se torna não suportada quando ela atinge o final do seu ciclo de vida.


1. **É possível implementar uma imagem mais antiga do SQL Server que não está visível no portal do Azure?**

   Sim, com o PowerShell. Para obter mais informações sobre a implementação de VMs do SQL Server com o PowerShell, consulte [como aprovisionar máquinas virtuais do SQL Server com o Azure PowerShell](virtual-machines-windows-ps-sql-create.md).

1. **Pode criar uma imagem VHD de uma VM do SQL Server?**

   Sim, mas aqui estão algumas considerações. Se implementar este VHD para uma nova VM no Azure, fazer não ge a seção de configuração do SQL Server no portal. Em seguida, tem de gerir as opções de configuração do SQL Server através do PowerShell. Além disso, faturada à tarifa da VM de SQL foi originalmente baseia sua imagem. Isso vale mesmo que remova o SQL Server a partir de VHD antes de implementar. 

1. **É possível definir configurações não apresentadas na galeria da máquina virtual (por exemplo, Windows 2008 R2 + SQL Server 2012)?**

   Não. Para imagens de Galeria de máquinas virtuais que incluem o SQL Server, tem de selecionar uma das imagens fornecidas pelo portal do Azure ou através de [PowerShell](virtual-machines-windows-ps-sql-create.md). 


## <a name="creation"></a>Criação

1. **Como posso criar uma máquina virtual do Azure com o SQL Server?**

   A solução mais simples é criar uma Máquina Virtual que inclui o SQL Server. Para um tutorial sobre a inscrição para o Azure e criar uma VM do SQL a partir do portal, consulte [aprovisionar uma máquina virtual do SQL Server no portal do Azure](virtual-machines-windows-portal-sql-server-provision.md). Pode selecionar uma imagem de máquina virtual que utiliza o pagamento-por-segundo licenciamento do SQL Server ou pode utilizar uma imagem que permite que traga a sua própria licença do SQL Server. Tem também a opção de instalar manualmente o SQL Server numa VM com o uma edição gratuita licenciada (desenvolvedor ou Express) ou ao reutilizar uma licença de acesso no local. Se utilizar a sua própria licença, tem de ter [mobilidade de licenças através do Software Assurance no Azure](https://azure.microsoft.com/pricing/license-mobility/). Para obter mais informações, consulte [Pricing guidance for SQL Server Azure VMs (Documentação de orientação sobre preços de VMs do Azure do SQL Server)](virtual-machines-windows-sql-server-pricing-guidance.md).

1. **Como posso migrar meu banco de dados do SQL Server no local para a Cloud?**

   Em primeiro lugar, crie uma máquina virtual do Azure com uma instância do SQL Server. Em seguida, migre as suas bases de dados no local a essa instância. Para estratégias de migração de dados, consulte [migrar uma base de dados do SQL Server para o SQL Server numa VM do Azure](virtual-machines-windows-migrate-sql.md).

## <a name="licensing"></a>Licenciamento

1. **Como posso instalar minha cópia licenciada do SQL Server numa VM do Azure?**

   Existem duas formas de fazê-lo. Pode aprovisionar um da [imagens de máquinas virtuais que suporta licenças](virtual-machines-windows-sql-server-iaas-overview.md#BYOL), que também é conhecido como bring-your-própria licença (BYOL). Outra opção é copiar a mídia de instalação do SQL Server para uma VM do Windows Server, e, em seguida, instalar o SQL Server na VM. No entanto, se instalar manualmente o SQL Server, não há nenhuma integração do portal e a extensão do agente IaaS do SQL Server não é suportada, por isso, funcionalidades, tais como cópia de segurança automatizada e aplicação de patches automatizada não funcionará neste cenário. Por esse motivo, recomendamos que utilize uma das imagens da Galeria BYOL. Para utilizar o seu próprio suporte de dados do SQL Server ou de BYOL numa VM do Azure, tem de ter [mobilidade de licenças através do Software Assurance no Azure](https://azure.microsoft.com/pricing/license-mobility/). Para obter mais informações, consulte [Pricing guidance for SQL Server Azure VMs (Documentação de orientação sobre preços de VMs do Azure do SQL Server)](virtual-machines-windows-sql-server-pricing-guidance.md).


1. **Tenho de pagar a licença do SQL Server numa VM do Azure, se está a ser utilizado apenas para o modo de espera/ativação pós-falha?**

   Se tem o Software Assurance e utilizar a mobilidade de licenças, conforme descrito em [FAQ de licenciamento de Máquina Virtual](https://azure.microsoft.com/pricing/licensing-faq/), então não tenha que pagar o licenciamento do SQL Server a participar como réplica secundária passiva numa implantação de HA. Caso contrário, terá de pagar a licença-lo.

1. **Posso alterar uma VM para utilizar a minha própria licença do SQL Server, se ele foi criado a partir de uma das imagens de galeria pay as you go?**

   Sim. Pode mover facilmente mover entre os dois modelos de licenciamento, se tiver começado originalmente com uma imagem de galeria pay as you go. No entanto, não será capaz de alternar sua licença para PAYG se inicialmente começou com uma imagem BYOL. Para obter mais informações, consulte [como alterar o modelo de licenciamento de uma VM do SQL Server](virtual-machines-windows-sql-ahb.md).

   > [!Note]
   > Atualmente, esta função só está disponível para clientes de Cloud pública.

1. **Posso utilizar imagens BYOL ou RP de VM do SQL para criar a nova VM do SQL?**

   Imagens do Bring-your-own-license (BYOL) apenas estão disponíveis para clientes com contrato EA. Outros clientes que tenham o Software Assurance devem utilizar o fornecedor de recursos de VM do SQL Server para criar uma VM do SQL com [Azure híbrido benefício (AHB)](https://azure.microsoft.com/pricing/licensing-faq/). 

1. **Modelos de licenciamento de comutação exigirá qualquer período de inatividade para o SQL Server?**

   Não. [Alterar o modelo de licenciamento](virtual-machines-windows-sql-ahb.md) não requer qualquer período de inatividade para o SQL Server como a alteração tem efeita imediato e não requer um reinício da VM. No entanto, para registar a sua VM do SQL Server com o fornecedor de recursos de VM do SQL Server, o [extensão SQL IaaS](virtual-machines-windows-sql-server-agent-extension.md) é um pré-requisito e instalar a extensão SQL IaaS reinicia o serviço SQL Server. Assim, se a extensão SQL IaaS tem de ser instalado, em seguida, ele deve ser feito durante uma janela de manutenção. 

1. **Subscrições de CSP podem ativar o benefício híbrido do Azure?**

   Sim, o benefício híbrido do Azure está disponível para subscrições de CSP. Clientes CSP primeiro devem implementar uma imagem de pay as you go e, em seguida [alterar o modelo de licenciamento](virtual-machines-windows-sql-ahb.md) para bring-your-own-license.  

1. **Registar a minha VM com o fornecedor de recursos de VM do SQL nova trará custos adicionais?**

   Não. O fornecedor de recursos de VM do SQL Server apenas permite a capacidade de gestão adicional para o SQL Server numa VM do Azure com sem custos adicionais. 

1. **O fornecedor de recursos de VM do SQL Server está disponível para todos os clientes?**
 
   Sim. Todos os clientes conseguem registar com o novo fornecedor de recursos de VM do SQL Server. No entanto, apenas os clientes com o benefício do Software Assurance podem ativar a [Azure híbrido benefício (AHB)](https://azure.microsoft.com/pricing/hybrid-benefit/) (ou de BYOL) numa VM do SQL Server. 

1. **O que acontece com o _Microsoft.SqlVirtualMachine_ recurso se o recurso da VM é movido ou removido?** 

   Quando o recurso de Microsoft.Compute/VirtualMachine é removido ou movido, em seguida, o recurso de Microsoft.SqlVirtualMachine associado é notificado para replicar a operação de forma assíncrona.

1. **O que acontece com a VM se a _Microsoft.SqlVirtualMachine_ é arrastado para recursos?**

    O recurso de Microsoft.Compute/VirtualMachine não é afetado quando é arrastado para o recurso de Microsoft.SqlVirtualMachine. No entanto, as alterações de licenciamento serão predefinido para a origem da imagem original. 

1. **É possível registar Self-implementado VMs do SQL Server com o fornecedor de recursos de VM do SQL?**

    Sim. Se implementada do SQL Server a partir do seu próprio suporte de dados e instalou a extensão SQL IaaS pode registar a sua VM do SQL Server com o fornecedor de recursos para obter os benefícios de capacidade de gerenciamento fornecidos pela extensão SQL IaaS. No entanto, é possível converter uma VM de SQL personalizada implementada para pay as you go.

## <a name="administration"></a>Administração

1. **Pode instalar uma segunda instância do SQL Server na mesma VM? Pode alterar as funcionalidades instaladas da instância predefinida?**

   Sim. O suporte de dados de instalação do SQL Server está localizado numa pasta na **C** unidade. Execute **Setup.exe** partir dessa localização para adicionar novas instâncias do SQL Server ou para funcionalidades de alteração outros instalada do SQL Server na máquina. Tenha em atenção que alguns recursos, como a cópia de segurança automatizada, a aplicação de patches automatizada e integração do Cofre de chaves do Azure, só funcionam em relação a instância predefinida ou uma determinada instância foi corretamente configurada (consulte a 3 de pergunta). 

1. **Pode desinstalar a instância predefinida do SQL Server?**

   Sim, mas aqui estão algumas considerações. Conforme indicado na resposta anterior, existem recursos que contam com o [extensão de agente IaaS do SQL Server](virtual-machines-windows-sql-server-agent-extension.md).  Se desinstalar a instância predefinida sem remover a extensão de IaaS também, a extensão continua a procurar por ele e poderá gerar erros de registo de eventos. Estes erros são das duas seguintes origens: **Gestão de credenciais do Microsoft SQL Server** e **agente IaaS do Microsoft SQL Server**. Um dos erros pode ser semelhante ao seguinte:

      Ocorreu um erro relacionado com a rede ou específico da instância ao estabelecer uma ligação ao SQL Server. O servidor não foi encontrado ou não está acessível.

   Se optar por desinstalar a instância predefinida, também desinstalar o [extensão de agente IaaS do SQL Server](virtual-machines-windows-sql-server-agent-extension.md) também.

1. **Pode utilizar uma instância nomeada do SQL Server com a extensão de IaaS**?
   
   Sim, se a instância nomeada é a única ocorrência no SQL Server e se a instância predefinida original foi desinstalada corretamente. Para utilizar uma instância nomeada, faça o seguinte:
    1. Implemente uma VM do SQL Server do marketplace. 
    1. Desinstale a extensão de IaaS.
    1. Desinstale completamente o SQL Server.
    1. Instale o SQL Server com uma instância nomeada. 
    1. Instale a extensão de IaaS. 

1. **Posso remover do SQL Server completamente de uma VM do SQL?**

   Sim, mas continuará a ser cobrada a VM do SQL conforme descrito em [preços orientações para VMs do Azure do SQL Server](virtual-machines-windows-sql-server-pricing-guidance.md). Se já não precisar do SQL Server, pode implementar uma nova máquina virtual e migrar os dados e aplicativos para a nova máquina virtual. Em seguida, pode remover a máquina virtual do SQL Server.
   
## <a name="updating-and-patching"></a>A atualização e aplicação de patches

1. **Como posso alterar para uma nova versão/edição do SQL Server numa VM do Azure?**

   Os clientes com Software Assurance podem atualizações in-loco do seu SQL Server em execução na VM do Azure com a mídia de instalação no Portal de licenciamento de Volume. No entanto, atualmente, não é possível alterar a edição de uma instância do SQL Server. Criar uma nova máquina virtual do Azure com a edição do SQL Server pretendida e, em seguida, migre as suas bases de dados para o novo servidor com o padrão [técnicas de migração de dados](virtual-machines-windows-migrate-sql.md).

1. **Como são atualizações e service packs aplicadas numa VM do SQL Server?**

   As máquinas virtuais dão-lhe controlo sobre o computador anfitrião, incluindo o momento e a forma como aplica atualizações. Para o sistema operativo, pode aplicar manualmente as atualizações do windows, ou pode ativar a um serviço de agendamento denominado [aplicação de patches automatizada](virtual-machines-windows-sql-automated-patching.md). A Aplicação de Patches Automatizada instala todas as atualizações marcadas como importantes, incluindo atualizações do SQL Server nessa categoria. As outras atualizações opcionais do SQL Server têm de ser instaladas manualmente.

## <a name="general"></a>Geral

1. **Instâncias de Cluster (FCI) do SQL Server ativação pós-falha são suportados em VMs do Azure?**

   Sim. Pode [criar um Cluster de ativação pós-falha do Windows no Windows Server 2016](virtual-machines-windows-portal-sql-create-failover-cluster.md) e utilizar espaços de armazenamento direto (S2D) para o armazenamento de cluster. Em alternativa, pode utilizar soluções de clustering ou de armazenamento de terceiros conforme descrito em [elevada disponibilidade e recuperação após desastre para o SQL Server em máquinas de virtuais do Azure](virtual-machines-windows-sql-high-availability-dr.md#azure-only-high-availability-solutions).

   > [!IMPORTANT]
   > Neste momento, o [extensão de agente IaaS do SQL Server](virtual-machines-windows-sql-server-agent-extension.md) não é suportada para a FCI do servidor SQL no Azure. Recomendamos que desinstale a extensão de VMs que participam do fci. Esta extensão oferece suporte a recursos, tais como cópia de segurança automatizada e a aplicação de patches e alguns recursos de portal para SQL. Estas funcionalidades não funcionarão para VMs de SQL após a desinstalação do agente.

1. **O que é a diferença entre as VMs do SQL e o serviço de base de dados SQL?**

   Conceitualmente, o SQL Server numa máquina virtual do Azure não, isso é diferente de executar o SQL Server num datacenter remoto. Por outro lado, [base de dados SQL](../../../sql-database/sql-database-technical-overview.md) oferece a base de dados-como-serviço. Base de dados SQL, não tem acesso aos computadores que alojam as bases de dados. Para obter uma comparação completa, consulte [escolher uma opção de SQL Server na nuvem: Base de dados SQL do Azure (PaaS) ou o SQL Server em VMs do Azure (IaaS)](../../../sql-database/sql-database-paas-vs-sql-server-iaas.md).

1. **Como posso instalar ferramentas de dados do SQL na minha VM do Azure?**

    Transferir e instalar as ferramentas de dados SQL a partir [Microsoft SQL Server Data Tools – Business Intelligence para Visual Studio 2013](https://www.microsoft.com/en-us/download/details.aspx?id=42313).

## <a name="resources"></a>Recursos

**VMs do Windows**:

* [Descrição geral do SQL Server num VM do Windows](virtual-machines-windows-sql-server-iaas-overview.md).
* [Aprovisionar um Windows do servidor SQL VM](virtual-machines-windows-portal-sql-server-provision.md)
* [Migrar uma base de dados para o SQL Server numa VM do Azure](virtual-machines-windows-migrate-sql.md)
* [Elevada disponibilidade e recuperação após desastre para SQL Server em máquinas virtuais do Azure](virtual-machines-windows-sql-high-availability-dr.md)
* [Melhores práticas de desempenho do SQL Server nas Máquinas Virtuais do Azure](virtual-machines-windows-sql-performance.md)
* [Padrões de Aplicação e Estratégias de Desenvolvimento para o SQL Server em Máquinas Virtuais do Azure](virtual-machines-windows-sql-server-app-patterns-dev-strategies.md)

**VMs do Linux**:

* [Descrição geral do SQL Server numa VM do Linux](../../linux/sql/sql-server-linux-virtual-machines-overview.md)
* [Aprovisionar uma VM de Linux do SQL Server](../../linux/sql/provision-sql-server-linux-virtual-machine.md)
* [FAQ (Linux)](../../linux/sql/sql-server-linux-faq.md)
* [Documentação do SQL Server no Linux](https://docs.microsoft.com/sql/linux/sql-server-linux-overview)
