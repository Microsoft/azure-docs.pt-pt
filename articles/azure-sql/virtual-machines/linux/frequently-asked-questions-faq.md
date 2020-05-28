---
title: Servidor SQL em Máquinas Virtuais Linux Azure FAQ [ Máquinas Virtuais De Linux Azure] Microsoft Docs
description: Este artigo fornece respostas a perguntas frequentes sobre executar o SQL Server em VMs Linux Azure.
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
ms.openlocfilehash: f5447e61936b55db353404aae7cb391377d78294
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84048008"
---
# <a name="frequently-asked-questions-for-sql-server-on-linux-azure-virtual-machines"></a>Perguntas frequentes para O Servidor SQL em Máquinas Virtuais Linux Azure
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

> [!div class="op_single_selector"]
> * [Windows](../windows/frequently-asked-questions-faq.md)
> * [Linux](frequently-asked-questions-faq.md)

Este artigo fornece respostas a algumas das perguntas mais comuns sobre executar [o SQL Server em Máquinas Virtuais Linux Azure](sql-server-on-linux-vm-what-is-iaas-overview.md).

[!INCLUDE [support-disclaimer](../../../../includes/support-disclaimer.md)]

## <a name="images"></a><a id="images"></a>Imagens

1. **Que imagens de galeria de máquinas virtuais SQL Server estão disponíveis?**

   O Azure mantém imagens de máquinas virtuais para todas as grandes lançamentos suportadas do SQL Server em todas as edições tanto para linux como Windows. Para mais detalhes, consulte a lista completa de [imagens VM Linux](sql-server-on-linux-vm-what-is-iaas-overview.md#create) e [imagens VM do Windows](../windows/sql-server-on-azure-vm-iaas-what-is-overview.md#payasyougo).

1. **As imagens de galeria de máquinas virtuais sQL Server existentes são atualizadas?**

   De dois em dois meses, as imagens do SQL Server na galeria de máquinas virtuais são atualizadas com as mais recentes atualizações do Linux e windows. Para as imagens do Linux, isto inclui as mais recentes atualizações do sistema. Para imagens do Windows, isto inclui quaisquer atualizações que sejam marcadas como importantes no Windows Update, incluindo importantes atualizações de segurança do SQL Server e pacotes de serviços. As atualizações cumulativas do SQL Server são tratadas de forma diferente para o Linux e o Windows. Para o Linux, as atualizações cumulativas do SQL Server também estão incluídas na atualização. Mas, neste momento, os VMs do Windows não são atualizados com as atualizações cumulativas do SQL Server ou do Windows Server.

1. **Que pacotes relacionados do SQL Server também estão instalados?**

   Para ver os pacotes SQL Server que são instalados por padrão nos VMs SQL Server Linux, consulte [os pacotes instalados](sql-server-on-linux-vm-what-is-iaas-overview.md#packages).

1. **As imagens de máquinas virtuais do SQL Server podem ser removidas da galeria?**

   Sim. O Azure apenas mantém uma imagem por versão e edição principais. Por exemplo, quando um novo pacote de serviços SQL Server é lançado, o Azure adiciona uma nova imagem à galeria para esse pacote de serviços. A imagem do SQL Server para o pacote de serviço anterior é imediatamente removida do portal Azure. No entanto, ainda está disponível para o fornecimento da PowerShell para os próximos três meses. Após três meses, a imagem anterior do pacote de serviço já não está disponível. Esta política de remoção também se aplicaria se uma versão SQL Server não for suportada quando chegar ao fim do seu ciclo de vida.

## <a name="creation"></a>Criação

1. **Como posso criar uma máquina virtual Linux Azure com o SQL Server?**

   A solução mais fácil é criar uma Máquina Virtual Linux que inclua o Servidor SQL. Para um tutorial sobre a inscrição no Azure e a criação de um VM SQL a partir do portal, consulte [a Provision a Linux SQL Server virtual machine no portal Azure](sql-vm-create-portal-quickstart.md). Também tem a opção de instalar manualmente o SQL Server num VM com uma edição livremente licenciada (Developer ou Express) ou reutilizando uma licença no local. Se trouxer a sua própria licença, tem de ter [licença de mobilidade através](https://azure.microsoft.com/pricing/license-mobility)da Garantia de Software no Azure .

1. **Por que não posso fornecer um VM de servidor RHEL ou SLES SQL com uma subscrição Azure que tem um limite de gastos?**

   As máquinas virtuais RHEL e SLES requerem uma subscrição sem limite de gastos e um método de pagamento verificado (geralmente um cartão de crédito) associado à subscrição. Se fornecer um VM RHEL ou SLES sem remover o limite de gastos, a sua subscrição ficará desativada e todos os VMs/serviços parados. Se você correr para este estado, para reativar a subscrição [remover o limite de gastos](https://account.windowsazure.com/subscriptions). Os seus créditos restantes serão restaurados para o ciclo de faturação atual, mas uma sobretaxa de imagem RHEL ou SLES VM irá contra o seu cartão de crédito se optar por reiniciá-lo e continuar a executá-lo.

## <a name="licensing"></a>Licenciamento

1. **Como posso instalar a minha cópia licenciada do SQL Server numa VM do Azure?**

   Primeiro, crie uma máquina virtual só para os Sistemas Linux. Em seguida, executar os passos de [instalação do SQL Server](https://docs.microsoft.com/sql/linux/sql-server-linux-setup#platforms) para a sua distribuição Linux. A menos que esteja a instalar uma das edições livremente licenciadas do SQL Server, também deve ter uma licença SQL Server e licença de mobilidade através da Garantia de [Software no Azure.](https://azure.microsoft.com/pricing/license-mobility/)

1. **Existem imagens de máquina virtual De Porta-Sua-Própria (BYOL) para o Servidor SQL?**

   Neste momento, não existem imagens de máquinavirtual BYOL Linux para o SQL Server. No entanto, pode instalar manualmente o SQL Server num VM apenas para Linux, conforme discutido nas perguntas anteriores.

1. **Posso alterar uma VM para utilizar a minha própria licença do SQL Server caso tenha sido criada com uma das imagens da galeria pay as you go?**

   Não. Não pode passar do licenciamento pay-per-segundo para o uso da sua própria licença. Tem de criar um novo Linux VM, instalar o SQL Server e migrar os seus dados. Consulte a pergunta anterior para mais detalhes sobre como trazer a sua própria licença.

## <a name="administration"></a>Administração

1. **Posso gerir uma máquina virtual linux SQL Server com o SQL Server Management Studio (SSMS)?**

   Sim, mas o SSMS é atualmente uma ferramenta exclusivamente para windows. Tem de se ligar remotamente a partir de uma máquina do Windows para utilizar SSMS com VMs do Servidor Linux SQL. Localmente em Linux, a nova ferramenta [mssql-conf](https://docs.microsoft.com/sql/linux/sql-server-linux-configure-mssql-conf) pode executar muitas tarefas administrativas. Para uma ferramenta de gestão de bases de dados de plataformas cruzadas, consulte [o Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/what-is).

1. **Posso remover completamente o SQL Server de uma VM do SQL?**

   Sim, mas continuará a ser cobrado pelo seu VM SQL, conforme descrito na orientação de [preços para VMs SQL Server Azure](../windows/pricing-guidance.md?toc=%2fazure%2fvirtual-machines%2flinux%2fsql%2ftoc.json). Se já não precisar do SQL Server, pode implementar uma máquina virtual nova e migrar os dados e as aplicações para a mesma. Depois, pode remover a máquina virtual do SQL Server.

## <a name="updating-and-patching"></a>Atualização e Remendo

1. **Como faço upgrade para uma nova versão/edição do SQL Server num VM Azure?**

   Atualmente, não existe qualquer atualização no local para o SQL Server em execução numa VM do Azure. Crie uma nova máquina virtual do Azure com a versão/edição pretendida do SQL Server e, em seguida, migre as suas bases de dados para o novo servidor, [através de técnicas comuns de migração de dados](https://docs.microsoft.com/sql/linux/sql-server-linux-migrate-overview).

## <a name="general"></a>Geral

1. **As soluções de alta disponibilidade do SQL Server suportam-se em VMs Azure?**

   Neste momento, não. Sempre em Grupos de Disponibilidade e Agrupamento failover ambos requerem uma solução de clustering em Linux, como o Pacemaker. As distribuições linux suportadas para o SQL Server não suportam os seus add-ons de alta disponibilidade na Cloud.

## <a name="resources"></a>Recursos

**VMs Linux:**

* [Visão geral do Servidor SQL em um VM Linux](sql-server-on-linux-vm-what-is-iaas-overview.md)
* [Provision a SQL Server Linux VM](sql-vm-create-portal-quickstart.md)
* [Servidor SQL na documentação linux](https://docs.microsoft.com/sql/linux/sql-server-linux-overview)

**VMs do Windows:**

* [Visão geral do Servidor SQL num VM do Windows](../windows/sql-server-on-azure-vm-iaas-what-is-overview.md)
* [Provision a SQL Server Windows VM](../windows/sql-vm-create-portal-quickstart.md)
* [FAQ (Janelas)](../windows/frequently-asked-questions-faq.md)