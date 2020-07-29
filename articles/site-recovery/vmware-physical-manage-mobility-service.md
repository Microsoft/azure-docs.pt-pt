---
title: Gerir o agente de mobilidade para VMware/servidores físicos com recuperação do site Azure
description: Gerir o agente do Serviço de Mobilidade para a recuperação de desastres de VMware VMs e servidores físicos para a Azure utilizando o serviço de Recuperação do Local Azure.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: ramamill
ms.openlocfilehash: 9be758c286e072b0fbefc5f8b20b7accc4e6741b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "79256968"
---
# <a name="manage-the-mobility-agent"></a>Gerir o Agente de mobilidade 

Configura o agente de mobilidade no seu servidor quando utiliza a Recuperação do Site Azure para a recuperação de desastres de VMware VMs e servidores físicos para o Azure. O agente de mobilidade coordena as comunicações entre a sua máquina protegida, o servidor de processamento de configuração/servidor de processo de escala e gere a replicação de dados. Este artigo resume tarefas comuns para gerir o agente de mobilidade depois de ser implementado.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="update-mobility-service-from-azure-portal"></a>Atualizar o serviço de mobilidade a partir do portal Azure

1. Antes de começar, certifique-se de que o servidor de configuração, os servidores de processo de escala e quaisquer servidores-alvo principais que fazem parte da sua implementação são atualizados antes de atualizar o Serviço de Mobilidade em máquinas protegidas.
2. No portal abra o cofre > **itens replicados**.
3. Se o servidor de configuração for a versão mais recente, vê uma notificação onde se lê "A nova atualização do agente de replicação de recuperação do Site está disponível. Clique para instalar."

     ![Janela de itens replicados](./media/vmware-azure-install-mobility-service/replicated-item-notif.png)

4. Clique na notificação e na **atualização do Agente,** selecione as máquinas nas quais pretende atualizar o serviço Mobility. Em seguida, clique em **OK**.

     ![Lista de VM de itens replicados](./media/vmware-azure-install-mobility-service/update-okpng.png)

5. O trabalho do Serviço de Mobilidade de Atualização começa para cada uma das máquinas selecionadas.

## <a name="update-mobility-service-through-powershell-script-on-windows-server"></a>Atualizar o serviço de mobilidade através do script powershell no servidor do Windows

Antes de começar, certifique-se de que o servidor de configuração, os servidores de processo de escala e quaisquer servidores-alvo principais que fazem parte da sua implementação são atualizados antes de atualizar o Serviço de Mobilidade em máquinas protegidas.

Use o seguinte script para atualizar o serviço de mobilidade num servidor através do cmdlet power shell

```azurepowershell
Update-AzRecoveryServicesAsrMobilityService -ReplicationProtectedItem $rpi -Account $fabric.fabricSpecificDetails.RunAsAccounts[0]
```

## <a name="update-mobility-service-manually-on-each-protected-server"></a>Atualizar o serviço de mobilidade manualmente em cada servidor protegido

1. Antes de começar, certifique-se de que o servidor de configuração, os servidores de processo de escala e quaisquer servidores-alvo principais que fazem parte da sua implementação são atualizados antes de atualizar o Serviço de Mobilidade em máquinas protegidas.

2. [Localize o instalador do agente](vmware-physical-mobility-service-overview.md#locate-installer-files) com base no sistema operativo do servidor.

>[!IMPORTANT]
> Se estiver a replicar o Azure IaaS VM de uma região de Azure para outra, não utilize este método. Consulte a [nossa orientação](azure-to-azure-autoupdate.md) para obter informações sobre todas as opções disponíveis.

3. Copie o ficheiro de instalação para a máquina protegida e execute-o para atualizar o agente de mobilidade.

## <a name="update-account-used-for-push-installation-of-mobility-service"></a>Conta de atualização utilizada para a instalação de push do serviço de mobilidade

Quando implementou a Recuperação do Site, para permitir a instalação de push do serviço mobility, especificou uma conta que o servidor de processo de Recuperação do Site utiliza para aceder às máquinas e instalar o serviço quando a replicação está ativada para a máquina. Se pretender atualizar as credenciais desta conta, siga [estas instruções](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation).

## <a name="uninstall-mobility-service"></a>Desinstalar o serviço de mobilidade

### <a name="on-a-windows-machine"></a>Em uma máquina Windows

Desinstalar da UI ou de um pedido de comando.

- **A partir do UI**: No Painel de Controlo da máquina, selecione **Programas**. Selecione **o Serviço de Mobilidade do Site Microsoft Azure/servidor-alvo principal**  >  **Desinstalar**.
- **A partir de um aviso de comando**: Abra uma janela de ordem de comando como administrador na máquina. Execute o seguinte comando: 
    ```
    MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1} /L+*V "C:\ProgramData\ASRSetupLogs\UnifiedAgentMSIUninstall.log"
    ```

### <a name="on-a-linux-machine"></a>Em uma máquina Linux
1. Na máquina Linux, inscreva-se como um utilizador **de raiz.**
2. Num terminal, vá a /usr/local/ASR.
3. Execute o seguinte comando:
    ```
    uninstall.sh -Y
   ```
   
## <a name="install-site-recovery-vss-provider-on-source-machine"></a>Instalar o fornecedor VSS de recuperação do local na máquina de origem

O fornecedor Azure Site Recovery VSS é necessário na máquina de origem para gerar pontos de consistência da aplicação. Se a instalação do fornecedor não tiver sido bem sucedida através da instalação push, siga as instruções abaixo indicada para a instalar manualmente.

1. Abra a janela de administração cmd.
2. Navegue para o local de instalação do serviço de mobilidade. (Por exemplo - C:\Ficheiros de programas (x86)\Microsoft Azure Site Recovery\agent)
3. Executar o script InMageVSSProvider_Uninstall.cmd . Isto irá desinstalar o serviço se já existir.
4. Executar o script InMageVSSProvider_Install.cmd para instalar manualmente o fornecedor VSS.

## <a name="next-steps"></a>Próximos passos

- [Configurar a recuperação de desastres para VMware VMs](vmware-azure-tutorial.md)
- [Configurar a recuperação de desastres para servidores físicos](physical-azure-disaster-recovery.md)
