---
title: Sobre o serviço de mobilidade para a recuperação após desastre de VMs de VMware e servidores físicos com o Azure Site Recovery | Documentos da Microsoft
description: Saiba mais sobre o agente do serviço de mobilidade para recuperação após desastre de VMs de VMware e servidores físicos para o Azure com o serviço Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/04/2018
ms.author: raynew
ms.openlocfilehash: 24102593a294d60b3cc2107bef8d443e672443b3
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/05/2018
ms.locfileid: "51019271"
---
# <a name="about-the-mobility-service-for-vmware-vms-and-physical-servers"></a>Sobre o serviço de mobilidade para VMs de VMware e servidores físicos

Quando define a recuperação após desastre para VMs de VMware e servidores físicos usando [do Azure Site Recovery](site-recovery-overview.md), instalar o serviço de mobilidade de recuperação de Site em cada VM de VMware no local e o servidor físico.  O serviço de mobilidade obtém dados, escreve na máquina e reencaminha-os para o servidor de processos de recuperação de sites. Pode implementar o serviço de mobilidade através dos seguintes métodos:

[Instalação push](vmware-azure-install-mobility-service.md): configurar o Site Recovery para efetuar uma instalação push do serviço de mobilidade: para fazer isso, quando configurar a recuperação após desastre, também configurar uma conta que o servidor de processos de recuperação de Site pode utilizar para aceder à VM ou um servidor físico Para efeitos de instalar o serviço.
[Instalar manualmente](vmware-physical-mobility-service-install-manual.md): pode instalar o serviço de mobilidade manualmente em cada máquina através da interface do Usuário ou linha de comandos.
[Implementação automatizada](vmware-azure-mobility-install-configuration-mgr.md): pode automatizar a instalação com ferramentas de implantação de software, como o System Center Configuration Manager.

## <a name="azure-virtual-machine-agent"></a>Agente de Máquina Virtual do Azure

- **VMs do Windows**: da versão 9.7.0.0 do serviço de mobilidade, o [agente de VM do Azure](../virtual-machines/extensions/features-windows.md#azure-vm-agent) é instalado pelo instalador do serviço de mobilidade. Isto garante que, quando a máquina faz a ativação pós-falha para o Azure, a VM do Azure cumpre a pré-requisitos para a utilização de qualquer extensão de Vm a instalação do agente.
- **VMs do Linux**: A [WALinuxAgent](https://docs.microsoft.com/azure/virtual-machines/extensions/update-linux-agent) tem de ser instalado manualmente na VM do Azure após a ativação pós-falha.

## <a name="installer-files"></a>Arquivos do instalador

A tabela resume os arquivos do instalador para cada VM de VMware e o sistema operativo de servidor físico. Pode rever [sistemas operativos suportados](vmware-physical-azure-support-matrix.md#replicated-machines) antes de começar.


**Ficheiro de instalador** | **Sistema operativo (apenas 64 bits)** 
--- | ---
Microsoft-ASR\_UA\*Windows\*release.exe | Windows Server 2016; Windows Server 2012 R2; Windows Server 2012; Windows Server 2008 R2 SP1 
Microsoft ASR\_UA\*RHEL6-64\*release.tar.gz | Red Hat Enterprise Linux (RHEL) 6.* </br> CentOS 6.*
Microsoft-ASR\_UA\*RHEL7-64\*release.tar.gz | Red Hat Enterprise Linux (RHEL) 7.* </br> CentOS 7.* 
Microsoft ASR\_UA\*SLES12-64\*release.tar.gz | SUSE Linux Enterprise Server 12 SP1, SP2, SP3 
Microsoft-ASR\_UA\*SLES11-SP3-64\*release.tar.gz| SUSE Linux Enterprise Server 11 SP3 
Microsoft-ASR\_UA\*SLES11-SP4-64\*release.tar.gz| SP4 do SUSE Linux Enterprise Server 11 
Microsoft-ASR\_UA\*OL6-64\*release.tar.gz | Oracle Enterprise Linux 6.4, 6.5
Microsoft-ASR\_UA\*UBUNTU-14.04-64\*release.tar.gz | Ubuntu Linux 14.04
Microsoft-ASR\_UA\*UBUNTU-16.04-64\*release.tar.gz | Server 16.04 LTS do Ubuntu Linux
Microsoft-ASR_UA\*DEBIAN7-64\*release.tar.gz | Debian 7 
Microsoft-ASR_UA\*DEBIAN8-64\*release.tar.gz | Debian 8


## <a name="update-the-mobility-service"></a>Atualizar o serviço de mobilidade

1. Antes de iniciar, certifique-se de que o servidor de configuração, servidores de processos de escalamento horizontal e quaisquer servidores de destino mestre que fazem parte da implementação são atualizados antes de atualizar o serviço de mobilidade nas máquinas protegidas.
2. No portal, abra o Cofre > **itens replicados**.
3. Se o servidor de configuração é a versão mais recente, verá uma notificação que lê a "atualização do agente de replicação de recuperação do novo Site está disponível. Clique para instalar."

     ![Janela de itens replicados](.\media\vmware-azure-install-mobility-service\replicated-item-notif.png)

4. Clique na notificação e, em **atualização do agente**, selecione as máquinas em que pretende atualizar o serviço de mobilidade. Em seguida, clique em **OK**.

     ![Lista de VMS de itens de replicados](.\media\vmware-azure-install-mobility-service\update-okpng.png)

5. A tarefa de serviço de mobilidade de atualização é iniciada para cada uma das máquinas selecionadas.

## <a name="update-the-acount-used-for-push-installation-of-the-mobility-service"></a>Atualizar a conta utilizada para a instalação push do serviço de mobilidade

Quando implementou o Site Recovery, para ativar a instalação push do serviço de mobilidade, especificou uma conta que o servidor de processos de recuperação de Site utiliza para aceder as máquinas e instalar o serviço quando a replicação está ativada para a máquina. Se quiser atualizar as credenciais para esta conta, siga [estas instruções](vmware-azure-manage-configuration-server.md).

## <a name="uninstall-the-mobility-service"></a>Desinstale o serviço de mobilidade

### <a name="on-a-windows-machine"></a>Num computador Windows

Desinstale de interface do Usuário de um prompt de comando.

- **Desde a interface do Usuário**: no painel de controlo da máquina, selecione **programas**. Selecione **servidor do Microsoft Azure Site Recovery Mobility Service/destino principal** > **desinstalação**.
- **Num prompt de comando**: Abra uma janela de linha de comandos como administrador no computador. Execute o seguinte comando: 
    ```
    MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1} /L+*V "C:\ProgramData\ASRSetupLogs\UnifiedAgentMSIUninstall.log"
    ```

## <a name="on-a-linux-machine"></a>Num computador com Linux
1. Na máquina Linux, inicie sessão como um **raiz** utilizador.
2. Num terminal, vá para /user/local/ASR.
3. Execute o seguinte comando:

    ```
    uninstall.sh -Y
    ```

## <a name="next-steps"></a>Passos Seguintes

[Configurar a instalação push do serviço de mobilidade](vmware-azure-install-mobility-service.md).