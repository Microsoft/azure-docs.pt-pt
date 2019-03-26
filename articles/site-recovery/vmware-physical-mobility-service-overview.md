---
title: Sobre o serviço de mobilidade para a recuperação após desastre de VMs de VMware e servidores físicos com o Azure Site Recovery | Documentos da Microsoft
description: Saiba mais sobre o agente do serviço de mobilidade para recuperação após desastre de VMs de VMware e servidores físicos para o Azure com o serviço Azure Site Recovery.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: ramamill
ms.openlocfilehash: 6b06ee7710dedbf2283fc4e365b767aa57547e7c
ms.sourcegitcommit: 72cc94d92928c0354d9671172979759922865615
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/25/2019
ms.locfileid: "58417824"
---
# <a name="about-the-mobility-service-for-vmware-vms-and-physical-servers"></a>Sobre o serviço de mobilidade para VMs de VMware e servidores físicos

Quando define a recuperação após desastre para VMs de VMware e servidores físicos usando [do Azure Site Recovery](site-recovery-overview.md), instalar o serviço de mobilidade de recuperação de Site em cada VM de VMware no local e o servidor físico.  O serviço de mobilidade obtém dados, escreve na máquina e reencaminha-os para o servidor de processos de recuperação de sites. Pode implementar o serviço de mobilidade através dos seguintes métodos:

