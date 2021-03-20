---
title: SQL Server em máquinas virtuais Linux FAQ | Microsoft Docs
description: Este artigo fornece respostas a perguntas frequentes sobre a execução do SQL Server em máquinas virtuais Linux.
services: virtual-machines-linux
documentationcenter: ''
author: MashaMSFT
tags: azure-service-management
ms.service: virtual-machines-sql
ms.topic: troubleshooting
ms.workload: iaas-sql-server
ms.date: 12/13/2017
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: ad991974df30060e552d21a44d5796cd2ba165e2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92792553"
---
# <a name="frequently-asked-questions-for-sql-server-on-linux-virtual-machines"></a>Perguntas frequentes para SQL Server em máquinas virtuais Linux
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

> [!div class="op_single_selector"]
> * [Windows](../windows/frequently-asked-questions-faq.md)
> * [Linux](frequently-asked-questions-faq.md)

Este artigo fornece respostas a algumas das perguntas mais comuns sobre a execução do [SQL Server em máquinas virtuais Linux](sql-server-on-linux-vm-what-is-iaas-overview.md).

[!INCLUDE [support-disclaimer](../../../../includes/support-disclaimer.md)]

## <a name="images"></a><a id="images"></a> Imagens

1. **Que imagens de galeria de máquinas virtuais SQL Server estão disponíveis?**

   O Azure mantém imagens de máquinas virtuais (VM) para todas as grandes versões suportadas do SQL Server em todas as edições tanto para o Linux como para o Windows. Para mais detalhes, consulte a lista completa de [imagens Linux VM](sql-server-on-linux-vm-what-is-iaas-overview.md#create) e [imagens Do Windows VM](../windows/sql-server-on-azure-vm-iaas-what-is-overview.md#payasyougo).

1. **As imagens existentes na galeria de máquinas virtuais SQL Server estão atualizadas?**

   De dois em dois meses, as imagens do SQL Server na galeria de máquinas virtuais são atualizadas com as mais recentes atualizações do Linux e do Windows. Para imagens Linux, isto inclui as mais recentes atualizações do sistema. Para as imagens do Windows, isto inclui quaisquer atualizações que sejam marcadas como importantes no Windows Update, incluindo importantes atualizações de segurança do SQL Server e pacotes de serviço. As atualizações cumulativas do SQL Server são tratadas de forma diferente para o Linux e o Windows. Para o Linux, as atualizações cumulativas do SQL Server também estão incluídas na atualização. Mas, neste momento, os VMs do Windows não são atualizados com atualizações cumulativas do SQL Server ou do Windows Server.

1. **Quais os pacotes relacionados com o SQL Server também estão instalados?**

   Para ver os pacotes SQL Server que são instalados por predefinição no SQL Server em VMs Linux, consulte [pacotes instalados](sql-server-on-linux-vm-what-is-iaas-overview.md#packages).

1. **As imagens da máquina virtual SQL Server podem ser removidas da galeria?**

   Sim. O Azure mantém apenas uma imagem por grande versão e edição. Por exemplo, quando um novo pacote de serviços SQL Server é lançado, a Azure adiciona uma nova imagem à galeria para esse pacote de serviços. A imagem do SQL Server para o pacote de serviços anterior é imediatamente removida do portal Azure. No entanto, ainda está disponível para o fornecimento da PowerShell para os próximos três meses. Após três meses, a imagem do pacote de serviço anterior já não está disponível. Esta política de remoção também se aplicaria se uma versão SQL Server não for suportada quando chegar ao fim do seu ciclo de vida.

## <a name="creation"></a>Criação

1. **Como posso criar uma máquina virtual Linux com SQL Server?**

   A solução mais fácil é criar uma máquina virtual Linux que inclua o SQL Server. Para um tutorial sobre a inscrição no Azure e a criação de um SQL Server VM a partir do portal, consulte [Disposição de uma máquina virtual Linux que executa o SQL Server no portal Azure](sql-vm-create-portal-quickstart.md). Também tem a opção de instalar manualmente o SQL Server num VM com uma edição livremente licenciada (Developer ou Express) ou reutilizando uma licença no local. Se trouxer a sua própria licença, deve ter [Mobilidade de Licença através de Garantia de Software na Azure.](https://azure.microsoft.com/pricing/license-mobility)

1. **Por que não posso providenciar um RHEL ou SLES SQL Server VM com uma assinatura Azure que tem um limite de gastos?**

   As máquinas virtuais RHEL e SLES requerem uma subscrição sem limite de gastos e um método de pagamento verificado (geralmente um cartão de crédito) associado à subscrição. Se forte um RHEL ou SLES VM sem remover o limite de gastos, a sua subscrição ficará desativada e todos os VMs/serviços parados. Se encontrar este estado, para ree capacitar a [subscrição, retire o limite de gastos](https://account.windowsazure.com/subscriptions). Os seus créditos restantes serão restaurados para o ciclo de faturação atual, mas uma sobretaxa de imagem RHEL ou SLES VM irá contra o seu cartão de crédito se optar por recomeçar e continuar a executá-lo.

## <a name="licensing"></a>Licensing

1. **Como posso instalar a minha cópia licenciada do SQL Server numa VM do Azure?**

   Primeiro, crie uma máquina virtual apenas linux OS. Em seguida, executar os [passos de instalação do SQL Server](/sql/linux/sql-server-linux-setup#platforms) para a sua distribuição Linux. A menos que esteja a instalar uma das edições livremente licenciadas do SQL Server, também deve ter uma licença sql Server e [Mobilidade de Licença através de Software Assurance on Azure](https://azure.microsoft.com/pricing/license-mobility/).

1. **Existem imagens de máquinas virtuais De Trazer a Sua Própria Licença (BYOL) para o SQL Server?**

   Neste momento, não existem imagens de máquinas virtuais BYOL Linux para o SQL Server. No entanto, pode instalar manualmente o SQL Server num VM apenas para Linux, conforme discutido nas perguntas anteriores.

1. **Posso alterar uma VM para utilizar a minha própria licença do SQL Server caso tenha sido criada com uma das imagens da galeria pay as you go?**

   N.º Não pode mudar de licença pay-per/second para usar a sua própria licença. Tem de criar um novo Linux VM, instalar o SQL Server e migrar os seus dados. Consulte a pergunta anterior para mais detalhes sobre como trazer a sua própria licença.

## <a name="administration"></a>Administração

1. **Posso gerir uma máquina virtual Linux que executa o SQL Server com o SQL Server Management Studio (SSMS)?**

   Sim, mas o SSMS é atualmente uma ferramenta apenas para o Windows. Tem de ligar remotamente a partir de uma máquina Windows para utilizar SSMS com VMs Linux que executam o SQL Server. Localmente em Linux, a nova ferramenta [mssql-conf](/sql/linux/sql-server-linux-configure-mssql-conf) pode executar muitas tarefas administrativas. Para obter uma ferramenta de gestão de bases de dados cross-platform, consulte [o Azure Data Studio](/sql/azure-data-studio/what-is).

1. **Posso remover completamente o SQL Server de uma VM do SQL Server?**

   Sim, mas continuará a ser cobrado para o seu SQL Server VM, conforme descrito na [orientação de preços para VMs SQL Server Azure](../windows/pricing-guidance.md?toc=%2fazure%2fvirtual-machines%2flinux%2fsql%2ftoc.json). Se já não precisar do SQL Server, pode implementar uma máquina virtual nova e migrar os dados e as aplicações para a mesma. Depois, pode remover a máquina virtual do SQL Server.

## <a name="updating-and-patching"></a>Atualização e remendação

1. **Como faço o upgrade para uma nova versão/edição do SQL Server num VM Azure?**

   Atualmente, não existe qualquer atualização no local para o SQL Server em execução numa VM do Azure. Crie uma nova máquina virtual do Azure com a versão/edição pretendida do SQL Server e, em seguida, migre as suas bases de dados para o novo servidor, [através de técnicas comuns de migração de dados](/sql/linux/sql-server-linux-migrate-overview).

## <a name="general"></a>Geral

1. **As soluções de alta disponibilidade do SQL Server são suportadas em VMs Azure?**

   Neste momento, não. Sempre Em grupos de disponibilidade e Failover Clustering ambos requerem uma solução de clustering em Linux, como pacemaker. As distribuições de Linux suportadas para o SQL Server não suportam os seus add-ons de alta disponibilidade na nuvem.

## <a name="resources"></a>Recursos

**Linux VMs:**

* [Visão geral do SQL Server num Linux VM](sql-server-on-linux-vm-what-is-iaas-overview.md)
* [Provisão SQL Server em um Linux VM](sql-vm-create-portal-quickstart.md)
* [SqL Server na documentação do Linux](/sql/linux/sql-server-linux-overview)

**VMs do Windows:**

* [Visão geral do SQL Server num VM do Windows](../windows/sql-server-on-azure-vm-iaas-what-is-overview.md)
* [Provisão SQL Server em um VM Windows](../windows/sql-vm-create-portal-quickstart.md)
* [FAQ (Windows)](../windows/frequently-asked-questions-faq.md)