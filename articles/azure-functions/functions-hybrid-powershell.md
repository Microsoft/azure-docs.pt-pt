---
title: Gerenciar recursos locais remotos usando o PowerShell Azure Functions
description: Saiba como configurar Conexões Híbridas na retransmissão do Azure para conectar um aplicativo de funções do PowerShell a recursos locais, que podem ser usados para gerenciar remotamente o recurso local.
author: eamono
manager: gailey777
ms.service: azure-functions
ms.topic: conceptual
ms.date: 9/5/2019
ms.author: eamono
ms.openlocfilehash: 0acbe0892be50e9e1747e5839101110d4adcebcf
ms.sourcegitcommit: a4b5d31b113f520fcd43624dd57be677d10fc1c0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70775419"
---
# <a name="managing-hybrid-environments-with-powershell-in-azure-functions-and-app-service-hybrid-connections"></a>Gerenciando ambientes híbridos com o PowerShell no Azure Functions e no serviço de aplicativo Conexões Híbridas

O serviço de Azure App Conexões Híbridas permite o acesso a recursos em outras redes. Você pode saber mais sobre esse recurso na documentação do [conexões híbridas](../app-service/app-service-hybrid-connections.md) . As informações a seguir descrevem como usar esse recurso para executar as funções do PowerShell direcionadas a um servidor local. Esse servidor pode então ser usado para gerenciar todos os recursos no ambiente local por meio de uma função Azure PowerShell.


## <a name="configure-an-on-premises-server-for-powershell-remoting"></a>Configurar um servidor local para comunicação remota do PowerShell

O script abaixo habilita a comunicação remota do PowerShell, cria uma nova regra de firewall e um ouvinte de HTTPS do WinRM. Para fins de teste, é usado um certificado autoassinado. É recomendável usar um certificado assinado para produção.

```powershell
# For configuration of WinRM, please see
# https://docs.microsoft.com/windows/win32/winrm/installation-and-configuration-for-windows-remote-management

# Enable PowerShell remoting
Enable-PSRemoting -Force

# Create firewall rule for WinRM. The default HTTPS port is 5986 
New-NetFirewallRule -Name "WinRM HTTPS" `
                    -DisplayName "WinRM HTTPS" `
                    -Enabled True `
                    -Profile "Any" `
                    -Action "Allow" `
                    -Direction "Inbound" `
                    -LocalPort 5986 `
                    -Protocol "TCP"

# Create new self-signed-certificate to be used by WinRM
$Thumbprint = (New-SelfSignedCertificate -DnsName $env:COMPUTERNAME  -CertStoreLocation Cert:\LocalMachine\My).Thumbprint

