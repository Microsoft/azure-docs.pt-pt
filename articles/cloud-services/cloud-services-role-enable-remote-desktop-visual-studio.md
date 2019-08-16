---
title: Habilitar Conexão de Área de Trabalho Remota para uma função nos serviços de nuvem do Azure
description: Como configurar seu aplicativo de serviço de nuvem do Azure para permitir conexões de área de trabalho remota
services: cloud-services
author: ghogen
manager: jillfra
ms.assetid: f5727ebe-9f57-4d7d-aff1-58761e8de8c1
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.topic: conceptual
ms.workload: azure-vs
ms.date: 03/06/2018
ms.author: ghogen
ms.openlocfilehash: 6a6d045513e3e91c5a8b2004e47378a097be8963
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/15/2019
ms.locfileid: "69515913"
---
# <a name="enable-remote-desktop-connection-for-a-role-in-azure-cloud-services-using-visual-studio"></a>Habilitar Conexão de Área de Trabalho Remota para uma função nos serviços de nuvem do Azure usando o Visual Studio

> [!div class="op_single_selector"]
> * [Azure portal](cloud-services-role-enable-remote-desktop-new-portal.md)
> * [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md)
> * [Visual Studio](cloud-services-role-enable-remote-desktop-visual-studio.md)

Área de Trabalho Remota permite que você acesse a área de trabalho de uma função em execução no Azure. Você pode usar uma conexão Área de Trabalho Remota para solucionar e diagnosticar problemas com seu aplicativo enquanto ele está em execução.

O assistente de publicação que o Visual Studio fornece para serviços de nuvem inclui uma opção para habilitar Área de Trabalho Remota durante o processo de publicação, usando as credenciais fornecidas por você. O uso dessa opção é adequado ao usar o Visual Studio 2017 versão 15,4 e anterior.

Com o Visual Studio 2017 versão 15,5 e posterior, no entanto, é recomendável evitar a habilitação de Área de Trabalho Remota por meio do assistente de publicação, a menos que você esteja trabalhando apenas como um único desenvolvedor. Para qualquer situação na qual o projeto possa ser aberto por outros desenvolvedores, você habilita Área de Trabalho Remota por meio do portal do Azure, por meio do PowerShell ou de um pipeline de lançamento em um fluxo de trabalho de implantação contínua. Essa recomendação ocorre devido a uma alteração no modo como o Visual Studio se comunica com Área de Trabalho Remota na VM do serviço de nuvem, como é explicado neste artigo.

## <a name="configure-remote-desktop-through-visual-studio-2017-version-154-and-earlier"></a>Configurar Área de Trabalho Remota por meio do Visual Studio 2017 versão 15,4 e anterior

Ao usar o Visual Studio 2017 versão 15,4 e anterior, você pode usar a opção **habilitar área de trabalho remota para todas as funções** no assistente de publicação. Você ainda pode usar o assistente com o Visual Studio 2017 versão 15,5 e posterior, mas não use a opção Área de Trabalho Remota.

1. No Visual Studio, inicie o assistente de publicação clicando com o botão direito do mouse em seu projeto de serviço de nuvem no Gerenciador de Soluções e escolhendo **publicar**.

2. Entre em sua assinatura do Azure, se necessário, e selecione **Avançar**.

3. Na página **configurações** , selecione **habilitar área de trabalho remota para todas as funções**e, em seguida, selecione o link **configurações...** para abrir a caixa de diálogo **configuração de área de trabalho remota** .

4. Na parte inferior da caixa de diálogo, selecione **mais opções**. Esse comando exibe uma lista suspensa na qual você cria ou escolhe um certificado para que você possa criptografar informações de credenciais ao se conectar por meio da área de trabalho remota.

   > [!Note]
   > Os certificados necessários para uma conexão de área de trabalho remota são diferentes dos certificados que você usa para outras operações do Azure. O certificado de acesso remoto deve ter uma chave privada.

5. Selecione um certificado na lista ou escolha  **&lt;criar... &gt;** . Se estiver criando um novo certificado, forneça um nome amigável para o novo certificado quando solicitado e selecione **OK**. O novo certificado é exibido na caixa de listagem suspensa.

6. Forneça um nome de usuário e uma senha. Você não pode usar uma conta existente. Não use "administrador" como o nome de usuário para a nova conta.

7. Escolha uma data na qual a conta irá expirar e, após a qual Área de Trabalho Remota conexões serão bloqueadas.

8. Depois de fornecer todas as informações necessárias, selecione **OK**. O Visual Studio adiciona as configurações de área de trabalho remota aos arquivos `.cscfg` e `.csdef` do projeto, incluindo a senha que é criptografada usando o certificado escolhido.

9. Conclua as etapas restantes usando o botão **Avançar** e, em seguida, selecione **publicar** quando estiver pronto para publicar seu serviço de nuvem. Se você não estiver pronto para publicar, selecione **Cancelar** e responda **Sim** quando for solicitado a salvar as alterações. Você pode publicar seu serviço de nuvem mais tarde com essas configurações.

## <a name="configure-remote-desktop-when-using-visual-studio-2017-version-155-and-later"></a>Configurar Área de Trabalho Remota ao usar o Visual Studio 2017 versão 15,5 e posterior

Com o Visual Studio 2017 versão 15,5 e posterior, você ainda pode usar o assistente de publicação com um projeto de serviço de nuvem. Você também pode usar a opção **habilitar área de trabalho remota para todas as funções** se estiver trabalhando apenas como um único desenvolvedor.

Se você estiver trabalhando como parte de uma equipe, deverá habilitar a área de trabalho remota no serviço de nuvem do Azure usando o [portal do Azure](cloud-services-role-enable-remote-desktop-new-portal.md) ou o [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md).

