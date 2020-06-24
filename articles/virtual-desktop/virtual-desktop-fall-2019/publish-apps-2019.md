---
title: Publicar aplicativos incorporados no Windows Virtual Desktop - Azure
description: Como publicar aplicativos incorporados no Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 7a453ef44a1ede86290f4130a6147eaaad09fa97
ms.sourcegitcommit: 6fd28c1e5cf6872fb28691c7dd307a5e4bc71228
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/23/2020
ms.locfileid: "85214150"
---
# <a name="publish-built-in-apps-in-windows-virtual-desktop"></a>Publicar aplicativos incorporados no Windows Virtual Desktop

>[!IMPORTANT]
>Este conteúdo aplica-se à versão outono 2019 que não suporta objetos de ambiente de trabalho virtuais do Gestor de Recursos Azure. Se está a tentar gerir os objetos virtuais do Azure Resource Manager Windows, introduzidos na atualização da primavera de 2020, consulte [este artigo](../publish-apps.md).

Este artigo irá dizer-lhe como publicar aplicações no seu ambiente de ambiente de trabalho virtual do Windows.

## <a name="publish-built-in-apps"></a>Publicar aplicações incorporadas

Para publicar uma aplicação incorporada:

1. Ligue-se a uma das máquinas virtuais da sua piscina de anfitriões.
2. Obtenha o **Nome Embalado** da app que pretende publicar seguindo as instruções [deste artigo.](/powershell/module/appx/get-appxpackage?view=win10-ps/)
3. Por fim, executar o seguinte cmdlet com `<PackageFamilyName>` substituído pelo **Nome Demómilia Pacote** que encontrou no passo anterior:

   ```powershell
   New-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname> -Name <remoteappname> -FriendlyName <remoteappname> -FilePath "shell:appsFolder\<PackageFamilyName>!App"
   ```

>[!NOTE]
> O Windows Virtual Desktop só suporta a publicação de aplicações com locais de instalação que começam com `C:\Program Files\Windows Apps` .

## <a name="update-app-icons"></a>Atualizar ícones de aplicativos

Depois de publicar uma aplicação, terá o ícone de aplicação padrão do Windows em vez da sua imagem de ícone regular. Para alterar o ícone para o seu ícone regular, coloque a imagem do ícone que pretende numa partilha de rede. Os formatos de imagem suportados são PNG, BMP, GIF, JPG, JPEG e ICO.

## <a name="publish-microsoft-edge"></a>Publicar Microsoft Edge

O processo que utiliza para publicar o Microsoft Edge é um pouco diferente do processo de publicação de outras apps. Para publicar o Microsoft Edge com a página inicial predefinida, execute este cmdlet:

```powershell
New-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname> -Name <remoteappname> -FriendlyName <remoteappname> -FilePath "shell:Appsfolder\Microsoft.MicrosoftEdge_8wekyb3d8bbwe!MicrosoftEdge"
```

## <a name="next-steps"></a>Passos seguintes

- Saiba como configurar feeds para organizar a forma como as aplicações são apresentadas para os utilizadores no [Feed Personalizado para utilizadores do Windows Virtual Desktop](customize-feed-virtual-desktop-users-2019.md).
- Saiba mais sobre a funcionalidade de anexação de aplicações MSIX no [anexo de aplicações set up MSIX](../app-attach.md).

