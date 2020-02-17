---
title: Gerir grupos de aplicativos para Windows Virtual Desktop - Azure
description: Descreve como configurar os inquilinos do Windows Virtual Desktop no Diretório Ativo Azure.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 08/29/2019
ms.author: helohr
ms.openlocfilehash: 8469b54afe01d9ee42dda8cf99f2f0125a4a1982
ms.sourcegitcommit: f97f086936f2c53f439e12ccace066fca53e8dc3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/15/2020
ms.locfileid: "77367312"
---
# <a name="tutorial-manage-app-groups-for-windows-virtual-desktop"></a>Tutorial: Gerir grupos de aplicativos para windows virtual desktop

O grupo de aplicações predefinido criado para um novo pool de anfitriões do Windows Virtual Desktop também publica todo o ambiente de trabalho. Além disso, pode criar um ou mais grupos de aplicações RemoteApp para o pool anfitrião. Siga este tutorial para criar um grupo de aplicações RemoteApp e publique aplicações individuais de menu **Start.**

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Crie um grupo RemoteApp.
> * Conceder acesso a programas RemoteApp.

Antes de começar, [faça o download e importe o módulo Windows Virtual Desktop PowerShell](/powershell/windows-virtual-desktop/overview/) para utilizar na sua sessão PowerShell se ainda não o fez. Depois disso, execute o seguinte cmdlet para iniciar sessão na sua conta:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="create-a-remoteapp-group"></a>Criar um grupo RemoteApp

1. Execute o seguinte cmdlet PowerShell para criar um novo grupo de aplicações RemoteApp vazio.

   ```powershell
   New-RdsAppGroup <tenantname> <hostpoolname> <appgroupname> -ResourceType "RemoteApp"
   ```

2. (Opcional) Para verificar se o grupo de aplicações foi criado, pode executar o seguinte cmdlet para ver uma lista de todos os grupos de aplicações para o pool anfitrião.

   ```powershell
   Get-RdsAppGroup <tenantname> <hostpoolname>
   ```

3. Execute o seguinte cmdlet para obter uma lista de aplicações de menu **Iniciar** na imagem virtual da máquina do pool anfitrião. Anote os valores para **FilePath,** **IconPath,** **IconIndex,** e outras informações importantes para a aplicação que pretende publicar.

   ```powershell
   Get-RdsStartMenuApp <tenantname> <hostpoolname> <appgroupname>
   ```
   
4. Executar o seguinte cmdlet para instalar a aplicação com base em `AppAlias`. `AppAlias` torna-se visível quando executa a saída a partir do passo 3.

   ```powershell
   New-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname> -Name <remoteappname> -AppAlias <appalias>
   ```

5. (Opcional) Execute o seguinte cmdlet para publicar um novo programa RemoteApp para o grupo de aplicações criado no passo 1.

   ```powershell
   New-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname> -Name <remoteappname> -Filepath <filepath>  -IconPath <iconpath> -IconIndex <iconindex>
   ```

6. Para verificar se a aplicação foi publicada, execute o seguinte cmdlet.

   ```powershell
   Get-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname>
   ```

7. Repita os passos 1-5 para cada aplicação que pretende publicar para este grupo de aplicações.
8. Executar o seguinte cmdlet para dar aos utilizadores acesso aos programas RemoteApp no grupo de aplicações.

   ```powershell
   Add-RdsAppGroupUser <tenantname> <hostpoolname> <appgroupname> -UserPrincipalName <userupn>
   ```

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu a criar um grupo de aplicações, povoá-lo com programas RemoteApp e atribuir utilizadores ao grupo de aplicações. Para aprender a criar uma piscina de anfitriões de validação, consulte o seguinte tutorial. Você pode usar um pool anfitrião de validação para monitorizar atualizações de serviço antes de as lançar para o seu ambiente de produção.

> [!div class="nextstepaction"]
> [Crie um pool de anfitriões para validar atualizações de serviço](./create-validation-host-pool.md)
