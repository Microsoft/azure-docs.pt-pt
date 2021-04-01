---
title: Gerir um servidor de processo para VMware VMs/recuperação de desastres de servidor físico na Recuperação do Site Azure
description: Este artigo descreve gerir um servidor de processo para a recuperação de desastres de VMware VMs/servidores físicos usando a Recuperação do Site Azure.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/28/2019
ms.author: ramamill
ms.openlocfilehash: a547a874c42d06d8453b154847561d8b5f0dabb8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96019204"
---
# <a name="manage-process-servers"></a>Gerir servidores de processos

Este artigo descreve tarefas comuns para gerir o servidor de processo de recuperação do site.

O servidor de processo é utilizado para receber, otimizar e enviar dados de replicação para o Azure. Também executa uma instalação push do serviço mobility em VMware VMs e servidores físicos que pretende replicar, e executa a descoberta automática de máquinas no local. Para replicar VMs vMware no local ou servidores físicos para Azure, o servidor de processo é instalado por padrão na máquina do servidor de configuração. 

- Para grandes implementações, poderá necessitar de servidores de processo adicionais no local para aumentar a capacidade.
- Para o recuo de Azure para o local, tem de configurar um servidor de processo temporário em Azure. Pode eliminar este VM quando o failback estiver feito. 

Saiba mais sobre o servidor de processos.


## <a name="upgrade-a-process-server"></a>Atualizar um servidor de processo

Quando implementa um servidor de processo no local, ou como um VM Azure para falha, a versão mais recente do servidor de processos é instalada. As equipas de Recuperação do Site lançam correções e melhorias regularmente, e recomendamos que mantenha os servidores de processo atualizados. Pode atualizar um servidor de processo da seguinte forma:

[!INCLUDE [site-recovery-vmware-upgrade -process-server](../../includes/site-recovery-vmware-upgrade-process-server-internal.md)]


## <a name="move-vms-to-balance-the-process-server-load"></a>Mover VMs para equilibrar a carga do servidor de processo

Equilibrar a carga movendo VMs entre dois servidores de processo, da seguinte forma:

1. No cofre, em **Gestão clique** **na Infraestrutura de Recuperação do Local**. Em **Para as máquinas & físicas VMware,** clique em **Servidores de Configuração**.
2. Clique no servidor de configuração com o qual os servidores de processo estão registados.
3. Clique no servidor de processo para o qual pretende carregar o tráfego de equilíbrio.

    ![A screenshot mostra um Servidor de Processo para o qual pode carregar o tráfego de equilíbrio.](media/vmware-azure-manage-process-server/LoadBalance.png)

4. Clique **no equilíbrio de carga,** selecione o servidor de processo-alvo para o qual pretende mover máquinas. Em seguida, clique **EM OK**

    ![A screenshot mostra o painel de equilíbrio de carga com o servidor de processo de destino seleto selecionado.](media/vmware-azure-manage-process-server/LoadPS.PNG)

2. Clique **em Selecionar máquinas** e escolha as máquinas que pretende mover da corrente para o servidor de processo-alvo. Os detalhes da mudança média de dados são apresentados contra cada máquina virtual. Em seguida, clique em **OK**. 
3. No cofre, monitorize o progresso do trabalho no âmbito dos trabalhos de  >  **recuperação do local de monitorização.**

