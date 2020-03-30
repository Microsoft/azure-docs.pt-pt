---
title: Gestão do Azure Arc para servidores (pré-visualização) agente
description: Este artigo descreve as diferentes tarefas de gestão que irá executar normalmente durante o ciclo de vida do Azure Arc para servidores Connected Machine agent.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
author: mgoedtel
ms.author: magoedte
ms.date: 03/24/2020
ms.topic: conceptual
ms.openlocfilehash: 758e6123fd09df1e3f8b2e883a729b9fec4328d1
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "80367294"
---
# <a name="managing-and-maintaining-the-connected-machine-agent"></a>Gestão e manutenção do agente máquina conectada

Após a implementação inicial do Azure Arc para servidores (pré-visualização) Agente de máquina conectada para Windows ou Linux, poderá ser necessário reconfigurar o agente, atualizá-lo ou removê-lo do computador se tiver atingido a fase de reforma no seu ciclo de vida. Pode gerir facilmente estas tarefas de manutenção de rotina manualmente ou através da automatização, o que reduz tanto o erro operacional como as despesas.

## <a name="upgrading-agent"></a>Agente de upgrade

O agente Azure Connected Machine para Windows e Linux pode ser atualizado para a mais recente versão manual ou automaticamente, dependendo dos seus requisitos. A tabela seguinte descreve os métodos suportados para realizar a atualização do agente.

