---
title: Gerir o agente de servidores ativado pelo Arco Azure
description: Este artigo descreve as diferentes tarefas de gestão que normalmente irá executar durante o ciclo de vida do agente ativado pelos servidores Azure Arc Connected Machine.
ms.date: 10/21/2020
ms.topic: conceptual
ms.openlocfilehash: 184b0425b956232b4485047cafb00a7ced21c7dd
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/22/2020
ms.locfileid: "92371431"
---
# <a name="managing-and-maintaining-the-connected-machine-agent"></a>Gerir e manter o agente da Máquina Conectada

Após a implantação inicial do Arco Azure ativado o agente de máquinas conectadas para Windows ou Linux, poderá ter de reconfigurar o agente, atualizá-lo ou removê-lo do computador se tiver atingido a fase de reforma no seu ciclo de vida. Pode gerir facilmente estas tarefas de manutenção de rotina manualmente ou através da automatização, o que reduz tanto os erros operacionais como as despesas.

## <a name="before-uninstalling-agent"></a>Antes de desinstalar o agente

Antes de remover o agente 'Máquina Conectada' do seu servidor ativado pelo Arco, considere o seguinte para evitar problemas ou custos inesperados adicionados à sua conta Azure:

* Se tiver implantado extensões Azure VM num servidor ativado e remover o agente 'Máquina Conectada' ou eliminar o recurso que representa o servidor ativado pelo Arco no grupo de recursos, essas extensões continuam a funcionar e a executar o seu funcionamento normal.

* Se eliminar o recurso que representa o servidor ativado pelo Arco no seu grupo de recursos, mas não desinstale as extensões VM, quando voltar a registar a máquina, não poderá gerir as extensões VM instaladas.

Para servidores ou máquinas que já não pretende gerir com servidores ativados do Azure Arc, é necessário seguir estes passos para deixar de o gerir com sucesso:

1. Retire as extensões VM da máquina ou servidor. Os passos são fornecidos abaixo.

