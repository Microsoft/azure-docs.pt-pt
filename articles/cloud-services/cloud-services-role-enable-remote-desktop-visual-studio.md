---
title: Ativar a ligação de ambiente de trabalho remoto para uma função nos serviços Cloud do Azure
description: Como configurar a aplicação do serviço cloud do Azure para permitir ligações de ambiente de trabalho remotas
services: cloud-services
author: ghogen
manager: douge
ms.assetid: f5727ebe-9f57-4d7d-aff1-58761e8de8c1
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.topic: conceptual
ms.workload: azure-vs
ms.date: 03/06/2018
ms.author: ghogen
ms.openlocfilehash: 924719a8371f4d41cb9ead09252d8f3d3424326a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "64717748"
---
# <a name="enable-remote-desktop-connection-for-a-role-in-azure-cloud-services-using-visual-studio"></a>Ativar a ligação de ambiente de trabalho remoto para uma função nos serviços Cloud do Azure com o Visual Studio

> [!div class="op_single_selector"]
> * [Portal do Azure](cloud-services-role-enable-remote-desktop-new-portal.md)
> * [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md)
> * [Visual Studio](cloud-services-role-enable-remote-desktop-visual-studio.md)

Ambiente de trabalho remoto permite-lhe aceder a área de trabalho de uma função em execução no Azure. Pode utilizar uma ligação de ambiente de trabalho remoto para resolver problemas e diagnosticar problemas com a sua aplicação durante a execução.

O Assistente de publicação que o Visual Studio fornece para serviços em nuvem inclui uma opção para ativar o ambiente de trabalho remoto durante o processo de publicação, usando as credenciais fornecidas. Utilizar esta opção é adequada ao utilizar o Visual Studio 2017 versão 15.4 e anterior.

No entanto, com o Visual Studio 2017 versão 15.5 e posterior, é recomendável que evite ativar o ambiente de trabalho remoto através do Assistente de publicação, a menos que está trabalhando apenas como um único desenvolvedor. Para qualquer situação em que o projeto pode ser aberto por outros desenvolvedores, ativar em vez disso, ambiente de trabalho remoto através do portal do Azure, através do PowerShell ou a partir de um pipeline de lançamento num fluxo de trabalho de implementação contínua. Esta recomendação é devido a uma alteração na forma como Visual Studio comunica com o ambiente de trabalho remoto no serviço em nuvem da VM, conforme é explicado neste artigo.

## <a name="configure-remote-desktop-through-visual-studio-2017-version-154-and-earlier"></a>Configurar o ambiente de trabalho remoto através do Visual Studio 2017 versão 15.4 e anterior

Ao utilizar o Visual Studio 2017 versão 15.4 e anterior, pode utilizar o **ativar o ambiente de trabalho remoto para todas as funções** opção no Assistente de publicação. Pode continuar a utilizar o assistente com o Visual Studio 2017 versão 15.5 e posterior, mas não utilize a opção de ambiente de trabalho remoto.

1. No Visual Studio, inicie o Assistente de publicação, clicando com o botão direito do projeto de serviço em nuvem no Explorador de soluções e escolha **publicar**.

2. Inicie sessão na sua subscrição do Azure, se for necessário e selecione **seguinte**.

3. Na **configurações** página, selecione **ativar o ambiente de trabalho remoto para todas as funções**, em seguida, selecione o **definições...**  link para abrir o **configuração do ambiente de trabalho remoto** caixa de diálogo.

4. Na parte inferior da caixa de diálogo, selecione **mais opções**. Este comando apresenta uma lista pendente, em que crie ou escolha um certificado para que pode criptografar informações de credenciais quando pode ligar através do ambiente de trabalho remoto.

   > [!Note]
   > Os certificados que sejam necessárias para uma conexão de área de trabalho remota são diferentes dos certificados que utiliza para outras operações do Azure. O certificado de acesso remoto tem de ter uma chave privada.

5. Selecione um certificado a partir da lista ou escolha  **&lt;criar... &gt;** . Se criar um novo certificado, forneça um nome amigável para o novo certificado quando lhe for pedido e selecione **OK**. O novo certificado é apresentado na caixa de lista pendente.

6. Forneça um nome de utilizador e uma palavra-passe. Não é possível utilizar uma conta existente. Não utilize "Administrador" como nome de utilizador para a nova conta.

7. Escolha uma data em que a conta que irá expirar e depois as ligações de ambiente de trabalho remoto serão bloqueadas.

8. Depois de fornecer todas as informações necessárias, selecione **OK**. O Visual Studio adiciona as definições de ambiente de trabalho remoto ao seu projeto `.cscfg` e `.csdef` ficheiros, incluindo a palavra-passe é encriptada utilizando o certificado escolhido.

9. Conclua os restantes passos com o **próxima** botão, em seguida, selecione **Publish** quando estiver pronto para publicar o seu serviço cloud. Se não estiver pronto para publicar, selecione **Cancelar** e a resposta **Sim** quando lhe for pedido para guardar as alterações. Pode publicar o seu serviço cloud mais tarde com estas definições.

## <a name="configure-remote-desktop-when-using-visual-studio-2017-version-155-and-later"></a>Configurar o ambiente de trabalho remoto quando utilizar o Visual Studio 2017 versão 15.5 e posterior

