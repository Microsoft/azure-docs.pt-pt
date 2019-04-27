---
title: SQL Server em máquinas virtuais do Linux do Azure FAQ | Documentos da Microsoft
description: Este artigo fornece respostas às perguntas mais frequentes sobre a execução do SQL Server em VMs do Linux do Azure.
services: virtual-machines-linux
documentationcenter: ''
author: MashaMSFT
manager: craigg
tags: azure-service-management
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: troubleshooting
ms.workload: iaas-sql-server
ms.date: 12/13/2017
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: fb1cafcf9405576749ea91aeea033c6ee783a026
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60739272"
---
# <a name="frequently-asked-questions-for-sql-server-on-linux-azure-virtual-machines"></a>Perguntas mais frequentes para o SQL Server em máquinas de virtuais do Linux do Azure

> [!div class="op_single_selector"]
> * [Windows](../../windows/sql/virtual-machines-windows-sql-server-iaas-faq.md)
> * [Linux](sql-server-linux-faq.md)

Este artigo fornece respostas para algumas das perguntas mais comuns sobre a execução [SQL Server em máquinas de virtuais do Azure Linux](sql-server-linux-virtual-machines-overview.md).

> [!NOTE]
> Este artigo se concentra em problemas específicos para o SQL Server em VMs do Linux. Se estiver a executar o SQL Server em VMs do Windows, consulte a [FAQ do Windows](../../windows/sql/virtual-machines-windows-sql-server-iaas-faq.md).

[!INCLUDE [support-disclaimer](../../../../includes/support-disclaimer.md)]

## <a id="images"></a> Imagens

