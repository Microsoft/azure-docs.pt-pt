---
title: Alargar o suporte para o SQL Server 2008 & 2008 R2
description: Alargar o suporte para o SQL Server 2008 e o SQL Server 2008 R2 migrando a sua instância de Servidor SQL para o Azure, ou comprando suporte alargado para manter as instâncias no local.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: craigg
tags: azure-service-management
ms.service: virtual-machines-sql
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 04/08/2019
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: 6c27fa8f80140e6db92ea0f998852507260cc278
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80159545"
---
# <a name="extend-support-for-sql-server-2008-and-sql-server-2008-r2-with-azure"></a>Alargar o suporte para O Servidor SQL 2008 e SQL Server 2008 R2 com Azure

O SQL Server 2008 e o SQL Server 2008 R2 atingiram o [fim do seu ciclo de vida de suporte (EOS).](https://www.microsoft.com/sql-server/sql-server-2008) Como muitos clientes ainda estão a usar ambas as versões, estamos a oferecer várias opções para continuar a receber suporte. Pode migrar as suas instâncias de SQL Server no local para máquinas virtuais Azure (VMs), migrar para a Base de Dados Azure SQL ou permanecer no local e adquirir atualizações de segurança alargadas.

Ao contrário de um caso gerido, migrar para um VM Azure não requer a recertificação das suas aplicações. E ao contrário de ficar no local, receberá patches de segurança alargados gratuitos migrando para um VM Azure.

O resto deste artigo fornece considerações para migrar a sua instância SQL Server para um VM Azure.

Para mais informações sobre o fim das opções [de suporte,](/sql/sql-server/end-of-support/sql-server-end-of-life-overview)consulte Fim do suporte .

## <a name="provisioning"></a>Aprovisionamento

Existe uma imagem **SQL Server 2008 R2** pay-as-you-go no Windows Server 2008 R2 disponível no Azure Marketplace.

Os clientes que estiverem no SQL Server 2008 terão de se autoinstalar ou fazer upgrade para o SQL Server 2008 R2. Da mesma forma, os clientes do Windows Server 2008 terão de implementar o seu VM a partir de um VHD personalizado ou de upgrade para o Windows Server 2008 R2.

As imagens implementadas através do Azure Marketplace vêm com a extensão SQL IaaS pré-instalada. A extensão SQL IaaS é um requisito para licenciamento flexível e patching automatizado. Os clientes que implementarem VMs auto-instalados terão de instalar manualmente a extensão SQL IaaS. A extensão SQL IaaS não é suportada no Windows Server 2008.

> [!NOTE]
> Embora as lâminas SQL Server **Create** and **Manage** funcionem com a imagem SQL Server 2008 R2 no portal Azure, as seguintes funcionalidades não são _suportadas:_ Backups automáticos, integração do Cofre chave Azure, Serviços R e configuração de armazenamento.

## <a name="licensing"></a>Licenciamento
As implementações Pay-as-you-go SQL Server 2008 R2 podem converter-se em [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/).

Para converter uma licença baseada em Software Assurance (SA) para pay-as-you-go, os clientes devem registar-se com o fornecedor de [recursos](virtual-machines-windows-sql-register-with-resource-provider.md)SQL VM . Após esse registo, o tipo de licença SQL será permutável entre o Azure Hybrid Benefit e o pay-as-you-go.

As instâncias SQL Server 2008 ou SQL Server 2008 R2 num VM Azure podem registar-se junto do fornecedor de recursos SQL VM e converter o seu tipo de licença para pay-as-you-go.

## <a name="migration"></a>Migração
Pode migrar as instâncias do EOS SQL Server para um VM Azure com métodos de cópia de segurança/restauro manuais. Este é o método de migração mais comum desde o local até um VM Azure.

### <a name="azure-site-recovery"></a>Azure Site Recovery

Para migrações a granel, recomendamos o serviço de recuperação do [site Azure.](/azure/site-recovery/site-recovery-overview) Com a Recuperação do Site Azure, os clientes podem replicar todo o VM, incluindo o SQL Server de instalações para o Azure VM.

O SQL Server requer instantâneos de recuperação de sites azure consistentes para garantir a recuperação. A Recuperação do Site Azure suporta instantâneos consistentes com aplicações com um intervalo mínimo de 1 hora. O objetivo mínimo de ponto de recuperação (RPO) possível para o Servidor SQL com migrações de recuperação do site Azure é de 1 hora. O objetivo do tempo de recuperação (RTO) é de 2 horas mais o tempo de recuperação do Servidor SQL.

### <a name="database-migration-service"></a>Serviço de Migração de Bases de Dados

O Serviço de [Migração](/azure/dms/dms-overview) de Bases de Dados é uma opção para os clientes se estiverem a migrar do local para um Azure VM, atualizando o SQL Server para a versão de 2012 ou mais tarde.

## <a name="disaster-recovery"></a>Recuperação após desastre

As soluções de recuperação de desastres para o EOS SQL Server num VM Azure são as seguintes:

- **Backups do SQL Server**: Utilize backup azure para ajudar a proteger o seu EOS SQL Server 2008 e 2008 R2 contra ransomware, eliminação acidental e corrupção com RPO de 15 min e recuperação pontual. Para mais detalhes, consulte [este artigo.](https://docs.microsoft.com/azure/backup/backup-azure-sql-database#scenario-support)
- **Envio de registos:** Pode criar uma réplica de transporte de registo sonante noutra zona ou região de Azure com restauros contínuos para reduzir o RTO. Precisa configurar manualmente o envio de diáriode sessão.
- **Recuperação do site Azure:** Pode replicar o seu VM entre zonas e regiões através da replicação de Recuperação do Sítio Azure. O SQL Server requer instantâneos consistentes com aplicações para garantir a recuperação em caso de desastre. A Recuperação do Site Azure oferece um RPO mínimo de 1 hora e um RTO de 2 horas (mais o tempo de recuperação do Servidor SQL) para a recuperação de desastres do Servidor EOS SQL.

## <a name="security-patching"></a>Correção de segurança
As atualizações de segurança estendidas para VMs do Servidor SQL são entregues através dos canais de Atualização da Microsoft depois de o VM do Servidor SQL ter sido registado com o fornecedor de [recursos](virtual-machines-windows-sql-register-with-resource-provider.md)SQL VM . Patches podem ser descarregados manualmente ou automaticamente.

A *Aplicação de patches automatizada* está ativada por predefinição. A Aplicação de patches automatizada permite ao Azure automaticamente aplicar o patch do SQL Server e o sistema operativo. Pode especificar um dia da semana, hora e duração para uma janela de manutenção se a extensão SQL Server IaaS estiver instalada. O Azure executa a aplicação de patches nesta janela de manutenção. A agenda da janela de manutenção utiliza a região da VM para a hora.  Para mais informações, consulte [patching automatizado para SQL Server em Máquinas Virtuais Azure](virtual-machines-windows-sql-automated-patching.md).


## <a name="next-steps"></a>Passos seguintes

Migrar o seu VM do Servidor SQL para O Azure:

* [Migrar uma base de dados do SQL Server para o SQL Server numa VM do Azure](virtual-machines-windows-migrate-sql.md)

Inicie-se com o SQL Server em Máquinas Virtuais Azure:

* [Criar uma VM do SQL Server no portal do Azure](quickstart-sql-vm-create-portal.md)

Obtenha respostas a perguntas comum sobre VMs do Servidor SQL:

* [FAQ para Servidor SQL em Máquinas Virtuais Azure](virtual-machines-windows-sql-server-iaas-faq.md)

Saiba mais sobre o fim das opções de suporte e atualizações de segurança alargadas:

* [Fim do suporte](/sql/sql-server/end-of-support/sql-server-end-of-life-overview) & [Atualizações de Segurança Estendidas](/sql/sql-server/end-of-support/sql-server-extended-security-updates)