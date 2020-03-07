---
title: Suporte para migração de servidores físicos em Azure Migrate
description: Saiba mais sobre o suporte à migração de servidores físicos em Azure Migrate.
ms.topic: conceptual
ms.date: 01/07/2020
ms.openlocfilehash: e55cf6dddbc8dafd33b444e4a0dbe378d807aea1
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78388998"
---
# <a name="support-matrix-for-physical-server-migration"></a>Matriz de suporte para migração de servidores físicos

Este artigo resume as definições de suporte e limitações para os servidores físicos migratórios com [o Azure Migrate: Server Migration](migrate-services-overview.md#azure-migrate-server-migration-tool) . Se procura informações sobre a avaliação de servidores físicos para migração para O Azure, reveja a matriz de suporte de [avaliação](migrate-support-matrix-physical.md).


## <a name="overview"></a>Descrição geral

Pode migrar as máquinas no local como servidores físicos, utilizando replicação baseada em agentes. Utilizando esta ferramenta, pode migrar uma vasta gama de máquinas para O Azul:

- Servidores físicos no local.
- VMs virtualizados por plataformas como Xen, KVM.
- VMs hiper-V ou VMware se por alguma razão não quiser utilizar os fluxos padrão de [Hiper-V](tutorial-migrate-hyper-v.md) ou [VMware.](server-migrate-overview.md)
- VMs correndo em nuvens privadas.
- VMs correndo em nuvens públicas como Amazon Web Services (AWS) ou Google Cloud Platform (GCP).


## <a name="migration-limitations"></a>Limitações de migração

Pode selecionar até 10 máquinas ao mesmo tempo para replicação. Se quiser migrar mais máquinas, reproduza-se em grupos de 10.


## <a name="physical-server-requirements"></a>Requisitos físicos do servidor

A tabela resume o suporte aos servidores físicos que pretende migrar utilizando a migração baseada em agentes.

**Suporte** | **Detalhes**
--- | ---
**Carga de trabalho da máquina** | A Azure Migrate suporta a migração de qualquer carga de trabalho (por exemplo, Ative Directory, servidor SQL, etc.) em funcionamento numa máquina suportada.
**Sistemas operativos** | Para obter as informações mais recentes, reveja o suporte do [sistema operativo](../site-recovery/vmware-physical-azure-support-matrix.md#replicated-machines) para a Recuperação do Site. A Azure Migrate fornece suporte idêntico ao sistema operativo.
**Sistema de ficheiros Linux/armazenamento de hóspedes** | Para obter as informações mais recentes, reveja o suporte do sistema de [ficheiros Linux](../site-recovery/vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage) para a Recuperação do Site. A Azure Migrate fornece suporte idêntico ao sistema de ficheiros Linux.
**Rede/Armazenamento** | Para obter as informações mais recentes, reveja os pré-requisitos de [rede](../site-recovery/vmware-physical-azure-support-matrix.md#network) e [armazenamento](../site-recovery/vmware-physical-azure-support-matrix.md#storage) para a Recuperação do Site. A Azure Migrate fornece requisitos idênticos de rede/armazenamento.
**Requisitos do Azure** | Para obter as mais recentes informações, reveja os requisitos da [rede Azure,](../site-recovery/vmware-physical-azure-support-matrix.md#azure-vm-network-after-failover) [armazenamento](../site-recovery/vmware-physical-azure-support-matrix.md#azure-storage)e [cálculo](../site-recovery/vmware-physical-azure-support-matrix.md#azure-compute) para a Recuperação do Site. A Azure Migrate tem requisitos idênticos para a migração do servidor físico.
**Serviço de mobilidade** | O agente de serviço de Mobilidade deve ser instalado em cada máquina que pretende migrar.
**Bota UEFI** | A máquina migrada em Azure será automaticamente convertida para uma bota BIOS Azure VM.<br/><br/> O disco osso deve ter até quatro divisórias, e os volumes devem ser formatados com NTFS.
**Disco-alvo** | As máquinas só podem ser migradas para discos geridos (HDD padrão, SSD premium) em Azure.
**Tamanho do disco** | Disco de 2 TB OS; 8 TB para discos de dados.
**Limites de disco** |  Até 63 discos por máquina.
**Discos/volumes encriptados** |  As máquinas com discos/volumes encriptados não são suportadas para migração.
**Cluster de disco partilhado** | Não suportado.
**Discos independentes** | Suportado.
**Discos de passagem** | Suportado.
**NFS** | Os volumes nFS montados à medida que os volumes das máquinas não serão replicados.
**metas iSCSI** | Máquinas com alvos iSCSI não são suportadas para migração sem agente.
**IO multipata** | Não suportado.
**Armazenamento vMotion** | Suportadas
**NICs em equipa** | Não suportado.
**IPv6** | Não suportado.



## <a name="replication-appliance-requirements"></a>Requisitos de aplicação de replicação

Se configurar manualmente o aparelho de replicação num servidor físico, certifique-se de que está em conformidade com os requisitos resumidos na tabela. Quando configura o aparelho de replicação Azure Migrate como VMware VMm utilizando o modelo OVA fornecido no hub De migração Azure, o aparelho é configurado com o Windows Server 2016, e cumpre os requisitos de suporte. 

- Conheça os [requisitos do aparelho](migrate-replication-appliance.md#appliance-requirements)de replicação .
- O MySQL deve ser instalado no aparelho. Saiba mais sobre as opções de [instalação.](migrate-replication-appliance.md#mysql-installation)
- Saiba mais sobre [os URLs](migrate-replication-appliance.md#url-access) que o aparelho de replicação precisa de aceder.

## <a name="azure-vm-requirements"></a>Requisitos de VM Azure

Todos os VMs no local replicados ao Azure devem satisfazer os requisitos de VM Azure resumidos nesta tabela. Quando a Recuperação do Site executa um pré-requisito para verificar a replicação, a verificação falhará se alguns dos requisitos não forem cumpridos.

**Componente** | **Requisitos** | **Detalhes**
--- | --- | ---
Sistema operativo convidado | Verifica sistemas operativos suportados.<br/> Pode migrar qualquer carga de trabalho em funcionamento num sistema operativo suportado. | A verificação falha se não for suportada.
Arquitetura do sistema operativo convidado | 64-bit. | A verificação falha se não for suportada.
Tamanho do disco do sistema operativo | Até 2.048 GB. | A verificação falha se não for suportada.
Contagem de discos do sistema operativo | 1 | A verificação falha se não for suportada.
Contagem de discos de dados | 64 ou menos. | A verificação falha se não for suportada.
Tamanho do disco de dados | Até 4.095 GB | A verificação falha se não for suportada.
Adaptadores de rede | São suportados múltiplos adaptadores. |
VHD Partilhado | Não suportado. | A verificação falha se não for suportada.
Disco fc | Não suportado. | A verificação falha se não for suportada.
BitLocker | Não suportado. | O BitLocker deve ser desativado antes de ativar a replicação de uma máquina.
o nome da VM | De 1 a 63 caracteres.<br/> Limitado a letras, números e hífenes.<br/><br/> O nome da máquina deve começar e terminar com uma letra ou número. |  Atualize o valor nas propriedades da máquina na Recuperação do Site.
Conecte-se após a migração-Windows | Para ligar aos VMs Azure que executam o Windows após a migração:<br/> - Antes que a migração permita o PDR no VM no local. Confirme que são adicionadas regras de TCP e UDP ao perfil **Público** e que o protocolo RDP é permitido em **Firewall do Windows** > **Aplicações Permitidas** para todos os perfis.<br/> Para acesso VPN site-to-site, ative rdP e permita RDP no **Windows Firewall** -> **permitidas aplicações e funcionalidades** para redes **De domínio e privadas.** Além disso, verifique se a política SAN do sistema operativo está definida para **OnlineAll**. [Saiba mais](prepare-for-migration.md). |
Conecte-se após a migração-Linux | Para ligar aos VMs Azure após a migração utilizando o SSH:<br/> Antes da migração, na máquina no local, verifique se o serviço Secure Shell está definido para iniciar e que as regras de firewall permitem uma ligação SSH.<br/> Após a falha, no Azure VM, permitir ligações de entrada à porta SSH para as regras do grupo de segurança da rede sobre o VM falhado, e para a subnet Azure a que está conectado. Além disso, adicione um endereço IP público para o VM. |  


## <a name="next-steps"></a>Passos Seguintes

[Migrar](tutorial-migrate-physical-virtual-machines.md) servidores físicos.
