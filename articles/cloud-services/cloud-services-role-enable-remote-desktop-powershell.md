---
title: Use powerShell para ativar o ambiente de trabalho remoto para uma função
titleSuffix: Azure Cloud Services
description: Como configurar a sua aplicação de serviço em nuvem azul utilizando o PowerShell para permitir ligações remotas de ambiente de trabalho
services: cloud-services
documentationcenter: ''
author: tgore03
ms.service: cloud-services
ms.topic: article
ms.date: 07/18/2017
ms.author: tagore
ms.openlocfilehash: e4e8dca6c5359e865e6a17fc47fe47802b0ee9e6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75386124"
---
# <a name="enable-remote-desktop-connection-for-a-role-in-azure-cloud-services-using-powershell"></a>Ativar a ligação remota para desktop para uma função nos serviços de nuvem azure usando powerShell

> [!div class="op_single_selector"]
> * [Portal do Azure](cloud-services-role-enable-remote-desktop-new-portal.md)
> * [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md)
> * [Visual Studio](cloud-services-role-enable-remote-desktop-visual-studio.md)

O Remote Desktop permite-lhe aceder ao ambiente de trabalho de uma função em funcionamento no Azure. Pode utilizar uma ligação Remote Desktop para resolver problemas e diagnosticar problemas com a sua aplicação enquanto esta estiver em execução.

Este artigo descreve como ativar o ambiente de trabalho remoto nas suas Funções de Serviço cloud utilizando o PowerShell. Veja como instalar e configurar o [Azure PowerShell](/powershell/azure/overview) para os pré-requisitos necessários para este artigo. A PowerShell utiliza a extensão remota do ambiente de trabalho para que possa ativar o Ambiente de Trabalho Remoto após a implementação da aplicação.

## <a name="configure-remote-desktop-from-powershell"></a>Configure ambiente de trabalho remoto a partir de PowerShell
O [cmdlet set-AzureServiceRemoteDesktopExtension](/powershell/module/servicemanagement/azure/set-azureserviceremotedesktopextension?view=azuresmps-3.7.0) permite-lhe ativar o Ambiente de Trabalho Remoto em funções especificadas ou em todas as funções da implementação do seu serviço na nuvem. O cmdlet permite especificar o nome de utilizador e palavra-passe para o utilizador remoto do ambiente de trabalho através do parâmetro *Credencial* que aceita um objeto PSCredential.

