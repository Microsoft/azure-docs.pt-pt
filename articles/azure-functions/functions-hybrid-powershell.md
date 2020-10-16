---
title: Gerir recursos remotos no local utilizando funções PowerShell
description: Aprenda a configurar ligações híbridas no Azure Relay para ligar uma aplicação de função PowerShell a recursos no local, que pode ser usada para gerir remotamente o recurso no local.
author: eamono
ms.topic: conceptual
ms.date: 04/26/2020
ms.author: eamono
ms.openlocfilehash: 6034d1327d263eda49881af5eedf94ae06495128
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "83122277"
---
# <a name="managing-hybrid-environments-with-powershell-in-azure-functions-and-app-service-hybrid-connections"></a>Gerir ambientes híbridos com PowerShell em Funções Azure e Conexões Híbridas de Serviço de Aplicações

A funcionalidade Azure App Service Hybrid Connections permite o acesso a recursos noutras redes. Pode saber mais sobre esta capacidade na documentação [hybrid Connections.](../app-service/app-service-hybrid-connections.md) Este artigo descreve como usar esta capacidade para executar funções PowerShell que visam um servidor no local. Este servidor pode então ser utilizado para gerir todos os recursos no ambiente no local a partir de uma função Azure PowerShell.


## <a name="configure-an-on-premises-server-for-powershell-remoting"></a>Configurar um servidor no local para a remoagem powerShell

O seguinte script permite a remoting powerShell, e cria uma nova regra de firewall e um ouvinte https WinRM. Para efeitos de teste, é utilizado um certificado auto-assinado. Num ambiente de produção, recomendamos que utilize um certificado assinado.

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

A funcionalidade de Conexões Híbridas do Serviço de Aplicações está disponível apenas em planos básicos, standard e isolados. Quando criar a aplicação de função com o PowerShell, crie ou selecione um destes planos.

1. A partir do menu do portal do Azure ou a partir da **Home page**, selecione **Criar um recurso**.

1. Na **nova** página, selecione App de Função **computacional**  >  **Function App**.

