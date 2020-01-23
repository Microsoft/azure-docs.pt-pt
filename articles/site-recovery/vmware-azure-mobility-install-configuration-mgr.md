---
title: Automatizar o serviço de mobilidade para recuperação de desastre de instalação no Azure Site Recovery
description: Como instalar automaticamente o serviço de mobilidade para a recuperação de desastre do VMware/Physical Server com o Azure Site Recovery.
author: Rajeswari-Mamilla
ms.topic: how-to
ms.date: 12/22/2019
ms.author: ramamill
ms.openlocfilehash: 43e6a39a52eb81573b4a4ba8ad63d48d0e51dedd
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/22/2020
ms.locfileid: "76514828"
---
# <a name="automate-mobility-service-installation"></a>Automatizar a instalação do serviço de mobilidade

Este artigo descreve como automatizar a instalação e as atualizações para o agente do serviço de mobilidade no [Azure site Recovery](site-recovery-overview.md).

Ao implantar Site Recovery para recuperação de desastre de VMs VMware locais e servidores físicos no Azure, você instala o agente do serviço de mobilidade em cada computador que deseja replicar. O serviço de mobilidade captura gravações de dados no computador e as encaminha para o servidor de processo de Site Recovery para replicação. Você pode implantar o serviço de mobilidade de duas maneiras:

- **Instalação por push**: permita que site Recovery instale o agente do serviço de mobilidade quando você habilitar a replicação para um computador no portal do Azure.
- **Instalação manual**: Instale o serviço de mobilidade manualmente em cada computador. [Saiba mais](/vmware-physical-mobility-service-overview.md) sobre a instalação manual e por push.
- **Implantação automatizada**: automatize a instalação com ferramentas de implantação de software, como o Microsoft Endpoint Configuration Manager ou ferramentas de terceiros, como Intigua JetPatch.

A instalação automatizada e a atualização fornece uma solução se:

- Sua organização não permite a instalação por push em servidores protegidos.
- A política de sua empresa requer que as senhas sejam alteradas periodicamente. Você precisa especificar uma senha para a instalação por push.
- Sua política de segurança não permite adicionar exceções de firewall para computadores específicos.
- Você está atuando como um provedor de serviços de hospedagem e não deseja fornecer as credenciais do computador do cliente que são necessárias para a instalação por push com o Site Recovery.
- Você precisa dimensionar a instalação do próprio servidor para vários servidores simultaneamente.
- Você deseja agendar instalações e atualizações durante as janelas de manutenção planejadas.



## <a name="prerequisites"></a>Pré-requisitos

Para a instalação automatizada, você precisará do seguinte:

