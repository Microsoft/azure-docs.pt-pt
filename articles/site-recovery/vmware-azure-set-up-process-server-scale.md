---
title: Crie um servidor de processo de escala durante a recuperação de desastres de VMware VMs e servidores físicos com | de recuperação do site Azure Microsoft Docs'
description: Este artigo descreve como configurar o servidor de processo de escala durante a recuperação de desastres de VMware VMs e servidores físicos.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 4/23/2019
ms.author: ramamill
ms.openlocfilehash: 40f912122e6ffb9cccbd32a747f6f0d46fd6c330
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96019153"
---
# <a name="scale-with-additional-process-servers"></a>Escala com servidores de processos adicionais

Por predefinição, quando está a replicar VMs vMware ou servidores físicos para o Azure usando a [Recuperação do Site,](site-recovery-overview.md)um servidor de processo é instalado na máquina do servidor de configuração e é utilizado para coordenar a transferência de dados entre a Recuperação do Site e a sua infraestrutura no local. Para aumentar a capacidade e aumentar a sua implementação de replicação, pode adicionar servidores de processo autónomos adicionais. Este artigo descreve como configurar um servidor de processo de escala.

## <a name="before-you-start"></a>Antes de começar

### <a name="capacity-planning"></a>Planeamento de capacidade

Certifique-se de que executou [o planeamento de capacidade](site-recovery-plan-capacity-vmware.md) para a replicação de VMware. Isto ajuda-o a identificar como e quando deve implementar servidores de processo adicionais.

A partir da versão 9.24, a orientação é adicionada durante a seleção do servidor de processo para novas replicações. O servidor de processo será marcado como Saudável, Aviso e Crítico com base em determinados critérios. Para compreender diferentes cenários que podem influenciar o estado do servidor de processo, reveja os alertas do servidor de [processos](vmware-physical-azure-monitor-process-server.md#process-server-alerts).

> [!NOTE]
> A utilização de um componente do Servidor de Processo clonado não é suportada. Siga os passos deste artigo para cada escala do PS.

### <a name="sizing-requirements"></a>Requisitos de dimensionamento 

Verifique os requisitos de dimensionamento resumidos na tabela. Em geral, se tiver de escalar a sua implementação para mais de 200 máquinas de origem, ou se tiver uma taxa diária total de churn de mais de 2 TB, precisa de servidores de processos adicionais para lidar com o volume de tráfego.

| **Servidor de processo adicional** | **Tamanho do disco cache** | **Taxa de alteração de dados** | **Máquinas protegidas** |
| --- | --- | --- | --- |
|4 vCPUs (2 tomadas * 2 núcleos \@ 2,5 GHz), memória de 8 GB |300 GB |250 GB ou menos |Replique 85 ou menos máquinas. |
|8 vCPUs (2 tomadas * 4 núcleos \@ 2,5 GHz), memória de 12 GB |600 GB |250 GB a 1 TB |Replique entre 85-150 máquinas. |
|12 vCPUs (2 tomadas * 6 núcleos \@ 2,5 GHz) memória de 24 GB |1 TB |1 TB a 2 TB |Replique entre 150-225 máquinas. |

Onde cada máquina de origem protegida é configurada com 3 discos de 100 GB cada.

### <a name="prerequisites"></a>Pré-requisitos

Os pré-requisitos para o servidor de processo adicional são resumidos na tabela seguinte.

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]

## <a name="download-installation-file"></a>Descarregar ficheiro de instalação

Descarregue o ficheiro de instalação para o servidor de processo da seguinte forma:

1. Inscreva-se no portal Azure e navegue pelo cofre dos Serviços de Recuperação.
2. Servidores de   >  configuração **VMware e Máquinas Físicas** de Recuperação de Locais  >  **Abertos** (em VMware & Máquinas Físicas).
3. Selecione o servidor de configuração para perfurar os detalhes do servidor. Em seguida, clique **em + Servidor de Processo**.
4. In **Add Process server** Escolha onde pretende implementar o seu servidor de  >   **processos**, selecione Implementar um Servidor de Processo de Escala no **local**.

   ![Adicionar página de servidores](./media/vmware-azure-set-up-process-server-scale/add-process-server.png)
1. Clique **em Baixar a Configuração Unificada de Recuperação do Site microsoft Azure**. Isto descarrega a versão mais recente do ficheiro de instalação.

   > [!WARNING]
   > A versão de instalação do servidor de processo deve ser a mesma que, ou mais cedo, a versão do servidor de configuração que tem em execução. Uma forma simples de garantir a compatibilidade da versão é utilizar o mesmo instalador, que mais recentemente utilizou para instalar ou atualizar o seu servidor de configuração.

## <a name="install-from-the-ui"></a>Instalação a partir do UI

Instale da seguinte forma. Depois de configurar o servidor, migra máquinas de origem para usá-lo.

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-add-process-server.md)]


## <a name="install-from-the-command-line"></a>Instalação a partir da linha de comando

Instale executando o seguinte comando:

```
UnifiedSetup.exe [/ServerMode <CS/PS>] [/InstallDrive <DriveLetter>] [/MySQLCredsFilePath <MySQL credentials file path>] [/VaultCredsFilePath <Vault credentials file path>] [/EnvType <VMware/NonVMware>] [/PSIP <IP address to be used for data transfer] [/CSIP <IP address of CS to be registered with>] [/PassphraseFilePath <Passphrase file path>]
```

Onde os parâmetros da linha de comando são os seguintes:

[!INCLUDE [site-recovery-unified-setup-parameters](../../includes/site-recovery-unified-installer-command-parameters.md)]

Por exemplo:

```
MicrosoftAzureSiteRecoveryUnifiedSetup.exe /q /x:C:\Temp\Extracted
cd C:\Temp\Extracted
UNIFIEDSETUP.EXE /AcceptThirdpartyEULA /servermode "PS" /InstallLocation "D:\" /EnvType "VMware" /CSIP "10.150.24.119" /PassphraseFilePath "C:\Users\Administrator\Desktop\Passphrase.txt" /DataTransferSecurePort 443
```
### <a name="create-a-proxy-settings-file"></a>Criar um ficheiro de configurações proxy

Se precisar de configurar um representante, o parâmetro ProxySettingsFilePath toma um ficheiro como entrada. Pode criar o ficheiro da seguinte forma e passá-lo como parâmetro ProxySettingsFilePath.

```
* [ProxySettings]
* ProxyAuthentication = "Yes/No"
* Proxy IP = "IP Address"
* ProxyPort = "Port"
* ProxyUserName="UserName"
* ProxyPassword="Password"
```

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre [a gestão das definições do servidor de processos](vmware-azure-manage-process-server.md)
