---
title: Serviço de Mobilidade Automatizada para recuperação de desastres na Recuperação do Sítio de Azure
description: Como instalar automaticamente o Serviço de Mobilidade para recuperação de desastres VMware/servidor físico com recuperação do site Azure.
author: Rajeswari-Mamilla
ms.topic: how-to
ms.date: 2/5/2020
ms.author: ramamill
ms.openlocfilehash: 2159ab8c2639f0f87fd53e8559dad518a3daa663
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92544822"
---
# <a name="automate-mobility-service-installation"></a>Instalação do Serviço de Mobilidade Automatizada

Este artigo descreve como automatizar a instalação e as atualizações do agente do Serviço de Mobilidade na [Recuperação do Sítio Azure.](site-recovery-overview.md)

Quando implementar a Recuperação do Local para a recuperação de desastres dos VMware VMs e servidores físicos para o Azure, instale o agente do Serviço de Mobilidade em cada máquina que pretende replicar. O Serviço de Mobilidade captura os dados escritos na máquina e reencaminha-os para o servidor de processo de recuperação do site para replicação. Pode implantar o Serviço de Mobilidade de várias formas:

- **Instalação push**: Deixe a Recuperação do Local instalar o agente de serviço de mobilidade quando ativar a replicação de uma máquina no portal Azure.
- **Instalação manual**: Instale manualmente o serviço de mobilidade em cada máquina. [Saiba mais](vmware-physical-mobility-service-overview.md) sobre a instalação manual e de impulso.
- **Implementação automatizada**: Automatizar a instalação com ferramentas de implementação de software como o Microsoft Endpoint Configuration Manager ou ferramentas de terceiros, como o JetPatch.

A instalação e atualização automatizada fornecem uma solução se:

- A sua organização não permite a instalação de push em servidores protegidos.
- A política da sua empresa exige que as palavras-passe sejam alteradas periodicamente. Tem de especificar uma palavra-passe para a instalação push.
- A sua política de segurança não permite adicionar exceções de firewall para máquinas específicas.
- Está a agir como um prestador de serviços de hospedagem e não quer fornecer credenciais de máquina de clientes necessárias para a instalação de push com a Recuperação do Site.
- É necessário escalar as instalações de agentes em muitos servidores simultaneamente.
- Pretende agendar instalações e atualizações durante as janelas de manutenção planeadas.

## <a name="prerequisites"></a>Pré-requisitos

Para automatizar a instalação, necessita dos seguintes itens:

