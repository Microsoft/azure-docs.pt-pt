---
title: Automatizar a instalação do serviço de mobilidade do Azure Site Recovery para recuperação após desastre de VMs de VMware e servidores físicos para o Azure com o System Center Configuration Manager | Documentos da Microsoft
description: Este artigo ajuda-o a automatizar a instalação do serviço de mobilidade com o System Center Configuration Manager, para recuperação após desastre de VMs de VMware e servidores físicos para o Azure com o Site Recovery.
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: ramamill
ms.openlocfilehash: ca9e58dbae6952f7e432868c7ede7dc42741a87b
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58104255"
---
# <a name="automate-mobility-service-installation-with-system-center-configuration-manager"></a>Automatizar a instalação do serviço de mobilidade com o System Center Configuration Manager

O serviço de mobilidade está instalado em VMs de VMware e servidores físicos que pretende replicar para o Azure com [Azure Site Recovery](site-recovery-overview.md)

Este artigo fornece um exemplo de como pode usar o System Center Configuration Manager para implementar o serviço de mobilidade do Azure Site Recovery numa VM do VMware. Usando uma ferramenta de implementação de software, como o Configuration Manager possui as seguintes vantagens:

* Agendar instalações zero e atualizações durante a janela de manutenção planeada para atualizações de software
* Dimensionar a implementação para centenas de servidores em simultâneo

Este artigo utiliza o System Center Configuration Manager 2012 R2 para demonstrar a atividade de implementação. Podemos pressupõe que está a utilizar versão **9.9.4510.1** ou superior do serviço de mobilidade.

Em alternativa, pode automatizar a instalação do serviço de mobilidade com o [DSC de automatização do Azure](vmware-azure-mobility-deploy-automation-dsc.md).

## <a name="prerequisites"></a>Pré-requisitos

