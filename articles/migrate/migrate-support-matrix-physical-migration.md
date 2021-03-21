---
title: Suporte para migração de servidores físicos em Azure Migrate
description: Saiba mais sobre o suporte à migração de servidores físicos em Azure Migrate.
author: rahulg1190
ms.author: rahugup
ms.manager: bsiva
ms.topic: conceptual
ms.date: 06/14/2020
ms.openlocfilehash: 4ebc363f29ed8956d10e91f41be1d257cbcc492f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98703891"
---
# <a name="support-matrix-for-physical-server-migration"></a>Matriz de suporte para migração de servidores físicos

Este artigo resume as configurações e limitações de suporte para migrar servidores físicos para Azure com [Azure Migrate: Migração do servidor](migrate-services-overview.md#azure-migrate-server-migration-tool) . Se procura informações sobre a avaliação de servidores físicos para migração para Azure, reveja a matriz de suporte de [avaliação](migrate-support-matrix-physical.md).

## <a name="migrating-machines-as-physical"></a>Máquinas migratórias como físicas

Pode migrar máquinas no local como servidores físicos, utilizando a replicação baseada em agentes. Utilizando esta ferramenta, pode migrar uma vasta gama de máquinas para Azure:

- Servidores físicos no local.
- VMs virtualizados por plataformas como Xen, KVM.
- VMs hiper-V ou VMware se por alguma razão não quiser utilizar os fluxos padrão [de Hiper-V](tutorial-migrate-hyper-v.md) ou [VMware.](server-migrate-overview.md)
- VMs correndo em nuvens privadas.
- VMs correndo em nuvens públicas, incluindo Amazon Web Services (AWS) ou Google Cloud Platform (GCP).


## <a name="migration-limitations"></a>Limitações da migração

Pode selecionar até 10 máquinas ao mesmo tempo para replicação. Se quiser migrar mais máquinas, então replique em grupos de 10.


## <a name="physical-server-requirements"></a>Requisitos do servidor físico

A tabela resume o suporte para servidores físicos que pretende migrar usando migração baseada em agentes.

**Suporte** | **Detalhes**
--- | ---
**Carga de trabalho da máquina** | A Azure Migrate suporta a migração de qualquer carga de trabalho (por exemplo, Ative Directory, servidor SQL, etc.) que funciona numa máquina suportada.
**Sistemas operativos** | Para obter as informações mais recentes, reveja o suporte do [sistema operativo](../site-recovery/vmware-physical-azure-support-matrix.md#replicated-machines) para recuperação do local. A Azure Migrate fornece suporte ao sistema operativo idêntico.
**Sistema de ficheiros Linux/armazenamento de hóspedes** | Para obter as informações mais recentes, reveja o suporte do [sistema de ficheiros Linux](../site-recovery/vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage) para recuperação do site. A Azure Migrate fornece suporte idêntico ao sistema de ficheiros Linux.
**Rede/Armazenamento** | Para obter as informações mais recentes, reveja os pré-requisitos de [rede](../site-recovery/vmware-physical-azure-support-matrix.md#network) e [armazenamento](../site-recovery/vmware-physical-azure-support-matrix.md#storage) para a Recuperação do Local. A Azure Migrate fornece requisitos idênticos de rede/armazenamento.
**Requisitos do Azure** | Para obter as informações mais recentes, reveja a [rede Azure,](../site-recovery/vmware-physical-azure-support-matrix.md#azure-vm-network-after-failover) [armazenamento](../site-recovery/vmware-physical-azure-support-matrix.md#azure-storage)e requisitos [de cálculo](../site-recovery/vmware-physical-azure-support-matrix.md#azure-compute) para recuperação de locais. Azure Migrate tem requisitos idênticos para a migração de servidores físicos.
**Serviço de Mobilidade** | O agente de serviço de mobilidade deve ser instalado em cada máquina que pretende migrar.
**Bota UEFI** | Suportado. As máquinas baseadas na UEFI serão migradas para a geração Azure 2 VMs.  <br/><br/> O disco de so deve ter até quatro divisórias, e os volumes devem ser formatados com NTFS.
**UEFI - Arranque seguro**         | Não apoiado para a migração.
**Disco-alvo** | As máquinas só podem ser migradas para discos geridos (HDD padrão, SSD padrão, SSD premium) em Azure.
**Tamanho do disco** | 2 Disco de SO TB; 32 TB para discos de dados.
**Limites de disco** |  Até 63 discos por máquina.
**Discos/volumes encriptados** |  As máquinas com discos/volumes encriptados não são suportadas para migração.
**Aglomerado de disco compartilhado** | Não suportado.
**Discos independentes** | Suportado.
**Discos passthrough** | Suportado.
**NFS** | Os volumes NFS montados à medida que os volumes das máquinas não serão replicados.
**metas iSCSI** | As máquinas com alvos iSCSI não são suportadas para migração sem agentes.
**IO multipata** | Não suportado.
**NICs em equipa** | Não suportado.
**IPv6** | Não suportado.



## <a name="replication-appliance-requirements"></a>Requisitos de aplicação de replicação

Se configurar manualmente o aparelho de replicação num servidor físico, certifique-se de que está em conformidade com os requisitos resumidos na tabela. Quando configurar o aparelho de replicação Azure Migrate como VMware VM utilizando o modelo OVA fornecido no hub Azure Migrate, o aparelho é configurado com o Windows Server 2016 e cumpre os requisitos de suporte. 

- Saiba mais sobre [os requisitos do aparelho de replicação](migrate-replication-appliance.md#appliance-requirements).
- O MySQL deve ser instalado no aparelho. Saiba mais sobre [as opções de instalação.](migrate-replication-appliance.md#mysql-installation)
- Saiba mais [sobre URLs](migrate-replication-appliance.md#url-access) que o aparelho de replicação precisa de aceder.

## <a name="azure-vm-requirements"></a>Requisitos da VM do Azure

Todos os VMs no local replicados ao Azure devem satisfazer os requisitos Azure VM resumidos nesta tabela. Quando a Recuperação do Site executa uma verificação prévia para a replicação, a verificação falhará se alguns dos requisitos não forem cumpridos.

**Componente** | **Requisitos** | **Detalhes**
--- | --- | ---
Sistema operativo convidado | Verifica os sistemas operativos suportados.<br/> Pode migrar qualquer carga de trabalho em execução num sistema operativo suportado. | A verificação falha se não for apoiada.
Arquitetura do sistema operativo convidado | 64-bit. | A verificação falha se não for apoiada.
Tamanho do disco do sistema operativo | Até 2.048 GB. | A verificação falha se não for apoiada.
Contagem de discos do sistema operativo | 1 | A verificação falha se não for apoiada.
Contagem de discos de dados | 64 ou menos. | A verificação falha se não for apoiada.
Tamanho do disco de dados | Até 32 TB | A verificação falha se não for apoiada.
Placas de rede | Vários adaptadores são suportados. |
VHD partilhado | Não suportado. | A verificação falha se não for apoiada.
Disco FC | Não suportado. | A verificação falha se não for apoiada.
BitLocker | Não suportado. | O BitLocker tem de ser desativado antes de ativar a replicação de uma máquina.
o nome da VM | De 1 a 63 caracteres.<br/> Limitado a letras, números e hífenes.<br/><br/> O nome da máquina deve começar e terminar com uma letra ou número. |  Atualize o valor nas propriedades da máquina na Recuperação do Local.
Conecte-se após a migração-Windows | Para ligar aos VMs Azure que executam o Windows após a migração:<br/> - Antes da migração permite pDR nas VM no local. Confirme que são adicionadas regras de TCP e UDP ao perfil **Público** e que o protocolo RDP é permitido em **Firewall do Windows** > **Aplicações Permitidas** para todos os perfis.<br/> Para acesso local-a-site VPN, ative o RDP e permita o RDP em  ->  **aplicações e funcionalidades permitidas** para o Windows Firewall e para redes **de domínio e privado.** Além disso, verifique se a política SAN do sistema operativo está definida para **OnlineAll**. [Saiba mais](prepare-for-migration.md). |
Conecte-se após migração-Linux | Para ligar aos VMs Azure após a migração utilizando SSH:<br/> Antes da migração, na máquina no local, verifique se o serviço Secure Shell está definido para iniciar e que as regras de firewall permitem uma ligação SSH.<br/> Após o failover, no Azure VM, permita a entrada de ligações à porta SSH para as regras do grupo de segurança da rede sobre o falhado sobre vM, e para a sub-rede Azure à qual está ligada. Além disso, adicione um endereço IP público para o VM. |  


## <a name="next-steps"></a>Passos seguintes

[Migrar](tutorial-migrate-physical-virtual-machines.md) servidores físicos.
