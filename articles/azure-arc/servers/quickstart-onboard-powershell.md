---
title: Início rápido – conectar computadores ao Azure usando o Azure ARC para servidores-PowerShell
description: Neste início rápido, você aprende a conectar computadores ao Azure usando o Azure ARC para servidores usando o PowerShell
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
author: bobbytreed
ms.author: robreed
keywords: automação do Azure, DSC, PowerShell, configuração de estado desejado, gerenciamento de atualizações, controle de alterações, inventário, runbooks, Python, gráfico, híbrido, integrado
ms.date: 11/04/2019
ms.custom: mvc
ms.topic: quickstart
ms.openlocfilehash: ddade9472517d080d01b04c853db9dd1848fe0f3
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73668476"
---
# <a name="quickstart-connect-machines-to-azure-using-azure-arc-for-servers---powershell"></a>Início rápido: conectar computadores ao Azure usando o Azure ARC para servidores-PowerShell

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Revise os clientes com suporte e a configuração de rede necessária na [visão geral do arco do Azure para servidores](overview.md).

## <a name="create-a-service-principal-for-onboarding-at-scale"></a>Criar uma entidade de serviço para integração em escala

Uma entidade de serviço é uma identidade de gerenciamento limitada especial que recebe apenas a permissão mínima necessária para conectar computadores ao Azure. Isso é mais seguro do que usar uma conta mais potente como uma Administrador de Locatários. A entidade de serviço é usada somente durante a integração. Você pode excluir com segurança a entidade de serviço depois de conectar os servidores desejados.

> [!NOTE]
> Esta etapa é recomendada, mas não é necessária.

### <a name="steps-to-create-the-service-principal"></a>Etapas para criar a entidade de serviço

Neste exemplo, usaremos [Azure PowerShell](/powershell/azure/install-az-ps) para criar um SPN (nome da entidade de serviço). Como alternativa, você pode seguir as etapas listadas em [criar uma entidade de serviço usando o portal do Azure](../../active-directory/develop/howto-create-service-principal-portal.md) para esta tarefa.

A função `Azure Connected Machine Onboarding` contém apenas as permissões necessárias para a integração. Você pode definir a permissão de um SPN para permitir que seu escopo cubra um grupo de recursos ou uma assinatura.

Você deve armazenar a saída do cmdlet [`New-AzADServicePrincipal`](/powershell/module/az.resources/new-azadserviceprincipal) ou não será possível recuperar a senha para usar em uma etapa posterior.

```azurepowershell-interactive
$sp = New-AzADServicePrincipal -DisplayName "Arc-for-servers" -Role "Azure Connected Machine Onboarding"
$sp
```

```output
Secret                : System.Security.SecureString
ServicePrincipalNames : {ad9bcd79-be9c-45ab-abd8-80ca1654a7d1, https://Arc-for-servers}
ApplicationId         : ad9bcd79-be9c-45ab-abd8-80ca1654a7d1
ObjectType            : ServicePrincipal
DisplayName           : Hybrid-RP
Id                    : 5be92c87-01c4-42f5-bade-c1c10af87758
Type                  :
```

Agora, recupere a senha usando o PowerShell.

```azurepowershell-interactive
$credential = New-Object pscredential -ArgumentList "temp", $sp.Secret
$credential.GetNetworkCredential().password
```

Na saída, copie a **senha** e a **ApplicationId** (da etapa anterior) e armazene-as para mais tarde em um local seguro, como o repositório de segredo para a ferramenta de configuração do servidor. Se você esquecer ou perder sua senha de SPN, poderá redefini-la usando o cmdlet [`New-AzADSpCredential`](/powershell/module/azurerm.resources/new-azurermadspcredential) .

No script de integração do agente de instalação:

* A propriedade **ApplicationId** é usada para o parâmetro `--service-principal-id` usado no agente de instalação
* A propriedade **password** é usada para o parâmetro `--service-principal-secret` no agente de instalação.

## <a name="manually-install-the-agent-and-connect-to-azure"></a>Instalar manualmente o agente e conectar-se ao Azure

