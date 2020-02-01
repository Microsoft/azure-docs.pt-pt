---
title: Ligue as máquinas híbridas ao Azure a partir do portal Azure
description: Neste artigo, aprende-se a instalar o agente e a ligar máquinas ao Azure utilizando o Azure Arc para servidores (pré-visualização) a partir do portal Azure.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
author: mgoedtel
ms.author: magoedte
ms.date: 01/29/2020
ms.custom: mvc
ms.topic: quickstart
ms.openlocfilehash: 165ed8d0aad7011f1ea71ff870ee4629f1f12613
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/31/2020
ms.locfileid: "76898594"
---
# <a name="connect-hybrid-machines-to-azure-from-the-azure-portal"></a>Ligue as máquinas híbridas ao Azure a partir do portal Azure

Pode ativar o Azure Arc para servidores (pré-visualização) para um ou um pequeno número de máquinas Windows ou Linux no seu ambiente, executando um conjunto de passos manualmente, ou utilizando um método automatizado executando um script de modelo que fornecemos. Este guião automatiza o download e instalação de ambos os agentes.

Este método de instalação requer que tenha direitos administrativos sobre a máquina para instalar e configurar o agente. No Linux, utilizando a conta raiz e no Windows, é membro do grupo de Administradores Locais.

Antes de começar, certifique-se de revisar os [pré-requisitos](overview.md#prerequisites) e verificar se sua assinatura e seus recursos atendem aos requisitos.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="generate-install-script-from-the-azure-portal"></a>Gereça o script de instalação a partir do portal Azure

O guião para automatizar o download, instalação e estabelecer a ligação com o Azure Arc está disponível no portal Azure. Os seguintes passos descrevem como completar este processo.

1. A partir do seu navegador, [lance https://aka.ms/hybridmachineportal. ](https://aka.ms/hybridmachineportal)

2. Na página **Máquinas - Azure Arc,** selecione **+Adicione** no canto superior esquerdo ou selecione a **opção Criar - Azure Arc** a partir da parte inferior do painel médio. 

3. Na página **Select a method,** selecione a partir das máquinas Adicionar utilizando o script **de script interativo** **Generate script**.

4. Na página de **script Generate,** selecione o grupo de subscrição e recursos onde pretende que a máquina seja gerida dentro do Azure. Selecione uma localização Azure onde os metadados da máquina serão armazenados.

    >[!NOTE]
    >O Azure Arc para servidores (pré-visualização), suporta apenas as seguintes regiões:
    >- WestUS2
    >- Europa Ocidental
    >- Ásia Ocidental
    >

5. Na página de **script Generate,** na lista de drop-down do **sistema operativo,** selecione o sistema operativo apropriado em que o script estará em execução.

6. Se a máquina estiver a comunicar através de um servidor proxy para se ligar à Internet, selecione a opção **Seguinte: Proxy Server>** . No separador **proxy do servidor,** especifique o endereço IP do servidor proxy ou nome e número de porta que a máquina utilizará para comunicar com o servidor proxy. Introduza o valor seguindo o formato `http://<proxyURL>:<proxyport>`. Uma vez concluído, selecione **Review + gerar**.  Caso contrário, selecione **Review + gere** para completar os passos.

7. No separador **Review + gere,** reveja as informações sumárias e, em seguida, selecione **Download**. Caso contrário, se precisar de fazer alterações, pode selecionar **Anterior**.

## <a name="install-and-validate-the-agent-on-windows"></a>Instale e valide o agente no Windows

### <a name="install-manually"></a>Instalar manualmente

Pode instalar o agente Connected Machine manualmente executando o pacote de instalação do Instalador do Windows `AzureConnectedMachineAgent.msi` depois de o descarregar e copiá-lo para uma pasta no servidor alvo ou a partir de uma pasta de rede partilhada. Se executar o pacote Instalador sem opções, inicia um assistente de configuração que pode seguir para instalar o agente interactivamente.

>[!NOTE]
>*Administrador* privilégios são necessários para instalar ou desinstalar o agente.

Se a máquina precisar de comunicar através de um servidor proxy para o serviço, depois de instalar o agente, precisa de executar um comando descrito numa secção abaixo, para definir a variável do ambiente do servidor proxy `https_proxy`.

A tabela seguinte realça os parâmetros que são suportados pelo programa de configuração para o agente a partir da linha de comandos.

| Parâmetro | Descrição |
|:--|:--|
| /? | Devolve uma lista das opções da linha de comandos. |
| /S | Executa uma instalação silenciosa, sem interação do utilizador. |

Por exemplo, para executar o programa de instalação com o parâmetro `/?`, insira `msiexec.exe /i AzureConnectedMachineAgent.msi /?`.

Os ficheiros do agente Máquina Conectada são instalados em *Ficheiros C:\Program\AzureConnectedMachineAgent* por padrão. Se o agente não começar depois de a configuração estar concluída, verifique se os registos estão a obter informações detalhadas sobre o erro. O diretório de registo é *%Programfiles%\AzureConnectedMachineAgentAgent\logs*.

### <a name="install-using-scripted-method"></a>Instalar utilizando o método scripted

1. Inicie sessão no servidor.

2. Abra um pedido de comando PowerShell elevado.

3. Mude para a pasta ou partilhe que copiou o script para e execute-o no servidor executando o comando `./OnboardingScript.ps1`.

### <a name="configure-agent-proxy-setting"></a>Configurar a definição de procuração de agente

Executar o seguinte comando para definir a variável ambiente do servidor proxy.

```powershell
# If a proxy server is needed, execute these commands with proxy URL and port
[Environment]::SetEnvironmentVariable("https_proxy", "http://{proxy-url}:{proxy-port}", "Machine")
$env:https_proxy = [System.Environment]::GetEnvironmentVariable("https_proxy","Machine")
# The agent service needs to be restarted after the proxy environment variable is set in order for the changes to take effect.
Restart-Service -Name himds
```

>[!NOTE]
>O agente não suporta a definição de autenticação por procuração nesta pré-visualização.
>

### <a name="configure-agent-communication"></a>Configurar a comunicação do agente

Depois de instalar o agente, é necessário configurar o agente para comunicar com o serviço Azure Arc executando o seguinte comando:

`%ProgramFiles%\AzureConnectedMachineAgent\azcmagent.exe" connect --resource-group "<resourceGroupName>" --tenant-id "<tenantID>" --location "<regionName>" --subscription-id "<subscriptionID>"`

## <a name="install-and-validate-the-agent-on-linux"></a>Instale e valide o agente no Linux

O agente Máquina Conectada para Linux é fornecido no formato de pacote preferido para a distribuição (. RPM ou . DEB) hospedado no [repositório](https://packages.microsoft.com/)de pacotes da Microsoft . Um pacote de script sujeira `Install_linux_azcmagent.sh` localizado em [https://aka.ms/azcmagent](https://aka.ms/azcmagent) executa as seguintes ações:

- Configura a máquina hospedeira para descarregar o pacote de agente a partir de packages.microsoft.com.
- Instala o pacote do provedor de recursos híbrido.
- Opcionalmente, configure o agente com as suas informações de procuração, incluindo o parâmetro `--proxy "{proxy-url}:{proxy-port}"`.

O script também contém lógica para identificar distribuições suportadas e não suportadas, bem como verificar as permissões necessárias para realizar a instalação. 

O exemplo abaixo descarrega o agente e instala-o, sem realizar nenhuma das verificações condicionais.

```bash
# Download the installation package
wget https://aka.ms/azcmagent -O ~/Install_linux_azcmagent.sh

# Install the connected machine agent. 
bash ~/Install_linux_azcmagent.sh
```

Para descarregar e instalar o agente, incluindo o parâmetro `--proxy` para configurar o agente para comunicar através do seu servidor proxy, execute os seguintes comandos:

```bash
# Download the installation package
wget https://aka.ms/azcmagent -O ~/Install_linux_azcmagent.sh

# Install the connected machine agent. 
bash ~/Install_linux_azcmagent.sh --proxy "{proxy-url}:{proxy-port}"
```

### <a name="configure-agent-communication"></a>Configurar a comunicação do agente

Depois de instalar o agente, é necessário configurar o agente para comunicar com o serviço Azure Arc executando o seguinte comando:

`/opt/azcmagent/bin/azcmagent.exe" connect --resource-group "<resourceGroupName>" --tenant-id "<tenantID>" --location "<regionName>" --subscription-id "<subscriptionID>"`

## <a name="verify-connection-with-azure-arc"></a>Verifique a ligação com o Arco Azure

Depois de executar os passos para instalar o agente e configurá-lo para ligar ao Azure Arc para servidores (pré-visualização), vá ao portal Azure para verificar se o servidor foi ligado com sucesso. Pode ver as suas máquinas no portal Azure visitando [https://aka.ms/hybridmachineportal](https://aka.ms/hybridmachineportal).

![Integração bem-sucedida](./media/quickstart-onboard/arc-for-servers-successful-onboard.png)

## <a name="clean-up"></a>Limpeza

Para desligar uma máquina do Azure Arc para servidores (pré-visualização), é necessário executar os seguintes passos.

1. Abra o Arco Azure para servidores (pré-visualização) visitando [https://aka.ms/hybridmachineportal](https://aka.ms/hybridmachineportal).

2. Selecione a máquina na lista, clique na elipse (`...`) e **selecione Eliminar**.

3. Para desinstalar o agente Windows da máquina, execute o seguinte:

    1. Faça logon no computador com uma conta que tenha direitos administrativos.

    2. No **Painel de Controlo,** selecione **Programas e Funcionalidades.**

    3. Em **Programas e Funcionalidades,** selecione **Agente de Máquina Seletiva,** selecione **Desinstalar**, e, em seguida, selecione **Sim**.

        >[!NOTE]
        >O assistente de configuração do agente também pode ser executado por um pacote de instalação **AzureConnectedMachineAgent.msi** de dois cliques.

    Se quiser escrever a desinstalação, pode utilizar o seguinte exemplo, que recupera o código do produto e desinstala o agente utilizando a linha de comando Msiexec.exe - `msiexec /x {Product Code}`. Abra o Editor de Registo e procure sob a chave de registo `HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\Uninstall` e encontre o código do produto GUID. Em seguida, pode desinstalar o agente usando o Msiexec.

   O exemplo a seguir demonstra a desinstalação do agente.

   ```powershell
   Get-ChildItem -Path HKLM:\Software\Microsoft\Windows\CurrentVersion\Uninstall | `
   Get-ItemProperty | `
   Where-Object {$_.DisplayName -eq "Azure Connected Machine Agent"} | `
   ForEach-Object {MsiExec.exe /x "$($_.PsChildName)" /qn}
   ```

4. Para desinstalar o agente Linux, execute o seguinte comando para desinstalar o agente.

   ```bash
   sudo apt purge hybridagent
   ```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Atribuir uma política a computadores conectados](../../governance/policy/assign-policy-portal.md)