| Sistema operativo | Método de atualização |
|------------------|----------------|
| Windows | Manualmente<br> Windows Update |
| Ubuntu | [Apto](https://help.ubuntu.com/lts/serverguide/apt.html) |
| SUSE Linux Enterprise Server | [zigpper](https://en.opensuse.org/SDB:Zypper_usage_11.3) |
| RedHat Enterprise, Amazon, CentOS Linux | [yum](https://wiki.centos.org/PackageManagement/Yum) | 

### <a name="windows-agent"></a>Agente do Windows

Para atualizar o agente numa máquina do Windows para a versão mais recente, o agente está disponível a partir do Microsoft Update e pode ser implementado utilizando o processo de gestão de atualizações de software existente. Também pode ser executado manualmente a partir do Comando Solicitativo, a partir de `AzureConnectedMachine.msi`um script ou outra solução de automatização, ou do assistente ui executando . 

> [!NOTE]
> * Para atualizar o agente, tem de ter permissões do *Administrador.*
> * Para atualizar manualmente, tem primeiro de descarregar e copiar o pacote Installer para uma pasta no servidor alvo ou a partir de uma pasta de rede partilhada. 

Se não estiver familiarizado com as opções de linha de comando para pacotes de instaladores do Windows, reveja as [opções padrão de linha de comando msiexec](https://docs.microsoft.com/windows/win32/msi/standard-installer-command-line-options) e [opções de linha de comando Msiexec](https://docs.microsoft.com/windows/win32/msi/command-line-options).

#### <a name="to-upgrade-using-the-setup-wizard"></a>Para atualizar usando o Assistente de Configuração

1. Inscreva-se no computador com uma conta que tenha direitos administrativos.

2. Execute **AzureConnectedMachineAgent.msi** para iniciar o Assistente de Configuração.

O Assistente de Configuração descobre se existe uma versão anterior e, em seguida, executa automaticamente uma atualização do agente. Quando a atualização estiver concluída, o Assistente de Configuração fecha automaticamente.

#### <a name="to-upgrade-from-the-command-line"></a>Para atualizar a partir da linha de comando

1. Inscreva-se no computador com uma conta que tenha direitos administrativos.

2. Para atualizar o agente silenciosamente e criar `C:\Support\Logs` um ficheiro de registo de configuração na pasta, execute o seguinte comando.

    ```dos
    msiexec.exe /i AzureConnectedMachineAgent.msi /qn /l*v "C:\Support\Logs\Azcmagentupgradesetup.log"
    ```

### <a name="linux-agent"></a>Agente Linux

Para atualizar o agente numa máquina Linux para a versão mais recente, envolve dois comandos. Um comando para atualizar o índice de pacotelocal com a lista dos pacotes mais recentes disponíveis dos repositórios, e um comando para atualizar o pacote local. 

#### <a name="upgrade-ubuntu"></a>Upgrade Ubuntu

1. Para atualizar o índice de pacotelocal com as últimas alterações feitas nos repositórios, executar o seguinte comando:

    ```bash
    apt update
    ```

2. Para atualizar o seu sistema, execute o seguinte comando:

    ```bash
    apt upgrade
    ```

As ações do comando [apto,](https://help.ubuntu.com/lts/serverguide/apt.html) tais como a instalação `/var/log/dpkg.log` e remoção de pacotes, são registadas no ficheiro de registo.

#### <a name="upgrade-red-hatcentosamazon-linux"></a>Upgrade Red Hat/CentOS/Amazon Linux

1. Para atualizar o índice de pacotelocal com as últimas alterações feitas nos repositórios, executar o seguinte comando:

    ```bash
    yum check-update
    ```

2. Para atualizar o seu sistema, execute o seguinte comando:

    ```bash
    yum update
    ```

As ações do comando [yum,](https://access.redhat.com/articles/yum-cheat-sheet) tais como a instalação `/var/log/yum.log` e remoção de pacotes, são registadas no ficheiro de registo. 

#### <a name="upgrade-suse-linux-enterprise"></a>Upgrade SUSE Linux Enterprise

1. Para atualizar o índice de pacotelocal com as últimas alterações feitas nos repositórios, executar o seguinte comando:

    ```bash
    zypper refresh
    ```

2. Para atualizar o seu sistema, execute o seguinte comando:

    ```bash
    zypper update
    ```

As ações do comando [zypper,](https://en.opensuse.org/Portal:Zypper) tais como a instalação `/var/log/zypper.log` e remoção de pacotes, são registadas no ficheiro de registo. 

## <a name="remove-the-agent"></a>Remova o agente

Utilize um dos seguintes procedimentos para desinstalar o agente Windows ou Linux utilizando a linha de comando ou o assistente de configuração descrito nesta secção. Antes de desinstalar o agente, desligue primeiro a máquina do Azure Arc para servidores (pré-visualização) completando estes passos: 

1. Abra o Arco Azure para servidores (pré-visualização) indo para o [portal Azure](https://aka.ms/hybridmachineportal).

2. Selecione a máquina na lista, selecione a elipse (**...**) e, em seguida, selecione **Delete**.

### <a name="windows-agent"></a>Agente do Windows

#### <a name="uninstall-from-control-panel"></a>Desinstalar do Painel de Controlo

1. Para desinstalar o agente windows da máquina, faça o seguinte:

    a. Inscreva-se no computador com uma conta que tenha permissões de administrador.  
    b. No **Painel de Controlo,** selecione **Programas e Funcionalidades.**  
    c. Em **Programas e Funcionalidades,** selecione **Agente de Máquina Seletiva,** selecione **Desinstalar**, e, em seguida, selecione **Sim**.  

    >[!NOTE]
    > Também pode executar o assistente de configuração do agente clicando duas vezes no pacote de instalação **AzureConnectedMachineAgent.msi.**

#### <a name="uninstall-from-the-command-line"></a>Desinstalar a partir da linha de comando

Para desinstalar o agente manualmente a partir do Pedido de Comando ou utilizar um método automatizado, como um script, pode utilizar o seguinte exemplo. Primeiro é necessário recuperar o código do produto, que é um GUID que é o principal identificador do pacote de aplicação, do sistema operativo. A desinstalação é realizada utilizando a linha de `msiexec /x {Product Code}`comando Msiexec.exe - .
    
1. Abra o Editor de Registo.

2. Sob a `HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\Uninstall`chave de registo, procure e copie o código do produto GUID.

3. Em seguida, pode desinstalar o agente utilizando a Msiexec utilizando os seguintes exemplos:

   * Do tipo de linha de comando:

       ```dos
       msiexec.exe /x {product code GUID} /qn
       ```

   * Pode executar os mesmos passos utilizando o PowerShell:

       ```powershell
       Get-ChildItem -Path HKLM:\Software\Microsoft\Windows\CurrentVersion\Uninstall | `
       Get-ItemProperty | `
       Where-Object {$_.DisplayName -eq "Azure Connected Machine Agent"} | `
       ForEach-Object {MsiExec.exe /x "$($_.PsChildName)" /qn}
       ```

### <a name="linux-agent"></a>Agente Linux

Para desinstalar o agente Linux, o comando a utilizar depende do sistema operativo Linux.

- Para Ubuntu, executar o seguinte comando:

    ```bash
    sudo apt purge azcmagent
    ```

- Para rhel, CentOS e Amazon Linux, executar o seguinte comando:

    ```bash
    sudo yum remove azcmagent
    ```

- Para as SLES, executar o seguinte comando:

    ```bash
    sudo zypper remove azcmagent
    ```
