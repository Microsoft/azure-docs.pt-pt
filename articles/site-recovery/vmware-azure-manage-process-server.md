---
title: Gerir um servidor de processo utilizado para recuperação após desastre de VMs de VMware e servidores físicos para o Azure com o Azure Site Recovery | Documentos da Microsoft
description: Este artigo descreve gerir um servidor de processos que configurar para recuperação após desastre de VMs de VMware e servidores físicos para o Azure com o Azure Site Recovery.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/28/2019
ms.author: ramamill
ms.openlocfilehash: 2c27779719c73adf4d7fc1a61a0c77d03df71815
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/30/2019
ms.locfileid: "64925638"
---
# <a name="manage-process-servers"></a>Gerir servidores de processos

Este artigo descreve as tarefas comuns para gerir o servidor de processos de recuperação de sites.

O servidor de processos é utilizado para receber, otimizar e enviar dados de replicação para o Azure. Também executa uma instalação push do serviço de mobilidade em VMs de VMware e servidores físicos que pretende replicar, e efetua a deteção automática de máquinas no local. Para replicar VMs de VMware no local ou servidores físicos para o Azure, o servidor de processos está instalado por predefinição na máquina do servidor de configuração. 

- Para grandes implementações, poderá precisar de servidores de processos no local adicionais para dimensionar a capacidade.
- Para a reativação pós-falha do Azure para o local, tem de configurar um servidor de processo temporário no Azure. É possível eliminar esta VM quando a reativação pós-falha é realizada. 

Saiba mais sobre o servidor de processos.


## <a name="upgrade-a-process-server"></a>Atualizar um servidor de processos

Quando implementa um servidor de processos no local, ou como uma VM do Azure para reativação pós-falha, a versão mais recente do servidor de processos está instalada. A recuperação de Site equipas versão correções e aprimoramentos em intervalos regulares, e recomendamos que mantenha atualizados os servidores de processos. Pode atualizar um servidor de processos da seguinte forma:

[!INCLUDE [site-recovery-vmware-upgrade -process-server](../../includes/site-recovery-vmware-upgrade-process-server-internal.md)]


## <a name="move-vms-to-balance-the-process-server-load"></a>Mover VMs para balancear a carga do servidor de processo

Balancear a carga por mover VMs entre dois servidores de processo, da seguinte forma:

1. No cofre, sob **Manage** clique em **infraestrutura do Site Recovery**. Sob **máquinas do VMware e físicas**, clique em **servidores de configuração**.
2. Clique no servidor de configuração com a qual os servidores de processo são registados.
3. Clique no servidor de processos para o qual pretende balancear carga de tráfego.

    ![LoadBalance](media/vmware-azure-manage-process-server/LoadBalance.png)

4. Clique em **balanceamento de carga**, selecione o servidor de processos de destino ao qual pretende mover as máquinas. Em seguida, clique em **OK**

    ![LoadPS](media/vmware-azure-manage-process-server/LoadPS.PNG)

2. Clique em **selecionar máquinas**e escolha as máquinas que pretende mover de atual para o servidor de processos de destino. Detalhes de alteração de dados de média são exibidos em relação a cada máquina virtual. Em seguida, clique em **OK**. 
3. No cofre, monitorizar o progresso da tarefa sob **monitorização** > **tarefas do Site Recovery**.

Ele irá demorar cerca de 15 minutos para que as alterações sejam refletidas no portal. Para um efeito mais rápido, [atualizar o servidor de configuração](vmware-azure-manage-configuration-server.md#refresh-configuration-server).

## <a name="switch-an-entire-workload-to-another-process-server"></a>Mudar de uma carga de trabalho inteira para outro servidor de processos

Mova a carga de trabalho inteira manipulada por um servidor de processo para um servidor de processo diferente, da seguinte forma:

1. No cofre, sob **Manage** clique em **infraestrutura do Site Recovery**. Sob **máquinas do VMware e físicas**, clique em **servidores de configuração**.
2. Clique no servidor de configuração com a qual os servidores de processo são registados.
3. Clique no servidor de processos a partir do qual pretende mudar a carga de trabalho.
4. Clique em **comutador**, selecione o servidor de processos de destino ao qual pretende mover a carga de trabalho. Em seguida, clique em **OK**

    ![Comutador](media/vmware-azure-manage-process-server/Switch.PNG)

5. No cofre, monitorizar o progresso da tarefa sob **monitorização** > **tarefas do Site Recovery**.

Ele irá demorar cerca de 15 minutos para que as alterações sejam refletidas no portal. Para um efeito mais rápido, [atualizar o servidor de configuração](vmware-azure-manage-configuration-server.md#refresh-configuration-server).



## <a name="reregister-a-process-server"></a>Voltar a registar um servidor de processos

Voltar a registar um servidor de processos em execução no local ou numa VM do Azure com o servidor de configuração da seguinte forma:

[!INCLUDE [site-recovery-vmware-register-process-server](../../includes/site-recovery-vmware-register-process-server.md)]

Depois de salvar as definições, efetue o seguinte:

1. No servidor de processos, abra uma linha de comandos de administrador.
2. Navegue até à pasta **%PROGRAMDATA%\ASR\Agent**, e execute o comando:

    ```
    cdpcli.exe --registermt
    net stop obengine
    net start obengine
    ```

## <a name="modify-proxy-settings-for-an-on-premises-process-server"></a>Modificar definições de proxy para um servidor de processos no local

Se um servidor de processos no local utiliza um proxy para ligar ao Azure, pode modificar as definições de proxy, da seguinte forma:

1. Inicie sessão na máquina do servidor de processo. 
2. Abra uma janela de comando do PowerShell de administrador e execute o seguinte comando:
   ```powershell
   $pwd = ConvertTo-SecureString -String MyProxyUserPassword
   Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber –ProxyUserName domain\username -ProxyPassword $pwd
   net stop obengine
   net start obengine
   ```
2. Navegue até à pasta **%PROGRAMDATA%\ASR\Agent**, e execute este comando:
   ```
   cmd
   cdpcli.exe --registermt

   net stop obengine

   net start obengine

   exit
   ```

## <a name="remove-a-process-server"></a>Remover um servidor de processos

[!INCLUDE [site-recovery-vmware-unregister-process-server](../../includes/site-recovery-vmware-unregister-process-server.md)]

## <a name="exclude-folders-from-anti-virus-software"></a>Excluir pastas do software antivírus

Se o software antivírus está em execução num servidor de processos de escalamento horizontal (ou o servidor de destino mestre), exclua as seguintes pastas de software antivírus operações:


- C:\Program Files\Microsoft Azure Recovery Services Agent
- C:\ProgramData\ASR
- C:\ProgramData\ASRLogs
- C:\ProgramData\ASRSetupLogs
- C:\ProgramData\LogUploadServiceLogs
- C:\ProgramData\Microsoft Azure Site Recovery
- Diretório de instalação do servidor de processo. Por exemplo: C:\Program Files (x86) \Microsoft do Azure Site Recovery