---
title: Gerir um servidor de processo para VMware VMs/recuperação de desastres de servidor físico na Recuperação do Site Azure
description: Este artigo descreve a gestão de um servidor de processo para recuperação de desastres de VMware VMs/servidores físicos usando a Recuperação do Site Azure.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/28/2019
ms.author: ramamill
ms.openlocfilehash: ef16e3b75ca8e051b1b7abb1a92843279884c697
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78362867"
---
# <a name="manage-process-servers"></a>Gerir servidores de processos

Este artigo descreve tarefas comuns para gerir o servidor do processo de recuperação do site.

O servidor de processos é utilizado para receber, otimizar e enviar dados de replicação para o Azure. Também realiza uma instalação push do serviço mobility em VMware VMs e servidores físicos que pretende replicar, e realiza a descoberta automática de máquinas no local. Para replicar VMs no local ou servidores físicos para o Azure, o servidor de processo é instalado por padrão na máquina do servidor de configuração. 

- Para grandes implementações, poderá necessitar de servidores de processos adicionais no local para escalar a capacidade.
- Para o failback do Azure para o local, você deve configurar um servidor de processo temporário em Azure. Pode eliminar este VM quando o failback estiver feito. 

Saiba mais sobre o servidor de processos.


## <a name="upgrade-a-process-server"></a>Atualizar um servidor de processo

Quando implementa um servidor de processo no local, ou como um VM Azure para failback, a versão mais recente do servidor de processos está instalada. As equipas de recuperação do site libertam correções e melhorias regularmente, e recomendamos que mantenha os servidores de processo atualizados. Pode atualizar um servidor de processo da seguinte forma:

[!INCLUDE [site-recovery-vmware-upgrade -process-server](../../includes/site-recovery-vmware-upgrade-process-server-internal.md)]


## <a name="move-vms-to-balance-the-process-server-load"></a>Mova vMs para equilibrar a carga do servidor de processo

Equilibre a carga movendo VMs entre dois servidores de processo, da seguinte forma:

1. No cofre, sob **a Manage clique** na Infraestrutura de **Recuperação do Local**. **Para VMware e máquinas físicas,** clique em Servidores de **Configuração**.
2. Clique no servidor de configuração com o qual os servidores de processo estão registados.
3. Clique no servidor de processos para o qual pretende carregar o tráfego de equilíbrio.

    ![LoadBalance](media/vmware-azure-manage-process-server/LoadBalance.png)

4. Clique no **equilíbrio de carga,** selecione o servidor de processo de destino para o qual pretende mover as máquinas. Em seguida, clique em **OK**

    ![LoadPS](media/vmware-azure-manage-process-server/LoadPS.PNG)

2. Clique em **Selecionar máquinas**e escolha as máquinas que pretende mover da corrente para o servidor de processo-alvo. Os detalhes da alteração média dos dados são apresentados contra cada máquina virtual. Em seguida, clique em **OK**. 
3. No cofre, monitorize o progresso do trabalho no âmbito da **monitorização** > trabalhos de recuperação do **local.**

Levará cerca de 15 minutos para que as alterações se reflitam no portal. Para um efeito mais rápido, [refresque o servidor de configuração](vmware-azure-manage-configuration-server.md#refresh-configuration-server).

## <a name="switch-an-entire-workload-to-another-process-server"></a>Mude toda uma carga de trabalho para outro servidor de processo

Mova toda a carga de trabalho manuseada por um servidor de processo para um servidor de processo diferente, da seguinte forma:

1. No cofre, sob **a Manage clique** na Infraestrutura de **Recuperação do Local**. **Para VMware e máquinas físicas,** clique em Servidores de **Configuração**.
2. Clique no servidor de configuração com o qual os servidores de processo estão registados.
3. Clique no servidor de processos a partir do qual pretende mudar a carga de trabalho.
4. Clique no **Switch,** selecione o servidor de processo de destino para o qual pretende mover a carga de trabalho. Em seguida, clique em **OK**

    ![Comutador](media/vmware-azure-manage-process-server/Switch.PNG)

5. No cofre, monitorize o progresso do trabalho no âmbito da **monitorização** > trabalhos de recuperação do **local.**

Levará cerca de 15 minutos para que as alterações se reflitam no portal. Para um efeito mais rápido, [refresque o servidor de configuração](vmware-azure-manage-configuration-server.md#refresh-configuration-server).

## <a name="register-a-master-target-server"></a>Registe um servidor-alvo principal

O servidor de alvo principal reside no servidor de configuração e nos servidores de processos de escala. Deve ser registado com o servidor de configuração. No caso de haver uma falha neste registo, pode ter impacto na saúde dos itens protegidos. Para registar o servidor de alvo principal com o servidor de configuração, inicie o login no servidor de processo sintetizado/scale-out no qual o registo é necessário. Navegue para pasta **%PROGRAMDATA%\ASR\Agent,** e execute o seguinte no pedido de comando do administrador.

   ```
   cmd
   cdpcli.exe --registermt

   net stop obengine

   net start obengine

   exit
   ```

## <a name="reregister-a-process-server"></a>Reregistre um servidor de processo

Reregistre um servidor de processo sintetizando no local ou num VM Azure com o servidor de configuração da seguinte forma:

[!INCLUDE [site-recovery-vmware-register-process-server](../../includes/site-recovery-vmware-register-process-server.md)]

Depois de ter guardado as definições, faça o seguinte:

1. No servidor de processos, abra um pedido de comando de administrador.
2. Navegue para pasta **%PROGRAMDATA%\ASR\Agent,** e executar o comando:

    ```
    cdpcli.exe --registermt
    net stop obengine
    net start obengine
    ```

## <a name="modify-proxy-settings-for-an-on-premises-process-server"></a>Modificar as definições de procuração para um servidor de processo no local

Se um servidor de processo no local utilizar um proxy para ligar ao Azure, pode modificar as definições de procuração da seguinte forma:

1. Inscreva-se na máquina do servidor de processos. 
2. Abra uma janela de comando Da Admin PowerShell e executar o seguinte comando:
   ```powershell
   $pwd = ConvertTo-SecureString -String MyProxyUserPassword
   Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber –ProxyUserName domain\username -ProxyPassword $pwd
   net stop obengine
   net start obengine
   ```
2. Navegue para pasta **%PROGRAMDATA%\ASR\Agent,** e execute este comando:
   ```
   cmd
   cdpcli.exe --registermt

   net stop obengine

   net start obengine

   exit
   ```

## <a name="remove-a-process-server"></a>Remover um servidor de processo

[!INCLUDE [site-recovery-vmware-unregister-process-server](../../includes/site-recovery-vmware-unregister-process-server.md)]

## <a name="exclude-folders-from-anti-virus-software"></a>Excluir pastas do software antivírus

Se o software antivírus estiver a funcionar num servidor de processos de escala (ou servidor de alvo principal), exclua as seguintes pastas das operações antivírus:


- C:\Program Files\Microsoft Azure Recovery Services Agent
- C:\ProgramData\ASR
- C:\ProgramData\ASRLogs
- C:\ProgramData\ASRSetupLogs
- C:\ProgramData\LogUploadServiceLogs
- C:\ProgramData\Microsoft Azure Site Recovery
- Diretório de instalação de servidor de processos. Por exemplo: C:\Program Files (x86)\Microsoft Azure Site Recovery