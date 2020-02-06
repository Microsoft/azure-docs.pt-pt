---
title: Suporte para migração do Hyper-V em migrações para Azure
description: Saiba mais sobre o suporte para migração do Hyper-V com migrações para Azure.
ms.topic: conceptual
ms.date: 01/08/2020
ms.openlocfilehash: 4ca946597417ccde0e00c8bf09c70207bc4f85b9
ms.sourcegitcommit: f0f73c51441aeb04a5c21a6e3205b7f520f8b0e1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2020
ms.locfileid: "77031651"
---
# <a name="support-matrix-for-hyper-v-migration"></a>Matriz de suporte para migração do Hyper-V

Este artigo resume as definições de suporte e limitações para os VMs hiper-V migratórios com [o Azure Migrate: Server Migration](migrate-services-overview.md#azure-migrate-server-migration-tool) . Se procura informações sobre a avaliação de VMs Hiper-V para migração para Azure, reveja a matriz de suporte de [avaliação](migrate-support-matrix-hyper-v.md).

## <a name="migration-limitations"></a>Limitações de migração

Você pode selecionar até 10 VMs de uma só vez para replicação. Se você quiser migrar mais máquinas, replique em grupos de 10.


## <a name="hyper-v-hosts"></a>Anfitriões hiper-V

| **Suporte**                | **Detalhes**               
| :-------------------       | :------------------- |
| **Implementação**       | O host Hyper-V pode ser autônomo ou implantado em um cluster. |
| **Permissões**           | Você precisa de permissões de administrador no host do Hyper-V. |
| **Sistema operativo anfitrião** | Windows Server 2019, Windows Server 2016 ou Windows Server 2012 R2. |
| **Acesso url** | Os hosts Hyper-V precisam de acesso a essas URLS:<br/><br/> -login.microsoftonline.com: controle de acesso e gerenciamento de identidade usando Active Directory.<br/><br/> -*. backup.windowsazure.com: transferência e coordenação de dados de replicação. Migrar URLs de serviço.<br/><br/> -*. blob.core.windows.net: carregar dados em contas de armazenamento.<br/><br/> -dc.services.visualstudio.com: carregar logs de aplicativo usados para monitoramento interno.<br/><br/> - time.windows.com | Verifica a sincronização de hora entre o sistema e o horário global.
| **Acesso portuário** |  Conexões de saída na porta HTTPS 443 para enviar dados de replicação da VM.

## <a name="hyper-v-vms"></a>VMs Hyper-V

| **Suporte**                  | **Detalhes**               
| :----------------------------- | :------------------- |
| **Sistema operativo** | Todos os sistemas operativos [Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) e [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) que são suportados pelo Azure. |
| **Permissões**           | Você precisa de permissões de administrador em cada VM do Hyper-V que deseja avaliar. |
| **Serviços de Integração**       | Os [Serviços de Integração Hiper-V](https://docs.microsoft.com/virtualization/hyper-v-on-windows/reference/integration-services) devem estar a funcionar em VMs que avalia, de modo a capturar informações do sistema operativo. |
| **Alterações necessárias para O Azure** | Algumas VMs podem exigir alterações para que possam ser executadas no Azure. Tens de fazer ajustes manualmente antes da migração. Os artigos relevantes contêm instruções sobre como fazer isso. |
| **Bota Linux**                 | Se/boot estiver em uma partição dedicada, ele deverá residir no disco do sistema operacional e não poderá ser distribuído em vários discos.<br/> Se/boot fizer parte da partição raiz (/), a partição '/' deverá estar no disco do sistema operacional e não poderá abranger outros discos. |
| **Bota UEFI**                  | A VM migrada no Azure será convertida automaticamente em uma VM de inicialização do BIOS. A VM deve estar executando apenas o Windows Server 2012 e posterior. O disco do sistema operacional deve ter até cinco partições ou menos e o tamanho do disco do sistema operacional deve ser inferior a 300 GB.
  |
| **Tamanho do disco**                  | 2 TB para o disco do sistema operacional, 4 TB para discos de dados.
| **Número do disco** | Um máximo de 16 discos por VM.
| **Discos/volumes encriptados**    | Sem suporte para migração. |
| **Discos RDM/passthrough**      | Sem suporte para migração. |
| **Disco partilhado** | As VMs que usam discos compartilhados não têm suporte para migração.
| **NFS**                        | Volumes NFS montados como volumes nas VMs não serão replicados. |
| **ISCSI**                      | As VMs com destinos iSCSI não têm suporte para migração.
| **Disco-alvo**                | Você pode migrar para VMs do Azure somente com o Managed disks. |
| **IPv6** | Não suportado.
| **Equipa NIC** | Não suportado.
| **Azure Site Recovery** | Não é possível replicar usando a migração de servidor de migrações para Azure se a VM estiver habilitada para replicação com Azure Site Recovery.
| **Portas** | Conexões de saída na porta HTTPS 443 para enviar dados de replicação da VM.

## <a name="azure-vm-requirements"></a>Requisitos de VM do Azure

Todas as VMs locais replicadas para o Azure devem atender aos requisitos de VM do Azure resumidos nesta tabela. Quando Site Recovery executar uma verificação de pré-requisitos para replicação, a verificação falhará se alguns dos requisitos não forem atendidos.

**Componente** | **Requisitos** | **Detalhes**
--- | --- | ---
Sistema operativo convidado | Verifica os sistemas operacionais de VM VMware com suporte para migração.<br/> Você pode migrar qualquer carga de trabalho em execução em um sistema operacional com suporte. | A verificação falhará se não houver suporte.
Arquitetura do sistema operacional convidado | 64-bit. | A verificação falhará se não houver suporte.
Tamanho do disco do sistema operacional | Até 2.048 GB. | A verificação falhará se não houver suporte.
Contagem de disco do sistema operacional | 1 | A verificação falhará se não houver suporte.
Contagem de disco de dados | 64 ou menos. | A verificação falhará se não houver suporte.
Tamanho do disco de dados | Até 4.095 GB | A verificação falhará se não houver suporte.
Adaptadores de rede | Há suporte para vários adaptadores. |
VHD Partilhado | Não suportado. | A verificação falhará se não houver suporte.
Disco FC | Não suportado. | A verificação falhará se não houver suporte.
BitLocker | Não suportado. | O BitLocker deve ser desabilitado antes de habilitar a replicação para um computador.
o nome da VM | De 1 a 63 caracteres.<br/> Limitado a letras, números e hífenes.<br/><br/> O nome do computador deve começar e terminar com uma letra ou número. |  Atualize o valor nas propriedades da máquina em Site Recovery.
Conectar após a migração-Windows | Para se conectar às VMs do Azure que executam o Windows após a migração:<br/> -Antes de a migração habilitar o RDP na VM local. Confirme que são adicionadas regras de TCP e UDP ao perfil **Público** e que o protocolo RDP é permitido em **Firewall do Windows** > **Aplicações Permitidas** para todos os perfis.<br/> Para acesso VPN site-to-site, ative rdP e permita RDP no **Windows Firewall** -> **permitidas aplicações e funcionalidades** para redes **De domínio e privadas.** Além disso, verifique se a política SAN do sistema operativo está definida para **OnlineAll**. [Saiba mais](prepare-for-migration.md). |
Conectar após a migração-Linux | Para se conectar às VMs do Azure após a migração usando SSH:<br/> Antes da migração, no computador local, verifique se o serviço Secure Shell está definido como iniciar e se as regras de firewall permitem uma conexão SSH.<br/> Após o failover, na VM do Azure, permita conexões de entrada para a porta SSH para as regras do grupo de segurança de rede na VM com failover e para a sub-rede do Azure à qual ela está conectada. Além disso, adicione um endereço IP público para a VM. |  

## <a name="next-steps"></a>Passos seguintes

[Migrar VMs hiper-V](tutorial-migrate-hyper-v.md) para migração.