1. **As imagens de Galeria de máquinas virtuais do SQL Server estão disponíveis?**

   O Azure mantém as imagens de máquinas virtuais para todas as versões principais suportadas do SQL Server em todas as edições para Linux e Windows. Para obter mais detalhes, consulte a lista completa de [imagens de VM do Linux](sql-server-linux-virtual-machines-overview.md#create) e [imagens de VM do Windows](../../windows/sql/virtual-machines-windows-sql-server-iaas-overview.md#payasyougo).

1. **Imagens de Galeria de máquinas virtuais do SQL Server existentes são atualizadas?**

   A cada dois meses, imagens do SQL Server na galeria da máquina virtual são atualizadas com a Linux mais recente e atualizações do Windows. Para imagens do Linux, isso inclui as atualizações mais recentes do sistema. Para imagens do Windows, isso inclui todas as atualizações marcadas como importantes no Windows Update, incluindo atualizações de segurança importantes do SQL Server e service packs. As atualizações cumulativas do SQL Server são tratadas de forma diferente para Linux e Windows. Para o Linux, atualizações cumulativas do SQL Server também inclui a atualização. Mas, neste momento, as VMs do Windows não são atualizadas com atualizações cumulativas do SQL Server ou Windows Server.

1. **O que relacionados com pacotes do SQL Server também são instalados?**

   Para ver os pacotes do SQL Server que são instalados por predefinição em VMs do Linux do SQL Server, consulte [pacotes instalados](sql-server-linux-virtual-machines-overview.md#packages).

1. **Imagens de máquina virtual do SQL Server podem ser removidas da Galeria?**

   Sim. O Azure mantém apenas uma imagem por versão principal e de edição. Por exemplo, quando um novo service pack do SQL Server for lançado, o Azure adiciona uma nova imagem na Galeria para esse pacote de serviço. A imagem do SQL Server para o service pack anterior é imediatamente removida do portal do Azure. No entanto, ele ainda está disponível para aprovisionamento a partir do PowerShell dos três próximos meses. Depois de três meses, a imagem do service pack anterior já não está disponível. Esta política de remoção também seria aplicada caso uma versão do SQL Server se torna não suportada quando ela atinge o final do seu ciclo de vida.

## <a name="creation"></a>Criação

1. **Como posso criar uma máquina virtual do Linux do Azure com o SQL Server?**

   A solução mais simples é criar uma Máquina Virtual do Linux que inclui o SQL Server. Para um tutorial sobre a inscrição para o Azure e criar uma VM do SQL a partir do portal, consulte [aprovisionar uma máquina virtual do SQL Server do Linux no portal do Azure](provision-sql-server-linux-virtual-machine.md). Tem também a opção de instalar manualmente o SQL Server numa VM com o uma edição gratuita licenciada (desenvolvedor ou Express) ou ao reutilizar uma licença de acesso no local. Se utilizar a sua própria licença, tem de ter [mobilidade de licenças através do Software Assurance no Azure](https://azure.microsoft.com/pricing/license-mobility).

1. **Por que não é possível aprovisionar uma RHEL ou SLES VM do SQL Server com uma subscrição do Azure que tem um limite de gastos?**

   As máquinas de virtuais RHEL e SLES exigem uma subscrição sem limite de gastos e um método de pagamento verificado (normalmente, um cartão de crédito) associados à subscrição. Se aprovisionar uma VM de SLES ou RHEL sem remover o limite de gastos, a subscrição será obter desativada e todas as VMs/serviços parados. Caso se depare com este estado, para reativar a subscrição [remover o limite de gastos](https://account.windowsazure.com/subscriptions). Os créditos restantes vão ser restaurados para o ciclo de faturação atual, mas uma sobretaxa de imagem RHEL ou SLES VM entram no seu cartão de crédito se optar por começar novamente e continuar a executá-lo.

## <a name="licensing"></a>Licenciamento

1. **Como posso instalar a minha cópia licenciada do SQL Server numa VM do Azure?**

   Primeiro, crie uma máquina de virtual de só de SO Linux. Em seguida, execute o [passos de instalação do SQL Server](https://docs.microsoft.com/sql/linux/sql-server-linux-setup#platforms) para a distribuição de Linux. A menos que estiver instalando uma das edições livremente licenciadas do SQL Server, também tem de ter uma licença do SQL Server e [mobilidade de licenças através do Software Assurance no Azure](https://azure.microsoft.com/pricing/license-mobility/).

1. **Existem imagens de máquinas virtuais do Linux de Bring-Your-Own-License (BYOL) para o SQL Server?**

   Neste momento, não há nenhum imagens de máquinas virtuais do BYOL Linux para o SQL Server. No entanto, pode instalar manualmente do SQL Server numa VM do Linux apenas conforme discutido às perguntas anteriores.

1. **Posso alterar uma VM para utilizar a minha própria licença do SQL Server caso tenha sido criada com uma das imagens da galeria pay as you go?**

   Não. Não é possível mudar do pagamento por segundo de licenciamento para a sua própria licença de utilização. Tem de criar uma nova VM do Linux, instale o SQL Server e migre os seus dados. Consulte a pergunta anterior para obter mais detalhes sobre trazer a sua própria licença.

## <a name="administration"></a>Administração

1. **Pode gerir uma máquina virtual de Linux SQL Server com o SQL Server Management Studio (SSMS)?**

   Sim, mas SSMS atualmente é uma ferramenta única de Windows. Tem de ligar remotamente a partir de uma máquina do Windows para utilizar o SSMS com VMs do Linux SQL Server. Localmente no Linux, a nova [mssql-conf](https://docs.microsoft.com/sql/linux/sql-server-linux-configure-mssql-conf) ferramenta pode realizar diversas tarefas administrativas. Para uma ferramenta de gerenciamento de banco de dados de várias plataformas, consulte [Estúdio de dados do Azure](https://docs.microsoft.com/sql/azure-data-studio/what-is).

1. **Posso remover completamente o SQL Server de uma VM do SQL?**

   Sim, mas continuará a ser cobrada a VM do SQL conforme descrito em [preços orientações para VMs do Azure do SQL Server](../../windows/sql/virtual-machines-windows-sql-server-pricing-guidance.md?toc=%2fazure%2fvirtual-machines%2flinux%2fsql%2ftoc.json). Se já não precisar do SQL Server, pode implementar uma máquina virtual nova e migrar os dados e as aplicações para a mesma. Depois, pode remover a máquina virtual do SQL Server.

## <a name="updating-and-patching"></a>A atualização e aplicação de patches

1. **Como posso atualizar para uma nova versão/edição do SQL Server numa VM do Azure?**

   Atualmente, não existe qualquer atualização no local para o SQL Server em execução numa VM do Azure. Crie uma nova máquina virtual do Azure com a versão/edição pretendida do SQL Server e, em seguida, migre as suas bases de dados para o novo servidor, [através de técnicas comuns de migração de dados](https://docs.microsoft.com/sql/linux/sql-server-linux-migrate-overview).

## <a name="general"></a>Geral

1. **Soluções de alta disponibilidade do SQL Server são suportadas em VMs do Azure?**

   Neste momento, não. Grupos de Disponibilidade AlwaysOn e Clustering de ativação pós-falha ambas requerem uma solução de clustering no Linux, como Pacemaker. As distribuições suportadas de Linux para o SQL Server não suportam os suplementos de elevada disponibilidade na Cloud.

## <a name="resources"></a>Recursos

**VMs do Linux**:

* [Descrição geral do SQL Server numa VM do Linux](sql-server-linux-virtual-machines-overview.md)
* [Aprovisionar uma VM de Linux do SQL Server](provision-sql-server-linux-virtual-machine.md)
* [Documentação do SQL Server no Linux](https://docs.microsoft.com/sql/linux/sql-server-linux-overview)

**VMs do Windows**:

* [Descrição geral do SQL Server num VM do Windows](../../windows/sql/virtual-machines-windows-sql-server-iaas-overview.md)
* [Aprovisionar um Windows do servidor SQL VM](../../windows/sql/virtual-machines-windows-portal-sql-server-provision.md)
* [FAQ (Windows)](../../windows/sql/virtual-machines-windows-sql-server-iaas-faq.md)