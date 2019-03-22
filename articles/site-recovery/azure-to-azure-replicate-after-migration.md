---
title: Configurar a recuperação após desastre para VMs do Azure após a migração para o Azure com o Azure Site Recovery | Documentos da Microsoft
description: Este artigo descreve como preparar as máquinas para configurar a recuperação após desastre entre regiões do Azure após a migração para o Azure com o Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: article
ms.date: 03/18/2019
ms.author: raynew
ms.openlocfilehash: 76119c912ac6ad1447bfcff1f4c98e60f34b072f
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58317141"
---
# <a name="set-up-disaster-recovery-for-azure-vms-after-migration-to-azure"></a>Configurar a recuperação após desastre de VMs do Azure após a migração para o Azure 


Utilize este artigo se tiver [migrar máquinas no local para VMs do Azure](tutorial-migrate-on-premises-to-azure.md) utilizando o [Site Recovery](site-recovery-overview.md) serviço e agora pretende obter as VMs que configurar para recuperação após desastre para uma região secundária do Azure. O artigo descreve como garantir que o agente de VM do Azure está instalado em VMs migradas e como remover o serviço de mobilidade de recuperação de Site que já não é necessário após a migração.



## <a name="verify-migration"></a>Verificar a migração

Antes de configurar a recuperação após desastre, certifique-se de que a migração foi concluída conforme esperado. Para concluir uma migração com êxito, após a ativação pós-falha, deve selecionar o **concluir a migração** opção, para cada máquina que pretende migrar. 

## <a name="verify-the-azure-vm-agent"></a>Verifique se o agente de VM do Azure

Cada VM do Azure tem de ter o [agente de VM do Azure](../virtual-machines/extensions/agent-windows.md) instalado. Para replicar VMs do Azure, o Site Recovery instala uma extensão no agente.

- Se a máquina está a executar a versão 9.7.0.0 ou posterior do serviço de mobilidade de recuperação de Site, o agente de VM do Azure é instalado automaticamente pelo serviço de mobilidade em VMs do Windows. Em versões anteriores do serviço de mobilidade, terá de instalar o agente automaticamente.
- Para VMs do Linux, tem de instalar manualmente o agente de VM do Azure. Só tem de instalar o agente de VM do Azure se o serviço de mobilidade instalado na máquina migrada é v9.6 ou anterior.


### <a name="install-the-agent-on-windows-vms"></a>Instalar o agente em VMs do Windows

Se estiver a executar uma versão do serviço de mobilidade da recuperação de Site anterior ao 9.7.0.0 ou, se tiver alguns outra necessidade de instalar manualmente o agente, efetue o seguinte:  

1. Certifique-se de que tem permissões de administrador na VM.
2. Transfira o [instalador do agente de VM](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409).
3. Execute o ficheiro de instalador.

#### <a name="validate-the-installation"></a>Validar a instalação
Para verificar que o agente está instalado:

1. Na VM do Azure, na pasta C:\WindowsAzure\Packages, deverá ver o ficheiro de WaAppAgent.exe.
2. O arquivo, com o botão direito e, em **propriedades**, selecione a **detalhes** separador.
3. Certifique-se de que o **versão do produto** campo mostra 2.6.1198.718 ou superior.

[Saiba mais](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows) sobre a instalação do agente para Windows.

### <a name="install-the-agent-on-linux-vms"></a>Instalar o agente em VMs do Linux

Instalar o [VM Linux do Azure](../virtual-machines/extensions/agent-linux.md) agente manualmente da seguinte forma:

1. Certifique-se de que tem permissões de administrador na máquina.
2. Recomendamos vivamente que instale o agente de VM do Linux utilizando um pacote DEB ou um RPM do repositório de pacotes de sua distribuição. Todas as a [apoiadas pelo fornecedores de distribuição](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) integrar o pacote do agente Linux do Azure em suas imagens e repositórios.
    - Recomendamos vivamente que Atualize o agente apenas por meio de um repositório de distribuição.
    - Não é recomendada a instalação do agente de VM com Linux diretamente a partir do GitHub e atualizá-lo.
    -  Se o agente mais recente para a sua distribuição não é suportada de distribuição disponíveis, contacte para obter instruções sobre como instalá-lo. 

#### <a name="validate-the-installation"></a>Validar a instalação 

1. Execute este comando: **ps -e** para se certificar de que o agente do Azure está em execução na VM do Linux.
2. Se o processo não está em execução, reinicie-o usando os seguintes comandos:
    - Para o Ubuntu: **walinuxagent início de serviço**
    - Para outras distribuições: **waagent início de serviço**


## <a name="uninstall-the-mobility-service"></a>Desinstale o serviço de mobilidade

1. Desinstale manualmente o serviço de mobilidade da VM do Azure, através de um dos seguintes métodos. 
    - Para Windows, no painel de controlo > **Adicionar/remover programas**, desinstalar **servidor do Microsoft Azure Site Recovery Mobility Service/destino principal**. Na linha de comandos elevada, execute:
        ```
        MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1} /L+*V "C:\ProgramData\ASRSetupLogs\UnifiedAgentMSIUninstall.log"
        ```
    - Para o Linux, inicie sessão como utilizador raiz. Num terminal, aceda a **/user/local/ASR**, e execute o seguinte comando:
        ```
        uninstall.sh -Y
        ```
2. Reinicie a VM antes de configurar a replicação.

## <a name="next-steps"></a>Passos Seguintes

[Resolução de problemas de revisão](site-recovery-extension-troubleshoot.md) para a extensão de recuperação de sites no agente de VM do Azure.
[Replicar rapidamente](azure-to-azure-quickstart.md) uma VM do Azure para uma região secundária.
