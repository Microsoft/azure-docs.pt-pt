---
title: Usar o PowerShell para habilitar a Área de Trabalho Remota para uma função
titleSuffix: Azure Cloud Services
description: Como configurar seu aplicativo de serviço de nuvem do Azure usando o PowerShell para permitir conexões de área de trabalho remota
services: cloud-services
documentationcenter: ''
author: tgore03
ms.service: cloud-services
ms.topic: article
ms.date: 07/18/2017
ms.author: tagore
ms.openlocfilehash: e4e8dca6c5359e865e6a17fc47fe47802b0ee9e6
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75386124"
---
# <a name="enable-remote-desktop-connection-for-a-role-in-azure-cloud-services-using-powershell"></a>Habilitar Conexão de Área de Trabalho Remota para uma função nos serviços de nuvem do Azure usando o PowerShell

> [!div class="op_single_selector"]
> * [Portal do Azure](cloud-services-role-enable-remote-desktop-new-portal.md)
> * [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md)
> * [Visual Studio](cloud-services-role-enable-remote-desktop-visual-studio.md)

Área de Trabalho Remota permite que você acesse a área de trabalho de uma função em execução no Azure. Você pode usar uma conexão Área de Trabalho Remota para solucionar e diagnosticar problemas com seu aplicativo enquanto ele está em execução.

Este artigo descreve como habilitar a área de trabalho remota em suas funções de serviço de nuvem usando o PowerShell. Consulte [como instalar e configurar Azure PowerShell](/powershell/azure/overview) para os pré-requisitos necessários para este artigo. O PowerShell utiliza a extensão Área de Trabalho Remota para que você possa habilitar Área de Trabalho Remota depois que o aplicativo for implantado.

## <a name="configure-remote-desktop-from-powershell"></a>Configurar Área de Trabalho Remota do PowerShell
O cmdlet [set-AzureServiceRemoteDesktopExtension](/powershell/module/servicemanagement/azure/set-azureserviceremotedesktopextension?view=azuresmps-3.7.0) permite habilitar área de trabalho remota em funções especificadas ou em todas as funções da implantação do serviço de nuvem. O cmdlet permite que você especifique o nome de usuário e a senha para a área de trabalho remota por meio do parâmetro *Credential* que aceita um objeto PSCredential.