# Create WinRM HTTPS listener
$Cmd = "winrm create winrm/config/Listener?Address=*+Transport=HTTPS @{Hostname=""$env:COMPUTERNAME ""; CertificateThumbprint=""$Thumbprint""}"
cmd.exe /C $Cmd
```

## <a name="create-a-powershell-function-app-in-the-portal"></a>Criar um aplicativo de funções do PowerShell no portal

Os Conexões Híbridas do serviço de aplicativo estão disponíveis apenas nos planos de preços básico, padrão e isolado. Ao criar o aplicativo de funções com o PowerShell, crie ou selecione um desses planos.

1. Aceda ao [Portal do Azure](https://portal.azure.com).

1. Selecione **+ criar um recurso** no lado esquerdo e, em seguida, escolha **aplicativo de funções**.

1. Para o **plano de hospedagem**, escolha plano do serviço de **aplicativo**e, em seguida, selecione **plano/local do serviço de aplicativo**.

1. Selecione **criar novo**, digite um nome de **plano do serviço de aplicativo** , escolha um **local** em uma [região](https://azure.microsoft.com/regions/) perto de você ou perto de outros serviços que suas funções acessam e selecione **tipo de preço**.

1. Escolha o plano padrão S1 e, em seguida, selecione **aplicar**.

1. Selecione **OK** para criar o plano e, em seguida, use as configurações do aplicativo de funções restantes, conforme especificado na tabela abaixo da imagem. 

    ![Aplicativo de funções do PowerShell Core](./media/functions-hybrid-powershell/create-function-powershell-app.png)  

    | Definição      | Valor sugerido  | Descrição                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Nome da aplicação** | Nome globalmente exclusivo | Nome que identifica a sua aplicação Function App nova. Os carateres válidos são `a-z`, `0-9` e `-`.  | 
    | **Subscrição** | A sua subscrição | A subscrição sob a qual esta nova aplicação de função é criada. |
    | **Grupo de Recursos** |  myResourceGroup | Nome do grupo de recursos novo no qual a aplicação Function App vai ser criada. Você também pode usar o valor sugerido. |
    | **OS** | Sistema operacional preferencial | Selecione Windows. |
    | **Pilha de tempo de execução** | Linguagem preferencial | Escolha PowerShell Core. |
    | **Armazenamento** |  Nome globalmente exclusivo |  Crie uma conta de armazenamento para ser utilizada pela sua aplicação de funções. Os nomes das contas do Storage devem ter entre 3 e 24 carateres de comprimento e apenas podem conter números e letras minúsculas. Também pode utilizar uma conta já existente.
    | **Application Insights** | Predefinição | Cria um recurso Application Insights do mesmo *nome de aplicativo* na região com suporte mais próximo. Ao expandir essa configuração, você pode alterar o **nome do novo recurso** ou escolher um **local** diferente em uma [Geografia do Azure](https://azure.microsoft.com/global-infrastructure/geographies/) onde você deseja armazenar seus dados. |

1. Depois que as configurações forem validadas, selecione **criar**.

1. Selecione o ícone de Notificação no canto superior direito do portal e procure a mensagem **Implementação concluída com êxito**.

1. Selecione **Ir para o recurso** para ver a sua nova aplicação de funções. Você também pode selecionar **fixar no painel**. A fixação torna mais fácil retornar a esse recurso do aplicativo de funções do seu painel.

## <a name="create-a-hybrid-connection-for-the-function-app"></a>Criar uma conexão híbrida para o aplicativo de funções

As conexões híbridas são configuradas na seção rede do aplicativo de funções.

1. Selecione a guia plataforma do aplicativo de funções e selecione rede.
![Visão geral do aplicativo para rede de plataforma](./media/functions-hybrid-powershell/app-overview-platform-networking.png)  
1. Selecione configurar seus pontos de extremidade de conexões híbridas.
![Redes](./media/functions-hybrid-powershell/select-network-feature.png)  
1. Selecione Adicionar conexão híbrida.
![Conexão híbrida](./media/functions-hybrid-powershell/hybrid-connection-overview.png)  
1. Insira informações sobre a conexão híbrida, conforme mostrado abaixo. O host do ponto de extremidade pode, opcionalmente, corresponder ao nome do host do servidor local para facilitar a memorização do servidor posteriormente durante a execução de comandos remotos. A porta corresponde à porta padrão do serviço de gerenciamento remoto do Windows definida no servidor anteriormente.
![Adicionar conexão híbrida](./media/functions-hybrid-powershell/add-hybrid-connection.png)  

    **Nome da conexão híbrida** ContosoHybridOnPremisesServer
    
    **Host do ponto de extremidade** finance1
    
    **Porta do ponto de extremidade** 5986
    
    **Namespace do ServiceBus** Criar novo
    
    **Local** do Escolha um local disponível
    
    **Nome** contosopowershellhybrid

5. Clique em OK para criar a conexão híbrida

## <a name="download-and-install-the-hybrid-connection"></a>Baixar e instalar a conexão híbrida

1. Selecione o ícone baixar Gerenciador de conexões para salvar o arquivo. msi localmente no seu computador.
![Baixar instalador](./media/functions-hybrid-powershell/download-hybrid-connection-installer.png)  
1. Copie o. msi do seu computador local para o servidor no local.
1. Execute o instalador de conexão híbrida para instalar o serviço no servidor local.
![Instalar conexão híbrida](./media/functions-hybrid-powershell/hybrid-installation.png)  
1. No portal, abra a conexão híbrida e copie a cadeia de conexão do gateway para a área de transferência.
![Copiar cadeia de conexão híbrida](./media/functions-hybrid-powershell/copy-hybrid-connection.png)  
1. Abra a interface do usuário do Gerenciador de Conexões Híbridas no servidor local.
![Abrir interface do usuário de conexão híbrida](./media/functions-hybrid-powershell/hybrid-connection-ui.png)  
1. Selecione o botão "inserir manualmente" e cole a cadeia de conexão da área de transferência.
![Colar conexão](./media/functions-hybrid-powershell/enter-manual-connection.png)  
1. Reinicie o Gerenciador de conexões híbridas do PowerShell se ele não mostrar conectado.
```powershell
Restart-Service HybridConnectionManager
```

## <a name="create-an-app-setting-for-the-password-of-an-administrator-account"></a>Criar uma configuração de aplicativo para a senha de uma conta de administrador

1. Selecione a guia plataforma no aplicativo de funções
1. Selecione a configuração na seção ![configurações gerais selecionar configuração de plataforma](./media/functions-hybrid-powershell/select-configuration.png)  
1. Selecione nova configuração de aplicativo para criar uma nova configuração para a senha
1. Nomeie a configuração ContosoUserPassword e insira a senha
1. Selecione OK e, em seguida, salvar para armazenar a senha no ![aplicativo de funções adicionar configuração de aplicativo para senha](./media/functions-hybrid-powershell/add-appsetting-password.png)  

## <a name="create-a-function-http-trigger-to-test"></a>Criar um gatilho http de função para teste

1. Criar uma nova função de gatilho http no aplicativo ![de funções criar novo gatilho http](./media/functions-hybrid-powershell/create-http-trigger-function.png)  
1. Substitua o código do PowerShell do modelo pelo código a seguir:

    ```powershell
    # Input bindings are passed in via param block.
    param($Request, $TriggerMetadata)
    
    # Write to the Azure Functions log stream.
    Write-Output "PowerShell HTTP trigger function processed a request."
    
    # Note that ContosoUserPassword is a function app setting, so I can access it as $env:ContosoUserPassword
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

