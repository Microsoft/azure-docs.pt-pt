---
title: Sobre o serviço de mobilidade para recuperação de desastre de VMs VMware e servidores físicos com Azure Site Recovery | Microsoft Docs
description: Saiba mais sobre o agente do serviço de mobilidade para recuperação de desastre de VMs VMware e servidores físicos no Azure usando o serviço Azure Site Recovery.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: ramamill
ms.openlocfilehash: b2c59fd6ee925d531a5a5ff3bb26fdebea025b83
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/22/2020
ms.locfileid: "76513563"
---
# <a name="about-the-mobility-service-for-vmware-vms-and-physical-servers"></a>Sobre o serviço de mobilidade para VMs VMware e servidores físicos

Ao configurar a recuperação de desastre para VMs VMware e servidores físicos usando [Azure site Recovery](site-recovery-overview.md), você instala o serviço de mobilidade de site Recovery em cada servidor físico e VM VMware local.  O serviço de mobilidade captura gravações de dados no computador e as encaminha para o servidor de processo de Site Recovery. Você pode implantar o serviço de mobilidade usando os seguintes métodos:

- [Instalação por push](#push-installation): Site Recovery instala o agente de mobilidade no servidor quando a proteção é habilitada via portal do Azure.
- Instalar manualmente: você pode instalar o serviço de mobilidade manualmente em cada computador por meio [da interface do usuário ou do](#install-mobility-agent-through-ui) [prompt de comando](#install-mobility-agent-through-command-prompt).
- [Implantação automatizada](vmware-azure-mobility-install-configuration-mgr.md): você pode automatizar a instalação com ferramentas de implantação de software, como Configuration Manager.

## <a name="anti-virus-on-replicated-machines"></a>Antivírus em máquinas replicadas

Se as máquinas que você deseja replicar tiverem um software antivírus ativo em execução, certifique-se de excluir a pasta de instalação do serviço de mobilidade das operações de antivírus (*C:\ProgramData\ASR\agent*). Isso garante que a replicação funcione conforme o esperado.

## <a name="push-installation"></a>Instalação por push

A instalação por push é parte integrante do trabalho de "[habilitar replicação](vmware-azure-enable-replication.md#enable-replication)" disparado no Portal. Depois de escolher o conjunto de máquinas virtuais que você deseja proteger e disparar "Habilitar replicação", o servidor de configuração envia por push o agente de mobilidade para os servidores, instala o agente e completa o registro do agente com o servidor de configuração. Para a conclusão bem-sucedida dessa operação,

- Verifique se todos os [pré-requisitos](vmware-azure-install-mobility-service.md) de instalação por push foram atendidos.
- Verifique se todas as configurações de servidores enquadram-se na [matriz de suporte do VMware para o cenário de Dr do Azure](vmware-physical-azure-support-matrix.md).

Os detalhes do fluxo de trabalho de instalação por push foram descritos nas seções a seguir.

### <a name="from-923-versionhttpssupportmicrosoftcomen-inhelp4494485update-rollup-35-for-azure-site-recovery-onwards"></a>Da [versão 9,23](https://support.microsoft.com/en-in/help/4494485/update-rollup-35-for-azure-site-recovery) em diante

Durante a instalação por push do agente de mobilidade, as etapas a seguir são executadas

1. Envia o agente para o computador de origem. A cópia do agente no computador de origem pode falhar devido a vários erros ambientais. Visite [nossas diretrizes](vmware-azure-troubleshoot-push-install.md) para solucionar falhas de instalação por push.
2. Depois que o agente é copiado com êxito para as verificações de pré-requisitos do servidor são executadas no servidor. A instalação falhará se um ou mais dos [pré-requisitos](vmware-physical-azure-support-matrix.md) não forem atendidos. Se todos os pré-requisitos forem atendidos, a instalação será disparada.
3. Azure Site Recovery provedor VSS está instalado no servidor como parte da instalação do agente de mobilidade. Esse provedor é usado para gerar pontos consistentes do aplicativo. Se a instalação do provedor VSS falhar, esta etapa será ignorada e a instalação do agente continuará.
4. Se a instalação do agente for bem-sucedida, mas a instalação do provedor do VSS falhar, o status do trabalho será marcado como "aviso". Isso não afeta a geração de pontos de consistência de falha.

    a. Para gerar pontos consistentes do aplicativo, consulte [nossas diretrizes](vmware-physical-manage-mobility-service.md#install-site-recovery-vss-provider-on-source-machine) para concluir a instalação do provedor de site Recovery VSS manualmente. </br>
    b.  Se você não quiser que os pontos consistentes do aplicativo sejam gerados, [modifique a política de replicação](vmware-azure-set-up-replication.md#create-a-policy) para desativar os pontos consistentes do aplicativo.

### <a name="before-922-versions"></a>Antes de 9,22 versões

1. Envia o agente para o computador de origem. A cópia do agente no computador de origem pode falhar devido a vários erros ambientais. Visite [nossas diretrizes](vmware-azure-troubleshoot-push-install.md) para solucionar falhas de instalação por push.
2. Depois que o agente é copiado com êxito para as verificações de pré-requisitos do servidor são executadas no servidor. A instalação falhará se um ou mais dos [pré-requisitos](vmware-physical-azure-support-matrix.md) não forem atendidos. Se todos os pré-requisitos forem atendidos, a instalação será disparada.
3. Azure Site Recovery provedor VSS está instalado no servidor como parte da instalação do agente de mobilidade. Esse provedor é usado para gerar pontos consistentes do aplicativo. Se a instalação do provedor VSS falhar, a instalação do agente falhará. Para evitar a falha da instalação do agente de mobilidade, use a [versão 9,23](https://support.microsoft.com/en-in/help/4494485/update-rollup-35-for-azure-site-recovery) ou superior para gerar pontos consistentes de falha e instalar o provedor do VSS manualmente.

## <a name="install-mobility-agent-through-ui"></a>Instalar o agente de mobilidade por meio da interface do usuário

### <a name="prerequisite"></a>Pré-requisito

- Verifique se todas as configurações de servidores enquadram-se na [matriz de suporte do VMware para o cenário de Dr do Azure](vmware-physical-azure-support-matrix.md).
- [Localize o instalador](#locate-installer-files) com base no sistema operacional do servidor.

>[!IMPORTANT]
> Se você estiver replicando a VM IaaS do Azure de uma região do Azure para outra, não use esse método. Em vez disso, use o método de instalação baseado em linha de comando.

1. Copie o arquivo de instalação para o computador e execute-o.
2. Em **opção de instalação**, selecione **instalar serviço de mobilidade**.
3. Selecione o local de instalação > **instalar**.

    ![Página opção de instalação do serviço de mobilidade](./media/vmware-physical-mobility-service-install-manual/mobility1.png)

4. Monitore a instalação em **andamento da instalação**. Após a conclusão da instalação, selecione **prosseguir para a configuração** para registrar o serviço no servidor de configuração.

    ![Página de registro do serviço de mobilidade](./media/vmware-physical-mobility-service-install-manual/mobility3.png)

5. Em **detalhes do servidor de configuração**, especifique o endereço IP e a senha que você configurou.  

    ![Página de registro do serviço de mobilidade](./media/vmware-physical-mobility-service-install-manual/mobility4.png)

6. Selecione **registrar** para concluir o registro.

    ![Página final de registro do serviço de mobilidade](./media/vmware-physical-mobility-service-install-manual/mobility5.png)

## <a name="install-mobility-agent-through-command-prompt"></a>Instalar o agente de mobilidade por meio do prompt de comando

### <a name="prerequisite"></a>Pré-requisito

- Verifique se todas as configurações de servidores enquadram-se na [matriz de suporte do VMware para o cenário de Dr do Azure](vmware-physical-azure-support-matrix.md).
- [Localize o instalador](#locate-installer-files) com base no sistema operacional do servidor.

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
Utilização | UnifiedAgent. exe/role \<MS/MT >/InstallLocation \<instalação local >/Platform "VmWare"/Silent
Registos de configuração | Em%ProgramData%\ASRSetupLogs\ASRUnifiedAgentInstaller.log.
/Role | Parâmetro de instalação obrigatório. Especifica se o serviço de mobilidade (MS) ou o destino mestre (MT) deve ser instalado.
/InstallLocation| Parâmetro opcional. Especifica o local de instalação do serviço de mobilidade (qualquer pasta).
/Platform | Obrigatório. Especifica a plataforma na qual o serviço de mobilidade está instalado. **VMware** para VMs VMware/servidores físicos; **Azure** para VMs do Azure.<br/><br/> Se você estiver tratando VMs do Azure como máquinas físicas, especifique **VMware**.
/Silent| Opcional. Especifica se o instalador deve ser executado no modo silencioso.

#### <a name="registration-settings"></a>Configurações de registro
**Definição** | **Detalhes**
--- | ---
Utilização | UnifiedAgentConfigurator. exe/CSEndPoint \<CSIP >/PassphraseFilePath \<PassphraseFilePath >
Logs de configuração do agente | Em%ProgramData%\ASRSetupLogs\ASRUnifiedAgentConfigurator.log.
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
Utilização | ./Install-d \<local de instalação >-r \<MS/MT >-v VmWare-q
-r | Parâmetro de instalação obrigatório. Especifica se o serviço de mobilidade (MS) ou o destino mestre (MT) deve ser instalado.
-d | Parâmetro opcional. Especifica o local de instalação do serviço de mobilidade:/usr/local/ASR.
-v | Obrigatório. Especifica a plataforma na qual o serviço de mobilidade está instalado. **VMware** para VMs VMware/servidores físicos; **Azure** para VMs do Azure.
-q | Opcional. Especifica se o instalador deve ser executado no modo silencioso.

#### <a name="registration-settings"></a>Configurações de registro
**Definição** | **Detalhes**
--- | ---
Utilização | CD/usr/local/ASR/Vx/bin<br/><br/> UnifiedAgentConfigurator.sh-i \<CSIP >-P \<PassphraseFilePath >
-i | Parâmetro obrigatório. Especifica o endereço IP do servidor de configuração. Use qualquer endereço IP válido.
-P |  Obrigatório. Caminho de arquivo completo do arquivo no qual a frase secreta é salva. Use qualquer pasta válida.

## <a name="azure-virtual-machine-agent"></a>Agente de máquina virtual do Azure

- **VMs do Windows**: da versão 9.7.0.0 do serviço de mobilidade, o [agente de VM do Azure](../virtual-machines/extensions/features-windows.md#azure-vm-agent) é instalado pelo instalador do serviço de mobilidade. Isso garante que, quando o computador fizer failover no Azure, a VM do Azure atenderá ao pré-requisito de instalação do agente para usar qualquer extensão de VM.
- **VMs do Linux**: o [WALinuxAgent](https://docs.microsoft.com/azure/virtual-machines/extensions/update-linux-agent) deve ser instalado manualmente na VM do Azure após o failover.

## <a name="locate-installer-files"></a>Localizar arquivos do instalador

Vá para a pasta%ProgramData%\ASR\home\svsystems\pushinstallsvc\repository no servidor de configuração. Verifique qual instalador é necessário com base no sistema operacional. A tabela a seguir resume os arquivos do instalador para cada sistema operacional VMware VM e servidor físico. Você pode examinar os [sistemas operacionais com suporte](vmware-physical-azure-support-matrix.md#replicated-machines) antes de começar.

**Arquivo do instalador** | **Sistema operacional (somente 64 bits)**
--- | ---
Microsoft-ASR\_UA\*Windows\*release.exe | Windows Server 2016; Windows Server 2012 R2; Windows Server 2012; Windows Server 2008 R2 SP1
Microsoft-ASR\_UA\*RHEL6-64\*release.tar.gz | Red Hat Enterprise Linux (RHEL) 6. * </br> CentOS 6. *
Microsoft-ASR\_UA\*RHEL7-64\*release.tar.gz | Red Hat Enterprise Linux (RHEL) 7. * </br> CentOS 7. *
Microsoft-ASR\_UA\*SLES12-64\*release.tar.gz | SUSE Linux Enterprise Server 12 SP1, SP2, SP3
Microsoft-ASR\_UA\*SLES11-SP3-64\*release.tar.gz| SUSE Linux Enterprise Server 11 SP3
Microsoft-ASR\_UA\*SLES11-SP4-64\*release.tar.gz| SUSE Linux Enterprise Server 11 SP4
Microsoft-ASR\_UA\*OL6-64\*release.tar.gz | Oracle Enterprise Linux 6,4, 6,5
Microsoft-ASR\_UA\*UBUNTU-14.04-64\*release.tar.gz | Ubuntu Linux 14.04
Microsoft-ASR\_UA\*UBUNTU-16.04-64\*release.tar.gz | Ubuntu Linux servidor LTS 16, 4
Microsoft-ASR_UA\*DEBIAN7-64\*release.tar.gz | Debian 7
Microsoft-ASR_UA\*DEBIAN8-64\*release.tar.gz | Debian 8

## <a name="next-steps"></a>Passos seguintes

[Configure a instalação por push para o serviço de mobilidade](vmware-azure-install-mobility-service.md).
