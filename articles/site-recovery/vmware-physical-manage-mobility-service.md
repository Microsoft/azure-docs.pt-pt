---
title: Gerenciar o agente de mobilidade para servidores VMware/físicos com Azure Site Recovery
description: Gerencie o agente do serviço de mobilidade para recuperação de desastre de VMs VMware e servidores físicos no Azure usando o serviço Azure Site Recovery.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: ramamill
ms.openlocfilehash: e6e7beeb4c10098f36636aad2709e03d1a1a0fea
ms.sourcegitcommit: 44c2a964fb8521f9961928f6f7457ae3ed362694
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/12/2019
ms.locfileid: "73953651"
---
# <a name="manage-the-mobility-agent"></a>Gerenciar o agente de mobilidade 

Você configura o agente de mobilidade no servidor quando usa Azure Site Recovery para recuperação de desastre de VMs VMware e servidores físicos para o Azure. O agente de mobilidade coordena as comunicações entre o computador protegido, o servidor de configuração/servidor de processo de expansão e gerencia a replicação de dados. Este artigo resume as tarefas comuns para gerenciar o agente de mobilidade após sua implantação.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="update-mobility-service-from-azure-portal"></a>Atualizar o serviço de mobilidade de portal do Azure

1. Antes de começar, verifique se o servidor de configuração, os servidores de processo de expansão e todos os servidores de destino mestre que fazem parte de sua implantação são atualizados antes de atualizar o serviço de mobilidade em computadores protegidos.
2. No portal, abra o cofre > **itens replicados**.
3. Se o servidor de configuração for a versão mais recente, você verá uma notificação que lê "a nova atualização do agente de replicação do site Recovery está disponível. Clique para instalar. "

     ![Janela itens replicados](./media/vmware-azure-install-mobility-service/replicated-item-notif.png)

4. Clique na notificação e, em **atualização do agente**, selecione os computadores nos quais você deseja atualizar o serviço de mobilidade. Em seguida, clique em **OK**.

     ![Lista de VMs de itens replicados](./media/vmware-azure-install-mobility-service/update-okpng.png)

5. O trabalho do serviço de mobilidade de atualização é iniciado para cada um dos computadores selecionados.

## <a name="update-mobility-service-through-powershell-script-on-windows-server"></a>Atualizar o serviço de mobilidade por meio do script do PowerShell no Windows Server

Use o script a seguir para atualizar o serviço de mobilidade em um servidor por meio do cmdlet do PowerShell

```azurepowershell
Update-AzRecoveryServicesAsrMobilityService -ReplicationProtectedItem $rpi -Account $fabric.fabricSpecificDetails.RunAsAccounts[0]
```

## <a name="update-account-used-for-push-installation-of-mobility-service"></a>Atualizar a conta usada para a instalação por push do serviço de mobilidade

Quando você implantou o Site Recovery, para habilitar a instalação por push do serviço de mobilidade, você especificou uma conta que o servidor de processo de Site Recovery usa para acessar os computadores e instalar o serviço quando a replicação está habilitada para o computador. Se você quiser atualizar as credenciais para essa conta, siga [estas instruções](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation).

## <a name="uninstall-mobility-service"></a>Desinstalar o serviço de mobilidade

### <a name="on-a-windows-machine"></a>Em um computador Windows

Desinstale a partir da interface do usuário ou de um prompt de comando.

- Na **interface do usuário**: no painel de controle da máquina, selecione **programas**. Selecione **Microsoft Azure site Recovery serviço de mobilidade/servidor de destino mestre** > **desinstalar**.
- **Em um prompt de comando**: Abra uma janela de prompt de comando como um administrador no computador. Execute o seguinte comando: 
    ```
    MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1} /L+*V "C:\ProgramData\ASRSetupLogs\UnifiedAgentMSIUninstall.log"
    ```

### <a name="on-a-linux-machine"></a>Em um computador Linux
1. No computador Linux, entre como um usuário **raiz** .
2. Em um terminal, vá para/usr/local/ASR.
3. Execute o seguinte comando:
    ```
    uninstall.sh -Y
   ```
   
## <a name="install-site-recovery-vss-provider-on-source-machine"></a>Instalar Site Recovery provedor VSS no computador de origem

Azure Site Recovery provedor de VSS é necessário no computador de origem para gerar pontos de consistência de aplicativo. Se a instalação do provedor não tiver sido realizada com a instalação por push, siga as diretrizes abaixo para instalá-lo manualmente.

1. Abra a janela do administrador cmd.
2. Navegue até o local de instalação do serviço de mobilidade. (Por exemplo, C:\Arquivos de programas (x86) \Microsoft Azure site Recovery\agent)
3. Execute o script InMageVSSProvider_Uninstall. cmd. Isso desinstalará o serviço, se ele já existir.
4. Execute o script InMageVSSProvider_Install. cmd para instalar o provedor VSS manualmente.

## <a name="next-steps"></a>Passos seguintes

- [Configurar a recuperação de desastre para VMs VMware](vmware-azure-tutorial.md)
- [Configurar a recuperação de desastre para servidores físicos](physical-azure-disaster-recovery.md)
