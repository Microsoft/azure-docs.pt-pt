---
title: Gerir o agente de mobilidade nos servidores para recuperação após desastre de VMs de VMware e servidores físicos com o Azure Site Recovery | Documentos da Microsoft
description: Gerir o agente do serviço de mobilidade de recuperação após desastre de VMs de VMware e servidores físicos para o Azure com o serviço Azure Site Recovery.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: ramamill
ms.openlocfilehash: 441b58e60bf8dfd5f164ac24d746b9791158ade2
ms.sourcegitcommit: 72cc94d92928c0354d9671172979759922865615
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/25/2019
ms.locfileid: "58420121"
---
# <a name="manage-mobility-agent-on-protected-machines"></a>Gerir o agente de mobilidade nas máquinas protegidas

Configurar o agente de mobilidade no seu servidor quando utiliza o Azure Site Recovery para recuperação após desastre de VMs de VMware e servidores físicos para o Azure. Agente de mobilidade coordena as comunicações entre o seu computador protegido, o servidor de processos de servidor/aumentar horizontalmente de configuração e gere a replicação de dados. Este artigo resume as tarefas comuns para gerir o agente de mobilidade, após a sua implementação.

## <a name="update-mobility-service-from-azure-portal"></a>Atualização do serviço de mobilidade a partir do portal do Azure

1. Antes de iniciar, certifique-se de que o servidor de configuração, servidores de processos de escalamento horizontal e quaisquer servidores de destino mestre que fazem parte da implementação são atualizados antes de atualizar o serviço de mobilidade nas máquinas protegidas.
2. No portal, abra o Cofre > **itens replicados**.
3. Se o servidor de configuração é a versão mais recente, verá uma notificação que lê a "atualização do agente de replicação de recuperação do novo Site está disponível. Clique para instalar."

     ![Janela de itens replicados](./media/vmware-azure-install-mobility-service/replicated-item-notif.png)

4. Clique na notificação e, em **atualização do agente**, selecione as máquinas em que pretende atualizar o serviço de mobilidade. Em seguida, clique em **OK**.

     ![Lista de VMS de itens de replicados](./media/vmware-azure-install-mobility-service/update-okpng.png)

5. A tarefa de serviço de mobilidade de atualização é iniciada para cada uma das máquinas selecionadas.

## <a name="update-mobility-service-through-powershell-script-on-windows-server"></a>Atualização do serviço de mobilidade por meio de script do powershell no servidor do Windows

Utilize a seguinte script para atualizar o serviço de mobilidade num servidor através do cmdlet do power shell

```azurepowershell
Update-AzureRmRecoveryServicesAsrMobilityService -ReplicationProtectedItem $rpi -Account $fabric.fabricSpecificDetails.RunAsAccounts[0]
```

## <a name="update-account-used-for-push-installation-of-mobility-service"></a>Atualizar a conta utilizada para a instalação push do serviço de mobilidade

Quando implementou o Site Recovery, para ativar a instalação push do serviço de mobilidade, especificou uma conta que o servidor de processos de recuperação de Site utiliza para aceder as máquinas e instalar o serviço quando a replicação está ativada para a máquina. Se quiser atualizar as credenciais para esta conta, siga [estas instruções](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation).

## <a name="uninstall-mobility-service"></a>Desinstale o serviço de mobilidade

### <a name="on-a-windows-machine"></a>Num computador Windows

Desinstale de interface do Usuário de um prompt de comando.

- **Desde a interface do Usuário**: No painel de controlo da máquina, selecione **programas**. Selecione **servidor do Microsoft Azure Site Recovery Mobility Service/destino principal** > **desinstalação**.
- **Num prompt de comando**: Abra uma janela de linha de comandos como administrador no computador. Execute o seguinte comando: 
    ```
    MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1} /L+*V "C:\ProgramData\ASRSetupLogs\UnifiedAgentMSIUninstall.log"
    ```

### <a name="on-a-linux-machine"></a>Num computador com Linux
1. Na máquina Linux, inicie sessão como um **raiz** utilizador.
2. Num terminal, vá para /user/local/ASR.
3. Execute o seguinte comando:
    ```
    uninstall.sh -Y

## Install Site Recovery VSS provider on source machine

Azure Site Recovery VSS provider is required on the source machine to generate application consistency points. If the installation of the provider didn't succeed through push installation, follow the below given guidelines to install it manually.

1. Open admin cmd window.
2. Navigate to the mobility service installation location. (Eg - C:\Program Files (x86)\Microsoft Azure Site Recovery\agent)
3. Run the script InMageVSSProvider_Uninstall.cmd . This will uninstall the service if it already exists.
4. Run the script InMageVSSProvider_Install.cmd to install the VSS provider manually.

## Next steps

- [Set up disaster recovery for VMware VMs](vmware-azure-tutorial.md)
- [Set up disaster recovery for physical servers](physical-azure-disaster-recovery.md)
