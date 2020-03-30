---
title: Gerir recursos remotos no local utilizando funções PowerShell
description: Aprenda a configurar ligações híbridas em Relé Azure para ligar uma aplicação de função PowerShell aos recursos no local, que podem ser usados para gerir remotamente o recurso no local.
author: eamono
ms.topic: conceptual
ms.date: 9/5/2019
ms.author: eamono
ms.openlocfilehash: 36fc4c873dccfe9fa814bddccd829ed04207f095
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74226942"
---
# <a name="managing-hybrid-environments-with-powershell-in-azure-functions-and-app-service-hybrid-connections"></a>Gerir ambientes híbridos com PowerShell em Funções Azure e Conexões Híbridas do Serviço de Aplicações

A funcionalidade Ligações Híbridas do Serviço de Aplicações Azure permite o acesso a recursos noutras redes. Pode saber mais sobre esta capacidade na documentação [conexões híbridas.](../app-service/app-service-hybrid-connections.md) Este artigo descreve como usar esta capacidade para executar funções PowerShell que visam um servidor no local. Este servidor pode então ser utilizado para gerir todos os recursos no ambiente no local a partir de uma função Azure PowerShell.


## <a name="configure-an-on-premises-server-for-powershell-remoting"></a>Configure um servidor no local para remo powerShell

O seguinte script permite o remo powerShell, e cria uma nova regra de firewall e um ouvinte winRM https. Para efeitos de teste, é utilizado um certificado auto-assinado. Em ambiente de produção, recomendamos que use um certificado assinado.

```powershell
# For configuration of WinRM, see
# https://docs.microsoft.com/windows/win32/winrm/installation-and-configuration-for-windows-remote-management.

# Enable PowerShell remoting.
Enable-PSRemoting -Force

# Create firewall rule for WinRM. The default HTTPS port is 5986.
New-NetFirewallRule -Name "WinRM HTTPS" `
                    -DisplayName "WinRM HTTPS" `
                    -Enabled True `
                    -Profile "Any" `
                    -Action "Allow" `
                    -Direction "Inbound" `
                    -LocalPort 5986 `
                    -Protocol "TCP"

# Create new self-signed-certificate to be used by WinRM.
$Thumbprint = (New-SelfSignedCertificate -DnsName $env:COMPUTERNAME  -CertStoreLocation Cert:\LocalMachine\My).Thumbprint

