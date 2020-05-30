---
title: Ligue máquinas híbridas ao Azure a partir do portal Azure
description: Neste artigo, aprende-se a instalar o agente e a ligar máquinas ao Azure utilizando o Azure Arc para servidores (pré-visualização) a partir do portal Azure.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
author: mgoedtel
ms.author: magoedte
ms.date: 05/18/2020
ms.topic: conceptual
ms.custom: references_regions
ms.openlocfilehash: 0b43bb92a58af58ae5924610f0667e9796a426e6
ms.sourcegitcommit: 1f48ad3c83467a6ffac4e23093ef288fea592eb5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/29/2020
ms.locfileid: "84193747"
---
# <a name="connect-hybrid-machines-to-azure-from-the-azure-portal"></a>Ligue máquinas híbridas ao Azure a partir do portal Azure

Pode ativar o Azure Arc para servidores (pré-visualização) para um ou um pequeno número de máquinas Windows ou Linux no seu ambiente, realizando manualmente um conjunto de passos. Ou pode usar um método automatizado executando um script de modelo que fornecemos. Este script automatiza o download e instalação de ambos os agentes.

Este método requer que tenha permissões de administrador na máquina para instalar e configurar o agente. No Linux, utilizando a conta raiz e no Windows, é membro do grupo de Administradores Locais.

