---
title: Sobre o serviço de Mobilidade para recuperação de desastres de VMware VMs e servidores físicos com Recuperação do Site Azure [ Microsoft Docs
description: Conheça o agente de serviço de Mobilidade para recuperação de desastres de VMware VMs e servidores físicos para o Azure utilizando o serviço de recuperação do site Azure.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: how-to
ms.date: 04/10/2020
ms.author: ramamill
ms.openlocfilehash: ec4d1cfbe0c76c8245c4beeaa7c044d76d917a7a
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/13/2020
ms.locfileid: "81259811"
---
# <a name="about-the-mobility-service-for-vmware-vms-and-physical-servers"></a>Sobre o serviço de Mobilidade para VMware VMs e servidores físicos

Quando configura restabelecimento de recuperação de desastres para máquinas virtuais VMware (VM) e servidores físicos utilizando a Recuperação do [Site Azure,](site-recovery-overview.md)instala o serviço de Mobilidade de Recuperação do Site em cada VM e servidor físico no local. O serviço de Mobilidade captura os dados escritos na máquina e encaminha-os para o servidor do processo de Recuperação do Site. O serviço mobility é instalado pelo software do agente de serviços mobility que pode implementar utilizando os seguintes métodos:

- [Instalação de impulso](#push-installation): Quando a proteção é ativada através do portal Azure, a Recuperação do Site instala o serviço de Mobilidade no servidor.
- Instalação manual: Pode instalar manualmente o serviço mobility em cada máquina através da interface do [utilizador (UI)](#install-the-mobility-service-using-ui) ou [do comando](#install-the-mobility-service-using-command-prompt).
- [Implementação automatizada](vmware-azure-mobility-install-configuration-mgr.md): Pode automatizar a instalação do serviço Mobility com ferramentas de implementação de software, como o Gestor de Configuração.

> [!NOTE]
> O serviço de Mobilidade utiliza aproximadamente 6%-10% da memória em máquinas de origem para VMware VMs ou máquinas físicas.

## <a name="antivirus-on-replicated-machines"></a>Antivírus em máquinas replicadas

Se as máquinas que pretende replicar estiverem a executar software antivírus, exclua a pasta de instalação do serviço de Mobilidade _C:\ProgramData\ASR\agent_ das operações antivírus. Esta exclusão garante que a replicação funcionará como esperado.

## <a name="push-installation"></a>Instalação de empurrar

A instalação push é parte integrante do trabalho que é executado a partir do portal Azure para permitir a [replicação](vmware-azure-enable-replication.md#enable-replication). Depois de escolher o conjunto de VMs que pretende proteger e ativar a replicação, o servidor de configuração empurra o agente de serviço mobility para os servidores, instala o agente e completa o registo do agente com o servidor de configuração.

### <a name="prerequisites"></a>Pré-requisitos

- Certifique-se de que todos os [pré-requisitos](vmware-azure-install-mobility-service.md) de instalação push são cumpridos.
- Certifique-se de que todas as configurações do servidor satisfazem os critérios na matriz de [Suporte para recuperação de VMware vMs e servidores físicos para o Azure](vmware-physical-azure-support-matrix.md).

O fluxo de trabalho de instalação push é descrito nas seguintes secções:

### <a name="mobility-service-agent-version-923-and-higher"></a>Versão 9.23 do agente de serviço de mobilidade

Para mais informações sobre a versão 9.23 consulte [Update Rollup 35 para recuperação](https://support.microsoft.com/help/4494485/update-rollup-35-for-azure-site-recovery)do site Azure .

Durante uma instalação push do serviço de Mobilidade, são realizados os seguintes passos:

1. O agente é empurrado para a máquina de origem. Copiar o agente para a máquina de origem pode falhar devido a múltiplos erros ambientais. Visite [a nossa orientação](vmware-azure-troubleshoot-push-install.md) para resolver falhas de instalação de impulso.
1. Depois de o agente ser copiado com sucesso para o servidor, é efetuada uma verificação prévia no servidor.
   - Se todos os pré-requisitos forem cumpridos, a instalação começa.
   - A instalação falha se um ou mais dos [pré-requisitos](vmware-physical-azure-support-matrix.md) não forem cumpridos.
1. Como parte da instalação do agente, o fornecedor do Serviço de Cópia seleção de Volume Shadow (VSS) para a Recuperação do Site Azure está instalado. O fornecedor VSS é utilizado para gerar pontos de recuperação consistentes com aplicações. Se a instalação do fornecedor VSS falhar, este passo é ignorado e a instalação do agente continua.
1. Se a instalação do agente for bem sucedida, mas a instalação do fornecedor VSS falhar, o estado de trabalho está marcado como **Aviso**. Isto não afeta a geração de pontos de recuperação consistente.

    - Para gerar pontos de recuperação consistentes com aplicações, consulte [a nossa orientação](vmware-physical-manage-mobility-service.md#install-site-recovery-vss-provider-on-source-machine) para completar uma instalação manual do fornecedor VSS recovery site.
    - Se não quiser gerar pontos de recuperação consistentes com aplicações, [modifique a política de replicação](vmware-azure-set-up-replication.md#create-a-policy) para desligar pontos de recuperação consistentes com aplicações.

### <a name="mobility-service-agent-version-922-and-below"></a>Versão 9.22 do agente de serviço de mobilidade e abaixo

1. O agente é empurrado para a máquina de origem. Copiar o agente para a máquina de origem pode falhar devido a múltiplos erros ambientais. Consulte [a nossa orientação](vmware-azure-troubleshoot-push-install.md) para falhas de instalação de push de resolução de problemas.
1. Depois de o agente ser copiado com sucesso para o servidor, é efetuada uma verificação prévia no servidor.
   - Se todos os pré-requisitos forem cumpridos, a instalação começa.
   - A instalação falha se um ou mais dos [pré-requisitos](vmware-physical-azure-support-matrix.md) não forem cumpridos.

1. Como parte da instalação do agente, o fornecedor do Serviço de Cópia seleção de Volume Shadow (VSS) para a Recuperação do Site Azure está instalado. O fornecedor VSS é utilizado para gerar pontos de recuperação consistentes com aplicações.
   - Se a instalação do fornecedor VSS falhar, a instalação do agente falhará. Para evitar falhas na instalação do agente, utilize a [versão 9.23](https://support.microsoft.com/help/4494485/update-rollup-35-for-azure-site-recovery) ou superior para gerar pontos de recuperação consistentes com falhas e efecia uma instalação manual do fornecedor VSS.

## <a name="install-the-mobility-service-using-ui"></a>Instale o serviço de Mobilidade utilizando a UI

### <a name="prerequisites"></a>Pré-requisitos

- Certifique-se de que todas as configurações do servidor satisfazem os critérios na matriz de [Suporte para recuperação de VMware vMs e servidores físicos para o Azure](vmware-physical-azure-support-matrix.md).
- [Localize o instalador](#locate-installer-files) para o sistema operativo do servidor.

>[!IMPORTANT]
> Não utilize o método de instalação da UI se estiver a replicar uma Infraestrutura Azure como Um VM de serviço (IaaS) de uma região de Azure para outra. Utilize a instalação [de solicitação](#install-the-mobility-service-using-command-prompt) de comando.

1. Copie o ficheiro de instalação para a máquina e execute-o.
1. Na **opção de instalação,** selecione **Instalar o serviço de mobilidade.**
1. Escolha o local de instalação e selecione **Instalar**.

    :::image type="content" source="./media/vmware-physical-mobility-service-install-manual/mobility1.png" alt-text="Página de opção de instalação de serviço de mobilidade.":::

1. Monitorize a instalação em Evolução da **Instalação**. Depois de terminar a instalação, selecione **'Proceder à Configuração'** para registar o serviço com o servidor de configuração.

    :::image type="content" source="./media/vmware-physical-mobility-service-install-manual/mobility3.png" alt-text="Página de registo do serviço de mobilidade.":::

1. Em Detalhes do Servidor de **Configuração,** especifique o endereço IP e a frase-passe que configura.

    :::image type="content" source="./media/vmware-physical-mobility-service-install-manual/mobility4.png" alt-text="Página de registo do serviço de mobilidade.":::

1. Selecione **Registar** para terminar a inscrição.

    :::image type="content" source="./media/vmware-physical-mobility-service-install-manual/mobility5.png" alt-text="Página final do registo do serviço de mobilidade.":::

## <a name="install-the-mobility-service-using-command-prompt"></a>Instale o serviço de Mobilidade utilizando o pedido de comando

### <a name="prerequisites"></a>Pré-requisitos

- Certifique-se de que todas as configurações do servidor satisfazem os critérios na matriz de [Suporte para recuperação de VMware vMs e servidores físicos para o Azure](vmware-physical-azure-support-matrix.md).
- [Localize o instalador](#locate-installer-files) para o sistema operativo do servidor.

### <a name="windows-machine"></a>Máquina do Windows

- A partir de um pedido de comando, execute os seguintes comandos para copiar o instalador para uma pasta local, como _C:\Temp_, no servidor que pretende proteger. Substitua o nome de ficheiro do instalador pelo nome real do ficheiro.

  ```cmd
  cd C:\Temp
  ren Microsoft-ASR_UA_version_Windows_GA_date_release.exe MobilityServiceInstaller.exe
  MobilityServiceInstaller.exe /q /x:C:\Temp\Extracted
  cd C:\Temp\Extracted
  ```

- Execute este comando para instalar o agente.

  ```cmd
  UnifiedAgent.exe /Role "MS" /InstallLocation "C:\Program Files (x86)\Microsoft Azure Site Recovery" /Platform "VmWare" /Silent
  ```

- Executar estes comandos para registar o agente com o servidor de configuração.

  ```cmd
  cd C:\Program Files (x86)\Microsoft Azure Site Recovery\agent
  UnifiedAgentConfigurator.exe  /CSEndPoint <CSIP> /PassphraseFilePath <PassphraseFilePath>
  ```

#### <a name="installation-settings"></a>Configurações de instalação

Definição | Detalhes
--- | ---
Sintaxe | `UnifiedAgent.exe /Role \<MS/MT> /InstallLocation \<Install Location> /Platform "VmWare" /Silent`
Registos de configuração | `%ProgramData%\ASRSetupLogs\ASRUnifiedAgentInstaller.log`
`/Role` | Parâmetro de instalação obrigatório. Especifica se o serviço de Mobilidade (MS) ou o alvo principal (MT) devem ser instalados.
`/InstallLocation`| Parâmetro opcional. Especifica a localização de instalação do serviço mobility (qualquer pasta).
`/Platform` | Obrigatório. Especifica a plataforma em que o serviço de Mobilidade está instalado: <br/> **VMware** para VMware VMs/servidores físicos. <br/> **Azure** para VMs Azure.<br/><br/> Se estiver a tratar os VMs Azure como máquinas físicas, especifique **vMware**.
`/Silent`| Opcional. Especifica se deve executar o instalador em modo silencioso.

#### <a name="registration-settings"></a>Configurações de registo

Definição | Detalhes
--- | ---
Sintaxe | `UnifiedAgentConfigurator.exe  /CSEndPoint \<CSIP> /PassphraseFilePath \<PassphraseFilePath>`
Registos de configuração do agente | `%ProgramData%\ASRSetupLogs\ASRUnifiedAgentConfigurator.log`
`/CSEndPoint` | Parâmetro obrigatório. `<CSIP>`especifica o endereço IP do servidor de configuração. Utilize qualquer endereço IP válido.
`/PassphraseFilePath` |  Obrigatório. Localização da frase-passe. Utilize qualquer caminho de ficheiro sem e não abertos à base de dados válido ou local.

### <a name="linux-machine"></a>Máquina linux

1. A partir de uma sessão terminal, copie o instalador para uma pasta local como _/tmp_ no servidor que pretende proteger. Substitua o nome de ficheiro do instalador pelo nome real do ficheiro da distribuição do Linux e, em seguida, execute os comandos.

   ```shell
   cd /tmp ;
   tar -xvf Microsoft-ASR_UA_version_LinuxVersion_GA_date_release.tar.gz
   ```

2. Instale da seguinte forma:

   ```shell
   sudo ./install -d <Install Location> -r MS -v VmWare -q
   ```

3. Após a instalação estar terminada, o serviço de Mobilidade deve ser registado no servidor de configuração. Executar o seguinte comando para registar o serviço de Mobilidade com o servidor de configuração.

   ```shell
   /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <CSIP> -P /var/passphrase.txt
   ```

#### <a name="installation-settings"></a>Configurações de instalação

Definição | Detalhes
--- | ---
Sintaxe | `./install -d \<Install Location> -r \<MS/MT> -v VmWare -q`
`-r` | Parâmetro de instalação obrigatório. Especifica se o serviço de Mobilidade (MS) ou o alvo principal (MT) devem ser instalados.
`-d` | Parâmetro opcional. Especifica a localização da `/usr/local/ASR`instalação do serviço mobility: .
`-v` | Obrigatório. Especifica a plataforma em que o serviço de Mobilidade está instalado. <br/> **VMware** para VMware VMs/servidores físicos. <br/> **Azure** para VMs Azure.
`-q` | Opcional. Especifica se deve executar o instalador em modo silencioso.

#### <a name="registration-settings"></a>Configurações de registo

Definição | Detalhes
--- | ---
Sintaxe | `cd /usr/local/ASR/Vx/bin<br/><br/> UnifiedAgentConfigurator.sh -i \<CSIP> -P \<PassphraseFilePath>`
`-i` | Parâmetro obrigatório. `<CSIP>`especifica o endereço IP do servidor de configuração. Utilize qualquer endereço IP válido.
`-P` |  Obrigatório. Caminho completo do ficheiro em que a frase-passe é guardada. Utilize qualquer pasta válida.

## <a name="azure-virtual-machine-agent"></a>Agente máquina virtual Azure

- **VMs do Windows**: A partir da versão 9.7.0.0 do serviço mobility, o [agente Azure VM](/azure/virtual-machines/extensions/features-windows#azure-vm-agent) é instalado pelo instalador de serviços mobility. Isto garante que quando a máquina falha no Azure, o Azure VM cumpre o pré-requisito de instalação do agente para a utilização de qualquer extensão VM.
- **VMs Linux**: O [WALinuxAgent](/azure/virtual-machines/extensions/update-linux-agent) deve ser instalado manualmente no VM Azure após a falha.

## <a name="locate-installer-files"></a>Localizar ficheiros de instalação

No servidor de configuração vá para a pasta _%ProgramData%\ASR\home\svsystems\pushinstallsvc\repositório_. Verifique qual é o instalador de que necessita com base no sistema operativo. A tabela seguinte resume os ficheiros do instalador para cada VMware VM e sistema operativo de servidor físico. Antes de começar, pode rever os [sistemas operativos suportados.](vmware-physical-azure-support-matrix.md#replicated-machines)

> [!NOTE]
> Os nomes dos ficheiros utilizam a sintaxe mostrada na tabela seguinte com _versão_ e _data_ como espaços reservados para os valores reais. Os nomes reais dos ficheiros serão semelhantes a estes exemplos:
> - `Microsoft-ASR_UA_9.30.0.0_Windows_GA_22Oct2019_release.exe`
> - `Microsoft-ASR_UA_9.30.0.0_UBUNTU-16.04-64_GA_22Oct2019_release.tar.gz`

Arquivo de instalação | Sistema operativo (apenas 64 bits)
--- | ---
`Microsoft-ASR_UA_version_Windows_GA_date_release.exe` | Windows Server 2016 </br> Windows Server 2012 R2 </br> Windows Server 2012 </br> Windows Server 2008 R2 SP1
`Microsoft-ASR_UA_version_RHEL6-64_GA_date_release.tar.gz` | Red Hat Enterprise Linux (RHEL) 6 </br> Centos 6
`Microsoft-ASR_UA_version_RHEL7-64_GA_date_release.tar.gz` | Red Hat Enterprise Linux (RHEL) 7 </br> CentOS 7
`Microsoft-ASR_UA_version_SLES12-64_GA_date_release.tar.gz` | SUSE Linux Enterprise Server 12 SP1 </br> Inclui SP2 e SP3.
`Microsoft-ASR_UA_version_SLES11-SP3-64_GA_date_release.tar.gz` | SUSE Linux Enterprise Server 11 SP3
`Microsoft-ASR_UA_version_SLES11-SP4-64_GA_date_release.tar.gz` | SUSE Linux Enterprise Server 11 SP4
`Microsoft-ASR_UA_version_OL6-64_GA_date_release.tar.gz` | Oracle Enterprise Linux 6.4 </br> Oracle Enterprise Linux 6.5
`Microsoft-ASR_UA_version_UBUNTU-14.04-64_GA_date_release.tar.gz` | Ubuntu Linux 14.04
`Microsoft-ASR_UA_version_UBUNTU-16.04-64_GA_date_release.tar.gz` | Servidor Ubuntu Linux 16.04 LTS
`Microsoft-ASR_UA_version_DEBIAN7-64_GA_date_release.tar.gz` | Debiano 7
`Microsoft-ASR_UA_version_DEBIAN8-64_GA_date_release.tar.gz` | Debiano 8

## <a name="next-steps"></a>Passos seguintes

[Instale a instalação de impulso para o serviço de Mobilidade](vmware-azure-install-mobility-service.md).