Essa recomendação ocorre devido a uma alteração no modo como o Visual Studio 2017 versão 15,5 e posterior se comunica com a VM do serviço de nuvem. Ao habilitar Área de Trabalho Remota por meio do assistente de publicação, as versões anteriores do Visual Studio se comunicam com a VM por meio do que chamamos de "plug-in RDP". O Visual Studio 2017 versão 15,5 e posterior se comunica usando a "extensão RDP", que é mais segura e mais flexível. Essa alteração também se alinha com o fato de que os métodos portal do Azure e PowerShell para habilitar Área de Trabalho Remota também usam a extensão RDP.

Quando o Visual Studio se comunica com a extensão RDP, ele transmite uma senha de texto sem formatação por SSL. No entanto, os arquivos de configuração do projeto armazenam apenas uma senha criptografada, que pode ser descriptografada em texto sem formatação somente com o certificado local que foi originalmente usado para criptografá-lo.

Se você implantar o projeto de serviço de nuvem do mesmo computador de desenvolvimento a cada vez, esse certificado local estará disponível. Nesse caso, você ainda pode usar a opção **habilitar área de trabalho remota para todas as funções** no assistente de publicação.

No entanto, se você ou outros desenvolvedores desejarem implantar o projeto de serviço de nuvem de diferentes computadores, esses outros computadores não terão o certificado necessário para descriptografar a senha. Como resultado, você verá a seguinte mensagem de erro:

```output
Applying remote desktop protocol (RDP) extension.
Certificate with thumbprint [thumbprint] doesn't exist.
```

Você pode alterar a senha toda vez que implantar o serviço de nuvem, mas essa ação torna-se inconveniente para todos que precisam usar Área de Trabalho Remota.

Se você estiver compartilhando o projeto com uma equipe, é melhor desmarcar a opção no assistente de publicação e, em vez disso, habilitar Área de Trabalho Remota diretamente por meio do [portal do Azure](cloud-services-role-enable-remote-desktop-new-portal.md) ou usando o [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md).

### <a name="deploying-from-a-build-server-with-visual-studio-2017-version-155-and-later"></a>Implantando de um servidor de compilação com o Visual Studio 2017 versão 15,5 e posterior

Você pode implantar um projeto de serviço de nuvem de um servidor de compilação (por exemplo, com Azure DevOps Services) no qual o Visual Studio 2017 versão 15,5 ou posterior está instalado no agente de compilação. Com esse arranjo, a implantação ocorre no mesmo computador em que o certificado de criptografia está disponível.

Para usar a extensão RDP do Azure DevOps Services, inclua os seguintes detalhes em seu pipeline de compilação:

1. Inclua `/p:ForceRDPExtensionOverPlugin=true` em seus argumentos do MSBuild para garantir que a implantação funcione com a extensão RDP em vez do plug-in RDP. Por exemplo:

    ```
    msbuild AzureCloudService5.ccproj /t:Publish /p:TargetProfile=Cloud /p:DebugType=None
        /p:SkipInvalidConfigurations=true /p:ForceRDPExtensionOverPlugin=true
    ```

1. Após as etapas de compilação, adicione a etapa de **implantação do serviço de nuvem do Azure** e defina suas propriedades.

1. Após a etapa de implantação, adicione uma etapa **do Azure PowerShell** , defina sua propriedade **nome de exibição** como "implantação do Azure: Habilite a extensão RDP "(ou outro nome adequado) e selecione sua assinatura do Azure apropriada.

1. Defina o **tipo de script** como "embutido" e cole o código abaixo no campo **script embutido** . (Você também pode criar um `.ps1` arquivo em seu projeto com esse script, definir o **tipo de script** como "caminho do arquivo de script" e definir o caminho do **script** para apontar para o arquivo.)

    ```ps
    Param(
        [Parameter(Mandatory=$True)]
        [string]$username,

        [Parameter(Mandatory=$True)]
        [string]$password,

        [Parameter(Mandatory=$True)]
        [string]$serviceName,

        [Datetime]$expiry = ($(Get-Date).AddYears(1))
    )

    Write-Host "Service Name: $serviceName"
    Write-Host "User Name: $username"
    Write-Host "Expiry: $expiry"

    $securepassword = ConvertTo-SecureString -String $password -AsPlainText -Force
    $credential = New-Object System.Management.Automation.PSCredential $username,$securepassword

    # Try to remote existing RDP Extensions
    try
    {
        $existingRDPExtension = Get-AzureServiceRemoteDesktopExtension -ServiceName $servicename
        if ($existingRDPExtension -ne $null)
        {
            Remove-AzureServiceRemoteDesktopExtension -ServiceName $servicename -UninstallConfiguration
        }
    }
    catch
    {
    }

    Set-AzureServiceRemoteDesktopExtension -ServiceName $servicename -Credential $credential -Expiration $expiry -Verbose
    ```

## <a name="connect-to-an-azure-role-by-using-remote-desktop"></a>Conectar-se a uma função do Azure usando Área de Trabalho Remota

Depois de publicar seu serviço de nuvem no Azure e habilitar o Área de Trabalho Remota, você pode usar o Visual Studio Gerenciador de Servidores para fazer logon na VM do serviço de nuvem:

1. No Gerenciador de Servidores, expanda o nó **Azure** e, em seguida, expanda o nó para um serviço de nuvem e uma de suas funções para exibir uma lista de instâncias.

2. Clique com o botão direito do mouse em um nó de instância e selecione **conectar usando área de trabalho remota**.

3. Insira o nome de usuário e a senha que você criou anteriormente. Agora você está conectado à sessão remota.

## <a name="additional-resources"></a>Recursos adicionais

[Como configurar um Serviços Cloud](cloud-services-how-to-configure-portal.md)