- [Instalação push](#push-installation): Site Recovery instala o agente de mobilidade no servidor quando a proteção é ativada através do portal do Azure.
- Instale manualmente: Pode instalar o serviço de mobilidade manualmente em cada máquina através de [IU](#install-mobility-agent-through-ui) ou [linha de comandos](#install-mobility-agent-through-command-prompt).
- [Implementação automatizada](vmware-azure-mobility-install-configuration-mgr.md): Pode automatizar a instalação com ferramentas de implantação de software, como o System Center Configuration Manager.

## <a name="anti-virus-on-replicated-machines"></a>Software antivírus em máquinas replicadas

Se as máquinas que pretende replicar tem active software de antivírus em execução, certifique-se de excluir a pasta de instalação do serviço de mobilidade de operações de software antivírus (*C:\ProgramData\ASR\agent*). Isto garante que a replicação funciona conforme esperado.

## <a name="push-installation"></a>Instalação de push

Instalação de push é parte integrante do "[ativar a replicação](vmware-azure-enable-replication.md#enable-replication)" tarefa acionada no portal. Depois de escolher o conjunto de máquinas virtuais que pretende proteger e acione "Ativar replicação", o servidor de configuração envia por push do agente de mobilidade sessão nos servidores, instala o agente e o registro completo do agente com o servidor de configuração. Para a conclusão com êxito desta operação

- Certifique-se de que todos instalação de push [pré-requisitos](vmware-azure-install-mobility-service.md) são cumpridos.
- Certifique-se de que todas as configurações de servidores sujeitos [matriz de suporte do VMware para o cenário de DR do Azure](vmware-physical-azure-support-matrix.md).

Detalhes do fluxo de trabalho de instalação de push já foi amplamente descrito nas seções a seguir.

### <a name="from-923-versionhttpssupportmicrosoftcomen-inhelp4494485update-rollup-35-for-azure-site-recovery-onwards"></a>Partir [versão 9.23](https://support.microsoft.com/en-in/help/4494485/update-rollup-35-for-azure-site-recovery) e posteriores

Durante a instalação push do agente de mobilidade, os passos seguintes são executados

1. Agente de pushes no computador de origem. Copiar o agente de sessão no computador de origem pode falhar devido a vários erros ambientais. Visite [nossa orientação](vmware-azure-troubleshoot-push-install.md) para resolver problemas de falhas de instalação de push.
2. Depois do agente foi copiado com êxito para as verificações de pré-requisitos do servidor são executadas no servidor. Instalação do falha se um ou mais da [pré-requisitos](vmware-physical-azure-support-matrix.md) não forem cumpridos. Se todos os pré-requisitos são cumpridos, é acionada a instalação.
3. O fornecedor de VSS de recuperação de Site do Azure está instalado no servidor como parte da instalação do agente de mobilidade. Este fornecedor é utilizado para gerar pontos consistentes da aplicação. Se a instalação do fornecedor de VSS falhar, este passo vai ser ignorado e irá continuar a instalação do agente.
4. Se a instalação do agente tiver êxito, mas ocorre uma falha de instalação do fornecedor VSS, em seguida, o estado da tarefa é marcado como "Aviso". Isto não afeta a geração de pontos de consistência de falhas.
    a. Para gerar pontos consistente da aplicação, consulte [nossa orientação](vmware-physical-manage-mobility-service.md#install-site-recovery-vss-provider-on-source-machine) para concluir a instalação do fornecedor de VSS de recuperação de Site manualmente.
    b.  Se não pretender pontos consistente do aplicativo a ser gerado [modificar a política de replicação](vmware-azure-set-up-replication.md#create-a-policy) desativar pontos consistente da aplicação.

### <a name="before-922-versions"></a>Antes de 9.22 versões

1. Agente de pushes no computador de origem. Copiar o agente de sessão no computador de origem pode falhar devido a vários erros ambientais. Visite [nossa orientação](vmware-azure-troubleshoot-push-install.md) para resolver problemas de falhas de instalação de push.
2. Depois do agente foi copiado com êxito para as verificações de pré-requisitos do servidor são executadas no servidor. Instalação do falha se um ou mais da [pré-requisitos](vmware-physical-azure-support-matrix.md) não forem cumpridos. Se todos os pré-requisitos são cumpridos, é acionada a instalação.
3. O fornecedor de VSS de recuperação de Site do Azure está instalado no servidor como parte da instalação do agente de mobilidade. Este fornecedor é utilizado para gerar pontos consistentes da aplicação. Se a instalação do fornecedor de VSS falhar, a instalação do agente irá falhar. Para evitar a falha da instalação do agente de mobilidade, utilize [versão 9.23](https://support.microsoft.com/en-in/help/4494485/update-rollup-35-for-azure-site-recovery) ou superior para pontos de consistente com a falha de gerar e instalar manualmente o fornecedor VSS.

## <a name="install-mobility-agent-through-ui"></a>Instalar o agente de mobilidade através da interface do Usuário

### <a name="prerequisite"></a>Pré-requisito

- Certifique-se de que todas as configurações de servidores sujeitos [matriz de suporte do VMware para o cenário de DR do Azure](vmware-physical-azure-support-matrix.md).
- [Localizar o instalador](#locate-installer-files) com base no sistema operativo do servidor.

>[!IMPORTANT]
> Se estiver a replicar VMS de IaaS do Azure de uma região do Azure para outro, não utilize este método. Use o método de instalação baseada na linha de comandos.

1. Copie o ficheiro de instalação para a máquina e executá-lo.
2. Na **opção de instalação**, selecione **instalar o serviço de mobilidade**.
3. Selecione a localização de instalação > **instalar**.

    ![Página de opção de instalação de serviço de mobilidade](./media/vmware-physical-mobility-service-install-manual/mobility1.png)

4. Monitorizar a instalação no **progresso da instalação**. Depois de concluída a instalação, selecione **continuar a configuração** para registar o serviço com o servidor de configuração.

    ![Página de registo do serviço de mobilidade](./media/vmware-physical-mobility-service-install-manual/mobility3.png)

5. na **detalhes do servidor de configuração**, especifique o endereço IP e a frase de acesso que configurou.  

    ![Página de registo do serviço de mobilidade](./media/vmware-physical-mobility-service-install-manual/mobility4.png)

6. Selecione **registar** para concluir o registo.

    ![Página final de registo do serviço de mobilidade](./media/vmware-physical-mobility-service-install-manual/mobility5.png)

## <a name="install-mobility-agent-through-command-prompt"></a>Instalar o agente de mobilidade através da linha de comandos

### <a name="prerequisite"></a>Pré-requisito

- Certifique-se de que todas as configurações de servidores sujeitos [matriz de suporte do VMware para o cenário de DR do Azure](vmware-physical-azure-support-matrix.md).
- [Localizar o instalador](#locate-installer-files) com base no sistema operativo do servidor.

### <a name="on-a-windows-machine"></a>Num computador Windows

- Copie o instalador para uma pasta local (por exemplo, C:\Temp) no servidor que pretende proteger.

    ```
    cd C:\Temp
    ren Microsoft-ASR_UA*Windows*release.exe MobilityServiceInstaller.exe
    MobilityServiceInstaller.exe /q /x:C:\Temp\Extracted
    cd C:\Temp\Extracted
    ```

- Instale da seguinte forma:

    ``` 
    UnifiedAgent.exe /Role "MS" /InstallLocation "C:\Program Files (x86)\Microsoft Azure Site Recovery" /Platform "VmWare" /Silent
    ```

- Registe o agente com o servidor de configuração.

    ``` 
    cd C:\Program Files (x86)\Microsoft Azure Site Recovery\agent
    UnifiedAgentConfigurator.exe  /CSEndPoint <CSIP> /PassphraseFilePath <PassphraseFilePath>
    ```

#### <a name="installation-settings"></a>Definições de instalação
**Definição** | **Detalhes**
--- | ---
Utilização | . UnifiedAgent.exe /Role < MS|MT > /InstallLocation <Install Location> /Platform "VmWare" /Silent
Registos de configuração | Under %ProgramData%\ASRSetupLogs\ASRUnifiedAgentInstaller.log.
/Role | Parâmetro de instalação obrigatória. Especifica se o serviço de mobilidade (MS) ou o destino mestre (TA) deve ser instalado.
/InstallLocation| Parâmetro opcional. Especifica a localização de instalação do serviço de mobilidade (qualquer pasta).
/ Plataforma | Obrigatório. Especifica a plataforma em que o serviço de mobilidade está instalado. **VMware** para VMware VMs/servidores físicos; **Azure** para VMs do Azure. 
/Silent| Opcional. Especifica se pretende executar o instalador em modo silencioso.

#### <a name="registration-settings"></a>Definições de registo
**Definição** | **Detalhes**
--- | ---
Utilização | UnifiedAgentConfigurator.exe /CSEndPoint <CSIP> /PassphraseFilePath <PassphraseFilePath>
Registos de configuração do agente | Under %ProgramData%\ASRSetupLogs\ASRUnifiedAgentConfigurator.log.
/CSEndPoint | Parâmetro obrigatório. Especifica o endereço IP do servidor de configuração. Utilize qualquer endereço IP válido.
/PassphraseFilePath |  Obrigatório. Localização da frase de acesso. Utilize qualquer válido UNC ou caminho de ficheiro local.

### <a name="on-a-linux-machine"></a>Num computador com Linux

1. Copie o instalador para uma pasta local (por exemplo, /tmp) no servidor que pretende proteger. Num terminal, execute os seguintes comandos:

    ```
    cd /tmp ;
    tar -xvzf Microsoft-ASR_UA*release.tar.gz
    ```

2. Instale da seguinte forma:

    ```
    sudo ./install -d <Install Location> -r MS -v VmWare -q
    ```

3. Após a instalação estiver concluída, o serviço de mobilidade tem de estar registado para o servidor de configuração. Execute o seguinte comando para registar o serviço de mobilidade com o servidor de configuração:

    ```
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <CSIP> -P /var/passphrase.txt
    ```

#### <a name="installation-settings"></a>Definições de instalação
**Definição** | **Detalhes**
--- | ---
Utilização | . /Install -d <Install Location> - r < MS|MT> -v VmWare -q
-r | Parâmetro de instalação obrigatória. Especifica se o serviço de mobilidade (MS) ou o destino mestre (TA) deve ser instalado.
-d | Parâmetro opcional. Especifica a localização de instalação do serviço de mobilidade: /usr/local/ASR.
-v | Obrigatório. Especifica a plataforma em que o serviço de mobilidade está instalado. **VMware** para VMware VMs/servidores físicos; **Azure** para VMs do Azure. 
-q | Opcional. Especifica se pretende executar o instalador em modo silencioso.

#### <a name="registration-settings"></a>Definições de registo
**Definição** | **Detalhes**
--- | ---
Utilização | CD /usr/local/ASR/Vx/bin<br/><br/> UnifiedAgentConfigurator.sh -i <CSIP> -P <PassphraseFilePath>
-i | Parâmetro obrigatório. Especifica o endereço IP do servidor de configuração. Utilize qualquer endereço IP válido.
-P |  Obrigatório. Caminho completo do ficheiro em que a frase de acesso é guardado. Utilize qualquer pasta válido.

## <a name="azure-virtual-machine-agent"></a>Agente de Máquina Virtual do Azure

- **VMs do Windows**: Da versão 9.7.0.0 do serviço de mobilidade, o [agente de VM do Azure](../virtual-machines/extensions/features-windows.md#azure-vm-agent) é instalado pelo instalador do serviço de mobilidade. Isto garante que, quando a máquina faz a ativação pós-falha para o Azure, a VM do Azure cumpre a pré-requisitos para a utilização de qualquer extensão de Vm a instalação do agente.
- **VMs do Linux**: O [WALinuxAgent](https://docs.microsoft.com/azure/virtual-machines/extensions/update-linux-agent) tem de ser instalado manualmente na VM do Azure após a ativação pós-falha.

## <a name="locate-installer-files"></a>Localizar ficheiros de instalador

Aceda à pasta de %ProgramData%\ASR\home\svsystems\pushinstallsvc\repository no servidor de configuração. Verifique qual installer, é necessário com base no sistema operativo. A tabela seguinte resume os arquivos do instalador para cada VM de VMware e o sistema operativo de servidor físico. Pode rever [sistemas operativos suportados](vmware-physical-azure-support-matrix.md#replicated-machines) antes de começar.

**Ficheiro de instalador** | **Sistema operativo (apenas 64 bits)** 
--- | ---
Microsoft-ASR\_UA\*Windows\*release.exe | Windows Server 2016; Windows Server 2012 R2; Windows Server 2012; Windows Server 2008 R2 SP1 
Microsoft-ASR\_UA\*RHEL6-64\*release.tar.gz | Red Hat Enterprise Linux (RHEL) 6.* </br> CentOS 6.*
Microsoft-ASR\_UA\*RHEL7-64\*release.tar.gz | Red Hat Enterprise Linux (RHEL) 7.* </br> CentOS 7.* 
Microsoft-ASR\_UA\*SLES12-64\*release.tar.gz | SUSE Linux Enterprise Server 12 SP1,SP2,SP3 
Microsoft-ASR\_UA\*SLES11-SP3-64\*release.tar.gz| SUSE Linux Enterprise Server 11 SP3 
Microsoft-ASR\_UA\*SLES11-SP4-64\*release.tar.gz| SUSE Linux Enterprise Server 11 SP4 
Microsoft-ASR\_UA\*OL6-64\*release.tar.gz | Oracle Enterprise Linux 6.4, 6.5
Microsoft-ASR\_UA\*UBUNTU-14.04-64\*release.tar.gz | Ubuntu Linux 14.04
Microsoft-ASR\_UA\*UBUNTU-16.04-64\*release.tar.gz | Server 16.04 LTS do Ubuntu Linux
Microsoft-ASR_UA\*DEBIAN7-64\*release.tar.gz | Debian 7 
Microsoft-ASR_UA\*DEBIAN8-64\*release.tar.gz | Debian 8

## <a name="next-steps"></a>Passos Seguintes

[Configurar a instalação push do serviço de mobilidade](vmware-azure-install-mobility-service.md).
