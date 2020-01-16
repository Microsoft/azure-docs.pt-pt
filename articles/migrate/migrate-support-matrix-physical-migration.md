---
title: Suporte para migração de servidor físico nas migrações para Azure
description: Saiba mais sobre o suporte para migração de servidor físico nas migrações para Azure.
ms.topic: conceptual
ms.date: 01/07/2020
ms.openlocfilehash: e55cf6dddbc8dafd33b444e4a0dbe378d807aea1
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/15/2020
ms.locfileid: "76030240"
---
# <a name="support-matrix-for-physical-server-migration"></a>Matriz de suporte para migração de servidor físico

Este artigo resume as configurações de suporte e as limitações para a migração de servidores físicos com o [migrações para Azure: migração de servidor](migrate-services-overview.md#azure-migrate-server-migration-tool) . Se você estiver procurando informações sobre como avaliar servidores físicos para migração para o Azure, examine a [matriz de suporte de avaliação](migrate-support-matrix-physical.md).


## <a name="overview"></a>Visão geral

Você pode migrar computadores locais como servidores físicos, usando a replicação baseada em agente. Usando essa ferramenta, você pode migrar uma ampla variedade de máquinas para o Azure:

- Servidores físicos locais.
- VMs virtualizadas por plataformas como Xen, KVM.
- VMs do Hyper-V ou VMs do VMware se por algum motivo você não quiser usar os fluxos do [Hyper-v](tutorial-migrate-hyper-v.md) ou [VMware](server-migrate-overview.md) padrão.
- VMs em execução em nuvens privadas.
- VMs em execução em nuvens públicas, como Amazon Web Services (AWS) ou Google Cloud Platform (GCP).


## <a name="migration-limitations"></a>Limitações da migração

Você pode selecionar até 10 máquinas de uma só vez para a replicação. Se você quiser migrar mais máquinas, faça a replicação em grupos de 10.


## <a name="physical-server-requirements"></a>Requisitos do servidor físico

A tabela resume o suporte para servidores físicos que você deseja migrar usando a migração baseada em agente.

**Suporte** | **Detalhes**
--- | ---
**Carga de trabalho da máquina** | As migrações para Azure dão suporte à migração de qualquer carga de trabalho (digamos Active Directory, SQL Server, etc.) em execução em um computador com suporte.
**Sistemas operativos** | Para obter as informações mais recentes, examine o [suporte do sistema operacional](../site-recovery/vmware-physical-azure-support-matrix.md#replicated-machines) para site Recovery. As migrações para Azure fornecem suporte ao sistema operacional idêntico.
**Armazenamento de sistema de arquivos/convidado do Linux** | Para obter as informações mais recentes, examine o [suporte do sistema de arquivos do Linux](../site-recovery/vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage) para site Recovery. As migrações para Azure fornecem suporte idêntico ao sistema de arquivos do Linux.
**Rede/armazenamento** | Para obter as informações mais recentes, examine os pré-requisitos de [rede](../site-recovery/vmware-physical-azure-support-matrix.md#network) e [armazenamento](../site-recovery/vmware-physical-azure-support-matrix.md#storage) para site Recovery. As migrações para Azure fornecem requisitos de rede/armazenamento idênticos.
**Requisitos do Azure** | Para obter as informações mais recentes, examine os requisitos de rede, [armazenamento](../site-recovery/vmware-physical-azure-support-matrix.md#azure-storage)e [computação](../site-recovery/vmware-physical-azure-support-matrix.md#azure-compute) do [Azure](../site-recovery/vmware-physical-azure-support-matrix.md#azure-vm-network-after-failover)para site Recovery. As migrações para Azure têm requisitos idênticos para a migração de servidor físico.
**Serviço de mobilidade** | O agente do serviço de mobilidade deve ser instalado em cada computador que você deseja migrar.
**Inicialização UEFI** | O computador migrado no Azure será convertido automaticamente em uma VM do Azure de inicialização do BIOS.<br/><br/> O disco do sistema operacional deve ter até quatro partições, e os volumes devem ser formatados com NTFS.
**Disco de destino** | Os computadores só podem ser migrados para discos gerenciados (HDD padrão, SSD Premium) no Azure.
**Tamanho do disco** | disco do sistema operacional de 2 TB; 8 TB para discos de dados.
**Limites de disco** |  Até 63 discos por computador.
**Discos/volumes criptografados** |  Computadores com discos/volumes criptografados não têm suporte para migração.
**Cluster de disco compartilhado** | Não suportado.
**Discos independentes** | Suportado.
**Discos de passagem** | Suportado.
**NFS** | Volumes NFS montados como volumes nos computadores não serão replicados.
**destinos iSCSI** | Não há suporte para computadores com destinos iSCSI para migração sem agente.
**E/s de vários caminhos** | Não suportado.
**VMotion de armazenamento** | Suportadas
**NICs agrupadas** | Não suportado.
**Protocolo** | Não suportado.



## <a name="replication-appliance-requirements"></a>Requisitos do dispositivo de replicação

Se você configurar o dispositivo de replicação manualmente em um servidor físico, verifique se ele está em conformidade com os requisitos resumidos na tabela. Quando você configura o dispositivo de replicação de migrações para Azure como uma VM VMware usando o modelo OVA fornecido no Hub migrações para Azure, o dispositivo é configurado com o Windows Server 2016 e está em conformidade com os requisitos de suporte. 

- Saiba mais sobre [os requisitos do dispositivo de replicação](migrate-replication-appliance.md#appliance-requirements).
- O MySQL deve estar instalado no dispositivo. Saiba mais sobre [as opções de instalação](migrate-replication-appliance.md#mysql-installation).
- Saiba mais sobre [URLs](migrate-replication-appliance.md#url-access) que o dispositivo de replicação precisa acessar.

## <a name="azure-vm-requirements"></a>Requisitos de VM do Azure

Todas as VMs locais replicadas para o Azure devem atender aos requisitos de VM do Azure resumidos nesta tabela. Quando Site Recovery executar uma verificação de pré-requisitos para replicação, a verificação falhará se alguns dos requisitos não forem atendidos.

**Componente** | **Requisitos** | **Detalhes**
--- | --- | ---
Sistema operativo convidado | Verifica os sistemas operacionais com suporte.<br/> Você pode migrar qualquer carga de trabalho em execução em um sistema operacional com suporte. | A verificação falhará se não houver suporte.
Arquitetura do sistema operacional convidado | 64 bits. | A verificação falhará se não houver suporte.
Tamanho do disco do sistema operacional | Até 2.048 GB. | A verificação falhará se não houver suporte.
Contagem de disco do sistema operacional | 1 | A verificação falhará se não houver suporte.
Contagem de disco de dados | 64 ou menos. | A verificação falhará se não houver suporte.
Tamanho do disco de dados | Até 4.095 GB | A verificação falhará se não houver suporte.
Placas de rede | Há suporte para vários adaptadores. |
VHD Partilhado | Não suportado. | A verificação falhará se não houver suporte.
Disco FC | Não suportado. | A verificação falhará se não houver suporte.
BitLocker | Não suportado. | O BitLocker deve ser desabilitado antes de habilitar a replicação para um computador.
o nome da VM | De 1 a 63 caracteres.<br/> Limitado a letras, números e hífenes.<br/><br/> O nome do computador deve começar e terminar com uma letra ou número. |  Atualize o valor nas propriedades da máquina em Site Recovery.
Conectar após a migração-Windows | Para se conectar às VMs do Azure que executam o Windows após a migração:<br/> -Antes de a migração habilitar o RDP na VM local. Confirme que são adicionadas regras de TCP e UDP ao perfil **Público** e que o protocolo RDP é permitido em **Firewall do Windows** > **Aplicações Permitidas** para todos os perfis.<br/> Para acesso VPN site a site, habilite o RDP e permita que o RDP no **Firewall do Windows** -> **aplicativos e recursos permitidos** para redes de **domínio e privadas** . Além disso, verifique se a política de SAN do sistema operacional está definida como **OnlineAll**. [Saiba mais](prepare-for-migration.md). |
Conectar após a migração-Linux | Para se conectar às VMs do Azure após a migração usando SSH:<br/> Antes da migração, no computador local, verifique se o serviço Secure Shell está definido como iniciar e se as regras de firewall permitem uma conexão SSH.<br/> Após o failover, na VM do Azure, permita conexões de entrada para a porta SSH para as regras do grupo de segurança de rede na VM com failover e para a sub-rede do Azure à qual ela está conectada. Além disso, adicione um endereço IP público para a VM. |  


## <a name="next-steps"></a>Passos seguintes

[Migrar](tutorial-migrate-physical-virtual-machines.md) servidores físicos.