3. Clique em salvar e executar para testar o ![aplicativo função de teste de função](./media/functions-hybrid-powershell/test-function-hybrid.png)  

## <a name="managing-other-systems-on-premises"></a>Gerenciando outros sistemas locais

Você pode usar o servidor local conectado para se conectar a outros servidores e sistemas de gerenciamento no ambiente local. Isso permite que você gerencie suas operações de data center do Azure usando suas funções do PowerShell. O script a seguir registra uma sessão de configuração do PowerShell que é executada nas credenciais fornecidas. Essas credenciais precisam ser um administrador nos servidores remotos. Você pode usar essa configuração para acessar outros pontos de extremidade no servidor local ou data center.

```powershell
# Input bindings are passed in via param block.
param($Request, $TriggerMetadata)

# Write to the Azure Functions log stream.
Write-Host "PowerShell HTTP trigger function processed a request."

# Note that ContosoUserPassword is a function app setting, so I can access it as $env:ContosoUserPassword
$UserName = "ContosoUser"
$SecuredPassword = ConvertTo-SecureString  $Env:ContosoUserPassword -AsPlainText -Force
$Credential = [System.management.automation.pscredential]::new($UserName, $SecuredPassword)

# This is the name of the hybrid connection Endpoint.
$HybridEndpoint = "finance1"

# The remote server that will be conected to run remote PowerShell commands on
$RemoteServer = "finance2"

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

# Script to run on the jump box to run against the second machine
$RemoteScriptCommand = {
    param (
        [Parameter(Mandatory=$True)]
        $ComputerName)
        # Write out the hostname of the hybrid connection server
        hostname
        # Write out the hostname of the remote server
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

Substitua essas variáveis no script acima pelos valores que você tem em seu ambiente:
* $HybridEndpoint
* $RemoteServer

Os dois cenários acima permitem que você conecte e gerencie seus ambientes locais usando o PowerShell em Azure Functions e Conexões Híbridas. Você pode aprender informações adicionais sobre o [conexões híbridas](../app-service/app-service-hybrid-connections.md) ou o [PowerShell em funções](./functions-reference-powershell.md) na documentação do.

Você também pode usar [redes virtuais](./functions-create-vnet.md) do Azure para se conectar ao seu ambiente local usando Azure functions.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"] 
> [Saiba mais sobre como trabalhar com funções do PowerShell](functions-reference-powershell.md)
