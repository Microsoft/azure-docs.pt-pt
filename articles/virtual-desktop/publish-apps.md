---
title: Publicar aplicativos incorporados no Windows Virtual Desktop - Azure
description: Como publicar aplicativos incorporados no Windows Virtual Desktop.
author: Heidilohr
ms.topic: how-to
ms.date: 04/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 32441a7da0e079a2de2fbd6906184c518d60e4a5
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102562865"
---
# <a name="publish-built-in-apps-in-windows-virtual-desktop"></a>Publicar aplicativos incorporados no Windows Virtual Desktop

>[!IMPORTANT]
>Este conteúdo aplica-se ao Windows Virtual Desktop com objetos de ambiente de trabalho virtuais do Windows, gestor de recursos do Azure. Se estiver a utilizar o Windows Virtual Desktop (clássico) sem objetos Azure Resource Manager, consulte [este artigo](./virtual-desktop-fall-2019/publish-apps-2019.md).

Este artigo irá dizer-lhe como publicar aplicações no seu ambiente de ambiente de trabalho virtual do Windows.

## <a name="publish-built-in-apps"></a>Publicar aplicações incorporadas

Para publicar uma aplicação incorporada:

1. Ligue-se a uma das máquinas virtuais da sua piscina de anfitriões.
2. Obtenha o **Nome Embalado** da app que pretende publicar seguindo as instruções [deste artigo.](/powershell/module/appx/get-appxpackage)
3. Por fim, executar o seguinte cmdlet com `<PackageFamilyName>` substituído pelo **Nome Demómilia Pacote** que encontrou no passo anterior:

   ```powershell
   New-AzWvdApplication -Name <applicationname> -ResourceGroupName <resourcegroupname> -ApplicationGroupName <appgroupname> -FilePath "shell:appsFolder\<PackageFamilyName>!App" -CommandLineSetting <Allow|Require|DoNotAllow> -IconIndex 0 -IconPath <iconpath> -ShowInPortal:$true
   ```

>[!NOTE]
> O Windows Virtual Desktop só suporta a publicação de aplicações com locais de instalação que começam com `C:\Program Files\WindowsApps` .

## <a name="update-app-icons"></a>Atualizar ícones de aplicativos

Depois de publicar uma aplicação, terá o ícone de aplicação padrão do Windows em vez da sua imagem de ícone regular. Para alterar o ícone para o seu ícone regular, coloque a imagem do ícone que pretende numa partilha de rede. Os formatos de imagem suportados são PNG, BMP, GIF, JPG, JPEG e ICO.

## <a name="publish-microsoft-edge"></a>Publicar Microsoft Edge

O processo que utiliza para publicar o Microsoft Edge é um pouco diferente do processo de publicação de outras apps. Para publicar o Microsoft Edge com a página inicial predefinida, execute este cmdlet:

```powershell
New-AzWvdApplication -Name -ResourceGroupName -ApplicationGroupName -FilePath "shell:Appsfolder\Microsoft.MicrosoftEdge_8wekyb3d8bbwe!MicrosoftEdge" -CommandLineSetting <Allow|Require|DoNotAllow> -iconPath "C:\Windows\SystemApps\Microsoft.MicrosoftEdge_8wekyb3d8bbwe\microsoftedge.exe" -iconIndex 0 -ShowInPortal:$true
```

## <a name="next-steps"></a>Passos seguintes

- Saiba como configurar feeds para organizar a forma como as aplicações são apresentadas para os utilizadores no [Feed Personalizado para utilizadores do Windows Virtual Desktop](customize-feed-for-virtual-desktop-users.md).
- Saiba mais sobre a funcionalidade de anexação de aplicações MSIX no [anexo de aplicações set up MSIX](app-attach.md).

