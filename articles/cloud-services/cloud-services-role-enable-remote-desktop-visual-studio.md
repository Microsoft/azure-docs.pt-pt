---
title: Utilizando o Visual Studio, ative o Remote Desktop para um role (clássico dos Serviços Azure Cloud)
description: Como configurar a sua aplicação de serviço em nuvem Azure para permitir ligações remotas de desktop
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: ad95cefbdf839c28b0979b051e217a1dfec76eea
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98743241"
---
# <a name="enable-remote-desktop-connection-for-a-role-in-azure-cloud-services-classic-using-visual-studio"></a>Ativar a conexão de ambiente de trabalho remoto para um papel em Azure Cloud Services (clássico) usando o Visual Studio

> [!IMPORTANT]
> [Azure Cloud Services (suporte alargado)](../cloud-services-extended-support/overview.md) é um novo modelo de implementação baseado em Recursos Azure para o produto Azure Cloud Services.Com esta alteração, os Serviços Azure Cloud em execução no modelo de implementação baseado no Azure Service Manager foram renomeados como Cloud Services (clássico) e todas as novas implementações devem utilizar [os Serviços Cloud (suporte alargado)](../cloud-services-extended-support/overview.md).

> [!div class="op_single_selector"]
> * [Portal do Azure](cloud-services-role-enable-remote-desktop-new-portal.md)
> * [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md)
> * [Visual Studio](cloud-services-role-enable-remote-desktop-visual-studio.md)

O Ambiente de Trabalho Remoto permite-lhe aceder ao ambiente de trabalho de uma função em execução no Azure. Pode utilizar uma ligação remote desktop para resolver problemas e diagnosticar problemas com a sua aplicação enquanto está em funcionamento.

O assistente de publicação que o Visual Studio fornece para serviços na nuvem inclui uma opção para ativar o Ambiente de Trabalho Remoto durante o processo de publicação, utilizando credenciais que fornece. A utilização desta opção é adequada quando se utiliza a versão 15.4 e mais cedo do Visual Studio 2017.

Com o Visual Studio 2017 versão 15.5 e mais tarde, no entanto, recomenda-se que evite ativar o Remote Desktop através do assistente de publicação, a menos que esteja a trabalhar apenas como um único desenvolvedor. Para qualquer situação em que o projeto possa ser aberto por outros desenvolvedores, em vez disso, ativa o Remote Desktop através do portal Azure, através do PowerShell, ou a partir de um pipeline de libertação num fluxo de trabalho de implementação contínua. Esta recomendação deve-se a uma alteração na forma como o Visual Studio comunica com o Remote Desktop no serviço de nuvem VM, como se explica neste artigo.

## <a name="configure-remote-desktop-through-visual-studio-2017-version-154-and-earlier"></a>Configurar o Ambiente de Trabalho Remoto através do Visual Studio 2017 versão 15.4 e anterior

Ao utilizar a versão 15.4 e mais cedo do Visual Studio 2017, pode utilizar o **Enable Remote Desktop para todas as funções** no assistente de publicação. Ainda pode utilizar o assistente com visual Studio 2017 versão 15.5 e posterior, mas não utilize a opção Remote Desktop.

1. No Visual Studio, inicie o assistente de publicação clicando à direita no seu projeto de serviço em nuvem no Solution Explorer e escolhendo **a Publish**.

2. Inscreva-se na sua subscrição Azure se necessário e selecione **Next**.

3. Na página **Definições,** selecione **Ativar o Ambiente de Trabalho Remoto para todas as funções** e, em seguida, selecione o link **Definições...** para abrir a caixa de diálogo **de configuração de ambiente de trabalho remoto.**

4. Na parte inferior da caixa de diálogo, selecione **Mais Opções**. Este comando apresenta uma lista de drop-down na qual cria ou escolhe um certificado para que possa encriptar informações de credenciais ao ligar através de um ambiente de trabalho remoto.

   > [!Note]
   > Os certificados de que necessita para uma ligação remota no ambiente de trabalho são diferentes dos certificados que utiliza para outras operações do Azure. O certificado de acesso remoto deve ter uma chave privada.

5. Selecione um certificado da lista ou escolha **&lt; Criar... &gt;**. Se criar um novo certificado, forneça um nome amigável para o novo certificado quando solicitado e selecione **OK**. O novo certificado aparece na caixa da lista.

6. Forneça um nome de utilizador e uma senha. Não pode usar uma conta existente. Não utilize o "Administrador" como nome de utilizador da nova conta.

7. Escolha uma data em que a conta expirará e após a qual as ligações de Ambiente de Trabalho Remoto serão bloqueadas.

8. Depois de fornecer todas as informações necessárias, selecione **OK**. O Visual Studio adiciona as definições de Desktop Remoto ao do seu projeto `.cscfg` e `.csdef` aos ficheiros, incluindo a palavra-passe encriptada utilizando o certificado escolhido.

9. Preencha os passos restantes utilizando o botão **Seguinte** e, em seguida, selecione **Publicar** quando estiver pronto para publicar o seu serviço na nuvem. Se não estiver pronto para publicar, selecione **Cancelar** e **responda Sim** quando solicitado para guardar alterações. Pode publicar o seu serviço de cloud mais tarde com estas definições.

## <a name="configure-remote-desktop-when-using-visual-studio-2017-version-155-and-later"></a>Configure o Ambiente de Trabalho Remoto ao utilizar a versão 15.5 do Visual Studio 2017 e posterior

