---
title: Ativar a ligação de ambiente de trabalho remoto para uma função nos serviços Cloud do Azure com o PowerShell
description: Como configurar a aplicação do serviço cloud do azure com o PowerShell para permitir ligações de ambiente de trabalho remotas
services: cloud-services
documentationcenter: ''
author: jpconnock
manager: timlt
editor: ''
ms.assetid: bf2f70a4-20dc-4302-a91a-38cd7a2baa62
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/18/2017
ms.author: jeconnoc
ms.openlocfilehash: 43ccc8e53c30219630ad10ee66a4db38656818e6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60525383"
---
# <a name="enable-remote-desktop-connection-for-a-role-in-azure-cloud-services-using-powershell"></a>Ativar a ligação de ambiente de trabalho remoto para uma função nos serviços Cloud do Azure com o PowerShell

> [!div class="op_single_selector"]
> * [Portal do Azure](cloud-services-role-enable-remote-desktop-new-portal.md)
> * [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md)
> * [Visual Studio](cloud-services-role-enable-remote-desktop-visual-studio.md)

Ambiente de trabalho remoto permite-lhe aceder a área de trabalho de uma função em execução no Azure. Pode utilizar uma ligação de ambiente de trabalho remoto para resolver problemas e diagnosticar problemas com a sua aplicação durante a execução.

Este artigo descreve como ativar o ambiente de trabalho remoto em suas funções de serviço em nuvem com o PowerShell. Ver [como instalar e configurar o Azure PowerShell](/powershell/azure/overview) para os pré-requisitos necessários para este artigo. PowerShell utiliza a extensão de ambiente de trabalho remoto para que possa permitir o ambiente de trabalho remoto após a aplicação é implementada.

## <a name="configure-remote-desktop-from-powershell"></a>Configurar o ambiente de trabalho remoto do PowerShell
O [Set-AzureServiceRemoteDesktopExtension](/powershell/module/servicemanagement/azure/set-azureserviceremotedesktopextension?view=azuresmps-3.7.0) cmdlet permite-lhe ativar o ambiente de trabalho remoto em funções especificadas ou todas as funções da sua implementação do serviço de nuvem. O cmdlet permite-lhe especificar o nome de utilizador e palavra-passe para o usuário de área de trabalho remoto através da *credencial* parâmetro que aceita um objeto PSCredential.

