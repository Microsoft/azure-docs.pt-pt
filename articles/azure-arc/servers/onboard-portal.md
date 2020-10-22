---
title: Ligue máquinas híbridas ao Azure a partir do portal Azure
description: Neste artigo, aprende-se a instalar o agente e a ligar máquinas ao Azure utilizando servidores ativados pelo Azure Arc a partir do portal Azure.
ms.date: 10/21/2020
ms.topic: conceptual
ms.openlocfilehash: 8769a3b76172bc6508b7c52eda359695c01eaa4b
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/22/2020
ms.locfileid: "92370156"
---
# <a name="connect-hybrid-machines-to-azure-from-the-azure-portal"></a>Ligue máquinas híbridas ao Azure a partir do portal Azure

Pode ativar os servidores ativados do Azure Arc para um ou um pequeno número de máquinas Windows ou Linux no seu ambiente, realizando manualmente um conjunto de passos. Ou pode usar um método automatizado executando um script de modelo que fornecemos. Este script automatiza o download e instalação de ambos os agentes.

Este método requer que tenha permissões de administrador na máquina para instalar e configurar o agente. No Linux, utilizando a conta raiz e no Windows, é membro do grupo de Administradores Locais.

Antes de começar, certifique-se de rever os [pré-requisitos](agent-overview.md#prerequisites) e verificar se a sua subscrição e recursos cumprem os requisitos. Para obter informações sobre regiões apoiadas e outras considerações conexas, consulte [as regiões de Azure apoiadas.](overview.md#supported-regions)

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="generate-the-installation-script-from-the-azure-portal"></a>Gere o script de instalação a partir do portal Azure

O script para automatizar o download e instalação, e para estabelecer a ligação com a Azure Arc, está disponível a partir do portal Azure. Para concluir o processo, faça o seguinte:

1. A partir do seu navegador, aceda ao [portal Azure.](https://portal.azure.com)

1. Na página **Servers - Azure Arc,** selecione **Adicionar** na parte superior esquerda.

1. Na página **de método Selecione uma** página de método, selecione os **servidores Add utilizando azulejos de script interativos** e, em seguida, selecione **Gerar script**.

1. Na página **de script 'Gerar',** selecione o grupo de subscrição e recursos onde pretende que a máquina seja gerida dentro do Azure. Selecione um local Azure onde os metadados da máquina serão armazenados. Esta localização pode ser a mesma ou diferente, como a localização do grupo de recursos.

1. Na página **Pré-Requisitos,** reveja as informações e, em seguida, selecione **Seguinte: Detalhes do recurso**.

1. Na página **de detalhes do recurso,** forneça o seguinte:

    1. Na lista de down-down do **grupo de recursos,** selecione o grupo de recursos a partir do quais a máquina será gerida.
    1. Na lista de drop-down da **Região,** selecione a região Azure para armazenar os metadados dos servidores.
    1. Na lista de **suspensos do sistema Operativo,** selecione o sistema operativo que o script está configurado para ser executado.
    1. Se a máquina estiver a comunicar através de um servidor proxy para se ligar à internet, especifique o endereço IP do servidor proxy ou o nome e número de porta que a máquina utilizará para comunicar com o servidor proxy. Introduza o valor no `http://<proxyURL>:<proxyport>` formato.
    1. Selecione **Seguinte: Tags**.

1. Na página **Tags,** reveja as etiquetas de **localização físicas** padrão sugeridas e introduza um valor, ou especifique uma ou mais **tags personalizadas** para suportar os seus padrões.

1. Selecione **Seguinte: Descarregue e execute o script**.

1. Na página de **descarregamento e execução** do script, reveja as informações do resumo e, em seguida, selecione **Download**. Se ainda precisar de fazer alterações, selecione **Anterior**.

## <a name="install-and-validate-the-agent-on-windows"></a>Instale e valide o agente no Windows

### <a name="install-manually"></a>Instalar manualmente

Pode instalar manualmente o agente 'Máquina Conectada' executando o pacote do Instalador do Windows *AzureConnectedMachineAgent.msi*. Pode descarregar a versão mais recente do [pacote Windows Agent Windows Installer](https://aka.ms/AzureConnectedMachineAgent) a partir do Microsoft Download Center.

>[!NOTE]
>* Para instalar ou desinstalar o agente, tem de ter permissões *de Administrador.*
>* Primeiro, tem de descarregar e copiar o pacote Installer para uma pasta no servidor alvo ou a partir de uma pasta de rede partilhada. Se executar o pacote Installer sem quaisquer opções, inicia um assistente de configuração que pode seguir para instalar o agente interativamente.

Se a máquina precisar de comunicar através de um servidor proxy para o serviço, depois de instalar o agente, é necessário executar um comando descrito nos degraus abaixo. Este comando define a variável ambiente do sistema de servidor proxy `https_proxy` .

Se não estiver familiarizado com as opções de linha de comando para pacotes do Windows Installer, reveja [as opções padrão da linha de comando msiexec](/windows/win32/msi/standard-installer-command-line-options) e [as opções de linha de comando Msiexec](/windows/win32/msi/command-line-options).

Por exemplo, executar o programa de instalação com o `/?` parâmetro para rever a ajuda e a opção de referência rápida.

```dos
msiexec.exe /i AzureConnectedMachineAgent.msi /?
```

1. Para instalar o agente em silêncio e criar um ficheiro de registo de configuração na `C:\Support\Logs` pasta que existe, executar o seguinte comando.

    ```dos
    msiexec.exe /i AzureConnectedMachineAgent.msi /qn /l*v "C:\Support\Logs\Azcmagentsetup.log"
    ```

    Se o agente não iniciar após a configuração terminar, verifique se os registos são informativo de erro. O diretório de registos é *%ProgramData%\AzureConnectedMachineAgent\log*.

2. Se a máquina precisar de comunicar através de um servidor proxy, para definir a variável de ambiente do servidor proxy, executar o seguinte comando:

    ```powershell
    [Environment]::SetEnvironmentVariable("https_proxy", "http://{proxy-url}:{proxy-port}", "Machine")
    $env:https_proxy = [System.Environment]::GetEnvironmentVariable("https_proxy","Machine")
    # For the changes to take effect, the agent service needs to be restarted after the proxy environment variable is set.
    Restart-Service -Name himds
    ```

    >[!NOTE]
    >O agente não suporta a autenticação de configuração por procuração nesta pré-visualização.
    >

3. Após instalar o agente, terá de configurá-lo para comunicar com o serviço Azure Arc ao executar o seguinte comando:

    ```dos
    "%ProgramFiles%\AzureConnectedMachineAgent\azcmagent.exe" connect --resource-group "resourceGroupName" --tenant-id "tenantID" --location "regionName" --subscription-id "subscriptionID"
    ```

### <a name="install-with-the-scripted-method"></a>Instalar com o método scripted

1. Faça login no servidor.

1. Abra uma linha de comandos elevada do PowerShell.

    >[!NOTE]
    >O script suporta apenas a execução a partir de uma versão de 64 bits do Windows PowerShell.
    >

1. Altere para a pasta ou partilhe para a seguinte cópia do script e execute-o no servidor executando o `./OnboardingScript.ps1` script.

Se o agente não iniciar após a configuração terminar, verifique se os registos são informativo de erro. O diretório de registos é *%ProgramData%\AzureConnectedMachineAgent\log*.

## <a name="install-and-validate-the-agent-on-linux"></a>Instale e valide o agente no Linux

O agente da Máquina Conectada para o Linux é fornecido no formato de pacote preferido para a distribuição (. RPM ou . DEB) que está hospedado no [repositório](https://packages.microsoft.com/)de pacotes da Microsoft. O [pacote `Install_linux_azcmagent.sh` de script](https://aka.ms/azcmagent) shell executa as seguintes ações:

- Configura a máquina hospedeira para descarregar o pacote de agente a partir de packages.microsoft.com.
- Instala o pacote Fornecedor de Recursos Híbridos.
- Regista a máquina com Arco Azure

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

## <a name="verify-the-connection-with-azure-arc"></a>Verificar a ligação com o Azure Arc

Depois de instalar o agente e configurá-lo para ligar aos servidores ativados do Azure Arc, vá ao portal Azure para verificar se o servidor se ligou com sucesso. Veja as suas máquinas no [portal do Azure](https://aka.ms/hybridmachineportal).

![Uma ligação de servidor bem sucedida](./media/onboard-portal/arc-for-servers-successful-onboard.png)

## <a name="next-steps"></a>Passos seguintes

* As informações relativas à resolução de problemas podem ser encontradas no guia do [agente da máquina conectado de resolução de problemas.](troubleshoot-agent-onboard.md)

* Aprenda a gerir a sua máquina utilizando [a Azure Policy](../../governance/policy/overview.md), para coisas como [a configuração do hóspede](../../governance/policy/concepts/guest-configuration.md)VM , verificando se a máquina está a reportar ao espaço de trabalho esperado do Log Analytics, permitir a monitorização com o [Azure Monitor com VMs](../../azure-monitor/insights/vminsights-enable-policy.md), e muito mais.

* Saiba mais sobre o [agente Log Analytics.](../../azure-monitor/platform/log-analytics-agent.md) O agente Log Analytics para Windows e Linux é necessário quando pretende recolher dados de monitorização do sistema operativo e da carga de trabalho, geri-lo utilizando livros de aplicação da Automação ou funcionalidades como Update Management, ou utilizar outros serviços Azure como [o Azure Security Center](../../security-center/security-center-introduction.md).