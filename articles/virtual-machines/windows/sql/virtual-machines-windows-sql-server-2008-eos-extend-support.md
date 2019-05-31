---
title: Expandir o suporte para o SQL Server 2008 e SQL Server 2008 R2 com o Azure
description: Saiba como estender o suporte para SQL Server 2008 e SQL Server 2008 R2 por migrar a sua instância do SQL Server para o Azure ou comprar o suporte estendido para manter as instâncias no local.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: craigg
tags: azure-service-management
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 04/08/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 62261e46dc4744597acd10c32f0a835f4a597d4d
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2019
ms.locfileid: "66243963"
---
# <a name="extend-support-for-sql-server-2008-and-sql-server-2008-r2-with-azure"></a>Expandir o suporte para o SQL Server 2008 e SQL Server 2008 R2 com o Azure

SQL Server 2008 e SQL Server 2008 R2 ambos a aproximar-na [final do respetivo ciclo de vida de suporte (término do suporte ao)](https://www.microsoft.com/sql-server/sql-server-2008). Uma vez que muitos dos nossos clientes ainda estiver a utilizar as duas versões, estamos fornecendo várias opções para continuar a obter suporte. Pode migrar as instâncias do SQL Server no local para máquinas virtuais do Azure (VMs), migrar para a base de dados SQL do Azure, ou mantenha-se no local e comprar as atualizações de segurança expandida.

Ao contrário com uma instância gerida, migrar para uma VM do Azure não requer recertifying seus aplicativos. E ao contrário com mantendo-se no local, irá receber patches de segurança expandida gratuita ao migrar para uma VM do Azure. 

O restante deste artigo fornece considerações para migrar a sua instância do SQL Server para uma VM do Azure. 

## <a name="provisioning"></a>Aprovisionamento 

Existe uma pay as you go `SQL Server 2008 R2 on Windows Server 2008 R2` imagem disponível no Azure marketplace. 

Os clientes que estejam no SQL Server 2008 terão para Self-instalar ou atualizar para o SQL Server 2008 R2. Da mesma forma, os clientes no Windows Server 2008 terão implementar a sua VM a partir de um VHD personalizado ou atualizar para o Windows Server 2008 R2. 

Imagens implementadas através do Marketplace são fornecidos com a extensão SQL IaaS pré-instalado. A extensão SQL IaaS é um requisito para licenciamento flexível e aplicação de patches automatizada. Os clientes que implementam VMs Self-instaladas tem de instalar manualmente a extensão SQL IaaS. A extensão SQL IaaS não é suportada no Windows 2008. 

  > [!NOTE]
  > Enquanto o servidor de SQL `Create` e `Manage` painéis irão funcionar com a imagem do SQL Server 2008 R2 no portal do Azure, as seguintes funcionalidades estão _nepodporuje_: Cópias de segurança automáticas, integração do Azure Key Vault, R Services e configuração de armazenamento.

## <a name="licensing"></a>Licenciamento
Implementações do pay as you go do SQL Server 2008 R2 podem converter [Azure híbrido benefício (AHB)](https://azure.microsoft.com/pricing/hybrid-benefit/).

Para converter uma licença do Software Assurance (SA) com base em pay as you go, os clientes se deverá registar com a VM de SQL [fornecedor de recursos](virtual-machines-windows-sql-ahb.md#register-sql-server-vm-with-sql-resource-provider). Depois de registado com o fornecedor de recursos de VM do SQL Server, o tipo de licença do SQL será intercambiável entre AHB e pay as you go. 

Instâncias de SQL Server 2008 ou o SQL Server 2008 R2 Self-instaladas na VM do Azure podem registar com o fornecedor de recursos do SQL e converter o tipo de licença para pay as you go.

## <a name="migration"></a>Migração
Pode migrar instâncias do término do suporte ao SQL Server para uma VM do Azure com métodos de cópia de segurança/restauro manual; Este é o método mais comuns de migração no local para uma VM do Azure.

### <a name="azure-site-recovery"></a>Azure Site Recovery

Para migrações em massa, recomendamos [do Azure Site Recovery](/azure/site-recovery/site-recovery-overview) serviço. Com o Azure Site Recovery, os clientes podem replicar a VM inteira, incluindo o SQL Server no local para uma VM do Azure.

SQL Server requer instantâneos consistentes com aplicações do Azure Site Recovery para garantir a recuperação; e o Azure Site Recovery suporta instantâneos consistentes com a aplicação com o intervalo mínimo de 1 hora. O RPO mínimo possível para o SQL Server com as migrações do Azure Site Recovery é de 1 hora e o RTO é de 2 horas mais tempo de recuperação do SQL Server.

### <a name="database-migration-service"></a>Serviço de Migração de Bases de Dados

O [Database Migration Service](/azure/dms/dms-overview) é uma opção para os clientes se migrar do local para a VM do Azure com a atualização do SQL Server para SQL Server 2012 e superior.

## <a name="disaster-recovery"></a>Recuperação após desastre

Soluções de recuperação após desastre para o término do suporte ao SQL Server numa VM do Azure são os seguintes:

- **Cópias de segurança do SQL Server**: Cópias de segurança do SQL Server podem ser utilizadas para recuperar o SQL Server em caso de regionais ou falhas de zona. Uma vez que a funcionalidade de cópia de segurança gerida não é suportada para o término do suporte ao SQL Server, os clientes terão de fazer cópias de segurança manualmente.
- **Envio de log**: Pode criar uma réplica de envio de log em outra zona ou a região do Azure com contínuos restauros para reduzir o RTO. Os clientes terão de configurar manualmente o envio de log.
- **Azure Site Recovery**: Pode replicar a VM entre zonas e as regiões através da replicação do Azure Site Recovery. SQL Server necessita de instantâneos consistentes com a aplicação garantir a recuperação em caso de desastre. O Azure Site Recovery oferece RPO mínimo de 1 hora e de 2 horas + tempo de recuperação do SQL Server RTO de término do suporte ao SQL Server DR.

## <a name="security-patching"></a>Aplicação de patches de segurança
Atualizações de segurança expandida para VMs do SQL Server serão entregue através dos canais do Microsoft Update, assim que a VM do SQL Server foi registado com o SQL [fornecedor de recursos](virtual-machines-windows-sql-ahb.md#register-sql-server-vm-with-sql-resource-provider). Também podem ser transferidos patches manual ou automaticamente. 

A **Aplicação de patches automatizada** está ativada por predefinição. A Aplicação de patches automatizada permite ao Azure automaticamente aplicar o patch do SQL Server e o sistema operativo. Se a extensão de IaaS do SQL está instalada, pode especificar um dia da semana, hora e a duração de uma janela de manutenção. O Azure executa a aplicação de patches nesta janela de manutenção. A agenda da janela de manutenção utiliza a região da VM para a hora.  Para obter mais informações, consulte o artigo [Aplicação de Patches Automatizada para o SQL Server nas Virtual Machines do Azure](virtual-machines-windows-sql-automated-patching.md).


## <a name="next-steps"></a>Passos Seguintes

Migrar a sua VM do SQL Server para o Azure

* [Migrar uma base de dados do SQL Server para o SQL Server numa VM do Azure](virtual-machines-windows-migrate-sql.md)

Introdução ao SQL Server em máquinas virtuais do Azure:

* [Criar uma VM do SQL Server no portal do Azure](quickstart-sql-vm-create-portal.md)

Obtenha respostas às perguntas mais comuns sobre as VMs do SQL:

* [FAQ do SQL Server nas Máquinas Virtuais do Azure](virtual-machines-windows-sql-server-iaas-faq.md)