# Create WinRM HTTPS listener.
$Cmd = "winrm create winrm/config/Listener?Address=*+Transport=HTTPS @{Hostname=""$env:COMPUTERNAME ""; CertificateThumbprint=""$Thumbprint""}"
cmd.exe /C $Cmd
```

## <a name="create-a-powershell-function-app-in-the-portal"></a>Criar uma aplicação de função PowerShell no portal

A funcionalidade de conexões híbridas do serviço de aplicações está disponível apenas em planos de preços Básicos, Standard e Isolados. Quando criar a aplicação de funções com o PowerShell, crie ou selecione um destes planos.

1. No [portal Azure, selecione](https://portal.azure.com) **+ Crie um recurso** no menu à esquerda e, em seguida, selecione a **aplicação Função**.

1. Para **o plano de hospedagem,** selecione o plano de serviço de **aplicações**e, em seguida, selecione **o plano/localização**do Serviço de Aplicações .

1. Selecione **Criar um novo**nome de plano de serviço de **aplicações,** escolha um **Local** numa [região](https://azure.microsoft.com/regions/) próxima ou perto de outros serviços a que aceda as suas funções e, em seguida, selecione **o nível**de Preços .

1. Escolha o plano Padrão S1 e, em seguida, selecione **Aplicar**.

1. Selecione **OK** para criar o plano e, em seguida, configure as definições restantes da **App de Função,** conforme especificado na tabela imediatamente após a seguinte imagem:

    ![App de função PowerShell Core](./media/functions-hybrid-powershell/create-function-powershell-app.png)  

    | Definição      | Valor sugerido  | Descrição                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Nome da aplicação** | Nome globalmente exclusivo | Nome que identifica a sua aplicação Function App nova. Os carateres válidos são `a-z`, `0-9` e `-`.  | 
    | **Assinatura** | A sua subscrição | A subscrição sob a qual esta nova aplicação de função é criada. |
    | **Grupo de Recursos** |  myResourceGroup | Nome do grupo de recursos novo no qual a aplicação Function App vai ser criada. Também pode usar o valor sugerido. |
    | **OS** | Os Preferidos | selecione o Windows. |
    | **Pilha de tempo de execução** | Linguagem preferencial | Escolha powershell Core. |
    | **Armazenamento** |  Nome globalmente exclusivo |  Crie uma conta de armazenamento para ser utilizada pela sua aplicação de funções. Os nomes da conta de armazenamento devem ser de 3 a 24 caracteres de comprimento e podem conter números e letras minúsculas apenas. Também pode utilizar uma conta já existente.
    | **Application Insights** | Predefinição | Cria um recurso Application Insights com o mesmo *nome app* na região mais próxima suportada. Ao expandir este cenário, pode alterar o **nome de novo recurso** ou escolher um **Local** diferente numa região de [geografia Azure](https://azure.microsoft.com/global-infrastructure/geographies/) onde pretende armazenar os seus dados. |

1. Depois de validadas as definições, selecione **Criar**.

1. Selecione o ícone **notificação** no canto superior direito do portal e aguarde a mensagem "Deployment succeeded".

1. Selecione **Ir para o recurso** para ver a sua nova aplicação de funções. Também pode selecionar **Pin para painel de instrumentos**. A fixação facilita o regresso a esta função de recurso da aplicação a partir do seu painel de instrumentos.

## <a name="create-a-hybrid-connection-for-the-function-app"></a>Criar uma ligação híbrida para a aplicação de funções

As ligações híbridas são configuradas a partir da secção de rede da aplicação de funções:

1. Selecione o separador de **funcionalidades** da Plataforma na aplicação de funções e, em seguida, selecione **Networking**. 
   ![Visão geral da aplicação para networking de plataformas](./media/functions-hybrid-powershell/app-overview-platform-networking.png)  
1. **Selecione Configurar os pontos finais das ligações híbridas**.
   ![Redes](./media/functions-hybrid-powershell/select-network-feature.png)  
1. **Selecione Adicionar ligação híbrida**.
   ![Ligação Híbrida](./media/functions-hybrid-powershell/hybrid-connection-overview.png)  
1. Introduza informações sobre a ligação híbrida, conforme mostrado logo após a seguinte imagem. Tem a opção de fazer com que a definição do **Endpoint Host** corresponda ao nome do anfitrião do servidor no local para facilitar a reposição do servidor mais tarde quando estiver a executar comandos remotos. A porta corresponde à porta de serviço de gestão remota do Windows que foi definida no servidor anteriormente.
  ![Adicionar conexão híbrida](./media/functions-hybrid-powershell/add-hybrid-connection.png)  

    Nome de **ligação híbrida**: ContosoHybridOnPremisesServer
    
    **Endpoint Host**: finanças1
    
    **Porta endpoint**: 5986
    
    **Espaço de nome servicebus**: Criar Novo
    
    **Localização**: Escolha um local disponível
    
    **Nome**: contosopowershellhybrid

5. Selecione **OK** para criar a ligação híbrida.

## <a name="download-and-install-the-hybrid-connection"></a>Descarregue e instale a ligação híbrida

1. Selecione o gestor de **ligação de download** para guardar o ficheiro .msi localmente no seu computador.
![Descarregue o instalador](./media/functions-hybrid-powershell/download-hybrid-connection-installer.png)  
1. Copie o ficheiro .msi do seu computador local para o servidor no local.
1. Executar o instalador Hybrid Connection Manager para instalar o serviço no servidor no local.
![Instalar conexão híbrida](./media/functions-hybrid-powershell/hybrid-installation.png)  
1. A partir do portal, abra a ligação híbrida e, em seguida, copie a cadeia de ligação do gateway à área de reparação.
![Copiar cadeia de ligação híbrida](./media/functions-hybrid-powershell/copy-hybrid-connection.png)  
1. Abra o Hybrid Connection Manager UI no servidor no local.
![Conexão Híbrida Aberta UI](./media/functions-hybrid-powershell/hybrid-connection-ui.png)  
1. Selecione o botão **Enter Manualmente** e cola a corda de ligação da área de reparação.
![Ligação à pasta](./media/functions-hybrid-powershell/enter-manual-connection.png)  
1. Reinicie o Gestor de Ligação Híbrida da PowerShell se não mostrar como ligado.
    ```powershell
    Restart-Service HybridConnectionManager
    ```

## <a name="create-an-app-setting-for-the-password-of-an-administrator-account"></a>Criar uma definição de aplicativo para a palavra-passe de uma conta de administrador

1. Selecione o separador **de funcionalidades** da Plataforma na aplicação de funções.
1. Em **Configurações Gerais,** selecione **Configuração**.
![Selecione configuração da plataforma](./media/functions-hybrid-powershell/select-configuration.png)  
1. Expandir **a nova definição** de aplicação para criar uma nova definição para a palavra-passe.
1. Nomeie a definição _ContosoUserPassword_e introduza a palavra-passe.
1. Selecione **OK** e, em seguida, guarde para armazenar a palavra-passe na aplicação de função.
![Adicione a definição de aplicativo para senha](./media/functions-hybrid-powershell/add-appsetting-password.png)  

## <a name="create-a-function-http-trigger-to-test"></a>Criar uma função http gatilho para testar

1. Crie uma nova função de gatilho HTTP a partir da aplicação de função.
![Criar novo gatilho HTTP](./media/functions-hybrid-powershell/create-http-trigger-function.png)  
1. Substitua o código PowerShell do modelo pelo seguinte código:

    ```powershell
    # Input bindings are passed in via param block.
    param($Request, $TriggerMetadata)
    
    # Write to the Azure Functions log stream.
    Write-Output "PowerShell HTTP trigger function processed a request."
    
    # Note that ContosoUserPassword is a function app setting, so I can access it as $env:ContosoUserPassword.
    $UserName = "ContosoUser"
    $securedPassword = ConvertTo-SecureString  $Env:ContosoUserPassword -AsPlainText -Force
    $Credential = [System.management.automation.pscredential]::new($UserName, $SecuredPassword)
    
    # This is the name of the hybrid connection Endpoint.
    $HybridEndpoint = "finance1"
    
    $Script = {
        Param(
            [Parameter(Mandatory=$True)]
            [String] $Service
        )
        Get-Service $Service
    }
    
    Write-Output "Scenario 1: Running command via Invoke-Command"
    Invoke-Command -ComputerName $HybridEndpoint `
                   -Credential $Credential `
                   -Port 5986 `
                   -UseSSL `
                   -ScriptBlock $Script `
                   -ArgumentList "*" `
                   -SessionOption (New-PSSessionOption -SkipCACheck)
    ```

3. Selecione **Guardar** e **correr** para testar a função.
![Testar a aplicação de função](./media/functions-hybrid-powershell/test-function-hybrid.png)  

## <a name="managing-other-systems-on-premises"></a>Gestão de outros sistemas no local

Pode utilizar o servidor ligado no local para se ligar a outros servidores e sistemas de gestão no ambiente local. Isto permite-lhe gerir as suas operações de datacenter a partir do Azure utilizando as suas funções PowerShell. O script seguinte regista uma sessão de configuração PowerShell que corre sob as credenciais fornecidas. Estas credenciais devem ser para um administrador nos servidores remotos. Em seguida, pode utilizar esta configuração para aceder a outros pontos finais no servidor local ou datacenter.

```powershell
# Input bindings are passed in via param block.
param($Request, $TriggerMetadata)