1. Uma implementação ferramenta de software, como o Configuration Manager, que já estiver implementado no seu ambiente.
2. Deve criar dois [coleções de dispositivos](https://technet.microsoft.com/library/gg682169.aspx), um para todos **servidores do Windows**e outro para todos os **servidores Linux**, que deseja proteger com o Site Recovery.
3. Um servidor de configuração que já está registado no cofre dos serviços de recuperação.
4. Uma rede segura partilha de ficheiros (partilha SMB) que pode ser acessada pela máquina do Gestor de configuração.

## <a name="deploy-on-windows-machines"></a>Implementar em máquinas do Windows
> [!NOTE]
> Este artigo pressupõe que o endereço IP do servidor de configuração é 192.168.3.121 e de que a partilha de ficheiros de rede segura é \\\ContosoSecureFS\MobilityServiceInstallers.

### <a name="prepare-for-deployment"></a>Preparar para a implementação
1. Crie uma pasta na partilha de rede e designe- **MobSvcWindows**.
2. Inicie sessão no seu servidor de configuração e abra uma linha de comandos administrativa.
3. Execute os seguintes comandos para gerar um ficheiro da frase de acesso:

    `cd %ProgramData%\ASR\home\svsystems\bin`

    `genpassphrase.exe -v > MobSvc.passphrase`
4. Copiar o **MobSvc.passphrase** de ficheiros para o **MobSvcWindows** pasta na partilha de rede.
5. Navegue para o repositório de instalador no servidor de configuração ao executar o seguinte comando:

   `cd %ProgramData%\ASR\home\svsystems\pushinstallsvc\repository`

6. Copiar o **Microsoft ASR\_UA\_*versão*\_Windows\_GA\_*data*\_Release.exe**  para o **MobSvcWindows** pasta na partilha de rede.
7. Copie o código seguinte e guarde-o como **bat** para o **MobSvcWindows** pasta.

   > [!NOTE]
   > Substitua os marcadores de posição [CSIP] neste script com os valores reais do endereço IP do seu servidor de configuração.

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

### <a name="create-a-package"></a>Criar um pacote

1. Inicie sessão na consola do Configuration Manager.
2. Navegue até **biblioteca de Software** > **gestão de aplicações** > **pacotes**.
3. Com o botão direito **pacotes**e selecione **criar pacote**.
4. Fornece valores para o nome, descrição, fabricante, idioma e versão.
5. Selecione o **este pacote contém ficheiros de origem** caixa de verificação.
6. Clique em **navegue**e selecione a partilha de rede onde está armazenado o instalador (\\\ContosoSecureFS\MobilityServiceInstaller\MobSvcWindows).

   ![Assistente de captura de ecrã de criar pacote e programa](./media/vmware-azure-mobility-install-configuration-mgr/create_sccm_package.png)

7. Sobre o **escolha o tipo de programa que pretende criar** página, selecione **programa padrão**e clique em **seguinte**.

   ![Assistente de captura de ecrã de criar pacote e programa](./media/vmware-azure-mobility-install-configuration-mgr/sccm-standard-program.png)

8. Sobre o **especificar informações sobre este programa padrão** página, fornecer as entradas seguintes e clique em **próxima**. (As outras entradas podem usar seus valores padrão).

   | **Parameter name** (Nome do parâmetro) | **Valor** |
   |--|--|
   | Name | Instalar o serviço de mobilidade do Microsoft Azure (Windows) |
   | Linha de comandos | install.bat |
   | Programa pode ser executado | Se pretende ou não um utilizador tem sessão iniciada |

   ![Assistente de captura de ecrã de criar pacote e programa](./media/vmware-azure-mobility-install-configuration-mgr/sccm-program-properties.png)

9. Na página seguinte, selecione os sistemas operacionais de destino. O serviço de mobilidade pode ser instalado apenas no Windows Server 2012 R2, Windows Server 2012 e Windows Server 2008 R2.

   ![Assistente de captura de ecrã de criar pacote e programa](./media/vmware-azure-mobility-install-configuration-mgr/sccm-program-properties-page2.png)

10. Para concluir o assistente, clique em **seguinte** duas vezes.


> [!NOTE]
> O script oferece suporte a ambas as novas instalações de agentes de serviço de mobilidade e atualizações para agentes que já estejam instaladas.

### <a name="deploy-the-package"></a>Implementar o pacote
1. Na consola do Configuration Manager, o pacote com o botão direito e selecione **distribuir conteúdo**.
   ![Consola de captura de ecrã do Configuration Manager](./media/vmware-azure-mobility-install-configuration-mgr/sccm_distribute.png)
2. Selecione o **[pontos de distribuição](https://technet.microsoft.com/library/gg712321.aspx#BKMK_PlanForDistributionPoints)** para que os pacotes devem ser copiados.
3. Conclua o assistente. O pacote, em seguida, começa a replicar para pontos de distribuição especificados.
4. Depois de terminar a distribuição do pacote, o pacote com o botão direito e selecione **Deploy**.
   ![Consola de captura de ecrã do Configuration Manager](./media/vmware-azure-mobility-install-configuration-mgr/sccm_deploy.png)
5. Selecione a coleção de dispositivos do Windows Server que criou na secção pré-requisitos, como a coleção de destino para implementação.

   ![Assistente de captura de ecrã de implementação de Software](./media/vmware-azure-mobility-install-configuration-mgr/sccm-select-target-collection.png)

6. Sobre o **especifique o destino do conteúdo** página, selecione seu **pontos de distribuição**.
7. Sobre o **especificar as definições para controlar a forma como este software é implementado** página, certifique-se de que o objetivo é **necessário**.

   ![Assistente de captura de ecrã de implementação de Software](./media/vmware-azure-mobility-install-configuration-mgr/sccm-deploy-select-purpose.png)

8. Sobre o **especifique o agendamento para esta implementação** , especifique uma agenda. Para obter mais informações, consulte [agendamento pacotes](https://technet.microsoft.com/library/gg682178.aspx).
9. Sobre o **pontos de distribuição** página, configure as propriedades de acordo com as necessidades do seu datacenter. Em seguida, conclua o assistente.

> [!TIP]
> Para evitar reinícios desnecessários, agende a instalação do pacote durante a janela de manutenção mensal ou janela de atualizações de software.

Pode monitorizar o progresso da implementação, utilizando a consola do Configuration Manager. Aceda a **monitorização** > **implementações** > *[nome do seu pacote]*.

  ![Opção de captura de ecrã do Configuration Manager para monitorizar as implementações](./media/vmware-azure-mobility-install-configuration-mgr/report.PNG)

## <a name="deploy-on-linux-machines"></a>Implementar em máquinas do Linux
> [!NOTE]
> Este artigo pressupõe que o endereço IP do servidor de configuração é 192.168.3.121 e de que a partilha de ficheiros de rede segura é \\\ContosoSecureFS\MobilityServiceInstallers.

### <a name="prepare-for-deployment"></a>Preparar para a implementação
1. Crie uma pasta na partilha de rede e nomeie-o como **MobSvcLinux**.
2. Inicie sessão no seu servidor de configuração e abra uma linha de comandos administrativa.
3. Execute os seguintes comandos para gerar um ficheiro da frase de acesso:

    `cd %ProgramData%\ASR\home\svsystems\bin`

    `genpassphrase.exe -v > MobSvc.passphrase`
4. Copiar o **MobSvc.passphrase** de ficheiros para o **MobSvcLinux** pasta na partilha de rede.
5. Navegue para o repositório de instalador no servidor de configuração ao executar o comando:

   `cd %ProgramData%\ASR\home\svsystems\pushinstallsvc\repository`

6. Copie os seguintes ficheiros para o **MobSvcLinux** pasta na partilha de rede:
   * Microsoft-ASR\_UA\*RHEL6-64\*release.tar.gz
   * Microsoft-ASR\_UA\*RHEL7-64\*release.tar.gz
   * Microsoft-ASR\_UA\*SLES11-SP3-64\*release.tar.gz
   * Microsoft-ASR\_UA\*SLES11-SP4-64\*release.tar.gz
   * Microsoft-ASR\_UA\*OL6-64\*release.tar.gz
   * Microsoft-ASR\_UA\*UBUNTU-14.04-64\*release.tar.gz


7. Copie o código seguinte e guarde-o como **install_linux.sh** para o **MobSvcLinux** pasta.
   > [!NOTE]
   > Substitua os marcadores de posição [CSIP] neste script com os valores reais do endereço IP do seu servidor de configuração.

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

1. Inicie sessão na consola do Configuration Manager.
2. Navegue até **biblioteca de Software** > **gestão de aplicações** > **pacotes**.
3. Com o botão direito **pacotes**e selecione **criar pacote**.
4. Fornece valores para o nome, descrição, fabricante, idioma e versão.
5. Selecione o **este pacote contém ficheiros de origem** caixa de verificação.
6. Clique em **navegue**e selecione a partilha de rede onde está armazenado o instalador (\\\ContosoSecureFS\MobilityServiceInstaller\MobSvcLinux).

   ![Assistente de captura de ecrã de criar pacote e programa](./media/vmware-azure-mobility-install-configuration-mgr/create_sccm_package-linux.png)

7. Sobre o **escolha o tipo de programa que pretende criar** página, selecione **programa padrão**e clique em **seguinte**.

   ![Assistente de captura de ecrã de criar pacote e programa](./media/vmware-azure-mobility-install-configuration-mgr/sccm-standard-program.png)

8. Sobre o **especificar informações sobre este programa padrão** página, fornecer as entradas seguintes e clique em **próxima**. (As outras entradas podem usar seus valores padrão).

    | **Parameter name** (Nome do parâmetro) | **Valor** |
   |--|--|
   | Name | Instalar o serviço de mobilidade do Microsoft Azure (Linux) |
   | Linha de comandos | ./install_linux.sh |
   | Programa pode ser executado | Se pretende ou não um utilizador tem sessão iniciada |

   ![Assistente de captura de ecrã de criar pacote e programa](./media/vmware-azure-mobility-install-configuration-mgr/sccm-program-properties-linux.png)

9. Na página seguinte, selecione **este programa pode ser executado em qualquer plataforma**.
   ![Assistente de captura de ecrã de criar pacote e programa](./media/vmware-azure-mobility-install-configuration-mgr/sccm-program-properties-page2-linux.png)

10. Para concluir o assistente, clique em **seguinte** duas vezes.

> [!NOTE]
> O script oferece suporte a ambas as novas instalações de agentes de serviço de mobilidade e atualizações para agentes que já estejam instaladas.

### <a name="deploy-the-package"></a>Implementar o pacote
1. Na consola do Configuration Manager, o pacote com o botão direito e selecione **distribuir conteúdo**.
   ![Consola de captura de ecrã do Configuration Manager](./media/vmware-azure-mobility-install-configuration-mgr/sccm_distribute.png)
2. Selecione o **[pontos de distribuição](https://technet.microsoft.com/library/gg712321.aspx#BKMK_PlanForDistributionPoints)** para que os pacotes devem ser copiados.
3. Conclua o assistente. O pacote, em seguida, começa a replicar para pontos de distribuição especificados.
4. Depois de terminar a distribuição do pacote, o pacote com o botão direito e selecione **Deploy**.
   ![Consola de captura de ecrã do Configuration Manager](./media/vmware-azure-mobility-install-configuration-mgr/sccm_deploy.png)
5. Selecione a coleção de dispositivos de servidor Linux que criou na secção pré-requisitos, como a coleção de destino para implementação.

   ![Assistente de captura de ecrã de implementação de Software](./media/vmware-azure-mobility-install-configuration-mgr/sccm-select-target-collection-linux.png)

6. Sobre o **especifique o destino do conteúdo** página, selecione seu **pontos de distribuição**.
7. Sobre o **especificar as definições para controlar a forma como este software é implementado** página, certifique-se de que o objetivo é **necessário**.

   ![Assistente de captura de ecrã de implementação de Software](./media/vmware-azure-mobility-install-configuration-mgr/sccm-deploy-select-purpose.png)

8. Sobre o **especifique o agendamento para esta implementação** , especifique uma agenda. Para obter mais informações, consulte [agendamento pacotes](https://technet.microsoft.com/library/gg682178.aspx).
9. Sobre o **pontos de distribuição** página, configure as propriedades de acordo com as necessidades do seu datacenter. Em seguida, conclua o assistente.

O serviço de mobilidade é instalado na coleção de dispositivos de servidor Linux, de acordo com a agenda configurada.


## <a name="uninstall-the-mobility-service"></a>Desinstale o serviço de mobilidade
Pode criar pacotes do Configuration Manager para desinstalar o serviço de mobilidade. Utilize o seguinte script para fazer isso:

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

## <a name="next-steps"></a>Passos Seguintes
Agora, está pronto para [ativar a proteção](vmware-azure-enable-replication.md) para as suas máquinas virtuais.
