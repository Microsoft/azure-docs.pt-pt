---
title: Gerir o agente de Mobilidade para VMware/servidores físicos com recuperação do site Azure
description: Gerencie o agente do Serviço de Mobilidade para a recuperação de desastres de VMware VMs e servidores físicos para o Azure utilizando o serviço de recuperação de sites Azure.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: ramamill
ms.openlocfilehash: 9be758c286e072b0fbefc5f8b20b7accc4e6741b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79256968"
---
# <a name="manage-the-mobility-agent"></a>Gerir o Agente de mobilidade 

Configura o agente de mobilidade no seu servidor quando utiliza a Recuperação do Site Azure para a recuperação de desastres de VMware VMs e servidores físicos para o Azure. O agente de mobilidade coordena as comunicações entre a sua máquina protegida, servidor de configuração/servidor de processo sem escalas e gere a replicação de dados. Este artigo resume tarefas comuns para gerir o agente de mobilidade depois de implementado.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="update-mobility-service-from-azure-portal"></a>Atualizar serviço de mobilidade do portal Azure

1. Antes de começar a garantir que o servidor de configuração, servidores de processos de escala e quaisquer servidores-alvo principais que façam parte da sua implementação são atualizados antes de atualizar o Serviço de Mobilidade em máquinas protegidas.
2. No portal abrir o cofre > **itens replicados.**
3. Se o servidor de configuração for a versão mais recente, verá uma notificação onde se lê "A atualização do agente de replicação de recuperação do Novo Site está disponível. Clique para instalar."

     ![Janela de itens replicados](./media/vmware-azure-install-mobility-service/replicated-item-notif.png)

4. Clique na notificação e na **atualização do Agente,** selecione as máquinas nas quais pretende atualizar o serviço de Mobilidade. Em seguida, clique em **OK**.

     ![Lista de itens replicados VM](./media/vmware-azure-install-mobility-service/update-okpng.png)

5. O trabalho do Serviço de Mobilidade atualizada começa para cada uma das máquinas selecionadas.

## <a name="update-mobility-service-through-powershell-script-on-windows-server"></a>Atualizar o serviço de Mobilidade através do script powershell no servidor Windows

Antes de começar a garantir que o servidor de configuração, servidores de processos de escala e quaisquer servidores-alvo principais que façam parte da sua implementação são atualizados antes de atualizar o Serviço de Mobilidade em máquinas protegidas.

Utilize o seguinte script para atualizar o serviço de mobilidade num servidor através de cmdlet de concha de potência

```azurepowershell
Update-AzRecoveryServicesAsrMobilityService -ReplicationProtectedItem $rpi -Account $fabric.fabricSpecificDetails.RunAsAccounts[0]
```

## <a name="update-mobility-service-manually-on-each-protected-server"></a>Atualizar o serviço de mobilidade manualmente em cada servidor protegido

1. Antes de começar a garantir que o servidor de configuração, servidores de processos de escala e quaisquer servidores-alvo principais que façam parte da sua implementação são atualizados antes de atualizar o Serviço de Mobilidade em máquinas protegidas.

2. [Localize o instalador do agente](vmware-physical-mobility-service-overview.md#locate-installer-files) com base no sistema operativo do servidor.

>[!IMPORTANT]
> Se estiver a replicar o VM Azure IaaS de uma região de Azure para outra, não utilize este método. Consulte a [nossa orientação](azure-to-azure-autoupdate.md) para obter informações sobre todas as opções disponíveis.

3. Copie o ficheiro de instalação na máquina protegida e execute-o para atualizar o agente de mobilidade.

## <a name="update-account-used-for-push-installation-of-mobility-service"></a>Conta de atualização utilizada para a instalação de push do serviço mobility

Quando implementou a Recuperação do Site, para permitir a instalação de impulsos do serviço mobility, especificou uma conta que o servidor do processo de recuperação do site utiliza para aceder às máquinas e instalar o serviço quando a replicação está ativada para a máquina. Se pretender atualizar as credenciais para esta conta, siga [estas instruções](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation).

## <a name="uninstall-mobility-service"></a>Desinstalar o serviço de Mobilidade

### <a name="on-a-windows-machine"></a>Em uma máquina do Windows

Desinstale a partir da UI ou a partir de um pedido de comando.

- **A partir da UI**: No Painel de Controlo da máquina, selecione **Programas**. Selecione **Microsoft Azure Site Recovery Mobility Service/Master Target server** > **Desinstalar**.
- **A partir de um pedido de comando:** Abra uma janela de solicitação de comando como administrador na máquina. Execute o seguinte comando: 
    ```
    MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1} /L+*V "C:\ProgramData\ASRSetupLogs\UnifiedAgentMSIUninstall.log"
    ```

### <a name="on-a-linux-machine"></a>Em uma máquina linux
1. Na máquina Linux, inscreva-se como um utilizador **de raiz.**
2. Num terminal, vá a /usr/local/ASR.
3. Execute o seguinte comando:
    ```
    uninstall.sh -Y
   ```
   
## <a name="install-site-recovery-vss-provider-on-source-machine"></a>Instale o fornecedor VSS de recuperação do site na máquina de origem

O fornecedor VsS de recuperação do site Azure é necessário na máquina de origem para gerar pontos de consistência da aplicação. Se a instalação do fornecedor não tiver sucesso através da instalação do impulso, siga as seguintes diretrizes para a instalação manual.

1. Abra a janela cmd de administração.
2. Navegue para o local de instalação do serviço de mobilidade. (Eg - C:\Program Files (x86)\Microsoft Azure Site Recovery\agent)
3. Executar o guião InMageVSSProvider_Uninstall.cmd . Isto irá desinstalar o serviço se já existir.
4. Executar o guião InMageVSSProvider_Install.cmd para instalar manualmente o fornecedor VSS.

## <a name="next-steps"></a>Passos seguintes

- [Configurar a recuperação de desastres para VMware VMs](vmware-azure-tutorial.md)
- [Configurar a recuperação de desastres para servidores físicos](physical-azure-disaster-recovery.md)