# Write to the Azure Functions log stream.
Write-Host "PowerShell HTTP trigger function processed a request."

# Note that ContosoUserPassword is a function app setting, so I can access it as $env:ContosoUserPassword.
$UserName = "ContosoUser"
$SecuredPassword = ConvertTo-SecureString  $Env:ContosoUserPassword -AsPlainText -Force
$Credential = [System.management.automation.pscredential]::new($UserName, $SecuredPassword)

# This is the name of the hybrid connection Endpoint.
$HybridEndpoint = "finance1"

# The remote server that will be connected to run remote PowerShell commands on
$RemoteServer = "finance2".

Write-Output "Use hybrid connection server as a jump box to connect to a remote machine"

# We are registering an endpoint that runs under credentials ($Credential) that has access to the remote server.
$SessionName = "HybridSession"
$ScriptCommand = {
    param (
        [Parameter(Mandatory=$True)]
        $SessionName)

    if (-not (Get-PSSessionConfiguration -Name $SessionName -ErrorAction SilentlyContinue))
    {
        Register-PSSessionConfiguration -Name $SessionName -RunAsCredential $Using:Credential
    }
}

Write-Output "Registering session on hybrid connection jumpbox"
Invoke-Command -ComputerName $HybridEndpoint `
               -Credential $Credential `
               -Port 5986 `
               -UseSSL `
               -ScriptBlock $ScriptCommand `
               -ArgumentList $SessionName `
               -SessionOption (New-PSSessionOption -SkipCACheck)

# Script to run on the jump box to run against the second machine.
$RemoteScriptCommand = {
    param (
        [Parameter(Mandatory=$True)]
        $ComputerName)
        # Write out the hostname of the hybrid connection server.
        hostname
        # Write out the hostname of the remote server.
        Invoke-Command -ComputerName $ComputerName -Credential $Using:Credential -ScriptBlock {hostname} `
                        -UseSSL -Port 5986 -SessionOption (New-PSSessionOption -SkipCACheck)
}

Write-Output "Running command against remote machine via jumpbox by connecting to the PowerShell configuration session"
Invoke-Command -ComputerName $HybridEndpoint `
               -Credential $Credential `
               -Port 5986 `
               -UseSSL `
               -ScriptBlock $RemoteScriptCommand `
               -ArgumentList $RemoteServer `
               -SessionOption (New-PSSessionOption -SkipCACheck) `
               -ConfigurationName $SessionName
```

Substitua as seguintes variáveis neste script com os valores aplicáveis do seu ambiente:
* $HybridEndpoint
* $RemoteServer

Nos dois cenários anteriores, pode ligar e gerir os seus ambientes no local utilizando o PowerShell em Funções Azure e Ligações Híbridas. Encorajamo-lo a aprender mais sobre [Conexões Híbridas](../app-service/app-service-hybrid-connections.md) e [PowerShell em funções.](./functions-reference-powershell.md)

Também pode utilizar [redes virtuais](./functions-create-vnet.md) Azure para se conectar ao seu ambiente no local através das Funções Azure.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"] 
> [Saiba mais sobre trabalhar com funções powerShell](functions-reference-powershell.md)
