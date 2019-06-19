---
title: Gerir grupos de aplicações para Windows Virtual Desktop Preview - Azure
description: Descreve como configurar a inquilinos de pré-visualização de ambiente de Trabalho Virtual do Windows no Azure Active Directory.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 03/21/2019
ms.author: helohr
ms.openlocfilehash: 73425df1f0cfedd2a681650fc2b536a652b621d5
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67206681"
---
# <a name="tutorial-manage-app-groups-for-windows-virtual-desktop-preview"></a>Tutorial: Gerir grupos de aplicações para Windows Virtual Desktop pré-visualização

O grupo de aplicações predefinido criado para um novo conjunto de anfitrião do Windows Virtual Desktop Preview também publica a área de trabalho completa. Além disso, pode criar um ou mais grupos de aplicações de RemoteApp para o conjunto de anfitrião. Siga este tutorial para criar um grupo de aplicações do RemoteApp e publicar individuais **iniciar** menu aplicações.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Crie um grupo do RemoteApp.
> * Conceder acesso a programas do RemoteApp.

Antes de começar, [transferir e importar o módulo do Windows PowerShell de ambiente de Trabalho Virtual](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) a utilizar na sua sessão do PowerShell, se ainda não o fez.

## <a name="create-a-remoteapp-group"></a>Criar um grupo do RemoteApp

1. Execute o seguinte cmdlet do PowerShell para criar um novo grupo de aplicação RemoteApp vazio.

   ```powershell
   New-RdsAppGroup <tenantname> <hostpoolname> <appgroupname> -ResourceType "RemoteApp"
   ```

2. (Opcional) Para verificar se o grupo de aplicação foi criado, pode executar o cmdlet seguinte para ver uma lista de todos os grupos de aplicações para o conjunto de anfitrião.

   ```powershell
   Get-RdsAppGroup <tenantname> <hostpoolname>
   ```

3. Execute o seguinte cmdlet para obter uma lista de **iniciar** menu aplicações numa imagem de máquina virtual do conjunto de anfitrião. Indique os valores para **FilePath**, **IconPath**, **IconIndex**e outras informações importantes para a aplicação que pretende publicar.

   ```powershell
   Get-RdsStartMenuApp <tenantname> <hostpoolname> <appgroupname>
   ```
   
4. Execute o seguinte cmdlet para instalar a aplicação com base em `AppAlias`. `AppAlias` se torna visível quando executa a saída do passo 3.

   ```powershell
   New-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname> -Name <remoteappname> -AppAlias <appalias>
   ```

5. (Opcional) Execute o seguinte cmdlet para publicar um novo programa RemoteApp para o grupo de aplicações que criou no passo 1.

   ```powershell
   New-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname> -Name <remoteappname> -Filepath <filepath>  -IconPath <iconpath> -IconIndex <iconindex>
   ```

6. Para verificar que a aplicação foi publicada, execute o seguinte cmdlet.

   ```powershell
   Get-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname>
   ```

7. Repita os passos 1 a 5 para cada aplicação que pretende publicar para este grupo de aplicações.
8. Execute o cmdlet seguinte para conceder aos utilizadores acesso a programas RemoteApp no grupo de aplicações.

   ```powershell
   Add-RdsAppGroupUser <tenantname> <hostpoolname> <appgroupname> -UserPrincipalName <userupn>
   ```

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, aprendeu a criar um grupo de aplicações, preenchê-lo com programas RemoteApp e atribuir utilizadores ao grupo de aplicações. Para saber como criar um conjunto de anfitrião de validação, veja o tutorial seguinte. Pode utilizar um conjunto de anfitrião de validação para monitorizar atualizações de serviço antes de distribuí-las ao seu ambiente de produção.

> [!div class="nextstepaction"]
> [Criar um conjunto de anfitrião para validar as atualizações de serviço](./create-validation-host-pool.md)
