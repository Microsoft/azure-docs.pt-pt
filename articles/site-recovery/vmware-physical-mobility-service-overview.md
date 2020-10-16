---
title: Sobre o serviço de Mobilidade para recuperação de desastres de VMware VMs e servidores físicos com Recuperação do Site Azure Microsoft Docs
description: Saiba mais sobre o agente de serviço de mobilidade para a recuperação de desastres de VMware VMs e servidores físicos para Azure usando o serviço de Recuperação do Local Azure.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: how-to
ms.date: 04/10/2020
ms.author: ramamill
ms.openlocfilehash: 14f0eaee1ede4da3b80ddd94d5c915438e97f8f4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90530068"
---
# <a name="about-the-mobility-service-for-vmware-vms-and-physical-servers"></a>Sobre o serviço de Mobilidade de VMware VMs e servidores físicos

Quando configurar a recuperação de desastres para máquinas virtuais VMware (VM) e servidores físicos utilizando [a Recuperação do Site Azure,](site-recovery-overview.md)instale o serviço de Mobilidade de Recuperação do Site em cada VMware VMware e servidor físico no local. O serviço Mobility captura os dados escritos na máquina e reencaminha-os para o servidor de processo de Recuperação do Site. O serviço Mobility é instalado pelo software de serviço de serviço de mobilidade que pode implementar utilizando os seguintes métodos:

