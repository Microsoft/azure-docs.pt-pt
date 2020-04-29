---
title: Utilizando o Estúdio Visual, ative o Ambiente de Trabalho Remoto para uma Função (Serviços Azure Cloud)
description: Como configurar a sua aplicação de serviço em nuvem Azure para permitir ligações remotas de ambiente de trabalho
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
ms.openlocfilehash: f4622e44c795182ee68c617f335c9e1651d3adcc
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80294393"
---
# <a name="enable-remote-desktop-connection-for-a-role-in-azure-cloud-services-using-visual-studio"></a>Ativar a ligação remota para desktop para uma função nos serviços azure cloud usando o estúdio visual

> [!div class="op_single_selector"]
> * [Portal do Azure](cloud-services-role-enable-remote-desktop-new-portal.md)
> * [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md)
> * [Visual Studio](cloud-services-role-enable-remote-desktop-visual-studio.md)

O Remote Desktop permite-lhe aceder ao ambiente de trabalho de uma função em funcionamento no Azure. Pode utilizar uma ligação Remote Desktop para resolver problemas e diagnosticar problemas com a sua aplicação enquanto esta estiver em execução.

O assistente de publicação que o Visual Studio fornece para serviços na nuvem inclui uma opção para ativar o Remote Desktop durante o processo de publicação, utilizando credenciais que fornece. A utilização desta opção é adequada quando se utiliza o Visual Studio 2017 versão 15.4 e anterior.

Com o Visual Studio 2017 versão 15.5 e mais tarde, no entanto, recomenda-se que evite ativar o Remote Desktop através do assistente de publicação, a menos que esteja a trabalhar apenas como um único desenvolvedor. Para qualquer situação em que o projeto possa ser aberto por outros desenvolvedores, você em vez disso permite o Remote Desktop através do portal Azure, através da PowerShell, ou de um pipeline de lançamento num fluxo de trabalho de implantação contínua. Esta recomendação deve-se a uma mudança na forma como o Visual Studio comunica com o Remote Desktop no serviço de nuvem VM, como é explicado neste artigo.

## <a name="configure-remote-desktop-through-visual-studio-2017-version-154-and-earlier"></a>Configure Remote Desktop através do Visual Studio 2017 versão 15.4 e anterior

Ao utilizar o Visual Studio 2017 versão 15.4 e mais cedo, pode utilizar o **Enable Remote Desktop para todas as funções** no assistente de publicação. Ainda pode utilizar o assistente com a versão 15.5 do Visual Studio 2017 e posteriormente, mas não utilize a opção Remote Desktop.

1. No Estúdio Visual, inicie o assistente de publicação clicando no seu projeto de serviço na nuvem no Solution Explorer e escolhendo **publicar**.

2. Assine a subscrição do Azure se necessário e selecione **Next**.

3. Na página **Definições,** selecione Ativar o **Ambiente de Trabalho Remoto para todas as funções**e, em seguida, selecione o link **Definições...** para abrir a caixa de diálogo de **configuração de ambiente** de trabalho remoto.

4. Na parte inferior da caixa de diálogo, selecione **Mais Opções**. Este comando apresenta uma lista de drop-down na qual cria ou escolhe um certificado para que possa encriptar informações de credenciais ao ligar-se através de um ambiente de trabalho remoto.

   > [!Note]
   > Os certificados de que necessita para uma ligação remota de ambiente de trabalho são diferentes dos certificados que utiliza para outras operações do Azure. O certificado de acesso remoto deve ter uma chave privada.

5. Selecione um certificado da lista ou escolha ** &lt;Criar... &gt;**. Se criar um novo certificado, forneça um nome amigável para o novo certificado quando solicitado e selecione **OK**. O novo certificado aparece na caixa de listas.

6. Forneça um nome de utilizador e uma senha. Não pode usar uma conta existente. Não utilize "Administrador" como nome de utilizador para a nova conta.

7. Escolha uma data em que a conta expirará e após a qual as ligações remote Desktop serão bloqueadas.

8. Depois de fornecer todas as informações necessárias, selecione **OK**. O Visual Studio adiciona as definições `.cscfg` `.csdef` do Ambiente de Trabalho Remoto aos ficheiros e ficheiros do seu projeto, incluindo a palavra-passe encriptada utilizando o certificado escolhido.

9. Complete quaisquer passos restantes utilizando o botão **Seguinte** e, em seguida, selecione **Publicar** quando estiver pronto para publicar o seu serviço na nuvem. Se não estiver pronto para publicar, selecione **Cancelar** e responder **Sim** quando solicitado para guardar alterações. Pode publicar o seu serviço de cloud mais tarde com estas definições.

## <a name="configure-remote-desktop-when-using-visual-studio-2017-version-155-and-later"></a>Configure Desktop Remoto ao utilizar visual studio 2017 versão 15.5 e mais tarde