O guia a seguir permite que você conecte um computador ao Azure fazendo logon no computador e executando as etapas. Você também pode conectar computadores ao Azure [por meio do portal](quickstart-onboard-portal.md).

### <a name="download-and-install-the-agent"></a>Baixar e instalar o agente

A instalação do pacote do agente requer acesso de administrador local ou raiz no servidor de destino, mas sem acesso do Azure.

#### <a name="linux"></a>Linux

Para servidores **Linux** , o agente é distribuído por meio do [repositório de pacotes da Microsoft](https://packages.microsoft.com) usando o formato de pacote preferencial para a distribuição (. RPM ou. DEB).

> [!NOTE]
> Durante a visualização pública, apenas um pacote foi liberado, o que é adequado para o Ubuntu 16, 4 ou 18, 4.

<!-- What about this aks? -->
A opção mais simples é registrar o repositório de pacotes e, em seguida, instalar o pacote usando o Gerenciador de pacotes da distribuição.
O script bash localizado em [https://aka.ms/azcmagent](https://aka.ms/azcmagent) executa as seguintes ações:

1. Configura o computador host para download em `packages.microsoft.com`.
2. Instala o pacote do provedor de recursos híbrido.
3. Opcionalmente, o configura o agente para a operação de proxy, se você especificar `--proxy`.

O script também contém verificações de distribuições com suporte e sem suporte, bem como a detecção de permissões necessárias para a instalação.

O exemplo a seguir baixa o agente e o instala, sem nenhuma das verificações condicionais.

```bash
# Download the installation package
wget https://aka.ms/azcmagent -O ~/Install_linux_azcmagent.sh

# Install the connected machine agent. Omit the '--proxy "{proxy-url}"' parameters if proxy is not needed
bash ~/Install_linux_azcmagent.sh--proxy "{proxy-url}"
```

> [!NOTE]
> Se preferir não fazer referência ao repositório de pacotes da Microsoft, você poderá copiar o arquivo de pacote desse local para o repositório interno.

#### <a name="windows"></a>Windows

Para o **Windows**, o agente é empacotado em um arquivo de Windows Installer (`.MSI`) e pode ser baixado de [https://aka.ms/AzureConnectedMachineAgent](https://aka.ms/AzureConnectedMachineAgent), hospedado em [https://download.microsoft.com](https://download.microsoft.com).

```powershell
# Download the package
Invoke-WebRequest -Uri https://aka.ms/AzureConnectedMachineAgent -OutFile AzureConnectedMachineAgent.msi

# Install the package
msiexec /i AzureConnectedMachineAgent.msi /l*v installationlog.txt /qn | Out-String
```

> [!NOTE]
> No Linux, executar o script de instalação novamente atualizará automaticamente para a versão mais recente. No Windows, você deve desinstalar o "agente de máquina conectado do Azure" antes de executar o instalador novamente para atualizar.

### <a name="connecting-to-azure"></a>Conectando-se ao Azure

Uma vez instalado, você pode gerenciar e configurar o agente usando uma ferramenta de linha de comando chamada `azcmagent.exe`. O agente está localizado em `/opt/azcmagent/bin` no Linux e `$env:programfiles\AzureConnectedMachineAgent` no Windows.

No Windows, abra o PowerShell como administrador em um nó de destino e execute:

```powershell
& "$env:ProgramFiles\AzureConnectedMachineAgent\azcmagent.exe" connect `
  --service-principal-id "{your-spn-appid}" `
  --service-principal-secret "{your-spn-password}" `
  --resource-group "{your-resource-group-name}" `
  --tenant-id "{your-tenant-id}" `
  --location "{location-of-your-resource-group}" `
  --subscription-id "{your-subscription-id}"
```

No Linux, abra um shell e execute

<!-- Same command for linux?-->
```bash
azcmagent connect \
  --service-principal-id "{your-spn-appid}" \
  --service-principal-secret "{your-spn-password}" \
  --resource-group "{your-resource-group-name}" \
  --tenant-id "{your-tenant-id}" \
  --location "{location-of-your-resource-group}" \
  --subscription-id "{your-subscription-id}"
```

Parâmetros:

* `tenant-id`: o GUID do locatário. Você pode encontrá-lo em portal do Azure selecionando **Azure active directory** -> **Propriedades** -> **ID do diretório**.
* `subscription-id`: o GUID da assinatura, no Azure, onde você deseja conectar seu computador.
* `resource-group`: o grupo de recursos onde você deseja que seu computador seja conectado.
* `location`: consulte [regiões e locais do Azure](https://azure.microsoft.com/global-infrastructure/regions/). Esse local pode ser o mesmo, ou diferente, como o local do grupo de recursos. Para a visualização pública, há suporte para o serviço em **WestUS2** e **Europa Ocidental**.
* `resource-name`: (*opcional*) usado para a representação de recursos do Azure do seu computador local. Se você não especificar esse valor, o nome do host do computador será usado.

Você pode encontrar mais informações sobre a ferramenta ' azcmagent ' na [referência do azcmagent](azcmagent-reference.md).
<!-- Isn't this still needed to view machines? -->

Após a conclusão bem-sucedida, o computador estará conectado ao Azure. Você pode exibir seu computador na portal do Azure visitando [https://aka.ms/hybridmachineportal](https://aka.ms/hybridmachineportal).

![Integração bem-sucedida](./media/quickstart-onboard/arc-for-servers-successful-onboard.png)

### <a name="proxy-server-configuration"></a>Configuração do servidor proxy

#### <a name="linux"></a>Linux

<!-- New proxy name? -->

Para o **Linux**, se o servidor exigir um servidor proxy, você poderá:

* Execute o script `install_linux_hybrid_agent.sh` na seção [instalar o agente](#download-and-install-the-agent) acima, com `--proxy`.
* Se você já tiver instalado o agente, execute o comando `/opt/azcmagent/bin/hybridrp_proxy add https://{proxy-url}:{proxy-port}`, que configura o proxy e reinicia o agente.

#### <a name="windows"></a>Windows

Para o **Windows**, se o servidor exigir o servidor proxy para acessar recursos da Internet, execute o comando a seguir para definir a variável de ambiente do servidor proxy. Isso permite que o agente use o servidor proxy para acesso à Internet.

```powershell
# If a proxy server is needed, execute these commands with actual proxy URL
[Environment]::SetEnvironmentVariable("https_proxy", "{https:\\proxy-url:proxyport}", "Machine")
$env:https_proxy = [System.Environment]::GetEnvironmentVariable("https_proxy","Machine")
# The agent service needs to be restarted after the proxy environment variable is set in order for the changes to take effect.
Restart-Service -Name himds
```

> [!NOTE]
> Não há suporte para proxies autenticados para visualização pública.

## <a name="clean-up"></a>Limpeza

Para desconectar um computador do arco do Azure para servidores, você precisa executar duas etapas.

1. Selecione o computador no [portal](https://aka.ms/hybridmachineportal), clique nas reticências (`...`) e selecione **excluir**.
1. Desinstale o agente do computador.

   No Windows, você pode usar o painel de controle "aplicativos & recursos" para desinstalar o agente.
  
  ![Recursos de & de aplicativos](./media/quickstart-onboard/apps-and-features.png)

   Se você quiser criar um script para a desinstalação, poderá usar o exemplo a seguir, que recupera o **PackageID** e desinstala o agente usando `msiexec /X`.

   Procure na chave do registro `HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\Uninstall` e localize a **PackageID**. Em seguida, você pode desinstalar o agente usando `msiexec`.

   O exemplo a seguir demonstra a desinstalação do agente.

   ```powershell
   Get-ChildItem -Path HKLM:\Software\Microsoft\Windows\CurrentVersion\Uninstall | `
   Get-ItemProperty | `
   Where-Object {$_.DisplayName -eq "Azure Connected Machine Agent"} | `
   ForEach-Object {MsiExec.exe /Quiet /X "$($_.PsChildName)"}
   ```

   No Linux, execute o comando a seguir para desinstalar o agente.

   ```bash
   sudo apt purge hybridagent
   ```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Atribuir uma política a computadores conectados](../../governance/policy/assign-policy-portal.md)
