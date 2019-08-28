---
title: Perguntas frequentes sobre o SQL Server em Linux máquinas virtuais do Azure | Microsoft Docs
description: Este artigo fornece respostas para perguntas frequentes sobre como executar SQL Server em Linux VMs do Azure.
services: virtual-machines-linux
documentationcenter: ''
author: MashaMSFT
manager: craigg
tags: azure-service-management
ms.service: virtual-machines-sql
ms.topic: troubleshooting
ms.workload: iaas-sql-server
ms.date: 12/13/2017
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 1e729c608a2cad28c810f8d5236360c909a496b0
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70082033"
---
# <a name="frequently-asked-questions-for-sql-server-on-linux-azure-virtual-machines"></a>Perguntas frequentes para SQL Server em Linux máquinas virtuais do Azure

> [!div class="op_single_selector"]
> * [Windows](../../windows/sql/virtual-machines-windows-sql-server-iaas-faq.md)
> * [Linux](sql-server-linux-faq.md)

Este artigo fornece respostas para algumas das perguntas mais comuns sobre a execução de [SQL Server em Linux máquinas virtuais do Azure](sql-server-linux-virtual-machines-overview.md).

> [!NOTE]
> Este artigo se concentra em problemas específicos para SQL Server em Linux VMs. Se você estiver executando SQL Server em VMs do Windows, consulte as [perguntas frequentes do Windows](../../windows/sql/virtual-machines-windows-sql-server-iaas-faq.md).

[!INCLUDE [support-disclaimer](../../../../includes/support-disclaimer.md)]

## <a id="images"></a>Imagens