Com o Visual Studio 2017 versão 15.5 e mais tarde, ainda pode utilizar o assistente de publicação com um projeto de serviço na nuvem. Também pode utilizar o **Enable Remote Desktop para todas as funções** se estiver a trabalhar apenas como um único desenvolvedor.

Se estiver a trabalhar como parte de uma equipa, deve, em vez disso, ativar o ambiente de trabalho remoto no serviço de nuvem Azure utilizando o [portal Azure](cloud-services-role-enable-remote-desktop-new-portal.md) ou o [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md).

Esta recomendação deve-se a uma mudança na forma como o Visual Studio 2017 versão 15.5 e posteriormente comunica com o serviço de nuvem VM. Ao ativar o Remote Desktop através do assistente de publicação, as versões anteriores do Estúdio Visual comunicam com o VM através do chamado "plugin RDP". Visual Studio 2017 versão 15.5 e posteriormente comunica em vez usando a "extensão RDP" que é mais segura e flexível. Esta alteração também está alinhada com o facto de o portal Azure e os métodos PowerShell permitirem que o Ambiente de Trabalho Remoto também utilize a extensão RDP.

Quando o Visual Studio comunica com a extensão RDP, transmite uma senha de texto simples sobre tLS. No entanto, os ficheiros de configuração do projeto armazenam apenas uma senha encriptada, que só pode ser desencriptada em texto simples com o certificado local que foi originalmente utilizado para a encriptar.

Se implementar o projeto de serviço na nuvem a partir do mesmo computador de desenvolvimento de cada vez, então esse certificado local está disponível. Neste caso, ainda pode utilizar o **Enable Remote Desktop para todas as funções** no assistente de publicação.

Se você ou outros desenvolvedores quiserem implementar o projeto de serviço na nuvem a partir de diferentes computadores, no entanto, esses outros computadores não terão o certificado necessário para desencriptar a palavra-passe. Como resultado, vê a seguinte mensagem de erro:

```output
Applying remote desktop protocol (RDP) extension.
Certificate with thumbprint [thumbprint] doesn't exist.
```

Pode alterar a palavra-passe sempre que implementar o serviço de cloud, mas essa ação torna-se inconveniente para todos os que precisam de utilizar o Remote Desktop.

Se estiver a partilhar o projeto com uma equipa, então, o melhor é limpar a opção no assistente de publicação e, em vez disso, ativar o Remote Desktop diretamente através do [portal Azure](cloud-services-role-enable-remote-desktop-new-portal.md) ou utilizando o [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md).

### <a name="deploying-from-a-build-server-with-visual-studio-2017-version-155-and-later"></a>Implantação a partir de um servidor de construção com visual studio 2017 versão 15.5 e mais tarde

Pode implementar um projeto de serviço na nuvem a partir de um servidor de construção (por exemplo, com os Serviços Azure DevOps) no qual a versão 15.5 do Visual Studio 2017 está instalada no agente de construção. Com este arranjo, a implementação acontece a partir do mesmo computador no qual o certificado de encriptação está disponível.

Para utilizar a extensão RDP dos Serviços Azure DevOps, inclua os seguintes detalhes no seu pipeline de construção:

1. Inclua `/p:ForceRDPExtensionOverPlugin=true` nos seus argumentos MSBuild para garantir que a implementação funciona com a extensão RDP em vez do plugin RDP. Por exemplo:

    ```
    msbuild AzureCloudService5.ccproj /t:Publish /p:TargetProfile=Cloud /p:DebugType=None
        /p:SkipInvalidConfigurations=true /p:ForceRDPExtensionOverPlugin=true
    ```

1. Depois dos seus passos de construção, adicione o passo de implantação do **serviço azure cloud** e defina as suas propriedades.

1. Após o passo de implantação, adicione um passo **Azure Powershell,** coloque a sua propriedade **de nome display** em "Azure Deployment: Enable RDP Extension" (ou outro nome adequado), e selecione a sua subscrição Azure apropriada.

1. Delineie **o script para** "Inline" e colá o código abaixo no campo Script **Inline.** (Também pode criar `.ps1` um ficheiro no seu projeto com este script, definir **o Script Type** para "Script File Path", e definir o Script **Path** para apontar para o ficheiro.)

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

## <a name="connect-to-an-azure-role-by-using-remote-desktop"></a>Ligue-se a uma função Azure utilizando o Ambiente de Trabalho Remoto

Depois de publicar o seu serviço de cloud no Azure e ter ativado o Remote Desktop, pode utilizar o Visual Studio Server Explorer para iniciar sessão no serviço de nuvem VM:

1. No Server Explorer, expanda o nó **Azure** e, em seguida, expanda o nó para um serviço na nuvem e uma das suas funções para apresentar uma lista de instâncias.

2. Clique num nó de instância e selecione **Ligar Utilizando o ambiente de trabalho remoto**.

3. Introduza o nome de utilizador e a palavra-passe que criou anteriormente. Está agora ligado à sua sessão remota.

## <a name="additional-resources"></a>Recursos adicionais

[Como configurar um Serviços Cloud](cloud-services-how-to-configure-portal.md)
