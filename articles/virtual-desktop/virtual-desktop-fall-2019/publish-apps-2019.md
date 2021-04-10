---
title: Publicar aplicativos incorporados no Windows Virtual Desktop (clássico) - Azure
description: Como publicar aplicações incorporadas no Windows Virtual Desktop (clássico).
author: Heidilohr
ms.topic: how-to
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 1b179ac555ea86aff381c1217e834b8d0aa85e8c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "102561709"
---
# <a name="publish-built-in-apps-in-windows-virtual-desktop-classic"></a>Publicar aplicativos incorporados no Windows Virtual Desktop (clássico)

>[!IMPORTANT]
>Este conteúdo aplica-se ao Windows Virtual Desktop (clássico), que não suporta objetos de desktop virtual do Gestor de Recursos Azure Windows. Se estiver a tentar gerir os objetos de ambiente de trabalho virtuais do Azure Resource Manager Windows, consulte [este artigo](../publish-apps.md).

Este artigo irá dizer-lhe como publicar aplicações no seu ambiente de ambiente de trabalho virtual do Windows.

## <a name="publish-built-in-apps"></a>Publicar aplicações incorporadas

Para publicar uma aplicação incorporada:

1. Ligue-se a uma das máquinas virtuais da sua piscina de anfitriões.
2. Obtenha o **Nome Embalado** da app que pretende publicar seguindo as instruções [deste artigo.](/powershell/module/appx/get-appxpackage)
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

