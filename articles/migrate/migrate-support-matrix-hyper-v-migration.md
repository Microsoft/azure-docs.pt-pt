---
title: Apoio à migração hyper-V em Migração Azure
description: Saiba mais sobre o apoio à migração hyper-V com a Migração Azure.
ms.topic: conceptual
ms.date: 01/08/2020
ms.openlocfilehash: 1eab96df7ee58a8170f75b41c5a2a06f033ced19
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78393290"
---
# <a name="support-matrix-for-hyper-v-migration"></a>Matriz de suporte para migração Hyper-V

Este artigo resume as definições de suporte e limitações para os VMs hiper-V migratórios com [o Azure Migrate: Server Migration](migrate-services-overview.md#azure-migrate-server-migration-tool) . Se procura informações sobre a avaliação de VMs Hiper-V para migração para Azure, reveja a matriz de suporte de [avaliação](migrate-support-matrix-hyper-v.md).

## <a name="migration-limitations"></a>Limitações de migração

Pode selecionar até 10 VMs de uma só vez para replicação. Se quiser migrar mais máquinas, reproduza-se em grupos de 10.


## <a name="hyper-v-hosts"></a>Anfitriões hiper-V

| **Suporte**                | **Detalhes**               
| :-------------------       | :------------------- |
| **Implementação**       | O hospedeiro Hyper-V pode ser autónomo ou implantado num aglomerado. <br/>O software de replicação da Migração Azure (fornecedor de replicação Hyper-V) precisa de ser instalado nos anfitriões Hyper-V.|
| **Permissões**           | Precisa de permissões de administrador no hospedeiro hyper-V. |
| **Sistema operativo anfitrião** | Windows Server 2019, Windows Server 2016 ou Windows Server 2012 R2. |
| **Acesso url** | O software do fornecedor de replicação nos anfitriões hyper-V precisará de acesso a estes URLS:<br/><br/> - login.microsoftonline.com: Controlo de acesso e gestão de identidade utilizando o Ative Directory.<br/><br/> - *.backup.windowsazure.com: Transferência e coordenação de dados de replicação. UrLs de serviço migratório.<br/><br/> - *.blob.core.windows.net: Fazer o upload de dados para contas de armazenamento.<br/><br/> - dc.services.visualstudio.com: Carregar os registos de aplicações utilizados para monitorização interna.<br/><br/> - time.windows.com: Verifica a sincronização do tempo entre o sistema e o tempo global.
| **Acesso portuário** |  Ligações de saída na porta HTTPS 443 para enviar dados de replicação VM.

## <a name="hyper-v-vms"></a>VMs Hyper-V

| **Suporte**                  | **Detalhes**               
| :----------------------------- | :------------------- |
| **Sistema operativo** | Todos os sistemas operativos [Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) e [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) que são suportados pelo Azure. |
| **Alterações necessárias para O Azure** | Alguns VMs podem exigir alterações para que possam correr em Azure. Tens de fazer ajustes manualmente antes da migração. Os artigos relevantes contêm instruções sobre como fazê-lo. |
| **Bota Linux**                 | Se /boot estiver numa divisória dedicada, deve residir no disco OS e não ser espalhado por vários discos.<br/> Se a /bota fizer parte da partição raiz (/), então a partição '/' deve estar no disco OS e não abranger outros discos. |
| **Bota UEFI**                  | O VM migrado em Azure será automaticamente convertido para um VM de arranque BIOS. O VM deve estar a executar o Windows Server 2012 e mais tarde apenas. O disco OS deve ter até cinco divisórias ou menos e o tamanho do disco OS deve ser inferior a 300 GB.
  |
| **Tamanho do disco**                  | 2 TB para o disco OS, 4 TB para discos de dados.
| **Número do disco** | Um máximo de 16 discos por VM.
| **Discos/volumes encriptados**    | Não apoiado para a migração. |
| **Discos RDM/passthrough**      | Não apoiado para a migração. |
| **Disco partilhado** | VMs usando discos partilhados não são suportados para migração.
| **NFS**                        | Os volumes nFS montados à medida que os volumes nos VMs não serão replicados. |
| **ISCSI**                      | VMs com alvos iSCSI não são apoiados para a migração.
| **Disco-alvo**                | Pode migrar para VMs Azure apenas com discos geridos. |
| **IPv6** | Não suportado.
| **Equipa NIC** | Não suportado.
| **Azure Site Recovery** | Não é possível replicar usando a Migração do Servidor Migratório Migratório De Migração de Migração de Migração de Migração de Migração de Migração de Migração de Migração de Migração de Migração de Migrações Azure se o VM estiver habilitado para a replicação com a Recuperação do Site Azure.
| **Portas** | Ligações de saída na porta HTTPS 443 para enviar dados de replicação VM.

## <a name="azure-vm-requirements"></a>Requisitos de VM Azure

Todos os VMs no local replicados ao Azure devem satisfazer os requisitos de VM Azure resumidos nesta tabela.

**Componente** | **Requisitos** | **Detalhes**
--- | --- | ---
Tamanho do disco do sistema operativo | Até 2.048 GB. | A verificação falha se não for suportada.
Contagem de discos do sistema operativo | 1 | A verificação falha se não for suportada.
Contagem de discos de dados | 16 ou menos. | A verificação falha se não for suportada.
Tamanho do disco de dados | Até 4.095 GB | A verificação falha se não for suportada.
Adaptadores de rede | São suportados múltiplos adaptadores. |
VHD Partilhado | Não suportado. | A verificação falha se não for suportada.
Disco fc | Não suportado. | A verificação falha se não for suportada.
BitLocker | Não suportado. | O BitLocker deve ser desativado antes de ativar a replicação de uma máquina.
o nome da VM | De 1 a 63 caracteres.<br/> Limitado a letras, números e hífenes.<br/><br/> O nome da máquina deve começar e terminar com uma letra ou número. |  Atualize o valor nas propriedades da máquina na Recuperação do Site.
Conecte-se após a migração-Windows | Para ligar aos VMs Azure que executam o Windows após a migração:<br/> - Antes que a migração permita o PDR no VM no local. Confirme que são adicionadas regras de TCP e UDP ao perfil **Público** e que o protocolo RDP é permitido em **Firewall do Windows** > **Aplicações Permitidas** para todos os perfis.<br/> Para acesso VPN site-to-site, ative rdP e permita RDP no **Windows Firewall** -> **permitidas aplicações e funcionalidades** para redes **De domínio e privadas.** Além disso, verifique se a política SAN do sistema operativo está definida para **OnlineAll**. [Saiba mais](prepare-for-migration.md). |
Conecte-se após a migração-Linux | Para ligar aos VMs Azure após a migração utilizando o SSH:<br/> Antes da migração, na máquina no local, verifique se o serviço Secure Shell está definido para iniciar e que as regras de firewall permitem uma ligação SSH.<br/> Após a falha, no Azure VM, permitir ligações de entrada à porta SSH para as regras do grupo de segurança da rede sobre o VM falhado, e para a subnet Azure a que está conectado. Além disso, adicione um endereço IP público para o VM. |  

## <a name="next-steps"></a>Passos seguintes

[Migrar VMs hiper-V](tutorial-migrate-hyper-v.md) para migração.