Levará cerca de 15 minutos para que as alterações sejam refletidas no portal. Para um efeito mais rápido, [refresque o servidor de configuração](vmware-azure-manage-configuration-server.md#refresh-configuration-server).

## <a name="switch-an-entire-workload-to-another-process-server"></a>Altere uma carga de trabalho inteira para outro servidor de processo

Mover toda a carga de trabalho tratada por um servidor de processo para um servidor de processo diferente, da seguinte forma:

1. No cofre, em **Gestão clique** **na Infraestrutura de Recuperação do Local**. Em **Para as máquinas & físicas VMware,** clique em **Servidores de Configuração**.
2. Clique no servidor de configuração com o qual os servidores de processo estão registados.
3. Clique no servidor de processo a partir do qual pretende mudar a carga de trabalho.
4. Clique no **Switch,** selecione o servidor de processo-alvo para o qual pretende mover a carga de trabalho. Em seguida, clique **EM OK**

    ![A screenshot mostra o painel do servidor do processo de destino Select.](media/vmware-azure-manage-process-server/Switch.PNG)

5. No cofre, monitorize o progresso do trabalho no âmbito dos trabalhos de  >  **recuperação do local de monitorização.**

Levará cerca de 15 minutos para que as alterações sejam refletidas no portal. Para um efeito mais rápido, [refresque o servidor de configuração](vmware-azure-manage-configuration-server.md#refresh-configuration-server).

## <a name="register-a-master-target-server"></a>Registar um servidor-alvo principal

O servidor-alvo principal reside no servidor de configuração e nos servidores de processo de escala. Deve ser registado com servidor de configuração. No caso de haver uma falha neste registo, pode afetar a saúde dos itens protegidos. Para registar o servidor alvo principal com o servidor de configuração, inicie sessão no servidor de processo de configuração/escala específico no qual o registo é necessário. Navegue para a pasta **%PROGRAMDATA%\ASR\Agente**, e execute o seguinte na indicação do comando do administrador.

   ```
   cmd
   cdpcli.exe --registermt

   net stop obengine

   net start obengine

   exit
   ```

## <a name="reregister-a-process-server"></a>Reregisterar um servidor de processo

Reregisterar um servidor de processo em execução no local ou num VM Azure com o servidor de configuração da seguinte forma:

[!INCLUDE [site-recovery-vmware-register-process-server](../../includes/site-recovery-vmware-register-process-server.md)]

Depois de guardar as definições, faça o seguinte:

1. No servidor de processo, abra um pedido de comando do administrador.
2. Navegue para a pasta **%PROGRAMDATA%\ASR\Agente,** e executar o comando:

    ```
    cdpcli.exe --registermt
    net stop obengine
    net start obengine
    ```

## <a name="modify-proxy-settings-for-an-on-premises-process-server"></a>Modificar as definições de procuração para um servidor de processo no local

Se um servidor de processo no local utilizar um proxy para se ligar ao Azure, pode modificar as definições de procuração da seguinte forma:

1. Inscreva-se na máquina do servidor de processo. 
2. Abra uma janela de comando Admin PowerShell e executar o seguinte comando:
   ```powershell
   $pwd = ConvertTo-SecureString -String MyProxyUserPassword
   Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber –ProxyUserName domain\username -ProxyPassword $pwd
   net stop obengine
   net start obengine
   ```
2. Navegue para a pasta **%PROGRAMDATA%\ASR\Agente,** e executar este comando:
   ```
   cmd
   cdpcli.exe --registermt

   net stop obengine

   net start obengine

   exit
   ```

## <a name="remove-a-process-server"></a>Remover um servidor de processo

[!INCLUDE [site-recovery-vmware-unregister-process-server](../../includes/site-recovery-vmware-unregister-process-server.md)]

## <a name="exclude-folders-from-anti-virus-software"></a>Excluir pastas de software antivírus

Se o software antivírus estiver a ser realizado num servidor de processo de escala (ou servidor-alvo principal), exclua as seguintes pastas das operações antivírus:


- C:\Ficheiros de programa\Microsoft Azure Recovery Services Agent
- C:\ProgramData\ASR
- C:\ProgramData\ASRLogs
- C:\ProgramData\ASRSetupLogs
- C:\ProgramData\LogUploadServiceLogs
- C:\ProgramData\Microsoft Azure Site Recovery
- Processar o diretório de instalação do servidor. Por exemplo: C:\Ficheiros de programa (x86)\Microsoft Azure Site Recovery