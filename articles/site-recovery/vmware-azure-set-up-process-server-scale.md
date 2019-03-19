---
title: Configurar um servidor de processos no Azure para falhar durante a recuperação após desastre de VMs de VMware e servidores físicos com o Azure Site Recovery | Documentos da Microsoft
description: Este artigo descreve como configurar um servidor de processos no Azure, para a reativação pós-falha do Azure no local durante a recuperação após desastre de VMs de VMware e servidores físicos.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 12/11/2018
ms.author: mayg
ms.openlocfilehash: e3f6a160f57a4432f91c395a2e0dd664bc8f323d
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58106547"
---
# <a name="scale-for-failback-with-additional-process-servers"></a>Escala para reativação pós-falha com servidores de processos adicionais

Por predefinição, quando está a replicar VMs de VMware ou servidores físicos para o Azure com [recuperação de Site](site-recovery-overview.md), um servidor de processos está instalado na máquina do servidor de configuração e é usado para coordenar a transferência de dados entre o Site Recovery e sua infraestrutura no local. Para aumentar a capacidade e ampliar a sua implementação de replicação, pode adicionar servidores de processos adicionais autónomo. Este artigo descreve como fazer isso.

## <a name="before-you-start"></a>Antes de começar

### <a name="capacity-planning"></a>Planeamento de capacidade

Certifique-se de que já executou [planeamento de capacidade](site-recovery-plan-capacity-vmware.md) para a replicação de VMware. Isto ajuda a identificar como e quando deve implantar os servidores de processos adicionais.

> [!NOTE]
> Não é suportada a utilização de um componente de servidor de processos clonado. Siga os passos neste artigo para cada PS Escalamento horizontal.

### <a name="sizing-requirements"></a>Requisitos de dimensionamento 

Verifique se os requisitos de dimensionamento resumidos na tabela. Em geral, se tiver de dimensionar a sua implementação para mais de 200 máquinas de origem ou tem um total de alterações diariamente a taxa de mais de 2 TB, terá de servidores de processos adicionais para processar o volume de tráfego.

| **Servidor de processos adicionais** | **Tamanho do disco de cache** | **Taxa de alteração de dados** | **Máquinas protegidas** |
| --- | --- | --- | --- |
|4 vCPUs (2 sockets * 2 núcleos \@ 2.5ghz), 8 GB de memória |300 GB |250 GB ou menos |Replicar máquinas 85 ou menos. |
|8 vCPUs (2 sockets * 4 núcleos \@ 2.5ghz), 12 GB de memória |600 GB |De 250 GB a 1 TB |Replicar entre máquinas de 85 150. |
|12 vCPUs (2 sockets * 6 núcleos \@ 2.5ghz) 24 GB de memória |1 TB |1 TB para 2 TB |Replicar entre 150 225 máquinas. |

Em que cada máquina de origem protegida está configurada com discos de 3 de 100 GB cada.

### <a name="prerequisites"></a>Pré-requisitos

As pré-requisitos para o servidor de processos adicionais estão resumidos na tabela seguinte.

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]



## <a name="download-installation-file"></a>Transferir ficheiro de instalação

Transfira o ficheiro de instalação para o servidor de processos da seguinte forma:

1. Inicie sessão no portal do Azure e navegue para o seu Cofre dos serviços de recuperação.
2. Open **infraestrutura do Site Recovery** > **VMWare e máquinas físicas** > **servidores de configuração** (em para VMware e físicas Máquinas).
3. Selecione o servidor de configuração para desagregar os detalhes do servidor. Em seguida, clique em **+ servidor de processos**.
4. Na **servidor de processos adicione** >  **escolha onde pretende implementar o servidor de processos**, selecione **servidor de processos de escalamento horizontal de implementar no local**.

   ![Adicionar página de servidores](./media/vmware-azure-set-up-process-server-scale/add-process-server.png)
1. Clique em **transferir o Microsoft Azure Site Recovery a configuração unificada**. Esta ação transfere a versão mais recente do ficheiro de instalação.

   > [!WARNING]
   > A versão de instalação do servidor de processo deve ser o mesmo como, ou anterior, a versão do servidor de configuração tem em execução. Uma forma simples de garantir a compatibilidade da versão é usar o mesmo instalador, que utilizou mais recentemente para instalar ou atualizar o seu servidor de configuração.

## <a name="install-from-the-ui"></a>Instalar a partir da interface do Usuário

Instale da seguinte forma. Depois de configurar o servidor, migrar as máquinas de origem para utilizá-lo.

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-add-process-server.md)]


## <a name="install-from-the-command-line"></a>Instalar a partir da linha de comandos

Instale, executar o seguinte comando:

```
UnifiedSetup.exe [/ServerMode <CS/PS>] [/InstallDrive <DriveLetter>] [/MySQLCredsFilePath <MySQL credentials file path>] [/VaultCredsFilePath <Vault credentials file path>] [/EnvType <VMWare/NonVMWare>] [/PSIP <IP address to be used for data transfer] [/CSIP <IP address of CS to be registered with>] [/PassphraseFilePath <Passphrase file path>]
```

Em que os parâmetros de linha de comandos são os seguintes:

[!INCLUDE [site-recovery-unified-setup-parameters](../../includes/site-recovery-unified-installer-command-parameters.md)]

Por exemplo:

```
MicrosoftAzureSiteRecoveryUnifiedSetup.exe /q /x:C:\Temp\Extracted
cd C:\Temp\Extracted
UNIFIEDSETUP.EXE /AcceptThirdpartyEULA /servermode "PS" /InstallLocation "D:\" /EnvType "VMWare" /CSIP "10.150.24.119" /PassphraseFilePath "C:\Users\Administrator\Desktop\Passphrase.txt" /DataTransferSecurePort 443
```
### <a name="create-a-proxy-settings-file"></a>Crie um ficheiro de definições de proxy

Se precisar de configurar um proxy, o parâmetro ProxySettingsFilePath usa um arquivo como entrada. Pode criar o ficheiro da seguinte forma e passá-la como parâmetro de entrada ProxySettingsFilePath.

```
* [ProxySettings]
* ProxyAuthentication = "Yes/No"
* Proxy IP = "IP Address"
* ProxyPort = "Port"
* ProxyUserName="UserName"
* ProxyPassword="Password"
```

## <a name="next-steps"></a>Passos Seguintes
Saiba mais sobre [gerir processar as definições do servidor](vmware-azure-manage-process-server.md)