- Uma solução de instalação de software implantada, como [Configuration Manager](https://docs.microsoft.com/configmgr/) ou [JetPatch](https://jetpatch.com/microsoft-azure-site-recovery/). 
-  Os pré-requisitos de implantação estão em vigor no [Azure](tutorial-prepare-azure.md) e [no local](vmware-azure-tutorial-prepare-on-premises.md) para a recuperação de desastres do VMware ou para a recuperação de desastres do [servidor físico](physical-azure-disaster-recovery.md) . Você também deve examinar [os requisitos de suporte](vmware-physical-azure-support-matrix.md) para recuperação de desastre.

## <a name="prepare-for-automated-deployment"></a>Preparar para implantação automatizada

A tabela a seguir resume as ferramentas e os processos para automatizar a implantação do serviço de mobilidade.

**Ferramenta** | **Detalhes** | **Instruções**
--- | --- | ---
**Configuration Manager** | 1. Verifique se você tem os [pré-requisitos](#prerequisites) listados acima em vigor. <br/><br/>2. implante a recuperação de desastres Configurando o ambiente de origem, incluindo o download de um arquivo OVA para implantar o servidor de configuração de Site Recovery como uma VM VMware usando um modelo OVF.<br/><br/> 2. você registra o servidor de configuração com o serviço de Site Recovery, configura o ambiente de destino do Azure e configura uma política de replicação.<br/><br/> 3. para a implantação automatizada do serviço de mobilidade, você cria um compartilhamento de rede que contém a senha do servidor de configuração e os arquivos de instalação do serviço de mobilidade.<br/><br/> 4. você cria um pacote de Configuration Manager que contém a instalação ou as atualizações e prepara para a implantação do serviço de mobilidade.<br/><br/> 5. você pode habilitar a replicação para o Azure para os computadores que têm o serviço de mobilidade instalado. | [Automatize com o Configuration Manager](#automate-with-configuration-manager).
**JetPatch** | 1. Verifique se você tem os [pré-requisitos](#prerequisites) listados acima em vigor. <br/><br/> 2. implante a recuperação de desastres Configurando o ambiente de origem, incluindo baixar e implantar JetPatch Gerenciador de Agentes para Azure Site Recovery em seu ambiente de Site Recovery, usando um modelo OVF.<br/><br/> 2. você registra o servidor de configuração com Site Recovery, configura o ambiente de destino do Azure e configura uma política de replicação.<br/><br/> 3. para implantação automatizada, inicialize e conclua a configuração de Gerenciador de Agentes JetPatch.<br/><br/> 4. no JetPatch, você pode criar uma política de Site Recovery para automatizar a implantação e a atualização do agente do serviço de mobilidade. <br/><br/> 5. você pode habilitar a replicação para o Azure para os computadores que têm o serviço de mobilidade instalado. | [Automatize com o JetPatch Gerenciador de agentes](https://jetpatch.com/microsoft-azure-site-recovery-deployment-guide/).<br/><br/> [Solucionar problemas de instalação do agente](https://kc.jetpatch.com/hc/articles/360035981812) no JetPatch.

## <a name="automate-with-configuration-manager"></a>Automatizar com o Configuration Manager

### <a name="prepare-the-installation-files"></a>Preparar os arquivos de instalação

1. Verifique se você tem os pré-requisitos em vigor.
2. Crie um compartilhamento de arquivos de rede segura (compartilhamento SMB) que possa ser acessado pelo computador que executa o servidor de configuração.
3. Em Configuration Manager, [categorize os servidores](https://docs.microsoft.com/sccm/core/clients/manage/collections/automatically-categorize-devices-into-collections) nos quais você deseja instalar ou atualizar o serviço de mobilidade. Uma coleção deve conter todos os servidores Windows, o outro servidor Linux. 
5. No compartilhamento de rede, crie uma pasta:

    - Para a instalação em computadores Windows, crie uma pasta **MobSvcWindows**.
    - Para a instalação em computadores Linux, crie uma pasta **MobSvcLinux**.

6. Entre no computador do servidor de configuração.
7. No computador, abra um prompt de comando de administrador.
8. Execute este comando para gerar o arquivo de frase secreta:

    ```
    cd %ProgramData%\ASR\home\svsystems\bin
    genpassphrase.exe -v > MobSvc.passphrase
    ```
9. Copie o arquivo arquivo mobsvc. passphrase para a pasta do Windows e para a pasta do Linux.
10. Execute este comando para navegar até a pasta que contém os arquivos de instalação:

    ```
    cd %ProgramData%\ASR\home\svsystems\pushinstallsvc\repository
    ```

11. Copie esses arquivos de instalação para o compartilhamento de rede:

    - Para **MobSvcWindows**, copie **Microsoft-ASR_UA_version_Windows_GA_date_Release. exe**
    - Para **MobSvcLinux**, copie:
        - Microsoft-ASR_UA*RHEL6-64*versão. tar. gz
        - Microsoft-ASR_UA*RHEL7-64*versão. tar. gz
        - Microsoft-ASR_UA*SLES11-SP3-64*versão. tar. gz
        - Microsoft-ASR_UA*SLES11-SP4-64*versão. tar. gz
        - Microsoft-ASR_UA*OL6-64*versão. tar. gz
        - Microsoft-ASR_UA*Ubuntu-14.04-64*versão. tar. gz
      
12. Copie o código para as pastas do Windows ou do Linux, conforme descrito nos próximos procedimentos. Estamos supondo que:
    - O endereço IP do servidor de configuração é 192.168.3.121.
    - O compartilhamento de arquivos de rede segura é **\\\ContosoSecureFS\MobilityServiceInstallers**.

### <a name="copy-code-to-the-windows-folder"></a>Copiar o código para a pasta do Windows

Copie o código a seguir:

- Salve-o como **install. bat** na pasta **MobSvcWindows**
- Substitua os espaços reservados [CSIP] neste script pelos valores reais do endereço IP do seu servidor de configuração.
- O script dá suporte a novas instalações do agente do serviço de mobilidade e atualizações para agentes que já estão instalados.

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
### <a name="copy-code-to-the-linux-folder"></a>Copiar o código para a pasta do Linux

Copie o código a seguir:

- Salve-o como **install_linux. sh** na pasta **MobSvcLinux**
- Substitua os espaços reservados [CSIP] neste script pelos valores reais do endereço IP do seu servidor de configuração.
- O script dá suporte a novas instalações do agente do serviço de mobilidade e atualizações para agentes que já estão instalados.

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

1. Entre no console do Configuration Manager > **biblioteca de Software** > **pacotes**de > de **Gerenciamento de aplicativos** .
2. Clique com o botão direito do mouse em **pacotes** > **criar pacote**.
3. Forneça detalhes do pacote, incluindo nome, descrição, fabricante, idioma e versão.
4. Selecione **Este pacote contém arquivos de origem**.
5. Clique em **procurar**e selecione o compartilhamento de rede e a pasta que contém o instalador relevante (MobSvcWindows ou MobSvcLinux) e clique em **Avançar**.

   ![Captura de tela do assistente para criar pacote e programa](./media/vmware-azure-mobility-install-configuration-mgr/create_sccm_package.png)

7. Na página **escolha o tipo de programa que você deseja criar** , selecione **programa padrão** > **Avançar**.

   ![Captura de tela do assistente para criar pacote e programa](./media/vmware-azure-mobility-install-configuration-mgr/sccm-standard-program.png)

8. Em **especificar informações sobre esta página de programa padrão** , especifique os seguintes valores:

    **Parâmetro** | **Valor do Windows** | **Valor do Linux**
    --- | --- | ---
    **Nome** | Instalar o serviço de mobilidade de Microsoft Azure (Windows) | Instale o serviço de mobilidade de Microsoft Azure (Linux).
    **Linha de comandos** | install.bat | ./install_linux.sh
    **O programa pode ser executado** | Quer um utilizador tenha ou não sessão iniciada | Quer um utilizador tenha ou não sessão iniciada
    **Outros parâmetros** | Usar configuração padrão | Usar configuração padrão

   ![Captura de tela do assistente para criar pacote e programa](./media/vmware-azure-mobility-install-configuration-mgr/sccm-program-properties.png)

9. Em **especificar os requisitos para este programa padrão**, faça o seguinte:

    - Para computadores Windows, selecione **este programa pode ser executado somente em plataformas especificadas**. Em seguida, selecione os [sistemas operacionais Windows com suporte](vmware-physical-azure-support-matrix.md#replicated-machines). Clique depois em **Seguinte**.
    - Para computadores Linux, selecione **este programa pode ser executado em qualquer plataforma**. Clique depois em **Seguinte**.
   
10. Conclua o assistente.



### <a name="deploy-the-package"></a>Implantar o pacote

1. No console do Configuration Manager, clique com o botão direito do mouse no pacote > **distribuir conteúdo**.
   Captura de tela do ![Configuration Manager Console](./media/vmware-azure-mobility-install-configuration-mgr/sccm_distribute.png)
2. Selecione os pontos de distribuição nos quais os pacotes devem ser copiados. [Saiba mais](https://docs.microsoft.com/sccm/core/servers/deploy/configure/install-and-configure-distribution-points).
3. Conclua o assistente. Em seguida, o pacote inicia a replicação para os pontos de distribuição especificados.
4. Depois que a distribuição do pacote for concluída, clique com o botão direito do mouse no pacote > **implantar**.
   Captura de tela do ![Configuration Manager Console](./media/vmware-azure-mobility-install-configuration-mgr/sccm_deploy.png)
5. Selecione a coleção de dispositivos Windows ou Linux que você criou anteriormente.
6. Na página **especificar o destino do conteúdo** , selecione **pontos de distribuição**.
7. Na página **especificar configurações para controlar como este software é implantado** , defina a **finalidade** como **obrigatória**.

   ![Captura de tela do assistente de implantação de software](./media/vmware-azure-mobility-install-configuration-mgr/sccm-deploy-select-purpose.png)

8. Em **especificar o agendamento para essa implantação**, configure uma agenda. [Saiba mais](https://docs.microsoft.com/sccm/apps/deploy-use/deploy-applications#bkmk_deploy-sched).

    - O serviço de mobilidade é instalado de acordo com o agendamento que você especificar. 
    - Para evitar reinicializações desnecessárias, Agende a instalação do pacote durante a janela de manutenção mensal ou a janela de atualizações de software.
9. Na página **pontos de distribuição** , defina as configurações e conclua o assistente.
10. Monitore o progresso da implantação no console do Configuration Manager. Acesse **monitoramento** > **implantações** >  *[nome do pacote]* .





### <a name="uninstall-the-mobility-service"></a>Desinstalar o serviço de mobilidade
Você pode criar pacotes de Configuration Manager para desinstalar o serviço de mobilidade. Use o seguinte script para fazer isso:

```
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
Agora [habilite a proteção](vmware-azure-enable-replication.md) para VMs.
