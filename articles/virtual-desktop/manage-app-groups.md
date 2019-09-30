---
title: Gerenciar grupos de aplicativos para a área de trabalho virtual do Windows-Azure
description: Descreve como configurar locatários de área de trabalho virtual do Windows no Azure Active Directory.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 08/29/2019
ms.author: helohr
ms.openlocfilehash: e158c0a6090493bec0169c144f030300de921516
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/30/2019
ms.locfileid: "71679460"
---
# <a name="tutorial-manage-app-groups-for-windows-virtual-desktop"></a>Tutorial: Gerenciar grupos de aplicativos para área de trabalho virtual do Windows

O grupo de aplicativos padrão criado para um novo pool de hosts da área de trabalho virtual do Windows também publica a área de trabalho completa. Além disso, você pode criar um ou mais grupos de aplicativos do RemoteApp para o pool de hosts. Siga este tutorial para criar um grupo de aplicativos do RemoteApp e publicar aplicativos de menu **Iniciar** individuais.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Crie um grupo do RemoteApp.
> * Conceder acesso a programas RemoteApp.

Antes de começar, [Baixe e importe o módulo do PowerShell de área de trabalho virtual do Windows](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) para usar em sua sessão do PowerShell, se ainda não tiver feito isso. Depois disso, execute o seguinte cmdlet para entrar em sua conta:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="create-a-remoteapp-group"></a>Criar um grupo do RemoteApp

1. Execute o seguinte cmdlet do PowerShell para criar um novo grupo de aplicativos do RemoteApp vazio.

   ```powershell
   New-RdsAppGroup <tenantname> <hostpoolname> <appgroupname> -ResourceType "RemoteApp"
   ```

2. Adicional Para verificar se o grupo de aplicativos foi criado, você pode executar o cmdlet a seguir para ver uma lista de todos os grupos de aplicativos para o pool de hosts.

   ```powershell
   Get-RdsAppGroup <tenantname> <hostpoolname>
   ```

3. Execute o cmdlet a seguir para obter uma lista de aplicativos do menu **Iniciar** na imagem da máquina virtual do pool de hosts. Anote os valores de **FilePath**, **IconPath**, **IconIndex**e outras informações importantes para o aplicativo que você deseja publicar.

   ```powershell
   Get-RdsStartMenuApp <tenantname> <hostpoolname> <appgroupname>
   ```
   
4. Execute o seguinte cmdlet para instalar o aplicativo com base em `AppAlias`. `AppAlias` torna-se visível quando você executa a saída da etapa 3.

   ```powershell
   New-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname> -Name <remoteappname> -AppAlias <appalias>
   ```

5. Adicional Execute o cmdlet a seguir para publicar um novo programa RemoteApp para o grupo de aplicativos criado na etapa 1.

   ```powershell
   New-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname> -Name <remoteappname> -Filepath <filepath>  -IconPath <iconpath> -IconIndex <iconindex>
   ```

6. Para verificar se o aplicativo foi publicado, execute o cmdlet a seguir.

   ```powershell
   Get-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname>
   ```

7. Repita as etapas 1 a 5 para cada aplicativo que você deseja publicar para este grupo de aplicativos.
8. Execute o cmdlet a seguir para conceder aos usuários acesso aos programas do RemoteApp no grupo de aplicativos.

   ```powershell
   Add-RdsAppGroupUser <tenantname> <hostpoolname> <appgroupname> -UserPrincipalName <userupn>
   ```

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, você aprendeu a criar um grupo de aplicativos, preenchê-lo com programas RemoteApp e atribuir usuários ao grupo de aplicativos. Para saber como criar um pool de hosts de validação, consulte o tutorial a seguir. Você pode usar um pool de hosts de validação para monitorar atualizações de serviço antes de distribuí-las para seu ambiente de produção.

> [!div class="nextstepaction"]
> [Criar um pool de hosts para validar atualizações de serviço](./create-validation-host-pool.md)
