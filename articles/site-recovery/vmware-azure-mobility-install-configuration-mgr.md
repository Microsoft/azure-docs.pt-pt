---
title: Automate serviço de mobilidade para recuperação de desastres em recuperação do local de Azure
description: Como instalar automaticamente o Serviço de Mobilidade para recuperação de desastres vMware/servidor físico com recuperação do site Azure.
author: Rajeswari-Mamilla
ms.topic: how-to
ms.date: 2/5/2020
ms.author: ramamill
ms.openlocfilehash: f24d321e882024d324435498adf11694037547f7
ms.sourcegitcommit: 79cbd20a86cd6f516acc3912d973aef7bf8c66e4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/14/2020
ms.locfileid: "77252232"
---
# <a name="automate-mobility-service-installation"></a>Instalação do Selfmate Mobility Service

Este artigo descreve como automatizar a instalação e atualizações para o agente do Serviço de Mobilidade na Recuperação do [Local Azure](site-recovery-overview.md).

Quando implementa a Recuperação do Site para recuperação de desastres de VMware vMs e servidores físicos no Azure, instala o agente de Serviço de Mobilidade em cada máquina que pretende replicar. O Serviço de Mobilidade captura os dados escritos na máquina e encaminha-os para o servidor do processo de recuperação do site para replicação. Pode implementar o Serviço de Mobilidade de algumas formas:

- **Instalação de impulso**: Deixe a Recuperação do Local instalar o agente de serviço de Mobilidade quando ativa restar uma máquina no portal Azure.
- **Instalação manual**: Instale manualmente o serviço de Mobilidade em cada máquina. [Saiba mais](vmware-physical-mobility-service-overview.md) sobre o impulso e a instalação manual.
- **Implementação automatizada**: Amate a instalação com ferramentas de implementação de software, como o Microsoft Endpoint Configuration Manager ou ferramentas de terceiros, como o JetPatch.

A instalação e a atualização automatizadas fornece uma solução se:

- A sua organização não permite a instalação de impulsoem servidores protegidos.
- A política da sua empresa exige que as palavras-passe sejam alteradas periodicamente. Tem de especificar uma palavra-passe para a instalação push.
- A sua política de segurança não permite adicionar exceções à firewall para máquinas específicas.
- Está a agir como prestador de serviços de hospedagem e não quer fornecer credenciais de máquinas de clientes que são necessárias para a instalação de impulsos com a Recuperação do Site.
- Precisa de dimensionar as instalações do agente para muitos servidores simultaneamente.
- Deverá agendar instalações e upgrades durante as janelas de manutenção planeadas.

## <a name="prerequisites"></a>Pré-requisitos

Para automatizar a instalação, necessita dos seguintes itens:

