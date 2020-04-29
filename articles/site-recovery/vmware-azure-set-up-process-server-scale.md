---
title: Configurar um servidor de processo supérbio durante a recuperação de desastres de VMware VMs e servidores físicos com recuperação do site Azure [ Microsoft Docs'
description: Este artigo descreve como configurar o servidor de processo seleto durante a recuperação de vMware vMware e servidores físicos.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 4/23/2019
ms.author: ramamill
ms.openlocfilehash: 1b6084b4e93f3dc17f633f1b8496f9c26e7f576f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79257150"
---
# <a name="scale-with-additional-process-servers"></a>Escala com servidores de processos adicionais

Por predefinição, quando está a replicar VMs ou servidores físicos para o Azure utilizando a Recuperação do [Site,](site-recovery-overview.md)um servidor de processo está instalado na máquina do servidor de configuração e é utilizado para coordenar a transferência de dados entre a Recuperação do Site e a sua infraestrutura no local. Para aumentar a capacidade e aumentar a sua implementação de replicação, pode adicionar servidores de processo sonantes adicionais. Este artigo descreve como configurar um servidor de processo supérno.

## <a name="before-you-start"></a>Antes de começar

### <a name="capacity-planning"></a>Planeamento de capacidade

Certifique-se de que realizou o planeamento da [capacidade](site-recovery-plan-capacity-vmware.md) para a replicação de VMware. Isto ajuda-o a identificar como e quando deve implementar servidores de processos adicionais.

A partir da versão 9.24, é adicionada orientação durante a seleção do servidor de processos para novas replicações. O servidor de processos será marcado como Saudável, Aviso e Crítico com base em certos critérios. Para compreender diferentes cenários que podem influenciar o estado do servidor de processos, reveja os alertas do servidor de [processos](vmware-physical-azure-monitor-process-server.md#process-server-alerts).

> [!NOTE]
> A utilização de um componente clonado do Servidor de Processos não é suportada. Siga os passos neste artigo para cada PS.

### <a name="sizing-requirements"></a>Requisitos de dimensionamento 

Verifique os requisitos de dimensionamento resumidos na tabela. Em geral, se tiver de dimensionar a sua implementação para mais de 200 máquinas de origem, ou se tiver uma taxa diária total de mais de 2 TB, precisa de servidores de processo adicionais para lidar com o volume de tráfego.

| **Servidor de processo adicional** | **Tamanho do disco cache** | **Taxa de alteração de dados** | **Máquinas protegidas** |
| --- | --- | --- | --- |
|4 vCPUs (2 tomadas \@ * 2 núcleos 2,5 GHz), memória de 8-GB |300 GB |250 GB ou menos |Replicar 85 ou menos máquinas. |
|8 vCPUs (2 tomadas \@ * 4 núcleos 2,5 GHz), memória de 12-GB |600 GB |250 GB a 1 TB |Replicar entre 85-150 máquinas. |
|12 vCPUs (2 tomadas \@ * 6 núcleos 2,5 GHz) 24-GB memória |1 TB |1 TB a 2 TB |Replicar entre 150-225 máquinas. |

Quando cada máquina de origem protegida estiver configurada com 3 discos de 100 GB cada.

### <a name="prerequisites"></a>Pré-requisitos

Os pré-requisitos para o servidor de processo adicional são resumidos na tabela seguinte.

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]

## <a name="download-installation-file"></a>Descarregue o ficheiro de instalação

Descarregue o ficheiro de instalação para o servidor de processos da seguinte forma:

1. Inscreva-se no portal Azure e navegue até ao seu Cofre de Serviços de Recuperação.
2. **Infraestrutura** > de recuperação de site aberto**VMWare e servidores** > de configuração de máquinas**físicas** (em VMware & Máquinas Físicas).
3. Selecione o servidor de configuração para aprofundar os detalhes do servidor. Em seguida, clique em **+ Servidor de processos**.
4. No **servidor** >  de processos de adicionar Escolha onde pretende implementar o seu servidor de**processos,** selecione Implementar um Servidor de **Processo scale-out no local**.

   ![Adicionar página de servidores](./media/vmware-azure-set-up-process-server-scale/add-process-server.png)
1. Clique em **Baixar a configuração unificada de recuperação do site do Microsoft Azure**. Isto descarrega a versão mais recente do ficheiro de instalação.

   > [!WARNING]
   > A versão de instalação do servidor de processos deve ser a mesma que, ou mais cedo do que, a versão do servidor de configuração que tem em execução. Uma forma simples de garantir a compatibilidade da versão é utilizar o mesmo instalador, que mais recentemente utilizou para instalar ou atualizar o seu servidor de configuração.

## <a name="install-from-the-ui"></a>Instalação a partir da UI

Instale da seguinte forma. Depois de configurar o servidor, migra-se as máquinas de origem para o utilizar.

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-add-process-server.md)]


## <a name="install-from-the-command-line"></a>Instalar a partir da linha de comando

Instale com o seguinte comando:

```
UnifiedSetup.exe [/ServerMode <CS/PS>] [/InstallDrive <DriveLetter>] [/MySQLCredsFilePath <MySQL credentials file path>] [/VaultCredsFilePath <Vault credentials file path>] [/EnvType <VMWare/NonVMWare>] [/PSIP <IP address to be used for data transfer] [/CSIP <IP address of CS to be registered with>] [/PassphraseFilePath <Passphrase file path>]
```

Onde os parâmetros da linha de comando são os seguintes:

[!INCLUDE [site-recovery-unified-setup-parameters](../../includes/site-recovery-unified-installer-command-parameters.md)]

Por exemplo:

```
MicrosoftAzureSiteRecoveryUnifiedSetup.exe /q /x:C:\Temp\Extracted
cd C:\Temp\Extracted
UNIFIEDSETUP.EXE /AcceptThirdpartyEULA /servermode "PS" /InstallLocation "D:\" /EnvType "VMWare" /CSIP "10.150.24.119" /PassphraseFilePath "C:\Users\Administrator\Desktop\Passphrase.txt" /DataTransferSecurePort 443
```
### <a name="create-a-proxy-settings-file"></a>Criar um ficheiro de definições de procuração

Se precisar de configurar um proxy, o parâmetro ProxySettingsFilePath tem um ficheiro como entrada. Pode criar o ficheiro da seguinte forma e passá-lo como parâmetro ProxySettingsFilePath.

```
* [ProxySettings]
* ProxyAuthentication = "Yes/No"
* Proxy IP = "IP Address"
* ProxyPort = "Port"
* ProxyUserName="UserName"
* ProxyPassword="Password"
```

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre [a gestão das definições](vmware-azure-manage-process-server.md) do servidor de processos