Se estiver a utilizar PowerShell interativamente, é possível definir facilmente o objeto PSCredential ao chamar o [Get-Credentials](https://technet.microsoft.com/library/hh849815.aspx) cmdlet.

```powershell
$remoteusercredentials = Get-Credential
```

Este comando apresenta uma caixa de diálogo que permite que insira o nome de utilizador e palavra-passe para o usuário remoto de forma segura.

Uma vez que a ajuda do PowerShell em cenários de automação, também pode configurar o **PSCredential** objeto de uma forma que não exige interação do usuário. Em primeiro lugar, terá de configurar uma palavra-passe segura. Começar com a especificação de uma palavra-passe de texto sem formatação convertê-lo para utilizar uma cadeia segura [ConvertTo-SecureString](https://technet.microsoft.com/library/hh849818.aspx). Em seguida precisa converter essa cadeia de caracteres segura numa cadeia padrão encriptada utilizando [ConvertFrom-SecureString](https://technet.microsoft.com/library/hh849814.aspx). Agora pode salvar essa cadeia de caracteres padrão criptografada num arquivo usando [Set-Content](https://technet.microsoft.com/library/ee176959.aspx).

Também pode criar um ficheiro de palavra-passe segura para que não precisa escrever a palavra-passe sempre. Além disso, um arquivo de palavra-passe segura é melhor do que um ficheiro de texto sem formatação. Utilize o PowerShell seguinte para criar um ficheiro de palavra-passe segura:

```powershell
ConvertTo-SecureString -String "Password123" -AsPlainText -Force | ConvertFrom-SecureString | Set-Content "password.txt"
```

> [!IMPORTANT]
> Ao definir a palavra-passe, certifique-se de que cumpre os [requisitos de complexidade](https://technet.microsoft.com/library/cc786468.aspx).

Para criar o objeto de credencial a partir do ficheiro de palavra-passe segura, tem de ler o conteúdo do ficheiro e convertê-los a usar uma cadeia segura [ConvertTo-SecureString](https://technet.microsoft.com/library/hh849818.aspx).

O [Set-AzureServiceRemoteDesktopExtension](/powershell/module/servicemanagement/azure/set-azureserviceremotedesktopextension?view=azuresmps-3.7.0) cmdlet também aceita um *expiração* parâmetro, que especifica um **DateTime** em que a conta de utilizador expira. Por exemplo, pode definir a conta para expirar alguns dias a partir da data e hora atuais.

Este exemplo do PowerShell mostra como definir a extensão de ambiente de trabalho remoto num serviço em nuvem:

```powershell
$servicename = "cloudservice"
$username = "RemoteDesktopUser"
$securepassword = Get-Content -Path "password.txt" | ConvertTo-SecureString
$expiry = $(Get-Date).AddDays(1)
$credential = New-Object System.Management.Automation.PSCredential $username,$securepassword
Set-AzureServiceRemoteDesktopExtension -ServiceName $servicename -Credential $credential -Expiration $expiry
```
Opcionalmente, também pode especificar o bloco de implementação e as funções que pretende ativar o ambiente de trabalho remoto em. Se estes parâmetros não forem especificados, o cmdlet permite que o ambiente de trabalho remoto em todas as funções no **produção** bloco de implementação.

A extensão de ambiente de trabalho remoto está associada uma implementação. Se criar uma nova implementação para o serviço, tem de ativar o ambiente de trabalho remoto dessa implementação. Se pretender sempre tem o ambiente de trabalho remoto ativado, deve considerar integrar os scripts do PowerShell no seu fluxo de trabalho de implantação.

## <a name="remote-desktop-into-a-role-instance"></a>Ambiente de trabalho remoto numa instância de função

O [Get-AzureRemoteDesktopFile](/powershell/module/servicemanagement/azure/get-azureremotedesktopfile?view=azuresmps-3.7.0) cmdlet seja utilizado remota para ambiente de trabalho numa instância de função específica do seu serviço cloud. Pode utilizar o *LocalPath* parâmetro para transferir o RDP de ficheiros localmente. Ou pode utilizar o *inicie* parâmetro para iniciar diretamente a caixa de diálogo de conexão de área de trabalho remoto para acessar a instância de função do serviço de nuvem.

```powershell
Get-AzureRemoteDesktopFile -ServiceName $servicename -Name "WorkerRole1_IN_0" -Launch
```

## <a name="check-if-remote-desktop-extension-is-enabled-on-a-service"></a>Verifique se a extensão de ambiente de trabalho remoto está ativado num serviço

O [Get-AzureServiceRemoteDesktopExtension](/powershell/module/servicemanagement/azure/get-azureremotedesktopfile?view=azuresmps-3.7.0) cmdlet apresenta que ambiente de trabalho remoto está ativado ou desativado na implementação de serviço. O cmdlet devolve o nome de utilizador para o usuário de área de trabalho remoto e as funções que a extensão de área de trabalho remota está ativada para. Por predefinição, isto acontece na ranhura de implementação e pode optar por utilizar o bloco de teste em vez disso.

```powershell
Get-AzureServiceRemoteDesktopExtension -ServiceName $servicename
```

## <a name="remove-remote-desktop-extension-from-a-service"></a>Remover a extensão de ambiente de trabalho remoto de um serviço

Se já tiver ativado a extensão de área de trabalho remota numa implementação e tem de atualizar as definições de ambiente de trabalho remoto, primeiro de remover a extensão. E ativá-la novamente com as novas definições. Por exemplo, se pretender definir uma nova palavra-passe para a conta de utilizador remoto ou a conta expirou. Isso é necessário nas Implantações existentes que tenham a extensão de área de trabalho remota ativada. Para novas Implantações, pode simplesmente aplicar a extensão diretamente.

Para remover a extensão de área de trabalho remota da implementação, pode utilizar o [Remove-AzureServiceRemoteDesktopExtension](/powershell/module/servicemanagement/azure/remove-azureserviceremotedesktopextension?view=azuresmps-3.7.0) cmdlet. Opcionalmente, também pode especificar o bloco de implementação e a função a partir do qual pretende remover a extensão de área de trabalho remota.

```powershell
Remove-AzureServiceRemoteDesktopExtension -ServiceName $servicename -UninstallConfiguration
```

> [!NOTE]
> Para remover completamente a configuração da extensão, deve chamar o *Remova* cmdlet com o **UninstallConfiguration** parâmetro.
>
> O **UninstallConfiguration** parâmetro desinstala qualquer configuração de extensão que é aplicada ao serviço. Cada configuração de extensão está associada com a configuração do serviço. Chamar o *Remova* cmdlet sem **UninstallConfiguration** disassociates o <mark>implementação</mark> da configuração de extensão, portanto, removendo efetivamente o extensão. No entanto, a configuração de extensão que permanece associada com o serviço.

## <a name="additional-resources"></a>Recursos adicionais

[Como configurar um Serviços Cloud](cloud-services-how-to-configure-portal.md)