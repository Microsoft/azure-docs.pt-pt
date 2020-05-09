---
title: Publique aplicações incorporadas no Windows Virtual Desktop - Azure
description: Como publicar aplicações incorporadas no Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: a02bf514ff76f5528bc46f0a60642163c9278ebf
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/30/2020
ms.locfileid: "82615139"
---
# <a name="publish-built-in-apps-in-windows-virtual-desktop"></a>Publique aplicações incorporadas no Windows Virtual Desktop

>[!IMPORTANT]
>Este conteúdo aplica-se à versão outono 2019 que não suporta objetos de ambiente de trabalho virtual do Gestor de Recursos Do Azure. Se está a tentar gerir os objetos de ambiente de trabalho virtual do Gestor de Recursos Do Azure Windows introduzidos na atualização da primavera de 2020, consulte [este artigo](../publish-apps.md).

Este artigo dir-lhe-á como publicar aplicações no seu ambiente de ambiente de trabalho virtual do Windows.

## <a name="publish-built-in-apps"></a>Publicar aplicações incorporadas

Para publicar uma aplicação incorporada:

1. Ligue-se a uma das máquinas virtuais da sua piscina anfitriã.
2. Obtenha o **Nome família** da aplicação que pretende publicar seguindo as instruções [deste artigo](/powershell/module/appx/get-appxpackage?view=win10-ps/).
3. Por fim, execute o `<PackageFamilyName>` seguinte cmdlet com substituído pelo **PackageFamilyName** que encontrou no passo anterior:
   
   ```powershell
   New-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname> -Name <remoteappname> -FriendlyName <remoteappname> -FilePath "shell:appsFolder\<PackageFamilyName>!App"
   ```

>[!NOTE]
> O Windows Virtual Desktop apenas suporta a publicação `C:\Program Files\Windows Apps`de aplicações com instalações de locais que começam com .

## <a name="update-app-icons"></a>Atualizar ícones de aplicativos

Depois de publicar uma aplicação, terá o ícone de aplicação padrão do Windows em vez da sua imagem de ícone regular. Para alterar o ícone para o seu ícone regular, coloque a imagem do ícone que deseja numa partilha de rede. Os formatos de imagem suportados são PNG, BMP, GIF, JPG, JPEG e ICO.

## <a name="publish-microsoft-edge"></a>Publicar Microsoft Edge

O processo que utiliza para publicar o Microsoft Edge é um pouco diferente do processo de publicação de outras aplicações. Para publicar o Microsoft Edge com a página inicial predefinida, execute este cmdlet:

```powershell
New-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname> -Name <remoteappname> -FriendlyName <remoteappname> -FilePath "shell:Appsfolder\Microsoft.MicrosoftEdge_8wekyb3d8bbwe!MicrosoftEdge" 
```

## <a name="next-steps"></a>Passos seguintes

- Saiba como configurar feeds para organizar a forma como as aplicações são exibidas para os utilizadores no [Feed Personalizado para utilizadores do Windows Virtual Desktop](customize-feed-virtual-desktop-users-2019.md).
- Saiba mais sobre a funcionalidade anexada da aplicação MSIX na configuração da [aplicação MSIX](../app-attach.md).