Se estiver a utilizar o PowerShell interactivamente, pode configurar facilmente o objeto PSCredential chamando o cmdlet [Get-Credentials.](https://technet.microsoft.com/library/hh849815.aspx)

```powershell
$remoteusercredentials = Get-Credential
```

Este comando apresenta uma caixa de diálogo que lhe permite introduzir o nome de utilizador e a palavra-passe para o utilizador remoto de forma segura.

Uma vez que o PowerShell ajuda em cenários de automação, também pode configurar o objeto **PSCredential** de uma forma que não requer interação do utilizador. Primeiro, tens de configurar uma senha segura. Comece por especificar uma palavra-passe de texto simples convertê-la numa cadeia segura utilizando [convertto-secureString](https://technet.microsoft.com/library/hh849818.aspx). Em seguida, precisa converter esta cadeia segura numa cadeia padrão encriptada utilizando [convertfrom-SecureString](https://technet.microsoft.com/library/hh849814.aspx). Agora pode guardar esta cadeia padrão encriptada para um ficheiro utilizando [o Set-Content](https://technet.microsoft.com/library/ee176959.aspx).

Também pode criar um ficheiro de senha seguro para que não tenha de escrever sempre a palavra-passe. Além disso, um ficheiro de senha seguro é melhor do que um ficheiro de texto simples. Utilize o seguinte PowerShell para criar um ficheiro de senha seguro:

```powershell
ConvertTo-SecureString -String "Password123" -AsPlainText -Force | ConvertFrom-SecureString | Set-Content "password.txt"
```

> [!IMPORTANT]
> Ao definir a palavra-passe, certifique-se de que satisfaz os [requisitos](https://technet.microsoft.com/library/cc786468.aspx)de complexidade .

Para criar o objeto credencial a partir do ficheiro de palavra-passe segura, tem de ler o conteúdo do ficheiro e convertê-los de volta para uma cadeia segura utilizando [o ConvertTo-SecureString](https://technet.microsoft.com/library/hh849818.aspx).

O [cmdlet set-AzureServiceRemoteDesktopExtension](/powershell/module/servicemanagement/azure/set-azureserviceremotedesktopextension?view=azuresmps-3.7.0) também aceita um parâmetro de *expiração,* que especifica um Tempo de **Data** no qual a conta de utilizador expira. Por exemplo, pode definir a conta para expirar a poucos dias da data e hora atuais.

Este exemplo powerShell mostra-lhe como definir a extensão do ambiente de trabalho remoto num serviço na nuvem:

```powershell
$servicename = "cloudservice"
$username = "RemoteDesktopUser"
$securepassword = Get-Content -Path "password.txt" | ConvertTo-SecureString
$expiry = $(Get-Date).AddDays(1)
$credential = New-Object System.Management.Automation.PSCredential $username,$securepassword
Set-AzureServiceRemoteDesktopExtension -ServiceName $servicename -Credential $credential -Expiration $expiry
```
Também pode especificar opcionalmente a ranhura de implementação e as funções que pretende ativar no ambiente de trabalho remoto. Se estes parâmetros não forem especificados, o cmdlet permite um ambiente de trabalho remoto em todas as funções na ranhura de implantação da **Produção.**

A extensão Remote Desktop está associada a uma implementação. Se criar uma nova implementação para o serviço, tem de ativar o ambiente de trabalho remoto nessa implementação. Se sempre quiser ter um ambiente de trabalho remoto ativado, então deve considerar integrar os scripts PowerShell no seu fluxo de trabalho de implementação.

## <a name="remote-desktop-into-a-role-instance"></a>Ambiente de Trabalho Remoto em uma instância de papel

O cmdlet [Get-AzureRemoteDesktopFile](/powershell/module/servicemanagement/azure/get-azureremotedesktopfile?view=azuresmps-3.7.0) é utilizado para remotamente desktop em uma instância de função específica do seu serviço na nuvem. Pode utilizar o parâmetro *LocalPath* para descarregar o ficheiro RDP localmente. Ou pode utilizar o parâmetro *de lançamento* para lançar diretamente o diálogo de Ligação remota de ambiente de trabalho para aceder à função de serviço na nuvem.

```powershell
Get-AzureRemoteDesktopFile -ServiceName $servicename -Name "WorkerRole1_IN_0" -Launch
```

## <a name="check-if-remote-desktop-extension-is-enabled-on-a-service"></a>Verifique se a extensão do Ambiente de Trabalho Remoto está ativada num serviço

O [cmdlet get-AzureServiceRemoteDesktopExtension](/powershell/module/servicemanagement/azure/get-azureremotedesktopfile?view=azuresmps-3.7.0) mostra que o ambiente de trabalho remoto está ativado ou desativado numa implementação de serviço. O cmdlet devolve o nome de utilizador para o utilizador remoto de ambiente de trabalho e as funções para as as que a extensão remota do ambiente de trabalho está ativada. Por predefinição, isto acontece na ranhura de implantação e pode optar por utilizar a ranhura de encenação.

```powershell
Get-AzureServiceRemoteDesktopExtension -ServiceName $servicename
```

## <a name="remove-remote-desktop-extension-from-a-service"></a>Remova a extensão do Ambiente de Trabalho Remoto de um serviço

Se já ativou a extensão remota do ambiente de trabalho numa implementação e precisa de atualizar as definições remotas de ambiente de trabalho, retire primeiro a extensão. E ative-o novamente com as novas definições. Por exemplo, se pretender definir uma nova senha para a conta de utilizador remota, ou a conta expirou. Ao fazê-lo é necessário em implementações existentes que tenham a extensão remota do ambiente de trabalho ativada. Para novas implementações, pode simplesmente aplicar a extensão diretamente.

Para remover a extensão remota do ambiente de trabalho da implementação, pode utilizar o cmdlet [remove-AzureServiceRemoteDesktopExtension.](/powershell/module/servicemanagement/azure/remove-azureserviceremotedesktopextension?view=azuresmps-3.7.0) Também pode especificar opcionalmente a ranhura de implementação e a função a partir da qual pretende remover a extensão remota do ambiente de trabalho.

```powershell
Remove-AzureServiceRemoteDesktopExtension -ServiceName $servicename -UninstallConfiguration
```

> [!NOTE]
> Para remover completamente a configuração da extensão, deve ligar para *remover* cmdlet com o parâmetro **Desinstalação Configuração.**
>
> O parâmetro **Desinstalação Configuração desinstala** qualquer configuração de extensão que seja aplicada ao serviço. Todas as configurações de extensão estão associadas à configuração do serviço. Chamar o cmdlet *de remoção* sem **desinstalar A Configuração** dissocia a <mark>implementação</mark> da configuração da extensão, removendo assim eficazmente a extensão. No entanto, a configuração da extensão permanece associada ao serviço.

## <a name="additional-resources"></a>Recursos adicionais

[Como configurar um Serviços Cloud](cloud-services-how-to-configure-portal.md)


