---
title: Personalize o feed para utilizadores do Windows Virtual Desktop - Azure
description: Como personalizar o feed para utilizadores do Windows Virtual Desktop com cmdlets PowerShell.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 08/29/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 99c63fd04a40b1a4e591f5ad42d8f776e8e5b67c
ms.sourcegitcommit: 6fd28c1e5cf6872fb28691c7dd307a5e4bc71228
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/23/2020
ms.locfileid: "85208506"
---
# <a name="customize-feed-for-windows-virtual-desktop-users"></a>Personalizar o feed para utilizadores do Windows Virtual Desktop

>[!IMPORTANT]
>Este conteúdo aplica-se à atualização primavera 2020 com objetos de desktop virtual do Windows Manager do Azure. Se estiver a utilizar o desbloqueio virtual do Windows Desktop Fall 2019 sem objetos Azure Resource Manager, consulte [este artigo](./virtual-desktop-fall-2019/customize-feed-virtual-desktop-users-2019.md).
>
> A atualização Virtual Desktop Spring 2020 do Windows está atualmente em pré-visualização pública. Esta versão de pré-visualização é fornecida sem um acordo de nível de serviço, e não recomendamos a sua utilização para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.
> Para obter mais informações, consulte [termos de utilização suplementares para pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Pode personalizar o feed para que os recursos de ambiente de trabalho remoto e remoto apareçam de forma reconhecível para os seus utilizadores.

## <a name="prerequisites"></a>Pré-requisitos

Este artigo pressupõe que já descarregou e instalou o módulo Windows Virtual Desktop PowerShell. Caso não o tenha, siga as instruções do [módulo PowerShell](powershell-module.md).

## <a name="customize-the-display-name-for-a-remoteapp"></a>Personalize o nome do ecrã para um RemoteApp

Pode alterar o nome de exibição para um RemoteApp publicado definindo o nome amigável. Por predefinição, o nome amigável é o mesmo que o nome do programa RemoteApp.

Para obter uma lista de RemoteApps publicados para um grupo de aplicações, execute o seguinte cmdlet PowerShell:

```powershell
Get-AzWvdApplication -ResourceGroupName <resourcegroupname> -ApplicationGroupName <appgroupname>
```

Para atribuir um nome amigável a um RemoteApp, execute o seguinte cmdlet com os parâmetros necessários:

```powershell
Update-AzWvdApplication -ResourceGroupName <resourcegroupname> -ApplicationGroupName <appgroupname> -Name <applicationname> -FriendlyName <newfriendlyname>
```

Por exemplo, digamos que recuperou as aplicações atuais com o seguinte exemplo cmdlet:

```powershell
Get-AzWvdApplication -ResourceGroupName 0301RG -ApplicationGroupName 0301RAG | format-list
```

A saída seria assim:

```powershell
CommandLineArgument :
CommandLineSetting  : DoNotAllow
Description         :
FilePath            : C:\Program Files\Windows NT\Accessories\wordpad.exe
FriendlyName        : Microsoft Word
IconContent         : {0, 0, 1, 0…}
IconHash            : --iom0PS6XLu-EMMlHWVW3F7LLsNt63Zz2K10RE0_64
IconIndex           : 0
IconPath            : C:\Program Files\Windows NT\Accessories\wordpad.exe
Id                  : /subscriptions/<subid>/resourcegroups/0301RG/providers/Microsoft.DesktopVirtualization/applicationgroups/0301RAG/applications/Microsoft Word
Name                : 0301RAG/Microsoft Word
ShowInPortal        : False
Type                : Microsoft.DesktopVirtualization/applicationgroups/applications
```
Para atualizar o nome amigável, execute este cmdlet:

```powershell
Update-AzWvdApplication -GroupName 0301RAG -Name "Microsoft Word" -FriendlyName "WordUpdate" -ResourceGroupName 0301RG -IconIndex 0 -IconPath "C:\Program Files\Windows NT\Accessories\wordpad.exe" -ShowInPortal:$true -CommandLineSetting DoNotallow -FilePath "C:\Program Files\Windows NT\Accessories\wordpad.exe"
```

Para confirmar que atualizou com sucesso o nome amigável, execute este cmdlet:

```powershell
Get-AzWvdApplication -ResourceGroupName 0301RG -ApplicationGroupName 0301RAG | format-list FriendlyName
```

O cmdlet deve dar-lhe a seguinte saída:

```powershell
FriendlyName        : WordUpdate
```

## <a name="customize-the-display-name-for-a-remote-desktop"></a>Personalize o nome do visor para um ambiente de trabalho remoto

Pode alterar o nome de visualização para um ambiente de trabalho remoto publicado definindo um nome amigável. Se criou manualmente um grupo de aplicações de anfitrião e de mesa através do PowerShell, o nome padrão amigável é "Session Desktop". Se criou um grupo de aplicações de pool e desktop de anfitriões através do modelo GitHub Azure Resource Manager ou da oferta do Azure Marketplace, o nome padrão amigável é o mesmo que o nome da piscina do anfitrião.

Para recuperar o recurso de ambiente de trabalho remoto, execute o seguinte cmdlet PowerShell:

```powershell
Get-AzWvdDesktop -ResourceGroupName <resourcegroupname> -ApplicationGroupName <appgroupname> -Name <applicationname>
```

Para atribuir um nome amigável ao recurso de ambiente de trabalho remoto, execute o seguinte cmdlet PowerShell:

```powershell
Update-AzWvdDesktop -ResourceGroupName <resourcegroupname> -ApplicationGroupName <appgroupname> -Name <applicationname> -FriendlyName <newfriendlyname>
```

## <a name="customize-a-display-name-in-azure-portal"></a>Personalize um nome de exibição no portal Azure

Pode alterar o nome de exibição para um ambiente de trabalho remoto publicado, definindo um nome amigável utilizando o portal Azure.

1. Inicie sessão no portal do Azure em <https://portal.azure.com>.

2. Procure por **Windows Virtual Desktop**.

3. Em Serviços, selecione **Windows Virtual Desktop**.

4. Na página de Ambiente de Trabalho Virtual do Windows, selecione **grupos de aplicações** no lado esquerdo do ecrã e, em seguida, selecione o nome do grupo de aplicações que pretende editar.

5. Selecione **Aplicações** no menu no lado esquerdo do ecrã.

6. Selecione a aplicação que pretende atualizar e, em seguida, introduza um novo **nome de Exibição**.

7. Selecione **Guardar**. A aplicação que editou deve agora apresentar o nome atualizado.

## <a name="next-steps"></a>Passos seguintes

Agora que personalizou o feed para os utilizadores, pode iniciar sedução num cliente do Windows Virtual Desktop para o testar. Para tal, continue a ligar ao Windows Virtual Desktop Como-tos:

 * [Ligue-se ao Windows 10 ou ao Windows 7](connect-windows-7-and-10.md)
 * [Ligar com o cliente web](connect-web.md)
 * [Ligar ao cliente Android](connect-android.md)
 * [Ligar ao cliente de iOS](connect-ios.md)
 * [Ligar ao cliente de macOS](connect-macos.md)
