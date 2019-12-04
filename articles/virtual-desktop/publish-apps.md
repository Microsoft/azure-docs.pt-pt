---
title: Publicar aplicativos internos na área de trabalho virtual do Windows – Azure
description: Como publicar aplicativos modernos na área de trabalho virtual do Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 12/03/2019
ms.author: helohr
ms.openlocfilehash: 896fd41cff0ab8257da7b91687aaae389a1c81ca
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74769664"
---
# <a name="publish-built-in-apps-in-windows-virtual-desktop"></a>Publicar aplicativos internos na área de trabalho virtual do Windows

Este artigo lhe dirá como publicar aplicativos em seu ambiente de área de trabalho virtual do Windows.

## <a name="publish-built-in-apps"></a>Publicar aplicações incorporadas

Para publicar um aplicativo interno:

1. Conecte-se a uma das máquinas virtuais em seu pool de hosts.
2. Obtenha o **PackageFamilyName** do aplicativo que você deseja publicar seguindo as instruções neste [artigo](https://docs.microsoft.com/powershell/module/appx/get-appxpackage?view=win10-ps).
3. Por fim, execute o seguinte cmdlet com `<PackageFamilyName>` substituído pelo **PackageFamilyName** encontrado na etapa anterior:
   
   ```powershell
   New-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname> -Name <remoteappname> -FriendlyName <remoteappname> -FilePath "shell:appsFolder\<PackageFamilyName>!App"
   ```

>[!NOTE]
> A área de trabalho virtual do Windows só dá suporte à publicação de aplicativos com locais de instalação que começam com `C:\Program Files\Windows Apps`.

## <a name="update-app-icons"></a>Atualizar ícones de aplicativos

Depois de publicar um aplicativo, ele terá o ícone de aplicativo padrão do Windows em vez de sua imagem de ícone normal. Para alterar o ícone para o ícone normal, coloque a imagem do ícone desejado em um compartilhamento de rede. Os formatos de imagem com suporte são PNG, BMP, GIF, JPG, JPEG e ICO.

## <a name="publish-microsoft-edge"></a>Publicar o Microsoft Edge

O processo usado para publicar o Microsoft Edge é um pouco diferente do processo de publicação para outros aplicativos. Para publicar o Microsoft Edge com a página inicial padrão, execute este cmdlet:

```powershell
New-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname> -Name <remoteappname> -FriendlyName <remoteappname> -FilePath "shell:Appsfolder\Microsoft.MicrosoftEdge_8wekyb3d8bbwe!MicrosoftEdge" 
```

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre como configurar feeds para organizar como os aplicativos são exibidos para usuários em [Personalizar feed para usuários da área de trabalho virtual do Windows](customize-feed-for-virtual-desktop-users.md).
- Saiba mais sobre o recurso de anexo de aplicativo MSIX em [Configurar anexação de aplicativo MSIX](app-attach.md).