1. Na página **Basics,** utilize as definições da aplicação de função conforme especificado na tabela seguinte.

    | Definição      | Valor sugerido  | Descrição |
    | ------------ | ---------------- | ----------- |
    | **Subscrição** | A sua subscrição | A subscrição no âmbito da qual esta nova aplicação de funções é criada. |
    | **[Grupo de Recursos](../azure-resource-manager/management/overview.md)** |  *myResourceGroup* | Nome do grupo de recursos novo no qual a aplicação Function App vai ser criada. |
    | **Nome da Aplicação de Funções** | Nome globalmente exclusivo | Nome que identifica a sua aplicação Function App nova. Os carateres válidos são `a-z` (não sensível a maiúsculas e minúsculas), `0-9` e `-`.  |
    |**Publicar**| Código | Opção para publicar ficheiros de código ou um contentor de Docker. |
    | **Pilha de runtime** | Linguagem preferencial | Escolha o núcleo powershell. |
    |**Versão**| Número da versão | Escolha a versão do seu tempo de execução instalado.  |
    |**Região**| Região preferida | Escolha uma [região](https://azure.microsoft.com/regions/) perto de si ou perto de outros serviços a que as suas funções acedam. |

    :::image type="content" source="./media/functions-hybrid-powershell/function-app-create-basics.png" alt-text="Criar uma aplicação de função - Básicos." border="true":::

1. Selecione **Seguinte : Hospedagem**. Na página **'Hospedagem',** insira as seguintes definições.

    | Definição      | Valor sugerido  | Descrição |
    | ------------ | ---------------- | ----------- |
    | **[Conta de armazenamento](../storage/common/storage-account-create.md)** |  Nome globalmente exclusivo |  Crie uma conta de armazenamento para ser utilizada pela sua aplicação de funções. Os nomes da conta de armazenamento devem ter entre 3 e 24 caracteres de comprimento e podem conter apenas números e letras minúsculas. Também pode utilizar uma conta existente, que deve satisfazer os requisitos da [conta de armazenamento.](../azure-functions/functions-scale.md#storage-account-requirements) |
    |**Sistema operativo**| Sistema operativo preferido | Um sistema operativo é pré-selecionado para si com base na seleção da sua pilha de tempo de execução, mas pode alterar a definição se necessário. |
    | **[Tipo de plano](../azure-functions/functions-scale.md)** | **Plano de serviço de aplicações** | Escolha o **plano de serviço da App.** Quando executa num plano do Serviço de Aplicações, tem de gerir o [dimensionamento da sua aplicação de funções](../azure-functions/functions-scale.md).  |

    :::image type="content" source="./media/functions-hybrid-powershell/function-app-create-hosting.png" alt-text="Criar uma aplicação de função - Básicos." border="true":::

1. Selecione **Seguinte : Monitorização**. Na página **'Monitor'** insira as seguintes definições.

    | Definição      | Valor sugerido  | Descrição |
    | ------------ | ---------------- | ----------- |
    | **[Application Insights](../azure-functions/functions-monitoring.md)** | Predefinição | Cria um recurso Application Insights com o mesmo *nome app* na região suportada mais próxima. Ao expandir esta definição ou selecionar **Criar novos,** pode alterar o nome De Insights de Aplicação ou escolher uma região diferente numa [geografia Azure](https://azure.microsoft.com/global-infrastructure/geographies/) onde pretende armazenar os seus dados. |

    :::image type="content" source="./media/functions-hybrid-powershell/function-app-create-monitoring.png" alt-text="Criar uma aplicação de função - Básicos." border="true":::

1. Selecione **Review + crie** para rever as seleções de configuração da aplicação.

1. Na página **'Rever + criar',** rever as definições e, em seguida, selecionar **Criar** para provisão e implementar a aplicação de função.

1. Selecione o ícone **notificações** no canto superior direito do portal e observe a mensagem **de implementação conseguida.**

1. Selecione **Ir para o recurso** para ver a sua nova aplicação de funções. Também pode selecionar **Pin para painel de instrumentos.** A fixação facilita o regresso a esta função de recurso de aplicação a partir do seu dashboard.

## <a name="create-a-hybrid-connection-for-the-function-app"></a>Criar uma ligação híbrida para a aplicação de função

As ligações híbridas são configuradas a partir da secção de networking da aplicação de função:

1. Em **Definições** na aplicação de função que acabou de criar, **selecione Networking**. 
1. Selecione **Configurar os pontos finais das suas ligações híbridas**.
   
    :::image type="content" source="./media/functions-hybrid-powershell/configure-hybrid-connection-endpoint.png" alt-text="Criar uma aplicação de função - Básicos." border="true":::

1. **Selecione Adicionar ligação híbrida**.
   
    :::image type="content" source="./media/functions-hybrid-powershell/hybrid-connection-overview.png" alt-text="Criar uma aplicação de função - Básicos." border="true":::

1. Introduza informações sobre a ligação híbrida, tal como mostrado logo após a seguinte imagem. Tem a opção de fazer com que a definição do **Anfitrião Endpoint** corresponda ao nome de anfitrião do servidor no local, para facilitar a recordação do servidor mais tarde quando estiver a executar comandos remotos. A porta corresponde à porta de serviço de gestão remota padrão do Windows que foi definida no servidor anteriormente.
  
      :::image type="content" source="./media/functions-hybrid-powershell/add-hybrid-connection.png" alt-text="Criar uma aplicação de função - Básicos." border="true":::

    | Definição      | Valor sugerido  |
    | ------------ | ---------------- |
    | **Nome de conexão híbrido** | ContosoHybridOnPremisesServer |
    | **Anfitrião endpoint** | finanças1 |
    | **Porto endpoint** | 5986 |
    | **Espaço de nome de servicebus** | Criar Novo |
    | **Localização** | Escolha um local disponível |
    | **Nome** | contosopowershellhybrid | 

1. Selecione **OK** para criar a ligação híbrida.

## <a name="download-and-install-the-hybrid-connection"></a>Descarregue e instale a ligação híbrida

1. Selecione **Baixar o gestor de conexões** para guardar o ficheiro *.msi* localmente no seu computador.

    :::image type="content" source="./media/functions-hybrid-powershell/download-hybrid-connection-installer.png" alt-text="Criar uma aplicação de função - Básicos." border="true":::

1. Copie o ficheiro *.msi* do seu computador local para o servidor no local.
1. Executar o instalador Hybrid Connection Manager para instalar o serviço no servidor no local.

    :::image type="content" source="./media/functions-hybrid-powershell/hybrid-installation.png" alt-text="Criar uma aplicação de função - Básicos." border="true":::

1. A partir do portal, abra a ligação híbrida e, em seguida, copie a cadeia de ligação gateway para a área de transferência.

    :::image type="content" source="./media/functions-hybrid-powershell/copy-hybrid-connection.png" alt-text="Criar uma aplicação de função - Básicos." border="true":::

1. Abra o UI do Gestor de Ligação Híbrida no servidor no local.

    :::image type="content" source="./media/functions-hybrid-powershell/hybrid-connection-ui.png" alt-text="Criar uma aplicação de função - Básicos." border="true":::

1. Selecione **Introduza manualmente** e cole a cadeia de ligação da área de transferência.

    :::image type="content" source="./media/functions-hybrid-powershell/enter-manual-connection.png" alt-text="Criar uma aplicação de função - Básicos." border="true":::

1. Reinicie o Gestor de Ligação Híbrido da PowerShell se não aparecer como conectado.
    ```powershell
    Restart-Service HybridConnectionManager
    ```

## <a name="create-an-app-setting-for-the-password-of-an-administrator-account"></a>Criar uma definição de aplicativo para a palavra-passe de uma conta de administrador

1. Em **Definições** para a sua aplicação de função, selecione **Configuração**. 
1. Selecione **+ Nova definição de aplicação**.

    :::image type="content" source="./media/functions-hybrid-powershell/select-configuration.png" alt-text="Criar uma aplicação de função - Básicos." border="true":::

1. Nomeie a definição **ContosoUserPassword**e introduza a palavra-passe. Selecione **OK**.
1. **Selecione Guardar** para guardar a palavra-passe na aplicação de função.

    :::image type="content" source="./media/functions-hybrid-powershell/save-administrator-password.png" alt-text="Criar uma aplicação de função - Básicos." border="true":::

## <a name="create-a-function-http-trigger"></a>Criar um gatilho HTTP de função

1. Na sua aplicação de funções, selecione **Funções**e, em seguida, selecione **+ Adicionar**.

    :::image type="content" source="./media/functions-hybrid-powershell/create-http-trigger-function.png" alt-text="Criar uma aplicação de função - Básicos." border="true":::

1. Selecione o modelo **de gatilho HTTP.**

    :::image type="content" source="./media/functions-hybrid-powershell/select-http-trigger-template.png" alt-text="Criar uma aplicação de função - Básicos." border="true":::

1. Nomeie a nova função e selecione **Criar Função**.

    :::image type="content" source="./media/functions-hybrid-powershell/create-new-http-function.png" alt-text="Criar uma aplicação de função - Básicos." border="true":::

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

    :::image type="content" source="./media/functions-hybrid-powershell/save-http-function.png" alt-text="Criar uma aplicação de função - Básicos." border="true":::

 1. Selecione **Teste**e, em seguida, selecione **Executar** para testar a função. Reveja os registos para verificar se o teste foi bem sucedido.

     :::image type="content" source="./media/functions-hybrid-powershell/test-function-hybrid.png" alt-text="Criar uma aplicação de função - Básicos." border="true":::

## <a name="managing-other-systems-on-premises"></a>Gestão de outros sistemas no local

Pode utilizar o servidor de bordo conectado para se ligar a outros servidores e sistemas de gestão no ambiente local. Isto permite-lhe gerir as operações do datacenter a partir do Azure utilizando as suas funções PowerShell. O seguinte script regista uma sessão de configuração PowerShell que funciona sob as credenciais fornecidas. Estas credenciais devem ser para um administrador nos servidores remotos. Em seguida, pode utilizar esta configuração para aceder a outros pontos finais no servidor local ou no datacenter.

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

Substitua as seguintes variáveis neste roteiro pelos valores aplicáveis do seu ambiente:
* $HybridEndpoint
* $RemoteServer

Nos dois cenários anteriores, pode ligar e gerir os seus ambientes no local utilizando o PowerShell em Funções Azure e Conexões Híbridas. Encorajamo-lo a aprender mais sobre [conexões híbridas](../app-service/app-service-hybrid-connections.md) e [PowerShell em funções.](./functions-reference-powershell.md)

Também pode utilizar [redes virtuais](./functions-create-vnet.md) Azure para ligar ao seu ambiente no local através de Funções Azure.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"] 
> [Saiba mais sobre trabalhar com funções PowerShell](functions-reference-powershell.md)