Com o Visual Studio 2017 versão 15.5 e mais tarde, ainda pode utilizar o assistente de publicação com um projeto de serviço na nuvem. Também pode utilizar o **Enable Remote Desktop para todas as opções de funções** se estiver a trabalhar apenas como um único desenvolvedor.

Se estiver a trabalhar como parte de uma equipa, deverá, em vez disso, ativar o ambiente de trabalho remoto no serviço de nuvem Azure utilizando o [portal Azure](cloud-services-role-enable-remote-desktop-new-portal.md) ou [o PowerShell](cloud-services-role-enable-remote-desktop-powershell.md).

Esta recomendação deve-se a uma mudança na forma como o Visual Studio 2017 versão 15.5 e posteriormente comunica com o serviço de nuvem VM. Ao ativar o Remote Desktop através do assistente de publicação, as versões anteriores do Visual Studio comunicam com o VM através do chamado "plugin RDP". Visual Studio 2017 versão 15.5 e mais tarde comunica em vez de usar a "extensão RDP" que é mais segura e mais flexível. Esta alteração também está alinhada com o facto de que o portal Azure e os métodos PowerShell para ativar o Ambiente de Trabalho Remoto também utilizam a extensão RDP.

Quando o Visual Studio comunica com a extensão RDP, transmite uma palavra-passe de texto simples sobre o TLS. No entanto, os ficheiros de configuração do projeto armazenam apenas uma palavra-passe encriptada, que pode ser desencriptada em texto simples apenas com o certificado local que foi originalmente usado para criptografá-lo.

Se implementar o projeto de serviço na nuvem a partir do mesmo computador de desenvolvimento de cada vez, então esse certificado local está disponível. Neste caso, ainda pode utilizar o **Enable Remote Desktop para todas as funções** no assistente de publicação.

No entanto, se você ou outros desenvolvedores quiserem implementar o projeto de serviço na nuvem a partir de diferentes computadores, então esses outros computadores não terão o certificado necessário para desencriptar a palavra-passe. Como resultado, vê a seguinte mensagem de erro:

```output
Applying remote desktop protocol (RDP) extension.
Certificate with thumbprint [thumbprint] doesn't exist.
```

Pode alterar a palavra-passe sempre que implementar o serviço de cloud, mas essa ação torna-se inconveniente para todos os que precisam de utilizar o Remote Desktop.

Se estiver a partilhar o projeto com uma equipa, então, o melhor é limpar a opção no assistente de publicação e, em vez disso, ativar o Remote Desktop diretamente através do [portal Azure](cloud-services-role-enable-remote-desktop-new-portal.md) ou utilizando o [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md).

### <a name="deploying-from-a-build-server-with-visual-studio-2017-version-155-and-later"></a>Implantação a partir de um servidor de construção com visual Studio 2017 versão 15.5 e posterior

Pode implementar um projeto de serviço em nuvem a partir de um servidor de construção (por exemplo, com serviços Azure DevOps) no qual a versão 15.5 do Visual Studio 2017 ou posteriormente é instalada no agente de construção. Com este arranjo, a implementação ocorre a partir do mesmo computador em que o certificado de encriptação está disponível.

Para utilizar a extensão RDP dos Serviços Azure DevOps, inclua os seguintes detalhes no seu pipeline de construção:

1. Inclua `/p:ForceRDPExtensionOverPlugin=true` nos seus argumentos MSBuild para garantir que a implementação funciona com a extensão RDP em vez do plugin RDP. Por exemplo:

    ```
    msbuild AzureCloudService5.ccproj /t:Publish /p:TargetProfile=Cloud /p:DebugType=None
        /p:SkipInvalidConfigurations=true /p:ForceRDPExtensionOverPlugin=true
    ```

1. Após os seus passos de construção, adicione o passo de implantação do **Serviço de Nuvem Azure** e desaveie as suas propriedades.

1. Após o passo de implementação, adicione um passo **Azure Powershell,** desa estale a propriedade **do nome display** para "Azure Deployment: Enable RDP Extension" (ou outro nome adequado) e selecione a subscrição Azure apropriada.

1. Desa estaca **escreva** o tipo de script para "Inline" e cole o código abaixo no campo **Script Inline.** (Também pode criar um `.ps1` ficheiro no seu projeto com este script, definir o Tipo de **Script** para "Script File Path", e definir o Caminho **do Script** para apontar para o ficheiro.)

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

## <a name="connect-to-an-azure-role-by-using-remote-desktop"></a>Conecte-se a um papel Azure utilizando o Ambiente de Trabalho Remoto

Depois de publicar o seu serviço na nuvem no Azure e ter ativado o Remote Desktop, pode utilizar o Visual Studio Server Explorer para iniciar sessão no serviço de nuvem VM:

1. No Server Explorer, expanda o nó **Azure** e, em seguida, expanda o nó para um serviço de nuvem e um dos seus papéis para apresentar uma lista de casos.

2. Clique com o botão direito num nó de instância e selecione **Connect Using Remote Desktop**.

3. Introduza o nome de utilizador e a palavra-passe que criou anteriormente. Está agora ligado à sua sessão remota.

## <a name="additional-resources"></a>Recursos adicionais

[Como configurar um Serviços Cloud](cloud-services-how-to-configure-portal.md)