- Uma solução de instalação de software implantada, como [o Gestor de Configuração](/configmgr/) ou [o JetPatch.](https://jetpatch.com/microsoft-azure-site-recovery/)
- Pré-requisitos de implementação em vigor em [Azure](tutorial-prepare-azure.md) e no local para recuperação [de desastres](vmware-azure-tutorial-prepare-on-premises.md) VMware, ou para recuperação de desastres [de servidor físico.](physical-azure-disaster-recovery.md) Reveja os requisitos de apoio para a recuperação de [desastres.](vmware-physical-azure-support-matrix.md)

## <a name="prepare-for-automated-deployment"></a>Preparar para implantação automatizada

A tabela seguinte resume ferramentas e processos para automatizar a implantação do Serviço de Mobilidade.

**Ferramenta** | **Detalhes** | **Instruções**
--- | --- | ---
**Configuration Manager** | 1. Verifique se tem os [pré-requisitos acima indicados.](#prerequisites) <br/><br/> 2. Implementar a recuperação de desastres configurando o ambiente de origem, incluindo o descarregamento de um ficheiro OVA para implantar o servidor de configuração de recuperação do site como VMware VM usando um modelo OVF.<br/><br/> 3. Regista o servidor de configuração com o serviço de Recuperação do Site, configura o ambiente Azure alvo e configura uma política de replicação.<br/><br/> 4. Para a implantação automatizada do Serviço de Mobilidade, cria uma partilha de rede contendo as palavras de passe do servidor de configuração e ficheiros de instalação do Serviço de Mobilidade.<br/><br/> 5. Cria um pacote de Gestor de Configuração que contém a instalação ou atualizações e prepara-se para a implantação do Serviço de Mobilidade.<br/><br/> 6. Pode então permitir a replicação do Azure para as máquinas que instalam o Serviço de Mobilidade. | [Automatize com Gestor de Configuração](#automate-with-configuration-manager)
**JetPatch** | 1. Verifique se tem os [pré-requisitos acima indicados.](#prerequisites) <br/><br/> 2. Implementar a recuperação de desastres configurando o ambiente de origem, incluindo o descarregamento e implementação do Gestor de Agente JetPatch para a recuperação do local de Azure no seu ambiente de recuperação do site, utilizando um modelo OVF.<br/><br/> 3. Regista o servidor de configuração com recuperação do site, configura o ambiente Azure alvo e configura uma política de replicação.<br/><br/> 4. Para implantação automatizada, inicialize e complete a configuração do Gestor de Agente JetPatch.<br/><br/> 5. No JetPatch pode criar uma política de Recuperação de Sítio para automatizar a implementação e a atualização do agente do Serviço de Mobilidade. <br/><br/> 6. Pode então permitir a replicação do Azure para as máquinas que instalam o Serviço de Mobilidade. | [Automatize com o Gestor de Agente JetPatch](https://jetpatch.com/microsoft-azure-site-recovery-deployment-guide/)<br/><br/> [Instalação de agente de resolução de problemas no JetPatch](https://kc.jetpatch.com/hc/articles/360035981812)

## <a name="automate-with-configuration-manager"></a>Automatize com Gestor de Configuração

### <a name="prepare-the-installation-files"></a>Preparar os ficheiros de instalação

1. Certifique-se de ter os pré-requisitos no lugar.
1. Crie uma partilha de ficheiros de rede segura (partilha SMB) que possa ser acedida pela máquina que executa o servidor de configuração.
1. No Gestor de Configuração, [categorize os servidores](/sccm/core/clients/manage/collections/automatically-categorize-devices-into-collections) nos quais pretende instalar ou atualizar o Serviço de Mobilidade. Uma coleção deve conter todos os servidores do Windows, o outro todos os servidores Linux.
1. Na partilha de rede, crie uma pasta:

   - Para a instalação em máquinas Windows, crie uma pasta chamada _MobSvcWindows_.
   - Para a instalação em máquinas Linux, crie uma pasta chamada _MobSvcLinux_.

1. Inscreva-se na máquina do servidor de configuração.
1. Na máquina do servidor de configuração, abra um pedido de comando administrativo.
1. Para gerar o ficheiro de palavras-passe, execute este comando:

    ```Console
    cd %ProgramData%\ASR\home\svsystems\bin
    genpassphrase.exe -v > MobSvc.passphrase
    ```

1. Copie o ficheiro _MobSvc.passphrase_ para a pasta Windows e para a pasta Linux.
1. Para navegar na pasta que contém os ficheiros de instalação, execute este comando:

    ```Console
    cd %ProgramData%\ASR\home\svsystems\pushinstallsvc\repository
    ```

1. Copie estes ficheiros de instalação para a partilha de rede:

   - Para windows, copie _Microsoft-ASR_UA_version_Windows_GA_date_Release.exe_ para _MobSvcWindows_.
   - Para o Linux, copie os seguintes ficheiros para _MobSvcLinux:_
     - _Microsoft-ASR_UARHEL6-64release.tar.gz_
     - _Microsoft-ASR_UARHEL7-64release.tar.gz_
     - _Microsoft-ASR_UASLES11-SP3-64release.tar.gz_
     - _Microsoft-ASR_UASLES11-SP4-64release.tar.gz_
     - _Microsoft-ASR_UAOL6-64release.tar.gz_
     - _Microsoft-ASR_UAUBUNTU-14.04-64release.tar.gz_

1. Conforme descrito nos seguintes procedimentos, copie o código para as pastas Windows ou Linux. Estamos assumindo que:

   - O endereço IP do servidor de configuração é `192.168.3.121` .
   - A partilha de ficheiros de rede segura é `\\ContosoSecureFS\MobilityServiceInstallers` .

### <a name="copy-code-to-the-windows-folder"></a>Código de cópia para a pasta Do Windows

Copiar o seguinte código:

- Guarde o código na pasta _MobSvcWindows_ como _install.bat_.
- Substitua os `[CSIP]` espaços reservados neste script pelos valores reais do endereço IP do seu servidor de configuração.
- O script suporta novas instalações do agente do Serviço de Mobilidade e atualizações para agentes que já estão instalados.

```DOS
Time /t >> C:\Temp\logfile.log
REM ==================================================
REM ==== Clean up the folders ========================
RMDIR /S /q %temp%\MobSvc
MKDIR %Temp%\MobSvc
MKDIR C:\Temp
REM ==================================================

REM ==== Copy new files ==============================
COPY M*.* %Temp%\MobSvc
CD %Temp%\MobSvc
REN Micro*.exe MobSvcInstaller.exe
REM ==================================================

REM ==== Extract the installer =======================
MobSvcInstaller.exe /q /x:%Temp%\MobSvc\Extracted
REM ==== Wait 10s for extraction to complete =========
TIMEOUT /t 10
REM =================================================

REM ==== Perform installation =======================
REM =================================================

CD %Temp%\MobSvc\Extracted
whoami >> C:\Temp\logfile.log
SET PRODKEY=HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Uninstall
REG QUERY %PRODKEY%\{275197FC-14FD-4560-A5EB-38217F80CBD1}
IF NOT %ERRORLEVEL% EQU 0 (
    echo "Product is not installed. Goto INSTALL." >> C:\Temp\logfile.log
    GOTO :INSTALL
) ELSE (
    echo "Product is installed." >> C:\Temp\logfile.log

    echo "Checking for Post-install action status." >> C:\Temp\logfile.log
    GOTO :POSTINSTALLCHECK
)

:POSTINSTALLCHECK
    REG QUERY "HKLM\SOFTWARE\Wow6432Node\InMage Systems\Installed Products\5" /v "PostInstallActions" | Find "Succeeded"
    If %ERRORLEVEL% EQU 0 (
        echo "Post-install actions succeeded. Checking for Configuration status." >> C:\Temp\logfile.log
        GOTO :CONFIGURATIONCHECK
    ) ELSE (
        echo "Post-install actions didn't succeed. Goto INSTALL." >> C:\Temp\logfile.log
        GOTO :INSTALL
    )

:CONFIGURATIONCHECK
    REG QUERY "HKLM\SOFTWARE\Wow6432Node\InMage Systems\Installed Products\5" /v "AgentConfigurationStatus" | Find "Succeeded"
    If %ERRORLEVEL% EQU 0 (
        echo "Configuration has succeeded. Goto UPGRADE." >> C:\Temp\logfile.log
        GOTO :UPGRADE
    ) ELSE (
        echo "Configuration didn't succeed. Goto CONFIGURE." >> C:\Temp\logfile.log
        GOTO :CONFIGURE
    )


:INSTALL
    echo "Perform installation." >> C:\Temp\logfile.log
    UnifiedAgent.exe /Role MS /InstallLocation "C:\Program Files (x86)\Microsoft Azure Site Recovery" /Platform "VmWare" /Silent
    IF %ERRORLEVEL% EQU 0 (
        echo "Installation has succeeded." >> C:\Temp\logfile.log
        (GOTO :CONFIGURE)
    ) ELSE (
        echo "Installation has failed." >> C:\Temp\logfile.log
        GOTO :ENDSCRIPT
    )

:CONFIGURE
    echo "Perform configuration." >> C:\Temp\logfile.log
    cd "C:\Program Files (x86)\Microsoft Azure Site Recovery\agent"
    UnifiedAgentConfigurator.exe  /CSEndPoint "[CSIP]" /PassphraseFilePath %Temp%\MobSvc\MobSvc.passphrase
    IF %ERRORLEVEL% EQU 0 (
        echo "Configuration has succeeded." >> C:\Temp\logfile.log
    ) ELSE (
        echo "Configuration has failed." >> C:\Temp\logfile.log
    )
    GOTO :ENDSCRIPT

:UPGRADE
    echo "Perform upgrade." >> C:\Temp\logfile.log
    UnifiedAgent.exe /Platform "VmWare" /Silent
    IF %ERRORLEVEL% EQU 0 (
        echo "Upgrade has succeeded." >> C:\Temp\logfile.log
    ) ELSE (
        echo "Upgrade has failed." >> C:\Temp\logfile.log
    )
    GOTO :ENDSCRIPT

:ENDSCRIPT
    echo "End of script." >> C:\Temp\logfile.log
```

### <a name="copy-code-to-the-linux-folder"></a>Código de cópia para a pasta Linux

Copiar o seguinte código:

- Guarde o código na pasta _MobSvcLinux_ _install_linux.sh_.
- Substitua os `[CSIP]` espaços reservados neste script pelos valores reais do endereço IP do seu servidor de configuração.
- O script suporta novas instalações do agente do Serviço de Mobilidade e atualizações para agentes que já estão instalados.

```Bash
#!/usr/bin/env bash

rm -rf /tmp/MobSvc
mkdir -p /tmp/MobSvc
INSTALL_DIR='/usr/local/ASR'
VX_VERSION_FILE='/usr/local/.vx_version'

echo "=============================" >> /tmp/MobSvc/sccm.log
echo `date` >> /tmp/MobSvc/sccm.log
echo "=============================" >> /tmp/MobSvc/sccm.log

if [ -f /etc/oracle-release ] && [ -f /etc/redhat-release ]; then
    if grep -q 'Oracle Linux Server release 6.*' /etc/oracle-release; then
        if uname -a | grep -q x86_64; then
            OS="OL6-64"
            echo $OS >> /tmp/MobSvc/sccm.log
            cp *OL6*.tar.gz /tmp/MobSvc
        fi
    fi
elif [ -f /etc/redhat-release ]; then
    if grep -q 'Red Hat Enterprise Linux Server release 6.* (Santiago)' /etc/redhat-release || \
        grep -q 'CentOS Linux release 6.* (Final)' /etc/redhat-release || \
        grep -q 'CentOS release 6.* (Final)' /etc/redhat-release; then
        if uname -a | grep -q x86_64; then
            OS="RHEL6-64"
            echo $OS >> /tmp/MobSvc/sccm.log
            cp *RHEL6*.tar.gz /tmp/MobSvc
        fi
    elif grep -q 'Red Hat Enterprise Linux Server release 7.* (Maipo)' /etc/redhat-release || \
        grep -q 'CentOS Linux release 7.* (Core)' /etc/redhat-release; then
        if uname -a | grep -q x86_64; then
            OS="RHEL7-64"
            echo $OS >> /tmp/MobSvc/sccm.log
            cp *RHEL7*.tar.gz /tmp/MobSvc
                fi
    fi
elif [ -f /etc/SuSE-release ] && grep -q 'VERSION = 11' /etc/SuSE-release; then
    if grep -q "SUSE Linux Enterprise Server 11" /etc/SuSE-release && grep -q 'PATCHLEVEL = 3' /etc/SuSE-release; then
        if uname -a | grep -q x86_64; then
            OS="SLES11-SP3-64"
            echo $OS >> /tmp/MobSvc/sccm.log
            cp *SLES11-SP3*.tar.gz /tmp/MobSvc
        fi
    elif grep -q "SUSE Linux Enterprise Server 11" /etc/SuSE-release && grep -q 'PATCHLEVEL = 4' /etc/SuSE-release; then
        if uname -a | grep -q x86_64; then
            OS="SLES11-SP4-64"
            echo $OS >> /tmp/MobSvc/sccm.log
            cp *SLES11-SP4*.tar.gz /tmp/MobSvc
        fi
    fi
elif [ -f /etc/lsb-release ] ; then
    if grep -q 'DISTRIB_RELEASE=14.04' /etc/lsb-release ; then
       if uname -a | grep -q x86_64; then
           OS="UBUNTU-14.04-64"
           echo $OS >> /tmp/MobSvc/sccm.log
           cp *UBUNTU-14*.tar.gz /tmp/MobSvc
       fi
    fi
else
    exit 1
fi

if [ -z "$OS" ]; then
    exit 1
fi

Install()
{
    echo "Perform Installation." >> /tmp/MobSvc/sccm.log
    ./install -q -d ${INSTALL_DIR} -r MS -v VmWare
    RET_VAL=$?
    echo "Installation Returncode: $RET_VAL" >> /tmp/MobSvc/sccm.log
    if [ $RET_VAL -eq 0 ]; then
        echo "Installation has succeeded. Proceed to configuration." >> /tmp/MobSvc/sccm.log
        Configure
    else
        echo "Installation has failed." >> /tmp/MobSvc/sccm.log
        exit $RET_VAL
    fi
}

Configure()
{
    echo "Perform configuration." >> /tmp/MobSvc/sccm.log
    ${INSTALL_DIR}/Vx/bin/UnifiedAgentConfigurator.sh -i [CSIP] -P MobSvc.passphrase
    RET_VAL=$?
    echo "Configuration Returncode: $RET_VAL" >> /tmp/MobSvc/sccm.log
    if [ $RET_VAL -eq 0 ]; then
        echo "Configuration has succeeded." >> /tmp/MobSvc/sccm.log
    else
        echo "Configuration has failed." >> /tmp/MobSvc/sccm.log
        exit $RET_VAL
    fi
}

Upgrade()
{
    echo "Perform Upgrade." >> /tmp/MobSvc/sccm.log
    ./install -q -v VmWare
    RET_VAL=$?
    echo "Upgrade Returncode: $RET_VAL" >> /tmp/MobSvc/sccm.log
    if [ $RET_VAL -eq 0 ]; then
        echo "Upgrade has succeeded." >> /tmp/MobSvc/sccm.log
    else
        echo "Upgrade has failed." >> /tmp/MobSvc/sccm.log
        exit $RET_VAL
    fi
}

cp MobSvc.passphrase /tmp/MobSvc
cd /tmp/MobSvc

tar -zxvf *.tar.gz

if [ -e ${VX_VERSION_FILE} ]; then
    echo "${VX_VERSION_FILE} exists. Checking for configuration status." >> /tmp/MobSvc/sccm.log
    agent_configuration=$(grep ^AGENT_CONFIGURATION_STATUS "${VX_VERSION_FILE}" | cut -d"=" -f2 | tr -d " ")
    echo "agent_configuration=$agent_configuration" >> /tmp/MobSvc/sccm.log
     if [ "$agent_configuration" == "Succeeded" ]; then
        echo "Agent is already configured. Proceed to Upgrade." >> /tmp/MobSvc/sccm.log
        Upgrade
    else
        echo "Agent is not configured. Proceed to Configure." >> /tmp/MobSvc/sccm.log
        Configure
    fi
else
    Install
fi

cd /tmp

```

### <a name="create-a-package"></a>Create a package (Criar um pacote)

1. Inscreva-se na consola Do Gestor de Configuração e vá aos  >  Pacotes **de Gestão de Aplicações da** Biblioteca  >  **de** Software .
1. **Pacotes** de clique à direita  >  **Criam Pacote**.
1. Forneça detalhes do pacote, incluindo um nome, descrição, fabricante, idioma e versão.
1. Selecione **Este pacote contém ficheiros de origem**.
1. Clique **em Procurar** e selecione a partilha de rede e a pasta que contém o instalador relevante _(MobSvcWindows_ ou _MobSvcLinux)._ Em seguida, selecione **Seguinte**.

   ![Screenshot do assistente de pacote e programa criar](./media/vmware-azure-mobility-install-configuration-mgr/create_sccm_package.png)

1. Em **Escolha o tipo de programa que pretende criar** página, selecione Programa **Standard**  >  **Next**.

   ![Screenshot do programa Create Package e programa que mostra a opção Programa Padrão.](./media/vmware-azure-mobility-install-configuration-mgr/sccm-standard-program.png)

1. Em **Especificar informações sobre esta página de programa padrão,** especifique os seguintes valores:

    **Parâmetro** | **Valor do Windows** | **Valor Linux**
    --- | --- | ---
    **Nome** | Instalar o Microsoft Azure Mobility Service (Windows) | Instale o Microsoft Azure Mobility Service (Linux).
    **Linha de comandos** | install.bat | ./install_linux.sh
    **Programa pode correr** | Se um utilizador está ou não ligado | Se um utilizador está ou não ligado
    **Outros parâmetros** | Use a definição predefinição | Use a definição predefinição

   ![Screenshot que mostra as informações que pode especificar para o programa padrão.](./media/vmware-azure-mobility-install-configuration-mgr/sccm-program-properties.png)

1. Em **Especificar os requisitos para este programa padrão,** faça as seguintes tarefas:

   - Para máquinas Windows, selecione **Este programa só pode ser executado em plataformas especificadas.** Em seguida, selecione os [sistemas operativos Windows suportados](vmware-physical-azure-support-matrix.md#replicated-machines) e selecione **Next**.
   - Para máquinas Linux, selecione **Este programa pode ser executado em qualquer plataforma**. Em seguida, selecione **Seguinte**.

1. Conclua o assistente.

### <a name="deploy-the-package"></a>Implementar o pacote

1. Na consola 'Gestor de Configuração', clique com o botão direito no pacote e selecione **Distribute Content**.

   ![Screenshot da consola do Gestor de Configuração](./media/vmware-azure-mobility-install-configuration-mgr/sccm_distribute.png)

1. Selecione os pontos de distribuição em que as embalagens devem ser copiadas. [Saiba mais](/sccm/core/servers/deploy/configure/install-and-configure-distribution-points).
1. Conclua o assistente. Em seguida, o pacote começa a replicar-se nos pontos de distribuição especificados.
1. Depois de terminar a distribuição do pacote, clique com o botão direito na embalagem > **Implementar**.

   ![Screenshot da consola 'Gestor de Configuração' que mostra a opção de menu Implementar.](./media/vmware-azure-mobility-install-configuration-mgr/sccm_deploy.png)

1. Selecione a coleção de dispositivos Windows ou Linux que criou anteriormente.
1. Na página **de destino do conteúdo,** selecione **Pontos de Distribuição**.
1. Em **Especificar as definições para controlar a forma como este software é implementado** página, defina a **Finalidade** para **Requerido**.

   ![Screenshot do assistente de software de implementação](./media/vmware-azure-mobility-install-configuration-mgr/sccm-deploy-select-purpose.png)

1. Em **Especificar o calendário para esta implementação,** estabeleça um horário. [Saiba mais](/sccm/apps/deploy-use/deploy-applications#bkmk_deploy-sched).

   - O Serviço de Mobilidade está instalado de acordo com o horário especificado.
   - Para evitar reinicializações desnecessárias, agende a instalação do pacote durante a janela de manutenção mensal ou a janela de atualizações de software.

1. Na página **Pontos de Distribuição,** configurar as definições e terminar o assistente.
1. Monitorize o progresso da implementação na consola Do Gestor de Configuração. Vá a **Implementações de**  >  **Monitorização**  >  _\<your package name\>_ .

### <a name="uninstall-the-mobility-service"></a>Desinstalar o Serviço de Mobilidade

Pode criar pacotes de Gestor de Configuração para desinstalar o Serviço de Mobilidade. Por exemplo, o seguinte script desinstala o Serviço de Mobilidade:

```DOS
Time /t >> C:\logfile.log
REM ==================================================
REM ==== Check if Mob Svc is already installed =======
REM ==== If not installed no operation required ========
REM ==== Else run uninstall command =====================
REM ==== {275197FC-14FD-4560-A5EB-38217F80CBD1} is ====
REM ==== guid for Mob Svc Installer ====================
whoami >> C:\logfile.log
NET START | FIND "InMage Scout Application Service"
IF  %ERRORLEVEL% EQU 1 (GOTO :INSTALL) ELSE GOTO :UNINSTALL
:NOOPERATION
                echo "No Operation Required." >> c:\logfile.log
                GOTO :ENDSCRIPT
:UNINSTALL
                echo "Uninstall" >> C:\logfile.log
                MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1} /L+*V "C:\ProgramData\ASRSetupLogs\UnifiedAgentMSIUninstall.log"
:ENDSCRIPT
```

## <a name="next-steps"></a>Passos seguintes

[Ativar a replicação](vmware-azure-enable-replication.md) dos VMs.
