---
title: Gerir grupos de aplicações para Windows Virtual Desktop (pré-visualização) - Azure
description: Descreve como configurar a inquilinos de área de Trabalho Virtual do Windows no Azure Active Directory.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 03/21/2019
ms.author: helohr
ms.openlocfilehash: d0b5dede5846b3ff9eac3ee865685fe5b3f773f9
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58318107"
---
# <a name="tutorial-manage-app-groups-for-windows-virtual-desktop-preview"></a>Tutorial: Gerir grupos de aplicações para a área de Trabalho Virtual do Windows (pré-visualização)

O grupo de aplicações predefinido criado para um novo conjunto de anfitrião também publica a área de trabalho completa. Além disso, pode criar um ou mais grupos de aplicações de RemoteApp (pré-visualização) para o conjunto de anfitrião. Siga este tutorial para criar um grupo de aplicações do RemoteApp e publicar aplicações de menu início individuais.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Crie um grupo do RemoteApp.
> * Conceder acesso a aplicativos remotos.

Antes de começar, [transferir e importar o módulo do Windows PowerShell de ambiente de Trabalho Virtual](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) o módulo de área de Trabalho Virtual do Windows para utilizar na sua sessão do PowerShell, se ainda não o fez.

## <a name="create-a-remoteapp-group"></a>Criar um grupo do RemoteApp

1. Execute o seguinte cmdlet do PowerShell para criar um novo grupo vazio do RemoteApp.

   ```powershell
   New-RdsAppGroup <tenantname> <hostpoolname> <appgroupname> -ResourceType "RemoteApp"
   ```

2. (Opcional) Para verificar que o grupo de aplicações foi criado, pode executar o cmdlet seguinte para ver uma lista de todos os grupos de aplicações para o conjunto de anfitrião.

   ```powershell
   Get-RdsAppGroup <tenantname> <hostpoolname>
   ```

3. Execute o seguinte cmdlet para obter uma lista de início do menu de aplicações numa imagem de máquina virtual do conjunto de anfitrião. Indique os valores para **FilePath**, **IconPath**, **IconIndex**e outras informações importantes para a aplicação que pretende publicar.

   ```powershell
   Get-RdsStartMenuApp <tenantname> <hostpoolname> <appgroupname>
   ```

4. Execute o seguinte cmdlet para publicar um novo RemoteApp para o grupo de aplicações que criou no passo 1.

   ```powershell
   New-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname> <remoteappname> -Filepath <filepath>  -IconPath <iconpath> -IconIndex <iconindex>
   ```

5. (Opcional) Execute o cmdlet seguinte para instalar a aplicação com base em appalias. appalias se torna visível quando executa a saída do passo 3.

   ```powershell
   New-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname> <remoteappname> -AppAlias <appalias>
   ```

6. Para verificar que a aplicação foi publicada, execute o seguinte cmdlet.

   ```powershell
   Get-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname>
   ```

7. Repita os passos 1 a 5 para cada aplicação que pretende publicar para este grupo de aplicações.
8. Execute o cmdlet seguinte para conceder aos utilizadores acesso a RemoteApps no grupo de aplicações.

   ```powershell
   Add-RdsAppGroupUser <tenantname> <hostpoolname> <appgroupname> -UserPrincipalNames <userupn>
   ```

## <a name="next-steps"></a>Passos Seguintes

Depois de criar os grupos de aplicações, pode criar principais de serviço e atribuir funções aos seus utilizadores. Para saber como fazer isso, veja o tutorial para saber como criar principais de serviço e as atribuições de funções com o PowerShell.

> [!div class="nextstepaction"]
> [Criar principais de serviço e as atribuições de funções com o PowerShell](create-service-principal-role-powershell.md)