Se você estiver usando o PowerShell interativamente, poderá definir facilmente o objeto PSCredential chamando o cmdlet [Get-Credentials](https://technet.microsoft.com/library/hh849815.aspx) .

```powershell
$remoteusercredentials = Get-Credential
```

Esse comando exibe uma caixa de diálogo que permite que você insira o nome de usuário e a senha para o usuários remotos de maneira segura.

Como o PowerShell ajuda em cenários de automação, você também pode configurar o objeto **PSCredential** de uma maneira que não exija interação do usuário. Primeiro, você precisa configurar uma senha segura. Você começa com a especificação de uma senha de texto sem formatação para convertê-la em uma cadeia de caracteres segura usando [ConvertTo-SecureString](https://technet.microsoft.com/library/hh849818.aspx). Em seguida, você precisa converter essa cadeia de caracteres segura em uma cadeia de caracteres criptografada padrão usando [ConvertFrom-SecureString](https://technet.microsoft.com/library/hh849814.aspx). Agora você pode salvar essa cadeia de caracteres criptografada padrão em um arquivo usando [Set-Content](https://technet.microsoft.com/library/ee176959.aspx).

Você também pode criar um arquivo de senha segura para que não precise digitar a senha todas as vezes. Além disso, um arquivo de senha segura é melhor do que um arquivo de texto sem formatação. Use o PowerShell a seguir para criar um arquivo de senha segura:

```powershell
ConvertTo-SecureString -String "Password123" -AsPlainText -Force | ConvertFrom-SecureString | Set-Content "password.txt"
```

> [!IMPORTANT]
> Ao definir a senha, verifique se você atende aos [requisitos de complexidade](https://technet.microsoft.com/library/cc786468.aspx).

Para criar o objeto de credencial a partir do arquivo de senha segura, você deve ler o conteúdo do arquivo e convertê-lo de volta para uma cadeia de caracteres segura usando [ConvertTo-SecureString](https://technet.microsoft.com/library/hh849818.aspx).

O cmdlet [set-AzureServiceRemoteDesktopExtension](/powershell/module/servicemanagement/azure/set-azureserviceremotedesktopextension?view=azuresmps-3.7.0) também aceita um parâmetro de *expiração* , que especifica um **DateTime** no qual a conta de usuário expira. Por exemplo, você pode definir a conta para expirar alguns dias a partir da data e hora atuais.

Este exemplo do PowerShell mostra como definir a extensão de Área de Trabalho Remota em um serviço de nuvem:

```powershell
$servicename = "cloudservice"
$username = "RemoteDesktopUser"
$securepassword = Get-Content -Path "password.txt" | ConvertTo-SecureString
$expiry = $(Get-Date).AddDays(1)
$credential = New-Object System.Management.Automation.PSCredential $username,$securepassword
Set-AzureServiceRemoteDesktopExtension -ServiceName $servicename -Credential $credential -Expiration $expiry
```
Opcionalmente, você também pode especificar o slot de implantação e as funções nas quais deseja habilitar a área de trabalho remota. Se esses parâmetros não forem especificados, o cmdlet habilitará a área de trabalho remota em todas as funções no slot de implantação de **produção** .

A extensão de Área de Trabalho Remota está associada a uma implantação. Se você criar uma nova implantação para o serviço, precisará habilitar a área de trabalho remota nessa implantação. Se você sempre quiser que a área de trabalho remota esteja habilitada, considere a integração dos scripts do PowerShell ao seu Workflow de implantação.

## <a name="remote-desktop-into-a-role-instance"></a>Área de Trabalho Remota em uma instância de função

O cmdlet [Get-AzureRemoteDesktopFile](/powershell/module/servicemanagement/azure/get-azureremotedesktopfile?view=azuresmps-3.7.0) é usado para a área de trabalho remota em uma instância de função específica do seu serviço de nuvem. Você pode usar o parâmetro *LocalPath* para baixar o arquivo RDP localmente. Ou você pode usar o parâmetro de *inicialização* para iniciar diretamente a caixa de diálogo de conexão de área de trabalho remota para acessar a instância de função do serviço de nuvem.

```powershell
Get-AzureRemoteDesktopFile -ServiceName $servicename -Name "WorkerRole1_IN_0" -Launch
```

## <a name="check-if-remote-desktop-extension-is-enabled-on-a-service"></a>Verificar se a extensão de Área de Trabalho Remota está habilitada em um serviço

O cmdlet [Get-AzureServiceRemoteDesktopExtension](/powershell/module/servicemanagement/azure/get-azureremotedesktopfile?view=azuresmps-3.7.0) exibe que a área de trabalho remota está habilitada ou desabilitada em uma implantação de serviço. O cmdlet retorna o nome de usuário para a área de trabalho remota e as funções para as quais a extensão de área de trabalho remota está habilitada. Por padrão, isso ocorre no slot de implantação e você pode optar por usar o slot de preparo em vez disso.

```powershell
Get-AzureServiceRemoteDesktopExtension -ServiceName $servicename
```

## <a name="remove-remote-desktop-extension-from-a-service"></a>Remover a extensão de Área de Trabalho Remota de um serviço

Se você já tiver habilitado a extensão de área de trabalho remota em uma implantação e precisar atualizar as configurações da área de trabalho remota, primeiro remova a extensão. E habilitá-lo novamente com as novas configurações. Por exemplo, se você quiser definir uma nova senha para a conta de usuário remoto ou se a conta tiver expirado. Isso é necessário em implantações existentes que têm a extensão de área de trabalho remota habilitada. Para novas implantações, você pode simplesmente aplicar a extensão diretamente.

Para remover a extensão da área de trabalho remota da implantação, você pode usar o cmdlet [Remove-AzureServiceRemoteDesktopExtension](/powershell/module/servicemanagement/azure/remove-azureserviceremotedesktopextension?view=azuresmps-3.7.0) . Opcionalmente, você também pode especificar o slot de implantação e a função da qual deseja remover a extensão da área de trabalho remota.

```powershell
Remove-AzureServiceRemoteDesktopExtension -ServiceName $servicename -UninstallConfiguration
```

> [!NOTE]
> Para remover completamente a configuração de extensão, você deve chamar o cmdlet *Remove* com o parâmetro **UninstallConfiguration** .
>
> O parâmetro **UninstallConfiguration** desinstala qualquer configuração de extensão que é aplicada ao serviço. Cada configuração de extensão é associada à configuração do serviço. Chamar o cmdlet *Remove* sem **UninstallConfiguration** desassocia a <mark>implantação</mark> da configuração de extensão, removendo efetivamente a extensão. No entanto, a configuração de extensão permanece associada ao serviço.

## <a name="additional-resources"></a>Recursos adicionais

[Como configurar um Serviços Cloud](cloud-services-how-to-configure-portal.md)


