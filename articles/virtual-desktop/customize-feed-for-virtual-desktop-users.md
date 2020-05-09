---
title: Personalize o feed para utilizadores do Windows Virtual Desktop - Azure
description: Como personalizar o feed para utilizadores do Windows Virtual Desktop com cmdlets PowerShell.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 961fadfff0147d8c5258fa5acf31d8b0649ea12a
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/30/2020
ms.locfileid: "82612899"
---
# <a name="customize-feed-for-windows-virtual-desktop-users"></a>Personalizar o feed para utilizadores do Windows Virtual Desktop

>[!IMPORTANT]
>Este conteúdo aplica-se à atualização da primavera de 2020 com os objetos de ambiente de trabalho virtual do Gestor de Recursos Do Azure Windows. Se estiver a utilizar o lançamento do Windows Virtual Desktop Fall 2019 sem objetos do Gestor de Recursos Azure, consulte [este artigo](./virtual-desktop-fall-2019/customize-feed-virtual-desktop-users-2019.md).
>
> A atualização Do Windows Virtual Desktop Spring 2020 encontra-se atualmente em pré-visualização pública. Esta versão de pré-visualização é fornecida sem um acordo de nível de serviço, e não recomendamos usá-la para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. 
> Para mais informações, consulte [os Termos Suplementares de Utilização para pré-visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)do Microsoft Azure .

Pode personalizar o feed para que os recursos remotos e de ambiente de trabalho remoto apareçam de forma reconhecível para os seus utilizadores.

## <a name="prerequisites"></a>Pré-requisitos

Este artigo assume que já descarregou e instalou o módulo PowerShell do Windows Virtual Desktop. Se não o fez, siga as instruções no [módulo PowerShell](powershell-module.md).

## <a name="customize-the-display-name-for-a-remoteapp"></a>Personalize o nome do ecrã para um RemoteApp

Pode alterar o nome do ecrã para um RemoteApp publicado, definindo o nome amigável. Por padrão, o nome amigável é o mesmo que o nome do programa RemoteApp.

Para recuperar uma lista de RemoteApps publicados para um grupo de aplicações, execute o seguinte cmdlet PowerShell:

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

## <a name="customize-the-display-name-for-a-remote-desktop"></a>Personalize o nome do ecrã para um ambiente de trabalho remoto

Pode alterar o nome do ecrã para um ambiente de trabalho remoto publicado, definindo um nome amigável. Se criou manualmente um grupo de aplicativos de hospedagem e desktop através do PowerShell, o nome amigável padrão é "Session Desktop". Se criou um grupo de aplicativos de piscina e desktop através do modelo GitHub Azure Resource Manager ou da oferta do Azure Marketplace, o nome amigável padrão é o mesmo que o nome da piscina anfitriã.

Para recuperar o recurso remoto de ambiente de trabalho, execute o seguinte cmdlet PowerShell:

```powershell
Get-AzWvdDesktop -ResourceGroupName <resourcegroupname> -ApplicationGroupName <appgroupname> -Name <applicationname>
```

Para atribuir um nome amigável ao recurso de ambiente de trabalho remoto, execute o seguinte cmdlet PowerShell:

```powershell
Update-AzWvdDesktop -ResourceGroupName <resourcegroupname> -ApplicationGroupName <appgroupname> -Name <applicationname> -FriendlyName <newfriendlyname>
```

## <a name="customize-a-display-name-in-azure-portal"></a>Personalize um nome de exibição no portal Azure

Pode alterar o nome do ecrã para um ambiente de trabalho remoto publicado, definindo um nome amigável utilizando o portal Azure. 

1. Inicie sessão no portal do Azure em <https://portal.azure.com>. 

2. Pesquisa por **Windows Virtual Desktop**.

3. Em Serviços, selecione **Windows Virtual Desktop**. 

4. Na página do Windows Virtual Desktop, selecione **grupos de aplicação** no lado esquerdo do ecrã e, em seguida, selecione o nome do grupo de aplicações que pretende editar. 

5. Selecione **Aplicações** no menu no lado esquerdo do ecrã.

6. Selecione a aplicação que pretende atualizar e introduza um novo **nome display**. 

7. Selecione **Guardar**. A aplicação que editou deve agora apresentar o nome atualizado.

## <a name="next-steps"></a>Passos seguintes

Agora que personalizou o feed para os utilizadores, pode iniciar sessão num cliente do Windows Virtual Desktop para o testar. Para tal, continue a ligar ao Windows Virtual Desktop How-tos:
    
 * [Conecte-se com o Windows 10 ou windows 7](connect-windows-7-and-10.md)
 * [Ligar com o cliente web](connect-web.md) 
 * [Ligar ao cliente Android](connect-android.md)
 * [Ligar ao cliente de iOS](connect-ios.md)
 * [Ligar ao cliente de macOS](connect-macos.md)
