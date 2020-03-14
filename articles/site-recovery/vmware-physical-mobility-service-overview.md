---
title: Sobre o Serviço de Mobilidade para recuperação de desastres de VMware VMs e servidores físicos com Recuperação do Site Azure  Microsoft Docs
description: Conheça o agente do Serviço de Mobilidade para recuperação de desastres de VMware VMs e servidores físicos para o Azure utilizando o serviço de recuperação do site Azure.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: ramamill
ms.openlocfilehash: c5acc9637fe5afe8f7dd32d23fbdbb80373b4f61
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79256838"
---
# <a name="about-the-mobility-service-for-vmware-vms-and-physical-servers"></a>Sobre o serviço de Mobilidade para VMware VMs e servidores físicos

Quando configurar a recuperação de desastres para VMware VMs e servidores físicos utilizando a Recuperação do [Site Azure,](site-recovery-overview.md)instala o serviço de Mobilidade de Recuperação do Site em cada VM e servidor físico no local.  O serviço de Mobilidade captura os dados escritos na máquina e encaminha-os para o servidor do processo de Recuperação do Site. Pode utilizar o Serviço de Mobilidade utilizando os seguintes métodos:

- [Instalação de impulso](#push-installation): A Recuperação do Local instala o agente de mobilidade no servidor quando a proteção é ativada através do portal Azure.
- Instale manualmente: Pode instalar manualmente o serviço mobility em cada máquina através de [UI](#install-mobility-agent-through-ui) ou pedido de [comando](#install-mobility-agent-through-command-prompt).
- [Implementação automatizada](vmware-azure-mobility-install-configuration-mgr.md): Pode automatizar a instalação com ferramentas de implementação de software, como o Gestor de Configuração.

> [!NOTE]
> O agente de Mobilidade utiliza aproximadamente 6%-10% da memória em máquinas de origem para VMware VMs ou máquinas físicas.

## <a name="anti-virus-on-replicated-machines"></a>Antivírus em máquinas replicadas

Se as máquinas que pretende replicar tiverem software antivírus ativo em funcionamento, certifique-se de que exclui a pasta de instalação do serviço mobility de operações antivírus *(C:\ProgramData\ASR\agent).* Isto garante que a replicação funciona como esperado.

## <a name="push-installation"></a>Instalação de empurrar

A instalação push é parte integrante do trabalho de["Enable Replication](vmware-azure-enable-replication.md#enable-replication)" desencadeado no portal. Depois de escolher o conjunto de máquinas virtuais que pretende proteger e acionar "Enable Replication", o servidor de configuração empurra o agente de mobilidade para os servidores, instala o agente e completa o registo do agente com o servidor de configuração. Para a conclusão bem sucedida desta operação,

- Certifique-se de que todos os [pré-requisitos](vmware-azure-install-mobility-service.md) de instalação push são cumpridos.
- Certifique-se de que todas as configurações dos servidores se enquadram na [matriz de suporte do VMware para o cenário Azure DR](vmware-physical-azure-support-matrix.md).

Os detalhes do fluxo de trabalho da instalação push foram descritos nas seguintes secções.

### <a name="from-923-version-onwards"></a>A partir de [9.23 versão](https://support.microsoft.com/en-in/help/4494485/update-rollup-35-for-azure-site-recovery) em diante

Durante a instalação de impulso do agente de mobilidade, são realizados passos seguintes

1. Empurra o agente para a máquina de origem. Copiar o agente para a máquina de origem pode falhar devido a múltiplos erros ambientais. Visite [a nossa orientação](vmware-azure-troubleshoot-push-install.md) para resolver falhas de instalação de impulso.
2. Depois de o agente ser copiado com sucesso para as verificações pré-requisito do servidor são realizadas no servidor. A instalação falha se um ou mais dos [pré-requisitos](vmware-physical-azure-support-matrix.md) não forem cumpridos. Se todos os pré-requisitos forem cumpridos, a instalação é acionada.
3. O fornecedor VsS de recuperação do site Azure está instalado no servidor como parte da instalação do agente de mobilidade. Este fornecedor é utilizado para gerar pontos consistentes da Aplicação. Se a instalação do fornecedor VSS falhar, este passo será ignorado e a instalação do agente continuará.
4. Se a instalação do agente for bem sucedida, mas a instalação do fornecedor VSS falhar, o estado de trabalho está marcado como "Aviso". Isto não afeta a consistência do crash pontos de geração.

    a. Para gerar pontos consistentes de aplicação, consulte [a nossa orientação](vmware-physical-manage-mobility-service.md#install-site-recovery-vss-provider-on-source-machine) para completar a instalação do fornecedor VSS de Recuperação do Site manualmente. </br>
    b.  Se não desejar que sejam gerados pontos consistentes de aplicação, [modifique a política de replicação](vmware-azure-set-up-replication.md#create-a-policy) para desligar pontos consistentes da aplicação.

### <a name="before-922-versions"></a>Antes das versões 9.22

1. Empurra o agente para a máquina de origem. Copiar o agente para a máquina de origem pode falhar devido a múltiplos erros ambientais. Visite [a nossa orientação](vmware-azure-troubleshoot-push-install.md) para resolver falhas de instalação de impulso.
2. Depois de o agente ser copiado com sucesso para as verificações pré-requisito do servidor são realizadas no servidor. A instalação falha se um ou mais dos [pré-requisitos](vmware-physical-azure-support-matrix.md) não forem cumpridos. Se todos os pré-requisitos forem cumpridos, a instalação é acionada.
3. O fornecedor VsS de recuperação do site Azure está instalado no servidor como parte da instalação do agente de mobilidade. Este fornecedor é utilizado para gerar pontos consistentes da Aplicação. Se a instalação do fornecedor VSS falhar, a instalação do agente falhará. Para evitar falhas na instalação do agente de mobilidade, utilize [a versão 9.23](https://support.microsoft.com/en-in/help/4494485/update-rollup-35-for-azure-site-recovery) ou superior para gerar pontos consistentes de colisão e instale manualmente o fornecedor VSS.

## <a name="install-mobility-agent-through-ui"></a>Instale o agente de mobilidade através da UI

### <a name="prerequisite"></a>Pré-requisito

- Certifique-se de que todas as configurações dos servidores se enquadram na [matriz de suporte do VMware para o cenário Azure DR](vmware-physical-azure-support-matrix.md).
- [Localize o instalador](#locate-installer-files) com base no sistema operativo do servidor.

>[!IMPORTANT]
> Se estiver a replicar o VM Azure IaaS de uma região de Azure para outra, não utilize este método. Utilize o método de instalação baseado em linha de comando.

1. Copie o ficheiro de instalação para a máquina e execute-o.
2. Na **opção de instalação,** selecione **Instalar o serviço de mobilidade.**
3. Selecione a localização de instalação > **Instale**.

    ![Página de opção de instalação do Serviço de Mobilidade](./media/vmware-physical-mobility-service-install-manual/mobility1.png)

4. Monitorize a instalação em Evolução da **Instalação**. Depois de terminar a instalação, selecione **'Proceder à Configuração'** para registar o serviço com o servidor de configuração.

    ![Página de inscrição do Serviço de Mobilidade](./media/vmware-physical-mobility-service-install-manual/mobility3.png)

5. Em Detalhes do Servidor de **Configuração,** especifique o endereço IP e a frase-passe configurada.

    ![Página de inscrição do Serviço de Mobilidade](./media/vmware-physical-mobility-service-install-manual/mobility4.png)

6. Selecione **Registar** para terminar a inscrição.

    ![Página final de inscrição do Serviço de Mobilidade](./media/vmware-physical-mobility-service-install-manual/mobility5.png)

## <a name="install-mobility-agent-through-command-prompt"></a>Instale o agente de mobilidade através de um pedido de comando

### <a name="prerequisite"></a>Pré-requisito

- Certifique-se de que todas as configurações dos servidores se enquadram na [matriz de suporte do VMware para o cenário Azure DR](vmware-physical-azure-support-matrix.md).
- [Localize o instalador](#locate-installer-files) com base no sistema operativo do servidor.

### <a name="on-a-windows-machine"></a>Em uma máquina do Windows

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

#### <a name="installation-settings"></a>Configurações de instalação
**Definição** | **Detalhes**
--- | ---
Utilização | Agente Unificado.exe /Função \<MS/MT> /InstalaçãoLocalização \<Instalar Localização> /Plataforma "VmWare" /Silencioso
Registos de configuração | Em %ProgramData%\ASRSetupLogs\ASRUnifiedAgentInstaller.log.
/Papel | Parâmetro de instalação obrigatório. Especifica se o serviço de Mobilidade (MS) ou o alvo principal (MT) devem ser instalados.
/InstallLocation| Parâmetro opcional. Especifica a localização de instalação do serviço mobility (qualquer pasta).
/Plataforma | Obrigatório. Especifica a plataforma em que o Serviço de Mobilidade está instalado. **VMware** para VMware VMs/servidores físicos; **Azure** para VMs Azure.<br/><br/> Se estiver a tratar os VMs Azure como máquinas físicas, especifique **vMware**.
/Silêncio| Opcional. Especifica se deve executar o instalador em modo silencioso.

#### <a name="registration-settings"></a>Configurações de registo
**Definição** | **Detalhes**
--- | ---
Utilização | UnifiedAgentConfigurator.exe /CSEndPoint \<CSIP> /PassphraseFilePath \<PassphraseFilePath>
Registos de configuração do agente | Em %ProgramData%\ASRSetupLogs\ASRUnifiedAgentConfigurator.log.
/CSEndPoint | Parâmetro obrigatório. Especifica o endereço IP do servidor de configuração. Utilize qualquer endereço IP válido.
/PassphraseFilePath |  Obrigatório. Localização da frase-passe. Utilize qualquer caminho de ficheiro sem e não abertos à base de dados válido ou local.

### <a name="on-a-linux-machine"></a>Em uma máquina linux

1. Copie o instalador para uma pasta local (por exemplo, /tmp) no servidor que pretende proteger. Num terminal, execute os seguintes comandos:

    ```
    cd /tmp ;
    tar -xvf Microsoft-ASR_UA*release.tar.gz
    ```

2. Instale da seguinte forma:

    ```
    sudo ./install -d <Install Location> -r MS -v VmWare -q
    ```

3. Após a instalação, o Serviço de Mobilidade deve ser registado no servidor de configuração. Executar o seguinte comando para registar o Serviço de Mobilidade com o servidor de configuração:

    ```
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <CSIP> -P /var/passphrase.txt
    ```

#### <a name="installation-settings"></a>Configurações de instalação
**Definição** | **Detalhes**
--- | ---
Utilização | ./instalar -d \<Instalar Localização> -r \<MS/MT> -v VmWare -q
-r | Parâmetro de instalação obrigatório. Especifica se o serviço de Mobilidade (MS) ou o alvo principal (MT) devem ser instalados.
-d | Parâmetro opcional. Especifica a localização do serviço de mobilidade: /usr/local/ASR.
-v | Obrigatório. Especifica a plataforma em que o Serviço de Mobilidade está instalado. **VMware** para VMware VMs/servidores físicos; **Azure** para VMs Azure.
-q | Opcional. Especifica se deve executar o instalador em modo silencioso.

#### <a name="registration-settings"></a>Configurações de registo
**Definição** | **Detalhes**
--- | ---
Utilização | cd /usr/local/ASR/Vx/bin<br/><br/> UnifiedAgentConfigurator.sh -i \<CSIP> -P \<PassphraseFilePath>
-i | Parâmetro obrigatório. Especifica o endereço IP do servidor de configuração. Utilize qualquer endereço IP válido.
-P |  Obrigatório. Caminho completo do ficheiro em que a frase-passe é guardada. Utilize qualquer pasta válida.

## <a name="azure-virtual-machine-agent"></a>Agente máquina virtual Azure

- **VMs do Windows**: A partir da versão 9.7.0.0 do serviço mobility, o [agente Azure VM](../virtual-machines/extensions/features-windows.md#azure-vm-agent) é instalado pelo instalador de serviços mobility. Isto garante que quando a máquina falha no Azure, o Azure VM cumpre o pré-requisito de instalação do agente para a utilização de qualquer extensão VM.
- **VMs Linux**: O [WALinuxAgent](https://docs.microsoft.com/azure/virtual-machines/extensions/update-linux-agent) deve ser instalado manualmente no VM Azure após a falha.

## <a name="locate-installer-files"></a>Localizar ficheiros de instalação

Vá para %ProgramData%\ASR\home\svsystems\pushinstallsvc\repositório no servidor de configuração. Verifique qual é o instalador de que necessita com base no sistema operativo. A tabela seguinte resume os ficheiros do instalador para cada VMware VM e sistema operativo de servidor físico. Pode rever [os sistemas operativos suportados](vmware-physical-azure-support-matrix.md#replicated-machines) antes de iniciar.

**Arquivo de instalação** | **Sistema operativo (apenas 64 bits)**
--- | ---
Microsoft-ASR\_UA\*Windows\*release.exe | Windows Server 2016; Windows Server 2012 R2; Windows Server 2012; Windows Server 2008 R2 SP1
Microsoft-ASR\_UA\*RHEL6-64\*lançamento.tar.gz | Red Hat Enterprise Linux (RHEL) 6.* </br> Centos 6.*
Microsoft-ASR\_UA\*RHEL7-64\*lançamento.tar.gz | Red Hat Enterprise Linux (RHEL) 7.* </br> Centos 7.*
Microsoft-ASR\_UA\*SLES12-64\*lançamento.tar.gz | SUSE Linux Enterprise Server 12 SP1,SP2,SP3
Microsoft-ASR\_UA\*SLES11-SP3-64\*lançamento.tar.gz| SUSE Linux Enterprise Server 11 SP3
Microsoft-ASR\_UA\*SLES11-SP4-64\*lançamento.tar.gz| SUSE Linux Enterprise Server 11 SP4
Microsoft-ASR\_UA\*OL6-64\*lançamento.tar.gz | Oracle Enterprise Linux 6.4, 6.5
Microsoft-ASR\_UA\*UBUNTU-14.04-64\*release.tar.gz | Ubuntu Linux 14.04
Microsoft-ASR\_UA\*UBUNTU-16.04-64\*release.tar.gz | Servidor Ubuntu Linux 16.04 LTS
Microsoft-ASR_UA\*DEBIAN7-64\*lançamento.tar.gz | Debiano 7
Microsoft-ASR_UA\*DEBIAN8-64\*lançamento.tar.gz | Debian 8

## <a name="next-steps"></a>Passos seguintes

[Instale a instalação de impulso para o serviço de Mobilidade](vmware-azure-install-mobility-service.md).