2. Desligue a máquina do Arco Azure utilizando um dos seguintes métodos:

    * O `azcmagent disconnect` comando de funcionamento na máquina ou servidor.

    * A partir do servidor ativado pelo Arc registado selecionado no portal Azure, selecionando **Delete** a partir da barra superior.

    * Utilizando o [Azure CLI](../../azure-resource-manager/management/delete-resource-group.md?tabs=azure-cli#delete-resource) ou [a Azure PowerShell](../../azure-resource-manager/management/delete-resource-group.md?tabs=azure-powershell#delete-resource). Para a utilização do `ResourceType` parâmetro `Microsoft.HybridCompute/machines` .

3. Desinstale o agente da máquina ou do servidor. Siga os passos abaixo.

## <a name="upgrading-agent"></a>Agente de upgrade

O agente Azure Connected Machine para Windows e Linux pode ser atualizado para a versão mais recente manualmente ou automaticamente, dependendo dos seus requisitos. A tabela seguinte descreve os métodos suportados para realizar a atualização do agente.

| Sistema operativo | Método de atualização |
|------------------|----------------|
| Windows | Manualmente<br> Windows Update |
| Ubuntu | [Apt](https://help.ubuntu.com/lts/serverguide/apt.html) |
| SUSE Linux Enterprise Server | [zypper](https://en.opensuse.org/SDB:Zypper_usage_11.3) |
| RedHat Enterprise, Amazon, CentOS Linux | [yum](https://wiki.centos.org/PackageManagement/Yum) |

### <a name="windows-agent"></a>Agente do Windows

O pacote de atualização para o agente de máquinas conectadas para windows está disponível a partir de:

* Microsoft Update

* [Catálogo de atualizações da Microsoft](https://www.catalog.update.microsoft.com/Home.aspx)

* [Pacote do Windows Agent Windows Installer](https://aka.ms/AzureConnectedMachineAgent) do Microsoft Download Center.

O agente pode ser atualizado seguindo uma variedade de métodos para suportar o seu processo de gestão de atualização de software. Além de obter a partir do Microsoft Update, pode descarregar e executar manualmente a partir do Pedido de Comando, a partir de um script ou outra solução de automação, ou do assistente de UI executando `AzureConnectedMachine.msi` .

> [!NOTE]
> * Para atualizar o agente, tem de ter permissões *de administrador.*
> * Para atualizar manualmente, tem primeiro de descarregar e copiar o pacote Installer para uma pasta no servidor alvo ou a partir de uma pasta de rede partilhada. 

Se não estiver familiarizado com as opções de linha de comando para pacotes do Windows Installer, reveja [as opções padrão da linha de comando msiexec](/windows/win32/msi/standard-installer-command-line-options) e [as opções de linha de comando Msiexec](/windows/win32/msi/command-line-options).

#### <a name="to-upgrade-using-the-setup-wizard"></a>Para atualizar usando o Assistente de Configuração

1. Inscreva-se no computador com uma conta que tenha direitos administrativos.

2. Execute **AzureConnectedMachineAgent.msi** para iniciar o Assistente de Configuração.

O Assistente de Configuração descobre se existe uma versão anterior e, em seguida, executa automaticamente uma atualização do agente. Quando a atualização termina, o Assistente de Configuração fecha-se automaticamente.

#### <a name="to-upgrade-from-the-command-line"></a>Para atualizar a partir da linha de comando

1. Inscreva-se no computador com uma conta que tenha direitos administrativos.

2. Para atualizar o agente em silêncio e criar um ficheiro de registo de configuração na `C:\Support\Logs` pasta, execute o seguinte comando.

    ```dos
    msiexec.exe /i AzureConnectedMachineAgent.msi /qn /l*v "C:\Support\Logs\Azcmagentupgradesetup.log"
    ```

### <a name="linux-agent"></a>Agente Linux

Para atualizar o agente numa máquina Linux para a versão mais recente, envolve dois comandos. Um comando para atualizar o índice de pacotes locais com a lista dos mais recentes pacotes disponíveis dos repositórios, e um comando para atualizar o pacote local.

Você pode baixar o mais recente pacote de agente do [repositório](https://packages.microsoft.com/)de pacotes da Microsoft .

> [!NOTE]
> Para atualizar o agente, tem de ter permissões de acesso *à raiz* ou com uma conta que tenha direitos elevados utilizando o Sudo.

#### <a name="upgrade-ubuntu"></a>Upgrade Ubuntu

1. Para atualizar o índice de pacote local com as últimas alterações es feitas nos repositórios, executar o seguinte comando:

    ```bash
    apt update
    ```

2. Para atualizar o seu sistema, executar o seguinte comando:

    ```bash
    apt upgrade
    ```

As ações do comando [apt,](https://help.ubuntu.com/lts/serverguide/apt.html) como a instalação e remoção de pacotes, estão registadas no `/var/log/dpkg.log` ficheiro de registo.

#### <a name="upgrade-red-hatcentosamazon-linux"></a>Upgrade Red Hat/CentOS/Amazon Linux

1. Para atualizar o índice de pacote local com as últimas alterações es feitas nos repositórios, executar o seguinte comando:

    ```bash
    yum check-update
    ```

2. Para atualizar o seu sistema, executar o seguinte comando:

    ```bash
    yum update
    ```

As ações do comando [yum,](https://access.redhat.com/articles/yum-cheat-sheet) como a instalação e remoção de pacotes, estão registadas no `/var/log/yum.log` ficheiro de registo. 

#### <a name="upgrade-suse-linux-enterprise"></a>Upgrade SUSE Linux Enterprise

1. Para atualizar o índice de pacote local com as últimas alterações es feitas nos repositórios, executar o seguinte comando:

    ```bash
    zypper refresh
    ```

2. Para atualizar o seu sistema, executar o seguinte comando:

    ```bash
    zypper update
    ```

As ações do comando [zypper,](https://en.opensuse.org/Portal:Zypper) tais como a instalação e remoção de pacotes, estão registadas no `/var/log/zypper.log` ficheiro de registo.

## <a name="about-the-azcmagent-tool"></a>Sobre a ferramenta Azcmagent

A ferramenta Azcmagent (Azcmagent.exe) é utilizada para configurar o agente de máquinas conectadas do Arco Azure durante a instalação ou modificar a configuração inicial do agente após a instalação. Azcmagent.exe fornece parâmetros de linha de comando para personalizar o agente e ver o seu estado:

* **Ligar** - Ligar a máquina ao Arco Azure

* **Desconexão** - Para desligar a máquina do Arco Azure

* **Mostrar** - Ver o estado do agente e as suas propriedades de configuração (nome do Grupo de Recursos, ID de subscrição, versão, etc.), o que pode ajudar na resolução de problemas com o agente. Inclua o `-j` parâmetro para a saída dos resultados no formato JSON.

* **Registos** - Cria um ficheiro .zip no diretório atual que contém registos para o ajudar durante a resolução de problemas.

* **Versão** - Mostra a versão do agente 'Máquina Conectada'.

* **-h ou --ajuda** - Mostra parâmetros disponíveis de linha de comando

    Por exemplo, para ver uma ajuda detalhada para o parâmetro **Deconnect, escreva** `azcmagent reconnect -h` . 

* **-v ou --verbose** - Permitir a exploração de verbose

Pode executar um **Connect** and **Disconnect** manualmente enquanto iniciado ininterruptamente, ou automatizar utilizando o mesmo principal de serviço que usou para bordo de vários agentes ou com um [token de acesso à](../../active-directory/develop/access-tokens.md)plataforma de identidade da Microsoft. Se não usou um principal de serviço para registar a máquina com servidores ativados Azure Arc, consulte o [seguinte artigo](onboard-service-principal.md#create-a-service-principal-for-onboarding-at-scale) para criar um principal de serviço.

>[!NOTE]
>Você deve ter permissões de acesso à *raiz* em máquinas Linux para executar **azcmagent**.

### <a name="connect"></a>Ligar

Este parâmetro especifica um recurso no Azure Resource Manager que representa a máquina é criado em Azure. O recurso encontra-se no grupo de subscrição e recursos especificado, e os dados sobre a máquina são armazenados na região de Azure especificada pela `--location` definição. O nome de recurso predefinido é o nome de hospedeiro da máquina, se não for especificado.

Um certificado correspondente à identidade atribuída ao sistema da máquina é então descarregado e armazenado localmente. Uma vez concluído este passo, o Serviço de Metadados da Máquina Conectada Azure e o Agente de Configuração de Convidados começam a sincronizar com os servidores ativados do Azure Arc.

Para ligar utilizando um principal de serviço, executar o seguinte comando:

`azcmagent connect --service-principal-id <serviceprincipalAppID> --service-principal-secret <serviceprincipalPassword> --tenant-id <tenantID> --subscription-id <subscriptionID> --resource-group <ResourceGroupName> --location <resourceLocation>`

Para ligar usando um token de acesso, executar o seguinte comando:

`azcmagent connect --access-token <> --subscription-id <subscriptionID> --resource-group <ResourceGroupName> --location <resourceLocation>`

Para se conectar com as suas credenciais elevadas registadas (interativas), executar o seguinte comando:

`azcmagent connect --tenant-id <TenantID> --subscription-id <subscriptionID> --resource-group <ResourceGroupName> --location <resourceLocation>`

### <a name="disconnect"></a>Desligar

Este parâmetro especifica que um recurso no Azure Resource Manager que representa a máquina é eliminado em Azure. Não apaga o agente da máquina, isto deve ser feito como um passo separado. Depois de a máquina ser desligada, se pretender re-registrá-la com servidores ativados Azure Arc, utilize `azcmagent connect` para que seja criado um novo recurso para o mesmo em Azure.

> [!NOTE]
> Se tiver implementado uma ou mais extensões Azure VM para o seu servidor ativado pelo Arco e eliminar o seu registo no Azure, as extensões ainda estão instaladas. É importante entender que, dependendo da extensão instalada, está a desempenhar ativamente a sua função. As máquinas que se destinam a ser retiradas ou que já não sejam geridas pelos servidores ativados pela Arc devem primeiro remover as extensões antes de remover o seu registo do Azure.

Para desligar utilizando um principal de serviço, executar o seguinte comando:

`azcmagent disconnect --service-principal-id <serviceprincipalAppID> --service-principal-secret <serviceprincipalPassword> --tenant-id <tenantID>`

Para desligar usando um token de acesso, executar o seguinte comando:

`azcmagent disconnect --access-token <accessToken>`

Para desligar com as suas credenciais elevadas registadas (interativas), executar o seguinte comando:

`azcmagent disconnect`

## <a name="remove-the-agent"></a>Remova o agente

Execute um dos seguintes métodos para desinstalar o windows ou o agente da máquina ligada Linux da máquina. A remoção do agente não desbloqueia a máquina com servidores ativados pelo Arco nem remove as extensões Azure VM instaladas. É necessário efetuar esses passos separadamente quando já não precisar de gerir a máquina em Azure, e devem ser concluídos antes de desinstalar o agente.

### <a name="windows-agent"></a>Agente do Windows

Ambos os métodos seguintes removem o agente, mas não removem a pasta *C:\Program\AzureConnectedMachineAgent* na máquina.

#### <a name="uninstall-from-control-panel"></a>Desinstalar do Painel de Controlo

1. Para desinstalar o agente Windows a partir da máquina, faça o seguinte:

    a. Inscreva-se no computador com uma conta que tenha permissões de administrador.  
    b. No **Painel de Controlo**, selecione Programas e **Funcionalidades**.  
    c. Em **Programas e Funcionalidades**, selecione **Azure Connected Machine Agent**, selecione **Desinstalar**e, em seguida, selecione **Sim**.  

    >[!NOTE]
    > Também pode executar o assistente de configuração do agente clicando duas vezes no pacote de instalação **AzureConnectedMachineAgent.msi.**

#### <a name="uninstall-from-the-command-line"></a>Desinstalar da linha de comando

Para desinstalar manualmente o agente a partir do Pedido de Comando ou para utilizar um método automatizado, como um script, pode utilizar o seguinte exemplo. Primeiro, é necessário recuperar o código do produto, que é um GUID que é o principal identificador do pacote de aplicações, do sistema operativo. A desinstalação é realizada utilizando a linha de comando Msiexec.exe - `msiexec /x {Product Code}` .

1. Abra o Editor de Registos.

2. Na tecla de `HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\Uninstall` registo, procure e copie o código do produto GUID.

3. Em seguida, pode desinstalar o agente utilizando o Msiexec utilizando os seguintes exemplos:

   * Do tipo de linha de comando:

       ```dos
       msiexec.exe /x {product code GUID} /qn
       ```

   * Pode executar os mesmos passos utilizando o PowerShell:

       ```powershell
       Get-ChildItem -Path HKLM:\Software\Microsoft\Windows\CurrentVersion\Uninstall | `
       Get-ItemProperty | `
       Where-Object {$_.DisplayName -eq "Azure Connected Machine Agent"} | `
       ForEach-Object {MsiExec.exe /x "$($_.PsChildName)" /qn}
       ```

### <a name="linux-agent"></a>Agente Linux

> [!NOTE]
> Para desinstalar o agente, tem de ter permissões de acesso *à raiz* ou com uma conta que tenha direitos elevados utilizando o Sudo.

Para desinstalar o agente Linux, o comando a utilizar depende do sistema operativo Linux.

- Para Ubuntu, executar o seguinte comando:

    ```bash
    sudo apt purge azcmagent
    ```

- Para RHEL, CentOS e Amazon Linux, executar o seguinte comando:

    ```bash
    sudo yum remove azcmagent
    ```

- Para sles, executar o seguinte comando:

    ```bash
    sudo zypper remove azcmagent
    ```

## <a name="unregister-machine"></a>Máquina não registro

Se estiver a planear parar de gerir a máquina com serviços de suporte em Azure, execute os seguintes passos para não registar a máquina com servidores ativados pelo Arc. Pode efetuar estes passos antes ou depois de ter removido o agente 'Máquina Conectada' da máquina.

1. Abra o Arco Azure ativando os servidores indo para o [portal Azure](https://aka.ms/hybridmachineportal).

2. Selecione a máquina na lista, selecione a elipse **(... )** e, em seguida, selecione **Delete**.

## <a name="update-or-remove-proxy-settings"></a>Atualizar ou remover definições de procuração

Para configurar o agente para comunicar ao serviço através de um servidor proxy ou remover esta configuração após a implementação, ou utilizar um dos seguintes métodos para completar esta tarefa.

### <a name="windows"></a>Windows

Para definir a variável ambiente do servidor proxy, executar o seguinte comando:

```powershell
# If a proxy server is needed, execute these commands with the proxy URL and port.
[Environment]::SetEnvironmentVariable("https_proxy","http://{proxy-url}:{proxy-port}","Machine")
$env:https_proxy = [System.Environment]::GetEnvironmentVariable("https_proxy","Machine")
# For the changes to take effect, the agent service needs to be restarted after the proxy environment variable is set.
Restart-Service -Name himds
```

Para configurar o agente para parar de comunicar através de um servidor proxy, executar o seguinte comando para remover a variável ambiental do servidor proxy e reiniciar o serviço de agente:

```powershell
[Environment]::SetEnvironmentVariable("https_proxy",$null,"Machine")
$env:https_proxy = [System.Environment]::GetEnvironmentVariable("https_proxy","Machine")
# For the changes to take effect, the agent service needs to be restarted after the proxy environment variable removed.
Restart-Service -Name himds
```

### <a name="linux"></a>Linux

Para definir o servidor proxy, executar o seguinte comando a partir do diretório que descarregou o pacote de instalação do agente para:

```bash
# Reconfigure the connected machine agent and set the proxy server.
bash ~/Install_linux_azcmagent.sh --proxy "{proxy-url}:{proxy-port}"
```

Para configurar o agente para parar de comunicar através de um servidor proxy, executar o seguinte comando para remover a configuração de procuração:

```bash
sudo azcmagent_proxy remove
```

## <a name="next-steps"></a>Passos seguintes

* As informações relativas à resolução de problemas podem ser encontradas no guia do [agente da máquina conectado de resolução de problemas.](troubleshoot-agent-onboard.md)

* Aprenda a gerir a sua máquina utilizando [a Azure Policy](../../governance/policy/overview.md), para coisas como [a configuração do hóspede](../../governance/policy/concepts/guest-configuration.md)VM , verificando se a máquina está a reportar ao espaço de trabalho esperado do Log Analytics, permitir a monitorização com o [Azure Monitor com VMs](../../azure-monitor/insights/vminsights-enable-policy.md), e muito mais.

* Saiba mais sobre o [agente Log Analytics.](../../azure-monitor/platform/log-analytics-agent.md) O agente Log Analytics para Windows e Linux é necessário quando pretende recolher dados de monitorização do sistema operativo e da carga de trabalho, geri-lo utilizando livros de aplicação da Automação ou funcionalidades como Update Management, ou utilizar outros serviços Azure como [o Azure Security Center](../../security-center/security-center-introduction.md).