- Uma solução de instalação de software implementada, como O Gestor de [Configuração](/configmgr/) ou [JetPatch.](https://jetpatch.com/microsoft-azure-site-recovery/)
- Requisitos de implementação em vigor em [Azure](tutorial-prepare-azure.md) e no local para recuperação [de desastres](vmware-azure-tutorial-prepare-on-premises.md) vMware, ou para recuperação de desastres de [servidor físico.](physical-azure-disaster-recovery.md) Reveja os requisitos de apoio para a recuperação de [desastres.](vmware-physical-azure-support-matrix.md)

## <a name="prepare-for-automated-deployment"></a>Preparar para a implantação automatizada

A tabela que se segue resume ferramentas e processos para automatizar a implantação do Serviço de Mobilidade.

**Ferramenta** | **Detalhes** | **Instruções**
--- | --- | ---
**Configuration Manager** | 1. Verifique se tem os [pré-requisitos](#prerequisites) listados acima. <br/><br/> 2. Implemente a recuperação de desastres configurando o ambiente de origem, incluindo o download de um ficheiro OVA para implementar o servidor de configuração de recuperação do site como vMware VM utilizando um modelo OVF.<br/><br/> 3. Registe o servidor de configuração com o serviço de recuperação do site, configure o ambiente-alvo Do Azure e configure uma política de replicação.<br/><br/> 4. Para a implementação automatizada do Serviço de Mobilidade, cria uma quota de rede contendo a frase-passe do servidor de configuração e os ficheiros de instalação do Serviço de Mobilidade.<br/><br/> 5. Cria um pacote de Gestor de Configuração contendo a instalação ou atualizações e prepara-se para a implantação do Serviço de Mobilidade.<br/><br/> 6. Em seguida, pode ativar a replicação ao Azure para as máquinas que têm o Serviço de Mobilidade instalado. | [Automatizar com Gestor de Configuração](#automate-with-configuration-manager)
**JetPatch** | 1. Verifique se tem os [pré-requisitos](#prerequisites) listados acima. <br/><br/> 2. Implemente a recuperação de desastres, criando o ambiente de origem, incluindo o descarregamento e implementação do JetPatch Agent Manager para a recuperação do site azure no ambiente de recuperação do site, utilizando um modelo OVF.<br/><br/> 3. Registe o servidor de configuração com a Recuperação do Site, configure o ambiente-alvo Do Azure e configure uma política de replicação.<br/><br/> 4. Para a implementação automatizada, inicialize e complete a configuração do JetPatch Agent Manager.<br/><br/> 5. No JetPatch pode criar uma política de recuperação do site para automatizar a implementação e atualização do agente do Serviço de Mobilidade. <br/><br/> 6. Em seguida, pode ativar a replicação ao Azure para as máquinas que têm o Serviço de Mobilidade instalado. | [Automatizar com o JetPatch Agent Manager](https://jetpatch.com/microsoft-azure-site-recovery-deployment-guide/)<br/><br/> [Instalação de agente de resolução de problemas no JetPatch](https://kc.jetpatch.com/hc/articles/360035981812)

## <a name="automate-with-configuration-manager"></a>Automatizar com Gestor de Configuração

### <a name="prepare-the-installation-files"></a>Preparar os ficheiros de instalação

1. Certifique-se de que tem os pré-requisitos no lugar.
1. Crie uma partilha de ficheiros de rede segura (partilha SMB) que possa ser acedida pela máquina que executa o servidor de configuração.
1. No 'Gestor de Configuração', [categorize os servidores](/sccm/core/clients/manage/collections/automatically-categorize-devices-into-collections) nos quais pretende instalar ou atualizar o Serviço de Mobilidade. Uma coleção deve conter todos os servidores do Windows, o outro todos os servidores Linux.
1. Na partilha da rede, crie uma pasta:

   - Para a instalação em máquinas Windows, crie uma pasta chamada _MobSvcWindows_.
   - Para a instalação em máquinas Linux, crie uma pasta chamada _MobSvcLinux_.

1. Inscreva-se na máquina do servidor de configuração.
1. Na máquina do servidor de configuração, abra um pedido de comando administrativo.
1. Para gerar o ficheiro de frase sinuosa, execute este comando:

    ```Console
    cd %ProgramData%\ASR\home\svsystems\bin
    genpassphrase.exe -v > MobSvc.passphrase
    ```

1. Copie o ficheiro _MobSvc.passphrase_ para a pasta Windows e a pasta Linux.
1. Para navegar na pasta que contém os ficheiros de instalação, execute este comando:

    ```Console
    cd %ProgramData%\ASR\home\svsystems\pushinstallsvc\repository
    ```

1. Copie estes ficheiros de instalação para a partilha da rede:

   - Para windows, copie _Microsoft-ASR_UA_version_Windows_GA_date_Release.exe_ para _MobSvcWindows_.
   - Para o Linux, copie os seguintes ficheiros para _MobSvcLinux:_
     - _Microsoft-ASR_UARHEL6-64release.tar.gz_
     - _Microsoft-ASR_UARHEL7-64release.tar.gz_
     - _Microsoft-ASR_UASLES11-SP3-64release.tar.gz_
     - _Microsoft-ASR_UASLES11-SP4-64release.tar.gz_
     - _Microsoft-ASR_UAOL6-64release.tar.gz_
     - _Microsoft-ASR_UAUBUNTU-14.04-64release.tar.gz_

1. Tal como descrito nos seguintes procedimentos, copie o código para as pastas Windows ou Linux. Assumimos que:

   - O endereço IP do servidor de configuração está `192.168.3.121`.
   - A partilha de ficheiros de rede segura é `\\ContosoSecureFS\MobilityServiceInstallers`.

### <a name="copy-code-to-the-windows-folder"></a>Copiar código para a pasta Windows

Copiar o seguinte código:

- Guarde o código na pasta _MobSvcWindows_ como _instalar.bat_.
- Substitua os espaços reservados `[CSIP]` neste script com os valores reais do endereço IP do seu servidor de configuração.
- O script suporta novas instalações do agente do Serviço de Mobilidade e atualiza para agentes que já estão instalados.

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

- Guarde o código na pasta _MobSvcLinux_ como _install_linux.sh_.
- Substitua os espaços reservados `[CSIP]` neste script com os valores reais do endereço IP do seu servidor de configuração.
- O script suporta novas instalações do agente do Serviço de Mobilidade e atualiza para agentes que já estão instalados.

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

### <a name="create-a-package"></a>Criar um pacote

1. Inscreva-se na consola do Gestor de Configuração e vá à Biblioteca de **Software** > **Gestão** de Aplicações > **Pacotes.**
1. Clique à direita **Pacotes** > **Criar Pacote**.
1. Forneça detalhes do pacote, incluindo um nome, descrição, fabricante, idioma e versão.
1. Selecione **Este pacote contém ficheiros de origem**.
1. Clique em **Navegar**, e selecione a partilha e pasta da rede que contém o instalador relevante (_MobSvcWindows_ ou _MobSvcLinux_). Em seguida, selecione **Seguinte**.

   ![Screenshot do criar pacote e assistente de programa](./media/vmware-azure-mobility-install-configuration-mgr/create_sccm_package.png)

1. Em **Escolha o tipo de programa que pretende criar** página, selecione Programa **Standard** > **Seguinte**.

   ![Screenshot do criar pacote e assistente de programa](./media/vmware-azure-mobility-install-configuration-mgr/sccm-standard-program.png)

1. Em **Especificar informações sobre esta** página padrão do programa, especifique os seguintes valores:

    **Parâmetro** | **Valor do Windows** | **Valor linux**
    --- | --- | ---
    **Nome** | Instalar o Microsoft Azure Mobility Service (Windows) | Instale o Microsoft Azure Mobility Service (Linux).
    **Linha de comandos** | install.bat | ./install_linux.sh
    **Programa pode correr** | Quer um utilizador tenha ou não sessão iniciada | Quer um utilizador tenha ou não sessão iniciada
    **Outros parâmetros** | Utilize a definição predefinida | Utilize a definição predefinida

   ![Screenshot do criar pacote e assistente de programa](./media/vmware-azure-mobility-install-configuration-mgr/sccm-program-properties.png)

1. Em **especificar os requisitos para este programa padrão,** faça as seguintes tarefas:

   - Para máquinas Windows, selecione **Este programa só pode funcionar em plataformas especificadas**. Em seguida, selecione os [sistemas operativos Windows suportados](vmware-physical-azure-support-matrix.md#replicated-machines) e selecione **Next**.
   - Para máquinas Linux, selecione **Este programa pode ser executado em qualquer plataforma**. Em seguida, selecione **Seguinte**.

1. Termine o feiticeiro.

### <a name="deploy-the-package"></a>Implementar o pacote

1. Na consola 'Gestor de Configuração', clique no pacote e selecione **Distribute Content**.

   ![Screenshot da consola de Gestor de Configuração](./media/vmware-azure-mobility-install-configuration-mgr/sccm_distribute.png)

1. Selecione os pontos de distribuição em que as embalagens devem ser copiadas. [Saiba mais](/sccm/core/servers/deploy/configure/install-and-configure-distribution-points).
1. Conclua o assistente. O pacote começa então a replicar-se para os pontos de distribuição especificados.
1. Depois de terminar a distribuição do pacote, clique à direita no pacote > **Implemente**.

   ![Screenshot da consola de Gestor de Configuração](./media/vmware-azure-mobility-install-configuration-mgr/sccm_deploy.png)

1. Selecione a coleção de dispositivos Windows ou Linux que criou anteriormente.
1. Na página **de destino Especificar o conteúdo,** selecione **Pontos**de Distribuição .
1. Em **especificar as definições para controlar a forma como este software é implantado** na página, definir o **propósito** para o **necessário**.

   ![Screenshot do assistente de software de implementação](./media/vmware-azure-mobility-install-configuration-mgr/sccm-deploy-select-purpose.png)

1. Em **especificar o calendário para esta implementação,** estabeleça um horário. [Saiba mais](/sccm/apps/deploy-use/deploy-applications#bkmk_deploy-sched).

   - O Serviço de Mobilidade está instalado de acordo com o horário que especifica.
   - Para evitar reboots desnecessários, agende a instalação do pacote durante a janela de manutenção mensal ou a janela de atualizações de software.

1. Na página Pontos de **Distribuição,** configure as definições e termine o assistente.
1. Monitorize o progresso de implementação na consola Do Gestor de Configuração. Vá para **monitorização** > **Implantações** >  _\<o seu nome de pacote\>_ .

### <a name="uninstall-the-mobility-service"></a>Desinstalar o Serviço de Mobilidade

Pode criar pacotes de Gestor de Configuração para desinstalar o Serviço de Mobilidade. Por exemplo, o seguinte guião desinstala o Serviço de Mobilidade:

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

[Ativar a replicação](vmware-azure-enable-replication.md) para VMs.