Antes de começar, certifique-se de rever os [pré-requisitos](agent-overview.md#prerequisites) e verificar se a sua subscrição e recursos cumprem os requisitos.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="generate-the-installation-script-from-the-azure-portal"></a>Gere o script de instalação a partir do portal Azure

O script para automatizar o download e instalação, e para estabelecer a ligação com a Azure Arc, está disponível a partir do portal Azure. Para concluir o processo, faça o seguinte:

1. A partir do seu navegador, aceda ao [portal Azure.](https://aka.ms/hybridmachineportal)

1. Na página **Machines - Azure Arc,** selecione **adicione,** na parte superior esquerda, ou a opção **Create - Azure Arc** na parte inferior do painel médio. 

1. Na página **de método Selecione uma** página de método, selecione as **máquinas Add utilizando azulejos de script interativos** e, em seguida, selecione **Gerar script**.

1. Na página **de script 'Gerar',** selecione o grupo de subscrição e recursos onde pretende que a máquina seja gerida dentro do Azure. Selecione um local Azure onde os metadados da máquina serão armazenados.

    >[!NOTE]
    >O Azure Arc para servidores (pré-visualização) suporta apenas as seguintes regiões:
    >- Westus2
    >- Europa Ocidental
    >- Oeste da Ásia
    >
    >Reveja considerações adicionais ao selecionar uma região [aqui](overview.md#supported-regions) no artigo de visão geral.

1. Na página **de script 'Gerar',** na lista de down-down **do sistema Operativo,** selecione o sistema operativo em que o script estará em execução.

1. Se a máquina estiver a comunicar através de um servidor proxy para se ligar à internet, selecione **Seguinte: Proxy Server**. 
1. No **separador servidor Proxy,** especifique o endereço IP do servidor proxy ou o nome e número de porta que a máquina utilizará para comunicar com o servidor proxy. Introduza o valor no `http://<proxyURL>:<proxyport>` formato. 
1. Selecione **Review + gerar**.

1. No **separador 'Rever +' gerar,** rever as informações de resumo e, em seguida, selecione **Download**. Se ainda precisar de fazer alterações, selecione **Anterior**.

## <a name="install-and-validate-the-agent-on-windows"></a>Instale e valide o agente no Windows

### <a name="install-manually"></a>Instalar manualmente

Pode instalar manualmente o agente 'Máquina Conectada' executando o pacote do Instalador do Windows *AzureConnectedMachineAgent.msi*. Pode descarregar a versão mais recente do [pacote Windows Agent Windows Installer](https://aka.ms/AzureConnectedMachineAgent) a partir do Microsoft Download Center. 

> [!NOTE]
> * Para instalar ou desinstalar o agente, tem de ter permissões *de Administrador.*
> * Primeiro, tem de descarregar e copiar o pacote Installer para uma pasta no servidor alvo ou a partir de uma pasta de rede partilhada. Se executar o pacote Installer sem quaisquer opções, inicia um assistente de configuração que pode seguir para instalar o agente interativamente.

Se a máquina precisar de comunicar através de um servidor proxy para o serviço, depois de instalar o agente, é necessário executar um comando que é descrito mais tarde no artigo. Isto define a variável do ambiente do sistema de servidor de procuração `https_proxy` .

Se não estiver familiarizado com as opções de linha de comando para pacotes do Windows Installer, reveja [as opções padrão da linha de comando msiexec](https://docs.microsoft.com/windows/win32/msi/standard-installer-command-line-options) e [as opções de linha de comando Msiexec](https://docs.microsoft.com/windows/win32/msi/command-line-options).

Por exemplo, executar o programa de instalação com o `/?` parâmetro para rever a ajuda e a opção de referência rápida. 

```dos
msiexec.exe /i AzureConnectedMachineAgent.msi /?
```

Para instalar o agente em silêncio e criar um ficheiro de registo de configuração na `C:\Support\Logs` pasta que existe, executar o seguinte comando.

```dos
msiexec.exe /i AzureConnectedMachineAgent.msi /qn /l*v "C:\Support\Logs\Azcmagentsetup.log"
```

Se o agente não iniciar após a configuração terminar, verifique se os registos são informativo de erro. O diretório de registos é *%Programfiles%\AzureConnectedMachineAgentAgent\logs*.

### <a name="install-with-the-scripted-method"></a>Instalar com o método scripted

1. Faça login no servidor.

1. Abra uma linha de comandos elevada do PowerShell.

1. Altere para a pasta ou partilhe para a seguinte cópia do script e execute-o no servidor executando o `./OnboardingScript.ps1` script.

Se o agente não iniciar após a configuração terminar, verifique se os registos são informativo de erro. O diretório de registos é *%Programfiles%\AzureConnectedMachineAgentAgent\logs*.

### <a name="configure-the-agent-proxy-setting"></a>Configure a definição de procuração de agente

Para definir a variável ambiente do servidor proxy, executar o seguinte comando:

```powershell
# If a proxy server is needed, execute these commands with the proxy URL and port.
[Environment]::SetEnvironmentVariable("https_proxy", "http://{proxy-url}:{proxy-port}", "Machine")
$env:https_proxy = [System.Environment]::GetEnvironmentVariable("https_proxy","Machine")
# For the changes to take effect, the agent service needs to be restarted after the proxy environment variable is set.
Restart-Service -Name himds
```

>[!NOTE]
>O agente não suporta a autenticação de configuração por procuração nesta pré-visualização.
>

### <a name="configure-agent-communication"></a>Comunicação de agente de configuração

Depois de instalar o agente, é necessário configurar o agente para comunicar com o serviço Azure Arc executando o seguinte comando:

`"%ProgramFiles%\AzureConnectedMachineAgent\azcmagent.exe" connect --resource-group "<resourceGroupName>" --tenant-id "<tenantID>" --location "<regionName>" --subscription-id "<subscriptionID>"`

## <a name="install-and-validate-the-agent-on-linux"></a>Instale e valide o agente no Linux

O agente da Máquina Conectada para o Linux é fornecido no formato de pacote preferido para a distribuição (. RPM ou . DEB) que está hospedado no [repositório](https://packages.microsoft.com/)de pacotes da Microsoft. O [pacote `Install_linux_azcmagent.sh` de script](https://aka.ms/azcmagent) shell executa as seguintes ações:

- Configura a máquina hospedeira para descarregar o pacote de agente a partir de packages.microsoft.com.
- Instala o pacote Fornecedor de Recursos Híbridos.

Opcionalmente, pode configurar o agente com as suas informações de procuração, incluindo o `--proxy "{proxy-url}:{proxy-port}"` parâmetro.

O script também contém lógica para identificar as distribuições suportadas e não suportadas, e verifica as permissões que são necessárias para realizar a instalação. 

O exemplo a seguir descarrega o agente e instala-o:

```bash
# Download the installation package.
wget https://aka.ms/azcmagent -O ~/Install_linux_azcmagent.sh

# Install the connected machine agent. 
bash ~/Install_linux_azcmagent.sh
```

Para descarregar e instalar o agente, incluindo o `--proxy` parâmetro para configurar o agente para comunicar através do seu servidor proxy, executar os seguintes comandos:

```bash
# Download the installation package.
wget https://aka.ms/azcmagent -O ~/Install_linux_azcmagent.sh

# Install the connected machine agent. 
bash ~/Install_linux_azcmagent.sh --proxy "{proxy-url}:{proxy-port}"
```

### <a name="configure-the-agent-communication"></a>Configure a comunicação do agente

Depois de instalar o agente, configuure-o para comunicar com o serviço Azure Arc executando o seguinte comando:

`azcmagent connect --resource-group "<resourceGroupName>" --tenant-id "<tenantID>" --location "<regionName>" --subscription-id "<subscriptionID>"`

## <a name="verify-the-connection-with-azure-arc"></a>Verifique a ligação com a Azure Arc

Depois de instalar o agente e configurá-lo para ligar ao Azure Arc para servidores (pré-visualização), vá ao portal Azure para verificar se o servidor foi conectado com sucesso. Veja as suas máquinas no [portal Azure](https://aka.ms/hybridmachineportal).

![Uma ligação de servidor bem sucedida](./media/onboard-portal/arc-for-servers-successful-onboard.png)

## <a name="next-steps"></a>Passos seguintes

- Aprenda a gerir a sua máquina utilizando [a Azure Policy](../../governance/policy/overview.md), para coisas como [a configuração do hóspede](../../governance/policy/concepts/guest-configuration.md)VM , verificando se a máquina está a reportar ao espaço de trabalho esperado do Log Analytics, permitir a monitorização com o [Azure Monitor com VMs](../../azure-monitor/insights/vminsights-enable-at-scale-policy.md), e muito mais.

- Saiba mais sobre o [agente Log Analytics.](../../azure-monitor/platform/log-analytics-agent.md) O agente Log Analytics para Windows e Linux é necessário quando pretende monitorizar proactivamente o SISTEMA e as cargas de trabalho em funcionamento na máquina, geri-lo utilizando livros de automação ou soluções como a Update Management, ou utilizar outros serviços Azure como o [Azure Security Center.](../../security-center/security-center-intro.md)