Com o Visual Studio 2017 versão 15.5 e posterior, pode continuar a utilizar o Assistente de publicação com um projeto de serviço em nuvem. Também pode utilizar o **ativar o ambiente de trabalho remoto para todas as funções** opção se estiver a trabalhar apenas como um único desenvolvedor.

Se estiver a trabalhar como parte de uma equipe, em vez disso, deve ativar o ambiente de trabalho remoto no serviço cloud do Azure utilizando o [portal do Azure](cloud-services-role-enable-remote-desktop-new-portal.md) ou [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md).

Esta recomendação é devido a uma alteração em como o Visual Studio 2017 versão 15.5 e posterior comunica com o serviço em nuvem da VM. Ao ativar o ambiente de trabalho remoto através do Assistente de publicação, as versões anteriores do Visual Studio se comunicar com a VM através do que é chamado de "RDP Plug-in." Visual Studio 2017 versão 15.5 e versões posterior se comunica com em vez disso, a extensão"RDP" que é mais segura e mais flexível. Esta alteração também se alinha com o fato de que o portal do Azure e os métodos de PowerShell para ativar o ambiente de trabalho remoto também utilizam a extensão RDP.

Quando o Visual Studio se comunica com a extensão RDP, transmitir uma palavra-passe de texto simples através de SSL. No entanto, os ficheiros de configuração do projeto armazenam apenas uma palavra-passe encriptada, que pode ser descriptografada em texto sem formatação apenas com o certificado de local que tinha sido originalmente utilizado para criptografá-la.

Se implementar o projeto de serviço em nuvem no mesmo computador de desenvolvimento cada vez, esse certificado local está disponível. Neste caso, pode continuar a utilizar o **ativar o ambiente de trabalho remoto para todas as funções** opção no Assistente de publicação.

Se ou outros desenvolvedores desejam implantar o projeto de serviço em nuvem a partir de computadores diferentes, no entanto, em seguida, esses outros computadores não terá o certificado necessário para desencriptar a palavra-passe. Como resultado, verá a seguinte mensagem de erro:

```output
Applying remote desktop protocol (RDP) extension.
Certificate with thumbprint [thumbprint] doesn't exist.
```

Pode alterar a palavra-passe sempre que implementar o serviço em nuvem, mas essa ação torna-se inconveniente para todas as pessoas que tem de utilizar o ambiente de trabalho remoto.

Se estiver a partilhar o projeto com uma equipe, em seguida, é melhor desmarque a opção no Assistente de publicação e em vez disso, ative o ambiente de trabalho remoto diretamente através da [portal do Azure](cloud-services-role-enable-remote-desktop-new-portal.md) ou utilizando [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md).

### <a name="deploying-from-a-build-server-with-visual-studio-2017-version-155-and-later"></a>Implementar a partir de um servidor de compilação com o Visual Studio 2017 versão 15.5 e posterior

Pode implementar um projeto de serviço em nuvem de um servidor de compilação (por exemplo, com os serviços de DevOps do Azure) no qual Visual Studio 2017 versão 15.5 ou posterior está instalado no agente de compilação. Com essa organização, a implementação acontece no mesmo computador no qual o certificado de encriptação está disponível.

Para utilizar a extensão RDP dos serviços de DevOps do Azure, incluem os seguintes detalhes no seu pipeline de compilação:

1. Incluir `/p:ForceRDPExtensionOverPlugin=true` em seus argumentos de MSBuild para se certificar de que a implantação funciona com a extensão RDP em vez do plug-in RDP. Por exemplo:

    ```
    msbuild AzureCloudService5.ccproj /t:Publish /p:TargetProfile=Cloud /p:DebugType=None
        /p:SkipInvalidConfigurations=true /p:ForceRDPExtensionOverPlugin=true
    ```

1. Depois dos passos de compilação, adicione a **implementação do serviço Cloud do Azure** passo e definir as respetivas propriedades.

1. Após o passo de implementação, adicione uma **do Azure Powershell** passo, defina seu **nome a apresentar** propriedade para "Implementação do Azure: Ativar a extensão RDP"(ou outro nome adequado) e selecione a sua subscrição do Azure adequada.

1. Definir **tipo de Script** para "Inline" e cole o código abaixo para o **Inline Script** campo. (Também pode criar uma `.ps1` do ficheiro no seu projeto com este script, defina **tipo de Script** "Caminho de ficheiro de Script" e o conjunto **caminho do Script** para apontar para o ficheiro.)

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

## <a name="connect-to-an-azure-role-by-using-remote-desktop"></a>Ligar a uma função do Azure com o ambiente de trabalho remoto

Depois de publicar o seu serviço cloud no Azure e tiver ativado o ambiente de trabalho remoto, pode utilizar o Visual Studio Server Explorer para iniciar sessão no serviço em nuvem da VM:

1. No Explorador de servidores, expanda o **Azure** nó e, em seguida, expanda o nó para um serviço em nuvem e uma de suas funções para apresentar uma lista de instâncias.

2. Um nó de instância com o botão direito e selecione **ligar através de ambiente de trabalho remoto**.

3. Introduza o nome de utilizador e palavra-passe que criou anteriormente. Agora são registadas para a sessão remota.

## <a name="additional-resources"></a>Recursos adicionais

[Como configurar um Serviços Cloud](cloud-services-how-to-configure-portal.md)
