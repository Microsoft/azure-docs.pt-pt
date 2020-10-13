---
title: Utilize o PowerShell para ativar o ambiente de trabalho remoto para uma função
titleSuffix: Azure Cloud Services
description: Como configurar a sua aplicação de serviço em nuvem azul usando o PowerShell para permitir ligações remotas de ambiente de trabalho
services: cloud-services
documentationcenter: ''
author: tgore03
ms.service: cloud-services
ms.topic: article
ms.date: 07/18/2017
ms.author: tagore
ms.openlocfilehash: 13d5be5b1e0bcdf84001f21a45364296d67a5da4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87013902"
---
# <a name="enable-remote-desktop-connection-for-a-role-in-azure-cloud-services-using-powershell"></a>Ativar a ligação remota de ambiente de trabalho para uma função em serviços de nuvem Azure usando PowerShell

> [!div class="op_single_selector"]
> * [Portal do Azure](cloud-services-role-enable-remote-desktop-new-portal.md)
> * [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md)
> * [Visual Studio](cloud-services-role-enable-remote-desktop-visual-studio.md)

O Ambiente de Trabalho Remoto permite-lhe aceder ao ambiente de trabalho de uma função em execução no Azure. Pode utilizar uma ligação remote desktop para resolver problemas e diagnosticar problemas com a sua aplicação enquanto está em funcionamento.

Este artigo descreve como ativar o ambiente de trabalho remoto nas suas Funções de Serviço de Cloud utilizando o PowerShell. Ver [Como instalar e configurar a Azure PowerShell](/powershell/azure/) para os pré-requisitos necessários para este artigo. O PowerShell utiliza a extensão de ambiente de trabalho remoto para que possa ativar o Ambiente de Trabalho Remoto após a implementação da aplicação.

## <a name="configure-remote-desktop-from-powershell"></a>Configurar o ambiente de trabalho remoto da PowerShell
O [cmdlet Set-AzureServiceRemoteDesktopExtension](/powershell/module/servicemanagement/azure.service/set-azureserviceremotedesktopextension?view=azuresmps-3.7.0) permite-lhe ativar o Ambiente de Trabalho Remoto em funções especificadas ou em todas as funções da sua implementação de serviço na nuvem. O cmdlet permite especificar o nome de utilizador e a palavra-passe para o utilizador remoto do ambiente de trabalho através do parâmetro *Credencial* que aceita um objeto PSCredential.

