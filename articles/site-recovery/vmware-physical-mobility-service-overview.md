---
title: Sobre o serviço de mobilidade para recuperação de desastre de VMs VMware e servidores físicos com Azure Site Recovery | Microsoft Docs
description: Saiba mais sobre o agente do serviço de mobilidade para recuperação de desastre de VMs VMware e servidores físicos no Azure usando o serviço Azure Site Recovery.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: ramamill
ms.openlocfilehash: c5acc9637fe5afe8f7dd32d23fbdbb80373b4f61
ms.sourcegitcommit: 163be411e7cd9c79da3a3b38ac3e0af48d551182
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/21/2020
ms.locfileid: "77539387"
---
# <a name="about-the-mobility-service-for-vmware-vms-and-physical-servers"></a>Sobre o serviço de mobilidade para VMs VMware e servidores físicos

Quando configurar a recuperação de desastres para VMware VMs e servidores físicos utilizando a Recuperação do [Site Azure,](site-recovery-overview.md)instala o serviço de Mobilidade de Recuperação do Site em cada VM e servidor físico no local.  O serviço de mobilidade captura gravações de dados no computador e as encaminha para o servidor de processo de Site Recovery. Você pode implantar o serviço de mobilidade usando os seguintes métodos:

- [Instalação de impulso](#push-installation): A Recuperação do Local instala o agente de mobilidade no servidor quando a proteção é ativada através do portal Azure.
- Instale manualmente: Pode instalar manualmente o serviço mobility em cada máquina através de [UI](#install-mobility-agent-through-ui) ou pedido de [comando](#install-mobility-agent-through-command-prompt).
- [Implementação automatizada](vmware-azure-mobility-install-configuration-mgr.md): Pode automatizar a instalação com ferramentas de implementação de software, como o Gestor de Configuração.

> [!NOTE]
> O agente de Mobilidade utiliza aproximadamente 6%-10% da memória em máquinas de origem para VMware VMs ou máquinas físicas.

## <a name="anti-virus-on-replicated-machines"></a>Antivírus em máquinas replicadas

Se as máquinas que pretende replicar tiverem software antivírus ativo em funcionamento, certifique-se de que exclui a pasta de instalação do serviço mobility de operações antivírus *(C:\ProgramData\ASR\agent).* Isso garante que a replicação funcione conforme o esperado.

## <a name="push-installation"></a>Instalação por push

A instalação push é parte integrante do trabalho de["Enable Replication](vmware-azure-enable-replication.md#enable-replication)" desencadeado no portal. Depois de escolher o conjunto de máquinas virtuais que você deseja proteger e disparar "Habilitar replicação", o servidor de configuração envia por push o agente de mobilidade para os servidores, instala o agente e completa o registro do agente com o servidor de configuração. Para a conclusão bem-sucedida dessa operação,

- Certifique-se de que todos os [pré-requisitos](vmware-azure-install-mobility-service.md) de instalação push são cumpridos.
- Certifique-se de que todas as configurações dos servidores se enquadram na [matriz de suporte do VMware para o cenário Azure DR](vmware-physical-azure-support-matrix.md).

Os detalhes do fluxo de trabalho de instalação por push foram descritos nas seções a seguir.

### <a name="from-923-version-onwards"></a>A partir de [9.23 versão](https://support.microsoft.com/en-in/help/4494485/update-rollup-35-for-azure-site-recovery) em diante

Durante a instalação por push do agente de mobilidade, as etapas a seguir são executadas

1. Envia o agente para o computador de origem. A cópia do agente no computador de origem pode falhar devido a vários erros ambientais. Visite [a nossa orientação](vmware-azure-troubleshoot-push-install.md) para resolver falhas de instalação de impulso.
2. Depois que o agente é copiado com êxito para as verificações de pré-requisitos do servidor são executadas no servidor. A instalação falha se um ou mais dos [pré-requisitos](vmware-physical-azure-support-matrix.md) não forem cumpridos. Se todos os pré-requisitos forem atendidos, a instalação será disparada.
3. Azure Site Recovery provedor VSS está instalado no servidor como parte da instalação do agente de mobilidade. Esse provedor é usado para gerar pontos consistentes do aplicativo. Se a instalação do provedor VSS falhar, esta etapa será ignorada e a instalação do agente continuará.
4. Se a instalação do agente for bem-sucedida, mas a instalação do provedor do VSS falhar, o status do trabalho será marcado como "aviso". Isso não afeta a geração de pontos de consistência de falha.

    a. Para gerar pontos consistentes de aplicação, consulte [a nossa orientação](vmware-physical-manage-mobility-service.md#install-site-recovery-vss-provider-on-source-machine) para completar a instalação do fornecedor VSS de Recuperação do Site manualmente. </br>
    b.  Se não desejar que sejam gerados pontos consistentes de aplicação, [modifique a política de replicação](vmware-azure-set-up-replication.md#create-a-policy) para desligar pontos consistentes da aplicação.

### <a name="before-922-versions"></a>Antes de 9,22 versões

1. Envia o agente para o computador de origem. A cópia do agente no computador de origem pode falhar devido a vários erros ambientais. Visite [a nossa orientação](vmware-azure-troubleshoot-push-install.md) para resolver falhas de instalação de impulso.
2. Depois que o agente é copiado com êxito para as verificações de pré-requisitos do servidor são executadas no servidor. A instalação falha se um ou mais dos [pré-requisitos](vmware-physical-azure-support-matrix.md) não forem cumpridos. Se todos os pré-requisitos forem atendidos, a instalação será disparada.
3. Azure Site Recovery provedor VSS está instalado no servidor como parte da instalação do agente de mobilidade. Esse provedor é usado para gerar pontos consistentes do aplicativo. Se a instalação do provedor VSS falhar, a instalação do agente falhará. Para evitar falhas na instalação do agente de mobilidade, utilize [a versão 9.23](https://support.microsoft.com/en-in/help/4494485/update-rollup-35-for-azure-site-recovery) ou superior para gerar pontos consistentes de colisão e instale manualmente o fornecedor VSS.

## <a name="install-mobility-agent-through-ui"></a>Instalar o agente de mobilidade por meio da interface do usuário

### <a name="prerequisite"></a>Pré-requisito

- Certifique-se de que todas as configurações dos servidores se enquadram na [matriz de suporte do VMware para o cenário Azure DR](vmware-physical-azure-support-matrix.md).
- [Localize o instalador](#locate-installer-files) com base no sistema operativo do servidor.

>[!IMPORTANT]
> Se você estiver replicando a VM IaaS do Azure de uma região do Azure para outra, não use esse método. Em vez disso, use o método de instalação baseado em linha de comando.

1. Copie o arquivo de instalação para o computador e execute-o.
2. Na **opção de instalação,** selecione **Instalar o serviço de mobilidade.**
3. Selecione a localização de instalação > **Instale**.

    ![Página opção de instalação do serviço de mobilidade](./media/vmware-physical-mobility-service-install-manual/mobility1.png)

4. Monitorize a instalação em Evolução da **Instalação**. Depois de terminar a instalação, selecione **'Proceder à Configuração'** para registar o serviço com o servidor de configuração.

    ![Página de registro do serviço de mobilidade](./media/vmware-physical-mobility-service-install-manual/mobility3.png)

5. Em Detalhes do Servidor de **Configuração,** especifique o endereço IP e a frase-passe configurada.

    ![Página de registro do serviço de mobilidade](./media/vmware-physical-mobility-service-install-manual/mobility4.png)

6. Selecione **Registar** para terminar a inscrição.

    ![Página final de registro do serviço de mobilidade](./media/vmware-physical-mobility-service-install-manual/mobility5.png)

## <a name="install-mobility-agent-through-command-prompt"></a>Instalar o agente de mobilidade por meio do prompt de comando

### <a name="prerequisite"></a>Pré-requisito

- Certifique-se de que todas as configurações dos servidores se enquadram na [matriz de suporte do VMware para o cenário Azure DR](vmware-physical-azure-support-matrix.md).
- [Localize o instalador](#locate-installer-files) com base no sistema operativo do servidor.

### <a name="on-a-windows-machine"></a>Em um computador Windows

- Copie o instalador para uma pasta local (por exemplo, C:\Temp) no servidor que você deseja proteger.

    ```
    cd C:\Temp
    ren Microsoft-ASR_UA*Windows*release.exe MobilityServiceInstaller.exe
    MobilityServiceInstaller.exe /q /x:C:\Temp\Extracted
    cd C:\Temp\Extracted
    ```

- Instale da seguinte maneira:

    ```
    UnifiedAgent.exe /Role "MS" /InstallLocation "C:\Program Files (x86)\Microsoft Azure Site Recovery" /Platform "VmWare" /Silent
    ```

- Registre o agente com o servidor de configuração.

    ```
    cd C:\Program Files (x86)\Microsoft Azure Site Recovery\agent
    UnifiedAgentConfigurator.exe  /CSEndPoint <CSIP> /PassphraseFilePath <PassphraseFilePath>
    ```

#### <a name="installation-settings"></a>Configurações de instalação
**Definição** | **Detalhes**
--- | ---
Utilização | Agente Unificado.exe /Função \<MS/MT> /InstalaçãoLocalização \<Instalar Localização> /Plataforma "VmWare" /Silencioso
Registos de configuração | Em %ProgramData%\ASRSetupLogs\ASRUnifiedAgentInstaller.log.
/Role | Parâmetro de instalação obrigatório. Especifica se o serviço de mobilidade (MS) ou o destino mestre (MT) deve ser instalado.
/InstallLocation| Parâmetro opcional. Especifica o local de instalação do serviço de mobilidade (qualquer pasta).
/Platform | Obrigatório. Especifica a plataforma na qual o serviço de mobilidade está instalado. **VMware** para VMware VMs/servidores físicos; **Azure** para VMs Azure.<br/><br/> Se estiver a tratar os VMs Azure como máquinas físicas, especifique **vMware**.
/Silêncio| Opcional. Especifica se o instalador deve ser executado no modo silencioso.

#### <a name="registration-settings"></a>Configurações de registro
**Definição** | **Detalhes**
--- | ---
Utilização | UnifiedAgentConfigurator.exe /CSEndPoint \<CSIP> /PassphraseFilePath \<PassphraseFilePath>
Logs de configuração do agente | Em %ProgramData%\ASRSetupLogs\ASRUnifiedAgentConfigurator.log.
/CSEndPoint | Parâmetro obrigatório. Especifica o endereço IP do servidor de configuração. Use qualquer endereço IP válido.
/PassphraseFilePath |  Obrigatório. Local da frase secreta. Use qualquer caminho de arquivo UNC ou local válido.

### <a name="on-a-linux-machine"></a>Em um computador Linux

1. Copie o instalador para uma pasta local (por exemplo,/tmp) no servidor que você deseja proteger. Em um terminal, execute os seguintes comandos:

    ```
    cd /tmp ;
    tar -xvf Microsoft-ASR_UA*release.tar.gz
    ```

2. Instale da seguinte maneira:

    ```
    sudo ./install -d <Install Location> -r MS -v VmWare -q
    ```

3. Após a conclusão da instalação, o serviço de mobilidade deve ser registrado no servidor de configuração. Execute o seguinte comando para registrar o serviço de mobilidade com o servidor de configuração:

    ```
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <CSIP> -P /var/passphrase.txt
    ```

#### <a name="installation-settings"></a>Configurações de instalação
**Definição** | **Detalhes**
--- | ---
Utilização | ./instalar -d \<Instalar Localização> -r \<MS/MT> -v VmWare -q
-r | Parâmetro de instalação obrigatório. Especifica se o serviço de mobilidade (MS) ou o destino mestre (MT) deve ser instalado.
-d | Parâmetro opcional. Especifica o local de instalação do serviço de mobilidade:/usr/local/ASR.
-v | Obrigatório. Especifica a plataforma na qual o serviço de mobilidade está instalado. **VMware** para VMware VMs/servidores físicos; **Azure** para VMs Azure.
-q | Opcional. Especifica se o instalador deve ser executado no modo silencioso.

#### <a name="registration-settings"></a>Configurações de registro
**Definição** | **Detalhes**
--- | ---
Utilização | CD/usr/local/ASR/Vx/bin<br/><br/> UnifiedAgentConfigurator.sh -i \<CSIP> -P \<PassphraseFilePath>
-i | Parâmetro obrigatório. Especifica o endereço IP do servidor de configuração. Use qualquer endereço IP válido.
-P |  Obrigatório. Caminho de arquivo completo do arquivo no qual a frase secreta é salva. Use qualquer pasta válida.

## <a name="azure-virtual-machine-agent"></a>Agente de máquina virtual do Azure

- **VMs do Windows**: A partir da versão 9.7.0.0 do serviço mobility, o [agente Azure VM](../virtual-machines/extensions/features-windows.md#azure-vm-agent) é instalado pelo instalador de serviços mobility. Isso garante que, quando o computador fizer failover no Azure, a VM do Azure atenderá ao pré-requisito de instalação do agente para usar qualquer extensão de VM.
- **VMs Linux**: O [WALinuxAgent](https://docs.microsoft.com/azure/virtual-machines/extensions/update-linux-agent) deve ser instalado manualmente no VM Azure após a falha.

## <a name="locate-installer-files"></a>Localizar arquivos do instalador

Vá para %ProgramData%\ASR\home\svsystems\pushinstallsvc\repositório no servidor de configuração. Verifique qual instalador é necessário com base no sistema operacional. A tabela a seguir resume os arquivos do instalador para cada sistema operacional VMware VM e servidor físico. Pode rever [os sistemas operativos suportados](vmware-physical-azure-support-matrix.md#replicated-machines) antes de iniciar.

**Arquivo de instalação** | **Sistema operativo (apenas 64 bits)**
--- | ---
Microsoft-ASR\_UA\*Windows\*release.exe | Windows Server 2016; Windows Server 2012 R2; Windows Server 2012; Windows Server 2008 R2 SP1
Microsoft-ASR\_UA\*RHEL6-64\*lançamento.tar.gz | Red Hat Enterprise Linux (RHEL) 6. * </br> CentOS 6. *
Microsoft-ASR\_UA\*RHEL7-64\*lançamento.tar.gz | Red Hat Enterprise Linux (RHEL) 7. * </br> CentOS 7. *
Microsoft-ASR\_UA\*SLES12-64\*lançamento.tar.gz | SUSE Linux Enterprise Server 12 SP1, SP2, SP3
Microsoft-ASR\_UA\*SLES11-SP3-64\*lançamento.tar.gz| SUSE Linux Enterprise Server 11 SP3
Microsoft-ASR\_UA\*SLES11-SP4-64\*lançamento.tar.gz| SUSE Linux Enterprise Server 11 SP4
Microsoft-ASR\_UA\*OL6-64\*lançamento.tar.gz | Oracle Enterprise Linux 6,4, 6,5
Microsoft-ASR\_UA\*UBUNTU-14.04-64\*release.tar.gz | Ubuntu Linux 14.04
Microsoft-ASR\_UA\*UBUNTU-16.04-64\*release.tar.gz | Ubuntu Linux servidor LTS 16, 4
Microsoft-ASR_UA\*DEBIAN7-64\*lançamento.tar.gz | Debiano 7
Microsoft-ASR_UA\*DEBIAN8-64\*lançamento.tar.gz | Debian 8

## <a name="next-steps"></a>Passos seguintes

[Instale a instalação de impulso para o serviço de Mobilidade](vmware-azure-install-mobility-service.md).
