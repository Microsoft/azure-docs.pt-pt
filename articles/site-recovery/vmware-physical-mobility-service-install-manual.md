---
title: Instalar o serviço de mobilidade manualmente para recuperação após desastre de VMs de VMware e servidores físicos com o Azure Site Recovery | Documentos da Microsoft
description: Saiba como instalar o agente do serviço de mobilidade para recuperação após desastre de VMs de VMware e servidores físicos para o Azure com o serviço Azure Site Recovery.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: ramamill
ms.openlocfilehash: 862846c8ec544cf082d45cea650269b6518a016f
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58003527"
---
# <a name="install-the-mobility-service-manually-on-vmware-vms-and-physical-servers"></a>Instalar o serviço de mobilidade manualmente em VMs de VMware e servidores físicos

Quando define a recuperação após desastre para VMs de VMware e servidores físicos usando [do Azure Site Recovery](site-recovery-overview.md), instalar o [serviço de mobilidade de recuperação de Site](vmware-physical-mobility-service-overview.md) em cada VM de VMware no local e o servidor físico.  O serviço de mobilidade obtém dados, escreve na máquina e reencaminha-os para o servidor de processos de recuperação de sites.

Este artigo descreve como instalar o serviço de mobilidade manualmente em cada máquina que pretende proteger.

## <a name="create-a-passphrase"></a>Criar uma frase de acesso

Antes de instalar, crie uma frase de acesso que será utilizado durante a instalação.

1. Inicie sessão no servidor de configuração.
2. Abra uma linha de comandos como administrador.
3. Altere o diretório para a pasta bin e, em seguida, crie um ficheiro da frase de acesso.

    ```
    cd %ProgramData%\ASR\home\svsystems\bin
    genpassphrase.exe -v > MobSvc.passphrase
    ```
3. Store o ficheiro da frase de acesso numa localização segura. 


## <a name="install-the-service-from-the-ui"></a>Instalar o serviço a partir da IU

>[!IMPORTANT]
> Se estiver a replicar VMS de IaaS do Azure de uma região do Azure para outro, não utilize este método. Utilize o insteadl do método de instalação baseada na linha de comandos.

1. Localize a versão de instalador que precisa para o sistema operativo da máquina. Programas de instalação estão localizados na pasta %ProgramData%\ASR\home\svsystems\pushinstallsvc\repository. [Verificar](vmware-physical-mobility-service-overview.md#installer-files) installer, que é necessário.
2. Copie o ficheiro de instalação para a máquina e executá-lo.
3. Na **opção de instalação**, selecione **instalar o serviço de mobilidade**.
4. Selecione a localização de instalação > **instalar**.

    ![Página de opção de instalação de serviço de mobilidade](./media/vmware-physical-mobility-service-install-manual/mobility1.png)

5. Monitorizar a instalação no **progresso da instalação**. Depois de concluída a instalação, selecione **continuar a configuração** para registar o serviço com o servidor de configuração.

    ![Página de registo do serviço de mobilidade](./media/vmware-physical-mobility-service-install-manual/mobility3.png)

6.  na **detalhes do servidor de configuração**, especifique o endereço IP e a frase de acesso que configurou.  

    ![Página de registo do serviço de mobilidade](./media/vmware-physical-mobility-service-install-manual/mobility4.png)

7. Selecione **registar** para concluir o registo.

    ![Página final de registo do serviço de mobilidade](./media/vmware-physical-mobility-service-install-manual/mobility5.png)

## <a name="install-the-service-from-the-command-prompt"></a>Instalar o serviço a partir da linha de comandos

### <a name="on-a-windows-machine"></a>Num computador Windows

1. Copie o instalador para uma pasta local (por exemplo, C:\Temp) no servidor que pretende proteger. 

   ```
   cd C:\Temp
   ren Microsoft-ASR_UA*Windows*release.exe MobilityServiceInstaller.exe
   MobilityServiceInstaller.exe /q /x:C:\Temp\Extracted
   cd C:\Temp\Extracted.
   ```
2. Instale da seguinte forma:

   ```
   UnifiedAgent.exe /Role "MS" /InstallLocation "C:\Program Files (x86)\Microsoft Azure Site Recovery" /Platform "VmWare" /Silent
   ```

3. Registe o agente com o servidor de configuração.

   ```
   cd C:\Program Files (x86)\Microsoft Azure Site Recovery\agent
   UnifiedAgentConfigurator.exe  /CSEndPoint <CSIP> /PassphraseFilePath <PassphraseFilePath>
   ```

#### <a name="installation-settings"></a>Definições de instalação

**Definição** | **Detalhes**
--- | ---
Utilização | . UnifiedAgent.exe /Role < MS\|MT > /InstallLocation <Install Location> /Platform "VmWare" /Silent
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
Utilização | . /Install -d <Install Location> - r < MS\|MT > - v. o VmWare - q
-r | Parâmetro de instalação obrigatória. Especifica se o serviço de mobilidade (MS) ou o destino mestre (TA) deve ser instalado.
-d | Parâmetro opcional. Especifica a localização de instalação do serviço de mobilidade: /usr/local/ASR.
-v | Obrigatório. Especifica a plataforma em que o serviço de mobilidade está instalado. **VMware** para VMware VMs/servidores físicos; **Azure** para VMs do Azure. 
-q | Opcional. Especifica se pretende executar o instalador em modo silencioso.

#### <a name="registration-settings"></a>Definições de registo

**Definição** | **Detalhes**
--- | ---
Utilização | CD /usr/local/ASR/Vx/bin<br/><br/> UnifiedAgentConfigurator.sh -i <CSIP> -P <PassphraseFilePath>
-i | Parâmetro obrigatório. Especifica o endereço IP do servidor de configuração. Utilize qualquer endereço IP válido.
-P |  Obrigatório. Caminho completo do ficheiro em que a frase de acesso é guardado. Utilize qualquer pasta válido

## <a name="next-steps"></a>Passos Seguintes

- [Configurar a recuperação após desastre para VMs de VMware](vmware-azure-tutorial.md)
- [Configurar a recuperação após desastre para servidores físicos](physical-azure-disaster-recovery.md)