Se estiver a utilizar o PowerShell interativamente, pode facilmente definir o objeto PSCredential chamando o cmdlet [Get-Credentials.](https://technet.microsoft.com/library/hh849815.aspx)

```powershell
$remoteusercredentials = Get-Credential
```

Este comando apresenta uma caixa de diálogo que lhe permite introduzir o nome de utilizador e a palavra-passe para o utilizador remoto de forma segura.

Uma vez que o PowerShell ajuda em cenários de automação, também pode configurar o objeto **PSCredential** de uma forma que não exija interação do utilizador. Primeiro, tens de configurar uma senha segura. Começa por especificar uma palavra-passe de texto simples convertê-la numa cadeia segura utilizando [o ConvertTo-SecureString](https://technet.microsoft.com/library/hh849818.aspx). Em seguida, você precisa converter esta cadeia de segurança em uma cadeia padrão encriptada usando [ConvertFrom-SecureString](https://technet.microsoft.com/library/hh849814.aspx). Agora pode guardar esta cadeia padrão encriptada para um ficheiro utilizando [o Set-Content](https://technet.microsoft.com/library/ee176959.aspx).

Também pode criar um ficheiro de senha seguro para que não tenha de digitar a palavra-passe sempre. Além disso, um ficheiro de senha seguro é melhor do que um ficheiro de texto simples. Utilize o seguinte PowerShell para criar um ficheiro de senha seguro:

```powershell
ConvertTo-SecureString -String "Password123" -AsPlainText -Force | ConvertFrom-SecureString | Set-Content "password.txt"
```

> [!IMPORTANT]
> Ao definir a palavra-passe, certifique-se de que satisfaz os [requisitos de complexidade](https://technet.microsoft.com/library/cc786468.aspx).

Para criar o objeto credencial a partir do ficheiro de senha segura, tem de ler o conteúdo do ficheiro e convertê-los de volta numa cadeia segura utilizando [o ConvertTo-SecureString](https://technet.microsoft.com/library/hh849818.aspx).

O [Set-AzureServiceRemoteDesktopExtension](/powershell/module/servicemanagement/azure.service/set-azureserviceremotedesktopextension?view=azuresmps-3.7.0) cmdlet também aceita um parâmetro *de expiração,* que especifica uma **DataTime** em que a conta de utilizador expira. Por exemplo, pode definir a conta para expirar a poucos dias da data e hora em vigor.

Este exemplo powerShell mostra-lhe como definir a extensão de ambiente de trabalho remoto num serviço de nuvem:

```powershell
$servicename = "cloudservice"
$username = "RemoteDesktopUser"
$securepassword = Get-Content -Path "password.txt" | ConvertTo-SecureString
$expiry = $(Get-Date).AddDays(1)
$credential = New-Object System.Management.Automation.PSCredential $username,$securepassword
Set-AzureServiceRemoteDesktopExtension -ServiceName $servicename -Credential $credential -Expiration $expiry
```
Também pode especificar opcionalmente a ranhura de implementação e as funções que pretende ativar o ambiente de trabalho remoto. Se estes parâmetros não forem especificados, o cmdlet permite o ambiente de trabalho remoto em todas as funções na ranhura de implantação de **Produção.**

A extensão remote desktop está associada a uma implementação. Se criar uma nova implementação para o serviço, tem de ativar o ambiente de trabalho remoto nessa implementação. Se sempre pretender ter um ambiente de trabalho remoto ativado, então deve considerar a integração dos scripts PowerShell no seu fluxo de trabalho de implementação.

## <a name="remote-desktop-into-a-role-instance"></a>Ambiente de trabalho remoto em uma instância de papel

O [cmdlet Get-AzureRemoteDesktopFile](/powershell/module/servicemanagement/azure.service/get-azureremotedesktopfile?view=azuresmps-3.7.0) é utilizado para evitar ambientes de trabalho numa instância específica do seu serviço na nuvem. Pode utilizar o parâmetro *LocalPath* para descarregar o ficheiro RDP localmente. Ou pode utilizar o parâmetro *De lançamento* para lançar diretamente o diálogo de Ligação ao Ambiente de Trabalho Remoto para aceder à instância de função de serviço de nuvem.

```powershell
Get-AzureRemoteDesktopFile -ServiceName $servicename -Name "WorkerRole1_IN_0" -Launch
```

## <a name="check-if-remote-desktop-extension-is-enabled-on-a-service"></a>Verifique se a extensão do Ambiente de Trabalho Remoto está ativada num serviço

O [cmdlet Get-AzureServiceRemoteDesktopExtension](/powershell/module/servicemanagement/azure.service/get-azureremotedesktopfile?view=azuresmps-3.7.0) mostra que o ambiente de trabalho remoto está ativado ou desativado numa implementação de serviço. O cmdlet devolve o nome de utilizador para o utilizador remoto do ambiente de trabalho e as funções para as seguintes. Por predefinição, isto acontece na ranhura de implementação e pode optar por utilizar a ranhura de paragem.

```powershell
Get-AzureServiceRemoteDesktopExtension -ServiceName $servicename
```

## <a name="remove-remote-desktop-extension-from-a-service"></a>Remover extensão de ambiente de trabalho remoto de um serviço

Se já ativou a extensão remota do ambiente de trabalho numa implementação e precisa de atualizar as definições remotas do ambiente de trabalho, remova primeiro a extensão. E ative-o novamente com as novas definições. Por exemplo, se pretender definir uma nova palavra-passe para a conta de utilizador remoto, ou a conta tiver expirado. Isto é necessário nas implementações existentes que tenham a extensão remota do ambiente de trabalho ativada. Para novas implementações, pode simplesmente aplicar a extensão diretamente.

Para remover a extensão de ambiente de trabalho remoto da implementação, pode utilizar o [cmdlet remove-AzureServiceRemoteDesktopExtension.](/powershell/module/servicemanagement/azure.service/remove-azureserviceremotedesktopextension?view=azuresmps-3.7.0) Também pode especificar opcionalmente a ranhura de implementação e a função a partir da qual pretende remover a extensão remota do ambiente de trabalho.

```powershell
Remove-AzureServiceRemoteDesktopExtension -ServiceName $servicename -UninstallConfiguration
```

> [!NOTE]
> Para remover completamente a configuração da extensão, deve chamar o cmdlet *de remoção* com o parâmetro **DesinstalarConfiguration.**
>
> O parâmetro **DesinstalarConfiguration** desinstala qualquer configuração de extensão que seja aplicada ao serviço. Todas as configurações de extensão estão associadas à configuração do serviço. Chamar o cmdlet *remover* sem **desinstalar a configuração** desassocia a <mark>implantação</mark> da configuração da extensão, removendo assim eficazmente a extensão. No entanto, a configuração da extensão permanece associada ao serviço.

## <a name="additional-resources"></a>Recursos adicionais

[Como configurar um Serviços Cloud](cloud-services-how-to-configure-portal.md)