- [Instalação push](#push-installation): Quando a proteção é ativada através do portal Azure, a Recuperação do Local instala o serviço de Mobilidade no servidor.
- Instalação manual: Pode instalar manualmente o serviço mobility em cada máquina através da interface do [utilizador (UI)](#install-the-mobility-service-using-ui) ou [do pedido de comando](#install-the-mobility-service-using-command-prompt).
- [Implantação automatizada](vmware-azure-mobility-install-configuration-mgr.md): Pode automatizar a instalação do serviço mobility com ferramentas de implementação de software, tais como o Gestor de Configuração.

> [!NOTE]
> O serviço mobility usa aproximadamente 6%-10% de memória em máquinas de origem para VMware vMs ou máquinas físicas.

## <a name="antivirus-on-replicated-machines"></a>Antivírus em máquinas replicadas

Se as máquinas que pretende replicar estiverem a executar software antivírus, exclua a pasta de instalação do serviço de Mobilidade _C:\ProgramData\ASR\agente_ das operações antivírus. Esta exclusão garante que a replicação funcionará como esperado.

## <a name="push-installation"></a>Instalação push

A instalação push é parte integrante do trabalho que é executado a partir do portal Azure para permitir a [replicação](vmware-azure-enable-replication.md#enable-replication). Depois de escolher o conjunto de VMs que pretende proteger e ativar a replicação, o servidor de configuração empurra o agente de serviço de Mobilidade para os servidores, instala o agente e completa o registo do agente com o servidor de configuração.

### <a name="prerequisites"></a>Pré-requisitos

- Certifique-se de que todos os [pré-requisitos de](vmware-azure-install-mobility-service.md) instalação de impulsos estão cumpridos.
- Certifique-se de que todas as configurações do servidor satisfazem os critérios na [matriz de Suporte para a recuperação de desastres de VMware VMs e servidores físicos para a Azure](vmware-physical-azure-support-matrix.md).
- A partir da versão 9.36, para sUSE Linux Enterprise Server 11 SP3, RHEL 5, CentOS 5, Debian 7 garantir que o mais recente instalador está [disponível no servidor de configuração e servidor de processo de escala](#download-latest-mobility-agent-installer-for-suse-11-sp3-rhel-5-debian-7-server)

O fluxo de trabalho de instalação push é descrito nas seguintes secções:

### <a name="mobility-service-agent-version-923-and-higher"></a>Agente de serviço de mobilidade versão 9.23 e superior

Para obter mais informações sobre a versão 9.23 consulte [Update Rollup 35 para recuperação do site Azure](https://support.microsoft.com/help/4494485/update-rollup-35-for-azure-site-recovery).

Durante uma instalação push do serviço mobility, são executadas as seguintes etapas:

1. O agente é empurrado para a máquina de origem. Copiar o agente para a máquina de origem pode falhar devido a múltiplos erros ambientais. Visite a [nossa orientação](vmware-azure-troubleshoot-push-install.md) para resolver problemas com falhas de instalação.
1. Depois de o agente ser copiado com sucesso para o servidor, é efetuada uma verificação prévia no servidor.
   - Se todos os pré-requisitos forem cumpridos, a instalação começa.
   - A instalação falha se um ou mais dos [pré-requisitos](vmware-physical-azure-support-matrix.md) não forem cumpridos.
1. Como parte da instalação do agente, é instalado o fornecedor volume shadow copy service (VSS) para a recuperação do local de Azure. O fornecedor VSS é utilizado para gerar pontos de recuperação consistentes com aplicações. Se a instalação do fornecedor VSS falhar, este passo é ignorado e a instalação do agente continua.
1. Se a instalação do agente for bem sucedida, mas a instalação do fornecedor VSS falhar, então o estado do trabalho é marcado como **Aviso**. Isto não afeta a geração de pontos de recuperação consistentes.

    - Para gerar pontos de recuperação consistentes com aplicações, consulte as [nossas orientações](vmware-physical-manage-mobility-service.md#install-site-recovery-vss-provider-on-source-machine) para completar uma instalação manual do fornecedor VSS de Recuperação do Local.
    - Se não pretender gerar pontos de recuperação consistentes com aplicações, [modifique a política](vmware-azure-set-up-replication.md#create-a-policy) de replicação para desligar pontos de recuperação consistentes com aplicações.

### <a name="mobility-service-agent-version-922-and-below"></a>Agente de serviço de mobilidade versão 9.22 e abaixo

1. O agente é empurrado para a máquina de origem. Copiar o agente para a máquina de origem pode falhar devido a múltiplos erros ambientais. Consulte a [nossa orientação](vmware-azure-troubleshoot-push-install.md) para resolver problemas com as falhas de instalação.
1. Depois de o agente ser copiado com sucesso para o servidor, é efetuada uma verificação prévia no servidor.
   - Se todos os pré-requisitos forem cumpridos, a instalação começa.
   - A instalação falha se um ou mais dos [pré-requisitos](vmware-physical-azure-support-matrix.md) não forem cumpridos.

1. Como parte da instalação do agente, é instalado o fornecedor volume shadow copy service (VSS) para a recuperação do local de Azure. O fornecedor VSS é utilizado para gerar pontos de recuperação consistentes com aplicações.
   - Se a instalação do fornecedor VSS falhar, a instalação do agente falhará. Para evitar falhas na instalação do agente, utilize a [versão 9.23](https://support.microsoft.com/help/4494485/update-rollup-35-for-azure-site-recovery) ou superior para gerar pontos de recuperação consistentes e faça uma instalação manual do fornecedor VSS.

## <a name="install-the-mobility-service-using-ui"></a>Instale o serviço de Mobilidade utilizando uI

### <a name="prerequisites"></a>Pré-requisitos

- Certifique-se de que todas as configurações do servidor satisfazem os critérios na [matriz de Suporte para a recuperação de desastres de VMware VMs e servidores físicos para a Azure](vmware-physical-azure-support-matrix.md).
- [Localize o instalador](#locate-installer-files) para o sistema operativo do servidor.

>[!IMPORTANT]
> Não utilize o método de instalação da UI se estiver a replicar uma Infraestrutura Azure como um VM de serviço (IaaS) de uma região de Azure para outra. Utilize a instalação [do pedido de comando.](#install-the-mobility-service-using-command-prompt)

1. Copie o ficheiro de instalação para a máquina e execute-o.
1. Na **Opção de Instalação**, selecione **Instalar o serviço de mobilidade.**
1. Escolha o local de instalação e **selecione Instalar.**

    :::image type="content" source="./media/vmware-physical-mobility-service-install-manual/mobility1.png" alt-text="Página de opções de instalação do serviço de mobilidade.":::

1. Monitorize a instalação em **"Installation Progress".** Depois de concluída a instalação, **selecione Proceder à Configuração** para registar o serviço com o servidor de configuração.

    :::image type="content" source="./media/vmware-physical-mobility-service-install-manual/mobility3.png" alt-text="Página de opções de instalação do serviço de mobilidade.":::

1. Nos **Detalhes do Servidor de Configuração**, especifique o endereço IP e a frase de passe que configura.

    :::image type="content" source="./media/vmware-physical-mobility-service-install-manual/mobility4.png" alt-text="Página de opções de instalação do serviço de mobilidade.":::

1. **Selecione Registar-se** para terminar a inscrição.

    :::image type="content" source="./media/vmware-physical-mobility-service-install-manual/mobility5.png" alt-text="Página de opções de instalação do serviço de mobilidade.":::

## <a name="install-the-mobility-service-using-command-prompt"></a>Instale o serviço de Mobilidade utilizando o pedido de comando

### <a name="prerequisites"></a>Pré-requisitos

- Certifique-se de que todas as configurações do servidor satisfazem os critérios na [matriz de Suporte para a recuperação de desastres de VMware VMs e servidores físicos para a Azure](vmware-physical-azure-support-matrix.md).
- [Localize o instalador](#locate-installer-files) para o sistema operativo do servidor.

### <a name="windows-machine"></a>Máquina windows

- A partir de um pedido de comando, executar os seguintes comandos para copiar o instalador para uma pasta local, como _C:\Temp,_ no servidor que pretende proteger. Substitua o nome do ficheiro do instalador pelo nome do ficheiro real.

  ```cmd
  cd C:\Temp
  ren Microsoft-ASR_UA*Windows*release.exe MobilityServiceInstaller.exe
  MobilityServiceInstaller.exe /q /x:C:\Temp\Extracted
  cd C:\Temp\Extracted
  ```

- Executar este comando para instalar o agente.

  ```cmd
  UnifiedAgent.exe /Role "MS" /InstallLocation "C:\Program Files (x86)\Microsoft Azure Site Recovery" /Platform "VmWare" /Silent
  ```

- Executar estes comandos para registar o agente com o servidor de configuração.

  ```cmd
  cd C:\Program Files (x86)\Microsoft Azure Site Recovery\agent
  UnifiedAgentConfigurator.exe  /CSEndPoint <CSIP> /PassphraseFilePath <PassphraseFilePath>
  ```

#### <a name="installation-settings"></a>Definições de instalação

Definição | Detalhes
--- | ---
Syntax | `UnifiedAgent.exe /Role \<MS/MT> /InstallLocation \<Install Location> /Platform "VmWare" /Silent`
Registos de configuração | `%ProgramData%\ASRSetupLogs\ASRUnifiedAgentInstaller.log`
`/Role` | Parâmetro de instalação obrigatório. Especifica se o serviço de mobilidade (MS) ou o alvo principal (MT) devem ser instalados.
`/InstallLocation`| Parâmetro opcional. Especifica o local de instalação do serviço de mobilidade (qualquer pasta).
`/Platform` | Obrigatório. Especifica a plataforma na qual o serviço mobility está instalado: <br/> **VMware** para VMware VMs/servidores físicos. <br/> **Azure** para Azure VMs.<br/><br/> Se estiver a tratar os VMs do Azure como máquinas físicas, especifique **o VMware**.
`/Silent`| Opcional. Especifica se deve executar o instalador em modo silencioso.

#### <a name="registration-settings"></a>Definições de registo

Definição | Detalhes
--- | ---
Syntax | `UnifiedAgentConfigurator.exe  /CSEndPoint \<CSIP> /PassphraseFilePath \<PassphraseFilePath>`
Registos de configuração do agente | `%ProgramData%\ASRSetupLogs\ASRUnifiedAgentConfigurator.log`
`/CSEndPoint` | Parâmetro obrigatório. `<CSIP>` especifica o endereço IP do servidor de configuração. Utilize qualquer endereço IP válido.
`/PassphraseFilePath` |  Obrigatório. Localização da frase. Utilize qualquer unc válido ou caminho de arquivo local.

### <a name="linux-machine"></a>Máquina Linux

1. A partir de uma sessão terminal, copie o instalador para uma pasta local, como _/tmp_ no servidor que pretende proteger. Substitua o nome do ficheiro do instalador pelo nome de ficheiro real da sua distribuição Linux e, em seguida, execute os comandos.

   ```shell
   cd /tmp ;
   tar -xvf Microsoft-ASR_UA_version_LinuxVersion_GA_date_release.tar.gz
   ```

2. Instale da seguinte forma:

   ```shell
   sudo ./install -d <Install Location> -r MS -v VmWare -q
   ```

3. Após a instalação estar concluída, o serviço Mobility deve ser registado no servidor de configuração. Executar o seguinte comando para registar o serviço Mobility com o servidor de configuração.

   ```shell
   /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <CSIP> -P /var/passphrase.txt
   ```

#### <a name="installation-settings"></a>Definições de instalação

Definição | Detalhes
--- | ---
Syntax | `./install -d \<Install Location> -r \<MS/MT> -v VmWare -q`
`-r` | Parâmetro de instalação obrigatório. Especifica se o serviço de mobilidade (MS) ou o alvo principal (MT) devem ser instalados.
`-d` | Parâmetro opcional. Especifica o local de instalação do serviço de mobilidade: `/usr/local/ASR` .
`-v` | Obrigatório. Especifica a plataforma na qual o serviço Mobility está instalado. <br/> **VMware** para VMware VMs/servidores físicos. <br/> **Azure** para Azure VMs.
`-q` | Opcional. Especifica se deve executar o instalador em modo silencioso.

#### <a name="registration-settings"></a>Definições de registo

Definição | Detalhes
--- | ---
Syntax | `cd /usr/local/ASR/Vx/bin<br/><br/> UnifiedAgentConfigurator.sh -i \<CSIP> -P \<PassphraseFilePath>`
`-i` | Parâmetro obrigatório. `<CSIP>` especifica o endereço IP do servidor de configuração. Utilize qualquer endereço IP válido.
`-P` |  Obrigatório. Percurso de ficheiro completo do ficheiro no qual a palavra-passe é guardada. Utilize qualquer pasta válida.

## <a name="azure-virtual-machine-agent"></a>Agente de máquina virtual Azure

- **VMs do Windows**: Da versão 9.7.0.0 do serviço mobility, o [agente Azure VM](../virtual-machines/extensions/features-windows.md#azure-vm-agent) é instalado pelo instalador do serviço Mobility. Isto garante que quando a máquina falha em Azure, o Azure VM satisfaz o pré-requisito de instalação do agente para a utilização de qualquer extensão VM.
- **Linux VMs**: O  [WALinuxAgent](../virtual-machines/extensions/update-linux-agent.md) deve ser instalado manualmente no Azure VM após a falha.

## <a name="locate-installer-files"></a>Localizar ficheiros de instaladores

No servidor de configuração aceda à pasta _%ProgramData%\ASR\home\home\svsystems\pushinstallsvc\repositório_. Verifique qual o instalador de que necessita com base no sistema operativo. A tabela seguinte resume os ficheiros do instalador para cada VMware VM e sistema operativo de servidor físico. Antes de começar, pode rever os [sistemas operativos suportados](vmware-physical-azure-support-matrix.md#replicated-machines).

> [!NOTE]
> Os nomes dos ficheiros utilizam a sintaxe mostrada na tabela seguinte com _versão_ e _data_ como espaços reservados para os valores reais. Os nomes dos ficheiros reais serão semelhantes a estes exemplos:
> - `Microsoft-ASR_UA_9.30.0.0_Windows_GA_22Oct2019_release.exe`
> - `Microsoft-ASR_UA_9.30.0.0_UBUNTU-16.04-64_GA_22Oct2019_release.tar.gz`

Ficheiro instalador | Sistema operativo (apenas 64 bits)
--- | ---
`Microsoft-ASR_UA_version_Windows_GA_date_release.exe` | Windows Server 2016 </br> Windows Server 2012 R2 </br> Windows Server 2012 </br> Windows Server 2008 R2 SP1
[Para ser descarregado e colocado nesta pasta manualmente](#rhel-5-or-centos-5-server) | Red Hat Enterprise Linux (RHEL) 5 </br> CentOS 5
`Microsoft-ASR_UA_version_RHEL6-64_GA_date_release.tar.gz` | Red Hat Enterprise Linux (RHEL) 6 </br> CentOS 6
`Microsoft-ASR_UA_version_RHEL7-64_GA_date_release.tar.gz` | Red Hat Enterprise Linux (RHEL) 7 </br> CentOS 7
`Microsoft-ASR_UA_version_RHEL8-64_GA_date_release.tar.gz` | Red Hat Enterprise Linux (RHEL) 8 </br> CentOS 8
`Microsoft-ASR_UA_version_SLES12-64_GA_date_release.tar.gz` | SUSE Linux Enterprise Server 12 SP1 </br> Inclui SP2 e SP3.
[Para ser descarregado e colocado nesta pasta manualmente](#suse-11-sp3-server) | SUSE Linux Enterprise Server 11 SP3
`Microsoft-ASR_UA_version_SLES11-SP4-64_GA_date_release.tar.gz` | SUSE Linux Enterprise Server 11 SP4
`Microsoft-ASR_UA_version_SLES15-64_GA_date_release.tar.gz` | SUSE Linux Enterprise Server 15 
`Microsoft-ASR_UA_version_OL6-64_GA_date_release.tar.gz` | Oracle Enterprise Linux 6.4 </br> Oracle Enterprise Linux 6.5
`Microsoft-ASR_UA_version_OL7-64_GA_date_release.tar.gz` | Oracle Enterprise Linux 7 
`Microsoft-ASR_UA_version_OL8-64_GA_date_release.tar.gz` | Oracle Enterprise Linux 8
`Microsoft-ASR_UA_version_UBUNTU-14.04-64_GA_date_release.tar.gz` | Ubuntu Linux 14.04
`Microsoft-ASR_UA_version_UBUNTU-16.04-64_GA_date_release.tar.gz` | Servidor Ubuntu Linux 16.04 LTS
`Microsoft-ASR_UA_version_UBUNTU-18.04-64_GA_date_release.tar.gz` | Servidor Ubuntu Linux 18.04 LTS
`Microsoft-ASR_UA_version_UBUNTU-20.04-64_GA_date_release.tar.gz` | Servidor Ubuntu Linux 20.04 LTS
[Para ser descarregado e colocado nesta pasta manualmente](#debian-7-server) | Debian 7
`Microsoft-ASR_UA_version_DEBIAN8-64_GA_date_release.tar.gz` | Debian 8
`Microsoft-ASR_UA_version_DEBIAN9-64_GA_date_release.tar.gz` | Debian 9

## <a name="download-latest-mobility-agent-installer-for-suse-11-sp3-rhel-5-debian-7-server"></a>Descarregue o mais recente instalador de agentes de mobilidade para SUSE 11 SP3, RHEL 5, Servidor Debian 7

### <a name="suse-11-sp3-server"></a>Servidor SUSE 11 SP3

Como **pré-requisito para atualizar ou proteger as máquinas SUSE Linux Enterprise Server 11 SP3** a partir da versão 9.36:

1. Certifique-se de que o mais recente instalador de agentes de mobilidade é descarregado do Microsoft Download Center e colocado no repositório de instaladores push no servidor de configuração e em todos os servidores de processos de escala
2. [Descarregue o](site-recovery-whats-new.md) mais recente instalador de agente SUSE Linux Enterprise Server 11 SP3. A versão mais recente do agente de mobilidade é [9.37](https://support.microsoft.com/help/4582666/)
3. Navegue para o servidor de configuração, copie o instalador de agente SUSE Linux Enterprise Server 11 SP3 no caminho - INSTALL_DIR\home\svsystems\pushinstallsvc\repositório
1. Depois de copiar o instalador mais recente, reinicie o serviço InMage PushInstall. 
1. Agora, navegue para servidores de processo de escala associados, repita o passo 3 e o passo 4.
1. **Por exemplo**, se o caminho de instalação for C:\Program Files (x86)\Microsoft Azure Site Recovery, então os diretórios acima mencionados serão
    1. C:\Ficheiros de programas (x86)\Microsoft Azure Site Recovery\home\svsystems\pushinstallsvc\repositório

### <a name="rhel-5-or-centos-5-server"></a>Servidor RHEL 5 ou CentOS 5

Como **pré-requisito para atualizar ou proteger as máquinas RHEL 5** a partir da versão 9.36:

1. Certifique-se de que o mais recente instalador de agentes de mobilidade é descarregado do Microsoft Download Center e colocado no repositório de instaladores push no servidor de configuração e em todos os servidores de processos de escala
2. [Descarregue o](site-recovery-whats-new.md) mais recente instalador de agente RHEL 5 ou CentOS 5. A versão mais recente do agente de mobilidade é [9.37](https://support.microsoft.com/help/4582666/)
3. Navegue para servidor de configuração, copie o instalador de agente RHEL 5 ou CentOS 5 no caminho - INSTALL_DIR\home\svsystems\pushinstallsvc\repositório
1. Depois de copiar o instalador mais recente, reinicie o serviço InMage PushInstall. 
1. Agora, navegue para servidores de processo de escala associados, repita o passo 3 e o passo 4.
1. **Por exemplo**, se o caminho de instalação for C:\Program Files (x86)\Microsoft Azure Site Recovery, então os diretórios acima mencionados serão
    1. C:\Ficheiros de programas (x86)\Microsoft Azure Site Recovery\home\svsystems\pushinstallsvc\repositório

## <a name="debian-7-server"></a>Servidor Debian 7

Como **pré-requisito para atualizar ou proteger as máquinas Debian 7** a partir da versão 9.36:

1. Certifique-se de que o mais recente instalador de agentes de mobilidade é descarregado do Microsoft Download Center e colocado no repositório de instaladores push no servidor de configuração e em todos os servidores de processos de escala
2. [Descarregue o](site-recovery-whats-new.md) mais recente instalador de agente Debian 7. A versão mais recente do agente de mobilidade é [9.37](https://support.microsoft.com/help/4582666/)
3. Navegue para o servidor de configuração, copie o instalador de agente Debian 7 no caminho - INSTALL_DIR\home\svsystems\pushinstallsvc\repositório
1. Depois de copiar o instalador mais recente, reinicie o serviço InMage PushInstall. 
1. Agora, navegue para servidores de processo de escala associados, repita o passo 3 e o passo 4.
1. **Por exemplo**, se o caminho de instalação for C:\Program Files (x86)\Microsoft Azure Site Recovery, então os diretórios acima mencionados serão
    1. C:\Ficheiros de programas (x86)\Microsoft Azure Site Recovery\home\svsystems\pushinstallsvc\repositório

## <a name="next-steps"></a>Passos seguintes

[Instale a instalação push para o serviço mobility](vmware-azure-install-mobility-service.md).