1. **Quais SQL Server imagens da Galeria de máquinas virtuais estão disponíveis?**

   O Azure mantém imagens de máquina virtual para todas as versões principais com suporte do SQL Server em todas as edições para Linux e Windows. Para obter mais detalhes, consulte a lista completa de [imagens de VM do Linux](sql-server-linux-virtual-machines-overview.md#create) e imagens de VM do [Windows](../../windows/sql/virtual-machines-windows-sql-server-iaas-overview.md#payasyougo).

1. **As imagens da Galeria de máquinas virtuais SQL Server existentes são atualizadas?**

   A cada dois meses, as imagens SQL Server na Galeria de máquinas virtuais são atualizadas com as atualizações mais recentes do Linux e do Windows. Para imagens do Linux, isso inclui as atualizações mais recentes do sistema. Para imagens do Windows, isso inclui todas as atualizações marcadas como importantes no Windows Update, incluindo atualizações de segurança SQL Server importantes e Service Packs. SQL Server atualizações cumulativas são tratadas de forma diferente para Linux e Windows. Para o Linux, SQL Server atualizações cumulativas também são incluídas na atualização. Mas, no momento, as VMs do Windows não são atualizadas com SQL Server ou atualizações cumulativas do Windows Server.

1. **Quais pacotes de SQL Server relacionados também são instalados?**

   Para ver os pacotes de SQL Server que são instalados por padrão em VMs SQL Server Linux, consulte [pacotes instalados](sql-server-linux-virtual-machines-overview.md#packages).

1. **É possível SQL Server imagens de máquina virtual sejam removidas da Galeria?**

   Sim. O Azure mantém apenas uma imagem por versão principal e edição. Por exemplo, quando um novo service pack de SQL Server é liberado, o Azure adiciona uma nova imagem à galeria para essa service pack. A imagem de SQL Server para a service pack anterior é removida imediatamente da portal do Azure. No entanto, ele ainda está disponível para provisionamento do PowerShell nos próximos três meses. Após três meses, a imagem de service pack anterior não estará mais disponível. Essa política de remoção também se aplicará se uma versão de SQL Server não for suportada quando atingir o final de seu ciclo de vida.

## <a name="creation"></a>Criação

1. **Como fazer criar uma máquina virtual Linux do Azure com SQL Server?**

   A solução mais fácil é criar uma máquina virtual Linux que inclua SQL Server. Para obter um tutorial sobre como se inscrever no Azure e criar uma VM do SQL por meio do portal, consulte provisionar [uma máquina virtual Linux SQL Server no portal do Azure](provision-sql-server-linux-virtual-machine.md). Você também tem a opção de instalar manualmente o SQL Server em uma VM com uma edição gratuita licenciada (Developer ou Express) ou reutilizando uma licença local. Se você traga sua própria licença, deve ter [mobilidade de licenças via Software Assurance no Azure](https://azure.microsoft.com/pricing/license-mobility).

1. **Por que não é possível provisionar uma VM RHEL ou SLES SQL Server com uma assinatura do Azure que tenha um limite de gastos?**

   As máquinas virtuais RHEL e SLES exigem uma assinatura sem limite de gastos e um método de pagamento verificado (geralmente um cartão de crédito) associado à assinatura. Se você provisionar uma VM RHEL ou SLES sem remover o limite de gastos, sua assinatura será desabilitada e todas as VMs/serviços serão interrompidos. Se você encontrar esse Estado, para reabilitar a assinatura, [remova o limite de gastos](https://account.windowsazure.com/subscriptions). Os créditos restantes serão restaurados para o ciclo de cobrança atual, mas uma sobretaxa de imagem de VM RHEL ou SLES entrará em seu cartão de crédito se você optar por reiniciá-lo e continuar a executá-lo.

## <a name="licensing"></a>Licenciamento

1. **Como posso instalar a minha cópia licenciada do SQL Server numa VM do Azure?**

   Primeiro, crie uma máquina virtual somente de SO Linux. Em seguida, execute as [etapas de instalação do SQL Server](https://docs.microsoft.com/sql/linux/sql-server-linux-setup#platforms) para sua distribuição do Linux. A menos que você esteja instalando uma das edições licenciadas gratuitamente do SQL Server, você também deve ter uma licença SQL Server e [mobilidade de licenças via Software Assurance no Azure](https://azure.microsoft.com/pricing/license-mobility/).

1. **Há imagens de máquina virtual Linux BYOL (traga sua própria licença) para SQL Server?**

   Neste momento, não há nenhuma imagem de máquina virtual BYOL Linux para SQL Server. No entanto, você pode instalar manualmente SQL Server em uma VM somente Linux, conforme discutido nas perguntas anteriores.

1. **Posso alterar uma VM para utilizar a minha própria licença do SQL Server caso tenha sido criada com uma das imagens da galeria pay as you go?**

   Não. Você não pode alternar do licenciamento de pagamento por segundo para o usando sua própria licença. Você deve criar uma nova VM do Linux, instalar SQL Server e migrar seus dados. Consulte a pergunta anterior para obter mais detalhes sobre como trazer sua própria licença.

## <a name="administration"></a>Administração

1. **Posso gerenciar uma máquina virtual Linux SQL Server com SQL Server Management Studio (SSMS)?**

   Sim, mas o SSMS é atualmente uma ferramenta somente para Windows. Você deve se conectar remotamente de um computador Windows para usar o SSMS com VMs SQL Server do Linux. Localmente no Linux, a nova ferramenta [MSSQL-conf](https://docs.microsoft.com/sql/linux/sql-server-linux-configure-mssql-conf) pode executar muitas tarefas administrativas. Para uma ferramenta de gerenciamento de banco de dados de plataforma cruzada, consulte [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/what-is).

1. **Posso remover completamente o SQL Server de uma VM do SQL?**

   Sim, mas você continuará a ser cobrado pela sua VM do SQL, conforme descrito em [diretrizes de preços para SQL Server VMs do Azure](../../windows/sql/virtual-machines-windows-sql-server-pricing-guidance.md?toc=%2fazure%2fvirtual-machines%2flinux%2fsql%2ftoc.json). Se já não precisar do SQL Server, pode implementar uma máquina virtual nova e migrar os dados e as aplicações para a mesma. Depois, pode remover a máquina virtual do SQL Server.

## <a name="updating-and-patching"></a>Atualização e aplicação de patch

1. **Como fazer atualizar para uma nova versão/edição do SQL Server em uma VM do Azure?**

   Atualmente, não existe qualquer atualização no local para o SQL Server em execução numa VM do Azure. Crie uma nova máquina virtual do Azure com a versão/edição pretendida do SQL Server e, em seguida, migre as suas bases de dados para o novo servidor, [através de técnicas comuns de migração de dados](https://docs.microsoft.com/sql/linux/sql-server-linux-migrate-overview).

## <a name="general"></a>Geral

1. **Há SQL Server soluções de alta disponibilidade com suporte em VMs do Azure?**

   Neste momento, não. Always On grupos de disponibilidade e clustering de failover exigem uma solução de clustering no Linux, como pacemaker. As distribuições Linux com suporte para SQL Server não dão suporte aos complementos de alta disponibilidade na nuvem.

## <a name="resources"></a>Recursos

**VMs do Linux**:

* [Visão geral de SQL Server em uma VM Linux](sql-server-linux-virtual-machines-overview.md)
* [Provisionar um SQL Server VM do Linux](provision-sql-server-linux-virtual-machine.md)
* [Documentação do SQL Server no Linux](https://docs.microsoft.com/sql/linux/sql-server-linux-overview)

**VMs do Windows**:

* [Visão geral de SQL Server em uma VM do Windows](../../windows/sql/virtual-machines-windows-sql-server-iaas-overview.md)
* [Provisionar uma VM SQL Server Windows](../../windows/sql/virtual-machines-windows-portal-sql-server-provision.md)
* [Perguntas frequentes (Windows)](../../windows/sql/virtual-machines-windows-sql-server-iaas-faq.md)