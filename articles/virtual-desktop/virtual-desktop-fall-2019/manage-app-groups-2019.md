---
title: Gerir grupos de aplicações para Windows Virtual Desktop (clássico) - Azure
description: Saiba como configurar os inquilinos virtuais do Windows Desktop (clássico) no Azure Ative Directory (AD).
author: Heidilohr
ms.topic: tutorial
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: e2a1f38918b2ea6af8a334b6648a463753f5c7b0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91295214"
---
# <a name="tutorial-manage-app-groups-for-windows-virtual-desktop-classic"></a>Tutorial: Gerir grupos de aplicações para o Windows Virtual Desktop (clássico)

>[!IMPORTANT]
>Este conteúdo aplica-se ao Windows Virtual Desktop (clássico), que não suporta objetos de desktop virtual do Gestor de Recursos Azure Windows. Se estiver a tentar gerir os objetos de ambiente de trabalho virtuais do Azure Resource Manager Windows, consulte [este artigo](../manage-app-groups.md).

O grupo de aplicações padrão criado para um novo conjunto de anfitriões do Windows Virtual Desktop também publica o ambiente de trabalho completo. Além disso, pode criar um ou mais grupos de aplicações RemoteApp para o pool anfitrião. Siga este tutorial para criar um grupo de aplicações RemoteApp e publicar aplicações individuais do menu **Start.**

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar um grupo RemoteApp.
> * Conceder acesso a programas RemoteApp.

Antes de começar, [descarregue e importe o módulo Windows Virtual Desktop PowerShell](/powershell/windows-virtual-desktop/overview/) para utilizar na sessão PowerShell se ainda não o fez. Depois disso, execute o seguinte cmdlet para iniciar scontabilidade na sua conta:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="create-a-remoteapp-group"></a>Criar um grupo RemoteApp

1. Executar o cmdlet PowerShell seguinte para criar um novo grupo de aplicações RemoteApp vazio.

   ```powershell
   New-RdsAppGroup <tenantname> <hostpoolname> <appgroupname> -ResourceType "RemoteApp"
   ```

2. (Opcional) Para verificar se o grupo de aplicações foi criado, pode executar o seguinte cmdlet para ver uma lista de todos os grupos de aplicações para a piscina anfitriã.

   ```powershell
   Get-RdsAppGroup <tenantname> <hostpoolname>
   ```

3. Execute o cmdlet seguinte para obter uma lista de aplicações de menu **Iniciar** na imagem da máquina virtual da piscina anfitriã. Anote os valores para **FilePath,** **IconPath,** **IconIndex**e outras informações importantes para a aplicação que pretende publicar.

   ```powershell
   Get-RdsStartMenuApp <tenantname> <hostpoolname> <appgroupname>
   ```

4. Executar o cmdlet seguinte para instalar a aplicação com base em `AppAlias` . `AppAlias` torna-se visível quando corre a saída do passo 3.

   ```powershell
   New-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname> -Name <remoteappname> -AppAlias <appalias>
   ```

5. (Opcional) Execute o cmdlet seguinte para publicar um novo programa RemoteApp para o grupo de aplicações criado no passo 1.

   ```powershell
   New-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname> -Name <remoteappname> -Filepath <filepath>  -IconPath <iconpath> -IconIndex <iconindex>
   ```

6. Para verificar se a aplicação foi publicada, execute o seguinte cmdlet.

   ```powershell
   Get-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname>
   ```

7. Repita os passos 1-5 para cada aplicação que pretende publicar para este grupo de aplicações.
8. Execute o cmdlet seguinte para garantir aos utilizadores o acesso aos programas RemoteApp no grupo de aplicações.

   ```powershell
   Add-RdsAppGroupUser <tenantname> <hostpoolname> <appgroupname> -UserPrincipalName <userupn>
   ```

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu a criar um grupo de aplicações, povoá-lo com programas RemoteApp e atribuir utilizadores ao grupo de aplicações. Para aprender a criar uma piscina de anfitriões de validação, consulte o seguinte tutorial. Pode utilizar um pool de anfitriões de validação para monitorizar as atualizações de serviço antes de as lançar para o seu ambiente de produção.

> [!div class="nextstepaction"]
> [Criar um conjunto de anfitriões para validar as atualizações de serviço](create-validation-host-pool-2019.md)
