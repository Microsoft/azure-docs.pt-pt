---
title: Ligue as máquinas híbridas ao Azure a partir do portal Azure
description: Neste artigo, aprende-se a instalar o agente e a ligar máquinas ao Azure utilizando o Azure Arc para servidores (pré-visualização) a partir do portal Azure.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
author: mgoedtel
ms.author: magoedte
ms.date: 02/12/2020
ms.topic: conceptual
ms.openlocfilehash: 12fc29cf12fba6325af3197e727d94b3073ef2ff
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/13/2020
ms.locfileid: "77192314"
---
# <a name="connect-hybrid-machines-to-azure-from-the-azure-portal"></a>Ligue as máquinas híbridas ao Azure a partir do portal Azure

Pode ativar o Azure Arc para servidores (pré-visualização) para um ou um pequeno número de máquinas Windows ou Linux no seu ambiente, executando manualmente um conjunto de passos. Ou pode usar um método automatizado executando um script de modelo que fornecemos. Este guião automatiza o download e instalação de ambos os agentes.

Este método requer que tenha permissões de administrador na máquina para instalar e configurar o agente. No Linux, utilizando a conta raiz e no Windows, é membro do grupo de Administradores Locais.

Antes de começar, certifique-se de rever os [pré-requisitos](overview.md#prerequisites) e verificar se a sua subscrição e recursos cumprem os requisitos.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="generate-the-installation-script-from-the-azure-portal"></a>Gere o script de instalação do portal Azure

O guião para automatizar o download e instalação, e para estabelecer a ligação com o Azure Arc, está disponível no portal Azure. Para completar o processo, faça o seguinte:

1. Do seu navegador, vá ao [portal Azure.](https://aka.ms/hybridmachineportal)

1. Na página **Máquinas - Azure Arc,** selecione **adicionar,** na parte superior esquerda, ou a **opção Criar - Opção Arco Azure** na parte inferior do painel médio. 

1. Na página **Select a method,** selecione as **máquinas Adicionar utilizando azulejos de script interativos** e, em seguida, selecione **Generate script**.

1. Na página de **script Generate,** selecione o grupo de subscrição e recursos onde pretende que a máquina seja gerida dentro do Azure. Selecione uma localização Azure onde os metadados da máquina serão armazenados.

    >[!NOTE]
    >O Azure Arc para servidores (pré-visualização) suporta apenas as seguintes regiões:
    >- WestUS2
    >- Europa Ocidental
    >- Ásia Ocidental
    >
    >Reveja considerações adicionais ao selecionar uma região [aqui](overview.md#supported-regions) no artigo Overview.

1. Na página de **script Generate,** na lista de drop-down do **sistema operativo,** selecione o sistema operativo em que o script estará em execução.

1. Se a máquina estiver a comunicar através de um servidor proxy para se ligar à internet, selecione **Next: Proxy Server**. 
1. No separador **proxy do servidor,** especifique o endereço IP do servidor proxy ou o nome e o número de porta que a máquina utilizará para comunicar com o servidor proxy. Introduza o valor no formato `http://<proxyURL>:<proxyport>`. 
1. Selecione **Review + gere.**

1. No separador **Review + gere,** reveja as informações sumárias e, em seguida, selecione **Download**. Se ainda precisar de fazer alterações, selecione **Anterior**.

## <a name="install-and-validate-the-agent-on-windows"></a>Instale e valide o agente no Windows

### <a name="install-manually"></a>Instalar manualmente
Pode instalar manualmente o agente Connected Machine executando o pacote do Instalador do Windows *AzureConnectedMachineAgent.msi*. 

> [!NOTE]
> * Para instalar ou desinstalar o agente, deve ter permissões *do Administrador.*
> * Primeiro deve transferir e copiar o pacote Installer para uma pasta no servidor alvo ou a partir de uma pasta de rede partilhada. Se executar o pacote Instalador sem opções, inicia um assistente de configuração que pode seguir para instalar o agente interactivamente.

Se a máquina precisar de comunicar através de um servidor proxy para o serviço, depois de instalar o agente, precisa de executar um comando descrito mais tarde no artigo. Isto define a variável do ambiente do servidor proxy `https_proxy`.

A tabela seguinte realça os parâmetros que são suportados pelo programa de configuração para o agente a partir da linha de comandos.

| Parâmetro | Descrição |
|:--|:--|
| /? | Devolve uma lista das opções da linha de comandos. |
| /S | Executa uma instalação silenciosa, sem interação do utilizador. |

Por exemplo, para executar o programa de instalação com o parâmetro `/?`, insira `msiexec.exe /i AzureConnectedMachineAgent.msi /?`.

Os ficheiros do agente Máquina Conectada são instalados em *Ficheiros C:\Program\AzureConnectedMachineAgent* por padrão. Se o agente não começar depois de a configuração estar concluída, verifique se os registos estão a obter informações detalhadas sobre o erro. O diretório de registo é *%Programfiles%\AzureConnectedMachineAgentAgent\logs*.

### <a name="install-with-the-scripted-method"></a>Instale com o método scriptd

1. Faça login no servidor.

1. Abra um pedido de comando PowerShell elevado.

1. Mude para a pasta ou partilhe para a qual copiou o script e execute-o no servidor executando o `./OnboardingScript.ps1` script.

### <a name="configure-the-agent-proxy-setting"></a>Configure a definição de procuração do agente

Para definir a variável ambiente do servidor proxy, executar o seguinte comando:

```powershell
# If a proxy server is needed, execute these commands with the proxy URL and port.
[Environment]::SetEnvironmentVariable("https_proxy", "http://{proxy-url}:{proxy-port}", "Machine")
$env:https_proxy = [System.Environment]::GetEnvironmentVariable("https_proxy","Machine")
# For the changes to take effect, the agent service needs to be restarted after the proxy environment variable is set.
Restart-Service -Name himds
```

>[!NOTE]
>O agente não suporta a definição de autenticação por procuração nesta pré-visualização.
>

### <a name="configure-agent-communication"></a>Configurar a comunicação do agente

Depois de instalar o agente, é necessário configurar o agente para comunicar com o serviço Azure Arc executando o seguinte comando:

`%ProgramFiles%\AzureConnectedMachineAgent\azcmagent.exe" connect --resource-group "<resourceGroupName>" --tenant-id "<tenantID>" --location "<regionName>" --subscription-id "<subscriptionID>"`

## <a name="install-and-validate-the-agent-on-linux"></a>Instale e valide o agente no Linux

O agente Máquina Conectada para Linux é fornecido no formato de pacote preferido para a distribuição (. RPM ou . DEB) que está hospedado no [repositório](https://packages.microsoft.com/)de pacotes da Microsoft . O pacote de script shell [`Install_linux_azcmagent.sh`](https://aka.ms/azcmagent) executa as seguintes ações:

- Configura a máquina hospedeira para descarregar o pacote de agente a partir de packages.microsoft.com.
- Instala o pacote Hybrid Resource Provider.

Opcionalmente, pode configurar o agente com as suas informações de procuração, incluindo o parâmetro `--proxy "{proxy-url}:{proxy-port}"`.

O script também contém lógica para identificar as distribuições suportadas e não suportadas, e verifica as permissões necessárias para realizar a instalação. 

O exemplo seguinte descarrega o agente e instala-o:

```bash
# Download the installation package.
wget https://aka.ms/azcmagent -O ~/Install_linux_azcmagent.sh

# Install the connected machine agent. 
bash ~/Install_linux_azcmagent.sh
```

Para descarregar e instalar o agente, incluindo o parâmetro `--proxy` para configurar o agente para comunicar através do seu servidor proxy, execute os seguintes comandos:

```bash
# Download the installation package.
wget https://aka.ms/azcmagent -O ~/Install_linux_azcmagent.sh

# Install the connected machine agent. 
bash ~/Install_linux_azcmagent.sh --proxy "{proxy-url}:{proxy-port}"
```

### <a name="configure-the-agent-communication"></a>Configure a comunicação do agente

Depois de instalar o agente, configure-o para comunicar com o serviço Azure Arc executando o seguinte comando:

`/opt/azcmagent/bin/azcmagent.exe" connect --resource-group "<resourceGroupName>" --tenant-id "<tenantID>" --location "<regionName>" --subscription-id "<subscriptionID>"`

## <a name="verify-the-connection-with-azure-arc"></a>Verifique a ligação com o Arco Azure

Depois de instalar o agente e configurá-lo para ligar ao Azure Arc para servidores (pré-visualização), vá ao portal Azure para verificar se o servidor foi ligado com sucesso. Veja as suas máquinas no [portal Azure.](https://aka.ms/hybridmachineportal)

![Uma ligação bem-sucedida do servidor](./media/onboard-portal/arc-for-servers-successful-onboard.png)

## <a name="clean-up"></a>Limpeza

Para desligar uma máquina do Azure Arc para servidores (pré-visualização), faça o seguinte:

1. Abra o Arco Azure para servidores (pré-visualização) indo para o [portal Azure](https://aka.ms/hybridmachineportal).

1. Selecione a máquina na lista, selecione a elipse ( **...** ) e, em seguida, selecione **Delete**.

1. Para desinstalar o agente windows da máquina, faça o seguinte:

    a. Inscreva-se no computador com uma conta que tenha permissões de administrador.  
    b. No **Painel de Controlo,** selecione **Programas e Funcionalidades.**  
    c. Em **Programas e Funcionalidades,** selecione **Agente de Máquina Seletiva,** selecione **Desinstalar**, e, em seguida, selecione **Sim**.  

    >[!NOTE]
    > Também pode executar o assistente de configuração do agente clicando duas vezes no pacote de instalação **AzureConnectedMachineAgent.msi.**

    Se pretender a remoção do script do agente, pode utilizar o seguinte exemplo, que recupera o código do produto e desinstala o agente utilizando a linha de comando Msiexec.exe - `msiexec /x {Product Code}`. Para tal:  
    
    a. Abra o Editor de Registo.  
    b. Sob a chave de registo `HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\Uninstall`, procure e copie o código do produto GUID.  
    c. Em seguida, pode desinstalar o agente utilizando a Msiexec.

    O exemplo que se segue demonstra como desinstalar o agente:

    ```powershell
    Get-ChildItem -Path HKLM:\Software\Microsoft\Windows\CurrentVersion\Uninstall | `
    Get-ItemProperty | `
    Where-Object {$_.DisplayName -eq "Azure Connected Machine Agent"} | `
    ForEach-Object {MsiExec.exe /x "$($_.PsChildName)" /qn}
    ```

1. Para desinstalar o agente Linux, execute o seguinte comando:

      ```bash
      sudo apt purge hybridagent
      ```

## <a name="next-steps"></a>Passos seguintes

- Saiba como gerir a sua máquina utilizando a [Política Azure](../../governance/policy/overview.md), para coisas como a configuração de [hóspedes](../../governance/policy/concepts/guest-configuration.md)VM , verificando que a máquina está a reportar ao espaço de trabalho esperado do Log Analytics, permitir a monitorização com o [Monitor Azure com VMs](../../azure-monitor/insights/vminsights-enable-at-scale-policy.md), e muito mais.

- Saiba mais sobre o [agente Log Analytics](../../azure-monitor/platform/log-analytics-agent.md). O agente Log Analytics para Windows e Linux é necessário quando pretende monitorizar proativamente o SISTEMA e as cargas de trabalho em funcionamento na máquina, geri-la utilizando livros de execução automation ou soluções como a Update Management, ou utilizar outros serviços Azure como o [Azure Security Center.](../../security-center/security-center-intro.md)