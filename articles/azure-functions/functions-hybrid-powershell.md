---
title: Gerir recursos remotos no local utilizando funções PowerShell
description: Aprenda a configurar ligações híbridas em Relé Azure para ligar uma aplicação de função PowerShell aos recursos no local, que podem ser usados para gerir remotamente o recurso no local.
author: eamono
ms.topic: conceptual
ms.date: 04/26/2020
ms.author: eamono
ms.openlocfilehash: 6034d1327d263eda49881af5eedf94ae06495128
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/12/2020
ms.locfileid: "83122277"
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

1. A partir do menu do portal do Azure ou a partir da **Home page**, selecione **Criar um recurso**.

1. Na **nova** página, **selecione Compute**  >  **Function App**.

1. Na página **Basics,** utilize as definições da aplicação de função conforme especificado na tabela seguinte.

    | Definição      | Valor sugerido  | Descrição |
    | ------------ | ---------------- | ----------- |
    | **Subscrição** | A sua subscrição | A subscrição sob a qual esta nova aplicação de função é criada. |
    | **[Grupo de Recursos](../azure-resource-manager/management/overview.md)** |  *myResourceGroup* | Nome do grupo de recursos novo no qual a aplicação Function App vai ser criada. |
    | **Nome da app de função** | Nome globalmente exclusivo | Nome que identifica a sua aplicação Function App nova. Os caracteres válidos são `a-z` (caso insensível), `0-9` e `-` .  |
    |**Publicar**| Código | Opção para publicar ficheiros de código ou um contentor de Docker. |
    | **Pilha de tempo de execução** | Linguagem preferencial | Escolha powershell Core. |
    |**Versão**| Número da versão | Escolha a versão do seu tempo de execução instalado.  |
    |**Região**| Região preferida | Escolha uma [região](https://azure.microsoft.com/regions/) perto de si ou de outros serviços aos quais as suas funções acedem. |

    :::image type="content" source="./media/functions-hybrid-powershell/function-app-create-basics.png" alt-text="Criar uma aplicação de função - Básicos." border="true":::

1. Selecione **Seguinte : Hospedagem**. Na página **de Hospedagem,** introduza as seguintes definições.

    | Definição      | Valor sugerido  | Descrição |
    | ------------ | ---------------- | ----------- |
    | **[Conta de armazenamento](../storage/common/storage-account-create.md)** |  Nome globalmente exclusivo |  Crie uma conta de armazenamento para ser utilizada pela sua aplicação de funções. Os nomes da conta de armazenamento devem ter entre 3 e 24 caracteres de comprimento e podem conter números e letras minúsculas apenas. Também pode utilizar uma conta existente, que deve satisfazer os requisitos da conta de [armazenamento.](../azure-functions/functions-scale.md#storage-account-requirements) |
    |**Sistema operativo**| Sistema operativo preferido | Um sistema operativo é pré-selecionado para si com base na sua seleção de pilhas de tempo de funcionamento, mas pode alterar a definição se necessário. |
    | **[Tipo de plano](../azure-functions/functions-scale.md)** | **Plano de serviço de aplicativos** | Escolha **o plano**de serviço da App . Quando executa num plano do Serviço de Aplicações, tem de gerir o [dimensionamento da sua aplicação de funções](../azure-functions/functions-scale.md).  |

    :::image type="content" source="./media/functions-hybrid-powershell/function-app-create-hosting.png" alt-text="Crie uma aplicação de função - Hospedagem." border="true":::

1. Selecione **Seguinte : Monitorização**. Na página **de Monitorização,** introduza as seguintes definições.

    | Definição      | Valor sugerido  | Descrição |
    | ------------ | ---------------- | ----------- |
    | **[Application Insights](../azure-functions/functions-monitoring.md)** | Predefinição | Cria um recurso Application Insights com o mesmo *nome app* na região mais próxima suportada. Ao expandir esta definição ou selecionar **Criar novos,** pode alterar o nome 'Insights de Aplicação' ou escolher uma região diferente numa [geografia Azure](https://azure.microsoft.com/global-infrastructure/geographies/) onde pretende armazenar os seus dados. |

    :::image type="content" source="./media/functions-hybrid-powershell/function-app-create-monitoring.png" alt-text="Criar uma aplicação de função - Monitorização." border="true":::

1. Selecione **Review + crie** para rever as seleções de configuração da aplicação.

1. Na página **Review + criar,** rever as suas definições e, em seguida, selecionar **Criar** para fornecer e implementar a aplicação de funções.

1. Selecione o ícone **notificações** no canto superior direito do portal e observe a mensagem bem sucedida da **Implementação.**

1. Selecione **Ir para o recurso** para ver a sua nova aplicação de funções. Também pode selecionar **Pin para painel de instrumentos**. A fixação facilita o regresso a esta função de recurso da aplicação a partir do seu painel de instrumentos.

## <a name="create-a-hybrid-connection-for-the-function-app"></a>Criar uma ligação híbrida para a aplicação de funções

As ligações híbridas são configuradas a partir da secção de rede da aplicação de funções:

1. Em **Definições** na aplicação de funções que acabou de criar, selecione **Networking**. 
1. **Selecione Configurar os pontos finais das ligações híbridas**.
   
    :::image type="content" source="./media/functions-hybrid-powershell/configure-hybrid-connection-endpoint.png" alt-text="Configure os pontos finais de ligação híbrido." border="true":::

1. **Selecione Adicionar ligação híbrida**.
   
    :::image type="content" source="./media/functions-hybrid-powershell/hybrid-connection-overview.png" alt-text="Adicione uma ligação híbrida." border="true":::

1. Introduza informações sobre a ligação híbrida, conforme mostrado logo após a seguinte imagem. Tem a opção de fazer com que a definição do **Endpoint Host** corresponda ao nome do anfitrião do servidor no local para facilitar a reposição do servidor mais tarde quando estiver a executar comandos remotos. A porta corresponde à porta de serviço de gestão remota do Windows que foi definida no servidor anteriormente.
  
      :::image type="content" source="./media/functions-hybrid-powershell/add-hybrid-connection.png" alt-text="Adicione a ligação híbrida." border="true":::

    | Definição      | Valor sugerido  |
    | ------------ | ---------------- |
    | **Nome de ligação híbrida** | ContosoHybridOnPremisesServer |
    | **Anfitrião endpoint** | finanças1 |
    | **Porto endpoint** | 5986 |
    | **Espaço de nome servicebus** | Criar Novo |
    | **Localização** | Escolha um local disponível |
    | **Nome** | contosopowershellhybrid | 

1. Selecione **OK** para criar a ligação híbrida.

## <a name="download-and-install-the-hybrid-connection"></a>Descarregue e instale a ligação híbrida

1. Selecione o gestor de **ligação de download** para guardar o ficheiro *.msi* localmente no seu computador.

    :::image type="content" source="./media/functions-hybrid-powershell/download-hybrid-connection-installer.png" alt-text="Descarregue o instalador." border="true":::

1. Copie o ficheiro *.msi* do seu computador local para o servidor no local.
1. Executar o instalador Hybrid Connection Manager para instalar o serviço no servidor no local.

    :::image type="content" source="./media/functions-hybrid-powershell/hybrid-installation.png" alt-text="Instale a ligação híbrida." border="true":::

1. A partir do portal, abra a ligação híbrida e, em seguida, copie a cadeia de ligação do gateway à área de reparação.

    :::image type="content" source="./media/functions-hybrid-powershell/copy-hybrid-connection.png" alt-text="Copie a cadeia de ligação híbrida." border="true":::

1. Abra o Hybrid Connection Manager UI no servidor no local.

    :::image type="content" source="./media/functions-hybrid-powershell/hybrid-connection-ui.png" alt-text="Abra a UI de Ligação Híbrida." border="true":::

1. Selecione **Introduzir Manualmente** e colar a cadeia de ligação da área de reparação.

    :::image type="content" source="./media/functions-hybrid-powershell/enter-manual-connection.png" alt-text="Cola a ligação híbrida." border="true":::

1. Reinicie o Gestor de Ligação Híbrida da PowerShell se não mostrar como ligado.
    ```powershell
    Restart-Service HybridConnectionManager
    ```

## <a name="create-an-app-setting-for-the-password-of-an-administrator-account"></a>Criar uma definição de aplicativo para a palavra-passe de uma conta de administrador

1. Em **Definições** para a sua aplicação de funções, **selecione Configuração**. 
1. Selecione **+ Nova definição de aplicação**.

    :::image type="content" source="./media/functions-hybrid-powershell/select-configuration.png" alt-text="Configure uma palavra-passe para a conta de administrador." border="true":::

1. Nomeie a definição **ContosoUserPassword**e introduza a palavra-passe. Selecione **OK**.
1. Selecione **Guardar** para armazenar a palavra-passe na aplicação de funções.

    :::image type="content" source="./media/functions-hybrid-powershell/save-administrator-password.png" alt-text="Guarde a palavra-passe para a conta do administrador." border="true":::

## <a name="create-a-function-http-trigger"></a>Criar um gatilho HTTP função

1. Na aplicação de funções, selecione **Funções,** e depois selecione **+ Adicionar**.

    :::image type="content" source="./media/functions-hybrid-powershell/create-http-trigger-function.png" alt-text="Crie um novo gatilho HTTP." border="true":::

1. Selecione o modelo de **gatilho HTTP.**

    :::image type="content" source="./media/functions-hybrid-powershell/select-http-trigger-template.png" alt-text="Selecione o modelo de gatilho HTTP." border="true":::

1. Nomeie a nova função e selecione **Criar Função**.

    :::image type="content" source="./media/functions-hybrid-powershell/create-new-http-function.png" alt-text="Nomeie e crie a nova função de gatilho HTTP." border="true":::

## <a name="test-the-function"></a>Testar a função

1. Na nova função, selecione **Código + Teste**. Substitua o código PowerShell do modelo pelo seguinte código:

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

1. Selecione **Guardar**.

    :::image type="content" source="./media/functions-hybrid-powershell/save-http-function.png" alt-text="Altere o código PowerShell e guarde a função de gatilho HTTP." border="true":::

 1. Selecione **Teste**e, em seguida, selecione **Executar** para testar a função. Reveja os registos para verificar se o teste foi bem sucedido.

     :::image type="content" source="./media/functions-hybrid-powershell/test-function-hybrid.png" alt-text="Função de gatilho de teste HTTP." border="true":::

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
