---
title: Alargar o suporte para o SQL Server 2008 & 2008 R2
description: Estenda o suporte para o SQL Server 2008 e SQL Server 2008 R2 migrando a sua instância sql Server para Azure, ou comprando suporte alargado para manter as instâncias no local.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
tags: azure-service-management
ms.service: virtual-machines-sql
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 04/08/2019
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: 48288ed3765fa939fc56a4469f64070315c4c6aa
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "84668751"
---
# <a name="extend-support-for-sql-server-2008-and-sql-server-2008-r2-with-azure"></a>Estender o suporte para SQL Server 2008 e SQL Server 2008 R2 com Azure
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

O SQL Server 2008 e o SQL Server 2008 R2 atingiram o [fim do seu ciclo de vida de suporte (EOS).](https://www.microsoft.com/sql-server/sql-server-2008) Como muitos clientes ainda estão a usar ambas as versões, estamos a oferecer várias opções para continuar a receber suporte. Pode migrar as suas instâncias de servidor SQL para máquinas virtuais Azure (VMs), migrar para a Base de Dados Azure SQL ou permanecer no local e comprar atualizações de segurança alargadas.

Ao contrário de um caso gerido, migrar para um VM Azure não requer recertificar as suas aplicações. E ao contrário de ficar no local, você receberá patches de segurança estendidos gratuitos migrando para um Azure VM.

O resto deste artigo fornece considerações para migrar a sua instância SQL Server para um Azure VM.

Para obter mais informações sobre o fim das opções de suporte, consulte [Fim do suporte](/sql/sql-server/end-of-support/sql-server-end-of-life-overview).

## <a name="provisioning"></a>Aprovisionamento

Existe um PAY-as-you-go **SQL Server 2008 R2 na imagem R2 do Windows Server 2008** disponível no Azure Marketplace.

Os clientes que estiverem no SQL Server 2008 terão de se instalar ou atualizar para o SQL Server 2008 R2. Da mesma forma, os clientes no Windows Server 2008 terão de implementar o seu VM a partir de um VHD personalizado ou de atualizar para o Windows Server 2008 R2.

As imagens implementadas através do Azure Marketplace vêm com a extensão SQL IaaS pré-instalada. A extensão SQL IaaS é um requisito para licenciamento flexível e remendação automatizada. Os clientes que implementarem VMs auto-instalados terão de instalar manualmente a extensão SQL IaaS. A extensão SQL IaaS não é suportada no Windows Server 2008.

> [!NOTE]
> Embora as lâminas SQL **Server Create** and **Manage** funcionem com a imagem SQL Server 2008 R2 no portal Azure, as seguintes funcionalidades não são _suportadas_: Backups automáticos, integração do Cofre de Chaves Azure, Serviços R e configuração de armazenamento.

## <a name="licensing"></a>Licenciamento
As implementações do Pay-as-you-go SQL Server 2008 R2 podem converter-se em [Benefício Híbrido Azure](https://azure.microsoft.com/pricing/hybrid-benefit/).

Para converter uma licença baseada em Software Assurance (SA) para pagar o que quiser, os clientes devem registar-se junto do fornecedor de [recursos](sql-vm-resource-provider-register.md)SQL VM . Após esse registo, o tipo de licença SQL será permutável entre a Azure Hybrid Benefit e o pay-as-you-go.

As instâncias AUTO-instaladas do SQL Server 2008 ou SQL Server 2008 R2 num VM Azure podem registar-se com o fornecedor de recursos SQL VM e converter o seu tipo de licença para pagamento-como-você-go.

## <a name="migration"></a>Migração
Pode migrar as instâncias do Servidor EOS SQL para um Azure VM com métodos de backup/restauro manual. Este é o método de migração mais comum desde as instalações até um VM Azure.

### <a name="azure-site-recovery"></a>Azure Site Recovery

Para migrações a granel, recomendamos o serviço [de Recuperação do Local Azure.](/azure/site-recovery/site-recovery-overview) Com a Recuperação do Site Azure, os clientes podem replicar todo o VM, incluindo o SQL Server de instalações para Azure VM.

O SQL Server requer instantâneos de recuperação do site Azure consistentes para garantir a recuperação. A Azure Site Recovery suporta instantâneos consistentes com um intervalo mínimo de 1 hora. O objetivo mínimo de ponto de recuperação (RPO) possível para o SQL Server com migrações de recuperação do local de Azure é de 1 hora. O objetivo de tempo de recuperação (RTO) é de 2 horas mais o tempo de recuperação do SQL Server.

### <a name="database-migration-service"></a>Database Migration Service

O [Azure Database Migration Service](/azure/dms/dms-overview) é uma opção para os clientes se estiverem a migrar de instalações para um Azure VM, atualizando o SQL Server para a versão de 2012 ou mais tarde.

## <a name="disaster-recovery"></a>Recuperação após desastre

As soluções de recuperação de desastres para o Servidor SQL do EOS num VM Azure são as seguintes:

- **Backups do SQL Server**: Utilize backup do Azure para ajudar a proteger o seu EOS SQL Server 2008 e 2008 R2 contra ransomware, eliminação acidental e corrupção com RPO de 15 minutos e recuperação pontual. Para mais detalhes, consulte [este artigo.](https://docs.microsoft.com/azure/backup/sql-support-matrix#scenario-support)
- **Envio de registo**: Pode criar uma réplica de envio de registos noutra zona ou região de Azure com restauros contínuos para reduzir o RTO. Você precisa configurar manualmente o envio de registos.
- **Recuperação do sítio azul**: Pode replicar o seu VM entre zonas e regiões através da replicação da Recuperação do Local de Azure. O SQL Server requer instantâneos consistentes com aplicações para garantir a recuperação em caso de desastre. A Azure Site Recovery oferece um RPO mínimo de 1 hora e um RTO de 2 horas (mais tempo de recuperação do SQL Server) para a recuperação de desastres do EOS SQL Server.

## <a name="security-patching"></a>Patching de segurança
As atualizações de segurança alargadas para VMs do Servidor SQL são entregues através dos canais Microsoft Update depois de o SQL Server VM ter sido registado com o [fornecedor de recursos](sql-vm-resource-provider-register.md)SQL VM . Os patches podem ser descarregados manualmente ou automaticamente.

A *Aplicação de patches automatizada* está ativada por predefinição. A Aplicação de patches automatizada permite ao Azure automaticamente aplicar o patch do SQL Server e o sistema operativo. Pode especificar um dia da semana, hora e duração para uma janela de manutenção se a extensão SQL Server IaaS for instalada. O Azure executa a aplicação de patches nesta janela de manutenção. A agenda da janela de manutenção utiliza a região da VM para a hora. Para obter mais informações, consulte [patching automatizado para O Servidor SQL em Máquinas Virtuais Azure](automated-patching.md).


## <a name="next-steps"></a>Passos seguintes

Migrar o seu SQL Server VM para Azure:

* [Migrar uma base de dados do SQL Server para o SQL Server numa VM do Azure](migrate-to-vm-from-sql-server.md)

Começa com o SQL Server em Azure Virtual Machines:

* [Criar uma VM do SQL Server no portal do Azure](sql-vm-create-portal-quickstart.md)

Obtenha respostas para perguntas comumente feitas sobre VMs do SERVIDOR SQL:

* [FAQ para SQL Server em Máquinas Virtuais Azure](frequently-asked-questions-faq.md)

Saiba mais sobre o fim das opções de suporte e atualizações de segurança alargadas:

* [Fim do apoio](/sql/sql-server/end-of-support/sql-server-end-of-life-overview)  &  [Atualizações de segurança alargadas](/sql/sql-server/end-of-support/sql-server-extended-security-updates)