---
title: Apoio à migração de Hiper-V em Azure Migrate
description: Saiba mais sobre o suporte à migração hiper-V com a Azure Migrate.
ms.topic: conceptual
ms.date: 04/15/2020
ms.openlocfilehash: 1e1fa7e9c2e2105bef02f833e40dadf113dea4b5
ms.sourcegitcommit: 58ff2addf1ffa32d529ee9661bbef8fbae3cddec
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/03/2020
ms.locfileid: "84323778"
---
# <a name="support-matrix-for-hyper-v-migration"></a>Matriz de suporte para migração hiper-V

Este artigo resume as configurações e limitações de suporte para a migração de VMs Hiper-V com [Azure Migrate: Migração do Servidor](migrate-services-overview.md#azure-migrate-server-migration-tool) . Se procura informações sobre a avaliação de Hiper-V VMs para migração para Azure, reveja a matriz de suporte de [avaliação](migrate-support-matrix-hyper-v.md).

## <a name="migration-limitations"></a>Limitações da migração

Pode selecionar até 10 VMs de uma só vez para replicação. Se quiser migrar mais máquinas, replique-se em grupos de 10.


## <a name="hyper-v-hosts"></a>Anfitriões de Hyper-V

| **Suporte**                | **Detalhes**               
| :-------------------       | :------------------- |
| **Implementação**       | O hospedeiro Hyper-V pode ser autónomo ou implantado num cluster. <br/>O software de replicação Azure Migrate (fornecedor de replicação Hiper-V) está instalado nos anfitriões Hyper-V.|
| **Permissões**           | Precisa de permissões de administrador no anfitrião do Hiper-V. |
| **Sistema operativo anfitrião** | Windows Server 2019, Windows Server 2016 ou Windows Server 2012 R2. |
| **Acesso portuário** |  Ligações de saída na porta HTTPS 443 para enviar dados de replicação VM.

### <a name="url-access-public-cloud"></a>Acesso a URL (nuvem pública)

O software do fornecedor de replicação nos anfitriões Hiper-V necessitará de acesso a estes URLs.

**URL** | **Detalhes**
--- | ---
login.microsoftonline.com | Controlo de acesso e gestão de identidade utilizando o Ative Directory.
backup.windowsazure.com | Transferência e coordenação de dados de replicação.
*.hypervrecoverymanager.windowsazure.com | Usado para gestão de replicação.
*.blob.core.windows.net | Faça upload de dados para contas de armazenamento. 
dc.services.visualstudio.com | Faça upload de registos de aplicativos utilizados para monitorização interna.
time.windows.com | Verifica a sincronização temporal entre o sistema e o tempo global.

### <a name="url-access-azure-government"></a>Acesso url (Governo Azure)

O software do fornecedor de replicação nos anfitriões Hiper-V necessitará de acesso a estes URLs.

**URL** | **Detalhes**
--- | ---
login.microsoftonline.us | Controlo de acesso e gestão de identidade utilizando o Ative Directory.
backup.windowsazure.us | Transferência e coordenação de dados de replicação.
*.hypervrecoverymanager.windowsazure.us | Usado para gestão de replicação.
*.blob.core.usgovcloudapi.net | Faça upload de dados para contas de armazenamento.
dc.services.visualstudio.com | Faça upload de registos de aplicativos utilizados para monitorização interna.
time.nist.gov | Verifica a sincronização temporal entre o sistema e o tempo global.


## <a name="hyper-v-vms"></a>VMs Hyper-V

| **Suporte**                  | **Detalhes**               
| :----------------------------- | :------------------- |
| **Sistema operativo** | Todos os sistemas operativos [Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) e [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) que são suportados pelo Azure. |
| **Alterações necessárias para o Azure** | Alguns VMs podem necessitar de alterações para que possam ser executados em Azure. Faça ajustes manualmente antes da migração. Os artigos relevantes contêm instruções sobre como fazê-lo. |
| **Bota Linux**                 | Se o arranque estiver numa divisória dedicada, deve residir no disco OS e não ser espalhado por vários discos.<br/> Se /boot é parte da raiz (/) partição, então a partição '/' deve estar no disco DE, e não abranger outros discos. |
| **Bota UEFI**                  | O VM migrado em Azure será automaticamente convertido para um VM de arranque BIOS. O VM deve estar a executar o Windows Server 2012 e mais tarde apenas. O disco de so deve ter até cinco divisórias ou menos e o tamanho do disco de SO deve ser inferior a 300 GB.|
| **Tamanho do disco**                  | 2 TB para o disco de so, 4 TB para discos de dados.|
| **Número do disco** | Um máximo de 16 discos por VM.|
| **Discos/volumes encriptados**    | Não apoiado para a migração.|
| **Discos RDM/passthrough**      | Não apoiado para a migração.|
| **Disco compartilhado** | Os VM que usam discos partilhados não são suportados para migração.|
| **NFS**                        | Os volumes NFS montados à medida que os volumes nos VMs não serão replicados.|
| **ISCSI**                      | VMs com metas iSCSI não são suportados para migração.
| **Disco-alvo**                | Pode migrar para VMs Azure apenas com discos geridos. |
| **IPv6** | Não suportado.|
| **Equipa nic** | Não suportado.|
| **Azure Site Recovery** | Não é possível replicar usando a migração do servidor Azure Migrate se o VM estiver ativado para replicação com recuperação do site Azure.|
| **Portas** | Ligações de saída na porta HTTPS 443 para enviar dados de replicação VM.|

## <a name="azure-vm-requirements"></a>Requisitos da VM do Azure

Todos os VMs no local replicados ao Azure devem satisfazer os requisitos Azure VM resumidos nesta tabela.

**Componente** | **Requisitos** | **Detalhes**
--- | --- | ---
Tamanho do disco do sistema operativo | Até 2.048 GB. | A verificação falha se não for apoiada.
Contagem de discos do sistema operativo | 1 | A verificação falha se não for apoiada.
Contagem de discos de dados | 16 ou menos. | A verificação falha se não for apoiada.
Tamanho do disco de dados | Até 4.095 GB | A verificação falha se não for apoiada.
Placas de rede | Vários adaptadores são suportados. |
VHD partilhado | Não suportado. | A verificação falha se não for apoiada.
Disco FC | Não suportado. | A verificação falha se não for apoiada.
BitLocker | Não suportado. | O BitLocker tem de ser desativado antes de ativar a replicação de uma máquina.
o nome da VM | De 1 a 63 caracteres.<br/> Limitado a letras, números e hífenes.<br/><br/> O nome da máquina deve começar e terminar com uma letra ou número. |  Atualize o valor nas propriedades da máquina na Recuperação do Local.
Conecte-se após a migração-Windows | Para ligar aos VMs Azure que executam o Windows após a migração:<br/> - Antes da migração permite pDR nas VM no local. Confirme que são adicionadas regras de TCP e UDP ao perfil **Público** e que o protocolo RDP é permitido em **Firewall do Windows** > **Aplicações Permitidas** para todos os perfis.<br/> Para acesso local-a-site VPN, ative o **Windows Firewall**RDP e permita o RDP em  ->  **aplicações e funcionalidades permitidas** para o Windows Firewall e para redes **de domínio e privado.** Além disso, verifique se a política SAN do sistema operativo está definida para **OnlineAll**. [Saiba mais](prepare-for-migration.md). |
Conecte-se após migração-Linux | Para ligar aos VMs Azure após a migração utilizando SSH:<br/> Antes da migração, na máquina no local, verifique se o serviço Secure Shell está definido para iniciar e que as regras de firewall permitem uma ligação SSH.<br/> Após o failover, no Azure VM, permita a entrada de ligações à porta SSH para as regras do grupo de segurança da rede sobre o falhado sobre vM, e para a sub-rede Azure à qual está ligada. Além disso, adicione um endereço IP público para o VM. |  

## <a name="next-steps"></a>Próximos passos

[Migrar VMs Hiper-V](tutorial-migrate-hyper-v.md) para migração.
